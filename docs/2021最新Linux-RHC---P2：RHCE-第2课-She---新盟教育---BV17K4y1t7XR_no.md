# Linux运维实战课程：P2：Shell之四剑客 🗡️

![](img/6c849a81548c058cae3efd8f594e5b3f_1.png)

在本节课中，我们将要学习Linux Shell中功能强大的四个文本处理工具，它们被合称为“四剑客”。这四个工具分别是 `find`、`grep`、`sed` 和 `awk`。我们将分三部分进行讲解，前两个工具主要用于命令行直接执行，后两个则更多地应用于脚本编写。掌握它们将极大地提升你在Linux环境下的工作效率。

## 第一部分：find与grep 🔍

![](img/6c849a81548c058cae3efd8f594e5b3f_3.png)

上一节我们介绍了四剑客的概览，本节中我们来看看 `find` 和 `grep` 这两个命令。

![](img/6c849a81548c058cae3efd8f594e5b3f_5.png)

### find命令：大海捞针

![](img/6c849a81548c058cae3efd8f594e5b3f_7.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_9.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_11.png)

`find` 命令用于在文件系统中查找符合条件的文件或目录。你可以把它想象成“大海捞针”，大海是整个Linux系统，针就是你要找的文件。

其基本语法结构如下：
```bash
find [路径] [选项] [动作]
```
*   **路径**：指定查找的起始目录。如果要从根目录开始查找整个系统，可以使用 `/`。
*   **选项**：这是命令的核心，用于指定查找条件。
*   **动作**：对查找到的结果执行的操作。

`find` 命令的参数没有太多逻辑关联，你只需要根据需求组合相应的参数即可。以下是常用的查找属性参数：

以下是 `find` 命令常用的参数列表：
*   **通过名称查找**
    *   `-name`：根据文件名查找，区分大小写。
    *   `-iname`：根据文件名查找，忽略大小写。
*   **通过大小查找**
    *   `-size`：根据文件大小查找。使用 `+` 表示大于，`-` 表示小于，不跟符号表示等于。例如，`-size +6M` 表示大于6兆字节，`-size -10M` 表示小于10兆字节。查找大于6兆小于10兆的文件需要连用两个 `-size` 参数：`-size +6M -size -10M`。
*   **通过类型查找**
    *   `-type`：根据文件类型查找。常用类型有 `f`（普通文件）和 `d`（目录）。
*   **通过时间查找**
    *   时间参数分为两组，一组以天为单位，一组以分钟为单位。
    *   `-atime`：访问时间。
    *   `-mtime`：内容修改时间。
    *   `-ctime`：状态改变时间（包括属性和内容）。
    *   同样使用 `+` 和 `-` 表示时间范围，例如 `-mtime +10` 表示10天前修改的文件。
*   **通过属主/属组查找**
    *   `-user`：根据文件属主查找。
    *   `-group`：根据文件属组查找。

`find` 命令支持通配符，例如 `*` 代表任意长度的任意字符。

![](img/6c849a81548c058cae3efd8f594e5b3f_13.png)

**综合案例**：在根目录下查找以 `a` 开头、以 `.log` 结尾、排除 `access.log`、修改时间在10天前、大小在5兆到20兆之间、属主为 `root` 的普通文件。
```bash
find / -name “a*.log” ! -name “access.log” -mtime +10 -size +5M -size -20M -user root -type f
```

![](img/6c849a81548c058cae3efd8f594e5b3f_15.png)

**对查找结果执行操作**：找到文件后，我们经常需要对其进行处理，例如复制。

以下是两种常用的方法：
*   **方法一：使用 `-exec` 参数**
    ```bash
    find /path -name “*.txt” -exec cp {} /opt/ \;
    ```
    *   `-exec` 表示执行后面的命令。
    *   `{}` 代表 `find` 命令匹配到的每一个结果。
    *   `\;` 是 `-exec` 参数的结束标志。
*   **方法二：使用管道符和 `xargs` 命令**
    ```bash
    find /path -name “*.txt” | xargs -I {} cp {} /opt/
    ```
    *   `xargs` 将前面命令的输出作为后面命令的参数。
    *   `-I {}` 指定替换字符串。

**两者的区别**：`-exec` 参数是 `find` 自带的，它为每一个匹配到的文件执行一次命令，更加稳定。而 `xargs` 是将所有匹配结果一次性传递给后面的命令，如果文件数量或总大小极大，可能不够稳定。因此在生产环境中，更推荐使用 `-exec`。

### grep命令：文本搜索

![](img/6c849a81548c058cae3efd8f594e5b3f_17.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_18.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_20.png)

`grep` 命令用于在文件中搜索匹配指定模式的文本行。它与 `find` 不同，`find` 找文件，`grep` 找文件里的内容。

![](img/6c849a81548c058cae3efd8f594e5b3f_22.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_23.png)

其基本语法是：
```bash
grep [选项] 模式 [文件...]
```
或者通过管道与其他命令结合：
```bash
cat file | grep [选项] 模式
```

![](img/6c849a81548c058cae3efd8f594e5b3f_25.png)

以下是 `grep` 命令常用的选项列表：
*   `-i`：忽略大小写。
*   `-n`：显示匹配行在原文件中的行号。
*   `-o`：只显示匹配到的内容本身，而不是整行。
*   `-v`：反向选择，即显示不包含匹配模式的所有行。

![](img/6c849a81548c058cae3efd8f594e5b3f_27.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_29.png)

一个常见用法是结合 `ps` 命令查看进程时，排除 `grep` 进程本身：
```bash
ps -ef | grep sshd | grep -v grep
```

![](img/6c849a81548c058cae3efd8f594e5b3f_31.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_33.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_35.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_37.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_39.png)

`grep` 支持强大的正则表达式进行模式匹配。

![](img/6c849a81548c058cae3efd8f594e5b3f_41.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_43.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_45.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_47.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_49.png)

---

![](img/6c849a81548c058cae3efd8f594e5b3f_51.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_53.png)

## 第二部分：sed命令 ✏️

![](img/6c849a81548c058cae3efd8f594e5b3f_55.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_57.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_59.png)

上一节我们介绍了用于查找的 `find` 和 `grep`，本节中我们来看看用于编辑文本的 `sed` 命令。

`sed` 是“流编辑器”，可以对文本进行增、删、改、查等操作，但最常用的功能是**替换**。它与 `vi` 等交互式编辑器最大的区别在于，`sed` 通过“缓冲区”工作：它逐行读取输入到模式空间（缓冲区），在缓冲区中处理，然后默认输出到屏幕。只有使用特定选项（如 `-i`）才会将修改写回原文件。

![](img/6c849a81548c058cae3efd8f594e5b3f_61.png)

其基本语法如下：
```bash
sed [选项] ‘子命令’ 文件1 [文件2 ...]
sed [选项] -f 脚本文件 文件1 [文件2 ...]
```

![](img/6c849a81548c058cae3efd8f594e5b3f_63.png)

以下是 `sed` 命令常用的选项列表：
*   `-e`：直接在命令行模式进行编辑（默认选项，可不写）。
*   `-f`：将 `sed` 动作写在一个脚本文件中，用 `-f` 执行。
*   `-i`：直接修改文件内容，跳过缓冲区。
*   `-n`：只打印经过 `sed` 处理的行，常与 `p` 命令连用。
*   `-r`：支持扩展正则表达式。

![](img/6c849a81548c058cae3efd8f594e5b3f_65.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_67.png)

`sed` 的核心在于其**子命令**，它们决定了执行什么操作。以下是一些关键子命令：
*   `s`：替换。格式为 `s/原字符串/新字符串/[标志]`。
*   `d`：删除行。
*   `a`：在当前行下一行追加文本。
*   `i`：在当前行上一行插入文本。
*   `p`：打印模式空间的内容。

### 核心功能：替换（s命令）

替换是 `sed` 最常用的功能。斜杠 `/` 是默认的**定界符**，如果替换内容中包含定界符，则需要转义 `\` 或者换用其他字符（如 `:`、`|`、`#`）作为定界符。

**基本替换**：将每行第一个 `books` 替换为 `sexy`，并打印到屏幕（不修改原文件）。
```bash
sed ‘s/books/sexy/‘ file.txt
```

**全局替换**：使用 `g` 标志，替换每一行中所有匹配项。
```bash
sed ‘s/books/sexy/g‘ file.txt
```

**替换并保存**：使用 `-i` 选项将修改直接写入原文件。
```bash
sed -i ‘s/books/sexy/g‘ file.txt
```

![](img/6c849a81548c058cae3efd8f594e5b3f_69.png)

**只打印发生替换的行**：结合 `-n` 选项和 `p` 命令。
```bash
sed -n ‘s/books/sexy/p‘ file.txt
```

![](img/6c849a81548c058cae3efd8f594e5b3f_71.png)

**指定行范围进行替换**：例如，只替换第1到第4行。
```bash
sed ‘1,4 s/books/sexy/g‘ file.txt
```

**使用正则表达式和反向引用**：`&` 或 `\1`、`\2` 等可以引用匹配到的子串。
```bash
# 将包含数字的行中，数字用括号括起来
sed ‘s/[0-9][0-9]*/(&)/‘ file.txt
# 调换两个单词的顺序（假设用空格分隔）
sed ‘s/\([a-z]*\) \([a-z]*\)/\2 \1/‘ file.txt
```

![](img/6c849a81548c058cae3efd8f594e5b3f_73.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_74.png)

### 其他常用功能

![](img/6c849a81548c058cae3efd8f594e5b3f_76.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_78.png)

**删除行**：删除第2行。
```bash
sed ‘2d‘ file.txt
```
删除空行。
```bash
sed ‘/^$/d‘ file.txt
```

**插入与追加**：在匹配 `test` 的行下一行追加 `hello world`。
```bash
sed ‘/test/a\hello world‘ file.txt
```
在匹配 `test` 的行上一行插入 `hello world`。
```bash
sed ‘/test/i\hello world‘ file.txt
```

![](img/6c849a81548c058cae3efd8f594e5b3f_80.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_82.png)

**多点编辑**：使用 `-e` 选项可以在同一行执行多条命令。例如，先删除1-5行，再将剩余行中的 `test` 替换为 `check`。
```bash
sed -e ‘1,5d‘ -e ‘s/test/check/g‘ file.txt
```

![](img/6c849a81548c058cae3efd8f594e5b3f_84.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_86.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_88.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_90.png)

**从文件读入**：`r` 命令可以将另一个文件的内容读入，插入到匹配行的后面。
```bash
# 将 file2.txt 的内容插入到 file1.txt 中所有包含 ‘test‘ 的行之后
sed ‘/test/r file2.txt‘ file1.txt
```

![](img/6c849a81548c058cae3efd8f594e5b3f_92.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_94.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_96.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_98.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_99.png)

---

![](img/6c849a81548c058cae3efd8f594e5b3f_101.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_103.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_105.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_107.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_108.png)

## 第三部分：awk命令 🚀

![](img/6c849a81548c058cae3efd8f594e5b3f_110.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_112.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_114.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_116.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_118.png)

上一节我们介绍了流编辑器 `sed`，本节中我们来看看功能更强大的 `awk`。`awk` 不仅仅是一个命令，它是一门完整的编程语言，支持变量、数组、函数、流程控制语句（if、while、for）等。它是四剑客中最复杂也最强大的工具。

![](img/6c849a81548c058cae3efd8f594e5b3f_120.png)

`awk` 的基本用途是**分析文本并生成报告**。它和 `grep` 的相同点是以行为处理单位；不同点在于，`awk` 能够将每一行分割成多个“字段”，进行更精确的匹配和处理。

![](img/6c849a81548c058cae3efd8f594e5b3f_122.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_124.png)

其基本语法如下：
```bash
awk [选项] ‘模式 {动作}‘ 文件1 [文件2 ...]
awk [选项] -f 脚本文件 文件1 [文件2 ...]
```

![](img/6c849a81548c058cae3efd8f594e5b3f_126.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_128.png)

### 基本概念与用法

**字段分割**：默认以空格作为字段分隔符。可以使用 `-F` 选项指定其他分隔符，例如以冒号分隔：
```bash
awk -F:‘{print $1}‘ /etc/passwd
```
*   `$1`、`$2`...`$NF` 分别代表第1、第2到最后一个字段。
*   `$0` 代表整行记录。
*   `NF` 变量代表当前行的字段总数。
*   `NR` 变量代表当前处理的行号（记录号）。

![](img/6c849a81548c058cae3efd8f594e5b3f_130.png)

**基本打印**：打印 `/etc/passwd` 文件中第一列（用户名）和第三列（用户ID）。
```bash
awk -F:‘{print $1, $3}‘ /etc/passwd
```
在输出中插入自定义文本：
```bash
awk -F:‘{print “Username:“ $1, “UID:“ $3}‘ /etc/passwd
```

![](img/6c849a81548c058cae3efd8f594e5b3f_132.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_133.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_135.png)

**模式匹配**：只处理匹配特定模式的行。例如，打印包含 `root` 的行。
```bash
awk ‘/root/ {print $0}‘ /etc/passwd
```
打印UID大于1000的用户名。
```bash
awk -F:‘$3 > 1000 {print $1}‘ /etc/passwd
```

![](img/6c849a81548c058cae3efd8f594e5b3f_137.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_139.png)

**BEGIN 和 END 块**：`BEGIN` 块在处理任何行之前执行，常用于初始化变量；`END` 块在处理完所有行之后执行，常用于输出总结信息。
```bash
awk ‘BEGIN {print “Start Processing“} {print $0} END {print “End Processing“}‘ file.txt
```

### 编程功能

**变量**：`awk` 可以自定义变量。
```bash
awk ‘BEGIN {sum=0} {sum+=$3} END {print “Total:“ sum}‘ file.txt
```

**条件语句（if）**：输出第10到20行的用户名。
```bash
awk -F:‘NR>=10 && NR<=20 {print $1}‘ /etc/passwd
```
判断并输出。
```bash
awk -F:‘{if($3==0) print $1 “ is admin“; else print $1 “ is user“}‘ /etc/passwd
```

![](img/6c849a81548c058cae3efd8f594e5b3f_141.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_143.png)

**循环语句（for， while）**：
```bash
# for 循环打印1到10
awk ‘BEGIN {for(i=1;i<=10;i++) print i}‘
# while 循环
awk ‘BEGIN {i=1; while(i<=10) {print i; i++}}‘
```

**数组**：`awk` 支持关联数组，索引可以是数字或字符串。统计不同Shell的使用次数。
```bash
awk -F:‘{shell[$NF]++} END {for(s in shell) print s, shell[s]}‘ /etc/passwd
```

![](img/6c849a81548c058cae3efd8f594e5b3f_145.png)

---

![](img/6c849a81548c058cae3efd8f594e5b3f_147.png)

## 总结 📚

![](img/6c849a81548c058cae3efd8f594e5b3f_149.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_151.png)

本节课中我们一起学习了Linux Shell中威力强大的“四剑客”：
1.  **`find`**：用于在文件系统中查找文件，功能强大但参数逻辑简单，需熟记常用属性参数。
2.  **`grep`**：用于在文本中搜索匹配的行，支持正则表达式，是过滤文本的利器。
3.  **`sed`**：流编辑器，擅长对文本进行批量编辑，尤其是替换操作，掌握其缓冲区概念和 `s` 命令是关键。
4.  **`awk`**：一门文本处理编程语言，能够将行分割成字段进行精确处理，支持变量、数组和流程控制，功能最为强大和灵活。

![](img/6c849a81548c058cae3efd8f594e5b3f_153.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_155.png)

![](img/6c849a81548c058cae3efd8f594e5b3f_157.png)

这四种工具各有侧重，组合使用可以解决Linux运维中绝大多数文本处理需求。要熟练掌握它们，关键在于理解其核心概念，并通过大量的练习来巩固。