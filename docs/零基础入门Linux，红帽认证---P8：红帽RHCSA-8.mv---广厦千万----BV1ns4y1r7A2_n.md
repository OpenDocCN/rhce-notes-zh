# Linux基础命令教程：P8：mv、cat、less、head、tail、rm命令学习 📚

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_1.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_3.png)

在本节课中，我们将要学习Linux系统中几个非常核心的文件操作命令：`mv`（移动/重命名）、`cat`、`less`、`head`、`tail`（查看文件内容）以及`rm`（删除）。掌握这些命令是进行日常文件管理和系统运维的基础。

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_5.png)

---

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_7.png)

## 使用`.`配合`cp`命令

上一节我们介绍了`cp`命令的基本用法，本节中我们来看看一个使用`.`（点）符号的小技巧。`.`代表当前目录。

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_9.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_11.png)

例如，我想将网卡配置文件复制到当前目录：
```bash
cp /etc/sysconfig/network-scripts/ifcfg-ens33 .
```
执行后，`ifcfg-ens33`文件就被复制到了你执行命令时所在的目录。这个`.`符号在指定目标路径时非常方便。

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_13.png)

---

## 深入理解`cp`命令的细节

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_15.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_17.png)

以下是关于`cp`命令在拷贝目录时的一个细节，主要涉及改名功能。

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_19.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_21.png)

当你将一个目录拷贝到一个已存在的同名目录时，行为会有所不同。如果目标目录已存在且为空，`cp`命令可能不会提示覆盖。更重要的是，如果拷贝时指定了新的目标名称，那么文件会被拷贝到以新名称命名的目录下。理解这一点可以避免在操作时产生混淆。

---

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_23.png)

## 移动与重命名：`mv`命令 🚚

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_25.png)

`mv`命令在Windows中对应的操作是“剪切”。它的功能是将文件或目录移动到其他位置，或者修改它们的名称。

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_27.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_29.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_31.png)

命令格式与`cp`命令类似：
```bash
mv [选项] 源文件或目录 目标文件或目录
```

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_33.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_34.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_35.png)

### 基本移动操作

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_37.png)

例如，将当前目录下的`hello`文件移动到`/opt/t1/`目录：
```bash
mv hello /opt/t1/
```
移动后，原位置的`hello`文件消失，出现在`/opt/t1/`目录下。

### 移动并重命名

`mv`命令也可以直接用来重命名。例如，将`test`目录移动到根目录`/`下，并改名为`xxxxx`：
```bash
mv test /xxxxx
```
执行后，根目录下会出现一个名为`xxxxx`的目录。

### 移动多个文件

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_39.png)

同时移动多个目录到指定位置：
```bash
mv t6 t7 t8 /opt/test/
```
`mv`命令简单直观，其核心在于理解“剪切”和“重命名”这两个功能。

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_41.png)

---

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_43.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_45.png)

## 查看文件内容：`cat`命令 👀

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_47.png)

在Linux中，没有图形界面的双击打开功能，我们需要使用命令来查看文件内容。`cat`命令用于查看内容较少的文件。

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_49.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_51.png)

基本命令格式：
```bash
cat [选项] 文件名
```
最常用的选项是`-n`，可以显示行号。

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_53.png)

以下是使用`cat`查看一些系统文件的例子：
*   查看系统所有用户信息：`cat /etc/passwd`
*   查看系统版本信息：`cat /etc/os-release`
*   查看主机名：`cat /etc/hostname`
*   查看网卡配置：`cat /etc/sysconfig/network-scripts/ifcfg-ens33`

`cat`命令的缺点是，如果文件内容很长（例如`/etc/services`），它会将所有内容一次性输出到屏幕，导致前面的内容无法回溯查看。

---

## 分页查看大文件：`less`命令 📄

为了解决查看大文件的问题，我们可以使用`less`命令。它像看书一样，支持分页显示。

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_55.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_57.png)

命令格式：
```bash
less [选项] 文件名
```
常用选项`-N`可以显示行号。

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_59.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_60.png)

进入`less`视图后，可以使用以下快捷键操作：
*   **上下箭头键**：向上/向下滚动一行。
*   **Page Down / Page Up** 或 **空格键 / b键**：向下/向上翻一页。
*   **g**：跳转到文件首行。
*   **G**：跳转到文件末行。
*   **/关键词**：向下搜索关键词。
*   **?关键词**：向上搜索关键词。
*   **:数字**：跳转到指定行，例如`:1000`跳转到第1000行。
*   **q**：退出`less`视图。

`less`命令在查看结束后会清空屏幕，保持终端整洁，非常适合浏览大型日志或配置文件。

---

## 查看文件首尾：`head`与`tail`命令 🔍

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_62.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_63.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_65.png)

有时我们不需要查看整个文件，而只关心开头或结尾的若干行。

### `head`命令

`head`命令默认显示文件开头10行。

常用命令格式：
```bash
head -n 行数 文件名
```
例如，查看`/etc/passwd`文件的前5行：
```bash
head -5 /etc/passwd
```
也可以简写为`-5`（注意`-`和数字间不能有空格）。

### `tail`命令

`tail`命令与`head`相反，默认显示文件末尾10行。

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_67.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_69.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_71.png)

常用命令格式：
```bash
tail -n 行数 文件名
```
例如，动态查看网卡配置文件的末尾变化，这在监控日志时非常有用：
```bash
tail -f /opt/test1.log
```
在另一个终端向该文件写入内容时（例如`echo “hello” >> /opt/test1.log`），使用`-f`选项的`tail`命令会实时显示新增的内容。按`Ctrl+C`可以终止动态查看。

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_73.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_75.png)

---

## 删除文件或目录：`rm`命令 🗑️ (谨慎使用！)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_77.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_79.png)

`rm`命令用于删除文件或目录。**请注意，Linux系统默认没有回收站，删除操作需格外谨慎。**

### 删除文件

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_81.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_82.png)

删除单个文件，系统会请求确认：
```bash
rm test.txt
```
输入`y`确认删除，`n`取消。

强制删除文件，无需确认：
```bash
rm -f test.txt
```

### 删除目录

删除目录必须使用`-r`（递归）选项，因为目录下可能包含其他文件或子目录。

交互式删除目录（会逐一确认）：
```bash
rm -r mydir/
```

强制递归删除目录及其下所有内容（**危险操作**）：
```bash
rm -rf mydir/
```

### 使用通配符`*`

`*`（星号）是通配符，代表“任意所有”。它可以用来匹配多个文件。

例如，删除`/opt`目录下的所有内容：
```bash
rm -rf /opt/*
```
**警告：** 最著名的危险命令之一就是`rm -rf /*`，它会强制删除根目录下的所有文件，导致系统崩溃。**在任何情况下都不要尝试！**

### 关于路径斜线`/`的说明

在指定路径时，目录名后面可以加`/`，也可以不加，系统都能识别（例如`/opt`和`/opt/`是等价的）。但是，文件名后面**绝对不能**加`/`，否则系统会将其误判为目录。路径中的`/`主要作用是分隔目录层级。

---

## 虚拟机快照功能 💾

在进行可能破坏系统的练习（如`rm`命令）前，建议使用虚拟机的“快照”功能。快照可以保存虚拟机在某个时间点的完整状态。

如果后续系统被误操作破坏，可以通过恢复快照，一键回到之前健康的状态。这是一个非常重要的学习和实验保障手段。

---

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_84.png)

![](img/e6ab88f4222ffe4ac44c5c2913af8f7e_86.png)

本节课中我们一起学习了Linux中关于文件移动、查看和删除的核心命令。`mv`用于移动和重命名，`cat`、`less`、`head`、`tail`用于以不同方式查看文件内容，而`rm`则是强大的删除工具，使用时务必小心。理解并熟练运用这些命令，是成为合格Linux用户的第一步。