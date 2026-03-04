# Linux运维教程：P58：文件共享服务FTP、vsftpd安装与使用 🖥️📁

![](img/9eeb41f57467972502c8653c53baca9b_1.png)

![](img/9eeb41f57467972502c8653c53baca9b_3.png)

## 概述
在本节课中，我们将学习企业内部的文件共享服务FTP，特别是vsftpd软件的安装、配置与基本使用。我们将了解FTP的基本概念、工作原理，并动手搭建一个简单的FTP服务器。

---

## FTP服务简介
上一节我们介绍了网络服务的基本概念，本节中我们来看看FTP。FTP是一种古老但仍在使用的文件传输方式。

FTP的全称是 **File Transfer Protocol**（文件传输协议）。它的功能类似于个人使用的百度网盘，用于传输各种类型的文件，例如：
*   MP3、MP4等音视频文件
*   JPG、PNG、GIF等图片文件
*   .txt、.sh、.py、.java、.html、.json等文本或脚本文件

在企业环境中，出于数据保密性考虑，通常不会使用公网网盘，而是在内网搭建自己的FTP服务器，实现安全可控的文件共享。

---

## FTP架构与端口
FTP采用 **C/S（Client/Server）** 架构。
*   **服务端（Server）**：提供文件共享服务。
*   **客户端（Client）**：连接服务器以下载或上传文件。

![](img/9eeb41f57467972502c8653c53baca9b_5.png)

FTP协议默认使用两个端口：
*   **21端口**：命令端口，用于接收客户端的指令（如登录、列出文件列表）。
*   **20端口**：数据端口，用于实际的文件数据传输。

![](img/9eeb41f57467972502c8653c53baca9b_7.png)

![](img/9eeb41f57467972502c8653c53baca9b_9.png)

---

![](img/9eeb41f57467972502c8653c53baca9b_11.png)

![](img/9eeb41f57467972502c8653c53baca9b_13.png)

![](img/9eeb41f57467972502c8653c53baca9b_15.png)

![](img/9eeb41f57467972502c8653c53baca9b_17.png)

![](img/9eeb41f57467972502c8653c53baca9b_19.png)

## FTP工作模式
FTP有两种主要的工作模式，区别在于数据连接由谁主动发起。

![](img/9eeb41f57467972502c8653c53baca9b_21.png)

![](img/9eeb41f57467972502c8653c53baca9b_23.png)

### 主动模式（Port Mode）
在主动模式下：
1.  客户端通过随机端口（如1024）向服务器的21端口发起连接，并发送命令。
2.  客户端在命令中告知服务器：“我的数据端口是1025”。
3.  服务器收到指令后，**主动**从自己的20端口连接到客户端指定的1025端口，建立数据连接进行传输。

![](img/9eeb41f57467972502c8653c53baca9b_25.png)

![](img/9eeb41f57467972502c8653c53baca9b_27.png)

**潜在问题**：如果客户端开启了防火墙，可能会阻止服务器发起的连接。

![](img/9eeb41f57467972502c8653c53baca9b_29.png)

![](img/9eeb41f57467972502c8653c53baca9b_31.png)

![](img/9eeb41f57467972502c8653c53baca9b_33.png)

### 被动模式（Passive Mode）
在被动模式下：
1.  客户端向服务器的21端口发起连接，并发送`PASV`命令进入被动模式。
2.  服务器随机开启一个高端口（如5000），并告知客户端：“我的数据端口是5000”。
3.  客户端**主动**连接到服务器的5000端口，建立数据连接进行传输。

![](img/9eeb41f57467972502c8653c53baca9b_35.png)

![](img/9eeb41f57467972502c8653c53baca9b_37.png)

**vsftpd默认使用被动模式**。这种模式下，需要确保服务器防火墙放行了FTP服务及相关数据端口范围。

---

## vsftpd软件介绍
vsftpd（Very Secure FTP Daemon）是一款运行在Linux系统上、开源且安全的FTP服务器软件。

![](img/9eeb41f57467972502c8653c53baca9b_39.png)

使用以下命令可以查看已安装的vsftpd信息：
```bash
rpm -qi vsftpd
```

![](img/9eeb41f57467972502c8653c53baca9b_41.png)

vsftpd支持三种用户访问模式：
1.  **本地用户模式**：用户使用服务器系统账户（如`useradd`创建的账户）登录。
2.  **匿名用户模式**：用户使用统一的`ftp`或`anonymous`账户登录，无需密码或使用任意邮箱作为密码。
3.  **虚拟用户模式**：用户信息存储在独立的数据库（如MySQL）中，安全性更高，配置更复杂。

![](img/9eeb41f57467972502c8653c53baca9b_43.png)

![](img/9eeb41f57467972502c8653c53baca9b_45.png)

![](img/9eeb41f57467972502c8653c53baca9b_47.png)

**企业常用匿名用户模式**，方便内网成员访问公共资源。

![](img/9eeb41f57467972502c8653c53baca9b_49.png)

![](img/9eeb41f57467972502c8653c53baca9b_51.png)

![](img/9eeb41f57467972502c8653c53baca9b_53.png)

---

![](img/9eeb41f57467972502c8653c53baca9b_55.png)

![](img/9eeb41f57467972502c8653c53baca9b_57.png)

![](img/9eeb41f57467972502c8653c53baca9b_59.png)

## 安装与启动vsftpd
以下是在服务端安装和配置vsftpd的步骤。

![](img/9eeb41f57467972502c8653c53baca9b_61.png)

![](img/9eeb41f57467972502c8653c53baca9b_63.png)

1.  **安装vsftpd软件包**：
    ```bash
    yum install -y vsftpd
    ```

2.  **启动服务并设置开机自启（生产环境建议）**：
    ```bash
    systemctl start vsftpd
    systemctl enable vsftpd
    ```
    *学习环境中，可以不用设置`enable`，需要时手动启动即可。*

![](img/9eeb41f57467972502c8653c53baca9b_65.png)

![](img/9eeb41f57467972502c8653c53baca9b_67.png)

3.  **检查服务状态**：
    ```bash
    systemctl status vsftpd
    ```

4.  **配置防火墙**：
    如果服务器开启了`firewalld`防火墙，需要放行FTP服务。
    ```bash
    firewall-cmd --permanent --add-service=ftp
    firewall-cmd --reload
    ```
    *为了方便实验，也可以直接关闭防火墙：`systemctl stop firewalld`。*

---

![](img/9eeb41f57467972502c8653c53baca9b_69.png)

## 客户端访问FTP服务器
客户端需要安装FTP客户端工具来连接服务器。

![](img/9eeb41f57467972502c8653c53baca9b_71.png)

![](img/9eeb41f57467972502c8653c53baca9b_73.png)

![](img/9eeb41f57467972502c8653c53baca9b_75.png)

![](img/9eeb41f57467972502c8653c53baca9b_77.png)

![](img/9eeb41f57467972502c8653c53baca9b_79.png)

以下是常用的客户端工具及连接方法：

![](img/9eeb41f57467972502c8653c53baca9b_81.png)

![](img/9eeb41f57467972502c8653c53baca9b_83.png)

![](img/9eeb41f57467972502c8653c53baca9b_85.png)

![](img/9eeb41f57467972502c8653c53baca9b_86.png)

1.  **安装客户端工具**（例如`lftp`，它是`ftp`的增强版）：
    ```bash
    yum install -y lftp
    ```

![](img/9eeb41f57467972502c8653c53baca9b_88.png)

2.  **使用`lftp`连接服务器**（假设服务器IP是192.168.0.40）：
    ```bash
    lftp 192.168.0.40
    ```
    连接后，默认以匿名用户身份登录，会进入服务器的默认共享目录`/var/ftp/pub/`。

![](img/9eeb41f57467972502c8653c53baca9b_90.png)

3.  **基本操作命令**：
    在`lftp`提示符下，可以执行很多命令：
    *   `ls`：列出服务器上的文件和目录。
    *   `cd [目录名]`：切换服务器上的目录。
    *   `get [文件名]`：下载文件到客户端当前目录。
    *   `put [文件名]`：上传客户端文件到服务器（需要服务器配置权限）。
    *   `mirror`：下载整个目录。
    *   `!ls`：在命令前加`!`可以执行客户端的本地shell命令。
    *   `help`：查看`lftp`支持的所有命令。
    *   `exit` 或 `bye`：退出`lftp`。

---

## 配置匿名用户上传权限
默认情况下，匿名用户只有下载权限。如果需要允许上传，需要在服务端进行配置。

![](img/9eeb41f57467972502c8653c53baca9b_92.png)

![](img/9eeb41f57467972502c8653c53baca9b_94.png)

1.  **修改vsftpd主配置文件**：
    ```bash
    vi /etc/vsftpd/vsftpd.conf
    ```

2.  找到并修改或添加以下行（确保行首没有`#`注释符）：
    ```bash
    # 允许匿名用户上传文件
    anon_upload_enable=YES
    # 允许匿名用户创建目录
    anon_mkdir_write_enable=YES
    # 设置匿名用户上传文件的默认权限（例如644）
    anon_umask=022
    ```

3.  **设置共享目录权限**：
    vsftpd匿名用户的默认根目录是`/var/ftp`。通常我们使用其下的`pub`子目录作为公共空间。
    ```bash
    # 将/var/ftp/pub目录的所有者改为ftp用户
    chown ftp:ftp /var/ftp/pub/
    # 为该目录添加写权限
    chmod 775 /var/ftp/pub/
    ```

4.  **重启vsftpd服务使配置生效**：
    ```bash
    systemctl restart vsftpd
    ```

配置完成后，客户端匿名登录即可在`pub`目录内进行上传、创建文件夹等操作。

---

![](img/9eeb41f57467972502c8653c53baca9b_96.png)

![](img/9eeb41f57467972502c8653c53baca9b_98.png)

## 总结
本节课我们一起学习了文件共享服务FTP的核心知识：
1.  了解了FTP协议的作用、C/S架构以及20/21端口的分工。
2.  理解了FTP的**主动**与**被动**两种工作模式及其区别。
3.  认识了**vsftpd**这款安全、高效的FTP服务器软件及其三种用户模式。
4.  完成了vsftpd服务的安装、启动和防火墙配置。
5.  学会了使用`lftp`客户端工具连接服务器并进行基本的文件操作。
6.  通过修改配置文件和目录权限，实现了匿名用户的上传功能。

![](img/9eeb41f57467972502c8653c53baca9b_100.png)

![](img/9eeb41f57467972502c8653c53baca9b_102.png)

![](img/9eeb41f57467972502c8653c53baca9b_104.png)

![](img/9eeb41f57467972502c8653c53baca9b_106.png)

![](img/9eeb41f57467972502c8653c53baca9b_108.png)

通过本课实践，你已经掌握了在企业内部搭建基础文件共享服务的能力。