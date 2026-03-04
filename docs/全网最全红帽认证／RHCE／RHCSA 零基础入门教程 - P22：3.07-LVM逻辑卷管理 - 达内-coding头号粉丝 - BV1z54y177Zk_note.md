# 全网最全红帽认证／RHCE／RHCSA 零基础入门教程：P22：3.07-LVM逻辑卷管理

![](img/fec02fb9a8cecef20f227c71f19df186_0.png)

![](img/fec02fb9a8cecef20f227c71f19df186_2.png)

![](img/fec02fb9a8cecef20f227c71f19df186_3.png)

![](img/fec02fb9a8cecef20f227c71f19df186_5.png)

![](img/fec02fb9a8cecef20f227c71f19df186_7.png)

![](img/fec02fb9a8cecef20f227c71f19df186_9.png)

![](img/fec02fb9a8cecef20f227c71f19df186_11.png)

![](img/fec02fb9a8cecef20f227c71f19df186_13.png)

在本节课中，我们将要学习Linux系统中一个非常重要的存储管理机制——LVM（逻辑卷管理）。我们将了解LVM的基本概念、优势以及如何创建、调整和管理逻辑卷。

![](img/fec02fb9a8cecef20f227c71f19df186_15.png)

![](img/fec02fb9a8cecef20f227c71f19df186_17.png)

## 概述：什么是LVM？

![](img/fec02fb9a8cecef20f227c71f19df186_19.png)

上一节我们介绍了交换分区的管理，本节中我们来看看逻辑卷。传统的磁盘使用方式是：分区、格式化、挂载。这种方式存在一些不足，例如分区大小固定，难以调整；单个分区大小受限于物理磁盘容量。

![](img/fec02fb9a8cecef20f227c71f19df186_21.png)

![](img/fec02fb9a8cecef20f227c71f19df186_23.png)

LVM（Logical Volume Manager，逻辑卷管理）机制就是为了解决这些问题而设计的。它在物理存储设备（磁盘或分区）和文件系统之间增加了一个抽象层，带来了两大核心优势：**化零为整**和**动态伸缩**。

## LVM的核心概念与优势

LVM的核心思想是将多个物理存储设备组合成一个大的“存储池”（卷组），然后从这个池中灵活地划分出“逻辑分区”（逻辑卷）供系统使用。

以下是LVM的三个核心层次：

1.  **物理卷（PV， Physical Volume）**：这是LVM的基础，可以是整个硬盘（如`/dev/vdb`）或硬盘分区（如`/dev/vdb1`）。它们是被LVM管理的原始存储块。
2.  **卷组（VG， Volume Group）**：由一个或多个物理卷组成，可以看作是一个大的“存储池”。这是化零为整的关键，例如将4块500GB的硬盘组合成一个2TB的卷组。
3.  **逻辑卷（LV， Logical Volume）**：从卷组中划分出来的逻辑存储单元，相当于传统意义上的“分区”。用户可以对其进行格式化并挂载使用。逻辑卷的大小可以动态调整，实现了动态伸缩。

![](img/fec02fb9a8cecef20f227c71f19df186_25.png)

![](img/fec02fb9a8cecef20f227c71f19df186_27.png)

**公式表示**：`多个PV` -> `组成一个VG` -> `从VG中划分出LV`

![](img/fec02fb9a8cecef20f227c71f19df186_29.png)

![](img/fec02fb9a8cecef20f227c71f19df186_31.png)

## LVM管理工具简介

![](img/fec02fb9a8cecef20f227c71f19df186_33.png)

![](img/fec02fb9a8cecef20f227c71f19df186_35.png)

![](img/fec02fb9a8cecef20f227c71f19df186_37.png)

![](img/fec02fb9a8cecef20f227c71f19df186_39.png)

LVM提供了三组命令来分别管理物理卷、卷组和逻辑卷。

![](img/fec02fb9a8cecef20f227c71f19df186_41.png)

![](img/fec02fb9a8cecef20f227c71f19df186_43.png)

![](img/fec02fb9a8cecef20f227c71f19df186_45.png)

![](img/fec02fb9a8cecef20f227c71f19df186_47.png)

![](img/fec02fb9a8cecef20f227c71f19df186_49.png)

以下是常用的LVM管理命令：

![](img/fec02fb9a8cecef20f227c71f19df186_51.png)

*   **物理卷（PV）管理**：
    *   `pvs` / `pvdisplay`：扫描或显示物理卷信息。
    *   `pvcreate`：将块设备初始化为物理卷。
*   **卷组（VG）管理**：
    *   `vgs` / `vgdisplay`：扫描或显示卷组信息。
    *   `vgcreate`：创建新的卷组。
    *   `vgextend`：向卷组中添加新的物理卷以扩展其容量。
*   **逻辑卷（LV）管理**：
    *   `lvs` / `lvdisplay`：扫描或显示逻辑卷信息。
    *   `lvcreate`：创建新的逻辑卷。
    *   `lvextend`：扩展逻辑卷的容量。
    *   `lvremove`：删除逻辑卷。

![](img/fec02fb9a8cecef20f227c71f19df186_53.png)

![](img/fec02fb9a8cecef20f227c71f19df186_55.png)

如果记不住具体命令，可以只记住`pv`， `vg`， `lv`这几个前缀，然后按`Tab`键查看补全选项。

![](img/fec02fb9a8cecef20f227c71f19df186_57.png)

![](img/fec02fb9a8cecef20f227c71f19df186_59.png)

## 实践一：调整现有逻辑卷大小

现在，我们来看第一道实践题目：调整一个已存在的逻辑卷的大小。

假设系统中已有一个名为`vo`的逻辑卷，我们需要将其大小扩展到300MB。

操作步骤如下：

![](img/fec02fb9a8cecef20f227c71f19df186_61.png)

![](img/fec02fb9a8cecef20f227c71f19df186_63.png)

1.  **查看逻辑卷信息**：首先确认`vo`逻辑卷的当前状态和路径。
    ```bash
    lvs
    # 或
    lvdisplay
    ```
    找到类似`/dev/test/vo`的设备路径（其中`test`是卷组名）。

![](img/fec02fb9a8cecef20f227c71f19df186_65.png)

![](img/fec02fb9a8cecef20f227c71f19df186_67.png)

![](img/fec02fb9a8cecef20f227c71f19df186_69.png)

![](img/fec02fb9a8cecef20f227c71f19df186_71.png)

2.  **扩展逻辑卷容量**：使用`lvextend`命令将逻辑卷扩展到指定大小。
    ```bash
    lvextend -L 300M /dev/test/vo
    ```
    参数`-L 300M`指定了扩展后的**新大小**为300MB。如果要增加100MB，可以使用`-L +100M`。

![](img/fec02fb9a8cecef20f227c71f19df186_73.png)

![](img/fec02fb9a8cecef20f227c71f19df186_75.png)

![](img/fec02fb9a8cecef20f227c71f19df186_76.png)

3.  **通知系统文件系统已扩容**：扩容物理空间后，需要让文件系统识别新的容量。首先检查文件系统类型。
    ```bash
    blkid /dev/test/vo
    ```
    *   **如果文件系统是 XFS**（常见于RHEL 7/8），使用以下命令：
        ```bash
        xfs_growfs /挂载点
        ```
        例如，如果`vo`挂载在`/vo`目录，则执行`xfs_growfs /vo`。如果未挂载，需要先挂载到一个临时目录再执行此命令。
    *   **如果文件系统是 EXT2/3/4**，使用以下命令：
        ```bash
        resize2fs /dev/test/vo
        ```

![](img/fec02fb9a8cecef20f227c71f19df186_78.png)

4.  **验证扩容结果**：再次使用`lvs`或`df -h`命令查看逻辑卷大小和文件系统使用情况，确认扩容成功。

![](img/fec02fb9a8cecef20f227c71f19df186_80.png)

![](img/fec02fb9a8cecef20f227c71f19df186_82.png)

![](img/fec02fb9a8cecef20f227c71f19df186_84.png)

**关键点总结**：逻辑卷扩容分为两步，第一步用`lvextend`扩展LV的物理边界，第二步用文件系统特定命令（`xfs_growfs`或`resize2fs`）扩展其文件系统边界。

![](img/fec02fb9a8cecef20f227c71f19df186_86.png)

## 实践二：创建新的逻辑卷

![](img/fec02fb9a8cecef20f227c71f19df186_88.png)

![](img/fec02fb9a8cecef20f227c71f19df186_90.png)

接下来，我们完成第二道题目：从零开始创建一个新的逻辑卷。

![](img/fec02fb9a8cecef20f227c71f19df186_92.png)

![](img/fec02fb9a8cecef20f227c71f19df186_94.png)

![](img/fec02fb9a8cecef20f227c71f19df186_96.png)

题目要求：创建一个名为`myLV`的逻辑卷，它属于名为`myVG`的卷组，大小为50个扩展单元（PE），且该卷组的PE大小需设置为16MB。最后将其格式化为VFAT文件系统，并挂载到`/mnt/data`目录实现开机自动挂载。

![](img/fec02fb9a8cecef20f227c71f19df186_98.png)

![](img/fec02fb9a8cecef20f227c71f19df186_100.png)

![](img/fec02fb9a8cecef20f227c71f19df186_101.png)

操作步骤如下：

![](img/fec02fb9a8cecef20f227c71f19df186_103.png)

1.  **准备物理存储**：假设我们已经准备好一个空闲分区（例如`/dev/vdb3`）作为物理卷。无需手动执行`pvcreate`，创建卷组时会自动处理。

![](img/fec02fb9a8cecef20f227c71f19df186_105.png)

![](img/fec02fb9a8cecef20f227c71f19df186_106.png)

![](img/fec02fb9a8cecef20f227c71f19df186_108.png)

![](img/fec02fb9a8cecef20f227c71f19df186_110.png)

2.  **创建卷组并指定PE大小**：创建卷组`myVG`，并指定物理扩展单元（PE）大小为16MB。
    ```bash
    vgcreate -s 16M myVG /dev/vdb3
    ```
    参数`-s 16M`设置了PE大小。可以使用`vgdisplay myVG`查看确认`PE Size`是否为16.00 MiB。

![](img/fec02fb9a8cecef20f227c71f19df186_112.png)

![](img/fec02fb9a8cecef20f227c71f19df186_114.png)

3.  **创建逻辑卷**：在卷组`myVG`中创建名为`myLV`的逻辑卷。
    ```bash
    lvcreate -L 800M -n myLV myVG
    ```
    *   `-L 800M`：指定逻辑卷大小为800MB（50 PE * 16MB）。
    *   `-n myLV`：指定逻辑卷名称为`myLV`。
    *   也可以使用`-l 50`来直接指定PE数量：`lvcreate -l 50 -n myLV myVG`。

![](img/fec02fb9a8cecef20f227c71f19df186_116.png)

![](img/fec02fb9a8cecef20f227c71f19df186_118.png)

![](img/fec02fb9a8cecef20f227c71f19df186_120.png)

![](img/fec02fb9a8cecef20f227c71f19df186_122.png)

4.  **格式化逻辑卷**：将新创建的逻辑卷格式化为VFAT文件系统。
    ```bash
    mkfs.vfat /dev/myVG/myLV
    ```

![](img/fec02fb9a8cecef20f227c71f19df186_123.png)

![](img/fec02fb9a8cecef20f227c71f19df186_125.png)

![](img/fec02fb9a8cecef20f227c71f19df186_127.png)

5.  **配置开机自动挂载**：
    *   创建挂载点目录：`mkdir -p /mnt/data`
    *   编辑`/etc/fstab`文件，在末尾添加一行配置：
        ```
        /dev/myVG/myLV /mnt/data vfat defaults 0 0
        ```
    *   执行`mount -a`命令，立即挂载`/etc/fstab`中所有未挂载的设备，并检查是否有错误。
    *   使用`df -hT`命令查看`/mnt/data`是否成功挂载。

![](img/fec02fb9a8cecef20f227c71f19df186_129.png)

## 总结

![](img/fec02fb9a8cecef20f227c71f19df186_131.png)

本节课中我们一起学习了LVM逻辑卷管理的核心知识。我们首先了解了传统磁盘管理的局限性以及LVM**化零为整**和**动态伸缩**的两大优势。接着，我们学习了PV、VG、LV这三个核心概念及其管理工具。

![](img/fec02fb9a8cecef20f227c71f19df186_133.png)

![](img/fec02fb9a8cecef20f227c71f19df186_135.png)

![](img/fec02fb9a8cecef20f227c71f19df186_137.png)

![](img/fec02fb9a8cecef20f227c71f19df186_138.png)

![](img/fec02fb9a8cecef20f227c71f19df186_139.png)

通过两个实践练习，我们掌握了如何**调整现有逻辑卷的大小**（使用`lvextend`并结合`xfs_growfs`或`resize2fs`），以及如何**从零开始创建新的逻辑卷**（涉及`vgcreate`， `lvcreate`， `mkfs`， 编辑`/etc/fstab`等操作）。熟练掌握LVM对于灵活管理Linux服务器存储空间至关重要。