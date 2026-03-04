# Linux运维培训教程：1.5：Linux文件类型、归属关系与基本权限介绍

![](img/ee312eac488f5d397ec9585dfc4f7bc6_1.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_3.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_5.png)

在本节课中，我们将深入学习 `ls` 命令，并重点介绍Linux系统中的文件类型、归属关系以及基本权限类别。这些概念是理解Linux文件系统的基础。

---

![](img/ee312eac488f5d397ec9585dfc4f7bc6_7.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_9.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_11.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_13.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_15.png)

## 命令回顾：`ls` 命令

![](img/ee312eac488f5d397ec9585dfc4f7bc6_17.png)

上一节我们介绍了 `ls` 命令的基本格式和常用选项。本节中，我们来看看如何通过 `ls` 命令获取文件的详细信息，并解读这些信息背后的核心概念。

![](img/ee312eac488f5d397ec9585dfc4f7bc6_19.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_21.png)

`ls` 命令用于查看目录下的内容以及目录和文件的详细属性。其命令格式非常灵活，可以组合使用多个选项和参数。

![](img/ee312eac488f5d397ec9585dfc4f7bc6_23.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_25.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_27.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_29.png)

以下是 `ls` 命令的几个常用选项：
*   **`-a`**：显示目录下的所有内容，包括隐藏文件（以 `.` 开头的文件）。
*   **`-l`**：以长格式显示目录下的内容及详细属性。
*   **`-h`**：以人性化的单位（K, M, G）显示文件大小，需与 `-l` 结合使用。

---

## 文件类型详解

在Linux系统中，一切皆文件。通过 `ls -l` 命令输出的第一列字符，我们可以识别文件的类型。

以下是常见的文件类型标识：
*   **`-`**：普通文件，例如文本文件、图片、视频等。
*   **`d`**：目录，相当于Windows系统中的文件夹。
*   **`l`**：链接文件，类似于Windows系统中的快捷方式。
*   **`b`**：块设备文件，例如硬盘。
*   **`c`**：字符设备文件，例如键盘、鼠标。
*   **`s`**：套接字文件，用于进程间通信。
*   **`p`**：管道文件，用于进程间通信。

---

## 文件归属关系

在Linux系统中，每个文件都有明确的归属关系，主要分为三类：
1.  **所有者 (Owner, u)**：文件的创建者，拥有对该文件的最高权限。
2.  **所属组 (Group, g)**：文件所属的用户组，组内成员共享该文件的组权限。
3.  **其他人 (Others, o)**：既不是文件所有者，也不在文件所属组内的其他所有用户。

![](img/ee312eac488f5d397ec9585dfc4f7bc6_31.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_33.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_35.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_37.png)

在 `ls -l` 的输出中，第三列显示文件的所有者，第四列显示文件的所属组。

![](img/ee312eac488f5d397ec9585dfc4f7bc6_39.png)

---

![](img/ee312eac488f5d397ec9585dfc4f7bc6_41.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_43.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_45.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_47.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_49.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_51.png)

## 基本权限类别

![](img/ee312eac488f5d397ec9585dfc4f7bc6_53.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_55.png)

文件的权限决定了用户能对文件执行何种操作。权限分为三种基本类型：
*   **读取 (r)**：允许查看文件内容或列出目录内容。
*   **写入 (w)**：允许修改文件内容或在目录中创建、删除文件。
*   **执行 (x)**：允许运行程序文件或进入目录。

![](img/ee312eac488f5d397ec9585dfc4f7bc6_57.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_59.png)

在 `ls -l` 命令的输出中，权限信息由9个字符组成，分为三组，分别对应所有者、所属组和其他人的权限。

![](img/ee312eac488f5d397ec9585dfc4f7bc6_61.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_63.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_65.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_67.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_69.png)

**权限表示公式：**
```
-rw-r--r--
```
*   第1位 `-` 表示文件类型（此处为普通文件）。
*   第2-4位 `rw-` 表示**所有者**的权限：可读、可写、不可执行。
*   第5-7位 `r--` 表示**所属组**的权限：只可读。
*   第8-10位 `r--` 表示**其他人**的权限：只可读。

![](img/ee312eac488f5d397ec9585dfc4f7bc6_71.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_73.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_75.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_77.png)

**权限代码示例：**
*   `r` = 读取 (read)
*   `w` = 写入 (write)
*   `x` = 执行 (execute)
*   `-` = 无相应权限

![](img/ee312eac488f5d397ec9585dfc4f7bc6_79.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_81.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_83.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_85.png)

---

![](img/ee312eac488f5d397ec9585dfc4f7bc6_87.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_89.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_91.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_93.png)

## 通过颜色辨别文件类型

![](img/ee312eac488f5d397ec9585dfc4f7bc6_95.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_97.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_99.png)

在终端中，`ls` 命令默认会使用颜色来区分不同类型的文件，使识别更加直观。

![](img/ee312eac488f5d397ec9585dfc4f7bc6_101.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_103.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_105.png)

以下是常见的颜色与文件类型对应关系：
*   **蓝色**：目录。
*   **白色**：普通文件。
*   **浅蓝色**：链接文件。
*   **绿色**：可执行文件。
*   **红色**：压缩文件。
*   **黄色**：设备文件。
*   **红色闪动**：链接文件指向的源文件已丢失或不可用。

![](img/ee312eac488f5d397ec9585dfc4f7bc6_107.png)

---

![](img/ee312eac488f5d397ec9585dfc4f7bc6_109.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_111.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_113.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_115.png)

## 查看指定路径下的内容

![](img/ee312eac488f5d397ec9585dfc4f7bc6_117.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_119.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_121.png)

要查看特定目录或文件，需要在 `ls` 命令后指定路径。路径之间使用 `/` 作为分隔符。

![](img/ee312eac488f5d397ec9585dfc4f7bc6_123.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_125.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_127.png)

**查看命令示例：**
```bash
# 查看根目录下的内容
ls /

# 查看 /etc 目录下的内容
ls /etc

![](img/ee312eac488f5d397ec9585dfc4f7bc6_129.png)

# 查看 /etc/passwd 文件的详细信息
ls -l /etc/passwd
```

![](img/ee312eac488f5d397ec9585dfc4f7bc6_131.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_133.png)

---

![](img/ee312eac488f5d397ec9585dfc4f7bc6_135.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_137.png)

## 其他实用选项

![](img/ee312eac488f5d397ec9585dfc4f7bc6_139.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_141.png)

除了上述核心选项，`ls` 命令还有其他一些有用的选项。

![](img/ee312eac488f5d397ec9585dfc4f7bc6_143.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_145.png)

以下是几个补充选项的介绍：
*   **`-d`**：仅显示目录本身的信息，而不显示其内容。常与 `-l` 结合使用以查看目录属性。
    ```bash
    ls -ld /etc
    ```
*   **`-i`**：显示文件的inode号。inode是文件在系统中的唯一标识符，类似于身份证号。
    ```bash
    ls -i
    ```
*   **`-R`**：递归显示目录及其所有子目录的内容。
    ```bash
    ls -R /home
    ```

---

## 命令行编辑技巧

![](img/ee312eac488f5d397ec9585dfc4f7bc6_147.png)

在输入命令时，可以使用快捷操作提高效率。一个常用的技巧是快速引用上一条命令的参数。

![](img/ee312eac488f5d397ec9585dfc4f7bc6_149.png)

**快捷操作示例：**
按下 `Esc` 键后，再按 `.` 键，可以将上一条命令的参数快速填入当前命令行光标处。

![](img/ee312eac488f5d397ec9585dfc4f7bc6_151.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_153.png)

![](img/ee312eac488f5d397ec9585dfc4f7bc6_155.png)

---

![](img/ee312eac488f5d397ec9585dfc4f7bc6_157.png)

本节课中我们一起学习了 `ls` 命令的深入用法，理解了Linux文件的类型、归属关系和基本权限。掌握这些概念是后续学习文件操作和系统管理的基础。请务必熟悉 `ls -l` 输出的含义，并通过实践加深理解。