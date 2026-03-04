# Linux文本处理：P32：sed高级使用方法详解 🚀

![](img/d7aed9d06c8e1a971b8a81898653ffe0_1.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_3.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_4.png)

在本节课中，我们将深入学习流编辑器 `sed` 的高级使用方法。`sed` 是一个功能强大的文本处理工具，能够在不打开文件的情况下，对文本内容进行查找、替换、删除、插入等多种操作。掌握 `sed` 对于编写脚本和高效处理文本数据至关重要。

![](img/d7aed9d06c8e1a971b8a81898653ffe0_6.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_8.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_10.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_11.png)

## 概述

![](img/d7aed9d06c8e1a971b8a81898653ffe0_13.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_15.png)

`sed` 的工作原理是逐行读取文件内容到模式空间（缓存），在模式空间内执行指定的编辑命令，然后将结果输出。默认情况下，`sed` 不会修改原文件，除非使用 `-i` 选项。

![](img/d7aed9d06c8e1a971b8a81898653ffe0_17.png)

## sed基本选项回顾

![](img/d7aed9d06c8e1a971b8a81898653ffe0_19.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_21.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_23.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_24.png)

在深入高级用法之前，我们先回顾几个核心选项：
*   **`-n`**：静默模式，不自动打印模式空间的内容。通常与打印命令 `p` 配合使用。
*   **`-i`**：直接编辑原文件。使用 `-i.bak` 可以在修改前备份原文件。
*   **`-e`**：连接多个编辑命令。
*   **`-r`**：使用扩展正则表达式，使模式匹配更强大。

![](img/d7aed9d06c8e1a971b8a81898653ffe0_26.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_28.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_30.png)

## 地址定位：精确操作目标行

`sed` 的强大之处在于可以精确指定要操作的行。地址决定了命令的作用范围。

![](img/d7aed9d06c8e1a971b8a81898653ffe0_32.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_34.png)

以下是几种主要的地址指定方式：

![](img/d7aed9d06c8e1a971b8a81898653ffe0_36.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_38.png)

*   **数字地址**：直接指定行号。
    ```bash
    sed -n ‘5p‘ file.txt  # 打印第5行
    ```

*   **最后一行**：使用 `$` 符号。
    ```bash
    sed -n ‘$p‘ file.txt  # 打印文件的最后一行
    ```

![](img/d7aed9d06c8e1a971b8a81898653ffe0_40.png)

*   **正则表达式匹配**：使用 `/pattern/` 匹配包含特定模式的行。
    ```bash
    sed -n ‘/root/p‘ file.txt  # 打印包含“root”的行
    sed -n ‘/^root/p‘ file.txt # 打印以“root”开头的行
    ```

![](img/d7aed9d06c8e1a971b8a81898653ffe0_42.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_44.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_46.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_48.png)

*   **忽略大小写**：在正则表达式后加 `i`。
    ```bash
    sed -n ‘/root/ip‘ file.txt # 打印包含“root”的行，忽略大小写
    ```

![](img/d7aed9d06c8e1a971b8a81898653ffe0_50.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_52.png)

*   **地址范围**：使用 `start,end` 指定一个行范围。
    ```bash
    sed -n ‘1,3p‘ file.txt           # 打印第1到第3行
    sed -n ‘/start/,/end/p‘ file.txt # 打印从匹配“start”的行到匹配“end”的行
    ```

![](img/d7aed9d06c8e1a971b8a81898653ffe0_54.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_56.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_58.png)

*   **步进地址**：使用 `first~step` 指定步长。
    ```bash
    sed -n ‘1~2p‘ file.txt # 打印第1,3,5,7...行（步长为2）
    ```

![](img/d7aed9d06c8e1a971b8a81898653ffe0_60.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_62.png)

## 编辑命令：对文本执行操作

![](img/d7aed9d06c8e1a971b8a81898653ffe0_64.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_66.png)

确定了地址范围后，我们可以使用各种命令对文本进行操作。

![](img/d7aed9d06c8e1a971b8a81898653ffe0_68.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_70.png)

上一节我们介绍了如何定位目标行，本节中我们来看看能对这些行执行哪些操作。

![](img/d7aed9d06c8e1a971b8a81898653ffe0_72.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_74.png)

以下是 `sed` 的核心编辑命令列表：

*   **删除命令 `d`**：删除匹配的行。
    ```bash
    sed ‘2d‘ file.txt          # 删除第2行（屏幕输出结果，原文件不变）
    sed -i ‘1,5d‘ file.txt     # 删除原文件的第1到第5行
    sed -i.bak ‘3d‘ file.txt   # 删除第3行，并备份原文件为 file.txt.bak
    ```

![](img/d7aed9d06c8e1a971b8a81898653ffe0_76.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_78.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_80.png)

*   **追加命令 `a`**：在指定行**之后**追加新行。
    ```bash
    sed ‘2a\This is new line‘ file.txt # 在第2行后追加一行文本
    ```

*   **插入命令 `i`**：在指定行**之前**插入新行。
    ```bash
    sed ‘2i\This is new line‘ file.txt # 在第2行前插入一行文本
    ```

![](img/d7aed9d06c8e1a971b8a81898653ffe0_82.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_84.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_85.png)

*   **替换行命令 `c`**：用新文本替换整行。
    ```bash
    sed ‘2c\This replaces line 2‘ file.txt # 将第2行替换为新内容
    ```

![](img/d7aed9d06c8e1a971b8a81898653ffe0_87.png)

*   **查找替换命令 `s`**：这是最常用的命令，用于替换行内的文本。
    ```bash
    sed ‘s/old/new/‘ file.txt       # 将每行第一个“old”替换为“new”
    sed ‘s/old/new/g‘ file.txt      # 将每行所有的“old”替换为“new”（全局替换）
    sed ‘s/old/new/2‘ file.txt      # 将每行第2个“old”替换为“new”
    sed -n ‘s/old/new/gp‘ file.txt  # 全局替换，并只打印发生替换的行
    sed ‘s/old/new/gi‘ file.txt     # 全局替换，且忽略大小写
    ```

![](img/d7aed9d06c8e1a971b8a81898653ffe0_89.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_91.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_93.png)

*   **写入文件命令 `w`**：将匹配的行写入另一个文件。
    ```bash
    sed -n ‘1,2w output.txt‘ file.txt # 将第1、2行保存到 output.txt 文件
    ```

![](img/d7aed9d06c8e1a971b8a81898653ffe0_95.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_97.png)

## 总结

![](img/d7aed9d06c8e1a971b8a81898653ffe0_99.png)

![](img/d7aed9d06c8e1a971b8a81898653ffe0_101.png)

本节课中我们一起学习了 `sed` 流编辑器的高级功能。我们掌握了如何通过数字、正则表达式和范围来精确定位需要操作的行，并学习了删除(`d`)、追加(`a`)、插入(`i`)、整行替换(`c`)、查找替换(`s`)和写入文件(`w`)等核心编辑命令。特别是 `s` 命令的灵活运用，结合正则表达式，可以解决绝大部分的文本替换需求。记住 `-n`、`-i` 等关键选项的用法，将使你在处理文本时事半功倍。