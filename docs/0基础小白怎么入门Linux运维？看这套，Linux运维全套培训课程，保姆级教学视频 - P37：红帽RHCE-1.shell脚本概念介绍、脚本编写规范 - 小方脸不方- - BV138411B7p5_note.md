# Linux运维全套培训课程：P37：红帽RHCE-1.shell脚本概念介绍、脚本编写规范 🐧

![](img/c54fd50ebd0dda197fde69b88050e4f7_1.png)

在本节课中，我们将要学习Shell脚本的基础概念和编写规范。Shell脚本是Linux运维工作中实现自动化、提升效率的核心工具。我们将从什么是Shell开始，逐步了解脚本的作用、结构以及如何编写一个简单的脚本。

![](img/c54fd50ebd0dda197fde69b88050e4f7_3.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_5.png)

---

![](img/c54fd50ebd0dda197fde69b88050e4f7_7.png)

## 什么是Shell？它能做什么？

![](img/c54fd50ebd0dda197fde69b88050e4f7_9.png)

上一节我们结束了RHCSA阶段的内容，本周正式进入RHCE阶段的学习。RHCE阶段将从Shell脚本开始讲起，一直延伸到后边的Ansible自动化运维工具。

![](img/c54fd50ebd0dda197fde69b88050e4f7_11.png)

Shell本身是一门编程语言。编程语言是人与计算机交流的媒介。计算机只能理解二进制（0和1），而我们通过Shell语言（例如`ls`、`cat`、`pwd`等命令）与计算机交互，是因为Shell充当了“翻译官”的角色。

![](img/c54fd50ebd0dda197fde69b88050e4f7_13.png)

编程语言主要分为两类：
*   **编译型语言**：如C、C++、Go。程序运行前需要专门的编译器将其整体转换为计算机能直接执行的二进制文件。
    *   **公式/代码示例**：`gcc hello.c -o hello` （将C源码编译成可执行文件）
*   **解释型语言**：如Shell、Python、PHP。程序运行时，由解释器一边读取代码一边逐行翻译并执行。
    *   **公式/代码示例**：`python hello.py` 或 `bash hello.sh` （调用解释器执行脚本）

我们日常在终端输入的命令，都属于Shell语言的范畴。因此，你已经在不知不觉中学习并使用了一门计算机语言。

![](img/c54fd50ebd0dda197fde69b88050e4f7_15.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_17.png)

---

![](img/c54fd50ebd0dda197fde69b88050e4f7_19.png)

## 为什么要学习Shell脚本？

Shell脚本功能强大，支持变量、数组、判断（if）、循环（for）、函数、运算等大多数编程语言都具备的功能。学习Shell脚本可以让我们：

![](img/c54fd50ebd0dda197fde69b88050e4f7_21.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_23.png)

*   对服务器进行**批量维护**。
*   实现**自动化部署**和软件安装。
*   完成服务器的**自动化初始化**配置。
*   编写**系统监控**和**故障报警**脚本。

![](img/c54fd50ebd0dda197fde69b88050e4f7_25.png)

简单来说，**Shell脚本的本质是命令的堆积**。它将一系列需要手动输入的命令，按照逻辑顺序写入一个文件。当执行这个文件时，系统就会自动按序执行其中的所有命令。

**核心优势**：
*   **提升效率**：将重复性工作（如部署LNMP环境、半夜备份数据）写成脚本，一次编写，多次使用，解放双手。
*   **减少错误**：避免手动输入命令可能带来的拼写错误。
*   **实现自动化**：结合计划任务（如cron），可以让脚本在指定时间（如凌晨3点）自动运行，无需人工值守。

---

## 如何编写一个规范的Shell脚本？

![](img/c54fd50ebd0dda197fde69b88050e4f7_27.png)

理解了脚本的重要性后，我们来看看如何编写一个规范的Shell脚本。一个规范的脚本通常包含以下几个部分。

![](img/c54fd50ebd0dda197fde69b88050e4f7_29.png)

### 1. 脚本的创建与命名

首先，我们创建一个专门的目录来存放练习脚本。
```bash
mkdir /script
cd /script
```
创建脚本文件，通常以 `.sh` 作为后缀，这只是一个约定，便于识别文件类型。
```bash
vim hello.sh
```

![](img/c54fd50ebd0dda197fde69b88050e4f7_31.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_33.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_35.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_36.png)

### 2. 脚本的基本结构

![](img/c54fd50ebd0dda197fde69b88050e4f7_38.png)

一个规范的Shell脚本包含以下部分：

**（1）环境声明（Shebang）**
脚本的第一行，用于声明执行本脚本所使用的解释器。
```bash
#!/bin/bash
```
*   `#!` 是一个特殊标记，告诉系统该文件需要指定解释器执行。
*   `/bin/bash` 指定了Bash解释器的路径。这是Linux系统最常用的Shell解释器。

**（2）注释信息**
以 `#` 开头的行为注释，用于解释脚本的功能、作者、更新时间等，方便他人或自己日后阅读。注释不会被执行。
```bash
# Author: Xiaofanglian
# Description: This script prints “Hello World” to the screen.
```

![](img/c54fd50ebd0dda197fde69b88050e4f7_40.png)

**（3）可执行命令**
这是脚本的主体，由一系列Linux命令和Shell语法构成。
```bash
echo “Hello World”
```

![](img/c54fd50ebd0dda197fde69b88050e4f7_42.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_43.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_45.png)

### 3. 一个完整的脚本示例

![](img/c54fd50ebd0dda197fde69b88050e4f7_47.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_49.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_51.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_53.png)

以下是 `hello.sh` 文件的完整内容：
```bash
#!/bin/bash
# Author: Xiaofanglian
# Description: Print “Hello World”
echo “Hello World”
```

![](img/c54fd50ebd0dda197fde69b88050e4f7_55.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_57.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_59.png)

### 4. 执行脚本

脚本文件创建后，默认没有执行权限。需要先添加执行权限，然后才能运行。

![](img/c54fd50ebd0dda197fde69b88050e4f7_61.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_63.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_64.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_66.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_68.png)

以下是执行脚本的步骤：
1.  添加执行权限：
    ```bash
    chmod +x hello.sh
    ```
    （执行后，文件列表中的 `hello.sh` 会变成绿色，表示它具有可执行权限）。
2.  执行脚本：
    *   如果当前就在脚本所在目录，使用 `./` 来执行：
        ```bash
        ./hello.sh
        ```
    *   也可以使用Bash解释器直接指定脚本文件：
        ```bash
        bash hello.sh
        ```

![](img/c54fd50ebd0dda197fde69b88050e4f7_70.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_72.png)

执行后，终端将会输出：`Hello World`。

---

![](img/c54fd50ebd0dda197fde69b88050e4f7_74.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_76.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_78.png)

## 脚本编写规范与好习惯

![](img/c54fd50ebd0dda197fde69b88050e4f7_80.png)

*   **见名知意**：脚本文件名应能体现其功能，如 `deploy_lnmp.sh`、`backup_data.sh`。
*   **添加注释**：在关键步骤或复杂逻辑前添加注释，说明代码的意图。这在团队协作和后期维护时至关重要。
*   **输出提示信息**：在脚本中适当使用 `echo` 命令输出当前执行步骤，让执行过程更清晰。
*   **统一存放**：将所有的脚本文件放在一个统一的目录（如 `/script` 或 `/usr/local/bin`）下，便于管理。

---

![](img/c54fd50ebd0dda197fde69b88050e4f7_82.png)

![](img/c54fd50ebd0dda197fde69b88050e4f7_84.png)

本节课中我们一起学习了Shell脚本的核心概念和编写入门。我们了解到Shell是一门强大的解释型语言，是运维自动化的基石。脚本的本质是将命令写入文件并自动执行。一个规范的脚本需要包含环境声明、注释和可执行命令。从下一个简单的“Hello World”脚本开始，你已经迈出了自动化运维的第一步。在接下来的课程中，我们将深入学习Shell脚本的变量、判断、循环等高级功能。