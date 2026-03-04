# Redhat红帽 RHCE8.0认证体系课程：P43：LVM逻辑卷管理创建教程

![](img/25a50a629dd8434ce6c704f31ccb29a1_0.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_2.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_3.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_4.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_6.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_8.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_10.png)

## 概述 📚
在本节课中，我们将要学习Linux系统中的逻辑卷管理（LVM）。LVM是一种灵活的磁盘管理方案，允许我们动态调整分区大小，并更高效地利用存储空间。我们将从基本概念讲起，然后通过实际操作演示如何创建并挂载一个逻辑卷。

![](img/25a50a629dd8434ce6c704f31ccb29a1_12.png)

---

![](img/25a50a629dd8434ce6c704f31ccb29a1_14.png)

## 核心概念解析 🧠
在开始操作之前，我们需要理解LVM的几个核心组成部分及其关系。

![](img/25a50a629dd8434ce6c704f31ccb29a1_16.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_18.png)

### 重要名词解释
以下是LVM架构中的关键组件：

![](img/25a50a629dd8434ce6c704f31ccb29a1_20.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_22.png)

*   **分区 (Partition)**：磁盘上可用于读写的块设备。
*   **物理卷 (Physical Volume, PV)**：分区被打上物理卷标记后，即可成为物理卷。它是**卷组的组成单位**。
*   **卷组 (Volume Group, VG)**：一个或多个物理卷可以合并形成一个**存储池**，称为卷组。
*   **逻辑卷 (Logical Volume, LV)**：在卷组中划分出来的逻辑存储单元。经过格式化和挂载后，即可用于读写数据。
*   **物理块 (Physical Extent, PE)**：卷组中存储空间分配的最小单位。
*   **逻辑块 (Logical Extent, LE)**：逻辑卷中存储空间分配的最小单位。通常 `PE大小 = LE大小`。

![](img/25a50a629dd8434ce6c704f31ccb29a1_24.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_26.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_28.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_30.png)

### LVM架构图
我们可以用一个简单的图来理解它们之间的关系：
```
磁盘/分区1 (sdb1)  -->  物理卷PV1  \
                                    -->  卷组VG0  -->  逻辑卷LV1 (挂载到 /mnt/data1)
磁盘/分区2 (sdb2)  -->  物理卷PV2  /                    逻辑卷LV2 (挂载到 /mnt/data2)
```
**流程说明**：将物理磁盘或分区初始化为物理卷(PV)，多个PV可以合并成一个卷组(VG)，最后在VG这个“存储池”中划分出多个逻辑卷(LV)供系统使用。

![](img/25a50a629dd8434ce6c704f31ccb29a1_32.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_34.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_36.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_37.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_39.png)

---

![](img/25a50a629dd8434ce6c704f31ccb29a1_41.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_43.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_45.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_46.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_47.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_49.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_51.png)

## LVM创建实战演练 ⚙️
上一节我们介绍了LVM的核心概念，本节中我们来看看如何一步步创建并使用逻辑卷。我们将使用 `/dev/sdb` 磁盘的两个分区来演示。

![](img/25a50a629dd8434ce6c704f31ccb29a1_53.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_55.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_57.png)

### 第一步：创建分区
首先，我们需要在磁盘上创建用于LVM的分区。以下是在 `/dev/sdb` 上创建两个1GB分区的命令示例。
```bash
fdisk /dev/sdb
# 在fdisk交互界面中，依次输入：n (新建分区), p (主分区), 1 (分区号1), 回车 (起始扇区), +1G (大小)
# 再次输入：n, p, 2, 回车, +1G
# 最后输入：w (保存并退出)
partprobe /dev/sdb # 通知系统分区表变更
```

![](img/25a50a629dd8434ce6c704f31ccb29a1_59.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_61.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_62.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_64.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_66.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_67.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_69.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_70.png)

### 第二步：创建物理卷 (PV)
分区创建好后，需要将其初始化为物理卷。
```bash
# 将 /dev/sdb1 和 /dev/sdb2 创建为物理卷
pvcreate /dev/sdb1 /dev/sdb2
# 或者使用通配符
pvcreate /dev/sdb[1-2]
```
创建完成后，可以使用以下命令查看物理卷信息：
```bash
pvs          # 简要信息
pvdisplay    # 详细信息
pvscan       # 扫描所有物理卷
```

![](img/25a50a629dd8434ce6c704f31ccb29a1_71.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_73.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_75.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_77.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_79.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_81.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_83.png)

### 第三步：创建卷组 (VG)
接下来，将两个物理卷合并，创建一个名为 `vg0` 的卷组。
```bash
# 创建卷组 vg0，并将 /dev/sdb1 和 /dev/sdb2 加入其中
vgcreate vg0 /dev/sdb1 /dev/sdb2
# 默认PE大小为4MB，如需指定（如16MB），可使用 -s 参数：vgcreate -s 16M vg0 /dev/sdb1 /dev/sdb2
```
查看卷组信息：
```bash
vgs
vgdisplay
```
若要删除卷组，可使用命令 `vgremove vg0`。

![](img/25a50a629dd8434ce6c704f31ccb29a1_85.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_87.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_89.png)

### 第四步：创建逻辑卷 (LV)
现在，我们可以在卷组 `vg0` 中创建逻辑卷了。有两种指定大小的方式：
```bash
# 方式一：直接指定大小（如创建100MB的逻辑卷 lv0）
lvcreate -L 100M -n lv0 vg0
# 方式二：指定PE的数量（如创建包含30个PE的逻辑卷 lv1，假设PE为4MB，则总大小为120MB）
lvcreate -l 30 -n lv1 vg0
```
查看逻辑卷信息：
```bash
lvs
lvdisplay
```
逻辑卷的设备文件路径通常为 `/dev/mapper/vg0-lv0` 或 `/dev/vg0/lv0`。

![](img/25a50a629dd8434ce6c704f31ccb29a1_91.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_92.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_93.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_94.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_95.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_96.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_98.png)

### 第五步：格式化与挂载逻辑卷
创建好的逻辑卷需要格式化为文件系统并挂载才能使用。
```bash
# 1. 格式化逻辑卷（以xfs文件系统为例）
mkfs.xfs /dev/vg0/lv0
mkfs.ext4 /dev/vg0/lv1

![](img/25a50a629dd8434ce6c704f31ccb29a1_100.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_102.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_104.png)

# 2. 创建挂载点目录
mkdir -p /mnt/lv0 /mnt/lv1

![](img/25a50a629dd8434ce6c704f31ccb29a1_106.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_108.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_109.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_110.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_112.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_114.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_116.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_118.png)

# 3. 挂载逻辑卷（建议使用UUID，更稳定）
# 获取逻辑卷的UUID
blkid /dev/vg0/lv0
# 编辑 /etc/fstab 文件，添加挂载配置（使用查到的UUID）
# 例如：UUID=xxxxxxx /mnt/lv0 xfs defaults 0 0

![](img/25a50a629dd8434ce6c704f31ccb29a1_120.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_122.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_123.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_124.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_125.png)

# 4. 执行挂载
mount -a
```
挂载完成后，可以使用 `df -h` 命令查看挂载情况。请注意，由于文件系统元数据会占用少量空间，显示的可用容量可能略小于创建时指定的大小，这在考试允许的误差范围内。

![](img/25a50a629dd8434ce6c704f31ccb29a1_127.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_129.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_131.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_133.png)

![](img/25a50a629dd8434ce6c704f31ccb29a1_134.png)

---

## 总结 🎯
本节课中我们一起学习了Linux逻辑卷管理（LVM）的创建过程。我们从**分区**开始，将其初始化为**物理卷(PV)**，然后合并成**卷组(VG)**，最后在卷组中划分出可用的**逻辑卷(LV)**并进行格式化挂载。LVM的优势在于能够灵活地在线调整存储空间，是管理复杂存储需求的强大工具。在接下来的课程中，我们将学习如何对已创建的LVM进行扩容和缩容等高级操作。