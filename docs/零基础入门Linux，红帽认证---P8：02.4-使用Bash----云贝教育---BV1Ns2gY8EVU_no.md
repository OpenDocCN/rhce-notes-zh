# Linux入门与红帽认证：P8：02.4 使用Bash Shell执行命令

![](img/e9e129e58ed48eca31d2756f23de2d0b_1.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_3.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_5.png)

在本节课中，我们将学习如何在Bash Shell中高效地执行命令。我们将从命令的基本结构开始，介绍多种命令输入方式，学习一些常用的基础命令，并掌握提高命令执行效率的技巧，例如命令补全、历史记录和快捷键。

![](img/e9e129e58ed48eca31d2756f23de2d0b_7.png)

## 命令的基本结构

![](img/e9e129e58ed48eca31d2756f23de2d0b_9.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_11.png)

上一节我们介绍了如何在命令提示符中输入命令。一个完整的命令通常由**命令**、**选项**和**参数**组成。

![](img/e9e129e58ed48eca31d2756f23de2d0b_13.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_15.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_16.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_17.png)

*   **命令**：要执行的操作。
*   **选项**：用于修改命令行为的标志，通常以单个短横线 `-` 或两个短横线 `--` 开头。
*   **参数**：命令操作的对象，如文件名、目录名等。

![](img/e9e129e58ed48eca31d2756f23de2d0b_19.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_21.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_22.png)

这些元素之间必须使用**空格**进行分隔。

## 输入多个命令

在Shell提示符中，我们不仅可以输入单个命令，还可以在一行中输入多个命令。

![](img/e9e129e58ed48eca31d2756f23de2d0b_24.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_25.png)

以下是几种连接多个命令的方式：

*   **使用分号 `;`**：分号仅代表命令的先后执行顺序，无论前一个命令是否成功，后面的命令都会执行。
    **语法**：`command1 ; command2`
    **示例**：`whoami ; hostname`

![](img/e9e129e58ed48eca31d2756f23de2d0b_27.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_29.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_31.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_33.png)

*   **使用逻辑与 `&&`**：只有在前一个命令**成功执行**后，才会执行后一个命令。
    **语法**：`command1 && command2`
    **示例**：`make && make install` （先编译成功，再安装）

![](img/e9e129e58ed48eca31d2756f23de2d0b_35.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_37.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_39.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_41.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_43.png)

*   **使用逻辑或 `||`**：如果前一个命令**执行失败**，才会执行后一个命令。
    **语法**：`command1 || command2`
    **示例**：`ping -c1 server1 || echo “Server is down”`

![](img/e9e129e58ed48eca31d2756f23de2d0b_45.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_47.png)

## 常用基础命令

了解了命令的组合方式后，本节我们来看看一些在Linux终端中常用的基础命令。

![](img/e9e129e58ed48eca31d2756f23de2d0b_49.png)

*   **`clear`**：用于清空当前终端屏幕的内容。快捷键是 `Ctrl + L`。

![](img/e9e129e58ed48eca31d2756f23de2d0b_51.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_53.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_55.png)

*   **`date`**：用于显示或设置系统日期和时间。
    *   直接输入 `date` 显示当前时间。
    *   使用 `date --help` 可以查看详细帮助。
    *   使用 `date +”格式”` 可以按指定格式输出时间。
        **示例**：`date +”%Y-%m-%d %H:%M:%S”` 输出类似 `2023-12-16 21:25:00` 的格式。

![](img/e9e129e58ed48eca31d2756f23de2d0b_57.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_59.png)

*   **`passwd`**：用于更改用户密码。单独使用时，更改当前登录用户的密码。更改时需要先输入旧密码进行验证，新密码必须符合复杂性要求（通常至少8位，包含大小写字母、数字或符号）。

![](img/e9e129e58ed48eca31d2756f23de2d0b_61.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_63.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_64.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_65.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_66.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_67.png)

*   **`file`**：用于判断文件类型。在Linux中，“一切皆文件”，系统不依赖文件扩展名来识别类型，`file` 命令通过分析文件内容来识别。
    *   `file 文件名`：显示文件类型。
    *   `file -i 文件名`：显示文件的MIME类型信息。
    **示例**：`file /etc/passwd` 会显示 “ASCII text”。

![](img/e9e129e58ed48eca31d2756f23de2d0b_69.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_71.png)

*   **`cat`**：用于连接文件并打印到标准输出设备，常用来查看文件内容。
    **语法**：`cat 文件1 文件2 ...`
    **示例**：`cat /etc/passwd`

*   **`less` / `more`**：分页查看长文件内容。
    *   `less 文件名`：功能强大，支持上下翻页、搜索。
        *   空格键：向下翻一页。
        *   `b` 键：向上翻一页。
        *   `/关键词`：搜索。
        *   `q` 键：退出。
    *   `more 文件名`：基础分页。
        *   空格键：向下翻页。
        *   `Ctrl+B`：向上翻页。
        *   `q` 键：退出。

![](img/e9e129e58ed48eca31d2756f23de2d0b_73.png)

*   **`head` / `tail`**：查看文件开头或末尾部分。
    *   `head -n 行数 文件名`：查看文件前N行，默认10行。
        **示例**：`head -5 /etc/passwd`
    *   `tail -n 行数 文件名`：查看文件末尾N行，默认10行。
    *   `tail -f 文件名`：动态跟踪文件末尾新增的内容，常用于监控日志。
        **示例**：`tail -f /var/log/messages`

![](img/e9e129e58ed48eca31d2756f23de2d0b_75.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_77.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_79.png)

*   **`wc`**：统计文件的行数、单词数和字符数。
    *   `wc 文件名`：显示行数、单词数、字符数。
    *   `wc -l 文件名`：仅显示行数。
    *   `wc -w 文件名`：仅显示单词数。
    *   `wc -c 文件名`：仅显示字符数。

![](img/e9e129e58ed48eca31d2756f23de2d0b_81.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_83.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_85.png)

## 提高效率的技巧

![](img/e9e129e58ed48eca31d2756f23de2d0b_87.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_89.png)

掌握了基础命令后，使用一些技巧可以让我们在终端中工作得更快、更准确。

![](img/e9e129e58ed48eca31d2756f23de2d0b_91.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_93.png)

### 命令补全 (Tab)

![](img/e9e129e58ed48eca31d2756f23de2d0b_95.png)

按 `Tab` 键可以自动补全命令、文件名、目录名甚至某些命令的选项。
*   按一次 `Tab`：如果只有一个匹配项，则自动补全。
*   按两次 `Tab`：如果有多个匹配项，则列出所有可能性。
**示例**：输入 `pas` 后按 `Tab`，会自动补全为 `passwd`。

![](img/e9e129e58ed48eca31d2756f23de2d0b_97.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_99.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_101.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_103.png)

### 长命令换行

![](img/e9e129e58ed48eca31d2756f23de2d0b_105.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_107.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_109.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_111.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_113.png)

当命令很长时，可以使用反斜杠 `\` 加回车进行换行，使命令结构更清晰。
**示例**：
```bash
head -n 3 /etc/passwd \
/usr/share/dict/linux.words
```

![](img/e9e129e58ed48eca31d2756f23de2d0b_115.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_117.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_119.png)

### 命令历史 (history)

![](img/e9e129e58ed48eca31d2756f23de2d0b_121.png)

Bash Shell会记录之前执行过的命令。
*   `history`：查看命令历史记录列表。
*   `!编号`：重复执行历史记录中对应编号的命令。
    **示例**：`!42`
*   `!关键词`：重复执行最近一条以该关键词开头的命令。
    **示例**：`!date`
*   `Ctrl + R`：进入反向搜索历史模式，输入关键词可搜索历史命令。找到后按回车执行，或按右箭头键调出命令进行编辑。

![](img/e9e129e58ed48eca31d2756f23de2d0b_123.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_125.png)

### 实用快捷键

![](img/e9e129e58ed48eca31d2756f23de2d0b_127.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_129.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_131.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_133.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_135.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_136.png)

以下快捷键能极大提升命令行编辑效率：
*   `Ctrl + A`：光标跳到行首。
*   `Ctrl + E`：光标跳到行尾。
*   `Ctrl + U`：删除光标之前到行首的所有内容。
*   `Ctrl + K`：删除光标之后到行尾的所有内容。
*   `Ctrl + ←` / `Ctrl + →`：以单词为单位向前或向后移动光标。

![](img/e9e129e58ed48eca31d2756f23de2d0b_138.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_140.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_141.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_143.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_145.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_147.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_149.png)

## 总结

![](img/e9e129e58ed48eca31d2756f23de2d0b_151.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_153.png)

![](img/e9e129e58ed48eca31d2756f23de2d0b_154.png)

本节课我们一起学习了Bash Shell执行命令的核心知识。我们首先回顾了命令由命令、选项和参数构成，并通过空格分隔。然后，我们学习了如何使用分号、逻辑与、逻辑或来组合多个命令。接着，我们认识并实践了 `date`, `passwd`, `file`, `cat`, `less`, `head`, `tail`, `wc` 等常用基础命令。最后，为了提高工作效率，我们掌握了命令补全、历史记录查询以及一系列编辑快捷键的使用方法。熟练运用这些知识和技巧，是成为一名高效Linux用户的基础。