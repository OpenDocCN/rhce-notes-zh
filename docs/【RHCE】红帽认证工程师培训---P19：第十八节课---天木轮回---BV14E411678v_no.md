# RHCE红帽认证工程师培训课程：P19：第十八节课：数据库管理与无人值守安装系统

![](img/51b9e5527bb7a7007e63ef17734d18e7_1.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_2.png)

## 概述

![](img/51b9e5527bb7a7007e63ef17734d18e7_4.png)

在本节课中，我们将学习两个核心主题。首先，我们将了解MariaDB数据库的基本管理操作，包括用户管理、权限控制以及数据的增删改查。其次，我们将深入探讨如何利用PXE和Kickstart技术实现Linux系统的无人值守批量安装，这是一个在企业运维中非常实用的自动化部署技能。

![](img/51b9e5527bb7a7007e63ef17734d18e7_6.png)

---

## MariaDB数据库入门

上一节我们介绍了Linux系统中的各种服务，本节中我们来看看如何管理一个数据库系统——MariaDB。

### MariaDB的背景与意义

![](img/51b9e5527bb7a7007e63ef17734d18e7_8.png)

MariaDB是MySQL数据库的一个分支，由MySQL的原班开发团队创建。其诞生与Oracle公司收购MySQL后的商业策略变化有关。许多大型厂商，如Google、维基百科和红帽公司，都转而支持MariaDB，这既是对开源社区的支持，也避免了单一供应商带来的技术风险。红帽认证考试中包含MariaDB内容，也表明了红帽对其的官方支持态度。

对于初学者而言，学习MariaDB的好处在于其与MySQL高度兼容，掌握了MariaDB也就基本掌握了MySQL的操作。

### 安装与初始化MariaDB

![](img/51b9e5527bb7a7007e63ef17734d18e7_10.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_12.png)

首先，我们需要在系统上安装MariaDB服务器和客户端软件包。

```bash
yum install -y mariadb mariadb-server
```

![](img/51b9e5527bb7a7007e63ef17734d18e7_14.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_15.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_17.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_18.png)

安装完成后，启动服务并将其设置为开机自启。

```bash
systemctl start mariadb
systemctl enable mariadb
```

接着，执行安全初始化脚本。这个脚本会引导你进行一些安全设置，例如设置root密码、移除匿名用户、禁止root远程登录等。

```bash
mysql_secure_installation
```

### 基本数据库操作

初始化完成后，我们可以使用root用户登录数据库。

```bash
mysql -u root -p
```

以下是数据库的一些基本操作命令，请注意，每条SQL命令都需要以分号 `;` 结尾。

**查看所有数据库：**
```sql
SHOW DATABASES;
```

**创建新数据库：**
```sql
CREATE DATABASE linuxprobe;
```

**使用（切换）数据库：**
```sql
USE linuxprobe;
```

### 用户与权限管理

在工作中，直接使用root用户操作数据库是不安全的。我们应该创建普通用户并授予其最小必要权限。

**创建新用户：**
以下命令创建一个名为 `xiaozhu` 的用户，其密码为 `linuxprobe`，并限制其只能从本地主机登录。
```sql
CREATE USER ‘xiaozhu‘@‘localhost‘ IDENTIFIED BY ‘linuxprobe‘;
```

新创建的用户默认没有任何权限。我们需要为其授权。

![](img/51b9e5527bb7a7007e63ef17734d18e7_20.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_21.png)

**授予用户权限：**
以下命令授予 `xiaozhu` 用户对 `linuxprobe` 数据库的所有表进行查询(`SELECT`)、更新(`UPDATE`)、删除(`DELETE`)、插入(`INSERT`)的权限。
```sql
GRANT SELECT, UPDATE, DELETE, INSERT ON linuxprobe.* TO ‘xiaozhu‘@‘localhost‘;
```

![](img/51b9e5527bb7a7007e63ef17734d18e7_23.png)

授权后，需要刷新权限使其立即生效。
```sql
FLUSH PRIVILEGES;
```

![](img/51b9e5527bb7a7007e63ef17734d18e7_25.png)

**撤销用户权限：**
如果需要收回权限，可以使用 `REVOKE` 命令。
```sql
REVOKE SELECT, UPDATE, DELETE, INSERT ON linuxprobe.* FROM ‘xiaozhu‘@‘localhost‘;
```

### 表单与数据操作

数据库用于存储数据，数据被组织在“表”中。我们可以把数据库想象成一个文件柜，而表就是文件柜里的一个个文件夹。

![](img/51b9e5527bb7a7007e63ef17734d18e7_27.png)

**创建数据表：**
以下命令在 `linuxprobe` 数据库中创建一张名为 `mybook` 的表，包含书名(`name`)、价格(`price`)和页数(`pages`)三个字段。
```sql
CREATE TABLE mybook (name char(15), price int, pages int);
```

![](img/51b9e5527bb7a7007e63ef17734d18e7_29.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_31.png)

**描述表结构：**
```sql
DESC mybook;
```

**向表中插入数据：**
```sql
INSERT INTO mybook(name, price, pages) VALUES(‘linuxprobe1‘, 60, 518);
```

![](img/51b9e5527bb7a7007e63ef17734d18e7_33.png)

**查询表中数据：**
查询所有数据：
```sql
SELECT * FROM mybook;
```
按条件查询（例如，价格大于75元的书）：
```sql
SELECT * FROM mybook WHERE price > 75;
```

![](img/51b9e5527bb7a7007e63ef17734d18e7_35.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_36.png)

**更新表中数据：**
将书名为 `linuxprobe1` 的价格改为55。
```sql
UPDATE mybook SET price=55 WHERE name=‘linuxprobe1‘;
```

![](img/51b9e5527bb7a7007e63ef17734d18e7_38.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_40.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_41.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_42.png)

**删除表中数据：**
删除所有数据：
```sql
DELETE FROM mybook;
```
按条件删除（例如，删除价格为80的记录）：
```sql
DELETE FROM mybook WHERE price=80;
```

### 数据库的备份与恢复

![](img/51b9e5527bb7a7007e63ef17734d18e7_44.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_45.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_46.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_47.png)

定期备份数据库是运维工作的重要一环。

![](img/51b9e5527bb7a7007e63ef17734d18e7_49.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_51.png)

**备份数据库：**
使用 `mysqldump` 命令备份 `linuxprobe` 数据库到文件 `backup.sql`。
```bash
mysqldump -u root -p linuxprobe > backup.sql
```

**恢复数据库：**
首先，如果需要，创建一个空的数据库。
```sql
CREATE DATABASE linuxprobe;
```
然后，使用备份文件进行恢复。
```bash
mysql -u root -p linuxprobe < backup.sql
```

![](img/51b9e5527bb7a7007e63ef17734d18e7_53.png)

---

![](img/51b9e5527bb7a7007e63ef17734d18e7_55.png)

## 无人值守安装系统（PXE + Kickstart）

![](img/51b9e5527bb7a7007e63ef17734d18e7_57.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_59.png)

在掌握了数据库的基本管理后，我们来看一个更贴近企业实际运维的场景：如何批量、自动化地安装数十甚至上百台服务器系统。手动为每台机器插U盘或光盘显然效率低下，而PXE（预启动执行环境）配合Kickstart（自动应答文件）技术可以完美解决这个问题。

![](img/51b9e5527bb7a7007e63ef17734d18e7_61.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_62.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_63.png)

### 工作原理与组件

![](img/51b9e5527bb7a7007e63ef17734d18e7_65.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_67.png)

PXE允许计算机通过网络从远端服务器下载引导镜像并启动。Kickstart则通过一个预先编写好的应答文件，自动完成安装过程中所有需要人工交互的步骤（如分区、设置密码、选择软件包等）。

![](img/51b9e5527bb7a7007e63ef17734d18e7_69.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_71.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_73.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_75.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_77.png)

实现整个流程，需要多个服务协同工作：
1.  **DHCP服务**：为待安装的客户机分配IP地址，并告知其下一步从哪里获取引导文件。
2.  **TFTP服务**：一种简单的文件传输协议，用于向客户机传送引导镜像、内核文件等小文件。
3.  **VSFTPD/HTTPD服务**：用于存放完整的系统安装镜像（ISO内容），供客户机在安装过程中下载软件包。
4.  **Kickstart应答文件**：一个包含了系统安装所有配置的脚本文件。

![](img/51b9e5527bb7a7007e63ef17734d18e7_79.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_81.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_83.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_84.png)

### 配置步骤详解

![](img/51b9e5527bb7a7007e63ef17734d18e7_86.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_88.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_90.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_92.png)

以下是在一台服务器上配置PXE+Kickstart的完整步骤。

![](img/51b9e5527bb7a7007e63ef17734d18e7_94.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_96.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_98.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_100.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_101.png)

**第1步：安装并配置DHCP服务**
DHCP服务为客户机提供网络配置和引导信息。
```bash
yum install -y dhcp
```
编辑DHCP配置文件 `/etc/dhcp/dhcpd.conf`：
```bash
subnet 192.168.10.0 netmask 255.255.255.0 {
  range 192.168.10.100 192.168.10.200;
  option subnet-mask 255.255.255.0;
  option routers 192.168.10.1;
  default-lease-time 21600;
  max-lease-time 43200;
  next-server 192.168.10.10; # 指定TFTP服务器地址
  filename “pxelinux.0“; # 指定引导文件名
}
```
启动DHCP服务：
```bash
systemctl start dhcpd
systemctl enable dhcpd
```

![](img/51b9e5527bb7a7007e63ef17734d18e7_103.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_105.png)

**第2步：配置TFTP服务**
TFTP服务用于传输启动所需的初始文件。
```bash
yum install -y tftp-server xinetd
```
编辑 `/etc/xinetd.d/tftp`，将 `disable = yes` 改为 `disable = no`。
启动相关服务：
```bash
systemctl restart xinetd
systemctl enable xinetd
```

![](img/51b9e5527bb7a7007e63ef17734d18e7_107.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_109.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_110.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_112.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_114.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_116.png)

**第3步：准备引导文件**
安装 `syslinux` 包，它提供了PXE引导文件。
```bash
yum install -y syslinux
```
将必要的引导文件复制到TFTP默认目录：
```bash
cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/
cp /media/cdrom/images/pxeboot/{vmlinuz,initrd.img} /var/lib/tftpboot/
cp /media/cdrom/isolinux/{vesamenu.c32,boot.msg} /var/lib/tftpboot/
```
创建引导菜单目录和配置文件：
```bash
mkdir /var/lib/tftpboot/pxelinux.cfg
cp /media/cdrom/isolinux/isolinux.cfg /var/lib/tftpboot/pxelinux.cfg/default
```
编辑 `/var/lib/tftpboot/pxelinux.cfg/default` 文件，确保其指向正确的内核、镜像文件，并修改默认启动项为自动安装。

![](img/51b9e5527bb7a7007e63ef17734d18e7_118.png)

**第4步：配置文件共享服务（以VSFTPD为例）**
我们需要让客户机能访问完整的系统安装文件。
```bash
yum install -y vsftpd
cp -r /media/cdrom/* /var/ftp/ # 将光盘镜像全部复制到FTP目录
systemctl start vsftpd
systemctl enable vsftpd
```
**重要：** 必须配置防火墙，允许FTP服务。
```bash
firewall-cmd --permanent --add-service=ftp
firewall-cmd --reload
```

![](img/51b9e5527bb7a7007e63ef17734d18e7_120.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_122.png)

**第5步：创建Kickstart应答文件**
Kickstart文件定义了自动安装的细节。我们可以从系统模板开始修改。
```bash
cp ~/anaconda-ks.cfg /var/ftp/ks.cfg
chmod +r /var/ftp/ks.cfg
```
编辑 `/var/ftp/ks.cfg`，关键修改项包括：
-   `url --url=`：指定安装源路径，例如 `ftp://192.168.10.10`
-   `rootpw`：设置root密码（建议使用加密密码）。
-   `timezone`：设置时区，例如 `Asia/Shanghai`。
-   `clearpart --all --initlabel`：清空所有磁盘分区。
-   `%packages`：指定要安装的软件包组，例如 `@base`。

![](img/51b9e5527bb7a7007e63ef17734d18e7_124.png)

**第6步：修改引导菜单指向Kickstart文件**
最后，编辑TFTP引导菜单文件 `/var/lib/tftpboot/pxelinux.cfg/default`，在内核启动参数 `append` 一行末尾添加：
```
ks=ftp://192.168.10.10/ks.cfg
```

### 启动客户机进行安装

![](img/51b9e5527bb7a7007e63ef17734d18e7_126.png)

![](img/51b9e5527bb7a7007e63ef17734d18e7_127.png)

完成所有配置后，启动一台新的虚拟机或物理机，将其网卡设置为与PXE服务器在同一网络，并设置为网络引导（PXE Boot）。客户机将自动从网络获取IP，加载引导文件，并根据Kickstart文件的配置，完全无人值守地完成整个Red Hat Enterprise Linux系统的安装。

![](img/51b9e5527bb7a7007e63ef17734d18e7_129.png)

---

## 总结

![](img/51b9e5527bb7a7007e63ef17734d18e7_131.png)

本节课中我们一起学习了两个重要的运维技能。首先，我们掌握了MariaDB数据库的基础管理，包括安装、用户授权以及数据的增删改查和备份恢复。其次，我们深入探讨了PXE和Kickstart技术，实现了Linux系统的全自动批量部署。这套组合拳将DHCP、TFTP、FTP等多个服务串联起来，解决了大规模系统安装的效率瓶颈，是运维工程师必须掌握的自动化利器。请务必动手实践整个流程，理解每个服务的作用和配置细节。