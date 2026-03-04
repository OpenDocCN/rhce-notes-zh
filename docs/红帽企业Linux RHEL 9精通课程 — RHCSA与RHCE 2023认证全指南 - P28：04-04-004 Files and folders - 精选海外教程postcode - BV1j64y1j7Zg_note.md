# RHEL 9精通课程：P28：04-04-004 文件和文件夹

![](img/708a58664b1e4d58ac042187c41c9efa_0.png)

## 概述
在本节课中，我们将学习如何在Linux系统中创建文件和文件夹。我们将从基础的`touch`和`mkdir`命令开始，然后探索一个强大的功能——大括号扩展，它能让我们高效地批量创建大量文件和目录。

![](img/708a58664b1e4d58ac042187c41c9efa_2.png)

![](img/708a58664b1e4d58ac042187c41c9efa_4.png)

![](img/708a58664b1e4d58ac042187c41c9efa_6.png)

---

## 创建文件：`touch`命令
我们要查看的第一个命令是`touch`命令。`touch`命令可以创建新的空文件。

![](img/708a58664b1e4d58ac042187c41c9efa_8.png)

![](img/708a58664b1e4d58ac042187c41c9efa_10.png)

假设我想在桌面上创建一个名为“file_one.txt”的新文件。我们所要做的就是输入`touch file_one.txt`。我们可以看到文件已经创建并且出现了。

现在，您不必在您所在的目录中执行此操作。例如，如果我想在文档文件夹中创建另一个名为“remote_file”的文件，我可以输入`touch Documents/remote_file`。

![](img/708a58664b1e4d58ac042187c41c9efa_12.png)

现在，当我查看文档文件夹内部时，我们可以看到“remote_file”已创建。请记住，使用`touch`命令创建的文件都是完全空的。

![](img/708a58664b1e4d58ac042187c41c9efa_14.png)

---

## 创建包含内容的文件：`echo`命令
我们使用`touch`命令创建的文件完全是空的。但是，如果您想创建实际上包含某些内容的文件，那么我们可以使用`echo`命令将内容重定向到其中。

![](img/708a58664b1e4d58ac042187c41c9efa_16.png)

![](img/708a58664b1e4d58ac042187c41c9efa_18.png)

如果我想使用`echo`命令在桌面上创建一个名为“hello.txt”的文件，并将内容“Hello there”放入其中，我只需输入`echo "Hello there" > hello.txt`。

当您使用重定向符号`>`指向一个尚未创建的文件时，shell将自动创建该文件并把内容放进去。因此，当我们打开它时，我们会看到“Hello there”位于该文件中。这是创建文件的另一种方式。

![](img/708a58664b1e4d58ac042187c41c9efa_20.png)

---

![](img/708a58664b1e4d58ac042187c41c9efa_22.png)

![](img/708a58664b1e4d58ac042187c41c9efa_24.png)

## 创建文件夹：`mkdir`命令
在shell中创建目录的方式是使用`mkdir`命令。它代表“make directory”（创建目录），其工作原理与`touch`命令基本相同。

如果我想在桌面上创建一个名为“folder”的文件夹，那么我所要做的就是键入`mkdir folder`。我们会看到一个名为“folder”的新文件夹出现了。

同样，这可以在远处发生。它不必位于您当前的工作目录中。例如，如果我想在图片目录中创建一个名为“Holiday”的文件夹，我可以输入`mkdir Pictures/Holiday`。

---

![](img/708a58664b1e4d58ac042187c41c9efa_26.png)

## 创建多级目录：`mkdir -p`选项
您可以一次性创建整个文件夹路径，而不是一次创建一个文件夹。假设我想创建路径`blah/thing/qibla`，但如果直接运行`mkdir blah/thing/qibla`，shell会报错，提示“blah”文件夹不存在。这是因为shell试图先进入“blah”，再进入“thing”，然后创建“qibla”。

如果我们为`mkdir`命令提供`-p`选项，那么即使没有任何中间文件夹存在，它也会创建整个路径。因此，如果我们执行`mkdir -p blah/thing/qibla`，我们就会看到整个路径被一次性创建。

![](img/708a58664b1e4d58ac042187c41c9efa_28.png)

---

## 命名注意事项
创建新文件或目录时需要记住一些事情，那就是尽量避免在名称中添加空格。例如，如果我创建一个名为“Happy Birthday”的文件夹，输入`mkdir Happy Birthday`，实际发生的是我们创建了两个文件夹：“Happy”和“Birthday”。这是因为空格分隔了命令行参数。

执行此操作的正确方法是使用引号：`mkdir "Happy Birthday"`。但一般来说，在Linux中，文件夹和文件名中包含空格会带来很多麻烦，因为涉及空格和命令行参数等问题。

![](img/708a58664b1e4d58ac042187c41c9efa_30.png)

通常，您应该用下划线代替空格。例如，使用`Happy_Birthday`而不是“Happy Birthday”。这可以使Tab键自动补全等功能工作得更好，并防止搞乱命令行参数。

![](img/708a58664b1e4d58ac042187c41c9efa_32.png)

![](img/708a58664b1e4d58ac042187c41c9efa_34.png)

另一件重要的事情是，Linux中的文件夹和文件名是区分大小写的。因此，`Happy_Birthday`与`happy_birthday`是完全不同的文件夹。

![](img/708a58664b1e4d58ac042187c41c9efa_36.png)

---

![](img/708a58664b1e4d58ac042187c41c9efa_38.png)

## 进阶：使用大括号扩展批量创建
现在您已经知道如何使用`touch`和`mkdir`命令创建文件和目录了，让我们将技能提升一个档次。

假设您正在开展一个为期五年的大型项目，每个月都需要创建一个文件夹，例如“Jan_2017”、“Feb_2017”……一直到“Dec_2022”。每个文件夹中还需要创建100个文件，从“file1”到“file100”。如果手动操作，这将非常繁琐。

![](img/708a58664b1e4d58ac042187c41c9efa_40.png)

在命令行上，我们可以使用shell的一个强大功能——大括号扩展，来轻松实现这一点。

![](img/708a58664b1e4d58ac042187c41c9efa_42.png)

### 批量创建文件夹
首先，我们转到桌面并创建一个名为“Project”的大文件夹：`mkdir Project && cd Project`。

现在，使用大括号扩展来创建所有月份的文件夹：
```bash
mkdir {Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec}_{2017..2022}
```
这个命令中：
*   `{Jan,Feb,...Dec}` 会展开为12个月份。
*   `{2017..2022}` 会展开为从2017到2022的年份。
*   下划线 `_` 连接它们。
*   shell会将两个大括号集合进行组合展开，最终生成“Jan_2017”、“Feb_2017”……“Dec_2022”共72个文件夹名，并一次性创建它们。

![](img/708a58664b1e4d58ac042187c41c9efa_44.png)

### 批量创建文件
接下来，在每个刚创建的文件夹中，创建100个文件。我们可以再次利用大括号扩展，结合通配符路径：
```bash
touch {Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec}_{2017..2022}/file{1..100}
```
这个命令的意思是：在“Jan_2017”到“Dec_2022”的每一个文件夹中，创建“file1”、“file2”……直到“file100”。这样，我们用一个命令就创建了大约7200个文件。

![](img/708a58664b1e4d58ac042187c41c9efa_46.png)

![](img/708a58664b1e4d58ac042187c41c9efa_48.png)

### 大括号扩展的其他应用
大括号扩展不仅适用于`touch`和`mkdir`命令，它实际上可以在整个shell中使用。

例如，使用`ls`命令列出所有文件夹的内容：
```bash
ls {Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec}_{2017..2022}
```

![](img/708a58664b1e4d58ac042187c41c9efa_50.png)

或者，将所有输出重定向到一个文件：
```bash
ls {Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec}_{2017..2022} > output.txt
```

![](img/708a58664b1e4d58ac042187c41c9efa_52.png)

### 更简单的例子
大括号扩展也适用于简单的模式。例如，要创建file_a.txt, file_b.txt, file_c.txt，可以：
```bash
touch file_{a,b,c}.txt
```
或者使用序列：
```bash
touch file_{a..c}.txt
```

![](img/708a58664b1e4d58ac042187c41c9efa_54.png)

---

## 总结
在本节课中，我们一起学习了：
1.  使用 **`touch`** 命令创建空文件。
2.  使用 **`echo “内容” > 文件名`** 创建带有内容的文件。
3.  使用 **`mkdir`** 命令创建文件夹（目录）。
4.  使用 **`mkdir -p`** 选项创建多级目录路径。
5.  为文件和文件夹命名时，应避免使用空格，推荐使用下划线，并注意Linux系统区分大小写。
6.  掌握了强大的 **大括号扩展 `{}`** 功能，它可以与`touch`、`mkdir`、`ls`等命令结合，通过定义模式（如`{1..100}`、`{a,b,c}`、`{2017..2022}`）来批量、高效地操作文件和目录，极大地提升了在命令行下的工作效率。

![](img/708a58664b1e4d58ac042187c41c9efa_56.png)

通过结合这些基础命令和大括号扩展，您可以轻松管理大量文件和目录，这是Linux命令行高效性的一个完美体现。