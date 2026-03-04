# Linux运维培训教程：1.5：文件共享服务FTP、vsftpd安装与使用 🗂️

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_1.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_3.png)

在本节课中，我们将要学习企业内部的文件共享服务FTP，特别是vsftpd软件的安装与基本使用。FTP服务类似于企业内部的“百度网盘”，用于在内部网络中安全地共享和传输文件。

## 概述

文件共享服务FTP和NFS是比较古老的文件传输方式。它的功能类似于我们日常使用的百度网盘，用于共享文件。但在企业环境中，出于数据保密性的考虑，通常不会使用公网上的网盘服务，而是选择在企业内部搭建自己的文件共享服务器。这样，数据就存储在自己的服务器上，而非第三方平台。

FTP的全称是File Transfer Protocol（文件传输协议）。它是一个用于在网络上进行文件传输的协议，可以传输各种类型的文件，如音乐、视频、脚本、图片、文本等。在企业内部，当需要进行数据传输时，通常会使用FTP协议，而不是用于访问网站的HTTP协议。

FTP是一种基于TCP协议的端到端数据传输协议。这意味着客户端和服务端在建立连接时，需要通过TCP协议的三次握手机制来确保连接的可靠性。FTP采用客户端-服务器（C/S）架构，并默认使用20和21两个端口。其中，20端口是数据端口，用于实际的数据传输；21端口是命令端口，用于接收客户端的指令。

## FTP的工作模式

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_5.png)

FTP协议有两种主要的工作模式：主动模式和被动模式。理解这两种模式有助于解决可能遇到的网络连接问题，特别是在防火墙环境中。

### 主动模式

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_7.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_9.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_11.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_13.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_15.png)

在主动模式下，工作流程如下：
1.  客户端使用一个随机端口（例如1024）作为命令端口，连接服务端的21号命令端口，并发送指令（例如请求下载文件）。在指令中，客户端会告知服务端自己的数据端口号（例如1025）。
2.  服务端收到指令后，会用自己的20号数据端口，主动去连接客户端告知的数据端口（1025）。
3.  连接建立后，服务端通过这个连接将文件数据传输给客户端。

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_17.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_19.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_21.png)

**核心特点**：数据传输的连接是由**服务端主动向客户端发起**的。
**潜在问题**：如果客户端开启了防火墙，并且没有放行来自服务端20号端口的入站连接，那么这个数据连接可能会被防火墙拦截。

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_23.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_25.png)

### 被动模式

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_27.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_29.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_31.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_33.png)

在被动模式下，工作流程如下：
1.  客户端连接服务端的21号命令端口，并发送`PASV`指令，表示希望进入被动模式。
2.  服务端收到指令后，会随机开启一个端口（例如5000）作为数据端口，并将这个端口号告知客户端。
3.  客户端收到端口号后，主动用自己的数据端口去连接服务端的这个随机数据端口（5000）。
4.  连接建立后，数据传输通过这个连接进行。

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_35.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_36.png)

**核心特点**：数据传输的连接是由**客户端主动向服务端发起**的。
**优势**：这种模式通常能更好地适应客户端的防火墙环境，因为连接方向与常见的上网行为（客户端主动向外请求）一致。vsftpd默认使用被动模式。

## 实现FTP的软件

要在Linux系统上搭建FTP服务器，可以选择多种软件，例如：
*   Wuftpd：非常古老的FTP软件。
*   Proftpd：功能专业的FTP软件。
*   **vsftpd**：非常安全的FTP守护进程，也是本节教程将要使用的软件。

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_38.png)

在Windows系统上，则有如Serv-U等软件。对于客户端，无论是Linux还是Windows，都有相应的工具来访问FTP服务器，例如Linux下的`ftp`、`lftp`命令，或Windows下的FileZilla等图形化工具。

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_40.png)

## vsftpd的安装与基本配置

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_42.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_44.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_46.png)

vsftpd（Very Secure FTP Daemon）是一款在Linux上常用的、开源且安全的FTP服务器软件。

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_48.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_50.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_51.png)

### 安装vsftpd

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_53.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_55.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_57.png)

在服务端（例如主机名为`ftp-server`的机器）执行以下命令进行安装：
```bash
yum install -y vsftpd
```

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_59.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_60.png)

### 启动服务并设置防火墙

安装完成后，启动vsftpd服务并设置为开机自启（学习环境可跳过自启）：
```bash
systemctl start vsftpd
systemctl enable vsftpd
```
如果系统防火墙（firewalld）是开启状态，需要放行FTP服务，或者直接关闭防火墙（仅建议在实验环境这样做）：
```bash
# 方法一：放行FTP服务
firewall-cmd --permanent --add-service=ftp
firewall-cmd --reload

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_62.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_64.png)

# 方法二：关闭防火墙
systemctl stop firewalld
```
> **注意**：如果使用`iptables`，默认规则通常不会阻止FTP连接，一般无需额外配置。

### 客户端连接测试

在客户端机器上，我们可以安装`lftp`这个更先进的FTP客户端工具进行测试：
```bash
yum install -y lftp
```
然后连接FTP服务器（假设服务器IP为192.168.0.40）：
```bash
lftp 192.168.0.40
```
连接成功后，会进入`lftp`的命令行界面。此时，我们已经以“匿名用户”身份登录到了服务器。

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_66.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_68.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_70.png)

## vsftpd的用户访问模式

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_71.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_73.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_75.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_77.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_79.png)

vsftpd支持三种用户访问模式，用于控制客户端的认证方式：

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_81.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_82.png)

1.  **匿名用户模式**：用户无需输入用户名和密码即可访问。这是默认模式，访问时会映射到系统内置的`ftp`用户。
2.  **本地用户模式**：用户必须输入服务器上存在的真实系统用户名和密码才能访问。
3.  **虚拟用户模式**：用户使用存储在数据库（如MySQL）中的账号进行认证，而非系统账号。安全性更高，配置也更复杂。

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_84.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_86.png)

我们当前使用的默认配置即为**匿名用户模式**。匿名用户登录后，访问的根目录是服务器上的`/var/ftp/`目录。

## 匿名模式下的文件操作

### 查看与下载文件

在`lftp`命令行中，可以使用常见的Linux命令来操作：
*   `ls`：列出服务器共享目录下的文件。
*   `get 文件名`：下载指定文件到客户端当前目录。
例如，服务器`/var/ftp/pub/`目录下有一个`hello.txt`文件，我们可以这样下载：
```bash
lftp 192.168.0.40:~> cd pub
lftp 192.168.0.40:/pub> ls
lftp 192.168.0.40:/pub> get hello.txt
```

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_88.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_90.png)

### 上传文件与权限配置

默认情况下，匿名用户可能没有上传文件的权限。我们需要进行一些配置。

**第一步：设置共享目录权限**
建议在`/var/ftp/pub/`目录下进行操作，并确保该目录的所有者和权限正确：
```bash
# 在FTP服务器上执行
chown ftp:ftp /var/ftp/pub/
```
这样，`ftp`用户就对`/var/ftp/pub/`目录拥有了所有权。

**第二步：修改vsftpd配置文件，允许匿名用户上传**
编辑vsftpd的主配置文件`/etc/vsftpd/vsftpd.conf`：
```bash
vim /etc/vsftpd/vsftpd.conf
```
找到并取消以下两行配置的注释（删除行首的`#`）：
```bash
anon_upload_enable=YES
anon_mkdir_write_enable=YES
```
保存退出后，重启vsftpd服务使配置生效：
```bash
systemctl restart vsftpd
```

**第三步：进行上传操作**
现在，回到客户端`lftp`，就可以尝试上传文件了：
```bash
lftp 192.168.0.40:~> cd pub
lftp 192.168.0.40:/pub> put /path/to/your/local/file.txt  # 上传本地文件
lftp 192.168.0.40:/pub> mkdir new_folder                  # 创建新目录
```
常用的`lftp`命令总结如下：
*   `put`：上传单个文件。
*   `mput`：上传多个文件（支持通配符`*`）。
*   `get`：下载单个文件。
*   `mget`：下载多个文件。
*   `mkdir`：创建目录。
*   `rm`：删除文件。
*   `exit` 或 `bye`：退出`lftp`。

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_92.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_94.png)

## 总结

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_96.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_98.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_100.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_102.png)

![](img/9dccedc61d2d5af379d8c4b833b7bdc4_104.png)

本节课我们一起学习了企业级文件共享服务FTP的基础知识。我们了解了FTP协议的作用、C/S架构及其两种工作模式（主动与被动）。重点实践了使用**vsftpd**软件在Linux上快速搭建FTP服务器，并通过**匿名访问模式**让客户端能够连接、浏览、下载以及在上传权限配置完成后进行文件上传。我们还熟悉了功能强大的客户端工具`lftp`的基本命令。这为在企业内部部署安全的文件共享服务打下了坚实的基础。