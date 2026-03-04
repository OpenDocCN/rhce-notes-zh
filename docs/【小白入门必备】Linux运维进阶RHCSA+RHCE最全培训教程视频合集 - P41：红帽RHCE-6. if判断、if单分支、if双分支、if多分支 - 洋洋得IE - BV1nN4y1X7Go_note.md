# Linux运维进阶：P41：if判断、if单分支、if双分支、if多分支

![](img/aae706e2ef84ec36720ecd13a01ce4cd_1.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_3.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_5.png)

## 概述
在本节课中，我们将要学习Shell脚本编程中非常重要的**if条件判断**。我们将了解if判断的三种语法结构：单分支、双分支和多分支，并通过实例演示如何使用它们来实现不同的程序逻辑。

![](img/aae706e2ef84ec36720ecd13a01ce4cd_7.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_9.png)

---

## if判断简介
上一节我们介绍了条件判断的基础知识。本节中我们来看看**if判断**。if判断与我们之前讲的条件判断（如`&&`、`||`）实现的功能类似，都是根据条件是否满足来决定执行什么操作。

![](img/aae706e2ef84ec36720ecd13a01ce4cd_11.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_13.png)

if判断比简单的条件连接符功能更强大，适合实现更复杂的逻辑。对于简单的逻辑，可以使用`&&`和`||`；对于复杂的逻辑，就需要使用if判断了。

![](img/aae706e2ef84ec36720ecd13a01ce4cd_14.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_16.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_18.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_20.png)

我们需要了解if判断的三种语法结构。

![](img/aae706e2ef84ec36720ecd13a01ce4cd_22.png)

---

![](img/aae706e2ef84ec36720ecd13a01ce4cd_24.png)

## if单分支判断
if单分支判断的特点是：**只能判断条件成立的情况，不能处理条件失败的情况**。也就是说，它只在条件成立时执行指定的语句；如果条件失败，则不执行任何操作。

![](img/aae706e2ef84ec36720ecd13a01ce4cd_26.png)

以下是if单分支的语法格式。条件需要放在中括号 `[ ]` 内，我们之前学过的文件状态判断、数字对比等都可以作为if的条件。

**语法格式1：**
```bash
if [ 条件 ]; then
    要执行的命令
fi
```

![](img/aae706e2ef84ec36720ecd13a01ce4cd_28.png)

**语法格式2：**
```bash
if [ 条件 ]
then
    要执行的命令
fi
```

![](img/aae706e2ef84ec36720ecd13a01ce4cd_30.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_32.png)

两种语法效果完全相同。为了代码美观，通常会对`then`后面的命令进行缩进。`fi`表示if语句的结束。

![](img/aae706e2ef84ec36720ecd13a01ce4cd_34.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_36.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_38.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_40.png)

现在，让我们通过一个例子来实践。我们创建一个脚本，判断`/etc/passwd`文件是否存在。

![](img/aae706e2ef84ec36720ecd13a01ce4cd_42.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_44.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_46.png)

1.  进入脚本目录并创建文件：
    ```bash
    cd /script
    vim if01.sh
    ```

2.  编写脚本内容：
    ```bash
    #!/bin/bash
    if [ -f /etc/passwd ]; then
        echo "文件存在"
    fi
    ```
    或者使用第二种语法：
    ```bash
    #!/bin/bash
    if [ -f /etc/passwd ]
    then
        echo "文件存在"
    fi
    ```

3.  给脚本添加执行权限并运行：
    ```bash
    chmod +x if01.sh
    ./if01.sh
    ```
    输出结果为“文件存在”。

![](img/aae706e2ef84ec36720ecd13a01ce4cd_48.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_50.png)

4.  修改脚本，判断一个不存在的文件：
    ```bash
    #!/bin/bash
    if [ -f /opt/abcd ]; then
        echo "文件存在"
    fi
    ```
    再次运行脚本，将不会有任何输出，因为条件不成立，`echo`命令不会被执行。

这就是if单分支，它只能处理条件成立的情况。

---

![](img/aae706e2ef84ec36720ecd13a01ce4cd_52.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_54.png)

## if双分支判断
上一节我们介绍了只能判断“对”的单分支。本节中我们来看看功能更强的**if双分支**。双分支判断既能处理条件成立的情况，也能处理条件失败的情况。

它的语法结构如下：
```bash
if [ 条件 ]; then
    条件成立时执行的命令
else
    条件失败时执行的命令
fi
```

![](img/aae706e2ef84ec36720ecd13a01ce4cd_56.png)

让我们编写一个脚本，如果文件不存在则创建它。

![](img/aae706e2ef84ec36720ecd13a01ce4cd_58.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_60.png)

1.  创建脚本文件：
    ```bash
    vim if02.sh
    ```

![](img/aae706e2ef84ec36720ecd13a01ce4cd_62.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_64.png)

2.  编写脚本内容：
    ```bash
    #!/bin/bash
    if [ -f /opt/abcd.tt ]; then
        echo "文件存在"
    else
        touch /opt/abcd.tt
        echo "文件 /opt/abcd.tt 创建成功"
    fi
    ```

3.  执行脚本：
    ```bash
    chmod +x if02.sh
    ./if02.sh
    ```
    如果`/opt/abcd.tt`文件不存在，脚本会创建它并输出提示信息。

双分支判断在实际中非常有用。例如，我们可以编写一个安装软件包的脚本：先检查软件包是否已安装，如果已安装则启动服务；如果未安装，则先安装再启动服务。

![](img/aae706e2ef84ec36720ecd13a01ce4cd_66.png)

以下是结合变量和命令结果判断的示例：
```bash
#!/bin/bash
# 检查vsftpd软件包是否安装，将命令结果（不输出）重定向到/dev/null，只通过`$?`获取上一条命令的退出状态。
rpm -q vsftpd &> /dev/null

![](img/aae706e2ef84ec36720ecd13a01ce4cd_68.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_70.png)

# 判断上一条命令的退出状态码，0表示成功（包已安装）
if [ $? -eq 0 ]; then
    systemctl start vsftpd
    systemctl enable vsftpd &> /dev/null
    echo "vsftpd服务已启动并设置开机自启"
else
    echo "正在安装vsftpd软件包..."
    yum install -y vsftpd &> /dev/null
    systemctl start vsftpd
    systemctl enable vsftpd &> /dev/null
    echo "vsftpd安装完成，服务已启动并设置开机自启"
fi
```

---

![](img/aae706e2ef84ec36720ecd13a01ce4cd_72.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_74.png)

## if多分支判断
双分支只能处理“是”或“否”两种情况。本节中我们来看看**if多分支**，它可以处理多个连续的条件。

多分支的语法结构如下：
```bash
if [ 条件1 ]; then
    条件1成立时执行的命令
elif [ 条件2 ]; then
    条件2成立时执行的命令
elif [ 条件3 ]; then
    条件3成立时执行的命令
...
else
    以上所有条件都不成立时执行的命令
fi
```
脚本会从上到下依次判断条件。一旦某个条件成立，就会执行对应的命令块，然后直接结束整个if判断，后面的条件将不再检查。

让我们编写一个判断成绩等级的脚本作为例子。

1.  创建脚本文件：
    ```bash
    vim if04.sh
    ```

![](img/aae706e2ef84ec36720ecd13a01ce4cd_76.png)

2.  编写脚本内容：
    ```bash
    #!/bin/bash
    read -p "请输入您的成绩进行查询：" score

    if [ $score -ge 90 ]; then
        echo "您的成绩非常优秀！奖励哇塞女孩一枚。"
    elif [ $score -ge 80 ]; then
        echo "您的成绩比较优秀！但离哇塞女孩奖励仅差一步。"
    elif [ $score -ge 70 ]; then
        echo "您的成绩一般，还需努力，离目标尚有距离。"
    elif [ $score -ge 60 ]; then
        echo "您的成绩勉强及格。"
    else
        echo "成绩未及格，请继续加油！"
    fi
    ```
    **注意**：`[`、`$score`、`-ge`、`90`、`]` 每个部分之间都必须有空格。

3.  执行脚本并输入不同成绩进行测试：
    ```bash
    chmod +x if04.sh
    ./if04.sh
    ```
    输入85，会匹配第二个条件（`-ge 80`）。输入73，会匹配第三个条件（`-ge 70`）。输入55，则会执行`else`部分的命令。

![](img/aae706e2ef84ec36720ecd13a01ce4cd_78.png)

![](img/aae706e2ef84ec36720ecd13a01ce4cd_80.png)

---

## 补充：生成随机数
在编写“猜数字”这类脚本时，我们常常需要让计算机生成一个随机数。在Shell中，可以使用 `$RANDOM` 环境变量来获取一个随机整数（范围是0~32767）。

为了得到指定范围内的随机数（例如0~9），我们可以使用取余运算 `%`：
```bash
echo $(( $RANDOM % 10 ))
```
这条命令会输出一个0到9之间的随机数。原理是：任何数除以10的余数，结果必然在0到9之间。

---

![](img/aae706e2ef84ec36720ecd13a01ce4cd_82.png)

## 总结
本节课中我们一起学习了Shell脚本中if条件判断的三种形式：
1.  **if单分支**：只处理条件成立的情况，语法是 `if [条件]; then 命令 fi`。
2.  **if双分支**：能分别处理条件成立和失败的情况，语法是 `if [条件]; then 命令 else 命令 fi`。
3.  **if多分支**：能处理多个连续条件，语法是 `if [条件1]; then ... elif [条件2]; then ... else ... fi`。

![](img/aae706e2ef84ec36720ecd13a01ce4cd_84.png)

**核心要点**：
*   所有判断条件都必须放在 `[ ]` 中括号内。
*   中括号内的每一部分（如变量、操作符、数值）之间都必须有空格。
*   使用变量时，变量名前要加 `$` 符号。
*   多分支判断中，条件是从上到下依次判断的，一旦某个条件满足，就会执行对应的代码块并结束判断。

![](img/aae706e2ef84ec36720ecd13a01ce4cd_86.png)

if判断是Shell脚本实现逻辑控制的基础，结合之前学习的变量、条件测试和运算，你已经可以编写出功能丰富的脚本了。在实际工作中，很多脚本都是基于现有脚本修改而来，理解这些基础语法是读懂和修改他人脚本的关键。