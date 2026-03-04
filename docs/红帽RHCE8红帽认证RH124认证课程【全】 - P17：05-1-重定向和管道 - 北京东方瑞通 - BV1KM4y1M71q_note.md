# 红帽RHCE8认证课程：05-1：重定向和管道 📖

![](img/84c59784c225b2cea94aeebb574c0c8d_1.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_3.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_4.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_6.png)

在本节课中，我们将要学习Linux命令行中两个非常核心且强大的概念：**重定向**和**管道**。我们将了解如何控制命令的输入与输出，以及如何将多个命令串联起来，实现更复杂的数据处理。

![](img/84c59784c225b2cea94aeebb574c0c8d_8.png)

## 标准输入、输出与错误

![](img/84c59784c225b2cea94aeebb574c0c8d_10.png)

上一节我们介绍了命令执行的基本流程，本节中我们来看看Linux系统是如何管理命令的输入和输出的。

![](img/84c59784c225b2cea94aeebb574c0c8d_12.png)

在Linux中，每个运行的程序（进程）都有三个默认的“数据流”：
*   **标准输入 (stdin)**：用文件描述符 `0` 表示。程序从此读取输入数据，默认来自键盘。
*   **标准输出 (stdout)**：用文件描述符 `1` 表示。程序将正常的输出结果写入此处，默认显示在终端屏幕上。
*   **标准错误 (stderr)**：用文件描述符 `2` 表示。程序将错误信息写入此处，默认也显示在终端屏幕上。

你可以将它们想象成一个工厂：`0`是原材料入口，`1`是合格产品出口，`2`是次品出口。默认情况下，产品（输出）和次品（错误）都堆放在同一个地方（屏幕）。重定向就是帮助我们管理这些“出口”的工具。

## 输出重定向

输出重定向允许我们将命令的输出结果从默认的屏幕，转移到其他地方，例如一个文件。

### 重定向标准输出

![](img/84c59784c225b2cea94aeebb574c0c8d_14.png)

使用 `>` 符号可以将命令的标准输出保存到文件中。如果文件不存在，则会创建它；如果文件已存在，则会**覆盖**其原有内容。

**命令格式**：
```bash
命令 > 文件名
```
由于标准输出 `1` 是默认的，所以 `>` 等价于 `1>`。

![](img/84c59784c225b2cea94aeebb574c0c8d_16.png)

**示例**：
```bash
# 将 ls 命令的正确结果保存到 output.txt 文件，屏幕上只显示错误信息
ls -l file1 file2 > output.txt
```

![](img/84c59784c225b2cea94aeebb574c0c8d_18.png)

### 追加标准输出

使用 `>>` 符号可以将命令的标准输出**追加**到文件的末尾，而不会覆盖原有内容。

![](img/84c59784c225b2cea94aeebb574c0c8d_20.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_22.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_24.png)

**命令格式**：
```bash
命令 >> 文件名
```

![](img/84c59784c225b2cea94aeebb574c0c8d_26.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_28.png)

**示例**：
```bash
# 将一行文本追加到 log.txt 文件
echo “New log entry” >> log.txt
```

![](img/84c59784c225b2cea94aeebb574c0c8d_30.png)

### 重定向标准错误

![](img/84c59784c225b2cea94aeebb574c0c8d_32.png)

使用 `2>` 符号可以将命令的错误输出保存到文件中。

![](img/84c59784c225b2cea94aeebb574c0c8d_34.png)

**命令格式**：
```bash
命令 2> 错误文件名
```

![](img/84c59784c225b2cea94aeebb574c0c8d_36.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_38.png)

**示例**：
```bash
# 执行一个可能出错的命令，将错误信息保存到 error.log
ls non_existent_file 2> error.log
```

![](img/84c59784c225b2cea94aeebb574c0c8d_40.png)

同样，使用 `2>>` 可以追加错误信息到文件。

![](img/84c59784c225b2cea94aeebb574c0c8d_42.png)

### 合并标准输出和标准错误

![](img/84c59784c225b2cea94aeebb574c0c8d_44.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_46.png)

有时我们希望将正确结果和错误信息都保存到同一个文件。

![](img/84c59784c225b2cea94aeebb574c0c8d_48.png)

以下是两种常用方法：

![](img/84c59784c225b2cea94aeebb574c0c8d_50.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_52.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_54.png)

1.  **使用 `&>` 符号（推荐）**：这是最简洁的写法。
    ```bash
    命令 &> 文件名
    ```
    **示例**：
    ```bash
    ls -l file1 file2 &> all_output.txt
    ```

![](img/84c59784c225b2cea94aeebb574c0c8d_55.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_57.png)

2.  **使用 `2>&1`**：这表示“将标准错误重定向到标准输出所在的地方”。
    ```bash
    命令 > 文件名 2>&1
    ```

![](img/84c59784c225b2cea94aeebb574c0c8d_59.png)

### 丢弃输出

![](img/84c59784c225b2cea94aeebb574c0c8d_61.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_62.png)

如果不想看到任何输出（无论是正确还是错误），可以将它们重定向到 `/dev/null` 这个特殊的“空设备”文件，它就像一个黑洞，所有写入它的数据都会消失。

**命令格式**：
```bash
命令 > /dev/null 2>&1
# 或更简洁的写法
命令 &> /dev/null
```

![](img/84c59784c225b2cea94aeebb574c0c8d_64.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_66.png)

**示例**：
```bash
# 查找文件，但不显示任何“权限不足”之类的错误信息
find /etc -name “*.conf” 2> /dev/null
```

![](img/84c59784c225b2cea94aeebb574c0c8d_68.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_70.png)

## 管道

![](img/84c59784c225b2cea94aeebb574c0c8d_72.png)

上一节我们学习了如何将输出重定向到文件，本节中我们来看看如何将一个命令的输出，直接作为另一个命令的输入，这就是**管道**。

![](img/84c59784c225b2cea94aeebb574c0c8d_74.png)

管道使用竖线符号 `|` 连接两个命令。

![](img/84c59784c225b2cea94aeebb574c0c8d_76.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_78.png)

**命令格式**：
```bash
命令A | 命令B
```
这表示将命令A的**标准输出**，传递给命令B作为其**标准输入**。

![](img/84c59784c225b2cea94aeebb574c0c8d_80.png)

以下是管道的一些常见用途：

![](img/84c59784c225b2cea94aeebb574c0c8d_82.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_83.png)

*   **过滤内容**：使用 `grep` 命令筛选出包含特定文本的行。
    ```bash
    # 在 /etc/passwd 文件中查找包含 “root” 的行
    cat /etc/passwd | grep root
    ```

*   **统计数量**：使用 `wc` 命令统计行数、单词数等。
    ```bash
    # 统计当前目录下文件和文件夹的总数
    ls | wc -l
    ```

![](img/84c59784c225b2cea94aeebb574c0c8d_85.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_86.png)

*   **分页查看**：使用 `less` 或 `more` 命令分页查看长输出。
    ```bash
    # 分页查看 /etc 目录下所有以 .conf 结尾的文件列表
    find /etc -name “*.conf” | less
    ```

## tee 命令：分流输出

![](img/84c59784c225b2cea94aeebb574c0c8d_88.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_90.png)

管道是单向的。`tee` 命令像一个“三通管”，它从标准输入读取数据，同时做到两件事：
1.  将数据写入一个或多个文件。
2.  将数据继续传递到标准输出，以便后续管道处理或显示在屏幕上。

![](img/84c59784c225b2cea94aeebb574c0c8d_92.png)

**命令格式**：
```bash
命令A | tee 文件名 | 命令B
```

![](img/84c59784c225b2cea94aeebb574c0c8d_94.png)

**示例**：
```bash
# 将 ls 的输出同时保存到 list.txt 文件，并传递给 wc 命令统计行数
ls -l /usr/bin | tee list.txt | wc -l
```
执行后，`list.txt` 文件保存了 `ls -l /usr/bin` 的完整结果，同时屏幕上会显示统计出的行数。

![](img/84c59784c225b2cea94aeebb574c0c8d_96.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_97.png)

`tee` 命令在需要同时查看输出和保存输出到日志文件的场景中非常有用。

## 总结

本节课中我们一起学习了Linux中至关重要的数据流管理工具：
*   **重定向**：我们掌握了如何用 `>`、`>>` 控制输出到文件，用 `2>` 处理错误信息，用 `&>` 合并输出，以及用 `/dev/null` 丢弃不需要的输出。
*   **管道**：我们学会了用 `|` 将命令串联起来，让数据在不同命令间高效流动，实现复杂的文本处理。
*   **tee命令**：我们了解了这个“分流器”，它能同时实现输出到文件和继续传递数据的功能。

![](img/84c59784c225b2cea94aeebb574c0c8d_99.png)

![](img/84c59784c225b2cea94aeebb574c0c8d_101.png)

熟练掌握重定向和管道，是高效使用Linux命令行、编写Shell脚本的基础。请务必通过实践练习来巩固这些概念。