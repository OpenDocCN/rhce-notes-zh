# Linux基础命令教程：P7：mkdir、cd、pwd、rmdir、touch、cp命令学习 📚

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_1.png)

在本节课中，我们将要学习Linux系统中几个最基础且常用的文件和目录操作命令。这些命令是日常使用Linux的基石，掌握它们将帮助你轻松管理服务器上的文件和目录结构。

---

## 回顾上节课内容

上一节我们介绍了`ls`命令，它用于查看目录下的内容以及文件和目录的详细属性。其中，`-l`、`-a`、`-h`是最常用的三个选项。

## 本节命令概述

本节我们将学习六个核心命令：
1.  **`mkdir`**：创建新目录。
2.  **`cd`**：切换工作目录。
3.  **`pwd`**：打印当前工作目录。
4.  **`rmdir`**：删除空目录。
5.  **`touch`**：创建新的空白文件。
6.  **`cp`**：复制文件或目录。

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_3.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_4.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_6.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_7.png)

---

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_9.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_10.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_11.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_13.png)

## 1. `mkdir` 命令：创建目录 🗂️

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_14.png)

`mkdir`命令的英文全称是“make directory”，用于创建新的目录，类似于在Windows中新建文件夹。

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_16.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_18.png)

**命令格式**：
```bash
mkdir [选项] 目录名
```

### 基本用法

在当前目录下创建一个名为`test`的目录：
```bash
mkdir test
```
执行后无提示信息，可以使用`ls`命令查看是否创建成功。

同时创建多个目录：
```bash
mkdir t1 t2 t3 t4
```

在指定路径下创建目录（例如在`/opt`目录下创建`te`目录）：
```bash
mkdir /opt/te
```

### 常用选项：`-p`

`-p`选项用于递归创建多级目录。例如，你想在`/opt/t2`目录下创建`x1/x2/x3/x4`这样的层级结构，如果逐层创建会很麻烦。

使用`-p`选项可以一条命令完成：
```bash
mkdir -p /opt/t2/x1/x2/x3/x4
```
这条命令会依次创建所有不存在的父目录。

### 注意事项
*   目录名不能包含“/”，因为“/”是根目录和路径分隔符。
*   目录或文件名的长度不能超过255个字符。

---

## 2. `cd` 命令：切换目录 🔄

`cd`命令的英文全称是“change directory”，用于切换当前的工作目录。

**命令格式**：
```bash
cd [目录名]
```

### 基本用法

切换到指定目录（例如`/etc`）：
```bash
cd /etc
```

如果不指定目录名，直接输入`cd`，则会切换到当前用户的家目录（对于root用户是`/root`）：
```bash
cd
```

### 路径概念：绝对路径与相对路径

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_20.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_21.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_23.png)

*   **绝对路径**：以根目录`/`为起点的完整路径。例如`/opt/t2/x1`。
*   **相对路径**：以当前所在目录为起点的路径。例如，如果你当前在`/opt`目录，那么`t2/x1`就是相对路径。

使用绝对路径可以在任何位置准确切换到目标目录。使用相对路径则更快捷，但前提是你清楚当前目录与目标目录的相对关系。

### 快捷操作（懒人操作）

*   `~`：代表当前用户的家目录。`cd ~`等同于`cd`。
*   `.`：代表当前目录。`cd .`通常用于执行当前目录下的脚本（如`./script.sh`）。
*   `..`：代表上一级目录。`cd ..`可以向上切换一级。
*   `-`：在两个最近切换过的目录之间来回切换。例如，从目录A切换到目录B后，输入`cd -`可以立刻回到目录A。

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_25.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_26.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_28.png)

---

## 3. `pwd` 命令：显示当前目录 📍

`pwd`命令的英文全称是“print working directory”，用于打印（显示）你当前所在工作目录的绝对路径。

**命令格式**：
```bash
pwd
```
这条命令非常简单，直接执行即可。当你身处复杂的目录层级中时，它能帮你快速定位自己的位置。

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_30.png)

---

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_32.png)

## 4. `rmdir` 命令：删除空目录 🗑️

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_34.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_35.png)

`rmdir`命令用于删除**空目录**。如果目录内有任何文件或子目录，该命令将无法执行。

**命令格式**：
```bash
rmdir 目录名
```

### 用法与限制

删除一个空目录：
```bash
rmdir empty_dir
```

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_37.png)

尝试删除一个非空目录（例如`/opt/t2`，其下还有`x1`等子目录）会失败：
```bash
rmdir /opt/t2
# 输出：rmdir: failed to remove ‘/opt/t2’: Directory not empty
```

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_39.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_40.png)

由于`rmdir`只能删除空目录，在实际工作中使用频率较低。我们通常使用功能更强大的`rm`命令来删除目录及其内容（后续课程会讲到）。

---

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_42.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_44.png)

## 5. `touch` 命令：创建文件 📄

`touch`命令用于创建新的空白文件，类似于在Windows中新建文本文档。

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_46.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_47.png)

**命令格式**：
```bash
touch [选项] 文件名
```

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_49.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_51.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_52.png)

### 基本用法

在当前目录创建一个名为`test.txt`的文件：
```bash
touch test.txt
```

同时创建多个文件：
```bash
touch test1.txt test2.txt test3.txt
```

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_54.png)

在指定目录创建文件：
```bash
touch /opt/t3/newfile.txt
```

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_56.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_57.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_59.png)

> **注意**：在Linux中，文件扩展名（如`.txt`）不是必须的，系统主要通过文件内容来识别类型。添加扩展名主要是为了方便用户识别，以及在跨平台（如传到Windows系统）时能被正确识别。

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_61.png)

---

## 6. `cp` 命令：复制文件或目录 📋

`cp`命令的英文全称是“copy”，用于复制文件或目录，类似于Windows中的“复制-粘贴”操作。

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_63.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_64.png)

**命令格式**：
```bash
cp [选项] 源文件或目录 目标文件或目录
```

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_66.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_68.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_70.png)

### 复制文件

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_72.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_74.png)

将当前目录下的`test1.txt`文件复制到`/media`目录：
```bash
cp test1.txt /media
```
复制操作不会影响源文件。

同时复制多个文件到目标目录：
```bash
cp test2.txt test3.txt /media
```

### 复制目录

复制目录时必须使用`-r`（或`-R`）选项，表示递归复制，即复制目录及其内部所有子目录和文件。
```bash
cp -r /opt/t2 /media
```

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_76.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_78.png)

### 常用选项：`-p`

`-p`选项可以在复制时保留源文件的属性（如修改时间、权限等）不变。这在做数据备份时非常有用。

比较不加`-p`和加`-p`的区别：
```bash
# 普通复制，文件的“修改时间”会变为复制时的时间
cp file.txt /backup/

# 保留属性复制，文件的“修改时间”等属性与源文件一致
cp -p file.txt /backup/
```

### 复制时重命名

在指定目标时使用新名字，即可在复制的同时重命名。
```bash
# 将 test1.txt 复制到 /opt/t1 目录，并重命名为 abc.txt
cp test1.txt /opt/t1/abc.txt
```

---

## 总结 🎯

本节课我们一起学习了Linux中六个基础的文件和目录操作命令：
*   **`mkdir`** 和 **`touch`** 分别用于创建目录和文件。
*   **`cd`** 用于在不同目录间导航，理解了**绝对路径**和**相对路径**的概念。
*   **`pwd`** 用于快速确认自己所在的位置。
*   **`rmdir`** 用于删除空目录（了解即可，实际使用较少）。
*   **`cp`** 用于复制文件或目录，并掌握了保留属性复制(`-p`)和递归复制目录(`-r`)的用法。

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_80.png)

![](img/4f7de08a094a7c3cbc80cc3945d8d70b_82.png)

这些命令是Linux操作的基石，请务必多加练习，熟悉它们的各种用法。下一节课，我们将继续学习其他重要的文件管理命令。