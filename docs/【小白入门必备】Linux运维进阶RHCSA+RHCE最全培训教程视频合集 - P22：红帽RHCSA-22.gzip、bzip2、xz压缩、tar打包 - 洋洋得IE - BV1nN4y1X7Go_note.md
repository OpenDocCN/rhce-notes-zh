# Linux运维进阶：P22：gzip、bzip2、xz压缩与tar打包 📦

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_1.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_3.png)

在本节课中，我们将要学习Linux系统中常用的文件压缩与打包工具。我们将了解`gzip`、`bzip2`和`xz`这三种压缩格式的特点与用法，并掌握`tar`命令如何将多个文件或目录打包成一个文件，以便于进行压缩、传输和备份。

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_5.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_7.png)

## 压缩的概念与作用 💡

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_9.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_11.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_13.png)

上一节我们介绍了文件搜索与过滤命令，本节中我们来看看文件压缩。压缩的主要目的是减少文件占用的磁盘空间，并提高文件在网络中的传输速度。例如，在传输大文件或备份数据时，压缩可以显著提升效率。

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_15.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_17.png)

## Linux下的压缩格式 🔧

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_19.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_21.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_23.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_25.png)

在Linux系统中，有多种压缩工具，它们生成的压缩文件格式和压缩效率各不相同。以下是三种常见的压缩格式：

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_27.png)

*   **gzip**：压缩速度较快，但压缩比例相对一般。
*   **bzip2**：压缩比例优于gzip，但压缩速度稍慢。
*   **xz**：压缩比例最高，能生成最小的文件，但压缩速度最慢。

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_29.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_31.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_33.png)

### 压缩命令基本用法

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_35.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_37.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_39.png)

以下是三种压缩工具的基本命令格式：
```bash
# 使用gzip压缩文件
gzip 文件名
# 使用bzip2压缩文件
bzip2 文件名
# 使用xz压缩文件
xz 文件名
```
压缩后，原文件会被替换为带相应扩展名（`.gz`， `.bz2`， `.xz`）的压缩文件。

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_41.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_43.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_45.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_47.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_49.png)

### 查看压缩文件内容

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_51.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_53.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_55.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_57.png)

不能使用`cat`等普通命令直接查看压缩文件内容，需要使用对应的命令：
```bash
# 查看.gz文件内容
zcat 文件名.gz
# 查看.bz2文件内容
bzcat 文件名.bz2
# 查看.xz文件内容
xzcat 文件名.xz
```

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_59.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_61.png)

### 解压缩文件

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_63.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_64.png)

解压缩使用`-d`选项：
```bash
# 解压.gz文件
gzip -d 文件名.gz
# 解压.bz2文件
bzip2 -d 文件名.bz2
# 解压.xz文件
xz -d 文件名.xz
```

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_66.png)

## tar打包工具 📁

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_68.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_70.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_72.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_74.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_76.png)

上一节我们学习了单个文件的压缩，但上述压缩工具无法直接压缩目录。本节中我们来看看`tar`命令，它可以将多个文件或整个目录打包成一个文件（归档），然后可以再对这个打包文件进行压缩。

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_78.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_80.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_82.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_84.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_86.png)

`tar`命令的选项组合有些特殊，需要特别注意顺序，尤其是`-f`选项必须放在所有选项的最后，用于指定打包后的文件名。

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_88.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_90.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_92.png)

### 打包与压缩

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_94.png)

常用的打包并压缩的组合命令如下：
```bash
# 打包并调用gzip压缩
tar -czf 打包后文件名.tar.gz 要打包的目录或文件
# 打包并调用bzip2压缩
tar -cjf 打包后文件名.tar.bz2 要打包的目录或文件
# 打包并调用xz压缩
tar -cJf 打包后文件名.tar.xz 要打包的目录或文件
```
**参数解释**：
*   `-c`：创建新的打包文件。
*   `-z`：调用`gzip`进行压缩/解压。
*   `-j`：调用`bzip2`进行压缩/解压。
*   `-J`：调用`xz`进行压缩/解压。
*   `-f`：指定打包后的文件名（**必须紧跟在所有选项之后**）。
*   `-v`：可选，显示详细的打包过程。

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_96.png)

### 查看打包文件内容

列出打包文件（`.tar.gz`， `.tar.bz2`， `.tar.xz`）内的文件列表，无需解压：
```bash
tar -tf 打包文件名.tar.gz
```

### 解包与解压缩

解压打包文件时，`tar`命令会自动识别压缩格式，无需指定`-z`， `-j`或`-J`选项。
```bash
# 解压到当前目录
tar -xf 打包文件名.tar.gz
# 解压到指定目录
tar -xf 打包文件名.tar.gz -C /目标路径/
```
**参数解释**：
*   `-x`：从打包文件中提取文件（解包）。
*   `-C`：指定解压的目标目录。

## 总结 🎯

本节课中我们一起学习了Linux下的文件压缩与打包。
1.  我们了解了**gzip**、**bzip2**和**xz**三种压缩工具，知道了它们在压缩速度和压缩比例上的权衡。
2.  我们掌握了使用`tar`命令进行**打包**，并结合不同选项调用压缩工具进行**压缩**。
3.  我们学会了如何**查看**压缩包内容以及如何**解压**打包文件到指定位置。

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_98.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_100.png)

![](img/f31f4a3f64e5a13ab4f4e82e0f6cf751_102.png)

这些技能对于管理服务器磁盘空间、备份重要数据以及高效传输文件都非常重要。