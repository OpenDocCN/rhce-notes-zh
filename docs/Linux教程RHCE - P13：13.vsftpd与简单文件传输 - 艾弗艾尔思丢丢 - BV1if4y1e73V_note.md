# Linux教程RHCE：P13：vsftpd与简单文件传输

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_1.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_2.png)

在本节课中，我们将要学习Linux系统中的文件传输与共享服务。主要内容包括功能强大的vsftpd文件传输服务，以及轻量级的TFTP简单文件传输协议。我们将深入探讨vsftpd的三种用户验证模式，并了解如何配置Samba服务实现跨平台文件共享。

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_4.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_5.png)

## 文件传输协议（FTP）概述

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_7.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_8.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_9.png)

上一节我们介绍了课程的整体安排，本节中我们来看看文件传输协议（FTP）的基本原理。

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_11.png)

FTP协议用于在网络上进行文件传输。一个常见的误解是FTP服务只占用21端口。实际上，FTP协议使用两个端口：
*   **21端口**：用于传输控制命令。
*   **20端口**：用于传输实际数据。

因此，在配置防火墙时，需要同时放行20和21端口。

FTP协议本身通过明文传输数据，包括账户和密码信息，因此被认为不够安全。为了解决这个问题，我们通常使用 **vsftpd**（Very Secure FTP Daemon）服务程序来搭建更安全的FTP服务。

vsftpd支持三种用户验证模式，以提供不同级别的安全性和灵活性：
1.  匿名用户模式
2.  本地用户模式
3.  虚拟用户模式

## 安装vsftpd服务程序

在开始配置之前，我们需要先安装vsftpd软件包。建议在操作前为虚拟机创建快照，以便实验出错时可以快速还原。

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_13.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_14.png)

使用以下命令进行安装：
```bash
yum install -y vsftpd
```

## 配置vsftpd服务

在Linux系统中，配置服务通常意味着修改其对应的配置文件。vsftpd的主配置文件路径为：
```
/etc/vsftpd/vsftpd.conf
```

初次查看该文件可能会因为注释行较多而感到复杂。我们可以过滤掉注释和空行，查看实际生效的配置参数：
```bash
grep -v "^#" /etc/vsftpd/vsftpd.conf | grep -v "^$"
```

### 匿名用户模式配置

匿名用户模式允许任何人无需密码即可访问FTP服务器，默认登录到`/var/ftp/pub`目录。

我们需要在主配置文件中启用并配置匿名访问。以下是关键参数及其含义：

*   `anonymous_enable=YES`：启用匿名访问。
*   `anon_umask=022`：设置匿名用户创建文件的默认权限掩码。文件最终权限为`666-022=644`，目录权限为`777-022=755`。
*   `anon_upload_enable=YES`：允许匿名用户上传文件。
*   `anon_mkdir_write_enable=YES`：允许匿名用户创建目录。
*   `anon_other_write_enable=YES`：允许匿名用户执行其他写入操作，如重命名、删除。

配置完成后，需要重启服务使配置生效，并设置开机自启：
```bash
systemctl restart vsftpd
systemctl enable vsftpd
```

此外，还需要确保SELinux放行了FTP的相关权限。可以临时关闭SELinux进行测试，或永久设置相关布尔值：
```bash
setsebool -P ftpd_full_access=on
```

客户端可以使用`ftp`命令或图形化工具（如Windows下的FlashFXP）进行连接。匿名登录用户名为`anonymous`，密码为空。

### 本地用户模式配置

本地用户模式允许系统已存在的用户使用自己的账户和密码登录FTP服务器，默认登录到该用户的家目录。

此模式默认已启用，主要涉及以下参数：
*   `local_enable=YES`：允许本地用户登录。
*   `write_enable=YES`：允许本地用户进行写入操作。

需要注意的是，出于安全考虑，超级用户root默认被禁止登录。相关限制定义在`/etc/vsftpd/user_list`和`/etc/vsftpd/ftpusers`文件中。如需允许root登录，需从这些文件中移除`root`。

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_16.png)

配置修改后，同样需要重启vsftpd服务。

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_18.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_20.png)

### 虚拟用户模式配置

虚拟用户模式是安全性最高的一种模式。它创建一套独立的用户数据库，这些用户仅用于FTP登录，不在Linux系统中实际存在。

配置虚拟用户模式步骤较多：

1.  **创建用户明文文件**：例如`/etc/vsftpd/vuser.list`，奇数行为用户名，偶数行为密码。
    ```
    zhangsan
    redhat
    lisi
    redhat
    ```

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_22.png)

2.  **生成虚拟用户数据库文件**：使用`db_load`命令对明文文件进行哈希加密。
    ```bash
    db_load -T -t hash -f /etc/vsftpd/vuser.list /etc/vsftpd/vuser.db
    chmod 600 /etc/vsftpd/vuser.db # 设置权限确保安全
    rm -f /etc/vsftpd/vuser.list # 删除明文文件
    ```

3.  **创建虚拟用户映射的本地系统用户**：该用户无需登录系统，仅用于映射文件所有权。
    ```bash
    useradd -d /var/ftproot -s /sbin/nologin vuser
    chmod -R 755 /var/ftproot
    ```

4.  **建立PAM认证文件**：创建`/etc/pam.d/vsftpd.vu`，指定使用刚才创建的数据库文件进行认证。
    ```
    auth required pam_userdb.so db=/etc/vsftpd/vuser
    account required pam_userdb.so db=/etc/vsftpd/vuser
    ```

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_24.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_25.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_27.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_29.png)

5.  **修改vsftpd主配置文件**：指定使用PAM认证文件，并设置虚拟用户相关参数。
    ```
    anonymous_enable=NO
    local_enable=YES
    guest_enable=YES
    guest_username=vuser
    pam_service_name=vsftpd.vu
    user_config_dir=/etc/vsftpd/vusers_dir
    allow_writeable_chroot=YES
    ```

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_31.png)

6.  **为不同虚拟用户设置独立权限**：在`/etc/vsftpd/vusers_dir/`目录下为每个用户创建同名配置文件。例如，为`zhangsan`设置更多权限：
    ```
    anon_upload_enable=YES
    anon_mkdir_write_enable=YES
    anon_other_write_enable=YES
    ```

最后，重启vsftpd服务。虚拟用户登录后，将被限制在其映射的本地用户家目录（牢笼机制）。

## 简单文件传输协议（TFTP）

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_33.png)

上一节我们介绍了功能全面的vsftpd，本节中我们来看看一个更加轻量级的文件传输工具——TFTP。

TFTP（Trivial File Transfer Protocol）是一种非常简单的文件传输协议，基于UDP的69端口。它没有用户认证机制，只能进行基本的文件上传和下载，常用于网络设备传输配置文件或固件。

安装TFTP的客户端和服务端软件包：
```bash
yum install -y tftp tftp-server
```

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_35.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_36.png)

TFTP服务由xinetd超级服务管理。编辑其配置文件`/etc/xinetd.d/tftp`：
```
service tftp
{
    ...
    disable = no # 将“yes”改为“no”以启用服务
    ...
}
```

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_38.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_40.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_42.png)

重启xinetd服务以启用TFTP：
```bash
systemctl restart xinetd
systemctl enable xinetd
```

TFTP服务的默认根目录是`/var/lib/tftpboot`。客户端使用`tftp`命令连接服务器后，可以使用`get`和`put`命令下载或上传文件，但无法列出目录内容。

## Samba文件共享服务

除了FTP，另一种重要的文件共享方式是SMB/CIFS协议，在Linux上对应的服务是Samba。它可以实现Linux与Windows系统之间的文件共享。

Samba的配置相对直观。安装软件包后，编辑其主配置文件`/etc/samba/smb.conf`。一个简单的共享目录配置示例如下：
```
[database]
    comment = Do not arbitrarily modify the database file
    path = /database
    public = no
    writable = yes
```

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_44.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_45.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_47.png)

创建共享目录并设置权限和SELinux上下文：
```bash
mkdir /database
chmod -R 777 /database # 或设置更精细的权限
semanage fcontext -a -t samba_share_t "/database(/.*)?"
restorecon -Rv /database
```

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_49.png)

创建Samba用户（必须是已存在的系统用户）并设置密码：
```bash
smbpasswd -a lin
```

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_51.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_53.png)

在Windows客户端，可以通过文件资源管理器地址栏输入`\\服务器IP`来访问共享。在Linux客户端，可以安装`cifs-utils`包，然后通过mount命令挂载Samba共享：
```bash
mount -t cifs //192.168.10.10/database /media -o credentials=/root/auth.smb
```

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_55.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_56.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_57.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_59.png)

## 课程总结

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_61.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_63.png)

本节课中我们一起学习了Linux下的文件传输与共享服务。

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_65.png)

我们首先深入探讨了vsftpd服务，掌握了其三种用户验证模式（匿名、本地、虚拟用户）的配置方法与适用场景。虚拟用户模式因其高安全性而在生产环境中被推荐使用。

接着，我们了解了TFTP简单文件传输协议，它适用于无需认证的简单文件传输任务。

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_67.png)

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_69.png)

最后，我们介绍了Samba服务，它实现了跨平台的SMB/CIFS协议文件共享，使得Linux与Windows系统之间能够方便地共享文件和打印机。

![](img/a98abe6bc7192a97e8fb24b8a83abf0e_71.png)

通过本课的学习，您应该能够根据实际需求，选择和配置合适的文件传输或共享服务。