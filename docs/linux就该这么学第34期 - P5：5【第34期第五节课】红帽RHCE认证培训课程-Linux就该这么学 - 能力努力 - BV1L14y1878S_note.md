# Linux就该这么学：第34期：P5：管道符、重定向与环境变量 🚀

![](img/38304773fc79e1ddcea565c8e428bdd1_1.png)

## 概述

![](img/38304773fc79e1ddcea565c8e428bdd1_3.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_5.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_7.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_9.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_11.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_13.png)

在本节课中，我们将学习Linux命令行中的三个核心概念：**管道符**、**重定向**和**环境变量**。这些工具是高效使用Linux系统的关键，它们能帮助我们灵活地处理命令之间的数据流、管理输入输出，以及配置系统的工作环境。课程内容将简单直白，确保初学者能够轻松理解。

![](img/38304773fc79e1ddcea565c8e428bdd1_15.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_17.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_19.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_21.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_23.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_25.png)

---

![](img/38304773fc79e1ddcea565c8e428bdd1_27.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_29.png)

## 3.2 管道符：命令间的任意门 🚪

上一节我们介绍了重定向符，它用于处理命令与文件之间的数据交互。本节中，我们来看看**管道符**，它的作用是在**命令与命令之间**传递数据。

![](img/38304773fc79e1ddcea565c8e428bdd1_31.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_33.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_35.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_37.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_39.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_41.png)

管道符的写法是 `|`（按住Shift键，再按回车键上方的反斜杠键）。它的功能类似于“任意门”，能将前一个命令的输出结果，作为后一个命令的输入值进行二次处理。

![](img/38304773fc79e1ddcea565c8e428bdd1_43.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_45.png)

**基本格式如下：**
```bash
命令A | 命令B
```
这个格式表示将`命令A`的输出结果，通过管道符传递给`命令B`进行处理。管道符可以连续使用多次。

![](img/38304773fc79e1ddcea565c8e428bdd1_47.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_49.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_51.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_53.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_55.png)

以下是管道符的几个典型应用场景：

![](img/38304773fc79e1ddcea565c8e428bdd1_57.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_59.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_61.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_63.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_65.png)

*   **统计符合条件的用户数量**：例如，从`/etc/passwd`文件中筛选出可以登录系统的用户，并统计其数量。
    ```bash
    grep "/bin/bash" /etc/passwd | wc -l
    ```
*   **统计当前目录下的文件数量**：`ls`命令每行显示一个文件信息，通过管道传递给`wc -l`进行行数统计。
    ```bash
    ls | wc -l
    ```
*   **查找特定进程**：从所有进程信息中，快速过滤出我们关心的进程。
    ```bash
    ps aux | grep nginx
    ```
*   **非交互式设置密码**：通过`echo`输出密码，再利用管道符传递给`passwd`命令，实现自动化密码设置。
    ```bash
    echo "NewPassword" | passwd --stdin username
    ```

![](img/38304773fc79e1ddcea565c8e428bdd1_67.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_69.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_71.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_73.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_75.png)

---

![](img/38304773fc79e1ddcea565c8e428bdd1_77.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_79.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_81.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_83.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_85.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_87.png)

## 3.3 命令通配符：模糊匹配的利器 🎯

![](img/38304773fc79e1ddcea565c8e428bdd1_89.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_91.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_93.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_95.png)

通配符可以帮助我们根据已知的部分信息，来匹配或查找文件或数据。它主要用于处理文件名或字符串的模式匹配。

![](img/38304773fc79e1ddcea565c8e428bdd1_97.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_99.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_101.png)

Linux中常用的通配符主要有以下几种：

![](img/38304773fc79e1ddcea565c8e428bdd1_103.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_105.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_107.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_109.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_111.png)

*   **星号 `*`**：匹配零个、一个或多个任意字符。
    *   **示例**：`ls /dev/sda*` 会匹配 `/dev/sda`、`/dev/sda1`、`/dev/sda123` 等。
*   **问号 `?`**：匹配一个任意的单个字符。
    *   **示例**：`ls /dev/sda?` 会匹配 `/dev/sda1`、`/dev/sda2`，但不会匹配 `/dev/sda` 或 `/dev/sda12`。
*   **中括号 `[ ]`**：匹配括号内指定的单个字符范围。
    *   **匹配数字**：`[0-9]` 匹配任意一个数字。
    *   **匹配字母**：`[a-z]` 匹配任意一个小写字母；`[A-Z]` 匹配任意一个大写字母；`[[:alpha:]]` 匹配任意一个字母（不区分大小写）。
    *   **匹配指定字符**：`[a,c,e]` 匹配字符 `a`、`c` 或 `e`。

![](img/38304773fc79e1ddcea565c8e428bdd1_113.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_115.png)

**注意**：使用中括号 `[ ]` 时，如果匹配成功则显示结果，若无匹配则无输出。这与使用大括号 `{ }` 的行为不同（若无匹配会报错）。在实际工作中，建议使用更严谨、明确的中括号加逗号分隔的写法。

![](img/38304773fc79e1ddcea565c8e428bdd1_117.png)

---

![](img/38304773fc79e1ddcea565c8e428bdd1_119.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_121.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_123.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_125.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_127.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_129.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_131.png)

## 3.4 转义符与引用符：让命令理解你的意图 🛡️

![](img/38304773fc79e1ddcea565c8e428bdd1_133.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_135.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_137.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_139.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_141.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_143.png)

有时，命令行中的特殊字符（如`$`、`*`、`空格`）会被Shell解释为具有特殊功能（变量引用、通配符、参数分隔），而不是普通的文本字符。为了让这些字符“恢复原样”，我们需要使用转义和引用机制。

![](img/38304773fc79e1ddcea565c8e428bdd1_145.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_147.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_149.png)

*   **反斜杠 `\`（转义符）**：将其后面的单个字符转义为普通字符。
    *   **示例**：想输出美元符号 `$`，而不是引用变量。
        ```bash
        echo "Price is \$5"
        # 输出：Price is $5
        ```
*   **单引号 `‘ ’`（强引用）**：引号内的所有字符都视为普通字符，失去一切特殊含义。
    *   **示例**：原样输出包含`$`和空格的一整串字符。
        ```bash
        echo ‘Price is $5 USD’
        # 输出：Price is $5 USD
        ```
*   **双引号 `“ ”`（弱引用）**：引号内大部分字符被视为普通字符，但变量（`$VAR`）和命令替换（`` `command` `` 或 `$(command)`）仍会被解析。
    *   **示例**：保护含有空格的字符串作为一个整体参数。
        ```bash
        echo “Hello World”
        # 将“Hello World”作为一个整体字符串输出
        ```
*   **反引号 `` ` `` 或 `$()`（命令替换）**：执行引号或括号内的命令，并将其输出结果替换到当前位置。
    *   **示例**：将当前日期赋值给变量。
        ```bash
        CURRENT_DATE=`date`
        或
        CURRENT_DATE=$(date)
        ```

![](img/38304773fc79e1ddcea565c8e428bdd1_151.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_153.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_155.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_157.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_159.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_161.png)

---

![](img/38304773fc79e1ddcea565c8e428bdd1_163.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_165.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_167.png)

## 3.5 环境变量：系统的“记忆”与配置 🧠

![](img/38304773fc79e1ddcea565c8e428bdd1_169.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_171.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_173.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_175.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_177.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_179.png)

环境变量是操作系统或Shell中用来保存系统运行环境信息的一种变量。它们影响着命令的行为和系统的配置。

![](img/38304773fc79e1ddcea565c8e428bdd1_181.png)

**重要环境变量举例：**

![](img/38304773fc79e1ddcea565c8e428bdd1_183.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_185.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_187.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_189.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_191.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_193.png)

*   `HOME`：当前用户的家目录路径。
*   `SHELL`：当前用户使用的Shell解释器路径。
*   `HISTSIZE` / `HISTFILESIZE`：Shell命令历史记录保存的条数。
*   `PATH`：系统查找命令的目录路径列表，以冒号`:`分隔。当输入一个命令时，系统会依次在这些路径中查找可执行文件。
*   `LANG`：当前系统的语言和字符集设置。
*   `PS1`：命令行提示符的格式。
*   `RANDOM`：每次引用时生成一个随机整数。

![](img/38304773fc79e1ddcea565c8e428bdd1_195.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_197.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_199.png)

**如何查看与修改变量？**

![](img/38304773fc79e1ddcea565c8e428bdd1_201.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_203.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_205.png)

*   **查看变量**：使用 `echo $变量名`，例如 `echo $PATH`。
*   **设置用户变量**（仅当前Shell有效）：
    ```bash
    MY_VAR=”Hello”
    ```
*   **设置全局环境变量**（对所有用户生效）：
    ```bash
    export MY_VAR=”Hello”
    ```
    或者通过编辑全局配置文件 `/etc/profile` 或用户家目录的 `~/.bashrc` 文件，使其永久生效。修改后需执行 `source /etc/profile` 使配置立即生效。

**命令执行路径的奥秘：**
当您输入一个命令（如 `ls`）并回车时，Shell会按照以下顺序查找并执行：
1.  **绝对/相对路径**：如果命令以 `/` 或 `./` 开头，直接执行该路径下的文件。
2.  **别名**：检查是否为 `alias` 定义的命令简称。
3.  **内部命令**：检查是否为Shell内置的命令（如 `cd`, `history`）。
4.  **外部命令**：根据 `PATH` 环境变量中定义的目录顺序，依次查找名为 `ls` 的可执行文件。

---

![](img/38304773fc79e1ddcea565c8e428bdd1_207.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_209.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_211.png)

## 4.1 Vim文本编辑器：Linux的编辑利器 ✏️

![](img/38304773fc79e1ddcea565c8e428bdd1_213.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_215.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_217.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_219.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_221.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_223.png)

Vim是Linux系统中最强大、最通用的文本编辑器之一。它是Vi编辑器的增强版，主要特点是**语法高亮**，能有效避免配置错误。

![](img/38304773fc79e1ddcea565c8e428bdd1_225.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_227.png)

**Vim的三种主要模式：**

1.  **命令模式**：启动Vim后默认进入的模式。在此模式下，可以执行复制(`yy`)、粘贴(`p`)、删除(`dd`)、查找(`/`)等操作，但不能直接输入文本。它是**输入模式**和**末行模式**切换的桥梁。
2.  **输入模式**（也称插入模式）：在此模式下可以自由编辑文本。通过按 `i`（光标前插入）、`a`（光标后插入）、`o`（下一行插入）等键从命令模式进入。
3.  **末行模式**：在命令模式下按 `:` 进入。用于保存文件、退出编辑器、搜索替换、设置行号等。例如，`:w` 保存，`:q` 退出，`:wq` 保存并退出，`:wq!` 强制保存并退出。

![](img/38304773fc79e1ddcea565c8e428bdd1_229.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_231.png)

**基本编辑流程示例：**
```bash
vim hello.txt        # 打开/创建文件，进入命令模式
i                    # 按下 i 键，进入输入模式
（输入文本内容）
ESC                  # 按下 ESC 键，返回命令模式
:wq                  # 输入 :wq 并按回车，保存文件并退出Vim
```

![](img/38304773fc79e1ddcea565c8e428bdd1_233.png)

**核心操作记忆点：**
*   从**输入模式**返回**命令模式**，按 `ESC` 键。
*   从**命令模式**进入**末行模式**，按 `:` 键。
*   **输入模式**和**末行模式**之间不能直接切换，必须经由**命令模式**中转。

---

![](img/38304773fc79e1ddcea565c8e428bdd1_235.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_237.png)

## 总结

![](img/38304773fc79e1ddcea565c8e428bdd1_239.png)

本节课中我们一起学习了Linux命令行的三大高效工具和核心编辑器：
1.  **管道符 `|`**：实现了命令间输出与输入的连接，让多个命令可以协同工作。
2.  **通配符**：使用 `*`、`?`、`[ ]` 进行模式匹配，简化了对文件或数据的批量操作。
3.  **转义与引用**：通过 `\`、`‘ ’`、`“ ”`、`` ` `` 或 `$()` 来控制Shell对特殊字符的解释，使命令按预期执行。
4.  **环境变量**：了解了如 `PATH`、`HOME` 等关键环境变量的作用，以及如何查看、设置和修改它们，从而定制系统工作环境。
5.  **Vim编辑器**：掌握了Vim三种模式（命令、输入、末行）的切换与基本操作，能够完成文件的创建、编辑、保存和退出。

![](img/38304773fc79e1ddcea565c8e428bdd1_241.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_243.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_245.png)

![](img/38304773fc79e1ddcea565c8e428bdd1_247.png)

掌握这些知识，将使您在Linux命令行下的操作更加得心应手，为后续学习更复杂的系统管理和Shell脚本编程打下坚实基础。