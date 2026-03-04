# Linux运维入门：P22：gzip、bzip2、xz压缩与tar打包 📦

![](img/e52b37b31a765ca6212d15aeb877ed67_1.png)

在本节课中，我们将要学习Linux系统中常用的文件压缩与打包工具。我们将了解`gzip`、`bzip2`、`xz`这三种压缩格式的特点和用法，并重点掌握`tar`命令如何将多个文件或目录打包，并与压缩工具结合使用，以便高效地管理文件、节省磁盘空间和加速文件传输。

---

![](img/e52b37b31a765ca6212d15aeb877ed67_3.png)

## 压缩的概念与作用

上一节我们回顾了文件过滤和搜索命令，本节中我们来看看文件压缩。压缩的主要目的是**节约磁盘空间**和**提高文件传输速度**。通过压缩，可以将大文件变小，从而在存储和网络传输时更高效。例如，常见的MP3、MP4、JPG等格式都是压缩后的文件，这使得我们在浏览网页或观看视频时能快速加载。

![](img/e52b37b31a765ca6212d15aeb877ed67_5.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_7.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_9.png)

---

![](img/e52b37b31a765ca6212d15aeb877ed67_11.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_13.png)

## Linux下的压缩格式

在Linux系统中，主要有三种常见的压缩格式，它们各有特点：

![](img/e52b37b31a765ca6212d15aeb877ed67_15.png)

*   **gzip**：压缩速度最快，但压缩比例（即压缩后文件缩小的程度）相对一般。
*   **bzip2**：压缩比例优于gzip，但压缩速度稍慢。
*   **xz**：压缩比例最高，能将文件压缩到最小，但压缩速度最慢。

![](img/e52b37b31a765ca6212d15aeb877ed67_17.png)

选择哪种压缩格式，取决于你是更看重**压缩速度**还是**压缩后文件的大小**。

![](img/e52b37b31a765ca6212d15aeb877ed67_19.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_21.png)

### 压缩命令基本用法

![](img/e52b37b31a765ca6212d15aeb877ed67_23.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_25.png)

以下是三种压缩格式的基本命令格式，它们非常相似：

**1. 使用gzip压缩**
```bash
gzip 文件名
```
压缩后，原文件会被替换为以 `.gz` 结尾的新文件。

![](img/e52b37b31a765ca6212d15aeb877ed67_27.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_29.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_31.png)

**2. 使用bzip2压缩**
```bash
bzip2 文件名
```
压缩后，原文件会被替换为以 `.bz2` 结尾的新文件。

![](img/e52b37b31a765ca6212d15aeb877ed67_33.png)

**3. 使用xz压缩**
```bash
xz 文件名
```
压缩后，原文件会被替换为以 `.xz` 结尾的新文件。

![](img/e52b37b31a765ca6212d15aeb877ed67_35.png)

### 查看压缩文件内容

![](img/e52b37b31a765ca6212d15aeb877ed67_37.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_39.png)

压缩后的文件不能用`cat`等普通命令直接查看，需要使用对应的命令：

![](img/e52b37b31a765ca6212d15aeb877ed67_41.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_43.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_45.png)

*   查看 `.gz` 文件：`zcat 文件名.gz`
*   查看 `.bz2` 文件：`bzcat 文件名.bz2`
*   查看 `.xz` 文件：`xzcat 文件名.xz`

![](img/e52b37b31a765ca6212d15aeb877ed67_47.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_49.png)

### 解压缩文件

解压缩同样使用原命令，并加上 `-d` 选项：

![](img/e52b37b31a765ca6212d15aeb877ed67_51.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_53.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_55.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_57.png)

*   解压 `.gz` 文件：`gzip -d 文件名.gz`
*   解压 `.bz2` 文件：`bzip2 -d 文件名.bz2`
*   解压 `.xz` 文件：`xz -d 文件名.xz`

---

![](img/e52b37b31a765ca6212d15aeb877ed67_59.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_61.png)

## tar打包工具

上面介绍的三种压缩工具有一个共同的限制：**无法直接对目录进行压缩**。为了解决这个问题，我们需要先使用 `tar` 命令将目录“打包”成一个单独的文件，然后再对这个打包文件进行压缩。

![](img/e52b37b31a765ca6212d15aeb877ed67_63.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_64.png)

`tar` 命令可以将多个文件或目录整合成一个归档文件（通常以 `.tar` 结尾），这个过程本身不改变文件大小，只是将它们集合在一起。

### 打包与解包

![](img/e52b37b31a765ca6212d15aeb877ed67_66.png)

以下是使用tar进行打包和解包的核心选项：

![](img/e52b37b31a765ca6212d15aeb877ed67_68.png)

*   `-c`：创建新的打包文件。
*   `-x`：从打包文件中提取（解包）文件。
*   `-f`：指定打包文件的文件名。**这个选项必须放在所有选项的最后**。
*   `-v`：显示详细的处理过程（可选）。
*   `-C`：指定解包到的目标目录。

![](img/e52b37b31a765ca6212d15aeb877ed67_70.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_72.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_74.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_76.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_78.png)

**基本命令格式：**
```bash
# 打包：tar -cf 打包后的文件名.tar 要打包的目录或文件
tar -cf backup.tar /home/user/data

![](img/e52b37b31a765ca6212d15aeb877ed67_80.png)

# 解包：tar -xf 打包文件名.tar
tar -xf backup.tar
```

![](img/e52b37b31a765ca6212d15aeb877ed67_82.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_84.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_86.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_88.png)

### 打包并压缩（一步到位）

![](img/e52b37b31a765ca6212d15aeb877ed67_90.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_92.png)

`tar` 命令的强大之处在于，它可以与压缩工具联动，在打包的同时直接进行压缩，无需分两步操作。

以下是结合不同压缩格式的选项：

*   `-z`：调用 `gzip` 进行压缩或解压，生成 `.tar.gz` 或 `.tgz` 文件。
*   `-j`：调用 `bzip2` 进行压缩或解压，生成 `.tar.bz2` 文件。
*   `-J`：调用 `xz` 进行压缩或解压，生成 `.tar.xz` 文件。

![](img/e52b37b31a765ca6212d15aeb877ed67_94.png)

**命令格式示例：**
```bash
# 打包并用gzip压缩
tar -czf archive.tar.gz /path/to/directory

![](img/e52b37b31a765ca6212d15aeb877ed67_96.png)

# 打包并用bzip2压缩
tar -cjf archive.tar.bz2 /path/to/directory

# 打包并用xz压缩
tar -cJf archive.tar.xz /path/to/directory
```
**注意：** `-f` 选项必须紧跟指定的文件名。

### 查看打包文件内容

在解压之前，可以先查看打包文件里包含哪些内容：
```bash
tar -tf archive.tar.gz
```

### 解压打包文件

`tar` 命令能自动识别压缩格式并进行解压，非常方便。

**命令格式：**
```bash
# 解压到当前目录
tar -xf archive.tar.gz

# 解压到指定目录（使用-C选项）
tar -xf archive.tar.gz -C /opt/
```

---

## 总结

本节课中我们一起学习了Linux下的文件压缩与打包。

![](img/e52b37b31a765ca6212d15aeb877ed67_98.png)

1.  我们了解了**压缩**的目的：节省空间和加快传输。
2.  我们认识了三种常见的压缩工具：**gzip**（快）、**bzip2**（均衡）、**xz**（压缩率高但慢），并学会了它们基本的压缩、查看和解压命令。
3.  我们重点掌握了 **`tar` 打包工具**，因为它解决了压缩工具不能直接处理目录的问题。
4.  我们学会了如何使用 `tar` 命令的 `-c`、`-x`、`-f`、`-v`、`-C` 等关键选项进行打包、解包和查看。
5.  我们掌握了最实用的技巧：使用 `tar -zcf`、`tar -jcf`、`tar -Jcf` 来**一步完成打包和压缩**，以及使用 `tar -xf` 来**自动解压任何格式的打包压缩文件**。

![](img/e52b37b31a765ca6212d15aeb877ed67_100.png)

![](img/e52b37b31a765ca6212d15aeb877ed67_102.png)

熟练掌握这些命令，将极大地提升你在Linux系统中管理备份文件、传输数据的效率。