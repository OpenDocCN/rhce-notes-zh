# Linux运维：64：MySQL介绍及安装（下）

在本节课中，我们将继续学习MySQL的源码安装过程，包括配置文件的详细设置、初始化数据库、启动服务以及学习一些最基础的MySQL操作命令。

![](img/17c513e3613263f0c52e521eb4b42bb7_1.png)

上一节我们介绍了MySQL的编译安装步骤，本节中我们来看看如何配置和启动我们安装好的MySQL服务。

![](img/17c513e3613263f0c52e521eb4b42bb7_3.png)

## 配置文件详解与准备

![](img/17c513e3613263f0c52e521eb4b42bb7_5.png)

配置文件是MySQL的核心，它决定了数据库的运行方式。我们需要创建一个自定义的配置文件。

![](img/17c513e3613263f0c52e521eb4b42bb7_6.png)

![](img/17c513e3613263f0c52e521eb4b42bb7_8.png)

以下是配置文件的几个核心部分：

![](img/17c513e3613263f0c52e521eb4b42bb7_10.png)

1.  **客户端设置**：用于配置连接MySQL时的默认参数。
    ```ini
    [client]
    port = 3306
    socket = /usr/local/mysql/data/mysql.sock
    default-character-set = utf8mb4
    ```

![](img/17c513e3613263f0c52e521eb4b42bb7_12.png)

2.  **服务器设置**：配置MySQL服务本身。
    ```ini
    [mysqld]
    port = 3306
    user = mysql
    basedir = /usr/local/mysql
    datadir = /usr/local/mysql/data
    socket = /usr/local/mysql/data/mysql.sock
    character-set-server = utf8mb4
    ```

![](img/17c513e3613263f0c52e521eb4b42bb7_14.png)

![](img/17c513e3613263f0c52e521eb4b42bb7_16.png)

3.  **存储引擎**：`innodb`是MySQL 5.7及以后版本的默认存储引擎，它提供了事务安全等高级功能。更换存储引擎就像为汽车更换了一套动力系统，会改变数据库的工作模式和特性。

![](img/17c513e3613263f0c52e521eb4b42bb7_18.png)

![](img/17c513e3613263f0c52e521eb4b42bb7_20.png)

## 创建用户与目录

![](img/17c513e3613263f0c52e521eb4b42bb7_22.png)

在启动MySQL之前，需要为其创建专用的系统用户和目录。

以下是创建用户和目录的步骤：

![](img/17c513e3613263f0c52e521eb4b42bb7_24.png)

![](img/17c513e3613263f0c52e521eb4b42bb7_26.png)

1.  创建`mysql`用户组和用户。
    ```bash
    groupadd mysql
    useradd -r -g mysql -s /bin/false mysql
    ```

![](img/17c513e3613263f0c52e521eb4b42bb7_28.png)

![](img/17c513e3613263f0c52e521eb4b42bb7_29.png)

2.  切换到MySQL安装目录，并创建必要的子目录（如数据目录`data`、日志目录`logs`等）。
    ```bash
    cd /usr/local/mysql
    mkdir data etc logs tmp
    ```

![](img/17c513e3613263f0c52e521eb4b42bb7_31.png)

![](img/17c513e3613263f0c52e521eb4b42bb7_33.png)

3.  将创建的目录所有权赋予`mysql`用户和组。
    ```bash
    chown -R mysql:mysql /usr/local/mysql
    ```

![](img/17c513e3613263f0c52e521eb4b42bb7_35.png)

## 初始化数据库

初始化过程会根据配置文件，在指定的数据目录中生成MySQL系统数据库和表。

初始化命令如下：
```bash
/usr/local/mysql/bin/mysqld --defaults-file=/usr/local/mysql/etc/my.cnf --initialize --user=mysql
```
**注意**：初始化通常只能成功执行一次。如果需要重新初始化，必须先清空数据目录（`datadir`）下的所有文件。

![](img/17c513e3613263f0c52e521eb4b42bb7_37.png)

## 配置系统服务与环境变量

![](img/17c513e3613263f0c52e521eb4b42bb7_39.png)

为了方便管理，我们将MySQL添加到系统服务中，并设置环境变量。

以下是配置步骤：

1.  **设置环境变量**：编辑`/etc/profile`文件，在末尾添加以下内容，声明MySQL的安装路径和命令路径。
    ```bash
    export MYSQL_HOME=/usr/local/mysql
    export PATH=$PATH:$MYSQL_HOME/bin
    ```
    然后执行 `source /etc/profile` 使配置生效。

2.  **添加系统服务**：将MySQL提供的启动脚本复制到系统服务目录。
    ```bash
    cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
    chkconfig --add mysqld
    systemctl daemon-reload
    ```
    现在可以使用 `systemctl start mysqld` 来启动MySQL服务。

## 获取初始密码与修改

![](img/17c513e3613263f0c52e521eb4b42bb7_41.png)

MySQL 5.7之后，初始化后会为`root`用户生成一个随机临时密码。

![](img/17c513e3613263f0c52e521eb4b42bb7_43.png)

1.  密码记录在错误日志中，根据配置的日志路径查找。
    ```bash
    grep ‘temporary password‘ /usr/local/mysql/logs/error.log
    ```

2.  使用找到的密码登录MySQL。
    ```bash
    mysql -u root -p
    ```

3.  登录后，立即修改`root`用户密码。建议使用`PASSWORD()`函数进行加密，提升安全性。
    ```sql
    SET PASSWORD = PASSWORD(‘你的新密码‘);
    ```

![](img/17c513e3613263f0c52e521eb4b42bb7_45.png)

## 基础MySQL命令入门

![](img/17c513e3613263f0c52e521eb4b42bb7_47.png)

成功登录MySQL后，我们可以开始学习一些最基础的命令。

![](img/17c513e3613263f0c52e521eb4b42bb7_49.png)

以下是几个必须掌握的基础命令：

![](img/17c513e3613263f0c52e521eb4b42bb7_51.png)

*   **查看数据库**：`SHOW DATABASES;` 命令可以列出当前MySQL服务器中的所有数据库。
*   **切换数据库**：`USE database_name;` 命令用于切换到指定的数据库进行操作。
*   **查看数据表**：在切换到某个数据库后，使用 `SHOW TABLES;` 命令可以查看该数据库中的所有表。
*   **查询表数据**：`SELECT * FROM table_name;` 命令用于查看指定表中的所有数据。如果显示混乱，可以使用 `SELECT * FROM table_name\G` 以更清晰的格式查看。
*   **查看表结构**：`DESC table_name;` 或 `DESCRIBE table_name;` 命令用于查看数据表的字段名、数据类型等结构信息。

## 创建数据库与数据表

![](img/17c513e3613263f0c52e521eb4b42bb7_53.png)

现在，我们来创建自己的数据库和数据表。

![](img/17c513e3613263f0c52e521eb4b42bb7_55.png)

1.  **创建数据库**：
    ```sql
    CREATE DATABASE mydb;
    ```

2.  **创建数据表**：创建表时必须至少定义一个字段（列），并指定其数据类型。例如，创建一个只包含一个整数类型`id`字段的表。
    ```sql
    USE mydb;
    CREATE TABLE mytable (id INT);
    ```
    这里的`INT`就是一种数据类型约束，规定`id`列只能存储整数。

![](img/17c513e3613263f0c52e521eb4b42bb7_57.png)

3.  **插入数据**：向表中插入数据使用`INSERT INTO`语句。
    ```sql
    INSERT INTO mytable VALUES (1);
    ```
    可以多次执行插入不同值。

4.  **验证数据**：使用`SELECT`命令查看插入的数据。
    ```sql
    SELECT * FROM mytable;
    ```

![](img/17c513e3613263f0c52e521eb4b42bb7_59.png)

本节课中我们一起学习了MySQL源码安装后的配置与启动，并初次接触了MySQL的基础操作命令，包括连接数据库、查看信息、创建库表以及插入数据。这些是操作MySQL的基石，请务必熟练掌握。下节课我们将深入讲解SQL语句和更多的数据类型与约束规则。