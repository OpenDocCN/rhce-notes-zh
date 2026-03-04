# 红帽RHCE8认证课程：04-1：man手册-练习指导 📖

![](img/af7e9cfede399023d73b04074d383a16_1.png)

在本节课中，我们将通过一个指导练习来学习如何使用 `man` 手册。`man` 手册是Linux系统中获取命令和配置文件详细信息的重要工具。对于初学者来说，初次使用可能会觉得浏览起来有些繁琐。本节将通过实际操作，帮助你掌握高效查阅 `man` 手册的方法。

![](img/af7e9cfede399023d73b04074d383a16_3.png)

## 环境准备与登录

![](img/af7e9cfede399023d73b04074d383a16_5.png)

上一节我们介绍了 `man` 手册的基本概念，本节中我们来看看如何通过练习来应用它。

![](img/af7e9cfede399023d73b04074d383a16_7.png)

首先，我们需要登录到实验环境。请以 `student` 用户身份通过SSH远程登录到 `workstation` 主机。

![](img/af7e9cfede399023d73b04074d383a16_9.png)

```bash
ssh student@workstation
```

登录后，可以使用 `Ctrl+L` 快捷键或 `clear` 命令来清屏，以获得一个整洁的终端界面。

接下来，执行一个脚本来创建练习所需的初始环境。

![](img/af7e9cfede399023d73b04074d383a16_11.png)

```bash
lab help-menu start
```

![](img/af7e9cfede399023d73b04074d383a16_13.png)

该脚本会验证服务状态、创建文档并更新 `man` 数据库。等待命令执行完成后，即可开始练习。

![](img/af7e9cfede399023d73b04074d383a16_15.png)

![](img/af7e9cfede399023d73b04074d383a16_16.png)

## 练习一：查阅 `gedit` 手册

![](img/af7e9cfede399023d73b04074d383a16_18.png)

![](img/af7e9cfede399023d73b04074d383a16_19.png)

第一个练习是查阅 `gedit` 文本编辑器的 `man` 手册，目的是找到用于在打开文件时将光标定位到文件末尾的选项。

![](img/af7e9cfede399023d73b04074d383a16_21.png)

以下是查阅 `gedit` 手册并寻找特定选项的步骤：

![](img/af7e9cfede399023d73b04074d383a16_23.png)

1.  打开 `gedit` 的 `man` 手册。
    ```bash
    man gedit
    ```
2.  在手册页面内，可以使用 `/` 键进入搜索模式。例如，尝试搜索 `end` 或 `last` 等关键词。
3.  如果直接搜索未找到，则需要手动浏览。仔细阅读描述部分，通常会找到关于命令行选项的说明。
4.  经过查找，会发现 `+` 选项的说明：如果 `+` 后不跟具体行号，光标将定位在文件最后一行。

![](img/af7e9cfede399023d73b04074d383a16_25.png)

![](img/af7e9cfede399023d73b04074d383a16_27.png)

我们可以通过以下命令验证这个选项：
*   `gedit /etc/hosts`：光标在文件开头。
*   `gedit +2 /etc/hosts`：光标在第二行。
*   `gedit + /etc/hosts`：光标在文件最后一行。

![](img/af7e9cfede399023d73b04074d383a16_29.png)

此外，手册中还说明了 `gedit` 可以指定行号和列号，格式为 `+行号:列号`。例如：
```bash
gedit +2:5 /etc/hosts
```
此命令会在打开文件后，将光标定位在第二行第五列的位置。

![](img/af7e9cfede399023d73b04074d383a16_31.png)

查阅完毕后，按 `q` 键即可退出 `man` 手册。

![](img/af7e9cfede399023d73b04074d383a16_33.png)

![](img/af7e9cfede399023d73b04074d383a16_35.png)

## 练习二：查阅 `su` 手册

完成了对 `gedit` 的探索，接下来我们看看 `su` 命令。本练习要求查阅 `su` 手册，理解其默认行为以及使用短划线（`-`）参数时的区别。

![](img/af7e9cfede399023d73b04074d383a16_37.png)

![](img/af7e9cfede399023d73b04074d383a16_38.png)

![](img/af7e9cfede399023d73b04074d383a16_39.png)

查阅 `su` 命令第一章节的手册：
```bash
man 1 su
```

在手册中，需要找到以下两处关键描述：
1.  当 `su` 命令不带任何参数执行时，默认会切换到 `root` 用户，并启动一个交互式 `shell`。
2.  关于短划线（`-`）的说明：使用 `su -` 会启动一个**登录式shell**，并加载目标用户（如root）的环境配置；而不使用短划线（如 `su`）则启动一个**非登录式shell**，继承当前用户的大部分环境变量。

![](img/af7e9cfede399023d73b04074d383a16_41.png)

这两个概念（登录式shell与非登录式shell）的详细区别将在后续课程中讲解。目前，你只需要通过 `man` 手册找到并理解这段说明即可。

![](img/af7e9cfede399023d73b04074d383a16_42.png)

![](img/af7e9cfede399023d73b04074d383a16_43.png)

完成后，同样按 `q` 键退出。

![](img/af7e9cfede399023d73b04074d383a16_45.png)

## 练习三：使用 `whereis` 与 `man -k`

除了直接查阅，我们还可以通过其他命令辅助定位手册。本练习将学习使用 `whereis` 和 `man -k`。

![](img/af7e9cfede399023d73b04074d383a16_47.png)

首先，使用 `whereis` 命令查找 `passwd` 相关文件：
```bash
whereis passwd
```
命令输出会显示：
*   `passwd` 命令的二进制文件路径。
*   `passwd` 命令的 `man` 手册路径（如 `/usr/share/man/man1/passwd.1.gz`）。
*   `passwd` 配置文件的 `man` 手册路径（如 `/usr/share/man/man5/passwd.5.gz`）。

![](img/af7e9cfede399023d73b04074d383a16_49.png)

![](img/af7e9cfede399023d73b04074d383a16_50.png)

![](img/af7e9cfede399023d73b04074d383a16_52.png)

接下来，使用 `man -k`（等同于 `apropos`）命令进行关键字搜索。例如，搜索与 `zip` 存档相关的所有手册：
```bash
man -k zip
```
此命令会列出所有描述信息中包含“zip”关键词的手册页。

![](img/af7e9cfede399023d73b04074d383a16_53.png)

我们再尝试搜索与 `EXT4` 文件系统调整参数相关的工具：
```bash
man -k ext4
```
在列出的结果中，可以找到 `tune2fs` 命令，其描述为“调整ext2/ext3/ext4文件系统参数”。这正是我们需要的工具，随后可以通过 `man tune2fs` 来查看其详细用法。注意，此类系统管理命令的手册通常属于第8章节。

![](img/af7e9cfede399023d73b04074d383a16_55.png)

## 清理实验环境

所有练习完成后，需要执行清理脚本以结束本实验。
```bash
lab help-menu finish
```
执行此命令后，实验环境将被重置。

![](img/af7e9cfede399023d73b04074d383a16_57.png)

![](img/af7e9cfede399023d73b04074d383a16_59.png)

## 总结

![](img/af7e9cfede399023d73b04074d383a16_61.png)

![](img/af7e9cfede399023d73b04074d383a16_63.png)

本节课中我们一起学习了 `man` 手册的实践应用。我们通过三个练习，掌握了如何查阅手册寻找特定命令选项、理解命令行为的细微差别，以及利用 `whereis` 和 `man -k` 快速定位命令和手册。这些技能是高效使用Linux命令行的重要基础。

![](img/af7e9cfede399023d73b04074d383a16_65.png)

![](img/af7e9cfede399023d73b04074d383a16_67.png)

下一节课，我们将介绍另一种帮助文档系统——`info` 文档。