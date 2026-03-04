# Linux 零基础入门：第 30 章：交换分区 🧠

在本节课中，我们将要学习 Linux 系统中的**交换分区**。交换分区是硬盘上划分出来用于辅助内存工作的特殊区域，理解和管理它是系统运维的重要技能。我们将从概念入手，逐步学习如何创建、格式化、激活和管理交换分区。

---

## 什么是交换分区？

![](img/2efe656fdc1d56c4a26fcc505db8f46a_1.png)

上一节我们介绍了普通磁盘分区，本节中我们来看看一种特殊的分区——交换分区。

交换分区是硬盘空间的一部分，它被划分出来用作**内存的缓存**。当物理内存不足时，系统可以将内存中暂时不用的数据移动到交换分区，从而为当前运行的程序腾出内存空间。由于它位于硬盘上，其访问速度远**慢于**物理内存。

## 交换分区的特点与管理

![](img/2efe656fdc1d56c4a26fcc505db8f46a_3.png)

![](img/2efe656fdc1d56c4a26fcc505db8f46a_5.png)

了解了基本概念后，我们来看看交换分区有哪些独特之处以及如何管理它。

![](img/2efe656fdc1d56c4a26fcc505db8f46a_7.png)

![](img/2efe656fdc1d56c4a26fcc505db8f46a_9.png)

交换分区与普通分区的主要区别在于其**文件系统类型**和**管理命令**。

![](img/2efe656fdc1d56c4a26fcc505db8f46a_11.png)

以下是交换分区管理的关键步骤和命令：

1.  **分区类型**：在创建分区时，需要将其类型标识为 `Linux swap`。在 MBR 分区表中，其类型代码是 **82**；在 GPT 分区表中，其类型代码是 **19**。
2.  **格式化**：格式化交换分区使用专用的命令 `mkswap`。
    ```bash
    mkswap /dev/sdXN
    ```
3.  **激活与停用**：格式化后，需要激活交换分区才能使用，对应的命令是 `swapon`。停用则使用 `swapoff`。
    ```bash
    swapon /dev/sdXN   # 激活交换分区
    swapoff /dev/sdXN  # 停用交换分区
    ```
4.  **查看状态**：可以使用 `swapon --show` 或 `free -h` 命令查看当前系统中激活的交换分区信息。
    ```bash
    swapon --show
    ```

![](img/2efe656fdc1d56c4a26fcc505db8f46a_13.png)

**注意**：无论是主分区还是逻辑分区，都可以作为交换分区使用。

## 实战：创建与挂载交换分区

理论需要实践来巩固，现在让我们动手创建一个交换分区。

![](img/2efe656fdc1d56c4a26fcc505db8f46a_15.png)

我们将使用 `/dev/vdc` 磁盘进行演示。首先，使用 `fdisk` 或 `parted` 工具在磁盘上划分出新的分区。

1.  使用 `fdisk /dev/vdc` 进入分区工具。
2.  新建一个分区（例如 `/dev/vdc2`）。
3.  使用 `t` 命令修改分区类型，输入类型代码 **82**（Linux swap）。
4.  使用 `p` 命令确认分区类型已更改为 `Linux swap`。
5.  输入 `w` 保存并退出。

![](img/2efe656fdc1d56c4a26fcc505db8f46a_17.png)

![](img/2efe656fdc1d56c4a26fcc505db8f46a_19.png)

分区创建完成后，需要执行后续操作：

![](img/2efe656fdc1d56c4a26fcc505db8f46a_21.png)

![](img/2efe656fdc1d56c4a26fcc505db8f46a_23.png)

```bash
# 1. 格式化新分区为交换分区格式
mkswap /dev/vdc2

![](img/2efe656fdc1d56c4a26fcc505db8f46a_25.png)

# 2. 激活交换分区
swapon /dev/vdc2

# 3. 验证交换分区已激活
swapon --show
```

![](img/2efe656fdc1d56c4a26fcc505db8f46a_27.png)

![](img/2efe656fdc1d56c4a26fcc505db8f46a_29.png)

![](img/2efe656fdc1d56c4a26fcc505db8f46a_31.png)

此时，交换分区已经可以临时使用了。但系统重启后，这个激活状态会消失。

![](img/2efe656fdc1d56c4a26fcc505db8f46a_33.png)

![](img/2efe656fdc1d56c4a26fcc505db8f46a_35.png)

![](img/2efe656fdc1d56c4a26fcc505db8f46a_37.png)

![](img/2efe656fdc1d56c4a26fcc505db8f46a_39.png)

## 实现永久挂载 🔄

![](img/2efe656fdc1d56c4a26fcc505db8f46a_41.png)

![](img/2efe656fdc1d56c4a26fcc505db8f46a_43.png)

为了让交换分区在系统重启后自动生效，我们需要将其配置为永久挂载。这与普通分区的永久挂载类似，但配置稍有不同。

![](img/2efe656fdc1d56c4a26fcc505db8f46a_45.png)

![](img/2efe656fdc1d56c4a26fcc505db8f46a_47.png)

![](img/2efe656fdc1d56c4a26fcc505db8f46a_49.png)

永久挂载的配置文件是 `/etc/fstab`。该文件定义了系统启动时需要自动挂载的文件系统。

![](img/2efe656fdc1d56c4a26fcc505db8f46a_51.png)

以下是 `/etc/fstab` 文件中一行的典型结构，包含六个字段：
```
<设备标识> <挂载点> <文件系统类型> <挂载选项> <备份标记> <检查顺序>
```

![](img/2efe656fdc1d56c4a26fcc505db8f46a_53.png)

对于交换分区，其配置行示例如下：
```
UUID=xxxx-xxxx-xxxx-xxxx none swap defaults 0 0
```
**关键点**：
*   **挂载点**：交换分区没有传统的挂载目录，此处应填写 `none` 或 `swap`。
*   **文件系统类型**：填写 `swap`。
*   **设备标识**：建议使用分区的 `UUID`（可通过 `blkid` 命令查看），这比设备名（如 `/dev/vdc2`）更稳定。

配置完成后，可以使用以下命令测试并应用配置：
```bash
# 测试 /etc/fstab 配置是否正确（对于普通分区，此命令会尝试挂载）
mount -a

# 激活 /etc/fstab 中配置的所有交换分区
swapon -a
```

最后，**重启系统**并再次使用 `swapon --show` 命令验证交换分区是否已成功实现永久挂载。

![](img/2efe656fdc1d56c4a26fcc505db8f46a_55.png)

![](img/2efe656fdc1d56c4a26fcc505db8f46a_57.png)

![](img/2efe656fdc1d56c4a26fcc505db8f46a_59.png)

---

![](img/2efe656fdc1d56c4a26fcc505db8f46a_61.png)

## 课程总结

![](img/2efe656fdc1d56c4a26fcc505db8f46a_63.png)

本节课中我们一起学习了 Linux 交换分区的核心知识。

我们首先明确了交换分区是作为**内存缓存**的硬盘空间。然后，我们逐步掌握了管理交换分区的全流程：**创建分区**并设置正确的类型标识（82或19）、使用 **`mkswap`** 命令进行格式化、用 **`swapon`/`swapoff`** 命令激活或停用。最后，我们深入探讨了如何通过编辑 **`/etc/fstab`** 配置文件来实现交换分区的**永久挂载**，确保其在系统重启后依然有效。

![](img/2efe656fdc1d56c4a26fcc505db8f46a_65.png)

理解并熟练配置交换分区，是保障 Linux 系统在内存压力下稳定运行的重要基础。