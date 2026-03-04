# 红帽RHCE8认证课程：02-3：bash执行命令1 📝

![](img/ce978e9c799ba10ba929abf8c81221b8_1.png)

在本节课中，我们将学习如何在Bash命令行中执行命令，并详细介绍一些最常用的基础命令，包括 `date`、`passwd`、`file`、`cat`、`less`、`head`、`tail` 和 `wc`。这些命令是日常Linux系统操作的基础。

![](img/ce978e9c799ba10ba929abf8c81221b8_3.png)

上一节我们介绍了Bash命令行界面，本节中我们来看看如何具体执行命令以及这些常用命令的用法。

![](img/ce978e9c799ba10ba929abf8c81221b8_5.png)

![](img/ce978e9c799ba10ba929abf8c81221b8_7.png)

## 执行多个命令

![](img/ce978e9c799ba10ba929abf8c81221b8_9.png)

在Bash中，执行命令的基本方法是输入命令字符串后按回车。如果需要连续执行多个命令，可以使用分号 `;` 将它们分隔开。

以下是执行多个命令的示例：
```bash
ls; pwd
```
这个命令会先执行 `ls` 列出当前目录内容，然后执行 `pwd` 显示当前工作目录路径。命令会严格按照分号分隔的顺序执行。

我们还可以执行更多命令：
```bash
hostname; pwd; ls
```
这个命令会依次显示主机名、当前路径，然后列出目录内容。

## 查看和设置时间：`date` 命令

`date` 命令用于显示或设置系统日期和时间。要了解其详细用法，可以使用 `date --help` 查看帮助信息。

直接执行 `date` 会显示当前系统时间。`date` 命令支持丰富的选项和格式控制。

![](img/ce978e9c799ba10ba929abf8c81221b8_11.png)

![](img/ce978e9c799ba10ba929abf8c81221b8_12.png)

![](img/ce978e9c799ba10ba929abf8c81221b8_14.png)

![](img/ce978e9c799ba10ba929abf8c81221b8_16.png)

例如，使用 `-d` 选项可以根据描述的字符串显示时间：
```bash
date -d ‘+3 days‘
```
这条命令会显示三天后的日期和时间。我们也可以计算三个月后或三分钟后的时间。

![](img/ce978e9c799ba10ba929abf8c81221b8_18.png)

![](img/ce978e9c799ba10ba929abf8c81221b8_19.png)

`date` 命令还支持使用 `+` 和格式字符串来自定义输出。格式字符串以百分号 `%` 开头。

以下是自定义输出格式的示例：
```bash
date ‘+%a‘    # 显示简写的星期几，如 Thu
date ‘+%d‘    # 显示月份中的第几天
date ‘+%X‘    # 以本地格式显示日期，如 02/06/2020
```
通过组合不同的格式符，可以灵活地输出所需的时间信息。

## 显示主机名：`hostname` 命令

![](img/ce978e9c799ba10ba929abf8c81221b8_21.png)

![](img/ce978e9c799ba10ba929abf8c81221b8_23.png)

`hostname` 命令非常简单，用于显示当前系统的主机名。
```bash
hostname
```
执行后会直接输出主机名称。

## 管理用户密码：`passwd` 命令

`passwd` 命令用于修改用户密码。普通用户只能修改自己的密码。

如果以管理员（root）身份运行，则可以修改其他用户的密码，并且可以不受密码策略的严格限制（例如设置短密码）。
```bash
passwd root  # 修改root用户的密码（需要root权限）
```
关于系统的密码策略配置，属于更高级的内容，我们将在后续课程中介绍。

## 判断文件类型：`file` 命令

![](img/ce978e9c799ba10ba929abf8c81221b8_25.png)

`file` 命令用于判断指定文件的类型。其基本用法是 `file [文件名]`。

![](img/ce978e9c799ba10ba929abf8c81221b8_27.png)

![](img/ce978e9c799ba10ba929abf8c81221b8_29.png)

以下是判断文件类型的示例：
```bash
file /etc/passwd      # 显示为 ASCII text，即文本文件
file /bin/bash        # 显示为 ELF可执行文件，即二进制程序
file /bin             # 显示为 directory，即目录
file /dev/vda         # 显示为 block special，即块设备（如硬盘）
```
在Linux中，许多系统资源（如设备、目录）都被抽象为“文件”，`file` 命令帮助我们理解这些“文件”的具体类型。

![](img/ce978e9c799ba10ba929abf8c81221b8_31.png)

![](img/ce978e9c799ba10ba929abf8c81221b8_32.png)

## 查看文件内容：`cat` 命令

![](img/ce978e9c799ba10ba929abf8c81221b8_34.png)

`cat` 命令用于查看整个文件的内容。它适合查看内容较少的文件。

直接使用 `cat` 会输出文件的全部内容。它还有一些有用的选项：

以下是 `cat` 命令的常用选项示例：
```bash
cat /etc/hosts                # 查看文件全部内容
cat -n /etc/hosts             # 查看内容并显示行号（包括空行）
cat -b /etc/hosts             # 查看内容并显示行号（忽略空行）
cat -E /etc/hosts             # 在每行末尾显示 $ 符号
```
可以组合使用这些选项，例如 `cat -bE /etc/hosts`。

![](img/ce978e9c799ba10ba929abf8c81221b8_36.png)

## 分页查看文件内容：`less` 和 `more` 命令

![](img/ce978e9c799ba10ba929abf8c81221b8_38.png)

当文件内容很长时，使用 `cat` 会瞬间刷屏，不便于阅读。这时可以使用 `less` 或 `more` 命令进行分页查看。

`less` 命令功能强大，允许向前后翻页：
```bash
less /etc/passwd
```
在 `less` 的浏览界面中，可以按 **空格键** 向下翻一页，按 **上下方向键** 逐行移动，按 **q** 键退出。

`more` 命令与 `less` 类似，但功能稍少一些，通常只能向下翻页。

![](img/ce978e9c799ba10ba929abf8c81221b8_40.png)

![](img/ce978e9c799ba10ba929abf8c81221b8_41.png)

![](img/ce978e9c799ba10ba929abf8c81221b8_43.png)

## 查看文件头部或尾部：`head` 和 `tail` 命令

![](img/ce978e9c799ba10ba929abf8c81221b8_45.png)

有时我们只关心文件的开头或结尾部分，这时 `head` 和 `tail` 命令就非常有用。

`head` 命令默认显示文件的前10行：

![](img/ce978e9c799ba10ba929abf8c81221b8_47.png)

以下是 `head` 和 `tail` 命令的用法示例：
```bash
head /etc/passwd               # 查看文件前10行
head -n 5 /etc/passwd          # 查看文件前5行
head -n -3 /etc/passwd         # 查看文件，直到倒数第3行之前的所有行
```

![](img/ce978e9c799ba10ba929abf8c81221b8_49.png)

`tail` 命令默认显示文件的末尾10行：
```bash
tail /etc/passwd               # 查看文件末尾10行
tail -n 5 /etc/passwd          # 查看文件末尾5行
tail -n +2 /etc/passwd         # 查看从第2行开始到文件末尾的所有行
```
`head` 和 `tail` 是日常排查日志、查看长文件输出的利器。

![](img/ce978e9c799ba10ba929abf8c81221b8_51.png)

## 统计文件信息：`wc` 命令

`wc` 命令用于统计文件中的行数、单词数和字节数。

![](img/ce978e9c799ba10ba929abf8c81221b8_53.png)

直接使用 `wc` 会输出三个数字，分别代表行数、单词数、字节数，最后是文件名：
```bash
wc /etc/passwd
```
输出可能类似 `30 66 1563 /etc/passwd`，表示该文件有30行、66个单词、1563个字节。

![](img/ce978e9c799ba10ba929abf8c81221b8_55.png)

可以使用选项单独查看某一项统计：

![](img/ce978e9c799ba10ba929abf8c81221b8_57.png)

以下是 `wc` 命令的选项示例：
```bash
wc -l /etc/passwd      # 只统计行数
wc -w /etc/passwd      # 只统计单词数
wc -c /etc/passwd      # 只统计字节数
```
也可以同时统计多个文件，`wc` 会分别列出每个文件的统计信息，并给出总计。

![](img/ce978e9c799ba10ba929abf8c81221b8_59.png)

![](img/ce978e9c799ba10ba929abf8c81221b8_61.png)

---

![](img/ce978e9c799ba10ba929abf8c81221b8_63.png)

![](img/ce978e9c799ba10ba929abf8c81221b8_65.png)

本节课中我们一起学习了在Bash中执行多个命令的方法，并详细介绍了 `date`、`hostname`、`passwd`、`file`、`cat`、`less`、`head`、`tail` 和 `wc` 这些基础且重要的命令。它们是构建更复杂Linux操作技能的基石，请务必多加练习，熟悉其用法。下节课我们将继续探索Bash的其他功能。