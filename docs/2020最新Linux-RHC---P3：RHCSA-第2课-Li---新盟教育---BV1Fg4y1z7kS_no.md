# Linux-RHCSA入门实战课：P3：RHCSA-第2课-Linux常用基础命令 📚

![](img/b4a4824b32700ab1c446eef814f5d7d6_1.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_2.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_4.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_6.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_8.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_10.png)

在本节课中，我们将学习Linux系统中最常用的一些基础命令。掌握这些命令是进行系统管理和后续学习的基础。课程将涵盖终端的使用、命令的基本语法以及一系列用于文件操作、系统查看和管理的核心命令。

---

![](img/b4a4824b32700ab1c446eef814f5d7d6_12.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_14.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_15.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_17.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_19.png)

## 终端与Shell 🖥️

上一节我们介绍了Linux系统的安装和基本概念，本节中我们来看看如何与系统进行交互。Linux主要通过终端（Terminal）来接收用户命令。

![](img/b4a4824b32700ab1c446eef814f5d7d6_21.png)

Linux主要有两种终端模拟器：
*   **GNOME Terminal**： 图形桌面环境下的终端，消耗CPU较多，内存较少。
*   **KDE Konsole**： 类似于Windows的开始菜单，在Ubuntu等发行版中更常见。

在生产环境中，我们通常通过远程连接工具（如Xshell、CRT、PuTTY）来管理服务器。连接后，可以通过 `tty` 命令查看当前终端设备号（如 `/dev/pts/0`）。

![](img/b4a4824b32700ab1c446eef814f5d7d6_23.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_25.png)

**多终端操作**：
*   使用 `Ctrl+Shift+N` 可以快速打开新的终端窗口。
*   不同终端间可以相互通信。例如，向终端0发送消息：`echo "hello" > /dev/pts/0`。
*   使用 `w` 命令可以查看当前在线的用户。
*   使用 `wall` 命令可以向所有终端广播消息。

![](img/b4a4824b32700ab1c446eef814f5d7d6_27.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_29.png)

---

![](img/b4a4824b32700ab1c446eef814f5d7d6_31.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_33.png)

## Shell提示符与用户切换 🔄

![](img/b4a4824b32700ab1c446eef814f5d7d6_35.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_36.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_37.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_39.png)

Shell是用户与Linux内核之间的接口，负责解释和执行命令。命令分为内部命令（常驻内存，执行快）和外部命令（存储在硬盘上的程序）。可以使用 `type` 命令来区分，例如 `type cd` 会显示 `cd is a shell builtin`。

![](img/b4a4824b32700ab1c446eef814f5d7d6_41.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_43.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_45.png)

Shell提示符提供了重要信息：
*   `#`： 表示当前用户是超级管理员root。
*   `$`： 表示当前用户是普通用户。

![](img/b4a4824b32700ab1c446eef814f5d7d6_47.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_49.png)

使用 `su` 命令可以切换用户：
*   `su - username`： 切换用户并同时切换到该用户的家目录。
*   `su username`： 仅切换用户身份，但工作目录不变。

可以使用 `pwd` 命令查看当前所在目录。root用户的家目录是 `/root`，普通用户的家目录通常在 `/home/` 下。

![](img/b4a4824b32700ab1c446eef814f5d7d6_51.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_53.png)

---

![](img/b4a4824b32700ab1c446eef814f5d7d6_55.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_57.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_59.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_61.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_62.png)

## 命令基础语法与帮助 📖

![](img/b4a4824b32700ab1c446eef814f5d7d6_64.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_66.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_68.png)

Linux命令的基本格式是：**命令 [参数] [对象]**。
*   **命令**： 要执行的操作。
*   **参数**： 用于调整命令行为。分为长格式（如 `--help`）和短格式（如 `-h`）。
*   **对象**： 命令操作的目标，如文件、目录或用户。

![](img/b4a4824b32700ab1c446eef814f5d7d6_70.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_71.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_73.png)

**获取命令帮助**：
最常用的帮助命令是 `man`（manual的缩写）。例如，`man ls` 会显示ls命令的详细手册。
在man页面中：
*   按 `q` 键退出。
*   按 `/` 键后输入关键词，可以向下搜索。
*   按 `?` 键后输入关键词，可以向上搜索。
*   按 `n` 键查找下一个匹配项，按 `N` 键查找上一个。

![](img/b4a4824b32700ab1c446eef814f5d7d6_75.png)

---

![](img/b4a4824b32700ab1c446eef814f5d7d6_77.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_79.png)

## 常用基础命令详解 🛠️

![](img/b4a4824b32700ab1c446eef814f5d7d6_81.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_83.png)

以下是Linux系统管理中最核心的一系列命令，我们将逐一进行学习。

![](img/b4a4824b32700ab1c446eef814f5d7d6_85.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_87.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_89.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_91.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_93.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_94.png)

### 1. echo命令
`echo` 命令用于在终端输出字符串或变量的值。
*   基本用法：`echo "Hello World"`
*   输出变量：`echo $PATH` （`$`符号用于引用变量）
*   定义变量：`A=10` （注意等号两边不能有空格）
*   **重定向功能**：
    *   `echo "content" > file`： 将内容覆盖写入文件。
    *   `echo "content" >> file`： 将内容追加到文件末尾。

![](img/b4a4824b32700ab1c446eef814f5d7d6_96.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_98.png)

### 2. ls命令
`ls` 命令用于列出目录内容。
*   `ls`： 简单列出当前目录下的文件和目录名。
*   `ls -l`： 以长格式列出详细信息（权限、所有者、大小、时间等）。
*   `ls -a`： 列出所有文件，包括以 `.` 开头的隐藏文件。
*   `ll`： 通常是 `ls -l --color=auto` 的别名，同样显示详细信息。
*   `.` 代表当前目录，`..` 代表上级目录。

![](img/b4a4824b32700ab1c446eef814f5d7d6_100.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_101.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_103.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_105.png)

**命令别名**：
可以使用 `alias` 命令查看或设置命令别名。例如，`alias` 会显示 `ll='ls -l --color=auto'`。使用 `unalias ll` 可以取消这个别名。

![](img/b4a4824b32700ab1c446eef814f5d7d6_107.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_109.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_111.png)

### 3. cd命令
`cd` 命令用于切换当前工作目录。
*   `cd /path/to/dir`： 切换到绝对路径。
*   `cd ..`： 切换到上级目录。
*   `cd ~` 或 `cd`： 切换到当前用户的家目录。
*   `cd -`： 切换到上一次所在的目录。

![](img/b4a4824b32700ab1c446eef814f5d7d6_113.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_115.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_116.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_118.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_119.png)

### 4. history命令
`history` 命令用于查看和管理之前执行过的命令历史。
*   `history`： 显示命令历史列表。
*   `history -c`： 清空当前会话的历史记录。
*   `history -d 行号`： 删除历史记录中指定行号的命令。
*   `!行号`： 重新执行历史记录中对应行号的命令。
*   `!!`： 执行上一条命令。
*   `!字符串`： 执行最近一条以该字符串开头的命令。

![](img/b4a4824b32700ab1c446eef814f5d7d6_121.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_123.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_125.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_127.png)

**环境变量控制**：
在 `/etc/profile` 或 `~/.bashrc` 中设置 `HISTCONTROL=ignorespace`，可以让以空格开头的命令不被记录到历史中。

![](img/b4a4824b32700ab1c446eef814f5d7d6_129.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_131.png)

### 5. 实用快捷键 ⌨️
掌握快捷键能极大提高操作效率。
*   `Ctrl + C`： 终止当前正在前台运行的命令。
*   `Ctrl + D`： 退出当前终端会话或结束输入。
*   `Ctrl + L` 或 `clear`： 清屏。
*   `Ctrl + R`： 反向搜索历史命令。
*   `Ctrl + A`： 将光标移动到行首。
*   `Ctrl + E`： 将光标移动到行尾。
*   `Ctrl + W`： 删除光标前的一个单词（参数）。

![](img/b4a4824b32700ab1c446eef814f5d7d6_133.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_135.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_137.png)

### 6. 日期与时间命令 ⏰
Linux有两种时间：硬件时间（BIOS时间）和系统时间。
*   `hwclock`： 查看或设置硬件时间。
*   `date`： 查看或设置系统时间。
*   设置时区（如中国上海）：`cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime`
*   `date +%F`： 以 `YYYY-MM-DD` 格式显示日期。
*   `date -s "YYYY-MM-DD HH:MM:SS"`： 设置系统时间。

![](img/b4a4824b32700ab1c446eef814f5d7d6_139.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_140.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_142.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_144.png)

### 7. 关机和重启命令 ⚡
*   `reboot`： 重启系统。
*   `poweroff`： 关机。
*   `shutdown`： 更灵活的关机命令。
    *   `shutdown -h now`： 立即关机。
    *   `shutdown -r +10`： 10分钟后重启。
    *   `shutdown -h 20:00`： 在20:00定时关机。

![](img/b4a4824b32700ab1c446eef814f5d7d6_146.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_148.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_150.png)

**设置默认运行级别**：
在RHEL7/CentOS7及以上版本，使用 `systemctl set-default multi-user.target` 设置默认字符界面，使用 `systemctl set-default graphical.target` 设置默认图形界面。

![](img/b4a4824b32700ab1c446eef814f5d7d6_152.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_154.png)

### 8. 目录与文件操作命令 📂
以下是管理文件和目录的核心命令。

![](img/b4a4824b32700ab1c446eef814f5d7d6_156.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_158.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_160.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_162.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_164.png)

**查看路径与创建目录**：
*   `pwd`： 显示当前工作目录的绝对路径。
*   `mkdir`： 创建新目录。
    *   `mkdir dir1`： 创建单个目录。
    *   `mkdir -p a/b/c/d`： 递归创建多层目录。

![](img/b4a4824b32700ab1c446eef814f5d7d6_166.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_168.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_170.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_172.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_174.png)

**复制、移动与重命名**：
*   `cp`： 复制文件或目录。
    *   `cp file1 file2`： 复制文件。
    *   `cp -r dir1 dir2`： 递归复制目录。
    *   `cp -a source dest`： 归档复制，保留所有属性。
*   `mv`： 移动或重命名文件/目录。
    *   `mv oldname newname`： 重命名。
    *   `mv file /path/to/dest/`： 移动文件。

![](img/b4a4824b32700ab1c446eef814f5d7d6_176.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_178.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_180.png)

**删除文件与目录**：
*   `rm`： 删除文件或目录。
    *   `rm file`： 删除文件。
    *   `rm -r dir`： 递归删除目录及其内容。
    *   `rm -f file`： 强制删除，不提示。
    *   **警告**：`rm -rf /` 命令会递归强制删除根目录所有文件，导致系统毁灭，切勿尝试！

![](img/b4a4824b32700ab1c446eef814f5d7d6_182.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_184.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_186.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_188.png)

**创建文件**：
*   `touch`： 创建空文件或更新文件时间戳。
    *   `touch file1 file2`： 创建多个文件。
    *   `touch file{1..10}.txt`： 使用大括号扩展创建file1.txt到file10.txt。

![](img/b4a4824b32700ab1c446eef814f5d7d6_190.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_192.png)

### 9. 文件查看命令 👀
根据文件大小和查看需求，选择不同的命令。

![](img/b4a4824b32700ab1c446eef814f5d7d6_194.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_196.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_197.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_198.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_200.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_202.png)

**查看文件内容**：
*   `cat`： 查看内容较少的纯文本文件，会一次性显示全部内容。
*   `more`： 分页查看长文件，只能向下翻页。
*   `less`： 分页查看长文件，功能更强，可以上下翻页，支持搜索（用法类似 `vi`）。
*   `head`： 查看文件头部内容，默认显示前10行。`head -n 20 file` 显示前20行。
*   `tail`： 查看文件尾部内容，默认显示后10行。
    *   `tail -n 5 file`： 显示最后5行。
    *   `tail -f file`： **动态跟踪**文件末尾内容的变化，常用于实时查看日志，按 `Ctrl+C` 退出。

![](img/b4a4824b32700ab1c446eef814f5d7d6_204.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_206.png)

---

![](img/b4a4824b32700ab1c446eef814f5d7d6_208.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_210.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_212.png)

## 总结 🎯

![](img/b4a4824b32700ab1c446eef814f5d7d6_213.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_215.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_217.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_219.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_221.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_222.png)

本节课中我们一起学习了Linux系统管理的基石——常用基础命令。我们从了解终端和Shell开始，学习了命令的基本格式和获取帮助的方法。然后，我们深入探讨了包括 `echo`, `ls`, `cd`, `history`, `date`, 关机重启以及大量文件目录操作命令在内的核心工具。

![](img/b4a4824b32700ab1c446eef814f5d7d6_224.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_226.png)

![](img/b4a4824b32700ab1c446eef814f5d7d6_228.png)

请务必在实验环境中多加练习，熟悉这些命令的常用参数和组合用法。它们是您日后进行系统配置、故障排查和自动化脚本编写的基础。下节课我们将继续学习更多高级命令和系统管理知识。