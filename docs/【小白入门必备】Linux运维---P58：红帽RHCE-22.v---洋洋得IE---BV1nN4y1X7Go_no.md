# Linux运维进阶：P58：vsftpd本地用户访问模式与NFS网络附加存储 🚀

![](img/dd267bbe033da79addf97a1ad9a71767_1.png)

![](img/dd267bbe033da79addf97a1ad9a71767_3.png)

![](img/dd267bbe033da79addf97a1ad9a71767_5.png)

在本节课中，我们将学习vsftpd服务的本地用户访问模式配置，以及NFS网络附加存储服务的基本部署与使用。这两种技术都是企业环境中常见的文件共享与备份解决方案。

![](img/dd267bbe033da79addf97a1ad9a71767_7.png)

![](img/dd267bbe033da79addf97a1ad9a71767_9.png)

![](img/dd267bbe033da79addf97a1ad9a71767_11.png)

## 禁用匿名用户访问 🔒

![](img/dd267bbe033da79addf97a1ad9a71767_13.png)

上一节我们介绍了vsftpd的匿名用户模式。本节中，我们来看看如何禁用匿名访问，转而使用更安全的本地用户模式。

要禁用匿名用户访问，需要修改vsftpd的主配置文件 `/etc/vsftpd/vsftpd.conf`。

![](img/dd267bbe033da79addf97a1ad9a71767_15.png)

![](img/dd267bbe033da79addf97a1ad9a71767_17.png)

![](img/dd267bbe033da79addf97a1ad9a71767_19.png)

**核心配置**：
```bash
anonymous_enable=NO
```
将 `anonymous_enable` 参数的值改为 `NO`。注意，注释掉该行默认是允许匿名访问的，因此必须显式设置为 `NO`。修改后需要重启vsftpd服务使配置生效。

![](img/dd267bbe033da79addf97a1ad9a71767_20.png)

## 配置本地用户访问 👤

![](img/dd267bbe033da79addf97a1ad9a71767_22.png)

![](img/dd267bbe033da79addf97a1ad9a71767_24.png)

![](img/dd267bbe033da79addf97a1ad9a71767_26.png)

![](img/dd267bbe033da79addf97a1ad9a71767_28.png)

禁用匿名访问后，我们可以使用系统本地用户账号登录FTP服务器。

以下是配置本地用户访问的基本步骤：
1.  **创建本地用户**：在服务器上创建一个用于FTP访问的系统用户。
    ```bash
    useradd ftpuser
    passwd ftpuser
    ```
2.  **客户端连接**：客户端使用 `lftp` 或 `ftp` 命令，指定用户名和服务器IP地址进行连接。
    ```bash
    lftp ftpuser@192.168.0.40
    ```
3.  **访问目录**：本地用户登录后，默认会进入该用户在服务器上的家目录（如 `/home/ftpuser`）。所有共享文件都应放置于此目录下。

## 控制本地用户权限与活动范围 🧭

![](img/dd267bbe033da79addf97a1ad9a71767_30.png)

![](img/dd267bbe033da79addf97a1ad9a71767_32.png)

默认情况下，本地用户权限较大，且可以在服务器文件系统中随意切换目录，这存在安全风险。我们需要对其进行控制。

![](img/dd267bbe033da79addf97a1ad9a71767_34.png)

![](img/dd267bbe033da79addf97a1ad9a71767_36.png)

**核心配置参数**：
*   **限制目录切换**：`chroot_local_user=YES`
    *   启用后，用户将被限制在自己的家目录内，无法切换到其他目录。
*   **管理写权限**：`write_enable=YES`
    *   此参数控制本地用户是否拥有上传、删除、重命名等写入权限。若设为 `NO`，则用户只能下载和查看。

![](img/dd267bbe033da79addf97a1ad9a71767_38.png)

![](img/dd267bbe033da79addf97a1ad9a71767_40.png)

![](img/dd267bbe033da79addf97a1ad9a71767_42.png)

![](img/dd267bbe033da79addf97a1ad9a71767_44.png)

**重要注意事项**：
一旦启用 `chroot_local_user=YES`，用户家目录本身**不能**拥有写权限（`w`），否则连接会失败。这是vsftpd的一个安全限制。
```bash
chmod a-w /home/ftpuser
```
通常的实践是：在家目录下创建一个子目录（如 `pub` 或 `upload`），将该子目录的所有者改为FTP用户，并赋予相应权限。用户的实际文件操作应在这个子目录中进行。
```bash
mkdir /home/ftpuser/upload
chown ftpuser:ftpuser /home/ftpuser/upload
```

![](img/dd267bbe033da79addf97a1ad9a71767_46.png)

![](img/dd267bbe033da79addf97a1ad9a71767_48.png)

![](img/dd267bbe033da79addf97a1ad9a71767_50.png)

![](img/dd267bbe033da79addf97a1ad9a71767_52.png)

## 用户访问控制名单 📜

![](img/dd267bbe033da79addf97a1ad9a71767_54.png)

vsftpd 提供了用户名单机制来进一步控制访问。

以下是相关的配置文件：
*   **黑名单**：`/etc/vsftpd/ftpusers`
    *   默认文件已包含 `root` 等系统账号，禁止它们通过FTP登录。一般无需修改。
*   **灵活名单**：`/etc/vsftpd/user_list`
    *   此文件可兼作黑名单或白名单，具体角色由主配置文件中的参数决定：
        *   `userlist_deny=YES` (默认)：此文件为**黑名单**。
        *   `userlist_deny=NO`：此文件为**白名单**，仅允许文件内的用户登录。
    *   该功能通常使用默认配置即可。

![](img/dd267bbe033da79addf97a1ad9a71767_56.png)

![](img/dd267bbe033da79addf97a1ad9a71767_58.png)

![](img/dd267bbe033da79addf97a1ad9a71767_60.png)

![](img/dd267bbe033da79addf97a1ad9a71767_62.png)

## 部署NFS网络附加存储 💾

![](img/dd267bbe033da79addf97a1ad9a71767_64.png)

![](img/dd267bbe033da79addf97a1ad9a71767_66.png)

![](img/dd267bbe033da79addf97a1ad9a71767_68.png)

NFS（Network File System）是另一种文件共享服务，在数据备份场景中应用广泛。其特点是客户端可以将远程共享目录直接挂载到本地使用，如同本地磁盘一样。

![](img/dd267bbe033da79addf97a1ad9a71767_70.png)

![](img/dd267bbe033da79addf97a1ad9a71767_72.png)

以下是部署NFS服务的基本流程：
1.  **安装软件包**：在服务器端安装NFS相关软件。
    ```bash
    yum install -y nfs-utils
    ```
2.  **创建并共享目录**：
    ```bash
    mkdir /nfs_upload
    ```
    编辑配置文件 `/etc/exports`，指定共享目录和访问权限。
    **配置示例**：
    ```bash
    /nfs_upload 192.168.0.10(rw,no_root_squash)
    ```
    *   `/nfs_upload`：要共享的本地目录。
    *   `192.168.0.10`：允许访问的客户端IP地址。
    *   `rw`：授予读写权限。
    *   `no_root_squash`：允许客户端root用户保持权限，重要参数。
3.  **启动NFS服务**：
    ```bash
    systemctl start nfs
    systemctl enable nfs
    ```

![](img/dd267bbe033da79addf97a1ad9a71767_74.png)

![](img/dd267bbe033da79addf97a1ad9a71767_76.png)

![](img/dd267bbe033da79addf97a1ad9a71767_78.png)

![](img/dd267bbe033da79addf97a1ad9a71767_80.png)

## 客户端挂载使用NFS 🖥️

在客户端机器上，可以挂载NFS服务器共享的目录。

![](img/dd267bbe033da79addf97a1ad9a71767_82.png)

![](img/dd267bbe033da79addf97a1ad9a71767_84.png)

以下是客户端操作步骤：
1.  **安装工具包**（如需）：
    ```bash
    yum install -y nfs-utils
    ```
2.  **查看共享**：查看NFS服务器提供了哪些共享目录。
    ```bash
    showmount -e 192.168.0.40
    ```
3.  **挂载目录**：将远程目录挂载到本地的一个空目录。
    ```bash
    mkdir /mnt/nfs_upload
    mount -t nfs 192.168.0.40:/nfs_upload /mnt/nfs_upload
    ```
4.  **开机自动挂载**：编辑 `/etc/fstab` 文件实现开机自动挂载。
    ```bash
    192.168.0.40:/nfs_upload /mnt/nfs_upload nfs defaults,_netdev 0 0
    ```
    *   `_netdev` 参数指明这是网络设备，防止因网络未就绪导致系统启动失败。

挂载成功后，客户端向 `/mnt/nfs_upload` 目录写入数据，实际占用的是NFS服务器的存储空间。

![](img/dd267bbe033da79addf97a1ad9a71767_86.png)

![](img/dd267bbe033da79addf97a1ad9a71767_88.png)

## 存储模型简介 🗂️

![](img/dd267bbe033da79addf97a1ad9a71767_90.png)

![](img/dd267bbe033da79addf97a1ad9a71767_92.png)

最后，我们简单了解三种常见的存储模型：
*   **DAS (直连存储)**：存储设备直接连接到服务器，如内置硬盘、USB移动硬盘。
*   **NAS (网络附加存储)**：存储设备通过网络共享，提供**文件级**访问。本节课学习的FTP和NFS就是实现NAS的典型服务。
*   **SAN (存储区域网络)**：提供**块级**存储访问，将远程硬盘像本地硬盘一样使用，性能更高。常见技术有iSCSI、FC等。

![](img/dd267bbe033da79addf97a1ad9a71767_94.png)

---

![](img/dd267bbe033da79addf97a1ad9a71767_96.png)

本节课中我们一起学习了如何配置vsftpd的本地用户模式以提升安全性，包括禁用匿名访问、控制用户权限和活动范围。同时，我们也掌握了NFS网络附加存储服务的快速部署与客户端挂载方法，理解了其在数据备份中的便利性。通过对比，我们还简要了解了DAS、NAS、SAN三种主流的存储模型。