# Linux运维进阶：P76：SELECT多表查询，复制表，破解密码，授权访问，存储引擎-中

在本节课中，我们将继续深入学习MySQL数据库操作，涵盖多表查询的注意事项、表的复制方法、MySQL密码破解流程、远程访问授权配置以及存储引擎的初步介绍。这些知识是数据库管理和运维的核心技能。

![](img/53354db4a5afda305be1cb413dff778b_1.png)

![](img/53354db4a5afda305be1cb413dff778b_3.png)

上一节我们介绍了多表连接查询的基本语法，本节中我们来看看联合查询的注意事项。

![](img/53354db4a5afda305be1cb413dff778b_5.png)

## 联合查询的注意事项

联合查询（UNION）连接两个SELECT语句时，必须保证查询结果的列数相同，否则会报错。但列数相同只是最基本的要求。

例如，调整查询字段，从单列调整为两列（如ID和name），虽然列数匹配了，但数据显示可能不正常。前11行显示ID和姓名，从第12行开始却显示了班级ID和班级名称。

![](img/53354db4a5afda305be1cb413dff778b_7.png)

因此，联合查询连接的两个SELECT命令，其查询结果不仅在数量上，更要在意义上保持一致，这样的查询结果才有实际用途。否则，在数据量大的情况下，查询结果将难以理解。

以下是联合查询的最佳实践：
*   前后两个语句的列数必须相同。
*   前后两列所代表的数据含义应尽量保持一致。
*   这样查询出的内容至少是逻辑通顺的。

相比之下，全连接（FULL JOIN）或使用`UNION ALL`时，如果每个字段的数据含义都相同，查询结果就是清晰可用的。

这就是连接查询与联合查询的两种主要用法。

![](img/53354db4a5afda305be1cb413dff778b_9.png)

## 连接查询与联合查询总结

![](img/53354db4a5afda305be1cb413dff778b_11.png)

连接查询主要需注意结果集的数量和含义。而联合查询则需要区分清楚每种连接所代表的数据部分。

![](img/53354db4a5afda305be1cb413dff778b_13.png)

![](img/53354db4a5afda305be1cb413dff778b_15.png)

![](img/53354db4a5afda305be1cb413dff778b_17.png)

*   **内连接（INNER JOIN）**：查询两个表的交集部分。
*   **左外连接（LEFT OUTER JOIN）**：包含左表全部及与右表的交集。
*   **右外连接（RIGHT OUTER JOIN）**：包含右表全部及与左表的交集。
*   **全外连接（FULL OUTER JOIN）**：包含左右两表的全部数据。

![](img/53354db4a5afda305be1cb413dff778b_19.png)

在MySQL中，全外连接、左外连接和右外连接都需要使用`UNION`查询来实现，因为MySQL不支持`FULL OUTER JOIN`命令。使用联合查询时，正是因为两次查询的结果集结构一致，连接起来的数据才能正常显示，这与之前“强行联合”无意义的数据拼接不同。

正常的联合查询，可以不是同一张表，但必须保证数据结构相同。如果是同一张表，也应尽量保证列数相同。这就是多表查询部分的核心内容。

## SQL查询命令综合回顾

查询部分内容非常常用，我们用了相当长的时间讲解，包括单表查询、嵌套查询和多表连接查询。

以下是SQL查询语句各部分的综合梳理：

*   **SELECT 后面**：可以跟`*`（所有字段）、具体字段名称、函数或分组后的聚合信息。
*   **FROM 后面**：
    *   单表查询：只写一个表名。
    *   多表查询：写多个表名并使用连接条件（JOIN ... ON ...）。
    *   也可以跟一个子查询语句，该子查询需返回完整的表格结构，并且必须为其起一个别名。
*   **WHERE 后面**：条件语句，内容最丰富，是进行数据首次过滤的地方。
*   **GROUP BY**：用于分组。
*   **ORDER BY**：用于排序。
*   **LIMIT**：用于分页，可以加在任何查询语句的末尾。
*   **HAVING**：通常用在`GROUP BY`之后，对分组后的结果进行二次过滤。`WHERE`是首次过滤，`HAVING`是最终展示前的最后一次过滤，两者用法类似。
*   **多表连接**：在MySQL中主要有三种直接命令：
    *   `INNER JOIN`（内连接）
    *   `LEFT OUTER JOIN`（左外连接）
    *   `RIGHT OUTER JOIN`（右外连接）
    通过后两种的排列组合（使用`UNION`），可以衍生出其他四种连接方式。
*   **嵌套查询（子查询）**：尽量不要太冗长，最多两层即可。主要两种用法：
    *   放在`FROM`后面作为一个临时表，**必须加别名**。
    *   放在`WHERE`后面作为一个条件或数据值。

总体上，SQL查询命令多且重要，务必多加练习。

接下来，我们继续讲解其他数据库操作。

## 复制表

复制表是一个简单但实用的小命令，主要有两种用途：复制表结构和复制表数据。其中，复制表结构更为常用。

例如，当需要创建一个结构相似的新表时，重新设计字段、约束和索引需要时间。如果已有一个设计良好的表，可以先复制其结构，再简单修改，这是一种高效的方法。当然，数据也可以复制，但前提是两个表的结构必须一致。

因此，复制表通常首先复制结构，然后再考虑复制数据。

### 复制表结构

![](img/53354db4a5afda305be1cb413dff778b_21.png)

复制表结构使用`CREATE TABLE ... LIKE ...`命令。`LIKE`在这里表示“像...一样”，即参照原表的结构创建新表。

```sql
CREATE TABLE new_student LIKE students;
```
这条命令会创建一个名为`new_student`的新表，其结构与`students`表完全相同，包括字段、约束、索引、默认值、自增属性等，但不会复制任何数据。这相当于复制了存储表结构的`.frm`文件。

### 复制表数据

![](img/53354db4a5afda305be1cb413dff778b_23.png)

复制表数据使用`INSERT INTO ... SELECT ...`命令。这是`INSERT`语句的一种特殊用法。

![](img/53354db4a5afda305be1cb413dff778b_25.png)

```sql
INSERT INTO new_student SELECT * FROM students;
```
这条命令先执行`SELECT * FROM students`查询出原表所有数据，然后通过`INSERT INTO`插入到新表中。前提是新旧表的结构必须兼容。

![](img/53354db4a5afda305be1cb413dff778b_27.png)

![](img/53354db4a5afda305be1cb413dff778b_29.png)

### 快速复制表（结构与数据）

![](img/53354db4a5afda305be1cb413dff778b_31.png)

还有一种更直接的方法，通过`CREATE TABLE ... SELECT ...`一步完成表的创建和数据导入。

![](img/53354db4a5afda305be1cb413dff778b_33.png)

```sql
CREATE TABLE student_backup SELECT * FROM students;
```
但这种方法主要为了快速获取数据，在复制结构方面有缺陷。`SELECT`语句查询出的结果主要包含数据和字段名，对于主键约束、自增属性、索引等关键信息无法保留。没有索引的大表查询性能会很差。因此，**推荐使用先`LIKE`复制结构，再`INSERT`复制数据的方法**。

一个完整的表，其实只需要`CREATE`（定义结构）和`INSERT`（插入数据）这两种命令即可构成。

![](img/53354db4a5afda305be1cb413dff778b_35.png)

## 破解MySQL密码

当忘记MySQL的`root`密码时，可以在服务器本地进行破解重置。此操作需物理接触服务器，远程无法完成，因此是相对安全的。

![](img/53354db4a5afda305be1cb413dff778b_37.png)

![](img/53354db4a5afda305be1cb413dff778b_39.png)

**破解步骤：**

![](img/53354db4a5afda305be1cb413dff778b_41.png)

![](img/53354db4a5afda305be1cb413dff778b_43.png)

1.  **修改配置文件，跳过权限验证**：
    编辑MySQL的配置文件（如`/etc/my.cnf`或源码安装目录下的`/usr/local/mysql/etc/my.cnf`），在`[mysqld]`模块下添加一行：
    ```
    skip-grant-tables
    ```
    保存后重启MySQL服务。此配置项使MySQL服务启动时跳过权限验证表，实现免密登录。为方便日后使用，平时可将此行注释（前面加`#`），需要时再取消注释。

![](img/53354db4a5afda305be1cb413dff778b_45.png)

2.  **免密登录并修改密码**：
    重启后，使用`mysql -u root -p`命令，在提示输入密码时直接回车即可登录。
    登录后，通过`UPDATE`命令直接修改`mysql.user`系统表中的密码字段。**务必使用`PASSWORD()`函数对密码进行加密**。
    ```sql
    UPDATE mysql.user SET authentication_string = PASSWORD('你的新密码') WHERE User = 'root';
    ```
    **注意**：MySQL 5.7版本使用`skip-grant-tables`后，所有用户都可免密登录；MySQL 8.0后，仅`root`用户可免密登录。

3.  **恢复配置并重启**：
    密码修改成功后，退出MySQL。再次编辑配置文件，将`skip-grant-tables`一行注释掉或删除。
    保存后，再次重启MySQL服务。

4.  **使用新密码登录**：
    使用`mysql -u root -p新密码`即可正常登录。

![](img/53354db4a5afda305be1cb413dff778b_47.png)

此流程仅在首次安装后强制修改密码时需要，后续正常修改密码使用`SET PASSWORD`或`ALTER USER`命令即可。

![](img/53354db4a5afda305be1cb413dff778b_49.png)

## 授权远程访问

![](img/53354db4a5afda305be1cb413dff778b_51.png)

![](img/53354db4a5afda305be1cb413dff778b_53.png)

![](img/53354db4a5afda305be1cb413dff778b_54.png)

MySQL默认只允许本地（`localhost`）连接。若需要从其他服务器或客户端软件远程连接，必须进行授权。

![](img/53354db4a5afda305be1cb413dff778b_56.png)

![](img/53354db4a5afda305be1cb413dff778b_58.png)

![](img/53354db4a5afda305be1cb413dff778b_60.png)

![](img/53354db4a5afda305be1cb413dff778b_61.png)

授权命令格式如下：
```sql
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'客户端IP' IDENTIFIED BY '密码';
```

*   **权限列表**：如`ALL PRIVILEGES`（所有权限）、`SELECT, INSERT`等。
*   **数据库名.表名**：如`*.*`（所有库的所有表）、`test.*`（test库的所有表）。
*   **客户端IP**：
    *   `%`：代表允许任何IP地址连接。（**慎用**）
    *   `192.168.1.100`：代表只允许该特定IP连接。
    *   `192.168.1.%`：代表允许`192.168.1.0/24`网段连接。
*   **密码**：是远程连接时使用的密码，与本地登录密码可以不同。

**示例**：允许IP为`192.168.0.107`的客户端，使用用户`root`和密码`123456`连接本数据库的所有资源。
```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'192.168.0.107' IDENTIFIED BY '123456';
```
执行授权命令后，通常无需重启服务，权限即时生效（某些版本可能需要执行`FLUSH PRIVILEGES;`）。

![](img/53354db4a5afda305be1cb413dff778b_63.png)

**远程连接测试**：
在客户端机器上使用以下命令连接：
```bash
mysql -u root -p123456 -h 192.168.0.78
```

![](img/53354db4a5afda305be1cb413dff778b_65.png)

![](img/53354db4a5afda305be1cb413dff778b_67.png)

![](img/53354db4a5afda305be1cb413dff778b_69.png)

**常见问题：防火墙拦截**
如果连接失败，提示连接被拒绝，很可能是服务器防火墙拦截了MySQL默认的`3306`端口。解决方法有二：
1.  关闭防火墙（仅用于测试环境）：`systemctl stop firewalld`
2.  放行3306端口（生产环境推荐）：`firewall-cmd --add-port=3306/tcp --permanent && firewall-cmd --reload`

**注意**：授权时指定的`IP`、`用户名`和`密码`共同构成一个访问策略，可以为不同的客户端IP设置不同的用户和密码，以增强安全性。

![](img/53354db4a5afda305be1cb413dff778b_71.png)

![](img/53354db4a5afda305be1cb413dff778b_73.png)

本节课中我们一起学习了MySQL联合查询的要点、复制表的两种方式、忘记root密码后的重置流程，以及如何授权远程主机访问数据库。这些是数据库日常运维中的关键操作，需要熟练掌握。下一节，我们将继续探讨MySQL的存储引擎等重要概念。