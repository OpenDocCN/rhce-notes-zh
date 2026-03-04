# Linux文本处理工具：P31：文本处理工具之cut, sort, tr, sed等使用详解 🛠️

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_1.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_3.png)

在本节课中，我们将学习一系列强大的Linux文本处理工具，包括`cut`、`sort`、`tr`和`sed`。这些工具能够帮助我们以列或行为单位过滤、排序、转换和编辑文本数据，是日常系统管理和数据分析的必备技能。

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_5.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_6.png)

---

## 以列为单位过滤：cut工具 ✂️

上一节我们介绍了以行为单位进行过滤的`grep`工具。本节中我们来看看`cut`，它主要用于以列为单位过滤文本。

`cut`命令的核心是使用分隔符将每一行文本划分为多个列，然后提取指定的列。其基本语法如下：
```bash
cut -d '分隔符' -f 列号 文件名
```
*   `-d`：用于指定列之间的分隔符。
*   `-f`：用于指定要提取的列编号。

例如，`/etc/passwd`文件使用冒号`:`作为分隔符。要提取第一列（用户名），可以执行：
```bash
cut -d ':' -f 1 /etc/passwd
```

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_8.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_10.png)

---

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_12.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_14.png)

## 处理不规则空格：一个综合练习 🤔

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_16.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_18.png)

以下是`cut`命令的一个实际应用场景，它结合了其他工具来解决常见问题。

有时数据列之间的空格数量不规则，例如`df -h`命令的输出。如果只想提取`/dev/sda1`分区的使用率（如26%），直接使用`cut`会因空格数量不定而失败。

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_20.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_21.png)

解决方法是先用`tr`命令压缩连续的空格为单个空格，再用`cut`提取。`tr`的`-s`选项可以压缩（squeeze）重复的字符。
```bash
df -h | grep '/dev/sda1' | tr -s ' ' | cut -d ' ' -f 5
```
这个管道命令的执行流程是：
1.  `df -h`：列出磁盘使用情况。
2.  `grep '/dev/sda1'`：过滤出包含`/dev/sda1`的行。
3.  `tr -s ' '`：将该行中所有连续的空格压缩成一个空格。
4.  `cut -d ' ' -f 5`：以单个空格为分隔符，提取第5列（使用率）。

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_23.png)

---

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_25.png)

## 文本统计与排序：wc与sort工具 📊

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_27.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_29.png)

接下来，我们转向用于文本统计和排序的工具。

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_31.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_33.png)

`wc`命令用于统计文件的行数、单词数和字节数。
*   `wc -l`：统计行数。
*   `wc -w`：统计单词数。
*   `wc -c`：统计字节数。

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_34.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_36.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_37.png)

`sort`命令用于对文本行进行排序。默认按每行首字符的ASCII码值升序排列。
*   `sort -r`：降序排列。
*   `sort -u`：排序并去除重复行（等同于先排序再执行`uniq`）。
*   `sort -n`：按数值大小排序，而不是按字符串。
*   `sort -t ‘分隔符’ -k 列号`：指定分隔符并按特定列排序。

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_39.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_41.png)

例如，要按`/etc/passwd`中第三列（UID）的数值大小升序排序，可以执行：
```bash
sort -t ':' -k 3 -n /etc/passwd
```

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_43.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_45.png)

---

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_47.png)

## 相邻行去重与统计：uniq工具 🔄

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_49.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_51.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_53.png)

`uniq`命令通常与`sort`配合使用，用于报告或忽略文件中的重复行。

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_55.png)

**重要特性**：`uniq`只能识别并处理**相邻的**重复行。因此，通常需要先使用`sort`使所有相同行相邻。
*   `uniq`：去除相邻的重复行，只保留一行。
*   `uniq -c`：在每行前显示该行重复出现的次数。这对于统计词频非常有用。

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_57.png)

例如，统计一个文件中所有单词的出现频率，思路是：将每个单词变成单独一行 -> 排序 -> 使用`uniq -c`统计。
```bash
# 假设文件 words.txt 包含需要统计的文本
# 此处使用tr将非字母字符替换为换行符，使每个单词成一行，然后排序并统计
tr -cs 'a-zA-Z' '\n' < words.txt | sort | uniq -c
```

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_59.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_61.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_63.png)

---

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_65.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_67.png)

## 文件差异比较：diff工具 🔍

`diff`命令用于比较两个文件的差异，并输出不同之处的行。这在对比配置文件的不同版本时非常有用。
```bash
diff file1 file2
```
输出结果会指示第一个文件（file1）如何修改才能变成第二个文件（file2）。

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_69.png)

---

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_71.png)

## 字符转换与压缩：tr工具 🔀

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_73.png)

`tr`（translate）命令用于转换或删除字符。
*   基本转换：`tr ‘原字符’ ‘目标字符’`
*   压缩重复字符：`tr -s ‘要压缩的字符’`
*   删除字符：`tr -d ‘要删除的字符’`

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_75.png)

例如，将文本中的冒号全部替换为换行符：
```bash
cat /etc/passwd | tr ':' '\n'
```

---

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_77.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_78.png)

## 流编辑器：sed工具 🖋️

`sed`是一个功能强大的流编辑器，可以对文本进行过滤、替换、删除、打印等操作，而**默认不修改原文件**（除非使用`-i`选项）。

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_80.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_81.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_83.png)

**基本语法**：`sed [选项] ‘脚本命令’ 文件名`

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_85.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_87.png)

**常用操作**：
*   **打印特定行**：`sed -n ‘5,10p’ filename` 打印第5到第10行（`-n`表示静默模式，仅显示处理后的行）。
*   **全局查找替换**（类似Vim）：`sed ‘s/查找内容/替换内容/g’ filename`
*   **直接修改原文件**（务必谨慎）：`sed -i ‘s/查找内容/替换内容/g’ filename`
*   **修改前备份原文件**：`sed -i.bak ‘s/查找内容/替换内容/g’ filename` 会生成一个`filename.bak`的备份。

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_89.png)

**工作原理**：`sed`将文件内容读入**模式空间**（一块内存缓冲区）进行操作，默认将处理后的结果输出到屏幕。使用`-i`选项时，才会将模式空间的内容写回原文件。

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_91.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_93.png)

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_95.png)

---

## 课程总结 📝

本节课中我们一起学习了多个核心的Linux文本处理工具：
1.  **`cut`**：用于提取文本的特定列，需配合分隔符使用。
2.  **`sort`** & **`uniq`**：用于对文本行排序、去重和统计。`uniq`通常需在`sort`之后使用。
3.  **`tr`**：用于简单的字符转换、压缩或删除。
4.  **`diff`**：用于比较两个文件之间的差异。
5.  **`sed`**：功能强大的流编辑器，可用于复杂的文本替换、删除和打印操作，是处理文本的利器。

![](img/710fd9c8c916b04aec2bfbdf8ed2eb1f_97.png)

掌握这些工具的组合使用，可以高效地完成日志分析、数据提取和配置文件批量修改等任务。