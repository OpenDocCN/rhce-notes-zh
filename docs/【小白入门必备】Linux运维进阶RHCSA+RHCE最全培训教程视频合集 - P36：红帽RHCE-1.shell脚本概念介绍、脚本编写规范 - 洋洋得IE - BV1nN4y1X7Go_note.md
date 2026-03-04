# Linux运维进阶：P36：Shell脚本概念与编写规范 🐚

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_1.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_3.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_5.png)

在本节课中，我们将要学习Shell脚本的基础概念、编写规范以及如何创建并运行一个简单的脚本。Shell脚本是自动化运维工作的核心工具，掌握它能让你的工作效率大幅提升。

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_7.png)

## 什么是Shell？它能做什么？

上一节我们介绍了课程的整体安排，本节中我们来看看Shell脚本的基础概念。

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_9.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_11.png)

Shell本身是一门编程语言。编程语言是人与计算机打交道的工具。我们想管理或控制计算机，就必须使用计算机能理解的语言。计算机的底层语言是二进制（如010101）。然而，我们日常使用的命令（如 `ls`、`cat`）并非二进制，这是因为Shell这类编程语言充当了“翻译官”的角色。

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_13.png)

编程语言主要分为两类：
*   **编译型语言**：如C、C++、Go。程序运行前需要专门的编译器将其整体转换为计算机能识别的二进制文件。
    *   **公式/代码示例**：`gcc source_code.c -o executable_program`
*   **解释型语言**：如Shell、Python、PHP。程序运行时，由解释器一边读取代码一边逐行翻译并执行。
    *   **公式/代码示例**：`python script.py` 或 `bash script.sh`

我们日常在终端输入的命令，都属于Shell语言。因此，你已经在不知不觉中学习了一门与计算机交流的语言。

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_15.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_17.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_19.png)

Shell语言功能强大，支持变量、数组、判断（if）、循环（for）、函数、数学运算等大多数编程语言都具备的功能。我们可以用Shell脚本实现：
*   服务器的批量维护与初始化
*   软件的自动化部署与安装
*   系统监控与故障报警
*   定期备份等自动化任务

Shell属于**高级语言**，它离用户更近，使用起来相对方便，很多功能（如`ls`, `cat`命令）已经由他人实现，我们可以直接组合使用，而无需从零开始编写。

## 为什么需要Shell脚本？

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_21.png)

脚本的本质是**命令的堆积**。它将一系列需要手动输入的命令，按照顺序写入一个文件。当执行这个文件时，系统会自动按序执行其中的所有命令。

设想一个场景：你需要为50台新服务器部署一套完整的LNMP（Linux, Nginx, MySQL, PHP）环境。手动在一台台服务器上重复输入安装和配置命令，不仅效率低下，而且容易出错。

以下是使用脚本的优势：
1.  **提升效率**：将部署命令写入一个脚本文件（例如 `deploy_lnmp.sh`）。对新服务器，只需执行一次该脚本即可自动完成所有工作。
2.  **减少错误**：避免了人工输入可能产生的拼写错误或顺序错误。
3.  **实现自动化**：结合计划任务（如cron），可以让脚本在特定时间（如凌晨3点）自动执行，完成备份、日志清理等任务，无需人工值守。
4.  **知识沉淀与共享**：写好的脚本可以存档、复用和分享，是宝贵的经验资产。

## 如何编写一个规范的Shell脚本？

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_23.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_25.png)

了解了脚本的威力后，本节我们来看看如何创建一个规范的Shell脚本。

一个规范的Shell脚本通常包含以下几个部分：

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_27.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_29.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_31.png)

### 1. 脚本的创建与命名
首先，我们创建一个专门存放脚本的目录，并进入该目录。
```bash
mkdir -p /script
cd /script
```
然后，使用文本编辑器（如vim）创建一个新文件。
```bash
vim hello.sh
```
**命名规范**：
*   文件名应见名知意，例如 `deploy_lnmp.sh`、`backup_data.sh`。
*   后缀通常使用 `.sh`，这有助于使用者一眼识别出这是一个Shell脚本文件（类似Python的 `.py`，Java的 `.java`）。此外，后缀名是给用户看的，不加 `.sh` 脚本也能正常运行。

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_33.png)

### 2. 脚本的组成部分
一个规范的脚本主要包含以下内容：

**（1）环境声明（Shebang）**
脚本的第一行，用于声明执行此脚本所用的解释器。
```bash
#!/bin/bash
```
*   `#!` 是一个特殊标记，告诉系统该文件需要指定的解释器来执行。
*   `/bin/bash` 是Bash解释器的常见路径，也是大多数Linux系统的默认Shell。
*   系统自带的解释器列表可以在 `/etc/shells` 文件中查看。

**（2）注释信息**
注释以 `#` 开头，其后的内容会被解释器忽略。注释用于说明脚本的作者、功能、版本等信息，是良好的编程习惯。
```bash
# Author: Your Name
# Email: your.email@example.com
# Description: This script prints “Hello World” to the screen.
# Version: 1.0
```

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_35.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_37.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_38.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_40.png)

**（3）可执行命令**
这是脚本的主体部分，由一系列要执行的Linux命令组成。
```bash
echo “Hello World”
```

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_42.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_44.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_46.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_48.png)

### 3. 脚本的执行
脚本编写完成后，它是一个普通文本文件，默认没有执行权限。需要先赋予其执行权限，然后才能运行。

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_50.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_52.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_54.png)

以下是执行脚本的步骤：
1.  赋予执行权限：
    ```bash
    chmod +x hello.sh  # 为所有用户添加执行权限
    # 或
    chmod u+x hello.sh # 仅给文件所有者添加执行权限
    ```
    执行后，文件列表中的文件名会变成绿色。

2.  执行脚本：
    *   **如果脚本在当前目录**：需要使用 `./` 来指明路径。
        ```bash
        ./hello.sh
        ```
    *   **如果脚本在任何其他目录**：需要使用绝对路径或相对路径。
        ```bash
        /script/hello.sh
        ```
    **注意**：不能直接输入 `hello.sh`，因为系统会将其当作一个命令去 `PATH` 环境变量指定的路径中查找，而不会执行当前目录下的文件。

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_56.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_58.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_59.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_61.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_63.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_65.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_67.png)

## 一个完整的脚本示例

让我们将以上所有规范整合，创建一个完整的 `hello.sh` 脚本文件：
```bash
#!/bin/bash

# Author: RHCE Student
# Function: Print a greeting message.
# Date: 2023-10-27

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_69.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_71.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_73.png)

echo “Hello World, this is my first shell script!”
echo “Current date is: $(date)”
```
保存文件后，按照上述步骤赋予权限并执行，你将在屏幕上看到输出信息。

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_75.png)

---

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_77.png)

![](img/3f6cbe51502b8aad1e3b3dbdcb3a52a6_79.png)

本节课中我们一起学习了Shell脚本的核心概念、它在自动化运维中的重要性，以及如何规范地创建和执行一个简单的Shell脚本。记住，脚本的本质是命令的集合，而规范的结构（环境声明、注释、命令）是写出可读、可维护脚本的基础。从下一个简单的 `echo` 命令开始，你已经踏入了Shell编程的大门。在接下来的课程中，我们将学习变量、条件判断、循环等更强大的功能，让你能够编写出真正解决实际问题的脚本。