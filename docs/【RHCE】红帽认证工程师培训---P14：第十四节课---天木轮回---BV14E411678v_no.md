# RHCE红帽认证工程师培训课程：P14：文件共享与传输服务进阶

![](img/d4a9056f7357cfd6e8978fc179237963_1.png)

![](img/d4a9056f7357cfd6e8978fc179237963_2.png)

在本节课中，我们将深入学习文件传输与共享服务的进阶配置。上一节我们介绍了VSFTPD服务的基本概念和匿名访问模式，本节中我们将重点探讨VSFTPD的本地用户验证和虚拟用户验证模式，并介绍更简单的TFTP协议。此外，我们还将学习两种重要的网络文件共享服务：Samba和NFS，以及自动挂载服务autofs。

## 本地用户验证模式

上一节我们介绍了VSFTPD的匿名访问模式，本节中我们来看看本地用户验证模式。

顾名思义，本地用户验证模式允许使用Linux服务器本地的系统账户（如root或其他用户）登录FTP服务器。这种模式配置简单，安装服务后几乎无需额外配置即可使用。

以下是配置VSFTPD本地用户模式的基本步骤：

1.  **安装服务**：首先安装VSFTPD服务端和FTP客户端工具。
    ```bash
    yum install -y vsftpd ftp
    ```

2.  **配置主文件**：编辑VSFTPD的主配置文件 `/etc/vsftpd/vsftpd.conf`。通常，安装后默认已支持本地用户登录和写入权限。

3.  **处理用户黑名单**：VSFTPD默认通过 `/etc/vsftpd/user_list` 和 `/etc/vsftpd/ftpusers` 文件管理禁止登录的用户（黑名单）。若root等用户无法登录，需将其从这两个文件中移除。
    ```bash
    # 编辑黑名单文件，删除要允许登录的用户名
    vim /etc/vsftpd/user_list
    vim /etc/vsftpd/ftpusers
    ```

4.  **重启服务**：修改配置后，重启服务使配置生效，并设置为开机自启。
    ```bash
    systemctl restart vsftpd
    systemctl enable vsftpd
    ```

5.  **SELinux设置**：如果遇到权限问题，可能需要临时调整SELinux策略以允许FTP写入。
    ```bash
    setsebool -P ftpd_full_access on
    ```

配置完成后，即可使用 `ftp [服务器IP]` 命令，输入本地用户名和密码进行连接和文件操作。

## 虚拟用户验证模式

接下来，我们探讨VSFTPD中最复杂但安全性更高的虚拟用户验证模式。

虚拟用户并非服务器上真实的系统账户，而是专门为FTP服务创建的账户。即使密码被破解，攻击者也无法登录整个服务器，安全性更好。配置流程相对复杂，涉及创建用户数据库、PAM认证模块配置和用户权限独立设置。

以下是配置虚拟用户模式的核心步骤：

1.  **创建用户密码文件**：创建一个明文文件（如 `/etc/vsftpd/vuser.list`），奇数行为用户名，偶数行为对应密码。
    ```
    张三
    password1
    李四
    password2
    ```

2.  **生成加密的数据库文件**：使用 `db_load` 命令对明文文件进行哈希加密，生成数据库文件。
    ```bash
    db_load -T -t hash -f /etc/vsftpd/vuser.list /etc/vsftpd/vuser.db
    ```

![](img/d4a9056f7357cfd6e8978fc179237963_4.png)

![](img/d4a9056f7357cfd6e8978fc179237963_5.png)

3.  **创建本地映射用户**：创建一个用于映射所有虚拟用户的本地系统用户（如 `virtual_user`），并禁止其登录系统。
    ```bash
    useradd -d /var/ftproot -s /sbin/nologin virtual_user
    ```

4.  **配置PAM认证文件**：PAM（可插拔认证模块）是系统提供的认证接口。创建或修改PAM配置文件 `/etc/pam.d/vsftpd.vu`，指向刚才生成的数据库文件。
    ```
    auth required pam_userdb.so db=/etc/vsftpd/vuser
    account required pam_userdb.so db=/etc/vsftpd/vuser
    ```
    > **注意**：`db=` 参数后跟数据库文件的路径，但不包括 `.db` 后缀。

![](img/d4a9056f7357cfd6e8978fc179237963_7.png)

5.  **修改VSFTPD主配置**：编辑 `/etc/vsftpd/vsftpd.conf`，启用虚拟用户并指向PAM文件。
    ```
    anonymous_enable=NO
    local_enable=YES
    guest_enable=YES
    guest_username=virtual_user
    pam_service_name=vsftpd.vu
    user_config_dir=/etc/vsftpd/vusers.d
    ```

6.  **为虚拟用户配置独立权限**：创建目录 `/etc/vsftpd/vusers.d/`，并为每个虚拟用户创建独立的配置文件（如 `张三`、`李四`）。在配置文件中，可以独立设置是否允许写入、创建、删除文件等权限。虚拟用户的权限参数继承自匿名用户参数。
    ```bash
    # 例如 /etc/vsftpd/vusers.d/张三
    anon_upload_enable=YES
    anon_mkdir_write_enable=YES
    anon_other_write_enable=YES
    ```

![](img/d4a9056f7357cfd6e8978fc179237963_9.png)

![](img/d4a9056f7357cfd6e8978fc179237963_10.png)

配置完成后，重启VSFTPD服务，即可使用虚拟用户“张三”和“李四”进行登录，并且他们可以拥有不同的操作权限。

![](img/d4a9056f7357cfd6e8978fc179237963_12.png)

![](img/d4a9056f7357cfd6e8978fc179237963_14.png)

## 简单文件传输协议 (TFTP)

![](img/d4a9056f7357cfd6e8978fc179237963_16.png)

如果觉得VSFTPD配置过于复杂，或者只需要一个极其简单的文件下载方式，可以了解TFTP。

TFTP（Trivial File Transfer Protocol）是一种非常简单的文件传输协议，基于UDP 69端口。它没有用户认证过程，配置极其简单，常用于网络设备固件更新或获取启动文件等场景。

以下是配置和使用TFTP服务的步骤：

1.  **安装服务**：安装TFTP服务端和客户端。
    ```bash
    yum install -y tftp-server tftp
    ```

2.  **配置服务**：TFTP服务由xinetd超级守护进程管理。编辑其配置文件 `/etc/xinetd.d/tftp`，将 `disable = yes` 改为 `disable = no` 以启用服务。
    ```bash
    vim /etc/xinetd.d/tftp
    # 修改 disable = no
    ```

3.  **重启服务**：重启xinetd服务以加载TFTP。
    ```bash
    systemctl restart xinetd
    systemctl enable xinetd
    ```

![](img/d4a9056f7357cfd6e8978fc179237963_18.png)

4.  **使用TFTP**：默认共享目录为 `/var/lib/tftpboot/`。将文件放入此目录后，客户端即可使用 `tftp [服务器IP]` 连接，并通过 `get [文件名]` 命令下载文件，无需任何认证。

TFTP因其无认证、功能简单的特点，适用于特定内部环境，在使用时需要权衡便利性与安全性。

## Samba文件共享服务

从文件传输转向文件共享，我们首先学习Samba服务。

![](img/d4a9056f7357cfd6e8978fc179237963_20.png)

![](img/d4a9056f7357cfd6e8978fc179237963_21.png)

![](img/d4a9056f7357cfd6e8978fc179237963_22.png)

![](img/d4a9056f7357cfd6e8978fc179237963_23.png)

Samba基于SMB/CIFS协议，主要目的是实现Linux与Windows系统之间的文件共享。它的名字来源于其协议SMB，开发者为了注册商标而添加了元音字母“a”，并借鉴了热情洋溢的“桑巴舞”而得名。

![](img/d4a9056f7357cfd6e8978fc179237963_24.png)

![](img/d4a9056f7357cfd6e8978fc179237963_25.png)

![](img/d4a9056f7357cfd6e8978fc179237963_26.png)

![](img/d4a9056f7357cfd6e8978fc179237963_27.png)

![](img/d4a9056f7357cfd6e8978fc179237963_29.png)

![](img/d4a9056f7357cfd6e8978fc179237963_31.png)

以下是配置Samba服务实现共享的基本步骤：

![](img/d4a9056f7357cfd6e8978fc179237963_33.png)

1.  **安装服务**：安装Samba服务端和客户端工具。
    ```bash
    yum install -y samba samba-client
    ```

2.  **精简主配置**：Samba的主配置文件是 `/etc/samba/smb.conf`。初始文件注释很多，可以过滤掉注释和空行，得到一个简洁的版本。
    ```bash
    grep -v "#" /etc/samba/smb.conf | grep -v ";" | grep -v "^$" > /etc/samba/smb.conf
    ```

3.  **编写共享配置**：在 `smb.conf` 文件末尾添加自定义的共享段落。
    ```
    [shared_folder] # 共享名称，客户端可见
        comment = Important Files # 描述信息
        path = /srv/samba_share # 服务器上要共享的目录路径
        public = no # 不公开，需要认证
        writable = yes # 允许写入
        valid users = smbuser # 允许访问的用户
    ```

![](img/d4a9056f7357cfd6e8978fc179237963_35.png)

4.  **创建共享目录和用户**：创建共享目录，并设置权限。然后为Samba服务创建专属用户并设置密码（此密码独立于系统登录密码）。
    ```bash
    mkdir -p /srv/samba_share
    chmod -R 777 /srv/samba_share # 根据实际安全要求调整权限
    useradd smbuser
    smbpasswd -a smbuser # 设置Samba用户密码
    ```

![](img/d4a9056f7357cfd6e8978fc179237963_37.png)

![](img/d4a9056f7357cfd6e8978fc179237963_38.png)

5.  **处理SELinux和防火墙**：根据需要设置SELinux布尔值并放行防火墙端口（139, 445）。
    ```bash
    setsebool -P samba_export_all_rw on
    firewall-cmd --permanent --add-service=samba
    firewall-cmd --reload
    ```

![](img/d4a9056f7357cfd6e8978fc179237963_40.png)

![](img/d4a9056f7357cfd6e8978fc179237963_41.png)

![](img/d4a9056f7357cfd6e8978fc179237963_43.png)

6.  **重启服务**：
    ```bash
    systemctl restart smb
    systemctl enable smb
    ```

![](img/d4a9056f7357cfd6e8978fc179237963_45.png)

![](img/d4a9056f7357cfd6e8978fc179237963_47.png)

![](img/d4a9056f7357cfd6e8978fc179237963_48.png)

![](img/d4a9056f7357cfd6e8978fc179237963_49.png)

配置完成后，Windows客户端可以在文件资源管理器地址栏输入 `\\[服务器IP]` 进行访问。Linux客户端可以使用 `smbclient` 命令连接，或通过编辑 `/etc/fstab` 文件实现挂载。

![](img/d4a9056f7357cfd6e8978fc179237963_51.png)

![](img/d4a9056f7357cfd6e8978fc179237963_52.png)

## 网络文件系统 (NFS)

对于纯Linux环境下的文件共享，NFS是更原生、更简单的选择。

![](img/d4a9056f7357cfd6e8978fc179237963_54.png)

NFS（Network File System）允许将远程主机的目录挂载到本地，像使用本地磁盘一样操作文件。它在Linux/Unix环境中配置非常简洁。

以下是配置NFS服务的基本步骤：

![](img/d4a9056f7357cfd6e8978fc179237963_56.png)

1.  **安装服务**：NFS服务通常已默认安装。确保安装nfs-utils包。
    ```bash
    yum install -y nfs-utils
    ```

![](img/d4a9056f7357cfd6e8978fc179237963_58.png)

![](img/d4a9056f7357cfd6e8978fc179237963_59.png)

2.  **创建共享目录**：
    ```bash
    mkdir /nfs_share
    echo "NFS Test File" > /nfs_share/test.txt
    chmod -R 777 /nfs_share
    ```

3.  **配置共享导出**：编辑 `/etc/exports` 文件，定义共享目录、允许访问的客户端及权限。
    ```
    /nfs_share 192.168.10.0/24(rw,sync,root_squash)
    ```
    *   `/nfs_share`：要共享的目录。
    *   `192.168.10.0/24`：允许访问的网段。可以是单个IP、网段或 `*`（所有人）。
    *   `rw`：读写权限。
    *   `sync`：同步写入，数据安全性更高。
    *   `root_squash`：将远程root用户的权限映射为匿名用户，增强安全性。

4.  **启动服务并放行防火墙**：
    ```bash
    systemctl start nfs
    systemctl enable nfs
    firewall-cmd --permanent --add-service=nfs
    firewall-cmd --reload
    ```

5.  **客户端挂载**：在客户端查看共享并挂载。
    ```bash
    # 查看服务器共享列表
    showmount -e 192.168.10.1
    # 临时挂载
    mount -t nfs 192.168.10.1:/nfs_share /mnt/nfs_client
    # 永久挂载，编辑 /etc/fstab
    # 192.168.10.1:/nfs_share /mnt/nfs_client nfs defaults 0 0
    ```

![](img/d4a9056f7357cfd6e8978fc179237963_61.png)

NFS配置非常直观，一行配置即可完成共享定义。

![](img/d4a9056f7357cfd6e8978fc179237963_62.png)

![](img/d4a9056f7357cfd6e8978fc179237963_64.png)

## 自动挂载服务 (autofs)

![](img/d4a9056f7357cfd6e8978fc179237963_66.png)

![](img/d4a9056f7357cfd6e8978fc179237963_68.png)

最后，我们介绍autofs服务，它用于实现按需自动挂载。

autofs可以监控指定的挂载点，只有当用户真正访问该目录时，才自动挂载预先配置好的远程共享或本地设备（如光盘），访问结束后一段时间自动卸载，节省系统资源。

以下是配置autofs自动挂载光盘的示例：

1.  **安装服务**：
    ```bash
    yum install -y autofs
    ```

2.  **配置主映射文件**：编辑 `/etc/auto.master`，指定监控的顶级目录和对应的子配置文件。
    ```
    /misc /etc/auto.misc
    ```

![](img/d4a9056f7357cfd6e8978fc179237963_70.png)

3.  **配置子映射文件**：编辑 `/etc/auto.misc`，定义挂载细节。
    ```
    cdrom -fstype=iso9660,ro,nosuid :/dev/cdrom
    ```
    *   `cdrom`：在 `/misc` 下创建的子目录名。
    *   `-fstype=iso9660,ro,...`：文件系统类型和挂载选项。
    *   `:/dev/cdrom`：要挂载的设备。

4.  **重启服务**：
    ```bash
    systemctl restart autofs
    systemctl enable autofs
    ```

配置完成后，当用户访问 `/misc/cdrom` 时，系统会自动将光盘挂载到该目录；一段时间不访问后，会自动卸载。

---

![](img/d4a9056f7357cfd6e8978fc179237963_72.png)

![](img/d4a9056f7357cfd6e8978fc179237963_74.png)

本节课中我们一起学习了VSFTPD的本地用户和虚拟用户验证模式，认识了更简单的TFTP协议。然后，我们深入探讨了用于跨平台共享的Samba服务和用于Linux间共享的NFS服务，最后了解了能节省资源的自动挂载服务autofs。这些服务涵盖了从复杂安全验证到简单便捷共享的不同场景，是构建网络化文件服务环境的重要工具。