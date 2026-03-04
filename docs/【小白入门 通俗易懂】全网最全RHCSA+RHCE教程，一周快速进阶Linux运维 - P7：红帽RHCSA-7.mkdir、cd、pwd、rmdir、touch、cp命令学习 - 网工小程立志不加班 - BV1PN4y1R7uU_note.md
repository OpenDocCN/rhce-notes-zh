# Linux基础命令教程：P7：mkdir、cd、pwd、rmdir、touch、cp命令学习 📚

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_1.png)

## 概述
在本节课中，我们将学习Linux系统中几个最基础且最常用的命令。我们将了解如何创建目录、切换工作路径、查看当前位置、删除空目录、创建文件以及复制文件和目录。掌握这些命令是熟练操作Linux系统的第一步。

---

## 上节回顾
上一节我们介绍了`ls`命令，用于查看目录内容和文件属性。本节中，我们来看看如何创建和管理目录与文件。

## 1. mkdir命令：创建目录 📁
`mkdir`命令用于创建新的目录，其英文全称为“make directory”。这类似于在Windows系统中新建一个文件夹。

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_3.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_5.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_7.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_9.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_11.png)

**命令格式**：
```
mkdir [选项] 目录名
```

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_13.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_15.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_17.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_18.png)

**常用选项**：
*   `-p`：递归创建多级目录。

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_20.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_22.png)

### 基本用法
以下是如何使用`mkdir`命令创建目录。

**示例1：在当前目录创建单个目录**
```bash
mkdir test
```
执行后，会在你当前所在的目录下创建一个名为`test`的目录。

**示例2：在指定路径创建目录**
```bash
mkdir /opt/t1
```
这将在根目录下的`/opt`目录中创建一个名为`t1`的目录。

**示例3：同时创建多个目录**
```bash
mkdir test1 test2 test3 test4
```
这条命令会在当前目录下同时创建`test1`、`test2`、`test3`、`test4`四个目录。

### 使用 `-p` 选项创建层级目录
有时我们需要创建多层嵌套的目录结构。例如，想在`/opt/t2`目录下创建`x1/x2/x3/x4`这样的层级目录。

如果不加`-p`选项，需要逐层创建：
```bash
mkdir /opt/t2/x1
mkdir /opt/t2/x1/x2
mkdir /opt/t2/x1/x2/x3
mkdir /opt/t2/x1/x2/x3/x4
```

使用`-p`选项，可以一条命令完成：
```bash
mkdir -p /opt/t2/x1/x2/x3/x4
```
`-p`选项会递归地创建所有不存在的父目录。

### 注意事项
*   目录名不能包含“/”字符，因为“/”在Linux中代表根目录和路径分隔符。
*   目录或文件名的长度通常不能超过255个字符。

---

## 2. cd命令：切换目录 🔄
`cd`命令用于切换当前的工作目录，其英文全称为“change directory”。

**命令格式**：
```
cd [目录名]
```

### 绝对路径与相对路径
在切换目录时，需要理解两种路径表示方法：

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_24.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_26.png)

*   **绝对路径**：以根目录（`/`）为起点的完整路径。
    ```bash
    cd /opt/t2/x1/x2/x3/x4
    ```

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_28.png)

*   **相对路径**：以当前所在目录为起点的路径。
    ```bash
    # 假设当前在 /opt 目录
    cd t2/x1
    ```

### 快捷操作
`cd`命令有一些非常实用的快捷操作：

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_30.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_32.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_34.png)

*   `cd` 或 `cd ~`：直接切换到当前用户的家目录。
*   `cd -`：在两个最近访问过的目录之间快速切换。
*   `cd ..`：切换到上一级目录。
*   `cd .`：切换到当前目录（通常用于执行当前目录下的脚本或文件）。

---

## 3. pwd命令：显示当前目录 📍
`pwd`命令用于打印当前工作目录的绝对路径，其英文全称为“print working directory”。

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_36.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_38.png)

**命令格式**：
```
pwd
```

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_40.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_42.png)

**示例**：
当你进入一个很深的目录后，可以使用`pwd`来确认自己的确切位置。
```bash
pwd
# 输出可能为：/opt/t2/x1/x2/x3/x4
```

---

## 4. rmdir命令：删除空目录 🗑️
`rmdir`命令用于删除**空目录**。如果目录内有内容，此命令将无法执行。

**命令格式**：
```
rmdir 目录名
```

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_44.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_46.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_48.png)

**示例**：
```bash
rmdir test
```
这条命令会尝试删除当前目录下名为`test`的空目录。

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_50.png)

**局限性**：
由于`rmdir`只能删除空目录，在实际工作中使用频率较低。要删除非空目录，我们通常使用功能更强大的`rm`命令（将在后续课程中学习）。

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_52.png)

---

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_54.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_56.png)

## 5. touch命令：创建文件 📄
`touch`命令用于创建新的空白文件，类似于在Windows中新建一个文本文档。

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_58.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_60.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_62.png)

**命令格式**：
```
touch 文件名
```

### 基本用法
**示例1：在当前目录创建单个文件**
```bash
touch test.txt
```

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_64.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_66.png)

**示例2：在指定路径创建文件**
```bash
touch /opt/test1.txt
```

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_68.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_70.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_72.png)

**示例3：同时创建多个文件**
```bash
touch file1.txt file2.txt file3.txt
```

**说明**：
在Linux中，文件扩展名（如`.txt`）不是必需的，系统主要通过文件内容来识别类型。添加扩展名主要是为了方便用户识别，或与Windows系统兼容。

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_74.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_76.png)

---

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_78.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_80.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_82.png)

## 6. cp命令：复制文件或目录 📋
`cp`命令用于复制文件或目录，其英文全称为“copy”。这相当于Windows中的“复制”和“粘贴”操作。

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_84.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_86.png)

**命令格式**：
```
cp [选项] 源文件或目录 目标目录或新文件名
```

**常用选项**：
*   `-r`：递归复制，用于复制目录及其内部所有内容。
*   `-p`：保留源文件的属性（如修改时间、权限等）不变。

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_88.png)

### 复制文件
**示例1：将文件复制到另一个目录（不改名）**
```bash
cp test1.txt /media/
```
将当前目录下的`test1.txt`文件复制到`/media/`目录下，文件名不变。

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_90.png)

**示例2：复制时重命名**
```bash
cp test1.txt /media/abc.txt
```
将`test1.txt`文件复制到`/media/`目录下，并重命名为`abc.txt`。

**示例3：同时复制多个文件**
```bash
cp test1.txt test2.txt test3.txt /media/
```

### 复制目录
复制目录时，必须使用`-r`选项。
```bash
cp -r /opt/t2 /media/
```
将`/opt/t2`目录及其内部所有子目录和文件，完整地复制到`/media/`目录下。

### 使用 `-p` 选项保留文件属性
在进行重要文件备份时，我们可能希望备份文件与源文件的所有属性（如创建/修改时间）完全一致。
```bash
cp -p source_file.txt /backup/
```
使用`-p`选项后，复制到`/backup/`目录下的`source_file.txt`，其属性将与源文件完全相同。

---

## 总结
本节课我们一起学习了Linux系统中六个基础且核心的命令：
1.  **`mkdir`**：创建目录，可使用`-p`创建多级目录。
2.  **`cd`**：切换工作目录，需理解绝对路径与相对路径。
3.  **`pwd`**：显示当前所在的绝对路径。
4.  **`rmdir`**：删除空目录（使用有局限）。
5.  **`touch`**：创建空白文件。
6.  **`cp`**：复制文件或目录，常用`-r`复制目录，`-p`保留属性。

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_92.png)

![](img/834a02ed5b3ff6b2d30daa6dc98c4676_94.png)

这些命令是日常操作Linux的基础，请务必多加练习，熟悉它们的用法。下一节课，我们将继续学习其他重要的文件管理命令。