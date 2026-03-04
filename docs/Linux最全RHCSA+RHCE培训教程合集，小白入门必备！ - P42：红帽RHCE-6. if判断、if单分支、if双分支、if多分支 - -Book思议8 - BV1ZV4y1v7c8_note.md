# Linux最全RHCSA+RHCE培训教程合集，小白入门必备！ - P42：红帽RHCE-6. if判断、if单分支、if双分支、if多分支

![](img/c86bffb2c41282a61805ad05f94d250a_1.png)

![](img/c86bffb2c41282a61805ad05f94d250a_3.png)

![](img/c86bffb2c41282a61805ad05f94d250a_5.png)

在本节课中，我们将要学习Shell脚本中的`if`条件判断语句。`if`判断用于根据条件是否成立来执行不同的命令，是脚本编程中实现逻辑控制的核心。我们将从最简单的单分支判断开始，逐步学习双分支和多分支判断，并通过实例演示其用法。

![](img/c86bffb2c41282a61805ad05f94d250a_7.png)

![](img/c86bffb2c41282a61805ad05f94d250a_9.png)

## 概述

`if`判断与我们之前学习的条件测试（如`[ ]`或`test`）功能相似，都是根据条件执行相应操作。但`if`语句结构更清晰，能处理更复杂的逻辑。本节将介绍`if`判断的三种语法结构：单分支、双分支和多分支。

![](img/c86bffb2c41282a61805ad05f94d250a_11.png)

## if单分支判断

![](img/c86bffb2c41282a61805ad05f94d250a_13.png)

![](img/c86bffb2c41282a61805ad05f94d250a_14.png)

![](img/c86bffb2c41282a61805ad05f94d250a_16.png)

![](img/c86bffb2c41282a61805ad05f94d250a_18.png)

单分支判断的特点是：**只能判断条件成立的情况，无法处理条件失败的情况**。也就是说，只有当条件为真时，才会执行指定的命令；如果条件为假，则什么都不做。

![](img/c86bffb2c41282a61805ad05f94d250a_20.png)

![](img/c86bffb2c41282a61805ad05f94d250a_22.png)

以下是`if`单分支的两种语法格式：

**语法格式一：**
```bash
if [ 条件 ]; then
    要执行的命令
fi
```

![](img/c86bffb2c41282a61805ad05f94d250a_24.png)

![](img/c86bffb2c41282a61805ad05f94d250a_26.png)

**语法格式二：**
```bash
if [ 条件 ]
then
    要执行的命令
fi
```

两种语法效果完全相同。第一种更为常用，第二种的`then`另起一行有时看起来更清晰。为了代码美观，建议对`then`后面的命令进行缩进。

![](img/c86bffb2c41282a61805ad05f94d250a_28.png)

现在，让我们通过一个实例来理解单分支判断。我们将创建一个脚本，判断`/etc/passwd`文件是否存在。

![](img/c86bffb2c41282a61805ad05f94d250a_30.png)

1.  进入脚本目录并创建文件：
    ```bash
    cd /script
    vim if_01.sh
    ```

![](img/c86bffb2c41282a61805ad05f94d250a_32.png)

![](img/c86bffb2c41282a61805ad05f94d250a_34.png)

2.  编写脚本内容：
    ```bash
    #!/bin/bash
    # 判断 /etc/passwd 文件是否存在
    if [ -f /etc/passwd ]; then
        echo "文件存在"
    fi
    ```

![](img/c86bffb2c41282a61805ad05f94d250a_36.png)

![](img/c86bffb2c41282a61805ad05f94d250a_38.png)

![](img/c86bffb2c41282a61805ad05f94d250a_40.png)

3.  给脚本添加执行权限并运行：
    ```bash
    chmod +x if_01.sh
    ./if_01.sh
    ```
    如果文件存在，终端会输出“文件存在”。

![](img/c86bffb2c41282a61805ad05f94d250a_42.png)

![](img/c86bffb2c41282a61805ad05f94d250a_44.png)

![](img/c86bffb2c41282a61805ad05f94d250a_46.png)

4.  修改脚本，判断一个不存在的文件：
    ```bash
    #!/bin/bash
    # 判断 /opt/abcd 文件是否存在
    if [ -f /opt/abcd ]; then
        echo "文件存在"
    fi
    ```
    再次运行脚本，将不会有任何输出，因为条件不成立，`then`块内的命令不会执行。

正如我们所见，单分支判断只能处理条件成立的情况。但在实际应用中，我们往往需要根据条件成立与否执行不同的操作，这就需要用到双分支判断。

## if双分支判断

![](img/c86bffb2c41282a61805ad05f94d250a_48.png)

![](img/c86bffb2c41282a61805ad05f94d250a_50.png)

双分支判断在单分支的基础上增加了`else`部分，使得脚本**既能处理条件成立的情况，也能处理条件不成立的情况**。

其语法结构如下：
```bash
if [ 条件 ]; then
    条件成立时执行的命令
else
    条件不成立时执行的命令
fi
```

接下来，我们编写一个双分支判断脚本。这个脚本会检查`/opt/abcd.tt`文件是否存在，如果存在则输出提示，如果不存在则创建该文件。

![](img/c86bffb2c41282a61805ad05f94d250a_52.png)

1.  创建脚本文件：
    ```bash
    vim if_02.sh
    ```

![](img/c86bffb2c41282a61805ad05f94d250a_54.png)

2.  编写脚本内容：
    ```bash
    #!/bin/bash
    # 双分支判断示例
    if [ -f /opt/abcd.tt ]; then
        echo "文件存在"
    else
        touch /opt/abcd.tt
        echo "文件 /opt/abcd.tt 创建成功"
    fi
    ```

3.  执行脚本：
    ```bash
    chmod +x if_02.sh
    ./if_02.sh
    ```
    第一次执行时，由于文件不存在，会执行`else`块中的命令，创建文件并输出提示。再次执行时，因为文件已存在，则会执行`then`块中的命令，输出“文件存在”。

![](img/c86bffb2c41282a61805ad05f94d250a_56.png)

![](img/c86bffb2c41282a61805ad05f94d250a_58.png)

双分支判断非常实用。例如，我们可以编写一个脚本来自动安装软件包：先检查软件包是否已安装，如果已安装则启动服务，如果未安装则先安装再启动服务。

![](img/c86bffb2c41282a61805ad05f94d250a_60.png)

![](img/c86bffb2c41282a61805ad05f94d250a_62.png)

![](img/c86bffb2c41282a61805ad05f94d250a_64.png)

下面是一个安装并启动`vsftpd`服务的示例脚本：

```bash
#!/bin/bash
# 检查并安装 vsftpd 服务
if rpm -q vsftpd &> /dev/null; then
    # 软件包已存在，启动服务
    systemctl start vsftpd
    systemctl enable vsftpd &> /dev/null
    echo "vsftpd 服务已启动。"
else
    # 软件包不存在，安装并启动服务
    echo "正在安装软件包 vsftpd..."
    yum install -y vsftpd &> /dev/null
    systemctl start vsftpd
    systemctl enable vsftpd &> /dev/null
    echo "vsftpd 安装完成，服务已启动。"
fi
```

在这个脚本中，我们使用了命令执行状态返回值`$?`（通过`&>/dev/null`抑制输出后，判断上一条命令是否成功）作为条件，这是一种常见的判断方式。

## 综合实例：猜数字游戏

![](img/c86bffb2c41282a61805ad05f94d250a_66.png)

为了更生动地展示`if`判断的用法，我们编写一个简单的猜数字游戏脚本。这个脚本会生成一个0-9的随机数，让用户来猜。

![](img/c86bffb2c41282a61805ad05f94d250a_68.png)

![](img/c86bffb2c41282a61805ad05f94d250a_70.png)

1.  **生成随机数**：Linux系统中有一个`$RANDOM`环境变量，可以生成0-32767之间的随机数。为了得到0-9的范围，我们使用取余运算：`$RANDOM % 10`。
2.  **获取用户输入**：使用`read -p`命令提示用户输入。
3.  **判断结果**：使用`if`双分支判断用户输入的数字是否与随机数相等。

创建脚本文件并编写内容：
```bash
vim if_03.sh
```
```bash
#!/bin/bash
# 猜数字游戏
# 生成0-9的随机数
correct_num=$((RANDOM % 10))

![](img/c86bffb2c41282a61805ad05f94d250a_72.png)

![](img/c86bffb2c41282a61805ad05f94d250a_74.png)

read -p "请输入0-9之间的一个数字（猜对有奖哦！）: " guess_num

if [ $guess_num -eq $correct_num ]; then
    echo "恭喜你，猜对了！"
    echo "奖励哇塞女孩一枚！"
else
    echo "很遗憾，猜错了。"
    echo "哇塞女孩正在等你，请继续努力！"
    echo "本次的正确数字是：$correct_num"
fi
```

给脚本执行权限并运行，体验一下猜数字的乐趣吧！

## if多分支判断

![](img/c86bffb2c41282a61805ad05f94d250a_76.png)

当我们需要判断多个条件时，就需要使用多分支判断。其语法结构如下：
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
脚本会**从上到下依次判断条件**，一旦某个条件成立，就执行对应的命令块，然后直接结束整个`if`判断，后面的条件将不再检查。

让我们通过一个判断成绩等级的脚本来说明：

![](img/c86bffb2c41282a61805ad05f94d250a_78.png)

![](img/c86bffb2c41282a61805ad05f94d250a_80.png)

1.  创建脚本文件：
    ```bash
    vim if_04.sh
    ```

2.  编写脚本内容：
    ```bash
    #!/bin/bash
    # 成绩等级判断
    read -p "请输入您的成绩（0-100）: " score

    if [ $score -ge 90 ]; then
        echo "您的成绩非常优秀！奖励哇塞女孩一枚！"
    elif [ $score -ge 80 ]; then
        echo "您的成绩比较优秀！离哇塞女孩仅一步之遥。"
    elif [ $score -ge 70 ]; then
        echo "您的成绩一般，还需努力。"
    elif [ $score -ge 60 ]; then
        echo "您的成绩勉强及格。"
    else
        echo "成绩未及格，请继续加油！"
    fi
    ```

3.  执行脚本，输入不同的成绩，观察输出结果。例如，输入85分，脚本会匹配第二个条件（`-ge 80`），输出“比较优秀”，而不会继续判断后面的条件。

**重要提示**：在`[ ]`条件测试中，**括号内每一项前后都必须有空格**，这是一个非常常见的错误来源。

## 总结

![](img/c86bffb2c41282a61805ad05f94d250a_82.png)

本节课我们一起学习了Shell脚本中强大的`if`条件判断语句。

![](img/c86bffb2c41282a61805ad05f94d250a_84.png)

*   **if单分支**：使用`if [条件]; then ... fi`结构，只能处理条件为真的情况。
*   **if双分支**：使用`if [条件]; then ... else ... fi`结构，可以分别处理条件为真和为假的情况。
*   **if多分支**：使用`if ... elif ... elif ... else ... fi`结构，可以处理多个连续的条件判断。

![](img/c86bffb2c41282a61805ad05f94d250a_86.png)

`if`判断是脚本逻辑控制的基石，结合之前学习的变量、条件测试和运算，我们已经可以编写出功能丰富的脚本。记住，实践是学习脚本的最佳途径，多阅读、多修改、多编写，你的Shell脚本能力一定会稳步提升。