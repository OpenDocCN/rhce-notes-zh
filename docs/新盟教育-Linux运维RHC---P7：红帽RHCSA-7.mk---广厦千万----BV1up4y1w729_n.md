# Linux基础命令教程：P7：mkdir、cd、pwd、rmdir、touch、cp命令学习 📁

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_1.png)

在本节课中，我们将要学习Linux系统中几个最基础且最常用的文件和目录操作命令。我们将从创建目录开始，逐步学习如何切换工作目录、查看当前路径、删除空目录、创建文件以及复制文件和目录。这些命令是日常使用Linux系统的基础，掌握它们对于后续的学习至关重要。

---

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_3.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_4.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_6.png)

## 创建目录：mkdir命令

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_7.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_9.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_10.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_11.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_13.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_14.png)

上一节我们回顾了`ls`命令，本节中我们来看看如何创建新的目录。在Linux中，创建新目录的命令是`mkdir`，它是“make directory”的缩写。

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_16.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_18.png)

**命令格式**如下：
```bash
mkdir [选项] 目录名
```
其中，`[选项]`表示可选参数，而`目录名`是必须指定的参数，代表你要创建的目录名称。

以下是`mkdir`命令的基本用法和注意事项：

*   **基本创建**：直接使用`mkdir`后跟目录名，即可在当前目录下创建新目录。例如，`mkdir test`会在当前目录创建一个名为`test`的目录。
*   **指定路径创建**：你可以在目录名前加上完整路径，以在指定位置创建目录。例如，`mkdir /opt/myfolder`会在`/opt`目录下创建`myfolder`。
*   **同时创建多个目录**：`mkdir`命令可以一次性创建多个目录，只需用空格隔开目录名即可。例如，`mkdir dir1 dir2 dir3`。
*   **递归创建目录（-p选项）**：`-p`选项允许你创建嵌套的多级目录结构。如果父目录不存在，它会一并创建。例如，`mkdir -p /a/b/c/d`会一次性创建`a`、`b`、`c`、`d`这一系列目录。
*   **命名限制**：目录名不能包含斜杠`/`，因为`/`在Linux中代表根目录或路径分隔符。此外，目录名的长度通常不能超过255个字符。

---

## 切换工作目录：cd命令

创建了目录之后，我们经常需要进入目录进行操作。`cd`命令就是用于切换当前工作目录的，它是“change directory”的缩写。

**命令格式**如下：
```bash
cd [目录名]
```
如果不指定目录名，直接输入`cd`，则会切换到当前用户的家目录。

在理解`cd`命令前，我们需要先了解两个核心概念：**绝对路径**和**相对路径**。
*   **绝对路径**：以根目录`/`为起点，描述目标目录的完整路径。例如，`cd /usr/local/bin`。
*   **相对路径**：以当前目录为起点，描述目标目录的路径。例如，当前在`/usr`目录，`cd local`即可进入`/usr/local`。

以下是`cd`命令的一些快捷操作：

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_20.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_21.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_23.png)

*   `cd ~` 或 `cd`：切换到当前用户的家目录。
*   `cd -`：在两个最近访问过的目录之间快速切换。
*   `cd ..`：切换到上一级目录。
*   `cd .`：切换到当前目录（通常用于脚本执行等场景）。

---

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_25.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_26.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_28.png)

## 查看当前路径：pwd命令

在复杂的目录结构中切换后，有时会忘记自己身处何处。`pwd`命令可以清晰地告诉你当前所在的工作目录的绝对路径，它是“print working directory”的缩写。

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_30.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_32.png)

**命令格式**非常简单：
```bash
pwd
```
执行后，它会直接输出当前目录的完整路径。例如，如果你在`/home/user/Documents`目录下执行`pwd`，终端就会显示`/home/user/Documents`。

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_34.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_35.png)

---

## 删除空目录：rmdir命令

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_37.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_39.png)

有创建就有删除。`rmdir`命令用于删除**空目录**，它是“remove directory”的缩写。

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_40.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_42.png)

**命令格式**如下：
```bash
rmdir [选项] 目录名
```
**重要限制**：`rmdir`命令只能删除内容为空的目录。如果目录内包含任何文件或子目录，命令将执行失败并报错“目录非空”。

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_44.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_46.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_47.png)

因此，`rmdir`在实际工作中使用场景有限。如果需要删除非空目录，我们通常使用功能更强大的`rm`命令（配合`-r`选项），这将在后续课程中学习。

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_49.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_50.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_52.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_54.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_55.png)

---

## 创建空白文件：touch命令

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_57.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_59.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_60.png)

除了目录，我们还需要创建文件。`touch`命令的主要用途是创建新的空白文件。

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_62.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_64.png)

**命令格式**如下：
```bash
touch [选项] 文件名
```
它的用法与`mkdir`非常相似：
*   **基本创建**：`touch file.txt`会在当前目录创建一个名为`file.txt`的空白文件。
*   **同时创建多个文件**：`touch a.txt b.txt c.txt`。
*   **指定路径创建**：`touch /path/to/newfile`。

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_66.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_67.png)

在Linux中，文件扩展名（如`.txt`、`.sh`）不像Windows那样具有强制关联性，主要是为了用户识别方便。

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_69.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_71.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_73.png)

---

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_75.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_77.png)

## 复制文件或目录：cp命令

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_79.png)

最后，我们来学习复制操作。`cp`命令用于复制文件或目录，它是“copy”的缩写。

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_81.png)

**命令格式**如下：
```bash
cp [选项] 源文件或目录 目标文件或目录
```
*   **源**：你想要复制的文件或目录。
*   **目标**：复制后文件或目录存放的位置或新名称。

以下是`cp`命令的核心用法和常用选项：

*   **复制文件**：`cp source.txt destination/` 将`source.txt`复制到`destination`目录下，名字不变。
*   **复制时改名**：`cp source.txt destination/newname.txt` 将文件复制到目标位置并重命名。
*   **复制目录（-r选项）**：必须使用`-r`（递归）选项来复制目录及其内部所有内容。例如，`cp -r dir1/ dir2/`。
*   **保留文件属性（-p选项）**：`-p`选项可以在复制时保留源文件的属性（如修改时间、权限等），这在做备份时非常有用。例如，`cp -p oldfile backup/`。

---

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_83.png)

![](img/a9e09eed7477a0ba05f402e60b7a7f9f_85.png)

本节课中我们一起学习了Linux系统中六个基础命令：`mkdir`（创建目录）、`cd`（切换目录）、`pwd`（查看当前路径）、`rmdir`（删除空目录）、`touch`（创建文件）和`cp`（复制）。这些命令是构建Linux操作能力的基石，请务必通过练习熟练掌握它们。在接下来的课程中，我们将学习更多关于文件操作和管理的命令。