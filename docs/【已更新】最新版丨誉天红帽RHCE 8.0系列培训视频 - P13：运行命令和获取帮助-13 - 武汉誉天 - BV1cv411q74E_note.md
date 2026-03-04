# Linux基础入门：第3章：运行命令和获取帮助 📚

![](img/cecfa8fbafc99334b4ce479ec45f3b48_1.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_3.png)

在本节课中，我们将要学习Linux命令的基本语法结构，以及如何高效地执行命令和获取帮助信息。掌握这些是熟练使用Linux系统的关键第一步。

![](img/cecfa8fbafc99334b4ce479ec45f3b48_5.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_7.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_8.png)

## 命令的基本语法

上一节我们介绍了登录系统和一些基础概念，本节中我们来看看命令是如何构成的。

![](img/cecfa8fbafc99334b4ce479ec45f3b48_10.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_11.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_13.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_14.png)

一个完整的Linux命令通常由三部分组成：**命令**、**选项**和**参数**。其基本语法格式如下：
```
command [options] [arguments]
```
*   **命令 (Command)**：要执行的操作，永远是命令行的第一个单词。
*   **选项 (Options)**：用于修改命令行为的标志，通常以 `-` 或 `--` 开头。
*   **参数 (Arguments)**：命令操作的对象，如文件名、目录名或用户名等。

例如，在命令 `ls -l /home` 中：
*   `ls` 是命令（列出目录内容）。
*   `-l` 是选项（以长格式显示详细信息）。
*   `/home` 是参数（指定要列出的目录）。

![](img/cecfa8fbafc99334b4ce479ec45f3b48_16.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_17.png)

如果输入的命令不存在，系统会提示 `command not found`。

## 提高命令执行效率的技巧

![](img/cecfa8fbafc99334b4ce479ec45f3b48_19.png)

以下是几种能显著提高命令行操作效率的技巧。

### 使用Tab键自动补全

Tab键是命令行中最实用的工具之一，它可以自动补全命令、文件路径和参数。

![](img/cecfa8fbafc99334b4ce479ec45f3b48_21.png)

*   **补全命令或路径**：输入命令或路径的前几个字符后按一次 `Tab` 键，如果唯一匹配，系统会自动补全。按两次 `Tab` 键，会列出所有可能的匹配项。
*   **注意事项**：如果系统是最小化安装（无图形界面），可能无法使用Tab键补全功能。

![](img/cecfa8fbafc99334b4ce479ec45f3b48_23.png)

### 调用历史命令

系统会记录执行过的命令，使用历史记录可以快速重新执行命令，无需重复输入。

以下是调用历史记录的几种方法：
*   **`history`**：查看所有执行过的命令历史列表。
*   **`!n`**：执行历史列表中编号为 `n` 的命令（例如 `!58`）。
*   **`!string`**：执行最近一条以 `string` 开头的命令。
*   **`!!`**：执行上一条命令。
*   **上下方向键**：在历史命令中逐条浏览。
*   **`Ctrl + R`**：进入反向搜索模式，输入关键词可搜索历史命令。
*   **`Alt + .` 或 `Esc + .`**：快速输入上一条命令的最后一个参数。

## 获取命令帮助

![](img/cecfa8fbafc99334b4ce479ec45f3b48_25.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_27.png)

当你不熟悉某个命令的用法时，学会如何获取帮助至关重要。

![](img/cecfa8fbafc99334b4ce479ec45f3b48_29.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_31.png)

### 使用 `--help` 选项

![](img/cecfa8fbafc99334b4ce479ec45f3b48_33.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_35.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_36.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_38.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_40.png)

大多数命令都支持 `--help` 或 `-h` 选项来显示简明的使用帮助。
```
command --help
```

### 使用 `man` 手册页

![](img/cecfa8fbafc99334b4ce479ec45f3b48_42.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_44.png)

`man` (manual) 命令是Linux系统最权威的命令手册，提供了命令的详细说明、选项和参数信息。
```
man command
```
在 `man` 页面中，可以使用方向键浏览，按 `q` 键退出。

### 查阅红帽官方文档

![](img/cecfa8fbafc99334b4ce479ec45f3b48_46.png)

对于红帽企业Linux (RHEL) 系统，红帽官方提供了详尽的产品文档，是解决问题和深入学习的重要资源。

## 关于root用户的警告 ⚠️

![](img/cecfa8fbafc99334b4ce479ec45f3b48_48.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_49.png)

`root` 是Linux系统的超级用户，拥有最高权限，几乎可以执行任何操作，包括破坏系统。

![](img/cecfa8fbafc99334b4ce479ec45f3b48_51.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_52.png)

*   **慎用root**：在日常工作和学习中，除非必要，否则应避免直接使用 `root` 账户登录或操作，以防止误操作导致严重后果。
*   **使用普通用户**：建议使用普通用户账户进行日常操作，在需要提升权限时再使用 `su` 或 `sudo` 命令。
*   **实验环境与生产环境分离**：练习时务必在虚拟机或测试环境中进行，切勿直接在生产服务器上操作。

![](img/cecfa8fbafc99334b4ce479ec45f3b48_54.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_56.png)

---

![](img/cecfa8fbafc99334b4ce479ec45f3b48_58.png)

![](img/cecfa8fbafc99334b4ce479ec45f3b48_60.png)

本节课中我们一起学习了Linux命令的基本语法结构，掌握了利用Tab键和历史记录提升效率的技巧，并了解了如何通过 `--help`、`man` 手册和官方文档获取帮助。同时，我们再次强调了谨慎使用 `root` 权限的重要性。请务必在实验环境中多加练习，巩固这些基础知识。