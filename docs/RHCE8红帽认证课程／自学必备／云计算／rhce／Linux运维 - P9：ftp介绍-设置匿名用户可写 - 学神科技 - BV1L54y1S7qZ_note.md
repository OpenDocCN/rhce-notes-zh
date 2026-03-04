# RHCE8红帽认证课程：P9：FTP介绍与匿名用户可写设置 🖥️📁

在本节课中，我们将学习FTP（文件传输协议）服务器的基本概念，并重点实践如何安装和配置vsftpd服务，特别是设置允许匿名用户上传和下载文件的功能。

## FTP服务器简介

上一节我们介绍了课程的整体安排，本节中我们来看看FTP服务器。FTP是File Transfer Protocol的缩写，即文件传输协议。它是在互联网上提供文件存储和访问服务的计算机依据的协议。其作用是在Internet上传输文件。

常见的FTP服务器软件有很多，例如Windows平台上的Server-U，以及Linux/Unix平台上的ProFTPD。我们今天要学习的是vsftpd。

## vsftpd概述

![](img/c9594b2b681f01181aedae13e830bf86_1.png)

vsftpd代表“Very Secure FTP Daemon”。它是一个基于GPL协议发布的、在类Unix系统上使用的FTP服务器软件。从其名称可以看出，开发者的初衷是代码的安全性。它的特点是安全、高效、稳定。

![](img/c9594b2b681f01181aedae13e830bf86_3.png)

FTP服务采用C/S（客户端/服务器）架构。服务端监听端口，客户端连接服务端进行文件操作。FTP默认使用两个端口：
*   **21端口**：控制端口，用于建立连接和发送命令。
*   **20端口**：数据端口，用于传输文件数据。

您可以通过查看`/etc/services`文件来确认这些端口的定义。

## FTP工作模式

FTP有两种主要的工作模式：主动模式和被动模式。两者的区别在于建立数据连接的方式不同。

**主动模式**：
1.  客户端随机开启一个端口N（N>1024），连接服务器的21端口。
2.  客户端发送`PORT`命令，告知服务器自己的端口N，并声明使用主动模式。
3.  服务器使用20端口主动连接客户端的端口N，建立数据通道。

**被动模式**：
1.  客户端连接服务器的21端口。
2.  客户端发送`PASV`命令，声明使用被动模式。
3.  服务器在本地随机开启一个端口P，并告知客户端。
4.  客户端主动连接服务器的端口P，建立数据通道。

![](img/c9594b2b681f01181aedae13e830bf86_5.png)

默认情况下，vsftpd使用主动模式。

![](img/c9594b2b681f01181aedae13e830bf86_7.png)

## 安装vsftpd

接下来，我们开始安装vsftpd服务端和一款功能强大的命令行客户端lftp。

以下是安装步骤：
1.  安装vsftpd服务端软件包。
    ```bash
    yum install -y vsftpd
    ```
2.  安装lftp客户端工具。
    ```bash
    yum install -y lftp
    ```

安装完成后，主要的配置文件位于`/etc/vsftpd/`目录下。

![](img/c9594b2b681f01181aedae13e830bf86_9.png)

## 配置文件解析

![](img/c9594b2b681f01181aedae13e830bf86_11.png)

vsftpd有几个重要的配置文件：

*   **`/etc/vsftpd/vsftpd.conf`**：核心配置文件，所有主要设置都在这里。
*   **`/etc/vsftpd/ftpusers`**：黑名单文件，列在此文件中的用户禁止登录FTP。
*   **`/etc/vsftpd/user_list`**：用户列表文件。其生效规则由`vsftpd.conf`中的`userlist_deny`参数决定。默认`YES`时，此文件为黑名单；设置为`NO`时，则变为白名单。
*   **`/var/ftp/`**：匿名用户（anonymous）登录后的默认根目录。

![](img/c9594b2b681f01181aedae13e830bf86_13.png)

## 基础服务管理

![](img/c9594b2b681f01181aedae13e830bf86_15.png)

安装后，我们可以启动服务并测试基础连接。

以下是服务管理命令：
1.  启动vsftpd服务。
    ```bash
    systemctl start vsftpd
    ```
2.  设置vsftpd服务开机自启。
    ```bash
    systemctl enable vsftpd
    ```
3.  检查服务端口监听状态。
    ```bash
    netstat -antp | grep ftp
    ```
    通常只看到21端口在监听，20端口会在有数据传输时临时启用。

![](img/c9594b2b681f01181aedae13e830bf86_17.png)

![](img/c9594b2b681f01181aedae13e830bf86_19.png)

## 配置匿名用户可写

默认配置下，匿名用户可以登录，但只有下载权限。现在，我们将配置允许匿名用户上传、创建目录和修改文件。

需要修改`/etc/vsftpd/vsftpd.conf`配置文件中的相关参数。

以下是关键配置项与修改值：
```bash
anonymous_enable=YES        # 允许匿名登录
local_enable=YES            # 允许本地用户登录
write_enable=YES            # 开启全局写权限
anon_upload_enable=YES      # 允许匿名用户上传
anon_mkdir_write_enable=YES # 允许匿名用户创建目录
anon_other_write_enable=YES # 允许匿名用户执行重命名、删除等操作
```

![](img/c9594b2b681f01181aedae13e830bf86_21.png)

修改完成后，必须重启服务使配置生效。
```bash
systemctl restart vsftpd
```

## 设置目录权限

![](img/c9594b2b681f01181aedae13e830bf86_23.png)

仅修改配置文件还不够，还需要确保匿名用户的根目录（`/var/ftp/pub/`）具有正确的所有权，以便匿名用户（映射为系统用户`ftp`）能够写入。

![](img/c9594b2b681f01181aedae13e830bf86_25.png)

![](img/c9594b2b681f01181aedae13e830bf86_27.png)

以下是设置目录权限的命令：
```bash
chown ftp:ftp /var/ftp/pub/
```

![](img/c9594b2b681f01181aedae13e830bf86_29.png)

![](img/c9594b2b681f01181aedae13e830bf86_31.png)

## 连接测试

![](img/c9594b2b681f01181aedae13e830bf86_33.png)

配置完成后，我们可以使用多种方式进行测试。

![](img/c9594b2b681f01181aedae13e830bf86_35.png)

以下是测试方法：
1.  **使用lftp命令行客户端连接**：
    ```bash
    lftp 192.168.1.201
    ```
    连接后可以使用`ls`、`mkdir`、`put`等命令进行操作。
2.  **使用Windows文件资源管理器连接**：
    在地址栏输入：`ftp://服务器IP地址`，例如 `ftp://192.168.1.201`。
3.  **使用网页浏览器连接**：
    在地址栏输入同样的FTP地址。

![](img/c9594b2b681f01181aedae13e830bf86_37.png)

![](img/c9594b2b681f01181aedae13e830bf86_39.png)

现在，您应该可以在`/var/ftp/pub/`目录下创建文件夹、上传文件、重命名或删除文件了。

![](img/c9594b2b681f01181aedae13e830bf86_41.png)

![](img/c9594b2b681f01181aedae13e830bf86_43.png)

## 总结

![](img/c9594b2b681f01181aedae13e830bf86_45.png)

![](img/c9594b2b681f01181aedae13e830bf86_47.png)

本节课中我们一起学习了FTP协议的基础知识，安装了vsftpd服务器和lftp客户端。我们重点实践了如何通过修改`vsftpd.conf`配置文件和调整系统目录权限，来实现匿名用户对FTP服务器的完整读写访问。这是构建一个简单内部文件共享服务的核心步骤。