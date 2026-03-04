# Linux Shell脚本编程：14：Shell脚本基础与交互

![](img/e31072afa98cfa3def1f643f14c77aa8_1.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_2.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_4.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_6.png)

在本节课中，我们将要学习Shell脚本编程的基础知识，包括什么是Shell脚本、如何与脚本进行交互以及脚本的几种执行方式。Shell脚本是自动化系统管理任务的核心工具，掌握它对于理解和管理Linux系统至关重要。

![](img/e31072afa98cfa3def1f643f14c77aa8_8.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_9.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_10.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_11.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_13.png)

## 什么是Shell脚本编程

![](img/e31072afa98cfa3def1f643f14c77aa8_15.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_16.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_17.png)

上一节我们介绍了Shell的基本使用，本节中我们来看看如何将命令组合成脚本。Shell脚本，也称为Shell Script，其核心思想源于Unix系统“程序越小越好”的设计原则。在Linux中，每个命令功能单一，但通过Shell脚本可以将这些简单的命令组合起来，自动执行复杂的任务。

**脚本**的本质是一个文本文件，其中包含了一系列要执行的命令。这类似于电影剧本，不同的“演员”（如Bash Shell、Perl、Python）执行不同语法的“剧本”。由Shell（如Bash）来执行的文本文件，就称为Shell脚本。

![](img/e31072afa98cfa3def1f643f14c77aa8_19.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_20.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_21.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_22.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_23.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_25.png)

许多系统命令本身就是脚本，例如启动图形界面的 `startx` 命令。你可以通过以下命令查看并编辑它：
```bash
vi `which startx`
```
整个Linux系统的启动和运行过程也大量依赖于Shell脚本，例如系统欢迎信息就定义在 `/etc/rc.d/rc.sysinit` 等脚本文件中。

一个标准的Shell脚本第一行通常以 `#!`（称为shebang）开头，用于指定执行此脚本的解释器。例如：
```bash
#!/bin/bash
```
这行告诉系统，这个脚本需要由 `/bin/bash` 来解释执行。虽然在某些环境下省略此行脚本仍可运行，但为了兼容性和明确性，建议总是加上。

![](img/e31072afa98cfa3def1f643f14c77aa8_27.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_28.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_29.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_31.png)

## Shell脚本中的输入与输出

![](img/e31072afa98cfa3def1f643f14c77aa8_33.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_34.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_35.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_36.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_37.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_39.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_41.png)

了解了脚本的基本概念后，本节我们来看看脚本如何接收外部输入，以及如何产生输出。

![](img/e31072afa98cfa3def1f643f14c77aa8_43.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_45.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_47.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_48.png)

在Shell脚本中，可以使用特殊的变量来获取执行脚本时传递的参数：
*   **`$1`**, **`$2`**, **`$3`**... 分别代表传递给脚本的第一个、第二个、第三个参数。
*   **`$#`** 代表传递给脚本的参数总数。
*   **`$?`** 代表上一个命令的退出状态（0通常表示成功，非0表示失败）。
*   **`$$`** 代表当前脚本运行的进程ID（PID）。

![](img/e31072afa98cfa3def1f643f14c77aa8_49.png)

以下是一个演示这些变量的简单脚本示例：
```bash
#!/bin/bash
echo "第一个参数是: $1"
echo "第二个参数是: $2"
echo "参数总数是: $#"
echo "上一个命令的退出状态是: $?"
echo "本脚本的进程号是: $$"
```
给脚本加上执行权限并运行：
```bash
chmod a+x script.sh
./script.sh arg1 arg2 arg3
```

![](img/e31072afa98cfa3def1f643f14c77aa8_51.png)

脚本也可以主动与用户交互，读取输入。`read` 命令用于从标准输入读取值并赋值给变量。

![](img/e31072afa98cfa3def1f643f14c77aa8_53.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_54.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_55.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_56.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_58.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_60.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_61.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_62.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_63.png)

以下是使用 `read` 和 `echo` 进行交互的示例：
```bash
#!/bin/bash
read -p "请输入你的名字: " name
echo "你好, $name!"
```
`echo` 命令用于输出信息。对于更复杂的格式化输出，可以使用 `printf` 命令，它提供更强大的控制能力，例如制表符 `\t` 和换行符 `\n`。
```bash
printf "姓名:\t%s\n年龄:\t%d\n" "张三" 25
```

![](img/e31072afa98cfa3def1f643f14c77aa8_64.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_66.png)

## Shell脚本的执行方式

掌握了输入输出后，我们需要知道如何让脚本运行起来。Shell脚本主要有三种执行方式，它们之间有着重要的区别。

![](img/e31072afa98cfa3def1f643f14c77aa8_68.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_69.png)

以下是三种执行方式的对比与说明：

![](img/e31072afa98cfa3def1f643f14c77aa8_71.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_72.png)

1.  **使用绝对路径或相对路径执行（需要执行权限）**
    这种方式要求脚本文件具有可执行（x）权限。它会在一个新的子Shell进程中执行脚本。
    ```bash
    chmod a+x script.sh
    ./script.sh          # 相对路径
    /tmp/script.sh       # 绝对路径
    ```

![](img/e31072afa98cfa3def1f643f14c77aa8_74.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_75.png)

2.  **使用指定的Shell解释器执行（无需执行权限）**
    这种方式直接调用Shell解释器（如bash），并将脚本文件作为参数传入。它同样会在一个新的子Shell进程中执行脚本，且不要求脚本文件本身有x权限。
    ```bash
    bash script.sh
    sh script.sh
    ```

![](img/e31072afa98cfa3def1f643f14c77aa8_77.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_79.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_81.png)

3.  **使用 source 或 `.` 命令执行（在当前Shell执行）**
    这种方式使用 `source` 命令或其简写形式 `.`（点号）来执行脚本。**关键区别在于，它是在当前Shell环境中执行脚本，而不是新建子Shell。** 这意味着脚本中设置的变量、别名等在脚本执行结束后，在当前Shell中依然有效。
    ```bash
    source script.sh
    . ./script.sh        # 点号后有一个空格，然后是脚本路径
    ```
    这种执行方式常用于加载环境变量配置。例如，系统初始化脚本 `/etc/profile` 中加载其他配置脚本时，就使用了 `. /etc/profile.d/*.sh` 的方式。

![](img/e31072afa98cfa3def1f643f14c77aa8_83.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_85.png)

**重要总结：**
*   方式1和方式2是**新建子Shell执行**，脚本内部的环境变量更改不会影响父Shell。
*   方式3是**在当前Shell执行**，脚本内部的环境变量更改会持续生效。
*   方式2和方式3不要求脚本文件具有可执行权限。

![](img/e31072afa98cfa3def1f643f14c77aa8_87.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_88.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_89.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_91.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_93.png)

## 流程控制入门

在本节中，我们将初步接触Shell脚本的流程控制。流程控制是编程的核心，它让脚本能够根据条件做出判断，或者重复执行某些操作。

![](img/e31072afa98cfa3def1f643f14c77aa8_95.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_97.png)

主要的流程控制结构包括：
*   **条件判断（if）**：如果某个条件成立，则执行一段代码；否则，执行另一段代码。
*   **循环（for, while, until）**：重复执行一段代码，直到满足某个条件。

这些结构使得脚本变得智能和高效。在接下来的课程中，我们将详细学习每种结构的语法和应用场景。

![](img/e31072afa98cfa3def1f643f14c77aa8_99.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_100.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_102.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_104.png)

## 总结

![](img/e31072afa98cfa3def1f643f14c77aa8_106.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_107.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_108.png)

![](img/e31072afa98cfa3def1f643f14c77aa8_110.png)

本节课中我们一起学习了Shell脚本编程的基础。我们首先了解了Shell脚本的概念及其在系统管理中的重要性。接着，我们学习了如何在脚本中通过特殊变量（如`$1`, `$#`）接收参数，以及使用`read`和`echo`/`printf`命令进行交互式输入输出。然后，我们深入探讨了Shell脚本的三种执行方式及其关键区别，特别是`source`（`.`）命令在当前Shell执行的特点。最后，我们引出了流程控制的概念，为后续深入学习脚本逻辑打下基础。掌握这些基础知识是编写自动化脚本、高效管理系统的重要第一步。