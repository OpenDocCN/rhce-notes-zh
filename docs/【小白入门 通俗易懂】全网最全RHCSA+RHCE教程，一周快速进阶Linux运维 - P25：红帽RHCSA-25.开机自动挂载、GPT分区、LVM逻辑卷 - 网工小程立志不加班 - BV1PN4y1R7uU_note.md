# Linux磁盘管理：P25：开机自动挂载、GPT分区、LVM逻辑卷

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_1.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_3.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_5.png)

## 概述

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_7.png)

在本节课中，我们将学习Linux系统中磁盘管理的三个核心进阶主题。首先，我们将了解如何配置开机自动挂载，让系统重启后分区依然可用。接着，我们会学习GPT分区格式，它与MBR有何不同以及如何使用。最后，我们将深入探讨LVM逻辑卷管理，理解其如何实现灵活的空间扩容，解决传统分区空间固定的问题。

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_9.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_11.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_13.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_15.png)

---

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_17.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_19.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_21.png)

## 开机自动挂载

上一节我们介绍了使用`mount`命令进行临时挂载。本节中我们来看看如何实现开机自动挂载，避免每次重启后都需要手动重新挂载分区。

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_23.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_25.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_27.png)

临时挂载的命令格式非常简单：`mount 设备路径 挂载点目录`。这种方式的优点是立即生效，但缺点是机器关机重启后，挂载就会失效。

为了实现开机自动挂载，我们需要修改系统配置文件 `/etc/fstab`。系统启动时会自动读取这个文件的内容，并将指定的文件系统挂载到指定的目录。

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_29.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_31.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_33.png)

打开 `/etc/fstab` 文件，其内容格式如下：
```
设备路径 挂载点目录 文件系统类型 挂载参数 是否备份 检查顺序
```

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_35.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_37.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_39.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_41.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_43.png)

以下是每一列的具体含义：
1.  **第一列**：要挂载的设备路径，例如 `/dev/sdb1`。
2.  **第二列**：挂载点目录，例如 `/db_back`。
3.  **第三列**：文件系统类型，例如 `xfs`。
4.  **第四列**：挂载参数。`defaults` 代表使用所有默认参数（读写权限、自动挂载等）。
5.  **第五列**：是否对文件系统进行备份。`0` 表示不备份，`1` 表示备份。
6.  **第六列**：是否检查文件系统的挂载顺序。`0` 表示不检查，`1` 表示优先级最高（通常根分区 `/` 设为 `1`）。

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_45.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_47.png)

配置完成后，可以使用 `mount -a` 命令让系统立即读取 `/etc/fstab` 文件，挂载所有未挂载的设备。此后，系统每次启动都会自动完成挂载。

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_49.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_51.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_53.png)

---

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_55.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_57.png)

## GPT分区

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_59.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_61.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_63.png)

之前我们学习了MBR分区格式。本节中我们来看看另一种更现代的分区格式——GPT。

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_65.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_67.png)

GPT分区格式支持超过2TB的大容量磁盘，并且最多允许创建128个主分区，克服了MBR格式的限制。

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_69.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_71.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_73.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_75.png)

创建GPT分区的命令是 `gdisk`。其使用方式与 `fdisk` 类似：
```bash
gdisk /dev/sdc
```
进入交互界面后，常用命令如下：
*   `n`：添加一个新分区。
*   `p`：显示当前分区表。
*   `d`：删除一个分区。
*   `w`：保存分区表并退出。
*   `q`：不保存退出。
*   `?`：获取帮助信息。

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_77.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_79.png)

分区过程通常只需一路按提示操作，指定分区大小即可，系统会默认使用 `Linux filesystem` 类型。分区完成后，使用 `lsblk -f` 命令可以查看磁盘的分区格式已变为 `gpt`。

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_81.png)

后续的格式化、挂载和配置开机自动挂载步骤，与MBR分区完全一致。

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_83.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_85.png)

---

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_87.png)

## LVM逻辑卷管理

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_89.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_91.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_93.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_95.png)

前面我们解决了分区自动挂载和不同分区格式的问题。本节中我们来看看如何解决分区空间固定、无法灵活扩展的痛点，这就是LVM逻辑卷管理。

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_97.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_99.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_101.png)

LVM的全称是 Logical Volume Manager。它的核心功能是将底层的多个物理硬盘或分区，整合成一个大的、连续的“虚拟存储池”（称为卷组），然后在这个池子上灵活地划分出“逻辑卷”供系统使用。

**LVM的核心优势在于空间可以动态扩展。** 当一个逻辑卷的空间用尽时，可以从卷组的剩余空间中进行扩容，而无需迁移数据或创建新的分区，保证了数据存储的连续性。

LVM的创建和管理主要涉及以下三个概念和步骤：

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_103.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_105.png)

1.  **物理卷**：这是LVM的底层基础，可以是整块磁盘（如 `/dev/sdd`）或磁盘分区（如 `/dev/sdb1`）。使用 `pvcreate` 命令将其初始化为物理卷。
    ```bash
    pvcreate /dev/sdb1 /dev/sdc1
    ```

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_107.png)

2.  **卷组**：将一个或多个物理卷组合起来，形成一个大的存储池。使用 `vgcreate` 命令创建卷组。
    ```bash
    vgcreate myvg /dev/sdb1 /dev/sdc1
    ```

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_109.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_111.png)

3.  **逻辑卷**：在卷组这个“大池子”里划分出逻辑分区。使用 `lvcreate` 命令创建逻辑卷。
    ```bash
    lvcreate -L 100G -n mylv myvg
    ```
    此命令从名为 `myvg` 的卷组中，创建一个大小为100G、名为 `mylv` 的逻辑卷。创建完成后，系统会在 `/dev` 目录下生成对应的设备文件，如 `/dev/myvg/mylv`。

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_113.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_115.png)

此后，你就可以像使用普通分区一样，对这个逻辑卷设备进行格式化（`mkfs.xfs /dev/myvg/mylv`）、挂载和使用。当空间不足时，可以使用 `lvextend` 命令对其进行扩容，然后再扩展文件系统，即可实现不中断服务的空间增长。

## 总结

本节课我们一起学习了Linux磁盘管理的三个重要进阶知识。
1.  我们通过配置 `/etc/fstab` 文件实现了分机的**开机自动挂载**。
2.  我们使用 `gdisk` 工具创建了支持大容量和更多分区的**GPT分区**。
3.  我们初步了解了**LVM逻辑卷管理**的架构和优势，它通过物理卷、卷组、逻辑卷三层抽象，实现了存储空间的灵活管理和动态扩容，是生产环境中管理存储的重要工具。

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_117.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_119.png)

![](img/ab4d68cc64bc78fccbf4e033c4fe4fef_121.png)

掌握这些技能，将使你能够更高效、更灵活地管理Linux服务器的存储资源。