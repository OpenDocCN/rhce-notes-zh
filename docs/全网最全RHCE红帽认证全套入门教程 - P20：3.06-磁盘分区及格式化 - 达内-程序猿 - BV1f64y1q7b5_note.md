# RHCE红帽认证全套入门教程：P20：3.06-磁盘分区及格式化

![](img/b17afc39585b87d237bd53bfed543514_0.png)

![](img/b17afc39585b87d237bd53bfed543514_2.png)

![](img/b17afc39585b87d237bd53bfed543514_4.png)

![](img/b17afc39585b87d237bd53bfed543514_6.png)

![](img/b17afc39585b87d237bd53bfed543514_8.png)

![](img/b17afc39585b87d237bd53bfed543514_10.png)

在本节课中，我们将学习Linux系统中磁盘分区、格式化及挂载的基础知识，并完成添加交换分区的实践操作。这是RHCE考试中涉及磁盘管理的重要部分。

![](img/b17afc39585b87d237bd53bfed543514_12.png)

![](img/b17afc39585b87d237bd53bfed543514_14.png)

![](img/b17afc39585b87d237bd53bfed543514_16.png)

## 课程概述与考试环境

![](img/b17afc39585b87d237bd53bfed543514_18.png)

![](img/b17afc39585b87d237bd53bfed543514_20.png)

![](img/b17afc39585b87d237bd53bfed543514_22.png)

![](img/b17afc39585b87d237bd53bfed543514_24.png)

上一节我们介绍了系统基础操作，本节中我们来看看磁盘管理的核心步骤。首先，了解考试环境至关重要。

![](img/b17afc39585b87d237bd53bfed543514_26.png)

![](img/b17afc39585b87d237bd53bfed543514_28.png)

![](img/b17afc39585b87d237bd53bfed543514_30.png)

![](img/b17afc39585b87d237bd53bfed543514_32.png)

![](img/b17afc39585b87d237bd53bfed543514_33.png)

![](img/b17afc39585b87d237bd53bfed543514_35.png)

在RHCE考试环境中，通常会提供三块虚拟磁盘：
*   **`/dev/vda`**：系统盘，包含启动分区、根分区和交换分区。
*   **`/dev/vdb`**：用于完成“调整逻辑卷大小”、“添加交换分区”和“创建新逻辑卷”等题目。
*   **`/dev/vdc`**：一块**未分区**的空磁盘，专门用于完成“创建VDO卷”的题目。

![](img/b17afc39585b87d237bd53bfed543514_37.png)

![](img/b17afc39585b87d237bd53bfed543514_39.png)

使用 `lsblk` 或 `fdisk -l` 命令可以查看当前系统的磁盘和分区情况。**特别注意**：`/dev/vdc` 磁盘必须保持空闲，不要在其上创建交换分区或逻辑卷，以免影响后续的VDO题目。

## 磁盘使用的基本流程

![](img/b17afc39585b87d237bd53bfed543514_41.png)

![](img/b17afc39585b87d237bd53bfed543514_43.png)

![](img/b17afc39585b87d237bd53bfed543514_45.png)

![](img/b17afc39585b87d237bd53bfed543514_47.png)

在Linux中使用一块新磁盘，通常需要遵循以下流程：
1.  **识别磁盘**：系统启动时内核自动识别。管理员可用 `lsblk` 或 `fdisk -l` 查看。
2.  **创建分区**：使用 `fdisk` 或 `parted` 等工具在磁盘上划分分区。
3.  **格式化**：使用 `mkfs` 系列工具为分区创建文件系统（如ext4, xfs）。
4.  **挂载**：使用 `mount` 命令将格式化好的分区关联到系统目录。
5.  **持久化挂载**：编辑 `/etc/fstab` 文件，实现开机自动挂载。

## 分区工具详解

以下是两种常用的分区工具：

### 使用 `fdisk` 工具
`fdisk` 工具采用交互式操作，适合新手，因为修改**不会立即生效**，需输入 `w` 命令保存，输入 `q` 命令则不保存退出。
```bash
fdisk /dev/vdb
```
进入交互界面后，常用命令如下：
*   `p`：打印当前分区表。
*   `n`：创建新分区。
*   `d`：删除分区（**慎用**）。
*   `w`：保存分区表并退出。
*   `q`：不保存退出。

创建分区时，可以用 `+512M` 这样的格式直接指定分区大小，非常方便。

### 使用 `parted` 工具
`parted` 工具功能更强大，支持GPT分区表，但其操作**会立即生效**，适合有经验的管理员。
```bash
parted /dev/vdb
```
进入交互界面后，常用命令如下：
*   `print`：打印当前分区表。
*   `mkpart`：创建新分区。
*   `rm`：删除分区。
*   `quit`：退出。

![](img/b17afc39585b87d237bd53bfed543514_49.png)

![](img/b17afc39585b87d237bd53bfed543514_51.png)

![](img/b17afc39585b87d237bd53bfed543514_53.png)

![](img/b17afc39585b87d237bd53bfed543514_54.png)

### 使新分区表生效
分区操作后，需要让内核重新读取分区表。最彻底的方法是重启系统。也可以使用以下命令尝试刷新：
```bash
partprobe /dev/vdb
# 或
partx -a /dev/vdb
```

![](img/b17afc39585b87d237bd53bfed543514_56.png)

## 格式化与挂载

![](img/b17afc39585b87d237bd53bfed543514_58.png)

分区完成后，需要格式化才能存储数据。使用 `mkfs` 命令进行格式化。
```bash
mkfs.ext4 /dev/vdb3
```
上述命令将 `/dev/vdb3` 分区格式化为 ext4 文件系统。其他文件系统类型对应不同的命令，如 `mkfs.xfs`。

![](img/b17afc39585b87d237bd53bfed543514_60.png)

![](img/b17afc39585b87d237bd53bfed543514_61.png)

格式化后，使用 `mount` 命令挂载到目录。
```bash
mount /dev/vdb3 /mnt/data
```
如需开机自动挂载，需将挂载信息写入 `/etc/fstab` 配置文件。

![](img/b17afc39585b87d237bd53bfed543514_63.png)

## 实战：添加交换分区

现在，我们应用以上知识来完成“添加交换分区”的题目。题目要求：添加一个512MB的交换分区，并使其开机自动挂载。

**操作步骤如下：**

![](img/b17afc39585b87d237bd53bfed543514_65.png)

![](img/b17afc39585b87d237bd53bfed543514_66.png)

1.  **创建分区**：在 `/dev/vdb` 磁盘上创建一个512MB的新分区（例如 `/dev/vdb2`）。
    ```bash
    fdisk /dev/vdb
    # 交互界面内操作：n -> (分区号默认) -> (起始扇区默认) -> +512M -> w
    ```

![](img/b17afc39585b87d237bd53bfed543514_68.png)

2.  **格式化交换分区**：交换分区需要特殊的格式化处理。
    ```bash
    mkswap /dev/vdb2
    ```

3.  **启用交换分区**：
    ```bash
    swapon /dev/vdb2
    ```
    使用 `swapon -s` 命令检查是否启用成功。

![](img/b17afc39585b87d237bd53bfed543514_70.png)

![](img/b17afc39585b87d237bd53bfed543514_72.png)

4.  **配置开机自动挂载**：编辑 `/etc/fstab` 文件，添加一行。
    ```bash
    vim /etc/fstab
    ```
    在文件末尾添加如下内容（可复制已有的swap行进行修改）：
    ```
    /dev/vdb2 swap swap defaults 0 0
    ```

![](img/b17afc39585b87d237bd53bfed543514_74.png)

![](img/b17afc39585b87d237bd53bfed543514_76.png)

5.  **验证配置**：可以不重启，通过以下命令测试配置是否正确。
    ```bash
    swapoff /dev/vdb2          # 先停用
    swapon -a                  # 根据 /etc/fstab 启用所有swap
    swapon -s                  # 再次检查，应能看到 /dev/vdb2
    ```

## 课程总结

![](img/b17afc39585b87d237bd53bfed543514_78.png)

![](img/b17afc39585b87d237bd53bfed543514_80.png)

![](img/b17afc39585b87d237bd53bfed543514_82.png)

本节课中我们一起学习了Linux磁盘管理的核心操作。我们首先了解了RHCE考试的磁盘环境规划，然后系统性地讲解了从分区、格式化到挂载的完整流程，并详细介绍了 `fdisk` 和 `parted` 两种分区工具的使用区别。最后，我们通过“添加交换分区”的实战题目，完整演练了分区创建、`mkswap` 格式化、`swapon` 启用以及配置 `/etc/fstab` 实现开机自动挂载的全过程。掌握这些基础是完成后续逻辑卷、VDO等高级存储管理题目的关键。