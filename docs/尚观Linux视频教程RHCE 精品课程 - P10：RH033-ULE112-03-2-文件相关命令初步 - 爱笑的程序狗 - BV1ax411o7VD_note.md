# Linux基础入门：第3章：文件与目录操作初步 📂

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_1.png)

在本节课中，我们将要学习Linux系统中最基础也是最重要的部分：文件和目录的操作。我们将从查看文件开始，逐步学习如何创建、移动、复制、删除文件，以及如何在目录树中穿梭。这些命令是使用Linux系统的基石，掌握它们将为后续学习打下坚实的基础。

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_3.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_5.png)

---

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_7.png)

## 文件操作命令 📄

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_9.png)

上一节我们介绍了Linux系统的基本框架和使用习惯，本节中我们来看看如何对文件进行实际操作。

### 查看当前目录与文件

首先，我们需要知道自己在文件系统的哪个位置，以及当前目录下有哪些文件。

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_11.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_12.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_14.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_16.png)

*   **`pwd`**：显示当前所在的绝对路径。
    ```bash
    pwd
    ```
*   **`ls`**：列出当前目录下的文件和子目录。
    ```bash
    ls
    ```
*   **`ls -l`**：以长格式列出详细信息，包括文件权限、所有者、大小和修改时间。
    ```bash
    ls -l
    ```
*   **`ls -a`**：列出所有文件，包括以点`.`开头的隐藏文件。
    ```bash
    ls -a
    ```
*   **`ls -F`**：在条目后附加类型标识符（目录加`/`，可执行文件加`*`，符号链接加`@`）。这在没有颜色区分时非常有用。
    ```bash
    ls -F
    ```
    注意：通常我们使用的`ls`命令是`ls --color=auto`的别名，所以默认有颜色。`-F`参数可以作为一种备用方案。

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_18.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_19.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_21.png)

### 创建与更新文件

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_23.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_24.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_25.png)

以下是创建新文件或更新现有文件时间戳的命令。

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_27.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_28.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_29.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_31.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_33.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_35.png)

*   **`touch`**：如果文件不存在，则创建一个新的空文件；如果文件已存在，则更新该文件的三个时间戳（访问时间、修改时间、属性更改时间）。
    ```bash
    touch filename
    ```
    使用`stat`命令可以查看文件的详细时间信息。
    ```bash
    stat filename
    ```

### 移动、复制与删除文件

对文件进行位置变更或复制删除是常见操作。

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_37.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_39.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_41.png)

*   **`mv`**：移动文件或目录，也可用于重命名。
    ```bash
    mv source_file destination_file      # 重命名
    mv source_file /path/to/directory/   # 移动到目录
    mv source_file /path/to/directory/new_name # 移动并重命名
    ```
*   **`cp`**：复制文件或目录。
    ```bash
    cp source_file destination_file      # 复制文件
    cp -r source_directory /path/to/destination/ # 复制整个目录，需要`-r`参数
    ```
*   **`rm`**：删除文件或目录。
    ```bash
    rm filename          # 删除文件（默认可能是`rm -i`，会询问确认）
    rm -f filename       # 强制删除，不询问
    rm -r directory_name # 递归删除目录及其内容
    rm -rf directory_name # 强制递归删除目录，不进行任何确认
    ```
    注意：`rmdir`命令只能删除空目录，不如`rm -r`实用。

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_43.png)

---

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_45.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_46.png)

## 目录操作与路径导航 🌳

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_48.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_50.png)

理解了文件操作后，我们来看看如何在复杂的目录树结构中移动。Linux文件系统是一个树形结构，从根目录`/`开始分支。

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_52.png)

### 理解绝对路径与相对路径

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_54.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_55.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_56.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_58.png)

*   **绝对路径**：从根目录`/`开始的完整路径，如`/usr/bin/ls`。
*   **相对路径**：相对于当前目录的路径。
    *   `.` 代表当前目录。
    *   `..` 代表上一级目录（父目录）。
    *   `~` 代表当前用户的主目录。
    *   `~username` 代表指定用户的主目录。

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_60.png)

### 切换目录命令

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_62.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_63.png)

以下是用于在目录间导航的命令。

*   **`cd`**：切换当前工作目录。
    ```bash
    cd /path/to/directory  # 使用绝对路径
    cd ./subdir            # 使用相对路径，进入当前目录下的subdir
    cd ..                  # 返回上一级目录
    cd ~                   # 切换到当前用户的主目录
    cd -                   # 切换到上一个所在的目录
    ```
    通过组合`..`，可以在目录树中灵活移动，例如`cd ../../bin`表示向上退两级，再进入`bin`目录。

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_65.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_67.png)

---

## 其他基础命令 🔧

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_69.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_70.png)

除了文件和目录操作，还有一些基础命令在日常使用中必不可少。

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_72.png)

### 文本查看与编辑

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_74.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_75.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_77.png)

以下是查看和编辑文本文件的命令。

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_79.png)

*   **`echo`**：在终端输出文本或变量的值。`$`符号用于获取变量值。
    ```bash
    echo "Hello World"
    echo $USER  # 输出当前用户名
    ```
*   **`cat`**：连接文件并打印到标准输出，常用于快速查看文件内容。
    ```bash
    cat filename
    ```
*   **`more`** 和 **`less`**：分页查看长文件内容。`more`只能向下翻页，`less`功能更强大，可以上下翻页和搜索。
    ```bash
    more filename
    less filename
    ```
    在`less`中，按`/`键后输入内容可向下搜索，按`n`查找下一个，按`q`退出。
*   **`vi`** 或 **`vim`**：强大的文本编辑器。初学者需掌握几个基本模式：
    *   启动后是**命令模式**，按 `i` 进入**插入模式**进行编辑。
    *   按 `Esc` 键从插入模式返回命令模式。
    *   在命令模式下，按 `u` 撤销操作，`Ctrl+r` 重做。
    *   在命令模式下，输入 `:` 进入**底行命令模式**。
        *   `:wq` 保存并退出。
        *   `:q!` 不保存强制退出。
        *   `:w` 保存。

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_81.png)

### 命令别名管理

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_83.png)

可以为常用命令创建简短的别名，提高效率。

*   **`alias`**：查看或设置命令别名。
    ```bash
    alias                  # 查看所有别名
    alias ll='ls -l'       # 设置别名，使`ll`等效于`ls -l`
    unalias ll             # 取消别名
    ```
    要使别名永久生效，需要将`alias`命令添加到用户主目录的`~/.bashrc`配置文件中。

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_85.png)

### 用户账号管理

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_87.png)

以下是基本的用户管理命令，通常需要管理员权限。

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_89.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_91.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_92.png)

*   **`useradd`**：添加新用户账号。
    ```bash
    useradd newusername
    ```
*   **`passwd`**：更改用户密码。`root`用户可以为任何用户改密码且可忽略复杂性规则，普通用户修改自己密码时需符合复杂性要求（如包含大小写字母和数字）。
    ```bash
    passwd                 # 更改当前用户自己的密码
    passwd username        # root用户更改指定用户的密码
    ```
*   **`su`**：切换用户身份。`su - username`会完全切换到目标用户的环境。
    ```bash
    su - username          # 切换到指定用户
    exit                   # 退出当前用户，返回之前的用户
    ```

---

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_94.png)

## 总结 📝

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_96.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_98.png)

![](img/a97b1ecff5bf6eec048a08a2d75d65d0_99.png)

本节课中我们一起学习了Linux中最核心的文件与目录操作。我们掌握了如何使用`ls`查看文件、用`touch`创建文件、用`mv`和`cp`移动复制文件、用`rm`删除文件。我们也学会了通过`cd`命令和`pwd`命令在目录树中导航，理解了绝对路径和相对路径的区别。此外，我们还接触了文本查看命令`cat`、`more`、`less`，编辑器`vi`的基本用法，以及管理别名和用户账号的初步命令。这些是构建Linux知识体系的第一块积木，请务必多加练习，熟悉它们。