Linux文件权限管理：P58：递归修改文件权限

![](img/9ed64541a43af655ff133bcb8ce8440e_1.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_2.png)

在本节课中，我们将要学习Linux文件权限管理中的一个重要概念——递归修改。当我们需要对一个目录及其内部的所有子目录和文件统一设置权限或更改所有者时，就需要用到递归选项 `-R`。

![](img/9ed64541a43af655ff133bcb8ce8440e_3.png)

上一节我们介绍了基本的文件权限修改命令，本节中我们来看看如何将这些修改应用到整个目录树。

![](img/9ed64541a43af655ff133bcb8ce8440e_5.png)

### 场景准备

![](img/9ed64541a43af655ff133bcb8ce8440e_6.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_7.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_8.png)

首先，我们创建一个模拟场景，包含一个主目录和其下的子目录与文件。

![](img/9ed64541a43af655ff133bcb8ce8440e_9.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_10.png)

1.  创建目录结构：
    ```bash
    mkdir -p /home/dir1/dir2 /home/dir1/dir3
    ```
    这条命令使用 `-p` 选项，可以一次性创建多级目录。

2.  复制文件到各个目录：
    ```bash
    cp /etc/passwd /home/dir1/
    cp /etc/passwd /home/dir1/dir2/
    cp /etc/passwd /home/dir1/dir3/
    ```
    这样，我们在 `dir1`、`dir2`、`dir3` 目录下都放置了一个 `passwd` 文件副本。

![](img/9ed64541a43af655ff133bcb8ce8440e_12.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_13.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_14.png)

3.  使用 `tree` 命令查看结构：
    ```bash
    tree /home/dir1
    ```
    此时，目录结构应如下所示：
    ```
    /home/dir1
    ├── dir2
    │   └── passwd
    ├── dir3
    │   └── passwd
    └── passwd
    ```

![](img/9ed64541a43af655ff133bcb8ce8440e_15.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_16.png)

### 更改所有者与所属组时的递归应用

![](img/9ed64541a43af655ff133bcb8ce8440e_18.png)

默认情况下，使用 `chown` 或 `chgrp` 命令修改目录的所有者或所属组时，只影响该目录本身。

![](img/9ed64541a43af655ff133bcb8ce8440e_20.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_21.png)

例如，只修改 `dir1` 目录的所有者和所属组：
```bash
chown tianyun:it /home/dir1
```
执行后，只有 `/home/dir1` 目录的属主和属组发生变化，其下的 `dir2`、`dir3` 及所有文件均保持不变。

![](img/9ed64541a43af655ff133bcb8ce8440e_22.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_23.png)

如果希望将更改应用到 `dir1` 目录及其所有子目录和文件，就需要使用 `-R` 选项进行递归操作。

![](img/9ed64541a43af655ff133bcb8ce8440e_25.png)

以下是递归更改所有者和所属组的命令：
```bash
chown -R tianyun:it /home/dir1
```
执行此命令后，`/home/dir1` 目录下的所有内容（包括子目录 `dir2`、`dir3` 及其中的文件）的所有者和所属组都会被统一修改。

![](img/9ed64541a43af655ff133bcb8ce8440e_26.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_27.png)

### 更改文件权限时的递归应用

修改文件权限的命令 `chmod` 同样遵循此规则。

![](img/9ed64541a43af655ff133bcb8ce8440e_29.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_30.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_31.png)

如果不使用递归，以下命令只修改 `dir1` 目录本身的权限：
```bash
chmod 755 /home/dir1
```
此时，`dir1` 目录下的子目录和文件权限保持不变。

![](img/9ed64541a43af655ff133bcb8ce8440e_32.png)

如果需要将权限设置（无论是数字法如 `755`，还是字符法如 `u=rwx,g=rx,o=rx`）应用到整个目录树，必须加上 `-R` 选项。

![](img/9ed64541a43af655ff133bcb8ce8440e_34.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_35.png)

以下是递归修改权限的命令：
```bash
chmod -R 755 /home/dir1
```
执行后，`/home/dir1` 目录及其内部的所有子目录和文件的权限都会被设置为 `755`。

![](img/9ed64541a43af655ff133bcb8ce8440e_37.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_38.png)

### 核心要点总结

本节课中我们一起学习了递归选项 `-R` 在文件权限管理中的关键作用。

![](img/9ed64541a43af655ff133bcb8ce8440e_40.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_41.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_42.png)

*   **适用命令**：`chown`、`chgrp`、`chmod`。
*   **核心选项**：`-R` (Recursive，递归)。
*   **应用场景**：当需要对一个目录及其包含的所有子目录和文件进行**统一**的所有者、所属组或权限设置时。
*   **注意事项**：递归操作影响范围广，执行前请确认目标路径正确，避免误操作。

![](img/9ed64541a43af655ff133bcb8ce8440e_43.png)

![](img/9ed64541a43af655ff133bcb8ce8440e_44.png)

记住，`-R` 选项主要针对**目录**生效。对于单个文件使用 `-R` 选项没有额外效果，因为文件下面没有其他内容。合理使用递归功能，可以极大地提升管理批量文件的效率。