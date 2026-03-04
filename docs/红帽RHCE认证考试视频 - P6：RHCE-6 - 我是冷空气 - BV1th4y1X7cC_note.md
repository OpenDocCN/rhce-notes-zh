# 红帽RHCE认证考试视频：P6：NFS与Samba服务配置教程 🚀

## 概述
在本节课中，我们将学习如何配置NFS（网络文件系统）和Samba服务。课程分为两个主要部分：第一部分是配置NFS共享目录，包括基本的共享配置和通过Kerberos实现安全的NFS认证；第二部分是配置Samba服务，实现Linux系统间的文件共享，并重点学习Samba的多用户挂载功能。我们将通过一系列实验来巩固这些知识。

---

## NFS服务配置 🗂️

### 什么是NFS？
NFS是Network File System（网络文件系统）的缩写。它的主要功能是通过网络让不同的机器和操作系统共享彼此的文件。NFS服务器可以将本机上的一个目录共享出来，客户端可以远程挂载这个目录到本地文件系统中。这样，客户端访问远程目录就像访问本地磁盘分区一样。

![](img/2c4ceb251c567d6d70608edaf21b5815_1.png)

### NFS配置解析
NFS的配置文件位于`/etc/exports`。配置文件主要由三部分组成：
1. **目录**：需要共享的目录路径。
2. **客户端**：允许挂载的客户端地址。
3. **权限和安全验证方式**：挂载时的权限和认证方式。

![](img/2c4ceb251c567d6d70608edaf21b5815_3.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_5.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_6.png)

以下是配置文件的示例格式：
```
共享目录 客户端地址(权限,安全验证方式)
```

![](img/2c4ceb251c567d6d70608edaf21b5815_8.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_10.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_11.png)

#### 配置示例：
```
/nfs_share desktop0(rw,sec=krb5p)
```

### NFS相关命令
1. **`exportfs -r`**：重新读取`/etc/exports`文件，使配置生效。
2. **`showmount -e <NFS服务器IP>`**：查看NFS服务器上已共享的目录。

![](img/2c4ceb251c567d6d70608edaf21b5815_13.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_15.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_17.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_19.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_20.png)

### Kerberos认证流程 🔐
Kerberos是一种安全认证系统，用于验证客户端和服务器之间的身份。其认证流程如下：
1. 客户端向Kerberos服务器（KDC）发送请求。
2. KDC生成两个数据包：一个用服务器密钥加密的会话密钥和客户端信息，另一个用客户端密钥加密的会话密钥。
3. 客户端解密第二个数据包获取会话密钥，并向服务器发送用会话密钥加密的客户端信息和服务器加密的数据包。
4. 服务器解密数据包，验证客户端信息是否一致，从而完成认证。

![](img/2c4ceb251c567d6d70608edaf21b5815_22.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_24.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_26.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_28.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_30.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_32.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_34.png)

---

## 实验一：配置NFS共享目录 🧪

### 实验步骤
1. **服务器端配置**：
   - 安装NFS软件包：`yum install nfs-utils -y`
   - 启动NFS服务：`systemctl enable nfs-server && systemctl start nfs-server`
   - 创建共享目录：`mkdir /nfs_share`
   - 修改目录属组：`chown nfsnobody:nfsnobody /nfs_share`
   - 编辑配置文件`/etc/exports`：
     ```
     /nfs_share desktop0(rw)
     ```
   - 重新加载配置：`exportfs -r`
   - 配置防火墙：`firewall-cmd --permanent --add-service=nfs && firewall-cmd --reload`

2. **客户端配置**：
   - 创建挂载点：`mkdir /mnt/nfs_share`
   - 编辑`/etc/fstab`文件，添加以下内容：
     ```
     server0:/nfs_share /mnt/nfs_share nfs defaults 0 0
     ```
   - 挂载目录：`mount -a`
   - 验证挂载：`mount | grep nfs_share`

3. **验证实验**：
   - 在客户端创建测试文件：`touch /mnt/nfs_share/test.txt`
   - 在服务器端查看文件是否同步。

![](img/2c4ceb251c567d6d70608edaf21b5815_36.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_37.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_38.png)

---

## 实验二：配置安全的NFS（Kerberos认证） 🔒

### 实验步骤
1. **服务器端配置**：
   - 下载Kerberos密钥文件：`wget -O /etc/krb5.keytab http://classroom.example.com/pub/keytables/server0.keytab`
   - 修改NFS版本：编辑`/etc/sysconfig/nfs`，添加`-V4.2`
   - 启动NFS安全服务：`systemctl enable nfs-secure-server && systemctl restart nfs-secure-server`
   - 创建共享目录：`mkdir /secure_nfs`
   - 编辑配置文件`/etc/exports`：
     ```
     /secure_nfs desktop0(rw,sec=krb5p)
     ```
   - 重新加载配置：`exportfs -r`
   - 配置防火墙：`firewall-cmd --permanent --add-service=nfs && firewall-cmd --reload`

2. **客户端配置**：
   - 下载Kerberos密钥文件：`wget -O /etc/krb5.keytab http://classroom.example.com/pub/keytables/desktop0.keytab`
   - 启动NFS安全服务：`systemctl enable nfs-secure && systemctl start nfs-secure`
   - 创建挂载点：`mkdir /mnt/secure_nfs`
   - 编辑`/etc/fstab`文件，添加以下内容：
     ```
     server0:/secure_nfs /mnt/secure_nfs nfs defaults,v4.2,sec=krb5p 0 0
     ```
   - 挂载目录：`mount -a`
   - 验证挂载：`mount | grep secure_nfs`

![](img/2c4ceb251c567d6d70608edaf21b5815_40.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_42.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_44.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_45.png)

3. **验证实验**：
   - 在服务器端创建测试文件：`echo "Hello World" > /secure_nfs/test.txt`
   - 在客户端查看文件是否同步。

---

## Samba服务配置 🖥️

![](img/2c4ceb251c567d6d70608edaf21b5815_47.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_49.png)

### 什么是Samba？
Samba是一个实现不同操作系统之间文件共享和打印机共享的免费软件，基于SMB协议。它允许Linux系统与Windows系统共享目录和文件。

### Samba配置解析
Samba的配置文件位于`/etc/samba/smb.conf`。常用配置选项包括：
- **workgroup**：指定工作组名称。
- **path**：指定共享目录路径。
- **writable list**：指定具有写权限的用户或组。

![](img/2c4ceb251c567d6d70608edaf21b5815_51.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_53.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_55.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_56.png)

### Samba多用户挂载
多用户挂载允许客户端在挂载Samba共享后，切换访问用户身份而无需重新挂载。这是通过`multiuser`参数实现的。

![](img/2c4ceb251c567d6d70608edaf21b5815_57.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_58.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_60.png)

---

![](img/2c4ceb251c567d6d70608edaf21b5815_62.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_64.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_66.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_68.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_70.png)

## 实验三：配置Samba共享目录 🧪

### 实验步骤
1. **服务器端配置**：
   - 安装Samba软件包：`yum install samba -y`
   - 创建用户组：`groupadd marketing`
   - 创建共享目录：`mkdir /samba_share`
   - 修改目录属组：`chgrp marketing /samba_share`
   - 设置SELinux标签：`semanage fcontext -a -t samba_share_t "/samba_share(/.*)?" && restorecon -Rv /samba_share`
   - 编辑配置文件`/etc/samba/smb.conf`：
     ```
     [samba_share]
     path = /samba_share
     writable list = @marketing
     ```
   - 创建Samba用户：`smbpasswd -a brian`
   - 启动Samba服务：`systemctl enable smb nmb && systemctl start smb nmb`
   - 配置防火墙：`firewall-cmd --permanent --add-service=samba && firewall-cmd --reload`

2. **客户端配置**：
   - 安装Samba客户端软件：`yum install cifs-utils -y`
   - 创建挂载点：`mkdir /mnt/samba_share`
   - 挂载共享目录：`mount -o username=brian //server0/samba_share /mnt/samba_share`
   - 验证挂载：`mount | grep samba_share`

![](img/2c4ceb251c567d6d70608edaf21b5815_72.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_73.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_75.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_77.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_79.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_81.png)

3. **验证实验**：
   - 在客户端创建测试文件：`touch /mnt/samba_share/test.txt`
   - 在服务器端查看文件是否同步。

![](img/2c4ceb251c567d6d70608edaf21b5815_83.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_85.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_87.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_89.png)

![](img/2c4ceb251c567d6d70608edaf21b5815_90.png)

---

## 实验四：配置Samba多用户挂载 🔄

### 实验步骤
1. **客户端配置**：
   - 创建用户凭证文件`/root/smb_multi_user.txt`：
     ```
     username=brian
     password=redhat
     ```
   - 编辑`/etc/fstab`文件，添加以下内容：
     ```
     //server0/samba_share /mnt/multi_user cifs credentials=/root/smb_multi_user.txt,multiuser,sec=ntlmssp 0 0
     ```
   - 挂载目录：`mount -a`
   - 验证挂载：`mount | grep multi_user`

2. **切换用户身份**：
   - 使用`cifscreds`命令切换用户：
     ```
     cifscreds add server0 -u brian
     ```
   - 验证用户权限：尝试在挂载点创建或删除文件。

3. **验证实验**：
   - 使用不同用户身份测试读写权限，确保多用户挂载功能正常。

---

![](img/2c4ceb251c567d6d70608edaf21b5815_92.png)

## 总结 🎯
本节课我们学习了NFS和Samba服务的配置。通过实验，我们掌握了如何配置NFS共享目录、通过Kerberos实现安全的NFS认证，以及如何配置Samba共享目录和多用户挂载功能。这些知识对于在实际环境中实现文件共享和安全管理非常重要。希望大家通过练习，能够熟练掌握这些技能。