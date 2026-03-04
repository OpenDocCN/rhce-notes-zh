# RHCE课程：第4天：NFS客户端挂载配置 🖥️

在本节课中，我们将学习如何在客户端主机上挂载NFS服务器共享的目录。主要内容包括创建挂载点、下载身份验证密钥、配置自动挂载以及验证挂载结果。

---

## 概述

上一节我们介绍了如何配置NFS服务器端。本节中，我们来看看如何在客户端进行挂载操作。客户端需要挂载服务器提供的两个共享目录，其中一个需要密钥验证，并确保特定用户能在受保护的目录中创建文件。

## 配置步骤

### 1. 创建挂载点目录

首先，在客户端上创建两个本地目录，用于挂载来自NFS服务器的共享。

以下是创建挂载点的命令：
```bash
mkdir /mnt/nfsmount
mkdir /mnt/nfssecure
```

### 2. 下载身份验证密钥

受保护的共享目录需要密钥进行身份验证。我们需要从指定URL下载该密钥。

以下是下载密钥的命令：
```bash
wget -O /etc/krb5.keytab [密钥URL]
```
请将 `[密钥URL]` 替换为实际的密钥下载地址。

### 3. 安装必要软件包

客户端需要安装 `nfs-utils` 软件包来支持NFS挂载功能。

以下是安装命令：
```bash
yum install nfs-utils -y
```
如果系统已安装该软件包，则会提示无需操作。

### 4. 启动相关服务

**重要提示**：客户端只需启动 `nfs-secure` 服务，用于处理加密身份验证，而**不是**启动 `nfs-server` 服务。

以下是设置并启动服务的命令：
```bash
systemctl enable nfs-secure
systemctl start nfs-secure
```

### 5. 配置 `/etc/fstab` 文件

接下来，我们需要编辑 `/etc/fstab` 文件，配置系统启动时自动挂载NFS共享。

以下是需要添加的两行配置内容：
```
server0:/public /mnt/nfsmount nfs defaults,_netdev 0 0
server0:/protected /mnt/nfssecure nfs defaults,sec=krb5p,_netdev 0 0
```
**核心参数解释**：
*   `sec=krb5p`：指定使用Kerberos进行身份验证。
*   `_netdev`：指明这是网络设备，确保在网络就绪后再尝试挂载。
*   版本号（如 `nfsvers=4.2`）可以作为加分项添加在 `defaults` 之后。

### 6. 执行挂载并验证

配置完成后，执行挂载命令并检查结果。

以下是验证命令：
```bash
mount -a
df -Th
```
`df -Th` 命令的输出应显示两个NFS共享已成功挂载，类型为 `nfs4`。

### 7. 测试用户权限

最后，我们需要验证指定用户 `ldapuser1` 是否能在受保护的共享目录中创建文件。

以下是测试步骤：
```bash
su - ldapuser1
cd /mnt/nfssecure
touch testfile
```
如果 `testfile` 文件创建成功，则说明权限配置正确。在公共目录 `/mnt/nfsmount` 中，该用户可能只有只读权限。

## 总结

本节课中我们一起学习了NFS客户端的完整配置流程。关键点在于：创建正确的挂载点、下载并放置密钥、**仅启动 `nfs-secure` 服务**、在 `/etc/fstab` 中准确配置挂载选项（特别是 `sec=krb5p` 和 `_netdev`），以及最后的权限测试。确保理解客户端与服务端配置的区别，是成功实现NFS共享访问的基础。