# Linux教程RHCE：P5：vim编辑器与shell

![](img/5d368cc6f920b77878df7583c1156cca_1.png)

![](img/5d368cc6f920b77878df7583c1156cca_3.png)

![](img/5d368cc6f920b77878df7583c1156cca_5.png)

## 概述
在本节课中，我们将学习如何编写Shell脚本。我们将从脚本的基本结构开始，逐步深入到条件测试、循环语句等核心概念，并通过一系列实验来巩固所学知识。通过本节课的学习，你将能够编写出功能丰富的自动化脚本。

---

![](img/5d368cc6f920b77878df7583c1156cca_7.png)

## 脚本的基本结构 🏗️

![](img/5d368cc6f920b77878df7583c1156cca_9.png)

一个最基本的Shell脚本由三部分组成：脚本声明、脚本注释和脚本命令。

![](img/5d368cc6f920b77878df7583c1156cca_11.png)

**脚本声明**：以 `#!` 开头，指定由哪个解释器来执行脚本。例如，`#!/bin/bash` 表示使用Bash解释器。

![](img/5d368cc6f920b77878df7583c1156cca_13.png)

![](img/5d368cc6f920b77878df7583c1156cca_15.png)

**脚本注释**：以 `#` 开头，是对脚本功能或参数的说明，仅供阅读，程序不会执行。

**脚本命令**：脚本的核心，即需要按顺序执行的Linux命令。

![](img/5d368cc6f920b77878df7583c1156cca_17.png)

以下是一个最简单的脚本示例：

![](img/5d368cc6f920b77878df7583c1156cca_19.png)

![](img/5d368cc6f920b77878df7583c1156cca_20.png)

```bash
#!/bin/bash
# 这是一个简单的脚本示例
pwd
ls -l
```

这个脚本会依次执行 `pwd`（显示当前目录）和 `ls -l`（列出目录内容）两个命令。

---

## 接收用户参数 📥

上一节我们介绍了脚本的基本结构，本节中我们来看看脚本如何接收用户输入的参数。这就像命令可以接受 `-l`、`-a` 等参数一样，脚本也需要具备这种交互能力。

Shell脚本内置了一些特殊变量来处理参数：

![](img/5d368cc6f920b77878df7583c1156cca_22.png)

![](img/5d368cc6f920b77878df7583c1156cca_24.png)

![](img/5d368cc6f920b77878df7583c1156cca_26.png)

*   **`$0`**：脚本的名称。
*   **`$#`**：总共接收到的参数个数。
*   **`$*`**：所有参数的值。
*   **`$1`, `$2`, `$3`...**：第1、2、3...个参数的值。

![](img/5d368cc6f920b77878df7583c1156cca_28.png)

![](img/5d368cc6f920b77878df7583c1156cca_30.png)

![](img/5d368cc6f920b77878df7583c1156cca_31.png)

![](img/5d368cc6f920b77878df7583c1156cca_32.png)

![](img/5d368cc6f920b77878df7583c1156cca_33.png)

我们可以通过一个实验来理解这些变量：

![](img/5d368cc6f920b77878df7583c1156cca_35.png)

```bash
#!/bin/bash
echo "脚本名称是：$0"
echo "总共接收到 $# 个参数"
echo "所有参数是：$*"
echo "第一个参数是：$1"
echo "第三个参数是：$3"
```

执行脚本 `./test.sh A B C D E F G`，输出结果将验证上述变量的含义。

---

![](img/5d368cc6f920b77878df7583c1156cca_37.png)

## 条件测试语句 🔍

![](img/5d368cc6f920b77878df7583c1156cca_39.png)

仅仅接收参数还不够，脚本还需要根据不同的参数执行不同的操作。这就需要用到条件测试语句。条件测试语句使用中括号 `[ ]` 括起来，**注意中括号内部两端必须有空格**。

![](img/5d368cc6f920b77878df7583c1156cca_41.png)

![](img/5d368cc6f920b77878df7583c1156cca_43.png)

条件测试主要分为四种：文件测试、逻辑测试、整数值比较和字符串比较。

![](img/5d368cc6f920b77878df7583c1156cca_45.png)

![](img/5d368cc6f920b77878df7583c1156cca_47.png)

![](img/5d368cc6f920b77878df7583c1156cca_48.png)

### 文件测试
用于判断文件的属性，例如是否存在、是什么类型、是否有权限等。

![](img/5d368cc6f920b77878df7583c1156cca_50.png)

![](img/5d368cc6f920b77878df7583c1156cca_52.png)

![](img/5d368cc6f920b77878df7583c1156cca_54.png)

常用操作符：
*   `-d`：测试是否为目录。
*   `-f`：测试是否为一般文件。
*  -e`：测试文件是否存在。
*   `-r`：测试是否有读权限。
*   `-w`：测试是否有写权限。
*   `-x`：测试是否有执行权限。

![](img/5d368cc6f920b77878df7583c1156cca_56.png)

示例：`[ -d /home ]` 判断 `/home` 是否是一个目录。命令执行成功（条件为真）则返回 `0`，失败（条件为假）则返回非 `0` 值。可以通过 `$?` 获取上一条命令的返回值。

![](img/5d368cc6f920b77878df7583c1156cca_58.png)

### 逻辑测试
用于连接多个测试条件。

![](img/5d368cc6f920b77878df7583c1156cca_60.png)

常用操作符：
*   **`&&`**：逻辑“与”，表示前面的命令执行**成功**，才会执行后面的命令。
*   **`||`**：逻辑“或”，表示前面的命令执行**失败**，才会执行后面的命令。
*   **`!`**：逻辑“非”，表示对测试结果取反。

![](img/5d368cc6f920b77878df7583c1156cca_62.png)

![](img/5d368cc6f920b77878df7583c1156cca_63.png)

示例：
```bash
[ -e /tmp/test.txt ] && echo "文件存在"
[ ! -d /home ] || echo "这不是一个目录"
```

### 整数值比较
用于比较整数的大小。**注意：不能直接使用数学符号（>、<、=），而要使用专用操作符**。

常用操作符：
*   `-eq`：等于（equal）
*   `-ne`：不等于（not equal）
*   `-gt`：大于（greater than）
*   `-lt`：小于（less than）
*   `-ge`：大于或等于（greater or equal）
*   `-le`：小于或等于（less or equal）

示例：`[ 5 -gt 3 ]` 判断5是否大于3。

### 字符串比较
用于判断字符串是否相等、是否为空等。

常用操作符：
*   `=`：比较字符串内容是否相同。
*   `!=`：比较字符串内容是否不同。
*   `-z`：判断字符串内容是否为空。

示例：`[ $USER = "root" ]` 判断当前用户是否为root。

---

![](img/5d368cc6f920b77878df7583c1156cca_65.png)

![](img/5d368cc6f920b77878df7583c1156cca_66.png)

![](img/5d368cc6f920b77878df7583c1156cca_68.png)

## 流程控制语句 🚦

![](img/5d368cc6f920b77878df7583c1156cca_70.png)

![](img/5d368cc6f920b77878df7583c1156cca_72.png)

掌握了条件测试后，我们就可以用它来控制脚本的执行流程了。主要有 `if` 条件语句和循环语句。

![](img/5d368cc6f920b77878df7583c1156cca_74.png)

### if 条件语句
`if` 语句根据条件测试的结果，决定执行哪部分代码。

**1. 单分支 if 语句**
如果条件成立，则执行预设操作。
语法：
```bash
if 条件测试操作
  then
    命令序列
fi
```
示例：如果目录不存在，则创建它。
```bash
if [ ! -d /tmp/testdir ]
  then
    mkdir -p /tmp/testdir
fi
```

**2. 双分支 if 语句**
如果条件成立，执行操作A；否则，执行操作B。
语法：
```bash
if 条件测试操作
  then
    命令序列1
  else
    命令序列2
fi
```
示例：检测主机是否在线。
```bash
ping -c 3 -i 0.2 -W 3 $1 &> /dev/null
if [ $? -eq 0 ]
  then
    echo "主机 $1 在线！"
  else
    echo "主机 $1 不在线！"
fi
```

**3. 多分支 if 语句**
可以进行多个条件的判断。
语法：
```bash
if 条件测试操作1
  then
    命令序列1
elif 条件测试操作2
  then
    命令序列2
else
    命令序列3
fi
```
示例：根据分数判断成绩等级。
```bash
read -p "请输入您的分数（0-100）：" GRADE
if [ $GRADE -ge 85 ] && [ $GRADE -le 100 ]
  then
    echo "成绩优秀！"
elif [ $GRADE -ge 70 ] && [ $GRADE -le 84 ]
  then
    echo "成绩及格！"
else
    echo "成绩不及格！"
fi
```

![](img/5d368cc6f920b77878df7583c1156cca_76.png)

![](img/5d368cc6f920b77878df7583c1156cca_78.png)

![](img/5d368cc6f920b77878df7583c1156cca_80.png)

### for 循环语句
用于遍历一个列表（如文件内容、参数列表等），并对列表中的每个元素执行相同操作。

![](img/5d368cc6f920b77878df7583c1156cca_82.png)

语法：
```bash
for 变量名 in 取值列表
  do
    命令序列
  done
```
示例1：批量创建用户（用户名单来自文件）。
```bash
#!/bin/bash
read -s -p "请输入用户密码：" PASS
for UNAME in `cat users.txt`
do
  id $UNAME &> /dev/null
  if [ $? -eq 0 ]
    then
      echo "用户 $UNAME 已存在，跳过。"
    else
      useradd $UNAME &> /dev/null
      echo $PASS | passwd --stdin $UNAME &> /dev/null
      if [ $? -eq 0 ]
        then
          echo "用户 $UNAME 创建成功。"
        else
          echo "用户 $UNAME 创建失败。"
      fi
  fi
done
```
示例2：批量检测多个IP地址是否在线。
```bash
#!/bin/bash
HLIST=$(cat ip.txt)
for IP in $HLIST
do
  ping -c 3 -i 0.2 -W 3 $IP &> /dev/null
  if [ $? -eq 0 ]
    then
    echo "主机 $IP 在线。"
  else
    echo "主机 $IP 不在线。"
  fi
done
```

![](img/5d368cc6f920b77878df7583c1156cca_84.png)

### while 循环语句
根据条件测试结果来决定是否循环。条件为真（`true`）时继续循环。

语法：
```bash
while 条件测试操作
  do
    命令序列
  done
```
示例：一个简单的猜价格游戏。
```bash
#!/bin/bash
PRICE=$(expr $RANDOM % 1000)
TIMES=0
echo "商品实际价格是 0-999 之间的随机数，猜猜看是多少？"
while true
do
  read -p "请输入您猜测的价格：" INT
  let TIMES++
  if [ $INT -eq $PRICE ]
    then
      echo "恭喜您！猜对了！实际价格是 $PRICE。"
      echo "您总共猜了 $TIMES 次。"
      exit 0
    elif [ $INT -gt $PRICE ]
      then
        echo "太高了！"
      else
        echo "太低了！"
  fi
done
```

![](img/5d368cc6f920b77878df7583c1156cca_86.png)

![](img/5d368cc6f920b77878df7583c1156cca_87.png)

---

![](img/5d368cc6f920b77878df7583c1156cca_89.png)

![](img/5d368cc6f920b77878df7583c1156cca_91.png)

## 总结
本节课中我们一起学习了Shell脚本编程的核心知识。我们从脚本的基本结构（声明、注释、命令）开始，学习了如何通过特殊变量（`$0`, `$#`, `$*`等）接收用户参数。然后，深入探讨了条件测试语句，包括文件测试、逻辑测试、整数比较和字符串比较，这是实现脚本智能判断的基础。最后，我们掌握了流程控制语句：使用 `if` 进行条件分支，使用 `for` 和 `while` 进行循环操作。通过多个由浅入深的实验，我们实践了如何将这些知识组合起来，编写出能够批量处理任务、进行条件判断和实现交互功能的实用脚本。虽然内容有一定难度，但这是实现Linux系统自动化和高效管理的关键一步。