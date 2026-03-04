# Linux运维教程：P57：vsftpd本地用户访问模式与NFS网络附加存储

![](img/4029e18fc2c07d32644d89cd5534cfe2_1.png)

在本节课中，我们将学习vsftpd服务的本地用户访问模式配置，以及NFS网络文件系统的基本原理与部署方法。这两种技术都是实现网络文件共享与存储的重要方式。

![](img/4029e18fc2c07d32644d89cd5534cfe2_3.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_5.png)

## vsftpd本地用户访问模式

上一节我们介绍了vsftpd的匿名用户访问模式。本节中我们来看看如何配置和使用本地用户模式，以实现更精确的访问控制。

![](img/4029e18fc2c07d32644d89cd5534cfe2_7.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_8.png)

### 禁用匿名用户访问

![](img/4029e18fc2c07d32644d89cd5534cfe2_10.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_12.png)

某些企业环境可能希望仅允许特定的本地用户访问FTP服务器，而非所有人。为此，首先需要禁用匿名访问。

1.  编辑vsftpd的主配置文件 `/etc/vsftpd/vsftpd.conf`。
2.  找到 `anonymous_enable` 参数，将其值修改为 `NO`。请注意，注释掉该行（默认值为YES）并不能有效禁用匿名访问。
3.  保存配置文件并重启vsftpd服务使配置生效。

```bash
# 编辑配置文件
vi /etc/vsftpd/vsftpd.conf
# 修改参数
anonymous_enable=NO
# 重启服务
systemctl restart vsftpd
```

禁用后，尝试使用匿名方式连接服务器将会失败。

### 配置与使用本地用户

![](img/4029e18fc2c07d32644d89cd5534cfe2_14.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_16.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_18.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_20.png)

禁用匿名访问后，我们可以创建并使用本地系统用户账号来访问FTP服务器。

以下是创建本地用户并测试访问的步骤：

![](img/4029e18fc2c07d32644d89cd5534cfe2_22.png)

1.  在服务器端创建一个用于FTP访问的本地用户（例如 `ftpuser`）。
2.  客户端使用该用户名和密码进行连接。连接成功后，用户将进入其在服务器上的家目录（如 `/home/ftpuser`）。
3.  用户在家目录下的操作（如下载文件）将受系统权限和vsftpd配置的共同控制。

![](img/4029e18fc2c07d32644d89cd5534cfe2_24.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_26.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_28.png)

```bash
# 服务器端：创建用户
useradd ftpuser
# 客户端：使用lftp连接
lftp ftpuser@192.168.0.40
# 输入密码后即可登录
```

### 控制本地用户的活动范围

默认情况下，本地FTP用户可以切换到服务器文件系统的其他目录，这可能存在安全风险。为了将其活动范围限制在其家目录内，需要进行“禁锢”（chroot）配置。

以下是配置禁锢的步骤：

1.  在 `/etc/vsftpd/vsftpd.conf` 配置文件中，找到并取消 `chroot_local_user=YES` 这一行的注释。此设置将本地用户禁锢在其家目录。
2.  保存并重启服务后，用户再次登录时，其根目录（`/`）将变为其家目录，无法访问上级目录。
3.  **重要注意事项**：启用禁锢后，该用户的家目录**不能**拥有写权限（`w`），否则连接会失败。需要移除家目录的写权限。
4.  为了允许用户上传文件，可以在其家目录下创建一个子目录（如 `public`），并将该子目录的所有者改为相应用户，用户即可在此目录内进行读写操作。

![](img/4029e18fc2c07d32644d89cd5534cfe2_30.png)

```bash
# 1. 修改配置文件，启用禁锢
chroot_local_user=YES
# 2. 移除用户家目录的写权限
chmod u-w /home/ftpuser
# 3. 创建并授权上传目录
mkdir /home/ftpuser/public
chown ftpuser:ftpuser /home/ftpuser/public
```

![](img/4029e18fc2c07d32644d89cd5534cfe2_32.png)

### 控制本地用户的写权限

![](img/4029e18fc2c07d32644d89cd5534cfe2_34.png)

默认配置下，本地用户具备上传、删除等写权限。如果希望像控制匿名用户一样，仅允许其下载和查看，可以修改配置文件。

![](img/4029e18fc2c07d32644d89cd5534cfe2_36.png)

在 `/etc/vsftpd/vsftpd.conf` 中，注释掉 `write_enable=YES` 这一行，即可全局禁用本地用户的写权限。

![](img/4029e18fc2c07d32644d89cd5534cfe2_38.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_40.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_42.png)

```bash
# 将此行注释掉，以禁用所有本地用户的写权限
# write_enable=YES
```

![](img/4029e18fc2c07d32644d89cd5534cfe2_44.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_46.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_48.png)

### 用户访问控制名单

![](img/4029e18fc2c07d32644d89cd5534cfe2_50.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_52.png)

vsftpd通过两个文件实现用户访问控制：

![](img/4029e18fc2c07d32644d89cd5534cfe2_54.png)

*   `/etc/vsftpd/ftpusers`：**黑名单文件**。列在此文件中的用户禁止登录FTP服务。默认已包含 `root` 等系统账号。
*   `/etc/vsftpd/user_list`：此文件可灵活配置为黑名单或白名单。
    *   当配置文件中 `userlist_deny=YES`（默认）时，此文件为黑名单。
    *   当 `userlist_deny=NO` 时，此文件为白名单，仅允许列表中的用户登录。

通常，使用默认的 `ftpusers` 黑名单即可满足多数需求。

## NFS网络附加存储

![](img/4029e18fc2c07d32644d89cd5534cfe2_56.png)

接下来，我们学习另一种文件共享服务——NFS。NFS因其配置简单、使用方便，常用于数据备份等场景。

![](img/4029e18fc2c07d32644d89cd5534cfe2_58.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_59.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_61.png)

### NFS概述与部署

![](img/4029e18fc2c07d32644d89cd5534cfe2_63.png)

NFS允许将服务器上的目录共享给网络上的其他主机，客户端可以像使用本地目录一样挂载和使用这个共享目录。

![](img/4029e18fc2c07d32644d89cd5534cfe2_65.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_66.png)

部署NFS服务非常简单：

![](img/4029e18fc2c07d32644d89cd5534cfe2_68.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_70.png)

1.  安装NFS软件包。
2.  启动NFS及其依赖的RPC服务。
3.  创建准备共享的目录。
4.  编辑 `/etc/exports` 配置文件，声明共享目录、允许访问的客户端及访问权限。
5.  重新加载配置，使共享生效。

```bash
# 服务器端：安装与启动
yum install -y nfs-utils
systemctl start nfs
# 创建共享目录
mkdir /nfs_upload
# 编辑配置文件 /etc/exports，添加如下行：
# /nfs_upload 192.168.0.10(rw)
# 重新加载配置
exportfs -r
```

![](img/4029e18fc2c07d32644d89cd5534cfe2_72.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_73.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_75.png)

配置文件示例 `/etc/exports` 解析：
*   `/nfs_upload`：要共享的本地目录路径。
*   `192.168.0.10`：允许访问的客户端IP地址。
*   `(rw)`：授予客户端的权限，`rw` 表示读写。

![](img/4029e18fc2c07d32644d89cd5534cfe2_77.png)

### 客户端挂载与使用NFS共享

客户端要使用NFS共享，需要安装 `nfs-utils` 工具包（以获取 `showmount`、`mount.nfs` 等命令），然后挂载远程目录。

以下是客户端操作步骤：

![](img/4029e18fc2c07d32644d89cd5534cfe2_79.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_80.png)

1.  查询NFS服务器提供的共享列表。
2.  在本地创建一个挂载点目录。
3.  使用 `mount` 命令将远程共享目录挂载到本地挂载点。
4.  之后即可像操作本地目录一样使用该共享目录，所有数据实际存储在NFS服务器上。

```bash
# 客户端：查询共享
showmount -e 192.168.0.40
# 创建本地挂载点
mkdir /nfs_upload
# 挂载远程目录
mount -t nfs 192.168.0.40:/nfs_upload /nfs_upload
# 查看挂载结果
df -h
```

### 配置开机自动挂载与权限处理

![](img/4029e18fc2c07d32644d89cd5534cfe2_82.png)

为了使挂载在系统重启后依然有效，需要将挂载信息写入 `/etc/fstab` 文件。

![](img/4029e18fc2c07d32644d89cd5534cfe2_84.png)

同时，需要注意NFS的权限映射问题。默认情况下，客户端的 `root` 用户会被映射为NFS服务器上的 `nfsnobody` 用户，可能导致权限不足。可以通过在服务器端的 `/etc/exports` 配置中添加 `no_root_squash` 选项来保留客户端 `root` 用户的身份和权限。

![](img/4029e18fc2c07d32644d89cd5534cfe2_86.png)

```bash
# 服务器端 /etc/exports 配置示例，允许客户端root并保留其权限
/nfs_upload 192.168.0.10(rw,no_root_squash)

![](img/4029e18fc2c07d32644d89cd5534cfe2_88.png)

![](img/4029e18fc2c07d32644d89cd5534cfe2_90.png)

# 客户端 /etc/fstab 配置示例，实现开机自动挂载
192.168.0.40:/nfs_upload /nfs_upload nfs defaults,_netdev 0 0
```
`_netdev` 参数表明这是一个网络设备，防止因网络未就绪导致系统启动失败。

### 存储模型简介

最后，我们简单了解三种常见的存储模型：
*   **DAS**：直连存储，如服务器内置硬盘、直接连接的USB移动硬盘。
*   **NAS**：网络附加存储，如本节所学的NFS和FTP，通过网络共享文件或目录。
*   **SAN**：存储区域网络，如iSCSI、FC-SAN，通过网络共享块设备（硬盘），客户端可以像使用本地硬盘一样对其进行格式化、分区。

![](img/4029e18fc2c07d32644d89cd5534cfe2_92.png)

本节课中我们一起学习了vsftpd本地用户模式的配置与管理，包括禁用匿名访问、用户禁锢和权限控制。随后，我们掌握了NFS网络文件系统的快速部署、客户端挂载以及基本权限配置。这些是构建企业级文件共享与备份方案的基础技能。