# MySQL数据库管理：P76：中级运维-15：SELECT多表查询，复制表，破解密码，授权访问，存储引擎 🗄️

![](img/a3a6a70f1c837db86254e10de5003ae6_1.png)

![](img/a3a6a70f1c837db86254e10de5003ae6_2.png)

![](img/a3a6a70f1c837db86254e10de5003ae6_4.png)

在本节课中，我们将学习MySQL数据库管理中的几个核心操作，包括多表查询、数据表复制、密码重置、用户授权以及存储引擎的详细解析。这些知识是进行高效数据库运维和管理的基础。

![](img/a3a6a70f1c837db86254e10de5003ae6_6.png)

![](img/a3a6a70f1c837db86254e10de5003ae6_8.png)

## 存储引擎详解 🏗️

上一节我们介绍了用户授权访问，本节中我们来看看MySQL的存储引擎。在第一节课提到MySQL特点时，我们就曾涉及存储引擎的概念。

一个合适的存储引擎通常已由系统默认选定。目前MySQL使用更多的是**InnoDB**存储引擎。我们至今创建的所有数据库和表格，除了全文索引示例外，默认使用的都是InnoDB引擎。因为当前InnoDB引擎功能比较完善。

![](img/a3a6a70f1c837db86254e10de5003ae6_10.png)

选择存储引擎主要依据不同的索引方式和功能需求。总体而言，InnoDB的功能最为全面，因此现在默认基本都使用它。

![](img/a3a6a70f1c837db86254e10de5003ae6_12.png)

另一种存储引擎是之前演示全文索引时用到的**MyISAM**。这两种存储引擎各有优势和劣势，但InnoDB的优势更多。可以类比为Windows 7和Windows 10这两个不同版本的操作系统，新版本在大部分方面都进行了优化。

我们常用的两种存储引擎就是MyISAM和InnoDB。

![](img/a3a6a70f1c837db86254e10de5003ae6_14.png)

### 存储引擎的文件格式

我们可以通过查看文件来区分它们。进入MySQL的数据目录，查看数据库文件夹下的文件：

![](img/a3a6a70f1c837db86254e10de5003ae6_16.png)

*   **`.frm` 和 `.ibd`** 文件：属于 **InnoDB** 存储引擎。`.frm`文件存储表结构，`.ibd`文件存储数据。
*   **`.MYD` 和 `.MYI`** 文件：属于 **MyISAM** 存储引擎。`.MYD`文件主要存储数据，`.MYI`文件存储索引（结构）。

目前使用InnoDB格式的情况更多。

### InnoDB 与 MyISAM 特性对比

以下是InnoDB和MyISAM核心特性的对比，了解这些有助于选择合适的引擎：

*   **事务支持**：
    *   **InnoDB**：支持。事务是管理“增删改查”操作，保证数据准确性和安全性的重要机制，下一节课会详细讲解。
    *   **MyISAM**：不支持。这意味着使用MyISAM引擎的表无法使用事务控制语言。
*   **并发性能**：
    *   **InnoDB**：在所有存储引擎中并发性能最好。
    *   **MyISAM**：稍差一些。
*   **外键约束**：
    *   **InnoDB**：支持。外键用于实现表间的数据关联和同步。
    *   **MyISAM**：不支持。
*   **缓存**：InnoDB的缓存机制做得比较好。
*   **全文索引**：
    *   **MyISAM**：支持。
    *   **InnoDB**：较新版本也开始支持，但早期版本不支持。
*   **查询速度**：在只读或读多写少的场景下，MyISAM的查询速度很快，因为它存储了表的行数，相当于有缓存，不需要全表扫描。

![](img/a3a6a70f1c837db86254e10de5003ae6_18.png)

![](img/a3a6a70f1c837db86254e10de5003ae6_20.png)

**总结**：如果不需要事务支持，且读取操作远多于写入操作，MyISAM是很好的选择。但在大多数需要数据完整性和安全性的场景下，支持事务的InnoDB更为重要。InnoDB是目前MySQL默认且使用最广泛的存储引擎。

### 其他存储引擎简介

![](img/a3a6a70f1c837db86254e10de5003ae6_22.png)

除了上述两种，MySQL还有其他存储引擎，以下做一个简单介绍：

![](img/a3a6a70f1c837db86254e10de5003ae6_24.png)

![](img/a3a6a70f1c837db86254e10de5003ae6_26.png)

![](img/a3a6a70f1c837db86254e10de5003ae6_28.png)

![](img/a3a6a70f1c837db86254e10de5003ae6_30.png)

![](img/a3a6a70f1c837db86254e10de5003ae6_32.png)

*   **MEMORY**：数据存储在内存中，不会持久化到磁盘，适用于缓存或临时数据。
*   **BLACKHOLE（黑洞）**：写入的数据会被丢弃（“吞噬”），只保留表结构。适用于不需要实际存储数据，仅作为数据流转节点的特殊场景（例如主从复制中的中继）。下节课会具体演示。
*   **CSV**：数据以CSV格式存储，适合处理大量简单数据或需要导出CSV的场景。
*   **ARCHIVE**：用于存储大量历史数据，仅支持插入和查询。
*   **PERFORMANCE_SCHEMA**：主要用于监控MySQL服务器的性能和行为。
*   **FEDERATED**：允许在多个物理服务器间共享数据。
*   **MERGE**：用于合并多个MyISAM表，提高查询和管理效率。

![](img/a3a6a70f1c837db86254e10de5003ae6_34.png)

对于初学者，**重点掌握InnoDB、MyISAM和BLACKHOLE即可**。InnoDB最常用，MyISAM是其前身且在特定场景下高效，BLACKHOLE用于特殊架构。

![](img/a3a6a70f1c837db86254e10de5003ae6_36.png)

## 管理与修改存储引擎 ⚙️

![](img/a3a6a70f1c837db86254e10de5003ae6_38.png)

![](img/a3a6a70f1c837db86254e10de5003ae6_40.png)

![](img/a3a6a70f1c837db86254e10de5003ae6_42.png)

接下来我们看看如何查看和修改存储引擎。

![](img/a3a6a70f1c837db86254e10de5003ae6_44.png)

![](img/a3a6a70f1c837db86254e10de5003ae6_46.png)

![](img/a3a6a70f1c837db86254e10de5003ae6_48.png)

### 修改方法一：配置文件

可以修改MySQL的配置文件（如`/etc/my.cnf`），在`[mysqld]`部分添加或修改以下参数：
```ini
default-storage-engine=InnoDB
```
将其值改为`MyISAM`或`BLACKHOLE`等，修改后需要重启MySQL服务生效。此方法会改变之后所有新表的默认存储引擎，因此通常用于设置最常用的引擎。

### 修改方法二：建表时指定（推荐）

![](img/a3a6a70f1c837db86254e10de5003ae6_50.png)

![](img/a3a6a70f1c837db86254e10de5003ae6_51.png)

更常用的方式是在创建表时直接指定存储引擎。语法如下：
```sql
CREATE TABLE 表名 (
    列名 数据类型,
    ...
) ENGINE=存储引擎名称;
```
例如，创建一个使用MyISAM引擎的表：
```sql
CREATE TABLE my_table (
    id INT
) ENGINE=MyISAM;
```

![](img/a3a6a70f1c837db86254e10de5003ae6_53.png)

### 修改方法三：修改已有表

可以使用`ALTER TABLE`语句修改已有表的存储引擎：
```sql
ALTER TABLE 表名 ENGINE=新存储引擎名称;
```
**注意**：此操作需谨慎，如果两种引擎支持的特性不同（如MyISAM表有全文索引但转为不支持该索引的引擎），可能导致错误或数据丢失。建议在创建表时就规划好。

### 查看存储引擎

有多种方式可以查看表的存储引擎：

1.  使用 `SHOW CREATE TABLE 表名;` 查看建表语句。
2.  使用 `SHOW TABLE STATUS LIKE ‘表名’;` 查看表的详细信息，其中包含`Engine`字段。

## BLACKHOLE（黑洞）引擎演示 🕳️

最后，我们来演示一下BLACKHOLE引擎的特性。

1.  **创建黑洞引擎表**：
    ```sql
    CREATE TABLE blackhole_demo (
        id INT,
        name VARCHAR(10)
    ) ENGINE=BLACKHOLE;
    ```
2.  **查看表结构**：使用`DESC blackhole_demo;`查看，结构正常。
3.  **插入数据**：
    ```sql
    INSERT INTO blackhole_demo VALUES (1, ‘test’);
    ```
    命令执行成功，无报错。
4.  **查询数据**：
    ```sql
    SELECT * FROM blackhole_demo;
    ```
    查询结果为空。
5.  **查看数据文件**：在数据目录中，只会找到该表对应的`.frm`结构文件，而找不到存储数据的`.ibd`文件（对于InnoDB）或`.MYD`文件（对于MyISAM）。

**黑洞引擎的作用**：它并不存储数据，但会记录二进制日志（Binary Log）。这在一些特定架构中非常有用，例如在配置MySQL主从复制时，某些从库可能仅作为数据中转，不需要本地持久化数据，使用BLACKHOLE引擎可以节省存储空间并简化架构。具体应用场景会在后续课程中结合主从复制详细讲解。

---

![](img/a3a6a70f1c837db86254e10de5003ae6_55.png)

![](img/a3a6a70f1c837db86254e10de5003ae6_57.png)

本节课中我们一起学习了MySQL的存储引擎。我们对比了最常用的InnoDB和MyISAM引擎的特性与适用场景，介绍了查看和修改存储引擎的几种方法，并演示了特殊的BLACKHOLE引擎不存储数据的特性。理解存储引擎是优化数据库性能和设计合理架构的关键一步。下一节课，我们将深入讲解InnoDB引擎的核心特性之一——事务。