# Linux运维进阶：P87：MHA高可用配置（上）

![](img/9edee4dcac5be1e5e102d013984d3a5c_0.png)

## 概述
在本节课中，我们将学习MySQL高可用集群的核心概念与配置。我们将重点介绍MHA（Master High Availability）工具，它用于在MySQL主从复制架构中实现自动故障切换，确保数据库服务的高可用性。我们将从MHA的基本原理讲起，逐步完成其部署与基础配置。

## 集群架构回顾与高可用引入
上一节我们介绍了读写分离，它通过将读操作分发到从库来分担主库压力。本节中，我们来看看如何保证集群在单点故障时仍能持续提供服务，即实现“高可用”。

在主从复制架构中，如果主库因硬件故障、访问压力过大或网络问题而宕机，整个集群将无法处理写入请求。高可用的核心目标，就是在主库发生故障时，能自动将一台从库提升为新的主库，并让其他从库重新指向这个新主库，从而快速恢复服务，整个过程对应用透明。

MHA正是实现这一目标的常用工具。它专门用于MySQL（及MariaDB）环境下的故障切换与主从复制管理。其故障切换速度通常在10秒以内，并能最大程度保证数据一致性。

## MHA工作原理与组件
MHA的运作依赖于两个核心组件：管理节点（Manager）和数据节点（Node）。

![](img/9edee4dcac5be1e5e102d013984d3a5c_2.png)

*   **数据节点（Node）**：这是一个客户端程序，需要安装在**每一台**MySQL服务器上（包括主库和所有从库）。它负责收集本地MySQL服务器的状态信息。
*   **管理节点（Manager）**：这是一个服务端程序，通常单独部署在一台独立的监控服务器上。它通过定期与所有Node节点通信来监控整个MySQL集群的健康状况。

![](img/9edee4dcac5be1e5e102d013984d3a5c_4.png)

![](img/9edee4dcac5be1e5e102d013984d3a5c_6.png)

当Manager节点检测到主库无法连接时，便会触发故障转移流程：
1.  从存活的从库中，选取数据最新的一个。
2.  将其提升为新的主库。
3.  让其他从库指向这个新的主库，重新建立复制关系。

**前提条件**：部署MHA至少需要一个“一主两从”的MySQL复制环境。

## 实验环境准备
以下是配置MHA前的准备工作清单，我们需要确保一个由四台服务器构成的基础环境：

![](img/9edee4dcac5be1e5e102d013984d3a5c_8.png)

*   **数据库服务器1**：IP: 192.168.0.142， 角色：主库（Master）
*   **数据库服务器2**：IP: 192.168.0.143， 角色：从库（Slave）
*   **数据库服务器3**：IP: 192.168.0.144， 角色：从库（Slave）
*   **管理服务器**：IP: 192.168.0.254， 角色：MHA Manager节点

![](img/9edee4dcac5be1e5e102d013984d3a5c_10.png)

![](img/9edee4dcac5be1e5e102d013984d3a5c_11.png)

![](img/9edee4dcac5be1e5e102d013984d3a5c_13.png)

![](img/9edee4dcac5be1e5e102d013984d3a5c_15.png)

首先，在所有四台服务器上执行以下基础操作：

![](img/9edee4dcac5be1e5e102d013984d3a5c_17.png)

1.  **关闭防火墙**：避免网络连接被拦截。
    ```bash
    systemctl stop firewalld
    systemctl disable firewalld
    ```
2.  **配置MySQL主从复制**：在142上配置为主库，在143和144上配置为从库，并确保复制状态正常（`Slave_IO_Running`和`Slave_SQL_Running`均为`Yes`）。
3.  **配置访问授权**：在所有MySQL服务器（142, 143, 144）上，为Manager节点（254）授予访问权限。为了简化，我们可以使用一条命令授权所有主机。
    ```sql
    GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'your_password' WITH GRANT OPTION;
    FLUSH PRIVILEGES;
    ```
    > 注：生产环境中建议遵循最小权限原则，为Manager节点单独创建用户并精确授权。

![](img/9edee4dcac5be1e5e102d013984d3a5c_19.png)

![](img/9edee4dcac5be1e5e102d013984d3a5c_21.png)

## 安装MHA组件
安装分为两部分：在所有服务器上安装Node包，在Manager服务器上额外安装Manager包。

![](img/9edee4dcac5be1e5e102d013984d3a5c_23.png)

![](img/9edee4dcac5be1e5e102d013984d3a5c_25.png)

![](img/9edee4dcac5be1e5e102d013984d3a5c_27.png)

### 1. 安装数据节点（Node）
在**所有四台服务器**（142, 143, 144, 254）上，安装MHA Node组件及其依赖。

![](img/9edee4dcac5be1e5e102d013984d3a5c_29.png)

![](img/9edee4dcac5be1e5e102d013984d3a5c_30.png)

首先安装依赖包：
```bash
yum install -y perl-DBD-MySQL perl-Config-Tiny perl-Params-Validate perl-CPAN
```
然后安装Node的RPM包（请根据实际下载的包名调整）：
```bash
rpm -ivh mha4mysql-node-0.58-0.el7.centos.noarch.rpm
```

![](img/9edee4dcac5be1e5e102d013984d3a5c_32.png)

![](img/9edee4dcac5be1e5e102d013984d3a5c_34.png)

### 2. 安装管理节点（Manager）
仅在**Manager服务器**（254）上，安装MHA Manager组件及其依赖。

![](img/9edee4dcac5be1e5e102d013984d3a5c_36.png)

![](img/9edee4dcac5be1e5e102d013984d3a5c_37.png)

![](img/9edee4dcac5be1e5e102d013984d3a5c_39.png)

安装依赖包：
```bash
yum install -y perl-Config-Tiny perl-Time-HiRes perl-Parallel-ForkManager perl-Log-Dispatch perl-DBD-MySQL
```
安装Manager的RPM包：
```bash
rpm -ivh mha4mysql-manager-0.58-0.el7.centos.noarch.rpm
```

![](img/9edee4dcac5be1e5e102d013984d3a5c_41.png)

![](img/9edee4dcac5be1e5e102d013984d3a5c_43.png)

![](img/9edee4dcac5be1e5e102d013984d3a5c_45.png)

## 配置SSH免密登录
Manager节点需要通过SSH连接到所有MySQL服务器执行故障切换命令。为了实现自动化，我们需要配置从Manager到所有节点（包括自身）的SSH免密登录。

![](img/9edee4dcac5be1e5e102d013984d3a5c_47.png)

在**Manager服务器**（254）上执行以下操作：

1.  生成SSH密钥对（一直回车使用默认值）：
    ```bash
    ssh-keygen -t rsa
    ```
2.  将公钥分发到所有服务器（包括自己），需要输入各服务器`root`用户的密码：
    ```bash
    ssh-copy-id root@192.168.0.254
    ssh-copy-id root@192.168.0.142
    ssh-copy-id root@192.168.0.143
    ssh-copy-id root@192.168.0.144
    ```
3.  验证免密登录是否成功：
    ```bash
    ssh root@192.168.0.142
    ```
    如果无需输入密码即可登录，说明配置成功。

## 配置MHA Manager
核心配置在于Manager节点上的配置文件。我们创建一个新的配置文件。

1.  在Manager服务器上创建配置目录和文件：
    ```bash
    mkdir -p /etc/mha
    vim /etc/mha/app1.cnf
    ```
2.  将以下配置内容写入`app1.cnf`文件，并**根据你的实际环境修改IP地址、路径和密码**：
    ```ini
    [server default]
    # MySQL管理用户和密码（用于Manager监控和连接数据库）
    user=root
    password=your_mysql_root_password
    # SSH免密登录的用户
    ssh_user=root
    # MySQL复制用户和密码（用于主从复制）
    repl_user=root
    repl_password=your_mysql_root_password
    # 监控ping间隔（秒），用于检测节点存活
    ping_interval=1
    # Manager的工作目录
    working_dir=/var/log/mha/app1
    # Manager的日志文件
    manager_log=/var/log/mha/app1/manager.log
    # 故障切换脚本路径（需提前准备）
    master_ip_failover_script=/usr/local/bin/master_ip_failover
    # 主库二进制日志目录（非常重要！根据你的MySQL安装方式调整）
    master_binlog_dir=/var/lib/mysql

    [server1]
    hostname=192.168.0.142
    port=3306
    # 标记此节点为初始主库
    master_binlog_dir=/var/lib/mysql
    candidate_master=1

    [server2]
    hostname=192.168.0.143
    port=3306
    master_binlog_dir=/var/lib/mysql
    # candidate_master=1 表示有资格在故障时被提升为主库
    candidate_master=1

    [server3]
    hostname=192.168.0.144
    port=3306
    master_binlog_dir=/var/lib/mysql
    # no_master=1 表示该节点不允许被提升为主库
    no_master=1
    ```

**关键配置项说明**：
*   `[server default]`：全局默认配置。
*   `master_binlog_dir`：指定MySQL二进制日志的存放路径。故障切换时，新的从库需要从这个目录读取日志以与新主库同步。**必须根据实际安装路径正确设置**。
*   `candidate_master=1`：标记该节点有资格在故障时被提升为新主库。
*   `no_master=1`：标记该节点不允许被提升为主库。
*   `ping_interval`：Manager检测节点状态的心跳频率，设为1秒可实现快速故障发现。

## 总结
本节课中我们一起学习了MHA高可用的基本概念、架构组成以及前期部署与配置。我们完成了以下关键步骤：
1.  理解了高可用的必要性及MHA的工作原理。
2.  准备好了“一主两从”的MySQL复制环境。
3.  在所有节点上安装了MHA Node组件，在管理节点上安装了Manager组件。
4.  配置了SSH免密登录，为自动故障切换铺平道路。
5.  创建并详解了MHA Manager的核心配置文件，定义了集群节点、故障切换策略等关键信息。

![](img/9edee4dcac5be1e5e102d013984d3a5c_49.png)

下一节，我们将继续完成MHA的配置，包括设置故障切换脚本，并启动MHA服务进行最终测试，验证其自动故障切换功能。