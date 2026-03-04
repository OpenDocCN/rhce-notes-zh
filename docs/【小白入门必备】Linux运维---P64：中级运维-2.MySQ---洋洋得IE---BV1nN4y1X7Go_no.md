# Linux运维进阶：P64：MySQL介绍与安装（中）

![](img/62417b8039286ae1409167cd6d9f4ad2_1.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_3.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_5.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_7.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_9.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_11.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_13.png)

在本节课中，我们将学习MySQL的两种主要安装方式：YUM安装和源码编译安装。我们将了解它们各自的优缺点、具体安装步骤以及安装后的基本配置。

![](img/62417b8039286ae1409167cd6d9f4ad2_15.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_17.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_19.png)

## YUM安装MySQL

![](img/62417b8039286ae1409167cd6d9f4ad2_21.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_23.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_25.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_27.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_29.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_31.png)

上一节我们介绍了MySQL的背景知识，本节中我们来看看如何使用YUM包管理器来安装MySQL。YUM安装方式简单快捷，适合快速部署标准环境。

![](img/62417b8039286ae1409167cd6d9f4ad2_33.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_35.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_37.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_39.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_41.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_43.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_44.png)

### 安装前准备

![](img/62417b8039286ae1409167cd6d9f4ad2_46.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_48.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_50.png)

以下是安装前需要执行的步骤，主要是配置MySQL的YUM源。

![](img/62417b8039286ae1409167cd6d9f4ad2_52.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_54.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_56.png)

1.  下载MySQL官方的YUM源RPM包。
    ```bash
    wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
    ```
2.  安装下载的RPM包，此操作会为系统添加MySQL的YUM仓库。
    ```bash
    yum -y install mysql57-community-release-el7-11.noarch.rpm
    ```
3.  验证YUM源是否添加成功，检查 `/etc/yum.repos.d/` 目录下是否存在 `mysql-community.repo` 等文件。
4.  导入MySQL的GPG密钥，用于验证软件包。
    ```bash
    rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
    ```

![](img/62417b8039286ae1409167cd6d9f4ad2_58.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_60.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_62.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_64.png)

### 执行安装与初始化

![](img/62417b8039286ae1409167cd6d9f4ad2_66.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_68.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_70.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_72.png)

完成源配置后，即可开始安装MySQL服务器软件。

![](img/62417b8039286ae1409167cd6d9f4ad2_74.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_76.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_77.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_79.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_81.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_83.png)

1.  使用YUM安装MySQL社区版服务器。
    ```bash
    yum -y install mysql-community-server
    ```
2.  安装完成后，启动MySQL服务并设置为开机自启。
    ```bash
    systemctl start mysqld
    systemctl enable mysqld
    ```
3.  MySQL 5.7版本在安装后会为root用户生成一个临时随机密码。密码存储在日志文件中，使用以下命令查看。
    ```bash
    grep 'temporary password' /var/log/mysqld.log
    ```
4.  使用查看到的密码登录MySQL。
    ```bash
    mysql -u root -p
    ```
5.  首次登录后，必须修改root用户的密码。MySQL 5.7有密码强度策略，要求密码包含大小写字母、数字和特殊字符，且长度至少8位。
    ```sql
    ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
    ```

![](img/62417b8039286ae1409167cd6d9f4ad2_85.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_87.png)

> **注意**：YUM/RPM安装的MySQL，其安装路径、数据目录等都是固定的，且默认字符集可能不支持中文，需要额外配置。

![](img/62417b8039286ae1409167cd6d9f4ad2_89.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_91.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_93.png)

## 源码编译安装MySQL

![](img/62417b8039286ae1409167cd6d9f4ad2_95.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_97.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_99.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_101.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_103.png)

了解了便捷的YUM安装后，本节我们来看看更灵活但步骤稍复杂的源码编译安装。这种方式允许我们完全自定义安装路径、功能模块和系统配置（如字符集），适合对环境有特定要求的场景。

![](img/62417b8039286ae1409167cd6d9f4ad2_105.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_107.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_109.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_111.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_113.png)

### 安装前环境准备

![](img/62417b8039286ae1409167cd6d9f4ad2_115.png)

源码编译需要一些开发工具和库文件的支持，以下是需要提前安装的依赖包。

![](img/62417b8039286ae1409167cd6d9f4ad2_117.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_119.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_121.png)

```bash
yum -y install gcc gcc-c++ cmake ncurses-devel openssl-devel
```

![](img/62417b8039286ae1409167cd6d9f4ad2_123.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_125.png)

### 解决软件冲突

![](img/62417b8039286ae1409167cd6d9f4ad2_127.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_129.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_131.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_133.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_135.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_137.png)

在安装MySQL前，必须确保系统没有安装与其冲突的 `mariadb-libs` 包。如果存在，需要先卸载它。

![](img/62417b8039286ae1409167cd6d9f4ad2_139.png)

```bash
rpm -e --nodeps mariadb-libs
```

![](img/62417b8039286ae1409167cd6d9f4ad2_141.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_143.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_145.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_147.png)

### 编译与安装步骤

![](img/62417b8039286ae1409167cd6d9f4ad2_149.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_151.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_153.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_155.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_157.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_159.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_161.png)

源码安装主要分为配置、编译、安装三个核心步骤。

![](img/62417b8039286ae1409167cd6d9f4ad2_163.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_165.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_167.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_169.png)

1.  **解压源码包**：将下载的MySQL源码压缩包解压。
    ```bash
    tar -zxvf mysql-5.7.37.tar.gz
    cd mysql-5.7.37
    ```
2.  **配置编译参数**：使用 `cmake` 命令进行预编译配置，指定安装路径、数据目录、字符集等关键参数。这是一条完整的命令。
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
3.  **编译源码**：使用 `make` 命令进行编译，此过程耗时较长。可以使用 `-j` 参数指定CPU核心数以加速编译。
    ```bash
    make -j 4
    ```
4.  **安装软件**：将编译好的文件安装到之前配置的目录中。
    ```bash
    make install
    ```

![](img/62417b8039286ae1409167cd6d9f4ad2_171.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_173.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_175.png)

### 安装后配置

![](img/62417b8039286ae1409167cd6d9f4ad2_177.png)

软件安装到系统后，还需要进行一些目录、用户和配置文件的设置。

![](img/62417b8039286ae1409167cd6d9f4ad2_179.png)

![](img/62417b8039286ae1409167cd6d9f4ad2_181.png)

1.  **创建MySQL系统用户**：为MySQL服务创建一个专用的系统用户，提高安全性。
    ```bash
    groupadd mysql
    useradd -r -g mysql -s /bin/false mysql
    ```
2.  **创建必要目录并授权**：创建数据、日志等目录，并将所有权赋予mysql用户。
    ```bash
    mkdir -p /usr/local/mysql/data
    mkdir -p /usr/local/mysql/logs
    mkdir -p /tmp/mysql
    chown -R mysql:mysql /usr/local/mysql
    chown -R mysql:mysql /tmp/mysql
    ```
3.  **初始化数据库**：初始化MySQL的数据目录，生成系统表。
    ```bash
    cd /usr/local/mysql
    bin/mysqld --initialize-insecure --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
    ```
    > 使用 `--initialize-insecure` 参数初始化后，root用户密码为空。如需安全初始化（生成随机密码），请使用 `--initialize` 参数，并查看日志获取密码。
4.  **配置配置文件**：创建自定义的MySQL配置文件 `my.cnf`。
    ```bash
    vi /etc/my.cnf
    ```
    配置文件基础内容示例：
    ```ini
    [client]
    port = 3306
    socket = /tmp/mysql.sock

    [mysqld]
    port = 3306
    socket = /tmp/mysql.sock
    basedir = /usr/local/mysql
    datadir = /usr/local/mysql/data
    pid-file = /usr/local/mysql/data/mysql.pid
    character-set-server = utf8mb4
    collation-server = utf8mb4_general_ci
    log-error = /usr/local/mysql/logs/mysql-error.log
    ```
5.  **配置系统服务**：为了方便管理，将MySQL配置为系统服务。
    ```bash
    cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
    chmod +x /etc/init.d/mysqld
    systemctl daemon-reload
    ```
6.  **启动MySQL服务**：
    ```bash
    systemctl start mysqld
    ```
7.  **设置环境变量**：将MySQL的可执行文件路径添加到系统的PATH环境变量中。
    ```bash
    echo 'export PATH=/usr/local/mysql/bin:$PATH' >> /etc/profile
    source /etc/profile
    ```
8.  **登录并设置root密码**：
    ```bash
    mysql -u root -p # 初始密码为空，直接回车
    ```
    进入MySQL后修改密码：
    ```sql
    ALTER USER 'root'@'localhost' IDENTIFIED BY '你的新密码';
    FLUSH PRIVILEGES;
    ```

## 总结

![](img/62417b8039286ae1409167cd6d9f4ad2_183.png)

本节课中我们一起学习了MySQL的两种安装方法。YUM安装**简单快速**，适合标准环境下的快速部署；而源码安装**高度可定制**，允许我们根据需求灵活设置安装路径、字符集等参数，适合生产环境的个性化配置。请根据你的实际需求选择合适的安装方式。