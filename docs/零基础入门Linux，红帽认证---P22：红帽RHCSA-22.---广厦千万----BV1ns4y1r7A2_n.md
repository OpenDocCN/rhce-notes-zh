# Linux基础教程：22：文件压缩与打包 📦

![](img/3f19232063506e7f4f6e9bb28f0ece80_1.png)

在本节课中，我们将要学习Linux系统中文件压缩与打包的核心知识。我们将了解压缩的目的、常见的压缩格式（gzip、bzip2、xz）以及如何使用`tar`命令进行打包和压缩操作。掌握这些技能对于管理文件、节省磁盘空间和高效传输数据至关重要。

![](img/3f19232063506e7f4f6e9bb28f0ece80_3.png)

## 回顾与引入 🔄

上一节我们介绍了常用的特殊符号以及`grep`和`find`命令。本节中，我们来看看如何管理文件的体积，即文件的压缩与打包。

![](img/3f19232063506e7f4f6e9bb28f0ece80_5.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_7.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_9.png)

压缩的主要目的是**节约磁盘空间**和**提高文件传输速度**。例如，在传输大型项目文件或备份系统日志时，压缩可以显著减少数据量。

![](img/3f19232063506e7f4f6e9bb28f0ece80_11.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_13.png)

## Linux下的压缩格式 🗜️

在Linux系统中，主要有三种常见的压缩格式，它们在压缩速度和压缩比例上各有特点。

![](img/3f19232063506e7f4f6e9bb28f0ece80_15.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_17.png)

以下是三种常见压缩格式的对比：

![](img/3f19232063506e7f4f6e9bb28f0ece80_19.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_20.png)

*   **gzip**: 压缩速度最快，但压缩比例一般。
*   **bzip2**: 压缩速度中等，压缩比例优于gzip。
*   **xz**: 压缩速度最慢，但压缩比例最高（文件体积最小）。

![](img/3f19232063506e7f4f6e9bb28f0ece80_22.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_23.png)

> **提示**：选择哪种压缩格式取决于你的需求。如果需要快速压缩或解压（如流媒体传输），可选gzip；如果追求极限的存储空间节省，可选xz。

![](img/3f19232063506e7f4f6e9bb28f0ece80_25.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_27.png)

### 实践：比较不同压缩格式

![](img/3f19232063506e7f4f6e9bb28f0ece80_29.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_31.png)

我们通过一个例子来直观感受它们的区别。首先，准备一个测试文件。

![](img/3f19232063506e7f4f6e9bb28f0ece80_33.png)

```bash
# 进入/opt目录并清空
cd /opt
rm -rf *
# 复制一个系统文件作为测试
cp /etc/services .
# 查看原文件大小
du -h services
```
输出可能类似：`656K services`

![](img/3f19232063506e7f4f6e9bb28f0ece80_35.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_36.png)

接下来，我们分别用三种格式进行压缩。

![](img/3f19232063506e7f4f6e9bb28f0ece80_38.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_39.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_41.png)

**1. 使用gzip压缩**
```bash
gzip services
ls -lh services.gz
du -h services.gz
```
压缩后，文件变为`services.gz`，大小可能变为约`136K`。

![](img/3f19232063506e7f4f6e9bb28f0ece80_43.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_45.png)

**2. 使用bzip2压缩**
```bash
# 如果系统未安装bzip2，先安装
yum install -y bzip2
# 解压刚才的gz文件以重新测试
gzip -d services.gz
# 使用bzip2压缩
bzip2 services
ls -lh services.bz2
du -h services.bz2
```
压缩后，文件变为`services.bz2`，大小可能约为`124K`。

![](img/3f19232063506e7f4f6e9bb28f0ece80_47.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_48.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_50.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_52.png)

**3. 使用xz压缩**
```bash
# 解压bz2文件以重新测试
bzip2 -d services.bz2
# 使用xz压缩
xz services
ls -lh services.xz
du -h services.xz
```
压缩后，文件变为`services.xz`，大小可能约为`100K`。

通过对比可以发现，**xz**格式的压缩率最高，但压缩过程会稍慢一些。

![](img/3f19232063506e7f4f6e9bb28f0ece80_54.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_56.png)

### 查看与解压压缩文件 👀

![](img/3f19232063506e7f4f6e9bb28f0ece80_58.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_59.png)

对于不同格式的压缩文件，需要使用特定的命令来查看内容或解压。

以下是针对不同压缩格式的操作命令：

![](img/3f19232063506e7f4f6e9bb28f0ece80_61.png)

*   **查看压缩文件内容**:
    *   gzip格式: `zcat 文件名.gz`
    *   bzip2格式: `bzcat 文件名.bz2`
    *   xz格式: `xzcat 文件名.xz`
*   **解压缩文件**:
    *   gzip格式: `gzip -d 文件名.gz` 或 `gunzip 文件名.gz`
    *   bzip2格式: `bzip2 -d 文件名.bz2` 或 `bunzip2 文件名.bz2`
    *   xz格式: `xz -d 文件名.xz` 或 `unxz 文件名.xz`

![](img/3f19232063506e7f4f6e9bb28f0ece80_63.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_64.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_66.png)

## 使用tar进行打包与压缩 📦

![](img/3f19232063506e7f4f6e9bb28f0ece80_67.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_69.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_71.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_73.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_75.png)

上面介绍的`gzip`、`bzip2`、`xz`命令有一个共同的限制：**它们只能压缩文件，不能直接压缩目录**。为了解决这个问题，我们需要先使用`tar`命令将目录“打包”成一个单独的文件，然后再对这个文件进行压缩。`tar`命令也可以一步完成打包和压缩。

![](img/3f19232063506e7f4f6e9bb28f0ece80_77.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_79.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_81.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_82.png)

### tar命令核心选项

![](img/3f19232063506e7f4f6e9bb28f0ece80_84.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_86.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_88.png)

`tar`命令选项较多，以下是几个最常用的核心选项：

*   `-c`: **创建**新的打包文件。
*   `-x`: **解包**或解压打包文件。
*   `-z`: 调用**gzip**进行压缩或解压。
*   `-j`: 调用**bzip2**进行压缩或解压。
*   `-J`: 调用**xz**进行压缩或解压。
*   `-v`: 显示详细的处理过程（**verbose**）。
*   `-f`: 指定打包文件的**文件名**。**这个选项必须放在所有选项的最后**。
*   `-C`: 指定解压到的**目标目录**。

![](img/3f19232063506e7f4f6e9bb28f0ece80_90.png)

### 实践：打包与压缩目录

![](img/3f19232063506e7f4f6e9bb28f0ece80_92.png)

假设我们要备份`/var/log`目录。

**1. 仅打包（不压缩）**
```bash
tar -cf log_backup.tar /var/log
```
这条命令会将`/var/log`目录打包成名为`log_backup.tar`的文件。注意，打包本身通常不会减小文件体积。

**2. 打包并使用gzip压缩（一步完成）**
```bash
tar -czf log_backup.tar.gz /var/log
```
这条命令结合了打包（-c）和gzip压缩（-z），并指定了最终文件名（-f log_backup.tar.gz）。生成的文件扩展名通常是`.tar.gz`或`.tgz`。

**3. 打包并使用其他格式压缩**
```bash
# 使用bzip2压缩
tar -cjf log_backup.tar.bz2 /var/log
# 使用xz压缩
tar -cJf log_backup.tar.xz /var/log
```

**4. 查看打包文件内容**
```bash
tar -tf log_backup.tar.gz
```
使用`-t`选项可以列出打包文件内部包含的文件和目录列表。

**5. 解压打包文件**
```bash
# 解压到当前目录
tar -xf log_backup.tar.gz
# 解压到指定目录（例如/opt）
tar -xf log_backup.tar.gz -C /opt
```
`tar`命令会自动识别压缩格式并调用相应的解压程序，因此无论压缩格式是gz、bz2还是xz，解压时都使用`-xf`选项。

> **重要提示**：使用`tar`打包系统目录（如`/var/log`）时，你可能会看到“从成员名中删除开头的`/`”的提示。这是为了防止解压时文件被绝对路径覆盖，是一个安全特性。解压时，你可以通过`-C`选项自由指定目标路径。

## 总结 📝

本节课中我们一起学习了Linux下的文件压缩与打包。

*   **压缩的目的**：节省磁盘空间，加快网络传输速度。
*   **三种压缩工具**：`gzip`（快）、`bzip2`（中）、`xz`（压缩率高但慢），它们只能直接压缩文件。
*   **打包工具tar**：用于将目录整合成单个文件，常与压缩工具结合使用。
*   **常用命令组合**：
    *   打包压缩：`tar -czf 目标文件.tar.gz 源目录`
    *   查看内容：`tar -tf 打包文件.tar.gz`
    *   解压：`tar -xf 打包文件.tar.gz -C 目标目录`

![](img/3f19232063506e7f4f6e9bb28f0ece80_94.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_96.png)

![](img/3f19232063506e7f4f6e9bb28f0ece80_97.png)

记住`-f`选项必须放在参数的最后，并且要习惯根据文件扩展名（`.gz`, `.bz2`, `.xz`, `.tar`）来选择合适的操作命令。多加练习，你就能熟练地管理Linux系统中的文件体积了。