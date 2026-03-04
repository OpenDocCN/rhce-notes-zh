# Linux入门教程：P5：Linux文件类型、归属关系与基本权限介绍 📂

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_1.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_3.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_5.png)

在本节课中，我们将深入学习 `ls` 命令，并重点探讨Linux系统中的文件类型、归属关系以及基本权限类别。这些概念是理解Linux文件系统管理的基础。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_7.png)

---

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_9.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_11.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_13.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_15.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_17.png)

## 文件类型介绍

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_19.png)

上一节我们介绍了 `ls` 命令的基本格式和常用选项。本节中，我们来看看如何通过 `ls -l` 命令的输出结果来识别不同的文件类型。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_21.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_23.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_25.png)

在Linux系统中，每个文件或目录都有一个类型标识符，位于 `ls -l` 命令输出行的最左侧第一个字符。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_27.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_29.png)

以下是常见的文件类型标识符：

*   **`-`**： 代表普通文件。例如文本文件、图片、视频等。
*   **`d`**： 代表目录，相当于Windows系统中的文件夹。
*   **`l`**： 代表链接文件，类似于Windows系统中的快捷方式。
*   **`b`** 或 **`c`**： 代表设备文件。`b` 是块设备（如硬盘），`c` 是字符设备（如键盘、终端）。
*   **`s`**： 代表套接字文件，用于进程间通信。
*   **`p`**： 代表管道文件，也是用于进程间通信。

例如，执行 `ls -l /` 查看根目录，可以看到以 `d` 开头的行都是目录，如 `boot`, `etc`。

---

## 归属关系介绍

了解了文件类型后，我们来看看文件的归属关系。在Linux中，每个文件都关联着三类用户身份，这决定了谁可以对文件进行何种操作。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_31.png)

这三类归属关系是：

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_33.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_35.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_37.png)

1.  **所有者 (Owner/u)**： 文件的创建者，通常拥有对该文件的最大权限。
2.  **所属组 (Group/g)**： 文件所属的用户组。组内的所有成员共享对该文件的组权限。
3.  **其他人 (Others/o)**： 既不是文件所有者，也不在文件所属组内的其他所有用户。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_39.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_41.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_43.png)

在 `ls -l` 命令的输出中，所有者名称和所属组名称紧跟在权限字段之后。例如：
`-rw-r--r--. 1 root root 1234 Dec 5 10:00 hello.txt`
这表示文件 `hello.txt` 的所有者是 `root`，所属组也是 `root` 组。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_45.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_47.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_49.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_51.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_53.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_55.png)

---

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_57.png)

## 基本权限类别介绍

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_59.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_61.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_63.png)

现在，我们结合归属关系来解读文件的基本权限。在 `ls -l` 的输出中，紧接文件类型后的9个字符代表了权限。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_65.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_67.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_69.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_71.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_73.png)

这9个字符分为三组，每组三个字符，分别对应**所有者**、**所属组**和**其他人**的权限。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_75.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_77.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_79.png)

每组内的三个字符顺序固定，代表三种基本权限：

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_81.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_83.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_85.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_87.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_89.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_91.png)

*   **`r` (Read)**： 读取权限。对于文件，表示可以查看文件内容；对于目录，表示可以列出目录内的文件列表。
*   **`w` (Write)**： 写入权限。对于文件，表示可以修改文件内容；对于目录，表示可以在目录内创建、删除、重命名文件。
*   **`x` (Execute)**： 执行权限。对于文件，表示可以将文件作为程序来运行；对于目录，表示可以进入该目录。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_93.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_95.png)

如果某个位置是短横线 `-`，则表示没有对应的权限。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_97.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_99.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_101.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_103.png)

**权限解读示例：**
`-rw-r--r--`
1.  第一个字符 `-` 表示这是一个普通文件。
2.  前三个字符 `rw-` 是**所有者**的权限：可读(`r`)、可写(`w`)、不可执行(`-`)。
3.  中间三个字符 `r--` 是**所属组**的权限：可读(`r`)、不可写(`-`)、不可执行(`-`)。
4.  最后三个字符 `r--` 是**其他人**的权限：可读(`r`)、不可写(`-`)、不可执行(`-`)。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_105.png)

---

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_107.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_109.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_111.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_113.png)

## 通过颜色辨别文件类型

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_115.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_117.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_119.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_121.png)

除了查看详细列表，在默认配置的终端中，`ls` 命令会使用不同颜色来快速区分文件类型，这对初学者非常友好。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_123.png)

以下是常见的颜色约定：

*   **蓝色**： 目录。
*   **白色**： 普通文件。
*   **浅蓝色**： 链接文件。
*   **绿色**： 可执行文件。
*   **红色**： 压缩文件（如 `.tar.gz`, `.zip`）。
*   **黄色**： 设备文件（在 `/dev` 目录下常见）。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_125.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_127.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_129.png)

你可以尝试执行 `ls --color=auto /` 来查看根目录下各种颜色的文件。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_131.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_133.png)

---

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_135.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_137.png)

## `ls` 命令其他实用选项

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_139.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_141.png)

最后，我们补充几个 `ls` 命令的常用选项，帮助你更有效地查看文件信息。

以下是几个有用的选项示例：

*   **`-h`**： 以人类可读的格式显示文件大小（如K, M, G）。常与 `-l` 结合使用。
    ```bash
    ls -lh /etc/services
    ```
*   **`-d`**： 仅显示目录本身的信息，而不是其内容。常与 `-l` 结合查看目录属性。
    ```bash
    ls -ld /etc
    ```
*   **`-R`**： 递归显示目录及其所有子目录的内容。
    ```bash
    ls -R /home
    ```
*   **`-i`**： 显示文件的inode号。inode是文件在磁盘上的唯一标识。
    ```bash
    ls -i hello.txt
    ```

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_143.png)

---

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_145.png)

## 总结

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_147.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_149.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_151.png)

本节课中我们一起学习了Linux文件系统的几个核心概念：
1.  **文件类型**： 通过 `ls -l` 第一个字符识别普通文件(`-`)、目录(`d`)、链接(`l`)等。
2.  **归属关系**： 文件关联着**所有者**、**所属组**和**其他人**三类用户。
3.  **基本权限**： 权限分为**读(r)**、**写(w)**、**执行(x)**，并按 `所有者|所属组|其他人` 的顺序排列，决定了不同用户对文件的访问能力。
4.  **查看技巧**： 使用 `ls -l` 查看详细信息，利用颜色快速分辨文件类型，并掌握 `-h`, `-d` 等常用选项。

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_153.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_155.png)

![](img/8cf4a32572e5a67c51a6a1d2ad4ba9c2_157.png)

理解这些基础概念是后续学习文件权限管理、用户和组管理的关键。建议多在系统中使用 `ls -l` 命令查看不同路径下的文件，以熟悉这些信息的呈现方式。