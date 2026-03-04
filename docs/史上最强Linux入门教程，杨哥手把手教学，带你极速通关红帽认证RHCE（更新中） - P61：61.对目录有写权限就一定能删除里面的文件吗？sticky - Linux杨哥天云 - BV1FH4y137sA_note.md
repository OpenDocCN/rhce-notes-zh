# Linux入门教程：P61：对目录有写权限就一定能删除里面的文件吗？sticky

在本节课中，我们将要学习Linux文件系统中的一种特殊权限——粘滞位（sticky bit）。我们将探讨为什么对目录拥有写权限并不总是意味着可以删除其中的所有文件，以及如何使用粘滞位来解决这个问题。

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_1.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_2.png)

## 概述

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_3.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_4.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_5.png)

上一节我们介绍了SUID和SGID这两种特殊权限。本节中我们来看看第三种特殊权限：粘滞位（sticky bit）。它主要应用于目录，用于解决在多用户共享的目录中，文件被意外删除的问题。

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_6.png)

## 传统权限的局限性

在Linux中，删除一个文件的操作权限并不取决于文件本身的权限，而是取决于**对文件所在目录的权限**。如果用户对一个目录拥有写（`w`）权限，那么他就可以删除该目录下的任何文件，即使这些文件不属于他。

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_8.png)

以下是传统权限下的一个典型场景：

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_9.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_10.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_11.png)

1.  管理员创建一个公共目录 `/home/music`，并赋予所有用户读写执行权限。
    ```bash
    mkdir /home/music
    chmod 777 /home/music
    ```
2.  用户 `tianyun` 和 `alice` 都可以在该目录下创建自己的文件。
    ```bash
    # 以 tianyun 用户身份
    touch /home/music/tianyun.mp3
    # 以 alice 用户身份
    touch /home/music/alice.mp3
    ```
3.  由于对目录有写权限，`alice` 用户可以删除 `tianyun` 创建的文件，反之亦然。这在一个需要协作的公共空间里是不安全的。

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_12.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_13.png)

## 粘滞位（Sticky Bit）的作用

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_14.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_15.png)

为了解决上述问题，Linux引入了粘滞位。当对一个目录设置粘滞位后，该目录下的文件**只能被其所有者、目录所有者或root用户删除**。其他用户即使对目录有写权限，也无法删除不属于自己的文件。

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_17.png)

### 如何设置粘滞位

粘滞位使用符号 `t` 表示，通常设置在目录的其他用户（others）权限位上。

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_19.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_20.png)

以下是设置粘滞位的命令：

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_21.png)

*   **符号模式**：使用 `chmod o+t <目录名>`
    ```bash
    chmod o+t /home/music
    ```
*   **数字模式**：在原有的三位权限数字前加上 `1`（代表粘滞位）。例如，将目录权限设置为 `1777`。
    ```bash
    chmod 1777 /home/music
    ```

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_23.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_24.png)

设置后，使用 `ls -ld` 查看目录权限，会在其他用户的执行位看到 `t`。
```bash
drwxrwxrwt. 2 root root 4096 Nov 10 10:00 /home/music
```

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_26.png)

### 粘滞位的实际效果

让我们回到之前的例子，但这次为 `/home/music` 目录设置了粘滞位。

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_28.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_29.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_30.png)

1.  `tianyun` 和 `alice` 依然可以自由创建文件。
2.  现在，`alice` 尝试删除 `tianyun` 的文件时，操作会被系统拒绝。
    ```bash
    rm /home/music/tianyun.mp3
    # 输出：rm: cannot remove ‘tianyun.mp3’: Operation not permitted
    ```
3.  `alice` 仍然可以删除自己创建的文件 `alice.mp3`。

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_31.png)

这样，就实现了“公共区域，各自管理”的安全协作模式。

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_33.png)

## 粘滞位的典型应用场景

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_35.png)

粘滞位最常见的应用场景是系统的临时目录，例如 `/tmp` 和 `/var/tmp`。

以下是这些目录的特点：

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_37.png)

*   **权限开放**：任何用户和进程都可以在其中创建临时文件。
*   **需要粘滞位**：为了防止用户或进程无意或恶意删除他人的临时文件，这些目录默认都设置了粘滞位。
*   你可以通过命令验证：
    ```bash
    ls -ld /tmp
    # 输出中会包含 ‘drwxrwxrwt’，表示设置了粘滞位。
    ```

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_38.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_39.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_40.png)

## 总结

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_41.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_42.png)

本节课中我们一起学习了Linux的第三种特殊权限——粘滞位。

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_43.png)

*   我们了解到，仅凭对目录的写权限，用户就可以删除其中的任何文件，这存在安全隐患。
*   粘滞位（`t`）通过限制删除操作，规定目录中的文件只能被其所有者删除，从而解决了这个问题。
*   设置方法为 `chmod o+t <目录名>` 或 `chmod 1777 <目录名>`。
*   粘滞位是保障像 `/tmp` 这样的公共目录安全有序运行的关键机制。

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_45.png)

![](img/574a0ad9ca8e8104b92cfc17ab7873f4_46.png)

至此，我们已经完整介绍了Linux的三种特殊权限：SUID、SGID和粘滞位。它们扩展了基础权限模型，为系统管理和安全提供了更精细的控制能力。