# Redhat红帽 RHCE8.0认证体系课程：P7：从命令行管理文件 📂

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_1.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_3.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_5.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_7.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_9.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_11.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_13.png)

在本节课中，我们将学习如何在Linux命令行中管理文件和目录。我们将从理解文件路径开始，逐步学习如何列出、查看、创建、移动、复制和删除文件。这些是Linux系统管理中最基础也是最重要的操作。

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_15.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_17.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_19.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_21.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_23.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_25.png)

## 理解文件路径

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_27.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_29.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_31.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_33.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_35.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_37.png)

在Linux系统中，一切皆文件，包括目录、设备、配置等。要访问一个文件，我们需要知道它的路径。路径分为两种：绝对路径和相对路径。

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_39.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_41.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_43.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_45.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_47.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_49.png)

*   **绝对路径**：从根目录 `/` 开始的完整路径。例如，网卡配置文件的绝对路径是 `/etc/sysconfig/network-scripts/ifcfg-ens160`。
*   **相对路径**：相对于当前工作目录的路径。当前工作目录可以用 `pwd` 命令查看。

以下是几个常用的路径符号：
*   `~` 或 `$HOME`：代表当前用户的家目录。
*   `.`：代表当前目录。
*   `..`：代表上一级目录。

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_51.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_53.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_55.png)

上一节我们介绍了文件路径的概念，本节中我们来看看如何列出和查看文件。

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_57.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_59.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_61.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_63.png)

## 列出文件和目录：`ls` 命令

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_65.png)

`ls` 命令用于列出目录内容及其属性。它是使用最频繁的命令之一。

以下是 `ls` 命令的一些常用选项：

*   `ls -l`：以长格式列出详细信息。
*   `ls -a`：显示所有文件，包括以 `.` 开头的隐藏文件。
*   `ls -d`：仅显示目录本身的信息，而不是其内容。
*   `ls -h`：与 `-l` 结合使用，以人类可读的格式（如K、M、G）显示文件大小。
*   `ls -t`：按修改时间排序，最新的在前。
*   `ls -r`：反向排序。

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_67.png)

**长格式输出详解**
执行 `ls -l` 命令后，会看到类似下面的输出：
```
-rw-r--r--. 1 root root 1584 Jun 20 23:09 ifcfg-ens160
```
从左到右各字段含义如下：
1.  **文件类型与权限**：第一个字符表示文件类型（`-`普通文件，`d`目录，`l`链接文件，`b`块设备，`c`字符设备，`s`套接字）。后9个字符表示权限（`r`读，`w`写，`x`执行）。
2.  **链接数**：指向此文件或目录的硬链接数量。
3.  **所有者**：文件或目录的拥有者。
4.  **所属组**：文件或目录所属的用户组。
5.  **大小**：文件大小，默认以字节为单位。使用 `-h` 选项可转换为易读格式。
6.  **修改时间**：文件或目录最后被修改的日期和时间。
7.  **名称**：文件或目录的名称。

## 切换工作目录：`cd` 命令

`cd` 命令用于改变当前工作目录。

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_69.png)

以下是 `cd` 命令的常用用法：
*   `cd ~` 或 `cd`：切换到当前用户的家目录。
*   `cd /path/to/dir`：切换到指定的绝对路径。
*   `cd ..`：切换到上一级目录。
*   `cd -`：切换到上一次所在的目录。

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_71.png)

## 管理目录

学会了如何查看和切换目录，接下来我们学习如何创建和删除目录。

### 创建目录：`mkdir` 命令

`mkdir` 命令用于创建新目录。

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_73.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_75.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_77.png)

常用选项：
*   `mkdir dirname`：创建单个目录。
*   `mkdir -p /path/to/dir`：递归创建目录。如果父目录不存在，则会一并创建。

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_79.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_81.png)

### 删除目录：`rmdir` 和 `rm` 命令

*   `rmdir dirname`：**仅能删除空目录**。
*   `rm -r dirname`：递归删除目录及其内部所有内容。**这是一个危险命令，请谨慎使用**。系统通常会默认添加 `-i` 选项进行交互式确认。

## 管理文件

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_83.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_85.png)

### 创建文件

*   `touch filename`：创建一个空的文件，或更新已有文件的时间戳。
*   `echo “content” > filename`：创建文件并写入内容。`>` 符号表示重定向输出。

### 查看文件内容

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_87.png)

*   `cat filename`：一次性显示文件的全部内容。适合查看小文件。
    *   `cat -n filename`：显示行号。
*   `more filename`：分页显示文件内容，只能向下翻页，按空格键翻页。
*   `less filename`：分页显示文件内容，功能比 `more` 更强大，可以上下翻页（使用方向键或Page Up/Page Down），支持搜索（按 `/` 后输入关键词，`n` 查找下一个，`N` 查找上一个）。
*   `head filename`：显示文件开头部分，默认10行。
    *   `head -n 5 filename`：显示文件前5行。
*   `tail filename`：显示文件末尾部分，默认10行。
    *   `tail -n 5 filename`：显示文件最后5行。
    *   `tail -f filename`：实时追踪文件末尾的新增内容，常用于监控日志文件，按 `Ctrl+C` 退出。

### 复制、移动和重命名文件

*   `cp source destination`：复制文件或目录。
    *   `cp -r sourcedir destdir`：递归复制目录。
    *   `cp -a sourcedir destdir`：归档复制，保留文件的所有属性（如权限、时间戳）。
*   `mv source destination`：移动或重命名文件/目录。
    *   如果 `destination` 是已存在的目录，则将源文件**移动**到该目录下。
    *   如果 `destination` 是不存在的路径或文件名，则执行**重命名**操作。

### 删除文件

*   `rm filename`：删除文件。同样，默认带有 `-i` 交互确认选项。
*   `rm -f filename`：强制删除，不进行确认。**极度危险，请勿随意使用**。

**⚠️ 重要警告**
`rm -rf /*` 或 `rm -rf /` 等命令会强制递归删除根目录下的所有文件，导致系统崩溃且数据无法恢复。在生产环境中，必须极其谨慎地使用 `rm` 命令，并建议通过堡垒机等审计工具记录所有操作。

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_89.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_91.png)

## 关于 `/usr` 目录的说明

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_93.png)

![](img/4b2a1a3d0a5daefbacedae26ef79b1bc_95.png)

`/usr` 目录的全称是 **Unix System Resources**（Unix系统资源），它通常用于存放系统安装的应用程序和只读数据，而不是用户（user）目录。这是一个历史悠久的命名约定，请不要误解。

## 总结

本节课中我们一起学习了Linux命令行下管理文件和目录的核心操作。我们掌握了：
1.  理解**绝对路径**和**相对路径**。
2.  使用 `ls` 命令**列出**文件并理解其详细输出。
3.  使用 `cd` 命令**切换**工作目录。
4.  使用 `mkdir` 和 `rmdir`/`rm` **创建和删除**目录。
5.  使用 `touch` 和 `echo` **创建**文件。
6.  使用 `cat`, `more`, `less`, `head`, `tail` **查看**文件内容。
7.  使用 `cp` 和 `mv` **复制、移动和重命名**文件与目录。
8.  理解 `rm` 命令的**危险性**并谨慎使用。

这些命令是Linux日常操作的基础，请务必通过练习熟练掌握。在接下来的课程中，我们将深入学习文件权限和软硬链接等更高级的概念。