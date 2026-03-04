# Linux入门：第九章：了解bash shell

![](img/c62e484dd441be0a03917b772ca7b565_1.png)

![](img/c62e484dd441be0a03917b772ca7b565_2.png)

![](img/c62e484dd441be0a03917b772ca7b565_4.png)

![](img/c62e484dd441be0a03917b772ca7b565_6.png)

在本章中，我们将学习什么是shell，特别是bash shell。我们将了解shell的作用、它支持的通配符、命令扩展符以及变量的基本概念和使用方法。这些知识是后续学习shell脚本的基础。

## 什么是Shell？

![](img/c62e484dd441be0a03917b772ca7b565_8.png)

上一节我们介绍了本章的学习目标，本节中我们来看看什么是shell。

![](img/c62e484dd441be0a03917b772ca7b565_10.png)

Shell是一种命令解释器。它能够识别用户输入的各种命令，并将这些命令传递给操作系统执行。例如，当您输入 `ls` 命令时，shell会解释这个命令，并告诉内核去列出当前目录的内容。它的作用类似于Windows系统中的命令行界面（如PowerShell）。Shell是用户与操作系统内核交互的界面，也是一种脚本语言。

![](img/c62e484dd441be0a03917b772ca7b565_12.png)

![](img/c62e484dd441be0a03917b772ca7b565_14.png)

![](img/c62e484dd441be0a03917b772ca7b565_16.png)

![](img/c62e484dd441be0a03917b772ca7b565_18.png)

![](img/c62e484dd441be0a03917b772ca7b565_19.png)

![](img/c62e484dd441be0a03917b772ca7b565_21.png)

![](img/c62e484dd441be0a03917b772ca7b565_22.png)

![](img/c62e484dd441be0a03917b772ca7b565_24.png)

为了更好地理解，我们可以将操作系统分为几个层次：
*   **硬件**：计算机的物理组件。
*   **内核（Kernel）**：直接控制硬件的核心程序。
*   **Shell**：位于用户和内核之间，充当翻译官和桥梁。
*   **用户/应用程序**：发出命令的实体。

![](img/c62e484dd441be0a03917b772ca7b565_26.png)

![](img/c62e484dd441be0a03917b772ca7b565_27.png)

![](img/c62e484dd441be0a03917b772ca7b565_29.png)

![](img/c62e484dd441be0a03917b772ca7b565_31.png)

![](img/c62e484dd441be0a03917b772ca7b565_33.png)

![](img/c62e484dd441be0a03917b772ca7b565_35.png)

当用户在shell中输入命令（如 `ls`）后，shell会解释该命令，然后将解释结果传递给内核。内核再调度硬件去执行具体的操作（如读取磁盘目录）。因此，shell是我们与计算机硬件沟通的中间人。

![](img/c62e484dd441be0a03917b772ca7b565_37.png)

![](img/c62e484dd441be0a03917b772ca7b565_38.png)

![](img/c62e484dd441be0a03917b772ca7b565_40.png)

![](img/c62e484dd441be0a03917b772ca7b565_42.png)

![](img/c62e484dd441be0a03917b772ca7b565_44.png)

Linux系统中有多种shell，例如 `sh`、`bash`、`ksh`、`csh`。在Red Hat Enterprise Linux 8中，默认使用的是 `bash`（Bourne Again SHell）。`bash` 兼容 `sh`，并提供了更多便捷功能，如命令历史、Tab键补全等。

![](img/c62e484dd441be0a03917b772ca7b565_46.png)

![](img/c62e484dd441be0a03917b772ca7b565_48.png)

![](img/c62e484dd441be0a03917b772ca7b565_50.png)

![](img/c62e484dd441be0a03917b772ca7b565_52.png)

![](img/c62e484dd441be0a03917b772ca7b565_53.png)

![](img/c62e484dd441be0a03917b772ca7b565_55.png)

![](img/c62e484dd441be0a03917b772ca7b565_57.png)

![](img/c62e484dd441be0a03917b772ca7b565_58.png)

![](img/c62e484dd441be0a03917b772ca7b565_59.png)

![](img/c62e484dd441be0a03917b772ca7b565_61.png)

![](img/c62e484dd441be0a03917b772ca7b565_63.png)

您可以通过查看 `/etc/shells` 文件来了解系统支持哪些shell。用户的默认登录shell定义在 `/etc/passwd` 文件的最后一列。例如，将用户的shell改为 `/bin/bash`，该用户登录后就会使用bash shell。

![](img/c62e484dd441be0a03917b772ca7b565_65.png)

![](img/c62e484dd441be0a03917b772ca7b565_67.png)

![](img/c62e484dd441be0a03917b772ca7b565_69.png)

## Bash Shell功能回顾

![](img/c62e484dd441be0a03917b772ca7b565_71.png)

![](img/c62e484dd441be0a03917b772ca7b565_73.png)

![](img/c62e484dd441be0a03917b772ca7b565_75.png)

在深入了解新功能之前，我们先回顾一下bash shell已经提供的一些实用功能。

![](img/c62e484dd441be0a03917b772ca7b565_77.png)

以下是bash shell中常用的功能：
*   **命令历史（history）**：使用 `history` 命令查看之前执行过的命令。使用上下箭头键可以翻看历史命令。
*   **清空历史记录**：使用 `history -c` 清除当前shell内存中的历史记录。若要永久清除，还需要清空 `~/.bash_history` 文件。
*   **写入历史记录**：使用 `history -w` 将内存中的历史记录立即写入 `~/.bash_history` 文件。
*   **调用历史命令**：使用 `!` 加命令编号可以快速执行历史中的某条命令。
*   **搜索历史命令**：按 `Ctrl+R` 可以反向搜索历史命令。
*   **命令补全**：按 `Tab` 键可以自动补全命令、文件名或路径。
*   **输入输出重定向**：使用 `>`、`>>`、`<` 等符号来重定向命令的输入和输出。
*   **管道（Pipe）**：使用 `|` 符号将一个命令的输出作为另一个命令的输入。

![](img/c62e484dd441be0a03917b772ca7b565_79.png)

![](img/c62e484dd441be0a03917b772ca7b565_81.png)

![](img/c62e484dd441be0a03917b772ca7b565_83.png)

![](img/c62e484dd441be0a03917b772ca7b565_85.png)

![](img/c62e484dd441be0a03917b772ca7b565_87.png)

![](img/c62e484dd441be0a03917b772ca7b565_88.png)

## Shell快捷键

![](img/c62e484dd441be0a03917b772ca7b565_90.png)

![](img/c62e484dd441be0a03917b772ca7b565_91.png)

熟练使用快捷键可以极大提高在命令行下的工作效率。

![](img/c62e484dd441be0a03917b772ca7b565_93.png)

![](img/c62e484dd441be0a03917b772ca7b565_94.png)

![](img/c62e484dd441be0a03917b772ca7b565_96.png)

![](img/c62e484dd441be0a03917b772ca7b565_98.png)

![](img/c62e484dd441be0a03917b772ca7b565_99.png)

以下是bash shell中一些重要的快捷键：
*   `Ctrl + C`：中断当前正在运行的程序或命令。
*   `Ctrl + L`：清屏，效果等同于 `clear` 命令。
*   `Ctrl + R`：反向搜索历史命令。
*   `Ctrl + D`：退出当前终端或结束输入（EOF）。
*   `Ctrl + A`：将光标移动到命令行的开头。
*   `Ctrl + E`：将光标移动到命令行的末尾。
*   `Ctrl + U`：剪切从光标处到行首的所有字符。
*   `Ctrl + K`：剪切从光标处到行尾的所有字符。
*   `Ctrl + Y`：粘贴之前剪切（`Ctrl+U` 或 `Ctrl+K`）的内容。

![](img/c62e484dd441be0a03917b772ca7b565_101.png)

![](img/c62e484dd441be0a03917b772ca7b565_103.png)

![](img/c62e484dd441be0a03917b772ca7b565_105.png)

![](img/c62e484dd441be0a03917b772ca7b565_107.png)

## 通配符

![](img/c62e484dd441be0a03917b772ca7b565_109.png)

![](img/c62e484dd441be0a03917b772ca7b565_111.png)

![](img/c62e484dd441be0a03917b772ca7b565_113.png)

![](img/c62e484dd441be0a03917b772ca7b565_115.png)

通配符主要用于匹配文件名，是shell提供的一个强大功能。请注意，通配符用于匹配**文件名**，而之前学过的正则表达式主要用于匹配**文本内容**，两者应用场景不同。

![](img/c62e484dd441be0a03917b772ca7b565_117.png)

![](img/c62e484dd441be0a03917b772ca7b565_119.png)

![](img/c62e484dd441be0a03917b772ca7b565_120.png)

以下是bash shell中常用的通配符及其含义：

![](img/c62e484dd441be0a03917b772ca7b565_122.png)

![](img/c62e484dd441be0a03917b772ca7b565_124.png)

![](img/c62e484dd441be0a03917b772ca7b565_126.png)

**星号 `*`**
匹配零个或多个任意字符。
```bash
ls *.txt      # 列出所有以.txt结尾的文件
ls a*         # 列出所有以a开头的文件
```

![](img/c62e484dd441be0a03917b772ca7b565_128.png)

![](img/c62e484dd441be0a03917b772ca7b565_130.png)

**问号 `?`**
匹配任意一个单个字符。
```bash
ls file?.txt  # 列出如file1.txt, fileA.txt等文件，但不包括file10.txt
```

![](img/c62e484dd441be0a03917b772ca7b565_132.png)

![](img/c62e484dd441be0a03917b772ca7b565_134.png)

**中括号 `[]`**
匹配括号内列出的任意一个字符。
```bash
ls file[123].txt    # 匹配file1.txt, file2.txt, file3.txt
ls [abc]*           # 匹配以a、b或c开头的所有文件
```
可以在括号内使用短横线 `-` 表示一个范围，例如 `[a-z]` 匹配所有小写字母，`[0-9]` 匹配所有数字。使用 `^` 或 `!` 表示否定，例如 `[^abc]` 或 `[!abc]` 匹配除了a、b、c之外的任意一个字符。

**预定义字符类**
bash还提供了一些预定义的字符类，使匹配更简洁。
*   `[[:lower:]]`：匹配任意一个小写字母，等价于 `[a-z]`。
*   `[[:upper:]]`：匹配任意一个大写字母，等价于 `[A-Z]`。
*   `[[:digit:]]`：匹配任意一个数字，等价于 `[0-9]`。
*   `[[:alpha:]]`：匹配任意一个字母（包括大小写）。
*   `[[:alnum:]]`：匹配任意一个字母或数字。
*   `[[:space:]]`：匹配任意一个空白字符（如空格、制表符）。

![](img/c62e484dd441be0a03917b772ca7b565_136.png)

![](img/c62e484dd441be0a03917b772ca7b565_138.png)

![](img/c62e484dd441be0a03917b772ca7b565_140.png)

![](img/c62e484dd441be0a03917b772ca7b565_142.png)

![](img/c62e484dd441be0a03917b772ca7b565_144.png)

![](img/c62e484dd441be0a03917b772ca7b565_146.png)

![](img/c62e484dd441be0a03917b772ca7b565_148.png)

![](img/c62e484dd441be0a03917b772ca7b565_150.png)

## 命令扩展符

命令扩展符允许我们将一个命令的输出结果嵌入到另一个命令或字符串中。这是编写脚本和复杂命令时非常有用的功能。

**`$(command)` 或 `` `command` ``**
这两种语法的作用相同，都是执行 `command` 命令，并将其标准输出结果替换到当前位置。推荐使用 `$(command)`，因为它更清晰，且易于嵌套。
```bash
echo "Today is $(date)"        # 输出：Today is [当前日期时间]
echo "Hostname is `hostname`"  # 输出：Hostname is [主机名]
```
一个常见的用法是在创建带时间戳的文件或目录时：
```bash
cp -r /etc /tmp/etc_backup_$(date +%Y%m%d)
# 这将把/etc目录复制到/tmp/etc_backup_20231027（假设今天是2023年10月27日）
```

![](img/c62e484dd441be0a03917b772ca7b565_152.png)

![](img/c62e484dd441be0a03917b772ca7b565_154.png)

![](img/c62e484dd441be0a03917b772ca7b565_156.png)

![](img/c62e484dd441be0a03917b772ca7b565_158.png)

**大括号扩展 `{}`**
大括号扩展可以生成任意字符串的组合，常用于快速创建多个文件或目录。
```bash
touch file{1,2,3}.txt      # 创建file1.txt, file2.txt, file3.txt
mkdir -p dir{A,B,C}/{1,2,3} # 创建dirA/1, dirA/2, ... dirC/3 共9个目录
```
也可以使用 `..` 表示一个序列：
```bash
touch file{1..10}.txt      # 创建file1.txt到file10.txt
echo {a..z}                # 输出a到z的所有字母
```

![](img/c62e484dd441be0a03917b772ca7b565_160.png)

![](img/c62e484dd441be0a03917b772ca7b565_162.png)

![](img/c62e484dd441be0a03917b772ca7b565_164.png)

![](img/c62e484dd441be0a03917b772ca7b565_165.png)

![](img/c62e484dd441be0a03917b772ca7b565_167.png)

## 变量

![](img/c62e484dd441be0a03917b772ca7b565_169.png)

![](img/c62e484dd441be0a03917b772ca7b565_171.png)

变量是用于存储数据的命名内存空间。在shell中，变量名通常使用大写字母，这是一种约定俗成的做法。

![](img/c62e484dd441be0a03917b772ca7b565_173.png)

![](img/c62e484dd441be0a03917b772ca7b565_175.png)

![](img/c62e484dd441be0a03917b772ca7b565_177.png)

![](img/c62e484dd441be0a03917b772ca7b565_179.png)

**定义与赋值**
变量名必须以字母或下划线开头，区分大小写。
```bash
MY_VAR="Hello World"  # 定义变量MY_VAR并赋值为"Hello World"
```

![](img/c62e484dd441be0a03917b772ca7b565_181.png)

![](img/c62e484dd441be0a03917b772ca7b565_183.png)

**引用变量**
使用美元符号 `$` 来引用变量的值。
```bash
echo $MY_VAR          # 输出：Hello World
```
当变量名与其他字符连接时，为了明确变量名的边界，可以使用 `${}`。
```bash
SUFFIX="_backup"
echo "file${SUFFIX}"  # 输出：file_backup
# 如果不加{}，$SUFFIX会被当作变量名，导致错误
```

![](img/c62e484dd441be0a03917b772ca7b565_185.png)

![](img/c62e484dd441be0a03917b772ca7b565_187.png)

![](img/c62e484dd441be0a03917b772ca7b565_189.png)

![](img/c62e484dd441be0a03917b772ca7b565_191.png)

![](img/c62e484dd441be0a03917b772ca7b565_193.png)

![](img/c62e484dd441be0a03917b772ca7b565_195.png)

![](img/c62e484dd441be0a03917b772ca7b565_197.png)

**查看与取消变量**
*   `set`：查看当前shell中定义的所有变量（包括环境变量和本地变量）。
*   `unset`：取消一个变量的定义。
```bash
unset MY_VAR          # 取消变量MY_VAR
```

![](img/c62e484dd441be0a03917b772ca7b565_199.png)

![](img/c62e484dd441be0a03917b772ca7b565_201.png)

![](img/c62e484dd441be0a03917b772ca7b565_203.png)

![](img/c62e484dd441be0a03917b772ca7b565_205.png)

**变量作用域：本地变量与环境变量**
*   **本地变量**：仅在定义它的当前shell进程中有效。子shell（例如通过 `bash` 命令新开的shell）无法访问父shell的本地变量。
    ```bash
    LOCAL_VAR="I am local"
    bash               # 进入子shell
    echo $LOCAL_VAR    # 输出为空，因为子shell访问不到
    exit               # 退出子shell
    echo $LOCAL_VAR    # 输出：I am local
    ```
*   **环境变量**：在定义它的shell及其所有子shell中都有效。使用 `export` 命令可以将一个本地变量提升为环境变量。
    ```bash
    export GLOBAL_VAR="I am global"
    bash               # 进入子shell
    echo $GLOBAL_VAR   # 输出：I am global
    ```
    也可以直接定义环境变量：
    ```bash
    export ANOTHER_VAR="Another global var"
    ```

**重要的系统环境变量**
系统中有许多预定义的环境变量，它们影响着shell的行为。
*   `PATH`：定义了shell在执行命令时搜索可执行文件的目录列表。当您输入一个命令（如 `ls`）时，shell会按照 `PATH` 变量中列出的目录顺序去查找这个命令。
    ```bash
    echo $PATH
    # 输出类似：/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
    ```
    注意：`PATH` 中的目录只被搜索一层，不会递归搜索子目录。
*   `PS1`：定义了命令行提示符的格式。例如，它决定了提示符中显示用户名、主机名、当前目录等信息。
*   `HOME`：当前用户的家目录路径。
*   `USER` 或 `LOGNAME`：当前登录的用户名。
*   `SHELL`：当前用户使用的shell程序路径。
*   `HISTSIZE`：定义在历史记录中保存的命令数量。

![](img/c62e484dd441be0a03917b772ca7b565_207.png)

![](img/c62e484dd441be0a03917b772ca7b565_208.png)

![](img/c62e484dd441be0a03917b772ca7b565_210.png)

![](img/c62e484dd441be0a03917b772ca7b565_212.png)

![](img/c62e484dd441be0a03917b772ca7b565_214.png)

**只读系统变量**
有些变量主要用于查询系统状态，通常不建议修改。
*   `$UID`：当前用户的数字用户ID（UID）。root用户的UID是0。
*   `$PWD`：当前工作目录的路径。

## 命令别名

别名（alias）可以为常用的命令或命令组合创建一个简短的替代名称，从而提高效率。

**查看别名**
使用 `alias` 命令可以查看当前已定义的所有别名。
```bash
alias
```

![](img/c62e484dd441be0a03917b772ca7b565_216.png)

![](img/c62e484dd441be0a03917b772ca7b565_218.png)

**定义别名**
使用 `alias 别名='命令'` 的格式来定义别名。建议用单引号将命令括起来。
```bash
alias ll='ls -l --color=auto'
alias cp='cp -i'                # 使cp命令默认进行交互式确认
```
您可以创建更复杂的别名，例如：
```bash
alias mybackup='cp -r /etc /tmp/etc_backup_$(date +%Y%m%d)'
```

![](img/c62e484dd441be0a03917b772ca7b565_220.png)

![](img/c62e484dd441be0a03917b772ca7b565_222.png)

![](img/c62e484dd441be0a03917b772ca7b565_223.png)

![](img/c62e484dd441be0a03917b772ca7b565_225.png)

![](img/c62e484dd441be0a03917b772ca7b565_227.png)

![](img/c62e484dd441be0a03917b772ca7b565_229.png)

**取消别名**
使用 `unalias` 命令取消一个别名。
```bash
unalias ll
```

![](img/c62e484dd441be0a03917b772ca7b565_231.png)

![](img/c62e484dd441be0a03917b772ca7b565_233.png)

**别名的作用范围**
在命令行中直接定义的别名是临时的，只在当前shell会话中有效。一旦关闭终端或退出shell，别名就会失效。若想永久生效，需要将别名定义写入shell的配置文件（如 `~/.bashrc`），这将在后续章节中介绍。

![](img/c62e484dd441be0a03917b772ca7b565_235.png)

![](img/c62e484dd441be0a03917b772ca7b565_236.png)

![](img/c62e484dd441be0a03917b772ca7b565_238.png)

---

![](img/c62e484dd441be0a03917b772ca7b565_240.png)

本节课中我们一起学习了bash shell的核心概念。我们了解了shell作为用户与内核桥梁的作用，掌握了通配符匹配文件名的技巧，学会了使用命令扩展符 `$( )` 来嵌入命令结果，并深入理解了变量的定义、引用以及本地变量与环境变量的区别。最后，我们还学习了如何使用别名来简化常用命令。这些知识是高效使用Linux命令行和后续学习shell脚本编程的坚实基础。