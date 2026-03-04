# Linux基础教程：第九章：Shell基础与Bash Shell操作 🐚

在本章中，我们将学习Linux Shell的核心概念。Shell是用户与系统内核之间的桥梁，负责解释命令并传递给操作系统执行。我们将重点了解什么是Shell，特别是Bash Shell的基本操作、通配符、变量和别名等实用功能。这些知识是后续学习Shell脚本编程的重要基础。

## 什么是Shell？🤔

![](img/bc10f794bea979a41c0dd0b9a832620d_1.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_2.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_4.png)

上一节我们概述了本章内容，本节中我们来看看Shell到底是什么。

![](img/bc10f794bea979a41c0dd0b9a832620d_6.png)

Shell是一种命令解释器。它能识别用户输入的各种命令，并将这些命令传递给操作系统执行。例如，当您输入 `ls` 命令时，正是由Shell来解释这个命令，并告诉系统去列出当前目录的内容。它的作用类似于Windows系统中的命令行或PowerShell。

Shell是用户与系统交互的界面，同时也是一种用于控制系统的脚本语言。

为了更直观地理解，我们可以参考系统架构图。计算机的核心是硬件，硬件之上是直接管理硬件的**内核**。然而，用户无法直接与内核对话。**Shell**就位于用户命令和系统内核之间，充当翻译官的角色。用户在外层输入命令，Shell接收并解释这些命令，然后将解释结果交给内核，内核最终调度硬件完成操作。

![](img/bc10f794bea979a41c0dd0b9a832620d_8.png)

因此，Shell是连接用户与系统内核的桥梁。

## 系统中的Shell类型 🔧

了解了Shell的基本定义后，我们来看看Linux系统中常见的Shell类型。

在Linux系统中，存在多种Shell。常见的包括Bourne Shell、C Shell、Korn Shell以及我们最常用的**Bash Shell**。您可以通过查看 `/etc/shells` 文件来了解系统支持哪些Shell。

![](img/bc10f794bea979a41c0dd0b9a832620d_10.png)

在Red Hat Enterprise Linux 8中，默认的Shell是Bash。您可能会看到 `/bin/sh` 等路径，但它们通常都是链接到 `/bin/bash` 的符号链接，本质上都是Bash Shell。

![](img/bc10f794bea979a41c0dd0b9a832620d_12.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_14.png)

不同的Shell在功能和使用体验上可能有差异。例如，早期的某些Shell不支持命令历史回溯、Tab键补全或光标左右移动，而Bash Shell则提供了这些便捷功能，这也是它广为流行的原因。用户登录时使用的Shell类型，由用户配置文件中（如 `/etc/passwd` 的最后一列）的条目决定。

![](img/bc10f794bea979a41c0dd0b9a832620d_15.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_17.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_18.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_20.png)

## Bash Shell功能回顾与快捷键 ⌨️

![](img/bc10f794bea979a41c0dd0b9a832620d_22.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_24.png)

我们已经知道Bash Shell是我们主要使用的环境，现在我们来回顾和补充一些它的基本功能。

![](img/bc10f794bea979a41c0dd0b9a832620d_26.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_28.png)

以下是Bash Shell中一些常用功能的回顾：
*   **命令历史**：使用 `history` 命令查看之前执行过的命令。使用上下箭头键可以翻找历史命令。
*   **历史记录管理**：
    *   `history -c`：清除当前会话内存中的历史记录。
    *   `history -w`：将内存中的历史记录立即写入历史文件（默认为 `~/.bash_history`）。
    *   若要永久清除所有记录，需要同时清空历史文件。
*   **快速执行历史命令**：使用 `!` 加命令编号（如 `!203`）可以快速重新执行该条历史命令。
*   **搜索历史命令**：按 `Ctrl + R` 组合键，然后输入关键词进行搜索。
*   **命令补全**：输入命令或路径的一部分后，按 `Tab` 键可以自动补全。按两次 `Tab` 键可以列出所有可能的选项。
*   **输入/输出重定向与管道**：使用 `>`、`>>`、`<` 和 `|` 等符号，这些是Shell提供的强大功能。

![](img/bc10f794bea979a41c0dd0b9a832620d_30.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_32.png)

接下来，我们介绍一些提高效率的Bash Shell快捷键。

![](img/bc10f794bea979a41c0dd0b9a832620d_34.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_35.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_37.png)

以下是部分实用的Bash Shell快捷键列表：
*   `Ctrl + C`：终止当前正在运行的前台命令。
*   `Ctrl + L`：清空当前终端屏幕（等同于执行 `clear` 命令）。
*   `Ctrl + A`：将光标移动到命令行的行首。
*   `Ctrl + E`：将光标移动到命令行的行尾。
*   `Ctrl + U`：删除从光标位置到行首的所有字符。
*   `Ctrl + K`：删除从光标位置到行尾的所有字符。
*   `Ctrl + Y`：粘贴之前用 `Ctrl+U` 或 `Ctrl+K` 剪切的内容。
*   `Ctrl + D`：退出当前终端会话；在输入时，表示输入结束（EOF）。
*   `Ctrl + R`：逆向搜索命令历史。
*   `Ctrl + Z`：将当前前台任务挂起到后台（涉及进程管理，后续会详细讲解）。

![](img/bc10f794bea979a41c0dd0b9a832620d_39.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_41.png)

## 总结 📚

![](img/bc10f794bea979a41c0dd0b9a832620d_43.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_45.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_47.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_49.png)

![](img/bc10f794bea979a41c0dd0b9a832620d_50.png)

本节课中我们一起学习了Linux Shell的基础知识。我们明确了Shell作为“命令解释器”和“用户-内核桥梁”的角色。我们了解了系统中存在多种Shell，而Bash是RHEL 8的默认且功能强大的Shell。我们还回顾并学习了Bash Shell的诸多实用功能，包括历史命令管理和一系列能极大提升操作效率的快捷键。掌握这些基础是熟练使用Linux命令行和进一步学习Shell脚本的必经之路。