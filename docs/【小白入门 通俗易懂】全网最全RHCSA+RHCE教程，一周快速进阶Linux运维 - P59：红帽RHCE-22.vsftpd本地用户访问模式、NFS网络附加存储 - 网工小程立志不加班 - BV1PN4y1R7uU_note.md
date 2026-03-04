# Linux运维教程：P59：vsftpd本地用户访问模式与NFS网络附加存储 🚀

![](img/8427d2530f6dfc29af2cc3fea162d2c1_1.png)

在本节课中，我们将学习vsftpd服务的本地用户访问模式配置，以及NFS网络附加存储服务的搭建与使用。我们将从禁用匿名访问开始，逐步配置本地用户，并深入探讨权限控制。随后，我们将转向NFS，学习如何创建网络共享目录，实现跨主机的文件存储与备份。

![](img/8427d2530f6dfc29af2cc3fea162d2c1_3.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_5.png)

---

![](img/8427d2530f6dfc29af2cc3fea162d2c1_7.png)

## 禁用匿名用户访问 🔒

![](img/8427d2530f6dfc29af2cc3fea162d2c1_9.png)

上一节我们介绍了vsftpd的匿名用户模式。本节中，我们来看看如何禁用匿名访问，转而使用本地用户模式。

![](img/8427d2530f6dfc29af2cc3fea162d2c1_11.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_13.png)

某些企业环境可能认为匿名访问过于宽泛，需要限制为仅允许特定用户访问。这时就需要禁用匿名用户。

以下是禁用匿名用户的步骤：

1.  编辑vsftpd的主配置文件。
    ```bash
    vim /etc/vsftpd/vsftpd.conf
    ```
2.  找到控制匿名访问的参数 `anonymous_enable`。
3.  将其值修改为 `NO`。**注意**：如果只是注释掉该行，默认值仍然是允许匿名访问。
    ```bash
    anonymous_enable=NO
    ```
4.  保存配置文件并重启vsftpd服务使更改生效。
    ```bash
    systemctl restart vsftpd
    ```

![](img/8427d2530f6dfc29af2cc3fea162d2c1_15.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_17.png)

完成上述操作后，客户端将无法再使用匿名方式连接FTP服务器。

![](img/8427d2530f6dfc29af2cc3fea162d2c1_19.png)

---

![](img/8427d2530f6dfc29af2cc3fea162d2c1_21.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_23.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_24.png)

## 配置本地用户访问 👤

禁用匿名访问后，我们可以配置本地系统用户来访问FTP服务。

![](img/8427d2530f6dfc29af2cc3fea162d2c1_26.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_28.png)

首先，在服务器上创建一个用于FTP访问的本地用户。
```bash
useradd ftpuser
```
接下来，客户端可以使用此用户名和密码进行连接。
```bash
lftp ftpuser@192.168.0.40
```
输入正确的密码后即可登录。

![](img/8427d2530f6dfc29af2cc3fea162d2c1_30.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_32.png)

**重要提示**：本地用户登录后，默认会进入该用户在服务器上的家目录（如 `/home/ftpuser`）。用户只能看到和操作自己家目录下的文件。若希望为用户提供共享文件，只需将文件放置在其家目录中即可。

默认情况下，本地用户对自己的家目录拥有完整的读写权限（`rwx`），因此可以进行上传、下载、删除等操作。

---

## 控制本地用户的活动范围 🗺️

默认配置下，本地用户登录FTP后可以切换到服务器的其他目录，这存在安全风险。我们通常希望将用户的活动范围限制在其家目录内。

![](img/8427d2530f6dfc29af2cc3fea162d2c1_34.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_36.png)

这可以通过启用 **`chroot_local_user`** 参数来实现。该功能称为“切根”（chroot），即将用户的根目录限制在其家目录，使其无法访问上级目录。

![](img/8427d2530f6dfc29af2cc3fea162d2c1_38.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_40.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_42.png)

配置步骤如下：

![](img/8427d2530f6dfc29af2cc3fea162d2c1_44.png)

1.  编辑配置文件 `/etc/vsftpd/vsftpd.conf`。
2.  找到并取消 `chroot_local_user=YES` 这一行的注释。
    ```bash
    chroot_local_user=YES
    ```
3.  保存并重启服务。

![](img/8427d2530f6dfc29af2cc3fea162d2c1_46.png)

**关键注意事项**：一旦启用 `chroot_local_user`，用户的家目录**不能**拥有写权限（`w`），否则连接会失败。这是vsftpd的一项安全限制。

![](img/8427d2530f6dfc29af2cc3fea162d2c1_48.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_50.png)

因此，需要执行以下操作：
1.  移除用户对其家目录的写权限。
    ```bash
    chmod -w /home/ftpuser
    ```
2.  在家目录下创建一个新的子目录（例如 `public`）用于文件上传。
    ```bash
    mkdir /home/ftpuser/public
    ```
3.  将此子目录的所有者改为FTP用户，并赋予相应权限。
    ```bash
    chown ftpuser:ftpuser /home/ftpuser/public
    ```

![](img/8427d2530f6dfc29af2cc3fea162d2c1_52.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_54.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_56.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_58.png)

这样，用户登录后活动范围被限制在家目录，并且可以在 `public` 子目录中进行上传等写入操作。

![](img/8427d2530f6dfc29af2cc3fea162d2c1_60.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_62.png)

---

![](img/8427d2530f6dfc29af2cc3fea162d2c1_64.png)

## 管理本地用户的写权限 ✏️

在配置文件中，参数 **`local_enable=YES`** 用于启用本地用户登录，默认已开启。

参数 **`write_enable=YES`** 则控制本地用户是否拥有写权限（包括上传、删除、重命名）。如果希望用户只能下载和查看，不能修改，将此参数设置为 `NO` 或注释掉即可。
```bash
write_enable=NO
```

![](img/8427d2530f6dfc29af2cc3fea162d2c1_66.png)

---

![](img/8427d2530f6dfc29af2cc3fea162d2c1_68.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_70.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_72.png)

## 用户访问控制名单 📋

![](img/8427d2530f6dfc29af2cc3fea162d2c1_74.png)

vsftpd 提供了用户名单机制来进一步控制访问。

![](img/8427d2530f6dfc29af2cc3fea162d2c1_76.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_78.png)

*   **黑名单文件 (`/etc/vsftpd/ftpusers`)**：列在此文件中的用户**禁止**登录FTP服务。默认已包含 `root` 等系统账户。
*   **用户列表文件 (`/etc/vsftpd/user_list`)**：此文件可灵活配置为黑名单或白名单。
    *   其行为由 `/etc/vsftpd/vsftpd.conf` 中的两个参数共同决定：
        *   `userlist_enable=YES` （默认已开启）
        *   `userlist_deny=YES` （默认值）
    *   **`userlist_deny=YES`**：`user_list` 文件作为**黑名单**，其中的用户被拒绝登录。
    *   **`userlist_deny=NO`**：`user_list` 文件作为**白名单**，**仅允许**文件中的用户登录，其他用户全部拒绝。

![](img/8427d2530f6dfc29af2cc3fea162d2c1_80.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_82.png)

由于默认已有 `ftpusers` 作为黑名单，通常无需额外配置 `user_list` 文件。

---

![](img/8427d2530f6dfc29af2cc3fea162d2c1_84.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_86.png)

## 配置NFS网络附加存储 💾

![](img/8427d2530f6dfc29af2cc3fea162d2c1_88.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_90.png)

NFS（Network File System）是另一种常见的文件共享服务，常用于数据备份等场景。其特点是配置简单，客户端可以像挂载本地磁盘一样挂载远程目录。

### 服务端配置

1.  **安装软件包**：
    ```bash
    yum install -y nfs-utils
    ```
2.  **启动并启用服务**：
    ```bash
    systemctl start nfs
    systemctl enable nfs
    ```
3.  **创建共享目录**：
    ```bash
    mkdir /nfs_upload
    ```
4.  **配置共享权限**：编辑 `/etc/exports` 文件，指定共享目录和允许访问的客户端及权限。
    ```bash
    /nfs_upload 192.168.0.10(rw,sync,no_root_squash)
    ```
    *   `192.168.0.10`：允许访问的客户端IP。
    *   `rw`：读写权限。
    *   `sync`：同步写入。
    *   `no_root_squash`：不挤压root用户的权限，允许客户端root用户在共享目录中保持root权限（谨慎使用）。
5.  **重新加载配置**：
    ```bash
    exportfs -arv
    ```

![](img/8427d2530f6dfc29af2cc3fea162d2c1_92.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_94.png)

### 客户端配置

1.  **安装客户端工具**（如需）：
    ```bash
    yum install -y nfs-utils
    ```
2.  **查看服务器共享的目录**：
    ```bash
    showmount -e 192.168.0.40
    ```
3.  **创建本地挂载点**：
    ```bash
    mkdir /mnt/nfs_upload
    ```
4.  **挂载远程目录**：
    ```bash
    mount -t nfs 192.168.0.40:/nfs_upload /mnt/nfs_upload
    ```
5.  **实现开机自动挂载**：编辑 `/etc/fstab` 文件，添加一行。
    ```bash
    192.168.0.40:/nfs_upload /mnt/nfs_upload nfs defaults,_netdev 0 0
    ```
    *   `_netdev` 参数指明这是网络设备，若开机时网络不可用，系统会跳过此挂载，防止启动失败。

挂载成功后，客户端向 `/mnt/nfs_upload` 目录写入数据，实际占用的是NFS服务器端的存储空间。

---

![](img/8427d2530f6dfc29af2cc3fea162d2c1_96.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_98.png)

## 存储模型简介 🗂️

最后，我们简单了解三种主要的存储模型：

![](img/8427d2530f6dfc29af2cc3fea162d2c1_100.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_102.png)

![](img/8427d2530f6dfc29af2cc3fea162d2c1_104.png)

*   **DAS (Direct-Attached Storage)**：直连存储，如服务器内置硬盘、直接连接的USB移动硬盘。
*   **NAS (Network-Attached Storage)**：网络附加存储，如本节学习的 **NFS** 和 **FTP**。它们通过网络共享**文件或目录**。
*   **SAN (Storage Area Network)**：存储区域网络，如 iSCSI。它通过网络共享**块设备（整个硬盘）**，客户端可以像使用本地硬盘一样对其进行格式化、挂载。

vsftpd和NFS是实现NAS存储的两种典型技术方案。

---

![](img/8427d2530f6dfc29af2cc3fea162d2c1_106.png)

本节课中我们一起学习了如何将vsftpd从匿名模式切换到更安全的本地用户模式，并详细配置了用户禁锢和权限控制。随后，我们掌握了NFS网络文件系统的搭建与使用，了解了其作为便捷的网络备份方案的优势。最后，我们对主流的存储模型有了基本的认识。