# Linux运维教程：P37：Shell脚本概念与编写规范 🐚

![](img/20b46d87c8a24e4a961a8a62d7d36db7_1.png)

在本节课中，我们将要学习Shell脚本的基础概念和编写规范。Shell脚本是Linux系统运维中实现自动化、提升工作效率的核心技能。我们将从什么是Shell开始，逐步了解脚本的本质、作用以及如何编写一个规范的脚本。

![](img/20b46d87c8a24e4a961a8a62d7d36db7_3.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_5.png)

---

![](img/20b46d87c8a24e4a961a8a62d7d36db7_7.png)

## 什么是Shell？它能做什么？

上一节我们结束了RHCSA阶段的内容，本节中我们来看看RHCE阶段的起点——Shell脚本。

![](img/20b46d87c8a24e4a961a8a62d7d36db7_9.png)

Shell本身是一门编程语言。我们想管理计算机，就需要与计算机交流。计算机能理解的语言是二进制（如0101）。但我们日常使用的`ls`、`cat`等命令并非二进制，这是因为Shell作为一门**解释型语言**，充当了我们与计算机内核之间的“翻译官”。

![](img/20b46d87c8a24e4a961a8a62d7d36db7_11.png)

*   **编译型语言**（如C、C++、Go）：程序运行前需要编译器提前编译成二进制。
    *   **公式/代码示例**：`gcc -o program program.c` （将C源码编译为可执行程序）
*   **解释型语言**（如Shell、Python、PHP）：程序运行时由解释器一边翻译一边执行。
    *   **公式/代码示例**：`bash script.sh` （由bash解释器逐行执行脚本中的命令）

![](img/20b46d87c8a24e4a961a8a62d7d36db7_13.png)

我们日常在终端输入的所有命令，都属于Shell语言。因此，你已经在不知不觉中学习了一门与计算机交互的编程语言。

Shell脚本功能强大，支持变量、数组、判断、循环、函数、运算等编程特性。我们可以用它来实现：
*   服务器的批量维护与初始化
*   软件的自动化安装与部署
*   系统监控与故障报警
*   定时任务（如凌晨自动备份）

![](img/20b46d87c8a24e4a961a8a62d7d36db7_15.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_17.png)

Shell属于**高级语言**，离用户更近，使用起来比C/C++等底层语言更方便，因为它内置了大量现成的命令和工具供我们直接调用。

![](img/20b46d87c8a24e4a961a8a62d7d36db7_19.png)

---

## 为什么需要Shell脚本？

![](img/20b46d87c8a24e4a961a8a62d7d36db7_21.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_23.png)

脚本的本质是**命令的堆积**。

![](img/20b46d87c8a24e4a961a8a62d7d36db7_25.png)

想象一个场景：你需要为公司每一台新上线的服务器部署一套LNMP（Linux, Nginx, MySQL, PHP）环境。手动敲击几十条命令不仅效率低下，而且容易出错。

以下是解决此问题的思路对比：

*   **手动操作**：每台服务器重复输入相同的安装、配置命令，耗时耗力，且无法保证一致性。
*   **使用脚本**：将所有部署命令按顺序写入一个文件（例如 `deploy_lnmp.sh`）。之后，对新服务器只需执行一次这个脚本文件，所有命令便会自动顺序执行。

另一个常见需求是**凌晨备份服务器数据**。你不可能每天半夜手动操作。此时可以：
1.  将备份命令写入脚本（如 `backup.sh`）。
2.  结合计划任务（如cron），设定在每天凌晨3点自动执行该脚本。

这样，工作由脚本自动完成，你的幸福指数和工作效率都将大幅提升。

![](img/20b46d87c8a24e4a961a8a62d7d36db7_27.png)

---

![](img/20b46d87c8a24e4a961a8a62d7d36db7_29.png)

## 如何编写一个规范的Shell脚本？

现在，让我们动手编写第一个Shell脚本。我们将创建一个输出“Hello World”的脚本，并了解其规范组成。

![](img/20b46d87c8a24e4a961a8a62d7d36db7_31.png)

首先，创建一个用于存放练习脚本的目录：
```bash
mkdir /script
cd /script
```

![](img/20b46d87c8a24e4a961a8a62d7d36db7_33.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_35.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_37.png)

### 脚本的组成部分

一个规范的Shell脚本通常包含以下部分：

#### 1. 环境声明（Shebang）
脚本的第一行，用于声明执行此脚本的解释器。
```bash
#!/bin/bash
```
*   `#!` 是一个特殊标记，告诉系统该文件需要指定的解释器来执行。
*   `/bin/bash` 是我们最常用的Bash Shell解释器的路径。
*   系统中有多种解释器（如 `/bin/sh`, `/usr/bin/zsh`），在 `/etc/shells` 文件中可以查看。

#### 2. 注释信息
以 `#` 开头的行是注释，用于解释脚本的功能、作者、更新时间等，是良好的编程习惯。
```bash
# Author: Your Name
# Description: This script prints Hello World.
# Version: 1.0
```

![](img/20b46d87c8a24e4a961a8a62d7d36db7_39.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_41.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_42.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_44.png)

#### 3. 可执行命令
这是脚本的主体，由一系列Linux命令按逻辑顺序组成。
```bash
echo "Hello World"
```

![](img/20b46d87c8a24e4a961a8a62d7d36db7_46.png)

### 编写第一个脚本

![](img/20b46d87c8a24e4a961a8a62d7d36db7_48.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_50.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_52.png)

现在，我们将上述部分组合起来，创建 `hello.sh` 脚本：
```bash
vim hello.sh
```
在文件中输入以下内容：
```bash
#!/bin/bash
# My first shell script
# It will print Hello World to the screen.

![](img/20b46d87c8a24e4a961a8a62d7d36db7_54.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_56.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_58.png)

echo "Hello World"
```
保存并退出编辑器。

### 执行脚本

![](img/20b46d87c8a24e4a961a8a62d7d36db7_60.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_62.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_63.png)

刚创建的脚本只是一个文本文件，需要赋予其**执行权限**才能运行。
```bash
chmod +x hello.sh
```
授予执行权限后，脚本文件名会变为绿色（在某些终端配置下）。然后，可以通过以下方式执行：
```bash
./hello.sh
```
*   `./` 表示当前目录。直接输入 `hello.sh` 会被系统当作命令去查找，而非执行当前目录下的文件。
*   你也可以使用绝对路径执行，如 `/script/hello.sh`。

![](img/20b46d87c8a24e4a961a8a62d7d36db7_65.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_67.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_69.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_71.png)

执行后，终端将输出：
```
Hello World
```

---

### 脚本编写规范小结

![](img/20b46d87c8a24e4a961a8a62d7d36db7_73.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_75.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_77.png)

以下是编写Shell脚本时需要注意的规范要点：

![](img/20b46d87c8a24e4a961a8a62d7d36db7_79.png)

*   **见名知意**：脚本文件名应能体现其功能，如 `deploy_lnmp.sh`、`backup_mysql.sh`。
*   **后缀名**：虽然非强制，但使用 `.sh` 后缀有助于标识这是一个Shell脚本。
*   **规范结构**：建议包含Shebang行、注释区和命令主体。
*   **执行权限**：脚本文件必须拥有执行权限（`x`）才能直接运行。
*   **调试**：编写复杂脚本时，可使用 `bash -x script.sh` 来调试，查看每条命令的执行过程。

---

![](img/20b46d87c8a24e4a961a8a62d7d36db7_81.png)

![](img/20b46d87c8a24e4a961a8a62d7d36db7_83.png)

本节课中我们一起学习了Shell脚本的核心概念、重要作用以及基础的编写与执行规范。你理解了脚本是命令的自动化集合，是运维工程师提升效率、实现自动化的利器。从简单的“Hello World”开始，你已经迈出了Shell编程的第一步。在接下来的课程中，我们将深入学习变量、判断、循环等编程结构，让你能够编写出真正解决实际问题的脚本。