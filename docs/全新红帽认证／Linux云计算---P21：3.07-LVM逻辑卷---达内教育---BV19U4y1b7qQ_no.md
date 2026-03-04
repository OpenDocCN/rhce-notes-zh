# Linux存储管理：3.07：LVM逻辑卷管理 📚

![](img/23eb5eb67f206553a7d1eab6239e7b7e_0.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_2.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_3.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_5.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_7.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_9.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_11.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_13.png)

在本节课中，我们将要学习Linux中的LVM（逻辑卷管理）机制。这是一种比传统磁盘分区更灵活、更强大的存储管理方式。我们将了解其核心概念、优势以及如何通过具体命令创建、扩展和管理逻辑卷。

![](img/23eb5eb67f206553a7d1eab6239e7b7e_15.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_17.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_19.png)

## 概述：什么是LVM？🤔

![](img/23eb5eb67f206553a7d1eab6239e7b7e_21.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_23.png)

上一节我们介绍了交换分区的管理，本节中我们来看看逻辑卷。传统的磁盘使用方式是：分区 -> 格式化 -> 挂载 -> 访问。这种方式存在一些不足，例如分区大小固定，难以调整；多个磁盘无法灵活地合并使用。

LVM（Logical Volume Manager，逻辑卷管理）机制在物理存储设备和文件系统之间增加了一个抽象层。它带来了两大核心优势：
1.  **化零为整**：将多个物理磁盘或分区组合成一个大的、统一的“虚拟磁盘”（卷组）。
2.  **动态伸缩**：可以轻松地调整逻辑卷的大小，而无需重新格式化或移动数据。

## LVM核心概念与架构 🏗️

LVM通过三个核心层次来管理存储：

1.  **物理卷（PV， Physical Volume）**：这是LVM的基本存储单元，可以是整个硬盘（如 `/dev/vdb`）或硬盘分区（如 `/dev/vdb1`）。
2.  **卷组（VG， Volume Group）**：由一个或多个物理卷组成，形成一个大的存储池。你可以把它理解为一个“虚拟的大硬盘”。
3.  **逻辑卷（LV， Logical Volume）**：从卷组中划分出来的存储空间，相当于传统意义上的“分区”。我们最终是在逻辑卷上创建文件系统并挂载使用。

![](img/23eb5eb67f206553a7d1eab6239e7b7e_25.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_27.png)

其工作流程可以概括为：**PV -> VG -> LV -> 格式化 -> 挂载**。

![](img/23eb5eb67f206553a7d1eab6239e7b7e_29.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_31.png)

## LVM管理工具集 🔧

![](img/23eb5eb67f206553a7d1eab6239e7b7e_33.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_35.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_37.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_39.png)

以下是管理LVM各层次的主要命令：

![](img/23eb5eb67f206553a7d1eab6239e7b7e_41.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_43.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_45.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_47.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_49.png)

**管理物理卷（PV）**
*   `pvs` / `pvdisplay`：扫描或显示物理卷信息。
*   `pvcreate`：将块设备初始化为物理卷。

![](img/23eb5eb67f206553a7d1eab6239e7b7e_51.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_53.png)

**管理卷组（VG）**
*   `vgs` / `vgdisplay`：扫描或显示卷组信息。
*   `vgcreate`：创建一个新的卷组。
*   `vgextend`：向卷组中添加物理卷以扩展其容量。
*   `vgremove`：删除卷组。

![](img/23eb5eb67f206553a7d1eab6239e7b7e_55.png)

**管理逻辑卷（LV）**
*   `lvs` / `lvdisplay`：扫描或显示逻辑卷信息。
*   `lvcreate`：在卷组中创建新的逻辑卷。
*   `lvextend`：扩展逻辑卷的容量。
*   `lvremove`：删除逻辑卷。

![](img/23eb5eb67f206553a7d1eab6239e7b7e_57.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_59.png)

## 实践一：调整现有逻辑卷大小 📈

假设系统中已有一个名为 `vo` 的逻辑卷，我们需要将其大小扩展到300MB。

**操作步骤如下：**

1.  **检查当前逻辑卷状态**
    首先，使用 `lvs` 命令确认逻辑卷 `vo` 的当前大小和所属卷组。
    ```bash
    lvs
    ```

![](img/23eb5eb67f206553a7d1eab6239e7b7e_61.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_63.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_65.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_67.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_69.png)

2.  **扩展逻辑卷容量**
    使用 `lvextend` 命令将逻辑卷扩展到指定大小。`-L` 参数用于指定新的总大小。
    ```bash
    lvextend -L 300M /dev/test/vo
    ```
    *提示：也可以使用 `-L +100M` 来增加100MB。*

![](img/23eb5eb67f206553a7d1eab6239e7b7e_71.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_73.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_75.png)

3.  **通知文件系统容量已变更**
    逻辑卷的物理空间扩展后，其上的文件系统并不知道这一变化。我们需要根据文件系统类型，使用不同的命令来“通知”系统。
    *   首先，使用 `blkid` 命令查看逻辑卷的文件系统类型。
        ```bash
        blkid /dev/test/vo
        ```
    *   如果文件系统是 **XFS**（常见于RHEL/CentOS 7+），使用 `xfs_growfs` 命令，并指定挂载点。
        ```bash
        xfs_growfs /vo
        ```
    *   如果文件系统是 **EXT2/3/4**，则使用 `resize2fs` 命令，并指定逻辑卷设备。
        ```bash
        resize2fs /dev/test/vo
        ```

![](img/23eb5eb67f206553a7d1eab6239e7b7e_76.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_78.png)

4.  **验证扩展结果**
    再次使用 `lvs` 和 `df -h` 命令，确认逻辑卷和文件系统的容量已成功更新。

![](img/23eb5eb67f206553a7d1eab6239e7b7e_80.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_82.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_84.png)

## 实践二：创建新的逻辑卷 🆕

![](img/23eb5eb67f206553a7d1eab6239e7b7e_86.png)

题目要求：使用 `/dev/vdb3` 分区创建一个名为 `myvg` 的卷组，其扩展单元（PE）大小为16MB。然后在该卷组中创建一个名为 `mylv` 的逻辑卷，大小为50个PE（即800MB）。最后将其格式化为VFAT文件系统，并挂载到 `/mnt/data` 目录，实现开机自动挂载。

![](img/23eb5eb67f206553a7d1eab6239e7b7e_88.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_90.png)

**操作步骤如下：**

![](img/23eb5eb67f206553a7d1eab6239e7b7e_92.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_94.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_96.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_98.png)

1.  **创建卷组（VG）并指定PE大小**
    使用 `vgcreate` 命令，`-s` 参数用于指定物理扩展块（PE）的大小。
    ```bash
    vgcreate -s 16M myvg /dev/vdb3
    ```
    使用 `vgdisplay myvg` 可以验证PE大小是否为16MB。

![](img/23eb5eb67f206553a7d1eab6239e7b7e_100.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_101.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_103.png)

2.  **创建逻辑卷（LV）**
    使用 `lvcreate` 命令创建逻辑卷。
    *   `-n` 指定逻辑卷名称。
    *   `-l` 指定分配多少个PE（或使用 `-L` 指定具体容量，如 `-L 800M`）。
    *   最后指定从哪个卷组创建。
    ```bash
    lvcreate -l 50 -n mylv myvg
    ```

![](img/23eb5eb67f206553a7d1eab6239e7b7e_105.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_106.png)

3.  **格式化逻辑卷**
    使用 `mkfs.vfat` 命令将逻辑卷格式化为VFAT文件系统。
    ```bash
    mkfs.vfat /dev/myvg/mylv
    ```

![](img/23eb5eb67f206553a7d1eab6239e7b7e_108.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_110.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_112.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_114.png)

4.  **配置开机自动挂载**
    *   首先，创建挂载点目录。
        ```bash
        mkdir -p /mnt/data
        ```
    *   编辑 `/etc/fstab` 文件，在末尾添加一行配置。
        ```bash
        vim /etc/fstab
        ```
        添加如下内容：
        ```
        /dev/myvg/mylv  /mnt/data  vfat  defaults  0 0
        ```
        *   第一列：设备（逻辑卷路径）。
        *   第二列：挂载点目录。
        *   第三列：文件系统类型。
        *   第四列：挂载参数（`defaults`）。
        *   第五、六列：dump备份和fsck检查选项（通常设为0）。

![](img/23eb5eb67f206553a7d1eab6239e7b7e_116.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_118.png)

5.  **测试挂载**
    执行 `mount -a` 命令，它会尝试挂载 `/etc/fstab` 中所有未挂载的设备。然后使用 `df -hT` 命令检查 `/mnt/data` 是否成功挂载。

![](img/23eb5eb67f206553a7d1eab6239e7b7e_120.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_122.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_123.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_125.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_127.png)

## 总结 📝

本节课中我们一起学习了LVM逻辑卷管理的核心知识。我们了解到LVM通过**物理卷（PV）、卷组（VG）、逻辑卷（LV）** 三层结构，实现了存储空间的**化零为整**和**动态伸缩**，极大地提升了磁盘管理的灵活性。

![](img/23eb5eb67f206553a7d1eab6239e7b7e_129.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_131.png)

我们重点实践了两个场景：
1.  **扩展现有逻辑卷**：使用 `lvextend` 扩展空间后，务必根据文件系统类型（XFS用 `xfs_growfs`，EXT用 `resize2fs`）更新文件系统信息。
2.  **从零创建逻辑卷**：流程为 `vgcreate` -> `lvcreate` -> `mkfs` -> 编辑 `/etc/fstab` -> `mount -a`。

![](img/23eb5eb67f206553a7d1eab6239e7b7e_133.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_135.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_137.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_138.png)

![](img/23eb5eb67f206553a7d1eab6239e7b7e_139.png)

掌握LVM对于管理Linux服务器存储、应对空间增长需求至关重要。请务必多加练习，熟悉相关命令和流程。