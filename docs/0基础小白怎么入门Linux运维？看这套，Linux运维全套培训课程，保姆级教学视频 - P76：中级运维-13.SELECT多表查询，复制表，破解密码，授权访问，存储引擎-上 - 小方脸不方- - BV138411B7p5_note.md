# Linux运维全套培训课程：P76：中级运维-13.SELECT多表查询，复制表，破解密码，授权访问，存储引擎-上 🗄️

![](img/bbf0f0633c50857871b8c5b4e0dd866d_0.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_2.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_4.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_6.png)

## 概述
在本节课中，我们将深入学习MySQL中的SELECT多表查询。上节课我们介绍了嵌套查询，本节我们将重点探讨如何将多个数据表连接起来进行查询，理解不同的连接方式及其应用场景。

## 多表查询简介
上一节我们介绍了嵌套查询，本节中我们来看看多表查询。顾名思义，多表查询就是同时对多个数据表进行查询操作。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_8.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_10.png)

首先，我们来看一个简单的尝试：如果直接将两个表名写在`FROM`子句中，会发生什么。

```sql
SELECT * FROM students, class;
```

![](img/bbf0f0633c50857871b8c5b4e0dd866d_12.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_14.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_16.png)

执行上述语句后，会得到44行数据。这是因为这种写法会将两个表的所有行进行**笛卡尔积**运算，即每个学生记录都与每个班级记录组合一次（11个学生 × 4个班级 = 44行）。这产生了大量无意义的组合数据，并非我们想要的结果。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_18.png)

因此，我们需要使用专门的**连接查询**语法，通过表间的关联字段来筛选出有意义的数据。

## 内连接查询
连接查询的核心是通过两个表中具有相同含义的字段（如`students.class_id`和`class.id`）将数据关联起来。最常见的连接方式是**内连接**。

内连接有两种等效的写法：

**写法一：在WHERE子句中指定连接条件**
```sql
SELECT * FROM students, class WHERE students.class_id = class.id;
```
这种写法通过`WHERE`条件，只返回两个表中`class_id`与`id`值相等的记录。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_20.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_22.png)

**写法二：使用INNER JOIN关键字**
```sql
SELECT * FROM students INNER JOIN class ON students.class_id = class.id;
```
这是更标准的连接查询语法。`INNER JOIN`表示内连接，`ON`后面指定连接条件。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_24.png)

以上两种写法的查询结果完全相同，都只返回两个表中关联字段匹配的行（共10行）。那个`class_id`为5（在`class`表中不存在对应`id`）的学生“张三”的记录不会被显示。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_26.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_28.png)

**关于查询性能的提示**：在多表查询时，将数据量大的表（大表）放在`JOIN`的前面，数据量小的表（小表）放在后面，通常有助于提升查询效率。因为数据库引擎通常会先处理`FROM`或`JOIN`后面的第一个表。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_30.png)

## 左连接与右连接
内连接只返回两个表都匹配的记录。如果想包含某个表中所有记录，即使它在另一个表中没有匹配项，就需要使用**左连接**或**右连接**。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_32.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_34.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_36.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_38.png)

连接的方向（左或右）由表在查询语句中的位置决定。`JOIN`左边的表被视为左表，右边的表被视为右表。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_40.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_42.png)

以下是左连接与右连接的区别：
*   **左连接 (LEFT JOIN)**：返回**左表**的全部记录，以及**右表**中与左表匹配的记录。如果右表无匹配，则结果集中右表字段用`NULL`填充。
*   **右连接 (RIGHT JOIN)**：返回**右表**的全部记录，以及**左表**中与右表匹配的记录。如果左表无匹配，则结果集中左表字段用`NULL`填充。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_44.png)

**左连接查询示例**：
```sql
SELECT * FROM students LEFT JOIN class ON students.class_id = class.id;
```
此查询会显示所有学生（左表`students`）的信息。对于`class_id`在`class`表中没有对应`id`的学生（如张三），其班级信息字段会显示为`NULL`。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_46.png)

**右连接查询示例**：
```sql
SELECT * FROM students RIGHT JOIN class ON students.class_id = class.id;
```
此查询会显示所有班级（右表`class`）的信息。对于在`students`表中没有学生的班级（如`id`为6的班级），其学生信息字段会显示为`NULL`。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_48.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_50.png)

## 全外连接与联合查询
**全外连接** 理论上是指返回左表和右表的所有记录。匹配的记录正常连接，不匹配的部分则用`NULL`填充对应侧的字段。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_52.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_54.png)

然而，在MySQL中，不支持标准的`FULL OUTER JOIN`语法。我们可以使用 **联合查询 (UNION)** 来模拟实现全外连接的效果。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_56.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_58.png)

`UNION`操作符用于合并两个或多个`SELECT`语句的结果集。要求每个`SELECT`语句必须拥有相同数量的列，且列的数据类型也需要相似。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_60.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_62.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_64.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_66.png)

**使用UNION实现全外连接**：
```sql
-- 左连接结果
(SELECT * FROM students LEFT JOIN class ON students.class_id = class.id)
UNION
-- 右连接结果
(SELECT * FROM students RIGHT JOIN class ON students.class_id = class.id);
```
这条语句先将左连接的结果和右连接的结果合并。`UNION`会自动去除重复的行（即内连接部分的数据只出现一次），最终得到包含左表独有、右表独有以及两者共有数据的完整集合。

## 查询特定不匹配数据
有时，我们可能需要专门查询那些在连接中**没有匹配成功**的数据。这可以通过在左连接或右连接的基础上，增加`WHERE`条件过滤出连接字段为`NULL`的记录来实现。

**查询只在左表中存在的数据（如没有对应班级的学生）**：
```sql
SELECT * FROM students LEFT JOIN class ON students.class_id = class.id WHERE class.id IS NULL;
```

![](img/bbf0f0633c50857871b8c5b4e0dd866d_68.png)

**查询只在右表中存在的数据（如没有学生的班级）**：
```sql
SELECT * FROM students RIGHT JOIN class ON students.class_id = class.id WHERE students.id IS NULL;
```

## 总结
本节课我们一起学习了SELECT多表查询的核心内容。我们首先了解了直接查询多表会产生笛卡尔积的问题，进而引入了连接查询的概念。我们详细探讨了：
1.  **内连接 (INNER JOIN)**：获取两个表匹配的记录。
2.  **左连接 (LEFT JOIN)** 与 **右连接 (RIGHT JOIN)**：分别以左表或右表为主，获取其全部记录及匹配记录。
3.  **全外连接**：获取两个表的所有记录，在MySQL中通过`UNION`左连接和右连接的结果来实现。
4.  **查询不匹配数据**：通过`WHERE ... IS NULL`条件过滤出连接中未匹配的记录。

![](img/bbf0f0633c50857871b8c5b4e0dd866d_70.png)

![](img/bbf0f0633c50857871b8c5b4e0dd866d_72.png)

理解并掌握这些多表连接方式，是进行复杂数据检索和数据分析的基础。