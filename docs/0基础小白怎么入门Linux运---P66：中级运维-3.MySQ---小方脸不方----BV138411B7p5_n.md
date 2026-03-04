# Linux运维全套培训课程：P66：中级运维-3.MySQL介绍及安装-下 🛠️

在本节课中，我们将继续学习MySQL的源码安装与基础配置。我们将完成配置文件的设置、数据库的初始化、服务的启动，并学习几个最基础的MySQL操作命令。

![](img/374667def8ff2c778c20409691cecdad_1.png)

![](img/374667def8ff2c778c20409691cecdad_3.png)

---

## 配置文件详解与设置

![](img/374667def8ff2c778c20409691cecdad_5.png)

![](img/374667def8ff2c778c20409691cecdad_7.png)

![](img/374667def8ff2c778c20409691cecdad_9.png)

![](img/374667def8ff2c778c20409691cecdad_11.png)

上一节我们介绍了MySQL的基本配置文件结构。本节中，我们来看看如何具体配置并应用它。

![](img/374667def8ff2c778c20409691cecdad_13.png)

![](img/374667def8ff2c778c20409691cecdad_15.png)

以下是配置MySQL的基本步骤：

1.  **创建MySQL用户和组**：为了安全地运行MySQL服务，我们需要创建一个专用的系统用户和组。
    ```bash
    groupadd mysql
    useradd -r -g mysql -s /bin/false mysql
    ```

![](img/374667def8ff2c778c20409691cecdad_17.png)

![](img/374667def8ff2c778c20409691cecdad_19.png)

![](img/374667def8ff2c778c20409691cecdad_21.png)

2.  **创建必要的目录**：切换到MySQL的安装路径下，创建数据、日志、配置等目录。
    ```bash
    cd /usr/local/mysql
    mkdir -p /usr/local/mysql/data
    mkdir -p /usr/local/mysql/tmp
    mkdir -p /usr/local/mysql/etc
    mkdir -p /usr/local/mysql/logs
    ```

![](img/374667def8ff2c778c20409691cecdad_23.png)

3.  **设置目录权限**：将创建的目录所有权赋予`mysql`用户和组。
    ```bash
    chown -R mysql:mysql /usr/local/mysql
    ```

![](img/374667def8ff2c778c20409691cecdad_25.png)

![](img/374667def8ff2c778c20409691cecdad_27.png)

4.  **编辑配置文件**：将提供的配置文件内容复制到`/usr/local/mysql/etc/my.cnf`中。**注意**：如果从PDF复制，需检查并确保以`#`开头的注释行没有因格式问题变成独立行，否则可能导致配置文件报错。

---

![](img/374667def8ff2c778c20409691cecdad_29.png)

## 初始化与启动MySQL服务

![](img/374667def8ff2c778c20409691cecdad_31.png)

![](img/374667def8ff2c778c20409691cecdad_33.png)

![](img/374667def8ff2c778c20409691cecdad_35.png)

配置文件准备就绪后，下一步是初始化数据库并启动服务。

![](img/374667def8ff2c778c20409691cecdad_37.png)

![](img/374667def8ff2c778c20409691cecdad_39.png)

1.  **初始化数据库**：此命令会根据配置文件，向数据目录中导入初始的系统数据。**注意**：初始化命令通常只能成功执行一次，如需重新初始化，必须先清空相关数据目录。
    ```bash
    /usr/local/mysql/bin/mysqld --defaults-file=/usr/local/mysql/etc/my.cnf --initialize --user=mysql
    ```

![](img/374667def8ff2c778c20409691cecdad_41.png)

2.  **配置环境变量与服务**：为了方便使用，我们将MySQL命令路径加入系统环境变量，并将其启动脚本加入系统服务管理。
    -   编辑`/etc/profile`文件，在末尾添加：
        ```bash
        export MYSQL_HOME=/usr/local/mysql
        export PATH=$MYSQL_HOME/bin:$PATH
        ```
    -   使环境变量生效：
        ```bash
        source /etc/profile
        ```
    -   将MySQL启动脚本复制到系统服务目录并重载配置：
        ```bash
        cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
        systemctl daemon-reload
        ```

3.  **启动MySQL服务**：现在可以使用systemctl命令管理MySQL了。
    ```bash
    systemctl start mysqld
    systemctl status mysqld
    ```
    **常见问题**：如果启动失败，首要检查配置文件`my.cnf`的格式是否正确，特别是注释行是否因复制产生了错误换行。

---

![](img/374667def8ff2c778c20409691cecdad_43.png)

## 获取初始密码与修改

![](img/374667def8ff2c778c20409691cecdad_45.png)

服务启动后，我们需要登录并修改初始密码。

1.  **查找初始密码**：源码安装的MySQL，初始密码记录在错误日志文件中，路径由配置文件定义（例如`/usr/local/mysql/logs/error.log`）。
    ```bash
    grep ‘temporary password‘ /usr/local/mysql/logs/error.log
    ```

2.  **登录并修改密码**：使用查到的密码登录，并立即修改`root`用户密码。建议使用`PASSWORD()`函数进行加密，以增强安全性。
    ```bash
    mysql -u root -p
    ```
    登录后执行：
    ```sql
    SET PASSWORD = PASSWORD(‘你的新密码‘);
    ```

---

![](img/374667def8ff2c778c20409691cecdad_47.png)

## MySQL基础命令入门 🚀

![](img/374667def8ff2c778c20409691cecdad_49.png)

成功进入MySQL命令行后，我们来学习几个最基础的操作命令。

以下是初学者必须掌握的几条核心命令：

1.  **查看数据库 (SHOW DATABASES)**：显示当前MySQL服务器中的所有数据库。
    ```sql
    SHOW DATABASES;
    ```

![](img/374667def8ff2c778c20409691cecdad_51.png)

2.  **切换数据库 (USE)**：进入指定的数据库，以便后续在该库中操作。
    ```sql
    USE mysql;
    ```

![](img/374667def8ff2c778c20409691cecdad_53.png)

3.  **查看当前数据库中的表 (SHOW TABLES)**：显示当前数据库中的所有数据表。
    ```sql
    SHOW TABLES;
    ```

![](img/374667def8ff2c778c20409691cecdad_55.png)

![](img/374667def8ff2c778c20409691cecdad_57.png)

4.  **创建数据库 (CREATE DATABASE)**：创建一个新的数据库。
    ```sql
    CREATE DATABASE mydb;
    ```

5.  **创建数据表 (CREATE TABLE)**：在当前数据库中创建一张新表。创建时必须至少定义一个字段（列）及其数据类型。
    ```sql
    CREATE TABLE student (id INT);
    ```

![](img/374667def8ff2c778c20409691cecdad_59.png)

6.  **查看表结构 (DESC)**：查看指定数据表的字段名、数据类型等结构信息。
    ```sql
    DESC student;
    ```

![](img/374667def8ff2c778c20409691cecdad_61.png)

7.  **插入数据 (INSERT INTO)**：向指定的数据表中插入一行数据。
    ```sql
    INSERT INTO student VALUES (1);
    ```

8.  **查询数据 (SELECT)**：从数据表中检索数据。`*` 表示所有列。
    ```sql
    SELECT * FROM student;
    ```

![](img/374667def8ff2c778c20409691cecdad_63.png)

**重要提示**：在MySQL命令行中，每条SQL语句必须以分号 `;` 结尾才能执行。命令行不支持命令补全功能，这有助于初学者牢记命令。

---

![](img/374667def8ff2c778c20409691cecdad_65.png)

本节课中我们一起学习了MySQL源码安装后的配置、初始化与启动全过程，并初次接触了SQL语言，学会了创建库、表以及插入和查询数据的基本操作。这些是操作任何数据库的基石。下节课我们将深入讲解SQL语句的分类和更多的数据类型与约束规则。