# Linux运维全套培训课程：P58：红帽RHCE-21.文件共享服务FTP、vsftpd安装与使用 🖥️📁

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_1.png)

## 概述
在本节课中，我们将学习企业环境中常用的文件共享服务——FTP。我们将了解FTP的基本概念、工作原理，并重点学习如何在Linux系统上安装、配置和使用vsftpd软件来搭建一个安全的FTP服务器。通过本课，你将能够理解并实践企业内部文件共享的搭建与管理。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_3.png)

---

## FTP服务简介
上一节我们概述了课程内容，本节中我们来看看什么是FTP服务。

FTP，全称File Transfer Protocol（文件传输协议），是一种用于在网络上进行文件传输的协议。它的功能类似于我们个人使用的百度网盘，但主要应用于企业内部，用于安全地共享和传输文件。

在企业中，出于数据保密性考虑，通常不会将敏感数据存放在公网上的网盘（如百度网盘），而是选择在企业内部网络搭建自己的文件共享服务器。这样，所有数据都存储在企业自己的服务器上，确保了数据的安全与可控。

FTP协议可以传输各种类型的文件，例如：
*   **音频/视频文件**：如 `mp3`, `mp4`
*   **脚本文件**：如 `.sh`, `.py`
*   **图片文件**：如 `.jpg`, `.png`, `.gif`
*   **文本文件**：如 `.txt`
*   **代码文件**：如 `.java`, `.html`, `.xml`, `.json`

简单来说，FTP就是企业内部的“百度网盘”，专门用于文件传输。

---

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_5.png)

## FTP架构与工作模式
了解了FTP的基本功能后，本节我们来看看它的技术架构和两种核心工作模式。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_7.png)

FTP采用经典的**客户端-服务器（C/S）** 架构。
*   **客户端（Client）**：用于连接服务器并下载或上传文件。
*   **服务器（Server）**：用于存储和共享文件，响应客户端的请求。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_9.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_11.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_13.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_15.png)

FTP协议默认使用两个端口：
*   **21端口**：命令端口，用于接收客户端的指令（如登录、请求文件列表）。
*   **20端口**：数据端口，用于实际的文件数据传输。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_17.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_19.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_21.png)

FTP有两种主要的工作模式，区别在于**数据传输连接**的建立方式不同。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_23.png)

### 主动模式（Port Mode）
在主动模式下：
1.  客户端使用一个随机端口（如1024）连接服务器的21号命令端口，并发送指令（如请求下载文件）。
2.  在指令中，客户端会告知服务器：“我的数据端口是1025”。
3.  服务器收到指令后，主动从自己的20号数据端口，去连接客户端告知的1025号数据端口，建立数据传输通道。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_25.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_27.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_29.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_31.png)

**核心特点**：数据传输连接是由**服务器端主动发起**的。
**潜在问题**：如果客户端开启了防火墙，可能会阻止服务器发起的连接请求，导致数据传输失败。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_33.png)

### 被动模式（Passive Mode）
在被动模式下：
1.  客户端连接服务器的21号命令端口，并发送`PASV`指令，进入被动模式。
2.  服务器收到指令后，随机开启一个高端口（如5000）作为数据端口，并告知客户端：“我的数据端口是5000”。
3.  客户端收到信息后，主动用自己的一个随机端口去连接服务器的5000号数据端口，建立数据传输通道。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_35.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_37.png)

**核心特点**：数据传输连接是由**客户端主动发起**的。
**优点**：可以避免客户端防火墙对入站连接的拦截，兼容性更好。**vsftpd默认采用被动模式**。

---

## 常见FTP软件
在明确了FTP的工作原理后，我们可以选择相应的软件来搭建服务。以下是常见的FTP服务器端与客户端软件。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_39.png)

**FTP服务器端软件（安装在提供共享的机器上）：**
*   `wu-ftpd`：非常古老的FTP软件。
*   `ProFTPD`：功能丰富的专业FTP软件。
*   `vsftpd`：**非常安全的FTP守护进程**，在Linux中应用广泛。
*   `Pure-FTPd`：纯粹、高效的FTP软件。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_41.png)

**FTP客户端工具（安装在需要访问共享的机器上）：**
*   `ftp`：命令行基础工具。
*   `lftp`：`ftp`的增强版命令行工具，功能更强大。
*   `wget` / `curl`：支持FTP协议的命令行下载工具。
*   `FileZilla`：图形化界面的FTP客户端（支持Windows/Linux）。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_43.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_45.png)

在本课程中，我们将使用 **`vsftpd`** 来搭建FTP服务器。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_47.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_49.png)

---

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_51.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_53.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_55.png)

## vsftpd安装与基本使用
现在，我们开始动手安装和配置vsftpd服务器。我们将使用两台虚拟机进行实验：
*   **服务端**：IP为 `192.168.0.40`，主机名设为 `ftp-server`。
*   **客户端**：IP为 `192.168.0.41`，主机名设为 `ftp-client`。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_57.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_59.png)

### 服务端：安装与启动vsftpd
首先，在服务端进行操作。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_61.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_63.png)

1.  **修改主机名并安装vsftpd**：
    ```bash
    hostnamectl set-hostname ftp-server
    yum install -y vsftpd
    ```

2.  **启动服务并设置开机自启（生产环境建议，学习环境可选）**：
    ```bash
    systemctl start vsftpd          # 启动服务
    systemctl enable vsftpd         # 设置开机自启
    systemctl status vsftpd         # 查看服务状态
    ```

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_65.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_67.png)

3.  **配置防火墙**：
    如果系统防火墙（`firewalld`）是开启状态，需要放行FTP服务。
    ```bash
    firewall-cmd --permanent --add-service=ftp  # 永久添加ftp服务规则
    firewall-cmd --reload                        # 重载防火墙配置
    # 或者，在学习环境中可以直接关闭防火墙
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### 客户端：使用lftp连接测试
接下来，在客户端进行操作。

1.  **安装lftp客户端工具**：
    ```bash
    yum install -y lftp
    ```

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_69.png)

2.  **连接FTP服务器**：
    使用`lftp`命令连接服务器，默认以匿名用户身份访问。
    ```bash
    lftp 192.168.0.40
    ```
    连接成功后，会进入`lftp`的命令行交互界面。输入`ls`命令，可以看到服务器共享目录下的内容（默认是一个名为`pub`的目录）。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_71.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_73.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_75.png)

---

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_77.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_79.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_81.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_83.png)

## vsftpd用户访问模式与配置
成功连接后，我们来深入了解vsftpd支持的三种用户访问模式。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_85.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_86.png)

vsftpd支持三种用户认证模式：
1.  **匿名用户模式**：无需用户名和密码即可访问。这是默认模式。
2.  **本地用户模式**：需要使用服务器系统上存在的真实用户名和密码进行认证。
3.  **虚拟用户模式**：使用独立的数据库文件来存储用户认证信息，安全性更高，配置更复杂。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_88.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_90.png)

**默认情况下，vsftpd启用的是匿名用户模式。** 匿名用户登录后，默认进入的目录是 `/var/ftp/`。其中，`/var/ftp/pub/` 是预设的公共共享目录。

### 匿名用户模式配置与管理
我们来看看如何管理匿名用户的共享目录。

*   **查看匿名用户根目录**：
    在服务端，匿名用户的根目录是 `/var/ftp`。
    ```bash
    ls -ld /var/ftp/
    ```
*   **共享文件**：
    只需将需要共享的文件放入 `/var/ftp/pub/` 目录即可。例如：
    ```bash
    echo "Hello from FTP Server" > /var/ftp/pub/hello.txt
    ```
*   **客户端查看与下载**：
    在客户端`lftp`连接中，进入`pub`目录并查看、下载文件。
    ```lftp
    cd pub
    ls
    get hello.txt  # 下载文件到客户端当前目录
    ```

### 启用匿名用户上传功能
默认情况下，匿名用户只有下载和查看的权限。如果需要允许匿名用户上传文件，需要修改配置文件。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_92.png)

1.  **编辑vsftpd主配置文件**：
    ```bash
    vim /etc/vsftpd/vsftpd.conf
    ```
2.  **找到并修改以下配置项（去掉行首的`#`注释符）**：
    ```bash
    # 允许匿名用户上传文件
    anon_upload_enable=YES
    # 允许匿名用户创建目录
    anon_mkdir_write_enable=YES
    # 设置匿名用户上传目录的写权限（确保目录属主是ftp用户）
    anon_other_write_enable=YES
    ```
3.  **修改共享目录权限**：
    确保 `/var/ftp/pub` 目录的权限允许`ftp`用户写入。
    ```bash
    chown ftp:ftp /var/ftp/pub/
    chmod 755 /var/ftp/pub/  # 或根据需求设置为 777
    ```
4.  **重启vsftpd服务使配置生效**：
    ```bash
    systemctl restart vsftpd
    ```
5.  **客户端测试上传**：
    在客户端`lftp`中，可以尝试上传文件。
    ```lftp
    cd pub
    put /path/to/your/local/file.txt  # 上传本地文件到服务器
    ```

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_94.png)

---

## 常用FTP客户端命令
在客户端使用`lftp`时，以下是一些常用命令：

**基本导航与文件操作：**
*   `ls`：列出远程目录文件。
*   `cd`：切换远程目录。
*   `pwd`：显示当前远程工作目录。
*   `mkdir`：在远程创建目录。
*   `rm`：删除远程文件。
*   `mv`：移动或重命名远程文件。

**文件传输命令：**
*   `get <file>`：下载单个文件。
*   `mget <pattern>`：使用通配符批量下载文件（如 `mget *.txt`）。
*   `put <file>`：上传单个文件。
*   `mput <pattern>`：使用通配符批量上传文件。

**本地操作命令（在`lftp`中操作客户端本地文件）：**
*   `!ls`：列出**本地**当前目录文件。
*   `lcd <dir>`：切换**本地**工作目录。
*   `lpwd`：显示**本地**当前工作目录。

**其他命令：**
*   `help`：查看`lftp`支持的所有命令。
*   `exit` 或 `bye`：断开连接并退出`lftp`。

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_96.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_98.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_100.png)

---

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_102.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_104.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_106.png)

![](img/e44ab4ecbf329ec0193d7d79d4bc31d8_108.png)

## 总结
本节课中我们一起学习了企业级文件共享服务FTP的核心知识。我们从FTP的基本概念和在企业中的应用场景讲起，深入分析了它的C/S架构、21/20端口分工以及主动/被动两种工作模式。随后，我们重点实践了使用`vsftpd`软件搭建FTP服务器的全过程，包括安装、启动、防火墙配置。我们还探讨了vsftpd的三种用户访问模式，并详细配置了匿名用户模式下的文件下载与上传功能。最后，我们介绍了在客户端使用`lftp`工具进行连接和文件操作的常用命令。通过本课的学习，你应该已经掌握了在企业内部搭建和管理一个基本FTP文件共享服务的能力。