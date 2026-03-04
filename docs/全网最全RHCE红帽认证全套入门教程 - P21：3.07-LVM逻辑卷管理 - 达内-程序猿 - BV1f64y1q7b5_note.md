# RHCE红帽认证全套入门教程：P21：3.07-LVM逻辑卷管理

![](img/3e6d167ef264f790ac06fae66e312d66_0.png)

![](img/3e6d167ef264f790ac06fae66e312d66_2.png)

![](img/3e6d167ef264f790ac06fae66e312d66_3.png)

![](img/3e6d167ef264f790ac06fae66e312d66_5.png)

![](img/3e6d167ef264f790ac06fae66e312d66_7.png)

![](img/3e6d167ef264f790ac06fae66e312d66_9.png)

![](img/3e6d167ef264f790ac06fae66e312d66_11.png)

![](img/3e6d167ef264f790ac06fae66e312d66_13.png)

![](img/3e6d167ef264f790ac06fae66e312d66_15.png)

在本节课中，我们将要学习Linux系统中一个非常重要的存储管理机制——LVM（逻辑卷管理）。我们将了解LVM的基本概念、优势以及如何创建和管理逻辑卷，包括调整逻辑卷大小和创建新的逻辑卷。

![](img/3e6d167ef264f790ac06fae66e312d66_17.png)

![](img/3e6d167ef264f790ac06fae66e312d66_19.png)

## 概述：什么是LVM？

![](img/3e6d167ef264f790ac06fae66e312d66_21.png)

![](img/3e6d167ef264f790ac06fae66e312d66_23.png)

上一节我们介绍了交换分区的管理，本节中我们来看看LVM逻辑卷管理。LVM的全称是Logical Volume Manager，即逻辑卷管理机制。它提供了一种比传统磁盘分区方式更灵活、更强大的存储管理方案。

传统的磁盘使用方式是：对磁盘进行分区、格式化、挂载，然后访问。这种方式存在一些不足：
*   **分区大小固定**：一旦分区创建，其大小就难以调整。例如，一个512MB的交换分区空间不足时，无法直接扩大，只能创建新的分区并修改配置，过程繁琐。
*   **存储空间受限**：单个分区的最大容量受限于其所在的物理磁盘。例如，四块500GB的磁盘，如果不做磁盘阵列，无法直接获得一个2TB的单一存储空间。

LVM机制通过在物理存储设备（磁盘或分区）和文件系统之间增加一个抽象层，解决了上述问题，主要带来两大优势：
1.  **化零为整**：可以将多个物理存储设备（PV）组合成一个大的、统一的虚拟存储池（VG）。
2.  **动态伸缩**：可以从虚拟存储池中灵活地分配空间给逻辑卷（LV），并且可以随时调整逻辑卷的大小，而无需重启系统或卸载文件系统。

## LVM核心概念与架构

LVM的实现主要涉及三个核心概念，它们对应着三层结构：

![](img/3e6d167ef264f790ac06fae66e312d66_25.png)

![](img/3e6d167ef264f790ac06fae66e312d66_27.png)

1.  **物理卷（Physical Volume, PV）**：这是LVM的基本存储单元，可以是整个硬盘（如 `/dev/vdb`），也可以是硬盘上的一个分区（如 `/dev/vdb1`）。PV为LVM提供了底层的存储空间。
2.  **卷组（Volume Group, VG）**：由一个或多个PV组成，形成一个大的、统一的存储池。你可以把VG理解为一个“逻辑大硬盘”。
3.  **逻辑卷（Logical Volume, LV）**：从VG中划分出来的存储空间块，相当于传统意义上的“分区”。我们最终是在LV上创建文件系统（格式化）并挂载使用。

![](img/3e6d167ef264f790ac06fae66e312d66_29.png)

![](img/3e6d167ef264f790ac06fae66e312d66_31.png)

![](img/3e6d167ef264f790ac06fae66e312d66_33.png)

它们的关系可以用一个简单的公式表示：
**多个 PV -> 组成一个 VG -> 从 VG 中划分出多个 LV**

![](img/3e6d167ef264f790ac06fae66e312d66_35.png)

![](img/3e6d167ef264f790ac06fae66e312d66_37.png)

![](img/3e6d167ef264f790ac06fae66e312d66_39.png)

![](img/3e6d167ef264f790ac06fae66e312d66_41.png)

LVM的管理命令也围绕这三个概念展开：
*   管理物理卷（PV）：命令以 `pv` 开头，如 `pvs`, `pvdisplay`。
*   管理卷组（VG）：命令以 `vg` 开头，如 `vgs`, `vgdisplay`, `vgcreate`。
*   管理逻辑卷（LV）：命令以 `lv` 开头，如 `lvs`, `lvdisplay`, `lvcreate`。

![](img/3e6d167ef264f790ac06fae66e312d66_43.png)

![](img/3e6d167ef264f790ac06fae66e312d66_45.png)

![](img/3e6d167ef264f790ac06fae66e312d66_47.png)

![](img/3e6d167ef264f790ac06fae66e312d66_49.png)

![](img/3e6d167ef264f790ac06fae66e312d66_51.png)

## 实践一：调整现有逻辑卷大小

![](img/3e6d167ef264f790ac06fae66e312d66_53.png)

![](img/3e6d167ef264f790ac06fae66e312d66_55.png)

现在，我们来看第一道实践题目：将一个名为 `vo` 的现有逻辑卷的大小调整到300MB。

![](img/3e6d167ef264f790ac06fae66e312d66_57.png)

以下是操作步骤：

![](img/3e6d167ef264f790ac06fae66e312d66_59.png)

1.  **查看现有逻辑卷信息**
    首先，我们需要确认 `vo` 逻辑卷的当前状态和所在位置。
    ```bash
    lvscan
    # 或使用 lvs 命令查看更简洁的信息
    ```
    从输出中找到 `vo` 逻辑卷，记下其设备路径，通常格式为 `/dev/<卷组名>/<逻辑卷名>`，例如 `/dev/test/vo`。同时，注意其当前容量。

2.  **扩展逻辑卷容量**
    使用 `lvextend` 命令来扩大逻辑卷。这里我们指定新的总大小。
    ```bash
    lvextend -L 300M /dev/test/vo
    ```
    *   `-L 300M`：指定逻辑卷的新大小为300MB。
    *   你也可以使用 `-L +100M` 来增加100MB。

3.  **通知系统文件系统已扩容**
    扩容物理空间后，需要让操作系统（内核）知道文件系统的大小也改变了。首先，检查该逻辑卷的文件系统类型。
    ```bash
    blkid /dev/test/vo
    ```
    根据输出结果，选择对应的工具：
    *   如果文件系统是 **XFS**（常见于RHEL 7/8）：
        ```bash
        xfs_growfs /挂载点
        ```
        > **注意**：`xfs_growfs` 的操作对象是挂载点，而不是设备本身。如果 `vo` 未挂载，你需要先将其挂载到一个临时目录。
    *   如果文件系统是 **EXT2/3/4**：
        ```bash
        resize2fs /dev/test/vo
        ```
        > **注意**：`resize2fs` 的操作对象是设备本身。

![](img/3e6d167ef264f790ac06fae66e312d66_61.png)

![](img/3e6d167ef264f790ac06fae66e312d66_63.png)

![](img/3e6d167ef264f790ac06fae66e312d66_65.png)

4.  **验证扩容结果**
    再次使用 `lvscan` 或 `df -h` 命令查看逻辑卷和文件系统的新容量，确认扩容成功。

![](img/3e6d167ef264f790ac06fae66e312d66_67.png)

![](img/3e6d167ef264f790ac06fae66e312d66_69.png)

![](img/3e6d167ef264f790ac06fae66e312d66_71.png)

![](img/3e6d167ef264f790ac06fae66e312d66_73.png)

## 实践二：创建新的逻辑卷

![](img/3e6d167ef264f790ac06fae66e312d66_75.png)

![](img/3e6d167ef264f790ac06fae66e312d66_76.png)

![](img/3e6d167ef264f790ac06fae66e312d66_78.png)

接下来，我们完成第二道题目：创建一个符合特定要求的全新逻辑卷。

![](img/3e6d167ef264f790ac06fae66e312d66_80.png)

![](img/3e6d167ef264f790ac06fae66e312d66_82.png)

题目要求：
*   创建一个名为 `myvg` 的卷组。
*   卷组中逻辑卷的扩展单元（PE）大小为16MB。
*   在 `myvg` 卷组中创建一个名为 `mylv` 的逻辑卷，大小为50个PE（即 50 * 16MB = 800MB）。
*   使用VFAT文件系统格式化 `mylv`。
*   将其挂载到 `/mnt/data` 目录，并配置开机自动挂载。

![](img/3e6d167ef264f790ac06fae66e312d66_84.png)

![](img/3e6d167ef264f790ac06fae66e312d66_86.png)

以下是操作步骤：

![](img/3e6d167ef264f790ac06fae66e312d66_88.png)

1.  **创建卷组（VG）并指定PE大小**
    使用 `vgcreate` 命令创建卷组。我们用之前准备好的空闲分区 `/dev/vdb3` 来创建。
    ```bash
    vgcreate -s 16M myvg /dev/vdb3
    ```
    *   `-s 16M`：指定卷组的物理扩展单元（PE）大小为16MB。
    *   `myvg`：卷组的名称。
    *   `/dev/vdb3`：用于创建卷组的物理设备。

    使用 `vgdisplay myvg` 命令验证PE大小是否已设置为16MB。

![](img/3e6d167ef264f790ac06fae66e312d66_90.png)

![](img/3e6d167ef264f790ac06fae66e312d66_92.png)

![](img/3e6d167ef264f790ac06fae66e312d66_94.png)

![](img/3e6d167ef264f790ac06fae66e312d66_96.png)

2.  **创建逻辑卷（LV）**
    使用 `lvcreate` 命令从卷组中划分空间创建逻辑卷。
    ```bash
    lvcreate -L 800M -n mylv myvg
    ```
    *   `-L 800M`：指定逻辑卷大小为800MB。你也可以使用 `-l 50` 来指定分配50个PE。
    *   `-n mylv`：指定逻辑卷的名称为 `mylv`。
    *   `myvg`：指定从哪个卷组分配空间。

    使用 `lvscan` 或 `lvs` 命令查看新创建的逻辑卷。

![](img/3e6d167ef264f790ac06fae66e312d66_98.png)

![](img/3e6d167ef264f790ac06fae66e312d66_100.png)

![](img/3e6d167ef264f790ac06fae66e312d66_101.png)

3.  **格式化逻辑卷**
    使用 `mkfs` 命令格式化逻辑卷为VFAT文件系统。
    ```bash
    mkfs.vfat /dev/myvg/mylv
    ```

![](img/3e6d167ef264f790ac06fae66e312d66_103.png)

![](img/3e6d167ef264f790ac06fae66e312d66_105.png)

4.  **创建挂载点并配置开机自动挂载**
    *   创建挂载目录：
        ```bash
        mkdir -p /mnt/data
        ```
    *   编辑 `/etc/fstab` 文件，添加开机自动挂载配置：
        ```bash
        echo '/dev/myvg/mylv /mnt/data vfat defaults 0 0' >> /etc/fstab
        ```
        > **`/etc/fstab` 文件格式说明**：
        > 1.  第一列：要挂载的设备（`/dev/myvg/mylv`）。
        > 2.  第二列：挂载点目录（`/mnt/data`）。
        > 3.  第三列：文件系统类型（`vfat`）。
        > 4.  第四列：挂载选项（`defaults`）。
        > 5.  第五列：备份标记（`0` 表示不备份）。
        > 6.  第六列：开机磁盘检查顺序（`0` 表示不检查）。

![](img/3e6d167ef264f790ac06fae66e312d66_106.png)

![](img/3e6d167ef264f790ac06fae66e312d66_108.png)

![](img/3e6d167ef264f790ac06fae66e312d66_110.png)

![](img/3e6d167ef264f790ac06fae66e312d66_112.png)

![](img/3e6d167ef264f790ac06fae66e312d66_114.png)

5.  **挂载并验证**
    *   执行 `mount -a` 命令，尝试挂载 `/etc/fstab` 中所有配置的设备。
    *   使用 `df -hT` 命令查看 `/mnt/data` 是否已成功挂载，并检查文件系统类型和容量。

![](img/3e6d167ef264f790ac06fae66e312d66_116.png)

![](img/3e6d167ef264f790ac06fae66e312d66_118.png)

## 总结

![](img/3e6d167ef264f790ac06fae66e312d66_120.png)

![](img/3e6d167ef264f790ac06fae66e312d66_122.png)

![](img/3e6d167ef264f790ac06fae66e312d66_123.png)

![](img/3e6d167ef264f790ac06fae66e312d66_125.png)

本节课中我们一起学习了LVM逻辑卷管理的核心知识和实践操作。

![](img/3e6d167ef264f790ac06fae66e312d66_127.png)

我们首先了解了传统磁盘管理的局限性，并引出了LVM在**化零为整**和**动态伸缩**方面的巨大优势。接着，我们学习了LVM的三个核心概念：**物理卷（PV）、卷组（VG）和逻辑卷（LV）**，以及它们之间的层次关系。

![](img/3e6d167ef264f790ac06fae66e312d66_129.png)

![](img/3e6d167ef264f790ac06fae66e312d66_131.png)

通过两个实践练习，我们掌握了关键操作：
1.  **调整逻辑卷大小**：使用 `lvextend` 扩展LV容量，并根据文件系统类型（XFS用 `xfs_growfs`，EXT用 `resize2fs`）通知内核。
2.  **创建新的逻辑卷**：流程包括使用 `vgcreate` 创建VG（可指定PE大小），使用 `lvcreate` 从VG中创建LV，使用 `mkfs` 格式化LV，最后通过编辑 `/etc/fstab` 文件实现开机自动挂载。

![](img/3e6d167ef264f790ac06fae66e312d66_133.png)

![](img/3e6d167ef264f790ac06fae66e312d66_135.png)

![](img/3e6d167ef264f790ac06fae66e312d66_137.png)

![](img/3e6d167ef264f790ac06fae66e312d66_138.png)

![](img/3e6d167ef264f790ac06fae66e312d66_139.png)

LVM是Linux系统管理员必须掌握的高级存储管理技能，它能极大地提升存储管理的灵活性和效率。请务必多加练习，熟悉整个流程和命令。