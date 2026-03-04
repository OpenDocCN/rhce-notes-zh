# Linux文件管理：P17：3.03-tar归档及压缩 📦

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_1.png)

在本节课中，我们将要学习Linux系统中一个非常核心的命令——`tar`。我们将了解归档与压缩的区别，掌握如何使用`tar`命令创建、查看和恢复归档文件，并学习如何一步完成归档与压缩操作。

## 归档与压缩的概念 🔍

在Linux系统中，归档和压缩是两个不同的概念。

归档指的是将多个文件归纳合并到一个文件中，方便携带或备份。归档本身并不减少文件占用的总空间。例如，将10个10MB的文件归档后，会得到一个约100MB的归档文件。

压缩则是通过特定算法减少文件占用的磁盘空间大小。在Linux中，有专门的工具如`gzip`、`bzip2`、`xz`等来完成压缩工作。

`tar`命令最初只负责归档。后来通过增加选项，使其能够自动调用压缩工具，从而一步完成“归档并压缩”的操作。

## 使用tar命令进行归档 📁

`tar`命令是处理归档任务的核心工具。以下是其基本操作。

### 创建归档

要创建一个新的归档文件，需要使用 `-c`（create）选项，并通过 `-f`（file）选项指定归档文件的名称。

基本命令格式如下：
```bash
tar -cf 归档文件名.tar 文件1 文件2 ...
```

例如，将 `under` 和 `out` 两个文件归档到 `6.tar`：
```bash
tar -cf 6.tar under out
```
执行后，会生成一个名为 `6.tar` 的归档文件。

### 查看归档内容

创建归档后，可以使用 `-t`（list）选项查看归档文件中包含哪些内容。
```bash
tar -tf 6.tar
```
或者简写为：
```bash
tar tf 6.tar
```

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_3.png)

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_5.png)

### 恢复（解压）归档

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_7.png)

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_8.png)

要从归档文件中恢复原始文件，需要使用 `-x`（extract）选项。
```bash
tar -xf 6.tar
```
默认情况下，文件会被释放到当前目录。如果想指定释放目录，可以使用 `-C`（大写）选项。
```bash
tar -xf 6.tar -C /opt/
```

### 显示详细信息

在执行归档或恢复操作时，可以添加 `-v`（verbose）选项来显示详细的处理过程。
```bash
tar -cvf backup.tar /usr/local/
tar -xvf backup.tar -C /restore/path/
```

### 保留绝对路径

在备份系统目录时，保留文件的绝对路径非常重要，这关系到恢复的便利性。使用 `-P`（大写）选项可以保留原始文件的绝对路径。

例如，备份 `/usr/local` 目录：
```bash
tar -cPf backup_with_path.tar /usr/local/
```
如果在创建归档时使用了 `-P`，那么在恢复时也使用 `-P`，文件就会自动恢复到原始路径（`/usr/local/`）。否则，恢复时可能需要手动指定目标路径。

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_10.png)

**建议**：在实际备份工作中，组合使用 `-cP` 选项是一个好习惯。

## 文件的压缩与解压 🗜️

上一节我们介绍了如何使用 `tar` 进行纯归档操作。本节中我们来看看如何对文件进行压缩，以及如何将归档与压缩结合起来。

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_12.png)

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_14.png)

### 使用gzip进行压缩

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_16.png)

`gzip` 是一个常用的压缩工具。它可以压缩单个文件。
```bash
gzip 6.tar
```
执行后，原文件 `6.tar` 会变成压缩文件 `6.tar.gz`，原文件被删除。

### 使用gzip进行解压

要解压 `.gz` 文件，可以使用 `gzip -d` 或 `gunzip` 命令。
```bash
gzip -d 6.tar.gz
# 或
gunzip 6.tar.gz
```
解压后，会得到原始的 `6.tar` 文件。

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_18.png)

需要注意的是，`gzip` 默认对多个文件是分别压缩，而不是将它们打包成一个文件。将多个文件合并为一个文件是归档（`tar`）的工作。

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_20.png)

## 一步完成归档与压缩 🚀

在日常使用中，我们通常希望将归档和压缩一步完成。`tar` 命令通过特定的选项可以调用压缩工具，实现这个目的。

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_22.png)

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_23.png)

### 创建压缩归档包

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_25.png)

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_27.png)

以下是调用不同压缩格式的选项：
*   `-z`: 调用 `gzip` 工具，处理 `.gz` 格式。
*   `-j`: 调用 `bzip2` 工具，处理 `.bz2` 格式。
*   `-J`: 调用 `xz` 工具，处理 `.xz` 格式。

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_29.png)

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_31.png)

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_32.png)

例如，要将 `under` 和 `out` 文件归档并压缩为 `.gz` 格式：
```bash
tar -czf backup.tar.gz under out
```
这条命令等价于先执行 `tar -cf backup.tar under out`，再执行 `gzip backup.tar`。

### 解压压缩归档包

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_34.png)

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_36.png)

解压时使用对应的选项即可。
```bash
tar -xzf backup.tar.gz          # 解压 .gz 格式
tar -xjf backup.tar.bz2         # 解压 .bz2 格式
tar -xJf backup.tar.xz          # 解压 .xz 格式
```

**重要提示**：使用 `-j` 或 `-J` 选项前，请确保系统已安装 `bzip2` 或 `xz` 软件包。`gzip` 通常是默认安装的。
安装命令示例：
```bash
yum install -y bzip2 xz
```

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_38.png)

## 命令选项顺序与总结 📝

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_40.png)

关于 `tar` 命令选项的顺序，有一个关键点需要注意：`-f` 选项必须紧跟在归档文件名之前，它们是一个整体。其他选项（如 `-c`, `-z`, `-v`, `-P`）的顺序可以互换。

**正确示例**：
```bash
tar -czvPf backup.tar.gz /path/to/dir
```

**错误示例**（`-f` 放到了前面）：
```bash
tar -fczvP backup.tar.gz /path/to/dir  # 这会报错
```

在本节课中，我们一起学习了：
1.  **归档**与**压缩**的基本概念区别。
2.  使用 `tar -c` 创建归档，使用 `tar -t` 查看内容，使用 `tar -x` 恢复归档。
3.  使用 `-P` 选项保留绝对路径对于系统备份的重要性。
4.  使用 `gzip`、`bzip2` 等工具进行单独的文件压缩与解压。
5.  使用 `tar` 命令的 `-z`、`-j`、`-J` 选项，一步完成归档与压缩，这是最常用和高效的方式。

![](img/9eba19a87c28b1f8d28a1f3b86b41e44_42.png)

掌握 `tar` 命令是Linux系统管理和维护的必备技能，无论是日常文件打包，还是系统备份恢复，都离不开它。