# Linux运维全套培训课程：P74：中级运维-11.SELECT单表查询，嵌套查询-中 🗂️

![](img/c06b53fad835ae341ef2a14b9249991d_1.png)

在本节课中，我们将深入学习MySQL中`SELECT`语句的单表查询进阶操作，包括多重条件查询、`NOT`取反、排序、分页查询以及分组查询。这些功能将帮助你更精确、更高效地从数据库中检索和处理数据。

## 多重条件查询：`AND`与`OR` 🔄

上一节我们介绍了基本的`WHERE`条件查询，本节中我们来看看如何使用`AND`和`OR`进行多重条件查询。

![](img/c06b53fad835ae341ef2a14b9249991d_3.png)

![](img/c06b53fad835ae341ef2a14b9249991d_5.png)

![](img/c06b53fad835ae341ef2a14b9249991d_7.png)

同一个查询需求，往往有多种实现方式。例如，查询一班和二班同学的信息，你可以使用`BETWEEN`、`IN`、`LIKE`等多种方式。`AND`和`OR`是构建复杂查询逻辑的核心。

*   `AND`：表示“并且”，所有条件必须同时满足。添加的条件越多，满足条件的结果通常越少。
*   `OR`：表示“或者”，满足其中任意一个条件即可。添加的条件越多，满足条件的结果通常越多。

**核心概念**：
```sql
-- 使用 AND 查询一班且成绩大于80的学生
SELECT * FROM students WHERE class_id = 1 AND score > 80;

![](img/c06b53fad835ae341ef2a14b9249991d_9.png)

![](img/c06b53fad835ae341ef2a14b9249991d_11.png)

-- 使用 OR 查询一班或二班的学生
SELECT * FROM students WHERE class_id = 1 OR class_id = 2;
-- 等价于
SELECT * FROM students WHERE class_id IN (1, 2);
```

`OR`查询得到的结果集会包含`AND`查询的结果集。理解两者的区别对于编写正确的查询逻辑至关重要。

![](img/c06b53fad835ae341ef2a14b9249991d_13.png)

![](img/c06b53fad835ae341ef2a14b9249991d_15.png)

![](img/c06b53fad835ae341ef2a14b9249991d_17.png)

## 条件取反：`NOT`运算符 ⚠️

![](img/c06b53fad835ae341ef2a14b9249991d_19.png)

接下来，我们学习如何使用`NOT`运算符对条件进行取反。

`NOT`的用法是在一个完整的条件表达式前加上`NOT`关键字。它的含义是：排除满足该条件的数据，选择剩下的数据。它可以与`AND`、`OR`等运算符结合使用，但需要注意运算优先级，避免逻辑混乱。

![](img/c06b53fad835ae341ef2a14b9249991d_21.png)

**核心概念**：
```sql
-- 查询不是一班的学生
SELECT * FROM students WHERE NOT class_id = 1;
-- 注意：对于 NULL 值的判断，应使用 IS NOT NULL
SELECT * FROM students WHERE score IS NOT NULL;
```

![](img/c06b53fad835ae341ef2a14b9249991d_23.png)

![](img/c06b53fad835ae341ef2a14b9249991d_25.png)

![](img/c06b53fad835ae341ef2a14b9249991d_27.png)

使用`NOT`时需格外小心，尤其是在组合多个条件时，务必理清逻辑关系。

## 数据排序：`ORDER BY` 📊

![](img/c06b53fad835ae341ef2a14b9249991d_29.png)

在掌握了条件过滤后，我们常常需要对结果进行排序，以便更直观地查看数据。

排序使用`ORDER BY`子句，它通常放在查询语句的最后（在`WHERE`子句之后）。排序主要针对数值型字段，字符型字段按字母顺序排序。

**核心概念**：
```sql
-- 按成绩升序排序（从小到大，默认方式）
SELECT * FROM students ORDER BY score;
-- 或显式指明 ASC
SELECT * FROM students ORDER BY score ASC;

![](img/c06b53fad835ae341ef2a14b9249991d_31.png)

-- 按成绩降序排序（从大到小）
SELECT * FROM students ORDER BY score DESC;
```
`DESC`表示降序（Descending），`ASC`表示升序（Ascending，可省略）。排序常与`WHERE`条件结合，例如查看某个班级的成绩排名。

![](img/c06b53fad835ae341ef2a14b9249991d_33.png)

## 分页查询：`LIMIT`与`OFFSET` 📑

当数据量很大时，我们可能需要像翻书一样分批查看数据，这就是分页查询。

![](img/c06b53fad835ae341ef2a14b9249991d_35.png)

![](img/c06b53fad835ae341ef2a14b9249991d_37.png)

![](img/c06b53fad835ae341ef2a14b9249991d_39.png)

分页查询使用`LIMIT`和`OFFSET`子句，它们的作用类似于Linux中的`head`命令，用于截取结果集中的一部分进行显示。`LIMIT`指定返回多少行数据，`OFFSET`指定从第几行之后开始（起始行号为0）。

**核心概念**：
```sql
-- 查看成绩前三名（结合排序）
SELECT * FROM students ORDER BY score DESC LIMIT 3;

-- 查看从第4行开始（即跳过前3行）的2行数据（第4、5行）
SELECT * FROM students LIMIT 2 OFFSET 3;
-- 等价写法（注意顺序：LIMIT 行数 OFFSET 起始行）
SELECT * FROM students LIMIT 3, 2; -- 此写法中，3是OFFSET，2是LIMIT
```

![](img/c06b53fad835ae341ef2a14b9249991d_41.png)

以下是分页查询的几种常见场景：
*   **查看前N名**：`ORDER BY ... DESC LIMIT N`
*   **查看第M到第N行**：需要计算行数。`LIMIT (N-M+1) OFFSET (M-1)`
*   **查看最后一行**：需要先知道总行数。如果总行数为`total`，则：`LIMIT 1 OFFSET (total-1)`

![](img/c06b53fad835ae341ef2a14b9249991d_43.png)

为了动态获取总行数，我们需要借助函数。

![](img/c06b53fad835ae341ef2a14b9249991d_45.png)

## 聚合函数：`COUNT`, `MAX`, `MIN`, `SUM`, `AVG` 🧮

函数用于对一组值进行计算并返回单个值。它们写在`SELECT`关键字之后。

![](img/c06b53fad835ae341ef2a14b9249991d_47.png)

![](img/c06b53fad835ae341ef2a14b9249991d_49.png)

以下是几个常用的聚合函数：
*   **`COUNT()`**：计数，统计行数或非空值的数量。可用于任意数据类型。
*   **`MAX()`**：求最大值。
*   **`MIN()`**：求最小值。
*   **`SUM()`**：求和。
*   **`AVG()`**：求平均值。

![](img/c06b53fad835ae341ef2a14b9249991d_51.png)

![](img/c06b53fad835ae341ef2a14b9249991d_53.png)

**核心概念**：
```sql
-- 统计学生总人数（推荐以非空的主键字段计数，如id）
SELECT COUNT(id) FROM students;

![](img/c06b53fad835ae341ef2a14b9249991d_55.png)

-- 求成绩最高分、最低分、总分、平均分
SELECT MAX(score), MIN(score), SUM(score), AVG(score) FROM students;
```
`COUNT()`函数在配合分页查询计算总行数时非常有用。注意，聚合函数也可以与`WHERE`条件一起使用，对过滤后的数据进行计算。

## 数据分组：`GROUP BY` 👥

最后，我们学习如何将数据按照某个或某几个字段的相同值进行分组，然后对每个组进行聚合计算。

![](img/c06b53fad835ae341ef2a14b9249991d_57.png)

分组使用`GROUP BY`子句。分组后，`SELECT`后面通常跟两种内容：1) 分组的字段；2) 针对每个组的聚合函数（如`COUNT`）。

**核心概念**：
```sql
-- 按性别分组，并统计每组人数
SELECT gender, COUNT(id) AS number FROM students GROUP BY gender;

![](img/c06b53fad835ae341ef2a14b9249991d_59.png)

-- 按班级分组，统计每班人数，并列出每班学生名字
SELECT class_id, COUNT(id) AS number, GROUP_CONCAT(name) AS names
FROM students
GROUP BY class_id;
```
`GROUP_CONCAT()`函数用于将同一分组中某个字段的所有值连接成一个字符串，方便查看组内明细。分组查询能很好地回答诸如“每个班级有多少人”、“男生和女生的平均分各是多少”这类问题。

![](img/c06b53fad835ae341ef2a14b9249991d_61.png)

---

![](img/c06b53fad835ae341ef2a14b9249991d_63.png)

![](img/c06b53fad835ae341ef2a14b9249991d_65.png)

本节课中我们一起学习了MySQL单表查询的进阶操作。我们探讨了如何使用`AND`和`OR`构建复杂查询逻辑，用`NOT`进行条件取反，用`ORDER BY`对结果排序，用`LIMIT`和`OFFSET`实现分页，并介绍了`COUNT`、`MAX`等聚合函数以及强大的`GROUP BY`分组查询功能。掌握这些技能，你将能够更灵活、更精准地从数据库中提取和分析所需信息。