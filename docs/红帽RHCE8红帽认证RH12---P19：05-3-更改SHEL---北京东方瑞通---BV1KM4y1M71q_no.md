# 红帽RHCE8认证课程：P19：更改SHELL环境 🐚

![](img/8dbd386bf5dd7c8fdb7757038020431a_1.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_3.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_4.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_6.png)

在本节课中，我们将要学习Shell环境变量的概念、作用以及如何设置和管理它们。环境变量是控制Shell行为的关键，理解它们对于高效使用Linux系统至关重要。

## 什么是Shell变量？ 🤔

上一节我们介绍了命令提示符，其中提到了`PS1`变量。本节中我们来详细展开讲解Shell变量。

![](img/8dbd386bf5dd7c8fdb7757038020431a_8.png)

Shell支持设置变量来更改其执行行为。变量用于存储值，我们可以通过赋值语法来创建它们。

**变量赋值语法示例：**
```bash
USERNAME=老马
```
这行代码并非命令，而是Shell的一种特殊语法，用于将值“老马”赋予变量`USERNAME`。

要查看变量的值，可以使用`echo`命令并引用变量名：
```bash
echo $USERNAME
```
这将输出“老马”。

![](img/8dbd386bf5dd7c8fdb7757038020431a_10.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_12.png)

## 查看所有变量 📋

![](img/8dbd386bf5dd7c8fdb7757038020431a_14.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_16.png)

以下是查看当前Shell中所有已定义变量的方法。

![](img/8dbd386bf5dd7c8fdb7757038020431a_18.png)

使用`set`命令可以显示当前Shell会话中的所有变量及其值。
```bash
set
```
执行后会列出大量变量。我们可以结合`grep`来查找特定变量，例如我们自定义的`USERNAME`：
```bash
set | grep USERNAME
```
这些变量控制着Shell程序的运行方式，例如：
*   `BASH`：代表当前运行的Bash程序。
*   `HISTSIZE`：定义历史命令记录的最大行数。
*   `HOSTNAME`：定义系统的主机名。
*   `COLUMNS`：定义终端窗口的列数。

## 变量的作用范围 🔄

![](img/8dbd386bf5dd7c8fdb7757038020431a_20.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_22.png)

但是，Shell变量有一个重要的特性：它们默认只在**当前Shell进程**中生效。

![](img/8dbd386bf5dd7c8fdb7757038020431a_24.png)

这意味着，如果你打开一个新的终端窗口或启动一个子Shell，之前定义的变量将不可见。这是因为每个Shell进程都是独立的，拥有自己的变量空间。

![](img/8dbd386bf5dd7c8fdb7757038020431a_26.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_28.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_29.png)

**示例：**
1.  在终端A中定义变量：`USER1=老马一`
2.  在终端A中查看：`echo $USER1` （有输出）
3.  在终端B中查看：`echo $USER1` （无输出）

![](img/8dbd386bf5dd7c8fdb7757038020431a_31.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_33.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_35.png)

## 环境变量与导出 🌍

![](img/8dbd386bf5dd7c8fdb7757038020431a_37.png)

上一节我们了解到普通变量的局限性。本节中我们来看看如何扩大变量的作用范围，使其成为“环境变量”。

![](img/8dbd386bf5dd7c8fdb7757038020431a_39.png)

如果希望变量在子Shell（例如通过`bash`命令启动的新Shell）中也生效，需要使用`export`命令将其导出。

![](img/8dbd386bf5dd7c8fdb7757038020431a_41.png)

**导出变量语法：**
```bash
export USER1
```
或者直接在赋值时导出：
```bash
export USER1=老马一
```
导出后，该变量就变成了**环境变量**。此时，在当前Shell中启动的任何子进程（包括子Shell）都能继承这个变量。

![](img/8dbd386bf5dd7c8fdb7757038020431a_43.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_44.png)

**注意：** 导出操作只影响**当前Shell及其未来的子进程**。其他独立的Shell进程（如另一个终端窗口）不会受到影响。

![](img/8dbd386bf5dd7c8fdb7757038020431a_46.png)

## 管理环境变量 🛠️

![](img/8dbd386bf5dd7c8fdb7757038020431a_48.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_50.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_52.png)

以下是查看和管理环境变量的常用命令。

![](img/8dbd386bf5dd7c8fdb7757038020431a_54.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_56.png)

使用`env`命令可以专门查看当前的环境变量列表。
```bash
env
```
常见的环境变量包括：
*   `PS1`：定义命令提示符的格式。
*   `PATH`：定义命令的搜索路径。当输入命令时，系统会按照`PATH`中定义的目录顺序查找可执行文件。
    ```bash
    echo $PATH
    ```
*   `HOME`：定义当前用户的家目录路径。
*   `LANG`：定义Shell环境的语言设置。例如，将其改为英文：
    ```bash
    export LANG=en_US.UTF-8
    ```

![](img/8dbd386bf5dd7c8fdb7757038020431a_58.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_60.png)

若要取消一个环境变量，有两种方法：
1.  使用`export -n`取消导出（变量本身仍存在，但不再是环境变量）：
    ```bash
    export -n USER1
    ```
2.  使用`unset`命令彻底删除变量（包括环境变量）：
    ```bash
    unset USER1
    ```

![](img/8dbd386bf5dd7c8fdb7757038020431a_62.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_64.png)

## 永久设置环境变量 📁

![](img/8dbd386bf5dd7c8fdb7757038020431a_66.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_68.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_69.png)

以上设置都是临时的，退出Shell就会失效。为了让变量在每次登录时自动生效，需要将其写入Shell的配置文件中。

![](img/8dbd386bf5dd7c8fdb7757038020431a_71.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_73.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_75.png)

用户登录时，Bash Shell会自动读取一系列配置文件来初始化环境。加载顺序通常如下：
1.  `/etc/profile` （系统全局配置）
2.  `~/.bash_profile` 或 `~/.profile` （用户个人配置）
3.  `~/.bashrc` （用户个人的Shell配置）

**为当前用户永久设置环境变量：**
通常将变量定义添加到用户家目录下的 `~/.bashrc` 文件末尾。
```bash
echo 'export USER1=老马一' >> ~/.bashrc
```
然后执行以下命令使配置立即生效，或重新登录：
```bash
source ~/.bashrc
```

![](img/8dbd386bf5dd7c8fdb7757038020431a_77.png)

**为所有用户永久设置环境变量：**
需要将变量定义添加到 `/etc/profile` 或 `/etc/bashrc` 等系统级配置文件中（需要管理员权限）。

![](img/8dbd386bf5dd7c8fdb7757038020431a_79.png)

## 关于Shell函数 ⚙️

![](img/8dbd386bf5dd7c8fdb7757038020431a_80.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_82.png)

在使用`set`命令时，你可能会看到一些用花括号 `{}` 包裹起来的代码块。这些是**Shell函数**。

![](img/8dbd386bf5dd7c8fdb7757038020431a_84.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_85.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_87.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_89.png)

函数是将一系列命令封装起来，以便重复调用的代码块。其基本语法结构如下：
```bash
function_name () {
    # 在这里编写要执行的命令
    command1
    command2
}
```
对于初学者，目前只需知道这是Shell的一种功能即可，更深入的学习可以在掌握基础后进行。

![](img/8dbd386bf5dd7c8fdb7757038020431a_91.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_93.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_95.png)

---

本节课中我们一起学习了Shell的核心概念之一——变量。我们掌握了：
1.  **Shell变量**的赋值与查看方法。
2.  变量的**作用范围**，理解了普通变量与环境变量的区别。
3.  使用`export`命令创建**环境变量**，使其在子进程中可用。
4.  使用`env`查看环境变量，使用`unset`删除变量。
5.  通过编辑`~/.bashrc`等配置文件来**永久设置**环境变量。

![](img/8dbd386bf5dd7c8fdb7757038020431a_97.png)

![](img/8dbd386bf5dd7c8fdb7757038020431a_99.png)

理解并熟练运用环境变量，是定制个性化Shell工作环境、提高工作效率的基础。请大家课后多加练习。