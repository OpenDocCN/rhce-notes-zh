# Linux运维：第四章：FTP服务器搭建与配置

在本节课中，我们将学习如何在Linux系统上搭建和配置FTP服务器，实现文件共享。课程内容包括FTP的基本理论、VSFTPD的安装、匿名访问与加密访问的配置，以及NFS共享的初步介绍。我们将通过简单的实验，让初学者也能轻松掌握。

---

## FTP服务器简介

FTP（File Transfer Protocol）服务器是在互联网上提供文件存储和访问服务的计算机，它们依照FTP协议提供服务。在以往，FTP常与网站绑定，供研发部门上传程序代码。虽然现在更多使用Git等版本管理工具，但FTP在某些场景下仍有其用途。

在Windows系统下，常见的FTP服务器软件有Serv-U、FileZilla Server等。而在Linux系统下，则有ProFTPD和我们今天要重点讲解的**VSFTPD**。

![](img/7e7514c7de20269a1b674fc83cd275dc_1.png)

![](img/7e7514c7de20269a1b674fc83cd275dc_3.png)

**VSFTPD**（Very Secure FTP Daemon）是一个基于GPL协议发布的类Unix系统FTP服务器软件。GPL协议意味着它是自由软件，允许用户自由使用、修改和重新发布。VSFTPD以其安全、高速和稳定著称。

![](img/7e7514c7de20269a1b674fc83cd275dc_5.png)

![](img/7e7514c7de20269a1b674fc83cd275dc_7.png)

![](img/7e7514c7de20269a1b674fc83cd275dc_9.png)

![](img/7e7514c7de20269a1b674fc83cd275dc_11.png)

FTP服务采用C/S（客户端/服务器）架构，默认监听**20**和**21**端口。其中，**21端口**用于传输控制指令，**20端口**则用于数据传输。

![](img/7e7514c7de20269a1b674fc83cd275dc_13.png)

![](img/7e7514c7de20269a1b674fc83cd275dc_15.png)

![](img/7e7514c7de20269a1b674fc83cd275dc_17.png)

---

![](img/7e7514c7de20269a1b674fc83cd275dc_19.png)

## FTP的工作模式

FTP包含控制通道和数据传输通道，其工作模式主要分为两种：**主动模式**和**被动模式**。区分的关键在于以服务器为参照，看是服务器主动连接客户端，还是等待客户端来连接。

### 主动模式工作原理
1.  客户端连接到FTP服务器的21端口，发送用户名和密码。
2.  客户端随机开放一个高位端口（大于1024），并向服务器发送`PORT`命令，告知服务器客户端采用主动模式及开放的端口号。
3.  FTP服务器收到请求后，通过其20端口主动连接到客户端开放的高位端口。
4.  建立连接后，开始传输数据。

![](img/7e7514c7de20269a1b674fc83cd275dc_21.png)

![](img/7e7514c7de20269a1b674fc83cd275dc_23.png)

![](img/7e7514c7de20269a1b674fc83cd275dc_24.png)

### 被动模式工作原理
1.  客户端连接到FTP服务器的21端口，发送用户名和密码。
2.  客户端向服务器发送`PASV`（被动）命令。
3.  服务器在本地随机开放一个高位端口，并将该端口号告知客户端。
4.  客户端主动连接到服务器开放的这个高位端口。
5.  建立连接后，开始传输数据。

简单来说，如果服务器主动连接客户端进行数据传输，就是主动模式；如果是客户端连接服务器开放的端口，就是被动模式。

![](img/7e7514c7de20269a1b674fc83cd275dc_26.png)

---

## 安装VSFTPD

![](img/7e7514c7de20269a1b674fc83cd275dc_28.png)

安装过程非常简单，我们可以使用YUM包管理器进行安装。建议同时安装客户端工具`lftp`以便测试。

![](img/7e7514c7de20269a1b674fc83cd275dc_30.png)

![](img/7e7514c7de20269a1b674fc83cd275dc_32.png)

以下是安装命令：
```bash
# 在服务端安装VSFTPD服务器软件
yum install -y vsftpd

# 在服务端和客户端安装FTP客户端工具lftp
yum install -y lftp
```
`lftp`是一个功能强大的下载工具，不仅支持FTP，还支持HTTP、HTTPS等多种协议，具有命令补全、历史记录、多后台任务执行等功能。

![](img/7e7514c7de20269a1b674fc83cd275dc_34.png)

![](img/7e7514c7de20269a1b674fc83cd275dc_36.png)

安装完成后，服务端的主要配置文件位于`/etc/vsftpd/vsftpd.conf`。此外，还有`/etc/vsftpd/user_list`和`/etc/vsftpd/ftpusers`文件，分别用于设置用户白名单和黑名单。

![](img/7e7514c7de20269a1b674fc83cd275dc_38.png)

![](img/7e7514c7de20269a1b674fc83cd275dc_40.png)

![](img/7e7514c7de20269a1b674fc83cd275dc_42.png)

启动VSFTPD服务并设置为开机自启：
```bash
systemctl start vsftpd
systemctl enable vsftpd
```
使用`netstat -antp | grep vsftpd`命令可以查看到21端口已在监听，表示服务启动成功。

![](img/7e7514c7de20269a1b674fc83cd275dc_44.png)

---

![](img/7e7514c7de20269a1b674fc83cd275dc_46.png)

![](img/7e7514c7de20269a1b674fc83cd275dc_48.png)

## 配置匿名访问FTP

![](img/7e7514c7de20269a1b674fc83cd275dc_50.png)

上一节我们安装了VSFTPD，本节我们来配置允许匿名用户访问的FTP服务器。这种配置适用于需要向所有员工公开共享文件的场景。

![](img/7e7514c7de20269a1b674fc83cd275dc_52.png)

在修改主配置文件前，良好的习惯是先进行备份：
```bash
cp -a /etc/vsftpd/vsftpd.conf /etc/vsftpd/vsftpd.conf.back
```
接下来，编辑主配置文件`/etc/vsftpd/vsftpd.conf`，确保以下参数设置正确：
```bash
# 允许匿名用户登录
anonymous_enable=YES
# 允许本地用户登录（保持默认）
local_enable=YES
# 允许写操作（上传、创建目录）
write_enable=YES
# 允许匿名用户上传文件
anon_upload_enable=YES
# 允许匿名用户创建目录
anon_mkdir_write_enable=YES
```
保存并退出编辑器后，重启VSFTPD服务使配置生效：
```bash
systemctl restart vsftpd
```
仅修改配置还不够，还需要调整匿名用户默认共享目录`/var/ftp/pub`的权限，将其属主改为FTP用户：
```bash
chown -R ftp.ftp /var/ftp/pub
```
此时，匿名用户已经可以登录、上传文件和创建目录。但如果需要允许匿名用户删除文件，则需要在配置文件中额外添加一个高风险参数：
```bash
# 允许匿名用户删除和重命名文件（慎用！）
anon_other_write_enable=YES
```
**注意**：`anon_other_write_enable=YES`会赋予匿名用户过大的权限，在生产环境中通常不建议启用。匿名用户一般只应拥有只读权限。

---

![](img/7e7514c7de20269a1b674fc83cd275dc_54.png)

## 配置加密访问（本地用户）FTP

上一节我们配置了权限过大的匿名访问，本节我们来看更安全的加密访问方式，即使用系统本地用户账号登录FTP。

假设公司有两个团队`team1`和`team2`需要维护网站，要求他们只能通过FTP登录服务器，不能登录系统Shell，并且将其活动范围限制在网站根目录`/var/www/html`内。

首先，创建两个不能登录系统的用户账号：
```bash
useradd -s /sbin/nologin team1
useradd -s /sbin/nologin team2
# 为team1设置密码
echo “123456” | passwd --stdin team1
# 为team2设置密码
echo “123456” | passwd --stdin team2
```
接着，恢复VSFTPD的配置文件到初始状态，然后编辑`/etc/vsftpd/vsftpd.conf`：
```bash
# 禁止匿名用户登录
anonymous_enable=NO
# 允许本地用户登录
local_enable=YES
# 允许写操作
write_enable=YES

# 以下为新增配置，通常添加在文件末尾
# 启用chroot，将用户锁定在其家目录
chroot_local_user=YES
# 指定被锁定用户的列表文件
chroot_list_file=/etc/vsftpd/chroot_list
# 允许被锁定的用户有写权限
allow_writeable_chroot=YES
```
创建并编辑`/etc/vsftpd/chroot_list`文件，将需要锁定目录的用户名加入：
```bash
team1
team2
```
然后，创建网站根目录并设置权限，确保其他用户（Other）有写权限，以便FTP进程可以写入：
```bash
mkdir -p /var/www/html
chmod o+w /var/www/html
```
最后，重启VSFTPD服务：
```bash
systemctl restart vsftpd
```
现在，可以使用`team1`或`team2`账号和密码登录FTP。登录后，用户将被限制在`/var/www/html`目录下，可以正常上传、下载和删除文件（因为目录有`o+w`权限），但无法访问系统的其他目录。

---

## 总结

本节课我们一起学习了Linux下FTP服务器的搭建与配置。

![](img/7e7514c7de20269a1b674fc83cd275dc_56.png)

我们首先了解了FTP的基本概念、VSFTPD的特点以及FTP主动与被动两种工作模式的区别。然后，我们动手安装并配置了VSFTPD服务。

![](img/7e7514c7de20269a1b674fc83cd275dc_58.png)

在实践部分，我们完成了两个经典实验：
1.  **配置匿名访问FTP**：实现了无需账号即可上传下载文件的功能，同时我们也强调了赋予匿名用户写权限 (`anon_other_write_enable=YES`) 可能带来的安全风险。
2.  **配置加密访问FTP**：使用本地用户账号登录，并通过`chroot`机制将用户的活动范围严格限制在指定目录（如`/var/www/html`）内，极大地增强了安全性。

![](img/7e7514c7de20269a1b674fc83cd275dc_60.png)

![](img/7e7514c7de20269a1b674fc83cd275dc_62.png)

通过本章学习，你应该能够根据实际需求，在Linux系统上部署安全、可控的文件共享服务。