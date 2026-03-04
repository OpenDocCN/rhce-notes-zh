# Redhat红帽 RHCE8.0认证体系课程：P3：访问命令行 🖥️

![](img/9b5ed83fe47e32749142fb3e240ea7eb_1.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_3.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_5.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_7.png)

在本节课中，我们将学习如何访问Linux系统的命令行界面，这是进行系统管理和运维工作的基础。我们将了解Linux的基本框架、Shell的作用、命令行的构成以及如何通过不同方式登录系统。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_9.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_11.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_13.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_15.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_17.png)

---

![](img/9b5ed83fe47e32749142fb3e240ea7eb_19.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_20.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_21.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_22.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_23.png)

## 环境配置与连接

![](img/9b5ed83fe47e32749142fb3e240ea7eb_25.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_27.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_28.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_29.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_31.png)

上一节我们介绍了课程的整体安排，本节中我们来看看实验环境的配置和连接方法。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_33.png)

我的实验环境由两台虚拟机组成，以下是它们的网络配置信息：

*   **服务器A (servera.lab.example.com)**：IP地址为 `192.168.145.128`
*   **服务器B (serverb.lab.example.com)**：IP地址为 `192.168.145.129`

![](img/9b5ed83fe47e32749142fb3e240ea7eb_35.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_37.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_38.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_39.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_40.png)

两台虚拟机的用户名和密码如下：

![](img/9b5ed83fe47e32749142fb3e240ea7eb_42.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_43.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_44.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_45.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_47.png)

*   **root用户**：密码为 `redhat`
*   **student用户**：密码为 `student`

![](img/9b5ed83fe47e32749142fb3e240ea7eb_49.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_50.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_52.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_54.png)

为了能够从本地计算机连接到虚拟机，我们需要使用终端工具。在Windows系统上，可以使用Xshell、SecureCRT等工具；在macOS或Linux系统上，可以直接使用系统自带的终端。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_55.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_57.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_58.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_60.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_61.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_62.png)

以下是使用SSH协议连接服务器的命令格式：
```bash
ssh 用户名@服务器IP地址
```
例如，连接服务器A的root用户：
```bash
ssh root@192.168.145.128
```
首次连接时，系统会提示确认主机的真实性，输入 `yes` 并回车接受。然后输入对应用户的密码即可登录。登录成功后，会看到命令提示符，例如 `[root@servera ~]#`。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_64.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_66.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_68.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_69.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_71.png)

**操作建议**：在开始实验前，建议为虚拟机创建一个快照。这样，在练习后可以快速恢复到初始状态。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_73.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_74.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_75.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_77.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_79.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_80.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_81.png)

---

![](img/9b5ed83fe47e32749142fb3e240ea7eb_83.png)

## Linux系统简介

在深入命令行之前，我们先简要了解Linux系统本身，特别是Red Hat Enterprise Linux (RHEL) 和社区版CentOS的区别。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_85.png)

*   **Red Hat Enterprise Linux (RHEL)**：企业级Linux发行版，用于生产环境。它提供付费订阅服务，包括技术支持和长期维护。
*   **CentOS (Community Enterprise Operating System)**：社区企业操作系统。它基于RHEL的源代码重新编译而成，免费使用，但不提供官方商业支持。新特性通常会先在CentOS上测试，稳定后才加入RHEL。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_87.png)

对于学习者而言，两者的命令和操作方式几乎完全相同。本课程基于RHEL 8.0，但所学知识同样适用于CentOS 8。

Linux系统主要提供两种用户界面：
1.  **图形化界面 (GUI)**：类似于Windows的桌面环境。RHEL 8的桌面默认没有图标，所有程序入口集中在“活动”视图里。
2.  **字符界面 (CLI)**：即命令行界面，是服务器运维的核心。在已开启图形界面的系统上，可以按 `Ctrl+Alt+F2` 到 `F6` 切换到字符终端，按 `Ctrl+Alt+F1` 切回图形界面。

---

## Linux基本框架与Shell

![](img/9b5ed83fe47e32749142fb3e240ea7eb_89.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_91.png)

上一节我们了解了Linux的两种界面，本节中我们来看看Linux系统的基本构成和命令行的核心——Shell。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_93.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_95.png)

Linux系统可以简化为三层结构：
1.  **内核 (Kernel)**：操作系统的核心，管理硬件资源和进程。它采用模块化设计，许多功能（如设备驱动）是按需动态加载的。例如，插入U盘后，系统才会加载对应的USB存储模块。
    ```bash
    lsmod # 查看当前已加载的内核模块
    ```
2.  **Shell**：系统的“外壳”，是用户与内核交互的接口。它接收用户输入的命令，将其翻译给内核执行，并将结果返回给用户。Shell也指代运行在终端中的命令行解释器程序。
3.  **应用程序**：运行在Shell之上，完成各种具体任务的软件。

常见的Shell类型有Bash、Zsh等。在RHEL/CentOS中，默认的Shell是 **Bash (Bourne-Again Shell)**。我们通过输入命令与Bash交互，而Bash脚本则是将多条命令和逻辑判断组合在一起形成的自动化程序。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_97.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_99.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_101.png)

**开源精神**：Linux是开源软件的代表。开源意味着源代码公开，允许自由使用、修改和分发，但这不等同于免费。像Red Hat公司就通过提供技术支持和服务来获得收入。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_103.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_105.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_107.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_109.png)

一个完整的操作系统发行版（如RHEL）被称为 **GNU/Linux**，因为它包含了Linux内核和GNU项目的大量软件工具。

---

![](img/9b5ed83fe47e32749142fb3e240ea7eb_111.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_113.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_115.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_117.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_118.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_120.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_122.png)

## 命令行基础

![](img/9b5ed83fe47e32749142fb3e240ea7eb_124.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_126.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_128.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_130.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_132.png)

现在，让我们开始学习命令行的具体使用。命令行界面由名为Shell的程序提供，它显示一个提示符，等待用户输入命令。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_134.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_135.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_136.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_138.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_140.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_141.png)

### 命令提示符 (Prompt)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_143.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_145.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_147.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_149.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_151.png)

登录系统后看到的字符串就是命令提示符，例如 `[root@servera ~]#`。它的格式由 `PS1` 环境变量定义。
```bash
echo $PS1 # 查看当前PS1变量的值
```
其典型格式 `[\u@\h \W]\$` 含义如下：
*   `\u`：当前登录的用户名。
*   `\h`：主机名的简短形式。
*   `\W`：当前工作目录的基名。`~` 是用户家目录的缩写。
*   `\$`：提示符本身。`#` 表示root用户（超级管理员），`$` 表示普通用户。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_153.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_154.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_156.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_157.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_159.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_161.png)

### 命令的构成与执行

![](img/9b5ed83fe47e32749142fb3e240ea7eb_163.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_164.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_166.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_168.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_170.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_172.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_173.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_175.png)

一个完整的命令通常由三部分组成：**命令本身**、**选项**和**参数**。
```bash
命令 [选项] [参数]
```
*   **命令**：决定要执行什么操作。它本质上是一个可执行文件。系统通过 `PATH` 环境变量中定义的路径来查找命令文件。
    ```bash
    echo $PATH # 查看系统查找命令的路径
    ```
*   **选项**：用于调节命令的行为，通常以 `-`（短选项）或 `--`（长选项）开头。例如 `ls -l` 中的 `-l`。
*   **参数**：命令作用的对象。例如 `ls /tmp` 中的 `/tmp`。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_177.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_178.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_180.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_182.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_184.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_186.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_188.png)

如果输入一个不存在的命令，Shell会报错 `command not found`。如果需要运行一个不在 `PATH` 中的程序，可以指定其完整路径，如 `/opt/myapp`。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_190.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_192.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_193.png)

### 获取命令帮助

![](img/9b5ed83fe47e32749142fb3e240ea7eb_195.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_197.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_198.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_200.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_201.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_202.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_204.png)

如果不清楚某个命令的用法，可以使用 `--help` 选项来查看帮助信息。
```bash
ls --help # 查看ls命令的帮助
```
更详细的帮助文档可以使用 `man` 命令查看（后续章节会详述）。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_206.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_207.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_209.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_211.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_213.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_215.png)

---

![](img/9b5ed83fe47e32749142fb3e240ea7eb_217.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_219.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_221.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_223.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_224.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_226.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_228.png)

## 总结

![](img/9b5ed83fe47e32749142fb3e240ea7eb_230.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_231.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_233.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_235.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_236.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_237.png)

本节课中我们一起学习了访问Linux命令行的核心知识。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_239.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_241.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_242.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_244.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_246.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_248.png)

我们首先配置并连接了实验环境，掌握了使用SSH远程登录Linux服务器的方法。接着，我们了解了RHEL与CentOS的联系与区别，以及Linux图形界面和字符界面的切换方式。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_250.png)

![](img/9b5ed83fe47e32749142fb3e240ea7eb_251.png)

然后，我们剖析了Linux系统的基本框架：内核负责核心资源管理，Shell作为用户与内核交互的桥梁。我们重点讲解了默认的Bash Shell，包括命令提示符的含义、命令的基本结构（命令、选项、参数）以及系统如何查找并执行命令。

![](img/9b5ed83fe47e32749142fb3e240ea7eb_253.png)

掌握这些基础知识是后续所有Linux学习和运维工作的起点。下一节课，我们将开始学习具体的文件和目录操作命令。