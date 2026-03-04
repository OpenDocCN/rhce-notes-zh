# Linux服务管理：P9：FTP服务配置与匿名用户可写设置 🖥️

在本节课中，我们将学习FTP服务器的基本概念，并重点实践如何安装、配置VSFTPD服务，特别是设置匿名用户的上传和写入权限。

## FTP服务概述

FTP（File Transfer Protocol Server）是互联网上提供文件存储和访问服务的计算机，它们依照FTP协议提供服务。其作用是在Internet上传输文件。

常见的FTP服务软件有很多，例如Windows上的Serv-U，以及Linux上的ProFTPD等。本节课我们使用的是VSFTPD。

VSFTPD（Very Secure FTP Daemon）是一个基于GPL发布的、在Linux系统上使用的FTP服务器软件。其名称体现了开发者对代码安全的初衷，其特点是安全、高效、稳定。

FTP服务采用C/S（客户端/服务器）架构。其默认使用**20**和**21**两个端口：
*   **21端口**：控制端口，用于建立连接和传输命令。
*   **20端口**：数据端口，用于传输文件数据。

![](img/70b7bb7f7e59af22455c1ea071afd688_1.png)

## FTP工作模式

![](img/70b7bb7f7e59af22455c1ea071afd688_3.png)

FTP包含控制通道和数据传输通道。其工作模式主要分为两种：

以下是两种工作模式的简要说明：
*   **主动模式**：服务器使用固定的20端口主动连接客户端进行数据传输。
*   **被动模式**：服务器开启一个随机端口，等待客户端连接进行数据传输。

默认情况下，VSFTPD通常运行在主动模式。

## 安装VSFTPD服务

我们将在服务端安装`vsftpd`软件包，在客户端（用于测试）安装`lftp`命令行工具。

```bash
# 在服务端安装vsftpd
yum install -y vsftpd

![](img/70b7bb7f7e59af22455c1ea071afd688_5.png)

# 在客户端安装lftp（也可在同一台机器上安装用于测试）
yum install -y lftp
```

![](img/70b7bb7f7e59af22455c1ea071afd688_7.png)

`lftp`是一个功能强大的文件传输工具，支持FTP、FTPS、HTTP、HTTPS等多种协议，具有命令补全、历史记录和多任务后台执行等功能。

## 配置文件解析

VSFTPD的主要配置文件位于`/etc/vsftpd/`目录下。

以下是几个关键配置文件及其作用：
*   **`vsftpd.conf`**：核心配置文件，所有服务参数在此设置。
*   **`ftpusers`**：黑名单文件，列在此文件中的用户**禁止**登录FTP服务器。
*   **`user_list`**：用户列表文件。根据`vsftpd.conf`中`userlist_deny`的配置（默认为YES），决定此文件是作为黑名单（禁止登录）还是白名单（仅允许列表中的用户登录）。

![](img/70b7bb7f7e59af22455c1ea071afd688_9.png)

匿名用户登录后的默认根目录是`/var/ftp/`。

![](img/70b7bb7f7e59af22455c1ea071afd688_11.png)

## 启动服务与初步测试

安装完成后，我们可以启动服务并进行初步连接测试。

![](img/70b7bb7f7e59af22455c1ea071afd688_13.png)

```bash
# 启动vsftpd服务
systemctl start vsftpd

![](img/70b7bb7f7e59af22455c1ea071afd688_15.png)

# 设置开机自启
systemctl enable vsftpd

# 检查服务端口监听状态（应看到21端口在监听）
netstat -antp | grep ftp
```

![](img/70b7bb7f7e59af22455c1ea071afd688_17.png)

启动服务后，可以使用`lftp`客户端或Windows文件资源管理器进行匿名访问测试（地址格式为`ftp://服务器IP`）。此时默认只有查看权限。

![](img/70b7bb7f7e59af22455c1ea071afd688_19.png)

## 配置匿名用户可写权限

上一节我们启动了基础服务，本节中我们来看看如何修改配置，实现匿名用户上传、创建目录等写入操作。

需求分析：允许所有员工匿名上传和下载文件。
1.  默认已允许匿名登录和下载。
2.  需要额外开启匿名用户的上传和创建目录权限。

需要修改`/etc/vsftpd/vsftpd.conf`配置文件中的以下参数：

![](img/70b7bb7f7e59af22455c1ea071afd688_21.png)

```bash
# 允许匿名用户登录
anonymous_enable=YES
# 允许本地用户登录（默认已开启，建议保留）
local_enable=YES
# 允许写入操作（默认已开启，建议保留）
write_enable=YES

# 允许匿名用户上传文件
anon_upload_enable=YES
# 允许匿名用户创建目录
anon_mkdir_write_enable=YES
# 允许匿名用户执行其他写入操作（如重命名、删除）
anon_other_write_enable=YES
```

![](img/70b7bb7f7e59af22455c1ea071afd688_23.png)

修改配置文件后，必须重启服务使配置生效。

![](img/70b7bb7f7e59af22455c1ea071afd688_25.png)

![](img/70b7bb7f7e59af22455c1ea071afd688_27.png)

```bash
systemctl restart vsftpd
```

![](img/70b7bb7f7e59af22455c1ea071afd688_29.png)

![](img/70b7bb7f7e59af22455c1ea071afd688_31.png)

## 设置目录权限

![](img/70b7bb7f7e59af22455c1ea071afd688_33.png)

仅修改配置文件还不够，因为匿名用户映射的系统用户是`ftp`，而默认的FTP根目录`/var/ftp/`及其下的`pub`子目录的所有者是`root`，`ftp`用户没有写入权限。

以下是设置目录权限的步骤：
1.  确保匿名用户的可写目录（通常是`/var/ftp/pub`）的所有者和组为`ftp`。
2.  为该目录设置适当的权限，允许`ftp`用户写入。

![](img/70b7bb7f7e59af22455c1ea071afd688_35.png)

![](img/70b7bb7f7e59af22455c1ea071afd688_37.png)

```bash
# 更改/var/ftp/pub目录的所有者为ftp用户
chown ftp:ftp /var/ftp/pub/
```

![](img/70b7bb7f7e59af22455c1ea071afd688_39.png)

完成以上所有步骤后，匿名用户即可通过FTP客户端成功连接，并在`pub`目录下进行上传、创建文件夹、重命名和删除文件等操作。

![](img/70b7bb7f7e59af22455c1ea071afd688_41.png)

![](img/70b7bb7f7e59af22455c1ea071afd688_43.png)

## 课程总结

![](img/70b7bb7f7e59af22455c1ea071afd688_45.png)

![](img/70b7bb7f7e59af22455c1ea071afd688_47.png)

本节课中我们一起学习了FTP协议的基本概念、VSFTPD服务的安装与启动。我们重点实践了通过编辑`vsftpd.conf`配置文件，开启`anon_upload_enable`、`anon_mkdir_write_enable`和`anon_other_write_enable`等参数，并结合系统目录权限设置（`chown ftp:ftp`），最终实现了匿名用户对FTP服务器的可写访问。这是构建基础文件共享服务的关键一步。