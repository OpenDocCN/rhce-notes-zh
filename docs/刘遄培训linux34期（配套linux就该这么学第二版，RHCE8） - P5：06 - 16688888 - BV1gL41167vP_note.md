# Linux运维培训：第4章：Shell脚本编程入门

![](img/dd31f569b0ab87ade48b4d6e6225182b_1.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_3.png)

## 概述

在本节课中，我们将要学习Shell脚本编程的基础知识。我们将从最简单的脚本结构开始，逐步学习如何接收用户输入、进行条件判断，并最终编写出能够实现自动化任务的脚本。课程内容由浅入深，旨在让初学者能够轻松上手。

---

![](img/dd31f569b0ab87ade48b4d6e6225182b_5.png)

## 4.2.1 Shell脚本的基本结构

![](img/dd31f569b0ab87ade48b4d6e6225182b_7.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_9.png)

上一节我们介绍了Vim编辑器、管道符和环境变量，本节中我们来看看如何编写一个最简单的Shell脚本。

![](img/dd31f569b0ab87ade48b4d6e6225182b_11.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_13.png)

一个完整的Shell脚本主要由三部分组成：

![](img/dd31f569b0ab87ade48b4d6e6225182b_15.png)

1.  **脚本声明**：指定脚本由哪个解释器执行。
2.  **脚本注释**：对脚本功能或参数进行说明，程序不执行这部分内容。
3.  **脚本命令**：具体要执行的命令序列。

![](img/dd31f569b0ab87ade48b4d6e6225182b_17.png)

### 脚本声明

![](img/dd31f569b0ab87ade48b4d6e6225182b_19.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_21.png)

脚本声明的作用是告诉系统使用哪个解释器来执行此脚本。我们目前学习的是Bash，所以声明格式固定为：

![](img/dd31f569b0ab87ade48b4d6e6225182b_23.png)

```bash
#!/bin/bash
```

![](img/dd31f569b0ab87ade48b4d6e6225182b_25.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_27.png)

### 脚本注释

![](img/dd31f569b0ab87ade48b4d6e6225182b_29.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_31.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_33.png)

脚本注释以 `#` 号开头，用于说明脚本的功能、作者、参数等信息，方便他人或自己日后阅读。注释可以有多行，也可以没有。

例如：
```bash
# 这是一个测试脚本
# 创建于2022年4月15日
```

![](img/dd31f569b0ab87ade48b4d6e6225182b_35.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_37.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_39.png)

### 脚本命令

脚本命令就是将你想要系统按顺序执行的一系列命令写下来。一个最简单的Shell脚本，本质上就是命令的堆积。

![](img/dd31f569b0ab87ade48b4d6e6225182b_41.png)

---

![](img/dd31f569b0ab87ade48b4d6e6225182b_43.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_45.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_47.png)

## 动手实践：编写第一个脚本

![](img/dd31f569b0ab87ade48b4d6e6225182b_49.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_51.png)

让我们通过一个例子来理解。我们将创建一个脚本，依次执行几个查看系统信息的命令。

首先，使用Vim编辑器创建一个名为 `test.sh` 的脚本文件：

![](img/dd31f569b0ab87ade48b4d6e6225182b_53.png)

```bash
vim test.sh
```

进入Vim后，按 `i` 键进入编辑模式，然后输入以下内容：

![](img/dd31f569b0ab87ade48b4d6e6225182b_55.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_57.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_59.png)

```bash
#!/bin/bash
# 我的第一个Shell脚本
# 用于查看系统基本信息

date
ls -al
free -h
uname -a
```

![](img/dd31f569b0ab87ade48b4d6e6225182b_61.png)

输入完成后，按 `Esc` 键退出编辑模式，然后输入 `:wq` 保存并退出。

![](img/dd31f569b0ab87ade48b4d6e6225182b_63.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_65.png)

现在，我们来执行这个脚本。由于我们尚未学习文件权限（第五章内容），目前不能直接通过 `./test.sh` 的方式运行。我们可以通过调用Bash解释器来执行：

```bash
bash test.sh
```

执行后，屏幕上会依次显示当前时间、目录文件列表、内存使用情况和内核版本信息。

![](img/dd31f569b0ab87ade48b4d6e6225182b_67.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_69.png)

通过这个例子，我们可以看到，一个基础的Shell脚本并不复杂，它就是由声明、注释和命令三部分有序组合而成的。

![](img/dd31f569b0ab87ade48b4d6e6225182b_71.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_73.png)

---

![](img/dd31f569b0ab87ade48b4d6e6225182b_75.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_77.png)

## 4.2.2 接收用户参数

![](img/dd31f569b0ab87ade48b4d6e6225182b_79.png)

在上一节，我们编写了一个固定功能的脚本。但一个有用的脚本应该能够根据用户的输入做出不同的反应。本节中我们来看看Shell脚本如何接收用户传递的参数。

![](img/dd31f569b0ab87ade48b4d6e6225182b_81.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_83.png)

Shell脚本已经内置了一些变量，用于接收用户输入的参数，我们可以直接使用它们：

*   **`$0`**：代表脚本本身的名称。
*   **`$1`, `$2`, `$3`...**：分别代表接收到的第1个、第2个、第3个……参数。
*   **`$#`**：代表总共接收到的参数个数。
*   **`$*`**：代表所有接收到的参数列表。

让我们通过一个例子来理解这些变量的用法。

![](img/dd31f569b0ab87ade48b4d6e6225182b_85.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_87.png)

创建一个名为 `params.sh` 的脚本：

![](img/dd31f569b0ab87ade48b4d6e6225182b_89.png)

```bash
vim params.sh
```

![](img/dd31f569b0ab87ade48b4d6e6225182b_91.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_93.png)

输入以下内容：
```bash
#!/bin/bash
echo “脚本名称是：$0”
echo “接收到的第1个参数是：$1”
echo “接收到的第3个参数是：$3”
echo “接收到的第5个参数是：$5”
echo “一共接收了 $# 个参数”
echo “所有参数列表：$*”
```

![](img/dd31f569b0ab87ade48b4d6e6225182b_95.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_97.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_99.png)

保存退出后，执行这个脚本并传入一些参数：

![](img/dd31f569b0ab87ade48b4d6e6225182b_101.png)

```bash
bash params.sh A B C D E F G
```

在执行前，可以先思考一下预期的输出结果。执行后，对比你的思考，理解每个变量的含义。

![](img/dd31f569b0ab87ade48b4d6e6225182b_103.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_105.png)

---

![](img/dd31f569b0ab87ade48b4d6e6225182b_107.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_109.png)

## 4.2.3 条件测试语句

![](img/dd31f569b0ab87ade48b4d6e6225182b_111.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_113.png)

脚本能够接收参数后，下一步就是根据不同的参数值执行不同的操作。这就需要用到条件判断。本节中我们来看看如何进行条件测试。

![](img/dd31f569b0ab87ade48b4d6e6225182b_115.png)

在Shell脚本中，我们需要手动对要判断的内容进行分类测试，主要分为四种类型：

![](img/dd31f569b0ab87ade48b4d6e6225182b_117.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_119.png)

1.  **文件测试**：判断文件是否存在、是什么类型、有何种权限等。
2.  **逻辑测试**：进行“与(`&&`)”、“或(`||`)”、“非(`!`)”的逻辑操作。
3.  **整数比较**：比较两个整数的大小关系。
4.  **字符串比较**：判断两个字符串是否相同。

![](img/dd31f569b0ab87ade48b4d6e6225182b_121.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_123.png)

### 文件测试

![](img/dd31f569b0ab87ade48b4d6e6225182b_125.png)

文件测试常用于操作文件前的检查，例如判断文件是否存在、是否可读/写等。

![](img/dd31f569b0ab87ade48b4d6e6225182b_127.png)

测试语句的基本格式是：
```bash
[ 操作符 文件或目录 ]
```
**注意：`[` 和 `]` 前后都必须有空格。**

![](img/dd31f569b0ab87ade48b4d6e6225182b_129.png)

常用的文件测试操作符有：
*   `-e`：测试文件是否存在。
*   `-d`：测试是否为目录。
*   `-f`：测试是否为一般文件。
*   `-r`：测试是否可读。
*   `-w`：测试是否可写。
*   `-x`：测试是否可执行。

如何知道测试结果呢？我们可以使用 `$?` 变量。它代表上一条命令的返回值。如果返回值为 `0`，表示上一条语句执行成功（条件为真）；如果返回值为非 `0`（通常是`1`），表示执行失败（条件为假）。

![](img/dd31f569b0ab87ade48b4d6e6225182b_131.png)

**示例：**
```bash
[ -e /etc/passwd ]
echo $? # 输出 0，因为文件存在
[ -e /nonexistent_file ]
echo $? # 输出非 0，因为文件不存在
```

![](img/dd31f569b0ab87ade48b4d6e6225182b_133.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_135.png)

### 逻辑测试

![](img/dd31f569b0ab87ade48b4d6e6225182b_137.png)

逻辑测试操作符用于连接多个命令，实现复杂的条件判断。

![](img/dd31f569b0ab87ade48b4d6e6225182b_139.png)

*   **`&&`**：逻辑“与”。只有前面的命令执行成功，才会执行后面的命令。
    *   `命令A && 命令B`：若A成功则执行B。
*   **`||`**：逻辑“或”。只有前面的命令执行失败，才会执行后面的命令。
    *   `命令A || 命令B`：若A失败则执行B。
*   **`!`**：逻辑“非”。对条件测试结果取反。
    *   `! 测试条件`：条件为真则结果为假，条件为假则结果为真。

![](img/dd31f569b0ab87ade48b4d6e6225182b_141.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_143.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_145.png)

**示例：**
```bash
# 如果 /tmp 目录存在，则输出“存在”
[ -d /tmp ] && echo “目录存在”
# 如果当前用户不是root，则输出“非管理员”
[ $USER != “root” ] && echo “非管理员用户”
```

![](img/dd31f569b0ab87ade48b4d6e6225182b_147.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_149.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_151.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_153.png)

### 整数比较

![](img/dd31f569b0ab87ade48b4d6e6225182b_155.png)

整数比较时，**不能**使用数学符号 `>`、`<`、`=`，因为它们会被Shell解释为重定向符或赋值符。必须使用专用的整数比较操作符：

![](img/dd31f569b0ab87ade48b4d6e6225182b_157.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_159.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_161.png)

*   `-eq`：等于（Equal）
*   `-ne`：不等于（Not Equal）
*   `-gt`：大于（Greater Than）
*   `-lt`：小于（Less Than）
*   `-ge`：大于等于（Greater or Equal）
*   `-le`：小于等于（Less or Equal）

![](img/dd31f569b0ab87ade48b4d6e6225182b_163.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_165.png)

**示例：**
```bash
[ 10 -gt 5 ] && echo “10大于5”
[ 10 -eq 10 ] && echo “10等于10”
```

![](img/dd31f569b0ab87ade48b4d6e6225182b_167.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_169.png)

### 字符串比较

![](img/dd31f569b0ab87ade48b4d6e6225182b_171.png)

字符串比较主要用于判断字符串是否相等、是否为空等。

*   `=` 或 `==`：判断字符串是否相同。
*   `!=`：判断字符串是否不同。
*   `-z`：判断字符串是否为空。

**示例：**
```bash
[ $USER = “root” ] && echo “当前是管理员”
[ -z $VARIABLE ] && echo “变量VARIABLE为空”
```

![](img/dd31f569b0ab87ade48b4d6e6225182b_173.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_175.png)

---

![](img/dd31f569b0ab87ade48b4d6e6225182b_177.png)

## 4.2.4 流程控制语句

![](img/dd31f569b0ab87ade48b4d6e6225182b_179.png)

掌握了条件测试后，我们就可以让脚本根据不同的条件执行不同的代码块，实现流程控制。本节中我们主要学习 `if` 条件语句。

![](img/dd31f569b0ab87ade48b4d6e6225182b_181.png)

`if` 语句有三种基本结构：单分支、双分支和多分支。

![](img/dd31f569b0ab87ade48b4d6e6225182b_183.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_185.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_187.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_189.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_191.png)

### 单分支 if 语句

![](img/dd31f569b0ab87ade48b4d6e6225182b_193.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_195.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_197.png)

结构：如果条件成立，则执行某个操作。
```bash
if 条件测试; then
    命令序列
fi
```

![](img/dd31f569b0ab87ade48b4d6e6225182b_199.png)

**示例：** 如果 `/backup` 目录不存在，则创建它。
```bash
if [ ! -d /backup ]; then
    mkdir /backup
fi
```

### 双分支 if 语句

![](img/dd31f569b0ab87ade48b4d6e6225182b_201.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_203.png)

结构：如果条件成立，则执行操作A；否则（条件不成立），执行操作B。
```bash
if 条件测试; then
    命令序列A
else
    命令序列B
fi
```

**示例：** 检测一台主机是否在线。
```bash
#!/bin/bash
ping -c 3 -i 0.2 -W 3 $1 &> /dev/null
if [ $? -eq 0 ]; then
    echo “主机 $1 在线！”
else
    echo “主机 $1 离线！”
fi
```
（`&> /dev/null` 将命令的输出丢弃，保持屏幕整洁）

![](img/dd31f569b0ab87ade48b4d6e6225182b_205.png)

### 多分支 if 语句

结构：可以进行多次条件判断，直到满足某个条件为止。
```bash
if 条件测试1; then
    命令序列1
elif 条件测试2; then
    命令序列2
elif 条件测试3; then
    命令序列3
...
else
    命令序列N # 以上条件都不满足时的“兜底”操作
fi
```

**示例：** 成绩等级判断。
```bash
#!/bin/bash
read -p “请输入您的分数（0-100）：” GRADE
if [ $GRADE -ge 85 ] && [ $GRADE -le 100 ]; then
    echo “$GRADE 分！优秀！”
elif [ $GRADE -ge 70 ] && [ $GRADE -le 84 ]; then
    echo “$GRADE 分！合格！”
else
    echo “$GRADE 分！不及格！”
fi
```
（`read -p` 用于提示用户输入，并将输入值赋给变量）

---

## 4.2.5 for 循环语句

当我们需要对一系列值（如一个列表、一个数字范围）重复执行相同操作时，`for` 循环就非常有用。本节中我们来看看 `for` 循环的基本用法。

`for` 循环的基本语法是：
```bash
for 变量名 in 取值列表
do
    命令序列
done
```

**示例1：** 批量创建用户。
假设我们有一个文件 `user_list.txt`，里面每行是一个用户名：
```
zhangsan
lisi
wangwu
```
我们可以编写如下脚本：
```bash
#!/bin/bash
read -s -p “请输入初始密码：” PASSWD # -s 选项使输入不回显
for UNAME in `cat user_list.txt` # 反引号`用于执行命令并获取结果
do
    id $UNAME &> /dev/null # 检查用户是否存在
    if [ $? -eq 0 ]; then
        echo “用户 $UNAME 已存在。”
    else
        useradd $UNAME &> /dev/null
        echo $PASSWD | passwd --stdin $UNAME &> /dev/null
        echo “用户 $UNAME 创建成功。”
    fi
done
```

**示例2：** 批量检测一个IP地址列表中的主机是否在线。
假设我们有一个文件 `ip_list.txt`，里面是待检测的IP：
```
192.168.1.10
192.168.1.20
192.168.1.30
```
脚本如下：
```bash
#!/bin/bash
for IP in `cat ip_list.txt`
do
    ping -c 3 -i 0.2 -W 3 $IP &> /dev/null
    if [ $? -eq 0 ]; then
        echo “主机 $IP 在线。”
    else
        echo “主机 $IP 离线。”
    fi
done
```

![](img/dd31f569b0ab87ade48b4d6e6225182b_207.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_209.png)

通过 `for` 循环，我们可以轻松地将重复性的任务自动化。

---

## 总结

本节课中我们一起学习了Shell脚本编程的核心基础。

![](img/dd31f569b0ab87ade48b4d6e6225182b_211.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_213.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_215.png)

我们首先了解了Shell脚本由**声明**、**注释**和**命令**三部分组成。接着，我们学习了如何通过 `$1`、`$2`、`$#` 等内置变量来**接收用户参数**。

为了让脚本“智能”起来，我们深入学习了**条件测试语句**，包括对文件、整数、字符串的判断以及逻辑组合。基于条件测试，我们掌握了 `if` 流程控制语句，实现了单分支、双分支和多分支的判断逻辑。

最后，我们学习了 `for` 循环语句，它能够遍历一个列表或范围，对其中每个元素执行相同的操作，这是实现批量自动化任务的关键。

![](img/dd31f569b0ab87ade48b4d6e6225182b_217.png)

![](img/dd31f569b0ab87ade48b4d6e6225182b_218.png)

从最简单的命令堆积，到能够接收参数、进行判断、循环处理，我们编写的脚本功能越来越强大，也越来越实用。虽然内容逐步深入，但核心思想都是将这些基础构件组合起来，解决实际问题。课后请务必动手实践，加深理解。