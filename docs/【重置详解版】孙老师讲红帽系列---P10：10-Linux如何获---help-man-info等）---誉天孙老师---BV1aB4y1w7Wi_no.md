# Linux基础教程：P10：Linux如何获取帮助（which, whatis, --help, man, info等）

![](img/de84dbd78c80af70cfb802c7544c98d4_1.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_3.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_5.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_7.png)

## 概述
在本节课中，我们将学习在Linux系统中如何获取命令的帮助信息。掌握这些方法至关重要，因为你不可能记住所有命令的细节。我们将介绍多种获取帮助的工具，包括 `which`、`whatis`、`--help`、`man` 和 `info`，并学习如何有效地使用它们。

![](img/de84dbd78c80af70cfb802c7544c98d4_9.png)

## 为什么需要获取帮助
上一节我们学习了几个基础命令，但Linux命令众多，选项复杂。不要尝试记住一切，这是不现实的。关键在于学会在需要时查找帮助。此外，养成做笔记的习惯也非常重要，这能帮助你积累经验，在未来快速回顾和解决问题。

![](img/de84dbd78c80af70cfb802c7544c98d4_11.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_13.png)

## 帮助工具详解
以下是Linux系统中不同级别的帮助资源及其使用方法。

### 1. 使用 `which` 定位命令文件
首先，我们需要理解Linux中的命令本质上都是可执行文件。`which` 命令可以帮助我们查找某个命令对应的是哪个具体文件。

![](img/de84dbd78c80af70cfb802c7544c98d4_15.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_17.png)

**命令格式**：
```bash
which [命令名]
```

![](img/de84dbd78c80af70cfb802c7544c98d4_19.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_21.png)

例如，查找 `ls` 命令的位置：
```bash
which ls
```
输出可能为 `/usr/bin/ls`，这表示 `ls` 命令是 `/usr/bin/ls` 这个文件。

如果 `which` 命令查找不到对应的文件，则说明该命令在系统中不存在。

### 2. 使用 `whatis` 查看简短描述
当我们遇到一个陌生的命令，想快速了解其基本用途时，可以使用 `whatis` 命令。它会显示命令的简短描述。

**命令格式**：
```bash
whatis [命令名]
```

例如：
```bash
whatis ls
```
输出为 “list directory contents”（列出目录内容）。

需要注意的是，`whatis` 的查询基于一个数据库，该系统在安装后或新安装命令后可能不会立即更新。你可以手动更新此数据库：
```bash
mandb
```
或者等待系统在夜间自动更新。

![](img/de84dbd78c80af70cfb802c7544c98d4_23.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_24.png)

### 3. 使用 `--help` 获取内置帮助
大多数命令都支持 `--help` 选项（有时是 `-h`），它能输出该命令的语法、选项和参数的简要说明。

**命令格式**：
```bash
[命令名] --help
```

![](img/de84dbd78c80af70cfb802c7544c98d4_26.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_27.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_28.png)

例如，查看 `ls` 命令的帮助：
```bash
ls --help
```
输出会包含用法（Usage）、选项列表及其解释。

![](img/de84dbd78c80af70cfb802c7544c98d4_30.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_32.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_34.png)

在查看 `--help` 输出时，需要理解一些常见符号：
*   **中括号 `[]`**：表示括号内的内容是可选的。
*   **省略号 `...`**：表示前面的参数可以指定多个。
*   **竖线 `|`**：表示“或”的关系，例如 `-a | --all` 表示可以使用 `-a` 或 `--all`。

虽然 `--help` 很方便，但其内容可能不完整，且在纯字符界面下翻阅不太方便。

![](img/de84dbd78c80af70cfb802c7544c98d4_36.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_38.png)

### 4. 使用 `man` 手册（最重要）
`man`（manual的缩写）是Linux中最重要、最全面的帮助工具。它提供了命令的详细手册，结构清晰，便于查阅。

![](img/de84dbd78c80af70cfb802c7544c98d4_40.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_42.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_44.png)

**命令格式**：
```bash
man [命令名]
```

![](img/de84dbd78c80af70cfb802c7544c98d4_46.png)

进入 `man` 页面后，可以使用以下按键操作：
*   **空格键**：向下翻一页。
*   **回车键**：向下翻一行。
*   **`b`**：向上翻一页。
*   **`/关键词`**：搜索指定关键词，按 `n` 向下查找，按 `N` 向上查找。
*   **`q`**：退出 `man` 页面。

![](img/de84dbd78c80af70cfb802c7544c98d4_48.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_50.png)

一个典型的 `man` 手册包含以下部分：
*   **NAME（名称）**：命令名称及简要说明。
*   **SYNOPSIS（概要）**：命令的语法格式。
*   **DESCRIPTION（描述）**：命令功能的详细描述。
*   **OPTIONS（选项）**：所有选项的详细解释。
*   **EXAMPLES（示例）**：使用示例。
*   **SEE ALSO（参见）**：相关命令或文档。

![](img/de84dbd78c80af70cfb802c7544c98d4_52.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_54.png)

#### `man` 的章节
`man` 手册分为多个章节，涵盖不同内容。常用的章节有：
*   **1**：用户命令（可执行程序）。
*   **5**：文件格式和约定（如配置文件的格式）。
*   **8**：系统管理命令（通常需要root权限）。

![](img/de84dbd78c80af70cfb802c7544c98d4_56.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_58.png)

指定章节查看：
```bash
man 5 passwd  # 查看 /etc/passwd 文件格式的帮助
man 8 fdisk   # 查看 fdisk 命令的帮助（系统管理命令）
```
如果不指定章节，`man` 会显示默认章节（通常是第1章）的内容。

![](img/de84dbd78c80af70cfb802c7544c98d4_60.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_62.png)

使用 `man -k` 可以进行关键词搜索，查找所有相关的手册页：
```bash
man -k password
```

![](img/de84dbd78c80af70cfb802c7544c98d4_64.png)

### 5. 使用 `info` 和 `pinfo`
`info` 是另一种文档格式，通常比 `man` 提供更详细、结构更复杂的信息，类似于超链接文档。`pinfo` 是 `info` 的增强版，支持颜色和高亮，浏览体验更好。

![](img/de84dbd78c80af70cfb802c7544c98d4_66.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_68.png)

**命令格式**：
```bash
info [命令名]
pinfo [命令名]
```
对于初学者，`man` 通常已足够。`info`/`pinfo` 可以在需要更深入信息时使用。

![](img/de84dbd78c80af70cfb802c7544c98d4_70.png)

### 6. 其他帮助资源
除了上述系统内置工具，还有以下资源：
*   **软件包自带的文档**：安装在 `/usr/share/doc/` 目录下。
*   **在线文档**：尤其是发行商的官方文档。例如，Red Hat Enterprise Linux 的官方文档网站提供了安装、配置、管理等各方面的详细指南。虽然多为英文，但这是解决问题最权威的参考。

![](img/de84dbd78c80af70cfb802c7544c98d4_72.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_74.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_76.png)

## 实践练习
现在，让我们通过一个练习来巩固所学知识。

![](img/de84dbd78c80af70cfb802c7544c98d4_78.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_80.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_82.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_84.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_86.png)

**任务**：
1.  将系统时间设置为 2020年4月11日 9点整。
2.  以特定格式显示这个时间，格式要求为 “年-月-日 时:分:秒”，例如 “2020-04-11 09:00:00”。

![](img/de84dbd78c80af70cfb802c7544c98d4_88.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_89.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_91.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_93.png)

**提示**：
*   设置时间需要使用 `date` 命令，并查阅其 `man` 手册或 `--help` 来了解设置时间的语法。
*   显示特定格式也需要使用 `date` 命令，并查找关于格式化输出（`+FORMAT`）的部分。

![](img/de84dbd78c80af70cfb802c7544c98d4_95.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_97.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_99.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_100.png)

请尝试独立完成，然后对照答案进行检查。

![](img/de84dbd78c80af70cfb802c7544c98d4_102.png)

## 总结
本节课我们一起学习了在Linux中获取帮助的多种方法。我们介绍了：
*   `which`：定位命令文件。
*   `whatis`：查看命令的简短描述。
*   `--help`：获取命令的内置快速帮助。
*   `man`：查阅详细的手册页（最重要），包括其章节和操作方法。
*   `info`/`pinfo`：浏览更结构化的文档。
*   官方在线文档等外部资源。

![](img/de84dbd78c80af70cfb802c7544c98d4_104.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_106.png)

![](img/de84dbd78c80af70cfb802c7544c98d4_108.png)

记住，善于查找帮助是学好Linux的关键技能。不要害怕忘记命令，而要熟练运用这些工具来解决问题。从今天起，养成遇到新命令先查 `man` 的习惯吧！