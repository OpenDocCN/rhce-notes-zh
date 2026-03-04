# 尚观Linux视频教程RHCE精品课程：P8：RH033-ULE112-02-5-安装Windows和Linux双系统 🖥️➡️🐧

在本节课中，我们将要学习如何在同一台计算机上安装Windows和Linux双系统。我们将从启动引导的基本原理讲起，逐步了解双系统安装的流程、注意事项以及出现引导问题时的修复方法。内容力求简单直白，让初学者能够看懂。

## 概述：启动引导的基本原理

在开始安装双系统之前，我们需要先理解计算机是如何启动的。这个过程涉及到硬盘的特定区域和引导加载器。

计算机硬盘的最小单位是512字节的**扇区**。硬盘最开始的512字节非常重要，被称为**主引导记录**。

MBR的结构可以用以下公式表示：
```
MBR (512字节) = 引导代码 (446字节) + 分区表 (64字节) + 结束标志 (0x55AA, 2字节)
```

*   **引导代码 (446字节)**：这是系统启动时最先执行的代码。
*   **分区表 (64字节)**：记录硬盘的分区信息。
*   **结束标志 (2字节)**：固定为`0x55AA`，用于标识这是一个有效的MBR。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_1.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_3.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_5.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_7.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_9.png)

当只安装Windows时，MBR中的引导代码会自动跳转到被标记为“激活”的主分区（通常是C盘）。该分区开头也有一个引导记录，称为**分区引导记录**，它会进一步加载Windows的启动管理器，最终启动Windows内核。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_11.png)

上一节我们介绍了单系统启动的原理，本节中我们来看看安装双系统时会发生什么变化。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_13.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_15.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_17.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_19.png)

## 双系统安装流程与原理

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_21.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_23.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_25.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_27.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_29.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_31.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_33.png)

安装Windows和Linux双系统的标准流程是：先安装Windows，再安装Linux。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_35.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_37.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_39.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_40.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_41.png)

以下是其核心原理和步骤：

1.  **先安装Windows**：此过程会按照上述单系统流程，将Windows的引导信息写入MBR和C盘的PBR。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_43.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_45.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_47.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_48.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_50.png)

2.  **为Linux准备空间**：在安装Linux之前，你需要在硬盘上为它准备一块专属空间。这有两种常见方式：
    *   利用硬盘上**未分配的空间**。
    *   删除一个已有的、不重要的分区（如D盘），从而获得空闲空间。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_52.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_54.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_55.png)

3.  **再安装Linux**：在安装Linux（如Red Hat Enterprise Linux, CentOS）时，安装程序会检测到已存在的Windows。当你将Linux安装到准备好的空闲空间时，其引导加载器（通常是 **GRUB**）会**替换**掉MBR中原有的Windows引导代码。

4.  **GRUB接管引导**：GRUB被写入MBR后，启动时就不再是自动跳转到Windows，而是显示一个菜单，让用户选择启动哪个系统。其配置文件（通常是 `/boot/grub/grub.conf`）中会包含类似以下的条目：

    ```bash
    title Windows XP
        rootnoverify (hd0,0)
        chainloader +1

    title CentOS
        root (hd0,4)
        kernel /vmlinuz-2.6.18 ro root=LABEL=/
        initrd /initrd-2.6.18.img
    ```
    *   选择“Windows”时，GRUB执行`chainloader +1`命令，将控制权交给第一块硬盘第一个分区的PBR（即Windows的引导），从而启动Windows。
    *   选择“Linux”时，GRUB会直接加载Linux内核文件（`vmlinuz-*`）和初始内存盘（`initrd-*.img`），从而启动Linux。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_57.png)

因此，只要为Linux准备好独立的磁盘空间，按照先Windows后Linux的顺序安装，通常就能顺利实现双引导。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_59.png)

## 引导修复方法

如果引导出现问题（例如后安装的系统覆盖了先安装系统的引导），我们可以进行修复。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_61.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_63.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_65.png)

### Linux引导修复（当Windows覆盖了GRUB）

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_67.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_69.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_70.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_72.png)

如果你先安装了Linux，后安装Windows导致无法进入Linux，可以使用Linux安装光盘进入救援模式修复GRUB。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_74.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_76.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_78.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_80.png)

以下是修复步骤：
1.  用Linux安装光盘启动，在引导提示符输入 `linux rescue` 进入救援模式。
2.  按照提示选择语言、键盘，通常不需要启动网络。
3.  救援模式会尝试查找并挂载你硬盘上的Linux系统到 `/mnt/sysimage`。
4.  在出现的命令行中，执行以下命令切换根目录并重新安装GRUB到硬盘主引导记录：
    ```bash
    chroot /mnt/sysimage
    grub-install /dev/sda
    ```
    *   `/dev/sda` 通常代表第一块硬盘，请根据实际情况调整。
5.  退出并重启，GRUB引导菜单应该恢复。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_82.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_84.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_86.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_88.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_90.png)

### Windows引导修复（当GRUB覆盖了Windows引导）

如果你希望恢复为只启动Windows，可以使用Windows安装光盘修复其引导。

以下是修复步骤（以Windows XP为例）：
1.  用Windows安装光盘启动，在出现安装选项时按 `R` 进入“故障恢复控制台”。
2.  输入管理员密码登录。
3.  执行以下命令修复主引导记录和引导扇区：
    ```bash
    fixmbr
    fixboot C:
    ```
4.  退出并重启，计算机将直接启动Windows。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_92.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_94.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_96.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_98.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_100.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_102.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_104.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_106.png)

## 多Linux系统共存

GRUB的强大之处在于它可以引导多个操作系统，包括多个不同的Linux发行版。

其原理很简单：每个Linux系统都有自己的内核文件（`vmlinuz-*`）和根分区。你只需要在GRUB的配置文件（`grub.conf`）中为每个系统添加一个对应的`title`段落，指定正确的内核文件路径和根分区位置即可。

例如，在已有Windows和CentOS的电脑上再安装一个Fedora，你可以在`grub.conf`中添加：
```bash
title Fedora
    root (hd0,5)
    kernel /vmlinuz-fedora ro root=/dev/sda6
    initrd /initrd-fedora.img
```
这样，在启动时GRUB菜单就会出现第三个选项“Fedora”。

## 高级引导技巧（简介）

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_108.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_109.png)

引导加载器之间可以互相“链式加载”。这意味着你可以用Windows的引导加载器来启动GRUB，或者用一个Linux发行版的GRUB来启动另一个Linux发行版的GRUB。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_111.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_113.png)

这通常涉及更复杂的操作，例如：
*   将GRUB安装到某个**分区**的引导扇区（PBR），而不是整个硬盘的MBR。
*   在Windows的引导配置文件（如`boot.ini`）或Linux的`grub.conf`中，使用`chainloader`命令指向另一个分区的引导扇区。

这些技巧提供了更大的灵活性，但操作也更为复杂，更适合高级用户探索。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_115.png)

## 总结

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_117.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_119.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_120.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_122.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_124.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_126.png)

本节课中我们一起学习了Windows和Linux双系统的安装与引导原理。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_128.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_130.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_132.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_134.png)

*   **核心原则**：建议先安装Windows，后安装Linux，并为Linux准备独立的磁盘空间。
*   **引导核心**：Linux的GRUB引导加载器会接管MBR，并提供菜单供用户选择启动哪个系统。
*   **问题修复**：无论是Linux的GRUB被覆盖，还是想恢复Windows单引导，都可以通过系统安装光盘进入修复模式进行恢复。
*   **灵活扩展**：GRUB支持引导多个操作系统，包括多个Linux发行版共存。

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_136.png)

![](img/8fe827d8c07ed42c06328ae0cdc3b47b_138.png)

理解这些基本原理后，你就能从容地规划和管理计算机上的多系统环境了。