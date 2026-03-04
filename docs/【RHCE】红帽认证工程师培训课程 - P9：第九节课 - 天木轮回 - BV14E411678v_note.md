# RHCE红帽认证工程师培训课程：P9：磁盘管理与RAID技术

![](img/7709a8e97f301cb3f53e3bdd06bb752e_1.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_3.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_4.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_6.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_7.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_8.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_9.png)

在本节课中，我们将深入学习Linux系统中的磁盘管理，包括分区、格式化、挂载、磁盘配额以及RAID磁盘阵列技术。这些是系统管理员日常工作中必须掌握的核心技能。

![](img/7709a8e97f301cb3f53e3bdd06bb752e_11.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_12.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_14.png)

## 磁盘管理基础回顾

上一节我们介绍了Linux系统的基本架构，本节中我们来看看如何管理物理存储设备。管理一块新硬盘通常需要三个步骤：分区、格式化和挂载。

### 分区操作

![](img/7709a8e97f301cb3f53e3bdd06bb752e_16.png)

分区是将一块物理硬盘逻辑上划分为多个独立区域的过程。在Linux中，我们使用 `fdisk` 命令进行交互式分区。

![](img/7709a8e97f301cb3f53e3bdd06bb752e_18.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_19.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_20.png)

以下是使用 `fdisk` 对 `/dev/sdb` 硬盘进行分区的关键步骤：

```bash
fdisk /dev/sdb
```
在 `fdisk` 交互界面中：
*   输入 `n` 创建新分区。
*   选择分区类型（主分区 `p` 或扩展分区 `e`）。
*   指定分区编号和大小。
*   输入 `w` 保存并退出。

分区完成后，有时需要执行 `partprobe` 命令或重启系统，以使内核重新读取分区表。

![](img/7709a8e97f301cb3f53e3bdd06bb752e_22.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_24.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_26.png)

### 格式化操作

![](img/7709a8e97f301cb3f53e3bdd06bb752e_28.png)

格式化是在分区上创建文件系统，以便操作系统能够识别和存储数据。常用的命令是 `mkfs`。

![](img/7709a8e97f301cb3f53e3bdd06bb752e_30.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_32.png)

例如，将 `/dev/sdb1` 分区格式化为 ext4 文件系统：
```bash
mkfs.ext4 /dev/sdb1
```

### 挂载操作

挂载是将一个设备（如分区）关联到目录树中的某个目录，以便用户访问其中的数据。

挂载命令的基本格式为：
```bash
mount /dev/sdb1 /mnt/mydata
```
其中 `/dev/sdb1` 是设备文件，`/mnt/mydata` 是挂载点目录。

为了使挂载在系统重启后依然生效，需要编辑 `/etc/fstab` 文件，添加如下格式的配置行：
```
/dev/sdb1 /mnt/mydata ext4 defaults 0 0
```

## 交换分区管理

当物理内存不足时，系统可以使用硬盘空间来临时充当内存，这部分空间称为交换分区。

创建和启用交换分区的步骤如下：
1.  使用 `fdisk` 创建一个类型为 `Linux swap` 的分区（例如 `/dev/sdb5`）。
2.  使用 `mkswap` 命令格式化该分区：
    ```bash
    mkswap /dev/sdb5
    ```
3.  使用 `swapon` 命令启用交换分区：
    ```bash
    swapon /dev/sdb5
    ```
4.  同样，将配置写入 `/etc/fstab` 以实现开机自动启用：
    ```
    /dev/sdb5 swap swap defaults 0 0
    ```

可以使用 `free -m` 命令查看交换分区的使用情况。

## 磁盘配额配置

磁盘配额用于限制用户或组在特定文件系统上可使用的磁盘空间或文件数量，防止资源被过度占用。

![](img/7709a8e97f301cb3f53e3bdd06bb752e_34.png)

配置XFS文件系统磁盘配额的步骤如下：
1.  在 `/etc/fstab` 文件中，为需要启用配额的挂载点添加 `uquota` 参数（对于ext4文件系统，参数为 `usrquota`）。
2.  重新挂载文件系统或重启。
3.  使用 `xfs_quota` 命令设置配额限制。

例如，限制用户 `linuxprobe` 在 `/boot` 目录下只能使用 6MB 空间和创建 6 个文件：
```bash
xfs_quota -x -c ‘limit bsoft=3m bhard=6m isoft=3 ihard=6 linuxprobe‘ /boot
```
*   `bsoft`/`bhard`: 磁盘容量的软/硬限制。
*   `isoft`/`ihard`: 文件数量的软/硬限制。

![](img/7709a8e97f301cb3f53e3bdd06bb752e_36.png)

使用 `edquota -u username` 命令可以编辑已存在的用户配额。

![](img/7709a8e97f301cb3f53e3bdd06bb752e_38.png)

## 链接文件

链接文件类似于Windows系统中的快捷方式，分为软链接和硬链接。

![](img/7709a8e97f301cb3f53e3bdd06bb752e_40.png)

**软链接（符号链接）**：
*   创建命令：`ln -s 源文件 链接文件`
*   特点：是一个独立的文件，仅包含指向源文件的路径指针。删除源文件后，软链接失效。

**硬链接**：
*   创建命令：`ln 源文件 链接文件`
*   特点：与源文件共享相同的inode和数据块。删除源文件后，只要硬链接存在，数据依然可访问。不能为目录创建硬链接，也不能跨文件系统创建。

使用 `ls -li` 命令查看文件时，第二列的数值表示该文件的硬链接计数。

## RAID磁盘阵列技术

RAID技术通过将多块硬盘组合起来，以提升性能、增加容量或提供数据冗余。

### 常见RAID级别

以下是几种常见的RAID级别及其特性：

*   **RAID 0（条带化）**：
    *   **原理**：数据分散存储在多个磁盘上。
    *   **优点**：读写性能提升显著。
    *   **缺点**：无冗余，任何一块磁盘损坏将导致所有数据丢失。
    *   **可用容量**：所有磁盘容量之和。

*   **RAID 1（镜像）**：
    *   **原理**：相同的数据完全复制到另一块磁盘。
    *   **优点**：数据安全性高。
    *   **缺点**：成本高，磁盘利用率只有50%。
    *   **可用容量**：单块磁盘容量。

![](img/7709a8e97f301cb3f53e3bdd06bb752e_42.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_43.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_45.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_46.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_47.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_48.png)

*   **RAID 5（带奇偶校验的条带化）**：
    *   **原理**：数据与奇偶校验信息交替存储在多个磁盘上。
    *   **优点**：兼顾性能、安全性和成本。允许损坏一块磁盘而不丢失数据。
    *   **缺点**：写入性能有开销。
    *   **可用容量**：(N-1) * 单盘容量（N为磁盘总数）。

![](img/7709a8e97f301cb3f53e3bdd06bb752e_49.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_51.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_53.png)

*   **RAID 10（先镜像再条带化）**：
    *   **原理**：先做RAID 1镜像对，再将多个镜像对组成RAID 0。
    *   **优点**：兼具RAID 1的安全性和RAID 0的性能。
    *   **缺点**：成本最高。
    *   **可用容量**：(N/2) * 单盘容量（N为磁盘总数）。

### 使用 `mdadm` 配置软RAID

在Linux中，可以使用 `mdadm` 工具配置软件RAID。

**创建RAID 10阵列**：
```bash
mdadm -Cv /dev/md0 -n 4 -l 10 /dev/sd[b-e]
```
*   `-Cv`: 创建阵列并显示详细信息。
*   `/dev/md0`: 阵列设备名称。
*   `-n 4`: 使用4块活动盘。
*   `-l 10`: RAID级别为10。
*   `/dev/sd[b-e]`: 使用的物理磁盘。

创建后，需要格式化和挂载才能使用。

![](img/7709a8e97f301cb3f53e3bdd06bb752e_55.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_56.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_58.png)

**模拟磁盘故障与恢复**：
1.  标记某块磁盘为故障盘并从阵列中移除：
    ```bash
    mdadm /dev/md0 -f /dev/sde
    mdadm /dev/md0 -r /dev/sde
    ```
2.  更换新硬盘后，将其添加到阵列中，数据会自动重建：
    ```bash
    mdadm /dev/md0 -a /dev/sde
    ```

![](img/7709a8e97f301cb3f53e3bdd06bb752e_60.png)

### 配置热备盘

![](img/7709a8e97f301cb3f53e3bdd06bb752e_62.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_64.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_65.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_67.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_69.png)

热备盘是一块空闲磁盘，当阵列中某块活动盘故障时，它能自动顶替并开始数据重建。

![](img/7709a8e97f301cb3f53e3bdd06bb752e_71.png)

创建带有一块热备盘的RAID 5阵列：
```bash
mdadm -Cv /dev/md0 -n 3 -l 5 -x 1 /dev/sd[b-e]
```
*   `-x 1`: 指定1块热备盘。

当活动盘发生故障时，热备盘会自动启用并开始同步数据，无需手动干预。

![](img/7709a8e97f301cb3f53e3bdd06bb752e_73.png)

可以使用 `mdadm -D /dev/md0` 查看阵列的详细信息，包括同步进度。

---

![](img/7709a8e97f301cb3f53e3bdd06bb752e_75.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_76.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_78.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_79.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_81.png)

![](img/7709a8e97f301cb3f53e3bdd06bb752e_82.png)

本节课中我们一起学习了Linux磁盘管理的全套流程，从基础的分区、格式化、挂载，到高级的交换分区、磁盘配额和链接文件。最后，我们深入探讨了RAID磁盘阵列技术的原理、不同级别的优劣以及如何使用 `mdadm` 工具配置软RAID阵列和热备盘。这些知识对于构建稳定、高效、安全的存储系统至关重要。