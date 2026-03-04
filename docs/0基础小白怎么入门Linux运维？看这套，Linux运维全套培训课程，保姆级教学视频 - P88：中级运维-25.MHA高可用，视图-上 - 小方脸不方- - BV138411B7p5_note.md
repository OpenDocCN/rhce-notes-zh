# Linux运维全套培训课程：P88：中级运维-25.MHA高可用，视图-上 🛡️

![](img/08f122e99bc018ce9d2b530e3470a513_0.png)

在本节课中，我们将要学习MySQL高可用集群中的一个重要工具——MHA。我们将了解什么是高可用性，为什么它在集群中至关重要，并开始动手配置MHA环境，为后续的故障切换实战打下基础。

## 课程概述

上一节我们介绍了读写分离，它通过分担主库的读写压力来提升集群性能。本节中我们来看看如何保障集群的**高可用性**。

高可用性的核心目标是：当集群中的某台服务器（尤其是主库）因硬件故障、访问量过大或网络中断等原因宕机时，整个集群的业务能够**不受影响地继续运行**。MHA就是MySQL领域一个专门用于实现**故障自动切换**和**主从复制管理**的流行工具。

## 高可用性的必要性

一个MySQL集群通常包含多台数据库服务器。任何单台服务器都存在意外宕机的风险。如果只是某个从库故障，由于从库主要负责读操作，且数量通常多于主库，因此对整体业务影响有限。但如果是**主库发生故障**，整个集群将无法处理写入操作，导致业务中断。

MHA的作用正是在主库故障时，**自动**将一个数据最新的从库提升为新的主库，并让其他从库重新指向这个新主库，从而在极短的时间内（通常10秒以内）恢复集群的完整功能。

## MHA 简介

MHA 的全称是 **Master High Availability**，即主库高可用。它主要由两部分组成：

1.  **MHA Manager（管理节点）**：通常部署在一台独立的服务器上，负责监控和管理整个MySQL集群，并在故障时执行切换操作。
2.  **MHA Node（数据节点）**：需要安装在**每一台**MySQL服务器上（包括主库和所有从库）。它相当于一个代理，接收Manager节点的指令并执行具体操作。

MHA进行故障切换的前提是，集群已经配置好了**基于二进制日志的主从复制**，并且至少是**一主两从**的架构。

## 实验环境准备

![](img/08f122e99bc018ce9d2b530e3470a513_2.png)

我们将使用四台虚拟机来搭建实验环境：
*   **主库**：192.168.0.142
*   **从库1**：192.168.0.143
*   **从库2**：192.168.0.144
*   **MHA Manager节点**：192.168.0.254

![](img/08f122e99bc018ce9d2b530e3470a513_4.png)

以下是搭建MHA前的准备工作步骤。

![](img/08f122e99bc018ce9d2b530e3470a513_6.png)

### 步骤一：配置主从复制

首先，我们需要在142、143、144三台服务器上建立一主两从的复制关系。确保三台服务器间的数据一致，并且主从状态正常（`Slave_IO_Running` 和 `Slave_SQL_Running` 均为 `Yes`）。

**关键授权命令示例（在主库执行，授权给Manager节点和从库）：**
```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'192.168.0.254' IDENTIFIED BY 'your_password';
GRANT REPLICATION SLAVE ON *.* TO 'repl_user'@'%' IDENTIFIED BY 'repl_password';
-- 实际实验中为简化，可能使用root用户同时进行管理授权和复制授权。
```

### 步骤二：安装 MHA Node

MHA Node软件需要安装在**所有四台**服务器上（包括Manager节点本身）。

1.  上传安装包（如 `mha4mysql-node-XXX.rpm`）到服务器。
2.  安装依赖包。
3.  使用rpm命令安装Node包。

![](img/08f122e99bc018ce9d2b530e3470a513_8.png)

**安装依赖和Node的命令示例：**
```bash
# 安装依赖 (具体包名可能因系统而异)
yum install -y perl-DBD-MySQL perl-Config-Tiny perl-Params-Validate perl-CPAN

![](img/08f122e99bc018ce9d2b530e3470a513_10.png)

![](img/08f122e99bc018ce9d2b530e3470a513_11.png)

![](img/08f122e99bc018ce9d2b530e3470a513_13.png)

# 安装MHA Node
rpm -ivh mha4mysql-node-XXX.rpm
```
可以使用终端工具的“发送键入到所有会话”功能，同时在四台服务器上执行上述操作以提高效率。

![](img/08f122e99bc018ce9d2b530e3470a513_15.png)

### 步骤三：安装 MHA Manager

MHA Manager软件只需要安装在Manager节点（192.168.0.254）上。

![](img/08f122e99bc018ce9d2b530e3470a513_17.png)

1.  上传Manager安装包（如 `mha4mysql-manager-XXX.rpm`）。
2.  安装所需的额外依赖包。
3.  使用rpm命令安装Manager包。

![](img/08f122e99bc018ce9d2b530e3470a513_19.png)

![](img/08f122e99bc018ce9d2b530e3470a513_21.png)

**在Manager节点上执行的命令示例：**
```bash
# 安装Manager的依赖
yum install -y perl-Config-Tiny perl-Time-HiRes perl-Parallel-ForkManager perl-Log-Dispatch

# 安装MHA Manager
rpm -ivh mha4mysql-manager-XXX.rpm
```

### 步骤四：配置 SSH 免密登录

![](img/08f122e99bc018ce9d2b530e3470a513_23.png)

![](img/08f122e99bc018ce9d2b530e3470a513_25.png)

![](img/08f122e99bc018ce9d2b530e3470a513_27.png)

![](img/08f122e99bc018ce9d2b530e3470a513_29.png)

为了让Manager节点能够无密码远程登录并管理所有MySQL服务器，需要配置四台服务器之间的SSH免密登录。

![](img/08f122e99bc018ce9d2b530e3470a513_31.png)

以下是配置步骤：

1.  在**所有四台服务器**上生成SSH密钥对。
2.  将每台服务器的公钥分发到包括自己在内的所有其他服务器。

**操作命令示例（在Manager节点上执行即可，因为实验环境中密码一致）：**
```bash
# 1. 生成密钥（一路回车使用默认值）
ssh-keygen -t rsa

![](img/08f122e99bc018ce9d2b530e3470a513_33.png)

![](img/08f122e99bc018ce9d2b530e3470a513_35.png)

# 2. 将公钥拷贝到各服务器（包括自己），需要输入一次密码
ssh-copy-id root@192.168.0.254
ssh-copy-id root@192.168.0.142
ssh-copy-id root@192.168.0.143
ssh-copy-id root@192.168.0.144
```
配置完成后，使用 `ssh root@目标IP` 命令测试，应能直接登录而无需密码。

![](img/08f122e99bc018ce9d2b530e3470a513_37.png)

## 配置 MHA Manager

![](img/08f122e99bc018ce9d2b530e3470a513_39.png)

![](img/08f122e99bc018ce9d2b530e3470a513_41.png)

安装完成后，MHA的核心配置在于Manager节点的配置文件。我们需要创建一个配置文件来定义集群结构、监控参数和切换策略。

![](img/08f122e99bc018ce9d2b530e3470a513_43.png)

配置文件通常放在 `/etc/mha/` 目录下，例如 `/etc/mha/app1.cnf`。

![](img/08f122e99bc018ce9d2b530e3470a513_45.png)

![](img/08f122e99bc018ce9d2b530e3470a513_47.png)

以下是配置文件内容的关键部分及解释：

![](img/08f122e99bc018ce9d2b530e3470a513_49.png)

```bash
[server default]
# 数据库监控/管理用户（Manager连接数据库用的账号）
user=root
password=your_mysql_root_password

# SSH免密登录的用户
ssh_user=root

# 主从复制用户（从库连接主库用的账号）
repl_user=root
repl_password=your_mysql_root_password

# Manager监控ping间隔（秒），建议1秒
ping_interval=1

# 故障切换脚本路径
master_ip_failover_script=/usr/local/bin/master_ip_failover

# Manager工作目录和日志
manager_workdir=/var/log/mha/app1
manager_log=/var/log/mha/app1/manager.log

[server1]
hostname=192.168.0.142
port=3306
# 主库二进制日志存放目录（根据实际安装方式调整）
master_binlog_dir=/var/lib/mysql
# 标记此服务器为初始主库
candidate_master=1

[server2]
hostname=192.168.0.143
port=3306
master_binlog_dir=/var/lib/mysql
# 标记此服务器在故障切换时“可以”被提升为主库
candidate_master=1

[server3]
hostname=192.168.0.144
port=3306
master_binlog_dir=/var/lib/mysql
# 标记此服务器在故障切换时“不可以”被提升为主库
no_master=1
```

**配置文件要点说明：**
*   `[server default]`：全局默认配置，适用于所有服务器。
*   `user`/`password`：Manager连接各MySQL实例的凭证。
*   `ssh_user`：执行远程命令的SSH用户。
*   `repl_user`/`repl_password`：主从复制使用的凭证。
*   `ping_interval`：健康检查频率，值越小故障发现越快。
*   `master_ip_failover_script`：指定故障切换时执行的脚本路径（下节课详细讲解）。
*   `[serverX]`：定义每个MySQL服务器的个体信息，包括IP、端口、二进制日志路径。
*   `candidate_master=1` 和 `no_master=1`：用于指定故障切换时的优先级。Manager会优先提升标记为 `candidate_master` 的从库为新主库。

## 本节课总结

本节课中我们一起学习了MySQL高可用集群的基本概念，并完成了部署MHA的前期准备工作。

我们首先理解了**高可用性**对于保障业务连续性的重要性。然后，我们介绍了**MHA**这个工具及其两个核心组件：Manager和Node。接着，我们一步步搭建了实验环境，包括：
1.  建立了一主两从的MySQL复制集群。
2.  在所有节点上安装了MHA Node。
3.  在管理节点上安装了MHA Manager。
4.  配置了服务器间的SSH免密登录，为自动化管理铺平道路。
5.  创建并详解了MHA Manager的核心配置文件，定义了集群结构、监控参数和故障切换规则。

![](img/08f122e99bc018ce9d2b530e3470a513_51.png)

下一节，我们将深入讲解故障切换脚本的编写，并启动MHA服务，实际测试主库故障时的自动切换效果，敬请期待。