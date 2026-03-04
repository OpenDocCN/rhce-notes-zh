# Linux就该这么学：第34期：第10节课 - 硬盘管理进阶与磁盘阵列基础

![](img/8e40d72c40db46d8c99dcaffa91217a3_1.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_3.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_5.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_7.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_9.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_11.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_13.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_15.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_17.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_19.png)

## 概述

![](img/8e40d72c40db46d8c99dcaffa91217a3_21.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_23.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_25.png)

在本节课中，我们将深入学习Linux系统中硬盘管理的高级技术。课程将涵盖交换分区的创建与管理、磁盘配额技术的配置、虚拟数据优化器（VDO）的使用、软硬链接文件的原理与操作，并初步介绍RAID磁盘阵列组的基础概念。我们将通过理论与实践相结合的方式，确保每位初学者都能掌握这些核心技能。

![](img/8e40d72c40db46d8c99dcaffa91217a3_27.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_29.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_31.png)

---

![](img/8e40d72c40db46d8c99dcaffa91217a3_33.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_35.png)

## 6.6 交换分区（Swap）的管理

![](img/8e40d72c40db46d8c99dcaffa91217a3_37.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_39.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_41.png)

上一节我们介绍了硬盘的基础分区、格式化与挂载操作。本节中，我们来看看如何管理一个特殊的硬盘分区——交换分区。

![](img/8e40d72c40db46d8c99dcaffa91217a3_43.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_45.png)

交换分区（Swap）的作用是当物理内存（RAM）资源紧张时，将内存中不常用的数据临时存储到硬盘的指定区域，从而释放物理内存供更紧急的任务使用。这相当于在硬盘上开辟了一块虚拟内存区域。

![](img/8e40d72c40db46d8c99dcaffa91217a3_47.png)

### 查看当前内存与交换分区状态

![](img/8e40d72c40db46d8c99dcaffa91217a3_49.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_51.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_53.png)

首先，我们需要了解系统当前的内存使用情况。使用 `free` 命令可以查看：

![](img/8e40d72c40db46d8c99dcaffa91217a3_55.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_57.png)

```bash
free -h
```

![](img/8e40d72c40db46d8c99dcaffa91217a3_59.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_61.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_63.png)

**命令解释**：
*   `free`：显示内存使用情况。
*   `-h`：以人类易读的格式（如GB， MB）显示结果。

![](img/8e40d72c40db46d8c99dcaffa91217a3_65.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_67.png)

输出结果中，`Mem` 行显示物理内存信息，`Swap` 行则显示交换分区的信息，包括总大小、已使用量和空闲量。

![](img/8e40d72c40db46d8c99dcaffa91217a3_69.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_71.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_73.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_75.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_77.png)

### 为交换分区扩容

![](img/8e40d72c40db46d8c99dcaffa91217a3_79.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_81.png)

如果确认需要增加交换分区空间，可以按照以下步骤操作：

![](img/8e40d72c40db46d8c99dcaffa91217a3_83.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_85.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_87.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_89.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_91.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_93.png)

1.  **添加新硬盘**：为虚拟机添加一块新的硬盘。建议在关机状态下进行此操作，以确保稳定性。
2.  **对新硬盘进行分区**：使用 `fdisk` 或 `gdisk` 命令对新硬盘进行分区。可以创建主分区或逻辑分区作为交换分区。
    *   示例：使用 `fdisk /dev/sdb` 进入交互界面。
    *   输入 `n` 创建新分区。
    *   选择分区类型（主分区 `p` 或扩展分区 `e`/逻辑分区 `l`）。
    *   设置分区大小（例如：`+2G`）。
3.  **修改分区类型**：在 `fdisk` 交互界面中，输入 `t` 修改分区类型。交换分区的类型代码是 `82`。
4.  **保存并退出**：输入 `w` 保存分区表并退出。
5.  **格式化交换分区**：使用专门的命令格式化分区为交换分区格式。
    ```bash
    mkswap /dev/sdb1
    ```
    **命令解释**：
    *   `mkswap`：格式化设备为交换分区。
    *   `/dev/sdb1`：新创建的分区设备文件。
6.  **启用交换分区**：使用 `swapon` 命令将格式化好的分区挂载到系统中。
    ```bash
    swapon /dev/sdb1
    ```
7.  **永久生效配置**：以上操作在重启后会失效。需要编辑 `/etc/fstab` 文件，添加以下行以实现开机自动挂载：
    ```
    /dev/sdb1 swap swap defaults 0 0
    ```
    **配置解释**：
    *   第一列：设备名称。
    *   第二列：挂载点（交换分区无需挂载点，写 `swap`）。
    *   第三列：文件系统类型（`swap`）。
    *   第四列：挂载参数（`defaults`）。
    *   第五、六列：备份和自检选项（通常为 `0 0`）。

![](img/8e40d72c40db46d8c99dcaffa91217a3_95.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_97.png)

完成上述步骤后，重启系统并使用 `free -h` 验证新的交换分区是否已生效。

![](img/8e40d72c40db46d8c99dcaffa91217a3_99.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_101.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_103.png)

---

![](img/8e40d72c40db46d8c99dcaffa91217a3_105.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_107.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_109.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_111.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_113.png)

## 6.7 磁盘配额（Disk Quota）

![](img/8e40d72c40db46d8c99dcaffa91217a3_115.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_117.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_119.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_121.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_123.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_125.png)

在多人使用同一台Linux服务器时，为了防止个别用户过度占用磁盘空间，影响系统稳定和其他用户正常使用，我们需要使用磁盘配额技术。

![](img/8e40d72c40db46d8c99dcaffa91217a3_127.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_129.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_131.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_133.png)

磁盘配额可以针对用户或用户组，限制其在特定文件系统（分区）上所能使用的磁盘空间大小（块限制）和文件数量（inode限制）。

![](img/8e40d72c40db46d8c99dcaffa91217a3_135.png)

### 启用磁盘配额支持

![](img/8e40d72c40db46d8c99dcaffa91217a3_137.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_139.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_141.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_143.png)

磁盘配额功能需要在内核和文件系统层面启用。以XFS文件系统为例：

![](img/8e40d72c40db46d8c99dcaffa91217a3_145.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_147.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_149.png)

1.  **编辑 `/etc/fstab` 文件**，找到需要启用配额的挂载项，在 `defaults` 挂载参数后添加 `uquota`（针对用户）或 `gquota`（针对组）。
    ```
    /dev/sdb1 /boot xfs defaults,uquota 0 0
    ```
2.  **重新挂载文件系统** 或重启系统使配置生效。
    ```bash
    mount -o remount /boot
    # 或
    reboot
    ```
3.  **验证配额是否启用**：
    ```bash
    mount | grep boot
    ```
    在输出中应能看到 `uquota` 参数。

![](img/8e40d72c40db46d8c99dcaffa91217a3_151.png)

### 配置用户磁盘配额

![](img/8e40d72c40db46d8c99dcaffa91217a3_153.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_155.png)

以下是为特定用户配置磁盘配额的步骤：

![](img/8e40d72c40db46d8c99dcaffa91217a3_157.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_159.png)

1.  **设置实验环境**：确保配额目录（如 `/boot`）对测试用户有写入权限。
    ```bash
    chmod -R 777 /boot
    ```
2.  **使用 `xfs_quota` 命令配置配额**：
    ```bash
    xfs_quota -x -c 'limit bsoft=3m bhard=6m isoft=3 ihard=6 linuxprobe' /boot
    ```
    **命令解释**：
    *   `xfs_quota`：XFS文件系统的配额管理工具。
    *   `-x`：启用专家模式。
    *   `-c`：后面直接跟随要执行的命令。
    *   `limit`：设置限制。
    *   `bsoft=3m bhard=6m`：设置磁盘容量的软限制为3MB，硬限制为6MB。
    *   `isoft=3 ihard=6`：设置文件数量的软限制为3个，硬限制为6个。
    *   `linuxprobe`：要限制的用户名。
    *   `/boot`：要实施配额的挂载点。

![](img/8e40d72c40db46d8c99dcaffa91217a3_161.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_163.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_165.png)

### 验证配额效果

![](img/8e40d72c40db46d8c99dcaffa91217a3_167.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_169.png)

切换到被限制的用户（如 `linuxprobe`），尝试在 `/boot` 目录下创建超过限制的文件或占用超过限制的空间，系统将根据软硬限制给出警告或禁止操作。

![](img/8e40d72c40db46d8c99dcaffa91217a3_171.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_173.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_175.png)

### 编辑配额限制

![](img/8e40d72c40db46d8c99dcaffa91217a3_177.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_179.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_181.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_183.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_185.png)

如果需要修改已配置的配额，可以使用 `edquota` 命令：
```bash
edquota -u linuxprobe
```
此命令会打开一个配置文件，直接修改其中的软硬限制数值即可。

![](img/8e40d72c40db46d8c99dcaffa91217a3_187.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_189.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_191.png)

---

## 6.8 虚拟数据优化器（VDO）

![](img/8e40d72c40db46d8c99dcaffa91217a3_193.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_195.png)

虚拟数据优化器（VDO）是一种通过压缩和去重技术来节省磁盘空间的技术。它特别适用于存储大量重复数据或可压缩数据的场景。

![](img/8e40d72c40db46d8c99dcaffa91217a3_197.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_199.png)

### VDO的工作原理

![](img/8e40d72c40db46d8c99dcaffa91217a3_201.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_203.png)

VDO在逻辑层创建一个比物理磁盘更大的虚拟卷。当数据写入时，VDO会：
1.  **消除重复数据**：如果写入的数据块已经存在，则只保存一个副本并创建引用。
2.  **压缩数据**：使用压缩算法减少数据占用的物理空间。

![](img/8e40d72c40db46d8c99dcaffa91217a3_205.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_207.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_209.png)

### 配置VDO卷

![](img/8e40d72c40db46d8c99dcaffa91217a3_211.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_213.png)

以下是在新硬盘上创建VDO卷的步骤：

![](img/8e40d72c40db46d8c99dcaffa91217a3_215.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_217.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_219.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_221.png)

1.  **安装VDO软件包**（如果系统未预装）：
    ```bash
    yum install vdo kmod-kvdo -y
    ```
2.  **创建VDO卷**：
    ```bash
    vdo create --name=vdo0 --device=/dev/sdc --vdoLogicalSize=200G
    ```
    **命令解释**：
    *   `--name=vdo0`：指定VDO卷的名称为 `vdo0`。
    *   `--device=/dev/sdc`：指定底层物理设备。
    *   `--vdoLogicalSize=200G`：指定逻辑大小为200GB（远大于物理大小）。
3.  **格式化VDO卷**：
    ```bash
    mkfs.xfs /dev/mapper/vdo0
    ```
4.  **挂载并使用VDO卷**：
    ```bash
    mkdir /vdo
    mount /dev/mapper/vdo0 /vdo
    ```
5.  **配置开机自动挂载**：编辑 `/etc/fstab` 文件，添加挂载信息。
    ```
    /dev/mapper/vdo0 /vdo xfs defaults,_netdev 0 0
    ```
    **注意**：需要添加 `_netdev` 参数，防止网络未就绪时挂载失败。

![](img/8e40d72c40db46d8c99dcaffa91217a3_223.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_225.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_227.png)

创建完成后，使用 `df -h` 查看，逻辑卷显示为200G，但实际占用的物理空间会根据存储数据的重复率和可压缩性动态变化。可以使用 `vdostats --human-readable` 命令查看VDO卷的实际使用情况。

![](img/8e40d72c40db46d8c99dcaffa91217a3_229.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_231.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_233.png)

---

## 6.9 软链接与硬链接

![](img/8e40d72c40db46d8c99dcaffa91217a3_235.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_237.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_239.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_241.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_243.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_245.png)

在Linux中，链接文件允许一个文件有多个访问路径，类似于Windows的“快捷方式”。链接分为软链接（符号链接）和硬链接。

![](img/8e40d72c40db46d8c99dcaffa91217a3_247.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_249.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_251.png)

### 软链接（Symbolic Link）

![](img/8e40d72c40db46d8c99dcaffa91217a3_253.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_255.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_257.png)

*   **创建命令**：`ln -s 源文件 链接文件`
*   **特点**：
    *   类似于Windows快捷方式。
    *   是一个独立的文件，内容是指向源文件路径的指针。
    *   可以跨文件系统创建。
    *   可以链接目录。
    *   如果删除源文件，软链接将失效（成为“断链”）。

![](img/8e40d72c40db46d8c99dcaffa91217a3_259.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_261.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_263.png)

**示例**：
```bash
ln -s /etc/passwd /tmp/passwd_link
ls -l /tmp/passwd_link # 文件类型显示为 'l'
```

![](img/8e40d72c40db46d8c99dcaffa91217a3_265.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_267.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_269.png)

### 硬链接（Hard Link）

*   **创建命令**：`ln 源文件 链接文件`
*   **特点**：
    *   直接指向源文件的inode（数据块指针）。
    *   与源文件共享相同的inode编号和数据块。
    *   无法跨文件系统创建。
    *   无法链接目录。
    *   删除源文件，只要还有硬链接存在，文件数据就不会被真正删除（inode引用计数减1，直到为0才删除）。

![](img/8e40d72c40db46d8c99dcaffa91217a3_271.png)

![](img/8e40d72c40db46d8c99dcaffa91217a3_273.png)

**示例**：
```bash
ln /etc/passwd /tmp/passwd_hardlink
ls -li /etc/passwd /tmp/passwd_hardlink # 查看inode号，两者相同
```

### 核心概念与验证

理解硬链接的关键在于理解inode。每个文件都有一个唯一的inode，存储文件的元数据（权限、所有者、时间戳等）和指向数据块的指针。

使用 `ls -li` 命令查看文件时，第二列的数字就是“链接数”，即有多少个文件名指向这个inode。新建一个硬链接，该数字加1；删除一个硬链接（或源文件），该数字减1。只有当链接数变为0时，文件的数据块才会被系统回收。

---

## 7.1 RAID磁盘阵列组简介

随着数据量的增长，单块硬盘在性能、容量和可靠性上可能成为瓶颈。RAID（Redundant Array of Independent Disks，独立磁盘冗余阵列）技术通过将多块硬盘组合起来，形成一个逻辑上的大硬盘，从而提升性能、增加容量或提供数据冗余。

### 常见RAID级别

以下是几种最常用的RAID级别对比：

| RAID级别 | 最少磁盘数 | 容错能力 | 读取性能 | 写入性能 | 可用容量 | 特点 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **RAID 0** | 2 | 无（1块坏全坏） | **高**（并行） | **高**（并行） | N * 单盘容量 | **条带化**。纯提升性能，无冗余。 |
| **RAID 1** | 2 | **高**（允许坏1块） | 高（可并行读） | 一般 | 单盘容量 | **镜像**。数据完全复制，提供高冗余，成本高。 |
| **RAID 5** | 3 | 有（允许坏1块） | 高 | 一般（需计算校验） | (N-1) * 单盘容量 | **分布式奇偶校验**。兼顾性能、容量和冗余，常用。 |
| **RAID 10** | 4 | **高**（每组镜像允许坏1块） | **高** | **高** | (N/2) * 单盘容量 | **先镜像，再条带**。结合RAID1和RAID0优点，性能与冗余俱佳。 |

**公式与概念**：
*   **条带化（Striping）**：数据被分割成块，交替写入多个磁盘。**RAID 0** 的核心，提升性能。
    *   理论传输速率 ≈ 单盘速率 × 磁盘数
*   **镜像（Mirroring）**：相同的数据同时写入两块磁盘。**RAID 1** 的核心，提供冗余。
*   **奇偶校验（Parity）**：通过算法计算校验信息，并与数据一起分布存储在多块盘中。当一块盘失效时，可利用其余盘的数据和校验信息恢复数据。**RAID 5** 的核心。

### 热备盘（Hot Spare）

热备盘是一块空闲的、不存储数据的硬盘。当RAID阵列中的某块成员盘发生故障时，热备盘会自动顶替故障盘，并开始重建数据，无需人工立即干预，提高了系统的可用性。

### 选择建议

*   **追求极致性能，不关心数据安全**：RAID 0（如视频编辑缓存）。
*   **追求数据安全，可接受较高成本**：RAID 1 或 RAID 10。
*   **平衡性能、安全与成本**：RAID 5。
*   **企业关键业务，要求高可用**：RAID 10 + 热备盘。

---

## 总结

本节课中我们一起学习了Linux硬盘管理的多项进阶技术。我们掌握了如何创建和管理交换分区以优化内存使用；学会了配置磁盘配额来合理分配存储资源；体验了VDO技术通过压缩和去重来高效利用磁盘空间；理解了软硬链接的原理与区别，并学会了如何创建它们；最后，我们初步探讨了RAID磁盘阵列技术的基础概念、常见级别及其应用场景。

![](img/8e40d72c40db46d8c99dcaffa91217a3_275.png)

这些知识是构建稳定、高效、可管理的Linux服务器环境的重要组成部分。下一节课，我们将进入实战环节，亲手配置RAID阵列和逻辑卷管理器（LVM），请做好准备。