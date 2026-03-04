# Linux运维培训教程：P44：红帽RHCE-9.while循环 🌀

![](img/57bde70f7a9949d67748471decfa6632_0.png)

![](img/57bde70f7a9949d67748471decfa6632_2.png)

在本节课中，我们将要学习Shell脚本中的`while`循环。`while`循环与之前学过的`for`循环功能相似，都是用于重复执行命令，但它们的应用场景和循环机制有所不同。`while`循环的核心是**条件判断**，只要条件成立，就会一直执行循环体内的命令，因此常被用于创建“死循环”或需要持续监控的场景。

## 概述

![](img/57bde70f7a9949d67748471decfa6632_4.png)

![](img/57bde70f7a9949d67748471decfa6632_6.png)

![](img/57bde70f7a9949d67748471decfa6632_8.png)

上一节我们介绍了`for`循环，它基于一个给定的值列表进行循环。本节中我们来看看`while`循环。`while`循环的循环机制基于一个**条件表达式**。只要该条件为真（成立），循环就会一直执行下去。这使得它非常适合用于需要持续运行直到某个条件被满足（如用户猜对数字、文件达到特定大小）的脚本。

## while循环的基本语法

![](img/57bde70f7a9949d67748471decfa6632_10.png)

`while`循环的基本语法结构如下：
```bash
while [ 条件 ]
do
    # 要执行的命令
done
```
只要`[ 条件 ]`判断为真，`do`和`done`之间的命令就会被重复执行。

![](img/57bde70f7a9949d67748471decfa6632_12.png)

![](img/57bde70f7a9949d67748471decfa6632_14.png)

![](img/57bde70f7a9949d67748471decfa6632_16.png)

为了更直观地理解，我们可以与`for`循环做个对比：
*   `for`循环：`for 变量 in 值列表`，循环**次数由值列表决定**。
*   `while`循环：`while [ 条件 ]`，循环**次数由条件是否成立决定**。

![](img/57bde70f7a9949d67748471decfa6632_18.png)

![](img/57bde70f7a9949d67748471decfa6632_20.png)

## 一个简单的死循环示例

让我们编写一个最简单的`while`死循环脚本。

1.  创建脚本文件：
    ```bash
    vim while_demo.sh
    ```
2.  输入以下内容：
    ```bash
    #!/bin/bash
    while [ 1 -eq 1 ]
    do
        echo "Hello"
    done
    ```
3.  保存退出后，运行脚本：
    ```bash
    bash while_demo.sh
    ```
    你会发现终端会开始无限地、快速地打印“Hello”。这是因为条件`[ 1 -eq 1 ]`（1等于1）永远为真。要停止这个脚本，需要按`Ctrl+C`强制中断。

这个例子展示了`while`循环作为“死循环”的典型应用：**只要条件成立，就永不停止**。

## 控制循环次数

虽然`while`常用于条件触发的循环，但我们也可以通过变量控制它的循环次数，使其像`for`循环一样执行固定次数。

以下是实现循环5次的示例：
```bash
#!/bin/bash
number=1
while [ $number -le 5 ]
do
    echo “这是第 $number 次循环”
    let number++  # 等同于 number=$((number+1))
done
```
**代码解释：**
*   `number=1`：初始化一个计数器变量。
*   `[ $number -le 5 ]`：循环条件是“变量`number`的值**小于等于5**”。
*   `let number++`：每次循环后，将变量`number`的值加1。这是实现次数控制的关键。
*   执行过程：`number`从1开始，每次循环加1，当`number`变为6时，条件`[ 6 -le 5 ]`为假，循环结束。

运行这个脚本，它会精确地输出5行信息。这演示了如何通过**在循环体内改变条件变量的值**，来控制`while`循环的退出。

> **提示**：如果需要执行固定次数的循环，使用`for`循环通常更加直观和方便。`while`循环更擅长处理条件未知或动态变化的场景。

## 使用冒号(:)创建无限循环

在`while`循环中，有一个特殊的用法：使用冒号`:`作为条件。冒号在Shell中是一个空命令，总是返回真（退出状态码为0）。因此，`while :`会创建一个标准的无限循环。

以下是使用示例：
```bash
#!/bin/bash
while :
do
    echo “这是一个无限循环”
    sleep 1 # 暂停1秒，避免输出过快
done
```
这个脚本会每秒打印一次信息，直到你按下`Ctrl+C`。这种写法比`while [ 1 -eq 1 ]`更简洁、更专业。

## 实践案例：猜数字游戏升级版

还记得我们之前编写的只能猜一次的猜数字脚本吗？现在，我们可以利用`while`循环将其升级为一个可以持续猜测，直到猜对为止的游戏。

![](img/57bde70f7a9949d67748471decfa6632_22.png)

![](img/57bde70f7a9949d67748471decfa6632_24.png)

![](img/57bde70f7a9949d67748471decfa6632_26.png)

以下是升级后的脚本内容：
```bash
#!/bin/bash
# 生成一个1-10的随机数
target=$((RANDOM % 10 + 1))

![](img/57bde70f7a9949d67748471decfa6632_28.png)

![](img/57bde70f7a9949d67748471decfa6632_30.png)

![](img/57bde70f7a9949d67748471decfa6632_32.png)

![](img/57bde70f7a9949d67748471decfa6632_33.png)

echo “猜数字游戏开始！数字在1-10之间。”

![](img/57bde70f7a9949d67748471decfa6632_35.png)

![](img/57bde70f7a9949d67748471decfa6632_37.png)

![](img/57bde70f7a9949d67748471decfa6632_39.png)

![](img/57bde70f7a9949d67748471decfa6632_41.png)

while :
do
    read -p “请输入你猜的数字：” guess
    if [ $guess -eq $target ]
    then
        echo “恭喜你！猜对了！”
        exit 0 # 猜对后退出脚本
    else
        echo “很遗憾，猜错了，再试一次！”
    fi
done
```
**脚本逻辑说明：**
1.  `target=$((RANDOM % 10 + 1))`：生成目标数字。
2.  `while :`：开启一个无限循环。
3.  在循环体内，提示用户输入并判断。
4.  如果猜对（`if`条件成立），则打印成功信息并执行`exit 0`**退出整个脚本**，从而结束无限循环。
5.  如果猜错，则提示后继续下一次循环。

![](img/57bde70f7a9949d67748471decfa6632_43.png)

![](img/57bde70f7a9949d67748471decfa6632_45.png)

![](img/57bde70f7a9949d67748471decfa6632_47.png)

这个案例完美结合了`while`无限循环和条件判断，实现了交互式的持续功能，是`while`循环的一个典型应用。

![](img/57bde70f7a9949d67748471decfa6632_49.png)

![](img/57bde70f7a9949d67748471decfa6632_51.png)

![](img/57bde70f7a9949d67748471decfa6632_53.png)

## while循环的应用场景

![](img/57bde70f7a9949d67748471decfa6632_55.png)

![](img/57bde70f7a9949d67748471decfa6632_57.png)

![](img/57bde70f7a9949d67748471decfa6632_59.png)

![](img/57bde70f7a9949d67748471decfa6632_61.png)

以下是`while`循环常见的几种应用场景：

![](img/57bde70f7a9949d67748471decfa6632_63.png)

![](img/57bde70f7a9949d67748471decfa6632_65.png)

![](img/57bde70f7a9949d67748471decfa6632_67.png)

![](img/57bde70f7a9949d67748471decfa6632_69.png)

*   **持续监控**：例如，监控一个日志文件，当出现特定错误关键词时发送警报。
    ```bash
    while :
    do
        if grep -q “ERROR” /var/log/myapp.log; then
            send_alert “发现错误！”
        fi
        sleep 60 # 每分钟检查一次
    done
    ```
*   **等待条件满足**：例如，等待某个进程启动或某个文件被创建后再执行后续操作。
    ```bash
    while [ ! -f /tmp/flag.file ]
    do
        echo “等待文件生成...”
        sleep 2
    done
    echo “文件已就绪，开始处理...”
    ```
*   **读取文件每一行**：虽然`for`循环也能读文件，但`while`循环在处理包含特殊字符（如空格）的行时更安全。
    ```bash
    while IFS= read -r line
    do
        echo “处理行：$line”
    done < /path/to/file.txt
    ```

![](img/57bde70f7a9949d67748471decfa6632_71.png)

![](img/57bde70f7a9949d67748471decfa6632_73.png)

![](img/57bde70f7a9949d67748471decfa6632_75.png)

## 总结

![](img/57bde70f7a9949d67748471decfa6632_77.png)

![](img/57bde70f7a9949d67748471decfa6632_79.png)

![](img/57bde70f7a9949d67748471decfa6632_81.png)

![](img/57bde70f7a9949d67748471decfa6632_83.png)

本节课中我们一起学习了Shell脚本中的`while`循环。我们了解了它与`for`循环的核心区别：`while`基于**条件判断**进行循环，而`for`基于**值列表**。我们掌握了`while`循环的基本语法，学会了如何创建无限循环（包括使用冒号的简写形式），以及如何通过控制变量来限制循环次数。最后，通过升级猜数字游戏的实践案例，我们看到了`while`循环在创建交互式、持续运行脚本中的强大作用。

![](img/57bde70f7a9949d67748471decfa6632_85.png)

![](img/57bde70f7a9949d67748471decfa6632_87.png)

![](img/57bde70f7a9949d67748471decfa6632_89.png)

记住，当你的脚本需要**反复执行直到某个条件发生变化**时，`while`循环是你的最佳选择。