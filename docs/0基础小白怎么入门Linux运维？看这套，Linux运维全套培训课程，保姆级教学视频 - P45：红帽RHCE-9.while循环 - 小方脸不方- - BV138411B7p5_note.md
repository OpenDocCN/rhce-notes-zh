# Linux运维全套培训课程：P45：红帽RHCE-9.while循环 🔄

![](img/0137a954d280dd195c25eb419ff03f9b_0.png)

![](img/0137a954d280dd195c25eb419ff03f9b_2.png)

在本节课中，我们将要学习Shell脚本中的`while`循环。`while`循环与之前学过的`for`循环功能类似，都是用于重复执行命令，但它们的应用场景和循环机制有所不同。我们将通过实例来理解`while`循环的语法、特性以及如何控制循环。

## 概述：什么是while循环？

上一节我们介绍了`for`循环，它根据给定的值列表来执行循环。本节中我们来看看`while`循环。`while`循环的核心是**条件判断**：只要给定的条件成立（为真），它就会重复执行循环体内的命令。这与`for`循环基于“值”的循环方式不同。

![](img/0137a954d280dd195c25eb419ff03f9b_4.png)

![](img/0137a954d280dd195c25eb419ff03f9b_6.png)

![](img/0137a954d280dd195c25eb419ff03f9b_8.png)

## while循环的基本语法

`while`循环的基本语法结构如下：

![](img/0137a954d280dd195c25eb419ff03f9b_10.png)

```bash
while [ 条件 ]
do
    # 要执行的命令
done
```

![](img/0137a954d280dd195c25eb419ff03f9b_12.png)

![](img/0137a954d280dd195c25eb419ff03f9b_14.png)

![](img/0137a954d280dd195c25eb419ff03f9b_16.png)

只要`[ 条件 ]`判断为真，`do`和`done`之间的命令就会被反复执行。

## 死循环演示

让我们通过一个简单的脚本来理解`while`循环。首先，我们创建一个名为`while.sh`的脚本。

```bash
#!/bin/bash
while [ 1 -eq 1 ]
do
    echo "hello baby"
done
```

![](img/0137a954d280dd195c25eb419ff03f9b_18.png)

在这个脚本中，条件`[ 1 -eq 1 ]`永远为真（1永远等于1），因此`echo “hello baby”`这条命令会无限次地执行下去，形成一个“死循环”。运行脚本后，可以使用 `Ctrl + C` 来强制终止它。

这个例子展示了`while`循环的核心特点：**条件为真即循环**。

## 控制循环次数

虽然`while`循环常用于条件性循环或死循环，但我们也可以通过变量来控制它的执行次数，使其像`for`循环一样执行固定次数。

以下是实现循环5次的脚本示例：

```bash
#!/bin/bash
number=1
while [ $number -le 5 ]
do
    echo “第 $number 次循环：你好，宝贝”
    let number++  # 等同于 number=$((number+1))
done
```

让我们分析一下这个脚本的执行过程：
1.  初始时，变量`number`的值为1。
2.  条件`[ $number -le 5 ]`判断1是否小于等于5，结果为真，进入循环体。
3.  执行`echo`命令。
4.  执行`let number++`，将`number`的值增加1，变为2。
5.  循环回到条件判断，此时`number`为2，条件依然为真，继续执行。
6.  此过程持续，直到`number`变为6时，条件`[ 6 -le 5 ]`为假，循环结束。

这样，循环体一共执行了5次。虽然可以实现，但需要**指定循环次数**的场景，使用`for`循环通常更加直观和方便。

## while循环的特殊用法：冒号

在`while`循环中，可以使用一个特殊的符号——**冒号（`:`）** 作为条件。冒号在Shell中是一个空命令，总是返回真（成功）。因此，`while :` 就代表一个无限循环，是编写死循环的简洁写法。

示例脚本：
```bash
#!/bin/bash
while :
do
    echo “这是一个无限循环”
    sleep 1 # 暂停1秒，避免输出过快
done
```

## 实战应用：猜数字游戏升级版

之前我们写过一个只能猜一次的猜数字脚本。现在，利用`while`死循环，我们可以升级它，让用户能一直猜，直到猜对为止。

以下是升级后的`choujiang.sh`脚本：

![](img/0137a954d280dd195c25eb419ff03f9b_20.png)

![](img/0137a954d280dd195c25eb419ff03f9b_22.png)

```bash
#!/bin/bash
# 生成一个1-10的随机数
SUIJI=$((RANDOM % 10 + 1))

![](img/0137a954d280dd195c25eb419ff03f9b_23.png)

![](img/0137a954d280dd195c25eb419ff03f9b_25.png)

![](img/0137a954d280dd195c25eb419ff03f9b_27.png)

echo “哇塞女孩藏在1-10的一个数字后面，猜猜是几？”

![](img/0137a954d280dd195c25eb419ff03f9b_29.png)

![](img/0137a954d280dd195c25eb419ff03f9b_31.png)

![](img/0137a954d280dd195c25eb419ff03f9b_33.png)

![](img/0137a954d280dd195c25eb419ff03f9b_35.png)

while :
do
    read -p “请输入你猜的数字（1-10）: ” CAI
    if [ $CAI -eq $SUIJI ]
    then
        echo “恭喜你猜对了！哇塞女孩就是 $SUIJI 号！”
        exit 0 # 猜对后退出脚本
    else
        echo “很遗憾，$CAI 不对，再试试吧！”
    fi
done
```

![](img/0137a954d280dd195c25eb419ff03f9b_37.png)

![](img/0137a954d280dd195c25eb419ff03f9b_39.png)

**脚本逻辑说明：**
1.  使用`while :`创建一个无限循环。
2.  在循环体内，让用户输入猜测的数字。
3.  使用`if`判断用户是否猜对。
4.  如果猜对，打印成功信息并使用 **`exit 0`** 命令退出整个脚本，从而结束循环。
5.  如果猜错，提示用户继续猜，循环继续。

![](img/0137a954d280dd195c25eb419ff03f9b_41.png)

![](img/0137a954d280dd195c25eb419ff03f9b_43.png)

![](img/0137a954d280dd195c25eb419ff03f9b_45.png)

这个例子结合了`while`循环和条件判断，实现了一个交互式的、可重复执行的脚本。

![](img/0137a954d280dd195c25eb419ff03f9b_47.png)

![](img/0137a954d280dd195c25eb419ff03f9b_49.png)

![](img/0137a954d280dd195c25eb419ff03f9b_51.png)

## while循环的应用场景

![](img/0137a954d280dd195c25eb419ff03f9b_53.png)

![](img/0137a954d280dd195c25eb419ff03f9b_55.png)

![](img/0137a954d280dd195c25eb419ff03f9b_57.png)

了解了基本用法后，以下是`while`循环的一些典型应用场景：

![](img/0137a954d280dd195c25eb419ff03f9b_59.png)

![](img/0137a954d280dd195c25eb419ff03f9b_61.png)

*   **监控与等待**：持续检查某个条件是否满足，如等待某个文件生成、等待服务启动、监控磁盘使用率等。
    ```bash
    while [ ! -f /tmp/flag.file ]
    do
        echo “等待文件生成...”
        sleep 5
    done
    ```
*   **读取文件内容**：逐行读取一个文件并进行处理。
    ```bash
    while read line
    do
        echo “处理行： $line”
    done < /path/to/file.txt
    ```
*   **用户交互菜单**：显示一个菜单，根据用户输入执行不同操作，直到用户选择退出。
    ```bash
    while :
    do
        echo “1. 备份”
        echo “2. 恢复”
        echo “3. 退出”
        read -p “请选择: ” OPT
        case $OPT in
            1) echo “执行备份...” ;;
            2) echo “执行恢复...” ;;
            3) exit 0 ;;
            *) echo “输入错误” ;;
        esac
    done
    ```

![](img/0137a954d280dd195c25eb419ff03f9b_63.png)

![](img/0137a954d280dd195c25eb419ff03f9b_65.png)

![](img/0137a954d280dd195c25eb419ff03f9b_67.png)

![](img/0137a954d280dd195c25eb419ff03f9b_69.png)

## 总结

![](img/0137a954d280dd195c25eb419ff03f9b_71.png)

![](img/0137a954d280dd195c25eb419ff03f9b_73.png)

本节课中我们一起学习了Shell脚本中的`while`循环。我们掌握了它的基本语法`while [条件]; do ... done`，理解了它基于**条件判断**进行循环的特性。我们探讨了如何创建无限循环（死循环），包括使用永远为真的条件或特殊的`:`命令。同时，我们也学习了如何通过修改变量来控制`while`循环的执行次数。最后，通过升级“猜数字”脚本，我们实践了`while`循环在实现交互式、重复性任务中的应用。记住，`while`循环在需要**持续监控**或**条件满足前反复尝试**的场景中非常有用。