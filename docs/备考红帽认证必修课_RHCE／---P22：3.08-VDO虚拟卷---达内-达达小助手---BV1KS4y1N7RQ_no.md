# 备考红帽认证必修课：P22：3.08-VDO虚拟卷

![](img/1e669a2be74fe0589518c20da71bf4be_1.png)

![](img/1e669a2be74fe0589518c20da71bf4be_3.png)

在本节课中，我们将要学习一个名为VDO（虚拟数据优化器）的存储技术。这是一种用于优化存储空间、减少重复数据占用的方案。我们将了解其基本概念，并通过实践操作创建一个VDO虚拟卷。

## 认识VDO

VDO是Virtual Data Optimizer（虚拟数据优化器）的缩写。它是红帽系统中的一个内核模块，主要作用是通过删除磁盘上的重复数据来减少存储空间的占用。

![](img/1e669a2be74fe0589518c20da71bf4be_5.png)

它的工作原理类似于网盘服务。例如，多个用户都上传了同一个5GB的系统镜像文件。对于存储服务器而言，它实际上只需要保存一份该文件的数据，但每个用户都感觉自己占用了5GB的空间。VDO技术实现了类似的“去重”效果，允许管理员用较小的物理磁盘空间，为多个用户或应用提供更大的“逻辑”存储空间。

## 实验目标与准备

上一节我们介绍了VDO的基本概念，本节中我们来看看具体的实验要求。

实验要求如下：
*   使用一块未分区的磁盘（例如 `/dev/vdc`）。
*   创建一个名为 `myvdo` 的VDO卷，其逻辑大小为50GB。
*   使用XFS文件系统格式化此VDO卷。
*   将其挂载到 `/vblock` 目录，并配置为开机自动挂载。

**核心概念**：VDO卷的**逻辑大小**可以大于其底层**物理设备**的实际大小。例如，一个10GB的物理磁盘可以呈现为一个50GB的逻辑卷。

## 操作步骤详解

![](img/1e669a2be74fe0589518c20da71bf4be_7.png)

以下是创建和配置VDO卷的详细步骤。

![](img/1e669a2be74fe0589518c20da71bf4be_9.png)

![](img/1e669a2be74fe0589518c20da71bf4be_10.png)

### 1. 安装必要软件包

![](img/1e669a2be74fe0589518c20da71bf4be_12.png)

首先，我们需要安装提供VDO功能的软件包。

![](img/1e669a2be74fe0589518c20da71bf4be_14.png)

```bash
yum install -y vdo
```

![](img/1e669a2be74fe0589518c20da71bf4be_16.png)

安装完成后，系统会添加 `vdo` 管理命令和同名的 `vdo` 系统服务。

![](img/1e669a2be74fe0589518c20da71bf4be_18.png)

![](img/1e669a2be74fe0589518c20da71bf4be_20.png)

### 2. 创建VDO卷

![](img/1e669a2be74fe0589518c20da71bf4be_22.png)

创建VDO卷是核心步骤。如果记不住命令格式，可以使用 `man vdo` 查看手册，并在手册中搜索 `/EXAMPLE` 查找示例命令。

![](img/1e669a2be74fe0589518c20da71bf4be_24.png)

根据实验要求，我们创建逻辑大小为50GB的VDO卷：

```bash
vdo create --name=myvdo --device=/dev/vdc --vdoLogicalSize=50G
```

**命令解析**：
*   `--name=myvdo`：指定VDO卷的名称为 `myvdo`。
*   `--device=/dev/vdc`：指定使用的物理存储设备。
*   `--vdoLogicalSize=50G`：指定VDO卷呈现给用户的逻辑大小。

创建成功后，可以通过以下命令查看VDO卷的状态：

![](img/1e669a2be74fe0589518c20da71bf4be_26.png)

![](img/1e669a2be74fe0589518c20da71bf4be_27.png)

```bash
vdo status --name=myvdo
# 或使用更简洁的列表
vdo list
# 查看详细统计信息（注意实际物理空间占用）
vdo stats --name=myvdo --human-readable
```

### 3. 格式化VDO卷

接下来，我们需要使用XFS文件系统格式化新创建的VDO卷设备。设备路径通常为 `/dev/mapper/myvdo`。

**重要提示**：格式化时添加 `-K` 选项可以跳过磁盘初始化检查，这对于大容量的虚拟卷能显著加快格式化速度。

![](img/1e669a2be74fe0589518c20da71bf4be_29.png)

```bash
mkfs.xfs -K /dev/mapper/myvdo
```

![](img/1e669a2be74fe0589518c20da71bf4be_31.png)

![](img/1e669a2be74fe0589518c20da71bf4be_33.png)

如果要求格式化为ext4文件系统，则应使用以下命令：

![](img/1e669a2be74fe0589518c20da71bf4be_35.png)

```bash
mkfs.ext4 -E nodiscard /dev/mapper/myvdo
```

![](img/1e669a2be74fe0589518c20da71bf4be_37.png)

![](img/1e669a2be74fe0589518c20da71bf4be_39.png)

### 4. 配置挂载

首先，创建挂载点目录：

![](img/1e669a2be74fe0589518c20da71bf4be_41.png)

```bash
mkdir /vblock
```

![](img/1e669a2be74fe0589518c20da71bf4be_43.png)

![](img/1e669a2be74fe0589518c20da71bf4be_45.png)

然后，进行临时挂载以测试：

![](img/1e669a2be74fe0589518c20da71bf4be_47.png)

![](img/1e669a2be74fe0589518c20da71bf4be_49.png)

```bash
mount /dev/mapper/myvdo /vblock
```

![](img/1e669a2be74fe0589518c20da71bf4be_51.png)

![](img/1e669a2be74fe0589518c20da71bf4be_53.png)

现在可以使用 `df -h` 命令查看，`/vblock` 应该显示为约50GB的可用空间。

![](img/1e669a2be74fe0589518c20da71bf4be_55.png)

![](img/1e669a2be74fe0589518c20da71bf4be_57.png)

![](img/1e669a2be74fe0589518c20da71bf4be_59.png)

### 5. 配置开机自动挂载

为了确保系统重启后VDO卷能自动挂载，需要编辑 `/etc/fstab` 文件。

**关键配置**：必须在挂载选项中添加 `_netdev` 参数。这表示该存储设备依赖于网络（或类似VDO的后台服务），系统会等待相关服务就绪后再尝试挂载，避免启动时因服务未启动而挂载失败。

![](img/1e669a2be74fe0589518c20da71bf4be_61.png)

![](img/1e669a2be74fe0589518c20da71bf4be_63.png)

使用以下命令编辑 `/etc/fstab`：

```bash
echo \"/dev/mapper/myvdo /vblock xfs defaults,_netdev 0 0\" >> /etc/fstab
```

**配置解析**：
*   `_netdev`：核心参数，确保挂载在VDO服务启动后进行。
*   也可以使用更精确但更长的参数：`x-systemd.requires=vdo.service`。

验证配置是否正确，可以执行：

```bash
mount -a
```

![](img/1e669a2be74fe0589518c20da71bf4be_65.png)

![](img/1e669a2be74fe0589518c20da71bf4be_67.png)

如果命令执行没有报错，则说明配置成功。

![](img/1e669a2be74fe0589518c20da71bf4be_69.png)

![](img/1e669a2be74fe0589518c20da71bf4be_71.png)

### 6. 验证去重效果（选做）

![](img/1e669a2be74fe0589518c20da71bf4be_73.png)

为了直观感受VDO的去重效果，可以尝试以下测试：

![](img/1e669a2be74fe0589518c20da71bf4be_75.png)

![](img/1e669a2be74fe0589518c20da71bf4be_77.png)

1.  找一个较大的文件（例如一个ISO镜像）。
2.  将其复制多份到 `/vblock` 目录下，每次使用不同的文件名。
3.  分别观察 `df -h /vblock` 显示的“已用空间”和 `vdo stats --name=myvdo` 显示的“实际已用物理空间”。

![](img/1e669a2be74fe0589518c20da71bf4be_79.png)

![](img/1e669a2be74fe0589518c20da71bf4be_81.png)

你会发现，尽管用户层面看到已用空间在增加，但VDO底层的实际物理空间占用增长却非常缓慢，因为重复的数据块只存储了一次。

![](img/1e669a2be74fe0589518c20da71bf4be_83.png)

## 总结

![](img/1e669a2be74fe0589518c20da71bf4be_85.png)

![](img/1e669a2be74fe0589518c20da71bf4be_87.png)

![](img/1e669a2be74fe0589518c20da71bf4be_88.png)

本节课中我们一起学习了VDO虚拟卷技术。我们了解到VDO通过数据去重和压缩，可以有效提升存储空间的利用率。实践部分，我们掌握了从安装软件包、创建VDO卷、格式化、挂载到配置开机自动挂载的完整流程。特别需要注意在 `/etc/fstab` 中为VDO卷添加 `_netdev` 挂载选项，这是保证系统稳定启动的关键。