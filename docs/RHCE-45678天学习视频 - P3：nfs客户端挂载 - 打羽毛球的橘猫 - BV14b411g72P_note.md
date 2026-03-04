# RHCE-45678天学习视频：P3：NFS客户端挂载 🖥️➡️📂

在本节课中，我们将学习如何在客户端主机上挂载NFS服务器共享的目录。主要内容包括：创建本地挂载点、下载并配置身份验证密钥、正确启动客户端服务、编辑`/etc/fstab`文件实现永久挂载，以及验证挂载和用户权限。

---

## 创建本地挂载点

上一节我们介绍了如何配置NFS服务器。本节中，我们来看看如何在客户端进行挂载。首先，需要在客户端创建两个本地目录，用于挂载来自服务器的共享。

以下是创建挂载点的命令：
```bash
mkdir /mnt/nfsmount
mkdir /mnt/nfssecure
```

## 下载身份验证密钥

服务器提供了一个受保护的共享目录，客户端需要下载对应的密钥文件才能访问。

以下是下载密钥的命令：
```bash
wget -O /etc/krb5.keytab [密钥URL]
```
请将`[密钥URL]`替换为实际的密钥下载地址。

## 安装与启动客户端服务

客户端需要安装`nfs-utils`软件包来支持NFS功能，但更重要的是启动正确的服务。请注意，客户端**不**需要启动`nfs-server`服务。

以下是安装软件包和启动正确服务的命令：
```bash
# 安装nfs-utils（如果尚未安装）
yum install -y nfs-utils

# 设置并启动nfs-secure服务（这是客户端所需的核心服务）
systemctl enable nfs-secure
systemctl start nfs-secure
```
**重要提示**：客户端只需启用并启动`nfs-secure`服务，而不是`nfs-server`服务。

## 配置 `/etc/fstab` 实现永久挂载

为了让挂载在系统重启后依然生效，我们需要编辑`/etc/fstab`文件。这里需要挂载两个共享：一个公共共享和一个需要密钥验证的安全共享。

以下是`/etc/fstab`文件中需要添加的配置行：
```
server0:/public /mnt/nfsmount nfs defaults,_netdev 0 0
server0:/protected /mnt/nfssecure nfs defaults,sec=krb5p,_netdev 0 0
```
**核心参数解释**：
*   `server0:/public` 和 `server0:/protected`：NFS服务器的共享路径。
*   `/mnt/nfsmount` 和 `/mnt/nfssecure`：本地的挂载点。
*   `nfs`：文件系统类型。
*   `defaults`：使用默认挂载选项。
*   `sec=krb5p`：指定使用Kerberos进行身份验证（用于安全共享）。
*   `_netdev`：这是一个关键选项，表明这是一个网络设备，系统会等待网络就绪后再尝试挂载。
*   `0 0`：dump和fsck相关参数。

**加分项**：如果你知道服务器支持NFS v4.2，可以在选项中加入`vers=4.2`以指定版本。
```
server0:/public /mnt/nfsmount nfs defaults,vers=4.2,_netdev 0 0
```

编辑完成后，执行以下命令测试并应用配置：
```bash
# 测试fstab配置是否正确
mount -a

# 查看挂载结果和文件系统类型
df -Th
```
在`df -Th`的输出中，你应该能看到两个类型为`nfs4`的挂载点。

## 验证挂载与用户权限

配置完成后，我们需要验证挂载是否成功，并测试指定用户是否能在安全共享中创建文件。

以下是验证步骤：
1.  首先，切换到题目要求的用户`ldapuser1`。
    ```bash
    su - ldapuser1
    # 密码通常为 ‘kerberos’
    ```
2.  尝试在安全共享目录中创建文件。
    ```bash
    cd /mnt/nfssecure
    touch testfile
    ```
    如果文件创建成功，说明挂载和身份验证均正常工作。
3.  你可以尝试在公共共享（`/mnt/nfsmount`）中创建文件，通常会收到“read-only file system”的提示，这是符合预期的。

---

本节课中我们一起学习了NFS客户端挂载的完整流程。关键点总结如下：
1.  **创建挂载点**：使用`mkdir`命令。
2.  **获取密钥**：使用`wget`下载身份验证文件。
3.  **服务管理**：客户端只需启动`nfs-secure`服务。
4.  **永久挂载**：在`/etc/fstab`中正确配置挂载信息，特别注意`sec=krb5p`（用于安全共享）和`_netdev`（用于网络文件系统）选项。
5.  **验证测试**：使用`mount -a`和`df -Th`检查挂载，并切换相应用户测试写权限。

确保遵循这些步骤，你就能成功配置NFS客户端挂载。