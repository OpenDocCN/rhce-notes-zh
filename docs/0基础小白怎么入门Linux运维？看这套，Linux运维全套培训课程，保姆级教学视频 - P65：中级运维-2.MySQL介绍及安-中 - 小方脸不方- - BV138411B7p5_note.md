# Linux运维全套培训课程：P65：中级运维-2.MySQL介绍及安装-中 🗄️

![](img/667cef3e1d45e355ae907b1180046610_1.png)

![](img/667cef3e1d45e355ae907b1180046610_3.png)

![](img/667cef3e1d45e355ae907b1180046610_5.png)

![](img/667cef3e1d45e355ae907b1180046610_7.png)

![](img/667cef3e1d45e355ae907b1180046610_9.png)

在本节课中，我们将学习MySQL数据库的两种主要安装方式：YUM/RPM安装和源码编译安装。我们将详细讲解每种方法的步骤、优缺点以及安装过程中需要注意的关键点。

![](img/667cef3e1d45e355ae907b1180046610_11.png)

![](img/667cef3e1d45e355ae907b1180046610_13.png)

## 概述

![](img/667cef3e1d45e355ae907b1180046610_15.png)

上一节我们介绍了MySQL的基本概念和背景。本节中，我们来看看如何在Linux系统上实际安装MySQL。我们将重点对比YUM/RPM安装（便捷但定制性差）和源码编译安装（复杂但高度可定制）两种方法，并解决安装过程中常见的冲突问题。

![](img/667cef3e1d45e355ae907b1180046610_17.png)

![](img/667cef3e1d45e355ae907b1180046610_19.png)

![](img/667cef3e1d45e355ae907b1180046610_21.png)

![](img/667cef3e1d45e355ae907b1180046610_23.png)

![](img/667cef3e1d45e355ae907b1180046610_25.png)

## 安装方式对比与准备

![](img/667cef3e1d45e355ae907b1180046610_27.png)

![](img/667cef3e1d45e355ae907b1180046610_29.png)

![](img/667cef3e1d45e355ae907b1180046610_31.png)

MySQL的创始人后来基于MySQL代码重新开发了MariaDB。两者命令基本一致，但它们是两个不同的数据库软件。

![](img/667cef3e1d45e355ae907b1180046610_33.png)

![](img/667cef3e1d45e355ae907b1180046610_35.png)

![](img/667cef3e1d45e355ae907b1180046610_37.png)

![](img/667cef3e1d45e355ae907b1180046610_39.png)

![](img/667cef3e1d45e355ae907b1180046610_41.png)

![](img/667cef3e1d45e355ae907b1180046610_43.png)

**注意**：这两个软件在文件层面存在冲突，无法在同一系统上共存。后续安装MySQL时若遇到报错，罪魁祸首往往是系统中已存在的MariaDB相关文件。

![](img/667cef3e1d45e355ae907b1180046610_44.png)

![](img/667cef3e1d45e355ae907b1180046610_46.png)

![](img/667cef3e1d45e355ae907b1180046610_48.png)

常用的网络YUM源（如阿里云、清华大学镜像站）默认不包含MySQL的安装包。若想使用YUM安装MySQL，必须额外下载并配置MySQL官方提供的YUM源。

![](img/667cef3e1d45e355ae907b1180046610_50.png)

![](img/667cef3e1d45e355ae907b1180046610_52.png)

MySQL安装主要有三种方式：
1.  **RPM安装**：使用预编译好的`rpm`包安装。
2.  **YUM安装**：本质上是自动下载并安装RPM包。
3.  **源码安装**：下载源代码编译安装。

![](img/667cef3e1d45e355ae907b1180046610_54.png)

![](img/667cef3e1d45e355ae907b1180046610_56.png)

RPM/YUM安装的特点是**便捷快速**，软件目录、配置固定，类似于Windows中的标准安装。源码安装的特点是**速度较慢但可高度自定义**，允许用户指定安装路径、功能模块和字符集等。

![](img/667cef3e1d45e355ae907b1180046610_58.png)

![](img/667cef3e1d45e355ae907b1180046610_60.png)

## YUM/RPM安装MySQL 🛠️

![](img/667cef3e1d45e355ae907b1180046610_62.png)

![](img/667cef3e1d45e355ae907b1180046610_64.png)

以下是使用YUM安装MySQL 5.7的具体步骤。

![](img/667cef3e1d45e355ae907b1180046610_66.png)

![](img/667cef3e1d45e355ae907b1180046610_68.png)

### 步骤一：下载并安装MySQL YUM源

![](img/667cef3e1d45e355ae907b1180046610_70.png)

![](img/667cef3e1d45e355ae907b1180046610_72.png)

![](img/667cef3e1d45e355ae907b1180046610_73.png)

![](img/667cef3e1d45e355ae907b1180046610_75.png)

首先，需要从MySQL官网下载对应版本的YUM源配置包。

![](img/667cef3e1d45e355ae907b1180046610_77.png)

![](img/667cef3e1d45e355ae907b1180046610_79.png)

![](img/667cef3e1d45e355ae907b1180046610_81.png)

```bash
wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
```
> **注意**：如果系统是最小化安装，可能没有`wget`命令，需先执行 `yum -y install wget` 进行安装。

![](img/667cef3e1d45e355ae907b1180046610_83.png)

下载完成后，安装这个RPM包。这个包很小（约25KB），它并不包含MySQL软件本身，而是为系统添加一个指向MySQL官方仓库的YUM源。
```bash
yum -y install mysql57-community-release-el7-11.noarch.rpm
```
安装成功后，可以在 `/etc/yum.repos.d/` 目录下看到新增的 `mysql-community.repo` 等文件。

![](img/667cef3e1d45e355ae907b1180046610_85.png)

![](img/667cef3e1d45e355ae907b1180046610_87.png)

### 步骤二：安装MySQL服务器

![](img/667cef3e1d45e355ae907b1180046610_89.png)

![](img/667cef3e1d45e355ae907b1180046610_91.png)

在正式安装前，建议导入GPG密钥以确保软件包的完整性。
```bash
rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
```
然后使用YUM安装MySQL服务器软件包。
```bash
yum -y install mysql-community-server
```
此命令会自动解决依赖关系，安装`mysql-community-server`及其所需的组件。

![](img/667cef3e1d45e355ae907b1180046610_93.png)

![](img/667cef3e1d45e355ae907b1180046610_95.png)

![](img/667cef3e1d45e355ae907b1180046610_97.png)

![](img/667cef3e1d45e355ae907b1180046610_99.png)

![](img/667cef3e1d45e355ae907b1180046610_101.png)

### 步骤三：启动MySQL并获取初始密码

![](img/667cef3e1d45e355ae907b1180046610_103.png)

![](img/667cef3e1d45e355ae907b1180046610_104.png)

![](img/667cef3e1d45e355ae907b1180046610_106.png)

![](img/667cef3e1d45e355ae907b1180046610_108.png)

![](img/667cef3e1d45e355ae907b1180046610_110.png)

安装完成后，启动MySQL服务。
```bash
systemctl start mysqld
```
MySQL 5.7版本为了安全，在首次启动时会为`root`用户生成一个随机临时密码。该密码记录在日志文件中。
```bash
grep 'temporary password' /var/log/mysqld.log
```
输出结果类似：`A temporary password is generated for root@localhost: Jqfk&s1Ae3*x`。冒号后面的部分即为初始密码。

### 步骤四：登录MySQL并修改密码

![](img/667cef3e1d45e355ae907b1180046610_112.png)

使用获取到的初始密码登录MySQL。
```bash
mysql -u root -p
```
输入密码后即可进入MySQL命令行界面。出于安全考虑，首次登录后必须修改密码才能进行其他操作。
```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
```
> **注意**：MySQL 5.7默认的密码策略要求密码至少8位，且包含大小写字母、数字和特殊符号。可以通过修改`validate_password_policy`等参数来调整策略。

![](img/667cef3e1d45e355ae907b1180046610_114.png)

![](img/667cef3e1d45e355ae907b1180046610_116.png)

修改成功后，即可使用新密码登录。YUM/RPM安装的MySQL服务名称为 `mysqld`。

![](img/667cef3e1d45e355ae907b1180046610_118.png)

## 源码编译安装MySQL 🔧

![](img/667cef3e1d45e355ae907b1180046610_120.png)

![](img/667cef3e1d45e355ae907b1180046610_122.png)

![](img/667cef3e1d45e355ae907b1180046610_124.png)

源码安装虽然步骤繁琐，但可以完全控制安装细节，例如支持中文UTF-8编码。

![](img/667cef3e1d45e355ae907b1180046610_126.png)

![](img/667cef3e1d45e355ae907b1180046610_128.png)

![](img/667cef3e1d45e355ae907b1180046610_130.png)

![](img/667cef3e1d45e355ae907b1180046610_132.png)

### 步骤一：环境准备与清理

首先，确保系统中没有残留的MySQL或MariaDB组件。如果之前用YUM安装过，请先卸载。
```bash
yum -y remove mysql-community-server
```
检查并强制卸载可能冲突的`mariadb-libs`包。
```bash
rpm -e --nodeps mariadb-libs
```
安装编译所需的开发工具和库。
```bash
yum -y install gcc gcc-c++ cmake ncurses-devel openssl-devel
```

![](img/667cef3e1d45e355ae907b1180046610_134.png)

![](img/667cef3e1d45e355ae907b1180046610_136.png)

![](img/667cef3e1d45e355ae907b1180046610_138.png)

### 步骤二：解压源码包并编译安装

![](img/667cef3e1d45e355ae907b1180046610_140.png)

![](img/667cef3e1d45e355ae907b1180046610_142.png)

从网盘或官网下载MySQL源码包（如`mysql-5.7.37.tar.gz`），并解压。
```bash
tar -zxvf mysql-5.7.37.tar.gz -C /usr/src/
cd /usr/src/mysql-5.7.37
```
执行`cmake`进行配置预编译。这是一条完整的命令，定义了安装路径、数据目录、字符集等关键参数。
```bash
cmake . \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DMYSQL_DATADIR=/usr/local/mysql/data \
-DSYSCONFDIR=/etc \
-DWITH_MYISAM_STORAGE_ENGINE=1 \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_MEMORY_STORAGE_ENGINE=1 \
-DWITH_READLINE=1 \
-DMYSQL_UNIX_ADDR=/tmp/mysql.sock \
-DMYSQL_TCP_PORT=3306 \
-DENABLED_LOCAL_INFILE=1 \
-DWITH_PARTITION_STORAGE_ENGINE=1 \
-DEXTRA_CHARSETS=all \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci
```
配置成功后，执行编译。此过程较慢，可使用`-j`参数指定CPU核心数以加速（如`make -j 4`）。
```bash
make
```
编译完成后，执行安装。
```bash
make install
```

![](img/667cef3e1d45e355ae907b1180046610_144.png)

![](img/667cef3e1d45e355ae907b1180046610_146.png)

![](img/667cef3e1d45e355ae907b1180046610_148.png)

![](img/667cef3e1d45e355ae907b1180046610_150.png)

### 步骤三：创建用户与目录

![](img/667cef3e1d45e355ae907b1180046610_152.png)

![](img/667cef3e1d45e355ae907b1180046610_154.png)

为MySQL服务创建一个专用的系统用户和组。
```bash
groupadd mysql
useradd -r -g mysql -s /bin/false mysql
```
创建必要的目录并设置权限。
```bash
mkdir -p /usr/local/mysql/data
mkdir -p /usr/local/mysql/etc
mkdir -p /usr/local/mysql/tmp
mkdir -p /usr/local/mysql/log
chown -R mysql:mysql /usr/local/mysql
```

![](img/667cef3e1d45e355ae907b1180046610_156.png)

![](img/667cef3e1d45e355ae907b1180046610_158.png)

![](img/667cef3e1d45e355ae907b1180046610_160.png)

![](img/667cef3e1d45e355ae907b1180046610_162.png)

### 步骤四：配置文件与初始化

![](img/667cef3e1d45e355ae907b1180046610_164.png)

创建MySQL的配置文件`/etc/my.cnf`，并写入基本配置，包括安装目录、数据目录、端口、字符集和Socket文件位置等。
```ini
[client]
port = 3306
socket = /tmp/mysql.sock

![](img/667cef3e1d45e355ae907b1180046610_166.png)

[mysqld]
port = 3306
socket = /tmp/mysql.sock
basedir = /usr/local/mysql
datadir = /usr/local/mysql/data
pid-file = /usr/local/mysql/tmp/mysqld.pid
log-error = /usr/local/mysql/log/mysqld.log
character-set-server = utf8
collation-server = utf8_general_ci
```
初始化MySQL数据库。
```bash
cd /usr/local/mysql
./bin/mysqld --initialize-insecure --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
```
> **注意**：`--initialize-insecure`参数表示初始化后`root`用户密码为空。若需生成随机密码，可使用`--initialize`参数，然后去日志文件查看。

![](img/667cef3e1d45e355ae907b1180046610_168.png)

![](img/667cef3e1d45e355ae907b1180046610_170.png)

### 步骤五：启动服务与设置环境变量

![](img/667cef3e1d45e355ae907b1180046610_172.png)

将MySQL启动脚本复制到系统服务目录并启动服务。
```bash
cp support-files/mysql.server /etc/init.d/mysqld
systemctl start mysqld
```
为了方便使用`mysql`命令，可以将其添加到系统环境变量中。
```bash
echo 'export PATH=/usr/local/mysql/bin:$PATH' >> /etc/profile
source /etc/profile
```
现在，可以使用`mysql -u root -p`（密码为空）登录数据库，并执行`ALTER USER`命令设置新密码。

## 总结

本节课中我们一起学习了MySQL的两种核心安装方法。
*   **YUM/RPM安装**：适合快速部署标准环境，步骤简单，但定制性弱，可能需额外处理字符集等问题。
*   **源码编译安装**：步骤复杂耗时，但提供了最大的灵活性，可以在安装阶段就完美定制路径、功能和支持的字符集（如UTF-8），是生产环境中追求定制化和优化的常用选择。

![](img/667cef3e1d45e355ae907b1180046610_174.png)

关键点在于理解两者的区别，并根据实际需求选择合适的方法。同时，务必注意解决MySQL与MariaDB之间的软件冲突问题。