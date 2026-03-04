# 红帽RHCE8认证课程：02-2：命令行CLI介绍 🖥️

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_1.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_3.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_4.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_6.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_8.png)

在本节课中，我们将要学习Linux操作系统的核心——命令行接口（CLI）。我们将了解什么是命令行、它的工作原理、如何访问和使用它，以及执行命令的基本规则。掌握这些基础知识是后续所有Linux操作和维护工作的前提。

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_10.png)

## 什么是命令行？ 💬

命令行是用户与Linux操作系统交互的主要接口。Linux系统的构建核心就是围绕着命令行展开的。我们通过在命令行中输入特定的指令来维护和管理操作系统。

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_12.png)

例如，我们之前修改用户密码的操作，就是在命令行窗口中完成的。我们输入的字符序列被称为“指令”，系统会按照特定规则来识别和执行这些指令。

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_14.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_15.png)

## Shell程序：命令行的提供者 🐚

我们看到的命令行窗口，实际上是由一个名为“Shell”的后端程序提供的。Shell是一个应用程序，它负责接收用户输入的指令、解析指令，并与操作系统内核沟通以执行指令。

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_17.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_19.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_21.png)

例如，在图形界面下，我们常用的终端程序是 `gnome-terminal`。你可以通过按 `Alt+F2`，输入 `gnome-terminal` 来启动它。而在纯字符界面下，系统会提供其他类型的终端。

在红帽Linux 8系统中，默认使用的Shell程序是 **Bash**（Bourne Again Shell）。Bash是Unix/Linux系统中最成功、使用最广泛的Shell之一，它是早期SH Shell的改进版本。

### Shell的工作流程

为了更好地理解Shell的角色，我们可以将其工作流程简化如下：

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_23.png)

1.  **用户输入**：用户在命令行中输入一个字符串（例如 `ls` 或 `passwd`）。
2.  **Shell解析**：Shell程序解析这个字符串，判断它是一个有效的命令、选项还是参数。
3.  **内核执行**：Shell将解析后的指令交给操作系统内核（Kernel）去真正执行。
4.  **结果返回**：内核执行完毕后，将结果返回给Shell。
5.  **输出显示**：Shell将结果（标准输出）显示在终端窗口中。

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_25.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_27.png)

这个流程可以概括为：**用户 -> Shell（解析） -> 内核（执行） -> Shell（显示） -> 用户**。了解这个流程有助于在命令出错时进行排查。

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_29.png)

## 访问命令行终端 🖱️

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_31.png)

Linux提供了多种方式来访问命令行终端。

### 图形界面终端

在图形化桌面环境（如GNOME）中，我们可以打开终端应用程序。以下是一些常用的操作技巧：

*   **新建标签页**：快捷键 `Ctrl+Shift+T` 可以在当前窗口新建一个标签页。
*   **新建窗口**：快捷键 `Ctrl+Shift+N` 可以打开一个全新的独立终端窗口。
*   **切换标签页**：使用 `Alt+数字键`（如 `Alt+1`， `Alt+2`）可以在不同标签页间快速切换。
*   **调整字体**：使用 `Ctrl+Shift+加号(+)` 放大字体，`Ctrl+减号(-)` 缩小字体。
*   **分离标签页**：可以将标签页拖拽出来，使其成为一个独立的窗口。

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_33.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_35.png)

### 虚拟终端（TTY）

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_36.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_38.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_39.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_41.png)

即使在没有图形界面的服务器上，Linux也提供了虚拟终端供用户登录。在红帽8中，你可以通过快捷键在图形界面和虚拟终端之间切换：

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_43.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_45.png)

*   `Ctrl+Alt+F2` 到 `Ctrl+Alt+F6`：切换到字符界面的虚拟终端（如 tty2, tty3）。
*   `Ctrl+Alt+F1` 或 `Ctrl+Alt+F7`：通常切换回图形界面。

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_47.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_49.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_51.png)

在虚拟终端中登录后，你可以输入 `tty` 命令来查看当前所在的终端设备名（如 `/dev/tty2`）。而在图形界面打开的终端，设备名通常是 `/dev/pts/0`、`/dev/pts/1` 等。

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_53.png)

## 理解命令提示符 🎯

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_55.png)

打开终端后，你会看到一串字符，例如 `[kiosk@foundation0 Desktop]$`，这就是命令提示符。它包含了重要信息：

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_56.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_58.png)

*   `kiosk`：当前登录的用户名。
*   `@`：分隔符。
*   `foundation0`：主机名。
*   `Desktop`：当前所在的工作目录。
*   `$` 或 `#`：提示符结尾。`$` 表示当前是普通用户，`#` 表示当前是超级管理员（root）用户。

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_60.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_62.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_64.png)

命令提示符的格式由一个名为 `PS1` 的环境变量控制。你可以通过修改变量值来自定义提示符的显示。例如：
```bash
PS1='[\u@\h \W]\$ '
```
其中 `\u` 代表用户名，`\h` 代表主机名，`\W` 代表当前目录的基名，`\$` 会根据用户身份显示 `$` 或 `#`。

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_66.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_68.png)

## 命令行的结构与语法 📖

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_70.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_72.png)

在Shell中执行命令需要遵循一定的结构。一个典型的命令行由以下三部分组成：

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_74.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_76.png)

**命令 [选项] [参数...]**

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_78.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_80.png)

1.  **命令**：空格后的第一个单词，指定要执行的操作（如 `ls`, `cat`）。
2.  **选项**：用于调整命令的行为，通常以一个 (`-`) 或两个 (`--`) 横杠开头。例如 `ls -a` 或 `ls --all` 都表示列出所有文件（包括隐藏文件）。选项是可选的。
3.  **参数**：命令作用的对象，通常是文件名、目录名等。例如 `ls /home` 表示列出 `/home` 目录的内容。一个命令可以有多个参数。

### 语法示例

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_82.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_84.png)

查看命令的帮助文档时，通常会看到如下语法说明：
```
用法：ls [选项]... [文件]...
```
*   `ls`：命令本身。
*   `[选项]...`：方括号 `[]` 表示内容是可选的，省略号 `...` 表示可以指定多个同类项（多个选项）。
*   `[文件]...`：同样，可以指定零个、一个或多个文件或目录作为参数。

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_86.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_87.png)

## 远程访问命令行 🌐

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_89.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_91.png)

在实际工作中，我们经常需要远程管理服务器。Linux下最常用的远程登录工具是 `ssh`（Secure Shell）。

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_93.png)

基本用法是：`ssh 用户名@主机名或IP地址`
例如：`ssh student@servera`

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_95.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_96.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_98.png)

执行后，系统会提示输入对应用户的密码。验证通过后，你就会登录到远程服务器的命令行环境中。要结束远程会话，输入 `exit` 命令或按 `Ctrl+D` 即可退出。

有些配置了密钥认证的服务器，登录时可能不需要输入密码，这种方式更安全，我们会在后续课程中详细讲解。

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_100.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_102.png)

---

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_104.png)

![](img/9bbe304dc4a742f5aa59b8beb9f0349f_106.png)

本节课中我们一起学习了Linux命令行的基础知识。我们了解了命令行是Linux的核心交互界面，由Shell程序提供。我们学会了如何在图形界面和虚拟终端中访问命令行，并理解了命令提示符的含义。最重要的是，我们掌握了命令行指令的基本结构：**命令、选项和参数**，这是后续所有命令学习的基础。最后，我们还简单接触了如何使用 `ssh` 工具进行远程登录管理。