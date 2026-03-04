# Linux运维全套培训课程：P59：vsftpd本地用户访问模式与NFS网络附加存储 🖥️

![](img/ecb9cd9e3787de22cff4026477ebb6d6_1.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_3.png)

在本节课中，我们将学习vsftpd服务的本地用户访问模式配置，以及NFS网络文件系统的基本部署与使用。这两种技术都是实现文件共享与网络存储的重要方式。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_5.png)

## vsftpd本地用户访问模式 🔐

![](img/ecb9cd9e3787de22cff4026477ebb6d6_7.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_9.png)

上一节我们介绍了vsftpd的匿名用户访问模式。本节中我们来看看如何配置和使用本地用户模式。本地用户模式要求用户使用服务器上存在的系统账号和密码进行登录，访问控制更为严格。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_11.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_13.png)

### 禁用匿名用户访问

要启用本地用户模式，通常需要先禁用匿名用户访问。这通过修改vsftpd的主配置文件实现。

1.  打开vsftpd配置文件：
    ```bash
    vim /etc/vsftpd/vsftpd.conf
    ```
2.  找到 `anonymous_enable` 参数，将其值改为 `NO`。**注意**：直接注释该行默认是允许匿名访问的，因此必须显式设置为 `NO`。
    ```bash
    anonymous_enable=NO
    ```
3.  保存文件并重启vsftpd服务使配置生效：
    ```bash
    systemctl restart vsftpd
    ```

![](img/ecb9cd9e3787de22cff4026477ebb6d6_15.png)

完成此操作后，客户端将无法再使用匿名方式连接FTP服务器。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_17.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_19.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_20.png)

### 创建本地用户并测试访问

以下是创建本地用户并测试FTP访问的步骤。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_22.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_24.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_26.png)

1.  在服务器端创建一个用于FTP访问的本地用户（例如 `ftpuser`）：
    ```bash
    useradd ftpuser
    # 为用户设置密码
    passwd ftpuser
    ```
2.  在客户端使用该用户名和密码进行连接：
    ```bash
    lftp ftpuser@192.168.0.40
    # 或
    lftp 192.168.0.40 -u ftpuser
    ```
    连接成功后，用户会进入其在服务器上的家目录（如 `/home/ftpuser`）。初始时该目录为空，因此 `ls` 命令看不到文件。
3.  在服务器的用户家目录创建文件后，客户端即可看到并下载：
    ```bash
    # 在服务器端
    touch /home/ftpuser/hello.txt
    # 在客户端连接后
    get hello.txt
    ```

![](img/ecb9cd9e3787de22cff4026477ebb6d6_28.png)

### 配置本地用户权限

默认情况下，本地用户登录后拥有对其家目录的完整读写权限（如上传、删除、重命名文件）。这由配置文件中的以下参数控制：

*   `local_enable=YES`：允许本地用户登录（默认已启用）。
*   `write_enable=YES`：允许本地用户执行写入操作（如上传、删除）。若注释或设为 `NO`，则用户只能下载和查看。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_30.png)

### 限制本地用户的活动范围（禁锢家目录）

![](img/ecb9cd9e3787de22cff4026477ebb6d6_32.png)

出于安全考虑，我们通常不希望FTP用户能够切换到服务器上的其他目录。vsftpd提供了 `chroot_local_user` 参数来将用户的活动范围禁锢在其家目录内。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_34.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_36.png)

1.  在配置文件中取消 `chroot_local_user` 的注释，并将其值设为 `YES`：
    ```bash
    chroot_local_user=YES
    ```
2.  保存并重启服务后，用户将无法切换到其家目录以外的路径。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_38.png)

**重要注意事项**：启用 `chroot_local_user` 后，**用户家目录本身不能拥有写权限（W）**，否则连接会失败。这是vsftpd的一个安全限制。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_40.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_42.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_44.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_46.png)

以下是推荐的目录权限设置方案：

![](img/ecb9cd9e3787de22cff4026477ebb6d6_48.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_50.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_52.png)

1.  移除用户对其家目录的写权限：
    ```bash
    chmod -w /home/ftpuser
    ```
2.  在家目录下创建一个子目录（如 `pub` 或 `upload`）用于实际的文件上传下载，并将该目录的所有者改为FTP用户：
    ```bash
    mkdir /home/ftpuser/pub
    chown ftpuser:ftpuser /home/ftpuser/pub
    ```
3.  用户登录后进入家目录，然后可以 `cd pub` 进入该子目录进行所有读写操作。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_54.png)

### 用户访问控制列表

vsftpd通过两个文件实现用户访问控制，但通常无需额外配置。

*   `/etc/vsftpd/ftpusers`：**黑名单文件**。列在此文件中的用户禁止登录FTP。默认已包含root等系统账号。
*   `/etc/vsftpd/user_list`：此文件的角色（黑名单或白名单）由主配置文件中的 `userlist_deny` 参数决定。
    *   `userlist_deny=YES`（默认）：此文件为黑名单。
    *   `userlist_deny=NO`：此文件为白名单，仅文件中的用户允许登录。
    由于已有 `ftpusers` 作为黑名单，通常无需修改 `user_list` 的配置。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_56.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_58.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_60.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_62.png)

## NFS网络附加存储 📁

![](img/ecb9cd9e3787de22cff4026477ebb6d6_64.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_66.png)

接下来我们学习NFS。NFS也是一种文件共享服务，其特点是客户端可以将服务器共享的目录**挂载到本地文件系统**，像使用本地目录一样使用它。这在数据备份、集中存储等场景中非常方便。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_68.png)

### NFS服务端配置

![](img/ecb9cd9e3787de22cff4026477ebb6d6_70.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_72.png)

以下是搭建NFS服务器的基本步骤。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_74.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_76.png)

1.  安装NFS软件包：
    ```bash
    yum install -y nfs-utils
    ```
2.  启动NFS服务及其依赖的RPC服务：
    ```bash
    systemctl start nfs
    systemctl start rpcbind # 通常启动nfs时会自动启动
    systemctl enable nfs
    ```
3.  创建要共享的目录：
    ```bash
    mkdir /nfs_upload
    ```
4.  编辑NFS配置文件 `/etc/exports`，定义共享目录和访问权限：
    ```bash
    /nfs_upload 192.168.0.10(rw)
    ```
    这表示将 `/nfs_upload` 目录以读写（`rw`）权限共享给IP为 `192.168.0.10` 的客户端。
5.  使共享配置生效：
    ```bash
    exportfs -arv
    # 或重启nfs服务
    systemctl restart nfs
    ```

![](img/ecb9cd9e3787de22cff4026477ebb6d6_78.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_80.png)

### NFS客户端配置

客户端需要挂载NFS共享目录才能使用。

1.  安装NFS客户端软件包（通常系统已自带）：
    ```bash
    yum install -y nfs-utils
    ```
2.  查看NFS服务器共享了哪些目录：
    ```bash
    showmount -e 192.168.0.40
    ```
3.  在本地创建一个挂载点目录：
    ```bash
    mkdir /mnt/nfs_upload
    ```
4.  将远程NFS目录挂载到本地挂载点：
    ```bash
    mount -t nfs 192.168.0.40:/nfs_upload /mnt/nfs_upload
    ```
5.  使用 `df -h` 命令可以查看挂载情况。现在，对 `/mnt/nfs_upload` 的任何操作，实际上都是在访问NFS服务器上的 `/nfs_upload` 目录。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_82.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_84.png)

### 配置开机自动挂载与权限问题

1.  **开机自动挂载**：编辑 `/etc/fstab` 文件，添加一行。
    ```bash
    192.168.0.40:/nfs_upload /mnt/nfs_upload nfs defaults,_netdev 0 0
    ```
    `_netdev` 参数指明这是网络设备，防止因网络或服务器未就绪导致系统无法启动。
2.  **权限问题**：默认情况下，NFS服务器会以 `nfsnobody` 用户的身份来处理客户端请求。如果客户端用root用户写入文件遇到权限问题，可以在服务器端的 `/etc/exports` 配置中添加 `no_root_squash` 选项。
    ```bash
    /nfs_upload 192.168.0.10(rw,no_root_squash)
    ```
    这表示允许客户端的root用户保持root权限。或者，也可以简单地将共享目录权限设置为 `777`（`chmod 777 /nfs_upload`），但安全性较低。

### 存储模型简介

![](img/ecb9cd9e3787de22cff4026477ebb6d6_86.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_88.png)

最后，我们简单了解三种常见的存储模型：
*   **DAS**：直连式存储，如服务器内置硬盘、直接连接的USB移动硬盘。
*   **NAS**：网络附加存储，如本节学习的NFS和FTP。特点是提供**文件级**共享。
*   **SAN**：存储区域网络，如iSCSI。特点是提供**块级**共享，客户端可以将远程硬盘当作本地硬盘直接分区格式化。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_90.png)

![](img/ecb9cd9e3787de22cff4026477ebb6d6_92.png)

NFS和FTP都是实现NAS存储的具体技术方案。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_94.png)

## 总结 📝

本节课中我们一起学习了vsftpd的本地用户访问模式和NFS网络文件系统。
*   对于**vsftpd本地用户模式**，我们掌握了如何禁用匿名访问、创建本地用户、控制用户读写权限，以及通过 `chroot_local_user` 将用户禁锢在家目录等重要安全配置。
*   对于**NFS**，我们学习了服务端共享目录的配置、客户端挂载使用的方法，并了解了处理挂载权限和实现开机自动挂载的技巧。

![](img/ecb9cd9e3787de22cff4026477ebb6d6_96.png)

这两种服务是Linux系统中实现文件共享与网络存储的基石，理解其配置与原理对运维工作至关重要。