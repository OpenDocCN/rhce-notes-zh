# Linux脚本编程：P41：if判断、if单分支、if双分支、if多分支 📚

![](img/713e67f5e9d0fffdcb95854db1370f97_1.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_2.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_4.png)

在本节课中，我们将要学习Shell脚本中非常重要的流程控制结构——`if`判断。`if`判断允许我们根据条件是否成立来执行不同的命令，是实现脚本逻辑自动化的核心工具。我们将从最简单的单分支结构开始，逐步深入到双分支和多分支结构，并通过实例演示其应用。

![](img/713e67f5e9d0fffdcb95854db1370f97_6.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_8.png)

## 概述

`if`判断与我们之前学习的条件判断（如`&&`、`||`）功能类似，都是根据条件是否满足来决定执行什么操作。但`if`语句功能更强大，结构更清晰，适合处理更复杂的逻辑。本节我们将学习`if`判断的三种语法结构：单分支、双分支和多分支。

![](img/713e67f5e9d0fffdcb95854db1370f97_10.png)

## if单分支判断

![](img/713e67f5e9d0fffdcb95854db1370f97_12.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_13.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_15.png)

上一节我们介绍了条件判断的基本概念，本节中我们来看看`if`判断中最简单的形式——单分支判断。

![](img/713e67f5e9d0fffdcb95854db1370f97_16.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_18.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_20.png)

单分支判断的特点是：**只能判断条件成立的情况，无法处理条件失败的情况**。也就是说，只有当条件成立时，才会执行指定的命令；如果条件失败，则什么都不做。

以下是`if`单分支的两种语法格式：

![](img/713e67f5e9d0fffdcb95854db1370f97_22.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_24.png)

**语法格式一：**
```bash
if [ 条件 ]
then
    条件成立时要执行的命令
fi
```

**语法格式二：**
```bash
if [ 条件 ]; then
    条件成立时要执行的命令
fi
```

两种语法效果完全相同。`if`表示判断开始，`fi`表示判断结束。`[ 条件 ]`是判断条件，`then`后面的命令是条件成立时要执行的操作。为了代码美观，通常会对`then`后面的命令进行缩进。

![](img/713e67f5e9d0fffdcb95854db1370f97_26.png)

让我们通过一个实例来理解。创建一个脚本文件`if01.sh`：

![](img/713e67f5e9d0fffdcb95854db1370f97_28.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_30.png)

```bash
#!/bin/bash

![](img/713e67f5e9d0fffdcb95854db1370f97_32.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_34.png)

if [ -f /etc/passwd ]
then
    echo "文件存在"
fi
```

![](img/713e67f5e9d0fffdcb95854db1370f97_36.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_38.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_40.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_42.png)

执行这个脚本，如果`/etc/passwd`文件存在，就会输出“文件存在”。如果我们将条件改为判断一个不存在的文件，例如`/opt/abcd`，则不会输出任何内容，因为条件不成立，`then`后面的命令不会被执行。

![](img/713e67f5e9d0fffdcb95854db1370f97_44.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_46.png)

## if双分支判断

单分支判断只能处理条件成立的情况，这在实际应用中往往不够。双分支判断则能同时处理条件成立和条件失败两种情况。

![](img/713e67f5e9d0fffdcb95854db1370f97_48.png)

双分支判断的语法结构如下：

![](img/713e67f5e9d0fffdcb95854db1370f97_50.png)

```bash
if [ 条件 ]
then
    条件成立时要执行的命令
else
    条件失败时要执行的命令
fi
```

在这个结构中，如果条件成立，则执行`then`后面的命令；如果条件失败，则执行`else`后面的命令。

让我们通过一个猜数字的小游戏来演示双分支判断的应用。创建脚本`if03.sh`：

![](img/713e67f5e9d0fffdcb95854db1370f97_52.png)

```bash
#!/bin/bash

![](img/713e67f5e9d0fffdcb95854db1370f97_54.png)

# 生成一个0-9的随机数作为正确答案
number1=$((RANDOM % 10))

read -p "请输入0到9之间的一个数字进行猜奖：" number

![](img/713e67f5e9d0fffdcb95854db1370f97_56.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_58.png)

if [ $number -eq $number1 ]
then
    echo "恭喜中奖！"
    echo "奖励哇塞女孩一枚，可使用一天。"
else
    echo "猜错了，哇塞女孩正在等着你，请继续努力。"
    echo "本次猜奖的正确结果为：$number1"
fi
```

![](img/713e67f5e9d0fffdcb95854db1370f97_60.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_62.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_64.png)

这个脚本会生成一个0-9的随机数，然后提示用户输入猜测的数字。如果猜对了，会显示中奖信息；如果猜错了，会显示鼓励信息并公布正确答案。

## if多分支判断

在实际应用中，我们经常需要判断多个条件。这时就需要使用多分支判断结构。

多分支判断的语法结构如下：

![](img/713e67f5e9d0fffdcb95854db1370f97_66.png)

```bash
if [ 条件1 ]
then
    条件1成立时要执行的命令
elif [ 条件2 ]
then
    条件2成立时要执行的命令
elif [ 条件3 ]
then
    条件3成立时要执行的命令
else
    以上条件都不成立时要执行的命令
fi
```

![](img/713e67f5e9d0fffdcb95854db1370f97_68.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_69.png)

在这个结构中，脚本会从上到下依次判断每个条件。一旦某个条件成立，就会执行对应的命令，然后跳过剩余的所有`elif`和`else`部分。

让我们通过一个成绩判断的脚本来演示多分支判断。创建脚本`if04.sh`：

![](img/713e67f5e9d0fffdcb95854db1370f97_71.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_73.png)

```bash
#!/bin/bash

read -p "请输入自己的成绩进行查询：" score

if [ $score -ge 90 ]
then
    echo "你的成绩非常优秀！"
    echo "奖励哇塞女孩一枚。"
elif [ $score -ge 80 ]
then
    echo "你的成绩比较优秀。"
    echo "但是离哇塞女孩的奖励仅差一步。"
elif [ $score -ge 70 ]
then
    echo "你的成绩比较一般，还需努力。"
    echo "离哇塞女孩还差很远的距离。"
elif [ $score -ge 60 ]
then
    echo "勉强及格。"
else
    echo "成绩不及格，回家种地去吧。"
fi
```

这个脚本会根据输入的成绩分数，输出不同的评价和建议。注意，条件判断的顺序很重要，必须从高分到低分排列，否则逻辑会出现错误。

![](img/713e67f5e9d0fffdcb95854db1370f97_75.png)

## 实际应用：软件包安装脚本

结合我们之前学习的知识，让我们编写一个更实用的脚本——自动检查并安装软件包。创建脚本`if02.sh`：

```bash
#!/bin/bash

![](img/713e67f5e9d0fffdcb95854db1370f97_77.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_79.png)

# 检查VSFTPD软件包是否已安装
rpm -q vsftpd &> /dev/null

if [ $? -eq 0 ]
then
    # 如果已安装，则启动服务并设置开机自启
    systemctl start vsftpd &> /dev/null
    systemctl enable vsftpd &> /dev/null
    echo "VSFTPD服务已启动并设置开机自启。"
else
    # 如果未安装，则安装软件包
    echo "正在安装VSFTPD软件包..."
    yum install -y vsftpd &> /dev/null
    systemctl start vsftpd &> /dev/null
    systemctl enable vsftpd &> /dev/null
    echo "VSFTPD服务已启动并设置开机自启。"
fi
```

这个脚本首先检查`vsftpd`软件包是否已安装（通过`rpm -q`命令）。`$?`是一个特殊变量，用于获取上一条命令的执行结果（0表示成功，非0表示失败）。根据检查结果，脚本会执行不同的操作：如果已安装，则直接启动服务；如果未安装，则先安装再启动服务。

## 总结

本节课中我们一起学习了Shell脚本中`if`判断的三种结构：

![](img/713e67f5e9d0fffdcb95854db1370f97_81.png)

![](img/713e67f5e9d0fffdcb95854db1370f97_82.png)

1. **单分支判断**：只能处理条件成立的情况，语法简单，适合简单的逻辑判断。
2. **双分支判断**：能同时处理条件成立和条件失败两种情况，使用`if-else`结构。
3. **多分支判断**：能处理多个条件，使用`if-elif-else`结构，条件按顺序判断，一旦某个条件成立就执行对应的命令。

`if`判断是Shell脚本编程的核心工具之一，它让我们的脚本能够根据不同的情况做出智能的决策。在实际工作中，我们经常需要阅读和修改现有的脚本，因此理解`if`判断的各种用法至关重要。记住，`if`判断的条件必须放在中括号`[ ]`中，并且各部分之间要有空格，变量前要加`$`符号。

![](img/713e67f5e9d0fffdcb95854db1370f97_84.png)

通过本节课的学习，你应该能够编写简单的条件判断脚本，并为后续学习更复杂的脚本编程打下坚实的基础。