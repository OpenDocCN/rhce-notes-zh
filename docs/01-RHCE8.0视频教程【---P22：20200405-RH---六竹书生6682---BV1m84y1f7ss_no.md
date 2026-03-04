# Linux系统管理：05：磁盘分区管理

![](img/067d677d780d94fcccac5fea7fbfa178_0.png)

![](img/067d677d780d94fcccac5fea7fbfa178_2.png)

在本节课中，我们将学习Linux系统中磁盘分区的基础知识与管理方法。课程将涵盖分区的概念、MBR与GPT分区表的区别，以及如何使用`fdisk`、`gdisk`和`parted`命令进行分区操作。

![](img/067d677d780d94fcccac5fea7fbfa178_4.png)

## 概述：磁盘与分区

![](img/067d677d780d94fcccac5fea7fbfa178_6.png)

在Linux系统中，新添加的硬盘需要经过分区和格式化后才能使用。这个过程类似于规划房间：分区是划分出不同的房间，而格式化则是为每个房间铺设地板和打上格子，以便有序存放文件。本节我们重点讲解分区操作。

![](img/067d677d780d94fcccac5fea7fbfa178_8.png)

![](img/067d677d780d94fcccac5fea7fbfa178_10.png)

## 识别新硬盘

![](img/067d677d780d94fcccac5fea7fbfa178_12.png)

![](img/067d677d780d94fcccac5fea7fbfa178_14.png)

![](img/067d677d780d94fcccac5fea7fbfa178_16.png)

在虚拟机中添加新硬盘后，系统可能不会立即识别。我们可以使用命令查看所有硬盘信息。

![](img/067d677d780d94fcccac5fea7fbfa178_18.png)

![](img/067d677d780d94fcccac5fea7fbfa178_20.png)

![](img/067d677d780d94fcccac5fea7fbfa178_22.png)

以下是查看系统硬盘信息的命令：

![](img/067d677d780d94fcccac5fea7fbfa178_24.png)

![](img/067d677d780d94fcccac5fea7fbfa178_26.png)

```bash
cat /proc/partitions
```

![](img/067d677d780d94fcccac5fea7fbfa178_28.png)

![](img/067d677d780d94fcccac5fea7fbfa178_29.png)

如果图形化界面未能显示新硬盘，但命令行可以识别，这属于正常现象，我们可以继续通过命令行进行操作。

## 分区基础概念

上一节我们介绍了识别硬盘，本节中我们来看看如何对硬盘进行分区。分区前，需要了解两种主要的分区表格式：**MBR** 和 **GPT**。

### MBR（主引导记录）

MBR是一种较老的分区格式，它使用磁盘最开始的512字节来存储信息。
*   **446字节**：引导程序。
*   **64字节**：分区表，每条分区记录占用16字节，因此最多只能记录**4条**主分区信息。
*   **2字节**：结束标志。

由于分区表条目限制，MBR格式下分区方案有特定规则：
*   **主分区**：可以直接使用，最多创建4个。
*   **扩展分区**：不能直接使用，需在其内部创建**逻辑分区**。一个MBR磁盘只能有一个扩展分区，且“主分区+扩展分区”总数不超过4个。
*   **逻辑分区**：在扩展分区内创建，数量不限，可以直接使用。

**公式**：`主分区数量 + 扩展分区数量 ≤ 4`

### GPT（GUID分区表）

GPT是一种现代的分区格式，它突破了MBR的限制，理论上支持创建多达**128个**主分区，并且支持更大容量的磁盘。

![](img/067d677d780d94fcccac5fea7fbfa178_31.png)

![](img/067d677d780d94fcccac5fea7fbfa178_33.png)

## 分区管理工具

接下来，我们将学习三种常用的分区工具：`fdisk`、`gdisk`和`parted`。

![](img/067d677d780d94fcccac5fea7fbfa178_35.png)

![](img/067d677d780d94fcccac5fea7fbfa178_37.png)

![](img/067d677d780d94fcccac5fea7fbfa178_39.png)

### 使用 `fdisk` 管理 MBR 分区

![](img/067d677d780d94fcccac5fea7fbfa178_41.png)

`fdisk` 命令默认用于管理MBR格式的磁盘。它是一个交互式工具。

以下是进入`fdisk`交互界面的命令：

```bash
fdisk /dev/nvme0n2
```

进入交互模式后，可以使用以下常用命令：
*   `m`：显示帮助菜单。
*   `n`：创建新分区。
*   `d`：删除分区。
*   `p`：打印当前分区表。
*   `t`：更改分区类型ID（例如，82代表swap，83代表Linux，8e代表LVM）。
*   `w`：保存更改并退出。
*   `q`：不保存更改并退出。

**重要提示**：在`fdisk`中所做的修改在输入`w`命令之前，都仅保存在内存中，不会写入磁盘。输入`q`则会放弃所有更改。

#### 创建主分区

输入`n`命令后，选择`p`创建主分区，然后按提示输入分区编号、起始扇区和大小即可。

#### 创建扩展分区与逻辑分区

1.  输入`n`命令后，选择`e`创建扩展分区。
2.  扩展分区创建后，再次输入`n`命令，此时选项会自动变为`l`（逻辑分区），即可在扩展分区内创建逻辑分区。

### 使用 `gdisk` 管理 GPT 分区

`gdisk` 命令用于管理GPT格式的磁盘，其操作方式与`fdisk`非常相似。

以下是进入`gdisk`交互界面的命令：

```bash
gdisk /dev/nvme0n2
```

如果对原本是MBR格式的磁盘使用`gdisk`，工具会自动在内存中将其转换为GPT格式进行管理。其交互命令（如`n`， `p`， `w`， `q`）与`fdisk`类似。

### 使用 `parted` 进行高级分区操作

`parted` 命令功能更强大，可以交互或非交互式运行，并且能处理MBR和GPT两种格式。

![](img/067d677d780d94fcccac5fea7fbfa178_43.png)

![](img/067d677d780d94fcccac5fea7fbfa178_45.png)

#### 交互模式

进入交互模式的方法如下：

```bash
parted /dev/nvme0n2
```

#### 非交互模式（修改分区表格式）

`parted` 的一个常见用途是直接修改磁盘的分区表格式。

*   **将磁盘改为GPT格式**：
    ```bash
    parted -s /dev/nvme0n2 mklabel gpt
    ```
*   **将磁盘改为MBR（msdos）格式**：
    ```bash
    parted -s /dev/nvme0n2 mklabel msdos
    ```
*   **清除磁盘标签**：
    ```bash
    parted -s /dev/nvme0n2 mklabel loop
    ```

**警告**：修改分区表格式会**删除磁盘上所有现有分区**，请谨慎操作。

## 同步分区表信息

有时创建分区后，系统可能无法立即识别。这时需要手动通知内核重新读取分区表。

以下是同步分区信息的命令：

![](img/067d677d780d94fcccac5fea7fbfa178_47.png)

![](img/067d677d780d94fcccac5fea7fbfa178_49.png)

```bash
partprobe /dev/nvme0n2
```

执行此命令后，再使用`cat /proc/partitions`或`lsblk`命令通常就能看到新的分区了。

## 总结

![](img/067d677d780d94fcccac5fea7fbfa178_51.png)

本节课中我们一起学习了Linux磁盘分区的核心知识。我们了解了MBR和GPT两种分区表格式的区别与限制，并重点掌握了使用`fdisk`工具进行MBR分区（包括主分区、扩展分区和逻辑分区）的交互式操作方法。同时，我们也介绍了`gdisk`用于GPT分区，以及`parted`工具用于修改分区表格式等高级操作。记住，分区只是磁盘使用的第一步，接下来还需要对分区进行格式化才能存储数据。