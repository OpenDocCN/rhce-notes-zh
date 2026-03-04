# Linux运维培训教程：1：Shell脚本概念介绍与编写规范 🐚

![](img/fd630dd05c25c5e0ae992afbfd0b3868_1.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_3.png)

在本节课中，我们将要学习Shell脚本的基础概念，了解什么是Shell脚本，它能做什么，以及如何编写一个规范的Shell脚本。这对于后续学习自动化运维至关重要。

![](img/fd630dd05c25c5e0ae992afbfd0b3868_5.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_7.png)

## 什么是Shell？它能做什么？

![](img/fd630dd05c25c5e0ae992afbfd0b3868_9.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_11.png)

上一节我们提到了Linux命令，本节中我们来看看这些命令背后的语言——Shell。

![](img/fd630dd05c25c5e0ae992afbfd0b3868_13.png)

Shell本身是一门编程语言。我们想与计算机交流，必须使用计算机能理解的语言，即二进制。然而，我们日常使用的`ls`、`pwd`等命令并非二进制，这是因为Shell作为一门**解释型语言**，充当了“翻译官”的角色。

![](img/fd630dd05c25c5e0ae992afbfd0b3868_15.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_17.png)

*   **解释型语言**：如Shell、Python、PHP。这类语言的程序在运行时，由**解释器**逐行“翻译”成机器码执行。其特点是无需提前编译。
    *   公式：`用户命令` -> `Shell解释器` -> `二进制指令` -> `计算机执行`
*   **编译型语言**：如C、C++、Go。这类语言的程序在运行前，需要专用的**编译器**将其整体转换为二进制文件。

![](img/fd630dd05c25c5e0ae992afbfd0b3868_19.png)

我们敲的所有Linux命令都属于Shell语言。Shell功能强大，支持变量、数组、函数、条件判断（`if`）、循环（`for`）等大部分编程语言具备的功能。因此，我们可以用Shell脚本实现：
*   服务器的批量维护与初始化。
*   软件的自动化部署与安装。
*   系统监控与故障报警。

![](img/fd630dd05c25c5e0ae992afbfd0b3868_21.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_23.png)

Shell属于**高级语言**，离用户更近，使用方便（很多功能已内置），而C语言等更接近硬件，属于底层语言。

## 为什么学习Shell脚本？

![](img/fd630dd05c25c5e0ae992afbfd0b3868_25.png)

脚本的本质是**命令的堆积**。它将一系列需要手动执行的命令，按照顺序写入一个文件。当执行这个文件时，系统会自动按序执行其中的所有命令。

以下是Shell脚本带来的核心优势：
*   **提升效率**：将重复性工作（如部署LNMP环境）编写成脚本，一键执行，省时省力。
*   **减少错误**：避免手动输入可能带来的敲错命令、遗漏步骤等问题。
*   **实现自动化**：结合计划任务（如`cron`），可以让脚本在特定时间（如凌晨3点）自动运行，完成备份、同步等任务，实现无人值守运维。

## 如何编写一个规范的Shell脚本？

下面我们来看一个规范Shell脚本的组成部分。我们将创建一个简单的脚本，实现在屏幕上输出“Hello World”。

![](img/fd630dd05c25c5e0ae992afbfd0b3868_27.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_28.png)

首先，创建一个用于存放脚本的目录并进入：
```bash
mkdir -p /script
cd /script
```

### 脚本的组成部分

![](img/fd630dd05c25c5e0ae992afbfd0b3868_30.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_32.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_34.png)

1.  **脚本环境声明**
    指定使用哪个解释器来执行此脚本中的命令。这必须是脚本的**第一行**。
    ```bash
    #!/bin/bash
    ```
    *   `#!` 是一个特殊标记，称为“shebang”或“hashbang”。
    *   `/bin/bash` 指明了使用Bash解释器。这是Red Hat/CentOS系统的默认Shell。

![](img/fd630dd05c25c5e0ae992afbfd0b3868_36.png)

2.  **注释信息**
    以 `#` 开头的行是注释，用于解释脚本的功能、作者、日期等，不会被执行。良好的注释是优秀脚本的习惯。
    ```bash
    # Author: YourName
    # Description: This script prints Hello World.
    ```

3.  **可执行命令**
    从第三行开始，编写需要依次执行的命令。
    ```bash
    echo "Hello World"
    ```

![](img/fd630dd05c25c5e0ae992afbfd0b3868_38.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_39.png)

### 完整的脚本示例

![](img/fd630dd05c25c5e0ae992afbfd0b3868_40.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_42.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_44.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_46.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_48.png)

现在，我们将以上部分组合起来，创建 `hello.sh` 脚本：
```bash
vim hello.sh
```
在编辑器中输入以下内容：
```bash
#!/bin/bash
# Author: YourName
# Description: Print Hello World to the screen.

![](img/fd630dd05c25c5e0ae992afbfd0b3868_50.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_52.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_54.png)

echo "Hello World"
```
保存并退出（按 `Esc`，然后输入 `:wq`）。

![](img/fd630dd05c25c5e0ae992afbfd0b3868_56.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_58.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_59.png)

### 执行Shell脚本

![](img/fd630dd05c25c5e0ae992afbfd0b3868_61.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_63.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_64.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_66.png)

创建好的脚本只是一个文本文件，需要赋予其**执行权限**后才能运行。

![](img/fd630dd05c25c5e0ae992afbfd0b3868_68.png)

1.  添加执行权限（这里为所有用户添加）：
    ```bash
    chmod +x hello.sh
    ```
    执行后，`hello.sh` 的文件名通常会变为绿色，表示它具有可执行权限。

2.  执行脚本：
    *   如果当前就在脚本所在目录，使用 `./` 来执行：
        ```bash
        ./hello.sh
        ```
    *   如果不在脚本目录，需要使用绝对路径：
        ```bash
        /script/hello.sh
        ```

![](img/fd630dd05c25c5e0ae992afbfd0b3868_70.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_72.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_74.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_75.png)

执行后，终端将输出：
```
Hello World
```

![](img/fd630dd05c25c5e0ae992afbfd0b3868_77.png)

---

![](img/fd630dd05c25c5e0ae992afbfd0b3868_79.png)

![](img/fd630dd05c25c5e0ae992afbfd0b3868_81.png)

本节课中我们一起学习了Shell脚本的核心概念、学习价值以及规范的编写方法。我们了解到Shell是一门强大的解释型编程语言，是运维自动化的基石。记住脚本的三个基本部分：环境声明、注释和命令。从下一个简单的“Hello World”脚本开始，你已经迈出了自动化运维的第一步。在接下来的课程中，我们将深入学习Shell脚本的变量、逻辑判断等更强大的功能。