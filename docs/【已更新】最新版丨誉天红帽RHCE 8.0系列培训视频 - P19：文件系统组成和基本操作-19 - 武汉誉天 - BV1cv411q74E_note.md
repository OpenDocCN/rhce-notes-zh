# Linux文件系统操作：P19：文件系统组成和基本操作-19

![](img/8e192909aac86dc3f295f49b0bfcf8ad_1.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_3.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_5.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_7.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_9.png)

在本节课中，我们将要学习Linux系统中关于文件复制、移动、创建和删除的基本操作，并深入理解文件时间戳的概念及其应用。

![](img/8e192909aac86dc3f295f49b0bfcf8ad_11.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_13.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_15.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_17.png)

## 复制文件与目录

上一节我们介绍了文件系统的基本概念，本节中我们来看看如何使用`cp`命令复制文件和目录。

![](img/8e192909aac86dc3f295f49b0bfcf8ad_19.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_20.png)

`cp`命令的基本语法是：
```bash
cp [选项] 源文件... 目标路径
```
其中，`-r`或`-R`选项用于递归复制目录。

![](img/8e192909aac86dc3f295f49b0bfcf8ad_22.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_24.png)

复制操作中，目标路径的情况决定了复制行为。以下是目标路径的几种情况：

![](img/8e192909aac86dc3f295f49b0bfcf8ad_26.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_28.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_30.png)

1.  **目标是一个目录**：文件将被复制到该目录下。
2.  **目标是一个已存在的文件**：源文件将覆盖目标文件。
3.  **目标是一个不存在的文件**：源文件将被复制并重命名为指定的文件名。

![](img/8e192909aac86dc3f295f49b0bfcf8ad_32.png)

对于目录的复制，规则与文件类似，但有一个关键区别：**目录无法覆盖一个已存在的文件**。

## 移动与重命名文件

移动文件使用`mv`命令，其语法与`cp`类似：
```bash
mv [选项] 源文件... 目标路径
```
`mv`命令有两个主要功能：移动文件和重命名文件。移动后，源文件将不再存在于原位置。

![](img/8e192909aac86dc3f295f49b0bfcf8ad_34.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_36.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_38.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_40.png)

`mv`命令的目标路径规则与`cp`命令完全一致。重命名文件时，需要确保源文件和目标文件在同一目录下，否则会变成移动操作。

## 创建与删除文件

创建空文件使用`touch`命令：
```bash
touch 文件名
```
`touch`命令还有另一个重要功能：**更新文件的时间戳**。

![](img/8e192909aac86dc3f295f49b0bfcf8ad_42.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_44.png)

删除文件使用`rm`命令，删除目录需要加上`-r`选项：
```bash
rm 文件名
rm -r 目录名
```

![](img/8e192909aac86dc3f295f49b0bfcf8ad_46.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_48.png)

## 理解文件时间戳

![](img/8e192909aac86dc3f295f49b0bfcf8ad_50.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_52.png)

每个文件都包含三个重要的时间戳，可以通过`stat`命令查看：
```bash
stat 文件名
```

![](img/8e192909aac86dc3f295f49b0bfcf8ad_54.png)

以下是三个时间戳的含义：

![](img/8e192909aac86dc3f295f49b0bfcf8ad_56.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_58.png)

![](img/8e192909aac86dc3f295f49b0bfcf8ad_60.png)

1.  **访问时间**：文件最后一次被读取的时间。
2.  **修改时间**：文件内容最后一次被修改的时间。
3.  **变更时间**：文件元数据（如权限、所有者）最后一次被改变的时间。

![](img/8e192909aac86dc3f295f49b0bfcf8ad_62.png)

访问时间的更新在频繁读写的场景下（如Web服务器）可能会增加磁盘I/O负担。修改时间在数据备份策略中非常有用，常用于实现**增量备份**，即只备份自上次备份以来被修改过的文件，这可以极大地节省备份时间和存储空间。

![](img/8e192909aac86dc3f295f49b0bfcf8ad_64.png)

本节课中我们一起学习了文件的基本操作命令`cp`、`mv`、`touch`和`rm`，并深入了解了文件时间戳的构成及其在实际系统管理中的应用场景。掌握这些基础操作是熟练使用Linux系统的关键一步。