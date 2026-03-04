# Redhat红帽 RHCE8.0认证体系课程：P47：NFS与autofs自动挂载 🗂️

![](img/4000b020efd2a8e5c00b8244a677e5c6_1.png)

在本节课中，我们将要学习网络文件系统（NFS）的基本概念、配置方法以及如何实现自动挂载（autofs）。我们将从搭建NFS服务器开始，到客户端如何手动和自动挂载共享目录，并解决常见的权限问题。

## NFS网络文件系统概述

上一节我们介绍了课程的整体安排，本节中我们来看看NFS网络文件系统。NFS（Network File System）主要用于Unix和Linux系统之间的资源共享。它允许客户端像访问本地目录一样访问服务器上的共享目录。

![](img/4000b020efd2a8e5c00b8244a677e5c6_3.png)

## 配置NFS服务器端

![](img/4000b020efd2a8e5c00b8244a677e5c6_5.png)

要提供NFS共享服务，首先需要在服务器端进行配置。以下是配置NFS服务器的主要步骤。

![](img/4000b020efd2a8e5c00b8244a677e5c6_7.png)

### 安装NFS服务软件包

如果系统是最小化安装，需要安装`nfs-utils`软件包。

![](img/4000b020efd2a8e5c00b8244a677e5c6_9.png)

```bash
dnf install -y nfs-utils
```

### 启动NFS服务

安装完成后，启动并启用NFS服务。

![](img/4000b020efd2a8e5c00b8244a677e5c6_11.png)

```bash
systemctl enable --now nfs-server
systemctl status nfs-server
```

![](img/4000b020efd2a8e5c00b8244a677e5c6_13.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_15.png)

### 创建共享目录并准备示例文件

![](img/4000b020efd2a8e5c00b8244a677e5c6_17.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_19.png)

创建一个用于共享的目录，并在其中生成示例文件，以便客户端验证。

```bash
mkdir /mt/share
touch /mt/share/file1
mkdir /mt/share/dir1
```

### 配置共享导出文件

NFS通过`/etc/exports`文件定义共享目录和访问权限。

编辑`/etc/exports`文件，添加以下内容：

![](img/4000b020efd2a8e5c00b8244a677e5c6_21.png)

```
/mt/share 192.168.14.0/24(rw,sync)
```

![](img/4000b020efd2a8e5c00b8244a677e5c6_23.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_25.png)

**格式说明**：
*   **共享目录**：服务器上要共享的目录路径。
*   **网络地址范围**：允许访问该共享的客户端IP或网段。
*   **权限**：括号内的选项，例如：
    *   `rw`：读写权限。
    *   `ro`：只读权限。
    *   `sync`：同步写入（数据先写入服务器磁盘，再返回成功信号）。

![](img/4000b020efd2a8e5c00b8244a677e5c6_27.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_29.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_31.png)

### 使配置生效并检查

![](img/4000b020efd2a8e5c00b8244a677e5c6_33.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_35.png)

修改配置文件后，需要重新加载配置并检查共享是否发布成功。

![](img/4000b020efd2a8e5c00b8244a677e5c6_37.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_39.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_41.png)

```bash
# 重新加载exports配置
exportfs -rv
# 查看本机发布的NFS共享列表
showmount -e localhost
```

![](img/4000b020efd2a8e5c00b8244a677e5c6_43.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_45.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_47.png)

### 配置防火墙

![](img/4000b020efd2a8e5c00b8244a677e5c6_49.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_50.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_52.png)

默认情况下，防火墙会阻止NFS通信。需要在服务器端放行相关服务。

![](img/4000b020efd2a8e5c00b8244a677e5c6_54.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_56.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_58.png)

```bash
firewall-cmd --permanent --add-service=nfs
firewall-cmd --permanent --add-service=mountd
firewall-cmd --permanent --add-service=rpc-bind
firewall-cmd --reload
```

![](img/4000b020efd2a8e5c00b8244a677e5c6_60.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_62.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_64.png)

## 配置NFS客户端挂载

![](img/4000b020efd2a8e5c00b8244a677e5c6_66.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_68.png)

服务器配置完成后，客户端就可以挂载共享目录了。本节中我们来看看客户端如何进行手动和永久挂载。

![](img/4000b020efd2a8e5c00b8244a677e5c6_70.png)

### 检查NFS共享可用性

![](img/4000b020efd2a8e5c00b8244a677e5c6_72.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_74.png)

在客户端，首先确认可以查看到服务器发布的共享列表。

![](img/4000b020efd2a8e5c00b8244a677e5c6_76.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_78.png)

```bash
showmount -e 192.168.14.128
```

![](img/4000b020efd2a8e5c00b8244a677e5c6_80.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_82.png)

### 手动临时挂载

![](img/4000b020efd2a8e5c00b8244a677e5c6_84.png)

可以创建一个本地目录作为挂载点，并使用`mount`命令进行临时挂载。

![](img/4000b020efd2a8e5c00b8244a677e5c6_86.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_88.png)

```bash
# 创建本地挂载点
mkdir -p /mnt/nfs_share
# 手动挂载NFS共享
mount -t nfs 192.168.14.128:/mt/share /mnt/nfs_share
# 检查挂载结果
df -hT
```

![](img/4000b020efd2a8e5c00b8244a677e5c6_90.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_92.png)

**命令参数说明**：
*   `-t nfs`：指定文件系统类型为NFS。
*   `192.168.14.128:/mt/share`：服务器IP和共享目录路径。
*   `/mnt/nfs_share`：本地挂载点目录。

![](img/4000b020efd2a8e5c00b8244a677e5c6_94.png)

### 配置永久挂载

为了使挂载在系统重启后依然有效，需要编辑`/etc/fstab`文件。

![](img/4000b020efd2a8e5c00b8244a677e5c6_96.png)

在`/etc/fstab`文件中添加一行：

![](img/4000b020efd2a8e5c00b8244a677e5c6_98.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_100.png)

```
192.168.14.128:/mt/share  /mnt/nfs_share  nfs  defaults  0 0
```

添加后，使用`mount -a`命令测试配置是否正确，并重新挂载所有`fstab`中的条目。

```bash
mount -a
```

## 解决NFS写入权限问题

![](img/4000b020efd2a8e5c00b8244a677e5c6_102.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_104.png)

挂载成功后，客户端访问共享目录时可能会遇到无法写入文件的问题。本节中我们来分析原因并提供两种解决方案。

### 问题原因分析

默认情况下，NFS服务器会将客户端的root用户权限“压缩”或“映射”为一个低权限用户（通常是`nobody`，用户ID为65534）。这意味着客户端的root用户在服务器共享目录上只具有“其他用户”（others）的权限。

### 解决方案一：在服务器端放宽目录权限（推荐）

在NFS服务器上，直接为共享目录的“其他用户”添加写权限。

```bash
chmod o+w /mt/share
```

![](img/4000b020efd2a8e5c00b8244a677e5c6_106.png)

此操作后，客户端即可在共享目录中创建或修改文件。

![](img/4000b020efd2a8e5c00b8244a677e5c6_108.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_110.png)

### 解决方案二：修改NFS导出选项（不推荐）

![](img/4000b020efd2a8e5c00b8244a677e5c6_112.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_114.png)

在服务器端的`/etc/exports`文件中，为共享配置添加`no_root_squash`选项。

![](img/4000b020efd2a8e5c00b8244a677e5c6_116.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_118.png)

```
/mt/share 192.168.14.0/24(rw,sync,no_root_squash)
```

然后重新加载配置。

![](img/4000b020efd2a8e5c00b8244a677e5c6_120.png)

```bash
exportfs -rv
```

**注意**：`no_root_squash`选项允许客户端的root用户以root权限访问服务器共享目录，这存在严重的安全风险，通常不建议在生产环境中使用。

## 配置autofs自动挂载

手动或永久挂载会使共享目录一直处于连接状态。本节中我们来看看如何使用autofs实现按需挂载，即仅在访问目录时自动挂载，闲置一段时间后自动卸载。

### autofs工作原理

![](img/4000b020efd2a8e5c00b8244a677e5c6_122.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_123.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_124.png)

autofs服务监控指定的“挂载点”目录。当用户尝试访问该目录下的特定子目录时，autofs会根据配置规则，自动将对应的远程共享挂载到这个子目录上。如果超过默认时间（如5分钟）没有访问，则会自动卸载。

### 安装与启动autofs服务

在客户端安装`autofs`软件包并启动服务。

```bash
dnf install -y autofs
systemctl enable --now autofs
```

![](img/4000b020efd2a8e5c00b8244a677e5c6_126.png)

### 配置主映射文件

编辑autofs的主配置文件`/etc/auto.master`，定义监控目录和对应的映射文件。

在文件末尾添加一行：

![](img/4000b020efd2a8e5c00b8244a677e5c6_128.png)

```
/mnt/autofs  /etc/auto.ldap
```

![](img/4000b020efd2a8e5c00b8244a677e5c6_130.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_132.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_134.png)

*   `/mnt/autofs`：autofs服务的监控目录。
*   `/etc/auto.ldap`：自定义的映射规则文件（文件名可自定）。

![](img/4000b020efd2a8e5c00b8244a677e5c6_136.png)

### 配置子映射文件

![](img/4000b020efd2a8e5c00b8244a677e5c6_138.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_140.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_142.png)

创建并编辑上一步指定的映射规则文件`/etc/auto.ldap`。

![](img/4000b020efd2a8e5c00b8244a677e5c6_144.png)

```bash
vim /etc/auto.ldap
```

在文件中添加挂载规则，格式为：

```
nfs_share  -fstype=nfs  192.168.14.128:/mt/share
```

![](img/4000b020efd2a8e5c00b8244a677e5c6_146.png)

*   `nfs_share`：在监控目录`/mnt/autofs`下自动创建的挂载点子目录名称。
*   `-fstype=nfs`：指定文件系统类型。
*   `192.168.14.128:/mt/share`：远程NFS共享源。

![](img/4000b020efd2a8e5c00b8244a677e5c6_148.png)

### 测试自动挂载

![](img/4000b020efd2a8e5c00b8244a677e5c6_150.png)

![](img/4000b020efd2a8e5c00b8244a677e5c6_152.png)

配置完成后，无需重启服务，配置会自动加载。进行测试：

![](img/4000b020efd2a8e5c00b8244a677e5c6_154.png)

1.  首先查看`/mnt/autofs`目录，此时`nfs_share`子目录可能不存在或为空。
2.  尝试进入`/mnt/autofs/nfs_share`目录。
3.  进入后，使用`df -hT`命令，会发现系统自动挂载了NFS共享。
4.  退出该目录并等待几分钟（默认超时时间）后，再次使用`df -hT`命令，挂载会自动消失。

```bash
# 访问触发挂载
cd /mnt/autofs/nfs_share
# 检查是否挂载
df -hT | grep nfs
# 退出并等待后，检查是否卸载
cd /
df -hT | grep nfs
```

## 总结

本节课中我们一起学习了Red Hat Linux中NFS网络文件系统的配置与管理。我们从搭建NFS服务器开始，详细讲解了共享目录的发布、防火墙配置。接着，在客户端我们实践了手动挂载、永久挂载（通过`/etc/fstab`）以及如何解决常见的NFS写入权限问题。最后，我们探索了更高效的autofs自动挂载服务，实现了网络目录的按需挂载与自动卸载，这在实际生产环境中对于管理大量挂载点非常有用。通过掌握这些技能，你能够有效地在Linux系统间设置和管理文件共享服务。