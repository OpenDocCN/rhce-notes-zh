# MySQL数据库管理：P70：中级运维-9.约束，ALTER命令，索引，SELECT查询 🗂️

在本节课中，我们将要学习MySQL中索引的创建与管理，以及SELECT查询语句的基础用法。索引是提升数据库查询效率的关键工具，而SELECT语句则是我们从数据库中检索数据的核心命令。

## 索引的创建方法

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_1.png)

上一节我们介绍了约束，本节中我们来看看如何创建索引。索引的创建主要有三种方式。

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_3.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_5.png)

### 1. 使用ALTER TABLE命令添加索引
这是在表结构创建后，修改表以添加索引的方法。
以下是具体语法：
```sql
ALTER TABLE 表名 ADD INDEX 索引名称 (字段名);
```

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_7.png)

### 2. 在创建表时添加索引
这种方法在定义表结构的同时定义索引。
以下是创建表时添加索引的语法结构：
```sql
CREATE TABLE 表名 (
    字段1 数据类型,
    字段2 数据类型,
    ...,
    INDEX 索引名称 (字段名)
);
```

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_9.png)

### 3. 创建唯一或主键索引
主键约束和唯一性约束会自动创建索引，因此通常无需单独为其创建索引。
*   主键索引随 `PRIMARY KEY` 约束自动创建。
*   唯一索引随 `UNIQUE` 约束自动创建。

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_11.png)

这三种方法创建出的索引功能上没有区别，可以根据实际情况选择使用。

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_13.png)

## 普通索引的创建与使用

现在，我们通过一个例子来具体看看普通索引的创建与查询应用。

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_15.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_17.png)

首先，我们创建一个包含普通索引的新表：
```sql
CREATE TABLE 索引表格 (
    id INT,
    name VARCHAR(20),
    INDEX name_index (name)
);
```
创建后，使用 `DESC 索引表格;` 命令查看，会在 `name` 字段的 `Key` 列看到 `MUL` 标志，这表示该字段有多个键（即普通索引）。

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_19.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_21.png)

那么，索引在查询中是如何生效的呢？索引主要用于优化带有条件的查询。当我们使用 `WHERE` 子句对建有索引的字段进行过滤时，数据库就会利用索引来快速定位数据。
例如，查询名为“张三”的记录：
```sql
SELECT * FROM 索引表格 WHERE name = ‘张三’;
```
此时，数据库会使用 `name` 字段上的索引进行查找，而不是逐行扫描整个表，从而显著提高查询速度。可以使用 `EXPLAIN` 命令来查看查询是否使用了索引以及使用了哪种类型的索引。

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_23.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_25.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_27.png)

## 特殊类型的索引

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_29.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_31.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_32.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_34.png)

除了普通索引，MySQL还支持其他几种索引类型以满足不同的查询需求。

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_36.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_38.png)

### 全文索引
全文索引专门用于对大量文本内容（`TEXT` 类型字段）进行高效的词条搜索。它要求表使用 `MyISAM` 存储引擎。
以下是创建全文索引的示例：
```sql
CREATE TABLE 文章表 (
    id INT,
    content TEXT,
    FULLTEXT idx_content (content)
) ENGINE=MyISAM;
```

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_40.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_42.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_44.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_46.png)

### 联合索引
联合索引是指对两个或以上的列组合起来创建的索引。它适用于查询条件中经常同时使用多个列的情况。
以下是创建联合索引的语法：
```sql
CREATE TABLE 联合表 (
    id INT,
    name VARCHAR(20),
    INDEX idx_id_name (id, name)
);
```
联合索引可以是普通的 (`INDEX`)、唯一的 (`UNIQUE INDEX`) 甚至是主键 (`PRIMARY KEY`)。例如，创建联合主键：
```sql
CREATE TABLE 联合主键表 (
    id INT,
    name VARCHAR(20),
    PRIMARY KEY (id, name)
);
```
要查看详细的索引信息，建议使用 `SHOW INDEX FROM 表名;` 命令。

## SELECT 基础查询入门

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_48.png)

在掌握了索引之后，我们开始学习如何使用SELECT语句从表中检索数据。SELECT是数据库查询的核心。

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_50.png)

一个最基本的SELECT语句结构如下：
```sql
SELECT 字段列表 FROM 表名 [WHERE 查询条件];
```
*   `SELECT` 后面指定要查询的列，使用 `*` 代表所有列。
*   `FROM` 后面指定要查询的表。
*   `WHERE` 是可选的，用于指定过滤数据的条件。

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_52.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_54.png)

### 基础查询示例
假设我们有一个 `students` 表，包含 `id`, `name`, `score` 等字段。
1.  查询所有学生的所有信息：
    ```sql
    SELECT * FROM students;
    ```
2.  只查询学生的ID和姓名：
    ```sql
    SELECT id, name FROM students;
    ```
3.  查询班级ID为1的学生：
    ```sql
    SELECT * FROM students WHERE class_id = 1;
    ```

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_56.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_58.png)

### WHERE子句中的条件运算符
`WHERE` 子句支持多种运算符来构建查询条件。

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_60.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_62.png)

**1. 比较运算符**
主要用于数值比较。
*   `=`：等于
*   `>`：大于
*   `<`：小于
*   `>=`：大于等于
*   `<=`：小于等于
*   `!=` 或 `<>`：不等于
示例：查询成绩大于80分的学生。
```sql
SELECT * FROM students WHERE score > 80;
```
**注意**：对字符类型字段使用比较运算符（如 `>`， `<`）通常没有实际意义，字符字段更常用 `=` 和 `!=`。

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_64.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_66.png)

**2. 范围查询 (BETWEEN AND)**
用于查询某个范围内的值，包含边界值。
示例：查询成绩在60到80分之间（含60和80）的学生。
```sql
SELECT * FROM students WHERE score BETWEEN 60 AND 80;
```
其否定形式为 `NOT BETWEEN ... AND ...`，表示不在该范围内。

## 总结与预告

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_68.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_70.png)

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_72.png)

本节课中我们一起学习了MySQL索引的多种创建方式，包括普通索引、全文索引和联合索引，并理解了索引对提升查询性能的重要性。同时，我们开始了SELECT查询语句的学习，掌握了基础查询语法和 `WHERE` 子句中比较运算符与范围查询的用法。

![](img/d91e6cb5e30c34b3a3f92c3b2e51a438_74.png)

`WHERE` 子句的功能远不止于此，它还支持集合查询 (`IN`)、模糊匹配 (`LIKE`)、空值判断 (`IS NULL`)、多重条件组合 (`AND`, `OR`) 等。在接下来的课程中，我们将继续深入SELECT查询，学习这些高级条件用法，以及排序、分组、分页和聚合函数等强大功能。请大家利用提供的示例数据表进行预习和练习。