# 红帽RHCE认证培训（8.0版本）：02-3：bash执行命令1 📝

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_1.png)

在本节课中，我们将学习如何在Bash命令行中执行多个命令，并详细介绍几个常用的基础命令，包括 `date`、`passwd`、`file`、`cat`、`less`、`head`、`tail` 和 `wc`。这些命令是日常系统管理和文件操作的基础。

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_3.png)

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_5.png)

上一节我们介绍了Shell程序与命令行的基本概念。本节中，我们来看看如何在命令行中实际执行命令。

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_7.png)

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_9.png)

## 执行多个命令

在Bash中，要连续执行多个命令，可以使用分号 `;` 将它们分隔开。系统会按照命令的先后顺序依次执行。

以下是具体示例：
*   `ls ; pwd`：先执行 `ls` 命令列出当前目录内容，然后执行 `pwd` 命令显示当前工作目录。
*   `hostname ; pwd ; ls`：依次执行显示主机名、显示当前目录和列出目录内容。

## 常用命令详解

### 1. `date` 命令：显示和设置系统时间 🕐

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_11.png)

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_13.png)

`date` 命令用于显示或设置系统日期和时间。

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_15.png)

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_17.png)

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_19.png)

**基本用法**：
*   显示当前时间：`date`
*   显示特定描述的时间：`date -d ‘+3 days’` （显示3天后的日期）
*   显示特定描述的时间：`date -d ‘-3 minutes’` （显示3分钟前的时间）

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_21.png)

**格式化输出**：
使用 `+` 和格式控制符可以自定义输出格式。
*   `date +%a`：显示简写的星期几（如 Thu）。
*   `date +%d`：显示月份中的第几天（如 06）。
*   `date +%Y-%m-%d`：显示完整年月日（如 2020-02-06）。

### 2. `hostname` 命令：显示主机名 🖥️

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_23.png)

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_25.png)

`hostname` 命令用于显示当前系统的主机名。
执行 `hostname` 即可。

### 3. `passwd` 命令：修改用户密码 🔑

`passwd` 命令用于修改用户密码。普通用户只能修改自己的密码，而root管理员可以修改任何用户的密码。
*   修改当前用户密码：`passwd`
*   root用户修改其他用户密码：`passwd 用户名`

### 4. `file` 命令：确定文件类型 📄

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_27.png)

`file` 命令用于检测文件的类型。
*   `file /etc/passwd`：检测 `/etc/passwd` 文件的类型（通常是 ASCII text）。
*   `file /bin/passwd`：检测可执行文件 `/bin/passwd` 的类型（通常是 ELF 可执行文件）。
*   `file /home`：检测 `/home` 目录的类型（通常是 directory）。
*   `file /dev/vda`：检测设备文件 `/dev/vda` 的类型（通常是 block special，即块设备）。

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_29.png)

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_31.png)

### 5. `cat` 命令：查看文件内容 👀

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_33.png)

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_35.png)

`cat` 命令用于连接文件并打印到标准输出设备上，常用来查看文件内容。

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_37.png)

**基本用法**：
*   `cat filename`：查看文件内容。
*   `cat file1 file2`：连续查看多个文件内容。

**常用选项**：
*   `-n`：显示行号（包括空行）。
    *   **代码示例**：`cat -n /etc/passwd`
*   `-b`：显示行号（忽略空行）。
*   `-E`：在每行结尾显示 `$` 符号。

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_39.png)

### 6. `less` 和 `more` 命令：分页查看文件内容 📖

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_41.png)

当文件内容很长时，使用 `cat` 会瞬间滚屏。`less` 和 `more` 命令可以分页查看。
*   `less filename`：打开文件进行分页浏览。可以使用 **空格键** 向下翻页，**b键** 向上翻页，**上下箭头** 逐行移动，按 **q键** 退出。
*   `more filename`：功能类似 `less`，但功能相对简单。

### 7. `head` 命令：查看文件开头部分 ⬆️

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_43.png)

`head` 命令用于显示文件的开头部分，默认显示前10行。

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_45.png)

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_47.png)

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_49.png)

**基本用法**：
*   `head filename`：显示文件前10行。
*   `head -n 5 filename`：显示文件前5行。
    *   **代码示例**：`head -n 2 /etc/passwd`
*   `head -n -5 filename`：显示文件除了最后5行以外的所有内容。

### 8. `tail` 命令：查看文件末尾部分 ⬇️

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_51.png)

`tail` 命令用于显示文件的末尾部分，默认显示最后10行。常用于查看日志等实时更新的文件。

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_53.png)

**基本用法**：
*   `tail filename`：显示文件最后10行。
*   `tail -n 5 filename`：显示文件最后5行。
    *   **代码示例**：`tail -n 2 /etc/passwd`
*   `tail -n +5 filename`：显示从第5行开始到文件末尾的所有内容。

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_55.png)

### 9. `wc` 命令：统计文件信息 📊

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_57.png)

`wc` 命令用于统计文件中的字节数、字数、行数。

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_59.png)

**基本用法**：
`wc filename` 输出格式为：**行数 单词数 字节数 文件名**

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_61.png)

**常用选项**：
*   `-l`：只统计行数。
    *   **代码示例**：`wc -l /etc/passwd`
*   `-w`：只统计单词数。
*   `-c`：只统计字节数。

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_63.png)

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_65.png)

---

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_67.png)

![](img/9fec96f3fa406f4b7ea8cfe2e650c08b_69.png)

本节课中我们一起学习了Bash中执行多个命令的方法，并详细介绍了 `date`、`hostname`、`passwd`、`file`、`cat`、`less`、`head`、`tail` 和 `wc` 这九个基础且重要的命令。它们是Linux系统操作的基石，请务必多加练习，熟悉其用法。