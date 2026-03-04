# Linux运维全套培训课程：P42：if判断、if单分支、if双分支、if多分支 🐧

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_1.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_3.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_5.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_7.png)

在本节课中，我们将要学习Shell脚本编程中非常重要的**if条件判断**。我们将了解if判断的三种语法结构：单分支、双分支和多分支，并通过实例演示如何使用它们来实现不同的逻辑控制。

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_9.png)

---

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_11.png)

## 概述

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_13.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_14.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_16.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_18.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_20.png)

上一节我们介绍了Shell中的条件测试。本节中我们来看看如何使用`if`语句，根据条件测试的结果来执行不同的命令。`if`判断比之前学的简单条件判断功能更强大，适合实现更复杂的逻辑。

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_22.png)

## if单分支判断

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_24.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_26.png)

if单分支判断的特点是：**只能判断条件成立的情况，无法处理条件失败的情况**。也就是说，只有在条件成立时，才会执行指定的命令；如果条件失败，则什么都不做。

以下是if单分支的两种语法格式：

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_28.png)

```bash
# 语法格式一
if [ 条件 ]; then
    要执行的命令
fi

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_30.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_32.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_34.png)

# 语法格式二
if [ 条件 ]
then
    要执行的命令
fi
```

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_36.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_38.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_40.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_42.png)

**核心概念**：
*   `if`：语句的开始。
*   `[ 条件 ]`：中括号内放置判断条件，可以是文件测试、数值比较、字符串比较等。
*   `then`：条件成立后，执行其后的命令。
*   `fi`：表示`if`语句的结束。

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_44.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_46.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_48.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_50.png)

让我们通过一个例子来理解。我们将创建一个脚本，判断`/etc/passwd`文件是否存在。

1.  进入脚本目录并创建文件：
    ```bash
    cd /script
    vim if01.sh
    ```
2.  编写脚本内容：
    ```bash
    #!/bin/bash
    # 判断文件是否存在
    if [ -f /etc/passwd ]; then
        echo “文件存在”
    fi
    ```
3.  给脚本添加执行权限并运行：
    ```bash
    chmod +x if01.sh
    ./if01.sh
    ```
    如果`/etc/passwd`文件存在，则会输出“文件存在”。如果我们将条件改为一个不存在的文件（如`/opt/abcd`），则脚本不会输出任何内容，因为条件不成立，`then`后面的命令不会执行。

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_52.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_54.png)

**总结**：单分支`if`只处理条件为真的情况，适用于“如果...就...”的简单场景。

## if双分支判断

单分支判断无法处理条件失败的情况，这在实际应用中往往不够。双分支判断解决了这个问题，它**既能处理条件成立的情况，也能处理条件不成立的情况**。

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_56.png)

其语法结构如下：

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_58.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_60.png)

```bash
if [ 条件 ]; then
    条件成立时执行的命令
else
    条件不成立时执行的命令
fi
```

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_61.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_63.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_65.png)

**核心概念**：
*   `else`：当`if`后面的条件不成立时，执行`else`部分的命令。

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_67.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_69.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_71.png)

我们通过一个“猜数字”的小游戏来演示双分支判断。这个脚本会生成一个0-9的随机数，让用户猜测。

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_73.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_75.png)

1.  创建脚本文件：
    ```bash
    vim if03.sh
    ```
2.  编写脚本内容：
    ```bash
    #!/bin/bash
    # 生成一个0-9的随机数并存储
    num=$((RANDOM % 10))
    # 提示用户输入
    read -p “请输入一个0-9的数字：” guess
    # 判断用户输入与随机数是否相等
    if [ $guess -eq $num ]; then
        echo “恭喜你，猜对了！”
    else
        echo “很遗憾，猜错了。正确数字是：$num”
    fi
    ```
3.  运行脚本：
    ```bash
    bash if03.sh
    ```

**脚本解析**：
*   `RANDOM`是系统环境变量，每次调用都会产生一个随机数。`$((RANDOM % 10))`表示对随机数除以10取余，结果范围是0-9。
*   `read -p`用于提示用户输入，并将输入的值存入变量`guess`。
*   `[ $guess -eq $num ]`判断两个变量的值是否相等。
*   根据判断结果，执行`then`或`else`后面的命令。

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_77.png)

双分支判断非常实用，例如在安装软件包前检查是否已安装：
```bash
#!/bin/bash
if rpm -q vsftpd &> /dev/null; then
    echo “软件包已存在，启动服务...”
    systemctl start vsftpd
    systemctl enable vsftpd
else
    echo “软件包不存在，开始安装...”
    yum install -y vsftpd &> /dev/null
    systemctl start vsftpd
    systemctl enable vsftpd &> /dev/null
    echo “服务已启动”
fi
```

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_79.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_81.png)

## if多分支判断

当我们需要判断多个条件时，就需要使用多分支判断。它允许我们**依次检查多个条件，执行第一个满足条件所对应的命令块**。

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_83.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_85.png)

其语法结构如下：

```bash
if [ 条件1 ]; then
    命令1
elif [ 条件2 ]; then
    命令2
elif [ 条件3 ]; then
    命令3
else
    以上条件都不满足时执行的命令
fi
```

**核心概念**：
*   `elif`：是“else if”的缩写，用于添加额外的判断条件。可以有很多个`elif`。
*   执行流程：脚本会从上到下依次判断每个条件。一旦某个条件成立，就执行对应的命令块，然后**直接跳出整个`if`结构**，后面的条件不再判断。

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_87.png)

我们通过一个“成绩评级”的脚本来理解多分支判断：

1.  创建脚本文件：
    ```bash
    vim if04.sh
    ```
2.  编写脚本内容：
    ```bash
    #!/bin/bash
    read -p “请输入您的成绩（0-100）：” score
    if [ $score -ge 90 ]; then
        echo “成绩优秀！”
    elif [ $score -ge 80 ]; then
        echo “成绩良好。”
    elif [ $score -ge 70 ]; then
        echo “成绩中等。”
    elif [ $score -ge 60 ]; then
        echo “成绩及格。”
    else
        echo “成绩不及格，需要努力！”
    fi
    ```
3.  运行脚本并输入不同分数测试：
    ```bash
    bash if04.sh
    ```

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_89.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_91.png)

**注意**：在`[ ]`中进行条件判断时，**括号内每一项前后都必须有空格**，这是一个常见的错误来源。例如`[ $score -ge 90 ]`是正确的，而`[$score -ge 90]`会导致脚本报错。

## 总结与核心要点

本节课中我们一起学习了Shell脚本中`if`条件判断的三种形式。

*   **if单分支**：用于最简单的“如果条件成立，则执行操作”的场景。
*   **if双分支**：通过`if-else`结构，可以分别处理条件成立和不成立两种情况。
*   **if多分支**：通过`if-elif-else`结构，可以处理多个连续或并列的条件判断。

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_93.png)

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_95.png)

**核心要点回顾**：
1.  所有条件判断都放在**中括号 `[ ]`** 内进行。
2.  中括号内的**每一项（包括括号本身）之间都必须有空格**。
3.  如果条件中使用了变量，变量名前一定要加`$`符号，如`$score`。
4.  在脚本实践中，我们经常需要**借鉴和修改现有的成熟脚本**，这比从零开始编写更高效。学习的关键在于理解脚本的逻辑，并能根据需求进行修改。

![](img/7057db0f17f49b211a5e65e8d5a9a1c2_97.png)

通过结合之前学习的变量、条件测试和运算，`if`判断能帮助我们构建出功能丰富的自动化脚本，是Linux运维工作中不可或缺的工具。