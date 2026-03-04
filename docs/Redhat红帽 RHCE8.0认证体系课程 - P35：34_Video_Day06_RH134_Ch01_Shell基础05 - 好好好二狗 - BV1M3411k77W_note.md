# Redhat红帽 RHCE8.0认证体系课程：P35：Shell基础05

在本节课中，我们将要学习Shell脚本中的测试表达式。我们将深入探讨如何使用`test`命令及其语法来检查条件，包括数字比较、字符串比较、文件测试以及逻辑运算符的使用。掌握这些知识是编写健壮Shell脚本的基础。

## 测试脚本输入

上一节我们介绍了退出代码，本节中我们来看看如何使用`test`命令来测试脚本输入。`test`命令用于评估一个表达式，并在完成后生成一个退出代码，该代码存储在`$?`变量中。如果测试成功（条件为真），则返回值为0；如果测试失败（条件为假），则返回值为非零。

测试表达式的基本语法是：
```bash
test expression
```
或者使用方括号：
```bash
[ expression ]
```
注意，使用方括号时，表达式前后必须留有空格。

## 数字比较

![](img/d7ef74d3eeb2beb889e036d2a1e23074_1.png)

数字比较仅限于整数。以下是用于整数比较的二进制运算符：

![](img/d7ef74d3eeb2beb889e036d2a1e23074_3.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_5.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_7.png)

以下是数字比较运算符列表：
*   `-eq`：等于（equals）
*   `-ne`：不等于（not equals）
*   `-gt`：大于（greater than）
*   `-ge`：大于等于（greater equals）
*   `-lt`：小于（less than）
*   `-le`：小于等于（less than equals）

![](img/d7ef74d3eeb2beb889e036d2a1e23074_9.png)

让我们通过一些例子来理解。请注意，浮点数不能用于这些比较。

![](img/d7ef74d3eeb2beb889e036d2a1e23074_11.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_12.png)

```bash
# 错误的示范：尝试比较浮点数
[ 3.14 -eq 3.14 ]
echo $? # 输出非零，因为仅支持整数

# 正确的示范：比较整数
[ 1 -eq 1 ]
echo $? # 输出 0，表示测试成功（为真）

[ 1 -ne 1 ]
echo $? # 输出 1，表示测试失败（为假）

![](img/d7ef74d3eeb2beb889e036d2a1e23074_14.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_15.png)

[ 2 -ne 3 ]
echo $? # 输出 0

![](img/d7ef74d3eeb2beb889e036d2a1e23074_17.png)

[ 2 -gt 1 ]
echo $? # 输出 0

![](img/d7ef74d3eeb2beb889e036d2a1e23074_19.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_21.png)

[ 2 -ge 2 ]
echo $? # 输出 0

![](img/d7ef74d3eeb2beb889e036d2a1e23074_23.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_25.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_27.png)

[ 2 -lt 2 ]
echo $? # 输出 1

![](img/d7ef74d3eeb2beb889e036d2a1e23074_29.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_31.png)

[ 2 -le 2 ]
echo $? # 输出 0

![](img/d7ef74d3eeb2beb889e036d2a1e23074_33.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_35.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_37.png)

[ 1 -le 2 ]
echo $? # 输出 0

![](img/d7ef74d3eeb2beb889e036d2a1e23074_39.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_41.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_43.png)

[ 1 -ge 2 ]
echo $? # 输出 1
```
除了使用`-gt`这类运算符，也可以使用双括号`(( ))`进行算术比较，两者效果相同。
```bash
(( 1 == 1 ))
echo $? # 输出 0
(( 1 > 2 ))
echo $? # 输出 1
```

![](img/d7ef74d3eeb2beb889e036d2a1e23074_45.png)

## 字符串比较

![](img/d7ef74d3eeb2beb889e036d2a1e23074_47.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_49.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_51.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_52.png)

字符串比较主要使用以下三种运算符：

![](img/d7ef74d3eeb2beb889e036d2a1e23074_54.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_56.png)

以下是字符串比较运算符列表：
*   `=`：等于
*   `==`：恒等于（与`=`通常相同）
*   `!=`：不等于

![](img/d7ef74d3eeb2beb889e036d2a1e23074_58.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_59.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_61.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_62.png)

让我们看一些例子：
```bash
[ "abc" = "abc" ]
echo $? # 输出 0

![](img/d7ef74d3eeb2beb889e036d2a1e23074_64.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_65.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_67.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_69.png)

[ "abc" == "abc" ]
echo $? # 输出 0

![](img/d7ef74d3eeb2beb889e036d2a1e23074_71.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_72.png)

[ "abc" = "ab" ]
echo $? # 输出 1

![](img/d7ef74d3eeb2beb889e036d2a1e23074_74.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_76.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_78.png)

[ "abc" != "ab" ]
echo $? # 输出 0
```
通常，我们习惯使用两个等号`==`进行比较。

![](img/d7ef74d3eeb2beb889e036d2a1e23074_79.png)

在进行字符串大小比较（使用`>`或`<`）时，必须注意转义这些符号，否则它们会被Shell解释为重定向操作符。

![](img/d7ef74d3eeb2beb889e036d2a1e23074_81.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_83.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_85.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_86.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_87.png)

```bash
# 必须对 > 和 < 进行转义
[ "basketball" \> "football" ]
```
字符串比较的结果基于字符的ASCII码值顺序，而非字母表顺序或字符串长度。例如，比较`"basketball"`和`"football"`时，是比较首字符`'b'`和`'f'`的ASCII码（98 和 102），因此`"basketball"`小于`"football"`。

![](img/d7ef74d3eeb2beb889e036d2a1e23074_88.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_90.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_92.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_94.png)

这一点与`sort`命令的排序规则不同，`sort`通常根据本地化语言设置进行排序，可能让小写字母优先于大写字母。

![](img/d7ef74d3eeb2beb889e036d2a1e23074_96.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_98.png)

## 测试文件和目录

![](img/d7ef74d3eeb2beb889e036d2a1e23074_100.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_102.png)

在与文件和目录交互时，进行测试是良好的做法。Bash Shell提供了丰富的运算符来测试文件属性。

![](img/d7ef74d3eeb2beb889e036d2a1e23074_104.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_105.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_107.png)

以下是常用的文件测试运算符列表：
*   `-b file`：文件存在且为块设备文件。
*   `-c file`：文件存在且为字符设备文件。
*   `-d file`：文件存在且为目录。
*   `-e file`：文件是否存在。
*   `-f file`：文件存在且为常规文件。
*   `-L file`：文件存在且为符号链接。
*   `-r file`：文件存在且可读。
*   `-s file`：文件存在且大小大于零。
*   `-w file`：文件存在且可写。
*   `-x file`：文件存在且可执行。

![](img/d7ef74d3eeb2beb889e036d2a1e23074_109.png)

还有一些二进制比较运算符，如`-nt`（newer than，修改时间更晚）和`-ot`（older than，修改时间更早），但不太常用。

记住，测试表达式中的空格是语法必需，而不仅仅是为了美观。缺少空格会导致测试失败或产生意外结果。

例子：
```bash
# 检查 /dev/nvme0n1p1 是否为块设备
[ -b /dev/nvme0n1p1 ]
echo $?

# 检查 /dev/sda 是否为字符设备
[ -c /dev/sda ]
echo $?

![](img/d7ef74d3eeb2beb889e036d2a1e23074_111.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_113.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_115.png)

# 检查当前目录是否存在
[ -d . ]
echo $?

![](img/d7ef74d3eeb2beb889e036d2a1e23074_117.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_119.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_121.png)

# 检查 /etc/passwd 是否为常规文件且存在
[ -f /etc/passwd ]
echo $?
```

![](img/d7ef74d3eeb2beb889e036d2a1e23074_123.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_125.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_126.png)

## 逻辑运算符（复合测试）

![](img/d7ef74d3eeb2beb889e036d2a1e23074_128.png)

逻辑运算符允许我们将多个测试条件组合起来。主要逻辑运算符是“与”和“或”。

**逻辑与 (AND)** 的格式是：
```bash
command1 && command2
```
或者使用`-a`：
```bash
[ condition1 -a condition2 ]
```
说明：只有`command1`（或`condition1`）返回真（退出码为0）时，`command2`（或`condition2`）才会被执行。只要任何一个命令或条件为假，整个表达式的结果就为假。

**逻辑或 (OR)** 的格式是：
```bash
command1 || command2
```
或者使用`-o`：
```bash
[ condition1 -o condition2 ]
```
说明：只有`command1`（或`condition1`）返回假（退出码非0）时，`command2`（或`condition2`）才会被执行。只要任何一个命令或条件为真，整个表达式的结果就为真。这被称为“短路逻辑”。

![](img/d7ef74d3eeb2beb889e036d2a1e23074_130.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_131.png)

例子：
```bash
# 逻辑与
[ 1 -eq 1 ] && [ 2 -eq 2 ]
echo $? # 输出 0，两个都为真

[ 1 -eq 1 -a 2 -eq 3 ]
echo $? # 输出 1，第二个为假

![](img/d7ef74d3eeb2beb889e036d2a1e23074_133.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_135.png)

# 逻辑或
[ 1 -eq 1 ] || [ 2 -eq 3 ]
echo $? # 输出 0，第一个为真，短路，第二个不执行

[ 1 -eq 2 ] || [ 2 -eq 2 ]
echo $? # 输出 0，第一个为假，执行第二个，第二个为真

![](img/d7ef74d3eeb2beb889e036d2a1e23074_137.png)

![](img/d7ef74d3eeb2beb889e036d2a1e23074_139.png)

[ 1 -eq 2 -o 2 -eq 3 ]
echo $? # 输出 1，两个都为假
```
逻辑运算符可以用于构建复杂的条件判断，例如在脚本中检查文件属性：
```bash
#!/bin/bash
if [ -d "$HOME" ] && [ -w "$HOME/testfile" ]; then
    echo "目录存在且文件可写"
else
    echo "条件不满足"
fi
```

![](img/d7ef74d3eeb2beb889e036d2a1e23074_141.png)

本节课中我们一起学习了Shell脚本中测试表达式的核心知识。我们掌握了如何使用`test`命令进行数字和字符串比较，理解了比较时的注意事项（如整数限制、字符串转义和ASCII码比较）。我们还学习了如何测试文件和目录的各种属性，以及如何使用逻辑运算符`&&`、`||`、`-a`、`-o`来组合多个条件，形成复杂的复合测试逻辑。这些是编写条件判断和流程控制脚本的基石，请务必通过练习加以巩固。