# Linux趣味命令入门：P1：几个没用但好玩的Linux命令 🐧

在本节课中，我们将学习几个在Linux系统中“没用”但非常有趣的命令。这些命令虽然不常用于严肃的系统管理或开发工作，但它们能帮助你熟悉命令行环境，并从中获得乐趣。我们将逐一介绍这些命令的用法和效果。

## 概述

![](img/58d1fcc6e606fada5fbf18fcb71e0f26_0.png)

Linux命令行是一个功能强大的工具，除了执行严肃的任务，也隐藏着一些有趣的功能。本节将介绍几个这样的命令，它们能产生视觉或听觉效果，适合用于展示或娱乐。

## 命令一：`sl` - 蒸汽机车

上一个命令我们介绍了命令行本身，本节中我们来看看一个经典的趣味命令 `sl`。这个命令会在你的终端里显示一个蒸汽机车的ASCII艺术动画。

要使用这个命令，你需要先安装它。在基于Debian的系统（如Ubuntu）上，可以使用以下命令安装：

```bash
sudo apt install sl
```

安装完成后，只需在终端输入 `sl` 并回车，一个火车动画就会从屏幕右侧开往左侧。

![](img/58d1fcc6e606fada5fbf18fcb71e0f26_2.png)

## 命令二：`cmatrix` - 矩阵数字雨

看过《黑客帝国》吗？`cmatrix` 命令可以模拟电影中经典的绿色数字雨效果。

首先，你需要安装它：

```bash
sudo apt install cmatrix
```

安装后，运行 `cmatrix` 命令。终端将被不断下落的绿色字符填满，仿佛进入了矩阵世界。你可以按 `q` 键退出。

![](img/58d1fcc6e606fada5fbf18fcb71e0f26_4.png)

## 命令三：`fortune` - 随机名言

`fortune` 命令会在终端随机显示一条名言、笑话或谚语。

以下是安装和使用的步骤：

1.  安装命令：
    ```bash
    sudo apt install fortune
    ```
2.  运行命令：
    ```bash
    fortune
    ```
    每次执行都会显示一条不同的信息。

## 命令四：`cowsay` - 会说话的牛

![](img/58d1fcc6e606fada5fbf18fcb71e0f26_6.png)

`cowsay` 命令会让一头ASCII艺术构成的牛说出你指定的话。

安装和使用方法如下：

1.  安装命令：
    ```bash
    sudo apt install cowsay
    ```
2.  基础用法：让牛说“Hello Linux”
    ```bash
    cowsay "Hello Linux"
    ```
    你还可以通过管道 `|` 让其他命令的输出通过牛说出来，例如：
    ```bash
    fortune | cowsay
    ```

## 命令五：`lolcat` - 彩虹输出

![](img/58d1fcc6e606fada5fbf18fcb71e0f26_7.png)

`lolcat` 命令可以将任何文本输出渲染成彩虹色。

以下是具体操作：

1.  安装命令：
    ```bash
    sudo apt install lolcat
    ```
2.  它可以与其他命令结合使用，为输出添加色彩。例如，让 `cowsay` 的输出变成彩虹色：
    ```bash
    fortune | cowsay | lolcat
    ```
    单独使用 `lolcat` 会等待你输入文本，并按彩虹色回显。

## 总结

本节课中我们一起学习了五个有趣的Linux命令：动画火车 `sl`、矩阵效果 `cmatrix`、随机名言 `fortune`、说话的小牛 `cowsay` 以及彩虹输出 `lolcat`。这些命令展示了Linux命令行的另一面——趣味性与创造性。虽然它们在实际工作中可能用途不大，但却是探索和学习命令行文化的绝佳方式。