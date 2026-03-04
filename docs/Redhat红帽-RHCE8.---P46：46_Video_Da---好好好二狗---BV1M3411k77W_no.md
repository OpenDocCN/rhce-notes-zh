# Redhat红帽 RHCE8.0认证体系课程：P46：Stratis与VDO存储管理教程

![](img/17812b89ca42dd99b9552720499c6729_1.png)

## 概述
在本节课中，我们将学习Red Hat Enterprise Linux 8中的两个高级存储管理特性：Stratis和VDO。Stratis是一个精简配置的共享存储池管理工具，而VDO（虚拟数据优化器）则用于通过压缩和去重技术优化存储空间使用。我们将通过实际操作步骤来掌握它们的配置和使用方法。

---

## Stratis：精简配置存储池

上一节我们概述了课程内容，本节中我们来看看第一个新特性：Stratis。Stratis是一种本地存储管理工具，它将文件系统构建在一个精简配置的共享存储池中，旨在实现精简配置、快照和基于池的高级存储功能。

### 安装与启动Stratis服务
以下是安装和启动Stratis服务的步骤。

![](img/17812b89ca42dd99b9552720499c6729_3.png)

![](img/17812b89ca42dd99b9552720499c6729_5.png)

![](img/17812b89ca42dd99b9552720499c6729_7.png)

![](img/17812b89ca42dd99b9552720499c6729_8.png)

![](img/17812b89ca42dd99b9552720499c6729_9.png)

![](img/17812b89ca42dd99b9552720499c6729_10.png)

1.  安装必要的软件包。
    ```bash
    dnf install -y stratisd stratis-cli
    ```
2.  启动并启用Stratis守护进程服务。
    ```bash
    systemctl enable --now stratisd
    systemctl status stratisd
    ```

![](img/17812b89ca42dd99b9552720499c6729_12.png)

![](img/17812b89ca42dd99b9552720499c6729_14.png)

![](img/17812b89ca42dd99b9552720499c6729_16.png)

### 创建Stratis存储池与文件系统
安装并启动服务后，接下来我们创建存储池和文件系统。

![](img/17812b89ca42dd99b9552720499c6729_18.png)

![](img/17812b89ca42dd99b9552720499c6729_20.png)

![](img/17812b89ca42dd99b9552720499c6729_21.png)

1.  使用一个空白磁盘（例如 `/dev/nvme0n3`）创建名为 `testpool` 的存储池。
    ```bash
    stratis pool create testpool /dev/nvme0n3
    stratis pool list
    ```
2.  在存储池上创建名为 `testfs` 的文件系统。
    ```bash
    stratis filesystem create testpool testfs
    stratis filesystem list
    ```

![](img/17812b89ca42dd99b9552720499c6729_23.png)

![](img/17812b89ca42dd99b9552720499c6729_24.png)

![](img/17812b89ca42dd99b9552720499c6729_25.png)

![](img/17812b89ca42dd99b9552720499c6729_26.png)

![](img/17812b89ca42dd99b9552720499c6729_28.png)

![](img/17812b89ca42dd99b9552720499c6729_29.png)

![](img/17812b89ca42dd99b9552720499c6729_31.png)

### 挂载Stratis文件系统
创建文件系统后，需要将其挂载到系统才能使用。

![](img/17812b89ca42dd99b9552720499c6729_33.png)

1.  从 `stratis filesystem list` 命令输出中获取文件系统的UUID。
2.  编辑 `/etc/fstab` 文件以实现永久挂载。**关键点**：需要在挂载选项中添加 `x-systemd.requires=stratisd.service` 依赖。
    ```bash
    # /etc/fstab 示例条目
    UUID=你的文件系统UUID /mnt/testfs xfs defaults,x-systemd.requires=stratisd.service 0 0
    ```
3.  创建挂载点并挂载文件系统。
    ```bash
    mkdir /mnt/testfs
    mount -a
    df -h
    ```
    此时，您将看到一个显示为1TB的精简配置文件系统，其实际物理空间为20GB。

### 管理Stratis存储池
了解基础操作后，我们来看看如何管理存储池，例如添加新磁盘。

1.  查看当前存储池中的块设备。
    ```bash
    stratis blockdev list
    ```
2.  向现有存储池（如 `testpool`）添加新的空白磁盘（如 `/dev/nvme0n4`）。
    ```bash
    stratis pool add-data testpool /dev/nvme0n4
    ```
3.  添加后，存储池的物理容量会增加，但文件系统的精简配置逻辑大小不变。**重要提示**：写入数据总量切勿超过存储池的实际物理总容量，否则可能导致服务无法启动。

---

## VDO：虚拟数据优化器

![](img/17812b89ca42dd99b9552720499c6729_35.png)

![](img/17812b89ca42dd99b9552720499c6729_37.png)

![](img/17812b89ca42dd99b9552720499c6729_38.png)

![](img/17812b89ca42dd99b9552720499c6729_39.png)

![](img/17812b89ca42dd99b9552720499c6729_40.png)

![](img/17812b89ca42dd99b9552720499c6729_41.png)

![](img/17812b89ca42dd99b9552720499c6729_43.png)

![](img/17812b89ca42dd99b9552720499c6729_45.png)

上一节我们介绍了Stratis，本节中我们来看看第二个新特性：VDO。VDO（虚拟数据优化器）通过在块设备层进行数据压缩和去重，有效减少存储空间占用。

![](img/17812b89ca42dd99b9552720499c6729_47.png)

![](img/17812b89ca42dd99b9552720499c6729_49.png)

### VDO原理简介
VDO包含两个内核模块：
*   **kvdo**：用于透明地压缩数据。
*   **uds**：用于重复数据删除。

![](img/17812b89ca42dd99b9552720499c6729_51.png)

VDO按顺序处理数据以节省空间：
1.  **零块消除**：过滤掉全零的数据块，仅在元数据中记录。
2.  **重复数据删除**：消除冗余的数据块。
3.  **压缩**：使用LZ4算法压缩数据块。

![](img/17812b89ca42dd99b9552720499c6729_53.png)

![](img/17812b89ca42dd99b9552720499c6729_55.png)

![](img/17812b89ca42dd99b9552720499c6729_57.png)

![](img/17812b89ca42dd99b9552720499c6729_59.png)

![](img/17812b89ca42dd99b9552720499c6729_60.png)

![](img/17812b89ca42dd99b9552720499c6729_62.png)

![](img/17812b89ca42dd99b9552720499c6729_64.png)

### 安装VDO与创建卷
以下是配置VDO的步骤。

![](img/17812b89ca42dd99b9552720499c6729_66.png)

![](img/17812b89ca42dd99b9552720499c6729_67.png)

![](img/17812b89ca42dd99b9552720499c6729_69.png)

![](img/17812b89ca42dd99b9552720499c6729_71.png)

![](img/17812b89ca42dd99b9552720499c6729_73.png)

![](img/17812b89ca42dd99b9552720499c6729_75.png)

1.  安装VDO软件包。
    ```bash
    dnf install -y vdo kmod-kvdo
    ```
2.  在一个空白磁盘（例如 `/dev/nvme0n6`）上创建VDO卷。可以指定一个大于物理容量的逻辑大小，例如在20GB物理盘上创建50GB逻辑卷。
    ```bash
    vdo create --name=testvdo --device=/dev/nvme0n6 --vdoLogicalSize=50G
    ```
3.  查看创建的VDO卷状态，确认压缩和去重功能已启用。
    ```bash
    vdo status --name=testvdo | grep -iE "compression|deduplication"
    vdo status --name=testvdo --human-readable
    ```

![](img/17812b89ca42dd99b9552720499c6729_77.png)

![](img/17812b89ca42dd99b9552720499c6729_79.png)

### 使用VDO卷
创建VDO卷后，可以像普通块设备一样格式化和使用它。

1.  在VDO卷上创建文件系统。
    ```bash
    mkfs.xfs -K /dev/mapper/testvdo
    ```
2.  获取VDO卷的UUID并编辑 `/etc/fstab` 实现永久挂载。
    ```bash
    blkid /dev/mapper/testvdo
    # 将以下行添加到 /etc/fstab， UUID替换为实际值
    # UUID=你的VDO卷UUID /mnt/testvdo xfs defaults 0 0
    ```
3.  创建挂载点并挂载。
    ```bash
    mkdir /mnt/testvdo
    mount -a
    df -h /mnt/testvdo
    ```
    此时，`df` 命令将显示50GB的逻辑空间。

![](img/17812b89ca42dd99b9552720499c6729_81.png)

![](img/17812b89ca42dd99b9552720499c6729_83.png)

![](img/17812b89ca42dd99b9552720499c6729_84.png)

![](img/17812b89ca42dd99b9552720499c6729_86.png)

### 验证VDO效果
我们可以通过写入数据来验证VDO的压缩和去重效果。

![](img/17812b89ca42dd99b9552720499c6729_88.png)

![](img/17812b89ca42dd99b9552720499c6729_90.png)

![](img/17812b89ca42dd99b9552720499c6729_91.png)

![](img/17812b89ca42dd99b9552720499c6729_92.png)

![](img/17812b89ca42dd99b9552720499c6729_93.png)

![](img/17812b89ca42dd99b9552720499c6729_94.png)

![](img/17812b89ca42dd99b9552720499c6729_95.png)

![](img/17812b89ca42dd99b9552720499c6729_96.png)

![](img/17812b89ca42dd99b9552720499c6729_97.png)

1.  **测试零块消除**：向VDO卷写入大量全零数据，观察实际空间占用增长远小于逻辑写入量。
    ```bash
    dd if=/dev/zero of=/mnt/testvdo/zero.file bs=1G count=10
    vdo status --name=testvdo --human-readable
    ```
2.  **测试重复数据删除**：复制相同的文件（例如一个ISO镜像）到VDO卷内的不同目录。第二次复制时，逻辑空间增加，但实际物理占用几乎不变。
    ```bash
    cp /path/to/large.iso /mnt/testvdo/dir1/
    cp /path/to/large.iso /mnt/testvdo/dir2/
    vdo status --name=testvdo --human-readable
    ```

![](img/17812b89ca42dd99b9552720499c6729_99.png)

![](img/17812b89ca42dd99b9552720499c6729_101.png)

![](img/17812b89ca42dd99b9552720499c6729_102.png)

![](img/17812b89ca42dd99b9552720499c6729_103.png)

### VDO适用场景
VDO特别适用于以下场景：
*   存储重复性高的数据。
*   存储不常访问的冷数据。
*   数据备份场景。
*   需要精简配置的环境。

![](img/17812b89ca42dd99b9552720499c6729_105.png)

![](img/17812b89ca42dd99b9552720499c6729_106.png)

![](img/17812b89ca42dd99b9552720499c6729_107.png)

![](img/17812b89ca42dd99b9552720499c6729_109.png)

![](img/17812b89ca42dd99b9552720499c6729_110.png)

![](img/17812b89ca42dd99b9552720499c6729_111.png)

![](img/17812b89ca42dd99b9552720499c6729_113.png)

---

![](img/17812b89ca42dd99b9552720499c6729_114.png)

![](img/17812b89ca42dd99b9552720499c6729_116.png)

![](img/17812b89ca42dd99b9552720499c6729_117.png)

![](img/17812b89ca42dd99b9552720499c6729_119.png)

![](img/17812b89ca42dd99b9552720499c6729_121.png)

## 总结
本节课中我们一起学习了Red Hat Enterprise Linux 8的两个核心存储高级特性。

![](img/17812b89ca42dd99b9552720499c6729_123.png)

![](img/17812b89ca42dd99b9552720499c6729_125.png)

![](img/17812b89ca42dd99b9552720499c6729_127.png)

![](img/17812b89ca42dd99b9552720499c6729_129.png)

我们首先学习了 **Stratis**，掌握了如何创建和管理基于精简配置的共享存储池及文件系统，并了解了其挂载和扩容的注意事项。

![](img/17812b89ca42dd99b9552720499c6729_131.png)

![](img/17812b89ca42dd99b9552720499c6729_133.png)

![](img/17812b89ca42dd99b9552720499c6729_134.png)

![](img/17812b89ca42dd99b9552720499c6729_135.png)

![](img/17812b89ca42dd99b9552720499c6729_136.png)

接着，我们深入探讨了 **VDO（虚拟数据优化器）**，理解了其通过压缩和去重优化存储空间的原理，并通过实践操作验证了其在处理零数据和重复数据时的空间节省效果。

![](img/17812b89ca42dd99b9552720499c6729_138.png)

通过本课的学习，您应该能够配置和使用Stratis和VDO来有效管理Linux系统的存储资源。