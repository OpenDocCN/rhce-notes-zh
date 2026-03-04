# 全网最全RHCE红帽认证全套入门教程：P22：3.08-VDO虚拟卷

![](img/60bf70ed7e1074f22da7e178ad6748ba_1.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_3.png)

在本节课中，我们将要学习红帽8中的一个存储方案——VDO虚拟卷。VDO是一种虚拟数据优化器，它能够通过删除重复数据来节省磁盘空间，并提供一个比物理磁盘更大的逻辑存储空间给用户使用。虽然实际应用不多，但它是RHCE认证考试中的一个考点。

## 🧐 什么是VDO？

![](img/60bf70ed7e1074f22da7e178ad6748ba_5.png)

上一节我们介绍了多种存储管理方案，本节中我们来看看VDO。VDO是Virtual Data Optimizer（虚拟数据优化器）的缩写。它是一个内核模块，主要作用是**减少重复数据**在磁盘上的存储。

它的核心机制可以概括为：当多个用户或应用存储相同的数据时，VDO在物理磁盘上只保留一份副本，但对每个用户都呈现为一份独立、完整的数据。这类似于网盘服务，多个用户保存同一个文件，在服务器端实际上只存储一份。

## ⚙️ VDO的工作原理与优势

VDO通过“零区块排除”和“重复数据删除”等技术实现空间节省。其优势在于，管理员可以用较小的物理磁盘空间，为应用或用户提供更大的**逻辑存储空间**。

例如，一块10GB的物理磁盘，通过VDO可以创建一个50GB的逻辑卷。用户看到的是50GB的可用空间，但实际上数据占用的物理空间不会超过10GB（除非存储的都是全新的、不重复的数据）。

## 📝 实验要求与准备工作

以下是本次实验的具体要求：
*   使用未分区的磁盘 `/dev/vdc`。
*   创建一个名为 `myvdo` 的VDO卷。
*   设置其逻辑大小为 `50G`。
*   使用 `xfs` 文件系统进行格式化。
*   配置开机自动挂载。

![](img/60bf70ed7e1074f22da7e178ad6748ba_7.png)

开始操作前，我们需要安装必要的软件包并启动服务。

![](img/60bf70ed7e1074f22da7e178ad6748ba_9.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_10.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_12.png)

```bash
yum install vdo -y
systemctl enable --now vdo
```

安装的软件包、管理工具和系统服务都叫 `vdo`。

![](img/60bf70ed7e1074f22da7e178ad6748ba_14.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_16.png)

## 🔧 创建VDO卷

![](img/60bf70ed7e1074f22da7e178ad6748ba_18.png)

创建VDO卷是核心步骤，命令结构如下：

![](img/60bf70ed7e1074f22da7e178ad6748ba_20.png)

```bash
vdo create --name=<卷名称> --device=<物理设备路径> --vdoLogicalSize=<逻辑大小>
```

![](img/60bf70ed7e1074f22da7e178ad6748ba_22.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_24.png)

如果不记得完整命令，可以使用 `man vdo` 查看手册，在手册中搜索 `/EXAMPLE` 可以找到示例。

根据实验要求，我们执行以下命令：

```bash
vdo create --name=myvdo --device=/dev/vdc --vdoLogicalSize=50G
```

创建完成后，可以使用以下命令查看状态：

![](img/60bf70ed7e1074f22da7e178ad6748ba_26.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_27.png)

```bash
vdo list
vdo status --human-readable
```

## 💾 格式化与挂载

VDO卷创建后，设备文件位于 `/dev/mapper/` 目录下，以卷名命名，本例中为 `/dev/mapper/myvdo`。

接下来进行格式化。为了加快格式化速度（因为逻辑空间较大），建议使用 `-K` 选项跳过重复数据扫描。

![](img/60bf70ed7e1074f22da7e178ad6748ba_29.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_31.png)

```bash
mkfs.xfs -K /dev/mapper/myvdo
```

![](img/60bf70ed7e1074f22da7e178ad6748ba_33.png)

如果要求格式化为 `ext4`，则需使用 `-E nodiscard` 参数。

![](img/60bf70ed7e1074f22da7e178ad6748ba_35.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_37.png)

然后创建挂载点并挂载：

![](img/60bf70ed7e1074f22da7e178ad6748ba_39.png)

```bash
mkdir /vblock
mount /dev/mapper/myvdo /vblock
```

现在可以使用 `df -h` 命令查看，`/vblock` 应该显示为50G的可用空间。

![](img/60bf70ed7e1074f22da7e178ad6748ba_41.png)

## ⚡ 配置开机自动挂载

![](img/60bf70ed7e1074f22da7e178ad6748ba_43.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_45.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_47.png)

配置开机自动挂载需要编辑 `/etc/fstab` 文件。关键点在于，必须添加一个挂载选项，确保系统在挂载VDO卷之前先启动VDO服务。

![](img/60bf70ed7e1074f22da7e178ad6748ba_49.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_51.png)

以下是标准做法，在 `/etc/fstab` 中添加一行：

![](img/60bf70ed7e1074f22da7e178ad6748ba_53.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_55.png)

```
/dev/mapper/myvdo /vblock xfs defaults,_netdev,x-systemd.requires=vdo.service 0 0
```

![](img/60bf70ed7e1074f22da7e178ad6748ba_57.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_59.png)

其中 `x-systemd.requires=vdo.service` 就是确保依赖关系的选项。一个简化的替代写法是只使用 `_netdev` 选项，它表示等网络就绪后再挂载，通常此时VDO服务也已启动。

```bash
# 也可以写成
/dev/mapper/myvdo /vblock xfs defaults,_netdev 0 0
```

![](img/60bf70ed7e1074f22da7e178ad6748ba_61.png)

编辑完成后，执行 `mount -a` 测试配置是否正确，并且不会报错。

![](img/60bf70ed7e1074f22da7e178ad6748ba_63.png)

## 🧪 验证VDO去重效果

为了验证VDO的去重效果，我们可以进行一个简单测试。

首先，查看VDO卷的实际物理空间使用情况：

```bash
vdostats --human-readable
```

![](img/60bf70ed7e1074f22da7e178ad6748ba_65.png)

然后，向挂载点 `/vblock` 复制几个相同的大文件（例如一个系统镜像），但使用不同的文件名。

![](img/60bf70ed7e1074f22da7e178ad6748ba_67.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_69.png)

```bash
cp /path/to/large_file.iso /vblock/file1.iso
cp /path/to/large_file.iso /vblock/file2.iso
cp /path/to/large_file.iso /vblock/file3.iso
```

![](img/60bf70ed7e1074f22da7e178ad6748ba_71.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_73.png)

再次运行 `vdostats --human-readable`，观察“物理已用空间”的增长幅度。你会发现，尽管复制了多份文件，但物理空间的增长远小于文件总大小。而使用 `df -h` 查看 `/vblock`，则会显示这些文件的总大小已被占用。

![](img/60bf70ed7e1074f22da7e178ad6748ba_75.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_77.png)

这直观地演示了VDO的重复数据删除功能。

![](img/60bf70ed7e1074f22da7e178ad6748ba_79.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_81.png)

## 📚 本节课总结

![](img/60bf70ed7e1074f22da7e178ad6748ba_83.png)

本节课中我们一起学习了VDO虚拟卷。我们了解了VDO通过去重技术节省磁盘空间的原理，掌握了使用 `vdo create` 命令创建VDO卷、使用 `-K` 选项快速格式化、以及配置包含 `_netdev` 或 `x-systemd.requires` 选项的开机自动挂载。

![](img/60bf70ed7e1074f22da7e178ad6748ba_85.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_87.png)

![](img/60bf70ed7e1074f22da7e178ad6748ba_88.png)

关键点在于理解**物理空间**和**逻辑空间**的区别，并确保系统启动时能正确挂载VDO卷。虽然VDO在生产环境中不常用，但它是RHCE认证中需要掌握的一个知识点。