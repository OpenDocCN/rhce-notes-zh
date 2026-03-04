# Linux就该这么学：第34期：第22节课：iSCSI网络存储与MariaDB数据库管理 🚀

![](img/74fc2f7232448e19de8e1a8bed1648ab_1.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_3.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_5.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_7.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_9.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_11.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_13.png)

在本节课中，我们将学习两个核心内容：如何配置iSCSI网络存储服务以实现远程硬盘共享，以及如何在Linux系统上对MariaDB数据库进行基本的管理操作。我们将从实践出发，确保初学者能够理解并掌握关键步骤。

![](img/74fc2f7232448e19de8e1a8bed1648ab_15.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_17.png)

## 概述 📋

![](img/74fc2f7232448e19de8e1a8bed1648ab_19.png)

本节课分为两个主要部分。首先，我们将学习如何在本机创建一个RAID磁盘阵列以保证数据安全，然后通过iSCSI协议将这个阵列作为网络存储设备共享给其他主机。其次，我们将学习MariaDB数据库的基本管理，包括用户创建、权限分配、数据库与表单的增删改查以及数据备份与恢复。

![](img/74fc2f7232448e19de8e1a8bed1648ab_21.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_23.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_25.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_27.png)

---

![](img/74fc2f7232448e19de8e1a8bed1648ab_29.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_31.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_33.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_35.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_37.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_39.png)

## 第一部分：iSCSI网络存储服务配置 💾

![](img/74fc2f7232448e19de8e1a8bed1648ab_41.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_43.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_45.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_47.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_49.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_51.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_53.png)

上一节我们介绍了多种数据存储方案，本节中我们来看看如何将本地存储设备通过网络进行共享。

![](img/74fc2f7232448e19de8e1a8bed1648ab_55.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_57.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_59.png)

### 实验目标与环境准备

![](img/74fc2f7232448e19de8e1a8bed1648ab_61.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_63.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_65.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_67.png)

为了保证本机数据安全，我们首先需要还原虚拟机环境，并添加四块硬盘用于创建RAID阵列。我们将创建一个RAID 10阵列，然后通过iSCSI服务将其共享出去。

![](img/74fc2f7232448e19de8e1a8bed1648ab_69.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_71.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_73.png)

以下是实验准备步骤：
1.  关闭虚拟机，添加四块大小为5GB的硬盘。
2.  启动虚拟机，进入BIOS设置，确保SATA硬盘（系统盘）的启动优先级高于SCSI硬盘。
3.  保存设置并重启系统。

![](img/74fc2f7232448e19de8e1a8bed1648ab_75.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_77.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_79.png)

### 创建RAID 10阵列

![](img/74fc2f7232448e19de8e1a8bed1648ab_81.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_83.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_85.png)

在第七章我们已经学习过RAID的创建，这里我们快速回顾一下创建RAID 10的命令。

![](img/74fc2f7232448e19de8e1a8bed1648ab_87.png)

```bash
mdadm -Cv /dev/md0 -n 4 -l 10 /dev/sd[b-e]
```
*   `-Cv`：创建阵列并显示过程。
*   `/dev/md0`：阵列设备名称。
*   `-n 4`：使用4个硬盘。
*   `-l 10`：RAID级别为10。
*   `/dev/sd[b-e]`：指定四块硬盘（sdb, sdc, sdd, sde）。

![](img/74fc2f7232448e19de8e1a8bed1648ab_89.png)

创建完成后，等待阵列同步完成。

![](img/74fc2f7232448e19de8e1a8bed1648ab_91.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_93.png)

### 安装与配置iSCSI服务端

![](img/74fc2f7232448e19de8e1a8bed1648ab_95.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_97.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_99.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_101.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_103.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_105.png)

iSCSI服务允许我们将存储设备通过网络共享。在RHEL 7/8中，我们可以使用`targetcli`工具进行交互式配置，这比手动编写配置文件简单得多。

![](img/74fc2f7232448e19de8e1a8bed1648ab_107.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_109.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_111.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_113.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_115.png)

首先，安装必要的工具：
```bash
yum install -y targetcli
```
安装完成后，启动`targetcli`交互式配置界面。

以下是配置iSCSI服务端的核心步骤列表：
1.  **添加后端存储**：进入`/backstores/block`目录，为我们创建的RAID设备（`/dev/md0`）创建一个别名（如`disk0`）。
2.  **创建iSCSI目标**：进入`/iscsi`目录，创建一个iSCSI目标（Target）。系统会生成一个唯一的名称（IQN）。
3.  **设置访问控制**：进入新创建的目标下的`acls`目录，创建一个访问控制列表条目。这里客户端的发起方名称（Initiator Name）就是“密码”，建议使用目标IQN加上后缀（如`-client`）来命名，便于管理。
4.  **关联存储与目标**：进入同一目标下的`luns`目录，创建LUN，将之前创建的存储别名（`disk0`）关联到此目标。
5.  **设置服务端监听地址**：进入`portals`目录，删除默认的`0.0.0.0:3260`（允许所有地址），然后添加服务器具体的IP地址和端口（如`192.168.10.10:3260`）。

配置完成后，输入`exit`退出`targetcli`。然后启动并启用`target`服务，并放行防火墙。
```bash
systemctl restart target
systemctl enable target
firewall-cmd --permanent --add-service=iscsi-target
firewall-cmd --reload
```

![](img/74fc2f7232448e19de8e1a8bed1648ab_117.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_119.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_121.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_123.png)

### 配置Linux客户端连接

![](img/74fc2f7232448e19de8e1a8bed1648ab_125.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_127.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_129.png)

现在，我们从另一台Linux客户端来连接并使用这个网络存储。

![](img/74fc2f7232448e19de8e1a8bed1648ab_131.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_133.png)

以下是Linux客户端的配置步骤列表：
1.  **网络连通性**：确保客户端与服务器网络互通（例如，客户端IP为`192.168.10.20`）。
2.  **修改发起方名称**：编辑`/etc/iscsi/initiatorname.iscsi`文件，将客户端的发起方名称改为服务端ACL中设置的值。
3.  **重启客户端服务**：重启`iscsid`服务使配置生效。
    ```bash
    systemctl restart iscsid
    systemctl enable iscsid
    ```
4.  **发现目标**：使用`iscsiadm`命令探测服务器上的可用共享。
    ```bash
    iscsiadm -m discovery -t st -p 192.168.10.10
    ```
5.  **登录并挂载**：登录到发现的目标，系统会自动识别出新硬盘（如`/dev/sdb`）。
    ```bash
    iscsiadm -m node -T <目标IQN> -p 192.168.10.10 --login
    ```
    之后，您可以像使用本地硬盘一样，对`/dev/sdb`进行分区、格式化和挂载。

![](img/74fc2f7232448e19de8e1a8bed1648ab_135.png)

### 配置Windows客户端连接

iSCSI是跨平台的，Windows系统也可以方便地连接。

![](img/74fc2f7232448e19de8e1a8bed1648ab_137.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_139.png)

以下是Windows客户端的连接步骤列表：
1.  打开“控制面板” -> “管理工具” -> “iSCSI发起程序”。
2.  在“配置”选项卡中，将“发起程序名称”修改为服务端ACL中设置的值。
3.  在“目标”选项卡中，输入服务器IP地址（`192.168.10.10`），点击“快速连接”。
4.  连接成功后，打开“磁盘管理”，会发现多出一块磁盘（即共享的RAID阵列）。您可以对其进行初始化、新建简单卷（分区）、格式化等操作，之后在“我的电脑”中即可使用。

**注意**：iSCSI存储同一时间通常只允许一个客户端挂载使用，多客户端同时挂载可能引发数据冲突。

![](img/74fc2f7232448e19de8e1a8bed1648ab_141.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_143.png)

当需要断开连接时，在Windows的“iSCSI发起程序”中“断开”或“删除”目标即可。

![](img/74fc2f7232448e19de8e1a8bed1648ab_145.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_147.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_149.png)

---

![](img/74fc2f7232448e19de8e1a8bed1648ab_151.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_153.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_155.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_157.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_159.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_160.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_162.png)

## 第二部分：MariaDB数据库管理 🗄️

![](img/74fc2f7232448e19de8e1a8bed1648ab_164.png)

上一节我们实现了网络存储的共享，本节中我们来看看如何在Linux系统上进行基本的数据库管理。虽然RHCE考试已不再考察数据库配置，但掌握这些技能对日常工作非常有帮助。

![](img/74fc2f7232448e19de8e1a8bed1648ab_166.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_168.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_170.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_172.png)

### MariaDB简介与安装

![](img/74fc2f7232448e19de8e1a8bed1648ab_174.png)

MariaDB是MySQL的一个分支，两者高度兼容。在RHEL 7/8中，它取代了MySQL成为默认的数据库管理系统。

![](img/74fc2f7232448e19de8e1a8bed1648ab_176.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_178.png)

安装MariaDB服务器与客户端：
```bash
yum install -y mariadb mariadb-server
```
启动并启用服务：
```bash
systemctl start mariadb
systemctl enable mariadb
```

![](img/74fc2f7232448e19de8e1a8bed1648ab_180.png)

### 数据库安全初始化

![](img/74fc2f7232448e19de8e1a8bed1648ab_182.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_184.png)

首次安装后，建议运行安全初始化脚本，设置root密码、移除匿名用户、禁止root远程登录等。
```bash
mysql_secure_installation
```
按照提示操作，请务必牢记设置的root密码。

![](img/74fc2f7232448e19de8e1a8bed1648ab_186.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_188.png)

### 基本数据库操作

![](img/74fc2f7232448e19de8e1a8bed1648ab_190.png)

使用管理员账号登录数据库：
```bash
mysql -u root -p
```
输入密码后，进入MariaDB监控器。

以下是数据库的常用基本操作列表：
*   **查看所有数据库**：`SHOW DATABASES;`
*   **创建新数据库**：`CREATE DATABASE linuxprobe;`
*   **使用（切换）数据库**：`USE linuxprobe;`
*   **查看当前数据库中的表**：`SHOW TABLES;`
*   **创建新表**：例如，创建一个名为`mybook`的表，包含书名（字符型）、价格（整型）、页数（整型）三个字段。
    ```sql
    CREATE TABLE mybook (name char(15), price int, pages int);
    ```
*   **查看表结构**：`DESCRIBE mybook;`
*   **向表中插入数据**：
    ```sql
    INSERT INTO mybook(name, price, pages) VALUES('linuxprobe1', 30, 518);
    ```
*   **查询表中所有数据**：`SELECT * FROM mybook;`
*   **条件查询**：
    *   查询价格大于40的记录：`SELECT * FROM mybook WHERE price > 40;`
    *   查询书名为‘linuxprobe2’的记录：`SELECT * FROM mybook WHERE name='linuxprobe2';`
    *   多条件查询（AND）：`SELECT * FROM mybook WHERE price > 40 AND name='linuxprobe2';`
*   **更新数据**：将价格为30的书改为35。
    ```sql
    UPDATE mybook SET price=35 WHERE price=30;
    ```
*   **删除数据**：删除表中所有数据。
    ```sql
    DELETE FROM mybook;
    ```
*   **删除表**：`DROP TABLE mybook;`
*   **删除数据库**：`DROP DATABASE linuxprobe;`
    **（慎用！）**

![](img/74fc2f7232448e19de8e1a8bed1648ab_192.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_194.png)

### 用户与权限管理

在数据库监控器中，可以执行以下用户管理操作。

以下是用户权限管理的核心命令列表：
1.  **创建新用户**：创建一个名为`xianjian`，仅允许本地登录，密码为`linuxprobe`的用户。
    ```sql
    CREATE USER 'xianjian'@'localhost' IDENTIFIED BY 'linuxprobe';
    ```
2.  **为用户授权**：授予`xianjian`用户对`linuxprobe`数据库的所有表进行增删改查的权限。
    ```sql
    GRANT SELECT,INSERT,UPDATE,DELETE ON linuxprobe.* TO 'xianjian'@'localhost';
    ```
    授权后需执行`FLUSH PRIVILEGES;`使权限立即生效（某些版本自动生效）。
3.  **撤销用户权限**：撤销`xianjian`用户对`linuxprobe`数据库的所有权限。
    ```sql
    REVOKE SELECT,INSERT,UPDATE,DELETE ON linuxprobe.* FROM 'xianjian'@'localhost';
    ```
4.  **修改用户密码（管理员操作）**：
    ```sql
    SET PASSWORD FOR 'root'@'localhost' = PASSWORD('linuxprobe');
    ```

![](img/74fc2f7232448e19de8e1a8bed1648ab_196.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_198.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_200.png)

![](img/74fc2f7232448e19de8e1a8bed1648ab_202.png)

### 数据库备份与恢复

备份与恢复是运维工作中的重要环节。

以下是数据库备份与恢复的操作列表：
*   **备份单个数据库**：使用`mysqldump`命令将`linuxprobe`数据库备份到文件。
    ```bash
    mysqldump -u root -p linuxprobe > /root/linuxprobe.dump
    ```
*   **恢复数据库**：首先创建一个空数据库（如果需要），然后使用`mysql`命令导入备份文件。
    ```bash
    mysql -u root -p linuxprobe < /root/linuxprobe.dump
    ```

---

![](img/74fc2f7232448e19de8e1a8bed1648ab_204.png)

## 总结 🎯

![](img/74fc2f7232448e19de8e1a8bed1648ab_206.png)

本节课中我们一起学习了两个重要的服务。在第一部分，我们掌握了iSCSI网络存储服务的配置，从创建本地RAID阵列，到使用`targetcli`配置服务端，最后在Linux和Windows客户端成功挂载远程硬盘，实现了设备级的网络共享。在第二部分，我们学习了MariaDB数据库的基本管理，包括数据库、表单的增删改查操作，用户账号与权限的配置，以及数据的备份与恢复方法。这些技能将帮助您在日常运维工作中更有效地管理存储资源和数据库服务。