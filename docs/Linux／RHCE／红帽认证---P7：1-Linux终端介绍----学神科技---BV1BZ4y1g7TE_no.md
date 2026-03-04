# Linux基础操作：P7：Linux终端介绍、Shell提示符与Bash Shell基本语法

![](img/5e9029ff83d32df61ed44a3ee635ca5e_1.png)

![](img/5e9029ff83d32df61ed44a3ee635ca5e_2.png)

![](img/5e9029ff83d32df61ed44a3ee635ca5e_4.png)

## 概述
在本节课中，我们将学习Linux终端的基本概念、Shell提示符的含义以及Bash Shell的基础语法。通过本节内容，您将对Linux操作环境有一个初步的认识，并掌握一些实用的终端操作技巧。

![](img/5e9029ff83d32df61ed44a3ee635ca5e_6.png)

## 什么是Linux终端
Linux终端是用户与Linux系统进行交互的界面。在图形界面中，可以通过点击图标打开一个终端窗口。例如，在GNOME桌面环境中，这个终端称为GNOME Terminal。不同的桌面环境（如KDE）会有不同风格的终端。此外，还有专门的远程连接工具，如Xshell、SecureCRT等。

![](img/5e9029ff83d32df61ed44a3ee635ca5e_8.png)

打开终端后，输入`tty`命令可以查看当前终端的类型。

![](img/5e9029ff83d32df61ed44a3ee635ca5e_10.png)

**终端常用快捷键：**
*   `Ctrl` + `+`：放大终端字体。
*   `Ctrl` + `-`：缩小终端字体。
*   `Ctrl` + `Shift` + `T`：在当前窗口打开一个新的终端标签页。

![](img/5e9029ff83d32df61ed44a3ee635ca5e_12.png)

![](img/5e9029ff83d32df61ed44a3ee635ca5e_14.png)

![](img/5e9029ff83d32df61ed44a3ee635ca5e_16.png)

**修改终端外观：**
您可以根据个人喜好修改终端的颜色方案。例如，在GNOME Terminal中，进入“首选项”->“颜色”，取消勾选“使用系统主题中的颜色”，然后在“内置方案”中选择“白底黑字”即可。修改会立即生效，无需保存按钮。

## 终端间通信
Linux支持在不同终端之间进行简单的通信。这主要通过向特定的终端设备文件（如`/dev/pts/1`）写入信息来实现。

**示例：**
使用`echo`命令向另一个终端发送消息。
```bash
echo "Hello" > /dev/pts/1
```
这条命令会将“Hello”发送到`/dev/pts/1`这个终端上显示。

![](img/5e9029ff83d32df61ed44a3ee635ca5e_18.png)

![](img/5e9029ff83d32df61ed44a3ee635ca5e_19.png)

**实际应用：**
这个功能在过去多人共用一台服务器时比较有用，例如系统管理员可以通过`shutdown`命令向所有登录用户广播系统即将关机的消息。现在，我们更多使用`wall`命令向所有用户广播消息。
```bash
wall “系统将在10分钟后进行维护，请保存好您的工作。”
```

![](img/5e9029ff83d32df61ed44a3ee635ca5e_21.png)

## 认识Shell
Shell，俗称“壳”，是用户与Linux内核进行交互的接口。它接收用户输入的命令，并将其解释后交给内核执行。Shell本身也是一种解释型编程语言，允许用户编写包含循环、分支等逻辑的脚本。

![](img/5e9029ff83d32df61ed44a3ee635ca5e_23.png)

![](img/5e9029ff83d32df61ed44a3ee635ca5e_25.png)

![](img/5e9029ff83d32df61ed44a3ee635ca5e_27.png)

**Shell的工作流程：**
1.  用户输入命令。
2.  Shell接收命令。
3.  如果该命令是Shell**内部命令**，则直接调用系统内核中的功能执行。
4.  如果该命令是**外部命令**（通常是独立的可执行程序），则Shell会在系统的预定路径中查找该命令对应的文件，将其调入内存再执行。

![](img/5e9029ff83d32df61ed44a3ee635ca5e_29.png)

![](img/5e9029ff83d32df61ed44a3ee635ca5e_30.png)

![](img/5e9029ff83d32df61ed44a3ee635ca5e_32.png)

![](img/5e9029ff83d32df61ed44a3ee635ca5e_34.png)

**内部命令与外部命令：**
*   **内部命令**：是Shell程序自身的一部分，在系统启动时就被加载到内存中，执行效率高。例如`pwd`, `cd`。
*   **外部命令**：是独立的可执行文件，通常位于`/bin`, `/usr/bin`等目录下，需要时才从磁盘读取。例如`ls`, `cat`。

可以使用`type`命令来查看一个命令是内部命令还是外部命令。
```bash
type pwd  # 显示 pwd is a shell builtin (shell内建命令)
type ls   # 显示 ls is aliased to `ls --color=auto` (通常是外部命令)
```

通过Shell，我们可以实现对Linux系统的全面管理，包括用户管理、权限管理、磁盘管理、软件管理和网络管理等。

## Shell提示符详解
Shell提示符是命令行界面中等待用户输入命令的那一串字符。它包含了丰富的信息。

一个典型的Shell提示符如下所示：
```
[root@localhost ~]#
```
或者
```
[mk@localhost etc]$
```

**提示符各部分的含义：**
*   `root` / `mk`：当前登录的用户名。
*   `@`：分隔符。
*   `localhost`：主机名。
*   `~` / `etc`：当前所在的目录。`~`代表当前用户的**家目录**。
*   `#` 与 `$`：**命令提示符**。`#`表示当前用户是超级管理员（root）；`$`表示当前用户是普通用户。

![](img/5e9029ff83d32df61ed44a3ee635ca5e_36.png)

## Shell的种类
Linux系统支持多种Shell，常见的包括Bash、Zsh、Ksh等。用户使用哪种Shell，取决于其账户的配置。

![](img/5e9029ff83d32df61ed44a3ee635ca5e_38.png)

**查看系统支持的Shell：**
可以查看`/etc/shells`文件来了解系统安装了哪些Shell。
```bash
cat /etc/shells
```

**查看当前用户使用的Shell：**
每个用户使用的默认Shell记录在`/etc/passwd`文件的最后一个字段。可以使用`head`命令查看文件前几行。
```bash
head -1 /etc/passwd
```
输出类似 `root:x:0:0:root:/root:/bin/bash`，其中最后的`/bin/bash`就表示root用户默认使用Bash Shell。

`head`命令默认显示文件的前10行，`-1`参数表示只显示第一行。关于命令的更多用法，我们将在后续章节详细学习。

![](img/5e9029ff83d32df61ed44a3ee635ca5e_40.png)

## 总结
本节课我们一起学习了Linux终端的基础知识。我们了解了终端的打开方式、常用快捷键和外观设置；知道了不同终端间可以进行简单的通信；深入理解了Shell作为“命令解释器”的角色，及其内部命令与外部命令的区别；学会了解读Shell提示符中用户名、主机名、当前目录和用户权限等信息；最后，还了解了Linux系统中存在多种Shell，并知道如何查看当前使用的Shell类型。这些是开始Linux命令行操作的第一步，请务必熟练掌握。