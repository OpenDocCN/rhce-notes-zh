# Linux入门：P6：文件系统组成和基本操作 📁

![](img/5b029923e35f16f02cc275e3afd34c67_0.png)

![](img/5b029923e35f16f02cc275e3afd34c67_2.png)

![](img/5b029923e35f16f02cc275e3afd34c67_4.png)

![](img/5b029923e35f16f02cc275e3afd34c67_5.png)

## 概述
在本节课中，我们将学习Linux文件系统的基本组成结构，并掌握对文件和目录进行复制、移动、删除、创建等基本操作的命令。理解文件系统的组织方式是熟练使用Linux的基础。

![](img/5b029923e35f16f02cc275e3afd34c67_7.png)

![](img/5b029923e35f16f02cc275e3afd34c67_9.png)

![](img/5b029923e35f16f02cc275e3afd34c67_11.png)

---

## 文件系统组成结构

![](img/5b029923e35f16f02cc275e3afd34c67_13.png)

上一节我们介绍了命令的基本语法和帮助系统，本节中我们来看看Linux文件系统的核心组成。

![](img/5b029923e35f16f02cc275e3afd34c67_15.png)

Linux文件系统采用**单根倒树状结构**，一切皆从根目录（`/`）开始。这与Windows的多根（如C盘、D盘）结构不同。

**核心概念**：
*   **根目录**：`/`
*   **家目录**：普通用户在 `/home/用户名`，root用户在 `/root`
*   **路径分隔符**：`/`

### 重要系统目录简介
以下是Linux系统中一些关键目录及其作用：

*   **`/boot`**：存放系统启动时所需的文件。
*   **`/etc`**：存放系统配置文件。
*   **`/home`**：普通用户的家目录所在地。
*   **`/root`**：超级管理员root的家目录。
*   **`/tmp`**：存放系统临时文件。
*   **`/usr`**：存放用户应用程序和文件。
    *   `/usr/bin`：用户命令目录。
    *   `/usr/sbin`：系统管理员命令目录。
*   **`/var`**：存放经常变化的文件，如日志、网页文件。
*   **`/dev`**：存放设备文件。
*   **`/proc` 与 `/sys`**：虚拟文件系统，反映系统内核和进程的实时信息，数据存放在内存中。

![](img/5b029923e35f16f02cc275e3afd34c67_17.png)

![](img/5b029923e35f16f02cc275e3afd34c67_19.png)

![](img/5b029923e35f16f02cc275e3afd34c67_21.png)

![](img/5b029923e35f16f02cc275e3afd34c67_23.png)

![](img/5b029923e35f16f02cc275e3afd34c67_24.png)

![](img/5b029923e35f16f02cc275e3afd34c67_26.png)

### 绝对路径与相对路径
路径用于定位文件或目录，分为两种：

![](img/5b029923e35f16f02cc275e3afd34c67_28.png)

![](img/5b029923e35f16f02cc275e3afd34c67_30.png)

![](img/5b029923e35f16f02cc275e3afd34c67_32.png)

![](img/5b029923e35f16f02cc275e3afd34c67_34.png)

![](img/5b029923e35f16f02cc275e3afd34c67_36.png)

*   **绝对路径**：从根目录 `/` 开始的完整路径。在任何位置都可以使用。
    *   例如：`/etc/passwd`
*   **相对路径**：相对于当前工作目录的路径。不以 `/` 开头。
    *   例如：当前在 `/home`，`user1/file.txt` 指的是 `/home/user1/file.txt`
    *   特殊符号：
        *   `.` 代表当前目录。
        *   `..` 代表上一级目录。
        *   `~` 代表当前用户的家目录。
        *   `~用户名` 代表指定用户的家目录。

![](img/5b029923e35f16f02cc275e3afd34c67_38.png)

![](img/5b029923e35f16f02cc275e3afd34c67_40.png)

![](img/5b029923e35f16f02cc275e3afd34c67_42.png)

![](img/5b029923e35f16f02cc275e3afd34c67_44.png)

**命令示例**：
```bash
pwd                     # 显示当前工作目录
cd /etc                 # 使用绝对路径切换到/etc目录
cd sysconfig            # 使用相对路径切换到当前目录下的sysconfig目录
cd ..                   # 返回上一级目录
cd ~                    # 返回当前用户的家目录
cd -                    # 返回上一个工作目录
```

![](img/5b029923e35f16f02cc275e3afd34c67_46.png)

![](img/5b029923e35f16f02cc275e3afd34c67_48.png)

![](img/5b029923e35f16f02cc275e3afd34c67_50.png)

---

![](img/5b029923e35f16f02cc275e3afd34c67_52.png)

![](img/5b029923e35f16f02cc275e3afd34c67_54.png)

![](img/5b029923e35f16f02cc275e3afd34c67_56.png)

![](img/5b029923e35f16f02cc275e3afd34c67_58.png)

![](img/5b029923e35f16f02cc275e3afd34c67_60.png)

![](img/5b029923e35f16f02cc275e3afd34c67_62.png)

![](img/5b029923e35f16f02cc275e3afd34c67_64.png)

![](img/5b029923e35f16f02cc275e3afd34c67_65.png)

![](img/5b029923e35f16f02cc275e3afd34c67_67.png)

![](img/5b029923e35f16f02cc275e3afd34c67_68.png)

## 文件与目录的基本操作

![](img/5b029923e35f16f02cc275e3afd34c67_70.png)

![](img/5b029923e35f16f02cc275e3afd34c67_72.png)

理解了文件系统的结构后，我们就可以开始学习如何操作其中的文件和目录了。

![](img/5b029923e35f16f02cc275e3afd34c67_74.png)

![](img/5b029923e35f16f02cc275e3afd34c67_76.png)

### 1. 查看目录内容 (ls)
`ls` 命令用于列出目录内容。

![](img/5b029923e35f16f02cc275e3afd34c67_78.png)

![](img/5b029923e35f16f02cc275e3afd34c67_80.png)

以下是 `ls` 命令的一些常用选项：

*   `-l`：以长格式显示详细信息。
*   `-a`：显示所有文件，包括隐藏文件（以 `.` 开头的文件）。
*   `-R`：递归显示子目录内容。
*   `-d`：仅显示目录本身的信息，而不是其内容。

![](img/5b029923e35f16f02cc275e3afd34c67_82.png)

![](img/5b029923e35f16f02cc275e3afd34c67_84.png)

**长格式输出详解** (`ls -l`)：
```
-rw-r--r--. 1 root root 0 Apr 12 11:36 file1
```
*   第1字符：文件类型 (`-`普通文件，`d`目录，`l`链接文件，`b`块设备，`c`字符设备)。
*   第2-10字符：文件权限。
*   数字：对于文件是链接数；对于目录是其子目录数（含`.`和`..`）。
*   第1个`root`：文件拥有者。
*   第2个`root`：文件所属组。
*   `0`：文件大小（字节）。
*   `Apr 12 11:36`：文件最后修改时间（mtime）。
*   `file1`：文件名。

![](img/5b029923e35f16f02cc275e3afd34c67_86.png)

![](img/5b029923e35f16f02cc275e3afd34c67_88.png)

![](img/5b029923e35f16f02cc275e3afd34c67_90.png)

![](img/5b029923e35f16f02cc275e3afd34c67_92.png)

![](img/5b029923e35f16f02cc275e3afd34c67_94.png)

### 2. 创建文件与目录
*   **创建空文件**：`touch` 命令。
    ```bash
    touch newfile.txt
    ```
*   **创建目录**：`mkdir` 命令。
    ```bash
    mkdir newdir              # 创建单个目录
    mkdir -p dir1/dir2/dir3   # 递归创建多级目录
    ```

![](img/5b029923e35f16f02cc275e3afd34c67_96.png)

![](img/5b029923e35f16f02cc275e3afd34c67_98.png)

![](img/5b029923e35f16f02cc275e3afd34c67_100.png)

![](img/5b029923e35f16f02cc275e3afd34c67_102.png)

![](img/5b029923e35f16f02cc275e3afd34c67_103.png)

### 3. 复制文件与目录 (cp)
`cp` 命令用于复制文件或目录。

![](img/5b029923e35f16f02cc275e3afd34c67_105.png)

![](img/5b029923e35f16f02cc275e3afd34c67_107.png)

![](img/5b029923e35f16f02cc275e3afd34c67_109.png)

**基本语法**：`cp [选项] 源文件... 目标路径`

![](img/5b029923e35f16f02cc275e3afd34c67_111.png)

![](img/5b029923e35f16f02cc275e3afd34c67_113.png)

![](img/5b029923e35f16f02cc275e3afd34c67_115.png)

![](img/5b029923e35f16f02cc275e3afd34c67_117.png)

![](img/5b029923e35f16f02cc275e3afd34c67_119.png)

**常用选项**：
*   `-r` 或 `-R`：递归复制目录。
*   `-p`：保留源文件的属性（权限、时间戳等）。
*   `-a`：等同于 `-dR --preserve=all`，常用于复制目录并保留所有属性。

![](img/5b029923e35f16f02cc275e3afd34c67_121.png)

![](img/5b029923e35f16f02cc275e3afd34c67_123.png)

![](img/5b029923e35f16f02cc275e3afd34c67_125.png)

![](img/5b029923e35f16f02cc275e3afd34c67_127.png)

**目标路径的三种情况**：
1.  **目标是目录**：文件被复制到该目录下。
2.  **目标是已存在的文件**：源文件内容会覆盖目标文件。
3.  **目标是不存在的文件**：复制并重命名为目标文件名。

![](img/5b029923e35f16f02cc275e3afd34c67_129.png)

![](img/5b029923e35f16f02cc275e3afd34c67_131.png)

![](img/5b029923e35f16f02cc275e3afd34c67_133.png)

**命令示例**：
```bash
cp file1 /tmp/              # 复制file1到/tmp目录下
cp -r /etc /backup/         # 递归复制整个/etc目录到/backup下
cp -p file1 file1_backup    # 复制file1并保留属性
```

![](img/5b029923e35f16f02cc275e3afd34c67_135.png)

![](img/5b029923e35f16f02cc275e3afd34c67_137.png)

![](img/5b029923e35f16f02cc275e3afd34c67_139.png)

![](img/5b029923e35f16f02cc275e3afd34c67_140.png)

![](img/5b029923e35f16f02cc275e3afd34c67_141.png)

### 4. 移动与重命名文件与目录 (mv)
`mv` 命令用于移动或重命名文件/目录。

**基本语法**：`mv [选项] 源文件... 目标路径`

![](img/5b029923e35f16f02cc275e3afd34c67_143.png)

![](img/5b029923e35f16f02cc275e3afd34c67_145.png)

![](img/5b029923e35f16f02cc275e3afd34c67_147.png)

![](img/5b029923e35f16f02cc275e3afd34c67_149.png)

**目标路径的三种情况**（与`cp`类似，但源文件会消失）：
1.  **目标是目录**：移动到该目录。
2.  **目标是已存在的文件**：**移动并覆盖**目标文件。
3.  **目标是不存在的文件**：**移动并重命名**。

**命令示例**：
```bash
mv file1 /tmp/          # 移动file1到/tmp
mv oldname newname      # 将当前目录下的oldname重命名为newname
mv dir1 /home/user/     # 移动dir1目录到/home/user/下
```

![](img/5b029923e35f16f02cc275e3afd34c67_151.png)

![](img/5b029923e35f16f02cc275e3afd34c67_153.png)

![](img/5b029923e35f16f02cc275e3afd34c67_155.png)

![](img/5b029923e35f16f02cc275e3afd34c67_157.png)

### 5. 删除文件与目录 (rm)
`rm` 命令用于删除文件或目录。**请谨慎使用，尤其是`-rf`组合**。

![](img/5b029923e35f16f02cc275e3afd34c67_159.png)

![](img/5b029923e35f16f02cc275e3afd34c67_161.png)

![](img/5b029923e35f16f02cc275e3afd34c67_163.png)

**基本语法**：`rm [选项] 文件...`

![](img/5b029923e35f16f02cc275e3afd34c67_165.png)

![](img/5b029923e35f16f02cc275e3afd34c67_167.png)

![](img/5b029923e35f16f02cc275e3afd34c67_169.png)

![](img/5b029923e35f16f02cc275e3afd34c67_171.png)

**常用选项**：
*   `-r` 或 `-R`：递归删除目录及其内容。
*   `-f`：强制删除，不提示确认。
*   `-i`：交互式删除，删除前提示确认（系统通常为`rm`设置了别名 `rm -i`）。

**警告**：`rm -rf /` 命令会尝试删除根目录下的所有文件，导致系统毁灭性损坏，切勿尝试！

![](img/5b029923e35f16f02cc275e3afd34c67_173.png)

![](img/5b029923e35f16f02cc275e3afd34c67_175.png)

![](img/5b029923e35f16f02cc275e3afd34c67_177.png)

![](img/5b029923e35f16f02cc275e3afd34c67_179.png)

![](img/5b029923e35f16f02cc275e3afd34c67_181.png)

**命令示例**：
```bash
rm file1                # 删除文件（有提示）
rm -f file1             # 强制删除文件（无提示）
rm -r dir1              # 递归删除目录（有提示）
rm -rf dir1             # 强制递归删除目录（无提示）
```

![](img/5b029923e35f16f02cc275e3afd34c67_183.png)

![](img/5b029923e35f16f02cc275e3afd34c67_185.png)

![](img/5b029923e35f16f02cc275e3afd34c67_187.png)

![](img/5b029923e35f16f02cc275e3afd34c67_189.png)

### 6. 查看文件类型 (file)
在Linux中，文件后缀名不代表其真实类型。`file` 命令用于检测文件的实际类型。

![](img/5b029923e35f16f02cc275e3afd34c67_191.png)

![](img/5b029923e35f16f02cc275e3afd34c67_193.png)

![](img/5b029923e35f16f02cc275e3afd34c67_195.png)

![](img/5b029923e35f16f02cc275e3afd34c67_197.png)

**命令示例**：
```bash
file /etc/passwd        # 显示为 ASCII text
file /bin/ls            # 显示为 ELF 可执行文件
file a_document.pdf     # 如果可以，会显示 PDF document
```

![](img/5b029923e35f16f02cc275e3afd34c67_199.png)

![](img/5b029923e35f16f02cc275e3afd34c67_201.png)

---

![](img/5b029923e35f16f02cc275e3afd34c67_203.png)

![](img/5b029923e35f16f02cc275e3afd34c67_205.png)

## 文件的时间戳

每个文件都有三个重要的时间戳，可以通过 `stat` 命令查看：

![](img/5b029923e35f16f02cc275e3afd34c67_207.png)

![](img/5b029923e35f16f02cc275e3afd34c67_209.png)

![](img/5b029923e35f16f02cc275e3afd34c67_210.png)

![](img/5b029923e35f16f02cc275e3afd34c67_212.png)

![](img/5b029923e35f16f02cc275e3afd34c67_214.png)

```bash
stat filename
```
输出中包含：
*   **Access Time (atime)**：文件最后一次被**访问**的时间（如读取内容）。
*   **Modify Time (mtime)**：文件内容最后一次被**修改**的时间。
*   **Change Time (ctime)**：文件**状态**最后一次改变的时间（如权限、拥有者改变）。

![](img/5b029923e35f16f02cc275e3afd34c67_216.png)

![](img/5b029923e35f16f02cc275e3afd34c67_218.png)

**`touch` 命令的另一个作用**：更新文件的时间戳为当前时间。
```bash
touch existing_file
```

![](img/5b029923e35f16f02cc275e3afd34c67_220.png)

![](img/5b029923e35f16f02cc275e3afd34c67_221.png)

![](img/5b029923e35f16f02cc275e3afd34c67_223.png)

![](img/5b029923e35f16f02cc275e3afd34c67_225.png)

![](img/5b029923e35f16f02cc275e3afd34c67_227.png)

![](img/5b029923e35f16f02cc275e3afd34c67_229.png)

![](img/5b029923e35f16f02cc275e3afd34c67_231.png)

![](img/5b029923e35f16f02cc275e3afd34c67_233.png)

---

![](img/5b029923e35f16f02cc275e3afd34c67_235.png)

![](img/5b029923e35f16f02cc275e3afd34c67_237.png)

## 总结
本节课中我们一起学习了Linux文件系统的核心知识：
1.  **结构**：理解了单根倒树状的目录结构，以及绝对路径与相对路径的区别。
2.  **导航**：掌握了使用 `cd`、`pwd` 在目录间切换和定位。
3.  **查看**：学会了用 `ls` 及其选项查看目录和文件的详细信息。
4.  **操作**：熟练运用 `mkdir`、`touch`、`cp`、`mv`、`rm` 等命令进行创建、复制、移动、重命名和删除操作。
5.  **洞察**：了解了文件时间戳（atime， mtime， ctime）的概念，以及 `file` 命令的用途。

![](img/5b029923e35f16f02cc275e3afd34c67_239.png)

![](img/5b029923e35f16f02cc275e3afd34c67_241.png)

![](img/5b029923e35f16f02cc275e3afd34c67_243.png)

这些是管理Linux系统文件和目录的基础，务必通过实践加深理解。下一节我们将学习用户、组和权限管理。