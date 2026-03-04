# Linux运维进阶：P71：约束，ALTER命令，索引，SELECT查询-下 📚

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_1.png)

在本节课中，我们将深入学习MySQL中的索引概念、不同类型的索引创建方法，以及SELECT查询语句的基础用法。索引是提升数据库查询效率的关键工具，而SELECT语句则是我们从数据库中检索数据的核心命令。

## 索引的创建方法 🔍

上一节我们介绍了索引的基本概念，本节中我们来看看如何具体创建索引。创建索引主要有三种方式。

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_3.png)

以下是创建索引的三种主要方法：

1.  **使用 `CREATE INDEX` 命令**：在表已存在的情况下，直接为指定字段创建索引。
    ```sql
    CREATE INDEX 索引名称 ON 表名 (字段名);
    ```

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_5.png)

2.  **使用 `ALTER TABLE` 命令**：修改表结构，添加索引。
    ```sql
    ALTER TABLE 表名 ADD INDEX 索引名称 (字段名);
    ```

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_7.png)

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_9.png)

3.  **在 `CREATE TABLE` 时创建**：在创建表的同时定义索引。
    ```sql
    CREATE TABLE 表名 (
        字段1 数据类型,
        字段2 数据类型,
        ...
        INDEX 索引名称 (字段名)
    );
    ```

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_11.png)

这三种方法创建出的索引没有本质区别，可以根据实际情况选择使用。

## 普通索引的创建与使用 📝

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_13.png)

普通索引适用于那些数据可能重复、无法设置唯一性约束的字段。例如，`name`（姓名）字段通常不适合设置主键或唯一约束，但可以为其创建普通索引以提升查询速度。

我们创建一个名为“索引表格”的表来演示：

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_15.png)

```sql
CREATE TABLE 索引表格 (
    ID INT,
    name VARCHAR(50),
    INDEX name_index (name)
);
```

创建后，使用 `DESC 索引表格;` 命令查看，会在 `name` 字段的 `Key` 列看到 `MUL` 标志，这表示该字段有一个普通索引。

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_17.png)

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_19.png)

当执行带有 `WHERE` 条件且条件涉及索引字段的查询时，数据库会使用索引来加速查找。例如：
```sql
SELECT * FROM 索引表格 WHERE name = ‘某姓名’;
```
使用 `EXPLAIN` 命令分析此查询，如果 `type` 列不是 `ALL`（全表扫描），则说明使用了索引。

## 主键索引与唯一索引 📌

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_21.png)

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_23.png)

需要特别注意的是，主键索引和唯一索引在创建约束时就已经自动创建了。

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_25.png)

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_27.png)

*   **主键索引**：在定义 `PRIMARY KEY` 约束时自动创建。它是效率最高的索引。
*   **唯一索引**：在定义 `UNIQUE KEY` 约束时自动创建。效率同样很高，因为其字段值具有唯一性。

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_29.png)

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_31.png)

因此，对于这两种索引，我们通常不需要，也不应该使用 `CREATE INDEX` 语句单独创建，只需定义相应的约束即可。需要重点掌握的是**普通索引**的创建，因为它是约束无法替代的。

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_33.png)

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_35.png)

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_37.png)

## 全文索引与联合索引 🔗

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_39.png)

除了上述索引，还有两种特殊的索引类型。

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_41.png)

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_43.png)

**全文索引** 专门用于对大量文本内容（`TEXT` 类型字段）进行高效的关键词搜索。它需要特定的存储引擎（如 MyISAM）支持。创建方法如下：
```sql
CREATE TABLE 文章表 (
    id INT,
    content TEXT,
    FULLTEXT (content)
) ENGINE=MyISAM;
```

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_45.png)

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_47.png)

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_49.png)

**联合索引** 是指将多个字段组合起来创建一个索引。这在某些场景下非常有用，例如，当单个字段无法保证唯一性，但多个字段组合起来可以时，可以创建**联合主键索引**来获得最高的查询效率。

以下是创建联合索引的示例：
```sql
-- 创建联合普通索引
CREATE TABLE 联合表 (
    id INT,
    name VARCHAR(50),
    INDEX idx_id_name (id, name)
);

-- 创建联合唯一索引
CREATE TABLE 唯一联合表 (
    id INT,
    name VARCHAR(50),
    UNIQUE INDEX uni_idx_id_name (id, name)
);

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_51.png)

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_53.png)

-- 创建联合主键（索引）
CREATE TABLE 主键联合表 (
    id INT,
    name VARCHAR(50),
    PRIMARY KEY (id, name)
);
```
要查看索引的详细信息（如是否为联合索引），应使用 `SHOW INDEX FROM 表名;` 命令，而不是 `DESC` 命令。

## SELECT 查询语句入门 🚪

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_55.png)

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_57.png)

在掌握了数据定义（DDL）和数据操作（DML）语句后，我们进入最重要的数据查询（DQL）部分，即 `SELECT` 语句。

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_59.png)

最基本的 `SELECT` 语句结构包含三部分：
```sql
SELECT 要查询的字段 FROM 表名 WHERE 查询条件;
```

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_61.png)

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_63.png)

*   **要查询的字段**：可以使用星号 `*` 代表所有字段，也可以指定具体的字段名，如 `id, name`。
*   **表名**：数据来源的表。
*   **查询条件**：通过 `WHERE` 子句来筛选数据。

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_65.png)

为了进行后续复杂的查询演示，我们先创建并填充两个示例表（学生表和班级表）。

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_67.png)

## WHERE 子句：条件查询基础 ⚙️

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_69.png)

`WHERE` 子句是 `SELECT` 语句的核心，它支持多种条件运算符。

**1. 比较运算符**
主要用于数值型字段的比较。
*   `=`：等于
*   `>`：大于
*   `<`：小于
*   `>=`：大于等于
*   `<=`：小于等于
*   `!=` 或 `<>`：不等于

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_71.png)

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_73.png)

示例：查询成绩小于80分的学生。
```sql
SELECT * FROM students WHERE score < 80;
```
**注意**：字符型字段通常只使用 `=` 和 `!=` 进行比较，使用 `>`、`<` 等运算符可能无法得到预期的字母顺序结果。

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_75.png)

**2. 范围查询：BETWEEN AND**
用于查询某个范围内的值，包含边界值。
示例：查询成绩在60到80分之间（含60和80）的学生。
```sql
SELECT * FROM students WHERE score BETWEEN 60 AND 80;
```
其否定形式为 `NOT BETWEEN ... AND ...`，表示不在该范围内的值。

![](img/4f2a8fb650277f9c8d77e185ea3f70d9_77.png)

本节课中我们一起学习了索引的多种创建方式，重点区分了普通索引与自带索引的约束，并初步了解了 `SELECT` 查询语句的基本结构及 `WHERE` 子句中的比较运算符和范围查询。下节课我们将继续深入 `WHERE` 子句的其他强大功能。