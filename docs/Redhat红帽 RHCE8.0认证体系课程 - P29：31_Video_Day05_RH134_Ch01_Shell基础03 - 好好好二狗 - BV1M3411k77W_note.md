# Redhat红帽 RHCE8.0认证体系课程：P29：Shell基础扩展知识

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_1.png)

在本节课中，我们将要学习Bash Shell中的一些扩展知识，包括特殊字符的含义与使用、变量扩展、命令替换、算术运算以及循环结构的基础。这些内容是编写高效Shell脚本的基石。

## 特殊字符的含义与使用

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_3.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_5.png)

在Bash Shell中，某些字符具有特殊含义。理解并正确使用它们是编写脚本的关键。

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_7.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_9.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_11.png)

### 井号 (#)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_13.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_14.png)

井号 (`#`) 在Bash Shell中作为注释的开头。该字符以及同一行中该字符后面的所有内容都会被Shell忽略，不会执行。

**示例**：
```bash
# 这是一行注释，不会被执行
echo "Hello World" # 这是行内注释
```
建议在编写脚本时多加注释，以方便自己和他人理解代码。

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_16.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_17.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_18.png)

### 反斜杠 (\)、单引号 (') 和双引号 (")

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_20.png)

某些情况下，我们需要使用字符的字面值，而不是它的特殊含义。这时可以使用反斜杠、单引号或双引号。

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_22.png)

*   **反斜杠 (`\`)**：转义字符，用于取消单个字符的特殊含义。
*   **单引号 (`'`)**：强引用。会保留括起的所有字符的字面含义，不进行任何变量替换或命令替换。
*   **双引号 (`"`)**：弱引用。会保留美元符号 (`$`)、反引号 (`` ` ``) 和反斜杠 (`\`) 的特殊含义，但反斜杠仅在特定字符前才保留其转义作用。

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_24.png)

以下是具体示例，展示了它们的区别：

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_26.png)

**示例**：
```bash
# 井号被当作注释，无输出
echo This is # not a comment

# 使用反斜杠转义井号，输出井号本身
echo This is \# not a comment

# 使用单引号，所有字符按字面输出
echo 'This is # not a comment $PATH `pwd`'

# 使用双引号，$PATH和`pwd`会被替换为其值
echo "This is # not a comment $PATH `pwd`"

# 双引号中，反斜杠仅在特定字符前有效
echo "\$PATH"  # 输出 $PATH (转义了$)
echo "\\"      # 输出 \ (转义了\)
```

通常，为变量分配字符串值时，建议用引号括起来，以避免空格被解释为单词分隔符。

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_28.png)

### 花括号 ({})

花括号用于变量扩展，可以消除变量名引用时的歧义。

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_30.png)

**示例**：
```bash
first=Jim
last=Green
# 不加花括号会产生歧义，Shell会尝试寻找名为`first_`的变量
echo $first_$last   # 输出为空，因为`first_`变量未定义
# 使用花括号明确变量边界
echo ${first}_$last # 输出 Jim_Green
```

## Bash Shell的扩展功能

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_32.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_34.png)

上一节我们介绍了特殊字符，本节中我们来看看Bash Shell的几个核心扩展功能，它们能极大地增强脚本的能力。

### 命令替换

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_36.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_38.png)

命令替换允许我们将命令的输出结果作为值来使用。有两种语法形式：
*   反引号：`` `command` ``
*   `$()`：`$(command)`。这是首选方法，因为它更清晰且支持嵌套。

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_40.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_42.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_44.png)

**示例**：
```bash
# 获取当前日期并输出
echo "Today is $(date)"
# 与反引号效果相同
echo "Today is `date`"
# $()支持嵌套
echo "The process count is $(ps aux | wc -l)"
```

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_46.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_48.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_49.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_51.png)

### 算术扩展

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_53.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_54.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_56.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_57.png)

Bash Shell可以执行简单的整数算术运算。语法是 `$(( expression ))`。表达式中的变量引用可以省略美元符号。

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_59.png)

**示例**：
```bash
a=5
b=3
# 基本的算术运算
c=$((a + b)) # c=8
echo $c
# 直接计算
echo $(( 2 * (a + b) )) # 输出 16
# 使用变量，$可省略
echo $(( a * b )) # 输出 15
# 支持嵌套
echo $(( $(date +%H) * 60 )) # 输出当前小时数乘以60
```

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_61.png)

算术表达式中常用的运算符包括：
*   `++`, `--`：递增、递减
*   `+`, `-`：一元加、减
*   `**`：求幂
*   `*`, `/`, `%`：乘法、除法（取整）、取模
*   `+`, `-`：加法、减法

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_63.png)

运算符遵循标准的优先级规则，可以使用括号 `()` 改变运算顺序。

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_65.png)

## 循环结构

掌握了扩展功能后，我们来看看如何利用循环结构来重复执行任务。Bash提供了几种循环方式。

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_67.png)

### for 循环

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_69.png)

`for` 循环用于遍历一个项目列表。其基本语法如下：
```bash
for variable in item1 item2 ... itemN
do
    commands
done
```
项目列表可以通过多种方式生成：
*   直接列出：`for i in 1 2 3`
*   命令替换：`for file in $(ls *.txt)`
*   花括号扩展：`for num in {1..5}`
*   位置参数：`for arg in "$@"` 或简写为 `for arg`

**示例**：
```bash
# 遍历数字列表
for i in 1 2 3 4 5
do
    echo "Number: $i"
done
# 使用花括号扩展
for i in {1..5}; do echo "Num: $i"; done
# 使用seq命令生成序列
for i in $(seq 1 2 10); do echo $i; done # 输出1,3,5,7,9
# 遍历当前目录下的.txt文件
for file in *.txt; do echo "Processing $file"; done
```

### while 循环

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_71.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_73.png)

`while` 循环会在测试条件为真（退出状态码为0）时，持续执行循环体。

**语法**：
```bash
while test-commands
do
    commands
done
```
**示例**：
```bash
count=5
while [ $count -gt 0 ] # 当count大于0时循环
do
    echo "Countdown: $count"
    count=$((count - 1))
done
```
**重要**：循环体内必须有改变测试条件的语句，否则会导致无限循环。

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_75.png)

### until 循环

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_77.png)

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_79.png)

`until` 循环与 `while` 循环逻辑相反。它会在测试条件为假（退出状态码非0）时执行循环体，条件为真时停止。

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_81.png)

**语法**：
```bash
until test-commands
do
    commands
done
```
**示例**：
```bash
count=1
until [ $count -gt 5 ] # 直到count大于5才停止
do
    echo "Count: $count"
    count=$((count + 1))
done
```

## 总结

![](img/76ff113b2bc7bf69a55ed7ca58c8bf7a_83.png)

本节课中我们一起学习了Bash Shell的多个核心扩展知识。
我们首先了解了特殊字符（`#`、`\`、`'`、`"`、`{}`）的作用，它们控制着注释、引用和变量解析。
接着，我们探讨了命令替换 `$(...)` 和算术扩展 `$((...))`，它们分别用于捕获命令输出和执行数学计算。
最后，我们介绍了三种循环结构：`for` 循环用于遍历列表，`while` 和 `until` 循环则根据条件重复执行代码块。
掌握这些概念是编写自动化、高效Shell脚本的重要基础。