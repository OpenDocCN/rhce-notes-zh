# 备考红帽认证必修课_RHCE／RHCSA／Linux云计算架构师 - P21：3.07-LVM逻辑卷管理

![](img/bed660d04e7814f20f143fa0ba3de6f5_0.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_2.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_3.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_5.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_7.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_9.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_11.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_13.png)

在本节课中，我们将要学习Linux系统中一个非常重要的存储管理机制——LVM（逻辑卷管理）。我们将了解LVM的基本概念、优势以及如何创建和管理逻辑卷，以解决传统磁盘分区方式在灵活性和扩展性上的不足。

![](img/bed660d04e7814f20f143fa0ba3de6f5_15.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_17.png)

## LVM的基本概念与优势

![](img/bed660d04e7814f20f143fa0ba3de6f5_19.png)

上一节我们介绍了交换分区的管理，本节中我们来看看LVM逻辑卷管理。

![](img/bed660d04e7814f20f143fa0ba3de6f5_21.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_23.png)

传统的磁盘使用方式是：对磁盘进行分区、格式化，然后挂载使用。这种方式存在一些局限性。例如，一个分区的大小在创建时就被固定，后续难以调整。如果分区空间不足，通常需要添加新的分区或重新分区，过程繁琐且可能影响数据。

LVM（Logical Volume Manager，逻辑卷管理）机制就是为了解决这些问题而设计的。它在物理存储设备（如磁盘或分区）和文件系统之间增加了一个抽象层，从而带来了两大核心优势：

1.  **化零为整**：可以将多个物理存储设备（如多块硬盘）合并成一个更大的、统一的逻辑存储池（卷组）。
2.  **动态伸缩**：可以动态地调整逻辑卷（从卷组中划分出的空间）的大小，而无需卸载文件系统或中断服务。

这就像将多个小游击队（物理磁盘）整合成一支大部队（卷组），然后可以根据任务需求（存储需求）灵活地分配兵力（逻辑卷），并且兵力（空间）还可以随时增减。

## LVM的核心组件与命令

![](img/bed660d04e7814f20f143fa0ba3de6f5_25.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_27.png)

LVM架构主要包含三个核心概念，分别对应不同的管理命令：

![](img/bed660d04e7814f20f143fa0ba3de6f5_29.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_31.png)

*   **物理卷（PV， Physical Volume）**：这是LVM的基本存储单元，可以是整个硬盘（如 `/dev/vdb`）或一个分区（如 `/dev/vdb1`）。管理命令以 `pv` 开头。
*   **卷组（VG， Volume Group）**：由一个或多个物理卷组成，形成一个大的存储池。管理命令以 `vg` 开头。
*   **逻辑卷（LV， Logical Volume）**：从卷组中划分出来的存储空间，是最终可以被格式化并挂载使用的部分。管理命令以 `lv` 开头。

![](img/bed660d04e7814f20f143fa0ba3de6f5_33.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_35.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_37.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_39.png)

以下是常用命令的快速参考：

![](img/bed660d04e7814f20f143fa0ba3de6f5_41.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_43.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_45.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_47.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_49.png)

*   扫描/查看PV：`pvs` 或 `pvdisplay`
*   扫描/查看VG：`vgs` 或 `vgdisplay`
*   扫描/查看LV：`lvs` 或 `lvdisplay`
*   创建VG：`vgcreate`
*   创建LV：`lvcreate`
*   扩展LV：`lvextend`
*   删除VG：`vgremove`
*   删除LV：`lvremove`

![](img/bed660d04e7814f20f143fa0ba3de6f5_51.png)

如果不记得具体命令，可以输入 `pv`、`vg` 或 `lv` 后按 `Tab` 键查看补全选项，或使用 `man` 命令查看手册。

![](img/bed660d04e7814f20f143fa0ba3de6f5_53.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_55.png)

## 实践一：调整现有逻辑卷的大小

![](img/bed660d04e7814f20f143fa0ba3de6f5_57.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_59.png)

现在，我们来看第一道实践题目：将一个名为 `vo` 的现有逻辑卷的大小调整到300MB。

**操作步骤如下：**

1.  **确认逻辑卷现状**：首先，我们需要找到这个逻辑卷并查看其当前大小。
    ```bash
    lvs
    ```
    或使用更详细的查看方式：
    ```bash
    lvdisplay /dev/test/vo
    ```
    从输出中，我们可以确认逻辑卷 `vo` 属于卷组 `test`，并记下其当前容量（例如200MB）和设备路径（`/dev/test/vo`）。

2.  **扩展逻辑卷容量**：使用 `lvextend` 命令将逻辑卷扩展到300MB。
    ```bash
    lvextend -L 300M /dev/test/vo
    ```
    这里的 `-L 300M` 参数指定了扩展后的**新大小**为300MB。命令执行成功后，逻辑卷在LVM层面的容量就变成了300MB。

![](img/bed660d04e7814f20f143fa0ba3de6f5_61.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_63.png)

3.  **通知文件系统容量已变更**：虽然LVM已经扩展了底层空间，但操作系统上的文件系统并不知道这个变化。我们需要根据文件系统的类型，使用不同的工具来“通知”系统。
    *   **首先检查文件系统类型**：
        ```bash
        blkid /dev/test/vo
        ```
    *   **根据类型执行扩展**：
        *   如果类型是 `xfs`（常见于RHEL 7/8/9）：
            ```bash
            xfs_growfs <挂载点>
            ```
            例如，如果 `vo` 挂载在 `/vo` 目录下，则命令为 `xfs_growfs /vo`。如果未挂载，需要先挂载到一个临时目录再操作。
        *   如果类型是 `ext2/3/4`：
            ```bash
            resize2fs /dev/test/vo
            ```
            这个命令直接对设备操作。

![](img/bed660d04e7814f20f143fa0ba3de6f5_65.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_67.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_69.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_71.png)

4.  **验证结果**：最后，使用 `lvs` 和 `df -h` 命令分别验证LVM逻辑卷和文件系统的容量是否都已成功变为300MB。

![](img/bed660d04e7814f20f143fa0ba3de6f5_73.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_75.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_76.png)

**关键点总结**：调整逻辑卷大小的核心是 `lvextend` 命令，但完成后**必须**根据文件系统类型执行相应的扩容通知操作（`xfs_growfs` 或 `resize2fs`），否则系统无法使用新增的空间。

![](img/bed660d04e7814f20f143fa0ba3de6f5_78.png)

## 实践二：创建新的逻辑卷

![](img/bed660d04e7814f20f143fa0ba3de6f5_80.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_82.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_84.png)

接下来，我们进行第二项实践：从零开始创建一个符合要求的逻辑卷。

![](img/bed660d04e7814f20f143fa0ba3de6f5_86.png)

题目要求：创建一个名为 `myLV` 的逻辑卷，它属于名为 `myVG` 的卷组。该卷组的扩展单元（PE）大小为16MB，而 `myLV` 的大小为50个这样的单元（即 50 * 16MB = 800MB）。最后，将其格式化为VFAT文件系统，并挂载到 `/mnt/data` 目录，实现开机自动挂载。

![](img/bed660d04e7814f20f143fa0ba3de6f5_88.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_90.png)

**操作步骤如下：**

![](img/bed660d04e7814f20f143fa0ba3de6f5_92.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_94.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_96.png)

1.  **准备物理存储**：假设我们已经准备好了一个空闲分区 `/dev/vdb3` 作为物理卷。在实际考试中，这通常需要你提前用 `fdisk` 或 `gdisk` 命令创建。

![](img/bed660d04e7814f20f143fa0ba3de6f5_98.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_100.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_101.png)

2.  **创建卷组（VG）并指定PE大小**：使用 `vgcreate` 命令创建卷组，并通过 `-s` 选项设置PE大小为16MB。
    ```bash
    vgcreate -s 16M myVG /dev/vdb3
    ```
    可以使用 `vgdisplay myVG` 来确认 `PE Size` 是否为 `16.00 MiB`。

![](img/bed660d04e7814f20f143fa0ba3de6f5_103.png)

3.  **创建逻辑卷（LV）**：在 `myVG` 卷组中创建逻辑卷 `myLV`。指定大小有两种方式：
    *   指定具体容量：`-L 800M`
    *   指定PE个数：`-l 50`
    ```bash
    lvcreate -n myLV -l 50 myVG
    ```
    使用 `lvs` 命令检查是否成功创建了一个约800MB的 `myLV`。

![](img/bed660d04e7814f20f143fa0ba3de6f5_105.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_106.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_108.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_110.png)

4.  **格式化逻辑卷**：题目要求格式化为VFAT文件系统。
    ```bash
    mkfs.vfat /dev/myVG/myLV
    ```

![](img/bed660d04e7814f20f143fa0ba3de6f5_112.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_114.png)

5.  **配置开机自动挂载**：
    *   首先，创建挂载点目录：
        ```bash
        mkdir -p /mnt/data
        ```
    *   编辑 `/etc/fstab` 文件，在末尾添加一行配置：
        ```
        /dev/myVG/myLV /mnt/data vfat defaults 0 0
        ```
        这一行从左到右分别是：设备路径、挂载点、文件系统类型、挂载选项、备份标记、磁盘检查顺序。
    *   让配置立即生效，并验证挂载：
        ```bash
        mount -a
        df -hT /mnt/data
        ```
        如果 `df` 命令能正确显示 `/mnt/data` 挂载了VFAT文件系统且容量正确，则说明所有步骤成功。

![](img/bed660d04e7814f20f143fa0ba3de6f5_116.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_118.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_120.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_122.png)

**关键点总结**：创建新逻辑卷的流程是 **“准备PV -> 创建VG -> 创建LV -> 格式化 -> 挂载”**。要特别注意创建VG时用 `-s` 指定PE大小，以及在 `/etc/fstab` 中正确填写设备名、挂载点和文件系统类型。

![](img/bed660d04e7814f20f143fa0ba3de6f5_123.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_125.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_127.png)

## 课程总结

![](img/bed660d04e7814f20f143fa0ba3de6f5_129.png)

本节课中我们一起学习了LVM逻辑卷管理的核心知识。我们首先了解了LVM相比传统分区的两大优势：**化零为整**和**动态伸缩**。然后，我们掌握了LVM的三个核心组件：**物理卷（PV）、卷组（VG）和逻辑卷（LV）**，以及它们对应的管理命令。

![](img/bed660d04e7814f20f143fa0ba3de6f5_131.png)

通过两个实践练习，我们深入掌握了关键操作：
1.  **调整逻辑卷大小**：使用 `lvextend` 扩展空间后，务必使用 `xfs_growfs`（针对XFS）或 `resize2fs`（针对EXT4）来同步文件系统信息。
2.  **创建新逻辑卷**：遵循标准的创建流程，并注意在创建卷组时指定PE大小（`vgcreate -s`），在 `/etc/fstab` 中准确配置以实现开机自动挂载。

![](img/bed660d04e7814f20f143fa0ba3de6f5_133.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_135.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_137.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_138.png)

![](img/bed660d04e7814f20f143fa0ba3de6f5_139.png)

熟练掌握LVM是Linux系统管理，尤其是红帽认证考试中的一项重要技能，它能让你更灵活、高效地管理服务器存储空间。