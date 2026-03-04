# Linux运维培训教程：1：磁盘管理进阶与LVM逻辑卷

![](img/0c3a8aa874ae312d00c9dfde73219854_1.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_3.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_5.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_7.png)

在本节课中，我们将要学习磁盘管理的进阶知识，包括如何实现开机自动挂载、GPT分区格式的使用，以及LVM逻辑卷管理的基础概念和优势。这些技能对于管理服务器存储空间至关重要。

![](img/0c3a8aa874ae312d00c9dfde73219854_9.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_11.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_13.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_15.png)

上一节我们介绍了磁盘的基本分区、格式化和临时挂载操作。本节中我们来看看如何让挂载在系统重启后依然有效。

![](img/0c3a8aa874ae312d00c9dfde73219854_17.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_19.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_21.png)

## 开机自动挂载

![](img/0c3a8aa874ae312d00c9dfde73219854_23.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_25.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_27.png)

我们之前使用 `mount` 命令进行的挂载是临时挂载。其优点是立即生效，但缺点是系统重启后，挂载就会失效。为了实现持久化挂载，我们需要配置开机自动挂载。

开机自动挂载通过修改 `/etc/fstab` 配置文件实现。系统启动时会自动读取此文件，并将其中指定的文件系统挂载到指定目录。

![](img/0c3a8aa874ae312d00c9dfde73219854_29.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_31.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_33.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_35.png)

打开 `/etc/fstab` 文件：
```bash
vi /etc/fstab
```

![](img/0c3a8aa874ae312d00c9dfde73219854_37.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_39.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_41.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_43.png)

该文件每一行代表一个自动挂载项，共分为6列，以空格分隔。

![](img/0c3a8aa874ae312d00c9dfde73219854_45.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_47.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_49.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_51.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_53.png)

以下是每一列的含义：
1.  **设备路径**：要挂载的分区设备，例如 `/dev/sdb1`。
2.  **挂载点**：分区要挂载到的目录路径，例如 `/db_back`。
3.  **文件系统类型**：分区的文件系统格式，例如 `xfs`。
4.  **挂载参数**：挂载时使用的选项。`defaults` 代表使用默认参数（包含读写、自动挂载等）。
5.  **备份标记**：`0` 表示不使用 `dump` 命令备份该分区。
6.  **开机检查顺序**：`0` 表示开机时不使用 `fsck` 检查该文件系统。

![](img/0c3a8aa874ae312d00c9dfde73219854_55.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_57.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_59.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_61.png)

例如，添加以下行以实现 `/dev/sdb1` 的开机自动挂载：
```
/dev/sdb1 /db_back xfs defaults 0 0
```

![](img/0c3a8aa874ae312d00c9dfde73219854_63.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_65.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_67.png)

编辑并保存文件后，可以使用 `mount -a` 命令立即加载 `/etc/fstab` 中所有未挂载的配置。
```bash
mount -a
```
然后使用 `df -hT` 命令验证是否挂载成功。

![](img/0c3a8aa874ae312d00c9dfde73219854_69.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_71.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_73.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_75.png)

## GPT分区格式

![](img/0c3a8aa874ae312d00c9dfde73219854_77.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_79.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_81.png)

之前我们使用的是MBR分区格式，它最多支持4个主分区，且单个磁盘容量不能超过2.2TB。对于更大容量的磁盘，我们需要使用GPT分区格式。

![](img/0c3a8aa874ae312d00c9dfde73219854_83.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_85.png)

GPT分区格式支持最多128个主分区，并且没有2.2TB的容量限制。其管理工具是 `gdisk`。

![](img/0c3a8aa874ae312d00c9dfde73219854_87.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_89.png)

使用 `gdisk` 对磁盘（例如 `/dev/sdc`）进行分区：
```bash
gdisk /dev/sdc
```

![](img/0c3a8aa874ae312d00c9dfde73219854_91.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_93.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_95.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_97.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_99.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_101.png)

进入交互界面后，常用命令如下：
*   `n`：创建新分区。
*   `p`：显示当前分区表。
*   `w`：保存分区表并退出。
*   `q`：不保存并退出。
*   `?`：获取帮助。

分区过程与 `fdisk` 类似：输入 `n` 创建分区，指定分区编号、起始扇区（通常默认即可）、分区大小（如 `+10G`），最后选择分区类型（默认 `Linux filesystem` 即可）。完成后输入 `w` 保存。

![](img/0c3a8aa874ae312d00c9dfde73219854_103.png)

分区完成后，后续的格式化、挂载步骤与MBR分区完全相同：
```bash
mkfs.xfs /dev/sdc1
mkdir /web_back
mount /dev/sdc1 /web_back
```
同样，可以将挂载信息写入 `/etc/fstab` 实现开机自动挂载。

![](img/0c3a8aa874ae312d00c9dfde73219854_105.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_107.png)

## LVM逻辑卷管理基础

![](img/0c3a8aa874ae312d00c9dfde73219854_109.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_111.png)

逻辑卷管理（LVM）的主要功能是解决物理分区空间固定、无法灵活扩展的问题。它可以将多个物理分区整合成一个大的“虚拟硬盘”（卷组），然后在这个大空间上创建可以动态调整大小的“逻辑分区”（逻辑卷）。

![](img/0c3a8aa874ae312d00c9dfde73219854_113.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_115.png)

LVM的核心优势在于**空间动态扩容**。当逻辑卷空间不足时，可以将其在线扩大，而无需迁移数据或创建新的挂载点，保证了数据存储的集中性和管理的便利性。

其工作原理简述如下：
1.  将物理分区创建为**物理卷**。
2.  将一个或多个物理卷组合成**卷组**，形成一个大的存储池。
3.  在卷组上划分出**逻辑卷**，逻辑卷可以被格式化和挂载使用。
4.  当逻辑卷空间不足时，可以从卷组的剩余空间中进行扩展。

![](img/0c3a8aa874ae312d00c9dfde73219854_117.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_119.png)

![](img/0c3a8aa874ae312d00c9dfde73219854_121.png)

本节课中我们一起学习了磁盘管理的进阶操作。我们掌握了如何通过 `/etc/fstab` 文件配置开机自动挂载，了解了GPT分区格式及其创建方法，并初步认识了LVM逻辑卷管理在实现存储空间动态扩展方面的强大功能和基本理念。这些知识是构建灵活、可靠服务器存储架构的基础。