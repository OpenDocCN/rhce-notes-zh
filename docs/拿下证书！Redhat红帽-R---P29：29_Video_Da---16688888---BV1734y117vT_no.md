# Redhat红帽 RHCE8.0认证体系课程：RH134：Ch01_Shell基础01

![](img/42080dd608d41cb7f3a36c66bac4b7da_0.png)

在本节课中，我们将要学习Shell脚本的基础知识，包括变量的定义与使用、变量的类型与作用域、简单的数学运算以及文本处理工具`grep`和`cut`的基本用法。这些是编写自动化运维脚本的核心基础。

## 变量基础

上一节我们介绍了Shell脚本的用途，本节中我们来看看Shell脚本中变量的基本概念和使用方法。

Shell脚本可以理解为一门编程语言，变量是其重要组成部分。变量用于存储数据，以便在脚本中重复使用或传递。

![](img/42080dd608d41cb7f3a36c66bac4b7da_2.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_4.png)

以下是定义和使用变量的基本规则：

![](img/42080dd608d41cb7f3a36c66bac4b7da_6.png)

*   **定义与赋值**：格式为`变量名=变量值`。等号`=`是赋值符号，将右边的值赋予左边的变量名。
    *   **代码示例**：`var1=hello`
*   **引用变量**：使用`$`符号加上变量名来获取变量的值。
    *   **代码示例**：`echo $var1` 会输出 `hello`
*   **注意事项**：
    1.  等号`=`两边不能有空格。`var1 = hello`会被Shell误解为执行命令`var1`并传入参数`=`和`hello`。
    2.  变量名不能以数字开头。`1var=hello`是无效的。
    3.  变量名可以以下划线`_`开头。`_var=hello`是有效的。
    4.  如果变量值包含空格，必须用引号（单引号`'`或双引号`"`）将整个值括起来。
        *   **代码示例**：`var2="hello world"`
    5.  可以将命令的执行结果赋值给变量。
        *   **代码示例**：`var3=$(hostname)` 或 `` var3=`hostname` ``

![](img/42080dd608d41cb7f3a36c66bac4b7da_8.png)

## 引号与命令替换

我们了解了如何给变量赋值，现在来看看引号的差异以及如何更灵活地获取命令结果。

![](img/42080dd608d41cb7f3a36c66bac4b7da_10.png)

单引号`'`和双引号`"`在引用变量时有重要区别：双引号内的变量（如`$var`）会被替换为其值；而单引号内的所有内容都会原样输出。

![](img/42080dd608d41cb7f3a36c66bac4b7da_12.png)

将命令的输出结果作为值赋给变量，称为命令替换。有两种常用方法：
*   **使用反引号**：`` `command` ``
*   **使用`$()`**：`$(command)`
`$()`是更现代且推荐的方式，因为它更清晰且支持嵌套。

![](img/42080dd608d41cb7f3a36c66bac4b7da_14.png)

**代码示例对比**：
```bash
var4=$(pwd)      # 将pwd命令的结果（当前目录）赋给var4
echo $var4       # 输出当前目录路径
echo "$var4"     # 输出当前目录路径（双引号内变量被替换）
echo '$var4'     # 输出字符串 $var4（单引号内原样输出）
```

![](img/42080dd608d41cb7f3a36c66bac4b7da_16.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_18.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_19.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_21.png)

## 变量类型与作用域

![](img/42080dd608d41cb7f3a36c66bac4b7da_23.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_25.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_27.png)

变量不仅有不同的值类型，其生效的范围也有所不同，这被称为作用域。

![](img/42080dd608d41cb7f3a36c66bac4b7da_29.png)

变量主要分为两种类型：
*   **局部变量**：仅在定义它的当前Shell进程中有效，其子进程无法访问。
*   **全局变量（环境变量）**：使用`export`命令声明后，可以被当前进程及其子进程访问。

![](img/42080dd608d41cb7f3a36c66bac4b7da_31.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_33.png)

**代码示例**：
```bash
local_var="I am local"   # 定义局部变量
export global_var="I am global" # 定义并导出为全局变量
bash                     # 启动一个子Shell
echo $local_var          # 输出为空（无法访问）
echo $global_var         # 输出：I am global（可以访问）
exit                     # 退出子Shell
```

![](img/42080dd608d41cb7f3a36c66bac4b7da_35.png)

## 数学运算

![](img/42080dd608d41cb7f3a36c66bac4b7da_37.png)

在Shell中，变量默认被视为字符串。如果需要进行数学运算，需要特殊处理。

![](img/42080dd608d41cb7f3a36c66bac4b7da_39.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_41.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_43.png)

以下是三种实现简单数学运算的方法：

![](img/42080dd608d41cb7f3a36c66bac4b7da_45.png)

1.  **使用`declare`命令**：`declare -i`可以将变量声明为整数类型。
    *   **代码示例**：
        ```bash
        declare -i num1=5
        declare -i num2=3
        declare -i sum=num1+num2
        echo $sum # 输出：8
        ```
2.  **使用`$(( ))`**：双括号结构`$(( 表达式 ))`可以直接进行整数运算。
    *   **公式/代码示例**：`result=$(( 5 + 3 ))`，`echo $result`输出`8`。
3.  **使用`let`命令**：`let`命令专门用于执行算术运算。
    *   **代码示例**：`let "result=5+3"`，`echo $result`输出`8`。

## 文本处理工具：grep

Shell脚本经常需要处理文本数据。`grep`是一个强大的文本搜索工具，用于在文件中查找包含指定模式的行。

以下是`grep`的一些常见用法：

*   **基本搜索**：`grep "关键字" 文件名`
    *   **代码示例**：`grep "root" /etc/passwd` 查找包含`root`的行。
*   **忽略大小写**：使用`-i`选项。
    *   **代码示例**：`grep -i "ROOT" /etc/passwd`
*   **显示上下文**：
    *   `-A n`：显示匹配行及其后`n`行。
    *   `-B n`：显示匹配行及其前`n`行。
    *   `-C n`：显示匹配行及其前后各`n`行。
*   **反向选择**：`-v`选项输出不包含关键字的行。
*   **多模式匹配**：`-E`选项支持扩展正则表达式，或用`egrep`命令，使用`|`表示“或”。
    *   **代码示例**：`grep -E "root|nologin" /etc/passwd` 查找包含`root`或`nologin`的行。

![](img/42080dd608d41cb7f3a36c66bac4b7da_47.png)

## 文本处理工具：cut

`cut`命令用于从文件的每一行中截取部分内容。它通常以指定的分隔符将行切分成字段，然后输出指定的字段。

![](img/42080dd608d41cb7f3a36c66bac4b7da_49.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_50.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_52.png)

**基本语法**：`cut -d‘分隔符’ -f 字段编号 文件名`
*   `-d`：指定字段分隔符（默认是制表符）。
*   `-f`：指定要输出的字段编号（从1开始），多个编号用逗号分隔。

![](img/42080dd608d41cb7f3a36c66bac4b7da_54.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_56.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_57.png)

**代码示例**：
```bash
# 以冒号分隔，输出/etc/passwd文件每行的第1、3、7个字段（用户名、UID、Shell）
cut -d':' -f1,3,7 /etc/passwd
```
**局限性**：`cut`对于分隔符处理不够灵活，当遇到连续分隔符或非标准格式时，可能无法准确截取。更复杂的文本处理通常交给`awk`。

![](img/42080dd608d41cb7f3a36c66bac4b7da_59.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_61.png)

---

![](img/42080dd608d41cb7f3a36c66bac4b7da_63.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_64.png)

![](img/42080dd608d41cb7f3a36c66bac4b7da_66.png)

本节课中我们一起学习了Shell脚本的入门知识。我们掌握了如何定义和使用变量，理解了局部变量与全局变量的区别，学会了三种进行数学运算的方法。此外，我们还初步接触了两个重要的文本处理工具：用于搜索过滤的`grep`和用于字段截取的`cut`。这些是构建更复杂自动化脚本的基石。