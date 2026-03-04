# Linux文件系统：P18：文件系统组成和基本操作 📁

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_0.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_2.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_4.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_6.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_8.png)

在本节课中，我们将要学习Linux文件系统的基本组成结构，以及如何使用`cd`和`ls`命令来浏览和查看系统中的目录与文件。我们将从根目录开始，了解各个核心目录的作用，并掌握绝对路径与相对路径的区别及用法。

## 文件系统目录结构

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_10.png)

Linux文件系统采用树状结构，所有目录都从根目录（`/`）开始。以下是几个核心目录及其作用。

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_12.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_14.png)

### `/usr/bin` 与 `/usr/sbin`

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_16.png)

`/usr/bin`目录存放普通用户可执行的命令。例如，`ls`、`cat`等常用命令通常位于此目录下。

`/usr/sbin`目录存放系统管理员（root用户）执行的系统管理命令。例如，磁盘分区命令`fdisk`就位于此目录下。系统管理命令通常需要较高权限才能执行。

### `/usr/local`

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_18.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_19.png)

`/usr/local`目录是第三方或自定义软件的默认安装目录。当您安装非系统自带的软件时，其程序文件和相关配置通常会被放置在此目录下。

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_21.png)

### `/etc`

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_23.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_25.png)

`/etc`目录是系统配置文件的集中存放地。系统的各种配置，如主机名、网络设置、用户账户信息等，都通过此目录下的文本文件进行管理。例如，主机名配置文件是`/etc/hostname`。

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_27.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_29.png)

### `/var`

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_31.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_33.png)

`/var`目录用于存放经常变化的（Variable）数据，例如系统日志、网站内容等。运行中的服务（如Web服务器）产生的数据通常存放在此目录下。

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_35.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_37.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_39.png)

### `/tmp`

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_41.png)

`/tmp`目录是系统的临时文件目录。系统或程序在运行过程中产生的临时文件会存放在这里，这些文件可能会被定期清理。请注意，它并非类似Windows的“回收站”。

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_43.png)

### `/boot`

`/boot`目录存放系统启动过程中所必需的文件，例如内核文件、引导加载程序等。

### `/dev`

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_45.png)

`/dev`目录包含了所有的设备文件。在Linux中，硬件设备（如磁盘、终端）都被抽象为文件，存放在此目录下以便访问。

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_47.png)

### `/proc` 与 `/sys`

`/proc`和`/sys`是两个特殊的虚拟文件系统目录。它们并不占用硬盘空间，其内容映射自系统内存，反映了当前系统的实时运行状态和内核参数。

### 目录链接关系

在较新的Linux发行版（如RHEL 8）中，根目录下的`/bin`和`/sbin`实际上是`/usr/bin`和`/usr/sbin`的软链接（快捷方式）。您可以使用`ls -l /`命令查看这种链接关系。

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_49.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_51.png)

```bash
ls -l /bin
```
该命令会显示`/bin -> usr/bin`，表明`/bin`是指向`/usr/bin`的快捷方式。

## 文件名与路径规则

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_53.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_55.png)

在命名文件和目录时，需要遵循以下规则：
*   文件名长度不能超过255个字符。
*   除了正斜杠（`/`）因为被用作根目录和路径分隔符而不能出现在文件名中，其他字符基本都是有效的。
*   系统对文件名大小写敏感，`File.txt`和`file.txt`是两个不同的文件。
*   建议为文件和目录起有意义的名字，避免使用特殊字符，以提高可读性。

## 绝对路径与相对路径

路径用于定位文件或目录的位置，主要分为绝对路径和相对路径两种。

### 绝对路径

绝对路径是从根目录（`/`）开始的完整路径。其特点是**在任何当前工作目录下都能唯一、准确地指向同一个目标**。

例如，`/etc/ssh/sshd_config`就是一个绝对路径。无论您当前在哪个目录，使用该路径都能访问到同一个配置文件。

### 相对路径

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_57.png)

相对路径是相对于当前工作目录（Current Working Directory）的路径。它**不以斜杠开头**，其有效性依赖于当前所在的位置。

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_59.png)

例如，假设当前目录是`/etc`，那么`ssh/sshd_config`就是一个相对路径，它等价于绝对路径`/etc/ssh/sshd_config`。如果您切换到`/tmp`目录，再使用`ssh/sshd_config`这个相对路径就会出错，因为`/tmp`目录下不存在`ssh`子目录。

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_61.png)

**何时使用**：当您已经位于目标目录的父目录或附近时，使用相对路径更为简洁方便。

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_63.png)

## 目录导航命令 `cd`

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_65.png)

`cd`（Change Directory）命令用于切换当前工作目录。

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_67.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_69.png)

以下是`cd`命令的一些常用用法：
*   `cd /path/to/dir`：切换到指定的绝对或相对路径。
*   `cd` 或 `cd ~`：直接返回当前用户的家目录（如root用户是`/root`）。
*   `cd ~username`：切换到指定用户的家目录（如`cd ~admin`切换到admin用户的家目录）。
*   `cd ..`：切换到上一级目录（父目录）。
*   `cd -`：切换到上一次所在的工作目录，可以在两个目录间快速切换。

## 查看目录内容命令 `ls`

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_71.png)

`ls`（List）命令用于列出目录中的文件和子目录。

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_73.png)

### 常用选项

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_75.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_77.png)

*   `ls -l`：以长格式显示详细信息。
*   `ls -a`：显示所有文件，包括以点（`.`）开头的隐藏文件。
*   `ls -R`：递归显示目录及其所有子目录的内容。
*   `ls -d`：仅显示目录本身的信息，而不是其内容。常用于查看目录的属性，如`ls -ld /etc`。

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_79.png)

### 理解 `ls -l` 的输出

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_81.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_83.png)

以长格式（`-l`）列出时，会显示类似以下的信息：
```
-rw-r--r--. 1 root root 1234 Jun 10 15:30 example.txt
```
各字段含义如下：
1.  **文件类型与权限**：第一个字符表示类型（`-`普通文件，`d`目录，`l`链接文件，`b`块设备，`c`字符设备），后续字符表示权限。
2.  **链接数**：对于文件，表示有多少个文件名指向此文件（硬链接数）；对于目录，表示其包含的子目录总数（至少为2，包含`.`和`..`）。
3.  **所有者**：文件或目录的属主。
4.  **所属组**：文件或目录的属组。
5.  **大小**：文件的大小（字节）。
6.  **修改时间**：文件内容最后一次被修改的时间。
7.  **名称**：文件或目录名。

---

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_85.png)

![](img/a73d50f33cf8cbe2ddc88fc3396b8dfb_87.png)

本节课中我们一起学习了Linux文件系统的核心目录结构及其功能，掌握了区分和使用绝对路径与相对路径的方法。同时，我们深入练习了`cd`和`ls`这两个最基础的目录导航与查看命令，包括它们的关键选项和输出信息的解读。这些是熟练使用Linux命令行进行文件管理的基础。