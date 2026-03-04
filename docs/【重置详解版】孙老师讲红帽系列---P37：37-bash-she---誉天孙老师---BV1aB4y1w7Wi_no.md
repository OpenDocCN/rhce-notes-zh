# Linux基础教程：P37：bash shell中本地变量和环境变量的定义和使用 📚

![](img/994603ac94debed2d3409dbf2d423dca_0.png)

![](img/994603ac94debed2d3409dbf2d423dca_2.png)

![](img/994603ac94debed2d3409dbf2d423dca_4.png)

![](img/994603ac94debed2d3409dbf2d423dca_6.png)

在本节课中，我们将要学习Linux bash shell中变量的核心概念，包括本地变量和环境变量的定义、使用、查看以及它们之间的区别。变量是编程和脚本编写的基础，理解它们对于掌握Linux系统管理至关重要。

![](img/994603ac94debed2d3409dbf2d423dca_8.png)

## 变量基础概念 🔍

变量是一个可以存储数据的容器，其存储的值可以发生变化。从本质上讲，变量就是系统内存中用于保存数据的一块空间，而变量名则是访问这块空间的标识符。

在bash shell中，定义一个变量非常简单，其基本语法是：`变量名=变量值`。

## 变量的定义与命名规则 ✏️

![](img/994603ac94debed2d3409dbf2d423dca_10.png)

![](img/994603ac94debed2d3409dbf2d423dca_11.png)

以下是定义变量时需要遵循的规则和注意事项。

![](img/994603ac94debed2d3409dbf2d423dca_13.png)

*   **变量名规则**：变量名必须以字母或下划线开头，不能以数字开头。例如，`A=100` 或 `_var=200` 是有效的，而 `2A=200` 是无效的。
*   **大小写敏感**：变量名区分大小写。`VAR`、`Var` 和 `var` 是三个不同的变量。
*   **命名习惯**：在shell脚本中，习惯上使用大写字母来命名变量，以增加可读性，例如 `FILENAME`。
*   **赋值操作**：使用等号 `=` 进行赋值，等号两边不能有空格。例如：`NAME="Linux"`。

## 变量的引用与查看 🔎

定义变量后，我们需要知道如何获取和使用它的值。

![](img/994603ac94debed2d3409dbf2d423dca_15.png)

要引用一个变量的值，需要在变量名前加上美元符号 `$`。例如，`echo $NAME` 会输出变量 `NAME` 的值。

![](img/994603ac94debed2d3409dbf2d423dca_17.png)

![](img/994603ac94debed2d3409dbf2d423dca_19.png)

![](img/994603ac94debed2d3409dbf2d423dca_21.png)

有两种方式可以引用变量：
1.  `$变量名`，例如 `$A`。
2.  `${变量名}`，例如 `${A}`。

第二种方式（使用花括号）可以明确界定变量名的边界，特别是在变量名与后续字符串连接时非常有用。例如，要输出 “100B”，如果使用 `echo $AB`，shell会寻找名为 `AB` 的变量。正确的写法是 `echo ${A}B`。

要查看所有已定义的变量（包括本地变量和环境变量），可以使用 `set` 命令。如果想查看某个特定变量的值，使用 `echo $变量名`。

![](img/994603ac94debed2d3409dbf2d423dca_23.png)

![](img/994603ac94debed2d3409dbf2d423dca_25.png)

## 本地变量 🏠

![](img/994603ac94debed2d3409dbf2d423dca_27.png)

![](img/994603ac94debed2d3409dbf2d423dca_29.png)

![](img/994603ac94debed2d3409dbf2d423dca_31.png)

上一节我们介绍了变量的基本操作，本节中我们来看看变量的作用范围。首先介绍的是本地变量。

本地变量仅在定义它的当前shell会话中有效。一旦退出该shell或开启新的shell，这些变量就会消失。

![](img/994603ac94debed2d3409dbf2d423dca_33.png)

![](img/994603ac94debed2d3409dbf2d423dca_35.png)

**定义本地变量**：
```bash
A=100
```
**查看本地变量**：
```bash
echo $A
set | grep A=
```
**取消本地变量**：
```bash
unset A
```

![](img/994603ac94debed2d3409dbf2d423dca_37.png)

例如，在终端中定义 `A=100`，然后通过执行 `bash` 命令开启一个子shell，在子shell中 `echo $A` 将得不到任何输出，因为变量 `A` 是父shell的本地变量，不会传递给子shell。

## 环境变量 🌍

与本地变量不同，环境变量的作用范围更广。环境变量在当前shell及其所有子shell中都有效。

![](img/994603ac94debed2d3409dbf2d423dca_39.png)

![](img/994603ac94debed2d3409dbf2d423dca_41.png)

![](img/994603ac94debed2d3409dbf2d423dca_43.png)

**定义环境变量**（两种方法）：
1.  先定义为本地变量，再导出：
    ```bash
    A=100
    export A
    ```
2.  直接定义并导出：
    ```bash
    export A=100
    ```

![](img/994603ac94debed2d3409dbf2d423dca_45.png)

**查看所有环境变量**：
```bash
env
```
或
```bash
export
```

**取消环境变量**：
同样使用 `unset` 命令：
```bash
unset A
```

![](img/994603ac94debed2d3409dbf2d423dca_47.png)

环境变量的典型用途是配置系统环境，例如 `PATH` 变量定义了系统查找命令的路径，`USER` 变量保存了当前用户名。这些变量由父shell（如登录shell）设置，然后被所有子进程（包括子shell）继承。

![](img/994603ac94debed2d3409dbf2d423dca_49.png)

## 本地变量与环境变量的核心区别 ⚖️

以下是本地变量与环境变量的主要区别总结。

![](img/994603ac94debed2d3409dbf2d423dca_51.png)

![](img/994603ac94debed2d3409dbf2d423dca_53.png)

![](img/994603ac94debed2d3409dbf2d423dca_55.png)

*   **作用范围**：
    *   本地变量：仅在定义它的当前shell中有效。
    *   环境变量：在定义它的当前shell及其所有子shell中有效。
*   **查看命令**：
    *   本地变量：使用 `set` 命令查看。
    *   环境变量：使用 `env` 或 `export` 命令查看。
*   **定义方式**：
    *   本地变量：直接使用 `变量名=值`。
    *   环境变量：需要使用 `export` 命令导出。

![](img/994603ac94debed2d3409dbf2d423dca_57.png)

![](img/994603ac94debed2d3409dbf2d423dca_58.png)

![](img/994603ac94debed2d3409dbf2d423dca_60.png)

## 总结 📝

![](img/994603ac94debed2d3409dbf2d423dca_61.png)

![](img/994603ac94debed2d3409dbf2d423dca_63.png)

本节课中我们一起学习了bash shell中变量的核心知识。我们首先了解了变量的基本概念和定义规则，然后深入探讨了两种主要类型的变量：**本地变量**和**环境变量**。本地变量像私人房间里的物品，仅供自己使用；而环境变量像家里的公共设施，所有家庭成员（子shell）都可以使用。理解它们的作用范围和使用方法，是编写shell脚本和进行系统配置的重要基础。记住 `set`、`env`、`export` 和 `unset` 这些关键命令，它们是你管理shell变量的得力工具。