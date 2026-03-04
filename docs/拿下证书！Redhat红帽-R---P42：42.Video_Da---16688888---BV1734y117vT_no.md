# Red Hat RHCE 8.0 认证体系课程：P42：磁盘分区与交换空间管理进阶

![](img/cb93d41d14e8a212187707ad54a0f1ae_0.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_2.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_4.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_6.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_8.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_10.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_12.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_14.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_15.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_16.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_17.png)

在本节课中，我们将学习两种高级磁盘分区工具 `gdisk` 和 `parted` 的使用方法，并掌握如何创建和管理 Linux 交换空间。这些技能对于系统管理员进行磁盘规划和性能调优至关重要。

![](img/cb93d41d14e8a212187707ad54a0f1ae_18.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_20.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_21.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_23.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_25.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_27.png)

上一节我们介绍了使用 `fdisk` 进行 MBR 格式磁盘分区的基本操作。本节中，我们将看看针对 GPT 格式的 `gdisk` 工具和更灵活的 `parted` 工具，并学习如何配置交换分区。

![](img/cb93d41d14e8a212187707ad54a0f1ae_29.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_31.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_32.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_34.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_36.png)

## 使用 `gdisk` 管理 GPT 分区

![](img/cb93d41d14e8a212187707ad54a0f1ae_38.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_40.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_42.png)

`gdisk` 是专门用于管理 GPT 分区表的工具。一个磁盘只能有一种分区表格式。我们将在一块新磁盘 `/dev/nvme0n3` 上进行演示。

![](img/cb93d41d14e8a212187707ad54a0f1ae_44.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_46.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_48.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_50.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_51.png)

首先，我们使用 `gdisk` 命令进入交互式分区界面：
```bash
gdisk /dev/nvme0n3
```
该命令会扫描磁盘，如果不存在分区表，则会自动创建一个新的 GPT 分区表。

![](img/cb93d41d14e8a212187707ad54a0f1ae_53.png)

在 `gdisk` 交互界面中，输入 `?` 可以查看所有可用命令的帮助信息。

以下是创建一个新分区的主要步骤：
1.  输入 `n` 创建一个新分区。
2.  输入分区编号（GPT 支持 1-128 个分区，无主、扩展分区概念）。
3.  输入起始扇区，通常直接按回车使用默认值。
4.  输入分区大小，例如 `+2G` 表示创建 2GB 分区。
5.  设置分区类型代码，输入 `L` 可以查看所有支持的类型。Linux 文件系统通常使用默认的 `8300`。
6.  输入 `p` 可以打印当前分区表信息进行确认。
7.  最后，输入 `w` 并确认 (`y`) 将分区表写入磁盘。**此操作会覆盖磁盘原有数据，务必谨慎。**

分区创建完成后，需要格式化和挂载才能使用：
```bash
# 格式化为 ext4 文件系统
mkfs.ext4 /dev/nvme0n3p1
# 获取分区的 UUID
blkid /dev/nvme0n3p1
# 编辑 /etc/fstab 文件实现永久挂载
vim /etc/fstab
# 在文件中添加一行，格式为：UUID=你的UUID /mnt/data2 ext4 defaults 0 0
# 使挂载生效
mount -a
# 使用 df -h 命令检查挂载结果
df -h
```

![](img/cb93d41d14e8a212187707ad54a0f1ae_55.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_57.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_59.png)

## 使用 `parted` 进行高级分区

`parted` 是一个功能强大的分区工具，它既支持 MBR 也支持 GPT 格式，并且可以进行非交互式脚本操作。其基本语法为 `parted [选项] [设备 [命令]]`。

![](img/cb93d41d14e8a212187707ad54a0f1ae_61.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_63.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_65.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_67.png)

我们将以在 `/dev/nvme0n2` 上创建分区为例。**使用 `parted` 时，精确指定起始和结束位置是关键，操作前务必先打印现有分区信息。**

![](img/cb93d41d14e8a212187707ad54a0f1ae_69.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_71.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_73.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_75.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_77.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_78.png)

以下是使用 `parted` 创建分区的安全流程：
1.  首先，进入 `parted` 交互模式并打印现有分区表，以确定下一个分区的起始位置：
    ```bash
    parted /dev/nvme0n2
    (parted) print
    ```
2.  假设最后一个分区结束于 4196352KB，我们从此处开始创建一个 2000MB 的主分区：
    ```bash
    (parted) mkpart primary xfs 4196352KB 6196352KB
    ```
    *   `mkpart` 是创建分区命令。
    *   `primary` 指定为主分区。
    *   `xfs` 指定文件系统类型（此处仅为标识，实际格式需另用 `mkfs` 命令）。
    *   `4196352KB` 是分区的起始位置。
    *   `6196352KB` 是分区的结束位置（起始 + 2000MB）。
3.  再次使用 `print` 命令确认分区创建成功。
4.  输入 `quit` 退出 `parted`。系统会提示需要更新 `/etc/fstab`，但实际格式化与挂载步骤仍需手动完成。

![](img/cb93d41d14e8a212187707ad54a0f1ae_80.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_82.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_84.png)

**注意**：`parted` 的参数指定需要非常精确，容易出错。在考试或生产环境中，如果可以使用 `fdisk` 或 `gdisk`，建议优先选择它们。

![](img/cb93d41d14e8a212187707ad54a0f1ae_86.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_87.png)

## 创建与管理交换空间

![](img/cb93d41d14e8a212187707ad54a0f1ae_88.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_90.png)

交换空间在物理内存不足时，将部分内存数据暂存到磁盘上，以扩展可用内存。其操作步骤与普通分区略有不同。

![](img/cb93d41d14e8a212187707ad54a0f1ae_92.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_94.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_96.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_98.png)

以下是创建交换分区的完整步骤：
1.  **创建分区**：使用 `fdisk` 或 `gdisk` 创建一个新分区。**关键步骤是将其类型代码修改为 `82` (Linux swap)**。
2.  **格式化交换分区**：使用专用命令 `mkswap` 进行格式化，而不是普通的 `mkfs`。
    ```bash
    mkswap /dev/nvme0n2p3
    ```
3.  **启用交换分区**：使用 `swapon` 命令激活该交换分区。
    ```bash
    swapon /dev/nvme0n2p3
    ```
4.  **验证状态**：使用 `free -m` 或 `swapon -s` 命令查看交换空间是否已成功添加并生效。
5.  **永久挂载**：编辑 `/etc/fstab` 文件，添加一行配置以实现开机自动启用。
    ```
    UUID=你的swap分区UUID none swap defaults 0 0
    ```
    保存后，可以执行 `mount -a` 测试配置是否正确（对 swap 而言，此命令会静默执行）。

![](img/cb93d41d14e8a212187707ad54a0f1ae_100.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_102.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_103.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_105.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_106.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_108.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_110.png)

关于交换分区的大小，传统建议是物理内存的 1 到 2 倍。但如果物理内存足够大（例如超过 8GB），也可以不设置或设置较小的交换分区。

![](img/cb93d41d14e8a212187707ad54a0f1ae_112.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_114.png)

![](img/cb93d41d14e8a212187707ad54a0f1ae_116.png)

本节课中我们一起学习了 `gdisk` 和 `parted` 这两种高级分区工具的使用方法及注意事项，并掌握了创建、启用和管理 Linux 交换空间的完整流程。请务必在实验环境中充分练习这些操作，尤其是 `parted` 命令中起始与结束位置的精确计算。