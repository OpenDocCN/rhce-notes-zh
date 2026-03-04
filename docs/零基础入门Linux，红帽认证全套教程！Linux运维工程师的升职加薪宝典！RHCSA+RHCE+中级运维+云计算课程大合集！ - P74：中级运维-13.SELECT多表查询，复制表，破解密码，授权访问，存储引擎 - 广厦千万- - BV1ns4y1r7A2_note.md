# Linux运维教程：P74：SELECT多表查询，复制表，破解密码，授权访问，存储引擎

![](img/ce5e17cf00a83a3b78b8d61896610fe0_1.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_3.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_5.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_7.png)

## 概述
在本节课中，我们将深入学习MySQL中的SELECT多表查询。上节课我们介绍了嵌套查询，本节我们将重点探讨如何同时从多个表中检索和关联数据，包括内连接、左/右连接、全连接等多种查询方式。此外，我们还会简要介绍复制表、用户密码管理、授权访问和存储引擎等实用主题。

## 多表查询基础
上节课我们介绍了嵌套查询，本节中我们来看看如何同时查询多个表。多表查询，顾名思义，就是从多个表中一起检索数据。

首先，我们来看一个简单的尝试：直接查询两个表而不加任何条件。

![](img/ce5e17cf00a83a3b78b8d61896610fe0_9.png)

```sql
SELECT * FROM students, class;
```

![](img/ce5e17cf00a83a3b78b8d61896610fe0_11.png)

执行此语句会返回大量数据（例如44行），这是因为它将两个表的所有行进行了**笛卡尔积**（即排列组合）。例如，`students`表有11行，`class`表有4行，结果就是11 * 4 = 44行。其中只有部分行（如11行）是有效关联数据，其余都是无意义的组合。因此，直接查询多表通常不是我们想要的结果。

## 连接查询
为了从多表中获取有意义的关联数据，我们需要使用**连接查询**。连接查询的核心是通过两个表中具有相同含义的字段（如`students.class_id`和`class.id`）将数据关联起来。

![](img/ce5e17cf00a83a3b78b8d61896610fe0_13.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_15.png)

连接查询主要有两种写法，效果相同。

![](img/ce5e17cf00a83a3b78b8d61896610fe0_17.png)

### 方式一：在WHERE子句中连接
这种方法在WHERE条件中指定两个表的关联字段相等。

![](img/ce5e17cf00a83a3b78b8d61896610fe0_19.png)

```sql
SELECT * FROM students, class WHERE students.class_id = class.id;
```

这个查询的含义是：只显示`students.class_id`与`class.id`值相同的行。不满足此条件的行（如`students`表中`class_id`为5的记录，在`class`表中没有id为5的对应记录）将被过滤掉。

### 方式二：使用JOIN关键字（内连接）
这是一种更专业的写法，使用`JOIN ... ON ...`语法明确表示连接。

```sql
SELECT * FROM students JOIN class ON students.class_id = class.id;
```

这两种写法的查询结果完全相同。它们都属于**内连接**，只返回两个表中匹配的行。

![](img/ce5e17cf00a83a3b78b8d61896610fe0_21.png)

**关于查询性能**：在多表查询时，通常建议将数据量大的表（大表）放在`JOIN`的前面（即`FROM`后面），数据量小的表（小表）放在后面。因为数据库通常会先读取前面的表，然后用小表的索引快速匹配，这有助于提升查询速度。

![](img/ce5e17cf00a83a3b78b8d61896610fe0_23.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_25.png)

## 连接查询的多种类型
连接查询不止内连接一种。我们可以通过不同类型的连接，获取两个表之间不同部分的数据集合。想象有两个相交的椭圆，分别代表表A和表B的数据。

以下是主要的连接类型：

![](img/ce5e17cf00a83a3b78b8d61896610fe0_27.png)

### 1. 内连接
内连接返回两个表**交集**部分的数据，即只返回匹配的行。
*   **SQL关键字**：`JOIN` 或 `INNER JOIN`
*   **对应图示**：两个椭圆的相交部分。
*   **示例**：
    ```sql
    SELECT * FROM students INNER JOIN class ON students.class_id = class.id;
    ```

![](img/ce5e17cf00a83a3b78b8d61896610fe0_29.png)

### 2. 左外连接
左外连接返回**左表（表A）的全部数据**，以及右表（表B）中与左表匹配的数据。如果左表的某行在右表中没有匹配，则结果集中右表的部分用`NULL`填充。
*   **SQL关键字**：`LEFT JOIN` 或 `LEFT OUTER JOIN`
*   **对应图示**：整个左椭圆（包括相交部分）。
*   **示例**：
    ```sql
    SELECT * FROM students LEFT JOIN class ON students.class_id = class.id;
    ```

![](img/ce5e17cf00a83a3b78b8d61896610fe0_31.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_33.png)

### 3. 右外连接
右外连接返回**右表（表B）的全部数据**，以及左表（表A）中与右表匹配的数据。如果右表的某行在左表中没有匹配，则结果集中左表的部分用`NULL`填充。
*   **SQL关键字**：`RIGHT JOIN` 或 `RIGHT OUTER JOIN`
*   **对应图示**：整个右椭圆（包括相交部分）。
*   **示例**：
    ```sql
    SELECT * FROM students RIGHT JOIN class ON students.class_id = class.id;
    ```

![](img/ce5e17cf00a83a3b78b8d61896610fe0_35.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_37.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_39.png)

### 4. 全外连接
全外连接返回两个表的**并集**，即左表和右表的所有行。如果某行在另一个表中没有匹配，则另一个表的部分用`NULL`填充。
*   **SQL关键字**：`FULL JOIN` 或 `FULL OUTER JOIN`
*   **对应图示**：整个左椭圆和整个右椭圆。
*   **注意**：**MySQL数据库本身不支持`FULL JOIN`语法**。但可以通过`UNION`联合查询来实现相同效果。
*   **示例（MySQL中的实现）**：
    ```sql
    (SELECT * FROM students LEFT JOIN class ON students.class_id = class.id)
    UNION
    (SELECT * FROM students RIGHT JOIN class ON students.class_id = class.id);
    ```

![](img/ce5e17cf00a83a3b78b8d61896610fe0_41.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_43.png)

### 5. 左表独有数据
有时我们只想要左表中**不存在于右表**的数据（即左椭圆减去相交部分）。这可以通过左连接加上`WHERE`条件过滤来实现。
*   **实现方式**：`LEFT JOIN` + `WHERE right_table.key IS NULL`
*   **示例**：
    ```sql
    SELECT * FROM students LEFT JOIN class ON students.class_id = class.id WHERE class.id IS NULL;
    ```

![](img/ce5e17cf00a83a3b78b8d61896610fe0_45.png)

### 6. 右表独有数据
只想要右表中**不存在于左表**的数据（即右椭圆减去相交部分）。
*   **实现方式**：`RIGHT JOIN` + `WHERE left_table.key IS NULL`
*   **示例**：
    ```sql
    SELECT * FROM students RIGHT JOIN class ON students.class_id = class.id WHERE students.class_id IS NULL;
    ```

### 7. 两表独有数据（并集减去交集）
想要获取两个表中互不匹配的所有行（即两个椭圆各自独立的部分）。在MySQL中，可以通过组合查询实现。
*   **实现方式**：组合“左表独有”和“右表独有”的查询结果。
*   **示例**：
    ```sql
    (SELECT * FROM students LEFT JOIN class ON students.class_id = class.id WHERE class.id IS NULL)
    UNION
    (SELECT * FROM students RIGHT JOIN class ON students.class_id = class.id WHERE students.class_id IS NULL);
    ```

![](img/ce5e17cf00a83a3b78b8d61896610fe0_47.png)

## 联合查询
我们刚才在实现全外连接时用到了`UNION`，这就是**联合查询**。它的主要作用是将**两个或多个SELECT语句的结果集合并**成一个结果集。

**使用`UNION`的注意事项**：
1.  每个`SELECT`语句查询的**列数必须相同**。
2.  对应列的数据类型应该相似。
3.  `UNION`默认会**去除重复的行**。如果希望保留所有行（包括重复的），可以使用`UNION ALL`。

![](img/ce5e17cf00a83a3b78b8d61896610fe0_49.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_51.png)

**示例**：合并两个不同条件查询同一表的结果。
```sql
SELECT name, score FROM students WHERE score > 90
UNION
SELECT name, score FROM students WHERE score < 60;
```

![](img/ce5e17cf00a83a3b78b8d61896610fe0_53.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_55.png)

## 其他实用操作
了解了多表查询的核心后，我们再来看看几个相关的实用操作。

![](img/ce5e17cf00a83a3b78b8d61896610fe0_57.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_59.png)

### 复制表
有时我们需要快速创建一个与现有表结构（或包括数据）相同的新表。

![](img/ce5e17cf00a83a3b78b8d61896610fe0_61.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_62.png)

**1. 只复制表结构**：
```sql
CREATE TABLE new_students LIKE students;
```

![](img/ce5e17cf00a83a3b78b8d61896610fe0_64.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_66.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_68.png)

**2. 复制表结构及数据**：
```sql
CREATE TABLE new_students SELECT * FROM students;
```

![](img/ce5e17cf00a83a3b78b8d61896610fe0_70.png)

### 用户密码管理与授权访问
**1. 修改用户密码（MySQL 5.7）**：
```sql
SET PASSWORD FOR 'username'@'host' = PASSWORD('new_password');
```
**注意**：更现代的方式是使用`ALTER USER`语句。

**2. 授予用户权限**：
```sql
GRANT SELECT, INSERT ON database_name.table_name TO 'username'@'host';
```

**3. 刷新权限**：
```sql
FLUSH PRIVILEGES;
```

![](img/ce5e17cf00a83a3b78b8d61896610fe0_72.png)

### 存储引擎简介
存储引擎是数据库底层如何存储、索引和查询数据的组件。MySQL最常用的两种引擎是：
*   **InnoDB**：支持事务、行级锁、外键约束。适用于需要高可靠性和并发读写的大多数场景。**（默认引擎）**
*   **MyISAM**：不支持事务和外键，但读取速度快。适用于只读或读多写少的场景。

**查看表的存储引擎**：
```sql
SHOW TABLE STATUS LIKE 'table_name';
```

**修改表的存储引擎**：
```sql
ALTER TABLE table_name ENGINE = InnoDB;
```

![](img/ce5e17cf00a83a3b78b8d61896610fe0_74.png)

![](img/ce5e17cf00a83a3b78b8d61896610fe0_76.png)

## 总结
本节课我们一起深入学习了MySQL中的SELECT多表查询。我们从基础的笛卡尔积问题出发，重点讲解了使用连接查询（JOIN）来有效关联多个表的数据，包括内连接、左/右外连接以及如何在MySQL中实现全外连接。我们还介绍了联合查询（UNION）的用法，并简要了解了复制表、用户密码管理、授权访问和存储引擎等实用知识。掌握这些多表查询技巧，是进行复杂数据分析和操作的基础。