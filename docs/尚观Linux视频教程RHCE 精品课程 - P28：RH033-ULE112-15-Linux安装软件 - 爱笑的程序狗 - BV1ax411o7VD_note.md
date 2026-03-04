# 尚观Linux视频教程RHCE精品课程：P28：RH033-ULE112-15-Linux安装软件

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_1.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_2.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_3.png)

在本节课中，我们将要学习Linux系统中安装软件的两种主要方式：源代码编译安装和RPM包管理安装。我们将了解它们的工作原理、区别以及具体的使用方法。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_5.png)

## 概述：Linux与Windows软件安装的区别

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_7.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_8.png)

上一节我们介绍了Linux的基本文件操作，本节中我们来看看如何为系统添加新软件。在Windows系统中，安装软件通常是双击可执行文件并按照向导点击“下一步”即可完成。这个过程对用户非常友好。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_10.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_12.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_13.png)

然而，在Linux系统中，软件安装方式与Windows有显著区别，这也是两者之间的主要差异之一。Linux下的软件有多种分发形式。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_15.png)

## 源代码编译安装

### 为什么Linux流行源代码安装？

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_17.png)

Linux系统中的软件，很多都是以源代码形式分发的。源代码是指程序的原始文本形式，例如用C语言编写的`.c`文件。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_19.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_20.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_22.png)

那么，为什么Linux更倾向于使用源代码分发呢？这需要从平台一致性角度考虑。Windows系统平台相对统一，一个编译好的程序（如病毒）很容易在不同的Windows机器上运行。但在Linux世界，不同机器的配置千差万别，例如内核版本、CPU架构（如i686与x86_64）都可能不同。这导致为一个平台编译的程序，在另一个平台上很可能无法运行。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_24.png)

因此，为了让软件能在各种不同的Linux环境中运行，最可靠的方式就是提供源代码，让用户在各自的机器上本地编译。这样生成的二进制程序就完全适应当前机器的特定环境。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_26.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_28.png)

### 如何获取与安装源代码

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_30.png)

安装软件时，务必从软件官方站点下载源代码，以避免下载到被篡改或包含恶意代码的版本。

以下是典型的源代码安装步骤：

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_32.png)

1.  **下载源代码包**：通常是以 `.tar.gz` 或 `.tar.bz2` 结尾的压缩包。
2.  **解压源代码包**：
    ```bash
    tar -zxvf software.tar.gz   # 解压 .tar.gz 文件
    # 或
    tar -jxvf software.tar.bz2  # 解压 .tar.bz2 文件
    ```
3.  **进入源代码目录并配置**：
    ```bash
    cd software-version/
    ./configure --prefix=/usr/local/software
    ```
    其中 `--prefix` 参数用于指定软件的安装目录。
4.  **编译源代码**：
    ```bash
    make
    ```
5.  **安装软件**：
    ```bash
    make install
    ```

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_34.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_36.png)

完成以上步骤后，软件就会被安装到 `--prefix` 指定的目录中。这种方式的优点是灵活、可定制，但过程较为复杂，且容易因环境依赖问题导致编译失败。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_38.png)

## RPM包管理安装

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_40.png)

### 什么是RPM？

为了解决源代码安装的复杂性，Red Hat推出了 **RPM（Red Hat Package Manager）** 包管理系统。RPM包是预编译好的二进制文件，类似于Windows中的安装程序，它使得软件安装变得简单快捷。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_42.png)

一个典型的RPM包文件名如 `xpdf-3.00-10.1.i386.rpm`，其中包含了软件名、版本、发行号和适用的平台架构。

### RPM的基本命令

以下是RPM包管理中最常用的命令和参数：

**安装软件包**
```bash
rpm -ivh package-name.rpm
```
*   `-i`：安装。
*   `-v`：显示详细信息。
*   `-h`：显示安装进度条。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_44.png)

**升级软件包**
```bash
rpm -Uvh new-package-name.rpm
```
*   `-U`：升级。如果旧版包不存在，则执行安装。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_46.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_47.png)

```bash
rpm -Fvh new-package-name.rpm
```
*   `-F`：升级。仅当旧版包已存在时才进行升级，否则不执行任何操作。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_49.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_50.png)

**删除软件包**
```bash
rpm -e package-name
```
注意，这里使用的是软件包名（如 `xpdf`），而不是完整的RPM文件名。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_52.png)

**查询软件包**

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_54.png)

RPM提供了强大的查询功能，以下是常用查询参数：
*   `-qa`：查询所有已安装的包。
    ```bash
    rpm -qa | grep httpd
    ```
*   `-q`：查询指定包是否安装。
    ```bash
    rpm -q httpd
    ```
*   `-qi`：显示包的详细信息（描述、版本等）。
    ```bash
    rpm -qi httpd
    ```
*   `-ql`：列出包安装的所有文件。
    ```bash
    rpm -ql httpd
    ```
*   `-qf`：查询某个文件属于哪个已安装的包。
    ```bash
    rpm -qf /usr/bin/ls
    ```
*   `-qp`：针对**未安装**的RPM文件进行查询（需配合 `-i`， `-l` 等使用）。
    ```bash
    rpm -qpi package-file.rpm  # 查看未安装包的信息
    rpm -qpl package-file.rpm  # 查看未安装包包含的文件列表
    ```

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_56.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_58.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_59.png)

### 处理依赖关系

在安装或删除软件包时，经常会遇到依赖关系问题。例如，安装A包需要先安装B包，而B包又依赖于C包。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_61.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_62.png)

以下是处理依赖关系的常用参数：
*   `--nodeps`：忽略依赖关系，强制安装或删除。**（慎用，可能导致软件无法运行）**
    ```bash
    rpm -ivh --nodeps package-A.rpm
    rpm -e --nodeps package-name
    ```
*   `--force`：强制重新安装，覆盖现有文件。
    ```bash
    rpm -ivh --force package-name.rpm
    ```
*   `--aid`（在RHEL5及以后版本中已移除）：自动尝试解决依赖关系（需要所有相关RPM包都在当前目录）。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_64.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_65.png)

在RHEL5及更高版本中，推荐使用 `yum` 工具来自动解决依赖关系并从网络仓库安装软件。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_67.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_69.png)

### 验证软件包完整性

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_71.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_73.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_75.png)

`rpm -V` 命令用于验证已安装软件包的文件是否被修改过，这是系统安全审计的一个有用工具。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_77.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_79.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_81.png)

```bash
rpm -V httpd
```
如果输出为空，则表示文件未被修改。如果文件被改动，命令会输出提示，其中可能包含以下字符标识变更类型：
*   `S`：文件大小改变。
*   `M`：文件权限或类型改变。
*   `5`：MD5校验和改变（文件内容改变）。
*   `D`：设备文件改变。
*   `L`：符号链接路径改变。
*   `U`：文件所属用户改变。
*   `G`：文件所属组改变。
*   `T`：文件修改时间改变。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_83.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_84.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_85.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_86.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_88.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_90.png)

也可以验证单个文件：
```bash
rpm -Vf /bin/ls
```

## 其他Linux软件管理方式

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_92.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_93.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_95.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_96.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_97.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_98.png)

除了源代码和RPM，Linux还有其他软件管理方式，形成了不同“流派”：
*   **DPKG/APT**：Debian、Ubuntu等系统使用的包管理工具。
*   **YUM**：基于RPM的发行版（如RHEL、CentOS、Fedora）的高级包管理工具，能自动解决依赖关系。
*   **源码编译（Tarball）**：最原始、最通用的方式，如前文所述。

## 总结

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_100.png)

本节课中我们一起学习了Linux下安装软件的两种核心方法。

我们首先了解了**源代码编译安装**，这是Linux的传统方式，通过 `./configure && make && make install` 流程将源代码适配并安装到本地系统。这种方式灵活但步骤繁琐。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_102.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_104.png)

接着，我们重点学习了 **RPM包管理安装**。RPM通过预编译的二进制包简化了安装过程，使用 `rpm -ivh` 安装，`rpm -e` 卸载，并通过 `-q` 系列参数进行查询。我们还探讨了如何处理令人头疼的依赖关系（使用 `--nodeps` 或 `yum`），以及如何使用 `rpm -V` 来验证软件包的完整性，确保系统安全。

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_106.png)

![](img/5a5f3212204cc9b714acffdd0bdb9e9c_107.png)

理解并掌握这些软件安装方法，是有效管理和维护Linux系统的基础技能。