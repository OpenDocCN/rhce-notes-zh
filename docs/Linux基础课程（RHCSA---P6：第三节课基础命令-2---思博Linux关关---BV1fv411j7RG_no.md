# Linux基础课程（RHCSA）：P6：第三节课基础命令-2 📚

![](img/dcf185667a0d9a6a273e538c9da25bdd_1.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_3.png)

在本节课中，我们将继续学习Linux的基础命令，包括文件查看、移动/重命名、删除以及路径和通配符的使用。这些命令是日常操作中不可或缺的工具，掌握它们能让你更高效地管理Linux系统。

![](img/dcf185667a0d9a6a273e538c9da25bdd_5.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_6.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_7.png)

---

![](img/dcf185667a0d9a6a273e538c9da25bdd_9.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_10.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_11.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_13.png)

## 回顾与补充 📝

![](img/dcf185667a0d9a6a273e538c9da25bdd_15.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_16.png)

上一节我们介绍了`vi`编辑器和一些基础命令。关于`vi`的练习，答案并非唯一，关键在于灵活组合使用命令。例如，删除第10到20行并粘贴到文件末尾，可以通过 `:10`（定位）、`d11d`（删除）、`G`（跳转）、`p`（粘贴） 分步完成。

![](img/dcf185667a0d9a6a273e538c9da25bdd_18.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_19.png)

现在，我们继续学习其他基础命令。

---

## 文件查看命令 👀

除了使用`vi`或图形界面工具查看文件，Linux提供了多个命令行工具，适用于不同场景。

![](img/dcf185667a0d9a6a273e538c9da25bdd_21.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_23.png)

以下是常用的文件查看命令：

*   **`cat`**：完整显示文件内容。适合查看小文件。
    ```bash
    cat filename
    ```
*   **`less` / `more`**：分页显示文件内容，适合查看大文件。按回车键逐行或逐页向下翻看。
    ```bash
    cat filename | less
    # 或直接使用
    less filename
    more filename
    ```
*   **`head` / `tail`**：查看文件的开头或末尾部分。
    ```bash
    head -n 20 filename  # 查看文件前20行
    tail -n 20 filename  # 查看文件后20行
    ```
*   **组合使用**：通过管道符 `|` 可以组合命令。例如，查看文件的第11到13行：
    ```bash
    head -n 13 filename | tail -n 3
    ```

**注意**：`cat`命令还有一种特殊用法，可以创建文件并写入内容，使用 `EOF` 作为结束标记（`EOF`可替换为其他字符）：
```bash
cat > newfile << EOF
这是第一行内容
这是第二行内容
EOF
```

![](img/dcf185667a0d9a6a273e538c9da25bdd_25.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_27.png)

---

![](img/dcf185667a0d9a6a273e538c9da25bdd_29.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_31.png)

## 文件移动与重命名 🚚

`mv`命令用于移动文件或目录，也可用于重命名。

*   **重命名**：将文件`oldname`重命名为`newname`。
    ```bash
    mv oldname newname
    ```
*   **移动文件**：将文件移动到指定目录。
    ```bash
    mv filename /target/directory/
    ```

**重要提示**：
1.  如果目标文件名已存在，系统会询问是否覆盖（如果启用了`-i`交互模式）。
2.  在同一个目录下，不能存在同名的文件或目录。
3.  对于名称中包含空格的文件，操作时需要用反斜杠 `\` 进行转义，例如 `file\ name`。

![](img/dcf185667a0d9a6a273e538c9da25bdd_33.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_35.png)

---

## 文件与目录删除 🗑️

`rm`命令用于删除文件或目录。

![](img/dcf185667a0d9a6a273e538c9da25bdd_37.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_39.png)

*   **删除文件**：
    ```bash
    rm filename
    ```
*   **删除目录**：需要 `-r`（递归）参数。
    ```bash
    rm -r directoryname
    ```
*   **强制删除（无提示）**：使用 `-f` 参数。
    ```bash
    rm -rf directoryname
    ```

**安全建议**：
1.  删除重要文件前务必先备份。
2.  尽量先进入目标目录，再使用相对路径删除，避免因绝对路径输入错误导致误删系统文件（如误删根目录 `/`）。
3.  对于海量小文件的删除，如果遇到“参数列表过长”错误，可以使用以下命令：
    ```bash
    ls | xargs rm -rf
    ```

![](img/dcf185667a0d9a6a273e538c9da25bdd_41.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_42.png)

---

## 路径与特殊符号 🧭

理解路径和特殊符号是导航文件系统的关键。

*   **绝对路径与相对路径**：
    *   绝对路径：从根目录 `/` 开始，如 `/home/user/file`。
    *   相对路径：从当前目录开始，如 `./subdir/file`（`./` 表示当前目录）。
*   **常用特殊符号**：
    *   `.` 或 `./`：当前目录。
    *   `..`：上一级目录。
    *   `~`：当前用户的家目录（如`root`用户是`/root`，普通用户是`/home/username`）。
    *   `-`：切换到上一个工作目录。
*   **通配符**：
    *   `*`：匹配零个或多个任意字符。例如 `rm *.txt` 删除所有`.txt`文件。
    *   `?`：匹配一个任意字符。例如 `ls file?.txt` 匹配 `file1.txt`，但不匹配 `file10.txt`。

**注意**：通配符使用不当可能导致意外行为，例如，如果存在一个名为 `--help` 的文件，执行 `ls *` 可能会被解释为 `ls --help` 而显示帮助信息，而非文件列表。

---

## 命令别名 ⚙️

![](img/dcf185667a0d9a6a273e538c9da25bdd_44.png)

`alias`命令可以为常用命令创建简短的别名。例如，将 `ls -l` 别名化为 `ll`：
```bash
alias ll='ls -l'
```
要使别名永久生效，需要将别名设置写入用户家目录下的配置文件（如 `~/.bashrc`）中。

---

![](img/dcf185667a0d9a6a273e538c9da25bdd_46.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_48.png)

## 实验与练习 🔧

![](img/dcf185667a0d9a6a273e538c9da25bdd_50.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_52.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_54.png)

以下是本节课的实践内容，请在你的虚拟机中完成：

![](img/dcf185667a0d9a6a273e538c9da25bdd_56.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_57.png)

1.  **实验一：文件排序**
    *   在 `/tmp/lsdir` 目录下，使用 `dd` 命令创建三个大小分别为4K、8K、12K的文件（A, B, C）。
    *   使用 `man ls` 查找参数，实现将这三个文件**按从大到小的顺序**列出文件名和大小。

2.  **基础练习**：完成文档中列出的7项基础操作练习，巩固文件查看、移动、删除等命令的使用。

![](img/dcf185667a0d9a6a273e538c9da25bdd_59.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_61.png)

---

![](img/dcf185667a0d9a6a273e538c9da25bdd_63.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_65.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_66.png)

## 总结 📖

![](img/dcf185667a0d9a6a273e538c9da25bdd_68.png)

本节课我们一起深入学习了Linux的基础命令。我们掌握了如何使用 `cat`, `less`, `head`, `tail` 查看文件内容；使用 `mv` 移动和重命名文件；使用 `rm` 删除文件和目录（需谨慎）。同时，我们理解了绝对路径与相对路径的区别，以及通配符 `*` 和 `?` 的用法。通过实践练习，希望大家能熟练运用这些命令，为后续更复杂的系统操作打下坚实基础。

![](img/dcf185667a0d9a6a273e538c9da25bdd_70.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_72.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_74.png)

![](img/dcf185667a0d9a6a273e538c9da25bdd_76.png)

下一节，我们将开始学习Linux的SSH服务。