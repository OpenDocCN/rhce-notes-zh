# Linux运维：中级运维-25：MHA高可用配置教程 🛠️

![](img/d02fb7054781fae638e64b285b45d22f_0.png)

在本节课中，我们将学习如何配置MHA（Master High Availability）来实现MySQL数据库的高可用性。高可用性是集群架构中至关重要的部分，它能确保当主数据库发生故障时，整个集群仍能继续提供服务，而不会中断业务。

上一节我们介绍了读写分离，它通过分担主库的读写压力来提升集群性能。本节中，我们来看看如何通过MHA实现故障自动切换，进一步提升集群的可靠性。

## 概述：什么是MHA？

MHA是一个用于MySQL高可用环境下的故障切换和主从复制的软件。它的核心功能是监控主数据库，一旦检测到主库故障，便会自动将一个从库提升为新的主库，并让其他从库重新指向这个新主库，整个过程通常在10秒内完成，从而保证业务连续性。

## 前提条件

在配置MHA之前，必须满足以下条件：
*   一个正常运行的**一主两从**MySQL集群。
*   所有数据库节点之间已配置好**主从复制**。
*   管理节点（Manager）能够通过SSH**免密登录**到所有数据库节点。
*   所有数据库节点已为管理节点授权访问权限。

## 第一步：准备MySQL主从集群

首先，我们需要确保一个一主两从的MySQL集群已搭建完毕并运行正常。

以下是配置主从复制的关键步骤：

1.  **修改配置文件**：确保每台MySQL服务器的`server-id`唯一。
    ```ini
    # 在主库（例如192.168.0.142）的my.cnf中
    server-id=1
    log-bin=mysql-bin

    # 在从库1（例如192.168.0.143）的my.cnf中
    server-id=2

    # 在从库2（例如192.168.0.144）的my.cnf中
    server-id=3
    ```

![](img/d02fb7054781fae638e64b285b45d22f_2.png)

2.  **主库授权**：在主库上为从库和管理节点创建用户并授权。
    ```sql
    GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'your_password' WITH GRANT OPTION;
    FLUSH PRIVILEGES;
    ```

![](img/d02fb7054781fae638e64b285b45d22f_4.png)

![](img/d02fb7054781fae638e64b285b45d22f_6.png)

3.  **配置从库复制**：在每个从库上执行命令，指向主库。
    ```sql
    CHANGE MASTER TO
    MASTER_HOST='192.168.0.142',
    MASTER_USER='root',
    MASTER_PASSWORD='your_password',
    MASTER_LOG_FILE='mysql-bin.000002',
    MASTER_LOG_POS=678;
    START SLAVE;
    SHOW SLAVE STATUS\G; -- 确保 Slave_IO_Running 和 Slave_SQL_Running 均为 Yes
    ```

## 第二步：配置SSH免密登录

为了让MHA管理节点能无干预地操作所有数据库节点，需要配置SSH免密登录。

以下是配置免密登录的步骤：

1.  在**管理节点**（例如192.168.0.254）上生成SSH密钥对。
    ```bash
    ssh-keygen -t rsa
    ```
2.  将公钥分发到**所有节点**（包括管理节点自身和三个数据库节点）。
    ```bash
    ssh-copy-id root@192.168.0.254
    ssh-copy-id root@192.168.0.142
    ssh-copy-id root@192.168.0.143
    ssh-copy-id root@192.168.0.144
    ```
3.  验证免密登录是否成功。
    ```bash
    ssh root@192.168.0.142 # 应能直接登录，无需密码
    ```

## 第三步：安装MHA软件

![](img/d02fb7054781fae638e64b285b45d22f_8.png)

MHA由两部分组成：在所有节点上安装的`node`包，以及在管理节点上额外安装的`manager`包。

![](img/d02fb7054781fae638e64b285b45d22f_10.png)

![](img/d02fb7054781fae638e64b285b45d22f_11.png)

![](img/d02fb7054781fae638e64b285b45d22f_13.png)

以下是安装步骤：

![](img/d02fb7054781fae638e64b285b45d22f_15.png)

1.  **在所有节点上安装MHA Node**：
    ```bash
    # 1. 安装依赖
    yum install -y perl-DBD-MySQL perl-Config-Tiny perl-Log-Dispatch perl-Parallel-ForkManager
    # 2. 安装node包（请替换为实际的包名）
    rpm -ivh mha4mysql-node-0.58-0.el7.centos.noarch.rpm
    ```

![](img/d02fb7054781fae638e64b285b45d22f_17.png)

2.  **在管理节点上安装MHA Manager**：
    ```bash
    # 1. 安装额外依赖
    yum install -y perl-Config-Tiny perl-Time-HiRes perl-Log-Dispatch perl-Parallel-ForkManager perl-DBD-MySQL
    # 2. 安装manager包（请替换为实际的包名）
    rpm -ivh mha4mysql-manager-0.58-0.el7.centos.noarch.rpm
    ```

![](img/d02fb7054781fae638e64b285b45d22f_19.png)

## 第四步：配置MHA Manager

![](img/d02fb7054781fae638e64b285b45d22f_21.png)

配置的核心是创建并编辑MHA Manager的配置文件。

以下是配置文件 `/etc/mha/app1.cnf` 的示例内容及说明：

![](img/d02fb7054781fae638e64b285b45d22f_23.png)

![](img/d02fb7054781fae638e64b285b45d22f_25.png)

![](img/d02fb7054781fae638e64b285b45d22f_27.png)

```ini
[server default]
# 数据库访问用户（管理节点连接数据库用）
user=root
password=your_password
# SSH免密登录用户
ssh_user=root
# 主从复制用户
repl_user=root
repl_password=your_password

![](img/d02fb7054781fae638e64b285b45d22f_29.png)

![](img/d02fb7054781fae638e64b285b45d22f_30.png)

# 监控ping间隔（秒），建议1秒
ping_interval=1

# 故障切换脚本路径
master_ip_failover_script=/usr/local/bin/master_ip_failover

![](img/d02fb7054781fae638e64b285b45d22f_32.png)

![](img/d02fb7054781fae638e64b285b45d22f_34.png)

# 工作目录和日志
manager_workdir=/var/log/mha/app1
manager_log=/var/log/mha/app1/manager.log

[server1]
hostname=192.168.0.142
port=3306
# 主库二进制日志路径（根据实际安装方式调整）
master_binlog_dir=/var/lib/mysql
# 标记为候选主库
candidate_master=1

![](img/d02fb7054781fae638e64b285b45d22f_36.png)

![](img/d02fb7054781fae638e64b285b45d22f_37.png)

![](img/d02fb7054781fae638e64b285b45d22f_38.png)

![](img/d02fb7054781fae638e64b285b45d22f_40.png)

![](img/d02fb7054781fae638e64b285b45d22f_42.png)

[server2]
hostname=192.168.0.143
port=3306
master_binlog_dir=/var/lib/mysql
candidate_master=1

![](img/d02fb7054781fae638e64b285b45d22f_44.png)

![](img/d02fb7054781fae638e64b285b45d22f_46.png)

[server3]
hostname=192.168.0.144
port=3306
master_binlog_dir=/var/lib/mysql
# 标记为不可提升为主库
no_master=1
```

![](img/d02fb7054781fae638e64b285b45d22f_48.png)

![](img/d02fb7054781fae638e64b285b45d22f_50.png)

**关键配置项解释**：
*   `[server default]`：全局默认配置。
*   `ping_interval`：管理节点检查主库存活的心跳频率。
*   `master_ip_failover_script`：故障切换时自动执行的脚本路径，需要提前准备。
*   `candidate_master=1`：该从库可以被提升为新的主库。
*   `no_master=1`：该从库不允许被提升为主库。

![](img/d02fb7054781fae638e64b285b45d22f_52.png)

## 第五步：准备故障切换脚本

你需要准备一个故障切换脚本，MHA会在切换时调用它。脚本内容较长，核心逻辑是修改应用的连接地址（虚拟IP）指向新的主库。你可以在MHA的官方示例或课程资料中找到基础脚本，并根据你的网络环境进行修改，主要是更新虚拟IP地址。

将脚本保存到配置文件指定的路径（如`/usr/local/bin/master_ip_failover`），并赋予执行权限：
```bash
chmod +x /usr/local/bin/master_ip_failover
```

## 第六步：启动与验证MHA

1.  **检查MHA配置**：在管理节点上运行以下命令，检查配置和SSH连接是否正常。
    ```bash
    masterha_check_ssh --conf=/etc/mha/app1.cnf
    masterha_check_repl --conf=/etc/mha/app1.cnf
    ```
    两个命令都应输出 `All SSH connection tests passed successfully` 和 `MySQL Replication Health is OK.`。

2.  **启动MHA监控**：
    ```bash
    nohup masterha_manager --conf=/etc/mha/app1.cnf --remove_dead_master_conf --ignore_last_failover < /dev/null > /var/log/mha/app1/manager.log 2>&1 &
    ```

3.  **查看监控状态**：
    ```bash
    masterha_check_status --conf=/etc/mha/app1.cnf
    ```
    如果显示 `app1 (pid:XXXX) is running(0:PING_OK)`，表示MHA正在正常运行。

## 故障模拟测试

你可以通过关闭主库（192.168.0.142）的MySQL服务来模拟故障：
```bash
systemctl stop mysqld
```
观察MHA日志 (`/var/log/mha/app1/manager.log`)，你会看到它检测到主库宕机，并开始执行切换流程。稍后检查，原本的某个从库（如192.168.0.143）应该已被提升为新的主库，而另一个从库（192.168.0.144）则指向了这个新主库。

## 总结

本节课中我们一起学习了MHA高可用的完整配置流程。我们从搭建MySQL一主两从集群开始，接着配置了SSH免密登录，然后安装了MHA的Node和Manager组件，并详细讲解了核心配置文件的编写。最后，我们启动了MHA服务并了解了如何进行故障模拟测试。

![](img/d02fb7054781fae638e64b285b45d22f_54.png)

通过MHA，我们实现了数据库集群的自动故障转移，极大地提高了系统的可用性和可靠性。记住，高可用是生产环境数据库架构中不可或缺的一环。