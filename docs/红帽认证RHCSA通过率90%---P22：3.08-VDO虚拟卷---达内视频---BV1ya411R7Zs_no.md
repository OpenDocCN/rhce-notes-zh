# RHCSA精讲教程：P22：3.08-VDO虚拟卷

![](img/a4bdd8a4f8502fdaed60467763b57d1f_1.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_3.png)

在本节课中，我们将要学习VDO虚拟卷的创建与管理。VDO是一种虚拟数据优化器，它通过数据去重和压缩技术，能够用较小的物理存储空间提供较大的逻辑存储空间。本节内容将详细介绍其概念、操作步骤及注意事项。

## 🧠 认识VDO虚拟卷

![](img/a4bdd8a4f8502fdaed60467763b57d1f_5.png)

VDO是Virtual Data Optimizer（虚拟数据优化器）的缩写。它是红帽系统中的一个内核模块，主要作用是**减少重复数据**在磁盘上的存储，从而节省物理空间。

**核心机制**：
*   **零区块排除**：忽略全为零的数据块。
*   **重复数据删除**：识别并只存储一份相同的数据。

**举个例子**：
假设服务器物理磁盘只有10GB。通过VDO技术，我们可以创建一个逻辑大小为50GB的虚拟卷给用户使用。当用户存储数据时，如果存在重复文件（例如多个用户存储了相同的系统镜像），VDO在底层实际只保存一份，但每个用户都认为自己独占了该文件的存储空间。这实现了存储空间的“虚拟膨胀”。

**公式表示**：
`逻辑存储空间 > 物理存储空间`

## ⚙️ VDO部署与配置流程

![](img/a4bdd8a4f8502fdaed60467763b57d1f_7.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_9.png)

上一节我们介绍了VDO的基本概念，本节中我们来看看如何具体创建和配置一个VDO卷。整个流程可以概括为：安装软件 -> 创建卷 -> 格式化 -> 挂载使用。

![](img/a4bdd8a4f8502fdaed60467763b57d1f_10.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_12.png)

以下是创建VDO卷的核心步骤列表：

![](img/a4bdd8a4f8502fdaed60467763b57d1f_14.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_16.png)

1.  **安装必要软件包**：`yum install -y vdo kmod-kvdo`
2.  **创建VDO卷**：使用 `vdo create` 命令。
3.  **格式化VDO卷**：使用 `mkfs.xfs -K` 或 `mkfs.ext4 -E nodiscard`。
4.  **配置挂载**：更新 `/etc/fstab` 实现开机自动挂载。

![](img/a4bdd8a4f8502fdaed60467763b57d1f_18.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_20.png)

## 🔧 详细操作步骤

![](img/a4bdd8a4f8502fdaed60467763b57d1f_22.png)

### 步骤一：安装软件包并启动服务

![](img/a4bdd8a4f8502fdaed60467763b57d1f_24.png)

首先，需要安装VDO相关的软件包。

```bash
yum install -y vdo
```
安装完成后，系统服务 `vdo` 会自动启用。

### 步骤二：创建VDO卷

![](img/a4bdd8a4f8502fdaed60467763b57d1f_26.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_27.png)

这是最关键的一步。我们使用 `vdo create` 命令来创建虚拟卷。

**命令语法**：
```bash
vdo create --name=<卷名称> --device=<物理设备路径> --vdoLogicalSize=<逻辑大小>
```

**根据题目要求操作**：
我们的目标是使用 `/dev/vdc`（10GB物理盘）创建名为 `myvdo`、逻辑大小为50GB的VDO卷。

![](img/a4bdd8a4f8502fdaed60467763b57d1f_29.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_31.png)

```bash
vdo create --name=myvdo --device=/dev/vdc --vdoLogicalSize=50G
```

![](img/a4bdd8a4f8502fdaed60467763b57d1f_33.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_35.png)

**命令解析**：
*   `--name=myvdo`：指定VDO卷的名称为 `myvdo`。
*   `--device=/dev/vdc`：指定底层物理设备。
*   `--vdoLogicalSize=50G`：指定对用户呈现的逻辑大小为50GB。

![](img/a4bdd8a4f8502fdaed60467763b57d1f_37.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_39.png)

创建完成后，可以通过以下命令查看：
*   `vdo list`：列出所有VDO卷。
*   `vdo status`：查看VDO卷的详细状态。
*   `vdo stats`：查看VDO卷的统计数据（如物理/逻辑空间使用情况）。

### 步骤三：格式化VDO卷

![](img/a4bdd8a4f8502fdaed60467763b57d1f_41.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_43.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_45.png)

创建好的VDO卷设备文件位于 `/dev/mapper/` 目录下，以卷名称命名，即 `/dev/mapper/myvdo`。

![](img/a4bdd8a4f8502fdaed60467763b57d1f_47.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_49.png)

**格式化命令（XFS文件系统）**：
```bash
mkfs.xfs -K /dev/mapper/myvdo
```
**注意**：参数 `-K` 非常重要，它让格式化过程跳过磁盘空间丢弃（discard）操作，可以极大加快大容量虚拟卷的格式化速度。

![](img/a4bdd8a4f8502fdaed60467763b57d1f_51.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_53.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_55.png)

如果要求格式化为ext4，则使用：
```bash
mkfs.ext4 -E nodiscard /dev/mapper/myvdo
```

![](img/a4bdd8a4f8502fdaed60467763b57d1f_57.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_59.png)

### 步骤四：配置挂载

![](img/a4bdd8a4f8502fdaed60467763b57d1f_61.png)

首先，创建挂载点目录。
```bash
mkdir /vblock
```

![](img/a4bdd8a4f8502fdaed60467763b57d1f_63.png)

然后，临时挂载以测试。
```bash
mount /dev/mapper/myvdo /vblock
```

最后，配置开机自动挂载。编辑 `/etc/fstab` 文件，添加如下一行：
```
/dev/mapper/myvdo /vblock xfs defaults,_netdev 0 0
```
**关键点**：参数 `_netdev` 的作用是告诉系统，这个设备需要在网络就绪后再挂载。对于VDO这类可能依赖后台服务的存储设备，添加此参数可以避免系统启动时因服务未就绪而挂载失败，导致启动卡住的问题。

配置完成后，可以执行 `mount -a` 测试配置是否正确，并使用 `df -h` 命令查看挂载情况，此时 `/vblock` 应显示约为50GB的可用空间。

## 🧪 验证VDO去重效果

![](img/a4bdd8a4f8502fdaed60467763b57d1f_65.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_67.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_69.png)

为了直观感受VDO的去重功能，可以进行一个简单测试。

![](img/a4bdd8a4f8502fdaed60467763b57d1f_71.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_73.png)

1.  找一个较大的文件（如一个ISO镜像）。
2.  将其复制多份到 `/vblock` 目录下，每次复制使用不同的文件名。
    ```bash
    cp largefile.iso /vblock/copy1.iso
    cp largefile.iso /vblock/copy2.iso
    cp largefile.iso /vblock/copy3.iso
    ```
3.  观察空间变化：
    *   使用 `df -h /vblock` 查看，会发现已用空间显著增加（用户视角）。
    *   使用 `vdo stats --human-readable /dev/mapper/myvdo` 查看，会发现实际物理数据占用空间（`data blocks used`）增加得很少（管理员视角）。

![](img/a4bdd8a4f8502fdaed60467763b57d1f_75.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_77.png)

这个对比清晰地展示了VDO的重复数据删除效果。

![](img/a4bdd8a4f8502fdaed60467763b57d1f_79.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_81.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_83.png)

## 💎 课程总结

![](img/a4bdd8a4f8502fdaed60467763b57d1f_85.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_87.png)

![](img/a4bdd8a4f8502fdaed60467763b57d1f_88.png)

本节课中我们一起学习了VDO虚拟卷的配置与管理。我们首先了解了VDO通过去重和压缩优化存储空间的原理。然后，我们逐步实践了从安装软件、创建逻辑卷、格式化到配置挂载的完整流程。需要特别记住的要点包括：创建命令的格式、格式化时使用 `-K` 参数加速，以及在 `/etc/fstab` 中添加 `_netdev` 挂载选项以避免启动问题。掌握这些，你就能顺利完成RHCSA考试中关于VDO的题目，并在实际工作中应用这一存储优化技术。