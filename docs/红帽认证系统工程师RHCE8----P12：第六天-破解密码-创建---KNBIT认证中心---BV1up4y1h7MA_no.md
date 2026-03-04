# RHCE8认证课程：P12：第六天 破解密码、创建swap与LVM 📚

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_0.png)

在本节课中，我们将要学习三个核心的系统管理技能：如何破解（重置）root用户密码、如何创建和管理交换分区（swap），以及如何配置和管理逻辑卷管理器（LVM）。这些是RHCE认证考试中的关键考点，也是日常系统管理工作中必备的能力。

## 交换分区（Swap）的创建与管理 💾

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_2.png)

上一节我们介绍了磁盘分区的基础知识，本节中我们来看看如何创建和管理交换分区。

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_4.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_6.png)

交换分区（Swap Space）是硬盘上的一块空间，用于辅助内存管理，也被称为虚拟内存。当物理内存（RAM）不足时，操作系统会将内存中不活跃的“页面”（Page）置换到交换分区中，从而为当前活跃的进程腾出更多内存空间。

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_8.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_10.png)

在Linux系统中，内存管理的基本单位是“页”（Page），通常大小为4KB。这与磁盘读写的基本单位“块”（Block）不同。

以下是创建交换分区的标准步骤：

1.  **分区**：使用 `fdisk` 或 `gdisk` 工具在目标磁盘上创建一个新分区。
2.  **格式化**：使用 `mkswap` 命令将新分区格式化为交换分区。
3.  **激活**：使用 `swapon` 命令激活交换分区。
4.  **永久生效**：将交换分区信息写入 `/etc/fstab` 文件，以便系统重启后自动挂载。

**示例命令流程**：
```bash
# 1. 分区 (假设在 /dev/vdb 上操作)
fdisk /dev/vdb
# 在 fdisk 交互界面中创建新分区（例如 /dev/vdb5）

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_12.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_14.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_16.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_18.png)

# 2. 格式化
mkswap /dev/vdb5

# 3. 激活
swapon /dev/vdb5

# 4. 查看当前 swap 状态
free -m
swapon -s

# 5. 永久生效：编辑 /etc/fstab，添加一行
# /dev/vdb5 swap swap defaults 0 0
```

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_20.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_22.png)

**重要内核参数**：
系统使用交换分区的积极性由一个内核参数 `vm.swappiness` 控制，其值范围为0-100。
*   **数值越大**：系统越积极地使用交换分区。
*   **数值越小**：系统尽量避免使用交换分区，优先使用物理内存。
默认值通常为60。您可以通过 `sysctl vm.swappiness` 查看，或使用 `sysctl -w vm.swappiness=10` 临时修改。

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_24.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_26.png)

## 逻辑卷管理器（LVM）详解 🗂️

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_28.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_30.png)

交换分区解决了内存扩展的问题，而LVM则解决了磁盘空间动态管理的问题。与标准分区一旦创建大小就固定不同，LVM允许我们动态地调整逻辑卷的大小，这是其核心优势。

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_32.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_34.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_36.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_38.png)

### LVM核心概念

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_40.png)

LVM的架构分为几个层次：

1.  **物理卷（Physical Volume, PV）**：这是LVM的底层存储单元，可以是整个硬盘（如 `/dev/sdb`）或一个分区（如 `/dev/sdb1`）。使用 `pvcreate` 命令创建。
2.  **卷组（Volume Group, VG）**：由一个或多个PV组成，形成一个大的存储池。使用 `vgcreate` 命令创建。
3.  **逻辑卷（Logical Volume, LV）**：从VG中划分出来的逻辑块设备，这才是我们最终格式化并挂载使用的部分。使用 `lvcreate` 命令创建。
4.  **物理扩展块（Physical Extent, PE）**：这是VG中分配空间的最小单位，默认大小为4MB。在创建VG时可以通过 `-s` 参数指定其大小（如 `-s 16M`）。

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_42.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_44.png)

**数据流向**：物理设备（磁盘/分区） -> 物理卷（PV） -> 卷组（VG） -> 逻辑卷（LV） -> 文件系统 -> 挂载使用。

### 创建LVM逻辑卷

以下是创建一个完整LVM逻辑卷的步骤：

1.  **准备物理设备**：创建几个分区作为物理设备（例如 `/dev/vdc1`, `/dev/vdc2`, `/dev/vdc3`）。
2.  **创建物理卷（PV）**：`pvcreate /dev/vdc1 /dev/vdc2 /dev/vdc3`
3.  **创建卷组（VG）**：`vgcreate vg_test /dev/vdc1 /dev/vdc2`
4.  **创建逻辑卷（LV）**：`lvcreate -n lv_test -L 150M vg_test`
5.  **格式化逻辑卷**：`mkfs.ext4 /dev/vg_test/lv_test`
6.  **挂载并使用**：`mount /dev/vg_test/lv_test /mnt/lv1`

**常用查看命令**：
*   `pvs`, `vgs`, `lvs`：简要信息。
*   `pvdisplay`, `vgdisplay`, `lvdisplay`：详细信息。

### 扩展LVM逻辑卷

LVM的核心优势在于在线扩展。扩展前，请确保VG有足够的剩余空间。

1.  **扩展卷组（如果需要）**：如果VG空间不足，先将新的PV加入VG。
    ```bash
    vgextend vg_test /dev/vdc3
    ```
2.  **扩展逻辑卷**：扩大LV的容量。
    ```bash
    lvextend -L +50M /dev/vg_test/lv_test   # 增加50M
    # 或
    lvextend -L 200M /dev/vg_test/lv_test    # 扩展到200M
    ```
3.  **扩展文件系统**：LV容量扩大后，必须同步扩展其上的文件系统，否则新增空间无法使用。
    *   **对于ext2/3/4文件系统**：`resize2fs /dev/vg_test/lv_test`
    *   **对于XFS文件系统**：`xfs_growfs /挂载点` （如 `/mnt/lv1`）

**关键点**：必须先扩展LV，再扩展文件系统。顺序不能颠倒。

## 系统启动流程与密码破解 🔑

理解了磁盘和内存管理后，我们深入系统内部，了解Linux的启动流程。这不仅有助于故障排查，也是破解（重置）root密码的基础。

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_46.png)

### Linux系统启动流程概述

1.  **加电自检（POST）**：硬件初始化与检查。
2.  **引导加载器（Boot Loader）**：BIOS/UEFI根据设置找到磁盘上的引导程序，通常是 **GRUB2**（GRand Unified Bootloader）。
3.  **加载内核与初始内存盘**：GRUB2从其配置文件（`/boot/grub2/grub.cfg`）中读取信息，加载Linux内核（`vmlinuz-*`）和初始内存盘镜像（`initramfs-*.img`）。该镜像包含启动初期必需的驱动和工具。
4.  **内核初始化**：内核解压并初始化，挂载根文件系统（最初为只读）。
5.  **启动 systemd**：内核启动第一个用户空间进程，其PID为1。在RHEL8中，这是 `systemd`。
6.  **执行默认目标（target）**：`systemd` 启动 `default.target` 指定的运行级别。这决定了系统进入图形界面（`graphical.target`）还是字符界面（`multi-user.target`）。
7.  **启动服务**：根据目标，启动相应的服务单元（如网络、SSH等）。
8.  **显示登录界面**：最终呈现登录提示符。

### 破解（重置）Root密码

当忘记root密码时，我们可以通过中断启动流程，在单用户模式或紧急模式下重置密码。

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_48.png)

**标准步骤**：

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_50.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_52.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_54.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_56.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_58.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_60.png)

1.  重启系统，在GRUB2菜单界面，按 `e` 键编辑启动条目。
2.  找到以 `linux` 开头的一行，在行末（在 `quiet` 参数之后）添加 `rd.break`。
3.  按 `Ctrl+X` 启动。系统会中断启动，进入一个临时的shell环境。
4.  重新以读写方式挂载根文件系统：`mount -o remount,rw /sysroot`
5.  切换根目录环境：`chroot /sysroot`
6.  修改root密码：`passwd root`，然后输入新密码。
7.  如果系统启用了SELinux，需要创建自动重新标记文件，否则可能无法正常登录：`touch /.autorelabel`
8.  退出chroot环境：`exit`
9.  重启系统：`reboot`

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_62.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_64.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_66.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_68.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_70.png)

重启后，使用新设置的root密码登录即可。

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_72.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_74.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_76.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_78.png)

### 救援模式与紧急模式

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_80.png)

除了破解密码，当系统文件损坏导致无法正常启动时，可以进入救援（Rescue）或紧急（Emergency）模式进行修复。

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_82.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_84.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_86.png)

*   **救援模式**：提供最小的系统环境，并尝试自动挂载根文件系统。进入方法：在GRUB菜单编辑 `linux` 行，末尾添加 `systemd.unit=rescue.target`。
*   **紧急模式**：提供更基础的环境，需要手动挂载一切。进入方法：在GRUB菜单编辑 `linux` 行，末尾添加 `systemd.unit=emergency.target`。

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_88.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_90.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_92.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_94.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_96.png)

在这两种模式下，您可以挂载磁盘、编辑配置文件（如修复错误的 `/etc/fstab`）、恢复被删除的重要文件（如 `/boot/grub2/grub.cfg`）等。

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_98.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_100.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_101.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_103.png)

**手动修复GRUB示例**：
如果 `/boot/grub2/grub.cfg` 丢失，可以在救援模式下或启动时进入GRUB命令行（按 `c` 键），手动输入启动命令：
```
grub> set root=(hd0,msdos1)      # 设置 boot 分区，根据实际情况
grub> linux /vmlinuz-4.18.0-xxx.el8.x86_64 ro root=/dev/mapper/rhel-root
grub> initrd /initramfs-4.18.0-xxx.el8.x86_64.img
grub> boot
```

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_105.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_107.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_109.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_111.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_112.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_114.png)

---

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_116.png)

![](img/040a42d4d0c7bc30a18cd407b44e1ab4_117.png)

本节课中我们一起学习了RHCE认证中三个至关重要的实践技能。我们掌握了如何创建和管理交换分区以优化内存使用；深入理解了LVM的架构和操作，能够创建、扩展逻辑卷，实现存储空间的动态管理；最后，我们剖析了Linux系统的启动流程，并学会了在忘记密码或系统损坏时，通过破解密码和进入救援模式进行故障恢复。这些知识构成了系统管理员核心能力的基础，请务必通过实践熟练掌握。