# Linux最全RHCSA+RHCE培训教程合集，小白入门必备！ - P59：红帽RHCE-22.vsftpd本地用户访问模式、NFS网络附加存储

![](img/bea0e13435e183ac3942fdeafc319427_1.png)

## 概述 📚
在本节课中，我们将学习vsftpd服务的本地用户访问模式配置，以及NFS网络附加存储服务的搭建与使用。我们将从禁用匿名访问开始，逐步配置本地用户的权限控制，最后学习如何通过NFS实现跨主机的文件共享与备份。

![](img/bea0e13435e183ac3942fdeafc319427_3.png)

---

![](img/bea0e13435e183ac3942fdeafc319427_5.png)

## 禁用匿名用户访问模式 🔒
上一节我们介绍了vsftpd的匿名用户访问模式。本节中，我们来看看如何禁用匿名访问，转而使用本地用户模式。

某些企业环境可能认为匿名访问过于宽泛，需要限制为仅允许特定用户访问。此时，需要禁用匿名用户模式。

![](img/bea0e13435e183ac3942fdeafc319427_7.png)

![](img/bea0e13435e183ac3942fdeafc319427_9.png)

禁用方法如下：编辑vsftpd的主配置文件 `/etc/vsftpd/vsftpd.conf`，找到控制匿名访问的参数 `anonymous_enable`，并将其值修改为 `NO`。

![](img/bea0e13435e183ac3942fdeafc319427_11.png)

**注意**：如果仅注释掉该行，默认值仍为允许匿名访问。因此，必须显式地将其设置为 `NO`。

![](img/bea0e13435e183ac3942fdeafc319427_13.png)

```bash
# 编辑配置文件
vi /etc/vsftpd/vsftpd.conf
# 找到并修改以下行
anonymous_enable=NO
```

修改后，重启vsftpd服务使配置生效。此时，客户端将无法再使用匿名方式连接服务器。

```bash
systemctl restart vsftpd
```

---

## 配置本地用户访问 👤
禁用匿名访问后，我们需要允许特定的本地用户登录FTP服务器。这需要先在服务器上创建对应的系统用户账号。

![](img/bea0e13435e183ac3942fdeafc319427_15.png)

![](img/bea0e13435e183ac3942fdeafc319427_17.png)

例如，创建一个名为 `ftpuser` 的用户：

![](img/bea0e13435e183ac3942fdeafc319427_19.png)

![](img/bea0e13435e183ac3942fdeafc319427_20.png)

```bash
useradd ftpuser
# 并为该用户设置密码
passwd ftpuser
```

客户端可以使用此用户名和密码进行连接：

```bash
lftp ftpuser@192.168.0.40
```

![](img/bea0e13435e183ac3942fdeafc319427_22.png)

![](img/bea0e13435e183ac3942fdeafc319427_24.png)

连接成功后，用户会进入其在服务器上的家目录（如 `/home/ftpuser`）。用户只能看到和操作自己家目录下的文件。若想共享文件给该用户，只需将文件放置在其家目录中。

![](img/bea0e13435e183ac3942fdeafc319427_26.png)

![](img/bea0e13435e183ac3942fdeafc319427_28.png)

默认情况下，本地用户对其家目录拥有完整的读写权限（包括上传、下载、创建、删除、重命名文件等）。

---

## 控制本地用户的活动范围 🗺️
默认的本地用户模式存在一个问题：用户登录后可以切换到服务器的任意目录，这存在安全风险。通常，我们希望将用户的活动范围限制在其家目录内。

在vsftpd配置中，可以通过 `chroot_local_user` 参数来实现“禁锢用户”（即改变其根目录视图，使其无法跳出指定目录）。

1.  编辑配置文件 `/etc/vsftpd/vsftpd.conf`，找到并取消 `chroot_local_user=YES` 这一行的注释。
2.  保存并重启服务。

```bash
# 在配置文件中启用用户禁锢
chroot_local_user=YES
```

![](img/bea0e13435e183ac3942fdeafc319427_30.png)

启用此功能后，用户将被限制在自己的家目录中，无法切换到其他目录。

![](img/bea0e13435e183ac3942fdeafc319427_32.png)

**重要注意事项**：一旦启用了 `chroot_local_user=YES`，用户的家目录**不能**拥有写权限（即 `w` 权限），否则连接会失败。这是vsftpd的一项安全限制。

![](img/bea0e13435e183ac3942fdeafc319427_34.png)

因此，需要先移除用户对其家目录的写权限：

![](img/bea0e13435e183ac3942fdeafc319427_36.png)

```bash
chmod u-w /home/ftpuser
```

然后，为了能让用户正常上传文件，可以在其家目录下创建一个拥有写权限的子目录（例如 `pub`），并将该目录的所有者改为相应用户：

![](img/bea0e13435e183ac3942fdeafc319427_38.png)

![](img/bea0e13435e183ac3942fdeafc319427_40.png)

```bash
mkdir /home/ftpuser/pub
chown ftpuser:ftpuser /home/ftpuser/pub
```

![](img/bea0e13435e183ac3942fdeafc319427_42.png)

![](img/bea0e13435e183ac3942fdeafc319427_44.png)

这样，用户登录后进入被禁锢的家目录，虽然家目录本身不可写，但可以进入 `pub` 目录进行文件上传、创建等操作。

![](img/bea0e13435e183ac3942fdeafc319427_46.png)

![](img/bea0e13435e183ac3942fdeafc319427_48.png)

---

![](img/bea0e13435e183ac3942fdeafc319427_50.png)

![](img/bea0e13435e183ac3942fdeafc319427_52.png)

## 控制本地用户的写权限 ✏️
在配置文件中，参数 `local_enable=YES` 用于启用本地用户登录。而参数 `write_enable=YES` 则控制本地用户是否拥有写权限（包括上传、删除、重命名等）。

![](img/bea0e13435e183ac3942fdeafc319427_54.png)

如果希望本地用户只能浏览和下载文件，而不能修改，可以将 `write_enable` 设置为 `NO` 或直接注释掉该行（默认值为 `NO`）。

```bash
# 禁用本地用户的写权限
# write_enable=YES
```

修改此配置后，用户将无法执行上传、创建目录、删除文件等操作，但下载功能不受影响。

---

![](img/bea0e13435e183ac3942fdeafc319427_56.png)

## 用户访问控制名单 📋
vsftpd 提供了用户名单机制来进一步控制访问。

![](img/bea0e13435e183ac3942fdeafc319427_58.png)

![](img/bea0e13435e183ac3942fdeafc319427_60.png)

*   **固定黑名单 (`/etc/vsftpd/ftpusers`)**：此文件中列出的用户**绝对禁止**通过FTP登录。默认已包含 `root` 等系统关键账户，通常无需修改。
*   **灵活名单 (`/etc/vsftpd/user_list`)**：此文件可以灵活配置为黑名单或白名单，具体行为由主配置文件中的两个参数决定：
    *   `userlist_enable=YES`：启用 `user_list` 文件的功能（默认已启用）。
    *   `userlist_deny=YES/NO`：
        *   当 `userlist_deny=YES`（默认值）时，`user_list` 文件作为**黑名单**，文件中的用户被拒绝登录。
        *   当 `userlist_deny=NO` 时，`user_list` 文件作为**白名单**，**仅允许**文件中的用户登录，其他用户全部拒绝。

![](img/bea0e13435e183ac3942fdeafc319427_62.png)

由于已有固定的 `ftpusers` 黑名单，`user_list` 的复杂配置在实际中较少使用，了解其机制即可。

![](img/bea0e13435e183ac3942fdeafc319427_64.png)

![](img/bea0e13435e183ac3942fdeafc319427_66.png)

---

![](img/bea0e13435e183ac3942fdeafc319427_68.png)

## 配置NFS网络附加存储 💾
上一节我们结束了vsftpd的学习。本节中，我们来看看另一种重要的文件共享服务：NFS。NFS（Network File System）常用于数据备份等场景，它允许将服务器上的目录共享给网络上的其他主机，其他主机可以像使用本地目录一样挂载和使用它。

![](img/bea0e13435e183ac3942fdeafc319427_70.png)

![](img/bea0e13435e183ac3942fdeafc319427_72.png)

### 安装与启动NFS服务
在提供共享的服务器上，需要安装 `nfs-utils` 软件包并启动服务。

```bash
# 安装NFS软件包
yum install -y nfs-utils
# 启动NFS服务并设置开机自启
systemctl start nfs
systemctl enable nfs
```

![](img/bea0e13435e183ac3942fdeafc319427_74.png)

NFS服务依赖于RPC（Remote Procedure Call）机制进行通信，安装 `nfs-utils` 时会自动安装 `rpcbind` 包并启动相关服务。

![](img/bea0e13435e183ac3942fdeafc319427_76.png)

![](img/bea0e13435e183ac3942fdeafc319427_78.png)

### 创建与共享目录
1.  在服务器上创建一个准备共享的目录：
    ```bash
    mkdir /nfs_upload
    ```
2.  编辑NFS的配置文件 `/etc/exports`，指定共享目录、允许访问的客户端及访问权限：
    ```bash
    # 格式：共享目录 客户端IP或网段(权限参数)
    vi /etc/exports
    # 添加如下行，允许192.168.0.10主机读写访问
    /nfs_upload 192.168.0.10(rw)
    ```
    常用的权限参数：
    *   `rw`：读写权限。
    *   `ro`：只读权限。
    *   `no_root_squash`：默认情况下，NFS服务器会将客户端 `root` 用户的请求映射为服务器上的匿名用户（如 `nfsnobody`），这可能导致权限不足。添加此参数后，客户端的 `root` 将保持 `root` 权限，操作时需谨慎。
3.  使共享配置生效：
    ```bash
    exportfs -arv
    # 或重启nfs服务
    systemctl restart nfs
    ```

![](img/bea0e13435e183ac3942fdeafc319427_80.png)

### 客户端挂载与使用NFS共享
在客户端机器上，需要先安装 `nfs-utils` 以获取挂载命令，然后查看服务器提供的共享列表并进行挂载。

```bash
# 1. 安装软件包（获取showmount, mount.nfs等命令）
yum install -y nfs-utils

# 2. 查看服务器192.168.0.40共享了哪些目录
showmount -e 192.168.0.40

# 3. 在本地创建一个挂载点目录
mkdir /nfs_upload

![](img/bea0e13435e183ac3942fdeafc319427_82.png)

![](img/bea0e13435e183ac3942fdeafc319427_84.png)

# 4. 将远程共享目录挂载到本地
mount -t nfs 192.168.0.40:/nfs_upload /nfs_upload
```

挂载成功后，使用 `df -h` 命令可以看到挂载的NFS共享。此后，在客户端 `/nfs_upload` 目录下的所有操作，实际上都是在读写服务器 `/nfs_upload` 目录下的数据，不占用客户端本地存储空间。

### 设置开机自动挂载
编辑客户端的 `/etc/fstab` 文件，添加挂载配置以实现开机自动挂载。

```bash
vi /etc/fstab
# 添加如下行
192.168.0.40:/nfs_upload /nfs_upload nfs defaults,_netdev 0 0
```

**参数 `_netdev` 非常重要**：它告知系统这是一个网络设备。如果系统启动时网络未就绪或NFS服务器不可达，系统会跳过此挂载而不是无限等待，避免导致系统无法正常启动。

![](img/bea0e13435e183ac3942fdeafc319427_86.png)

![](img/bea0e13435e183ac3942fdeafc319427_88.png)

### NFS权限问题处理
默认情况下，出于安全考虑，NFS服务器会“压榨”客户端 `root` 用户的权限（root squashing）。如果客户端需要以 `root` 身份在共享目录中拥有完整权限，需要在服务器端的 `/etc/exports` 文件中为共享目录添加 `no_root_squash` 参数。

```bash
# 在服务器配置中允许客户端root保持权限
/nfs_upload 192.168.0.10(rw,no_root_squash)
```

![](img/bea0e13435e183ac3942fdeafc319427_90.png)

对于普通用户，如果遇到权限问题，通常可以通过放宽共享目录在服务器本地的文件系统权限来解决（例如，临时设置为 `777`），但这并非最佳安全实践。在生产环境中，更推荐规划好用户和组，并配合严格的本地目录权限与NFS导出参数进行管理。

![](img/bea0e13435e183ac3942fdeafc319427_92.png)

![](img/bea0e13435e183ac3942fdeafc319427_94.png)

---

## 总结 🎯
本节课中我们一起学习了两个重要的网络文件服务。

首先，我们深入探讨了vsftpd的本地用户模式：从如何禁用匿名访问，到创建和配置本地用户账户；重点讲解了如何通过 `chroot_local_user` 禁锢用户活动范围，以及相关的目录权限注意事项；最后介绍了通过 `write_enable` 控制写权限和用户访问名单机制。

接着，我们学习了NFS网络附加存储服务：了解了其用于数据备份的常见场景；完成了从服务端安装、创建共享目录、编辑 `/etc/exports` 配置文件，到客户端查看共享、挂载使用以及配置开机自动挂载的完整流程；并探讨了 `no_root_squash` 和 `_netdev` 等关键参数的作用。

![](img/bea0e13435e183ac3942fdeafc319427_96.png)

通过学习，我们掌握了两种实现网络文件共享与存储的不同技术方案，它们都是构建企业IT基础设施的实用技能。