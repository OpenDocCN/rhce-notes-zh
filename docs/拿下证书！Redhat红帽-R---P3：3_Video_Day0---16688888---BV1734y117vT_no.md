# RHCE8.0认证体系课程：P3：访问命令行

![](img/279a95bca1022dc21aa22ee8834e1a0c_0.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_2.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_4.png)

## 概述
在本节课中，我们将学习如何访问和使用Linux命令行。内容包括Linux系统的基本框架、Shell的概念、命令行的构成以及如何通过命令行与系统交互。这是后续所有操作的基础。

![](img/279a95bca1022dc21aa22ee8834e1a0c_6.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_8.png)

---

![](img/279a95bca1022dc21aa22ee8834e1a0c_10.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_12.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_13.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_15.png)

## Linux系统基本框架

![](img/279a95bca1022dc21aa22ee8834e1a0c_17.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_19.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_21.png)

上一节我们介绍了课程环境配置，本节中我们来看看Linux系统的基本构成。

Linux系统的核心是**内核**。内核是操作系统的核心部分，在系统启动时会被加载到内存中。为了保持高效，Linux内核采用模块化设计，许多特定功能（如硬件驱动）仅在需要时才动态加载。

例如，可以使用 `lsmod` 命令查看当前已加载的内核模块。

![](img/279a95bca1022dc21aa22ee8834e1a0c_23.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_24.png)

**代码示例：**
```bash
lsmod | grep kvm
```
此命令可以查看KVM虚拟化模块是否已加载。

![](img/279a95bca1022dc21aa22ee8834e1a0c_26.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_28.png)

另一个核心概念是**Shell**。Shell是一个命令行解释器，它充当用户与系统内核之间的“中介”或“外壳”，负责接收用户输入的命令，并将其转换为内核能够理解的操作。它起到保护内核和提供人机交互界面的作用。

![](img/279a95bca1022dc21aa22ee8834e1a0c_30.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_31.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_33.png)

---

![](img/279a95bca1022dc21aa22ee8834e1a0c_35.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_36.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_38.png)

## Shell类型与Bash Shell

![](img/279a95bca1022dc21aa22ee8834e1a0c_40.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_42.png)

Linux系统支持多种Shell。可以通过查看 `/etc/shells` 文件来了解系统支持哪些Shell类型。

![](img/279a95bca1022dc21aa22ee8834e1a0c_44.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_46.png)

**代码示例：**
```bash
cat /etc/shells
```

![](img/279a95bca1022dc21aa22ee8834e1a0c_48.png)

在Red Hat Enterprise Linux 8中，默认使用的Shell是**Bash Shell**。Bash是“Bourne-Again Shell”的缩写，是Unix Shell的一个成功改进版本，功能强大且应用广泛。

![](img/279a95bca1022dc21aa22ee8834e1a0c_50.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_52.png)

当Bash Shell等待用户输入命令时，会显示一个**命令提示符**。提示符的格式由 `PS1` 环境变量控制。

**公式/变量示例：**
```
PS1="\u@\h \W\$ "
```
*   `\u`：当前登录的用户名。
*   `\h`：主机名的简短部分。
*   `\W`：当前工作目录的基名（最后一部分）。
*   `\$`：提示符本身，普通用户显示`$`，超级用户root显示`#`。

例如，`root@servera ~#` 表示当前用户是root，主机名是servera，位于用户的家目录（`~`），并且拥有root权限。

---

## 命令的构成与执行

理解了Shell之后，我们来看看如何在其中执行命令。

![](img/279a95bca1022dc21aa22ee8834e1a0c_54.png)

在Linux中，**命令本质上是一个可执行文件**。当用户输入一个命令时，Shell会在一系列预设的目录中查找同名的可执行文件。这些目录的路径存储在 `PATH` 环境变量中。

**代码示例：**
```bash
echo $PATH
```

命令通常由三部分组成：**命令本身**、**选项**和**参数**。

以下是命令各部分的说明：
*   **命令**：决定要执行什么操作。
*   **选项**：通常以 `-` 或 `--` 开头，用于修改命令的行为或输出格式。单字母选项前加一个短横线（如 `-l`），完整单词选项前加两个短横线（如 `--help`）。
*   **参数**：命令操作的对象，例如文件名或目录名。

![](img/279a95bca1022dc21aa22ee8834e1a0c_56.png)

**命令示例：**
```bash
ls -l /home
```
*   `ls` 是命令，意为“列出目录内容”。
*   `-l` 是选项，表示以长格式（详细信息）显示。
*   `/home` 是参数，指定要操作的目标目录。

![](img/279a95bca1022dc21aa22ee8834e1a0c_58.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_60.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_61.png)

如果不知道某个命令的用法，可以使用 `--help` 选项或 `man` 命令来查看帮助文档。

**代码示例：**
```bash
ls --help
man ls
```

---

![](img/279a95bca1022dc21aa22ee8834e1a0c_63.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_65.png)

## 系统登录与访问方式

![](img/279a95bca1022dc21aa22ee8834e1a0c_67.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_69.png)

本节我们来看看如何登录和访问Linux系统。

![](img/279a95bca1022dc21aa22ee8834e1a0c_71.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_73.png)

访问Linux命令行主要有以下几种方式：
1.  **本地图形界面终端**：在系统桌面环境中打开终端应用程序。
2.  **本地虚拟控制台**：在物理服务器前，可以通过快捷键 `Ctrl+Alt+F2` 到 `F6` 切换到纯字符界面的虚拟控制台进行登录。使用 `Ctrl+Alt+F1` 可以切换回图形界面。
3.  **远程SSH连接**：这是运维工作中最常用的方式。通过网络使用SSH协议连接到服务器。

![](img/279a95bca1022dc21aa22ee8834e1a0c_75.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_76.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_78.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_79.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_81.png)

![](img/279a95bca1022dc21aa22ee8834e1a0c_83.png)

**远程连接示例：**
```bash
ssh username@server_ip_address
```
例如，`ssh root@192.168.1.100`。首次连接时会提示确认主机密钥，输入`yes`并回车，然后输入密码即可登录。

![](img/279a95bca1022dc21aa22ee8834e1a0c_85.png)

---

![](img/279a95bca1022dc21aa22ee8834e1a0c_87.png)

## 总结
本节课中我们一起学习了Linux命令行的基础知识。我们了解了Linux内核与Shell的关系，认识了默认的Bash Shell及其命令提示符的含义。我们重点掌握了命令的构成（命令、选项、参数）以及命令的执行原理（通过PATH变量查找可执行文件）。最后，我们介绍了访问Linux命令行的几种主要方式，特别是远程SSH连接，这是后续实验和实际工作的主要手段。掌握这些内容是熟练使用Linux系统的第一步。