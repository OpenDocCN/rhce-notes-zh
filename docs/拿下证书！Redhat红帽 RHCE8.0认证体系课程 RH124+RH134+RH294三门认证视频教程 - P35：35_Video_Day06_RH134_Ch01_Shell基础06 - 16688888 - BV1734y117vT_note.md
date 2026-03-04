# Red Hat RHCE 8.0 认证体系课程：RH134：Ch01_Shell基础06

![](img/19a132c7165360c53529d0cba67a641b_0.png)

![](img/19a132c7165360c53529d0cba67a641b_0.png)

在本节课程中，我们将学习Shell脚本中的条件结构、循环控制语句以及脚本调试方法。这些是编写复杂、健壮脚本的核心技能。

## 使用条件结构 🧠

上一节我们介绍了循环结构，本节中我们来看看如何使用条件结构进行决策。条件结构允许在Shell脚本中包含决策逻辑，以便仅在满足特定条件时才执行脚本的特定部分。

![](img/19a132c7165360c53529d0cba67a641b_2.png)

有一类命令会根据条件来跳过或执行某些命令，这类命令被称为结构化命令。结构化命令允许你改变程序执行的顺序。

### if-then 基础结构

![](img/19a132c7165360c53529d0cba67a641b_4.png)

最简单的条件结构是 `if-then`。其基本语法如下：

```bash
if command
then
    commands
fi
```

![](img/19a132c7165360c53529d0cba67a641b_6.png)

![](img/19a132c7165360c53529d0cba67a641b_7.png)

如果 `if` 后面的命令成功执行（退出状态码为0），则执行 `then` 后面的命令块，最后以 `fi` 结束。这与许多编程语言不同：Bash Shell的 `if` 语句执行的是其后的命令，并根据该命令的退出状态码（是否为0）来判断条件真假，而非判断一个等式的布尔值。

我们来看两个例子。

![](img/19a132c7165360c53529d0cba67a641b_9.png)

**示例1：命令执行成功**
```bash
if ls /tmp
then
    echo “Command works.”
fi
```
因为 `ls /tmp` 命令成功执行，所以会输出 “Command works.”。

![](img/19a132c7165360c53529d0cba67a641b_10.png)

![](img/19a132c7165360c53529d0cba67a641b_12.png)

**示例2：命令执行失败**
```bash
if lss /tmp  # 一个不存在的命令
then
    echo “This will not be printed.”
fi
```
因为 `lss` 命令不存在，执行失败，`then` 块中的命令将被跳过。

![](img/19a132c7165360c53529d0cba67a641b_14.png)

### 在条件判断中使用测试

![](img/19a132c7165360c53529d0cba67a641b_16.png)

![](img/19a132c7165360c53529d0cba67a641b_18.png)

之前的数值比较、字符串比较和文件测试经常用于 `if` 语句中进行条件判断。以下是一个判断服务状态的脚本示例：

```bash
#!/bin/bash
# 检查httpd服务是否运行
if systemctl is-active httpd > /dev/null
then
    echo “httpd service is running.”
else
    echo “httpd service is not running. Starting it...”
    systemctl start httpd
    systemctl status httpd
fi
```
这个脚本可以用于监控所需服务，如果服务未启动则自动启动它。

![](img/19a132c7165360c53529d0cba67a641b_20.png)

![](img/19a132c7165360c53529d0cba67a641b_22.png)

在 `if-then` 语句的 `then` 部分，可以列出多条命令。Bash Shell会将这些命令视为一个代码块。如果 `if` 语句的退出状态码为0，这个块中的所有命令都会被执行；如果不为0，则整个块都会被跳过。

![](img/19a132c7165360c53529d0cba67a641b_23.png)

![](img/19a132c7165360c53529d0cba67a641b_25.png)

![](img/19a132c7165360c53529d0cba67a641b_27.png)

以下是判断用户是否存在并执行多条命令的例子：
```bash
#!/bin/bash
if grep “tc” /etc/passwd > /dev/null
then
    echo “User tc exists.”
    ls -la /home/tc/
fi
```

![](img/19a132c7165360c53529d0cba67a641b_28.png)

### 判断输入是否为纯数字

以下脚本判断用户输入是否为纯数字（0-9）：
```bash
#!/bin/bash
read -p “Enter a number: “ num
if echo “$num” | grep -q ‘^[0-9]*$’
then
    echo “$num is a pure number.”
fi
```
其中 `grep -q` 选项用于静默模式，不显示匹配结果。

## 扩展条件结构：if-else 与 elif 🔄

在 `if-then` 语句中，无论命令是否成功执行，都只有一种选择。`if-else` 结构可以扩展这一功能，当条件不满足时执行另一套命令。

语法如下：
```bash
if command
then
    commands
else
    commands
fi
```

![](img/19a132c7165360c53529d0cba67a641b_30.png)

我们可以进一步嵌套，使用 `elif` (else if) 来处理多个条件。

![](img/19a132c7165360c53529d0cba67a641b_31.png)

![](img/19a132c7165360c53529d0cba67a641b_33.png)

```bash
if command1
then
    commands
elif command2
then
    commands
else
    commands
fi
```

![](img/19a132c7165360c53529d0cba67a641b_35.png)

以下是一个检查用户是否存在，若不存在则检查其家目录，并根据情况创建用户的例子：
```bash
#!/bin/bash
username=“testuser”
if grep “$username” /etc/passwd > /dev/null
then
    echo “User $username exists.”
elif [ -d “/home/$username” ]
then
    echo “Home directory exists but user does not. Creating user...”
    useradd $username
    grep “$username” /etc/passwd
else
    echo “User and home directory do not exist. Creating both...”
    useradd -m $username
fi
```
请注意，此处的 `else` 属于最外层 `if` 代码块。

虽然可以串联多个 `elif`，但过多的分支会使代码难以阅读和维护。这时，`case` 命令是更好的选择。

![](img/19a132c7165360c53529d0cba67a641b_37.png)

![](img/19a132c7165360c53529d0cba67a641b_39.png)

![](img/19a132c7165360c53529d0cba67a641b_40.png)

## 使用 case 命令进行多分支选择 📂

![](img/19a132c7165360c53529d0cba67a641b_41.png)

`case` 命令采用列表形式来检查单个变量的多个值，无需编写多个 `if` 语句。它会将变量与不同模式进行比较，如果匹配则执行对应的命令块。

![](img/19a132c7165360c53529d0cba67a641b_43.png)

基本语法如下：
```bash
case variable in
pattern1 | pattern2)
    commands
    ;;
pattern3)
    commands
    ;;
*)
    default commands
    ;;
esac
```
*   每个模式分支以 `)` 结束。
*   同一行可以用 `|` 分隔多个模式。
*   命令块以两个分号 `;;` 结束，代表该分支结束并跳出 `case`。
*   `*` 模式匹配所有其他情况，作为默认分支。
*   整个结构以 `esac` (`case` 的反写) 结束。

![](img/19a132c7165360c53529d0cba67a641b_45.png)

![](img/19a132c7165360c53529d0cba67a641b_47.png)

以下是一个判断输入字符类型的例子：
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

## 循环控制：break 与 continue ⏹️➡️

![](img/19a132c7165360c53529d0cba67a641b_49.png)

在循环中，有时需要提前终止循环或跳过当前迭代，这时可以使用 `break` 和 `continue` 语句。

![](img/19a132c7165360c53529d0cba67a641b_50.png)

![](img/19a132c7165360c53529d0cba67a641b_52.png)

![](img/19a132c7165360c53529d0cba67a641b_53.png)

### break 语句

![](img/19a132c7165360c53529d0cba67a641b_55.png)

![](img/19a132c7165360c53529d0cba67a641b_57.png)

`break` 语句用于立即终止整个循环的执行，并继续执行循环体之后的代码。

![](img/19a132c7165360c53529d0cba67a641b_59.png)

**跳出单层循环示例：**
```bash
#!/bin/bash
a=1
while [ $a -le 5 ]
do
    if [ $a -eq 3 ]
    then
        break
    fi
    echo “a = $a”
    a=$((a+1))
done
```
当 `a` 等于3时，循环终止，输出为：
```
a = 1
a = 2
```

**跳出指定层数循环 (`break n`)：**
`break n` 可以跳出多层嵌套循环，其中 `n` 指定要跳出的循环层数。
```bash
#!/bin/bash
for (( i=1; i<=3; i++ ))
do
    echo “Outer loop: $i”
    for (( j=1; j<=3; j++ ))
    do
        if [ $j -eq 2 ]
        then
            break 2  # 跳出两层循环
        fi
        echo “    Inner loop: $j”
    done
done
```
当内层循环 `j` 等于2时，`break 2` 会直接终止外层和内层循环。

### continue 语句

![](img/19a132c7165360c53529d0cba67a641b_61.png)

![](img/19a132c7165360c53529d0cba67a641b_63.png)

`continue` 语句用于跳过当前循环迭代中剩余的语句，直接进入下一次循环迭代。

**跳过特定迭代示例：**
```bash
#!/bin/bash
for var in {1..5}
do
    if [ $var -le 3 ]
    then
        continue
    fi
    echo “Number: $var”
done
```
此脚本会跳过数字1、2、3，只输出4和5。

![](img/19a132c7165360c53529d0cba67a641b_65.png)

![](img/19a132c7165360c53529d0cba67a641b_67.png)

**找出余数不为0的数字（模拟找非偶数）：**
```bash
#!/bin/bash
for num in {1..5}
do
    remainder=$(( num % 2 ))
    if [ $remainder -eq 0 ]
    then
        continue
    fi
    echo “$num has a remainder.”
done
```
此脚本会输出余数不为0（即奇数）的数字：1, 3, 5。

## Shell脚本调试与良好实践 🐛

脚本错误通常由输入错误、语法错误或逻辑不佳导致。遵循良好实践可以极大提升脚本的可读性和可维护性。

### 良好编码实践

以下是编写Shell脚本的一些良好做法：

![](img/19a132c7165360c53529d0cba67a641b_69.png)

![](img/19a132c7165360c53529d0cba67a641b_71.png)

![](img/19a132c7165360c53529d0cba67a641b_72.png)

*   **使用注释**：在脚本顶部说明用途、预期操作和逻辑。在复杂部分添加注释，帮助他人和自己日后理解。
*   **结构清晰**：
    *   将长命令分成多行。
    *   对齐控制结构（如 `if` 和 `fi`）的开始和结束位置。
    *   使用缩进来表示代码逻辑的层次结构。
    *   用空行分隔不同的代码块。
    *   在整个脚本中保持一致的格式。

![](img/19a132c7165360c53529d0cba67a641b_74.png)

![](img/19a132c7165360c53529d0cba67a641b_76.png)

### 调试模式

![](img/19a132c7165360c53529d0cba67a641b_78.png)

![](img/19a132c7165360c53529d0cba67a641b_80.png)

Bash Shell提供了几种调试脚本的方式：

![](img/19a132c7165360c53529d0cba67a641b_82.png)

![](img/19a132c7165360c53529d0cba67a641b_83.png)

1.  **详细模式 (`-v`)**：在执行前将每条命令打印到标准输出。
    ```bash
    bash -v script.sh
    ```

![](img/19a132c7165360c53529d0cba67a641b_85.png)

2.  **检查语法 (`-n`)**：只检查语法错误，不执行脚本。
    ```bash
    bash -n script.sh
    ```

![](img/19a132c7165360c53529d0cba67a641b_86.png)

![](img/19a132c7165360c53529d0cba67a641b_88.png)

![](img/19a132c7165360c53529d0cba67a641b_90.png)

3.  **跟踪模式 (`-x`)**：在执行时显示命令及其参数，是强大的调试工具。
    ```bash
    bash -x script.sh
    ```

![](img/19a132c7165360c53529d0cba67a641b_92.png)

4.  **局部调试**：可以在脚本内部使用 `set -x` 开启调试，用 `set +x` 关闭调试，只对特定代码段进行跟踪。
    ```bash
    #!/bin/bash
    echo “Normal output”
    set -x  # 开启调试
    echo “Debugging this line”
    ls /tmp
    set +x  # 关闭调试
    echo “Back to normal output”
    ```

![](img/19a132c7165360c53529d0cba67a641b_94.png)

![](img/19a132c7165360c53529d0cba67a641b_96.png)

---

![](img/19a132c7165360c53529d0cba67a641b_98.png)

![](img/19a132c7165360c53529d0cba67a641b_99.png)

![](img/19a132c7165360c53529d0cba67a641b_101.png)

本节课中我们一起学习了Shell脚本编程的核心控制结构。我们掌握了如何使用 `if-then-else` 和 `case` 进行条件分支判断，如何使用 `break` 和 `continue` 控制循环流程，并了解了脚本调试的方法和编写可维护脚本的良好实践。这些是构建自动化任务和复杂系统管理脚本的坚实基础。