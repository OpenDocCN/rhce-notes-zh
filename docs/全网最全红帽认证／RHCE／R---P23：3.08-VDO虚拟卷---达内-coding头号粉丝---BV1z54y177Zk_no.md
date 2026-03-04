# 红帽认证零基础入门教程：P23：3.08-VDO虚拟卷 🚀

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_1.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_3.png)

在本节课中，我们将要学习一个名为VDO（虚拟数据优化器）的存储技术。这是一种通过删除重复数据来优化存储空间使用的方法。我们将了解其核心概念，并完成创建、格式化、挂载VDO卷的完整操作流程。

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_5.png)

## 认识VDO虚拟卷 📚

上一节我们介绍了传统的存储管理方式，本节中我们来看看一种新的存储优化方案——VDO。

VDO是Virtual Data Optimizer（虚拟数据优化器）的缩写。它是红帽系统中的一个内核模块，主要作用是**减少磁盘上重复数据的存储**，从而节省物理存储空间。

它的工作原理可以类比为网盘服务：假设有10个用户，每个用户都上传了同一个5GB的系统镜像文件。对于网盘服务商来说，在物理磁盘上只需要存储一份该文件，但每个用户都感觉自己占用了5GB的空间。VDO实现了类似的效果，它让一块较小的物理磁盘，能够为上层应用提供一个容量大得多的“逻辑”存储空间。

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_7.png)

## 实验目标与准备 🎯

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_9.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_10.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_12.png)

本次实验的目标是：使用一块未分区的10GB磁盘（`/dev/vdc`），创建一个名为`myvdo`的VDO卷，其逻辑大小为50GB，然后将其格式化为XFS文件系统并挂载。

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_14.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_16.png)

以下是实验前的检查步骤，确认磁盘状态：
```bash
lsblk /dev/vdc
```

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_18.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_20.png)

## 安装必要软件包 🔧

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_22.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_24.png)

要使用VDO功能，首先需要安装对应的软件包。

运行以下命令安装VDO：
```bash
yum install vdo -y
```
安装完成后，系统会新增一个同名的`vdo`服务和管理工具`vdo`。

## 创建VDO卷 ⚙️

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_26.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_27.png)

上一节我们准备好了系统和软件，本节中我们来看看创建VDO卷的具体命令。

创建VDO卷的核心命令是`vdo create`。我们需要指定三个关键参数：VDO卷的名称、使用的物理设备以及逻辑大小。

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_29.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_31.png)

我们可以通过帮助手册查找命令示例：
```bash
man vdo
```
在手册中，输入`/EXAMPLE`查找示例部分，可以找到创建命令的模板。

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_33.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_35.png)

根据实验要求，创建VDO卷的命令如下：
```bash
vdo create --name=myvdo --device=/dev/vdc --vdoLogicalSize=50G
```
这条命令的含义是：使用物理设备`/dev/vdc`，创建一个名为`myvdo`的VDO卷，其逻辑大小为50GB。

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_37.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_39.png)

创建完成后，可以使用以下命令查看VDO卷的状态和信息：
```bash
vdo list
vdo status --name=myvdo
vdo stats --name=myvdo --human-readable
```
`vdo stats`命令可以查看物理空间使用情况（约10GB）和逻辑空间情况（50GB）。

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_41.png)

## 格式化与挂载 💾

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_43.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_45.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_47.png)

创建好VDO卷后，我们需要将其格式化为文件系统才能使用。

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_49.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_51.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_53.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_55.png)

VDO卷的设备路径位于`/dev/mapper/`目录下，以卷名命名，本例中为`/dev/mapper/myvdo`。

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_57.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_59.png)

**使用XFS文件系统格式化时，必须添加`-K`参数以跳过空间丢弃操作，这可以极大加快格式化速度。**
```bash
mkfs.xfs -K /dev/mapper/myvdo
```
如果要求格式化为ext4，则应使用以下命令：
```bash
mkfs.ext4 -E nodiscard /dev/mapper/myvdo
```

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_61.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_63.png)

接下来，创建挂载点并配置开机自动挂载。
```bash
mkdir /vblock
```
编辑`/etc/fstab`文件，添加以下挂载配置：
```
/dev/mapper/myvdo /vblock xfs defaults,_netdev 0 0
```
**关键点**：挂载选项中的`_netdev`非常重要。它表示这是一个“网络型”存储设备，系统会等待网络就绪后再尝试挂载，这确保了VDO服务（虽然并非真正的网络服务）在挂载前已启动。如果省略此选项，系统启动时可能会因挂载失败而进入紧急模式。

现在，启用VDO服务并挂载设备：
```bash
systemctl enable vdo --now
mount -a
```
使用`df -h`命令检查挂载，可以看到`/vblock`的容量约为50GB。

## 验证去重效果 🔍

为了直观感受VDO的去重（Deduplication）效果，我们可以进行一个简单的测试。

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_65.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_67.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_69.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_71.png)

首先，在VDO卷中存入一个较大的文件（例如一个ISO镜像）。
```bash
cp /path/to/large_file.iso /vblock/file1.iso
```
检查当前磁盘空间使用情况：
```bash
vdo stats --name=myvdo --human-readable | grep -E “physical|logical”
df -h /vblock
```
然后，将同一个文件以不同名称复制多份：
```bash
cp /vblock/file1.iso /vblock/file2.iso
cp /vblock/file1.iso /vblock/file3.iso
```
再次检查空间使用情况。你会发现：
*   通过`df`命令看到的`/vblock`目录使用空间会显著增加（因为记录了多份文件）。
*   通过`vdo stats`命令看到的**物理空间使用量**却增加得非常少，因为VDO在底层只存储了一份实际数据。

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_73.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_75.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_77.png)

这个测试清晰地展示了VDO的数据去重能力。

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_79.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_81.png)

## 总结 📝

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_83.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_85.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_87.png)

![](img/6687dbafb077fc87d4c4c1400c4c5ed5_88.png)

本节课中我们一起学习了VDO虚拟卷技术。我们首先了解了VDO通过去重和压缩来优化存储空间的原理。然后，我们完成了从安装软件包、创建VDO卷、格式化到挂载的完整实战操作。需要牢记的几个关键点是：创建命令的参数格式、格式化XFS时必须使用`-K`选项、以及在`/etc/fstab`中必须添加`_netdev`挂载选项以避免启动故障。VDO技术特别适用于存储大量相似或重复数据的场景，例如虚拟桌面、容器镜像或备份存储，能够有效提升存储资源的利用率。