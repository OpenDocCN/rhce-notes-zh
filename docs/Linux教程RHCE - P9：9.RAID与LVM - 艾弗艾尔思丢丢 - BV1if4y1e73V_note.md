# Linux教程RHCE：P9：RAID与LVM

![](img/1d3e60d203ea30f9d66c4d32f3246368_1.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_2.png)

## 概述
在本节课中，我们将要学习两个重要的存储管理技术：RAID（独立磁盘冗余阵列）和LVM（逻辑卷管理器）。RAID技术主要用于提升硬盘的读写速度和数据安全性，而LVM技术则允许我们灵活地管理和调整磁盘分区的大小。我们将从原理入手，分析不同RAID级别的特点，并通过实践操作演示如何创建、管理和维护RAID阵列与逻辑卷。

---

## RAID技术原理

上一节我们介绍了存储管理的基本需求，本节中我们来看看RAID技术是如何解决这些问题的。

RAID技术主要为了解决两个核心问题而产生：
1.  **硬盘读写速度（I/O吞吐量）问题**：如何提高硬盘的读写效率。
2.  **数据安全性问题**：如何防止因硬盘损坏导致的数据丢失。

接下来，我们将分析几种常见的RAID级别及其特性。

### RAID 0
RAID 0 至少需要2块硬盘。其工作原理是将数据条带化（Striping）分散存储在所有硬盘上。

**优势**：
*   **速度提升**：数据并行写入多块硬盘，减少了等待时间，实现了负载均衡。在理想情况下，读写速度可以接近单块硬盘的N倍（N为硬盘数量）。
*   **成本**：使用率为100%，没有额外的存储开销。

![](img/1d3e60d203ea30f9d66c4d32f3246368_4.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_5.png)

**劣势**：
*   **数据安全性下降**：任何一块硬盘损坏，都会导致所有数据丢失，因为没有冗余备份。

![](img/1d3e60d203ea30f9d66c4d32f3246368_7.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_9.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_10.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_12.png)

### RAID 1
RAID 1 同样至少需要2块硬盘。其工作原理是镜像（Mirroring），将相同的数据同时写入两块硬盘。

![](img/1d3e60d203ea30f9d66c4d32f3246368_14.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_15.png)

**优势**：
*   **数据安全性高**：数据有完整的备份。任何一块硬盘损坏，数据都不会丢失，安全性至少提升了一倍。
*   **读取速度可能提升**：可以从任意一块硬盘读取数据。

**劣势**：
*   **成本高**：存储利用率只有50%，因为所有数据都存储了两份。

### RAID 5
RAID 5 至少需要3块硬盘。它采用分布式奇偶校验，将数据和奇偶校验信息条带化地存储在所有硬盘上。

![](img/1d3e60d203ea30f9d66c4d32f3246368_17.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_18.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_20.png)

**优势**：
*   **平衡性**：在速度、安全性和成本之间取得了较好的平衡。
*   **速度与安全性兼顾**：通过奇偶校验提供容错能力，一块硬盘损坏后可以重建数据。同时，多块硬盘并行工作也提升了速度。
*   **存储利用率较高**：相比RAID 1，存储利用率更高（例如，3块盘利用率为2/3）。

![](img/1d3e60d203ea30f9d66c4d32f3246368_22.png)

**劣势**：
*   **写入性能有开销**：计算和写入奇偶校验信息会增加一些CPU和I/O开销。
*   **重建时间长**：损坏的硬盘被替换后，数据重建过程可能比较耗时。

![](img/1d3e60d203ea30f9d66c4d32f3246368_24.png)

### RAID 10 (RAID 1+0)
RAID 10 是RAID 1和RAID 0的结合，至少需要4块硬盘。它先做镜像（RAID 1），再做条带化（RAID 0）。

![](img/1d3e60d203ea30f9d66c4d32f3246368_26.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_28.png)

**优势**：
*   **高性能高可靠**：兼具RAID 0的速度优势和RAID 1的安全优势。理论上，速度和安全性都可以提升两倍。
*   **企业级首选**：对于数据价值高、预算充足的企业，这是非常推荐的方案。

![](img/1d3e60d203ea30f9d66c4d32f3246368_30.png)

**劣势**：
*   **成本最高**：存储利用率只有50%，需要至少两倍的硬盘数量。

![](img/1d3e60d203ea30f9d66c4d32f3246368_32.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_34.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_36.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_37.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_38.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_39.png)

### 热备盘
热备盘是一块平时不工作的闲置硬盘，当阵列中某块成员盘故障时，它会自动顶替上去并开始数据同步重建。

![](img/1d3e60d203ea30f9d66c4d32f3246368_41.png)

**作用**：
*   防止同一RAID 1镜像组或RAID 5/6阵列中的第二块硬盘在管理员更换故障盘之前损坏，导致数据彻底丢失。

**特点**：
*   **成本考量**：热备盘平时不提供服务，是一种为应对小概率事件而投入的保险，需要权衡成本与风险。

---

![](img/1d3e60d203ea30f9d66c4d32f3246368_43.png)

## RAID 管理实践

上一节我们介绍了各种RAID级别的原理，本节中我们来看看如何在Linux系统中使用`mdadm`命令管理软件RAID。

![](img/1d3e60d203ea30f9d66c4d32f3246368_45.png)

以下是创建和管理RAID阵列的基本步骤：

1.  **创建RAID 10阵列**
    ```bash
    # 假设有4块新硬盘：/dev/sdb, /dev/sdc, /dev/sdd, /dev/sde
    # 创建名为md0的RAID 10阵列
    mdadm -Cv /dev/md0 -a yes -n 4 -l 10 /dev/sd[b-e]
    ```
    *   `-C`：创建。
    *   `-v`：显示详细信息。
    *   `/dev/md0`：阵列设备名。
    *   `-a yes`：自动创建设备文件。
    *   `-n 4`：使用4块成员盘。
    *   `-l 10`：级别为RAID 10。
    *   `/dev/sd[b-e]`：使用这四块硬盘。

![](img/1d3e60d203ea30f9d66c4d32f3246368_47.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_48.png)

2.  **查看阵列状态**
    ```bash
    mdadm -D /dev/md0  # 查看详细信息
    cat /proc/mdstat   # 查看简要信息
    ```

3.  **格式化与挂载**
    ```bash
    mkfs.xfs /dev/md0
    mkdir /mnt/raid10
    mount /dev/md0 /mnt/raid10
    # 写入/etc/fstab实现开机自动挂载
    echo "/dev/md0 /mnt/raid10 xfs defaults 0 0" >> /etc/fstab
    ```

4.  **模拟磁盘故障与恢复**
    *   **标记磁盘故障并移除**：
        ```bash
        # 假设/dev/sde故障
        mdadm /dev/md0 -f /dev/sde  # 标记为故障
        mdadm /dev/md0 -r /dev/sde  # 从阵列中移除
        ```
    *   **添加新硬盘替换**：
        ```bash
        # 物理更换硬盘后（系统识别为新/dev/sde）
        mdadm /dev/md0 -a /dev/sde  # 添加新硬盘到阵列，自动开始重建
        ```

5.  **创建带热备盘的RAID 5**
    ```bash
    # 使用3块盘做RAID 5，1块盘做热备盘
    mdadm -Cv /dev/md1 -a yes -n 3 -l 5 -x 1 /dev/sd[f-i]
    ```
    *   `-x 1`：指定1块热备盘。

![](img/1d3e60d203ea30f9d66c4d32f3246368_50.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_51.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_53.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_54.png)

---

![](img/1d3e60d203ea30f9d66c4d32f3246368_56.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_57.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_58.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_60.png)

## LVM 技术原理

上一节我们掌握了RAID的配置，本节中我们来看看LVM如何提供更灵活的磁盘空间管理。

![](img/1d3e60d203ea30f9d66c4d32f3246368_62.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_63.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_65.png)

LVM的核心目标是允许用户动态地调整分区大小，而无需重新分区或担心底层物理磁盘的布局。它将物理存储资源抽象化，形成一个可灵活调配的资源池。

LVM的三个核心概念：
1.  **物理卷（PV， Physical Volume）**：可以是整个硬盘、硬盘分区或RAID设备。通过`pvcreate`命令初始化，使其可供LVM使用。
2.  **卷组（VG， Volume Group）**：由一个或多个PV组成，形成一个统一的存储池。VG是LVM中最高层次的抽象，所有空间管理都在VG层面进行。
3.  **逻辑卷（LV， Logical Volume）**：从VG中划分出来的逻辑区块，类似于传统分区，可以被格式化和挂载使用。LV的大小可以动态调整。

![](img/1d3e60d203ea30f9d66c4d32f3246368_67.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_69.png)

**工作流程比喻**：
*   **PV**：就像从不同人家（老张、老李、老王）借来的面粉袋。
*   **VG**：把所有面粉倒进一个大面缸，混合成一个面团资源池。
*   **LV**：从大面团中，按需切出合适大小的小面团来蒸馒头（创建文件系统）。

![](img/1d3e60d203ea30f9d66c4d32f3246368_71.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_73.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_74.png)

**优势**：
*   **灵活扩容/缩容**：可以在线调整LV的大小。
*   **存储抽象**：屏蔽底层物理磁盘差异。
*   **快照功能**：可以创建LV的时间点快照，用于备份或测试。

![](img/1d3e60d203ea30f9d66c4d32f3246368_76.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_78.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_80.png)

---

![](img/1d3e60d203ea30f9d66c4d32f3246368_82.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_84.png)

## LVM 管理实践

![](img/1d3e60d203ea30f9d66c4d32f3246368_86.png)

上一节我们理解了LVM的组成，本节中我们通过命令来实际操作LVM的整个生命周期。

以下是使用LVM的基本步骤：

![](img/1d3e60d203ea30f9d66c4d32f3246368_88.png)

1.  **创建物理卷（PV）**
    ```bash
    pvcreate /dev/sdb /dev/sdc
    pvs  # 查看物理卷列表
    ```

![](img/1d3e60d203ea30f9d66c4d32f3246368_90.png)

2.  **创建卷组（VG）**
    ```bash
    # 创建名为`vg_data`的卷组，包含/dev/sdb和/dev/sdc
    vgcreate vg_data /dev/sdb /dev/sdc
    vgs  # 查看卷组列表
    ```

![](img/1d3e60d203ea30f9d66c4d32f3246368_91.png)

![](img/1d3e60d203ea30f9d66c4d32f3246368_92.png)

3.  **创建逻辑卷（LV）**
    ```bash
    # 从vg_data中创建一个大小为10G，名为lv_web的逻辑卷
    lvcreate -L 10G -n lv_web vg_data
    lvs  # 查看逻辑卷列表
    ```
    *   `-L`：指定大小（如10G）。
    *   `-l`：指定PE个数（每个PE默认4MiB）。
    *   `-n`：指定逻辑卷名称。

![](img/1d3e60d203ea30f9d66c4d32f3246368_94.png)

4.  **格式化与挂载LV**
    ```bash
    mkfs.ext4 /dev/vg_data/lv_web
    mkdir /mnt/web
    mount /dev/vg_data/lv_web /mnt/web
    # 写入/etc/fstab
    echo "/dev/vg_data/lv_web /mnt/web ext4 defaults 0 0" >> /etc/fstab
    ```

5.  **扩展逻辑卷（LV）**
    ```bash
    # 1. 卸载文件系统（如果支持在线扩容，如ext4/xfs，可跳过）
    # umount /mnt/web
    # 2. 扩展LV大小（增加5G）
    lvextend -L +5G /dev/vg_data/lv_web
    # 3. 扩展文件系统（以ext4为例）
    resize2fs /dev/vg_data/lv_web
    # 如果是xfs文件系统，使用：xfs_growfs /mnt/web
    # 4. 重新挂载（如果卸载了）
    # mount /dev/vg_data/lv_web /mnt/web
    ```

6.  **缩减逻辑卷（LV）** - **（危险操作，需谨慎，且ext系列支持，xfs不支持）**
    ```bash
    # 1. 卸载文件系统
    umount /mnt/web
    # 2. 强制检查文件系统
    e2fsck -f /dev/vg_data/lv_web
    # 3. 缩减文件系统（先通知系统）
    resize2fs /dev/vg_data/lv_web 8G
    # 4. 缩减LV物理边界
    lvreduce -L 8G /dev/vg_data/lv_web
    # 5. 重新挂载
    mount /dev/vg_data/lv_web /mnt/web
    ```

7.  **创建快照卷（Snapshot）**
    ```bash
    # 为lv_web创建一个名为lv_web_snap的快照卷，大小1G
    lvcreate -L 1G -s -n lv_web_snap /dev/vg_data/lv_web
    # 挂载快照卷查看数据
    mount /dev/vg_data/lv_web_snap /mnt/snapshot
    ```

8.  **删除LVM（反向操作）**
    ```bash
    # 1. 卸载并删除挂载点
    umount /mnt/web
    # 2. 删除逻辑卷
    lvremove /dev/vg_data/lv_web
    # 3. 删除卷组
    vgremove vg_data
    # 4. 删除物理卷属性
    pvremove /dev/sdb /dev/sdc
    ```

---

## 总结
本节课中我们一起学习了RAID和LVM两大存储管理技术。
*   **RAID**：我们探讨了RAID 0、1、5、10等级别的原理、优缺点及适用场景，并学会了使用`mdadm`命令创建、管理和恢复软件RAID阵列，包括热备盘的使用。
*   **LVM**：我们理解了物理卷（PV）、卷组（VG）、逻辑卷（LV）三层模型，掌握了从创建、调整大小（扩容/缩容）到创建快照卷的完整操作流程。

![](img/1d3e60d203ea30f9d66c4d32f3246368_96.png)

通过结合RAID和LVM（通常在RAID之上构建LVM），可以构建出既高性能、高可靠，又具备高度灵活性和可管理性的企业级存储解决方案。请务必通过实践练习来巩固这些关键技能。