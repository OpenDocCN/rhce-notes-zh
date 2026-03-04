# 认识Bash Shell：第九章：Bash Shell基础入门

在本节课中，我们将要学习什么是Shell，特别是Bash Shell，并了解其核心功能，包括通配符、快捷键和变量等基础概念。这些知识是后续学习Shell脚本编程的重要基础。

## 什么是Shell？🤔

![](img/28a6bdb20c0d0eaaf553576a646e4c61_1.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_2.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_4.png)

上一节我们介绍了课程概述，本节中我们来看看Shell到底是什么。

![](img/28a6bdb20c0d0eaaf553576a646e4c61_6.png)

Shell是一种命令解释器。它能够识别用户输入的各种命令，并将这些命令传递给操作系统执行。例如，当您输入`ls`命令时，Shell负责解释这个命令，并告诉系统去列出当前目录的内容。它的作用类似于Windows系统中的命令行（如PowerShell）。Shell是用户与系统内核交互的界面，也是一种脚本语言。

为了更直观地理解，我们可以想象系统的结构：
*   **硬件**：计算机的物理部分。
*   **内核（Kernel）**：直接控制硬件的核心软件。
*   **Shell**：位于用户和内核之间的“翻译官”或“桥梁”。
*   **用户/应用程序**：发出指令的源头。

当您在命令行输入一个指令（如`ls`），Shell首先解释这个指令，然后将解释后的结果传递给内核，内核再去调度硬件执行相应的操作。因此，Shell是我们与计算机系统沟通的媒介。

不同的Shell有不同的特性和功能。常见的Shell有：
*   **Bash (Bourne-Again Shell)**：Linux系统默认的Shell，功能强大且兼容性好。
*   **Sh (Bourne Shell)**：早期的Unix Shell。
*   **Ksh (Korn Shell)**、**Csh (C Shell)**：其他类型的Shell。

![](img/28a6bdb20c0d0eaaf553576a646e4c61_8.png)

在Red Hat Enterprise Linux 8中，默认使用的是Bash Shell。您可以通过查看`/etc/shells`文件来了解系统支持哪些Shell，并通过修改用户配置文件（如`/etc/passwd`中用户的最后一列）来指定用户登录时使用的Shell类型。

## Bash Shell功能回顾 🔄

在深入了解新功能之前，我们先来回顾一下Bash Shell中我们已经接触过的一些实用功能。

以下是Bash Shell的一些常用功能：

![](img/28a6bdb20c0d0eaaf553576a646e4c61_10.png)

*   **历史命令（history）**：使用`history`命令可以查看之前执行过的命令列表。使用上下方向键可以快速调用历史命令。
*   **清空历史记录**：可以使用`history -c`命令清空当前会话的内存历史记录。若要永久清空，还需要清空用户家目录下的`.bash_history`文件（例如使用`echo “” > ~/.bash_history`）。使用`history -w`命令可以将当前内存中的历史记录写入文件。
*   **搜索历史命令**：按 `Ctrl + R` 可以反向搜索历史命令。
*   **命令补全（Tab键）**：输入命令或文件路径的一部分后，按`Tab`键可以自动补全。如果系统是最小化安装，可能需要安装`bash-completion`软件包才能使用此功能。
*   **输入/输出重定向**：使用 `>`、`>>`、`<` 等符号来重定向命令的输入和输出。
*   **管道（Pipe）**：使用 `|` 符号将一个命令的输出作为另一个命令的输入。

![](img/28a6bdb20c0d0eaaf553576a646e4c61_12.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_14.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_16.png)

## Bash Shell快捷键指南 ⌨️

![](img/28a6bdb20c0d0eaaf553576a646e4c61_18.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_19.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_21.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_22.png)

熟练使用快捷键可以极大提高在命令行下的工作效率。

![](img/28a6bdb20c0d0eaaf553576a646e4c61_24.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_26.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_27.png)

以下是Bash Shell中一些重要的快捷键列表：

![](img/28a6bdb20c0d0eaaf553576a646e4c61_29.png)

*   **`Ctrl + C`**：终止当前正在运行的前台命令。
*   **`Ctrl + L`**：清空当前终端屏幕（等同于执行`clear`命令）。
*   **`Ctrl + R`**：反向搜索历史命令。
*   **`Ctrl + D`**：退出当前终端会话；在输入时，表示“文件结束符”（EOF）。
*   **`Ctrl + A`** / **`Home`键**：将光标移动到命令行的开头。
*   **`Ctrl + E`** / **`End`键**：将光标移动到命令行的末尾。
*   **`Ctrl + U`**：剪切从光标处到行首的所有字符。
*   **`Ctrl + K`**：剪切从光标处到行尾的所有字符。
*   **`Ctrl + Y`**：粘贴之前剪切（`Ctrl+U`或`Ctrl+K`）的内容。
*   **`Ctrl + Z`**：将当前前台任务挂起到后台（关于进程管理，后续课程会详细讲解）。

![](img/28a6bdb20c0d0eaaf553576a646e4c61_31.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_33.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_35.png)

> **注意**：部分复制/粘贴快捷键（如`Ctrl+Shift+C/V`）可能因您使用的终端模拟器（如PuTTY, SecureCRT, Xshell等）而异。

![](img/28a6bdb20c0d0eaaf553576a646e4c61_37.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_38.png)

## 通配符：匹配文件名的利器 🎯

![](img/28a6bdb20c0d0eaaf553576a646e4c61_40.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_42.png)

上一节我们回顾了Shell的基本操作，本节中我们来看看Shell中用于匹配文件名的强大工具——通配符。

![](img/28a6bdb20c0d0eaaf553576a646e4c61_44.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_46.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_48.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_50.png)

通配符主要用于匹配文件名或路径名，这与之前学习的**正则表达式（用于匹配文本内容）**有本质区别，请务必区分。例如，`rm -rf /tmp/*` 中的星号`*`就是一个通配符，表示`/tmp`目录下的所有文件。

![](img/28a6bdb20c0d0eaaf553576a646e4c61_52.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_53.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_55.png)

以下是Bash Shell中常用的通配符及其含义：

![](img/28a6bdb20c0d0eaaf553576a646e4c61_57.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_58.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_59.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_61.png)

1.  **星号 `*`**
    *   **含义**：匹配零个或多个任意字符。
    *   **示例**：
        *   `ls *`：列出当前目录所有文件。
        *   `ls *.txt`：列出所有以`.txt`结尾的文件。
        *   `ls p*`：列出所有以字母`p`开头的文件。

![](img/28a6bdb20c0d0eaaf553576a646e4c61_63.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_65.png)

2.  **问号 `?`**
    *   **含义**：匹配恰好一个任意字符。
    *   **示例**：
        *   `ls ?`：列出所有文件名恰好为一个字符的文件。
        *   `ls a?.txt`：列出所有以`a`开头，后接一个任意字符，并以`.txt`结尾的文件（如`a1.txt`, `ab.txt`）。

![](img/28a6bdb20c0d0eaaf553576a646e4c61_67.png)

3.  **中括号 `[]`**
    *   **含义**：匹配括号内列出的任意一个字符。
    *   **示例**：
        *   `ls [abc]*`：列出所有以`a`、`b`或`c`开头的文件。
        *   `ls file[0-9].log`：列出如`file1.log`、`file2.log`...`file9.log`的文件。

4.  **感叹号 `!` （在中括号内使用）**
    *   **含义**：匹配**不在**括号内列出的任意一个字符。
    *   **示例**：
        *   `ls [!abc]*`：列出所有**不以**`a`、`b`或`c`开头的文件。

![](img/28a6bdb20c0d0eaaf553576a646e4c61_69.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_71.png)

5.  **花括号 `{}`**
    *   **含义**：生成一个序列或匹配多个模式，常用于批量创建文件或目录。
    *   **示例**：
        *   `touch file{1,2,3}.txt`：创建`file1.txt`， `file2.txt`， `file3.txt`三个文件。
        *   `mkdir -p /home/user/{dir1,dir2,dir3}`：在指定路径下同时创建三个目录。

## 总结 📚

![](img/28a6bdb20c0d0eaaf553576a646e4c61_73.png)

![](img/28a6bdb20c0d0eaaf553576a646e4c61_75.png)

本节课中我们一起学习了Bash Shell的基础知识。我们首先明确了Shell作为“命令解释器”和“用户与内核桥梁”的角色。接着，我们回顾并补充了Bash Shell的实用功能，如历史命令管理和常用快捷键。最后，我们重点讲解了**通配符**，学会了如何使用`*`、`?`、`[]`、`{}`等符号来灵活地匹配和操作文件名。

![](img/28a6bdb20c0d0eaaf553576a646e4c61_77.png)

理解这些基础概念和操作，是后续学习环境变量、Shell脚本编程等更高级内容的基石。请务必动手练习，熟练掌握。