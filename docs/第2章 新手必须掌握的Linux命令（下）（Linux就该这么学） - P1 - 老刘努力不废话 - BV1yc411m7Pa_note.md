# Linux新手教程：第2章：新手必须掌握的Linux命令（下）📚

![](img/bff1b6c819ac181e3d8c49a369de3014_1.png)

在本节课中，我们将继续学习Linux中用于管理文本文件的核心命令。我们将掌握如何查看、编辑、比较、压缩文件，以及如何管理文件的时间戳和权限。这些命令是日常系统操作的基础。

上一节我们学习了如何查找和定位系统中的文件。本节中，我们来看看如何对这些文件进行具体的“增删改查”操作。

![](img/bff1b6c819ac181e3d8c49a369de3014_3.png)

![](img/bff1b6c819ac181e3d8c49a369de3014_5.png)

---

## 登录与准备工作 🔐

![](img/bff1b6c819ac181e3d8c49a369de3014_7.png)

首先，请使用管理员（root）身份登录服务器。在文件管理操作中，管理员权限可以避免因权限不足导致的操作失败。关于用户权限的详细知识，我们将在第5章深入探讨。

登录后，请打开终端（Terminal）准备开始操作。

![](img/bff1b6c819ac181e3d8c49a369de3014_9.png)

---

![](img/bff1b6c819ac181e3d8c49a369de3014_11.png)

![](img/bff1b6c819ac181e3d8c49a369de3014_13.png)

## 查看文件内容 📖

以下是查看文件内容的几种常用命令。

### cat 命令

`cat` 命令用于查看内容较少的文件。其基本语法是命令后接文件名。

![](img/bff1b6c819ac181e3d8c49a369de3014_15.png)

![](img/bff1b6c819ac181e3d8c49a369de3014_17.png)

```bash
cat filename
```

![](img/bff1b6c819ac181e3d8c49a369de3014_19.png)

![](img/bff1b6c819ac181e3d8c49a369de3014_21.png)

![](img/bff1b6c819ac181e3d8c49a369de3014_23.png)

如果想在查看时显示行号，可以加上 `-n` 参数。

![](img/bff1b6c819ac181e3d8c49a369de3014_25.png)

```bash
cat -n filename
```

### more 命令

当查看内容较多的大文件时，`cat` 命令会导致文字快速滚动，难以阅读。此时推荐使用 `more` 命令，它可以像翻书一样逐页查看。

```bash
more filename
```
在 `more` 的浏览界面中，可以按**回车键**向下滚动一行，按**空格键**向下翻一页。界面左下角会显示阅读进度。

**总结**：查看已知的小文件用 `cat`，查看大文件或不确定大小的文件用 `more`。

---

## 查看文件首尾内容 🔍

有时我们只需要查看文件的开始或结尾部分。

### head 命令

`head` 命令用于查看文件的前N行，默认显示前10行。

```bash
head filename
```
指定查看前5行：
```bash
head -n 5 filename
```

![](img/bff1b6c819ac181e3d8c49a369de3014_27.png)

### tail 命令

`tail` 命令用于查看文件的末尾N行，默认显示后10行。

```bash
tail filename
```
指定查看后5行：
```bash
tail -n 5 filename
```

`tail` 命令还有一个强大的功能：使用 `-f` 参数可以实时刷新并显示文件的最新内容，常用于监控日志文件。

```bash
tail -f /var/log/message
```

![](img/bff1b6c819ac181e3d8c49a369de3014_29.png)

---

## 文本处理与转换 🔄

### tr 命令

![](img/bff1b6c819ac181e3d8c49a369de3014_31.png)

`tr` 命令用于对文本进行批量替换或删除。它通常需要配合管道符 `|` 使用，将前一个命令的输出作为其输入。

例如，将文件中的所有小写字母转换为大写字母：
```bash
cat filename | tr [a-z] [A-Z]
```

### wc 命令

`wc` 命令用于统计文件的行数、单词数和字节数。

*   `-l` 参数统计行数。
*   `-w` 参数统计单词数（对中文无效）。
*   `-c` 参数统计字节数。

一个标准的命令格式示例如下：
```bash
wc -l filename
```

![](img/bff1b6c819ac181e3d8c49a369de3014_33.png)

![](img/bff1b6c819ac181e3d8c49a369de3014_35.png)

**注意**：对于中文文本，字节数大致是文字数量的两倍。

---

## 文件时间戳 ⏰

在Linux中，文件有三个重要的时间戳，可以通过 `stat` 命令查看：

1.  **Access Time (atime)**：文件内容最后一次被访问的时间。
2.  **Change Time (ctime)**：文件属性（如权限、所有者）最后一次被更改的时间。
3.  **Modify Time (mtime)**：文件内容最后一次被修改的时间。

**重要关系**：修改文件内容（mtime变化）必然会导致文件属性（如文件大小）变化，因此 ctime 也会随之改变。

![](img/bff1b6c819ac181e3d8c49a369de3014_37.png)

---

![](img/bff1b6c819ac181e3d8c49a369de3014_39.png)

## 在文件中搜索 🔎

![](img/bff1b6c819ac181e3d8c49a369de3014_41.png)

### grep 命令

![](img/bff1b6c819ac181e3d8c49a369de3014_43.png)

`grep` 命令用于在文件内部按行搜索指定的关键词。

搜索包含 “keyword” 的行：
```bash
grep keyword filename
```
显示匹配行及其行号：
```bash
grep -n keyword filename
```
显示**不包含**关键词的行（反选）：
```bash
grep -v keyword filename
```

### cut 命令

`cut` 命令用于按列提取文本内容。常用于处理以特定符号（如冒号`:`）分隔的配置文件。

例如，提取 `/etc/passwd` 文件（以冒号分隔）的第一列（用户名）：
```bash
cut -d: -f1 /etc/passwd
```
*   `-d:` 指定分隔符为冒号。
*   `-f1` 指定提取第一列。

---

## 比较与去重 ⚖️

### diff 命令

![](img/bff1b6c819ac181e3d8c49a369de3014_45.png)

`diff` 命令用于比较两个文件的差异。

![](img/bff1b6c819ac181e3d8c49a369de3014_47.png)

简单比较，只告知是否不同：
```bash
diff --brief file1 file2
```
详细比较，显示具体差异行：
```bash
diff -c file1 file2
```

### uniq 命令

`uniq` 命令用于去除文件中**相邻的**重复行。它通常需要先使用 `sort` 命令将相同行排列到一起。

去除相邻重复行：
```bash
uniq filename
```

![](img/bff1b6c819ac181e3d8c49a369de3014_49.png)

### sort 命令

![](img/bff1b6c819ac181e3d8c49a369de3014_51.png)

`sort` 命令用于对文本行进行排序。

默认按字母顺序排序：
```bash
sort filename
```
按数字大小排序：
```bash
sort -n filename
```
排序并去除重复行：
```bash
sort -u filename
```
以冒号分隔，按第三列的数字值排序：
```bash
sort -t: -k3 -n /etc/passwd
```

---

## 文件操作（增、删、改、移动）📁

### 创建文件与目录

![](img/bff1b6c819ac181e3d8c49a369de3014_53.png)

*   **创建空文件**：`touch filename`
*   **创建目录**：`mkdir dirname`
*   **创建多级嵌套目录**：`mkdir -p dir1/dir2/dir3`

### 复制文件与目录

![](img/bff1b6c819ac181e3d8c49a369de3014_55.png)

*   **复制文件**：`cp source_file dest_file`
*   **复制目录**（需要递归参数 `-r`）：`cp -r source_dir dest_dir`

![](img/bff1b6c819ac181e3d8c49a369de3014_57.png)

### 移动/重命名文件与目录

`mv` 命令既可以移动文件，也可以在同一个目录内实现重命名。
*   **移动文件**：`mv source_file dest_path`
*   **重命名文件**：`mv old_name new_name`

### 删除文件与目录

![](img/bff1b6c819ac181e3d8c49a369de3014_59.png)

*   **删除文件**（强制删除，不询问）：`rm -f filename`
*   **删除目录**（递归强制删除）：`rm -rf dirname`

**警告**：`rm -rf` 命令非常危险，请谨慎使用。

![](img/bff1b6c819ac181e3d8c49a369de3014_61.png)

---

![](img/bff1b6c819ac181e3d8c49a369de3014_63.png)

## 高级文件操作 ⚙️

### dd 命令

`dd` 命令用于按数据块复制和转换文件，功能强大。常用参数：
*   `if=`：输入文件（Input File）
*   `of=`：输出文件（Output File）
*   `bs=`：块大小（Block Size）
*   `count=`：复制块的数量

例如，从 `input.file` 读取数据，创建大小为50字节的 `output.file`：
```bash
dd if=input.file of=output.file bs=50 count=1
```

**实用场景**：
1.  备份磁盘主引导记录（MBR）：
    ```bash
    dd if=/dev/sda of=mbr_backup bs=512 count=1
    ```
2.  制作光盘ISO镜像：
    ```bash
    dd if=/dev/cdrom of=system.iso
    ```

### file 命令

`file` 命令用于准确判断文件的类型，比单纯依赖颜色更可靠。
```bash
file filename
```

![](img/bff1b6c819ac181e3d8c49a369de3014_65.png)

### tar 命令

`tar` 命令用于打包和压缩文件，是Linux中最常用的归档工具。

**打包并压缩**（使用gzip算法，生成 `.tar.gz` 文件）：
```bash
tar czvf backup.tar.gz /etc
```
*   `c`：创建打包文件。
*   `z`：使用gzip压缩。
*   `v`：显示详细过程。
*   `f`：指定打包后的文件名。

**解压文件**：
```bash
tar xzvf backup.tar.gz
```
*   `x`：解压操作。

**提示**：如果使用 `bzip2` 算法（参数 `j`），则生成 `.tar.bz2` 文件。

![](img/bff1b6c819ac181e3d8c49a369de3014_67.png)

---

## 总结 🎯

本节课中我们一起学习了Linux第二章节下半部分的核心命令，涵盖了文本文件的查看、处理、比较、压缩以及基本的文件操作（创建、复制、移动、删除）。

![](img/bff1b6c819ac181e3d8c49a369de3014_69.png)

关键点回顾：
1.  **查看文件**：根据文件大小选择 `cat` 或 `more`，使用 `head`/`tail` 查看首尾。
2.  **处理文本**：使用 `tr` 转换，`wc` 统计，`grep` 搜索，`cut` 按列提取。
3.  **比较与去重**：`diff` 比较差异，`sort` 和 `uniq` 排序去重。
4.  **文件操作**：`touch`/`mkdir` 创建，`cp`/`mv`/`rm` 复制/移动/删除。
5.  **高级工具**：`dd` 用于块操作和备份，`tar` 用于打包压缩，`file` 用于识别文件类型。

![](img/bff1b6c819ac181e3d8c49a369de3014_71.png)

这些命令是构建Linux知识体系的基石，请务必多加练习，熟悉它们的用法。在接下来的章节中，我们将学习如何让这些命令相互协作，发挥更强大的功能。