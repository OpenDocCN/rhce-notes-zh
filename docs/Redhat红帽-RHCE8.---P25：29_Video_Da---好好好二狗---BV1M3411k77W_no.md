# Redhat红帽 RHCE8.0认证体系课程：P25：Shell基础01 🐚

在本节课中，我们将要学习Shell脚本的基础知识，包括变量的定义与使用、变量的类型与作用域、简单的数学运算以及文本处理工具`grep`和`cut`的基础用法。这些是编写自动化运维脚本的核心基础。

## 概述

恭喜大家完成第一本书的学习。接下来我们将开始第二本书的内容，首先面对的就是Shell脚本。Shell脚本是利用Shell的功能，结合控制语句编写的一种程序，主要用于系统维护。虽然也可以编写其他程序，但我们的学习重点是为系统运维服务。

之前我们在RH124课程中已经学习过Shell的基本命令功能，接下来我们会将这些命令结合到脚本中应用。

## 变量基础

在编程语言中，变量是一个非常重要的概念。Shell脚本也可以被视为一门编程语言，因此理解变量至关重要。本节中我们来看看如何定义、赋值和使用变量。

![](img/5ae4abb74bde0990c7fc929aed6b85e2_1.png)

### 变量的定义与赋值

![](img/5ae4abb74bde0990c7fc929aed6b85e2_3.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_4.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_6.png)

在命令行中直接定义变量是局部变量，只在当前Shell会话中生效。定义格式为`变量名=变量值`。

![](img/5ae4abb74bde0990c7fc929aed6b85e2_8.png)

**示例代码：**
```bash
var1=value
echo $var1
```

![](img/5ae4abb74bde0990c7fc929aed6b85e2_10.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_12.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_14.png)

以下是定义变量时的五点注意事项：

![](img/5ae4abb74bde0990c7fc929aed6b85e2_16.png)

1.  **等号两边不能有空格**：`var1 = value` 是错误的，它会被解析为命令`var1`和参数`=`、`value`。
2.  **变量名不能以数字开头**：`1var=value` 是错误的。
3.  **变量名可以以下划线开头**：`_var=value` 是允许的。
4.  **变量值包含空格时需用引号括起来**：`var2="hello world"`。
5.  **可以将命令执行结果赋值给变量**：`var3=$(hostname)` 或 `var3=`hostname``。

![](img/5ae4abb74bde0990c7fc929aed6b85e2_18.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_20.png)

### 引号的区别

![](img/5ae4abb74bde0990c7fc929aed6b85e2_22.png)

单引号和双引号在变量引用时有重要区别：
*   **双引号**：会解析其中的变量（`$var`）和命令替换（`$(command)`），然后输出。
*   **单引号**：原样输出所有内容，不进行任何解析。

![](img/5ae4abb74bde0990c7fc929aed6b85e2_24.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_26.png)

**示例代码：**
```bash
name="World"
echo "Hello, $name"  # 输出：Hello, World
echo 'Hello, $name'  # 输出：Hello, $name
```

反引号 `` `command` `` 与 `$(command)` 功能相同，都用于命令替换，但推荐使用 `$(command)`，因为它更清晰且支持嵌套。

![](img/5ae4abb74bde0990c7fc929aed6b85e2_28.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_30.png)

## 变量类型与作用域

上一节我们介绍了变量的基本赋值，本节中我们来看看变量的作用域和值类型。

### 局部变量与全局变量

![](img/5ae4abb74bde0990c7fc929aed6b85e2_32.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_34.png)

*   **局部变量**：只在定义它的当前Shell进程中有效，其子进程无法访问。
*   **全局变量（环境变量）**：使用 `export` 命令可以将变量导出为环境变量，这样子进程就可以访问它。

![](img/5ae4abb74bde0990c7fc929aed6b85e2_36.png)

**示例代码：**
```bash
local_var="I am local"
export global_var="I am global"

![](img/5ae4abb74bde0990c7fc929aed6b85e2_38.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_40.png)

bash  # 启动一个子Shell
echo $local_var   # 输出为空
echo $global_var  # 输出：I am global
exit  # 退出子Shell
```

![](img/5ae4abb74bde0990c7fc929aed6b85e2_42.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_43.png)

### 变量值的类型

![](img/5ae4abb74bde0990c7fc929aed6b85e2_45.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_47.png)

Shell变量值主要分为字符串和数字（整数）。默认情况下，所有值都被视为字符串。

![](img/5ae4abb74bde0990c7fc929aed6b85e2_49.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_50.png)

**示例代码：**
```bash
num1=1
num2=1
echo $num1+$num2  # 输出：1+1 （字符串拼接）
```

要进行数学运算，需要显式声明或使用特定语法：

![](img/5ae4abb74bde0990c7fc929aed6b85e2_52.png)

1.  **使用 `declare -i` 声明整数变量**
    ```bash
    declare -i num3=1
    declare -i num4=1
    declare -i sum1=num3+num4
    echo $sum1  # 输出：2
    ```

![](img/5ae4abb74bde0990c7fc929aed6b85e2_54.png)

2.  **使用双括号 `$(( ))` 进行算术运算**
    ```bash
    sum2=$(( 1 + 1 ))
    echo $sum2  # 输出：2
    ```

![](img/5ae4abb74bde0990c7fc929aed6b85e2_56.png)

3.  **使用 `let` 命令**
    ```bash
    let sum3=1+1
    echo $sum3  # 输出：2
    ```

![](img/5ae4abb74bde0990c7fc929aed6b85e2_58.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_60.png)

这三种方法都能实现简单的整数运算。

![](img/5ae4abb74bde0990c7fc929aed6b85e2_62.png)

## 文本处理工具

![](img/5ae4abb74bde0990c7fc929aed6b85e2_64.png)

在脚本中，我们经常需要处理文本数据来获取信息。接下来我们介绍两个基础的文本处理工具：`grep` 和 `cut`。

### grep：文本搜索工具 🕵️

![](img/5ae4abb74bde0990c7fc929aed6b85e2_66.png)

`grep` 用于在文件中搜索包含指定模式（字符串或正则表达式）的行。

以下是 `grep` 的一些常用选项：

*   **基本搜索**：`grep "pattern" filename`
*   **忽略大小写**：`grep -i "pattern" filename`
*   **显示匹配行及其后n行**：`grep -A n "pattern" filename`
*   **显示匹配行及其前n行**：`grep -B n "pattern" filename`
*   **反向选择，输出不匹配的行**：`grep -v "pattern" filename`
*   **使用扩展正则表达式，匹配多个模式**：`grep -E "pattern1|pattern2" filename` 或 `egrep "pattern1|pattern2" filename`

**示例代码：**
```bash
# 在 /etc/passwd 中搜索包含 "root" 的行
grep "root" /etc/passwd

# 不区分大小写搜索 "ROOT"
grep -i "ROOT" /etc/passwd

![](img/5ae4abb74bde0990c7fc929aed6b85e2_68.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_70.png)

# 搜索 "root" 并显示其后2行
grep -A 2 "root" /etc/passwd

![](img/5ae4abb74bde0990c7fc929aed6b85e2_72.png)

# 搜索既不包含 "root" 也不包含 "nologin" 的行
grep -v -e "root" -e "nologin" /etc/passwd
# 或使用 egrep
egrep -v "root|nologin" /etc/passwd
```

### cut：列截取工具 ✂️

`cut` 命令用于从文件的每一行中截取部分内容（列）。它通常以特定的分隔符（如冒号、制表符）来划分字段。

![](img/5ae4abb74bde0990c7fc929aed6b85e2_74.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_76.png)

**常用语法**：`cut -d '分隔符' -f 字段列表 filename`

![](img/5ae4abb74bde0990c7fc929aed6b85e2_78.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_79.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_80.png)

**示例代码：**
```bash
# 以冒号为分隔符，输出 /etc/passwd 文件的第1、3、7列（用户名，UID，登录Shell）
cut -d ':' -f 1,3,7 /etc/passwd
```

![](img/5ae4abb74bde0990c7fc929aed6b85e2_82.png)

**`cut` 的局限性**：它只能识别单个字符作为分隔符，并且对于连续的分隔符（如`a::b`）或非标准格式的文本处理能力有限。例如：
```bash
echo "a  b  c" > test.txt  # 两个空格作为间隔
cut -d ' ' -f 2 test.txt   # 可能无法正确输出 'b'，因为分隔符是单个空格
```
对于更复杂的文本处理，我们将在后续介绍功能更强大的 `awk` 工具。

![](img/5ae4abb74bde0990c7fc929aed6b85e2_84.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_86.png)

![](img/5ae4abb74bde0990c7fc929aed6b85e2_88.png)

## 总结

![](img/5ae4abb74bde0990c7fc929aed6b85e2_90.png)

本节课中我们一起学习了Shell脚本的基础入门知识。我们首先了解了变量的定义、赋值规则以及引号的使用区别。接着，我们探讨了局部变量与全局变量的作用域，以及如何让变量进行数学运算。最后，我们初步认识了两个重要的文本处理工具：用于搜索过滤的 `grep` 和用于按列提取的 `cut`。掌握这些基础知识是编写高效自动化运维脚本的第一步。