# 红帽企业Linux RHEL 9精通课程：P10：02-02-004 创建和配置文件系统 📂

![](img/2ff6952bc122ad054ea8c0edf74d9058_1.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_3.png)

在本节课中，我们将学习如何在RHEL 9系统上创建和配置文件系统。主要内容包括在逻辑卷上创建文件系统、挂载文件系统、实现持久化挂载、调整文件系统大小，以及挂载网络文件系统（NFS）和设置协作目录。

![](img/2ff6952bc122ad054ea8c0edf74d9058_5.png)

---

![](img/2ff6952bc122ad054ea8c0edf74d9058_7.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_9.png)

## 在逻辑卷上创建文件系统

![](img/2ff6952bc122ad054ea8c0edf74d9058_11.png)

上一节我们介绍了逻辑卷的创建，本节中我们来看看如何在逻辑卷上创建文件系统。

![](img/2ff6952bc122ad054ea8c0edf74d9058_13.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_15.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_16.png)

首先，我们需要使用 `mkfs` 命令在逻辑卷上创建一个文件系统。这里我们选择创建 **ext4** 类型的文件系统。

![](img/2ff6952bc122ad054ea8c0edf74d9058_18.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_20.png)

以下是创建文件系统的命令：
```bash
mkfs.ext4 /dev/mapper/test_vol-test_lv
```
执行此命令后，文件系统将快速创建完成。

![](img/2ff6952bc122ad054ea8c0edf74d9058_22.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_24.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_26.png)

---

![](img/2ff6952bc122ad054ea8c0edf74d9058_28.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_29.png)

## 挂载文件系统

![](img/2ff6952bc122ad054ea8c0edf74d9058_31.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_33.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_35.png)

创建文件系统后，我们需要将其挂载到一个目录才能使用。

![](img/2ff6952bc122ad054ea8c0edf74d9058_37.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_39.png)

首先，创建一个挂载点目录：
```bash
mkdir /mnt/test_fs
```
然后，使用 `mount` 命令将逻辑卷挂载到该目录：
```bash
mount /dev/mapper/test_vol-test_lv /mnt/test_fs
```
挂载完成后，可以使用 `df -h` 命令查看已挂载的文件系统，确认挂载成功。

![](img/2ff6952bc122ad054ea8c0edf74d9058_41.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_43.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_45.png)

为了验证文件系统可写，可以在挂载点创建一个测试文件：
```bash
touch /mnt/test_fs/test1
```

![](img/2ff6952bc122ad054ea8c0edf74d9058_47.png)

---

![](img/2ff6952bc122ad054ea8c0edf74d9058_49.png)

## 实现持久化挂载

![](img/2ff6952bc122ad054ea8c0edf74d9058_51.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_53.png)

目前，文件系统的挂载是临时的，系统重启后会失效。为了实现持久化挂载，我们需要将其信息添加到 `/etc/fstab` 文件中。

![](img/2ff6952bc122ad054ea8c0edf74d9058_55.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_57.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_59.png)

首先，获取逻辑卷的UUID：
```bash
blkid /dev/mapper/test_vol-test_lv
```
然后，编辑 `/etc/fstab` 文件，在文件末尾添加一行配置：
```
UUID=<你的UUID> /mnt/test_fs ext4 defaults 0 0
```
保存文件后，可以卸载并重新挂载所有在 `/etc/fstab` 中定义的文件系统以测试配置：
```bash
umount /mnt/test_fs
mount -a
df -h
```
现在，即使系统重启，该文件系统也会被自动挂载。

![](img/2ff6952bc122ad054ea8c0edf74d9058_61.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_63.png)

---

![](img/2ff6952bc122ad054ea8c0edf74d9058_65.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_67.png)

## 调整文件系统大小

![](img/2ff6952bc122ad054ea8c0edf74d9058_68.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_70.png)

有时我们需要扩展逻辑卷及其上的文件系统。这个过程分为两步：首先扩展逻辑卷，然后扩展文件系统。

![](img/2ff6952bc122ad054ea8c0edf74d9058_72.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_73.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_75.png)

首先，使用 `lvextend` 命令扩展逻辑卷的大小（例如，增加500MB）：
```bash
lvextend -L +500M /dev/mapper/test_vol-test_lv
```
此时，逻辑卷的物理空间已扩大，但文件系统还未感知到这一变化。运行 `df -h` 会发现可用空间没有增加。

![](img/2ff6952bc122ad054ea8c0edf74d9058_77.png)

接下来，需要扩展文件系统以使用新增的空间。首先卸载文件系统：
```bash
umount /mnt/test_fs
```
然后，检查文件系统（对于ext4，此步非强制但建议）：
```bash
e2fsck -f /dev/mapper/test_vol-test_lv
```
最后，使用 `resize2fs` 命令调整文件系统大小：
```bash
resize2fs /dev/mapper/test_vol-test_lv
```
调整完成后，重新挂载文件系统：
```bash
mount -a
df -h
```
现在，可以看到文件系统的可用空间已经增加。

![](img/2ff6952bc122ad054ea8c0edf74d9058_78.png)

---

![](img/2ff6952bc122ad054ea8c0edf74d9058_80.png)

## 挂载网络文件系统（NFS）

![](img/2ff6952bc122ad054ea8c0edf74d9058_82.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_84.png)

除了本地文件系统，我们还可以挂载网络文件系统（NFS）。这允许客户端访问服务器上共享的目录。

![](img/2ff6952bc122ad054ea8c0edf74d9058_86.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_88.png)

首先，在客户端安装必要的NFS工具包：
```bash
yum install nfs-utils
```
安装完成后，可以查看NFS服务器上共享的目录列表：
```bash
showmount -e <NFS服务器IP地址>
```
创建一个本地目录作为挂载点：
```bash
mkdir /mnt/nfs
```
然后，挂载NFS共享目录到本地挂载点：
```bash
mount -t nfs <NFS服务器IP地址>:/mnt/nfs /mnt/nfs
```
挂载后，可以使用 `df -h` 命令查看。同样，为了持久化，需要将NFS挂载信息添加到 `/etc/fstab` 文件中：
```
<NFS服务器IP地址>:/mnt/nfs /mnt/nfs nfs defaults 0 0
```

![](img/2ff6952bc122ad054ea8c0edf74d9058_90.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_92.png)

---

![](img/2ff6952bc122ad054ea8c0edf74d9058_94.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_96.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_98.png)

## 创建协作目录（SetGID）

![](img/2ff6952bc122ad054ea8c0edf74d9058_100.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_102.png)

SetGID权限位可以用于创建协作目录，使得在该目录下创建的任何文件都自动继承目录的所属组，方便组内成员协作。

![](img/2ff6952bc122ad054ea8c0edf74d9058_104.png)

首先，创建一个协作目录：
```bash
mkdir /mnt/collab
```
然后，为该目录设置SetGID权限：
```bash
chmod g+s /mnt/collab
```
可以更改目录的所属组，例如改为 `wheel` 组：
```bash
chgrp wheel /mnt/collab
```
现在，任何在该目录下创建的文件，其所属组都会自动设置为 `wheel` 组，无论创建者的主要组是什么。这简化了组内文件的共享和管理。

![](img/2ff6952bc122ad054ea8c0edf74d9058_106.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_108.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_110.png)

---

![](img/2ff6952bc122ad054ea8c0edf74d9058_111.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_113.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_115.png)

## 虚拟数据优化器（VDO）简介

![](img/2ff6952bc122ad054ea8c0edf74d9058_117.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_119.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_121.png)

虚拟数据优化器（VDO）是一种存储层，它通过压缩和去重技术来节省磁盘空间。它可以将一个物理上较小的磁盘，在逻辑上呈现为一个更大的卷给操作系统使用。

![](img/2ff6952bc122ad054ea8c0edf74d9058_123.png)

![](img/2ff6952bc122ad054ea8c0edf74d9058_125.png)

安装VDO非常简单：
```bash
yum install vdo kmod-kvdo
```
创建VDO卷的基本命令格式如下：
```bash
vdo create --name=<卷名> --device=<设备路径> --vdoLogicalSize=<逻辑大小>
```
例如，一个50GB的物理磁盘，通过VDO去重后，可能可以呈现给操作系统150GB的逻辑空间。实际能节省的空间取决于存储数据的重复程度。

---

## 总结

![](img/2ff6952bc122ad054ea8c0edf74d9058_127.png)

本节课中我们一起学习了RHEL 9中文件系统的核心管理操作。我们掌握了如何在逻辑卷上创建ext4文件系统、挂载文件系统、通过 `/etc/fstab` 实现持久化挂载、扩展逻辑卷和文件系统的大小、挂载网络NFS共享，以及使用SetGID权限创建协作目录。最后，我们还简要了解了虚拟数据优化器（VDO）的概念。这些技能是系统管理员进行日常存储管理和配置的基础。