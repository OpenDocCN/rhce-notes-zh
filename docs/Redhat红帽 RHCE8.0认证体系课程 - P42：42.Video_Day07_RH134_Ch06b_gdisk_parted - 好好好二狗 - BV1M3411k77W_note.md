# Redhat红帽 RHCE8.0认证体系课程：P42：磁盘分区与交换分区管理 📚

![](img/7134e44d186d96296359e0d2ddba100e_1.png)

![](img/7134e44d186d96296359e0d2ddba100e_3.png)

![](img/7134e44d186d96296359e0d2ddba100e_4.png)

![](img/7134e44d186d96296359e0d2ddba100e_6.png)

![](img/7134e44d186d96296359e0d2ddba100e_8.png)

![](img/7134e44d186d96296359e0d2ddba100e_10.png)

在本节课中，我们将要学习Linux系统中两种重要的磁盘分区工具——`gdisk`和`parted`，以及如何创建和管理交换分区。这些技能对于系统管理员进行磁盘管理和系统优化至关重要。

![](img/7134e44d186d96296359e0d2ddba100e_12.png)

![](img/7134e44d186d96296359e0d2ddba100e_14.png)

![](img/7134e44d186d96296359e0d2ddba100e_15.png)

![](img/7134e44d186d96296359e0d2ddba100e_17.png)

![](img/7134e44d186d96296359e0d2ddba100e_18.png)

![](img/7134e44d186d96296359e0d2ddba100e_20.png)

上一节我们介绍了`fdisk`工具的基本使用，本节中我们来看看另外两种分区工具。

![](img/7134e44d186d96296359e0d2ddba100e_22.png)

![](img/7134e44d186d96296359e0d2ddba100e_23.png)

![](img/7134e44d186d96296359e0d2ddba100e_25.png)

![](img/7134e44d186d96296359e0d2ddba100e_26.png)

![](img/7134e44d186d96296359e0d2ddba100e_28.png)

![](img/7134e44d186d96296359e0d2ddba100e_30.png)

## `gdisk`工具：GPT分区管理 🔧

![](img/7134e44d186d96296359e0d2ddba100e_32.png)

![](img/7134e44d186d96296359e0d2ddba100e_33.png)

![](img/7134e44d186d96296359e0d2ddba100e_35.png)

![](img/7134e44d186d96296359e0d2ddba100e_36.png)

![](img/7134e44d186d96296359e0d2ddba100e_38.png)

![](img/7134e44d186d96296359e0d2ddba100e_40.png)

`gdisk`是专门用于管理GPT分区表的工具。一个磁盘只能有一种分区表格式。为了演示，我们首先需要添加一块新的磁盘。

![](img/7134e44d186d96296359e0d2ddba100e_42.png)

![](img/7134e44d186d96296359e0d2ddba100e_44.png)

![](img/7134e44d186d96296359e0d2ddba100e_46.png)

![](img/7134e44d186d96296359e0d2ddba100e_48.png)

![](img/7134e44d186d96296359e0d2ddba100e_50.png)

以下是添加新磁盘并查看的步骤：
1.  在虚拟机中添加一块新的20GB磁盘。
2.  启动系统后，使用 `lsblk` 命令查看新磁盘，例如 `/dev/nvme0n3`。

现在，我们对这块新磁盘使用`gdisk`进行分区。

![](img/7134e44d186d96296359e0d2ddba100e_52.png)

![](img/7134e44d186d96296359e0d2ddba100e_54.png)

![](img/7134e44d186d96296359e0d2ddba100e_56.png)

```bash
gdisk /dev/nvme0n3
```

进入交互界面后，输入 `n` 创建新分区。GPT格式最多支持128个分区。设置起始扇区和分区大小（例如2GB）。当询问分区类型时，可以按 `L` 查看所有类型，默认的Linux文件系统类型是 `8300`。输入 `p` 可以打印分区信息，确认无误后输入 `w` 保存并退出。

**警告**：此操作会覆盖磁盘上原有的分区表，请务必在空白磁盘或确认数据可丢失的磁盘上操作。

![](img/7134e44d186d96296359e0d2ddba100e_58.png)

分区创建完成后，需要格式化和挂载才能使用。

```bash
# 格式化为ext4文件系统
mkfs.ext4 /dev/nvme0n3p1

![](img/7134e44d186d96296359e0d2ddba100e_60.png)

![](img/7134e44d186d96296359e0d2ddba100e_62.png)

# 编辑/etc/fstab文件实现永久挂载
# 添加如下一行，使用UUID更安全
UUID=你的分区UUID /mnt/data2 ext4 defaults 0 0

![](img/7134e44d186d96296359e0d2ddba100e_64.png)

![](img/7134e44d186d96296359e0d2ddba100e_65.png)

![](img/7134e44d186d96296359e0d2ddba100e_67.png)

![](img/7134e44d186d96296359e0d2ddba100e_68.png)

![](img/7134e44d186d96296359e0d2ddba100e_70.png)

# 重新加载fstab配置
mount -a
```

![](img/7134e44d186d96296359e0d2ddba100e_72.png)

![](img/7134e44d186d96296359e0d2ddba100e_74.png)

使用 `df -h` 命令可以查看挂载是否成功。

## `parted`工具：高级分区操作 ⚙️

![](img/7134e44d186d96296359e0d2ddba100e_76.png)

![](img/7134e44d186d96296359e0d2ddba100e_78.png)

![](img/7134e44d186d96296359e0d2ddba100e_80.png)

![](img/7134e44d186d96296359e0d2ddba100e_82.png)

![](img/7134e44d186d96296359e0d2ddba100e_84.png)

![](img/7134e44d186d96296359e0d2ddba100e_86.png)

![](img/7134e44d186d96296359e0d2ddba100e_88.png)

`parted`是一个功能强大的分区工具，它既支持MBR也支持GPT格式，并且可以进行非交互式脚本操作。但其参数设定需要格外小心。

![](img/7134e44d186d96296359e0d2ddba100e_90.png)

![](img/7134e44d186d96296359e0d2ddba100e_92.png)

![](img/7134e44d186d96296359e0d2ddba100e_94.png)

![](img/7134e44d186d96296359e0d2ddba100e_96.png)

![](img/7134e44d186d96296359e0d2ddba100e_98.png)

我们以 `/dev/nvme0n2` 磁盘为例进行操作。

![](img/7134e44d186d96296359e0d2ddba100e_100.png)

![](img/7134e44d186d96296359e0d2ddba100e_101.png)

![](img/7134e44d186d96296359e0d2ddba100e_103.png)

![](img/7134e44d186d96296359e0d2ddba100e_104.png)

![](img/7134e44d186d96296359e0d2ddba100e_106.png)

![](img/7134e44d186d96296359e0d2ddba100e_107.png)

![](img/7134e44d186d96296359e0d2ddba100e_108.png)

```bash
parted /dev/nvme0n2
```

![](img/7134e44d186d96296359e0d2ddba100e_110.png)

![](img/7134e44d186d96296359e0d2ddba100e_111.png)

![](img/7134e44d186d96296359e0d2ddba100e_112.png)

![](img/7134e44d186d96296359e0d2ddba100e_113.png)

![](img/7134e44d186d96296359e0d2ddba100e_114.png)

![](img/7134e44d186d96296359e0d2ddba100e_115.png)

![](img/7134e44d186d96296359e0d2ddba100e_117.png)

进入`parted`交互环境后，最安全的做法是先使用 `print` 命令查看现有分区布局，明确空闲空间的起始和结束位置。

![](img/7134e44d186d96296359e0d2ddba100e_119.png)

![](img/7134e44d186d96296359e0d2ddba100e_120.png)

![](img/7134e44d186d96296359e0d2ddba100e_122.png)

![](img/7134e44d186d96296359e0d2ddba100e_124.png)

![](img/7134e44d186d96296359e0d2ddba100e_126.png)

以下是创建一个新主分区的示例命令结构：
```bash
mkpart primary xfs 起始位置 结束位置
```
*   `起始位置` 和 `结束位置` 可以使用MB（如 `2048MB`）、GB或百分比表示。
*   关键是要确保新分区的范围在空闲空间内，且不与现有分区重叠。

![](img/7134e44d186d96296359e0d2ddba100e_128.png)

![](img/7134e44d186d96296359e0d2ddba100e_129.png)

![](img/7134e44d186d96296359e0d2ddba100e_131.png)

![](img/7134e44d186d96296359e0d2ddba100e_133.png)

![](img/7134e44d186d96296359e0d2ddba100e_134.png)

![](img/7134e44d186d96296359e0d2ddba100e_136.png)

![](img/7134e44d186d96296359e0d2ddba100e_138.png)

例如，在空闲空间从2149MB开始的地方，创建一个大小为2000MB的XFS分区：
```bash
mkpart primary xfs 2149MB 4148MB
```

![](img/7134e44d186d96296359e0d2ddba100e_140.png)

![](img/7134e44d186d96296359e0d2ddba100e_142.png)

![](img/7134e44d186d96296359e0d2ddba100e_144.png)

![](img/7134e44d186d96296359e0d2ddba100e_145.png)

创建完成后，同样输入 `print` 确认。输入 `quit` 退出，并根据提示更新磁盘分区表。

![](img/7134e44d186d96296359e0d2ddba100e_147.png)

![](img/7134e44d186d96296359e0d2ddba100e_149.png)

![](img/7134e44d186d96296359e0d2ddba100e_150.png)

![](img/7134e44d186d96296359e0d2ddba100e_152.png)

![](img/7134e44d186d96296359e0d2ddba100e_154.png)

**注意**：`parted`命令对分区起止位置要求精确，操作前务必仔细核对，否则极易出错。在RHCE考试中，建议优先使用更直观的`fdisk`或`gdisk`。

![](img/7134e44d186d96296359e0d2ddba100e_156.png)

![](img/7134e44d186d96296359e0d2ddba100e_158.png)

![](img/7134e44d186d96296359e0d2ddba100e_160.png)

![](img/7134e44d186d96296359e0d2ddba100e_162.png)

## 交换分区管理：提升系统内存能力 💾

![](img/7134e44d186d96296359e0d2ddba100e_164.png)

当物理内存不足时，Linux系统会使用交换分区作为内存的补充。虽然速度不及物理内存，但对于防止系统因内存耗尽而崩溃至关重要。

![](img/7134e44d186d96296359e0d2ddba100e_166.png)

![](img/7134e44d186d96296359e0d2ddba100e_168.png)

![](img/7134e44d186d96296359e0d2ddba100e_170.png)

创建和使用交换分区需要以下几个步骤：

![](img/7134e44d186d96296359e0d2ddba100e_172.png)

![](img/7134e44d186d96296359e0d2ddba100e_174.png)

![](img/7134e44d186d96296359e0d2ddba100e_175.png)

![](img/7134e44d186d96296359e0d2ddba100e_177.png)

![](img/7134e44d186d96296359e0d2ddba100e_178.png)

![](img/7134e44d186d96296359e0d2ddba100e_180.png)

1.  **创建分区**：使用`fdisk`或`gdisk`创建一个新分区（例如1GB），并将其**类型标识改为 `82**（Linux swap）。
2.  **格式化分区**：使用 `mkswap` 命令将分区格式化为交换分区格式。
    ```bash
    mkswap /dev/nvme0n2p3
    ```
3.  **启用交换分区**：使用 `swapon` 命令激活该交换分区。
    ```bash
    swapon /dev/nvme0n2p3
    ```
4.  **验证**：使用 `free -m` 或 `swapon -s` 命令查看交换分区是否已成功添加并生效。
5.  **永久挂载**：编辑 `/etc/fstab` 文件，添加以下配置以实现开机自动启用。
    ```
    UUID=你的交换分区UUID swap swap defaults 0 0
    ```

![](img/7134e44d186d96296359e0d2ddba100e_182.png)

![](img/7134e44d186d96296359e0d2ddba100e_184.png)

![](img/7134e44d186d96296359e0d2ddba100e_186.png)

![](img/7134e44d186d96296359e0d2ddba100e_188.png)

![](img/7134e44d186d96296359e0d2ddba100e_190.png)

![](img/7134e44d186d96296359e0d2ddba100e_191.png)

关于交换分区的大小，传统建议是物理内存的1到2倍。但如果物理内存足够大（例如32GB以上），也可以不设置或只设置一个较小的交换分区。

![](img/7134e44d186d96296359e0d2ddba100e_193.png)

![](img/7134e44d186d96296359e0d2ddba100e_195.png)

![](img/7134e44d186d96296359e0d2ddba100e_196.png)

![](img/7134e44d186d96296359e0d2ddba100e_198.png)

![](img/7134e44d186d96296359e0d2ddba100e_200.png)

![](img/7134e44d186d96296359e0d2ddba100e_202.png)

![](img/7134e44d186d96296359e0d2ddba100e_204.png)

---

![](img/7134e44d186d96296359e0d2ddba100e_206.png)

![](img/7134e44d186d96296359e0d2ddba100e_208.png)

本节课中我们一起学习了`gdisk`和`parted`两种磁盘分区工具的具体使用方法，并掌握了创建、格式化、挂载交换分区的完整流程。正确管理磁盘分区和合理配置交换空间是Linux系统管理的基础技能，需要多加练习以熟练掌握。接下来，我们将进入逻辑卷管理的学习。