# Linux运维教程：P63：MySQL介绍与安装（中）

![](img/efd532d711793ea7e831f3ed38b2ed86_1.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_3.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_5.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_7.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_9.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_11.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_13.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_15.png)

在本节课中，我们将学习MySQL数据库的两种主要安装方式：YUM安装和源码编译安装。我们将了解它们之间的区别、各自的优缺点以及详细的安装步骤。

![](img/efd532d711793ea7e831f3ed38b2ed86_17.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_19.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_21.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_23.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_25.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_27.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_28.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_30.png)

## 软件冲突与安装准备

![](img/efd532d711793ea7e831f3ed38b2ed86_32.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_34.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_36.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_38.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_40.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_42.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_43.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_45.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_47.png)

上一节我们介绍了MySQL的基本概念，本节中我们来看看安装MySQL前需要注意的关键问题。

![](img/efd532d711793ea7e831f3ed38b2ed86_49.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_51.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_53.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_55.png)

MySQL和MariaDB是两家公司的产品，在被收购后，MySQL的创始人基于MySQL的代码重新开发了MariaDB。这意味着两者的命令基本一致，但它们是两个不同的数据库软件。

![](img/efd532d711793ea7e831f3ed38b2ed86_57.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_59.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_61.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_63.png)

**核心冲突**：MySQL和MariaDB在系统文件层面存在冲突，无法在同一系统上共存。常见的安装报错往往源于此。在安装MySQL前，必须确保系统中没有安装MariaDB的相关组件，特别是 `mariadb-libs` 文件。

![](img/efd532d711793ea7e831f3ed38b2ed86_65.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_67.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_69.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_71.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_73.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_75.png)

以下是处理冲突的步骤：
1.  检查并卸载已存在的MariaDB组件。
2.  使用命令 `rpm -e --nodeps mariadb-libs` 强制卸载冲突的库文件。

![](img/efd532d711793ea7e831f3ed38b2ed86_77.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_79.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_81.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_83.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_85.png)

## YUM安装MySQL

![](img/efd532d711793ea7e831f3ed38b2ed86_87.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_89.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_91.png)

YUM安装方式便捷，适合快速部署标准环境。但默认的YUM仓库通常不包含MySQL软件包，需要手动添加MySQL官方的YUM源。

![](img/efd532d711793ea7e831f3ed38b2ed86_93.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_95.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_97.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_99.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_101.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_102.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_104.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_106.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_108.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_109.png)

以下是YUM安装的核心步骤：
1.  **下载并安装MySQL的YUM源配置包**。此包很小（约25KB），用于在 `/etc/yum.repos.d/` 目录下创建MySQL的仓库配置文件。
    ```bash
    wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
    yum -y install mysql57-community-release-el7-11.noarch.rpm
    ```
2.  **导入GPG密钥**，用于验证软件包。
    ```bash
    rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
    ```
3.  **使用YUM安装MySQL服务器**。
    ```bash
    yum -y install mysql-community-server
    ```
4.  **启动MySQL服务并查找初始密码**。安装完成后，MySQL 5.7会生成一个随机初始密码，记录在日志文件中。
    ```bash
    systemctl start mysqld
    grep 'temporary password' /var/log/mysqld.log
    ```
5.  **使用初始密码登录并修改密码**。首次登录后，必须修改密码才能进行其他操作。
    ```bash
    mysql -uroot -p
    # 输入查到的初始密码
    SET PASSWORD = PASSWORD('你的新密码');
    ```

![](img/efd532d711793ea7e831f3ed38b2ed86_111.png)

**YUM/RPM安装的特点**：安装快速、目录固定、配置统一，但自定义程度低，且默认字符集等设置可能不符合中文环境需求。

![](img/efd532d711793ea7e831f3ed38b2ed86_113.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_115.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_117.png)

## 源码编译安装MySQL

![](img/efd532d711793ea7e831f3ed38b2ed86_119.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_121.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_123.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_125.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_127.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_128.png)

源码安装过程复杂，但灵活性极高，允许我们自定义安装路径、功能模块和系统配置（如字符集），是生产环境推荐的安装方式。

![](img/efd532d711793ea7e831f3ed38b2ed86_130.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_132.png)

### 安装流程概述

![](img/efd532d711793ea7e831f3ed38b2ed86_134.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_136.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_138.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_140.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_141.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_143.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_145.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_147.png)

源码安装主要分为三个步骤：配置（configure）、编译（make）、安装（make install）。这类似于从零开始构建软件。

![](img/efd532d711793ea7e831f3ed38b2ed86_149.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_151.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_153.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_155.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_157.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_159.png)

### 详细安装步骤

![](img/efd532d711793ea7e831f3ed38b2ed86_161.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_163.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_165.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_167.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_169.png)

1.  **安装编译工具与环境**。源码编译需要相关的编译器及依赖库。
    ```bash
    yum -y install gcc gcc-c++ cmake ncurses-devel openssl-devel
    ```
2.  **解压源码包并进入目录**。
    ```bash
    tar -zxvf mysql-5.7.37.tar.gz
    cd mysql-5.7.37
    ```
3.  **执行配置命令**。此步骤用于指定安装参数，以下是一条完整的配置命令示例：
    ```bash
    cmake . \
    -DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
    -DMYSQL_DATADIR=/usr/local/mysql/data \
    -DSYSCONFDIR=/etc \
    -DWITH_INNOBASE_STORAGE_ENGINE=1 \
    -DWITH_ARCHIVE_STORAGE_ENGINE=1 \
    -DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
    -DWITH_READLINE=1 \
    -DWITH_SSL=system \
    -DWITH_ZLIB=system \
    -DWITH_LIBWRAP=0 \
    -DENABLED_LOCAL_INFILE=1 \
    -DMYSQL_UNIX_ADDR=/tmp/mysql.sock \
    -DDEFAULT_CHARSET=utf8 \
    -DDEFAULT_COLLATION=utf8_general_ci \
    -DEXTRA_CHARSETS=all
    ```
    关键参数说明：
    *   `-DCMAKE_INSTALL_PREFIX`：安装主目录。
    *   `-DMYSQL_DATADIR`：数据库数据文件目录。
    *   `-DDEFAULT_CHARSET=utf8`：设置默认字符集为UTF-8，支持中文。
4.  **编译源码**。此过程较耗时，可使用 `-j` 参数指定CPU核心数以加速编译。
    ```bash
    make -j 4
    ```
5.  **执行安装**。
    ```bash
    make install
    ```
6.  **创建MySQL系统用户及所需目录**。
    ```bash
    useradd -s /sbin/nologin mysql
    mkdir -p /usr/local/mysql/data
    mkdir -p /etc/mysql
    mkdir -p /tmp/mysql
    mkdir -p /usr/local/mysql/logs
    chown -R mysql:mysql /usr/local/mysql
    ```
7.  **初始化数据库并修改配置文件**。复制源码包中的配置文件模板，并根据安装路径进行修改，重点确保 `datadir`, `socket`, `character-set-server=utf8` 等配置项正确。
    ```bash
    cp support-files/my-default.cnf /etc/my.cnf
    # 编辑 /etc/my.cnf 文件
    vi /etc/my.cnf
    ```
8.  **初始化数据库并启动服务**。
    ```bash
    cd /usr/local/mysql
    ./bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
    ./support-files/mysql.server start
    ```
9.  **设置环境变量并修改root密码**。将MySQL可执行文件路径加入系统PATH，并使用初始化生成的临时密码登录修改。
    ```bash
    echo 'export PATH=/usr/local/mysql/bin:$PATH' >> /etc/profile
    source /etc/profile
    mysql -uroot -p
    # 输入初始化日志中的临时密码
    SET PASSWORD = PASSWORD('你的新密码');
    ```

![](img/efd532d711793ea7e831f3ed38b2ed86_171.png)

![](img/efd532d711793ea7e831f3ed38b2ed86_173.png)

**源码安装的特点**：过程繁琐、耗时，但可以完全自定义安装路径、启用功能及系统参数（如字符集），更适合对环境有特定要求的生产部署。

![](img/efd532d711793ea7e831f3ed38b2ed86_175.png)

## 总结

![](img/efd532d711793ea7e831f3ed38b2ed86_177.png)

本节课中我们一起学习了MySQL的两种安装方式。YUM安装适合需要快速搭建标准测试环境的场景；而源码编译安装虽然步骤复杂，但提供了最大的灵活性和控制权，允许我们优化配置（如中文字符集支持），是运维工程师在生产环境中需要掌握的核心技能。请根据实际需求选择合适的安装方法。