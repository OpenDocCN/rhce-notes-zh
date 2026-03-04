# Linux实战中级篇：1：Web服务器（一）概述

![](img/609602d0b83f713b90cffed920227c5c_1.png)

![](img/609602d0b83f713b90cffed920227c5c_3.png)

在本节课中，我们将要学习Linux服务器管理的基础，从最常用的Web服务器开始。我们将了解服务器的基本概念，重点介绍Apache HTTP服务器（httpd）的安装、配置和基本工作原理。通过本节课的学习，你将能够搭建一个简单的Web站点。

![](img/609602d0b83f713b90cffed920227c5c_5.png)

![](img/609602d0b83f713b90cffed920227c5c_6.png)

![](img/609602d0b83f713b90cffed920227c5c_7.png)

![](img/609602d0b83f713b90cffed920227c5c_8.png)

![](img/609602d0b83f713b90cffed920227c5c_10.png)

![](img/609602d0b83f713b90cffed920227c5c_11.png)

## 什么是服务器？

![](img/609602d0b83f713b90cffed920227c5c_12.png)

![](img/609602d0b83f713b90cffed920227c5c_13.png)

上一节我们介绍了课程的整体安排，本节中我们来看看“服务器”这个概念。服务器，简单来说，就是能够提供服务的设备或程序。

![](img/609602d0b83f713b90cffed920227c5c_15.png)

![](img/609602d0b83f713b90cffed920227c5c_16.png)

![](img/609602d0b83f713b90cffed920227c5c_17.png)

*   **硬件服务器**：指看得见、摸得着的物理计算机设备，例如塔式服务器、机架式服务器。
*   **软件服务器**：指安装在硬件服务器上，能够提供特定网络服务的软件程序。它能提供什么服务，就被称为什么服务器。例如，提供网站访问服务的软件就叫Web服务器。

![](img/609602d0b83f713b90cffed920227c5c_19.png)

![](img/609602d0b83f713b90cffed920227c5c_20.png)

![](img/609602d0b83f713b90cffed920227c5c_21.png)

![](img/609602d0b83f713b90cffed920227c5c_23.png)

## 常见的Web服务器

![](img/609602d0b83f713b90cffed920227c5c_25.png)

了解了服务器的分类后，我们来看看目前主流的Web服务器软件。

以下是三种广泛使用的Web服务器：

![](img/609602d0b83f713b90cffed920227c5c_27.png)

![](img/609602d0b83f713b90cffed920227c5c_29.png)

1.  **Microsoft IIS**：微软公司的Web服务器，主要运行在Windows操作系统平台上。
2.  **Apache HTTP Server (httpd)**：开源、跨平台的Web服务器，历史悠久，功能强大，在全球范围内市场占有率很高。
3.  **Nginx**：一款轻量级、高性能的HTTP和反向代理服务器，在中国市场非常流行，常用于高并发场景。

![](img/609602d0b83f713b90cffed920227c5c_31.png)

![](img/609602d0b83f713b90cffed920227c5c_32.png)

![](img/609602d0b83f713b90cffed920227c5c_33.png)

本课程将重点讲解Apache HTTP Server，因为它是红帽认证课程的标准组件，也是理解Web服务原理的绝佳起点。

## Apache HTTP Server 基础

### 安装与启动

![](img/609602d0b83f713b90cffed920227c5c_35.png)

默认情况下，RHEL/CentOS 7系统没有预装Apache。我们需要手动安装。

1.  检查是否已安装Apache：
    ```bash
    rpm -qa | grep httpd
    ```
2.  使用yum安装Apache软件包（`httpd`）及其依赖：
    ```bash
    yum install -y httpd
    ```
3.  设置开机自动启动并立即启动服务：
    ```bash
    systemctl enable httpd
    systemctl start httpd
    ```
4.  检查服务状态和监听端口：
    ```bash
    systemctl status httpd
    netstat -antulp | grep httpd
    ```
    你会看到`httpd`服务正在监听**TCP 80**端口。

### 进程模型

Apache采用主进程（Master Process）管理多个子进程（Worker Process）的模型来高效处理客户端请求。

![](img/609602d0b83f713b90cffed920227c5c_37.png)

*   **主进程 (root用户启动)**：负责管理服务，如读取配置、维护子进程池。
*   **子进程 (apache用户启动)**：实际处理客户端HTTP请求的工作进程。Apache会根据访问量动态调整子进程的数量。

![](img/609602d0b83f713b90cffed920227c5c_39.png)

使用`ps aux | grep httpd`命令可以查看这些进程。

![](img/609602d0b83f713b90cffed920227c5c_41.png)

### 核心概念：端口与协议

![](img/609602d0b83f713b90cffed920227c5c_43.png)

![](img/609602d0b83f713b90cffed920227c5c_44.png)

![](img/609602d0b83f713b90cffed920227c5c_45.png)

![](img/609602d0b83f713b90cffed920227c5c_46.png)

为了让网络中的多种服务互不干扰，我们引入了“端口”的概念。端口是逻辑上的通信通道，不同的服务使用不同的端口号。

![](img/609602d0b83f713b90cffed920227c5c_48.png)

*   **端口**：Web服务默认使用**80端口**。加密的HTTPS服务则使用**443端口**。这就像不同的业务走不同的专用通道。
*   **协议**：数据在端口上传输需要遵循规则，即协议。Web服务主要涉及两种传输层协议：
    *   **TCP**：面向连接，通过“三次握手”建立可靠连接，确保数据准确、有序、不丢失。适用于网页浏览、邮件等场景。
        公式：`客户端 --[SYN]--> 服务器；客户端 <--[SYN/ACK]-- 服务器；客户端 --[ACK]--> 服务器` (三次握手)
    *   **UDP**：无连接，直接发送数据包，不保证顺序和可靠性，但速度快。适用于视频流、语音通话、DNS查询等场景。

### 配置文件初探

Apache的主要配置文件是 `/etc/httpd/conf/httpd.conf`。我们不需要记住所有配置项，但需要了解几个关键参数。

![](img/609602d0b83f713b90cffed920227c5c_50.png)

![](img/609602d0b83f713b90cffed920227c5c_51.png)

![](img/609602d0b83f713b90cffed920227c5c_53.png)

```apache
# 服务器根目录，其他相对路径基于此目录
ServerRoot "/etc/httpd"

![](img/609602d0b83f713b90cffed920227c5c_55.png)

![](img/609602d0b83f713b90cffed920227c5c_56.png)

# 监听端口，默认为80
Listen 80

# 运行Apache服务的用户和组
User apache
Group apache

# 管理员邮箱（服务器出错时接收通知）
ServerAdmin root@localhost

# 网站数据文件的根目录
DocumentRoot "/var/www/html"

# 定义目录的访问权限。先默认拒绝所有访问，再对特定目录授权
<Directory />
    AllowOverride none
    Require all denied
</Directory>

# 允许访问网站根目录
<Directory "/var/www/html">
    ...
    Require all granted
</Directory>

# 默认的主页文件名
DirectoryIndex index.html

# 访问日志和错误日志的存放位置（相对路径，基于ServerRoot）
ErrorLog "logs/error_log"
CustomLog "logs/access_log" combined
```

**重要提示**：修改配置文件后，需要重新加载配置才能生效。
*   `systemctl restart httpd`：重启服务（进程ID会改变）。
*   `systemctl reload httpd`：平滑重载配置（不重启主进程，适用于生产环境）。

### 第一个实验：发布简单网页

现在，让我们动手创建一个最简单的网站。

1.  Apache的默认网站目录是 `/var/www/html`。
2.  在该目录下创建默认主页 `index.html`：
    ```bash
    echo “这是我的第一个网站” > /var/www/html/index.html
    ```
3.  在浏览器中访问你的服务器IP地址（如 `http://192.168.100.1`）或本机地址（`http://localhost`）。你应该能看到刚才创建的网页内容。

这个实验验证了Apache服务已正常工作，并能将指定目录下的网页文件发布到网络上。

## 总结

![](img/609602d0b83f713b90cffed920227c5c_58.png)

本节课中我们一起学习了Linux下Web服务器的基础知识。我们明确了服务器的定义与分类，认识了Apache HTTP Server，并成功安装、启动了它。我们理解了端口和TCP/UDP协议在网络通信中的作用，浏览了Apache的主配置文件，识别了关键参数。最后，我们通过发布一个简单的静态网页，完成了Web服务器搭建的初体验。这是后续所有服务器学习的重要基础。