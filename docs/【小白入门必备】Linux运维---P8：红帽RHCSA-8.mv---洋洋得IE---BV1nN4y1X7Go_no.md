# Linux运维基础：P8：mv、cat、less、head、tail、rm命令学习 📚

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_1.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_3.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_5.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_7.png)

在本节课中，我们将要学习几个非常核心的Linux文件操作命令。我们将了解如何移动和重命名文件，如何查看文件内容，以及如何安全地删除文件和目录。这些命令是日常系统管理工作的基础。

## 移动与重命名：mv命令

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_9.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_11.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_13.png)

上一节我们介绍了`cp`命令用于复制文件，本节中我们来看看`mv`命令。`mv`命令用于移动文件或目录到其他位置，其功能类似于Windows系统中的“剪切”。同时，它也可以用来修改文件或目录的名称。

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_15.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_17.png)

命令格式与`cp`命令类似：
```bash
mv [选项] 源文件或目录 目标文件或目录
```

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_19.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_21.png)

以下是`mv`命令的基本操作示例：

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_23.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_25.png)

*   **移动文件**：将当前目录下的`hello`文件移动到`/opt/t1`目录。
    ```bash
    mv hello /opt/t1
    ```
*   **移动并重命名**：将`test`目录移动到根目录`/`下，并重命名为`xxxxx`。
    ```bash
    mv test /xxxxx
    ```
*   **移动多个项目**：将当前目录下的`t6`、`t7`、`t8`目录移动到`/opt/test`目录。
    ```bash
    mv t6 t7 t8 /opt/test
    ```

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_27.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_29.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_30.png)

**核心概念**：`mv`命令执行后，源文件或目录会从原位置消失，出现在目标位置。如果目标位置已存在同名项目，系统通常会询问是否覆盖。

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_32.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_34.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_36.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_38.png)

## 查看文件内容：cat命令

在图形界面中，我们双击文件即可查看内容。在命令行中，我们使用`cat`命令来查看文件内容。它适合查看内容较少的文件。

命令格式非常简单：
```bash
cat [选项] 文件名
```

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_40.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_42.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_44.png)

最常用的选项是`-n`，它可以显示行号。

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_46.png)

以下是使用`cat`命令查看一些常见系统文件的例子：

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_48.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_50.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_52.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_54.png)

*   **查看用户信息文件**：
    ```bash
    cat /etc/passwd
    ```
*   **带行号查看文件系统表**：
    ```bash
    cat -n /etc/fstab
    ```
*   **查看主机名**：
    ```bash
    cat /etc/hostname
    ```
*   **查看系统版本**：
    ```bash
    cat /etc/centos-release
    ```
*   **查看网卡配置**：
    ```bash
    cat /etc/sysconfig/network-scripts/ifcfg-ens33
    ```

**注意**：`cat`命令会一次性将整个文件内容输出到屏幕。对于内容非常多的文件（例如超过1万行），屏幕会快速滚动，导致无法查看开头部分的内容。此时，我们需要更合适的工具。

## 分页查看大文件：less命令

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_56.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_58.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_60.png)

当文件内容过多时，`less`命令是更好的选择。它允许我们像翻书一样分页查看文件内容，而不会让输出充斥整个屏幕。

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_62.png)

命令格式如下：
```bash
less [选项] 文件名
```

使用`less`打开文件后，会进入一个交互式界面。以下是常用的操作键：

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_64.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_66.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_68.png)

*   **上下箭头键**：向上或向下滚动一行。
*   **Page Down / Page Up**：向下或向上翻一页。（笔记本键盘可能需要配合`Fn`键）
*   **`g`**：跳转到文件第一行。
*   **`G`**：跳转到文件最后一行。
*   **`:` + 行号**：输入冒号后跟行号（如`:1000`），然后按回车，可精准跳转到指定行。
*   **`q`**：退出`less`查看器。

例如，查看一个大型配置文件：
```bash
less /etc/services
```

## 查看文件首尾：head与tail命令

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_70.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_72.png)

有时我们只需要查看文件的开头或结尾部分，这时`head`和`tail`命令就非常有用。

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_74.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_76.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_78.png)

`head`命令默认显示文件开头10行内容。
```bash
head [选项] 文件名
```
使用`-n`选项可以指定显示的行数，例如显示前20行：
```bash
head -n 20 文件名
# 或简写为
head -20 文件名
```

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_80.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_82.png)

`tail`命令与`head`相反，默认显示文件末尾10行内容。
```bash
tail [选项] 文件名
```
同样，使用`-n`选项指定行数，例如显示末尾5行网卡配置：
```bash
tail -5 /etc/sysconfig/network-scripts/ifcfg-ens33
```

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_84.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_85.png)

`tail`命令还有一个非常实用的选项`-f`，用于**动态追踪**文件末尾的变化。这在监控日志文件时特别有用。
```bash
tail -f 日志文件名
```
执行此命令后，终端会持续显示该文件新增的内容。按`Ctrl+C`可以终止追踪。

## 删除文件与目录：rm命令 ⚠️

`rm`命令用于删除文件或目录。**请务必谨慎使用此命令**，因为Linux系统默认没有回收站功能，删除操作通常是不可逆的。

命令基本格式：
```bash
rm [选项] 文件或目录
```

以下是`rm`命令的关键选项和用法：

*   **删除普通文件**：直接使用`rm`命令。
    ```bash
    rm 文件名
    ```
    系统会询问是否确认删除。
*   **强制删除文件（不询问）**：使用`-f`选项。
    ```bash
    rm -f 文件名
    ```
*   **删除目录**：必须使用`-r`选项（递归删除）。
    ```bash
    rm -r 目录名
    ```
    系统会递归询问目录内的每一项是否删除。
*   **强制删除目录及其内容**：组合使用`-r`和`-f`选项。
    ```bash
    rm -rf 目录名
    ```
    这是最“危险”的命令之一，会直接删除目录和其中所有文件，没有任何确认提示。

**关于路径分隔符`/`的补充**：在指定路径时，`/`是目录层级的分隔符。对于目录，路径末尾加不加`/`通常效果一样（例如`/opt`和`/opt/`）。但对于文件，路径末尾**不能**加`/`，否则系统会认为它是一个目录而导致错误。

**极度危险警告**：`rm -rf /`或`rm -rf /*`这类命令会尝试删除根目录下的所有内容，导致系统崩溃，绝对禁止在生产环境或重要系统中尝试。在进行删除操作前，请务必**双重检查**路径是否正确。

---

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_87.png)

![](img/a0f1bb55ef865a5eef7e9ccfdad290e7_89.png)

本节课中我们一起学习了Linux中几个至关重要的文件操作命令。我们掌握了如何使用`mv`移动和重命名文件；使用`cat`、`less`、`head`、`tail`以不同方式查看文件内容；以及如何使用`rm`命令（及其危险选项`-rf`）删除文件和目录。请务必在理解的基础上勤加练习，尤其是对于`rm`命令，要时刻保持警惕，养成操作前确认路径的好习惯。