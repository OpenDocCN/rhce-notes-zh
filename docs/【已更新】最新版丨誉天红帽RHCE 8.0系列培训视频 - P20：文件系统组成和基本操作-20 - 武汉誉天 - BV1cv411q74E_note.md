# Linux文件系统与基本操作：P20：文件系统组成和基本操作-20

## 概述

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_1.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_3.png)

在本节课中，我们将学习Linux文件系统的核心概念，包括文件的时间戳属性、备份策略原理以及文件与目录的基本操作命令。我们还将探讨一些操作中的风险与注意事项，帮助初学者建立安全、规范的操作习惯。

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_5.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_7.png)

---

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_9.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_11.png)

## 文件时间戳与备份原理

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_13.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_15.png)

上一节我们介绍了文件系统的基本结构，本节中我们来看看文件的一些重要属性如何影响日常操作，例如数据备份。

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_17.png)

Linux系统中的每个文件都包含三个重要的时间戳，它们对于判断文件状态和实现增量备份至关重要。

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_19.png)

1.  **修改时间 (Mtime)**
    *   **定义**：文件内容最后一次被修改的时间。
    *   **公式/代码表示**：`ls -l` 命令默认显示的时间即为文件的 Mtime。

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_21.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_23.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_25.png)

2.  **访问时间 (Atime)**
    *   **定义**：文件内容最后一次被访问（读取）的时间。

3.  **状态变更时间 (Ctime)**
    *   **定义**：文件状态（元数据）最后一次发生变化的时间。状态包括文件权限、所有者、所属组、大小等属性。
    *   **重要关系**：当文件内容被修改（Mtime变化）时，其状态通常也会改变（例如文件大小可能变化），因此 **Ctime 一定会随之更新**。反之，仅改变文件属性（如权限）导致 Ctime 更新时，Mtime 不会改变。

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_27.png)

**备份策略应用**：
了解这些时间戳后，我们可以理解常见的备份策略：
*   **全量备份**：复制所有数据。优点是数据完整；缺点是耗时、占用存储空间大。
*   **增量备份**：基于上一次备份（无论是全备还是增备），只备份发生变化（通常根据 Mtime 判断）的数据。优点是速度快、节省空间；缺点是恢复数据时依赖备份链，中间任何一环损坏都可能导致后续备份失效。
*   **常用策略**：结合两者，例如每周进行一次全量备份，每天进行增量备份。

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_29.png)

---

## 文件与目录操作命令详解

掌握了文件的基础属性后，我们来看看如何对它们进行操作。以下是管理文件和目录的核心命令。

### 更新文件时间戳：`touch` 命令

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_31.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_33.png)

`touch` 命令常用于创建空文件，但其核心功能是更新文件的时间戳。这在某些场景下很有用，例如触发备份系统对重要文件进行再次备份。

**基本用法**：
*   `touch filename`：如果文件不存在则创建；如果存在，则将其所有时间戳更新为当前时间。
*   `touch -a filename`：仅更新文件的 Atime。
*   `touch -m filename`：仅更新文件的 Mtime。

### 删除文件或目录：`rm` 命令

`rm` (remove) 命令用于删除文件或目录。这是一个需要**极其谨慎**使用的命令。

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_35.png)

**基本用法**：
*   `rm file`：删除文件。在红帽企业版Linux中，默认会提示确认（因为设置了别名 `rm -i`）。
*   `rm -r directory`：递归删除目录及其内部所有内容。
*   `rm -f file_or_directory`：强制删除，不进行任何提示。
*   `rm -rf directory`：**强制递归删除目录**。这是最危险的命令组合之一。

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_37.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_38.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_40.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_42.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_44.png)

**⚠️ 严重警告与原理**：
*   `rm -rf /` 或 `rm -rf /*` 命令会尝试删除根目录下的所有文件，导致系统崩溃和数据永久丢失。**永远不要在生产环境或没有快照的系统中尝试此命令**。
*   删除目录的本质是递归删除该目录下的所有文件。
*   系统提供的“确认提示”是命令别名（alias）实现的，并非所有Linux发行版都默认启用。直接使用 `/usr/bin/rm` 就不会有提示。安全操作不应依赖提示。
*   在脚本中使用变量构造路径时，务必检查变量是否可能为空，否则可能意外匹配到根路径，造成灾难性后果。

### 创建目录：`mkdir` 命令

`mkdir` (make directory) 命令用于创建新目录。

**基本用法**：
*   `mkdir dirname`：创建单个目录。
*   `mkdir -p /path/to/dir`：递归创建目录。如果路径中的父目录不存在，`-p` (parents) 选项会一并创建它们。
*   `mkdir -pv /path/to/dir`：`-v` (verbose) 选项显示详细的创建过程。

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_46.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_48.png)

### 删除空目录：`rmdir` 命令

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_50.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_52.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_54.png)

`rmdir` 命令用于删除**空目录**。如果目录非空，该命令将失败。

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_56.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_58.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_60.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_62.png)

**基本用法**：
*   `rmdir empty_dir`：删除一个空目录。

### 查看文件类型：`file` 命令

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_64.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_66.png)

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_68.png)

在Linux中，文件扩展名（如 .txt, .pdf）并不决定文件类型，系统不依赖它来关联打开程序。`file` 命令通过分析文件内容来识别其真实类型。

**基本用法**：
*   `file filename`：显示文件的类型描述，例如 “ASCII text”, “PDF document”, “Zip archive data”, “directory” 等。

---

## 课程总结与作业要求

本节课中我们一起学习了Linux文件系统的关键属性——三种时间戳（Atime, Mtime, Ctime）及其在备份策略中的应用。我们详细讲解了文件与目录的创建（`touch`, `mkdir`）、删除（`rm`, `rmdir`）和类型查看（`file`）命令，并重点强调了 `rm -rf` 命令的极端危险性以及系统操作的规范性。

![](img/fdcea0b9cc714cbe0e8635076e8ea7f2_70.png)

**课后作业要求**：
1.  **提交方式**：将完成的作业文档发送至指定QQ邮箱。
2.  **文档命名**：`RHCE_姓名.docx`（请务必替换为自己的姓名）。
3.  **内容规范**：
    *   对于每个操作题，需要提供**命令截图**和**结果验证截图**。
    *   可以在截图上进行标记，清晰地指出操作步骤和验证点。
    *   如果题目未能完成，请如实说明，以便重点讲解。
4.  **截止时间**：请在周四之前提交作业。
5.  **学习建议**：按时上课，如有缺课请及时观看录播视频补课。多练习是掌握命令的关键。