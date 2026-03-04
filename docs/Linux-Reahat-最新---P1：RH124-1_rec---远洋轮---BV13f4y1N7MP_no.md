# Linux Red Hat 最新版 RHCE 7.0 培训视频教程：1：登录、控制台与命令基础 🖥️

![](img/904bdb04add120267ce188fdce60b7d8_1.png)

![](img/904bdb04add120267ce188fdce60b7d8_3.png)

![](img/904bdb04add120267ce188fdce60b7d8_4.png)

![](img/904bdb04add120267ce188fdce60b7d8_6.png)

![](img/904bdb04add120267ce188fdce60b7d8_8.png)

在本节课中，我们将要学习如何登录 Red Hat Enterprise Linux 系统，熟悉图形化界面和命令行控制台的基本操作，并掌握在命令行中执行命令的基础知识。

![](img/904bdb04add120267ce188fdce60b7d8_10.png)

![](img/904bdb04add120267ce188fdce60b7d8_12.png)

## 登录与图形化界面

![](img/904bdb04add120267ce188fdce60b7d8_14.png)

![](img/904bdb04add120267ce188fdce60b7d8_16.png)

![](img/904bdb04add120267ce188fdce60b7d8_17.png)

![](img/904bdb04add120267ce188fdce60b7d8_19.png)

![](img/904bdb04add120267ce188fdce60b7d8_21.png)

首先，我们来熟悉系统的登录界面和控制台。在安装系统时，我们创建了普通用户，这些用户会显示在登录界面上，而 root 用户默认不列出。

![](img/904bdb04add120267ce188fdce60b7d8_23.png)

![](img/904bdb04add120267ce188fdce60b7d8_25.png)

![](img/904bdb04add120267ce188fdce60b7d8_27.png)

![](img/904bdb04add120267ce188fdce60b7d8_29.png)

选择创建的用户（例如 `laoduan`）并点击，然后输入密码即可登录。登录前，可以点击界面上的齿轮图标进行设置，例如选择登录会话类型，默认是传统的 GNOME 桌面。

![](img/904bdb04add120267ce188fdce60b7d8_31.png)

![](img/904bdb04add120267ce188fdce60b7d8_33.png)

![](img/904bdb04add120267ce188fdce60b7d8_35.png)

登录后，我们会看到 GNOME 桌面环境。顶部栏有“应用程序”菜单，可以找到各种软件，例如终端、计算器、浏览器等。屏幕右上角是系统菜单，可以用于注销、关机、更改背景或进入系统设置。

![](img/904bdb04add120267ce188fdce60b7d8_37.png)

![](img/904bdb04add120267ce188fdce60b7d8_38.png)

![](img/904bdb04add120267ce188fdce60b7d8_40.png)

系统设置也可以在“应用程序” -> “系统工具” -> “设置”中打开。在这里，可以调整鼠标速度、电源管理等选项，以优化使用体验。

![](img/904bdb04add120267ce188fdce60b7d8_42.png)

![](img/904bdb04add120267ce188fdce60b7d8_44.png)

![](img/904bdb04add120267ce188fdce60b7d8_45.png)

如果想查看系统资源使用情况，可以打开“系统监视器”。要注销，只需点击右上角的用户名，然后选择“注销”。

![](img/904bdb04add120267ce188fdce60b7d8_46.png)

![](img/904bdb04add120267ce188fdce60b7d8_47.png)

![](img/904bdb04add120267ce188fdce60b7d8_48.png)

![](img/904bdb04add120267ce188fdce60b7d8_50.png)

![](img/904bdb04add120267ce188fdce60b7d8_52.png)

GNOME 桌面提供了两种主要的会话样式：默认的 GNOME Shell 和传统的 GNOME Classic。在登录时通过设置齿轮可以选择。GNOME Shell 将所有工具集中在“活动”概览中，你可以将常用应用添加到左侧的收藏夹栏，方便快速访问。

![](img/904bdb04add120267ce188fdce60b7d8_54.png)

![](img/904bdb04add120267ce188fdce60b7d8_56.png)

## 访问命令行终端

上一节我们介绍了图形化界面的基本操作，本节中我们来看看如何访问和使用命令行。

![](img/904bdb04add120267ce188fdce60b7d8_58.png)

在 Linux 中，许多操作需要在命令行中完成。要打开命令行终端，可以点击“活动”，然后搜索或直接点击“终端”图标。这类似于 Windows 系统中的命令提示符（CMD）。

![](img/904bdb04add120267ce188fdce60b7d8_59.png)

![](img/904bdb04add120267ce188fdce60b7d8_61.png)

![](img/904bdb04add120267ce188fdce60b7d8_63.png)

打开终端后，我们就可以在其中输入命令。如果觉得终端字体太小，可以通过菜单栏的“编辑” -> “首选项”来调整字体大小。

**Shell** 是 Linux 系统内置的一个子系统，它负责将用户输入的命令解析成系统能够执行的机器语言。我们无法直接“看到”Shell，它作为一个进程运行在后台。终端（如 GNOME Terminal）是一个**工具**，它为用户提供了一个连接并操作 Shell 的界面。

![](img/904bdb04add120267ce188fdce60b7d8_65.png)

![](img/904bdb04add120267ce188fdce60b7d8_66.png)

![](img/904bdb04add120267ce188fdce60b7d8_68.png)

![](img/904bdb04add120267ce188fdce60b7d8_70.png)

除了本机终端，我们也可以使用远程连接工具（如 PuTTY、Xshell）通过 SSH 协议连接到另一台 Linux 服务器。

![](img/904bdb04add120267ce188fdce60b7d8_72.png)

![](img/904bdb04add120267ce188fdce60b7d8_74.png)

## 执行简单命令

现在我们已经打开了终端，接下来学习如何执行命令。

命令的基本格式是：`命令 [选项] [参数]`。
*   **命令**：要执行的操作。
*   **选项**：通常以 `-` 或 `--` 开头，用于修改命令的行为。多个单字母选项可以合并，例如 `-la`。
*   **参数**：命令作用的对象，如文件名或目录名。

以下是几个简单命令的示例：
*   `pwd`：显示当前工作目录。
*   `date`：显示当前日期和时间。
*   `whoami`：显示当前登录的用户名。
*   `ls`：列出当前目录的内容。
    *   `ls -l`：以长格式列出详细信息。
    *   `ls -a`：列出所有文件，包括隐藏文件（以 `.` 开头的文件）。
    *   `ls -lh`：以人类可读的格式（如 K, M, G）显示文件大小。
    *   `ls -lha /home`：组合选项并指定 `/home` 目录作为参数。
*   `bc`：打开一个计算器。输入 `scale=3` 可以设置小数点后保留3位，输入 `quit` 退出。
*   `clear` 或 `Ctrl + l`：清空终端屏幕。

![](img/904bdb04add120267ce188fdce60b7d8_76.png)

![](img/904bdb04add120267ce188fdce60b7d8_78.png)

如果选项由多个字母作为一个整体（如 `--help`），则需要使用两个短横线 `--`。`--help` 选项可以显示命令的帮助信息。

## Shell 快捷键与技巧

Bash 是 Red Hat Linux 默认的 Shell，它提供了许多快捷键和功能来提高命令输入效率。

以下是 Bash 中一些常用的快捷键和技巧：

**命令补全与历史**
*   **Tab 键**：自动补全命令或路径。按一次尝试补全，按两次显示所有可能的选项。
*   **上下箭头键**：浏览之前执行过的命令历史。
*   **Ctrl + r**：反向搜索历史命令。输入关键词，它会动态匹配并显示最近的包含该词的命令。
*   **!!**：执行上一条命令。
*   **!字符串**：执行历史中最近一条以该字符串开头的命令。

**命令行编辑**
*   **Ctrl + a**：将光标移动到行首。
*   **Ctrl + e**：将光标移动到行尾。
*   **Ctrl + u**：删除从光标处到行首的所有内容。
*   **Ctrl + k**：删除从光标处到行尾的所有内容。
*   **Ctrl + 左/右箭头**：以单词为单位移动光标。
*   **Esc + .**：快速插入上一条命令的最后一个参数。

![](img/904bdb04add120267ce188fdce60b7d8_80.png)

**终端标签页与复制粘贴**
*   **Ctrl + Shift + t**：在当前终端窗口中打开一个新标签页。
*   **Ctrl + PageUp/PageDown**：在标签页之间切换。
*   **Ctrl + d**：关闭当前标签页。
*   **复制粘贴**：选中文本后，直接点击鼠标**中键（滚轮）** 即可粘贴，无需右键菜单。

## 控制台切换

![](img/904bdb04add120267ce188fdce60b7d8_82.png)

![](img/904bdb04add120267ce188fdce60b7d8_83.png)

![](img/904bdb04add120267ce188fdce60b7d8_85.png)

![](img/904bdb04add120267ce188fdce60b7d8_87.png)

访问命令行除了在图形界面中打开终端，还可以切换到纯字符界面的控制台。

![](img/904bdb04add120267ce188fdce60b7d8_89.png)

![](img/904bdb04add120267ce188fdce60b7d8_91.png)

![](img/904bdb04add120267ce188fdce60b7d8_93.png)

系统默认提供了多个虚拟控制台（tty）。通常，`tty1` 是图形界面，`tty2` 到 `tty6` 是字符界面。可以使用以下快捷键进行切换：
*   **Ctrl + Alt + F2** 到 **F6**：切换到对应的字符控制台。
*   **Ctrl + Alt + F1**：切换回图形界面控制台。

![](img/904bdb04add120267ce188fdce60b7d8_94.png)

![](img/904bdb04add120267ce188fdce60b7d8_96.png)

![](img/904bdb04add120267ce188fdce60b7d8_98.png)

![](img/904bdb04add120267ce188fdce60b7d8_100.png)

在字符控制台中，需要输入用户名和密码登录，然后就可以执行命令。

![](img/904bdb04add120267ce188fdce60b7d8_102.png)

![](img/904bdb04add120267ce188fdce60b7d8_104.png)

![](img/904bdb04add120267ce188fdce60b7d8_106.png)

![](img/904bdb04add120267ce188fdce60b7d8_108.png)

![](img/904bdb04add120267ce188fdce60b7d8_109.png)

![](img/904bdb04add120267ce188fdce60b7d8_111.png)

## 虚拟机工具（补充）

![](img/904bdb04add120267ce188fdce60b7d8_113.png)

![](img/904bdb04add120267ce188fdce60b7d8_115.png)

![](img/904bdb04add120267ce188fdce60b7d8_117.png)

![](img/904bdb04add120267ce188fdce60b7d8_119.png)

![](img/904bdb04add120267ce188fdce60b7d8_121.png)

对于在虚拟机中安装的系统，通常需要安装虚拟机增强工具（如 VMware Tools 或 VirtualBox Guest Additions）来获得更好的集成体验，例如共享剪贴板、自适应分辨率等。不过，在某些最新的虚拟环境和系统版本中，这些功能可能已默认启用或驱动完善，无需额外安装。

![](img/904bdb04add120267ce188fdce60b7d8_123.png)

![](img/904bdb04add120267ce188fdce60b7d8_125.png)

本节课中我们一起学习了 Red Hat Linux 7.0 的基本登录流程、图形化桌面环境（GNOME）的简单操作、命令行终端的访问方法、基础命令的格式与使用，以及 Bash Shell 中能极大提升效率的快捷键和技巧。掌握这些内容是后续深入学习 Linux 系统管理的基础。