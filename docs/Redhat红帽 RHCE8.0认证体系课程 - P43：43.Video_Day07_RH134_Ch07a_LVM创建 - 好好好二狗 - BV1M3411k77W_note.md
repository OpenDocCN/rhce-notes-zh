# Redhat红帽 RHCE8.0认证体系课程：P43：LVM逻辑卷管理创建 🛠️

![](img/689f5b07accdc681610b0ce5ecad95df_1.png)

![](img/689f5b07accdc681610b0ce5ecad95df_3.png)

在本节课中，我们将要学习Linux中一个非常重要的存储管理技术——逻辑卷管理（LVM）。我们将从基本概念入手，逐步讲解如何创建和管理逻辑卷，让你能够灵活地管理磁盘空间。

![](img/689f5b07accdc681610b0ce5ecad95df_5.png)

![](img/689f5b07accdc681610b0ce5ecad95df_7.png)

## 概述 📋

![](img/689f5b07accdc681610b0ce5ecad95df_9.png)

逻辑卷管理（LVM）是一种比传统分区更灵活的磁盘管理方式。它允许你将多个物理磁盘或分区组合成一个大的存储池（卷组），然后从这个池中按需划分出逻辑卷来使用。本节课我们将重点学习LVM的创建过程。

## 核心概念与架构 🧠

![](img/689f5b07accdc681610b0ce5ecad95df_11.png)

在开始动手操作之前，我们需要理解LVM的几个核心组件及其关系。

上一节我们介绍了传统的磁盘分区管理，本节中我们来看看LVM的架构。LVM的架构可以概括为以下几个层次：

![](img/689f5b07accdc681610b0ce5ecad95df_13.png)

1.  **物理卷（Physical Volume, PV）**：这是LVM的基本存储单元。它可以是整个硬盘（如 `/dev/sdb`），也可以是硬盘上的一个分区（如 `/dev/sdb1`）。在将其加入LVM系统前，需要先将其初始化为物理卷。
    *   **命令**：`pvcreate`
2.  **卷组（Volume Group, VG）**：一个或多个物理卷可以组合成一个卷组。卷组就像一个大的存储池，所有加入的物理卷的存储空间会被合并在一起。
    *   **命令**：`vgcreate`
3.  **逻辑卷（Logical Volume, LV）**：这是最终供用户使用的部分。我们可以从卷组中划分出任意大小的逻辑卷，然后像使用普通分区一样对其进行格式化和挂载。
    *   **命令**：`lvcreate`
4.  **物理扩展（Physical Extent, PE）** 与 **逻辑扩展（Logical Extent, LE）**：这是LVM管理空间的基本单位。创建卷组时会定义PE的大小（默认为4MB）。逻辑卷的空间分配是以LE为单位的，一个LE会映射到一个或多个PE上。
    *   **容量关系公式**：`LV总容量 = LE数量 × PE大小`

它们之间的关系可以用以下架构图表示：
```
[ 物理磁盘/分区 ] -> (pvcreate) -> [ 物理卷 PV ] -> (vgcreate) -> [ 卷组 VG ] -> (lvcreate) -> [ 逻辑卷 LV ] -> (mkfs, mount) -> [ 文件系统 ]
```

![](img/689f5b07accdc681610b0ce5ecad95df_15.png)

## LVM创建实战演练 💻

![](img/689f5b07accdc681610b0ce5ecad95df_17.png)

理解了基本概念后，我们开始一步步创建LVM。假设我们有一块新磁盘 `/dev/sdb` 用于演示。

![](img/689f5b07accdc681610b0ce5ecad95df_19.png)

![](img/689f5b07accdc681610b0ce5ecad95df_21.png)

### 第一步：准备物理存储（创建分区）

![](img/689f5b07accdc681610b0ce5ecad95df_23.png)

首先，我们需要为LVM准备底层存储。虽然可以直接使用整块磁盘，但更常见的做法是先分区。这里我们在 `/dev/sdb` 上创建两个分区。

![](img/689f5b07accdc681610b0ce5ecad95df_25.png)

以下是创建分区的关键步骤：
*   使用 `fdisk /dev/sdb` 命令进入分区工具。
*   输入 `n` 创建新分区。
*   选择分区类型为 `主分区` 或 `逻辑分区`。
*   设置分区大小（例如，各分配5GB空间）。
*   最关键的一步：将分区的类型ID更改为 `Linux LVM`（对应代码 `8e`）。在fdisk中，输入 `t`，然后输入 `8e`。
*   输入 `w` 保存并退出。
*   使用 `partprobe` 或重启系统让内核重新读取分区表。

![](img/689f5b07accdc681610b0ce5ecad95df_27.png)

![](img/689f5b07accdc681610b0ce5ecad95df_28.png)

![](img/689f5b07accdc681610b0ce5ecad95df_29.png)

![](img/689f5b07accdc681610b0ce5ecad95df_30.png)

![](img/689f5b07accdc681610b0ce5ecad95df_31.png)

**注意**：在操作前，务必使用 `fdisk -l /dev/sdb` 确认磁盘的分区表类型（如MBR/dos或GPT），并根据类型进行操作，避免损坏现有数据。

![](img/689f5b07accdc681610b0ce5ecad95df_33.png)

![](img/689f5b07accdc681610b0ce5ecad95df_34.png)

### 第二步：创建物理卷（PV）

![](img/689f5b07accdc681610b0ce5ecad95df_36.png)

![](img/689f5b07accdc681610b0ce5ecad95df_38.png)

![](img/689f5b07accdc681610b0ce5ecad95df_40.png)

![](img/689f5b07accdc681610b0ce5ecad95df_42.png)

分区创建好后，我们将它们初始化为物理卷。

![](img/689f5b07accdc681610b0ce5ecad95df_43.png)

![](img/689f5b07accdc681610b0ce5ecad95df_44.png)

![](img/689f5b07accdc681610b0ce5ecad95df_46.png)

![](img/689f5b07accdc681610b0ce5ecad95df_48.png)

创建物理卷的命令如下：
```bash
pvcreate /dev/sdb1 /dev/sdb2
```
这条命令将 `/dev/sdb1` 和 `/dev/sdb2` 标记为物理卷。

![](img/689f5b07accdc681610b0ce5ecad95df_50.png)

![](img/689f5b07accdc681610b0ce5ecad95df_52.png)

![](img/689f5b07accdc681610b0ce5ecad95df_53.png)

可以使用以下命令查看创建的物理卷：
```bash
pvs        # 简要信息
pvdisplay  # 详细信息
```

![](img/689f5b07accdc681610b0ce5ecad95df_55.png)

![](img/689f5b07accdc681610b0ce5ecad95df_57.png)

### 第三步：创建卷组（VG）

接下来，将两个物理卷合并，创建一个名为 `vg_data` 的卷组。

![](img/689f5b07accdc681610b0ce5ecad95df_59.png)

![](img/689f5b07accdc681610b0ce5ecad95df_60.png)

![](img/689f5b07accdc681610b0ce5ecad95df_62.png)

![](img/689f5b07accdc681610b0ce5ecad95df_64.png)

创建卷组的命令如下：
```bash
vgcreate vg_data /dev/sdb1 /dev/sdb2
```
*   `vg_data` 是卷组的名称。
*   后面的参数是组成该卷组的物理卷。

![](img/689f5b07accdc681610b0ce5ecad95df_65.png)

![](img/689f5b07accdc681610b0ce5ecad95df_66.png)

![](img/689f5b07accdc681610b0ce5ecad95df_68.png)

![](img/689f5b07accdc681610b0ce5ecad95df_70.png)

可以使用以下命令查看卷组信息：
```bash
vgs
vgdisplay
```
在创建时，可以使用 `-s` 参数指定物理扩展（PE）的大小，例如 `vgcreate -s 8M vg_data /dev/sdb1` 将PE大小设置为8MB。

![](img/689f5b07accdc681610b0ce5ecad95df_72.png)

![](img/689f5b07accdc681610b0ce5ecad95df_74.png)

![](img/689f5b07accdc681610b0ce5ecad95df_76.png)

### 第四步：创建逻辑卷（LV）

![](img/689f5b07accdc681610b0ce5ecad95df_78.png)

现在，我们从卷组 `vg_data` 中划分逻辑卷。我们将创建两个逻辑卷：`lv_www` 和 `lv_db`。

![](img/689f5b07accdc681610b0ce5ecad95df_80.png)

![](img/689f5b07accdc681610b0ce5ecad95df_82.png)

![](img/689f5b07accdc681610b0ce5ecad95df_84.png)

创建逻辑卷有两种常用方式：

![](img/689f5b07accdc681610b0ce5ecad95df_86.png)

![](img/689f5b07accdc681610b0ce5ecad95df_87.png)

![](img/689f5b07accdc681610b0ce5ecad95df_88.png)

1.  **直接指定大小**：
    ```bash
    lvcreate -L 3G -n lv_www vg_data
    ```
    *   `-L 3G`：指定逻辑卷大小为3GB。
    *   `-n lv_www`：指定逻辑卷名称为 `lv_www`。
    *   `vg_data`：从哪个卷组创建。

![](img/689f5b07accdc681610b0ce5ecad95df_90.png)

2.  **指定PE数量**：
    ```bash
    lvcreate -l 50 -n lv_db vg_data
    ```
    *   `-l 50`：指定逻辑卷由50个PE构成。如果PE是默认的4MB，则此逻辑卷大小为 50 * 4MB = 200MB。
    *   `-n lv_db`：指定逻辑卷名称为 `lv_db`。

![](img/689f5b07accdc681610b0ce5ecad95df_92.png)

![](img/689f5b07accdc681610b0ce5ecad95df_94.png)

可以使用以下命令查看逻辑卷：
```bash
lvs
lvdisplay
```
创建成功后，逻辑卷的设备文件通常位于 `/dev/mapper/` 目录下（如 `/dev/mapper/vg_data-lv_www`），同时也会有符号链接 `/dev/vg_data/lv_www`。

![](img/689f5b07accdc681610b0ce5ecad95df_96.png)

![](img/689f5b07accdc681610b0ce5ecad95df_98.png)

### 第五步：格式化与挂载逻辑卷

![](img/689f5b07accdc681610b0ce5ecad95df_100.png)

![](img/689f5b07accdc681610b0ce5ecad95df_101.png)

![](img/689f5b07accdc681610b0ce5ecad95df_102.png)

![](img/689f5b07accdc681610b0ce5ecad95df_104.png)

逻辑卷创建后，就可以像普通分区一样使用了。

![](img/689f5b07accdc681610b0ce5ecad95df_105.png)

![](img/689f5b07accdc681610b0ce5ecad95df_106.png)

![](img/689f5b07accdc681610b0ce5ecad95df_108.png)

1.  **格式化**：为逻辑卷创建文件系统。
    ```bash
    mkfs.xfs /dev/vg_data/lv_www   # 格式化为XFS文件系统
    mkfs.ext4 /dev/vg_data/lv_db   # 格式化为EXT4文件系统
    ```

![](img/689f5b07accdc681610b0ce5ecad95df_110.png)

2.  **挂载**：创建挂载点并挂载逻辑卷。
    ```bash
    mkdir -p /www /database
    mount /dev/vg_data/lv_www /www
    mount /dev/vg_data/lv_db /database
    ```

![](img/689f5b07accdc681610b0ce5ecad95df_112.png)

![](img/689f5b07accdc681610b0ce5ecad95df_113.png)

![](img/689f5b07accdc681610b0ce5ecad95df_114.png)

![](img/689f5b07accdc681610b0ce5ecad95df_115.png)

3.  **实现开机自动挂载（推荐使用UUID）**：
    首先获取逻辑卷的UUID：
    ```bash
    blkid /dev/vg_data/lv_www
    ```
    编辑 `/etc/fstab` 文件，添加如下行（请替换为实际的UUID）：
    ```
    UUID=你的lv_www的UUID  /www      xfs     defaults        0 0
    UUID=你的lv_db的UUID    /database ext4    defaults        0 0
    ```
    最后执行 `mount -a` 测试配置是否正确。

![](img/689f5b07accdc681610b0ce5ecad95df_117.png)

**重要提示**：一个挂载点（如 `/www`）只能挂载一个存储设备。虽然技术上可以将多个分区或逻辑卷挂载到同一个目录，但这会导致数据写入混乱，无法确定数据实际写入哪个设备，**在生产环境中必须严格避免**。

![](img/689f5b07accdc681610b0ce5ecad95df_119.png)

![](img/689f5b07accdc681610b0ce5ecad95df_120.png)

![](img/689f5b07accdc681610b0ce5ecad95df_122.png)

## 总结 🎯

本节课中我们一起学习了逻辑卷管理（LVM）的创建全过程。我们首先了解了PV（物理卷）、VG（卷组）、LV（逻辑卷）等核心概念及其层次关系。接着，我们通过实战演练，逐步完成了从磁盘分区到创建物理卷、卷组、逻辑卷，最后进行格式化和挂载的完整流程。

LVM的核心优势在于其灵活性，它允许我们突破物理磁盘的限制，轻松地在线调整逻辑卷的大小（扩容或缩容），并高效地整合多个磁盘的存储空间。掌握LVM是成为一名合格的Linux系统管理员的重要技能。在接下来的课程中，我们将学习如何对已创建的LVM进行扩容、缩容等动态管理操作。