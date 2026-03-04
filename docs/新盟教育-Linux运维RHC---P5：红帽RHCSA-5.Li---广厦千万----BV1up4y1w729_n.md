# Linux运维RHCSA+RHC培训教程：5：Linux文件类型、归属关系与基本权限

![](img/95b9f79bd257c3d390ce11afdff36e35_1.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_2.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_4.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_6.png)

在本节课中，我们将学习Linux系统中文件的核心概念，包括如何识别不同的文件类型、理解文件的归属关系以及解读文件的基本权限。这些知识是理解Linux文件系统管理和安全机制的基础。

![](img/95b9f79bd257c3d390ce11afdff36e35_8.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_9.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_10.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_12.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_14.png)

## 文件类型介绍

![](img/95b9f79bd257c3d390ce11afdff36e35_16.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_18.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_19.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_21.png)

上一节我们介绍了`ls`命令的基本用法，本节中我们来看看如何通过`ls -l`命令的输出结果来识别不同的文件类型。

![](img/95b9f79bd257c3d390ce11afdff36e35_23.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_25.png)

在Linux系统中，一切皆文件，但文件有不同的类型。使用`ls -l`命令时，输出行最左侧的第一个字符代表文件类型。

以下是常见的文件类型及其标识符：

*   **`-`**：普通文件。例如文本文件、图片、视频、程序等。
*   **`d`**：目录。类似于Windows系统中的文件夹，用于存放其他文件或目录。
*   **`l`**：链接文件。类似于Windows系统中的快捷方式，指向另一个文件或目录。
*   **`b`**：块设备文件。代表硬盘等块存储设备。
*   **`c`**：字符设备文件。代表键盘、鼠标等字符型输入输出设备。
*   **`s`**：套接字文件。用于进程间网络通信。
*   **`p`**：管道文件。用于进程间通信。

例如，`ls -l /etc/passwd`命令的输出行首字符是`-`，表示`/etc/passwd`是一个普通文件。

![](img/95b9f79bd257c3d390ce11afdff36e35_27.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_29.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_31.png)

## 文件归属关系介绍

![](img/95b9f79bd257c3d390ce11afdff36e35_33.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_35.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_37.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_39.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_41.png)

理解了文件类型后，我们来看看文件的归属关系。Linux系统为每个文件定义了三种归属身份，用于权限管理。

![](img/95b9f79bd257c3d390ce11afdff36e35_43.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_44.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_45.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_46.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_47.png)

文件的归属关系分为三类：

![](img/95b9f79bd257c3d390ce11afdff36e35_49.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_51.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_53.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_55.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_57.png)

*   **所有者 (Owner/u)**：文件的创建者或拥有者，通常拥有对该文件的最高权限。
*   **所属组 (Group/g)**：文件所属的用户组。组内的其他用户会继承该组的文件权限。
*   **其他人 (Others/o)**：既不是文件所有者，也不在文件所属组内的其他所有用户。

![](img/95b9f79bd257c3d390ce11afdff36e35_59.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_61.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_63.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_65.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_67.png)

在`ls -l`命令的输出中，所有者显示在权限字段之后，例如：
`-rw-r--r--. 1 root root 1765 Dec 11 2023 /etc/passwd`
这里的第一个`root`是文件的所有者，第二个`root`是文件的所属组。

![](img/95b9f79bd257c3d390ce11afdff36e35_69.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_70.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_72.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_74.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_75.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_77.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_79.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_81.png)

## 文件基本权限类别介绍

![](img/95b9f79bd257c3d390ce11afdff36e35_83.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_85.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_87.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_89.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_91.png)

最后，我们来学习文件的基本权限。权限决定了用户能对文件进行何种操作。

![](img/95b9f79bd257c3d390ce11afdff36e35_93.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_95.png)

Linux文件的基本权限有三种：

![](img/95b9f79bd257c3d390ce11afdff36e35_97.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_99.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_101.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_103.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_105.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_107.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_109.png)

*   **读取 (r)**：允许查看文件内容或列出目录中的文件列表。
*   **写入 (w)**：允许修改文件内容，或在目录中创建、删除文件。
*   **执行 (x)**：允许运行文件（如果是程序或脚本），或允许进入目录。

![](img/95b9f79bd257c3d390ce11afdff36e35_111.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_113.png)

在`ls -l`命令的输出中，权限由9个字符表示，分为三组，每组三个字符，分别对应**所有者**、**所属组**和**其他人**的权限。
权限顺序固定为：`rwx`。如果某个位置是`-`，则表示没有对应权限。

![](img/95b9f79bd257c3d390ce11afdff36e35_115.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_117.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_119.png)

例如，对于权限字符串 `rwxr-xr--`：
*   所有者权限：`rwx` (可读、可写、可执行)
*   所属组权限：`r-x` (可读、不可写、可执行)
*   其他人权限：`r--` (只读)

![](img/95b9f79bd257c3d390ce11afdff36e35_121.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_123.png)

## 通过颜色辨别文件类型

![](img/95b9f79bd257c3d390ce11afdff36e35_125.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_127.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_129.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_131.png)

除了查看详细属性，Linux终端通常会用颜色来快速区分文件类型，这对初学者非常友好。

以下是常见的颜色及其含义：

![](img/95b9f79bd257c3d390ce11afdff36e35_133.png)

*   **蓝色**：目录。
*   **白色**：普通文件。
*   **浅蓝色**：链接文件。
*   **绿色**：可执行文件。
*   **红色**：压缩文件（如 `.tar.gz`, `.zip`）。
*   **黄色**：设备文件（在 `/dev` 目录下）。
*   **粉色**：图片文件或套接字文件。
*   **红色闪烁**：指向一个不存在目标的坏链接。

![](img/95b9f79bd257c3d390ce11afdff36e35_135.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_137.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_138.png)

## 总结

![](img/95b9f79bd257c3d390ce11afdff36e35_140.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_142.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_144.png)

![](img/95b9f79bd257c3d390ce11afdff36e35_146.png)

本节课中我们一起学习了Linux文件系统的几个核心概念。我们了解了如何通过`ls -l`命令识别不同的文件类型（如普通文件`-`、目录`d`、链接`l`），理解了文件的三种归属关系（所有者、所属组、其他人），并学会了解读文件的基本权限（读`r`、写`w`、执行`x`）。此外，我们还知道了如何通过终端颜色快速辨别文件类型。掌握这些基础知识，是后续进行文件操作和权限管理的重要前提。