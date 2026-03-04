# Linux高级字符处理与Shell简介：11：高级字符处理与Shell基础

![](img/d0a73c80d084e704da927e985f26590a_1.png)

![](img/d0a73c80d084e704da927e985f26590a_3.png)

![](img/d0a73c80d084e704da927e985f26590a_5.png)

![](img/d0a73c80d084e704da927e985f26590a_7.png)

![](img/d0a73c80d084e704da927e985f26590a_9.png)

![](img/d0a73c80d084e704da927e985f26590a_11.png)

## 概述
在本节课中，我们将深入学习Linux中的高级字符处理工具，特别是流编辑器`sed`，并初步了解Shell（特别是Bash）的基本概念、功能与通配符。这些知识是进行高效系统管理和自动化脚本编写的基础。

![](img/d0a73c80d084e704da927e985f26590a_13.png)

![](img/d0a73c80d084e704da927e985f26590a_15.png)

![](img/d0a73c80d084e704da927e985f26590a_17.png)

![](img/d0a73c80d084e704da927e985f26590a_19.png)

---

![](img/d0a73c80d084e704da927e985f26590a_21.png)

![](img/d0a73c80d084e704da927e985f26590a_23.png)

![](img/d0a73c80d084e704da927e985f26590a_25.png)

## 回顾文本处理工具
上一节我们介绍了VIM编辑器和多种文本处理工具。本节中，我们来看看这些工具的总结与深化。

![](img/d0a73c80d084e704da927e985f26590a_27.png)

![](img/d0a73c80d084e704da927e985f26590a_29.png)

文本提取工具主要有三个：
*   `cat`：连接文件并打印到标准输出。
*   `more`：分页查看工具，用于内容较长的文件。
*   `less`：功能更强的分页查看工具，支持向前向后翻页。

![](img/d0a73c80d084e704da927e985f26590a_31.png)

![](img/d0a73c80d084e704da927e985f26590a_33.png)

文本筛选与过滤工具包括：
*   `head`：显示文件开头部分，默认前十行。命令格式：`head -n <行数> <文件名>`。
*   `tail`：显示文件末尾部分，默认后十行。命令格式：`tail -n <行数> <文件名>`。
*   `grep`：按关键字过滤行，支持正则表达式。命令格式：`grep [选项] ‘模式’ <文件名>`。

![](img/d0a73c80d084e704da927e985f26590a_35.png)

![](img/d0a73c80d084e704da927e985f26590a_37.png)

![](img/d0a73c80d084e704da927e985f26590a_39.png)

按列处理的工具：
*   `cut`：按列切割文本。命令格式：`cut -d‘分隔符’ -f<列号> <文件名>`。
*   `awk`：功能强大的文本分析工具，支持按列处理、计算和脚本。基本格式：`awk -F‘分隔符’ ‘{print $列号}’ <文件名>`。

![](img/d0a73c80d084e704da927e985f26590a_41.png)

![](img/d0a73c80d084e704da927e985f26590a_43.png)

![](img/d0a73c80d084e704da927e985f26590a_45.png)

![](img/d0a73c80d084e704da927e985f26590a_47.png)

文本分析工具：
*   `wc`：统计文本的行数、单词数和字节数。命令格式：`wc <文件名>`。
*   `sort`：对文本行进行排序。命令格式：`sort [选项] <文件名>`。
*   `uniq`：报告或忽略重复的行，常与`sort`连用。命令格式：`uniq [选项] <文件名>`。

![](img/d0a73c80d084e704da927e985f26590a_49.png)

![](img/d0a73c80d084e704da927e985f26590a_51.png)

---

![](img/d0a73c80d084e704da927e985f26590a_53.png)

![](img/d0a73c80d084e704da927e985f26590a_55.png)

![](img/d0a73c80d084e704da927e985f26590a_57.png)

## 文件比较工具
除了内容处理，比较文件差异也是常见需求。

![](img/d0a73c80d084e704da927e985f26590a_59.png)

![](img/d0a73c80d084e704da927e985f26590a_61.png)

![](img/d0a73c80d084e704da927e985f26590a_63.png)

`diff`工具可以比较两个文件的差异，但输出不够直观。
```bash
diff file1.txt file2.txt
```

![](img/d0a73c80d084e704da927e985f26590a_65.png)

![](img/d0a73c80d084e704da927e985f26590a_67.png)

![](img/d0a73c80d084e704da927e985f26590a_69.png)

![](img/d0a73c80d084e704da927e985f26590a_71.png)

更直观的比较方式是使用`vim`的差异模式：
```bash
vimdiff file1.txt file2.txt
```
这种方式会并排高亮显示两个文件的差异，便于查看配置文件被修改了哪些行。

![](img/d0a73c80d084e704da927e985f26590a_73.png)

![](img/d0a73c80d084e704da927e985f26590a_75.png)

![](img/d0a73c80d084e704da927e985f26590a_77.png)

![](img/d0a73c80d084e704da927e985f26590a_79.png)

![](img/d0a73c80d084e704da927e985f26590a_81.png)

---

![](img/d0a73c80d084e704da927e985f26590a_83.png)

![](img/d0a73c80d084e704da927e985f26590a_85.png)

![](img/d0a73c80d084e704da927e985f26590a_87.png)

![](img/d0a73c80d084e704da927e985f26590a_88.png)

## 字符与字符串处理工具
接下来，我们重点学习两个强大的字符处理工具：`tr`和`sed`。

![](img/d0a73c80d084e704da927e985f26590a_90.png)

![](img/d0a73c80d084e704da927e985f26590a_92.png)

![](img/d0a73c80d084e704da927e985f26590a_94.png)

![](img/d0a73c80d084e704da927e985f26590a_96.png)

![](img/d0a73c80d084e704da927e985f26590a_97.png)

![](img/d0a73c80d084e704da927e985f26590a_99.png)

![](img/d0a73c80d084e704da927e985f26590a_101.png)

![](img/d0a73c80d084e704da927e985f26590a_103.png)

### 字符转换工具 `tr`
`tr`（translate）命令用于对字符进行转换、压缩和删除。

![](img/d0a73c80d084e704da927e985f26590a_105.png)

![](img/d0a73c80d084e704da927e985f26590a_107.png)

![](img/d0a73c80d084e704da927e985f26590a_109.png)

![](img/d0a73c80d084e704da927e985f26590a_111.png)

以下是`tr`命令的一些应用：
1.  **字符转换**：将一组字符转换为另一组。
    ```bash
    echo “123” | tr ‘1-9’ ‘a-i’
    # 输出：abc
    ```
2.  **大小写转换**：
    ```bash
    echo “HELLO” | tr ‘A-Z’ ‘a-z’
    # 输出：hello
    ```
3.  **删除字符**：使用`-d`选项删除指定字符。
    ```bash
    echo “hello5world” | tr -d ‘5’
    # 输出：helloworld
    ```
    **注意**：`tr`命令默认将结果输出到屏幕，不直接修改源文件。如需保存，需使用重定向：`tr -d ‘5’ < input.txt > output.txt`。

![](img/d0a73c80d084e704da927e985f26590a_113.png)

![](img/d0a73c80d084e704da927e985f26590a_115.png)

### 流编辑器 `sed`
`sed`（stream editor）是一个强大的流编辑器，**基于行**进行处理，主要用于查找和替换。

![](img/d0a73c80d084e704da927e985f26590a_117.png)

![](img/d0a73c80d084e704da927e985f26590a_119.png)

![](img/d0a73c80d084e704da927e985f26590a_121.png)

`sed`的基本语法格式为：
```bash
sed [选项] ‘脚本命令’ 输入文件
```

![](img/d0a73c80d084e704da927e985f26590a_123.png)

![](img/d0a73c80d084e704da927e985f26590a_125.png)

![](img/d0a73c80d084e704da927e985f26590a_127.png)

![](img/d0a73c80d084e704da927e985f26590a_129.png)

![](img/d0a73c80d084e704da927e985f26590a_130.png)

![](img/d0a73c80d084e704da927e985f26590a_132.png)

#### 常用选项
*   `-n`：禁止默认输出，常与`p`命令连用。
*   `-i`：直接编辑源文件（谨慎使用）。
*   `-e`：允许多个编辑命令。
*   `-r`：使用扩展正则表达式。

![](img/d0a73c80d084e704da927e985f26590a_134.png)

![](img/d0a73c80d084e704da927e985f26590a_136.png)

![](img/d0a73c80d084e704da927e985f26590a_138.png)

![](img/d0a73c80d084e704da927e985f26590a_140.png)

![](img/d0a73c80d084e704da927e985f26590a_142.png)

![](img/d0a73c80d084e704da927e985f26590a_144.png)

#### 地址定界
地址定界用于指定`sed`命令作用的具体行或行范围。
*   `n`：第n行。
*   `$`：最后一行。
*   `n,m`：从第n行到第m行。
*   `/pattern/`：匹配该模式的行。
*   `n,+m`：从第n行开始的连续m行。
*   `!`：对匹配地址的行取反。

![](img/d0a73c80d084e704da927e985f26590a_146.png)

![](img/d0a73c80d084e704da927e985f26590a_148.png)

![](img/d0a73c80d084e704da927e985f26590a_150.png)

#### 常用编辑命令
*   `p`：打印模式空间内容。
*   `d`：删除模式空间内容。
*   `s`：查找并替换，格式为`s/查找内容/替换内容/标志`。
*   `a`：在指定行后追加文本。
*   `i`：在指定行前插入文本。
*   `c`：替换指定行。

![](img/d0a73c80d084e704da927e985f26590a_152.png)

![](img/d0a73c80d084e704da927e985f26590a_153.png)

![](img/d0a73c80d084e704da927e985f26590a_155.png)

![](img/d0a73c80d084e704da927e985f26590a_156.png)

![](img/d0a73c80d084e704da927e985f26590a_158.png)

#### `sed` 应用实例
以下是`sed`命令的一些典型应用场景：

![](img/d0a73c80d084e704da927e985f26590a_160.png)

![](img/d0a73c80d084e704da927e985f26590a_162.png)

![](img/d0a73c80d084e704da927e985f26590a_164.png)

1.  **打印指定行**：
    ```bash
    sed -n ‘5p’ /etc/passwd          # 打印第5行
    sed -n ‘1,10p’ /etc/passwd       # 打印第1到10行
    sed -n ‘$p’ /etc/passwd          # 打印最后一行
    ```

![](img/d0a73c80d084e704da927e985f26590a_166.png)

![](img/d0a73c80d084e704da927e985f26590a_168.png)

![](img/d0a73c80d084e704da927e985f26590a_170.png)

2.  **删除指定行**：
    ```bash
    sed ‘1,5d’ /etc/passwd           # 删除第1到5行（屏幕输出）
    sed -i ‘15,20d’ file.txt         # 直接删除文件中的第15到20行
    ```

![](img/d0a73c80d084e704da927e985f26590a_172.png)

![](img/d0a73c80d084e704da927e985f26590a_174.png)

![](img/d0a73c80d084e704da927e985f26590a_176.png)

![](img/d0a73c80d084e704da927e985f26590a_178.png)

![](img/d0a73c80d084e704da927e985f26590a_180.png)

![](img/d0a73c80d084e704da927e985f26590a_182.png)

3.  **查找与替换**：
    ```bash
    sed ‘s/old/new/’ file.txt        # 每行第一个”old”替换为”new”
    sed ‘s/old/new/g’ file.txt       # 全局替换所有”old”为”new”
    sed ‘1s/old/new/’ file.txt       # 仅替换第一行的第一个”old”
    sed ‘s/old/new/2’ file.txt       # 每行第二个”old”替换为”new”
    ```

![](img/d0a73c80d084e704da927e985f26590a_184.png)

![](img/d0a73c80d084e704da927e985f26590a_186.png)

![](img/d0a73c80d084e704da927e985f26590a_188.png)

![](img/d0a73c80d084e704da927e985f26590a_190.png)

![](img/d0a73c80d084e704da927e985f26590a_192.png)

4.  **在行前后添加内容**：
    ```bash
    sed ‘1a\Hello World’ file.txt    # 在第一行后添加”Hello World”
    ```

![](img/d0a73c80d084e704da927e985f26590a_194.png)

![](img/d0a73c80d084e704da927e985f26590a_196.png)

![](img/d0a73c80d084e704da927e985f26590a_198.png)

![](img/d0a73c80d084e704da927e985f26590a_200.png)

![](img/d0a73c80d084e704da927e985f26590a_202.png)

5.  **添加或删除注释**：
    ```bash
    sed ‘1,5s/^/#/’ file.conf        # 给第1到5行行首添加#注释
    sed ‘s/^#//’ file.conf           # 删除行首的#注释（非全局，每行第一个#）
    ```

![](img/d0a73c80d084e704da927e985f26590a_204.png)

![](img/d0a73c80d084e704da927e985f26590a_206.png)

![](img/d0a73c80d084e704da927e985f26590a_207.png)

6.  **删除行首第一个字符**：
    ```bash
    sed ‘s/^.//’ file.txt            # 删除每行第一个字符（`.`匹配单个字符）
    ```

![](img/d0a73c80d084e704da927e985f26590a_209.png)

![](img/d0a73c80d084e704da927e985f26590a_211.png)

---

![](img/d0a73c80d084e704da927e985f26590a_213.png)

![](img/d0a73c80d084e704da927e985f26590a_214.png)

![](img/d0a73c80d084e704da927e985f26590a_216.png)

![](img/d0a73c80d084e704da927e985f26590a_218.png)

## Shell (Bash) 简介
处理完文本，我们来看看命令的执行环境——Shell。

![](img/d0a73c80d084e704da927e985f26590a_220.png)

![](img/d0a73c80d084e704da927e985f26590a_222.png)

![](img/d0a73c80d084e704da927e985f26590a_224.png)

![](img/d0a73c80d084e704da927e985f26590a_226.png)

![](img/d0a73c80d084e704da927e985f26590a_228.png)

![](img/d0a73c80d084e704da927e985f26590a_230.png)

![](img/d0a73c80d084e704da927e985f26590a_232.png)

### 什么是Shell？
Shell是一个**命令解释器**，它是用户与Linux内核之间的桥梁。
1.  用户输入命令。
2.  Shell接收并解释命令。
3.  Shell将解释后的指令传递给内核。
4.  内核执行指令并返回结果。

![](img/d0a73c80d084e704da927e985f26590a_234.png)

![](img/d0a73c80d084e704da927e985f26590a_236.png)

![](img/d0a73c80d084e704da927e985f26590a_238.png)

![](img/d0a73c80d084e704da927e985f26590a_239.png)

![](img/d0a73c80d084e704da927e985f26590a_241.png)

![](img/d0a73c80d084e704da927e985f26590a_243.png)

![](img/d0a73c80d084e704da927e985f26590a_245.png)

![](img/d0a73c80d084e704da927e985f26590a_247.png)

![](img/d0a73c80d084e704da927e985f26590a_249.png)

![](img/d0a73c80d084e704da927e985f26590a_250.png)

最常见的Shell是**Bash（Bourne-Again Shell）**，它是大多数Linux发行版的默认Shell。

![](img/d0a73c80d084e704da927e985f26590a_252.png)

![](img/d0a73c80d084e704da927e985f26590a_254.png)

![](img/d0a73c80d084e704da927e985f26590a_256.png)

### Bash 的功能特性
Bash提供了许多便利的功能：
*   **命令历史**：`history`命令，使用`Ctrl+R`搜索历史。
*   **命令补全**：按`Tab`键自动补全命令或文件名。
*   **快捷键**：
    *   `Ctrl+C`：终止当前命令。
    *   `Ctrl+L`：清屏。
    *   `Ctrl+Shift+C/V`：在终端中复制/粘贴。
*   **输入输出重定向和管道**：`>`, `>>`, `<`, `|`。

![](img/d0a73c80d084e704da927e985f26590a_258.png)

![](img/d0a73c80d084e704da927e985f26590a_260.png)

![](img/d0a73c80d084e704da927e985f26590a_262.png)

![](img/d0a73c80d084e704da927e985f26590a_264.png)

### Bash 通配符
通配符用于匹配文件名，方便批量操作。

![](img/d0a73c80d084e704da927e985f26590a_266.png)

![](img/d0a73c80d084e704da927e985f26590a_268.png)

![](img/d0a73c80d084e704da927e985f26590a_270.png)

![](img/d0a73c80d084e704da927e985f26590a_271.png)

![](img/d0a73c80d084e704da927e985f26590a_273.png)

以下是常用的通配符：
*   `*`：匹配零个或多个任意字符。例如：`ls *.txt`。
*   `?`：匹配单个任意字符。例如：`ls file?.txt`。
*   `[]`：匹配括号内的任意一个字符。例如：`ls file[123].txt`。
*   `[!]` 或 `[^]`：匹配不在括号内的任意一个字符。例如：`ls file[!1].txt`。
*   `{}`：生成序列或组合。
    *   `{1..10}`：生成1到10的序列。例如：`touch file{1..10}.txt`。
    *   `{a,c,e}`：依次匹配a、c、e。例如：`mkdir dir_{a,c,e}`。

![](img/d0a73c80d084e704da927e985f26590a_275.png)

![](img/d0a73c80d084e704da927e985f26590a_277.png)

![](img/d0a73c80d084e704da927e985f26590a_279.png)

![](img/d0a73c80d084e704da927e985f26590a_281.png)

![](img/d0a73c80d084e704da927e985f26590a_283.png)

![](img/d0a73c80d084e704da927e985f26590a_285.png)

![](img/d0a73c80d084e704da927e985f26590a_286.png)

### Bash 扩展符号
*   `~`：代表当前用户的家目录。`~username`代表指定用户的家目录。
*   `$(命令)` 或 `` `命令` ``：命令替换，将命令的输出作为参数。例如：`echo “Today is $(date)”`。

![](img/d0a73c80d084e704da927e985f26590a_288.png)

![](img/d0a73c80d084e704da927e985f26590a_290.png)

![](img/d0a73c80d084e704da927e985f26590a_292.png)

![](img/d0a73c80d084e704da927e985f26590a_294.png)

![](img/d0a73c80d084e704da927e985f26590a_296.png)

![](img/d0a73c80d084e704da927e985f26590a_298.png)

![](img/d0a73c80d084e704da927e985f26590a_300.png)

---

![](img/d0a73c80d084e704da927e985f26590a_302.png)

![](img/d0a73c80d084e704da927e985f26590a_304.png)

![](img/d0a73c80d084e704da927e985f26590a_306.png)

![](img/d0a73c80d084e704da927e985f26590a_308.png)

![](img/d0a73c80d084e704da927e985f26590a_310.png)

![](img/d0a73c80d084e704da927e985f26590a_312.png)

![](img/d0a73c80d084e704da927e985f26590a_314.png)

## 总结
本节课中我们一起学习了：
1.  **高级字符处理**：重点掌握了流编辑器`sed`的用法，包括地址定界、查找替换(`s`)、删除(`d`)、打印(`p`)等核心操作，用于高效处理文本内容。
2.  **Shell基础**：了解了Shell作为用户与内核桥梁的作用，熟悉了Bash的常用功能（历史、补全、快捷键）和强大的通配符（`*`, `?`, `[]`, `{}`），这些是提高命令行效率的关键。
3.  **工具组合应用**：通过管道`|`将`grep`、`awk`、`sed`、`cut`等工具组合使用，可以应对复杂的文本处理需求，例如提取网络配置的特定信息。

![](img/d0a73c80d084e704da927e985f26590a_316.png)

![](img/d0a73c80d084e704da927e985f26590a_318.png)

![](img/d0a73c80d084e704da927e985f26590a_319.png)

这些技能是进行Linux系统管理、日志分析和Shell脚本编写的坚实基础，务必通过实践练习加以巩固。