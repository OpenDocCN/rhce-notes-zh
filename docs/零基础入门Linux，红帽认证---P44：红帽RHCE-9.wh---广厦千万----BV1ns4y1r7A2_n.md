# Linux脚本编程：P44：while循环 🔄

![](img/b1ba19b0b3bed34ea85b685180ea0a14_0.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_2.png)

在本节课中，我们将要学习Shell脚本编程中的`while`循环。`while`循环是一种条件循环，只要指定的条件成立，它就会重复执行循环体内的命令。这与我们之前学习的`for`循环功能相似，但应用场景和循环机制有所不同。我们将通过实例来理解其语法、特性以及如何控制循环。

## 概述 📋

![](img/b1ba19b0b3bed34ea85b685180ea0a14_4.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_6.png)

`while`循环的核心在于“条件判断”。只要条件成立，循环就会持续执行。这与`for`循环基于“值列表”进行迭代的方式不同。`while`循环常用于需要持续监控或等待某个条件变化的场景，有时也被用来创建“死循环”。

![](img/b1ba19b0b3bed34ea85b685180ea0a14_8.png)

## while循环的基本语法 💻

![](img/b1ba19b0b3bed34ea85b685180ea0a14_10.png)

`while`循环的语法结构如下：

![](img/b1ba19b0b3bed34ea85b685180ea0a14_12.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_14.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_16.png)

```bash
while [ 条件 ]
do
    # 要执行的命令
done
```

![](img/b1ba19b0b3bed34ea85b685180ea0a14_18.png)

或者，使用双括号进行算术比较（更现代的方式）：

![](img/b1ba19b0b3bed34ea85b685180ea0a14_20.png)

```bash
while (( 条件 ))
do
    # 要执行的命令
done
```

**关键点**：循环会持续检查`条件`。如果条件为真（成立），则执行`do`和`done`之间的命令；如果条件为假（不成立），则循环结束。

## 创建简单的while循环示例 🧪

让我们编写一个简单的脚本`while.sh`来演示。

1.  **基础死循环**：创建一个条件永远成立的循环。
    ```bash
    #!/bin/bash
    while [ 1 -eq 1 ]
    do
        echo "Hello"
    done
    ```
    执行这个脚本，它将不停地输出“Hello”，因为条件`1等于1`永远成立。这是一个“死循环”，需要使用 `Ctrl+C` 来强制终止。

2.  **使用冒号(:)的特殊死循环**：在`while`循环中，单独的冒号`:`是一个特殊的命令，它总是返回真（退出状态码为0）。因此，`while :` 是创建无限循环的一种简洁写法。
    ```bash
    #!/bin/bash
    while :
    do
        echo "This is an infinite loop."
    done
    ```

## 控制循环次数 🔢

虽然`while`常用于条件循环或死循环，但我们也可以通过变量来控制其执行次数，使其行为类似`for`循环。

上一节我们介绍了死循环，本节中我们来看看如何让`while`循环执行有限的次数。

**思路**：我们需要一个计数器变量，并在每次循环后更新它，直到不满足条件为止。

以下是实现循环5次的示例：
```bash
#!/bin/bash
number=1 # 初始化计数器
while [ $number -le 5 ] # 条件：number小于等于5时循环
do
    echo "这是第 $number 次循环。"
    let number++ # 等同于 number=$((number + 1))，计数器加1
done
```
**代码解释**：
*   `number=1`：设置初始值。
*   `[ $number -le 5 ]`：循环条件。`-le`表示“小于等于”。
*   `let number++`：每次循环后，变量`number`的值增加1。这是 `let “number=number+1”` 的简写形式。

执行后，脚本会精确地输出5行信息，然后结束。

## while循环的实用案例：猜数字游戏 🎮

![](img/b1ba19b0b3bed34ea85b685180ea0a14_22.png)

现在，我们将运用`while`循环来改进之前只能猜一次的数字游戏脚本，使其可以持续猜测，直到猜对为止。

![](img/b1ba19b0b3bed34ea85b685180ea0a14_24.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_25.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_26.png)

以下是实现步骤：
1.  使用`while :`创建一个无限循环，让游戏可以一直进行。
2.  在循环体内，让用户输入猜测的数字。
3.  判断用户输入与随机数是否相等。
4.  如果猜对，则输出成功信息并**使用`exit`命令退出整个脚本**，结束循环。
5.  如果猜错，则提示继续猜测，循环继续。

![](img/b1ba19b0b3bed34ea85b685180ea0a14_28.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_29.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_30.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_31.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_33.png)

**示例脚本 `guess.sh`**：
```bash
#!/bin/bash
# 生成一个1-10之间的随机数
target=$((RANDOM % 10 + 1))

![](img/b1ba19b0b3bed34ea85b685180ea0a14_35.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_36.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_37.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_39.png)

echo “猜数字游戏开始！目标数字在1到10之间。”

![](img/b1ba19b0b3bed34ea85b685180ea0a14_41.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_43.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_45.png)

while : # 无限循环
do
    read -p “请输入你猜的数字（1-10）: ” guess
    if [ $guess -eq $target ]
    then
        echo “恭喜你！猜对了！数字就是 $target。”
        exit 0 # 猜对后退出脚本
    else
        echo “猜错了，再试一次！”
    fi
done
```
**脚本要点**：
*   `$((RANDOM % 10 + 1))`：`RANDOM`是Bash的内置变量，每次调用产生一个随机整数。`% 10`取余数得到0-9，`+1`后得到1-10。
*   `exit 0`：退出脚本并返回状态码0（表示成功）。这是跳出`while`死循环并结束脚本的关键。

![](img/b1ba19b0b3bed34ea85b685180ea0a14_47.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_48.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_50.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_51.png)

## while循环的应用场景 💡

![](img/b1ba19b0b3bed34ea85b685180ea0a14_53.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_55.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_57.png)

`while`循环在系统管理中有很多实际用途：
*   **服务/进程监控**：持续检查某个服务是否在运行，如果停止则尝试重启。
    ```bash
    while :
    do
        if ! pgrep -x “nginx” > /dev/null
        then
            echo “Nginx 未运行，正在启动...”
            systemctl start nginx
        fi
        sleep 10 # 每隔10秒检查一次
    done
    ```
*   **等待某个条件达成**：例如，等待一个文件被创建，或者等待数据库启动完成。
    ```bash
    echo “等待文件 /tmp/ready.flag 出现...”
    while [ ! -f /tmp/ready.flag ]
    do
        sleep 2
    done
    echo “文件已就绪，开始后续操作。”
    ```
*   **读取文件每一行**：这是一种非常常见的用法。
    ```bash
    while IFS= read -r line
    do
        echo “处理行: $line”
    done < “filename.txt”
    ```

![](img/b1ba19b0b3bed34ea85b685180ea0a14_59.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_60.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_62.png)

## 总结 🎯

![](img/b1ba19b0b3bed34ea85b685180ea0a14_63.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_64.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_65.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_67.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_69.png)

本节课中我们一起学习了`while`循环。
*   **核心概念**：`while`循环基于**条件判断**进行重复执行。条件为真则循环，为假则停止。
*   **基本语法**：`while [ condition ]; do commands; done` 或 `while (( arithmetic condition )); do commands; done`。
*   **死循环**：可以通过永远为真的条件（如`while [ 1 -eq 1 ]`）或`while :`创建。
*   **循环控制**：通过结合变量和`let`命令，可以控制`while`循环的执行次数。
*   **跳出循环**：在循环体内使用`exit`可以退出整个脚本；使用`break`命令可以跳出当前循环（后续课程会详细讲解）。
*   **与for循环对比**：`for`循环更适用于已知迭代次数或遍历集合的场景；`while`循环更适用于条件未知、需要持续监控或等待的场景。

![](img/b1ba19b0b3bed34ea85b685180ea0a14_71.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_73.png)

![](img/b1ba19b0b3bed34ea85b685180ea0a14_75.png)

理解`while`循环是掌握Shell脚本自动化任务的关键一步，它使得脚本能够根据动态变化的条件做出响应。