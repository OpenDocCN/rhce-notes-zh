# Linux运维培训教程：1.7：mkdir、cd、pwd、rmdir、touch、cp命令学习 📚

![](img/3e39d2de561dbcf23e1e43c298a051ec_1.png)

在本节课中，我们将要学习Linux系统中几个最基础且常用的文件和目录操作命令。这些命令是日常运维工作的基石，掌握它们能帮助你高效地管理服务器上的数据。

上节课我们介绍了`ls`命令，用于查看目录内容。本节中我们来看看如何创建、切换、查看、删除目录以及创建和复制文件。

---

![](img/3e39d2de561dbcf23e1e43c298a051ec_3.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_5.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_7.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_9.png)

## 创建目录：mkdir命令

![](img/3e39d2de561dbcf23e1e43c298a051ec_11.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_13.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_15.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_17.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_18.png)

`mkdir`命令用于创建新的目录，其英文全称为“make directory”。它的功能类似于在Windows系统中新建一个文件夹。

![](img/3e39d2de561dbcf23e1e43c298a051ec_20.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_22.png)

**命令格式**：
```bash
mkdir [选项] 目录名
```

以下是`mkdir`命令的常用方法：

*   **创建单个目录**：`mkdir test` 会在当前目录下创建一个名为`test`的目录。
*   **创建多个目录**：`mkdir test1 test2 test3` 可以同时创建多个目录。
*   **在指定路径创建目录**：`mkdir /opt/t1` 会在`/opt`目录下创建名为`t1`的目录。
*   **递归创建多级目录**：使用`-p`选项可以一次性创建多层嵌套的目录结构。例如，`mkdir -p /opt/t2/x1/x2/x3` 会创建从`t2`到`x3`的完整目录链。

**注意事项**：
*   目录名不能包含“/”字符，因为它是路径分隔符。
*   目录名长度通常不超过255个字符。

---

## 切换目录：cd命令

`cd`命令用于切换当前的工作目录，其英文全称为“change directory”。这是导航文件系统最常用的命令。

**命令格式**：
```bash
cd [目录名]
```

如果不指定目录名，直接输入`cd`，则会切换到当前用户的家目录。

在理解`cd`命令时，需要掌握两个核心概念：**绝对路径**和**相对路径**。
*   **绝对路径**：以根目录`/`为起点，描述目标位置的完整路径。例如：`cd /opt/t2/x1/x2/x3/x4`。
*   **相对路径**：以当前目录为起点，描述目标位置的路径。例如，当前在`/opt`目录时，`cd t2`即可进入`t2`子目录。

![](img/3e39d2de561dbcf23e1e43c298a051ec_24.png)

以下是`cd`命令的一些快捷操作（懒人操作）：
*   `cd ~` 或 `cd`：切换到当前用户的家目录。
*   `cd .`：切换到当前目录（通常用于执行当前目录下的脚本或文件）。
*   `cd ..`：切换到上一级目录。
*   `cd -`：在两个最近访问过的目录之间来回切换。

![](img/3e39d2de561dbcf23e1e43c298a051ec_26.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_28.png)

---

## 查看当前目录：pwd命令

![](img/3e39d2de561dbcf23e1e43c298a051ec_30.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_32.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_34.png)

在频繁切换目录后，你可能需要确认自己当前所在的位置。`pwd`命令就是用于解决这个问题的。

`pwd`命令的英文全称是“print working directory”，即打印当前工作目录。执行该命令后，它会显示你当前所在目录的**绝对路径**。

![](img/3e39d2de561dbcf23e1e43c298a051ec_36.png)

**命令格式**：
```bash
pwd
```

![](img/3e39d2de561dbcf23e1e43c298a051ec_38.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_40.png)

例如，执行`pwd`后，可能会输出`/opt/t2/x1`，这明确告诉你正位于这个目录中。

![](img/3e39d2de561dbcf23e1e43c298a051ec_42.png)

---

## 删除空目录：rmdir命令

![](img/3e39d2de561dbcf23e1e43c298a051ec_44.png)

`rmdir`命令用于删除**空目录**，其英文全称为“remove directory”。

![](img/3e39d2de561dbcf23e1e43c298a051ec_46.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_48.png)

**命令格式**：
```bash
rmdir 目录名
```

![](img/3e39d2de561dbcf23e1e43c298a051ec_50.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_52.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_54.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_56.png)

这条命令的使用限制很大：它只能删除内容为空的目录。如果目录内包含任何文件或子目录，`rmdir`命令会执行失败并报错“目录非空”。

![](img/3e39d2de561dbcf23e1e43c298a051ec_58.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_60.png)

因此，`rmdir`在实际工作中使用频率较低。我们通常使用功能更强大的`rm`命令来删除目录及其所有内容，这将在后续课程中介绍。

![](img/3e39d2de561dbcf23e1e43c298a051ec_62.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_64.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_66.png)

---

## 创建文件：touch命令

![](img/3e39d2de561dbcf23e1e43c298a051ec_68.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_70.png)

`touch`命令用于创建新的空白文件，其功能类似于在Windows中“新建文本文档”。

![](img/3e39d2de561dbcf23e1e43c298a051ec_72.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_74.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_76.png)

**命令格式**：
```bash
touch [选项] 文件名
```

`touch`命令的用法与`mkdir`非常相似：
*   **创建单个文件**：`touch test.txt`
*   **创建多个文件**：`touch file1.txt file2.txt file3.txt`
*   **在指定路径创建文件**：`touch /opt/test1.txt`

![](img/3e39d2de561dbcf23e1e43c298a051ec_78.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_80.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_82.png)

在Linux中，文件扩展名（如`.txt`）不是必需的，系统主要通过文件内容来识别类型。添加扩展名主要是为了方便用户识别，或在与其他系统（如Windows）交互时使用。

![](img/3e39d2de561dbcf23e1e43c298a051ec_84.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_86.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_88.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_90.png)

---

## 复制文件或目录：cp命令

![](img/3e39d2de561dbcf23e1e43c298a051ec_92.png)

`cp`命令用于复制文件或目录，其英文全称为“copy file”。它相当于Windows中的“复制”+“粘贴”操作。

![](img/3e39d2de561dbcf23e1e43c298a051ec_94.png)

**命令格式**：
```bash
cp [选项] 源文件或目录 目标目录或新文件名
```

以下是`cp`命令的核心用法和选项：

*   **复制文件**：`cp source.txt /target/directory/` 将文件复制到目标目录。
*   **复制时重命名**：`cp source.txt /target/directory/newname.txt` 在复制的同时为文件改名。
*   **复制多个文件**：`cp file1.txt file2.txt /target/directory/` 可以同时复制多个文件到目标目录。
*   **复制目录**：必须使用`-r`（递归）选项来复制目录及其内部所有内容。例如：`cp -r /source/dir /target/directory/`。
*   **保留文件属性**：使用`-p`选项可以在复制时保留源文件的原始属性（如修改时间、权限等），这在做备份时非常有用。例如：`cp -p oldfile.txt backup/`。

**核心概念**：
*   **源**：你想要复制的原始文件或目录。
*   **目标**：你想要将副本放置的位置。如果目标参数以目录结尾，文件将复制到该目录下；如果指定了一个新文件名，则文件会被复制并重命名。

---

![](img/3e39d2de561dbcf23e1e43c298a051ec_96.png)

![](img/3e39d2de561dbcf23e1e43c298a051ec_98.png)

本节课中我们一起学习了Linux系统中六个基础命令：`mkdir`（创建目录）、`cd`（切换目录）、`pwd`（查看当前目录）、`rmdir`（删除空目录）、`touch`（创建文件）和`cp`（复制文件或目录）。这些命令是构建更复杂操作的基础，请务必通过练习熟练掌握它们。