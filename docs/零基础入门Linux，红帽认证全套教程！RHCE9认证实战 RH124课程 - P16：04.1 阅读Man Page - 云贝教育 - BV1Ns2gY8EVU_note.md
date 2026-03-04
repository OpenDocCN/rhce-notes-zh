# Linux入门与红帽认证：04：获取本地帮助

![](img/8bc094d4f73ba76261cadbcbe0915eaa_1.png)

在本章中，我们将学习如何在红帽企业Linux及其他Linux发行版中，利用系统自带的帮助系统来获取命令和功能的详细文档。掌握这些技能对于高效使用和排查Linux系统至关重要。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_3.png)

本章主要包含两个目标：一是学习使用手册页（Man Page）查找信息；二是了解一个补充的帮助工具 `info`。虽然 `info` 在RHCE9的RH124课程中已被移除，但它仍是一个有用的补充知识。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_5.png)

---

![](img/8bc094d4f73ba76261cadbcbe0915eaa_7.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_8.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_10.png)

## 04.1：阅读手册页（Man Page）📖

![](img/8bc094d4f73ba76261cadbcbe0915eaa_12.png)

在之前的章节中，我们介绍过通过 `command --help` 的语法来获取命令的简要帮助。然而，这种方法存在一些局限性。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_14.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_16.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_18.png)

上一节我们介绍了获取帮助的几种初步方法，本节中我们来看看功能更强大的手册页系统。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_20.png)

### 获取帮助的几种方式及其局限

![](img/8bc094d4f73ba76261cadbcbe0915eaa_22.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_24.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_26.png)

以下是几种常见的获取帮助方式及其存在的问题：

1.  **`command --help`**
    *   **缺点**：提供的信息通常比较简略，不够全面。
    *   **示例**：执行 `ps --help` 会输出帮助，但其中的选项（如 `-e`, `-f`）含义仍需进一步查询。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_28.png)

2.  **命令类型的影响**
    *   Linux命令分为**内置命令**（Shell自带）和**外部命令**（通过安装软件获得）。
    *   对于内置命令（如 `echo`），`--help` 可能无效。此时应使用 `help` 命令。
    *   **示例**：使用 `help echo` 来查看 `echo` 内置命令的详细帮助。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_30.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_32.png)

3.  **软件自带的文档**
    *   部分应用程序会在 `/usr/share/doc/` 目录下安装详细的文档。
    *   **访问方式**：
        *   命令行：`cd /usr/share/doc/<软件包名>/`
        *   图形界面：在浏览器中使用 `file:///usr/share/doc/` 协议访问。
    *   **缺点**：并非所有软件都附带文档，且文档的完整度不一。有时需要单独安装 `-doc` 后缀的软件包。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_34.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_35.png)

由于上述方法在信息量和可用性上都不够稳定，因此我们需要一个更系统、更全面的帮助来源——这就是手册页（Man Page）。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_37.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_39.png)

### 什么是手册页（Man Page）？

![](img/8bc094d4f73ba76261cadbcbe0915eaa_41.png)

手册页是Linux系统最核心、最全面的本地帮助文档。它最初由开发人员在开发过程中总结问题与解决方案而形成。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_43.png)

手册页的数据文件存放在 `/usr/share/man/` 目录下，并按章节分类。

### 手册页的章节（Section）

手册页根据内容分为不同的章节，类似于书籍的目录。了解章节有助于快速定位信息。

以下是主要章节及其含义：

![](img/8bc094d4f73ba76261cadbcbe0915eaa_45.png)

*   **man1**：普通用户可执行的命令或应用程序。
*   **man2**：系统调用（内核提供的函数接口）。
*   **man3**：库函数（程序开发用的函数接口）。
*   **man4**：特殊文件（如 `/dev` 目录下的设备文件）。
*   **man5**：文件格式与配置文件语法。
*   **man6**：游戏（当前版本通常为空）。
*   **man7**：惯例、协议与文件系统标准。
*   **man8**：系统管理命令（通常需要root权限）。
*   **man9**：内核内部例程。

### 如何使用 `man` 命令

![](img/8bc094d4f73ba76261cadbcbe0915eaa_47.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_48.png)

使用 `man` 命令的基本语法是：`man [章节号] <主题>`

![](img/8bc094d4f73ba76261cadbcbe0915eaa_50.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_52.png)

*   **`<主题>`**：可以是命令、函数、文件或协议名。
*   **`[章节号]`**：指定从哪个章节查找。如果省略，`man` 会从第1章开始依次搜索，直到找到第一个匹配的主题。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_54.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_56.png)

以下是各章节的查询示例：

![](img/8bc094d4f73ba76261cadbcbe0915eaa_58.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_60.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_62.png)

*   **查询普通命令**：`man 1 uname` （查看 `uname` 命令的用法）
*   **查询系统调用**：`man 2 read` （查看 `read()` 系统调用的说明）
*   **查询库函数**：`man 3 getpw` （查看 `getpw()` 库函数的说明）
*   **查询设备文件**：`man 4 pts` （查看伪终端设备的说明）
*   **查询文件格式**：`man 5 passwd` （查看 `/etc/passwd` 文件格式的说明）
*   **查询文件系统标准**：`man 7 hier` （查看文件系统目录结构的说明）
*   **查询管理命令**：`man 8 dnf` （查看 `dnf` 包管理器的说明）

![](img/8bc094d4f73ba76261cadbcbe0915eaa_64.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_66.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_68.png)

### 在手册页中导航

![](img/8bc094d4f73ba76261cadbcbe0915eaa_70.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_72.png)

打开手册页后，可以使用以下按键进行浏览和搜索：

![](img/8bc094d4f73ba76261cadbcbe0915eaa_74.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_75.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_77.png)

*   **空格键** / **PageDown**：向下翻一屏。
*   **PageUp**：向上翻一屏。
*   **方向键上/下**：向上/下移动一行。
*   **`d`** / **`u`**：向下/上移动半屏。
*   **`/关键词`**：向后搜索指定关键词。按 `n` 查找下一个，按 `Shift+n` (或 `N`) 查找上一个。
*   **`g`** / **`G`**：跳转到手册页开头/结尾。
*   **`q`**：退出手册页。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_79.png)

### 手册页的结构

![](img/8bc094d4f73ba76261cadbcbe0915eaa_81.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_83.png)

一个典型的手册页包含以下几个部分：

![](img/8bc094d4f73ba76261cadbcbe0915eaa_85.png)

*   **NAME（名称）**：命令或主题的名称及简要说明。
*   **SYNOPSIS（概要）**：命令的语法格式。
*   **DESCRIPTION（描述）**：详细的功能描述。
*   **OPTIONS（选项）**：各个命令行选项的说明。
*   **EXAMPLES（示例）**：使用示例（不一定每个手册页都有）。
*   **FILES（相关文件）**：与该命令或主题相关的配置文件。
*   **SEE ALSO（参见）**：其他相关的手册页主题。
*   **BUGS（已知问题）**：报告已知的软件缺陷。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_87.png)

### 搜索手册页

![](img/8bc094d4f73ba76261cadbcbe0915eaa_89.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_90.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_92.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_93.png)

如果你不确定主题名或所属章节，可以使用搜索功能。

1.  **基于描述搜索**：`man -k <关键词>`
    *   此命令在所有手册页的 **NAME** 和 **DESCRIPTION** 部分搜索关键词。
    *   **注意**：首次使用前，可能需要root权限更新缓存：`sudo mandb`。
    *   **示例**：`man -k systemd` 会列出所有描述中包含 “systemd” 的手册页。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_95.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_97.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_99.png)

2.  **全文搜索**：`man -K <关键词>`
    *   此命令在所有手册页的 **全部内容** 中进行搜索，速度较慢，但更全面。
    *   搜索到匹配项后，按回车查看，按 `Ctrl+d` 跳过，按 `Ctrl+c` 终止搜索。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_101.png)

---

![](img/8bc094d4f73ba76261cadbcbe0915eaa_103.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_105.png)

## 本章总结

![](img/8bc094d4f73ba76261cadbcbe0915eaa_107.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_109.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_111.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_113.png)

在本节课中，我们一起学习了如何在Linux系统中有效地获取本地帮助。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_114.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_116.png)

我们首先回顾了 `--help` 和软件自带文档等基础方法的局限性。然后，我们深入探讨了功能强大的手册页系统，包括其章节分类（Section 1-9）、使用方法（`man` 命令）、页面导航技巧以及基于关键词的搜索（`man -k` 和 `man -K`）。

![](img/8bc094d4f73ba76261cadbcbe0915eaa_118.png)

![](img/8bc094d4f73ba76261cadbcbe0915eaa_120.png)

掌握手册页的使用是成为一名熟练Linux用户或管理员的基础技能。当你遇到不熟悉的命令或需要了解配置细节时，学会查阅手册页将为你提供最权威、最直接的答案。