# Linux-RHCSA入门实战课：P13：RHCSA-第12课-shell脚本常用语句 🐧

![](img/f3df4c2caebf0e29a940832703af1706_0.png)

![](img/f3df4c2caebf0e29a940832703af1706_2.png)

## 概述
在本节课中，我们将要学习Shell脚本的基础知识，包括脚本的概念、变量、流程控制语句（如if、for、while、case）以及函数。Shell脚本是提升Linux运维效率的核心工具，通过将命令有逻辑地组织在一起，可以实现自动化任务。

---

## Shell脚本基础概念

上一节我们介绍了Linux环境变量和服务管理，本节中我们来看看提升效率的利器——Shell脚本。

Shell是用户与Linux内核之间的桥梁，它既是一个命令解释器，也是一个“壳”。用户通过命令行或图形界面输入的命令，由Shell传递给内核处理，再将结果返回给用户。我们最常用的是Bash Shell。

![](img/f3df4c2caebf0e29a940832703af1706_4.png)

Shell脚本的本质是将Linux命令进行有逻辑的排列组合，并保存到以`.sh`结尾的文件中。它类似于Windows中的批处理文件（`.bat`），目的是自动化执行任务，提升工作效率。

### 脚本标准化规范
在大型企业中，脚本的编写有严格的标准化要求，这有助于团队协作和后期维护。初学者应养成良好的习惯。

![](img/f3df4c2caebf0e29a940832703af1706_6.png)

![](img/f3df4c2caebf0e29a940832703af1706_8.png)

![](img/f3df4c2caebf0e29a940832703af1706_10.png)

以下是脚本标准化的几个要点：
*   **命名规则**：使用英文大小写字母和数字，不能使用特殊符号或空格。建议以`.sh`结尾。
*   **指定解释器**：脚本首行必须为 `#!/bin/bash`，用于指定使用Bash解释器执行此脚本。
*   **注释信息**：通常在脚本开头添加注释，说明脚本功能、作者、创建日期、联系方式等。

### 第一个Shell脚本：Hello World
创建一个简单的脚本是理解其工作原理的最佳方式。

![](img/f3df4c2caebf0e29a940832703af1706_12.png)

```bash
#!/bin/bash
# 这是一个简单的Hello World脚本
echo "Hello World"
```

![](img/f3df4c2caebf0e29a940832703af1706_14.png)

运行脚本有三种方式：
1.  使用 `bash` 或 `sh` 命令：`bash first_script.sh`
2.  赋予执行权限后直接运行：`chmod +x first_script.sh` 然后 `./first_script.sh`

![](img/f3df4c2caebf0e29a940832703af1706_16.png)

---

![](img/f3df4c2caebf0e29a940832703af1706_18.png)

![](img/f3df4c2caebf0e29a940832703af1706_19.png)

## Shell变量详解

![](img/f3df4c2caebf0e29a940832703af1706_21.png)

变量是Shell脚本中用于存储数据的容器，是脚本优化的基础之一。

![](img/f3df4c2caebf0e29a940832703af1706_23.png)

![](img/f3df4c2caebf0e29a940832703af1706_25.png)

### 变量类型
Shell变量主要分为三种：
1.  **系统变量**：由Shell或系统定义，用于脚本参数判断和命令返回值判断。
    *   `$0`：脚本名称。
    *   `$1, $2, $3...`：脚本的第1、2、3...个参数。
    *   `$*`：脚本的所有参数。
    *   `$#`：脚本参数的个数。
    *   `$?`：上一条命令的执行结果（0表示成功，非0表示失败）。
    *   `$$`：当前脚本的进程ID（PID）。
2.  **环境变量**：系统运行时需要的设置，如`PATH`, `HOME`。
3.  **用户变量（局部变量）**：用户自定义的变量。

![](img/f3df4c2caebf0e29a940832703af1706_27.png)

### 变量的定义与引用
定义变量使用等号`=`，注意等号两边不能有空格。引用变量需要在变量名前加美元符号`$`。

![](img/f3df4c2caebf0e29a940832703af1706_29.png)

```bash
#!/bin/bash
# 定义变量
name="Linux"
version=8
# 引用变量
echo "This is $name version $version."
# 使用单引号，变量不会被替换
echo 'This is $name version $version.'
```

### 变量操作示例
以下脚本演示了如何使用系统变量：

![](img/f3df4c2caebf0e29a940832703af1706_31.png)

![](img/f3df4c2caebf0e29a940832703af1706_33.png)

![](img/f3df4c2caebf0e29a940832703af1706_35.png)

```bash
#!/bin/bash
echo "Script name is: $0"
echo "Total parameters: $#"
echo "All parameters are: $*"
echo "The first parameter is: $1"
echo "The fifth parameter is: $5"
```

![](img/f3df4c2caebf0e29a940832703af1706_37.png)

运行脚本并传递参数：`./demo.sh A B 25 30`，观察输出结果。

---

## 流程控制语句

![](img/f3df4c2caebf0e29a940832703af1706_39.png)

![](img/f3df4c2caebf0e29a940832703af1706_41.png)

流程控制语句让脚本具备逻辑判断和循环执行的能力。

![](img/f3df4c2caebf0e29a940832703af1706_43.png)

![](img/f3df4c2caebf0e29a940832703af1706_45.png)

### 条件判断语句：if

if语句用于基于条件执行不同的代码块。它有单分支、双分支和多分支几种形式。

![](img/f3df4c2caebf0e29a940832703af1706_47.png)

**基本语法格式**：
```bash
if [ 条件表达式 ]; then
    # 条件为真时执行的命令
elif [ 其他条件表达式 ]; then
    # 其他条件为真时执行的命令
else
    # 所有条件都为假时执行的命令
fi
```

![](img/f3df4c2caebf0e29a940832703af1706_49.png)

**示例：比较数字大小**
```bash
#!/bin/bash
read -p "Please input a number: " num
if [ $num -gt 100 ]; then
    echo "The number is greater than 100."
elif [ $num -eq 100 ]; then
    echo "The number is equal to 100."
else
    echo "The number is less than 100."
fi
```

**判断符号与逻辑运算**：
*   **文件/目录判断**：`-f` (文件)，`-d` (目录)，`-e` (存在)。
*   **整数比较**：`-eq` (等于)，`-ne` (不等于)，`-gt` (大于)，`-lt` (小于)，`-ge` (大于等于)，`-le` (小于等于)。
*   **逻辑运算**：`-a` 或 `&&` (与)，`-o` 或 `||` (或)，`!` (非)。

![](img/f3df4c2caebf0e29a940832703af1706_51.png)

![](img/f3df4c2caebf0e29a940832703af1706_53.png)

**示例：判断目录是否存在并创建**
```bash
#!/bin/bash
if [ ! -d /tmp/data ] && [ ! -d /tmp/2019 ]; then
    mkdir /tmp/data
    echo "Directory /tmp/data created."
fi
```

![](img/f3df4c2caebf0e29a940832703af1706_55.png)

![](img/f3df4c2caebf0e29a940832703af1706_57.png)

![](img/f3df4c2caebf0e29a940832703af1706_59.png)

![](img/f3df4c2caebf0e29a940832703af1706_61.png)

![](img/f3df4c2caebf0e29a940832703af1706_62.png)

### 循环语句：for

![](img/f3df4c2caebf0e29a940832703af1706_64.png)

![](img/f3df4c2caebf0e29a940832703af1706_66.png)

for循环用于遍历一个列表（如数字序列、文件集合等）中的每一项。

![](img/f3df4c2caebf0e29a940832703af1706_68.png)

![](img/f3df4c2caebf0e29a940832703af1706_70.png)

![](img/f3df4c2caebf0e29a940832703af1706_72.png)

**基本语法格式**：
```bash
for 变量 in 列表
do
    # 循环体，针对列表中的每一项执行
done
```

**示例1：遍历数字序列**
```bash
#!/bin/bash
# 使用大括号生成序列
for i in {1..5}
do
    echo "Number: $i"
done
# 使用seq命令生成序列
for i in $(seq 1 2 10) # 从1到10，步长为2
do
    echo "Seq Number: $i"
done
```

![](img/f3df4c2caebf0e29a940832703af1706_74.png)

**示例2：C语言风格的for循环**
```bash
#!/bin/bash
sum=0
for ((i=1; i<=100; i++))
do
    sum=$((sum + i)) # 使用$(( ))进行算术运算
done
echo "The sum from 1 to 100 is: $sum"
```

![](img/f3df4c2caebf0e29a940832703af1706_76.png)

**示例3：遍历文件并打包**
```bash
#!/bin/bash
# 查找/var/log目录下所有.log文件并压缩
for file in $(find /var/log -name "*.log")
do
    tar -czf ${file}.tar.gz $file
    echo "Compressed $file"
done
```

![](img/f3df4c2caebf0e29a940832703af1706_78.png)

### 循环语句：while

while循环根据条件判断结果来决定是否继续循环，条件为真则继续。

![](img/f3df4c2caebf0e29a940832703af1706_80.png)

**基本语法格式**：
```bash
while [ 条件表达式 ]
do
    # 条件为真时执行的命令
done
```

![](img/f3df4c2caebf0e29a940832703af1706_82.png)

**示例1：读取文件内容**
```bash
#!/bin/bash
while read line
do
    echo "Line: $line"
done < /path/to/file.txt
```

**示例2：猜数字游戏**
```bash
#!/bin/bash
target=$((RANDOM % 100 + 1)) # 生成1-100的随机数
guess=0
while [ $guess -ne $target ]
do
    read -p "Guess the number (1-100): " guess
    if [ $guess -lt $target ]; then
        echo "Too low!"
    elif [ $guess -gt $target ]; then
        echo "Too high!"
    else
        echo "Congratulations! You got it!"
    fi
done
```

![](img/f3df4c2caebf0e29a940832703af1706_84.png)

![](img/f3df4c2caebf0e29a940832703af1706_85.png)

### 选择语句：case

![](img/f3df4c2caebf0e29a940832703af1706_87.png)

case语句用于多条件分支选择，比多个if-elif语句更清晰。

![](img/f3df4c2caebf0e29a940832703af1706_89.png)

![](img/f3df4c2caebf0e29a940832703af1706_91.png)

![](img/f3df4c2caebf0e29a940832703af1706_93.png)

**基本语法格式**：
```bash
case 变量 in
模式1)
    命令序列1
    ;;
模式2)
    命令序列2
    ;;
*)
    默认命令序列
    ;;
esac
```

![](img/f3df4c2caebf0e29a940832703af1706_95.png)

![](img/f3df4c2caebf0e29a940832703af1706_97.png)

![](img/f3df4c2caebf0e29a940832703af1706_99.png)

![](img/f3df4c2caebf0e29a940832703af1706_101.png)

**示例：简单的菜单选择**
```bash
#!/bin/bash
echo "Select an option:"
echo "1) Start Service"
echo "2) Stop Service"
echo "3) Restart Service"
read -p "Your choice: " choice
case $choice in
    1)
        echo "Starting service..."
        # systemctl start someservice
        ;;
    2)
        echo "Stopping service..."
        # systemctl stop someservice
        ;;
    3)
        echo "Restarting service..."
        # systemctl restart someservice
        ;;
    *)
        echo "Invalid choice. Exiting."
        exit 1
        ;;
esac
```

![](img/f3df4c2caebf0e29a940832703af1706_103.png)

![](img/f3df4c2caebf0e29a940832703af1706_105.png)

---

## Shell函数

函数将一段代码逻辑封装起来，实现代码的模块化和复用，使脚本结构更清晰，易于维护。

**基本语法格式**：
```bash
function 函数名() {
    # 函数体
    命令序列
    [return 值] # 可选
}
# 调用函数
函数名 参数1 参数2 ...
```

**示例：一个简单的函数**
```bash
#!/bin/bash
# 定义函数
function say_hello() {
    local name=$1 # local定义局部变量
    echo "Hello, $name!"
}
# 调用函数
say_hello "Alice"
say_hello "Bob"
```

在实际的运维脚本中，函数常用于封装安装、配置、备份等具体操作步骤。

---

## 总结

本节课中我们一起学习了Shell脚本的核心知识。我们从Shell脚本的基本概念和标准化规范入手，理解了变量（系统变量、环境变量、用户变量）的定义和使用。接着，我们深入探讨了流程控制语句，包括用于条件判断的`if`，用于遍历的`for`，基于条件循环的`while`，以及用于多分支选择的`case`。最后，我们了解了函数的作用，它可以将代码模块化，提高脚本的可读性和可维护性。

![](img/f3df4c2caebf0e29a940832703af1706_107.png)

记住，Shell脚本的精髓在于将日常命令通过逻辑组合实现自动化。学习的关键在于**多练习、多思考**。在动手编写脚本前，先理清逻辑和步骤，这会让编码过程事半功倍。从简单的“Hello World”和变量操作开始，逐步尝试编写带有判断、循环和函数的小脚本，你的Shell脚本能力一定会稳步提升。