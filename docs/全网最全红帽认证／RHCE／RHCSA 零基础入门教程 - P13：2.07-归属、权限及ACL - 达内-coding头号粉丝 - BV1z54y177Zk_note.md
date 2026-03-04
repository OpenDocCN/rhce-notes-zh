# 红帽认证零基础入门教程：P13：2.07-归属、权限及ACL 📂

![](img/31b93ce47840c27dae84dc7a7e101e56_1.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_3.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_4.png)

在本节课中，我们将要学习Linux系统中关于文件与目录的两个核心概念：**归属**与**权限**。理解并掌握它们，是管理多用户系统、保障系统安全的基础。我们还将学习一种更精细的权限控制机制——访问控制列表（ACL）。

![](img/31b93ce47840c27dae84dc7a7e101e56_6.png)

## 归属与权限概述 🔍

上一节我们介绍了用户账号和组账号的管理，本节中我们来看看文档的权限和归属。在Linux系统中，每个文件或目录都有明确的**所有者**和**所属组**，这决定了谁可以对这个文件做什么操作。理解归属和权限是解决许多系统管理任务的关键。

![](img/31b93ce47840c27dae84dc7a7e101e56_8.png)

### 什么是归属？

归属指的是一个文件或目录属于哪个用户以及哪个组。这类似于公司里，一台笔记本电脑会分配给特定员工（所有者）和其所在部门（所属组）使用。

![](img/31b93ce47840c27dae84dc7a7e101e56_10.png)

*   **属主**：文件的所有者，对应一个具体的用户账号。
*   **属组**：文件所属的组，对应一个组账号。

我们可以使用 `ls -l` 命令查看文件的归属信息。

![](img/31b93ce47840c27dae84dc7a7e101e56_12.png)

```bash
ls -l /etc/passwd
```

![](img/31b93ce47840c27dae84dc7a7e101e56_14.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_16.png)

命令输出中，`root root` 部分即表示 `/etc/passwd` 文件属于 `root` 用户和 `root` 组。

![](img/31b93ce47840c27dae84dc7a7e101e56_18.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_20.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_22.png)

### 权限的三种类别

![](img/31b93ce47840c27dae84dc7a7e101e56_24.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_26.png)

权限决定了用户能对文件或目录执行何种操作。基本权限分为三类：

![](img/31b93ce47840c27dae84dc7a7e101e56_28.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_30.png)

*   **r (Read)**：读取权限。
*   **w (Write)**：写入权限。
*   **x (eXecute)**：执行权限。

![](img/31b93ce47840c27dae84dc7a7e101e56_32.png)

### 权限如何与归属关联？

`ls -l` 命令输出的权限部分共有9个字符（忽略第一个表示文件类型的字符）。这9个字符分为三组，分别对应三类用户：

![](img/31b93ce47840c27dae84dc7a7e101e56_34.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_36.png)

*   **前3位**：**文件所有者（u）** 的 `r`、`w`、`x` 权限。
*   **中3位**：**文件所属组（g）** 成员的 `r`、`w`、`x` 权限。
*   **后3位**：**其他用户（o）** 的 `r`、`w`、`x` 权限。

![](img/31b93ce47840c27dae84dc7a7e101e56_37.png)

权限字符如果有，则显示 `r`、`w` 或 `x`；如果没有，则显示 `-`。

例如，对于文件 `/etc/passwd`，其权限 `-rw-r--r--` 表示：
*   所有者 `root` 可读、可写。
*   所属组 `root` 的成员可读。
*   其他用户可读。

![](img/31b93ce47840c27dae84dc7a7e101e56_39.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_40.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_42.png)

## 权限对文件和目录的不同含义 📝

虽然标记相同，但 `r`、`w`、`x` 权限对文件和目录的意义有所不同。

以下是针对文件和目录的权限含义说明：

*   **对文件**
    *   **r**：可以读取文件内容（如使用 `cat`、`vim`）。
    *   **w**：可以修改文件内容。
    *   **x**：可以将文件作为程序来执行。
*   **对目录**
    *   **r**：可以列出目录内的内容（如使用 `ls`）。
    *   **w**：可以在目录内创建、删除、重命名文件或子目录。
    *   **x**：可以进入该目录（如使用 `cd`）。

![](img/31b93ce47840c27dae84dc7a7e101e56_44.png)

**重要提示**：对于目录，如果用户没有 `x`（执行）权限，即使拥有 `r`（读取）权限，也无法进入目录或查看其内容。

## 修改归属关系 🔧

![](img/31b93ce47840c27dae84dc7a7e101e56_46.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_48.png)

我们可以使用 `chown` 命令来改变文件或目录的所有者和所属组。

其基本语法为：
```bash
chown 用户:组 文件或目录
```

以下是 `chown` 命令的几种常见用法示例：

*   **更改所有者**：`chown zhangsan /home/zhangsan`
*   **更改所属组**：`chown :users /home/zhangsan`
*   **同时更改所有者和所属组**：`chown zhangsan:zhangsan /home/zhangsan`
*   **递归更改目录内所有内容**：`chown -R zhangsan:zhangsan /home/zhangsan`

## 修改基本权限 🛠️

我们可以使用 `chmod` 命令来修改文件或目录的 `r`、`w`、`x` 权限。

其基本语法为：
```bash
chmod 谁 操作符 权限 文件或目录
```

*   **谁**：`u`（所有者），`g`（所属组），`o`（其他用户），`a`（所有人，即 `u+g+o`）。
*   **操作符**：`+`（添加权限），`-`（移除权限），`=`（设置精确权限）。
*   **权限**：`r`，`w`，`x`。

![](img/31b93ce47840c27dae84dc7a7e101e56_50.png)

以下是 `chmod` 命令的几种常见用法示例：

![](img/31b93ce47840c27dae84dc7a7e101e56_52.png)

*   **为所有者添加执行权限**：`chmod u+x file`
*   **为所属组移除写入权限**：`chmod g-w file`
*   **为其他用户设置只读权限**：`chmod o=r file`
*   **同时为组和其他用户添加读、执行权限**：`chmod g+rx,o+rx file`
*   **递归设置目录权限**：`chmod -R o+rx /home/share`

## 访问控制列表（ACL）详解 🎯

基本权限模型（所有者、组、其他人）有时不够灵活。例如，一个属于 `root` 的文件，需要让用户 `zhangsan` 可读写，而用户 `lisi` 完全不可访问。此时，`zhangsan` 和 `lisi` 都属于“其他人”，无法用 `chmod` 区分对待。

这时就需要 **访问控制列表（ACL）**。ACL 允许我们为特定的用户或组设置独立的权限。

![](img/31b93ce47840c27dae84dc7a7e101e56_54.png)

### 管理ACL的常用命令

![](img/31b93ce47840c27dae84dc7a7e101e56_56.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_57.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_59.png)

*   **查看ACL**：`getfacl 文件或目录`
*   **设置ACL**：`setfacl -m 条目 文件或目录`
*   **删除ACL**：`setfacl -x 条目 文件或目录`
*   **清空所有ACL**：`setfacl -b 文件或目录`

![](img/31b93ce47840c27dae84dc7a7e101e56_61.png)

### ACL条目格式

![](img/31b93ce47840c27dae84dc7a7e101e56_63.png)

*   **为用户设置**：`user:用户名:权限`，例如 `user:zhangsan:rw`
*   **为组设置**：`group:组名:权限`，例如 `group:developers:rx`

### 实践示例

![](img/31b93ce47840c27dae84dc7a7e101e56_65.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_67.png)

假设有一个文件 `/data/report.txt`，属于 `root:root`，基本权限为 `-rw-r--r--`。现在需要：
1.  用户 `zhangsan` 对其有读写权限。
2.  用户 `lisi` 对其没有任何权限。

![](img/31b93ce47840c27dae84dc7a7e101e56_69.png)

操作步骤如下：

```bash
# 1. 查看当前ACL（初始应无额外条目）
getfacl /data/report.txt

# 2. 为zhangsan设置读写权限
setfacl -m user:zhangsan:rw /data/report.txt

![](img/31b93ce47840c27dae84dc7a7e101e56_71.png)

# 3. 为lisi设置无任何权限（用‘-’表示）
setfacl -m user:lisi:- /data/report.txt

# 4. 再次查看ACL，确认设置生效
getfacl /data/report.txt
```

执行 `getfacl` 后，输出中会看到名为 `user:zhangsan` 和 `user:lisi` 的额外条目，分别标明了他们的特定权限。

![](img/31b93ce47840c27dae84dc7a7e101e56_73.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_74.png)

## 总结 📚

本节课中我们一起学习了Linux系统权限管理的核心知识。

![](img/31b93ce47840c27dae84dc7a7e101e56_76.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_78.png)

我们首先了解了**归属**的概念，即文件属于哪个用户和哪个组。接着，我们深入学习了**基本权限** `r`、`w`、`x` 及其对文件和目录的不同含义，并掌握了使用 `chown` 修改归属、使用 `chmod` 修改权限的方法。

![](img/31b93ce47840c27dae84dc7a7e101e56_80.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_82.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_84.png)

最后，为了满足更精细的权限控制需求，我们引入了**访问控制列表（ACL）**，学习了如何使用 `setfacl` 和 `getfacl` 命令为特定用户或组设置独立于基本权限模型之外的访问规则。

![](img/31b93ce47840c27dae84dc7a7e101e56_86.png)

![](img/31b93ce47840c27dae84dc7a7e101e56_88.png)

熟练掌握这些内容，是成为一名合格的Linux系统管理员的重要一步。