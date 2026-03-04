# Linux运维全套培训课程：P72：中级运维-9.约束，ALTER命令，索引，SELECT查询-下 📚

![](img/a92678b795b057ba72097c712887fd51_1.png)

在本节课中，我们将深入学习MySQL索引的创建与使用，并开始探索SELECT查询语句的基础知识。索引是提升数据库查询效率的关键，而SELECT语句则是我们与数据库交互、获取数据的核心工具。

## 索引的创建方法 🔧

![](img/a92678b795b057ba72097c712887fd51_3.png)

上一节我们介绍了索引的概念，本节中我们来看看如何创建索引。创建索引主要有三种方式，它们创建出的索引没有本质区别。

![](img/a92678b795b057ba72097c712887fd51_5.png)

以下是三种创建索引的方法：
*   **创建表后单独添加索引**：使用 `CREATE INDEX` 命令。
    ```sql
    CREATE INDEX index_name ON table_name (column_name);
    ```
*   **使用ALTER TABLE命令添加索引**：在修改表结构时添加。
    ```sql
    ALTER TABLE table_name ADD INDEX index_name (column_name);
    ```
*   **创建表时直接定义索引**：在`CREATE TABLE`语句中定义字段后指定。
    ```sql
    CREATE TABLE table_name (
        column1 datatype,
        column2 datatype,
        ...
        INDEX index_name (column_name)
    );
    ```

![](img/a92678b795b057ba72097c712887fd51_7.png)

## 普通索引的创建与使用 📖

![](img/a92678b795b057ba72097c712887fd51_9.png)

我们重点学习普通索引的创建，因为主键和唯一性索引在创建约束时会自动生成。

![](img/a92678b795b057ba72097c712887fd51_11.png)

![](img/a92678b795b057ba72097c712887fd51_13.png)

以下是创建一个带有普通索引的表示例：
```sql
-- 创建表时添加名为 `idx_name` 的普通索引
CREATE TABLE 索引表 (
    id INT,
    name VARCHAR(50),
    INDEX idx_name (name)
);
```
创建后，使用 `DESC 索引表;` 命令查看，会在 `name` 字段的 `Key` 列看到 `MUL` 标志，表示这是一个普通索引。

索引在查询时自动生效，但前提是查询条件（`WHERE`子句）中包含了索引字段。我们可以使用 `EXPLAIN` 命令来查看查询是否使用了索引。
```sql
EXPLAIN SELECT * FROM 索引表 WHERE name = ‘某值’;
```
在结果中，`type` 列如果显示为 `const`（主键/唯一索引）、`ref`（普通索引）等，而非 `ALL`（全表扫描），则说明索引已生效。

![](img/a92678b795b057ba72097c712887fd51_15.png)

## 主键索引与唯一性索引 🔑

主键约束（`PRIMARY KEY`）和唯一性约束（`UNIQUE KEY`）会自动创建对应的索引，因此我们无需单独为它们创建索引。它们拥有比普通索引更高的查询效率，因为其字段值具有唯一性。

![](img/a92678b795b057ba72097c712887fd51_17.png)

![](img/a92678b795b057ba72097c712887fd51_19.png)

## 全文索引与联合索引 📄

![](img/a92678b795b057ba72097c712887fd51_21.png)

![](img/a92678b795b057ba72097c712887fd51_23.png)

除了上述索引，还有两种特殊索引。

![](img/a92678b795b057ba72097c712887fd51_25.png)

**全文索引**：专门用于对大量文本内容（`TEXT` 类型字段）进行高效搜索，它依赖于 `MyISAM` 存储引擎。
```sql
CREATE TABLE 文章表 (
    id INT,
    content TEXT,
    FULLTEXT idx_content (content)
) ENGINE=MyISAM;
```

![](img/a92678b795b057ba72097c712887fd51_27.png)

![](img/a92678b795b057ba72097c712887fd51_29.png)

![](img/a92678b795b057ba72097c712887fd51_31.png)

**联合索引**：将多个字段组合起来创建一个索引。它可以是普通联合索引、唯一联合索引或联合主键。
```sql
-- 创建普通联合索引
CREATE TABLE 联合表 (
    id INT,
    name VARCHAR(50),
    INDEX idx_id_name (id, name)
);

![](img/a92678b795b057ba72097c712887fd51_33.png)

![](img/a92678b795b057ba72097c712887fd51_35.png)

![](img/a92678b795b057ba72097c712887fd51_37.png)

![](img/a92678b795b057ba72097c712887fd51_39.png)

-- 创建唯一联合索引
CREATE TABLE 唯一联合表 (
    id INT,
    name VARCHAR(50),
    UNIQUE INDEX uni_idx_id_name (id, name)
);

![](img/a92678b795b057ba72097c712887fd51_41.png)

-- 创建联合主键（也自带索引）
CREATE TABLE 主键联合表 (
    id INT,
    name VARCHAR(50),
    PRIMARY KEY (id, name)
);
```
要查看索引的详细信息（如索引类型、包含的字段等），应使用 `SHOW INDEX FROM table_name;` 命令。

![](img/a92678b795b057ba72097c712887fd51_43.png)

![](img/a92678b795b057ba72097c712887fd51_45.png)

![](img/a92678b795b057ba72097c712887fd51_47.png)

![](img/a92678b795b057ba72097c712887fd51_49.png)

## SELECT 查询语句入门 🚪

管理表结构（DDL）和增删改数据（DML）的命令我们已经学完，接下来进入最重要的部分——数据查询（DQL），其核心是 `SELECT` 语句。

![](img/a92678b795b057ba72097c712887fd51_51.png)

一个基础的 `SELECT` 语句结构如下：
```sql
SELECT 字段列表 FROM 表名 WHERE 查询条件;
```
*   `SELECT` 后指定要查看的字段，`*` 代表所有字段。
*   `FROM` 后指定要查询的表。
*   `WHERE` 后指定过滤数据的条件。

![](img/a92678b795b057ba72097c712887fd51_53.png)

## WHERE 条件查询：比较运算符 ⚖️

![](img/a92678b795b057ba72097c712887fd51_55.png)

![](img/a92678b795b057ba72097c712887fd51_57.png)

`WHERE` 子句是 `SELECT` 的精华，它支持多种条件设置。首先学习比较运算符。

![](img/a92678b795b057ba72097c712887fd51_59.png)

![](img/a92678b795b057ba72097c712887fd51_61.png)

以下是常用的比较运算符：
*   `=`：等于。用于数值和字符（字符需加引号，如 `WHERE name = ‘张三’`）。
*   `>`， `<`， `>=`， `<=`：大于、小于、大于等于、小于等于。**通常用于数值比较**。
*   `!=` 或 `<>`：不等于。

![](img/a92678b795b057ba72097c712887fd51_63.png)

![](img/a92678b795b057ba72097c712887fd51_65.png)

示例：查询成绩小于80分的学生。
```sql
SELECT * FROM students WHERE score < 80;
```

![](img/a92678b795b057ba72097c712887fd51_67.png)

## WHERE 条件查询：BETWEEN AND（范围） 🎯

![](img/a92678b795b057ba72097c712887fd51_69.png)

当需要查询某个范围内的数据时，可以使用 `BETWEEN ... AND ...` 运算符。它包含两端的值。

![](img/a92678b795b057ba72097c712887fd51_71.png)

示例：查询成绩在60到80分之间（含60和80）的学生。
```sql
SELECT * FROM students WHERE score BETWEEN 60 AND 80;
```
其等价于：
```sql
SELECT * FROM students WHERE score >= 60 AND score <= 80;
```
取反（查询不在该范围内的数据）使用 `NOT BETWEEN ... AND ...`。
```sql
SELECT * FROM students WHERE score NOT BETWEEN 60 AND 80;
```

![](img/a92678b795b057ba72097c712887fd51_73.png)

![](img/a92678b795b057ba72097c712887fd51_75.png)

---

![](img/a92678b795b057ba72097c712887fd51_77.png)

本节课中我们一起学习了MySQL索引的多种创建方式，重点是普通索引的创建与应用。同时，我们开启了SELECT查询语句的学习之旅，掌握了基础查询结构以及 `WHERE` 子句中比较运算符和 `BETWEEN AND` 范围查询的使用。下节课我们将继续深入 `WHERE` 子句的其他强大功能。