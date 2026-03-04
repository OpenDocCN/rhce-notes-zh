# Linux就该这么学：第34期：第11课：RAID与LVM实战教程 🚀

![](img/f6483c6b830c1813acbd946c8ea21fb6_1.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_3.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_5.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_7.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_9.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_11.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_13.png)

## 概述

![](img/f6483c6b830c1813acbd946c8ea21fb6_15.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_17.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_19.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_21.png)

在本节课中，我们将学习如何在Linux系统中配置和管理RAID磁盘阵列以及LVM逻辑卷管理器。我们将通过一系列实验，掌握软件RAID的创建、维护、故障模拟与恢复，以及LVM的创建、扩容、缩小、快照和完整删除等核心操作。这些技能对于构建稳定、灵活且易于管理的存储环境至关重要。

![](img/f6483c6b830c1813acbd946c8ea21fb6_23.png)

---

![](img/f6483c6b830c1813acbd946c8ea21fb6_25.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_27.png)

## RAID磁盘阵列实战 🛠️

![](img/f6483c6b830c1813acbd946c8ea21fb6_29.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_31.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_33.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_35.png)

上一节我们介绍了RAID的理论基础，本节中我们来看看如何通过`mdadm`命令在Linux中实际配置软件RAID。

### 实验准备：添加硬盘

![](img/f6483c6b830c1813acbd946c8ea21fb6_37.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_39.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_41.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_43.png)

在开始配置RAID之前，需要为虚拟机添加新的硬盘。建议将虚拟机还原到初始状态并关机后操作，以确保稳定性。

![](img/f6483c6b830c1813acbd946c8ea21fb6_45.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_47.png)

以下是添加硬盘的步骤：
1.  编辑虚拟机设置，添加新硬件。
2.  选择“硬盘”，点击“下一步”。
3.  选择磁盘类型（如SCSI），点击“下一步”。
4.  选择“创建新虚拟磁盘”，点击“下一步”。
5.  设置磁盘大小（例如5GB），点击“下一步”。
6.  指定磁盘文件名称，点击“完成”。
7.  重复以上步骤，添加总共四块5GB的硬盘。

![](img/f6483c6b830c1813acbd946c8ea21fb6_49.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_51.png)

添加完成后，启动虚拟机并使用`root`用户登录。

![](img/f6483c6b830c1813acbd946c8ea21fb6_53.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_55.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_57.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_59.png)

### 创建RAID 10阵列

![](img/f6483c6b830c1813acbd946c8ea21fb6_61.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_63.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_65.png)

RAID 10结合了RAID 1的镜像和RAID 0的条带化，至少需要四块硬盘。其可用空间为总容量的一半。

使用`mdadm`命令创建RAID 10阵列：
```bash
mdadm -Cv /dev/md0 -n 4 -l 10 /dev/sd[b-e]
```
*   `-Cv`：创建阵列并显示详细过程。
*   `/dev/md0`：阵列设备名称。
*   `-n 4`：使用4块硬盘。
*   `-l 10`：RAID级别为10。
*   `/dev/sd[b-e]`：参与阵列的硬盘（使用通配符）。

![](img/f6483c6b830c1813acbd946c8ea21fb6_67.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_69.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_71.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_73.png)

创建完成后，使用`mdadm -D /dev/md0`查看阵列详细信息，确认同步完成。

![](img/f6483c6b830c1813acbd946c8ea21fb6_75.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_77.png)

### 格式化与挂载

![](img/f6483c6b830c1813acbd946c8ea21fb6_79.png)

阵列创建并同步完成后，需要格式化为文件系统并挂载才能使用。

![](img/f6483c6b830c1813acbd946c8ea21fb6_81.png)

以下是格式化与挂载的步骤：
1.  使用`mkfs.xfs /dev/md0`命令将阵列格式化为XFS文件系统。
2.  使用`mkdir /xiaoye`命令创建一个挂载点目录。
3.  使用`mount /dev/md0 /xiaoye`命令将阵列挂载到该目录。
4.  使用`df -h`命令查看挂载情况，确认容量约为10GB（总20GB的一半）。

![](img/f6483c6b830c1813acbd946c8ea21fb6_83.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_85.png)

### 配置开机自动挂载

![](img/f6483c6b830c1813acbd946c8ea21fb6_87.png)

为了在系统重启后自动挂载RAID阵列，需要编辑`/etc/fstab`文件。

![](img/f6483c6b830c1813acbd946c8ea21fb6_89.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_91.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_93.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_95.png)

编辑`/etc/fstab`文件，添加以下行：
```
/dev/md0 /xiaoye xfs defaults 0 0
```
保存退出后，可以执行`reboot`命令重启系统进行验证。

![](img/f6483c6b830c1813acbd946c8ea21fb6_97.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_99.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_101.png)

### 模拟磁盘故障与恢复

![](img/f6483c6b830c1813acbd946c8ea21fb6_103.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_105.png)

RAID 10允许一块硬盘损坏而不丢失数据。我们可以模拟故障并恢复。

以下是模拟故障与恢复的步骤：
1.  **模拟故障**：在虚拟机设置中直接“移除”一块硬盘（如`/dev/sde`）。
2.  **验证数据**：使用`mdadm -D /dev/md0`查看阵列状态，会显示一块硬盘失效。访问`/xiaoye`目录，确认数据依然可读。
3.  **恢复阵列**：
    *   关闭虚拟机，添加一块新的5GB硬盘。
    *   启动系统，使用`mdadm /dev/md0 -a /dev/sde`命令将新硬盘加入阵列。
    *   再次使用`mdadm -D /dev/md0`查看，阵列将开始重建数据至新硬盘。

---

![](img/f6483c6b830c1813acbd946c8ea21fb6_107.png)

## 热备盘配置 🛡️

上一节我们手动替换了故障盘，本节中我们来看看如何配置热备盘实现自动故障恢复。

![](img/f6483c6b830c1813acbd946c8ea21fb6_109.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_111.png)

热备盘是一块空闲硬盘，当阵列中某块硬盘故障时，它能自动顶替并开始数据同步。

### 创建带热备盘的RAID 5阵列

![](img/f6483c6b830c1813acbd946c8ea21fb6_113.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_115.png)

RAID 5需要至少三块硬盘，并提供单盘容错能力。我们将用三块硬盘做RAID 5，一块做热备盘。

![](img/f6483c6b830c1813acbd946c8ea21fb6_117.png)

使用`mdadm`命令创建带热备盘的RAID 5：
```bash
mdadm -Cv /dev/md0 -n 3 -l 5 -x 1 /dev/sd[b-e]
```
*   `-x 1`：指定1块热备盘。

![](img/f6483c6b830c1813acbd946c8ea21fb6_119.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_121.png)

创建完成后，同样进行格式化、挂载（例如挂载到`/xiaoqiu`）并配置`/etc/fstab`。

### 验证热备盘自动恢复

1.  模拟一块硬盘故障（如移除`/dev/sdc`）。
2.  使用`mdadm -D /dev/md0`命令观察。稍等片刻，会发现热备盘自动激活并开始重建数据，阵列状态恢复为“clean”。

---

![](img/f6483c6b830c1813acbd946c8ea21fb6_123.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_125.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_127.png)

## 删除RAID阵列 🗑️

![](img/f6483c6b830c1813acbd946c8ea21fb6_129.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_131.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_133.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_135.png)

当我们不再需要某个RAID阵列时，需要按照步骤完整删除它。

![](img/f6483c6b830c1813acbd946c8ea21fb6_137.png)

以下是删除RAID阵列的步骤：
1.  **卸载文件系统**：使用`umount /dev/md0`命令。
2.  **停止阵列**：使用`mdadm -S /dev/md0`命令。
3.  **清除成员盘元数据**：使用`mdadm --zero-superblock /dev/sd[b-e]`命令（此步骤可选，但能彻底清理）。
4.  **删除`/etc/fstab`中的配置项**。
5.  **移除虚拟机中对应的硬盘**。

![](img/f6483c6b830c1813acbd946c8ea21fb6_139.png)

---

![](img/f6483c6b830c1813acbd946c8ea21fb6_141.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_143.png)

## LVM逻辑卷管理器 📦

![](img/f6483c6b830c1813acbd946c8ea21fb6_145.png)

RAID解决了磁盘冗余和性能问题，但分区大小固定不灵活。LVM（逻辑卷管理器）解决了磁盘分区后难以扩容或缩容的痛点。

![](img/f6483c6b830c1813acbd946c8ea21fb6_147.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_149.png)

### LVM核心概念

![](img/f6483c6b830c1813acbd946c8ea21fb6_151.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_153.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_155.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_157.png)

LVM将存储管理分为三个层次：
1.  **物理卷（PV）**：可以是整块硬盘或硬盘分区，是LVM的基础存储单元。
2.  **卷组（VG）**：由一个或多个PV组成，形成一个大的“存储池”。
3.  **逻辑卷（LV）**：从VG中划分出来的逻辑区块，可以被格式化和挂载使用。

![](img/f6483c6b830c1813acbd946c8ea21fb6_159.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_161.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_163.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_165.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_167.png)

此外，还有一个最小存储单元**物理扩展块（PE）**，默认大小为4MB。LV的大小必须是PE的整数倍。

![](img/f6483c6b830c1813acbd946c8ea21fb6_169.png)

### LVM管理命令

![](img/f6483c6b830c1813acbd946c8ea21fb6_171.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_173.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_175.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_177.png)

LVM命令有规律可循，通常以`pv`、`vg`、`lv`开头，后跟操作（`create`, `display`, `extend`, `reduce`, `remove`）。

以下是常用LVM命令：
*   `pvcreate /dev/sdb /dev/sdc`：将硬盘初始化为物理卷。
*   `vgcreate vg_group /dev/sdb /dev/sdc`：创建名为`vg_group`的卷组。
*   `lvcreate -n lv_vol -L 400M vg_group`：在`vg_group`中创建名为`lv_vol`、大小为400M的逻辑卷。
*   `lvdisplay`：查看逻辑卷详细信息。
*   `lvextend -L +400M /dev/vg_group/lv_vol`：为逻辑卷扩容400M。
*   `lvreduce -L 200M /dev/vg_group/lv_vol`：将逻辑卷缩小至200M。

### 创建与使用LVM

我们使用两块新硬盘来演示LVM的完整流程。

![](img/f6483c6b830c1813acbd946c8ea21fb6_179.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_181.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_183.png)

以下是创建与使用LVM的步骤：
1.  **创建PV**：`pvcreate /dev/sdb /dev/sdc`
2.  **创建VG**：`vgcreate vg_group /dev/sdb /dev/sdc`
3.  **创建LV**：`lvcreate -n lv_vol -L 400M vg_group`
4.  **格式化**：`mkfs.ext4 /dev/vg_group/lv_vol` （**注意**：XFS文件系统不支持缩小，因此演示使用ext4）。
5.  **挂载**：`mkdir /linuxprobe; mount /dev/vg_group/lv_vol /linuxprobe`
6.  **开机挂载**：在`/etc/fstab`中添加：`/dev/vg_group/lv_vol /linuxprobe ext4 defaults 0 0`

![](img/f6483c6b830c1813acbd946c8ea21fb6_185.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_187.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_189.png)

### LVM扩容与缩小

![](img/f6483c6b830c1813acbd946c8ea21fb6_191.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_193.png)

LVM的强大之处在于可以动态调整LV的大小。

![](img/f6483c6b830c1813acbd946c8ea21fb6_195.png)

#### 扩容逻辑卷

1.  **卸载**：`umount /linuxprobe` （严格来说，ext4支持在线扩容，但规范操作建议卸载）。
2.  **扩容LV**：`lvextend -L 800M /dev/vg_group/lv_vol`
3.  **检查文件系统**：`e2fsck -f /dev/vg_group/lv_vol`
4.  **扩展文件系统**：`resize2fs /dev/vg_group/lv_vol`
5.  **重新挂载**：`mount -a` 或 `mount /dev/vg_group/lv_vol /linuxprobe`

#### 缩小逻辑卷 **（需谨慎）**

![](img/f6483c6b830c1813acbd946c8ea21fb6_197.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_199.png)

缩小操作有数据丢失风险，系统会进行严格检查。

![](img/f6483c6b830c1813acbd946c8ea21fb6_201.png)

1.  **卸载**：`umount /linuxprobe`
2.  **检查文件系统**：`e2fsck -f /dev/vg_group/lv_vol`
3.  **缩小文件系统**：`resize2fs /dev/vg_group/lv_vol 200M`
4.  **缩小LV**：`lvreduce -L 200M /dev/vg_group/lv_vol` （系统会警告，输入`y`确认）。
5.  **重新挂载**：`mount -a`

![](img/f6483c6b830c1813acbd946c8ea21fb6_203.png)

### LVM快照卷 📸

![](img/f6483c6b830c1813acbd946c8ea21fb6_205.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_207.png)

LVM快照卷可以瞬间创建一个逻辑卷的“照片”，用于数据备份或快速回滚。快照卷大小需与原LV相同，且为一次性使用。

以下是创建与恢复快照的步骤：
1.  **创建快照**：`lvcreate -L 200M -s -n snap /dev/vg_group/lv_vol`
2.  **模拟数据损坏**：例如，误删除`/linuxprobe`目录下所有文件。
3.  **恢复快照**：
    *   卸载原LV：`umount /linuxprobe`
    *   合并快照：`lvconvert --merge /dev/vg_group/snap`
    *   重新挂载：`mount /dev/vg_group/lv_vol /linuxprobe`，数据恢复。

### 删除LVM

![](img/f6483c6b830c1813acbd946c8ea21fb6_209.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_211.png)

删除LVM是创建的逆过程：先删LV，再删VG，最后删PV。

以下是删除LVM的步骤：
1.  **卸载并删除配置**：`umount /linuxprobe`，并删除`/etc/fstab`中对应行。
2.  **删除LV**：`lvremove /dev/vg_group/lv_vol`
3.  **删除VG**：`vgremove vg_group`
4.  **删除PV**：`pvremove /dev/sdb /dev/sdc`
5.  使用`pvdisplay`, `vgdisplay`, `lvdisplay`确认已删除。

![](img/f6483c6b830c1813acbd946c8ea21fb6_213.png)

---

## 总结

![](img/f6483c6b830c1813acbd946c8ea21fb6_215.png)

本节课中我们一起学习了Linux系统中两大重要的存储管理技术：RAID和LVM。

我们掌握了：
*   使用`mdadm`配置和管理软件RAID 10、RAID 5阵列，包括创建、格式化、挂载、模拟故障恢复以及配置热备盘。
*   LVM的核心概念（PV、VG、LV、PE）及其管理命令。
*   创建LVM的完整流程，并实践了逻辑卷的**动态扩容和缩小**（使用ext4文件系统）。
*   使用LVM快照卷进行快速数据备份和恢复。
*   如何完整、干净地删除RAID阵列和LVM逻辑卷。

![](img/f6483c6b830c1813acbd946c8ea21fb6_217.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_219.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_221.png)

![](img/f6483c6b830c1813acbd946c8ea21fb6_223.png)

RAID提供了磁盘级别的冗余和性能提升，而LVM则在逻辑层面提供了无与伦比的存储灵活性。两者结合使用，可以构建出既安全又易于管理的企业级存储方案。虽然这些内容在RHCE考试中不直接考核，但却是实际运维工作中不可或缺的核心技能。