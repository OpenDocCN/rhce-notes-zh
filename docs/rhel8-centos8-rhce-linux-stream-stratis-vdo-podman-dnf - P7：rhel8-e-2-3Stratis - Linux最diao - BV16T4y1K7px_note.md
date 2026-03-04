# Linux存储管理：P7：Stratis卷管理教程 🚀

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_1.png)

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_3.png)

在本节课中，我们将要学习Stratis，这是一个新一代的存储管理解决方案，也称为卷管理文件系统。我们将了解其基本概念、工作原理，并通过实践操作学习如何创建、管理和删除Stratis池与文件系统。

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_5.png)

---

## 概述

Stratis是一种前沿的存储管理技术，旨在简化存储配置和管理。它通过池（Pool）的形式管理块设备，并自动创建和管理精简配置的文件系统。虽然目前尚未达到红帽企业级Linux生产环境所需的全部功能和稳定性水平，但它代表了存储管理的一个发展方向。

## 核心概念与原理

Stratis的核心是管理分层存储。它使用池来组织块设备，并在池上创建文件系统。其内部架构主要包含两个子系统：

*   **Backstore子系统**：负责管理底层块设备（如硬盘、分区），维护磁盘上的元数据，并能检查和纠正数据损坏。
*   **Pool子系统**：负责管理与Stratis文件系统关联的精简配置卷。它使用设备映射器（D-Mapper）驱动来动态调整卷的大小，取代了传统的LVM。

一个关键特性是，Stratis文件系统的**虚拟大小可以远大于其物理占用**。当物理空间即将耗尽时，Stratis服务会自动从池中分配更多空间进行扩展。

## 安装与配置

要使用Stratis，需要安装两个主要的软件包：
*   `stratis-cli`：提供命令行工具。
*   `stratis-d`：提供后台服务，用于监控和管理块设备、池及文件系统。

以下是安装和启动服务的命令：
```bash
# 安装必要的软件包
sudo dnf install stratis-cli stratis-d

# 启用并启动stratis服务
sudo systemctl enable --now stratisd
```

## 实践操作：创建与管理Stratis存储

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_7.png)

上一节我们介绍了Stratis的基本概念和安装。本节中，我们来看看如何使用Stratis命令行工具进行具体的存储管理操作。首先，请确保已添加额外的块设备（例如 `/dev/sdb`, `/dev/sdc`）以供实验使用。

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_9.png)

### 1. 创建存储池（Pool）

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_11.png)

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_13.png)

使用 `stratis pool create` 命令创建一个新的存储池。池是组织块设备的基础单元。

以下是创建名为 `pool1` 的池，并初始添加 `/dev/sdb` 设备的步骤：
```bash
# 创建存储池
sudo stratis pool create pool1 /dev/sdb

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_15.png)

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_17.png)

# 查看已创建的池
sudo stratis pool list
```
创建成功后，系统会在 `/stratis/pool1/` 路径下生成对应的池目录。

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_19.png)

你还可以向现有池中添加额外的块设备：
```bash
# 向pool1池中添加另一块设备
sudo stratis pool add-data pool1 /dev/sdc

# 再次查看池信息，确认容量已增加
sudo stratis pool list
```
要查看池中包含的具体块设备，可以使用：
```bash
sudo stratis blockdev list pool1
```

### 2. 创建文件系统（Filesystem）

在池创建好后，就可以在其上创建文件系统了。Stratis会自动将其格式化为XFS文件系统，无需手动操作。

以下是创建名为 `fs1` 的文件系统的命令：
```bash
# 在pool1池上创建文件系统fs1
sudo stratis filesystem create pool1 fs1

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_21.png)

# 查看创建的文件系统
sudo stratis filesystem list
```
创建后，你可以在 `/stratis/pool1/fs1` 路径下找到它。

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_23.png)

### 3. 挂载与使用文件系统

创建的文件系统可以像普通文件系统一样进行挂载和使用。

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_25.png)

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_27.png)

以下是挂载文件系统的步骤：
```bash
# 创建挂载点目录
sudo mkdir -p /opt/fs1

# 挂载Stratis文件系统
sudo mount /stratis/pool1/fs1 /opt/fs1

# 查看挂载情况，注意其显示的“虚拟”容量
df -hT
```

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_29.png)

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_31.png)

### 4. 配置永久挂载

为了实现开机自动挂载，需要编辑 `/etc/fstab` 文件。由于Stratis文件系统依赖于 `stratisd` 服务，挂载时必须添加特定参数以确保在服务启动后再进行挂载。

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_33.png)

首先，获取文件系统的UUID：
```bash
sudo blkid /stratis/pool1/fs1
```
然后，编辑 `/etc/fstab` 文件，添加如下行（请替换为你的实际UUID）：
```
UUID=<你的-UUID> /opt/fs1 xfs defaults,x-systemd.requires=stratisd.service 0 0
```
参数 `x-systemd.requires=stratisd.service` 确保了挂载动作会延迟到 `stratisd` 服务启动之后执行，避免系统启动时进入紧急模式。

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_35.png)

配置完成后，可以通过重启系统来验证永久挂载是否生效。

### 5. 删除Stratis配置

当你不再需要某个Stratis配置时，需要按照与创建相反的顺序进行删除，即先删除文件系统，再删除存储池。

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_37.png)

以下是删除操作的步骤：
```bash
# 1. 卸载文件系统
sudo umount /opt/fs1

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_39.png)

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_41.png)

# 2. 销毁文件系统
sudo stratis filesystem destroy pool1 fs1

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_43.png)

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_45.png)

# 3. 销毁存储池
sudo stratis pool destroy pool1

# 4. 验证池和文件系统均已删除
sudo stratis pool list
sudo stratis filesystem list
```
这个过程与LVM的管理逻辑相似：先移除最上层的逻辑结构（文件系统/LV），再移除底层的管理结构（池/VG）。

---

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_47.png)

## 总结

本节课中我们一起学习了Stratis卷管理文件系统。我们了解了它作为新一代存储解决方案的特点和架构原理，并通过实践掌握了其核心操作流程：
1.  安装 `stratis-cli` 和 `stratis-d` 软件包并启动服务。
2.  使用 `stratis pool create` 命令创建存储池并添加块设备。
3.  使用 `stratis filesystem create` 命令在池上创建自动格式化的XFS文件系统。
4.  挂载并使用文件系统，并通过在 `/etc/fstab` 中添加 `x-systemd.requires` 参数实现永久挂载。
5.  按照 `卸载文件系统 -> 销毁文件系统 -> 销毁存储池` 的顺序安全删除Stratis配置。

![](img/6e8897ae6c2a22b9c74367f1f6e231e0_49.png)

Stratis通过简化的命令和自动化的管理（如动态扩缩容），为存储管理提供了一种更便捷的思路。