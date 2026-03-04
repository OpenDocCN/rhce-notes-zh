# Red Hat RHCE 8.0 认证体系课程：RH134：第八章 - Stratis 与 VDO 存储管理 🚀

![](img/4ba04d6fcda0f644b27386745ad80655_0.png)

在本节课中，我们将学习 Red Hat Enterprise Linux 8.0 中的两个高级存储管理特性：**Stratis**（精简配置存储池）和 **VDO**（虚拟数据优化器）。我们将通过实际操作来理解它们的原理、配置和管理方法。

## 概述 📋

![](img/4ba04d6fcda0f644b27386745ad80655_2.png)

Stratis 和 VDO 是 RHEL 8 引入的用于优化存储空间和管理的高级工具。Stratis 提供了一个易于管理的精简配置存储池，而 VDO 则通过数据去重和压缩来减少存储占用。掌握这两项技术对于高效管理企业级存储环境至关重要。

---

## Stratis 存储管理 🗃️

上一节我们概述了课程内容，本节中我们来看看 Stratis 的具体原理和操作。

![](img/4ba04d6fcda0f644b27386745ad80655_4.png)

Stratis 是一种本地存储管理工具，它将文件系统构建在一个精简配置的共享存储池之上。其核心目的是通过池化管理、精简配置和快照等功能，简化高级存储功能的实现。

### Stratis 核心原理

![](img/4ba04d6fcda0f644b27386745ad80655_6.png)

![](img/4ba04d6fcda0f644b27386745ad80655_7.png)

Stratis 守护进程服务名为 `stratisd.service`。它管理一个存储池，池中的文件系统使用精简配置技术，这意味着它们可以呈现比实际物理空间更大的逻辑容量。

![](img/4ba04d6fcda0f644b27386745ad80655_9.png)

![](img/4ba04d6fcda0f644b27386745ad80655_11.png)

![](img/4ba04d6fcda0f644b27386745ad80655_13.png)

### 安装与启动 Stratis

以下是安装和启动 Stratis 服务的步骤。

![](img/4ba04d6fcda0f644b27386745ad80655_15.png)

![](img/4ba04d6fcda0f644b27386745ad80655_17.png)

![](img/4ba04d6fcda0f644b27386745ad80655_18.png)

1.  安装必要的软件包：
    ```bash
    dnf install -y stratis-cli stratisd
    ```
2.  启动并启用 Stratis 服务：
    ```bash
    systemctl enable --now stratisd
    systemctl status stratisd
    ```

![](img/4ba04d6fcda0f644b27386745ad80655_20.png)

![](img/4ba04d6fcda0f644b27386745ad80655_21.png)

![](img/4ba04d6fcda0f644b27386745ad80655_23.png)

![](img/4ba04d6fcda0f644b27386745ad80655_24.png)

### 配置 Stratis 存储池与文件系统

![](img/4ba04d6fcda0f644b27386745ad80655_26.png)

安装完成后，我们可以开始配置 Stratis。

1.  **创建存储池**：使用一个空白磁盘（例如 `/dev/nvme0n3`）创建名为 `testpool` 的存储池。
    ```bash
    stratis pool create testpool /dev/nvme0n3
    stratis pool list
    ```
2.  **在池中创建文件系统**：在 `testpool` 池中创建一个名为 `testfs` 的文件系统。
    ```bash
    stratis filesystem create testpool testfs
    stratis filesystem list
    ```
3.  **永久挂载文件系统**：
    *   首先，查看新文件系统的 UUID：
        ```bash
        stratis filesystem list
        ```
    *   创建挂载点目录：
        ```bash
        mkdir /mnt/testfs
        ```
    *   编辑 `/etc/fstab` 文件进行永久挂载。**关键**在于挂载选项需要指定依赖 `stratisd.service`。
        ```
        UUID=<你的文件系统UUID> /mnt/testfs xfs defaults,x-systemd.requires=stratisd.service 0 0
        ```
    *   执行挂载：
        ```bash
        mount -a
        df -Th
        ```
    此时，你会看到一个显示为 1TB 的精简配置文件系统，而实际物理池可能只有 20GB。

### 管理 Stratis 存储

配置好基础环境后，我们来看看如何进行日常管理。

*   **查看信息**：
    ```bash
    stratis pool list                 # 查看存储池
    stratis filesystem list          # 查看文件系统
    stratis blockdev list            # 查看池中的块设备
    ```
*   **扩展存储池**：向现有池（如 `testpool`）添加新的物理磁盘（如 `/dev/nvme0n4`）。
    ```bash
    stratis pool add-data testpool /dev/nvme0n4
    stratis pool list
    ```
    **重要提示**：务必监控池的实际使用量，避免写入数据超过物理容量，否则可能导致系统问题。

---

## VDO 虚拟数据优化器 💾

![](img/4ba04d6fcda0f644b27386745ad80655_28.png)

![](img/4ba04d6fcda0f644b27386745ad80655_30.png)

![](img/4ba04d6fcda0f644b27386745ad80655_31.png)

上一节我们介绍了 Stratis 存储池的管理，本节中我们来看看另一个存储优化工具 VDO。

![](img/4ba04d6fcda0f644b27386745ad80655_33.png)

![](img/4ba04d6fcda0f644b27386745ad80655_35.png)

VDO（虚拟数据优化器）是一个存储层，它通过**重复数据删除**和**压缩**来优化块设备上的数据存储，从而显著节省磁盘空间。

![](img/4ba04d6fcda0f644b27386745ad80655_37.png)

![](img/4ba04d6fcda0f644b27386745ad80655_39.png)

### VDO 核心原理

VDO 包含两个内核模块：
*   **kvdo**：用于透明地压缩数据。
*   **uds**：用于重复数据删除。

![](img/4ba04d6fcda0f644b27386745ad80655_41.png)

VDO 位于现有块设备（如硬盘、RAID 卷）之上。文件系统则创建在 VDO 卷之上。其数据处理分为三个阶段：零块消除、重复数据删除和压缩。

![](img/4ba04d6fcda0f644b27386745ad80655_43.png)

![](img/4ba04d6fcda0f644b27386745ad80655_45.png)

![](img/4ba04d6fcda0f644b27386745ad80655_47.png)

![](img/4ba04d6fcda0f644b27386745ad80655_49.png)

### 安装 VDO

![](img/4ba04d6fcda0f644b27386745ad80655_51.png)

![](img/4ba04d6fcda0f644b27386745ad80655_53.png)

以下是安装 VDO 所需的软件包。

![](img/4ba04d6fcda0f644b27386745ad80655_55.png)

![](img/4ba04d6fcda0f644b27386745ad80655_57.png)

```bash
dnf install -y kmod-kvdo vdo
```

### 创建与配置 VDO 卷

![](img/4ba04d6fcda0f644b27386745ad80655_59.png)

![](img/4ba04d6fcda0f644b27386745ad80655_61.png)

安装完成后，我们可以创建 VDO 卷。

![](img/4ba04d6fcda0f644b27386745ad80655_63.png)

![](img/4ba04d6fcda0f644b27386745ad80655_65.png)

![](img/4ba04d6fcda0f644b27386745ad80655_67.png)

1.  **创建 VDO 卷**：基于一个物理磁盘（如 `/dev/nvme0n6`）创建逻辑容量大于物理容量的 VDO 卷（例如物理20G，逻辑50G）。
    ```bash
    vdo create --name=testvdo --device=/dev/nvme0n6 --vdoLogicalSize=50G
    ```
2.  **查看 VDO 状态**：
    ```bash
    vdo list
    vdo status --name=testvdo | grep -iE 'compression|deduplication' # 检查功能是否启用
    ```
3.  **在 VDO 卷上创建文件系统并挂载**：
    *   格式化 VDO 卷为 XFS 文件系统：
        ```bash
        mkfs.xfs -K /dev/mapper/testvdo
        ```
    *   查看其 UUID 并编辑 `/etc/fstab` 进行永久挂载：
        ```bash
        blkid /dev/mapper/testvdo
        ```
        在 `/etc/fstab` 中添加：
        ```
        UUID=<VDO卷UUID> /mnt/testvdo xfs defaults 0 0
        ```
    *   创建挂载点并挂载：
        ```bash
        mkdir /mnt/testvdo
        mount -a
        df -Th
        ```
    现在，你会看到一个 50GB 的文件系统，它建立在物理容量仅为 20GB 的 VDO 卷上。

![](img/4ba04d6fcda0f644b27386745ad80655_69.png)

![](img/4ba04d6fcda0f644b27386745ad80655_70.png)

![](img/4ba04d6fcda0f644b27386745ad80655_71.png)

![](img/4ba04d6fcda0f644b27386745ad80655_73.png)

### 验证 VDO 效果

![](img/4ba04d6fcda0f644b27386745ad80655_75.png)

![](img/4ba04d6fcda0f644b27386745ad80655_77.png)

![](img/4ba04d6fcda0f644b27386745ad80655_78.png)

让我们通过实际操作来验证 VDO 的去重和压缩效果。

![](img/4ba04d6fcda0f644b27386745ad80655_80.png)

![](img/4ba04d6fcda0f644b27386745ad80655_82.png)

1.  **监控 VDO 空间使用**：使用人类可读格式查看 VDO 卷的详细状态。
    ```bash
    vdo status --name=testvdo --human-readable
    ```
    输出会显示“逻辑已用”、“物理已用”等数据。
2.  **测试去重功能**：
    *   向 `/mnt/testvdo` 复制一个大文件（如一个 4GB 的 ISO 镜像）到目录 `dir1`。观察 `vdo status` 中“物理已用”空间的增长。
    *   将**同一个文件**再次复制到 `dir2` 目录。你会发现“逻辑已用”空间增加，但“物理已用”空间几乎不变，这证明了重复数据删除在起作用。
3.  **VDO 适用场景**：VDO 非常适合存储重复性高、不常修改的“冷”数据，例如备份数据、虚拟机模板或归档文件。

![](img/4ba04d6fcda0f644b27386745ad80655_84.png)

![](img/4ba04d6fcda0f644b27386745ad80655_86.png)

![](img/4ba04d6fcda0f644b27386745ad80655_88.png)

---

![](img/4ba04d6fcda0f644b27386745ad80655_90.png)

![](img/4ba04d6fcda0f644b27386745ad80655_92.png)

![](img/4ba04d6fcda0f644b27386745ad80655_93.png)

![](img/4ba04d6fcda0f644b27386745ad80655_95.png)

## 总结 🎯

![](img/4ba04d6fcda0f644b27386745ad80655_96.png)

![](img/4ba04d6fcda0f644b27386745ad80655_98.png)

![](img/4ba04d6fcda0f644b27386745ad80655_100.png)

本节课中我们一起学习了 RHEL 8 的两个核心存储高级特性：

![](img/4ba04d6fcda0f644b27386745ad80655_102.png)

1.  **Stratis**：它通过池化管理简化了精简配置文件系统的创建和维护。我们学会了安装 Stratis、创建存储池和文件系统，以及如何扩展存储池。
2.  **VDO**：它通过数据去重和压缩技术，有效提升存储空间的利用率。我们实践了创建 VDO 卷、在其上建立文件系统，并通过测试验证了其空间节省效果。

![](img/4ba04d6fcda0f644b27386745ad80655_104.png)

![](img/4ba04d6fcda0f644b27386745ad80655_106.png)

理解并掌握 Stratis 和 VDO 的配置与管理，对于优化企业存储架构、降低成本至关重要，也是 RHCE 认证考核的重点内容。