# Linux服务管理：P8：NFS与FTP服务实战

![](img/6bf56b490ed48f4233b22cc221f5f4c4_1.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_3.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_5.png)

在本节课中，我们将深入学习两种重要的网络服务：FTP（文件传输协议）和NFS（网络文件系统）。我们将从FTP服务的安装、配置、用户管理以及SSL加密认证开始，然后过渡到NFS服务的部署、挂载与权限管理。通过本教程，你将掌握如何在Linux环境中搭建安全的文件共享服务。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_7.png)

---

![](img/6bf56b490ed48f4233b22cc221f5f4c4_9.png)

## FTP服务：基础理论与安装

![](img/6bf56b490ed48f4233b22cc221f5f4c4_11.png)

上一节我们介绍了DNS服务的智能解析，本节中我们来看看如何搭建FTP服务。FTP（File Transfer Protocol）是一种用于在网络上进行文件传输的标准协议。它基于客户端-服务器模型，默认使用TCP协议的21端口传输控制指令，20端口传输数据。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_13.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_15.png)

FTP支持两种连接模式：
*   **主动模式**：服务器主动使用20端口连接客户端的高位随机端口传输数据。
*   **被动模式**：服务器开放一个高位随机端口，等待客户端连接以传输数据。**被动模式是默认且更常用的方式**，因为它能更好地穿越防火墙。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_17.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_19.png)

在Linux中，我们使用`vsftpd`（Very Secure FTP Daemon）软件来提供FTP服务。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_21.png)

以下是安装步骤：
1.  安装服务端软件：`yum install -y vsftpd`
2.  安装客户端软件（可选，用于测试）：`yum install -y lftp`
3.  启动服务并设置开机自启：
    ```bash
    systemctl start vsftpd
    systemctl enable vsftpd
    ```
4.  验证服务是否启动，应看到21端口在监听：
    ```bash
    netstat -lnt | grep 21
    ```

![](img/6bf56b490ed48f4233b22cc221f5f4c4_23.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_25.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_27.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_29.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_31.png)

---

![](img/6bf56b490ed48f4233b22cc221f5f4c4_33.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_35.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_37.png)

## FTP服务：匿名访问配置

![](img/6bf56b490ed48f4233b22cc221f5f4c4_39.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_41.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_43.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_45.png)

FTP支持多种用户类型登录，包括匿名用户、本地系统用户和虚拟用户。我们先从最简单的匿名访问开始配置。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_47.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_49.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_51.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_53.png)

匿名用户的用户名为 `anonymous`，密码为空。其默认的共享目录是 `/var/ftp/pub`。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_55.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_57.png)

以下是配置匿名用户可上传文件的步骤：
1.  编辑主配置文件 `/etc/vsftpd/vsftpd.conf`，确保以下参数已设置：
    ```bash
    anonymous_enable=YES          # 允许匿名登录
    anon_upload_enable=YES        # 允许匿名用户上传
    anon_mkdir_write_enable=YES   # 允许匿名用户创建目录
    anon_other_write_enable=YES   # 允许匿名用户其他写入权限（如删除、重命名）
    ```
2.  为匿名用户的共享目录设置正确的所有权和权限。FTP服务进程默认以`ftp`用户身份运行，因此需要将目录所有者改为`ftp`，并赋予写入权限。
    ```bash
    chown ftp:ftp /var/ftp/pub
    chmod 755 /var/ftp/pub  # 或 chmod o+w /var/ftp/pub 仅给其他人写权限
    ```
3.  重启服务使配置生效：`systemctl restart vsftpd`

![](img/6bf56b490ed48f4233b22cc221f5f4c4_59.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_61.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_63.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_65.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_67.png)

配置完成后，即可使用FTP客户端（如FileZilla）以`anonymous`用户（无需密码）连接服务器，并尝试在`/var/ftp/pub`目录下上传文件。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_69.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_71.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_73.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_75.png)

---

![](img/6bf56b490ed48f4233b22cc221f5f4c4_77.png)

## FTP服务：本地用户与禁锢（Chroot）机制

![](img/6bf56b490ed48f4233b22cc221f5f4c4_79.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_81.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_83.png)

在企业环境中，我们更常使用本地系统用户登录FTP，但为了系统安全，需要限制用户只能访问其家目录，这称为“禁锢”（Chroot）机制。例如，为Web开发人员创建仅能访问网站目录的FTP账户。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_85.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_87.png)

以下是实现步骤：
1.  创建两个系统用户（如`team1`， `team2`），并设置密码。这些用户**不应该拥有本地Shell登录权限**（可将其Shell设置为`/sbin/nologin`）。
    ```bash
    useradd team1 -s /sbin/nologin
    echo “123456” | passwd --stdin team1
    ```
2.  编辑主配置文件 `/etc/vsftpd/vsftpd.conf`，进行如下关键配置：
    ```bash
    anonymous_enable=NO                # 禁止匿名登录
    local_enable=YES                   # 允许本地用户登录
    chroot_local_user=YES              # 启用禁锢机制，将所有本地用户限制在其家目录
    # 或者，使用用户列表进行更精细的控制
    # chroot_local_user=NO
    # chroot_list_enable=YES
    # chroot_list_file=/etc/vsftpd/chroot_list  # 指定被禁锢的用户列表文件
    allow_writeable_chroot=YES         # 允许被禁锢的用户对根目录有写权限
    ```
3.  如果使用用户列表方式，创建并编辑`/etc/vsftpd/chroot_list`文件，每行写入一个需要被禁锢的用户名（如`team1`）。
4.  设置网站目录（如`/var/www/html`）的权限，确保FTP用户（或其所属组）有读写权限。
    ```bash
    chmod o+w /var/www/html  # 为“其他人”添加写权限
    # 或者更安全地更改所有者
    # chown team1:team1 /var/www/html
    ```
5.  重启服务：`systemctl restart vsftpd`

![](img/6bf56b490ed48f4233b22cc221f5f4c4_89.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_91.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_93.png)

现在，用户`team1`可以通过FTP客户端登录，并且其根目录将被锁定在`/var/www/html`（或系统指定的其他目录），无法访问系统的其他部分。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_95.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_97.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_99.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_101.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_103.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_104.png)

---

![](img/6bf56b490ed48f4233b22cc221f5f4c4_106.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_108.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_109.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_111.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_113.png)

## FTP服务：SSL/TLS加密传输

![](img/6bf56b490ed48f4233b22cc221f5f4c4_115.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_116.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_118.png)

在公网环境下，明文传输的FTP存在安全风险。我们需要为`vsftpd`配置SSL/TLS加密，实现FTPS（FTP over SSL）。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_120.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_122.png)

以下是配置SSL加密的步骤：
1.  使用OpenSSL生成自签名证书和密钥（有效期为3650天）：
    ```bash
    openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
    -keyout /etc/vsftpd/vsftpd.pem \
    -out /etc/vsftpd/vsftpd.pem
    ```
    在生成过程中，需要填写国家、地区、组织等信息。
2.  设置证书文件严格的权限：
    ```bash
    chmod 400 /etc/vsftpd/vsftpd.pem
    ```
3.  编辑主配置文件 `/etc/vsftpd/vsftpd.conf`，在文件末尾添加SSL相关配置：
    ```bash
    # 启用SSL
    ssl_enable=YES
    # 指定证书和密钥文件（由于我们合并生成，路径相同）
    rsa_cert_file=/etc/vsftpd/vsftpd.pem
    rsa_private_key_file=/etc/vsftpd/vsftpd.pem
    # 强制所有用户使用SSL进行数据和登录传输
    force_local_data_ssl=YES
    force_local_logins_ssl=YES
    # 设置SSL协议版本和加密算法
    ssl_tlsv1=YES
    ssl_sslv2=NO
    ssl_sslv3=NO
    ```
4.  重启服务：`systemctl restart vsftpd`

![](img/6bf56b490ed48f4233b22cc221f5f4c4_124.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_126.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_128.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_130.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_132.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_134.png)

配置完成后，在FTP客户端（如FileZilla）中连接时，需要将连接类型设置为“要求显式的FTP over TLS”。首次连接时会提示证书验证，确认后即可建立加密连接。

---

![](img/6bf56b490ed48f4233b22cc221f5f4c4_136.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_138.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_140.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_142.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_144.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_146.png)

## NFS服务：原理与部署

![](img/6bf56b490ed48f4233b22cc221f5f4c4_148.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_150.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_152.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_154.png)

上一节我们完成了FTP的安全配置，本节中我们来看看NFS服务。NFS（Network File System）允许网络中的计算机之间通过TCP/IP共享目录和文件。它主要应用于Linux/Unix系统之间，可以实现将远程服务器的存储空间挂载到本地，像使用本地磁盘一样使用。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_156.png)

NFS基于客户端-服务器（C/S）架构，其服务端主要依赖于`rpcbind`服务进行端口映射。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_158.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_160.png)

以下是NFS服务端的安装与启动步骤：
1.  安装NFS服务器软件：`yum install -y nfs-utils`
2.  **必须先启动`rpcbind`服务**：`systemctl start rpcbind && systemctl enable rpcbind`
3.  启动NFS服务：`systemctl start nfs-server && systemctl enable nfs-server`
4.  验证服务，NFS默认使用2049端口：
    ```bash
    netstat -lnt | grep 2049
    ```

---

![](img/6bf56b490ed48f4233b22cc221f5f4c4_162.png)

## NFS服务：配置共享与客户端挂载

![](img/6bf56b490ed48f4233b22cc221f5f4c4_164.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_165.png)

NFS的共享配置通过`/etc/exports`文件定义。其基本格式为：
`共享目录 客户端IP或网段(权限选项1，权限选项2)`

以下是常用的权限选项：
*   `rw`：读写权限。
*   `ro`：只读权限。
*   `sync`：同步写入，数据安全性高但性能较低。
*   `async`：异步写入，性能高但数据有丢失风险。
*   `no_root_squash`：信任客户端root用户，映射为服务端的root（不安全）。
*   `root_squash`：将客户端的root用户映射为服务端的匿名用户（如nfsnobody，**默认且推荐**）。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_167.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_169.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_170.png)

配置示例与客户端挂载：
1.  在服务端编辑`/etc/exports`，添加一行：
    ```bash
    /data/share 192.168.1.0/24(rw,sync,root_squash)
    ```
    这表示将`/data/share`目录共享给`192.168.1.0/24`网段，权限为读写、同步、映射root。
2.  使配置生效，无需重启服务：
    ```bash
    exportfs -arv
    ```
3.  在服务端查看当前共享：`showmount -e localhost`
4.  在客户端上，安装NFS客户端软件：`yum install -y nfs-utils`
5.  查看服务端共享：`showmount -e 192.168.1.100`（假设服务端IP）
6.  挂载共享目录到本地（如`/mnt/nfs`）：
    ```bash
    mount -t nfs 192.168.1.100:/data/share /mnt/nfs
    ```
7.  若要实现开机自动挂载，编辑客户端的`/etc/fstab`文件，添加一行：
    ```bash
    192.168.1.100:/data/share /mnt/nfs nfs defaults 0 0
    ```
    然后使用`mount -a`测试挂载。

---

## NFS服务：性能优化与注意事项

在生产环境中，为了提升NFS的I/O性能，可以在客户端挂载时使用一些优化参数。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_172.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_174.png)

以下是常用的挂载优化选项：
*   `noatime`：不更新文件的访问时间，减少磁盘写操作。
*   `nodiratime`：不更新目录的访问时间。
*   `async`：启用异步I/O（权衡数据安全性与性能）。
*   `rsize/wsize`：调整读写缓冲区大小（如`rsize=32768，wsize=32768`）。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_176.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_177.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_178.png)

优化挂载命令示例：
```bash
mount -t nfs -o noatime，nodiratime，rsize=32768，wsize=32768 192.168.1.100:/data/share /mnt/nfs
```

![](img/6bf56b490ed48f4233b22cc221f5f4c4_180.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_182.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_184.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_186.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_187.png)

**重要注意事项**：
1.  权限问题：客户端访问文件时，实际权限由服务端对应文件的权限和NFS的`root_squash`等选项共同决定。通常需要确保服务端共享目录对NFS映射的用户（如`nfsnobody`）有相应权限。
2.  服务依赖：重启NFS服务前，应先重启`rpcbind`服务。
3.  防火墙：需要放行`rpcbind`（111端口）、`nfs`（2049端口）以及`mountd`、`nlockmgr`等动态端口的通信。

![](img/6bf56b490ed48f4233b22cc221f5f4c4_189.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_191.png)

![](img/6bf56b490ed48f4233b22cc221f5f4c4_193.png)

---

## 课程总结

![](img/6bf56b490ed48f4233b22cc221f5f4c4_195.png)

本节课中我们一起学习了两种核心的网络文件服务。
*   我们首先深入探讨了**FTP服务**，从匿名访问配置到本地用户的禁锢机制，最后实现了通过SSL/TLS证书进行加密传输，构建了安全的文件传输环境。
*   接着，我们学习了**NFS服务**，掌握了如何配置服务端共享目录，在客户端进行挂载使用，并了解了用于性能优化的挂载参数。

通过本课的学习，你应当能够根据实际需求，在企业环境中独立部署并管理安全的文件共享服务，为后续学习更复杂的集群存储方案（如MFS、GlusterFS）打下坚实基础。