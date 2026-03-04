# MySQL数据库教程：P72：SELECT单表查询，嵌套查询

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_1.png)

在本节课中，我们将深入学习MySQL中SELECT语句的单表查询功能，特别是条件查询、排序、分页和分组查询的多种用法。我们将通过具体的例子，帮助你理解如何灵活运用这些查询技巧来处理数据。

## 概述：单表查询的核心结构

一个基础的SELECT查询语句通常包含几个部分：`SELECT`、`FROM`、`WHERE`。其中，`SELECT`和`FROM`是必须的，`WHERE`及其他部分（如`ORDER BY`、`LIMIT`、`GROUP BY`）则用于实现更具体的查询需求。

**基础语法结构：**
```sql
SELECT 字段列表或函数
FROM 表名
[WHERE 条件]
[ORDER BY 排序字段]
[LIMIT 限制行数]
[GROUP BY 分组字段];
```

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_3.png)

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_5.png)

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_7.png)

## 多重条件查询：AND与OR

上一节我们介绍了基本的`WHERE`条件查询，本节中我们来看看如何使用`AND`和`OR`进行多重条件组合查询。

同一个查询需求，往往有多种实现方式。例如，查询一班或二班同学的信息，可以使用`IN`集合查询、`LIKE`模糊匹配，或者使用`OR`逻辑运算符。

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_9.png)

以下是使用`AND`和`OR`进行查询的要点：
*   **AND（与）**：连接多个条件，要求**所有**条件同时满足。增加`AND`条件会使结果集越来越小。
*   **OR（或）**：连接多个条件，要求满足**其中任意一个**条件。增加`OR`条件会使结果集越来越大。
*   **关系**：`OR`查询的结果集通常会包含`AND`查询的结果集，但两者的含义是相反的。

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_11.png)

**示例：查询班级ID为1或2的学生**
```sql
SELECT * FROM students WHERE class_id = 1 OR class_id = 2;
-- 等价于
SELECT * FROM students WHERE class_id IN (1, 2);
```

## 条件取反：NOT运算符

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_13.png)

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_15.png)

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_17.png)

在条件查询中，我们还可以使用`NOT`运算符对条件进行取反。

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_19.png)

`NOT`取反的含义是：执行原条件查询，然后**不要**满足原条件的结果，取剩下的部分。它可以和`AND`、`OR`一起使用，但需要注意逻辑优先级，使用括号`()`来明确组合关系。

**使用NOT的注意事项：**
*   对于判断空值，应使用 `IS NOT NULL`。
*   对于其他条件，通常在条件表达式前直接加 `NOT`。
*   例如：`NOT score > 60` 表示查询成绩不大于60分（即小于等于60分）的记录。

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_21.png)

## 数据排序：ORDER BY

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_23.png)

在获取数据后，我们经常需要按照某个字段进行排序。这就要用到`ORDER BY`子句。

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_25.png)

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_27.png)

排序通常对数值型字段更有意义，例如按成绩从高到低排名。`ORDER BY`子句放在`FROM`或`WHERE`子句之后。

**排序的两种方式：**
*   **升序（ASC）**：从小到大排列。`ASC`是默认值，可以省略。
*   **降序（DESC）**：从大到小排列。需要显式指定`DESC`。

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_29.png)

**示例：查询所有学生并按成绩降序排列**
```sql
-- 默认升序（ASC）
SELECT * FROM students ORDER BY score;

-- 明确指定降序（DESC）
SELECT * FROM students ORDER BY score DESC;
```

**结合WHERE条件：** 我们可以先过滤数据，再对结果排序。
```sql
-- 查询二班学生，并按成绩降序排列
SELECT * FROM students WHERE class_id = 2 ORDER BY score DESC;
```

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_31.png)

## 分页查询：LIMIT与OFFSET

当排序后的数据很多时，我们可能只需要查看其中的一部分，比如前三名或最后一名。这时就需要用到分页查询，其作用类似于Linux中的`head`和`tail`命令，用于截取部分结果。

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_33.png)

分页查询主要使用 `LIMIT` 和 `OFFSET` 关键字，并且**总是从上往下**截取数据。

**基本用法：**
*   `LIMIT n`：返回查询结果的前`n`行。
*   `LIMIT n OFFSET m`：从第`m`行开始（**注意：第一行的偏移量是0**），返回`n`行数据。
*   简写形式：`LIMIT m, n` 等价于 `LIMIT n OFFSET m`。**注意：此时`m`和`n`的顺序与`OFFSET`写法相反**。

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_35.png)

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_37.png)

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_39.png)

**示例：分页查询实践**
```sql
-- 1. 查看成绩前三名
SELECT * FROM students ORDER BY score DESC LIMIT 3;

-- 2. 查看成绩倒数第一名（假设共10条有效记录）
SELECT * FROM students ORDER BY score LIMIT 1 OFFSET 9;
-- 简写形式
SELECT * FROM students ORDER BY score LIMIT 9, 1;

-- 3. 查看从第3行开始的4条数据（即第3,4,5,6行）
SELECT * FROM students LIMIT 4 OFFSET 2;
-- 简写形式
SELECT * FROM students LIMIT 2, 4;
```

## 计数函数：COUNT()

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_41.png)

在进行分页查询，尤其是想获取倒数几行数据时，我们需要知道总行数。`COUNT()`函数可以帮助我们统计记录数量。

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_43.png)

**使用COUNT()的注意事项：**
*   建议对具有**主键、自增或非空约束**的字段（如`id`）进行计数，以确保统计准确。
*   计数时，应保持`WHERE`等过滤条件不变，只将`SELECT`后的字段换成`COUNT(字段)`。

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_45.png)

**示例：统计有成绩的学生数量**
```sql
SELECT COUNT(id) FROM students WHERE score IS NOT NULL;
```

**结合分页查询：** 先计数，再计算偏移量。
```sql
-- 假设通过COUNT()得知共有total条记录
-- 要查看最后3条记录
SELECT * FROM students ORDER BY score LIMIT 3 OFFSET (total - 3);
```

## 分组查询：GROUP BY

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_47.png)

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_49.png)

分组查询的目的是将具有相同特征的数据归为一组，然后对组内数据进行统计或计算。常用于按类别（如班级、性别）进行汇总。

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_51.png)

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_53.png)

分组查询使用 `GROUP BY` 子句，通常与聚合函数一起使用。

**常用聚合函数：**
*   `COUNT()`：计数。
*   `MAX()`：求最大值。
*   `MIN()`：求最小值。
*   `SUM()`：求和。
*   `AVG()`：求平均值。

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_55.png)

**函数可以单独使用：**
```sql
-- 求全班最高分、最低分、总分、平均分
SELECT MAX(score), MIN(score), SUM(score), AVG(score) FROM students;
```

**分组查询的基本用法：**
仅使用`GROUP BY`而不指定查看内容，数据库无法返回结果。我们通常需要查看分组字段本身以及组的统计信息。

**示例：按性别分组，统计每组人数**
```sql
SELECT gender, COUNT(*) AS number FROM students GROUP BY gender;
```

## 查看分组详情：GROUP_CONCAT()

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_57.png)

如果我们不仅想知道每组的统计信息，还想知道组内成员的具体数据（如姓名），可以使用`GROUP_CONCAT()`函数。

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_59.png)

**示例：按班级分组，列出每班学生姓名**
```sql
SELECT class_id, COUNT(*) AS student_count, GROUP_CONCAT(name) AS student_names
FROM students
GROUP BY class_id;
```
这条语句会返回每个班级的ID、该班学生数量，以及所有学生姓名连接成的字符串。

## 总结

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_61.png)

本节课我们一起学习了MySQL单表查询的进阶内容。我们掌握了：
1.  **多重条件查询**：使用`AND`、`OR`进行逻辑组合，并用`NOT`进行条件取反。
2.  **数据排序**：使用`ORDER BY`对结果集进行升序或降序排列。
3.  **分页查询**：使用`LIMIT`和`OFFSET`截取结果集的特定部分，并学会了如何结合`COUNT()`函数处理未知总行数的情况。
4.  **分组查询**：使用`GROUP BY`对数据进行分类，并配合`COUNT()`、`MAX()`等聚合函数以及`GROUP_CONCAT()`函数获取分组统计信息和详情。

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_63.png)

![](img/2aa1dded967a39a4ed8f0d75f9d958dd_65.png)

这些技能是进行高效数据检索和分析的基础，请务必通过练习熟练掌握。下一节课，我们将探讨更复杂的多表连接查询。