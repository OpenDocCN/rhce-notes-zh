# MySQL数据库查询：P73：SELECT单表查询进阶

![](img/8019605d6764d004e1e606d07266c5a1_1.png)

在本节课中，我们将深入学习MySQL中`SELECT`语句的进阶用法，包括多重条件查询、取反操作、结果排序、分页查询以及分组查询。这些技巧将帮助你更精确、更高效地从数据库中获取所需数据。

---

## 多重条件查询：AND与OR

![](img/8019605d6764d004e1e606d07266c5a1_3.png)

上一节我们介绍了基本的`WHERE`条件查询，本节中我们来看看如何使用`AND`和`OR`进行多重条件查询。同一个查询需求，往往有多种实现方式。

![](img/8019605d6764d004e1e606d07266c5a1_5.png)

![](img/8019605d6764d004e1e606d07266c5a1_7.png)

以下是多重条件查询的核心概念：
*   **AND**：表示“并且”，所有条件必须同时满足。
    *   公式：`条件1 AND 条件2 AND ...`
*   **OR**：表示“或者”，满足其中任意一个条件即可。
    *   公式：`条件1 OR 条件2 OR ...`

你可以使用`BETWEEN`、`IN`、`LIKE`等多种运算符与`AND`或`OR`组合。需要注意的是，`AND`会使结果集越来越小（条件更严格），而`OR`会使结果集越来越大（条件更宽松）。`OR`查询的结果集总是包含`AND`查询的结果集。

![](img/8019605d6764d004e1e606d07266c5a1_9.png)

---

![](img/8019605d6764d004e1e606d07266c5a1_11.png)

## 取反操作：NOT

![](img/8019605d6764d004e1e606d07266c5a1_13.png)

![](img/8019605d6764d004e1e606d07266c5a1_15.png)

在条件查询中，我们有时需要获取不满足某个条件的数据，这时就需要用到`NOT`运算符。

![](img/8019605d6764d004e1e606d07266c5a1_17.png)

取反操作就是在条件表达式前加上`NOT`关键字。例如，`NOT (score > 60)` 表示查询成绩不大于60的记录。它可以与`AND`、`OR`一起使用，但组合使用时需要特别注意逻辑关系，避免混淆。

![](img/8019605d6764d004e1e606d07266c5a1_19.png)

**注意**：对于判断空值（`NULL`），不能使用 `= NULL` 或 `!= NULL`，而必须使用 `IS NULL` 或 `IS NOT NULL`。

---

![](img/8019605d6764d004e1e606d07266c5a1_21.png)

![](img/8019605d6764d004e1e606d07266c5a1_23.png)

## 结果排序：ORDER BY

![](img/8019605d6764d004e1e606d07266c5a1_25.png)

![](img/8019605d6764d004e1e606d07266c5a1_27.png)

查询出数据后，我们经常需要按照某个字段的值进行排序，这时就需要使用`ORDER BY`子句。

排序通常对数值字段最有意义。`ORDER BY`子句放在查询语句的最后（在`WHERE`子句之后）。它可以与`WHERE`结合使用，先过滤再排序。

![](img/8019605d6764d004e1e606d07266c5a1_29.png)

以下是排序的两种方式：
*   **升序（从小到大）**：使用 `ASC` 关键字，或省略不写（默认即为升序）。
    *   代码：`SELECT * FROM students ORDER BY score ASC;`
*   **降序（从大到小）**：使用 `DESC` 关键字。
    *   代码：`SELECT * FROM students ORDER BY score DESC;`

例如，查询二班同学并按成绩降序排列：
```sql
SELECT * FROM students WHERE class_id = 2 ORDER BY score DESC;
```

---

![](img/8019605d6764d004e1e606d07266c5a1_31.png)

## 分页查询：LIMIT 与 OFFSET

![](img/8019605d6764d004e1e606d07266c5a1_33.png)

当查询结果数据量很大时，我们可能只需要查看其中的一部分，例如“前三名”或“第6到第10条记录”。这类似于Linux中的`head`和`tail`命令，在MySQL中我们使用`LIMIT`和`OFFSET`。

分页查询永远从上往下截取数据。

![](img/8019605d6764d004e1e606d07266c5a1_35.png)

![](img/8019605d6764d004e1e606d07266c5a1_37.png)

![](img/8019605d6764d004e1e606d07266c5a1_39.png)

以下是分页查询的用法介绍：
*   **LIMIT n**：返回查询结果的前n行。
    *   代码：`SELECT * FROM students ORDER BY score DESC LIMIT 3;` （查看成绩前三名）
*   **LIMIT n OFFSET m**：从第m行开始（**注意：第一行的偏移量是0**），返回接下来的n行。
    *   代码：`SELECT * FROM students ORDER BY score DESC LIMIT 1 OFFSET 9;` （查看第十名，即倒数第一名）
*   **简写形式 LIMIT m, n**：等价于 `LIMIT n OFFSET m`。**顺序是相反的**，务必注意。
    *   代码：`SELECT * FROM students LIMIT 2, 3;` （从第3行开始，显示3行，即第3、4、5行）

**注意**：要查看后N名，需要先知道总行数。这时可以借助**计数函数** `COUNT()`。

---

![](img/8019605d6764d004e1e606d07266c5a1_41.png)

## 常用聚合函数

![](img/8019605d6764d004e1e606d07266c5a1_43.png)

函数用于对一组值进行计算并返回单个值。它们通常写在`SELECT`关键字之后。

![](img/8019605d6764d004e1e606d07266c5a1_45.png)

以下是五个常用的聚合函数：
*   **COUNT()**：计数，统计行数或非空值的数量。
    *   代码：`SELECT COUNT(id) FROM students WHERE score IS NOT NULL;`
*   **MAX()**：求最大值。
    *   代码：`SELECT MAX(score) FROM students;`
*   **MIN()**：求最小值。
    *   代码：`SELECT MIN(score) FROM students;`
*   **SUM()**：求和。
    *   代码：`SELECT SUM(score) FROM students;`
*   **AVG()**：求平均值。
    *   代码：`SELECT AVG(score) FROM students;`

**提示**：使用`COUNT()`函数时，建议选择具有`主键`或`非空约束`的字段（如`id`），以确保计数准确。

![](img/8019605d6764d004e1e606d07266c5a1_47.png)

---

![](img/8019605d6764d004e1e606d07266c5a1_49.png)

![](img/8019605d6764d004e1e606d07266c5a1_51.png)

## 分组查询：GROUP BY

![](img/8019605d6764d004e1e606d07266c5a1_53.png)

分组查询用于将数据按照一个或多个字段相同的值分成不同的组，然后对每个组进行聚合计算。

![](img/8019605d6764d004e1e606d07266c5a1_55.png)

分组通常对具有重复值的字段（如班级、性别）进行。`GROUP BY`子句放在查询语句的最后。

以下是分组查询的典型用法：
1.  **基本分组与计数**：统计每个性别的学生人数。
    ```sql
    SELECT gender, COUNT(*) AS number FROM students GROUP BY gender;
    ```
2.  **查看组内成员详情**：使用`GROUP_CONCAT()`函数列出每个组内的具体成员信息。
    ```sql
    SELECT
        gender,
        COUNT(*) AS number,
        GROUP_CONCAT(name) AS names
    FROM students
    GROUP BY gender;
    ```
    同样，可以按班级分组：
    ```sql
    SELECT
        class_id,
        COUNT(*) AS number,
        GROUP_CONCAT(name) AS names
    FROM students
    GROUP BY class_id;
    ```

**注意**：在`SELECT`后面出现的、非聚合函数的字段，通常都必须包含在`GROUP BY`子句中。

![](img/8019605d6764d004e1e606d07266c5a1_57.png)

---

![](img/8019605d6764d004e1e606d07266c5a1_59.png)

## 单表查询语句结构总结

本节课中我们一起学习了`SELECT`单表查询的进阶部分。一个完整的单表查询语句结构可以概括如下：

![](img/8019605d6764d004e1e606d07266c5a1_61.png)

```sql
SELECT [字段|函数|*]
FROM 表名
[WHERE 条件]
[ORDER BY 字段 [ASC|DESC]]
[LIMIT [偏移量,] 行数]
[GROUP BY 分组字段];
```

![](img/8019605d6764d004e1e606d07266c5a1_63.png)

![](img/8019605d6764d004e1e606d07266c5a1_65.png)

其中，`SELECT`和`FROM`是必须的，其他子句可以根据需要添加。掌握这些子句的组合使用，你就能应对绝大部分的单表数据检索需求了。