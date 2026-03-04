# Shell脚本自动化编程实战：P2：2.4 变量的赋值方式 📝

![](img/18da9bc41e435c9db429d11e9693ac6f_0.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_1.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_2.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_4.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_6.png)

在本节课中，我们将要学习Shell脚本中变量的多种赋值方式。理解如何为变量赋值是编写脚本的基础，我们将从最简单的显示赋值开始，逐步介绍从键盘读取输入、命令替换等高级技巧，并深入探讨引号在变量赋值中的关键作用。

![](img/18da9bc41e435c9db429d11e9693ac6f_8.png)

## 变量的显示赋值

![](img/18da9bc41e435c9db429d11e9693ac6f_10.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_12.png)

上一节我们介绍了变量的基本概念和类型，本节中我们来看看最直接、最常见的赋值方式——显示赋值。

![](img/18da9bc41e435c9db429d11e9693ac6f_14.png)

显示赋值是指直接将一个值（字符串）赋予变量名。在Shell中，变量没有预定义的数据类型，所有值都被视为字符串。

![](img/18da9bc41e435c9db429d11e9693ac6f_16.png)

以下是显示赋值的几种常见形式：

![](img/18da9bc41e435c9db429d11e9693ac6f_18.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_20.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_22.png)

*   **直接赋值**：`变量名=值`
*   **赋值带空格的字符串**：需要使用引号包裹，例如 `name=“Zhang San”`
*   **将一个变量的值赋给另一个变量**：`var2=$var1`

![](img/18da9bc41e435c9db429d11e9693ac6f_24.png)

**代码示例**：
```bash
name=Tom
path="/home/user/docs"
today=$name
```

![](img/18da9bc41e435c9db429d11e9693ac6f_26.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_28.png)

## 使用read命令从键盘赋值

![](img/18da9bc41e435c9db429d11e9693ac6f_30.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_31.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_33.png)

除了直接赋值，我们还可以在脚本运行时，动态地从用户键盘输入获取值并赋给变量，这需要使用 `read` 命令。

![](img/18da9bc41e435c9db429d11e9693ac6f_35.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_37.png)

`read` 命令的基本用法是等待用户输入，并将输入的内容赋值给指定的变量。

以下是 `read` 命令的一些常用选项：

*   **基本读取**：`read 变量名`
*   **带提示信息**：`read -p “提示信息: ” 变量名`
*   **限制输入字符数**：`read -n 字符数 变量名`
*   **设置等待超时**：`read -t 秒数 变量名`
*   **一次读取多个值**：`read 变量1 变量2 变量3` (输入时用空格分隔)

**代码示例**：
```bash
read -p “Please enter your IP: ” ip_addr
read -n 1 -p “Press any key to continue... ” any_key
read -p “Enter name, gender and age: ” name gender age
```

![](img/18da9bc41e435c9db429d11e9693ac6f_39.png)

## 通过命令替换进行赋值

![](img/18da9bc41e435c9db429d11e9693ac6f_41.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_42.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_44.png)

有时，我们需要将一个命令的执行结果作为值赋给变量。这时就需要用到命令替换。

![](img/18da9bc41e435c9db429d11e9693ac6f_46.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_47.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_49.png)

命令替换是指Shell先执行命令，然后将命令的输出结果替换到命令所在的位置。有两种等价的语法可以实现命令替换。

![](img/18da9bc41e435c9db429d11e9693ac6f_51.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_53.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_55.png)

以下是命令替换的两种形式：

![](img/18da9bc41e435c9db429d11e9693ac6f_57.png)

*   **反引号**：`` `命令` ``
*   **`$()`**：`$(命令)` (更推荐使用，易于嵌套和阅读)

**代码示例**：
```bash
current_date=`date +%F`
current_date=$(date +%F)
disk_usage=$(df -h / | awk ‘NR==2 {print $5}’)
```

![](img/18da9bc41e435c9db429d11e9693ac6f_59.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_61.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_63.png)

## 引号在变量赋值中的作用

![](img/18da9bc41e435c9db429d11e9693ac6f_65.png)

在变量赋值和使用时，引号的选择至关重要，它决定了Shell如何解释字符串中的特殊字符（如变量名`$var`、命令替换符`` ` ``等）。

![](img/18da9bc41e435c9db429d11e9693ac6f_67.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_69.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_70.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_71.png)

以下是三种引号的主要区别：

![](img/18da9bc41e435c9db429d11e9693ac6f_73.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_75.png)

*   **双引号 `“ ”` (弱引用)**：允许解析其中的变量 (`$var`)、命令替换 (`$(cmd)`) 和转义字符 (`\`)。这是最常用的引号。
*   **单引号 `‘ ’` (强引用)**：禁止解析其中的任何特殊字符，所有内容都按字面意义处理。
*   **反引号 `` ` ``**：主要用于命令替换，已逐渐被 `$()` 替代。

![](img/18da9bc41e435c9db429d11e9693ac6f_77.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_79.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_81.png)

**公式/规则**：
*   需要变量和命令替换生效 → 使用 **双引号** 或 **不加引号** (简单字符串)。
*   需要完全保留字面值 → 使用 **单引号**。
*   需要获取命令输出 → 使用 **`$()`** 或 **反引号**。

![](img/18da9bc41e435c9db429d11e9693ac6f_83.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_85.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_87.png)

**代码示例**：
```bash
user=“Alice”
echo “Hello, $user”    # 输出：Hello, Alice
echo ‘Hello, $user’    # 输出：Hello, $user
echo “Today is $(date)” # 输出：Today is 2023-10-27
```

![](img/18da9bc41e435c9db429d11e9693ac6f_88.png)

![](img/18da9bc41e435c9db429d11e9693ac6f_90.png)

## 总结

![](img/18da9bc41e435c9db429d11e9693ac6f_92.png)

本节课中我们一起学习了Shell变量的核心赋值方式。我们掌握了**显示赋值**的直接性，了解了通过 **`read`命令**与用户交互获取输入，并学会了使用 **命令替换** (`$()`) 将命令输出转化为变量值。最后，我们深入理解了**引号**的差异：双引号允许扩展，单引号保持原样，而反引号或`$()`用于命令替换。牢记这些赋值方法和引号规则，是编写灵活、强大Shell脚本的坚实基础。