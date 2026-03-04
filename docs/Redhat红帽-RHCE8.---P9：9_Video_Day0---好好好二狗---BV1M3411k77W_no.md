# Redhat红帽 RHCE8.0认证体系课程：P9：重定向和vim编辑器 🖥️

![](img/0cf8639cb7eced62188608bfa7d4c96a_1.png)

在本节课中，我们将要学习Linux系统中的两个核心技能：**重定向**和**vim编辑器**。重定向是管理命令输入输出的重要手段，而vim则是Linux系统中最强大、最常用的文本编辑器。掌握它们对于日常运维和系统管理至关重要。

## 重定向 📤

上一节我们回顾了之前的知识，本节中我们来看看重定向。重定向是指改变命令默认的输入/输出方向。默认情况下，命令的执行结果会输出到终端屏幕。但我们可以通过重定向，将输出结果导入到文件中，或者从文件中读取输入。

![](img/0cf8639cb7eced62188608bfa7d4c96a_3.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_5.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_7.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_9.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_11.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_12.png)

这种情况主要应用于日志收集和排错。因为输出到屏幕上的信息稍纵即逝，而重定向可以让我们保存这些信息。

![](img/0cf8639cb7eced62188608bfa7d4c96a_14.png)

### 输出类型

![](img/0cf8639cb7eced62188608bfa7d4c96a_16.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_18.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_20.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_21.png)

在Linux系统中，命令的输出分为两种类型：
*   **标准输出 (stdout)**：命令正常执行后的输出结果，其文件描述符为 **1**。
*   **标准错误输出 (stderr)**：命令执行出错时的输出结果，其文件描述符为 **2**。

此外，命令的输入来源其文件描述符为 **0**。

![](img/0cf8639cb7eced62188608bfa7d4c96a_23.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_25.png)

### 输出重定向

![](img/0cf8639cb7eced62188608bfa7d4c96a_27.png)

以下是输出重定向的常见用法：

![](img/0cf8639cb7eced62188608bfa7d4c96a_29.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_31.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_33.png)

*   **覆盖重定向**：使用单个右尖括号 `>`。将命令的输出覆盖到指定文件。
    ```bash
    ls -l /tmp > /tmp/output.txt
    ```
*   **追加重定向**：使用两个右尖括号 `>>`。将命令的输出追加到指定文件的末尾。
    ```bash
    echo "New line" >> /tmp/output.txt
    ```
*   **错误输出重定向**：使用文件描述符 `2` 配合 `>` 或 `>>`。将错误信息重定向到文件。
    ```bash
    cd /root 2> /tmp/error.log  # student用户无权限进入/root目录
    ```
*   **丢弃输出**：重定向到空设备 `/dev/null`，相当于丢弃输出。
    ```bash
    cd /root 2> /dev/null
    ```
*   **合并重定向**：使用 `&>` 或 `&>>` 将标准输出和标准错误输出同时重定向到同一个文件。
    ```bash
    find /home -user student &> /tmp/find_result.txt
    ```
    另一种等效写法是 `2>&1`，表示将错误输出（2）重定向到标准输出（1）的同一位置。
    ```bash
    find /home -user student > /tmp/find_result.txt 2>&1
    ```

![](img/0cf8639cb7eced62188608bfa7d4c96a_35.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_37.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_39.png)

**特别说明**：在使用重定向，尤其是将输出内容保存到重要文件时，建议优先使用追加重定向 `>>`，以避免意外覆盖原有内容。

![](img/0cf8639cb7eced62188608bfa7d4c96a_41.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_43.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_45.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_47.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_49.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_51.png)

### 输入重定向与管道

![](img/0cf8639cb7eced62188608bfa7d4c96a_53.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_55.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_57.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_59.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_61.png)

*   **输入重定向**：使用左尖括号 `<`，将文件内容作为命令的输入。
    ```bash
    wc -l < /etc/passwd
    ```
*   **管道**：使用竖线 `|`，将一个命令的输出作为另一个命令的输入。这是Linux中组合命令的强大工具。
    ```bash
    cat /etc/passwd | more  # 分页查看
    cat /etc/passwd | wc -l # 统计行数
    ```
*   **tee命令**：在屏幕上显示输出结果的同时，将其保存到文件。
    ```bash
    cat /etc/passwd | tee /tmp/passwd_copy.txt
    ```

![](img/0cf8639cb7eced62188608bfa7d4c96a_63.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_64.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_66.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_68.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_70.png)

---

![](img/0cf8639cb7eced62188608bfa7d4c96a_72.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_74.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_76.png)

## Vim编辑器 ✏️

![](img/0cf8639cb7eced62188608bfa7d4c96a_78.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_80.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_82.png)

上一节我们介绍了如何控制命令的输入输出，本节中我们来看看如何高效地创建和编辑文本文件。Vim是Vi编辑器的增强版，提供了语法高亮等更友好的功能。如果系统未安装，可以使用以下命令安装：
```bash
dnf -y install vim-enhanced
```

![](img/0cf8639cb7eced62188608bfa7d4c96a_84.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_85.png)

### Vim的三种模式

![](img/0cf8639cb7eced62188608bfa7d4c96a_87.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_89.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_91.png)

Vim编辑器主要包含三种工作模式，理解并熟练切换模式是使用Vim的关键。

![](img/0cf8639cb7eced62188608bfa7d4c96a_93.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_95.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_97.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_99.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_101.png)

1.  **命令模式 (Command Mode)**：启动Vim后默认进入的模式。在此模式下可以执行复制、删除、查找、替换等快捷操作，但不能直接输入文本。按 `ESC` 键可以从其他模式返回命令模式。
2.  **插入模式 (Insert Mode)**：在此模式下可以像普通编辑器一样输入和编辑文本。从命令模式可以通过以下按键进入插入模式：
    *   `i`：在光标当前位置前开始插入。
    *   `I`：在光标所在行的行首开始插入。
    *   `a`：在光标当前位置后开始插入。
    *   `A`：在光标所在行的行尾开始插入。
    *   `o`：在光标所在行的下一行新建一行开始插入。
    *   `O`：在光标所在行的上一行新建一行开始插入。
3.  **末行模式 (Last Line Mode)**：在命令模式下按 `:` 进入。在此模式下可以执行保存、退出、查找替换、设置等需要输入命令的操作。

![](img/0cf8639cb7eced62188608bfa7d4c96a_102.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_104.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_106.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_108.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_110.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_112.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_114.png)

### 常用操作快捷键

![](img/0cf8639cb7eced62188608bfa7d4c96a_116.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_118.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_120.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_122.png)

以下是各模式下的一些核心操作：

![](img/0cf8639cb7eced62188608bfa7d4c96a_124.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_126.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_128.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_130.png)

**命令模式操作**
*   **复制/粘贴/剪切**：
    *   `yy`：复制光标所在整行。
    *   `nyy`：复制从光标所在行开始的n行（例如 `5yy`）。
    *   `p`：在光标下一行粘贴。
    *   `P`：在光标上一行粘贴。
    *   `dd`：剪切（删除）光标所在整行。
    *   `ndd`：剪切（删除）从光标所在行开始的n行。
*   **撤销/重做**：
    *   `u`：撤销上一次操作。
    *   `Ctrl + r`：重做被撤销的操作。
*   **删除字符**：
    *   `x`：删除光标所在位置的字符。
    *   `X`：删除光标所在位置前的一个字符。
*   **替换字符**：`r` + `新字符`：替换光标所在位置的单个字符。
*   **光标跳转**：
    *   `gg`：跳转到文件第一行。
    *   `G`：跳转到文件最后一行。
    *   `:n`：在末行模式下，跳转到第n行（例如 `:10`）。
*   **查找**：
    *   `/关键词`：从光标处向后查找关键词，按 `n` 查找下一个，按 `N` 查找上一个。
    *   `:nohl`：取消搜索结果的高亮显示。

**末行模式操作**
*   **保存与退出**：
    *   `:w`：保存文件。
    *   `:q`：退出Vim（如果文件有修改未保存，会提示）。
    *   `:wq` 或 `:x`：保存并退出。
    *   `:q!`：强制退出，不保存修改。
    *   `:wq!`：强制保存并退出（常用于只读文件的保存，需要权限）。
*   **显示行号**：
    *   `:set nu`：显示行号。
    *   `:set nonu`：取消显示行号。
*   **文本替换**：替换命令格式为 `:[范围]s/旧字符串/新字符串/[标志]`。
    *   `:%s/old/new/g`：将文件中所有的 `old` 替换为 `new`。
    *   `:10,20s/old/new/g`：将第10行到第20行所有的 `old` 替换为 `new`。
    *   **注意**：如果字符串中包含 `/`，需要使用反斜杠 `\` 进行转义，例如 `:%s/\/bin\/nologin/\/bin\/bash/g`。
*   **执行外部命令与读入内容**：
    *   `:!命令`：暂时离开Vim执行外部Shell命令。
    *   `:r 文件名`：将指定文件的内容读入到当前光标下方。
    *   `:r !命令`：将外部命令的执行结果读入到当前光标下方。

![](img/0cf8639cb7eced62188608bfa7d4c96a_132.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_133.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_134.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_136.png)

### 高级功能

![](img/0cf8639cb7eced62188608bfa7d4c96a_138.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_140.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_142.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_144.png)

*   **分屏编辑**：
    *   `vim -o 文件1 文件2`：水平分屏打开多个文件。
    *   `vim -O 文件1 文件2`：垂直分屏打开多个文件。
    *   在Vim内，`Ctrl+w` 然后按方向键（`h/j/k/l` 或 箭头键）可以在不同窗口间切换。
*   **可视化模式**：
    *   `v`：进入字符可视化模式，可选中单个字符。
    *   `V`：进入行可视化模式，可整行选中。
    *   `Ctrl+v`：进入块可视化模式，可进行矩形区域选择。选中后可以进行 `y`（复制）、`d`（删除）等操作。
*   **交换文件与恢复**：Vim在编辑文件时会生成一个 `.swp` 的交换文件，用于异常退出时恢复。如果打开文件时提示已存在交换文件，可以选择：
    *   `R`：恢复（Recover）内容。
    *   `D`：删除交换文件。
    *   `Q`：退出。
    *   也可以使用 `vim -r 文件名` 来恢复文件。

![](img/0cf8639cb7eced62188608bfa7d4c96a_146.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_148.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_150.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_151.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_153.png)

---

![](img/0cf8639cb7eced62188608bfa7d4c96a_155.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_157.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_159.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_161.png)

![](img/0cf8639cb7eced62188608bfa7d4c96a_162.png)

本节课中我们一起学习了Linux的重定向和vim编辑器。重定向帮助我们灵活管理命令的输入输出流，是脚本编写和日志管理的基石。而vim编辑器作为一款模态编辑器，虽然初学有一定门槛，但其高效的快捷键和强大的功能，一旦掌握将极大提升文本处理效率。请务必多加练习，熟悉三种模式的切换和常用快捷键。