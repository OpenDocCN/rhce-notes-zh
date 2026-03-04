# Linux磁盘管理：24：MBR分区、文件系统与挂载详解

![](img/3cd20a3e0310babffc7cbee99e5af81d_1.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_3.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_5.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_7.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_9.png)

在本节课中，我们将学习Linux系统中关于磁盘管理的核心知识，包括如何使用`fdisk`命令进行MBR分区、文件系统的作用与类型，以及如何通过挂载操作来使用磁盘分区。课程内容将循序渐进，确保初学者能够轻松理解。

## 概述：磁盘分区与使用流程

![](img/3cd20a3e0310babffc7cbee99e5af81d_11.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_13.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_15.png)

在Linux系统中，要使用一块新的硬盘，通常需要经过三个主要步骤：**分区**、**格式化**和**挂载**。分区是将一整块物理硬盘划分为多个逻辑区域；格式化是为这些分区创建文件管理系统；挂载则是将分区关联到系统目录，以便用户能够访问和使用其存储空间。本节我们将详细探讨这些概念和操作。

![](img/3cd20a3e0310babffc7cbee99e5af81d_17.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_19.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_21.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_23.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_25.png)

## 第一节：MBR分区实战

![](img/3cd20a3e0310babffc7cbee99e5af81d_27.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_29.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_31.png)

上一节我们介绍了MBR分区格式的理论知识，本节中我们来看看如何使用`fdisk`命令进行实际操作。

![](img/3cd20a3e0310babffc7cbee99e5af81d_33.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_35.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_37.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_39.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_41.png)

`fdisk`命令是用于查看磁盘信息和进行MBR格式分区的主要工具。其基本语法是 `fdisk [设备路径]`，设备路径通常是`/dev/`目录下的硬盘文件，如`/dev/sdb`。

在开始分区前，我们先了解一下`/dev`目录。该目录包含了系统中所有的硬件设备文件。

以下是`/dev`目录下一些常见的设备文件类型：
*   **`sd*`**：SCSI或SATA接口的硬盘，如`sda`, `sdb`。
*   **`hd*`**：IDE接口的硬盘（较旧）。
*   **`sr0` / `cdrom`**：光盘驱动器。
*   **`loop*`**：回环设备。
*   **`tty*`**：虚拟终端设备。
*   **`null`**：空设备（黑洞设备），写入的数据会被丢弃。
*   **`zero`**：零设备，提供无限的零字节流。

现在，我们以`/dev/sdb`这块100G的硬盘为例进行分区。首先执行命令：
```bash
fdisk /dev/sdb
```
进入交互式界面后，可以输入`m`获取帮助。常用命令如下：
*   `p`：打印当前磁盘的分区表。
*   `n`：创建一个新分区。
*   `d`：删除一个分区。
*   `w`：保存更改并退出。
*   `q`：不保存更改并退出。

输入`p`查看，可以看到磁盘标签类型为`dos`，这代表其采用MBR分区格式。接下来创建分区：
1.  输入`n`创建新分区。
2.  选择分区类型，`p`为主分区，`e`为扩展分区。MBR允许最多4个主分区。
3.  设置分区编号（1-4）。
4.  设置起始扇区，通常直接回车使用默认值。
5.  设置分区大小，例如`+10G`表示创建10GB的分区。

重复以上步骤可以创建多个分区。当需要创建超过4个分区时，必须将其中一个主分区创建为**扩展分区**（类型选`e`），然后在扩展分区内创建**逻辑分区**（编号从5开始）。所有操作在输入`w`命令之前都只保存在内存中，不会实际生效。

## 第二节：文件系统介绍

上一节我们学会了如何划分分区，本节中我们来看看如何让分区能够存储和管理数据，这就需要引入**文件系统**的概念。

![](img/3cd20a3e0310babffc7cbee99e5af81d_43.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_45.png)

文件系统是操作系统用于管理分区中数据的结构和方法的集合。它负责文件的存储、检索、更新和控制。没有文件系统的分区无法直接存储文件。

Linux支持多种文件系统，目前最主流的是**XFS**和**ext4**。

以下是两种主流文件系统的核心特性对比：
*   **XFS**
    *   **特点**：高性能日志文件系统，数据恢复速度快。
    *   **最大分区容量**：8 EB (Exabyte)
    *   **最大文件大小**：500 TB
    *   **应用**：CentOS/RHEL 7及更高版本的默认文件系统。
*   **ext4**
    *   **特点**：第四代扩展文件系统，稳定可靠。
    *   **最大分区容量**：1 EB
    *   **最大文件大小**：16 TB
    *   **应用**：CentOS/RHEL 6的默认文件系统。

![](img/3cd20a3e0310babffc7cbee99e5af81d_47.png)

要为分区创建文件系统，需要使用格式化命令。格式为 `mkfs.文件系统类型 [设备路径]`。
例如，将`/dev/sdb1`格式化为XFS文件系统：
```bash
mkfs.xfs /dev/sdb1
```
格式化为ext4文件系统：
```bash
mkfs.ext4 /dev/sdb1
```
可以使用 `lsblk -f` 命令查看分区的文件系统类型。

![](img/3cd20a3e0310babffc7cbee99e5af81d_49.png)

## 第三节：挂载操作详解

![](img/3cd20a3e0310babffc7cbee99e5af81d_51.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_53.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_55.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_57.png)

上一节我们为分区创建了文件系统，本节中我们来看看如何通过**挂载**操作，让用户能够访问和使用分区空间。

![](img/3cd20a3e0310babffc7cbee99e5af81d_59.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_61.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_63.png)

在Linux中，用户不能直接访问硬件设备（如`/dev/sdb1`）。必须通过一个**目录**作为入口来访问，这个目录称为**挂载点**。将分区关联到目录的过程就是挂载。

挂载的基本命令是 `mount`，语法为 `mount [设备路径] [挂载点目录]`。
例如，将格式化好的`/dev/sdb1`挂载到`/db_backup`目录：
```bash
mount /dev/sdb1 /db_backup
```
挂载成功后，所有存入`/db_backup`目录的文件，实际上都存储在了`/dev/sdb1`分区中。可以使用 `df -h` 命令查看已挂载的文件系统及其使用情况。

关于挂载，有几个重要的注意事项：
1.  挂载点理论上应是一个**空目录**。如果挂载点非空，挂载后原目录内容会被暂时隐藏，卸载后恢复。
2.  一个分区不能同时挂载到多个目录。
3.  一个目录不能同时挂载多个分区。

![](img/3cd20a3e0310babffc7cbee99e5af81d_65.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_66.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_68.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_70.png)

当需要移除分区访问时，使用 `umount` 命令卸载。可以指定设备路径或挂载点：
```bash
umount /dev/sdb1
# 或
umount /db_backup
```
卸载后，挂载点目录恢复为空（或显示原有的隐藏文件），分区中的数据保持不变。

![](img/3cd20a3e0310babffc7cbee99e5af81d_72.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_74.png)

## 总结

![](img/3cd20a3e0310babffc7cbee99e5af81d_76.png)

![](img/3cd20a3e0310babffc7cbee99e5af81d_78.png)

本节课中我们一起学习了Linux磁盘管理的核心流程。
1.  **分区**：使用`fdisk`命令对硬盘进行MBR格式的分区规划，理解主分区、扩展分区和逻辑分区的区别。
2.  **格式化**：使用`mkfs`命令为分区创建文件系统（如XFS或ext4），使其能够存储和管理文件。
3.  **挂载**：使用`mount`命令将分区关联到系统目录（挂载点），使得用户可以通过目录访问分区空间；使用`umount`命令可以解除这种关联。

![](img/3cd20a3e0310babffc7cbee99e5af81d_80.png)

通过这三个步骤，我们就能将一块物理硬盘变为Linux系统中可用的存储空间。理解并掌握这些操作，是进行系统管理和维护的重要基础。