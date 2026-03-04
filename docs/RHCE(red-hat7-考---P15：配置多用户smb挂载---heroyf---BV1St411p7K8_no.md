# RHCE考前讲解：P15：配置多用户SMB挂载 📂

在本节课中，我们将学习如何在Red Hat Enterprise Linux 7上配置多用户SMB（Server Message Block）挂载。SMB是一种网络文件共享协议，允许不同计算机之间共享文件和打印机。我们将从创建共享目录开始，逐步配置Samba服务，并在客户端实现自动挂载，确保多用户能够安全地访问共享资源。

![](img/8f2bbc634c783319ffba4d9302e13ba4_1.png)

---

![](img/8f2bbc634c783319ffba4d9302e13ba4_2.png)

## 创建共享目录与设置权限

![](img/8f2bbc634c783319ffba4d9302e13ba4_4.png)

首先，我们需要在服务器上创建用于共享的目录，并为其设置正确的所有权和权限。

以下是创建和配置共享目录的步骤：

![](img/8f2bbc634c783319ffba4d9302e13ba4_6.png)

1.  使用 `mkdir` 命令创建目录。
    ```bash
    mkdir /samba
    ```
2.  使用 `chown` 命令修改目录的所有者和所属组。
    ```bash
    chown user_manager:hr /samba
    ```
3.  使用 `chmod` 命令设置目录的访问权限。
    ```bash
    chmod 2770 /samba
    ```
    这里的权限 `2770` 表示：所有者（user_manager）和所属组（hr）拥有读、写、执行权限（7），并且设置了SGID位（2），确保在该目录下新建的文件会自动继承hr组的权限。

![](img/8f2bbc634c783319ffba4d9302e13ba4_8.png)

为了验证权限设置是否正确，可以使用 `getfacl` 命令查看目录的详细访问控制列表。
```bash
getfacl /samba
```

---

![](img/8f2bbc634c783319ffba4d9302e13ba4_10.png)

## 配置Samba服务

上一节我们创建了共享目录，本节中我们来看看如何配置Samba服务来发布这个共享。

![](img/8f2bbc634c783319ffba4d9302e13ba4_12.png)

Samba的主要配置文件是 `/etc/samba/smb.conf`。我们需要在其中添加共享定义。

以下是 `smb.conf` 配置文件中关于我们共享目录的关键部分：
```ini
[data]
    path = /samba
    valid users = @hr
    writable = yes
    browseable = yes
```
*   `[data]`：共享的名称，客户端将通过此名称访问共享。
*   `path`：指定服务器上共享目录的实际路径。
*   `valid users`：定义允许访问此共享的用户或组。`@hr` 表示hr组的所有成员。
*   `writable`：允许用户在此共享中创建和修改文件。
*   `browseable`：允许用户在浏览网络时看到此共享。

![](img/8f2bbc634c783319ffba4d9302e13ba4_14.png)

配置完成后，需要重启Samba服务以使更改生效。
```bash
systemctl restart smb
systemctl enable smb
```

---

![](img/8f2bbc634c783319ffba4d9302e13ba4_16.png)

## 创建Samba用户并设置密码

![](img/8f2bbc634c783319ffba4d9302e13ba4_17.png)

Samba服务有独立的用户数据库。即使系统用户已存在，也需要为其设置Samba密码。

以下是管理Samba用户的命令：
```bash
# 为系统用户user_manager设置Samba密码
smbpasswd -a user_manager

![](img/8f2bbc634c783319ffba4d9302e13ba4_19.png)

# 启用Samba用户
smbpasswd -e user_manager
```
你需要为所有需要访问共享的hr组成员执行此操作。

![](img/8f2bbc634c783319ffba4d9302e13ba4_21.png)

---

## 客户端安装与配置

现在，服务器端的共享已经准备就绪。接下来，我们需要在客户端进行配置，以便能够挂载并使用这个SMB共享。

![](img/8f2bbc634c783319ffba4d9302e13ba4_23.png)

首先，在客户端安装必要的软件包。
```bash
yum install samba-client cifs-utils -y
```
*   `samba-client`：提供访问SMB共享的客户端工具。
*   `cifs-utils`：提供挂载CIFS/SMB文件系统所需的工具。

![](img/8f2bbc634c783319ffba4d9302e13ba4_25.png)

---

## 配置自动挂载与凭据文件

![](img/8f2bbc634c783319ffba4d9302e13ba4_27.png)

为了实现自动挂载并安全地存储访问凭据，我们需要创建一个凭据文件。

![](img/8f2bbc634c783319ffba4d9302e13ba4_29.png)

凭据文件（例如 `/root/smb.cred`）的内容如下：
```ini
username=user_manager
password=your_password_here
domain=WORKGROUP
```
请将 `your_password_here` 替换为user_manager的实际Samba密码。务必确保该文件的权限仅为root可读，以保护密码安全。
```bash
chmod 600 /root/smb.cred
```

![](img/8f2bbc634c783319ffba4d9302e13ba4_31.png)

---

![](img/8f2bbc634c783319ffba4d9302e13ba4_33.png)

## 配置自动挂载（Autofs）

Autofs服务可以实现按需自动挂载，当访问挂载点时自动触发，不使用时自动卸载，非常方便。

![](img/8f2bbc634c783319ffba4d9302e13ba4_35.png)

我们需要编辑Autofs的主配置文件 `/etc/auto.master` 和子映射文件。

1.  在 `/etc/auto.master` 中添加以下行：
    ```
    /mnt/samba /etc/auto.samba --timeout=300
    ```
    这表示将 `/mnt/samba` 目录下的挂载交由 `/etc/auto.samba` 文件管理，超时时间为300秒。

![](img/8f2bbc634c783319ffba4d9302e13ba4_37.png)

2.  创建并编辑 `/etc/auto.samba` 文件，内容如下：
    ```
    data -fstype=cifs,credentials=/root/smb.cred ://server_ip/data
    ```
    *   `data`：这是在 `/mnt/samba` 下创建的子目录名（即最终的挂载点为 `/mnt/samba/data`）。
    *   `-fstype=cifs`：指定文件系统类型为CIFS。
    *   `credentials=/root/smb.cred`：指定包含登录信息的凭据文件路径。
    *   `://server_ip/data`：指定Samba服务器的IP地址和共享名。

![](img/8f2bbc634c783319ffba4d9302e13ba4_39.png)

3.  重启Autofs服务。
    ```bash
    systemctl restart autofs
    ```

![](img/8f2bbc634c783319ffba4d9302e13ba4_41.png)

---

## 测试与验证

![](img/8f2bbc634c783319ffba4d9302e13ba4_43.png)

配置完成后，我们需要测试挂载是否成功以及文件共享是否正常工作。

![](img/8f2bbc634c783319ffba4d9302e13ba4_44.png)

首先，使用 `mount` 命令检查挂载状态。当访问挂载点 `/mnt/samba/data` 时，Autofs会自动完成挂载。
```bash
# 切换到挂载目录，触发自动挂载
cd /mnt/samba/data
# 查看挂载信息
mount | grep cifs
```
如果配置正确，你应该能看到一条关于 `//server_ip/data` 的挂载记录。

然后，进行文件操作测试以验证读写权限。
```bash
# 在客户端挂载点创建一个测试文件
touch /mnt/samba/data/testfile.txt
# 在服务器的共享目录中检查该文件是否存在
ls -l /samba/
```
如果在服务器端的 `/samba/` 目录下能看到 `testfile.txt` 文件，则证明多用户SMB挂载配置成功，文件共享功能正常。

![](img/8f2bbc634c783319ffba4d9302e13ba4_46.png)

---

![](img/8f2bbc634c783319ffba4d9302e13ba4_47.png)

本节课中我们一起学习了在RHEL7上配置多用户SMB共享的完整流程。我们从创建目录、设置权限开始，然后配置了Samba服务端，创建了Samba用户。接着，在客户端安装了必要软件，配置了安全的凭据文件和基于Autofs的自动挂载。最后，通过测试验证了整个共享环境的可用性。掌握这些步骤对于RHCE认证考试中相关的文件共享服务题目至关重要。