# MySQL入门教程：P65：MySQL介绍及安装（下） 🛠️

在本节课中，我们将继续学习MySQL的安装与配置，特别是源码安装方式。我们将创建必要的目录和用户，编辑配置文件，初始化数据库，并学习一些基础的MySQL操作命令。

![](img/887c93e55e56bae301e6e5b362889dea_1.png)

![](img/887c93e55e56bae301e6e5b362889dea_3.png)

---

## 创建用户与目录

![](img/887c93e55e56bae301e6e5b362889dea_5.png)

![](img/887c93e55e56bae301e6e5b362889dea_7.png)

![](img/887c93e55e56bae301e6e5b362889dea_9.png)

![](img/887c93e55e56bae301e6e5b362889dea_11.png)

上一节我们介绍了MySQL的配置文件，本节中我们来看看如何为源码安装的MySQL创建专用的系统用户和目录结构。

![](img/887c93e55e56bae301e6e5b362889dea_13.png)

![](img/887c93e55e56bae301e6e5b362889dea_15.png)

以下是创建用户和目录的步骤：

1.  首先，创建一个名为`mysql`的用户组。
    ```bash
    groupadd mysql
    ```
2.  接着，创建一个名为`mysql`的用户，并将其加入到`mysql`组中。
    ```bash
    useradd -g mysql mysql
    ```
3.  切换到MySQL的安装路径（例如 `/usr/local/mysql`），并创建数据、配置、日志等目录。
    ```bash
    cd /usr/local/mysql
    mkdir -p /usr/local/mysql/data
    mkdir -p /usr/local/mysql/tmp
    mkdir -p /usr/local/mysql/etc
    mkdir -p /usr/local/mysql/logs
    ```
4.  将上述目录的所有权赋予`mysql`用户和组。
    ```bash
    chown -R mysql:mysql /usr/local/mysql
    ```

![](img/887c93e55e56bae301e6e5b362889dea_17.png)

![](img/887c93e55e56bae301e6e5b362889dea_19.png)

---

![](img/887c93e55e56bae301e6e5b362889dea_21.png)

![](img/887c93e55e56bae301e6e5b362889dea_23.png)

## 编辑配置文件

![](img/887c93e55e56bae301e6e5b362889dea_25.png)

![](img/887c93e55e56bae301e6e5b362889dea_27.png)

创建好目录后，我们需要编辑MySQL的配置文件`my.cnf`来指定这些路径。

配置文件位于 `/usr/local/mysql/etc/my.cnf`。由于是新建的，文件是空的。你需要将配置内容复制进去。**请注意**：从PDF复制时，可能会因格式问题导致换行错误，如果行首出现中文字符，需要将其注释掉（在行首加`#`）或删除，否则会导致MySQL启动失败。

![](img/887c93e55e56bae301e6e5b362889dea_29.png)

一个基础的配置文件示例如下（路径需根据你的安装位置调整）：
```ini
[client]
port = 3306
socket = /usr/local/mysql/tmp/mysql.sock

![](img/887c93e55e56bae301e6e5b362889dea_31.png)

![](img/887c93e55e56bae301e6e5b362889dea_33.png)

![](img/887c93e55e56bae301e6e5b362889dea_35.png)

[mysqld]
port = 3306
socket = /usr/local/mysql/tmp/mysql.sock
basedir = /usr/local/mysql
datadir = /usr/local/mysql/data
pid-file = /usr/local/mysql/data/mysql.pid
log-error = /usr/local/mysql/logs/error.log
```

![](img/887c93e55e56bae301e6e5b362889dea_37.png)

编辑完成后，保存退出。

![](img/887c93e55e56bae301e6e5b362889dea_39.png)

![](img/887c93e55e56bae301e6e5b362889dea_41.png)

![](img/887c93e55e56bae301e6e5b362889dea_43.png)

---

## 初始化数据库

配置文件就绪后，下一步是初始化MySQL数据库。初始化过程会根据配置文件，向数据目录中导入系统必需的数据。

初始化命令需要在MySQL的`bin`目录下执行：
```bash
cd /usr/local/mysql
./bin/mysqld --defaults-file=/usr/local/mysql/etc/my.cnf --initialize --user=mysql
```
**重要提示**：初始化命令通常只能成功执行一次。如果需要重新初始化，必须先彻底删除之前创建的数据目录（如`data`、`logs`等）下的所有内容。

![](img/887c93e55e56bae301e6e5b362889dea_45.png)

![](img/887c93e55e56bae301e6e5b362889dea_47.png)

---

## 启动MySQL服务

初始化成功后，便可以启动MySQL了。为了方便管理，我们将其添加到系统服务中。

以下是启动和配置系统服务的步骤：

1.  **设置环境变量**：编辑`/etc/profile`文件，在末尾添加以下内容，声明MySQL的安装路径和命令路径。
    ```bash
    export MYSQL_HOME=/usr/local/mysql
    export PATH=$PATH:$MYSQL_HOME/bin
    ```
    然后使环境变量生效：
    ```bash
    source /etc/profile
    ```

![](img/887c93e55e56bae301e6e5b362889dea_49.png)

![](img/887c93e55e56bae301e6e5b362889dea_51.png)

2.  **添加系统服务**：将MySQL的启动脚本复制到系统服务目录。
    ```bash
    cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
    chkconfig --add mysqld
    systemctl daemon-reload
    ```

3.  **启动MySQL**：现在可以使用systemctl命令来管理MySQL服务了。
    ```bash
    systemctl start mysqld
    systemctl status mysqld
    ```
    如果启动失败，最常见的原因是配置文件`my.cnf`存在语法错误，请仔细检查。

---

## 获取并修改初始密码

![](img/887c93e55e56bae301e6e5b362889dea_53.png)

MySQL启动后，我们需要登录并修改初始密码。对于源码安装，初始密码记录在错误日志中。

![](img/887c93e55e56bae301e6e5b362889dea_55.png)

1.  查看初始密码：
    ```bash
    grep ‘temporary password‘ /usr/local/mysql/logs/error.log
    ```
2.  使用查到的密码登录MySQL：
    ```bash
    mysql -u root -p
    ```
3.  登录后，立即修改`root`用户密码：
    ```sql
    SET PASSWORD = PASSWORD(‘你的新密码‘);
    ```
    **注意**：源码安装的MySQL默认没有密码复杂度要求，但使用`PASSWORD()`函数加密密码是更安全的做法。

![](img/887c93e55e56bae301e6e5b362889dea_57.png)

![](img/887c93e55e56bae301e6e5b362889dea_59.png)

---

## 基础MySQL命令入门

![](img/887c93e55e56bae301e6e5b362889dea_61.png)

成功进入MySQL命令行后，我们来学习几个最基础的命令。

![](img/887c93e55e56bae301e6e5b362889dea_63.png)

以下是几个必须掌握的基础操作命令：

*   **查看数据库**：显示当前MySQL服务器中的所有数据库。
    ```sql
    SHOW DATABASES;
    ```
*   **切换数据库**：进入指定的数据库，以便对其进行操作。
    ```sql
    USE 数据库名;
    ```
*   **查看当前数据库中的表**：显示当前数据库中的所有数据表。
    ```sql
    SHOW TABLES;
    ```
*   **创建数据库**：创建一个新的数据库。
    ```sql
    CREATE DATABASE 数据库名;
    ```
*   **创建数据表**：在当前数据库中创建一个新表。创建时必须至少定义一列（字段）。
    ```sql
    CREATE TABLE 表名 (列名 数据类型);
    ```
    例如，创建一个只有`id`列（整数类型）的表：
    ```sql
    CREATE TABLE test (id INT);
    ```
*   **查看表结构**：查看指定表的字段名、数据类型等结构信息。
    ```sql
    DESC 表名;
    ```
*   **向表中插入数据**：向指定表中插入一行数据。
    ```sql
    INSERT INTO 表名 VALUES (值1, 值2, ...);
    ```
    例如，向`test`表插入一个数字`1`：
    ```sql
    INSERT INTO test VALUES (1);
    ```
*   **查询表中数据**：查看指定表中的所有数据。
    ```sql
    SELECT * FROM 表名;
    ```

![](img/887c93e55e56bae301e6e5b362889dea_65.png)

---

## 总结

![](img/887c93e55e56bae301e6e5b362889dea_67.png)

本节课中我们一起学习了MySQL源码安装的后续步骤，包括创建用户和目录、编辑配置文件、初始化并启动数据库服务。我们还首次进入了MySQL命令行，学习了`SHOW`、`USE`、`CREATE`、`INSERT`、`SELECT`等最基础的SQL命令，为后续深入学习数据库操作打下了坚实的基础。请务必完成安装实践，并熟悉这些基础命令。