# Linux运维全套培训课程：P75：中级运维-12.SELECT单表查询，嵌套查询-下 📚

在本节课中，我们将深入学习SELECT单表查询的剩余部分，特别是嵌套查询的多种用法。我们将详细拆解查询语句的各个组成部分，并通过实例演示如何将多个查询语句组合使用，以解决更复杂的数据检索问题。

## 查询语句结构回顾 🔍

上一节我们介绍了SELECT查询的基本结构。本节中，我们来看看一个完整SELECT语句的各个部分及其作用。

一个典型的SELECT语句包含以下部分：
*   **SELECT**：指定要查询的字段。
*   **FROM**：指定数据来源的表。
*   **WHERE**：设置查询的过滤条件。
*   **GROUP BY**：对结果进行分组。
*   **HAVING**：对分组后的结果进行二次过滤。
*   **ORDER BY**：对结果进行排序。
*   **LIMIT**：限制返回的结果数量。

### SELECT子句详解

SELECT子句决定了查询结果中显示哪些内容。目前我们学过四种主要写法。

![](img/dbfb9f73b51ad353ca48adbc9fb52403_1.png)

以下是SELECT子句的几种用法：
1.  **`SELECT *`**：星号代表所有字段。它会显示查询结果中每一行的所有列信息。
2.  **`SELECT 字段1, 字段2, ...`**：查询指定的一个或多个字段。当表很大时，只获取关键列可以提高效率。多个字段用逗号隔开。
3.  **`SELECT COUNT(字段)`**：这是计数函数`COUNT()`。它可以单独使用，例如计算查询结果的总行数：`SELECT COUNT(*) FROM students;`。
4.  **`SELECT GROUP_CONCAT(字段)`**：这是分组连接函数，必须与`GROUP BY`一起使用。它用于显示每个分组内的详细信息。

![](img/dbfb9f73b51ad353ca48adbc9fb52403_3.png)

![](img/dbfb9f73b51ad353ca48adbc9fb52403_5.png)

### FROM、WHERE及其他子句

![](img/dbfb9f73b51ad353ca48adbc9fb52403_7.png)

`FROM`子句最简单，用于指定查询的表名。目前是单表查询，所以只写一个表名。如果是多表查询，表名之间也用逗号隔开。

![](img/dbfb9f73b51ad353ca48adbc9fb52403_9.png)

![](img/dbfb9f73b51ad353ca48adbc9fb52403_11.png)

![](img/dbfb9f73b51ad353ca48adbc9fb52403_13.png)

`WHERE`子句不是必须的，但加上它可以限制查询范围，过滤出符合条件的数据。

![](img/dbfb9f73b51ad353ca48adbc9fb52403_15.png)

![](img/dbfb9f73b51ad353ca48adbc9fb52403_17.png)

![](img/dbfb9f73b51ad353ca48adbc9fb52403_19.png)

`GROUP BY`、`ORDER BY`、`LIMIT`这些子句都加在`WHERE`条件之后。可以理解为，先通过`WHERE`得到初步的查询结果，然后再用这些子句进行二次处理（如分组、排序、分页），它们只改变数据的显示形式，不改变数据本身。

![](img/dbfb9f73b51ad353ca48adbc9fb52403_21.png)

![](img/dbfb9f73b51ad353ca48adbc9fb52403_23.png)

![](img/dbfb9f73b51ad353ca48adbc9fb52403_25.png)

### HAVING子句的作用

`HAVING`也是一个条件语句，用法与`WHERE`类似，但关键区别在于**生效的位置**。

`WHERE`是在分组(`GROUP BY`)或排序(`ORDER BY`)**之前**进行第一次数据过滤。而`HAVING`是在所有分组、排序等操作完成**之后**，对最终结果进行**二次限制**。

例如，先用`GROUP BY`按班级分组得到5个组的数据，如果只想看前3个班级的信息，就可以在最后使用`HAVING`进行限制。`HAVING`通常用于对聚合函数（如`COUNT()`, `SUM()`）的结果进行条件过滤。

![](img/dbfb9f73b51ad353ca48adbc9fb52403_27.png)

## 复杂查询语句示例 💻

![](img/dbfb9f73b51ad353ca48adbc9fb52403_29.png)

现在，我们将前面所学的知识组合起来，构建一个相对复杂的查询语句。

![](img/dbfb9f73b51ad353ca48adbc9fb52403_31.png)

假设我们想查询**女生中成绩前三名的同学，并按班级分组显示班级ID和姓名**。这个需求需要组合多个子句。

![](img/dbfb9f73b51ad353ca48adbc9fb52403_33.png)

```sql
SELECT class_id, GROUP_CONCAT(name)
FROM students
WHERE gender = ‘女’
ORDER BY score DESC
LIMIT 3;
```
执行以上语句后，我们得到了一个中间结果（一个虚拟的临时表格）。如果我们想对这个结果再按`class_id`分组，一个语句就无法完成了，因为`GROUP BY`和`ORDER BY`、`LIMIT`在同一个查询中的组合会受限。这时就需要引入**嵌套查询**。

## 嵌套查询入门 🔄

当一个SELECT语句无法完成复杂的查询需求时，就需要使用嵌套查询（子查询）。嵌套查询的核心思想是：**将一个查询语句的结果，作为另一个查询语句的一部分来使用**。

嵌套查询主要有两种应用场景：

### 1. 子查询作为条件（用在WHERE或HAVING中）

![](img/dbfb9f73b51ad353ca48adbc9fb52403_35.png)

这种用法是将一个查询的结果，作为另一个查询的过滤条件集合。

**示例：查询与“佩奇”、“瑞贝卡”成绩相同的学生姓名和分数。**

我们无法直接写出分数值，所以需要分两步：
1.  先查询出“佩奇”和“瑞贝卡”的成绩。
2.  再查询分数在这个成绩集合中的学生。

以下是实现此需求的步骤与代码：
```sql
— 第一步：先内层查询，获取特定学生的成绩集合
SELECT score FROM students WHERE name IN (‘佩奇‘， ’瑞贝卡‘);

![](img/dbfb9f73b51ad353ca48adbc9fb52403_37.png)

— 第二步：将内层查询嵌入外层查询，作为条件
SELECT name, score
FROM students
WHERE score IN (
    SELECT score FROM students WHERE name IN (‘佩奇‘， ’瑞贝卡‘)
);
```
注意，用作条件的子查询（`SELECT score ...`）通常只返回**单个字段**的多个值。

![](img/dbfb9f73b51ad353ca48adbc9fb52403_39.png)

![](img/dbfb9f73b51ad353ca48adbc9fb52403_41.png)

![](img/dbfb9f73b51ad353ca48adbc9fb52403_43.png)

#### 子查询与比较运算符（ANY/ALL）

![](img/dbfb9f73b51ad353ca48adbc9fb52403_45.png)

当子查询返回多个值，并且需要比较大小（而非相等）时，需要使用`ANY`或`ALL`。
*   **`> ALL (子查询)`**：大于子查询结果中的**最大值**。
*   **`< ALL (子查询)`**：小于子查询结果中的**最小值**。
*   **`> ANY (子查询)`**：大于子查询结果中的**任意一个值**，即大于**最小值**。
*   **`< ANY (子查询)`**：小于子查询结果中的**任意一个值**，即小于**最大值**。

![](img/dbfb9f73b51ad353ca48adbc9fb52403_46.png)

#### 子查询与EXISTS判断

![](img/dbfb9f73b51ad353ca48adbc9fb52403_48.png)

![](img/dbfb9f73b51ad353ca48adbc9fb52403_50.png)

`EXISTS`用于判断子查询是否返回结果。如果子查询有结果，则执行外层查询；如果子查询结果为空，则不执行。
```sql
SELECT * FROM table_a
WHERE EXISTS (SELECT * FROM table_b WHERE condition);
```

![](img/dbfb9f73b51ad353ca48adbc9fb52403_52.png)

### 2. 子查询作为数据源（用在FROM中）

这种用法是将一个查询的完整结果集，当作一张**临时表**，供外层查询继续操作。

**示例：承接前面的复杂查询，将女生成绩前三名的结果作为临时表，再按班级分组。**

![](img/dbfb9f73b51ad353ca48adbc9fb52403_54.png)

以下是实现此需求的步骤与代码：
```sql
— 内层查询：获取女生成绩前三名的临时结果
SELECT name, score, class_id FROM students WHERE gender = ‘女‘ ORDER BY score DESC LIMIT 3;

![](img/dbfb9f73b51ad353ca48adbc9fb52403_56.png)

![](img/dbfb9f73b51ad353ca48adbc9fb52403_58.png)

![](img/dbfb9f73b51ad353ca48adbc9fb52403_60.png)

![](img/dbfb9f73b51ad353ca48adbc9fb52403_62.png)

— 外层查询：将内层查询结果作为表‘temp‘，再进行分组
SELECT class_id, GROUP_CONCAT(name)
FROM (
    SELECT name, score, class_id FROM students WHERE gender = ‘女‘ ORDER BY score DESC LIMIT 3
) AS temp — 必须为子查询结果起一个别名
GROUP BY class_id;
```
**关键点**：当子查询用在`FROM`后面作为表时，**必须为其赋予一个别名**（例如`AS temp`），即使这个别名只在当前查询中临时使用。

![](img/dbfb9f73b51ad353ca48adbc9fb52403_64.png)

## 课程总结 📝

![](img/dbfb9f73b51ad353ca48adbc9fb52403_66.png)

![](img/dbfb9f73b51ad353ca48adbc9fb52403_68.png)

本节课中我们一起深入学习了SELECT单表查询的进阶内容。

我们首先系统回顾了SELECT语句的完整结构，明确了`SELECT`、`FROM`、`WHERE`、`GROUP BY`、`HAVING`、`ORDER BY`、`LIMIT`各子句的作用和顺序，重点区分了`WHERE`和`HAVING`在过滤时机上的本质不同。

随后，我们通过一个综合实例将多个子句组合使用，并引出了当单个查询语句能力不足时的解决方案——**嵌套查询**。我们详细讲解了嵌套查询的两种核心用法：
1.  将子查询结果作为**条件**，用在`WHERE`或`HAVING`子句中，常与`IN`、`ANY`、`ALL`、`EXISTS`等运算符结合。
2.  将子查询结果作为**数据源**，用在`FROM`子句中，此时必须为子查询结果集指定别名。

![](img/dbfb9f73b51ad353ca48adbc9fb52403_70.png)

嵌套查询是处理复杂数据检索的强大工具，它通过将问题分解为多个步骤，使复杂的查询逻辑变得清晰可行。理解并掌握这两种嵌套方式，是进行高效数据库查询的关键。下节课，我们将进入**多表查询**的学习，探索如何从多个关联表中联合获取数据。