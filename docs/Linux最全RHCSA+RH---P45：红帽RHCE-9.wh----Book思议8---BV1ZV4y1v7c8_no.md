# Linux最全RHCSA+RHCE培训教程合集：P45：红帽RHCE-9.while循环 📘

![](img/f4da95fb20b47138e86edb6f3bf76117_0.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_2.png)

在本节课中，我们将要学习Shell脚本编程中的`while`循环。`while`循环与之前学过的`for`循环功能类似，都是用于重复执行命令，但它们的应用场景和循环机制有所不同。我们将通过实例来理解`while`循环的基本语法、死循环的写法以及如何在实际脚本中应用它。

---

![](img/f4da95fb20b47138e86edb6f3bf76117_4.png)

## 循环机制对比 🔄

![](img/f4da95fb20b47138e86edb6f3bf76117_6.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_8.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_10.png)

上一节我们介绍了`for`循环，它通过遍历一个给定的值列表来控制循环次数。本节中我们来看看`while`循环。

`while`循环的核心是**条件判断**。只要给定的条件成立（为真），它就会重复执行循环体内的命令。这与`for`循环基于“值列表”的机制不同。`for`循环有明确的循环次数（例如254次或1万次），而`while`循环则可能因为条件一直成立而成为“死循环”。

![](img/f4da95fb20b47138e86edb6f3bf76117_12.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_14.png)

以下是两种循环的基本语法对比：

![](img/f4da95fb20b47138e86edb6f3bf76117_16.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_18.png)

*   **`for`循环语法**：
    ```bash
    for 变量名 in 值列表
    do
        要执行的命令
    done
    ```
    循环次数由“值列表”中的元素个数决定。

![](img/f4da95fb20b47138e86edb6f3bf76117_20.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_22.png)

*   **`while`循环语法**：
    ```bash
    while [ 条件 ]
    do
        要执行的命令
    done
    ```
    循环次数由“条件”是否成立决定。

---

![](img/f4da95fb20b47138e86edb6f3bf76117_24.png)

## 基础`while`循环示例 🧪

![](img/f4da95fb20b47138e86edb6f3bf76117_26.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_28.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_30.png)

让我们编写一个简单的`while`循环脚本来理解其工作原理。

![](img/f4da95fb20b47138e86edb6f3bf76117_32.png)

1.  创建一个名为 `while.sh` 的脚本文件。
2.  输入以下内容：
    ```bash
    #!/bin/bash
    while [ 1 -eq 1 ]
    do
        echo "hello baby"
    done
    ```
    这个脚本中，条件 `[ 1 -eq 1 ]` 永远成立（1永远等于1），因此 `echo "hello baby"` 这条命令会被无限重复执行，形成一个死循环。
3.  运行脚本，你会看到终端上不断打印“hello baby”。可以使用 `Ctrl + C` 组合键来强制终止这个死循环。

![](img/f4da95fb20b47138e86edb6f3bf76117_34.png)

---

![](img/f4da95fb20b47138e86edb6f3bf76117_36.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_38.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_40.png)

## 控制`while`循环次数 🔢

虽然`while`循环常用于条件判断，但我们也可以通过变量来控制其循环次数，使其像`for`循环一样执行固定次数。

以下是实现方法：
1.  修改 `while.sh` 脚本：
    ```bash
    #!/bin/bash
    number=1
    while [ $number -le 5 ]
    do
        echo "你好，宝贝"
        let number++  # 等同于 number=$((number+1))
    done
    ```
    *   `number=1`：初始化一个计数器变量。
    *   `[ $number -le 5 ]`：循环条件是“变量`number`的值小于等于5”。
    *   `let number++`：每次循环后，将变量`number`的值加1。这是 `number=$((number+1))` 的简写形式。

2.  运行脚本，你会看到“你好，宝贝”被准确地打印了5次。
    *   第一次循环：`number=1`，条件成立，打印，然后`number`变为2。
    *   第二次循环：`number=2`，条件成立，打印，然后`number`变为3。
    *   ...
    *   第五次循环：`number=5`，条件成立，打印，然后`number`变为6。
    *   第六次循环：`number=6`，条件不成立（6不大于5），循环结束。

**注意**：如果需要执行固定次数的循环，使用`for`循环通常更直观。但了解`while`循环也能实现此功能是有益的。

---

## 特殊的死循环符号 `:` ⏳

在`while`循环中，有一个特殊的符号 `:`（冒号）。它代表一个永远为真的条件，用于快速创建一个不需要复杂条件判断的无限循环（死循环）。

![](img/f4da95fb20b47138e86edb6f3bf76117_42.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_43.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_45.png)

示例：
```bash
#!/bin/bash
while :
do
    echo "这是一个死循环"
done
```
运行此脚本，它将不停地打印“这是一个死循环”，直到你按下 `Ctrl + C`。

![](img/f4da95fb20b47138e86edb6f3bf76117_47.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_49.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_51.png)

---

![](img/f4da95fb20b47138e86edb6f3bf76117_53.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_55.png)

## 实战应用：猜数字游戏 🎮

![](img/f4da95fb20b47138e86edb6f3bf76117_57.png)

之前我们写过一个只能猜一次的猜数字脚本。现在，利用`while`死循环，我们可以改造它，让用户能一直猜，直到猜对为止。

1.  创建一个名为 `choujiang.sh` 的脚本。
2.  输入以下内容：
    ```bash
    #!/bin/bash
    # 生成一个1-10的随机数
    NUMBER=$((RANDOM % 10 + 1))

    echo “猜猜看，哇塞女孩是哪个数字（1-10）？”

    while :
    do
        read -p “请输入数字：” INPUT_NUM
        if [ $INPUT_NUM -eq $NUMBER ]
        then
            echo “恭喜你猜对了！哇塞女孩就是 $NUMBER 号！”
            exit 0  # 猜对后退出脚本
        else
            echo “很遗憾，$INPUT_NUM 不对，再试试吧！”
        fi
    done
    ```
    *   `while :`：创建一个死循环，让游戏可以持续进行。
    *   `exit 0`：当用户猜对时，使用 `exit` 命令退出整个脚本，从而结束死循环。

![](img/f4da95fb20b47138e86edb6f3bf76117_59.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_61.png)

3.  运行脚本，你可以多次尝试猜数字，直到猜对后脚本自动结束。

![](img/f4da95fb20b47138e86edb6f3bf76117_63.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_65.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_67.png)

---

![](img/f4da95fb20b47138e86edb6f3bf76117_68.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_70.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_72.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_74.png)

## `while`循环的应用场景 💡

![](img/f4da95fb20b47138e86edb6f3bf76117_76.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_78.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_80.png)

了解了基本用法后，我们来看看`while`循环的一些典型应用场景：

![](img/f4da95fb20b47138e86edb6f3bf76117_82.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_84.png)

*   **交互式程序**：如上面的猜数字游戏，需要根据用户输入持续响应。
*   **监控与等待**：反复检查某个条件是否满足，然后执行操作。
    *   **示例**：监控一个日志文件的大小，当它超过1GB时自动执行备份和清理。
        ```bash
        while :
        do
            FILESIZE=$(du -m /var/log/app.log | awk ‘{print $1}’)
            if [ $FILESIZE -gt 1024 ]; then
                backup_log.sh  # 执行备份脚本
                exit 0
            fi
            sleep 300  # 每5分钟检查一次
        done
        ```
*   **读取文件内容**：可以逐行读取一个文件进行处理（通常与 `read` 命令结合使用）。

![](img/f4da95fb20b47138e86edb6f3bf76117_86.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_88.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_90.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_92.png)

---

![](img/f4da95fb20b47138e86edb6f3bf76117_94.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_96.png)

## 总结 📝

![](img/f4da95fb20b47138e86edb6f3bf76117_98.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_100.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_102.png)

本节课中我们一起学习了Shell脚本中的`while`循环。

![](img/f4da95fb20b47138e86edb6f3bf76117_104.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_106.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_108.png)

*   **核心概念**：`while`循环基于**条件判断**进行重复执行，只要条件为真，循环就会继续。
*   **与`for`循环的区别**：`for`循环遍历集合，`while`循环依赖条件状态。
*   **基础语法**：`while [ condition ]; do commands; done`
*   **死循环**：可以使用永远为真的条件（如 `[ 1 -eq 1 ]`）或特殊符号 `:` 来创建。
*   **循环控制**：可以通过在循环体内改变条件中变量的值（如计数器自增）来控制循环次数。
*   **退出循环**：在脚本中使用 `exit` 命令可以终止整个脚本，包括正在运行的循环。
*   **应用场景**：`while`循环特别适用于需要持续监控、等待特定条件或编写交互式程序的场景。

![](img/f4da95fb20b47138e86edb6f3bf76117_110.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_112.png)

![](img/f4da95fb20b47138e86edb6f3bf76117_114.png)

通过将`while`循环应用于猜数字游戏的改造，我们看到了它如何让脚本变得更加灵活和实用。掌握`while`循环后，你的Shell脚本编程能力将更进一步。