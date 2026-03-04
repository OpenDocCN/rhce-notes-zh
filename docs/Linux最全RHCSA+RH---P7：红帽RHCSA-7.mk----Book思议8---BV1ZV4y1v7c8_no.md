# Linux基础命令教程：P7：mkdir、cd、pwd、rmdir、touch、cp命令学习 📚

![](img/f994cc05092bdecc5cf75a28e2192da8_1.png)

在本节课中，我们将要学习Linux系统中几个最基础且最常用的文件和目录操作命令。我们将从创建目录开始，逐步学习如何切换工作目录、查看当前路径、删除空目录、创建文件以及复制文件和目录。这些命令是日常使用Linux系统的基础，掌握它们对于后续的学习至关重要。

---

## 创建目录：mkdir命令

上一节我们回顾了文件和目录的基本概念，本节中我们来看看如何创建新的目录。在Linux中，`mkdir`命令用于创建新的目录，其功能类似于Windows系统中的“新建文件夹”。

![](img/f994cc05092bdecc5cf75a28e2192da8_3.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_5.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_7.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_9.png)

**命令格式**：
```bash
mkdir [选项] 目录名
```

![](img/f994cc05092bdecc5cf75a28e2192da8_11.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_13.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_15.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_17.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_18.png)

**常用选项**：
*   `-p`：递归创建多级目录。

![](img/f994cc05092bdecc5cf75a28e2192da8_20.png)

以下是`mkdir`命令的基本用法演示：

![](img/f994cc05092bdecc5cf75a28e2192da8_22.png)

1.  **在当前目录创建单个目录**：
    ```bash
    mkdir test
    ```
    执行后，会在当前所在目录下创建一个名为`test`的目录。

2.  **在当前目录同时创建多个目录**：
    ```bash
    mkdir test1 test2 test3 test4
    ```
    这条命令会在当前目录下一次性创建`test1`、`test2`、`test3`、`test4`四个目录。

3.  **在指定路径创建目录**：
    ```bash
    mkdir /opt/t1
    ```
    这条命令会在根目录下的`/opt`目录中创建一个名为`t1`的目录。**注意**：如果要同时在`/opt`下创建多个目录，必须为每个目录指定完整路径，例如`mkdir /opt/t2 /opt/t3`。

4.  **使用`-p`选项递归创建多级目录**：
    ```bash
    mkdir -p /opt/t2/x1/x2/x3/x4
    ```
    这条命令会创建一整个目录层级结构。如果不加`-p`选项，当上级目录（如`x1`）不存在时，命令会执行失败。

**注意事项**：
*   目录名不能包含“/”字符，因为“/”在Linux中代表根目录和路径分隔符。
*   目录或文件名的长度不能超过255个字符。

---

## 切换工作目录：cd命令

学会了创建目录，接下来我们需要知道如何在不同的目录之间移动。`cd`命令就是用于切换当前工作目录的工具。

**命令格式**：
```bash
cd [目录名]
```

如果不指定目录名，直接输入`cd`，则会切换到当前用户的家目录。

**路径概念**：
在使用`cd`命令时，需要理解两种路径表示方法：
*   **绝对路径**：以根目录`/`为起点，描述目标位置的完整路径。例如：`cd /opt/t2/x1/x2`。
*   **相对路径**：以当前目录为起点，描述目标位置的路径。例如，如果当前在`/opt`目录，要进入`t2`，只需输入`cd t2`。

以下是`cd`命令的一些快捷操作：

1.  **`cd ~` 或 `cd`**：切换到当前用户的家目录。
2.  **`cd -`**：在两个最近工作过的目录之间快速切换。
3.  **`cd ..`**：切换到上一级目录。
4.  **`cd .`**：切换到当前目录（通常用于执行当前目录下的脚本或文件）。

![](img/f994cc05092bdecc5cf75a28e2192da8_24.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_26.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_28.png)

---

## 显示当前目录：pwd命令

在频繁切换目录后，有时会忘记自己身处何处。`pwd`命令可以清晰地告诉你当前所在目录的绝对路径。

![](img/f994cc05092bdecc5cf75a28e2192da8_30.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_32.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_34.png)

**命令格式**：
```bash
pwd
```

这条命令没有选项，直接执行即可。例如，执行后可能显示`/root`或`/home/username`，这明确指出了你当前的工作位置。

---

![](img/f994cc05092bdecc5cf75a28e2192da8_36.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_38.png)

## 删除空目录：rmdir命令

![](img/f994cc05092bdecc5cf75a28e2192da8_40.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_42.png)

有创建就有删除。`rmdir`命令用于删除**空目录**。请注意，它只能删除里面没有任何内容的目录。

**命令格式**：
```bash
rmdir 目录名
```

例如，要删除一个名为`empty_dir`的空目录，可以执行：
```bash
rmdir empty_dir
```

如果目录非空（包含文件或其他子目录），`rmdir`命令会报错并拒绝删除。因此，这条命令在实际工作中使用频率较低，我们将在后续课程学习功能更强大的删除命令`rm`。

![](img/f994cc05092bdecc5cf75a28e2192da8_44.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_46.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_48.png)

---

## 创建文件：touch命令

![](img/f994cc05092bdecc5cf75a28e2192da8_50.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_52.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_54.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_56.png)

除了目录，我们经常需要创建文件。`touch`命令的主要功能是创建新的空白文件。

![](img/f994cc05092bdecc5cf75a28e2192da8_58.png)

**命令格式**：
```bash
touch 文件名
```

![](img/f994cc05092bdecc5cf75a28e2192da8_60.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_62.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_64.png)

其用法与`mkdir`非常相似：

![](img/f994cc05092bdecc5cf75a28e2192da8_66.png)

1.  **创建单个文件**：
    ```bash
    touch test.txt
    ```
    会在当前目录创建一个名为`test.txt`的空文件。Linux不强制要求文件扩展名，添加`.txt`是为了方便识别。

2.  **同时创建多个文件**：
    ```bash
    touch file1 file2 file3
    ```

![](img/f994cc05092bdecc5cf75a28e2192da8_68.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_70.png)

3.  **在指定路径创建文件**：
    ```bash
    touch /opt/test1 /opt/test2
    ```

![](img/f994cc05092bdecc5cf75a28e2192da8_72.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_74.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_76.png)

---

## 复制文件或目录：cp命令

![](img/f994cc05092bdecc5cf75a28e2192da8_78.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_80.png)

最后，我们来学习如何复制文件和目录。`cp`命令相当于Windows中的“复制-粘贴”操作。

![](img/f994cc05092bdecc5cf75a28e2192da8_82.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_84.png)

**命令格式**：
```bash
cp [选项] 源文件或目录 目标目录或新文件名
```

![](img/f994cc05092bdecc5cf75a28e2192da8_86.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_88.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_90.png)

**常用选项**：
*   `-r`：递归复制，用于复制目录及其内部所有内容。
*   `-p`：保留源文件的属性（如修改时间、权限等）不变。

以下是`cp`命令的核心用法：

1.  **复制文件**：
    ```bash
    cp file1 /tmp/
    ```
    将当前目录的`file1`复制到`/tmp`目录下，文件名不变。

![](img/f994cc05092bdecc5cf75a28e2192da8_92.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_94.png)

2.  **复制文件并重命名**：
    ```bash
    cp file1 /tmp/file_backup.txt
    ```
    将`file1`复制到`/tmp`目录下，并重命名为`file_backup.txt`。

3.  **同时复制多个文件**：
    ```bash
    cp file1 file2 file3 /destination/
    ```

4.  **复制目录（必须加`-r`选项）**：
    ```bash
    cp -r dir1 /backup/
    ```
    将`dir1`目录及其内部所有文件和子目录完整地复制到`/backup/`目录中。

5.  **保留属性复制（常用于备份）**：
    ```bash
    cp -p important.conf /backup/
    ```
    将`important.conf`文件复制到备份目录，并保持其原始的时间戳、权限等属性。

---

## 总结

本节课中我们一起学习了Linux系统六个基础的文件和目录操作命令：
*   **`mkdir`**：创建目录。
*   **`cd`**：切换工作目录。
*   **`pwd`**：显示当前工作目录的绝对路径。
*   **`rmdir`**：删除空目录（使用受限）。
*   **`touch`**：创建空白文件。
*   **`cp`**：复制文件或目录。

![](img/f994cc05092bdecc5cf75a28e2192da8_96.png)

![](img/f994cc05092bdecc5cf75a28e2192da8_98.png)

这些命令是构建Linux操作能力的基石，请务必通过练习熟练掌握它们。下一节课，我们将继续学习更多关于文件操作和管理的命令。