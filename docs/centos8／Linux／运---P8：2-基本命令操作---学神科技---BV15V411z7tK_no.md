# CentOS8 操作系统从入门到精通：P2：基本命令操作 📝

![](img/b934b1d3f43841cb49aca14977accaff_1.png)

在本节课中，我们将学习 Linux 系统中最基本也是最重要的命令操作。掌握这些命令是熟练使用 Linux 系统的第一步。

![](img/b934b1d3f43841cb49aca14977accaff_3.png)

## 命令格式概述

![](img/b934b1d3f43841cb49aca14977accaff_5.png)

![](img/b934b1d3f43841cb49aca14977accaff_6.png)

![](img/b934b1d3f43841cb49aca14977accaff_8.png)

![](img/b934b1d3f43841cb49aca14977accaff_10.png)

Linux 命令的一般格式为：**命令 [选项] [参数]**。
*   **命令**：要执行的具体操作，例如 `ls`。
*   **选项**：用于调整命令行为的标志，通常以 `-` 或 `--` 开头，例如 `-l`。
*   **参数**：命令作用的对象，例如一个文件名或目录名。

![](img/b934b1d3f43841cb49aca14977accaff_12.png)

![](img/b934b1d3f43841cb49aca14977accaff_14.png)

![](img/b934b1d3f43841cb49aca14977accaff_15.png)

上一节我们介绍了命令的基本格式，本节中我们来看看几个最常用的命令及其选项。

![](img/b934b1d3f43841cb49aca14977accaff_17.png)

## `ls` 命令：列出目录内容

![](img/b934b1d3f43841cb49aca14977accaff_19.png)

![](img/b934b1d3f43841cb49aca14977accaff_21.png)

![](img/b934b1d3f43841cb49aca14977accaff_22.png)

`ls` 命令用于查看当前目录或指定目录下的文件和子目录。

![](img/b934b1d3f43841cb49aca14977accaff_24.png)

![](img/b934b1d3f43841cb49aca14977accaff_25.png)

### 基本用法

![](img/b934b1d3f43841cb49aca14977accaff_27.png)

执行 `ls` 命令，不加任何参数，可以查看当前目录下的内容。

![](img/b934b1d3f43841cb49aca14977accaff_29.png)

![](img/b934b1d3f43841cb49aca14977accaff_30.png)

```bash
ls
```

![](img/b934b1d3f43841cb49aca14977accaff_32.png)

如果想查看指定目录下的内容，可以在 `ls` 后加上目录路径。

![](img/b934b1d3f43841cb49aca14977accaff_34.png)

![](img/b934b1d3f43841cb49aca14977accaff_36.png)

```bash
ls /bin
```

### 常用选项

![](img/b934b1d3f43841cb49aca14977accaff_38.png)

以下是 `ls` 命令的一些常用选项，它们可以组合使用。

![](img/b934b1d3f43841cb49aca14977accaff_40.png)

![](img/b934b1d3f43841cb49aca14977accaff_42.png)

*   **`-l`**：以长格式列出文件和目录的详细信息。
    ```bash
    ls -l
    ```
    执行后，会显示类似以下的信息：
    ```
    drwxr-xr-x. 2 root root    6 Apr 11  2018 公共
    -rw-r--r--. 1 root root    0 Aug 13 10:00 文件.txt
    lrwxrwxrwx. 1 root root    7 Aug 13 10:01 link -> 文件.txt
    ```
    这些信息的含义从左到右依次是：
    1.  **文件类型与权限**：第一个字符表示文件类型，后续9个字符表示权限。
    2.  **硬链接数**。
    3.  **文件所有者**。
    4.  **文件所属组**。
    5.  **文件大小（字节）**。
    6.  **最后修改时间**。
    7.  **文件或目录名**。

![](img/b934b1d3f43841cb49aca14977accaff_44.png)

![](img/b934b1d3f43841cb49aca14977accaff_46.png)

*   **`-a`**：显示所有文件，包括以 `.` 开头的隐藏文件。在 Linux 中，以点开头的文件默认是隐藏的。
    ```bash
    ls -a
    ```

*   **`-d`**：仅显示目录本身的信息，而不是目录下的内容。常与 `-l` 连用查看目录的详细信息。
    ```bash
    ls -ld /etc
    ```

![](img/b934b1d3f43841cb49aca14977accaff_48.png)

*   **`-h`**：与 `-l` 一起使用时，以人类可读的格式（如 K, M, G）显示文件大小。
    ```bash
    ls -lh /boot
    ```

![](img/b934b1d3f43841cb49aca14977accaff_50.png)

*   **`-S`**：按文件大小排序，默认从大到小。常与 `-l` 和 `-h` 连用。
    ```bash
    ls -lSh
    ```

![](img/b934b1d3f43841cb49aca14977accaff_52.png)

### 文件类型与颜色

![](img/b934b1d3f43841cb49aca14977accaff_54.png)

在 `ls -l` 的输出中，第一个字符表示文件类型：
*   **`-`**：普通文件（黑色或白色）。
*   **`d`**：目录（浅蓝色）。
*   **`l`**：符号链接（软链接，浅蓝色或青色）。
*   **`b`**：块设备文件（黄色背景）。
*   **`c`**：字符设备文件（黄色背景）。

![](img/b934b1d3f43841cb49aca14977accaff_56.png)

在支持颜色的终端中，`ls` 命令会使用不同颜色区分文件类型，使结果更直观。

![](img/b934b1d3f43841cb49aca14977accaff_58.png)

## 命令别名

在 Linux 中，可以为常用的长命令设置一个简短的别名，提高效率。

![](img/b934b1d3f43841cb49aca14977accaff_60.png)

### 查看与设置别名

![](img/b934b1d3f43841cb49aca14977accaff_62.png)

`ll` 命令是 `ls -l` 的一个常见别名。可以使用 `type` 命令查看一个命令是否是别名。
```bash
type ll
```
输出会显示 `ll` 是 `ls -l` 的别名。

我们可以使用 `alias` 命令自定义别名。语法为：
```bash
alias 别名='原命令 [选项] [参数]'
```
例如，为打开网卡配置文件的命令设置别名：
```bash
alias mynet='vim /etc/sysconfig/network-scripts/ifcfg-ens33'
```
设置后，输入 `mynet` 即可执行那条长命令。

![](img/b934b1d3f43841cb49aca14977accaff_64.png)

![](img/b934b1d3f43841cb49aca14977accaff_66.png)

### 删除别名

![](img/b934b1d3f43841cb49aca14977accaff_68.png)

使用 `unalias` 命令可以删除已定义的别名。
```bash
unalias mynet
```

### 永久生效的别名

![](img/b934b1d3f43841cb49aca14977accaff_70.png)

![](img/b934b1d3f43841cb49aca14977accaff_72.png)

上面用 `alias` 设置的别名只在当前终端会话中有效。退出或重启后就会失效。若想永久生效，需要将别名定义写入配置文件。

![](img/b934b1d3f43841cb49aca14977accaff_74.png)

*   **对当前用户生效**：将别名命令添加到用户家目录下的 `~/.bashrc` 文件末尾。
*   **对所有用户生效**：将别名命令添加到 `/etc/bashrc` 文件末尾。

![](img/b934b1d3f43841cb49aca14977accaff_76.png)

编辑完配置文件后，需要执行 `source` 命令使其立即生效，或重新打开一个终端。
```bash
source ~/.bashrc
```

![](img/b934b1d3f43841cb49aca14977accaff_78.png)

![](img/b934b1d3f43841cb49aca14977accaff_80.png)

## `cd` 命令：切换工作目录

`cd` 命令用于切换当前工作目录。

![](img/b934b1d3f43841cb49aca14977accaff_82.png)

![](img/b934b1d3f43841cb49aca14977accaff_84.png)

### 基本用法

```bash
cd /path/to/directory
```

![](img/b934b1d3f43841cb49aca14977accaff_86.png)

### 特殊用法

![](img/b934b1d3f43841cb49aca14977accaff_88.png)

以下是 `cd` 命令的一些快捷用法：
*   **`cd` 或 `cd ~`**：切换到当前用户的家目录。
*   **`cd -`**：切换到上一次所在的目录。
*   **`cd ..`**：切换到上一级目录。
*   **`cd .`**：切换到当前目录（通常用于脚本中）。

![](img/b934b1d3f43841cb49aca14977accaff_90.png)

![](img/b934b1d3f43841cb49aca14977accaff_92.png)

![](img/b934b1d3f43841cb49aca14977accaff_94.png)

### `pwd` 命令

`pwd` 命令用于**打印当前工作目录**的完整路径。当你不确定自己身处何处时，可以使用它。
```bash
pwd
```

![](img/b934b1d3f43841cb49aca14977accaff_96.png)

![](img/b934b1d3f43841cb49aca14977accaff_98.png)

## `history` 命令：查看命令历史

![](img/b934b1d3f43841cb49aca14977accaff_100.png)

![](img/b934b1d3f43841cb49aca14977accaff_102.png)

![](img/b934b1d3f43841cb49aca14977accaff_104.png)

`history` 命令可以查看之前执行过的命令列表。

![](img/b934b1d3f43841cb49aca14977accaff_106.png)

### 基本用法与清除

直接输入 `history` 会列出所有历史命令。
```bash
history
```
使用 `-c` 选项可以**清除当前会话的历史记录**。
```bash
history -c
```
注意，这不会删除已保存到文件（`~/.bash_history`）中的历史记录。

![](img/b934b1d3f43841cb49aca14977accaff_108.png)

![](img/b934b1d3f43841cb49aca14977accaff_110.png)

![](img/b934b1d3f43841cb49aca14977accaff_112.png)

![](img/b934b1d3f43841cb49aca14977accaff_114.png)

### 调用历史命令的几种方法

![](img/b934b1d3f43841cb49aca14977accaff_116.png)

![](img/b934b1d3f43841cb49aca14977accaff_118.png)

高效地调用历史命令能极大提升操作速度，以下是几种常用方法：

![](img/b934b1d3f43841cb49aca14977accaff_120.png)

1.  **上下方向键**：在命令行中按上/下键可以逐条翻阅历史命令。
2.  **`Ctrl + R`**：进入反向搜索模式，输入关键字可以搜索历史命令，找到后按右方向键或回车即可执行。
3.  **`!n`**：执行历史记录中第 `n` 条命令。例如 `!48`。
4.  **`!!`**：执行上一条命令。
5.  **`!string`**：执行最近一条以 `string` 开头的命令。例如 `!vim`。

![](img/b934b1d3f43841cb49aca14977accaff_122.png)

![](img/b934b1d3f43841cb49aca14977accaff_124.png)

## 常用快捷键

![](img/b934b1d3f43841cb49aca14977accaff_126.png)

![](img/b934b1d3f43841cb49aca14977accaff_128.png)

除了命令，掌握一些终端快捷键也能让操作更流畅。

![](img/b934b1d3f43841cb49aca14977accaff_130.png)

![](img/b934b1d3f43841cb49aca14977accaff_132.png)

![](img/b934b1d3f43841cb49aca14977accaff_134.png)

*   **`Ctrl + C`**：终止当前正在前台运行的命令。
*   **`Ctrl + D`**：退出当前终端会话（相当于输入 `exit`）。
*   **`Ctrl + L`**：清屏（相当于输入 `clear` 命令）。
*   **`Ctrl + A`**：将光标移动到行首。
*   **`Ctrl + E`**：将光标移动到行尾。
*   **`Ctrl + U`**：删除从光标处到行首的所有字符。
*   **`Ctrl + K`**：删除从光标处到行尾的所有字符。
*   **`Tab`**：命令或路径补全。输入部分字符后按 `Tab` 键，系统会自动补全命令或文件路径。
*   **`!$`**：引用上一条命令的最后一个参数。例如，执行 `cat /etc/hosts` 后，再执行 `vim !$` 就相当于执行 `vim /etc/hosts`。

![](img/b934b1d3f43841cb49aca14977accaff_136.png)

![](img/b934b1d3f43841cb49aca14977accaff_138.png)

---

![](img/b934b1d3f43841cb49aca14977accaff_140.png)

![](img/b934b1d3f43841cb49aca14977accaff_142.png)

![](img/b934b1d3f43841cb49aca14977accaff_144.png)

本节课中我们一起学习了 Linux 的基本命令操作，包括如何使用 `ls` 查看文件、使用 `cd` 切换目录、使用 `history` 管理命令历史，以及如何设置别名和使用快捷键来提高工作效率。这些是构建 Linux 知识体系最坚实的基础，请务必多加练习，熟练掌握。