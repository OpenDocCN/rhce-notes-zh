# Linux运维进阶：P75：SELECT多表查询、复制表、破解密码、授权访问、存储引擎（上）

![](img/b63bb610e145df0bff9be5ef6810f007_0.png)

![](img/b63bb610e145df0bff9be5ef6810f007_2.png)

![](img/b63bb610e145df0bff9be5ef6810f007_4.png)

![](img/b63bb610e145df0bff9be5ef6810f007_6.png)

## 概述
在本节课中，我们将深入学习MySQL数据库的SELECT多表查询。上节课我们介绍了嵌套查询，本节课我们将重点探讨如何将多个数据表关联起来进行查询，理解不同的连接方式及其应用场景。

## 多表查询基础

上节课我们介绍了嵌套查询，本节中我们来看看如何对多个表进行联合查询。多表查询，顾名思义，就是同时对多个数据表进行查询操作。

![](img/b63bb610e145df0bff9be5ef6810f007_8.png)

首先，我们尝试一种最基础的写法：在`FROM`子句中直接列出两个表名。

![](img/b63bb610e145df0bff9be5ef6810f007_10.png)

```sql
SELECT * FROM students, class;
```

这种写法虽然能执行并返回结果，但会产生大量无意义的数据。例如，`students`表有11行数据，`class`表有4行数据，上述查询会返回11×4=44行结果。它实际上是将两个表的所有行进行了**笛卡尔积**（即排列组合），其中只有部分行是有关联的正确数据，其余都是无效组合。因此，我们需要更精确的方法来关联表。

![](img/b63bb610e145df0bff9be5ef6810f007_12.png)

![](img/b63bb610e145df0bff9be5ef6810f007_14.png)

## 连接查询（JOIN）

![](img/b63bb610e145df0bff9be5ef6810f007_16.png)

为了从多个表中提取有意义、相关联的数据，我们使用**连接查询**。连接查询的核心是通过两个表中具有相同含义的字段（例如都表示班级ID）将记录匹配起来。

![](img/b63bb610e145df0bff9be5ef6810f007_18.png)

以下是连接查询的两种等效写法：

### 写法一：在WHERE子句中关联
这种方法通过在`WHERE`条件中指定两个表的关联字段相等来实现连接。

```sql
SELECT * FROM students, class WHERE students.class_id = class.id;
```

### 写法二：使用JOIN关键字
这是更专业、更清晰的多表查询语法，使用`JOIN ... ON ...`结构。

```sql
SELECT * FROM students INNER JOIN class ON students.class_id = class.id;
```

![](img/b63bb610e145df0bff9be5ef6810f007_20.png)

**核心概念**：上述两种查询都只返回两个表中关联字段值相匹配的记录。这种查询称为**内连接（INNER JOIN）**，它相当于取两个表的“交集”。

![](img/b63bb610e145df0bff9be5ef6810f007_22.png)

![](img/b63bb610e145df0bff9be5ef6810f007_24.png)

在性能上，通常建议将数据量较大的表放在`JOIN`的前面（即作为驱动表），这有助于提升查询效率，因为大表的数据需要被遍历，先处理大表可以减少后续匹配的计算量。

![](img/b63bb610e145df0bff9be5ef6810f007_26.png)

## 左连接与右连接

![](img/b63bb610e145df0bff9be5ef6810f007_28.png)

内连接只返回两个表都能匹配上的记录。但有时我们需要包含某个表中所有记录，即使它在另一个表中没有匹配项。这时就需要**左连接（LEFT JOIN）** 和**右连接（RIGHT JOIN）**。

![](img/b63bb610e145df0bff9be5ef6810f007_30.png)

*   **左连接（LEFT JOIN）**：返回左表（`JOIN`关键字前的表）的全部记录，以及右表中匹配的记录。如果右表无匹配，则结果集中右表字段用`NULL`填充。
*   **右连接（RIGHT JOIN）**：返回右表（`JOIN`关键字后的表）的全部记录，以及左表中匹配的记录。如果左表无匹配，则结果集中左表字段用`NULL`填充。

![](img/b63bb610e145df0bff9be5ef6810f007_32.png)

![](img/b63bb610e145df0bff9be5ef6810f007_34.png)

以下是具体示例：
假设`students`表中有一条记录的`class_id`（例如5）在`class`表的`id`中不存在，而`class`表中有一条记录（例如`id`为6）没有对应的学生。

![](img/b63bb610e145df0bff9be5ef6810f007_36.png)

![](img/b63bb610e145df0bff9be5ef6810f007_38.png)

```sql
-- 左连接：以students表为主，显示所有学生及其班级，无班级的学生班级信息为NULL
SELECT * FROM students LEFT JOIN class ON students.class_id = class.id;

![](img/b63bb610e145df0bff9be5ef6810f007_40.png)

![](img/b63bb610e145df0bff9be5ef6810f007_42.png)

-- 右连接：以class表为主，显示所有班级及其学生，无学生的班级学生信息为NULL
SELECT * FROM students RIGHT JOIN class ON students.class_id = class.id;
```

![](img/b63bb610e145df0bff9be5ef6810f007_44.png)

**核心概念**：左/右连接查询的结果集包含了内连接的结果，并额外包含了左表或右表中那些“独有”的、未匹配上的记录。

![](img/b63bb610e145df0bff9be5ef6810f007_46.png)

## 全外连接与联合查询

**全外连接（FULL OUTER JOIN）** 会返回左表和右表中的所有记录。当某一行在另一个表中没有匹配时，另一个表的字段将显示为`NULL`。它相当于左连接和右连接的并集。

![](img/b63bb610e145df0bff9be5ef6810f007_48.png)

![](img/b63bb610e145df0bff9be5ef6810f007_50.png)

然而，在MySQL中，并不直接支持`FULL OUTER JOIN`语法。我们可以使用**联合查询（UNION）** 来达到相同效果。

`UNION`操作符用于合并两个或多个`SELECT`语句的结果集，并自动去除重复的行。

![](img/b63bb610e145df0bff9be5ef6810f007_52.png)

![](img/b63bb610e145df0bff9be5ef6810f007_54.png)

![](img/b63bb610e145df0bff9be5ef6810f007_56.png)

![](img/b63bb610e145df0bff9be5ef6810f007_58.png)

```sql
-- 使用UNION模拟全外连接
SELECT * FROM students LEFT JOIN class ON students.class_id = class.id
UNION
SELECT * FROM students RIGHT JOIN class ON students.class_id = class.id;
```

![](img/b63bb610e145df0bff9be5ef6810f007_60.png)

**核心概念**：`UNION`将两次查询的结果上下堆叠在一起，要求每个`SELECT`语句必须拥有相同数量的列，且列的数据类型必须兼容。

![](img/b63bb610e145df0bff9be5ef6810f007_62.png)

![](img/b63bb610e145df0bff9be5ef6810f007_64.png)

## 查询“独有”数据

![](img/b63bb610e145df0bff9be5ef6810f007_66.png)

有时我们只关心一个表中在另一个表里没有对应关系的“独有”数据。这可以通过在左连接或右连接的基础上，用`WHERE`子句过滤出关联字段为`NULL`的记录来实现。

以下是具体方法：

```sql
-- 查询只在students表中存在，而在class表中无对应班级的记录（即左表独有数据）
SELECT * FROM students LEFT JOIN class ON students.class_id = class.id WHERE class.id IS NULL;

![](img/b63bb610e145df0bff9be5ef6810f007_68.png)

-- 查询只在class表中存在，而在students表中无对应学生的记录（即右表独有数据）
SELECT * FROM students RIGHT JOIN class ON students.class_id = class.id WHERE students.class_id IS NULL;

-- 查询两个表中互不匹配的所有记录（即左右表独有数据的并集）
SELECT * FROM students LEFT JOIN class ON students.class_id = class.id WHERE class.id IS NULL
UNION
SELECT * FROM students RIGHT JOIN class ON students.class_id = class.id WHERE students.class_id IS NULL;
```

## 总结
本节课我们一起学习了MySQL中多表查询的核心知识。我们首先了解了直接列出多表会产生笛卡尔积的问题，然后重点掌握了使用`JOIN`进行连接查询的方法，包括：
1.  **内连接（INNER JOIN）**：获取两个表的交集。
2.  **左连接（LEFT JOIN）** 与**右连接（RIGHT JOIN）**：获取一个表的全部及另一个表的匹配记录。
3.  利用`UNION`实现**全外连接**效果。
4.  通过结合`WHERE ... IS NULL`来查询单个表的“独有”数据。

![](img/b63bb610e145df0bff9be5ef6810f007_70.png)

![](img/b63bb610e145df0bff9be5ef6810f007_72.png)

理解这些连接方式是进行复杂数据检索和分析的基础。下节课我们将继续学习表的复制、密码管理、权限控制等其他重要操作。