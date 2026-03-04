# Linux基础：P4：使用vi文本编辑器

![](img/6a93d427729b44c4834d8dbcdfe9df0b_0.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_2.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_4.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_6.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_8.png)

在本节课中，我们将要学习Linux系统中一个非常重要的文本编辑工具——vi编辑器及其增强版vim。我们将了解它的基本概念、三种工作模式以及如何用它来创建和修改文件。

上一节我们介绍了`ls`命令的一些高级选项，本节中我们来看看Linux系统中一个核心的文本编辑工具。

![](img/6a93d427729b44c4834d8dbcdfe9df0b_10.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_12.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_14.png)

## vi/vim编辑器简介

![](img/6a93d427729b44c4834d8dbcdfe9df0b_16.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_18.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_20.png)

vi是Unix/Linux系统中默认的文本编辑器。vim是vi的增强版本，提供了更多便利功能，例如语法高亮。在最小化安装的Linux系统中，通常只预装了vi命令。如果需要使用vim，需要额外安装`vim-enhanced`软件包。

![](img/6a93d427729b44c4834d8dbcdfe9df0b_22.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_24.png)

## vim的三种工作模式

理解vim的三种工作模式是熟练使用它的关键。你可以将其想象为变形金刚的不同形态，每种形态都有特定的功能。

以下是三种核心模式：
*   **命令模式**：打开文件后默认进入的模式。在此模式下可以执行复制、粘贴、删除、查找等操作，但不能直接输入文本。
*   **输入模式**：在此模式下，可以向文件中录入或编辑文本内容。
*   **末行模式**：在此模式下，可以执行保存文件、退出编辑器等操作。

![](img/6a93d427729b44c4834d8dbcdfe9df0b_26.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_28.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_30.png)

## 基本使用流程

![](img/6a93d427729b44c4834d8dbcdfe9df0b_31.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_33.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_34.png)

现在，我们来看如何使用vim完成创建或编辑文件的基本操作。

![](img/6a93d427729b44c4834d8dbcdfe9df0b_36.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_38.png)

以下是创建一个新文件并保存的步骤：
1.  **打开/创建文件**：使用命令 `vi 文件名` 或 `vim 文件名`。如果文件不存在，vim会新建一个临时文件。
    ```bash
    vi myfile.txt
    ```
2.  **进入输入模式**：在默认的命令模式下，按下字母键 **`i`**，屏幕下方出现 `-- INSERT --` 提示，表示已进入输入模式。
3.  **编辑文本**：此时可以像使用普通文本编辑器一样输入内容。
4.  **返回命令模式**：编辑完成后，按下键盘上的 **`Esc`** 键，退出输入模式，返回命令模式。
5.  **进入末行模式并保存退出**：在命令模式下，输入英文冒号 **`:`**，进入末行模式。然后输入 **`wq`** 并回车，即可保存文件并退出编辑器。
    ```vim
    :wq
    ```

## 其他常用操作

![](img/6a93d427729b44c4834d8dbcdfe9df0b_40.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_42.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_43.png)

除了创建文件，修改已有文件的方法完全相同，只需在第一步指定已存在的文件名即可。

![](img/6a93d427729b44c4834d8dbcdfe9df0b_45.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_47.png)

在末行模式下，还有两个非常实用的命令：
*   **`:q!`**：不保存任何修改，强制退出编辑器。
*   **`:wq`**：保存修改并退出编辑器（最常用）。

![](img/6a93d427729b44c4834d8dbcdfe9df0b_49.png)

如果只是查看文件而不做修改，可以在命令模式下直接输入 **`:q`** 退出。

![](img/6a93d427729b44c4834d8dbcdfe9df0b_51.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_53.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_55.png)

## 安装vim增强版

![](img/6a93d427729b44c4834d8dbcdfe9df0b_57.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_59.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_61.png)

如果你的系统没有vim命令，可以使用包管理器进行安装。例如在Red Hat/CentOS系统上：
```bash
yum -y install vim-enhanced
```

![](img/6a93d427729b44c4834d8dbcdfe9df0b_63.png)

![](img/6a93d427729b44c4834d8dbcdfe9df0b_65.png)

本节课中我们一起学习了vi/vim文本编辑器的核心概念和基本操作。我们掌握了它的三种工作模式（命令模式、输入模式、末行模式）以及使用 `i`、`Esc`、`:wq`、`:q!` 等关键命令完成文件创建、编辑和保存的完整流程。这是后续学习Linux系统管理和脚本编写的重要基础，请务必多加练习。