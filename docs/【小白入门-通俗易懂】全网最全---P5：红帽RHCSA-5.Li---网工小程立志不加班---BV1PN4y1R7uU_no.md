# Linux入门教程：P5：Linux文件类型、归属关系与基本权限介绍

![](img/8da5ecec0d59681f216c8cbf1109fc0c_1.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_3.png)

在本节课中，我们将深入学习 `ls` 命令，并重点探讨Linux系统中的文件类型、归属关系以及基本权限类别。这些概念是理解Linux文件系统管理的基础。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_5.png)

## 命令回顾：`ls` 命令格式

![](img/8da5ecec0d59681f216c8cbf1109fc0c_7.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_9.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_11.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_13.png)

上一节我们介绍了 `ls` 命令的基本格式。这条命令用于查看目录下的内容以及文件/目录的详细属性。其格式非常灵活，可以同时使用多个选项和参数，也可以不使用。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_15.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_17.png)

`ls [选项]... [参数]...`

![](img/8da5ecec0d59681f216c8cbf1109fc0c_19.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_21.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_23.png)

以下是 `ls` 命令的一些常用选项介绍：

![](img/8da5ecec0d59681f216c8cbf1109fc0c_25.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_27.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_29.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_31.png)

*   **`-a`**：显示目录下的所有内容，包括隐藏文件。在Linux中，以点 `.` 开头的文件或目录被视为隐藏的。
*   **`-l`**：以长格式显示目录下的内容及详细属性。
*   **`-h`**：与 `-l` 选项结合使用时，以人性化的单位（K, M, G）显示文件大小。
*   **`-d`**：仅显示目录本身的属性，而不显示其下的内容。
*   **`-i`**：显示文件或目录的inode编号（唯一标识号）。
*   **`-R`**：递归显示目录及其所有子目录下的内容。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_33.png)

对于初学者，最需要熟练掌握的是 `-a`、`-l` 和 `-h` 这三个选项。

## 文件详细属性解读

当我们使用 `ls -l` 命令时，会看到类似下面的输出：

`-rw-r--r--. 1 root root 0 Dec 10 15:30 hello.txt`

这行输出包含了文件的完整属性信息，我们从左到右进行解读：

1.  **文件类型与权限**：第一个字符代表**文件类型**，后续九个字符代表**权限**。
    *   **文件类型**：
        *   `-`：普通文件（如文本、图片、视频）。
        *   `d`：目录（相当于Windows的文件夹）。
        *   `l`：链接文件（相当于Windows的快捷方式）。
        *   `b`：块设备文件（如硬盘）。
        *   `c`：字符设备文件（如键盘、终端）。
        *   `s`：套接字文件。
        *   `p`：管道文件。
    *   **权限**：九个字符分为三组，每组三个字符，分别代表**所有者(u)**、**所属组(g)** 和**其他人(o)** 的权限。
        *   `r`：读取权限。对于文件，表示可以查看内容；对于目录，表示可以列出目录内容。
        *   `w`：写入权限。对于文件，表示可以修改内容；对于目录，表示可以在其中创建、删除文件。
        *   `x`：执行权限。对于文件，表示可以运行该程序；对于目录，表示可以进入该目录。
        *   `-`：表示没有对应权限。

2.  **硬链接数**：对于文件，表示有多少个文件名指向同一个inode（数据块）。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_35.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_37.png)

3.  **所有者**：文件或目录的拥有者账号。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_39.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_41.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_43.png)

4.  **所属组**：文件或目录所属的用户组。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_45.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_47.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_49.png)

5.  **文件大小**：默认以字节为单位显示。结合 `-h` 选项可以显示为K、M、G等易读格式。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_51.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_53.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_55.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_57.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_59.png)

6.  **最后修改时间**：文件或目录内容最后一次被修改的时间。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_61.png)

7.  **名称**：文件或目录的名字。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_63.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_65.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_67.png)

## 理解归属关系

![](img/8da5ecec0d59681f216c8cbf1109fc0c_69.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_71.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_73.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_75.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_77.png)

在Linux权限体系中，用户被分为三类，这构成了文件的**归属关系**：

![](img/8da5ecec0d59681f216c8cbf1109fc0c_79.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_81.png)

1.  **所有者 (Owner/u)**：创建该文件或目录的用户，拥有对该文件最大的控制权。
2.  **所属组 (Group/g)**：文件所属的用户组。组内的其他成员可以继承该组对文件的权限。通常，一个用户创建文件时，其所属的初始组会成为该文件的默认所属组。
3.  **其他人 (Others/o)**：既不是文件所有者，也不在文件所属组内的其他所有用户。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_83.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_85.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_87.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_89.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_91.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_93.png)

**类比理解**：你买了一个玩具（文件）。
*   **所有者**：就是你，拥有玩具的全部处置权。
*   **所属组**：你的家人或好朋友组成的小组，你可以允许他们一起玩这个玩具。
*   **其他人**：陌生人，跟你和你的小组都没关系，通常不能碰你的玩具。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_95.png)

## 权限类别详解

![](img/8da5ecec0d59681f216c8cbf1109fc0c_97.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_99.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_101.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_103.png)

权限由九个字符表示，分为三组，对应三种归属关系：

![](img/8da5ecec0d59681f216c8cbf1109fc0c_105.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_107.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_109.png)

`-rw-r--r--`

*   前三个字符 `rw-`：**所有者**的权限是**可读(r)**、**可写(w)**，但**不可执行(x)**。
*   中间三个字符 `r--`：**所属组**的权限是**仅可读(r)**。
*   后三个字符 `r--`：**其他人**的权限也是**仅可读(r)**。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_111.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_113.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_115.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_117.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_119.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_121.png)

权限的顺序是固定的：`r`(读)总是在第一位，`w`(写)在第二位，`x`(执行)在第三位。`-` 表示该位置权限缺失。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_123.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_125.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_127.png)

## 通过颜色辨别文件类型

在终端中，`ls` 命令默认会以不同颜色显示不同类型的文件，这是一个非常直观的辨别方法：

*   **蓝色**：目录。
*   **白色**：普通文件。
*   **浅蓝色**：链接文件（快捷方式）。
*   **绿色**：可执行文件（程序或脚本）。
*   **红色**：压缩文件（如 `.tar.gz`, `.zip`）。
*   **黄色**：设备文件（在 `/dev` 目录下）。
*   **粉色**：图片文件（部分系统）。
*   **红色闪烁**：链接文件损坏（指向的源文件不存在）。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_129.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_131.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_132.png)

## 查看指定路径下的内容

![](img/8da5ecec0d59681f216c8cbf1109fc0c_134.png)

`ls` 命令可以查看任何路径下的内容。路径之间使用斜杠 `/` 分隔。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_136.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_138.png)

*   查看根目录下的内容：`ls /`
*   查看 `/etc` 目录下的内容：`ls /etc`
*   查看 `/etc` 目录下 `passwd` 文件的详细信息：`ls -l /etc/passwd`

![](img/8da5ecec0d59681f216c8cbf1109fc0c_140.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_142.png)

路径分隔符 `/` 指明了文件或目录的层级关系。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_144.png)

## 命令行快捷操作技巧

在输入命令时，有一个提高效率的技巧：**`Esc + .`**（先按 `Esc` 键，然后按点号 `.` 键）。

这个操作可以将**上一条命令的参数**，直接粘贴到当前光标位置。例如，你刚执行了 `ls -l /etc/passwd`，接下来想用 `cat` 命令查看这个文件，可以这样操作：

![](img/8da5ecec0d59681f216c8cbf1109fc0c_146.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_148.png)

`cat `（然后按 `Esc + .`）

![](img/8da5ecec0d59681f216c8cbf1109fc0c_150.png)

此时，`/etc/passwd` 会自动补全到命令后面，形成 `cat /etc/passwd`。这能有效避免手动输入长路径时可能出现的错误。

![](img/8da5ecec0d59681f216c8cbf1109fc0c_152.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_154.png)

---

![](img/8da5ecec0d59681f216c8cbf1109fc0c_156.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_158.png)

![](img/8da5ecec0d59681f216c8cbf1109fc0c_160.png)

本节课中我们一起学习了 `ls` 命令的深入用法，重点掌握了Linux系统的文件类型、通过颜色辨别文件、文件的详细属性（特别是权限和归属关系）。理解所有者、所属组和其他人的概念，以及 `r`、`w`、`x` 三种基本权限的含义，是后续进行用户和权限管理的基础。请务必熟悉 `ls -l` 输出的格式，并尝试使用不同选项查看系统中的各种文件。