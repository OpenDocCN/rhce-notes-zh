# Linux运维：第四章：FTP服务器搭建与配置 🚀

在本节课中，我们将学习如何在Linux系统上搭建和配置FTP服务器，实现文件共享功能。课程将涵盖FTP的基本理论、VSFTPD服务的安装与配置，并通过实战演示匿名访问和加密访问两种模式。最后，我们还会简要介绍NFS共享服务的配置。

---

## FTP服务器概述 📚

FTP服务器是在互联网上提供文件存储和访问服务的计算机，它们依照FTP协议提供服务。在运维工作中，文件共享是常见需求。在Windows系统中设置共享文件夹可能大家已经熟悉，而在Linux下，我们可以通过搭建FTP或NFS服务器来实现类似功能。

本节课我们将重点学习基于GPL协议发布的VSFTPD软件，它具有安全、高速、稳定的特点。

---

## FTP工作原理与模式 🔄

![](img/a7dd083a85765a3db9b451edc32b8d12_1.png)

![](img/a7dd083a85765a3db9b451edc32b8d12_3.png)

![](img/a7dd083a85765a3db9b451edc32b8d12_5.png)

FTP采用C/S架构，默认监听**20**和**21**端口。其中，**21端口用于传输指令**，**20端口用于传输数据**。

![](img/a7dd083a85765a3db9b451edc32b8d12_7.png)

![](img/a7dd083a85765a3db9b451edc32b8d12_9.png)

![](img/a7dd083a85765a3db9b451edc32b8d12_11.png)

FTP有两种工作模式，区分的关键在于**数据连接是由服务器还是客户端主动发起**。

![](img/a7dd083a85765a3db9b451edc32b8d12_13.png)

![](img/a7dd083a85765a3db9b451edc32b8d12_15.png)

### 主动模式
在主动模式下，服务器主动连接客户端进行数据传输。
1.  客户端连接到FTP服务器的21端口，发送用户名和密码。
2.  客户端随机开放一个高位端口（>1024），并通过`PORT`命令告知服务器。
3.  服务器使用20端口主动连接到客户端开放的随机端口。
4.  建立数据连接，开始传输。

![](img/a7dd083a85765a3db9b451edc32b8d12_17.png)

### 被动模式
在被动模式下，服务器被动等待客户端连接进行数据传输。
1.  客户端连接到FTP服务器的21端口，发送用户名和密码。
2.  客户端发送`PASV`命令。
3.  服务器在本地随机开放一个高位端口，并告知客户端。
4.  客户端主动连接到服务器开放的随机端口。
5.  建立数据连接，开始传输。

![](img/a7dd083a85765a3db9b451edc32b8d12_19.png)

简单来说，**以服务器为参照，服务器主动连接即为主动模式，服务器等待连接即为被动模式**。

---

## 安装VSFTPD服务端与客户端 🔧

![](img/a7dd083a85765a3db9b451edc32b8d12_21.png)

安装过程非常简单，我们可以使用YUM包管理器进行安装。

![](img/a7dd083a85765a3db9b451edc32b8d12_23.png)

![](img/a7dd083a85765a3db9b451edc32b8d12_24.png)

以下是安装命令：
```bash
# 在服务端安装VSFTPD
yum install -y vsftpd

# 在客户端安装FTP客户端工具lftp
yum install -y lftp
```
从RHEL/CentOS 7开始，系统默认使用`lftp`作为FTP客户端。它是一个功能强大的工具，支持FTP、HTTP、HTTPS等多种协议，并具有命令补全、历史记录、多任务后台执行等功能。

![](img/a7dd083a85765a3db9b451edc32b8d12_26.png)

安装完成后，服务端的主配置文件位于`/etc/vsftpd/vsftpd.conf`。此外，还有两个重要的名单文件：
*   `/etc/vsftpd/user_list`：用户列表文件，可配置为黑名单或白名单。
*   `/etc/vsftpd/ftpusers`：默认的黑名单文件，用于禁止某些系统用户登录FTP。

启动服务并设置开机自启：
```bash
systemctl start vsftpd
systemctl enable vsftpd
```
使用`netstat -antp | grep vsftpd`命令可以查看到21端口已在监听。

---

![](img/a7dd083a85765a3db9b451edc32b8d12_28.png)

![](img/a7dd083a85765a3db9b451edc32b8d12_30.png)

## 实战一：配置匿名访问FTP 🕶️

![](img/a7dd083a85765a3db9b451edc32b8d12_32.png)

上一节我们安装了VSFTPD，本节我们来配置一个允许所有员工上传下载文件的匿名访问FTP。

**场景需求**：允许匿名用户登录，并拥有上传、创建目录的权限。

![](img/a7dd083a85765a3db9b451edc32b8d12_34.png)

以下是核心配置步骤：

![](img/a7dd083a85765a3db9b451edc32b8d12_36.png)

![](img/a7dd083a85765a3db9b451edc32b8d12_38.png)

1.  **备份配置文件**：修改前先备份是好习惯。
    ```bash
    cp -a /etc/vsftpd/vsftpd.conf /etc/vsftpd/vsftpd.conf.back
    ```

![](img/a7dd083a85765a3db9b451edc32b8d12_40.png)

![](img/a7dd083a85765a3db9b451edc32b8d12_42.png)

2.  **编辑主配置文件** `/etc/vsftpd/vsftpd.conf`，确保以下参数设置正确：
    ```bash
    # 允许匿名登录
    anonymous_enable=YES
    # 允许匿名用户上传文件
    anon_upload_enable=YES
    # 允许匿名用户创建目录
    anon_mkdir_write_enable=YES
    # 允许匿名用户重命名和删除文件 (谨慎开启！)
    anon_other_write_enable=YES
    ```
    `anon_other_write_enable=YES`会赋予匿名用户很大权限，生产环境请谨慎评估。

![](img/a7dd083a85765a3db9b451edc32b8d12_44.png)

3.  **设置共享目录权限**：VSFTPD默认的匿名共享目录是`/var/ftp/pub`。需要将其属主改为FTP用户，并确保目录有相应权限。
    ```bash
    chown -R ftp:ftp /var/ftp/pub
    chmod -R 755 /var/ftp/pub
    ```

![](img/a7dd083a85765a3db9b451edc32b8d12_46.png)

4.  **重启服务使配置生效**：
    ```bash
    systemctl restart vsftpd
    ```

![](img/a7dd083a85765a3db9b451edc32b8d12_48.png)

![](img/a7dd083a85765a3db9b451edc32b8d12_50.png)

配置完成后，客户端无需输入用户名密码即可访问，并能进行上传、创建文件夹等操作。但请注意，开放`anon_other_write_enable`会带来安全风险。

---

![](img/a7dd083a85765a3db9b451edc32b8d12_52.png)

## 实战二：配置加密访问FTP 🔐

上一节我们配置了权限过大的匿名访问，本节我们来看更安全的加密访问方式，即使用系统用户账号登录。

![](img/a7dd083a85765a3db9b451edc32b8d12_54.png)

**场景需求**：公司内部使用FTP维护网站，仅允许`team1`和`team2`账号登录FTP，且不能登录Linux系统。需要将这两个账号的活动范围限制在网站根目录`/var/www/html`内。

以下是配置步骤：

1.  **创建无法登录系统的用户**：
    ```bash
    useradd -s /sbin/nologin team1
    useradd -s /sbin/nologin team2
    echo “123456” | passwd --stdin team1
    echo “123456” | passwd --stdin team2
    ```

2.  **编辑主配置文件** `/etc/vsftpd/vsftpd.conf`：
    ```bash
    # 禁止匿名登录
    anonymous_enable=NO
    # 允许本地用户登录
    local_enable=YES
    # 允许本地用户有写权限
    write_enable=YES
    # 激活chroot功能，将用户限制在其家目录
    chroot_local_user=YES
    # 设置chroot列表文件，此文件中列出的用户将被限制
    chroot_list_enable=YES
    chroot_list_file=/etc/vsftpd/chroot_list
    # 允许被限制的用户有写权限
    allow_writeable_chroot=YES
    ```

3.  **创建并编辑chroot列表文件**：
    ```bash
    echo -e “team1\nteam2” > /etc/vsftpd/chroot_list
    ```

4.  **创建网站目录并设置权限**：确保`team1`和`team2`用户能写入。
    ```bash
    mkdir -p /var/www/html
    chmod -R o+w /var/www/html
    ```

5.  **重启服务**：
    ```bash
    systemctl restart vsftpd
    ```

配置完成后，客户端需要使用`team1`或`team2`账号及密码登录。登录后，用户将被锁定在`/var/www/html`目录下，无法访问上级目录，从而增强了安全性。

---

## 总结 📝

本节课我们一起学习了Linux下FTP服务器的搭建与配置。

![](img/a7dd083a85765a3db9b451edc32b8d12_56.png)

我们首先了解了FTP的作用、VSFTPD的特点以及FTP主动与被动两种工作模式的区别。接着，我们完成了VSFTPD服务端和客户端的安装。

![](img/a7dd083a85765a3db9b451edc32b8d12_58.png)

通过两个实战项目，我们掌握了两种常见的配置场景：
1.  **匿名访问配置**：实现了无需认证的文件共享，但需特别注意权限控制，避免安全风险。
2.  **加密访问配置**：使用本地用户账号登录，并通过`chroot`机制将用户活动限制在特定目录，大大提升了安全性。

![](img/a7dd083a85765a3db9b451edc32b8d12_60.png)

![](img/a7dd083a85765a3db9b451edc32b8d12_62.png)

这些技能是构建企业内部文件共享服务或Web发布平台的基础。请务必在实验环境中多加练习，理解每个参数的含义。