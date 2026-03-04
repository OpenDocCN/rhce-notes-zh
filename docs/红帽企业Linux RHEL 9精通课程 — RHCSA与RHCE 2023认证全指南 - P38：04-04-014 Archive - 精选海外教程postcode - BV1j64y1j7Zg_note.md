# Linux归档与压缩：04-04-014：创建与管理归档文件

![](img/569f8ac1b32b11a127f1482fb1f471d8_0.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_2.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_4.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_6.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_8.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_10.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_12.png)

在本节课中，我们将要学习Linux系统中一个非常实用的技能：如何将多个文件打包成一个归档文件（通常称为tarball），以及如何压缩这些归档文件以节省存储空间。我们将从基本概念讲起，逐步学习创建、查看、提取和压缩归档文件的具体命令。

![](img/569f8ac1b32b11a127f1482fb1f471d8_14.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_16.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_18.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_20.png)

## 什么是Tarball？📦

![](img/569f8ac1b32b11a127f1482fb1f471d8_22.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_24.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_26.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_28.png)

在Linux中压缩和归档文件时，首先需要了解一个称为**tarball**的东西。

![](img/569f8ac1b32b11a127f1482fb1f471d8_29.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_31.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_33.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_35.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_36.png)

想象一下你在商店购物，买了一些苹果。为了让这些苹果更容易携带，你会把它们放进一个袋子里。创建tarball的基本过程与此类似。它是一种将多个文件放入一个“包”中的方法，目的是让文件更容易管理和存储。

![](img/569f8ac1b32b11a127f1482fb1f471d8_38.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_40.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_42.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_44.png)

当你将文件添加到tarball时，就可以将所有文件集中存储在一个位置。需要注意的是，**tarball本身并不进行任何压缩**，它只是打包。但我们可以使用压缩算法来压缩这个tarball，使其体积变小。

![](img/569f8ac1b32b11a127f1482fb1f471d8_46.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_48.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_50.png)

因此，完整的归档过程基本上分为两步：
1.  制作一个tarball（打包）。
2.  使用某种压缩算法来压缩这个tarball。

![](img/569f8ac1b32b11a127f1482fb1f471d8_52.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_53.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_55.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_57.png)

## 创建第一个Tarball 🛠️

![](img/569f8ac1b32b11a127f1482fb1f471d8_59.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_61.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_63.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_65.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_67.png)

上一节我们介绍了tarball的概念，本节中我们来看看如何实际操作。假设我们桌面上有三个文件：`file1.txt`、`file2.txt`和`file3.txt`，每个文件大小约为10KB。我们将使用`tar`命令把它们打包成一个tarball。

![](img/569f8ac1b32b11a127f1482fb1f471d8_69.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_71.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_73.png)

`tar`命令需要配合不同的选项来工作。以下是创建归档的基本命令格式：

![](img/569f8ac1b32b11a127f1482fb1f471d8_75.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_77.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_79.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_81.png)

```bash
tar -cvf 归档文件名.tar 要打包的文件
```

![](img/569f8ac1b32b11a127f1482fb1f471d8_83.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_85.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_87.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_89.png)

以下是每个选项的含义：
*   **`-c`**：代表 **c**reate，告诉`tar`命令我们要创建一个新的存档。
*   **`-v`**：代表 **v**erbose，让命令在执行过程中输出详细信息，方便我们了解进度。此选项是可选的，但建议保留。
*   **`-f`**：代表 **f**ile，允许`tar`命令接受文件名作为参数。

![](img/569f8ac1b32b11a127f1482fb1f471d8_91.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_93.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_95.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_97.png)

按照惯例，我们通常以`.tar`作为tarball的文件扩展名，以便识别。

![](img/569f8ac1b32b11a127f1482fb1f471d8_99.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_101.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_102.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_103.png)

现在，让我们执行命令，将三个文件打包成名为`myarchive.tar`的归档文件：

![](img/569f8ac1b32b11a127f1482fb1f471d8_105.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_107.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_109.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_111.png)

```bash
tar -cvf myarchive.tar file*.txt
```

![](img/569f8ac1b32b11a127f1482fb1f471d8_113.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_115.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_117.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_119.png)

命令执行后，会列出被添加到归档中的文件。此时，我们就成功创建了第一个tarball。使用`ls -l`命令查看，可以看到新生成的`myarchive.tar`文件。

![](img/569f8ac1b32b11a127f1482fb1f471d8_121.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_123.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_125.png)

你会发现，这个tar文件的大小（约40KB）比三个原始文件的总和（约30KB）要大。这是因为创建tarball需要添加一些元数据（可以理解为“袋子”本身的重量），这是构建归档的必然开销。不过别担心，当我们后续压缩它时，总体积会显著减小。

![](img/569f8ac1b32b11a127f1482fb1f471d8_127.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_129.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_131.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_132.png)

**注意**：在Linux中，文件扩展名（如`.tar`）主要是为了方便人类识别，系统本身并不依赖它来判断文件类型。我们可以使用`file`命令来确认文件的真实类型：

![](img/569f8ac1b32b11a127f1482fb1f471d8_134.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_135.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_137.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_139.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_141.png)

```bash
file myarchive.tar
```
该命令会输出类似`POSIX tar archive`的信息，证实它是一个tar归档。

![](img/569f8ac1b32b11a127f1482fb1f471d8_143.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_145.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_147.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_149.png)

## 查看与提取Tarball内容 🔍

![](img/569f8ac1b32b11a127f1482fb1f471d8_151.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_153.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_155.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_157.png)

现在我们已经有了一个tarball，你可能想在不解压的情况下查看里面包含哪些文件，或者需要将里面的文件提取出来。

![](img/569f8ac1b32b11a127f1482fb1f471d8_159.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_161.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_163.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_164.png)

以下是查看tarball内容的命令：

![](img/569f8ac1b32b11a127f1482fb1f471d8_166.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_168.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_170.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_172.png)

```bash
tar -tf myarchive.tar
```
*   **`-t`**：代表 lis**t**，用于列出归档文件中的内容。
*   **`-f`**：同样用于指定文件。

![](img/569f8ac1b32b11a127f1482fb1f471d8_174.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_176.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_178.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_180.png)

执行后，它会列出`myarchive.tar`中包含的所有文件名。

![](img/569f8ac1b32b11a127f1482fb1f471d8_182.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_184.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_186.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_188.png)

接下来，如果我们想从tarball中提取文件，假设我们已经删除了原始的`file1.txt`等文件，现在需要从归档中恢复它们。

![](img/569f8ac1b32b11a127f1482fb1f471d8_190.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_192.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_194.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_196.png)

以下是提取tarball内容的命令：

![](img/569f8ac1b32b11a127f1482fb1f471d8_198.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_200.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_202.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_204.png)

```bash
tar -xvf myarchive.tar
```
*   **`-x`**：代表 e**x**tract，用于从归档中提取文件。
*   **`-v`** 和 **`-f`** 选项的作用与之前相同。

![](img/569f8ac1b32b11a127f1482fb1f471d8_206.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_208.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_210.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_212.png)

执行此命令后，`tar`会将归档中的所有文件提取到当前目录。**重要提示**：提取操作并不会清空或删除原始的tarball，它就像一个可重复使用的袋子，里面的内容可以多次取出。

![](img/569f8ac1b32b11a127f1482fb1f471d8_214.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_216.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_218.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_220.png)

## 压缩归档文件以节省空间 📉

![](img/569f8ac1b32b11a127f1482fb1f471d8_221.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_223.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_225.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_227.png)

上一节我们学会了打包和提取，本节中我们来看看如何压缩归档文件以节省磁盘空间。压缩需要使用压缩算法，Linux世界主要使用**GZIP**和**Bzip2**两种。

![](img/569f8ac1b32b11a127f1482fb1f471d8_229.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_231.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_233.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_234.png)

*   **GZIP**：速度较快，但压缩率通常较低。
*   **Bzip2**：压缩率通常更高（文件更小），但需要更多的计算时间。

![](img/569f8ac1b32b11a127f1482fb1f471d8_236.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_238.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_240.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_242.png)

压缩是一个两阶段过程：先制作tarball，再压缩它。不过，`tar`命令也支持一步完成。

![](img/569f8ac1b32b11a127f1482fb1f471d8_244.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_246.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_248.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_250.png)

### 使用GZIP压缩

![](img/569f8ac1b32b11a127f1482fb1f471d8_252.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_253.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_255.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_257.png)

首先，我们使用`gzip`命令来压缩已有的tarball：

![](img/569f8ac1b32b11a127f1482fb1f471d8_259.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_261.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_263.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_265.png)

```bash
gzip myarchive.tar
```
执行后，原来的`myarchive.tar`会变成`myarchive.tar.gz`。使用`ls -l`查看，你会发现文件体积变小了（例如从40KB变为23KB）。可以使用`file`命令确认其类型。

![](img/569f8ac1b32b11a127f1482fb1f471d8_267.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_268.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_270.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_272.png)

要解压.gz文件，恢复出tar包，使用`gunzip`命令：

![](img/569f8ac1b32b11a127f1482fb1f471d8_274.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_276.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_278.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_280.png)

```bash
gunzip myarchive.tar.gz
```

![](img/569f8ac1b32b11a127f1482fb1f471d8_282.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_284.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_286.png)

### 使用Bzip2压缩

![](img/569f8ac1b32b11a127f1482fb1f471d8_288.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_289.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_291.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_293.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_295.png)

使用`bzip2`命令压缩tarball：

![](img/569f8ac1b32b11a127f1482fb1f471d8_297.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_299.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_301.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_302.png)

```bash
bzip2 myarchive.tar
```
执行后，文件会变成`myarchive.tar.bz2`。同样，可以使用`file`命令查看其类型。

![](img/569f8ac1b32b11a127f1482fb1f471d8_304.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_306.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_308.png)

要解压.bz2文件，使用`bunzip2`命令：

![](img/569f8ac1b32b11a127f1482fb1f471d8_310.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_312.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_314.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_316.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_318.png)

```bash
bunzip2 myarchive.tar.bz2
```

![](img/569f8ac1b32b11a127f1482fb1f471d8_320.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_322.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_324.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_326.png)

### 一步完成创建与压缩

![](img/569f8ac1b32b11a127f1482fb1f471d8_328.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_330.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_332.png)

因为“打包并压缩”是非常常见的操作，`tar`命令可以直接一步完成。这通过在原有选项基础上增加一个代表压缩算法的选项来实现。

**使用GZIP一步创建压缩归档：**
```bash
tar -czvf myarchive.tar.gz file*.txt
```
*   **`-z`**：代表使用**gzip**进行压缩。
*   注意：我们需要在文件名中手动加上`.gz`扩展名。

![](img/569f8ac1b32b11a127f1482fb1f471d8_334.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_335.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_337.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_339.png)

**使用Bzip2一步创建压缩归档：**
```bash
tar -cjvf myarchive.tar.bz2 file*.txt
```
*   **`-j`**：代表使用**bzip2**进行压缩。
*   同样，需要手动指定`.bz2`扩展名。

![](img/569f8ac1b32b11a127f1482fb1f471d8_341.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_343.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_345.png)

### 一步完成解压与提取

![](img/569f8ac1b32b11a127f1482fb1f471d8_346.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_348.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_350.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_352.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_353.png)

相应地，我们也可以一步从压缩归档中提取文件。

![](img/569f8ac1b32b11a127f1482fb1f471d8_355.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_357.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_359.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_360.png)

**从.tar.gz文件中提取：**
```bash
tar -xzvf myarchive.tar.gz
```

![](img/569f8ac1b32b11a127f1482fb1f471d8_362.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_364.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_366.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_368.png)

**从.tar.bz2文件中提取：**
```bash
tar -xjvf myarchive.tar.bz2
```
只需记住：**`-z`对应gzip，`-j`对应bzip2**，并将创建(`-c`)选项替换为提取(`-x`)选项即可。

![](img/569f8ac1b32b11a127f1482fb1f471d8_370.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_371.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_373.png)

## 关于ZIP文件 📎

![](img/569f8ac1b32b11a127f1482fb1f471d8_375.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_377.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_379.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_381.png)

ZIP是Windows或macOS上更常见的压缩归档格式。在Linux中，创建和解压ZIP文件是一个一步过程，使用`zip`和`unzip`命令。

![](img/569f8ac1b32b11a127f1482fb1f471d8_383.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_385.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_387.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_389.png)

**创建ZIP文件：**
```bash
zip myarchive.zip file1.txt file2.txt file3.txt
```

![](img/569f8ac1b32b11a127f1482fb1f471d8_391.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_393.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_394.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_396.png)

**解压ZIP文件：**
```bash
unzip myarchive.zip
```

![](img/569f8ac1b32b11a127f1482fb1f471d8_398.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_400.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_402.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_404.png)

## 总结 📝

![](img/569f8ac1b32b11a127f1482fb1f471d8_406.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_408.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_410.png)

本节课中我们一起学习了Linux下归档与压缩的核心操作：
1.  **Tarball**：使用`tar -cvf`命令将多个文件打包成一个归档文件（.tar）。
2.  **查看内容**：使用`tar -tf`命令列出归档内的文件，无需解压。
3.  **提取文件**：使用`tar -xvf`命令从归档中恢复文件。
4.  **压缩**：学习了两种主要压缩算法。
    *   **GZIP**：使用`gzip`压缩（生成.gz文件），使用`gunzip`解压。在`tar`命令中用`-z`选项集成。
    *   **Bzip2**：使用`bzip2`压缩（生成.bz2文件），使用`bunzip2`解压。在`tar`命令中用`-j`选项集成。
5.  **高效操作**：掌握了使用`tar -czvf`和`tar -xzvf`等命令一步完成打包压缩与解压提取。
6.  **ZIP格式**：了解了使用`zip`和`unzip`命令处理跨平台通用的ZIP文件。

![](img/569f8ac1b32b11a127f1482fb1f471d8_412.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_414.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_416.png)

![](img/569f8ac1b32b11a127f1482fb1f471d8_418.png)

记住这些命令组合，你就能轻松地管理Linux系统中的文件归档与压缩任务了。