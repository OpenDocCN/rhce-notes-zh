# Linux系统管理：P22：文件压缩与打包 📦

![](img/2091edaf34e0cf78d98565423eaf21a0_1.png)

在本节课中，我们将要学习Linux系统中常用的文件压缩与打包工具。我们将了解`gzip`、`bzip2`、`xz`这三种压缩格式的特点，并掌握`tar`打包工具的使用方法，以便高效地管理文件，节约磁盘空间并提升文件传输效率。

---

![](img/2091edaf34e0cf78d98565423eaf21a0_3.png)

## 文件压缩基础 🧠

上一节我们回顾了特殊符号和文件搜索命令。本节中，我们来看看文件压缩。

![](img/2091edaf34e0cf78d98565423eaf21a0_5.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_7.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_9.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_11.png)

压缩的主要目的是**节约磁盘空间**和**提高文件传输速度**。通过压缩，可以将大文件体积变小，便于存储和网络传输。常见的音频（mp3）、视频（mp4）、图片（jpg, png）等格式都是压缩后的结果。

![](img/2091edaf34e0cf78d98565423eaf21a0_13.png)

在Linux系统中，主要有三种压缩工具，它们在压缩比例和速度上各有侧重。

以下是三种压缩工具的核心对比：
*   **gzip**：压缩速度最快，但压缩比例一般。
*   **bzip2**：压缩速度中等，压缩比例较高。
*   **xz**：压缩速度最慢，但压缩比例最高。

![](img/2091edaf34e0cf78d98565423eaf21a0_15.png)

---

![](img/2091edaf34e0cf78d98565423eaf21a0_17.png)

## 压缩工具实战 🔧

![](img/2091edaf34e0cf78d98565423eaf21a0_19.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_21.png)

我们将使用 `/etc/services` 文件作为示例，演示三种压缩工具的使用。

![](img/2091edaf34e0cf78d98565423eaf21a0_23.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_25.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_27.png)

### 1. gzip 压缩

![](img/2091edaf34e0cf78d98565423eaf21a0_29.png)

`gzip` 命令用于压缩文件，压缩后的文件扩展名为 `.gz`。

![](img/2091edaf34e0cf78d98565423eaf21a0_31.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_33.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_35.png)

**命令格式**：`gzip [选项] 文件名`

![](img/2091edaf34e0cf78d98565423eaf21a0_37.png)

首先，复制示例文件并查看其原始大小：
```bash
cp /etc/services /opt/
du -h /opt/services
```
输出显示文件大小约为 **656K**。

![](img/2091edaf34e0cf78d98565423eaf21a0_39.png)

现在使用 `gzip` 进行压缩：
```bash
cd /opt
gzip services
```
压缩后，原文件 `services` 会变成 `services.gz`。查看压缩后的大小：
```bash
du -h services.gz
```
输出显示文件大小变为约 **136K**。

![](img/2091edaf34e0cf78d98565423eaf21a0_41.png)

**查看压缩文件内容**：不能使用 `cat`，需使用 `zcat`。
```bash
zcat services.gz
```

![](img/2091edaf34e0cf78d98565423eaf21a0_43.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_45.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_47.png)

**解压缩文件**：使用 `-d` 选项。
```bash
gzip -d services.gz
```
解压后，文件恢复为原始大小。

![](img/2091edaf34e0cf78d98565423eaf21a0_49.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_51.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_53.png)

### 2. bzip2 压缩

`bzip2` 命令压缩后的文件扩展名为 `.bz2`。如果系统未安装，可使用 `yum install bzip2 -y` 安装。

![](img/2091edaf34e0cf78d98565423eaf21a0_55.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_57.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_59.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_61.png)

**命令格式**：`bzip2 [选项] 文件名`

对文件进行压缩：
```bash
bzip2 services
du -h services.bz2
```
输出显示文件大小约为 **124K**，压缩比例高于 `gzip`。

![](img/2091edaf34e0cf78d98565423eaf21a0_63.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_65.png)

**查看压缩文件内容**：使用 `bzcat`。
```bash
bzcat services.bz2
```

**解压缩文件**：同样使用 `-d` 选项。
```bash
bzip2 -d services.bz2
```

![](img/2091edaf34e0cf78d98565423eaf21a0_67.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_68.png)

### 3. xz 压缩

`xz` 命令压缩后的文件扩展名为 `.xz`。

![](img/2091edaf34e0cf78d98565423eaf21a0_70.png)

**命令格式**：`xz [选项] 文件名`

![](img/2091edaf34e0cf78d98565423eaf21a0_72.png)

对文件进行压缩：
```bash
xz services
du -h services.xz
```
输出显示文件大小约为 **100K**，压缩比例最高，但压缩时能感觉到速度稍慢。

![](img/2091edaf34e0cf78d98565423eaf21a0_74.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_76.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_78.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_80.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_82.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_84.png)

**查看压缩文件内容**：使用 `xzcat`。
```bash
xzcat services.xz
```

![](img/2091edaf34e0cf78d98565423eaf21a0_86.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_88.png)

**解压缩文件**：使用 `-d` 选项。
```bash
xz -d services.xz
```

![](img/2091edaf34e0cf78d98565423eaf21a0_90.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_92.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_94.png)

---

![](img/2091edaf34e0cf78d98565423eaf21a0_96.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_98.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_100.png)

## tar 打包工具 📦

上一节我们介绍了三种压缩工具，但它们都无法直接压缩目录。本节中我们来看看 `tar` 打包工具如何解决这个问题。

`tar` 命令本身是一个**打包工具**，能将多个文件或目录合并成一个文件（称为tar包，扩展名通常为 `.tar`），但它不改变文件大小。打包后，可以再调用压缩工具进行压缩，实现目录的压缩。

![](img/2091edaf34e0cf78d98565423eaf21a0_102.png)

### 打包与压缩

![](img/2091edaf34e0cf78d98565423eaf21a0_104.png)

**命令格式**：`tar [选项] 打包后的文件名 要打包的目录或文件`

常用选项：
*   `-c`：创建打包文件。
*   `-f`：指定打包后的文件名（**此选项必须放在所有选项的最后**）。
*   `-z`：调用 `gzip` 进行压缩/解压。
*   `-j`：调用 `bzip2` 进行压缩/解压。
*   `-J`：调用 `xz` 进行压缩/解压。
*   `-v`：显示详细过程（可选）。
*   `-x`：解包/解压。
*   `-C`：指定解压路径。

**示例：打包并压缩 `/var/log` 目录**

1.  **仅打包**（不压缩）：
    ```bash
    tar -cf log.tar /var/log
    du -h log.tar
    ```
    打包后文件大小变化不大。

2.  **打包并调用gzip压缩**（一步完成）：
    ```bash
    tar -czf log.tar.gz /var/log
    ```
    这条命令会创建一个名为 `log.tar.gz` 的压缩包。文件体积会显著减小。

3.  **查看压缩包内容**：
    ```bash
    tar -tf log.tar.gz
    ```

### 解包与解压

**解压到当前目录**：
```bash
tar -xf log.tar.gz
```

**解压到指定目录**（如 `/opt`）：
```bash
tar -xf log.tar.gz -C /opt
```
**注意**：`tar` 在打包时会自动“删除成员名开头的`/`（根目录）”，这是为了安全，防止解压时文件覆盖系统关键路径。因此可以安全地使用 `-C` 选项解压到任何目录。

---

## 总结 📝

![](img/2091edaf34e0cf78d98565423eaf21a0_106.png)

本节课中我们一起学习了Linux下的文件压缩与打包。
*   我们掌握了 **`gzip`、`bzip2`、`xz`** 三种压缩工具的使用方法，了解了它们在不同场景（追求速度或压缩比）下的选择。
*   我们重点学习了 **`tar`** 打包工具，它通过 `-c`、`-x`、`-f`、`-z`/`-j`/`-J` 等选项的组合，可以轻松实现对目录的打包、压缩、查看和解压操作。
*   记住核心要点：压缩小文件可能适得其反；`tar` 命令的 `-f` 选项必须放在最后；解压时使用 `-C` 可以指定目标路径。

![](img/2091edaf34e0cf78d98565423eaf21a0_108.png)

![](img/2091edaf34e0cf78d98565423eaf21a0_110.png)

熟练掌握这些命令，将极大提升你在Linux环境下管理文件、备份数据的效率。