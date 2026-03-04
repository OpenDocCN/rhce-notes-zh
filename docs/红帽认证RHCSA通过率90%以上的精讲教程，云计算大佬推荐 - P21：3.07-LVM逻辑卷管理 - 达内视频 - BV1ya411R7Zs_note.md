# RHCSA精讲教程：P21：3.07-LVM逻辑卷管理

![](img/e832f4bc4fba7df9b0a497393f106399_0.png)

![](img/e832f4bc4fba7df9b0a497393f106399_2.png)

![](img/e832f4bc4fba7df9b0a497393f106399_3.png)

![](img/e832f4bc4fba7df9b0a497393f106399_5.png)

![](img/e832f4bc4fba7df9b0a497393f106399_7.png)

![](img/e832f4bc4fba7df9b0a497393f106399_9.png)

![](img/e832f4bc4fba7df9b0a497393f106399_11.png)

![](img/e832f4bc4fba7df9b0a497393f106399_13.png)

在本节课中，我们将要学习Linux系统中一个非常重要的存储管理机制——LVM逻辑卷管理。我们将了解它的核心概念、优势以及如何通过具体命令来创建、扩展和管理逻辑卷。

![](img/e832f4bc4fba7df9b0a497393f106399_15.png)

![](img/e832f4bc4fba7df9b0a497393f106399_17.png)

![](img/e832f4bc4fba7df9b0a497393f106399_19.png)

## 概述：什么是LVM？

![](img/e832f4bc4fba7df9b0a497393f106399_21.png)

上一节我们介绍了交换分区的管理，本节中我们来看看逻辑卷。传统的磁盘使用方式是直接对物理磁盘进行分区、格式化、挂载。这种方式存在一些不足，例如分区大小固定，难以调整；多个磁盘无法灵活地合并使用。

![](img/e832f4bc4fba7df9b0a497393f106399_23.png)

LVM（Logical Volume Manager，逻辑卷管理器）机制就是为了解决这些问题而设计的。它在物理存储设备（磁盘或分区）和文件系统之间增加了一个抽象层，带来了两大核心优势：**化零为整**和**动态伸缩**。

## LVM的核心概念与架构

LVM通过三个层次来管理存储：

1.  **物理卷**：这是LVM的基础，可以是整个物理磁盘（如`/dev/vdb`），也可以是磁盘上的一个分区（如`/dev/vdb1`）。管理命令以`pv`开头。
2.  **卷组**：由一个或多个物理卷组合而成，形成一个大的、统一的“存储池”。管理命令以`vg`开头。
3.  **逻辑卷**：从卷组中划分出来的、可供用户格式化并使用的“虚拟分区”。管理命令以`lv`开头。

![](img/e832f4bc4fba7df9b0a497393f106399_25.png)

![](img/e832f4bc4fba7df9b0a497393f106399_27.png)

它们的关系可以用一个简单的流程表示：
**物理磁盘/分区 (PV) -> 组合成存储池 (VG) -> 划分出虚拟分区 (LV) -> 格式化并挂载使用**

![](img/e832f4bc4fba7df9b0a497393f106399_29.png)

![](img/e832f4bc4fba7df9b0a497393f106399_31.png)

## LVM管理工具简介

![](img/e832f4bc4fba7df9b0a497393f106399_33.png)

![](img/e832f4bc4fba7df9b0a497393f106399_35.png)

![](img/e832f4bc4fba7df9b0a497393f106399_37.png)

以下是管理LVM各层次的主要命令：

![](img/e832f4bc4fba7df9b0a497393f106399_39.png)

![](img/e832f4bc4fba7df9b0a497393f106399_41.png)

![](img/e832f4bc4fba7df9b0a497393f106399_43.png)

*   **物理卷管理**
    *   `pvs` / `pvdisplay`：扫描或显示物理卷信息。
    *   `pvcreate`：将块设备初始化为物理卷。

![](img/e832f4bc4fba7df9b0a497393f106399_45.png)

![](img/e832f4bc4fba7df9b0a497393f106399_47.png)

![](img/e832f4bc4fba7df9b0a497393f106399_49.png)

![](img/e832f4bc4fba7df9b0a497393f106399_51.png)

*   **卷组管理**
    *   `vgs` / `vgdisplay`：扫描或显示卷组信息。
    *   `vgcreate`：创建一个新的卷组。
    *   `vgremove`：删除一个卷组。

![](img/e832f4bc4fba7df9b0a497393f106399_53.png)

![](img/e832f4bc4fba7df9b0a497393f106399_55.png)

*   **逻辑卷管理**
    *   `lvs` / `lvdisplay`：扫描或显示逻辑卷信息。
    *   `lvcreate`：创建一个新的逻辑卷。
    *   `lvextend`：扩展一个逻辑卷的大小。
    *   `lvremove`：删除一个逻辑卷。

![](img/e832f4bc4fba7df9b0a497393f106399_57.png)

![](img/e832f4bc4fba7df9b0a497393f106399_59.png)

如果不记得具体命令，可以输入`pv`、`vg`或`lv`后按`Tab`键查看所有相关命令。

## 实践一：调整现有逻辑卷大小

现在，我们来看第一道实践题目：将一个名为`vo`的现有逻辑卷大小调整到300MB。

### 操作步骤

![](img/e832f4bc4fba7df9b0a497393f106399_61.png)

![](img/e832f4bc4fba7df9b0a497393f106399_63.png)

![](img/e832f4bc4fba7df9b0a497393f106399_65.png)

1.  **查看逻辑卷信息**
    首先，我们需要确认`vo`逻辑卷的当前状态和所在位置。
    ```bash
    lvscan
    # 或使用 lvs
    ```
    命令会显示类似`/dev/test/vo`的路径，其中`test`是卷组名，`vo`是逻辑卷名。

![](img/e832f4bc4fba7df9b0a497393f106399_67.png)

![](img/e832f4bc4fba7df9b0a497393f106399_69.png)

![](img/e832f4bc4fba7df9b0a497393f106399_71.png)

![](img/e832f4bc4fba7df9b0a497393f106399_73.png)

2.  **扩展逻辑卷容量**
    使用`lvextend`命令将逻辑卷扩展到300MB。这里使用`-L`选项指定新的总大小。
    ```bash
    lvextend -L 300M /dev/test/vo
    ```
    执行成功后，可以再次运行`lvscan`确认容量已变为300MB。

![](img/e832f4bc4fba7df9b0a497393f106399_75.png)

![](img/e832f4bc4fba7df9b0a497393f106399_76.png)

3.  **通知系统文件系统已扩容**
    逻辑卷的物理空间虽然扩大了，但操作系统内核和文件系统并不知道。我们需要根据文件系统类型，使用不同的工具来通知系统。
    *   **首先检查文件系统类型：**
        ```bash
        blkid /dev/test/vo
        ```
    *   **如果是XFS文件系统（常见于RHEL 7/8/9）：**
        使用`xfs_growfs`命令，其操作对象是挂载点。
        ```bash
        xfs_growfs /挂载点
        ```
        如果该逻辑卷尚未挂载，需要先创建一个目录并挂载，再对挂载点执行此命令。
    *   **如果是EXT2/3/4文件系统：**
        使用`resize2fs`命令，其操作对象是逻辑卷设备本身。
        ```bash
        resize2fs /dev/test/vo
        ```

![](img/e832f4bc4fba7df9b0a497393f106399_78.png)

![](img/e832f4bc4fba7df9b0a497393f106399_80.png)

完成以上步骤后，使用`df -h`命令查看挂载点，确认可用空间已增加。

![](img/e832f4bc4fba7df9b0a497393f106399_82.png)

![](img/e832f4bc4fba7df9b0a497393f106399_84.png)

## 实践二：创建新的逻辑卷

![](img/e832f4bc4fba7df9b0a497393f106399_86.png)

接下来，我们完成一个更完整的任务：从零开始创建一个符合要求的逻辑卷。

![](img/e832f4bc4fba7df9b0a497393f106399_88.png)

![](img/e832f4bc4fba7df9b0a497393f106399_90.png)

![](img/e832f4bc4fba7df9b0a497393f106399_92.png)

### 题目要求
1.  创建一个名为`myvg`的卷组，其物理扩展块（PE）大小为16MB。
2.  在`myvg`卷组中创建一个名为`mylv`的逻辑卷，大小为50个PE（即 50 * 16MB = 800MB）。
3.  使用VFAT文件系统格式化这个逻辑卷。
4.  将其挂载到`/mnt/data`目录，并配置为开机自动挂载。

![](img/e832f4bc4fba7df9b0a497393f106399_94.png)

![](img/e832f4bc4fba7df9b0a497393f106399_96.png)

![](img/e832f4bc4fba7df9b0a497393f106399_98.png)

### 操作步骤

![](img/e832f4bc4fba7df9b0a497393f106399_100.png)

![](img/e832f4bc4fba7df9b0a497393f106399_101.png)

![](img/e832f4bc4fba7df9b0a497393f106399_103.png)

1.  **创建卷组并指定PE大小**
    使用`vgcreate`命令，通过`-s`选项指定物理扩展块（PE）大小为16MB。这里我们使用事先准备好的空闲分区`/dev/vdb3`。
    ```bash
    vgcreate -s 16M myvg /dev/vdb3
    ```
    创建后可用`vgdisplay myvg`查看，确认`PE Size`为16.00 MiB。

![](img/e832f4bc4fba7df9b0a497393f106399_105.png)

![](img/e832f4bc4fba7df9b0a497393f106399_106.png)

![](img/e832f4bc4fba7df9b0a497393f106399_108.png)

![](img/e832f4bc4fba7df9b0a497393f106399_110.png)

2.  **创建逻辑卷**
    使用`lvcreate`命令创建逻辑卷。`-n`指定逻辑卷名，`-l`指定分配多少个PE（这里为50个）。
    ```bash
    lvcreate -l 50 -n mylv myvg
    ```
    也可以使用`-L 800M`直接指定大小。创建后可用`lvs`或`lvdisplay`查看。

![](img/e832f4bc4fba7df9b0a497393f106399_112.png)

![](img/e832f4bc4fba7df9b0a497393f106399_114.png)

3.  **格式化逻辑卷**
    使用`mkfs.vfat`命令将逻辑卷格式化为VFAT文件系统。
    ```bash
    mkfs.vfat /dev/myvg/mylv
    ```

![](img/e832f4bc4fba7df9b0a497393f106399_116.png)

![](img/e832f4bc4fba7df9b0a497393f106399_118.png)

![](img/e832f4bc4fba7df9b0a497393f106399_120.png)

4.  **配置开机自动挂载**
    *   首先创建挂载点目录：
        ```bash
        mkdir -p /mnt/data
        ```
    *   编辑`/etc/fstab`文件，在末尾添加一行配置：
        ```
        /dev/myvg/mylv /mnt/data vfat defaults 0 0
        ```
    *   执行`mount -a`命令，立即挂载`/etc/fstab`中所有配置好的设备。
    *   最后使用`df -h /mnt/data`命令验证是否挂载成功。

![](img/e832f4bc4fba7df9b0a497393f106399_122.png)

![](img/e832f4bc4fba7df9b0a497393f106399_123.png)

![](img/e832f4bc4fba7df9b0a497393f106399_125.png)

![](img/e832f4bc4fba7df9b0a497393f106399_127.png)

## 总结

本节课中我们一起学习了LVM逻辑卷管理的核心知识。我们首先了解了LVM相比传统磁盘管理的优势：**化零为整**和**动态伸缩**。然后，我们掌握了LVM的三个核心概念：物理卷（PV）、卷组（VG）和逻辑卷（LV），以及管理它们的基本命令。

![](img/e832f4bc4fba7df9b0a497393f106399_129.png)

![](img/e832f4bc4fba7df9b0a497393f106399_131.png)

通过两道实践题目，我们具体演练了如何**扩展一个现有逻辑卷**，以及如何**从零开始创建并挂载一个新的逻辑卷**。关键点在于扩展逻辑卷后，务必根据文件系统类型（XFS或EXT4）使用对应的命令（`xfs_growfs`或`resize2fs`）来让系统识别新的空间。

![](img/e832f4bc4fba7df9b0a497393f106399_133.png)

![](img/e832f4bc4fba7df9b0a497393f106399_135.png)

![](img/e832f4bc4fba7df9b0a497393f106399_137.png)

![](img/e832f4bc4fba7df9b0a497393f106399_138.png)

![](img/e832f4bc4fba7df9b0a497393f106399_139.png)

熟练掌握LVM将使你能够更灵活、高效地管理Linux服务器的存储资源。