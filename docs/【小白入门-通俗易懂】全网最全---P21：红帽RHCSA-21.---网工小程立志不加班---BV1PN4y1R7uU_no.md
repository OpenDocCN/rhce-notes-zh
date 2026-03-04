# Linux运维教程：P21：find文件查找 🔍

在本节课中，我们将要学习Linux系统中一个非常强大的文件查找命令——`find`。`find`命令可以根据多种条件（如文件名、类型、大小、时间等）在指定目录及其子目录中递归地搜索文件，并支持对搜索结果进行进一步处理。掌握`find`命令对于系统管理和日常运维至关重要。

## 命令格式与基本查找

`find`命令的基本格式是：`find [路径] [查找条件]`。你需要先指定一个查找的起始路径，然后给出查找条件。

![](img/cd20e923208ec85df669f8fabbc30223_1.png)

![](img/cd20e923208ec85df669f8fabbc30223_3.png)

![](img/cd20e923208ec85df669f8fabbc30223_5.png)

以下是几种最常用的查找条件：

*   **按类型查找 (`-type`)**
    这个条件用于指定你要查找的目标是文件、目录还是链接。
    *   `f` 代表普通文件。
    *   `d` 代表目录。
    *   `l` 代表符号链接文件。

    例如，查找 `/var/log` 目录下的所有文件：
    ```bash
    find /var/log -type f
    ```
    这条命令会递归地搜索 `/var/log` 目录及其所有子目录，并列出所有找到的文件。

*   **按名称查找 (`-name`)**
    这个条件用于按文件名进行精确或模糊查找。可以使用通配符 `*` 进行匹配，通常建议用引号将模式括起来。
    例如，查找 `/etc` 目录下所有以 `.conf` 结尾的文件：
    ```bash
    find /etc -name “*.conf”
    ```
    `find` 的查找是递归的，它会搜索 `/etc` 下的所有层级。这与 `ls /etc/*.conf` 只在 `/etc` 当前目录下查找有本质区别。

*   **忽略大小写按名称查找 (`-iname`)**
    当你不确定文件名的大小写时，可以使用 `-iname` 条件，它会忽略大小写进行匹配。这个选项使用频率相对较低。

![](img/cd20e923208ec85df669f8fabbc30223_7.png)

![](img/cd20e923208ec85df669f8fabbc30223_9.png)

## 进阶查找条件

上一节我们介绍了按类型和名称查找，本节中我们来看看更复杂的查找条件，例如按文件大小、时间，以及如何组合多个条件。

*   **按文件大小查找 (`-size`)**
    这个条件用于查找特定大小的文件。单位可以是 `k` (KB), `M` (MB), `G` (GB)。`+` 号表示“大于”，`-` 号表示“小于”。
    例如，查找 `/var/log` 目录下大于 10KB 的文件：
    ```bash
    find /var/log -size +10k -type f
    ```
    查找小于 10KB 的文件：
    ```bash
    find /var/log -size -10k -type f
    ```

*   **组合条件查找 (`-a`, `-o`)**
    `find` 命令允许使用逻辑运算符组合多个条件。
    *   `-a` 代表“并且” (and)，所有条件必须同时满足。
    *   `-o` 代表“或者” (or)，满足任意一个条件即可。

    例如，查找大小在 10KB 到 20KB 之间的文件（包含边界）：
    ```bash
    find /var/log -size +10k -a -size -20k -type f
    ```
    例如，查找文件名以 `.log` 结尾 **或者** 大小小于 10KB 的文件：
    ```bash
    find /var/log -name “*.log” -o -size -10k -type f
    ```

![](img/cd20e923208ec85df669f8fabbc30223_11.png)

![](img/cd20e923208ec85df669f8fabbc30223_13.png)

![](img/cd20e923208ec85df669f8fabbc30223_14.png)

*   **按用户查找 (`-user`)**
    这个条件用于查找属于特定用户的文件。
    例如，查找系统根目录下属于用户 `laowang` 的所有文件：
    ```bash
    find / -user laowang
    ```

![](img/cd20e923208ec85df669f8fabbc30223_16.png)

![](img/cd20e923208ec85df669f8fabbc30223_18.png)

![](img/cd20e923208ec85df669f8fabbc30223_20.png)

![](img/cd20e923208ec85df669f8fabbc30223_22.png)

![](img/cd20e923208ec85df669f8fabbc30223_24.png)

*   **按修改时间查找 (`-mtime`)**
    这个条件用于查找在指定天数内被修改过的文件。`+n` 表示 `n` 天之前，`-n` 表示 `n` 天之内。
    例如，查找 `/var/log` 目录下 10 天之内被修改过的文件：
    ```bash
    find /var/log -mtime -10 -type f
    ```
    查找 10 天之前被修改过的文件：
    ```bash
    find /var/log -mtime +10 -type f
    ```
    **提示**：`find` 命令功能非常丰富，还可以按分钟查找（`-mmin`）。如需更多高级用法，可以查阅在线文档或使用 `man find` 命令查看手册。

![](img/cd20e923208ec85df669f8fabbc30223_26.png)

![](img/cd20e923208ec85df669f8fabbc30223_28.png)

![](img/cd20e923208ec85df669f8fabbc30223_30.png)

![](img/cd20e923208ec85df669f8fabbc30223_32.png)

## 对查找结果进行操作

![](img/cd20e923208ec85df669f8fabbc30223_33.png)

![](img/cd20e923208ec85df669f8fabbc30223_35.png)

![](img/cd20e923208ec85df669f8fabbc30223_37.png)

`find` 命令查找到文件后，我们经常需要对它们进行一些操作，例如复制、移动或删除。`find` 命令对管道符 (`|`) 的支持并不友好，但它提供了自己的处理机制——`-exec` 选项。

`find` 的完整命令格式包含了对结果的操作部分：`find [路径] [条件] -exec [命令] {} \;`

*   `-exec`：表示开始执行额外的命令。
*   `{}`：这是一个占位符，代表 `find` 命令找到的每一个文件路径。
*   `\;`：表示 `-exec` 额外命令的结束，反斜杠 `\` 用于转义分号。

![](img/cd20e923208ec85df669f8fabbc30223_39.png)

例如，将 `/var/log` 目录下所有以 `.log` 结尾的文件备份到 `/opt/backup` 目录：
```bash
find /var/log -name “*.log” -type f -exec cp {} /opt/backup \;
```
这条命令会依次对每个找到的文件执行 `cp 文件路径 /opt/backup`。

![](img/cd20e923208ec85df669f8fabbc30223_41.png)

例如，删除 `/tmp` 目录下所有以 `.tmp` 结尾的文件：
```bash
find /tmp -name “*.tmp” -type f -exec rm {} \;
```
**警告**：删除操作务必谨慎，确保条件准确无误。

**高级技巧：清空文件内容**
有时我们需要清空一批文件的内容（例如日志文件），而不是删除文件本身。可以结合 Linux 的“黑洞”设备 `/dev/null` 来实现。向这个设备写入任何数据都会消失。
例如，清空 `/var/log` 目录下所有超过 10KB 的日志文件内容：
```bash
find /var/log -size +10k -type f -exec cp /dev/null {} \;
```
这条命令用空设备 `/dev/null` 的内容（即空）覆盖每一个找到的文件，从而达到清空内容的目的。

![](img/cd20e923208ec85df669f8fabbc30223_43.png)

---

![](img/cd20e923208ec85df669f8fabbc30223_45.png)

![](img/cd20e923208ec85df669f8fabbc30223_47.png)

![](img/cd20e923208ec85df669f8fabbc30223_49.png)

本节课中我们一起学习了 `find` 文件查找命令。我们掌握了其基本格式，学习了按类型 (`-type`)、名称 (`-name`, `-iname`)、大小 (`-size`)、用户 (`-user`) 和时间 (`-mtime`) 进行查找的方法，以及如何使用逻辑运算符 (`-a`, `-o`) 组合条件。最后，我们重点讲解了如何使用 `-exec` 选项对查找结果执行复制、删除等操作，并介绍了一个清空文件内容的实用技巧。`find` 命令是 Linux 运维中的利器，需要多加练习才能熟练掌握。