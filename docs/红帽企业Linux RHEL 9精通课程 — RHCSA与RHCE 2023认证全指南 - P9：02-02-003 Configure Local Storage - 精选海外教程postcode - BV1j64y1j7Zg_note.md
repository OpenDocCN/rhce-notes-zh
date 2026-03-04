# 红帽企业Linux RHEL 9精通课程：P9：配置本地存储 🖥️💾

![](img/ecabda79dd56eddb2b2c4003d117d354_1.png)

在本节课中，我们将学习如何在RHEL 9系统中配置本地存储。我们将回顾几个用于查看存储信息的核心命令，并实践从创建分区到建立逻辑卷管理（LVM）结构的完整流程。本节内容旨在帮助你掌握RHCSA与RHCE认证中关于本地存储配置的基础技能。

![](img/ecabda79dd56eddb2b2c4003d117d354_3.png)

---

![](img/ecabda79dd56eddb2b2c4003d117d354_5.png)

![](img/ecabda79dd56eddb2b2c4003d117d354_7.png)

## 查看存储信息

![](img/ecabda79dd56eddb2b2c4003d117d354_9.png)

![](img/ecabda79dd56eddb2b2c4003d117d354_11.png)

上一节我们介绍了课程概述，本节中我们来看看如何获取系统当前的存储信息。首先，我们将使用几个命令来查看磁盘空间、块设备和分区详情。

![](img/ecabda79dd56eddb2b2c4003d117d354_13.png)

以下是用于查看存储信息的命令列表：

*   **`df -h`**：此命令以人类可读的格式（如GB、MB）显示当前已挂载文件系统的可用磁盘空间。
    ```bash
    df -h
    ```
*   **`lsblk`**：此命令列出所有块设备（如磁盘、分区）及其大小、类型和挂载点信息。
    ```bash
    lsblk
    ```
*   **`blkid`**：此命令显示块设备的唯一标识符（UUID）和文件系统类型。它能帮助我们识别分区表类型（如MBR/DOS或GPT）。
    ```bash
    blkid
    ```
*   **`fdisk -l /dev/nvme1n1`**：此命令用于查看指定磁盘（例如 `/dev/nvme1n1`）的详细信息，包括空间大小和扇区数量。请注意，执行此类命令通常需要root权限。
    ```bash
    fdisk -l /dev/nvme1n1
    ```

![](img/ecabda79dd56eddb2b2c4003d117d354_15.png)

> **注意**：为了跟随本教程操作，你需要在实验环境中添加额外的磁盘。在Cloud Playground中，你可以通过服务器的“操作”按钮添加 `/dev/nvme` 设备，添加后可能需要重启服务器。

---

![](img/ecabda79dd56eddb2b2c4003d117d354_17.png)

## 创建分区与LVM结构

![](img/ecabda79dd56eddb2b2c4003d117d354_19.png)

![](img/ecabda79dd56eddb2b2c4003d117d354_21.png)

在了解了如何查看存储信息后，我们现在开始实践配置。我们将在一块新磁盘上创建分区，并以此为基础构建LVM（逻辑卷管理）结构。

### 1. 创建分区表与分区

![](img/ecabda79dd56eddb2b2c4003d117d354_23.png)

![](img/ecabda79dd56eddb2b2c4003d117d354_25.png)

首先，我们使用 `fdisk` 工具在新磁盘上创建MBR（DOS）分区表和一个分区。

![](img/ecabda79dd56eddb2b2c4003d117d354_27.png)

以下是使用 `fdisk` 创建分区的步骤：

![](img/ecabda79dd56eddb2b2c4003d117d354_29.png)

![](img/ecabda79dd56eddb2b2c4003d117d354_31.png)

1.  运行 `fdisk /dev/nvme1n1` 进入磁盘操作界面。
2.  输入 `m` 可以查看所有可用命令。
3.  输入 `o` 创建一个新的空DOS（MBR）分区表。
4.  输入 `n` 创建一个新分区，类型选择“主分区”（Primary），分区号、起始扇区和结束扇区均可使用默认值（按回车键），这将使用整个磁盘空间。
5.  输入 `t` 更改分区类型。输入 `L` 可以列出所有类型代码，我们选择 `8e`（Linux LVM）。
6.  输入 `p` 预览分区表。
7.  确认无误后，输入 `w` 将更改写入磁盘并退出。

![](img/ecabda79dd56eddb2b2c4003d117d354_33.png)

操作完成后，可以使用 `lsblk` 命令确认分区 `/dev/nvme1n1p1` 已成功创建。

![](img/ecabda79dd56eddb2b2c4003d117d354_35.png)

![](img/ecabda79dd56eddb2b2c4003d117d354_37.png)

### 2. 创建物理卷、卷组与逻辑卷

![](img/ecabda79dd56eddb2b2c4003d117d354_39.png)

分区准备就绪后，接下来我们使用LVM工具来创建灵活的逻辑存储。

以下是创建LVM结构的命令序列：

1.  **创建物理卷（PV）**：将分区初始化为LVM物理卷。
    ```bash
    pvcreate /dev/nvme1n1p1
    ```
    使用 `pvs` 命令可以列出所有物理卷。
2.  **创建卷组（VG）**：创建一个名为“test_vg”的卷组，并将物理卷加入其中。
    ```bash
    vgcreate test_vg /dev/nvme1n1p1
    ```
    使用 `vgs` 命令可以列出所有卷组。
3.  **创建逻辑卷（LV）**：在卷组内创建一个大小为1GB、名为“test_lv”的逻辑卷。
    ```bash
    lvcreate -L 1G -n test_lv test_vg
    ```
    使用 `lvs` 命令可以列出所有逻辑卷。

---

![](img/ecabda79dd56eddb2b2c4003d117d354_41.png)

![](img/ecabda79dd56eddb2b2c4003d117d354_43.png)

## 删除LVM结构与分区

![](img/ecabda79dd56eddb2b2c4003d117d354_45.png)

为了保持环境整洁或进行重新配置，了解如何清理已创建的LVM结构同样重要。

![](img/ecabda79dd56eddb2b2c4003d117d354_47.png)

以下是反向删除LVM组件和分区的步骤：

![](img/ecabda79dd56eddb2b2c4003d117d354_49.png)

1.  **删除逻辑卷**：使用 `lvremove` 命令。
    ```bash
    lvremove test_vg/test_lv
    ```
2.  **删除卷组**：使用 `vgremove` 命令。
    ```bash
    vgremove test_vg
    ```
3.  **删除物理卷**：使用 `pvremove` 命令。
    ```bash
    pvremove /dev/nvme1n1p1
    ```

![](img/ecabda79dd56eddb2b2c4003d117d354_51.png)

![](img/ecabda79dd56eddb2b2c4003d117d354_53.png)

完成以上步骤后，你可以再次使用 `fdisk` 工具删除分区或重新规划磁盘。

![](img/ecabda79dd56eddb2b2c4003d117d354_55.png)

---

![](img/ecabda79dd56eddb2b2c4003d117d354_57.png)

## 总结与进一步学习

![](img/ecabda79dd56eddb2b2c4003d117d354_59.png)

![](img/ecabda79dd56eddb2b2c4003d117d354_61.png)

本节课中我们一起学习了在RHEL 9中配置本地存储的核心操作。我们首先使用 `df`、`lsblk`、`blkid` 等命令查看存储状态，然后实践了使用 `fdisk` 创建分区，并最终完成了从物理卷（PV）、卷组（VG）到逻辑卷（LV）的完整LVM创建与删除流程。

![](img/ecabda79dd56eddb2b2c4003d117d354_63.png)

> **提示**：本教程展示的是最基础的命令用法。每个命令都支持更多标志和选项以实现复杂功能（例如，创建逻辑卷时指定具体大小或使用百分比）。要深入了解，请务必查阅系统手册页，例如使用 `man lvcreate` 命令。

![](img/ecabda79dd56eddb2b2c4003d117d354_65.png)

掌握这些基础操作是管理Linux系统存储的第一步，也是通过RHCSA和RHCE认证的关键技能之一。