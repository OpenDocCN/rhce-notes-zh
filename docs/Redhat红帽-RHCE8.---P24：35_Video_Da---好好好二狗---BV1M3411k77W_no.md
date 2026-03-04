# Redhat红帽 RHCE8.0认证体系课程：P24：Shell基础06 🐚

在本节课中，我们将要学习Shell脚本中的条件结构、分支控制以及调试技巧。这是编写复杂脚本的基础，能让你根据不同的情况执行不同的命令。

---

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_1.png)

## 使用条件结构

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_3.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_5.png)

上一节我们介绍了循环结构，本节中我们来看看如何让脚本根据条件做出决策。条件结构允许在Shell脚本中包含决策逻辑，以便仅在满足特定条件时才执行脚本的某些部分。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_7.png)

有一类命令会根据条件跳过某些命令，这类命令被称为**结构化命令**。结构化命令允许你改变程序执行的顺序。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_9.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_10.png)

### if-then 基础结构

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_12.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_14.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_15.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_17.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_18.png)

最简单的条件结构是 `if-then`。其基本语法如下：

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_19.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_20.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_21.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_22.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_23.png)

```bash
if command
then
    commands
fi
```

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_25.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_27.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_28.png)

在Bash Shell中，`if` 语句会运行其后的命令。**关键点在于**：如果该命令的退出状态码是 `0`（表示成功执行），那么 `then` 部分的命令才会被执行。如果退出状态码是其他值，`then` 后面的部分就会被跳过，脚本会继续执行 `fi` 之后的下一条命令。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_29.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_31.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_32.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_33.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_34.png)

这与许多其他编程语言不同。在Shell中，`if` 判断的是**命令是否成功执行**，而不是一个表达式的布尔值。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_35.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_36.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_37.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_39.png)

以下是几个例子：

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_41.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_42.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_44.png)

**示例1：命令执行成功**
```bash
if ls /tmp
then
    echo "The command works."
fi
```
因为 `ls /tmp` 命令成功执行（退出码为0），所以会输出 “The command works.”。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_46.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_48.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_49.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_50.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_51.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_52.png)

**示例2：命令执行失败**
```bash
if lss /tmp  # 这是一个不存在的命令
then
    echo "This will not be printed."
fi
```
因为 `lss` 命令不存在，执行失败（退出码非0），所以 `then` 块内的 `echo` 命令不会执行。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_53.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_54.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_56.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_58.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_59.png)

**示例3：监控服务状态**
我们可以编写一个脚本来检查服务（如httpd）是否运行，如果没有运行则启动它。
```bash
#!/bin/bash
if ! systemctl is-active --quiet httpd
then
    echo "HTTPD service is not active. Starting it now..."
    systemctl start httpd
    systemctl status httpd
fi
```
这个脚本判断httpd服务是否活跃，如果不活跃，则启动它并显示状态。这可以用于简单的服务监控。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_61.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_62.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_63.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_65.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_66.png)

### 在 then 部分执行多条命令

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_68.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_70.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_71.png)

在 `then` 部分，可以像脚本其他地方一样列出多条命令。Bash Shell会将这些命令视为一个代码块。如果 `if` 语句的退出状态码为0，这个块中的所有命令都会被执行；如果不为0，则整个块都会被跳过。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_73.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_75.png)

**示例：检查用户并列出家目录**
```bash
#!/bin/bash
if grep -q "^tc:" /etc/passwd
then
    echo “User tc exists.”
    ls -la /home/tc/
fi
```
这个脚本检查用户 `tc` 是否存在。如果存在，则输出一条消息并列出其家目录的内容。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_77.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_78.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_79.png)

### 测试数值与字符串

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_81.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_83.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_85.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_87.png)

之前的数值比较、字符串比较和文件测试操作符，经常在 `if` 语句中用于测试条件。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_89.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_90.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_91.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_93.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_94.png)

**示例：判断输入是否为纯数字**
```bash
#!/bin/bash
read -p “Please enter a number: “ input
if echo “$input” | grep -q ‘^[0-9]*$’
then
    echo “You entered a pure number.”
else
    echo “Your input contains non-digit characters.”
fi
```
脚本使用 `grep` 和正则表达式 `^[0-9]*$` 来判断输入是否只包含数字0-9。`-q` 选项让 `grep` 不显示匹配结果。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_96.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_97.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_98.png)

---

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_100.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_102.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_103.png)

## 扩展条件分支：if-then-else 与 elif

`if-then` 结构在命令失败时只有一种选择：跳过。使用 `if-then-else` 结构可以在命令返回非零状态码时，执行 `else` 部分的命令。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_105.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_106.png)

**基础语法：**
```bash
if command
then
    commands
else
    commands
fi
```

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_107.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_109.png)

### 使用 elif 进行多重判断

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_111.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_113.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_115.png)

有时需要进行多重条件判断，这时可以使用 `elif`（即 else if）。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_117.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_119.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_121.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_123.png)

**示例：检查用户及其家目录**
```bash
#!/bin/bash
username=“testuser”

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_124.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_126.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_128.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_130.png)

if grep -q “^$username:” /etc/passwd
then
    echo “User $username exists.”
elif [ -d “/home/$username” ]
then
    echo “Home directory for $username exists, but user does not. Creating user...”
    useradd $username
    grep “^$username:” /etc/passwd
else
    echo “User $username does not exist and has no home directory.”
fi
```
这个脚本首先检查用户是否存在。如果存在，输出信息。如果不存在，则检查是否有该用户的家目录。如果有目录，则创建用户。如果两者都没有，则输出相应信息。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_132.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_134.png)

**注意**：此处的 `else` 属于最外层 `if` 代码块。当使用多个 `elif` 时，只有第一个返回退出状态码为0的 `if` 或 `elif` 块会被执行。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_136.png)

---

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_138.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_139.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_140.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_142.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_144.png)

## 多分支选择：case 语句

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_146.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_148.png)

当需要对同一个变量进行多个值的匹配检查时，使用多个 `if-elif` 会显得冗长。`case` 命令提供了一种更清晰的多分支选择方式。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_150.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_152.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_153.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_155.png)

`case` 命令将指定的变量与不同的模式进行比较。如果变量与某个模式匹配，Shell就会执行与该模式关联的命令。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_157.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_159.png)

**基础语法：**
```bash
case variable in
pattern1 | pattern2)
    command1
    ;;
pattern3)
    command2
    ;;
*)
    default_command
    ;;
esac
```
*   每个模式分支以 `)` 结束。
*   用 `|` 分隔多个模式，表示“或”。
*   命令块以两个分号 `;;` 结束，这表示该分支的终止，并跳出 `case` 语句。
*   `*)` 模式匹配所有其他情况，作为默认分支。
*   整个结构以 `esac`（case的反写）结束。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_161.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_163.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_165.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_167.png)

**示例：判断输入字符类型**
```bash
#!/bin/bash
read -p “Press a key: “ key
case “$key” in
    [a-z]|[A-Z])
        echo “You pressed a letter.”
        ;;
    [0-9])
        echo “You pressed a digit.”
        ;;
    *)
        echo “You pressed a special character or other.”
        ;;
esac
```
这个脚本根据用户输入的单个字符，判断它是字母、数字还是其他特殊字符。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_169.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_171.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_172.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_174.png)

---

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_176.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_178.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_180.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_182.png)

## 循环控制：break 与 continue

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_183.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_184.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_186.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_187.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_188.png)

在循环中，有时需要提前终止循环或跳过当前迭代，这时可以使用 `break` 和 `continue` 命令。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_189.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_191.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_193.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_194.png)

### break 命令

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_196.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_198.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_200.png)

`break` 命令用于立即终止整个循环的执行，跳出循环体。它可以用于 `for`、`while`、`until` 循环。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_202.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_203.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_205.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_206.png)

**示例1：跳出单层循环**
```bash
#!/bin/bash
a=1
while [ $a -lt 10 ]
do
    echo $a
    if [ $a -eq 5 ]
    then
        break
    fi
    a=$((a+1))
done
```
当变量 `a` 等于5时，执行 `break`，循环终止。输出结果为1到5。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_208.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_209.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_210.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_212.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_213.png)

**示例2：跳出多层循环**
`break` 后面可以跟一个数字 `n`，表示跳出 `n` 层循环。
```bash
#!/bin/bash
for (( a=1; a<=5; a++ ))
do
    echo “Outer loop: $a”
    for (( b=1; b<=5; b++ ))
    do
        if [ $b -eq 3 ]
        then
            break 2  # 跳出2层循环
        fi
        echo “   Inner loop: $b”
    done
done
```
当内层循环变量 `b` 等于3时，`break 2` 会同时跳出内层和外层循环，脚本直接结束。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_215.png)

### continue 命令

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_217.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_219.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_221.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_222.png)

`continue` 命令用于跳过当前循环迭代中剩余的语句，直接开始下一次迭代。它终止的是本次循环，而不是整个循环。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_224.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_226.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_228.png)

**示例：打印1-5中的奇数**
```bash
#!/bin/bash
for var in 1 2 3 4 5
do
    remainder=$((var % 2))
    if [ $remainder -eq 0 ]
    then
        continue  # 如果是偶数，跳过本次循环的剩余部分
    fi
    echo “$var is an odd number.”
done
```
脚本计算每个数字除以2的余数。如果余数为0（偶数），则执行 `continue`，跳过 `echo` 语句，直接进入下一次循环。因此，只有奇数会被打印出来。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_230.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_232.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_234.png)

---

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_236.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_238.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_240.png)

## Shell脚本调试与良好实践 🐞

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_242.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_243.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_245.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_247.png)

脚本错误通常由输入错误、语法错误或逻辑不佳导致。遵循良好实践和利用调试工具可以高效地排查问题。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_249.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_250.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_252.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_254.png)

### 脚本编写良好实践

以下是提升脚本可读性和可维护性的建议：

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_256.png)

*   **使用注释**：在脚本顶部说明用途、预期操作和总体逻辑。在关键部分，特别是容易令人困惑的地方，使用注释进行阐明。
*   **结构化代码**：
    *   将长命令分成更小的代码块。
    *   对齐控制结构（如 `if` 和 `fi`， `case` 和 `esac`）的开始和结束位置。
    *   对代码块进行缩进，以显示逻辑层次。
    *   使用空行分隔不同的功能模块。
    *   在整个脚本中保持一致的格式。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_258.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_260.png)

### 调试模式

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_262.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_264.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_266.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_267.png)

Bash Shell提供了几种调试脚本的模式。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_269.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_270.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_271.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_272.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_273.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_275.png)

1.  **语法检查模式 (`-n`)**
    此模式只检查脚本语法，而不执行命令。
    ```bash
    bash -n script.sh
    ```

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_277.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_278.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_280.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_282.png)

2.  **详细模式 (`-v`)**
    此模式在执行脚本前，先将每条命令打印到标准输出。
    ```bash
    bash -v script.sh
    ```

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_284.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_286.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_288.png)

3.  **跟踪模式 (`-x`)**
    此模式在命令执行时，显示命令及其参数，是强大的调试工具。
    ```bash
    bash -x script.sh
    ```

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_290.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_292.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_294.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_295.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_297.png)

**示例脚本 `debug1.sh`：**
```bash
#!/bin/bash
echo “Start of script.”
ls /tmp
echo “End of script.”
```
使用跟踪模式运行：`bash -x debug1.sh`，会显示每条命令的展开和执行过程。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_298.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_300.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_301.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_302.png)

### 局部调试

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_303.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_305.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_307.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_308.png)

可以使用 `set` 命令在脚本内部开启或关闭调试。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_310.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_312.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_314.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_316.png)

*   `set -x`：在当前位置开启跟踪。
*   `set +x`：在当前位置关闭跟踪。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_317.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_319.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_320.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_322.png)

**示例脚本 `debug2.sh`：**
```bash
#!/bin/bash
echo “This line is not traced.”
set -x  # 开启跟踪
echo “This line is traced.”
ls /tmp
set +x  # 关闭跟踪
echo “Back to normal execution.”
```
运行 `bash debug2.sh`，只有 `set -x` 和 `set +x` 之间的命令会显示跟踪信息。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_324.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_326.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_328.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_330.png)

---

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_331.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_332.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_334.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_336.png)

## 总结

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_338.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_340.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_342.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_343.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_345.png)

本节课中我们一起学习了Shell脚本编程的核心控制结构。
*   我们掌握了 `if-then`、`if-then-else` 和 `elif` 条件判断语句，理解了Shell中条件判断基于**命令退出状态码**的本质。
*   我们学习了 `case` 语句，用于清晰高效地处理多分支选择。
*   我们了解了 `break` 和 `continue` 命令，用于在循环中进行流程控制。
*   最后，我们探讨了编写可维护脚本的良好实践，并学习了使用 `-n`、`-v`、`-x` 选项以及 `set` 命令进行脚本调试的方法。

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_347.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_349.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_351.png)

![](img/f7ec3a0f15c6f23133b1b7ec4ea5d13a_352.png)

掌握这些知识，你将能够编写出逻辑清晰、健壮且易于调试的Shell脚本。