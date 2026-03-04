# Linux运维培训教程：P58：vsftpd本地用户访问模式与NFS网络附加存储 🖥️

![](img/8b61e8e6e261c50448cea7ea8499657d_1.png)

在本节课中，我们将学习vsftpd服务的本地用户访问模式配置，以及NFS网络文件系统的基本原理与部署方法。这两种技术都是实现文件共享与网络存储的重要方式。

![](img/8b61e8e6e261c50448cea7ea8499657d_3.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_5.png)

## vsftpd本地用户访问模式 🔐

上一节我们介绍了vsftpd的匿名用户访问模式。本节中，我们来看看如何配置和使用本地用户模式，以实现更精确的访问控制。

![](img/8b61e8e6e261c50448cea7ea8499657d_7.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_8.png)

### 禁用匿名用户访问

![](img/8b61e8e6e261c50448cea7ea8499657d_10.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_12.png)

要启用本地用户模式，首先需要禁用匿名用户访问。编辑vsftpd的主配置文件 `/etc/vsftpd/vsftpd.conf`，找到 `anonymous_enable` 参数。

```bash
# 将 anonymous_enable 设置为 NO
anonymous_enable=NO
```

**注意**：如果只是注释掉该行，默认值仍为 `YES`，匿名访问依然有效。因此，必须明确设置为 `NO`。修改后需要重启vsftpd服务。

### 创建本地用户并测试访问

![](img/8b61e8e6e261c50448cea7ea8499657d_14.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_16.png)

创建一个用于FTP访问的本地用户。

![](img/8b61e8e6e261c50448cea7ea8499657d_18.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_20.png)

```bash
useradd ftpuser
```

在客户端，使用该用户名和密码进行连接。

![](img/8b61e8e6e261c50448cea7ea8499657d_22.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_24.png)

```bash
lftp ftpuser@192.168.0.40
```

![](img/8b61e8e6e261c50448cea7ea8499657d_26.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_28.png)

连接成功后，用户默认会进入其家目录（如 `/home/ftpuser`）。只有该目录下的文件对用户可见。

### 本地用户的权限控制

默认情况下，本地用户在其家目录拥有完整的读写权限。这可能导致安全问题，例如用户可以切换到系统其他目录。

以下是控制本地用户活动范围和权限的关键配置项：

![](img/8b61e8e6e261c50448cea7ea8499657d_30.png)

1.  **限制用户活动范围（禁锢在家目录）**：
    在配置文件中启用 `chroot_local_user=YES`。启用后，用户将被限制在其家目录内，无法切换到上级目录。

![](img/8b61e8e6e261c50448cea7ea8499657d_32.png)

2.  **处理家目录写权限冲突**：
    启用 `chroot_local_user=YES` 后，如果用户家目录对用户本身有写权限（`w`），连接可能会失败。这是出于安全考虑。解决方案通常是：
    *   移除用户对其家目录的写权限：`chmod u-w /home/ftpuser`
    *   在家目录下创建一个子目录（如 `pub`），并将所有权赋予该用户，作为其实际的工作和上传目录。

![](img/8b61e8e6e261c50448cea7ea8499657d_34.png)

3.  **控制写权限**：
    配置文件中的 `write_enable=YES` 参数控制本地用户是否拥有写权限（上传、删除、重命名）。若只希望用户下载，可将其设置为 `NO`。

![](img/8b61e8e6e261c50448cea7ea8499657d_36.png)

### 用户访问控制名单

![](img/8b61e8e6e261c50448cea7ea8499657d_38.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_40.png)

vsftpd通过两个文件管理用户访问：

![](img/8b61e8e6e261c50448cea7ea8499657d_42.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_44.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_46.png)

*   `/etc/vsftpd/user_list`：此文件的作用由 `/etc/vsftpd/vsftpd.conf` 中的参数决定。
    *   `userlist_deny=YES`（默认）：此文件为**黑名单**，列出的用户被拒绝访问。
    *   `userlist_deny=NO`：此文件为**白名单**，仅允许列出的用户访问。
*   `/etc/vsftpd/ftpusers`：此文件是固定的**黑名单**，通常用于禁止系统高权限账户（如root）登录。

![](img/8b61e8e6e261c50448cea7ea8499657d_48.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_50.png)

## NFS网络附加存储 💾

![](img/8b61e8e6e261c50448cea7ea8499657d_52.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_54.png)

vsftpd主要用于文件传输共享，而NFS则更侧重于实现透明的网络文件系统挂载，常用于数据备份、共享存储等场景。

### NFS服务端部署

NFS的部署非常简单。首先在服务器端安装必要的软件包并启动服务。

![](img/8b61e8e6e261c50448cea7ea8499657d_56.png)

```bash
# 安装NFS服务器软件包
yum install -y nfs-utils

![](img/8b61e8e6e261c50448cea7ea8499657d_58.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_59.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_61.png)

# 启动NFS服务及其依赖的RPC服务
systemctl start nfs
systemctl enable nfs
```

![](img/8b61e8e6e261c50448cea7ea8499657d_63.png)

RPC（远程过程调用）服务是NFS正常工作所必需的，它负责协调客户端与服务器端的通信。

![](img/8b61e8e6e261c50448cea7ea8499657d_65.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_66.png)

### 配置共享目录

![](img/8b61e8e6e261c50448cea7ea8499657d_68.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_70.png)

NFS通过 `/etc/exports` 文件来定义共享目录、允许访问的客户端及权限。

1.  创建要共享的目录。
    ```bash
    mkdir /nfs_upload
    ```

![](img/8b61e8e6e261c50448cea7ea8499657d_72.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_73.png)

2.  编辑 `/etc/exports` 文件，添加共享规则。
    ```
    /nfs_upload 192.168.0.10(rw,sync,no_root_squash)
    ```
    *   `/nfs_upload`：要共享的本地目录。
    *   `192.168.0.10`：允许访问的客户端IP地址（可使用网段，如 `192.168.0.0/24`）。
    *   `rw`：授予读写权限。
    *   `sync`：同步写入，保证数据一致性。
    *   `no_root_squash`：客户端root用户访问时，不映射为匿名用户，保留root权限（谨慎使用）。

![](img/8b61e8e6e261c50448cea7ea8499657d_75.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_77.png)

3.  使配置生效。
    ```bash
    exportfs -arv
    ```

### NFS客户端挂载使用

在客户端机器上，可以查看服务器共享的目录列表，并将其挂载到本地目录使用。

![](img/8b61e8e6e261c50448cea7ea8499657d_79.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_80.png)

1.  安装NFS客户端工具。
    ```bash
    yum install -y nfs-utils
    ```

2.  查看服务器端共享的目录。
    ```bash
    showmount -e 192.168.0.40
    ```

3.  在本地创建挂载点，并挂载NFS共享。
    ```bash
    mkdir /mnt/nfs_client
    mount -t nfs 192.168.0.40:/nfs_upload /mnt/nfs_client
    ```
    挂载成功后，对 `/mnt/nfs_client` 的操作实际上是在读写服务器端的 `/nfs_upload` 目录。

4.  配置开机自动挂载。
    编辑 `/etc/fstab` 文件，添加一行：
    ```
    192.168.0.40:/nfs_upload /mnt/nfs_client nfs defaults,_netdev 0 0
    ```
    `_netdev` 参数表明这是一个网络设备，防止因网络未就绪导致系统启动失败。

![](img/8b61e8e6e261c50448cea7ea8499657d_82.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_84.png)

### 权限与用户映射

NFS服务端默认会将客户端的root用户映射为服务器上的 `nfsnobody` 用户（权限压缩）。若需要客户端root拥有完全权限，需在共享配置中使用 `no_root_squash` 选项。

![](img/8b61e8e6e261c50448cea7ea8499657d_86.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_88.png)

![](img/8b61e8e6e261c50448cea7ea8499657d_90.png)

对于普通用户，NFS基于UID/GID进行权限匹配。确保客户端用户和服务端相应文件的属主UID一致，才能获得正确的访问权限。一种简单的通用方法是适当放宽共享目录的系统权限（如 `chmod 777`），但这会降低安全性。生产环境中更推荐规划好统一的用户UID或使用其他权限管理方式。

## 总结 📚

本节课中我们一起学习了：
1.  **vsftpd本地用户模式**：通过禁用匿名访问、创建本地用户、配置 `chroot` 禁锢和写权限控制，实现了更安全的FTP访问管理。
2.  **NFS网络文件系统**：掌握了NFS服务端共享目录的配置与发布，以及客户端如何挂载使用网络存储。理解了NFS在数据备份和共享存储场景下的便捷性，并了解了基本的权限映射原理。

![](img/8b61e8e6e261c50448cea7ea8499657d_92.png)

这两种技术是构建企业文件共享和网络存储解决方案的基础组件。vsftpd更适合于文件上传下载管理，而NFS则提供了近乎本地磁盘的透明访问体验，适用于需要高性能共享存储的环境。