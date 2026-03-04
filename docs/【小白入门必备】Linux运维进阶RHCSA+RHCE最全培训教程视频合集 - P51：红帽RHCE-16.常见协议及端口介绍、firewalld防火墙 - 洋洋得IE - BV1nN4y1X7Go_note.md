# Linux运维进阶：P51：常见协议、端口与firewalld防火墙 🔥

![](img/cfd45edcfb88b283313c99a4d5578061_1.png)

在本节课中，我们将学习网络中的常见协议与端口概念，并初步了解Linux系统中的firewalld防火墙。理解这些基础知识是进行网络配置和安全管理的基石。

![](img/cfd45edcfb88b283313c99a4d5578061_3.png)

## 协议与端口：网络世界的规则与门牌号

![](img/cfd45edcfb88b283313c99a4d5578061_5.png)

![](img/cfd45edcfb88b283313c99a4d5578061_7.png)

上一节我们介绍了网络的基本概念，本节中我们来看看数据传输的规则——协议，以及服务的入口——端口。

在互联网中，数据传输需要遵循特定的规则，这些规则被称为“协议”。这类似于现实社会中的法律法规，确保数据能够有序、准确地传输。例如，访问网站时，浏览器与服务器之间通常使用HTTP或HTTPS协议进行通信。

*   **HTTP**：超文本传输协议，是一种明文传输协议。其默认端口是 **80**。
*   **HTTPS**：安全的超文本传输协议，在HTTP基础上增加了SSL/TLS加密层。其默认端口是 **443**。

端口是网络服务在服务器上的“门牌号”。一台服务器可以运行多个服务（如网站、文件共享），每个服务监听一个特定的端口。当客户端访问服务器时，通过指定IP地址和端口号，就能连接到对应的服务。

![](img/cfd45edcfb88b283313c99a4d5578061_9.png)

![](img/cfd45edcfb88b283313c99a4d5578061_11.png)

![](img/cfd45edcfb88b283313c99a4d5578061_13.png)

以下是部分常见协议及其默认端口列表：
*   **HTTP**：端口 **80**，用于网页浏览。
*   **HTTPS**：端口 **443**，用于加密的网页浏览。
*   **FTP**：端口 **20（数据）**, **21（控制）**，用于文件传输。
*   **SSH**：端口 **22**，用于安全的远程登录管理。
*   **DNS**：端口 **53**，用于域名解析。

![](img/cfd45edcfb88b283313c99a4d5578061_15.png)

在Linux系统中，可以通过查看 `/etc/services` 文件来了解更多的协议与端口对应关系。

![](img/cfd45edcfb88b283313c99a4d5578061_17.png)

![](img/cfd45edcfb88b283313c99a4d5578061_19.png)

![](img/cfd45edcfb88b283313c99a4d5578061_21.png)

## 防火墙基础：数据流量的安检站

![](img/cfd45edcfb88b283313c99a4d5578061_23.png)

![](img/cfd45edcfb88b283313c99a4d5578061_25.png)

![](img/cfd45edcfb88b283313c99a4d5578061_27.png)

理解了协议和端口后，我们来看看如何控制对这些服务的访问，这就是防火墙的作用。

![](img/cfd45edcfb88b283313c99a4d5578061_29.png)

![](img/cfd45edcfb88b283313c99a4d5578061_31.png)

![](img/cfd45edcfb88b283313c99a4d5578061_33.png)

![](img/cfd45edcfb88b283313c99a4d5578061_35.png)

防火墙工作在主机或网络的边缘，根据预先定义的规则，对进出的网络数据包进行检查和过滤。它就像地铁站的安检，只有符合规定的数据才能被放行。

![](img/cfd45edcfb88b283313c99a4d5578061_37.png)

防火墙主要分为两类：
*   **软件防火墙**：通过安装在操作系统上的软件实现，如Linux的`firewalld`或`iptables`。
*   **硬件防火墙**：独立的物理设备，通常功能更强大，配置通过Web界面进行。

在RHEL/CentOS 7及更高版本中，默认的防火墙管理工具是 **firewalld**。

## 初识firewalld：区域与规则管理

本节中，我们通过实际操作来了解firewalld的基本管理。

![](img/cfd45edcfb88b283313c99a4d5578061_39.png)

![](img/cfd45edcfb88b283313c99a4d5578061_41.png)

首先，我们需要一个用于测试的服务。这里我们安装并启动Apache HTTP服务器（httpd），它默认监听80端口。
```bash
yum -y install httpd
systemctl start httpd
systemctl status httpd
```

![](img/cfd45edcfb88b283313c99a4d5578061_43.png)

![](img/cfd45edcfb88b283313c99a4d5578061_45.png)

![](img/cfd45edcfb88b283313c99a4d5578061_47.png)

![](img/cfd45edcfb88b283313c99a4d5578061_49.png)

![](img/cfd45edcfb88b283313c99a4d5578061_51.png)

启动后，可以使用`ss -ntlp | grep httpd`命令确认其正在监听80端口。

![](img/cfd45edcfb88b283313c99a4d5578061_53.png)

![](img/cfd45edcfb88b283313c99a4d5578061_55.png)

如果此时防火墙（firewalld）是开启状态，默认配置可能会阻止外部对80端口的访问。我们可以通过关闭防火墙来临时验证服务是否正常。
```bash
systemctl stop firewalld
```
停止防火墙后，应能正常访问网页。这证明了防火墙在影响网络访问。

![](img/cfd45edcfb88b283313c99a4d5578061_57.png)

![](img/cfd45edcfb88b283313c99a4d5578061_59.png)

![](img/cfd45edcfb88b283313c99a4d5578061_61.png)

firewalld的核心概念是“区域”。每个区域是一组预定义的规则，适用于不同的信任级别。我们可以查看和更改默认区域。

![](img/cfd45edcfb88b283313c99a4d5578061_63.png)

![](img/cfd45edcfb88b283313c99a4d5578061_65.png)

![](img/cfd45edcfb88b283313c99a4d5578061_67.png)

![](img/cfd45edcfb88b283313c99a4d5578061_69.png)

![](img/cfd45edcfb88b283313c99a4d5578061_71.png)

查看当前默认区域：
```bash
firewall-cmd --get-default-zone
```
设置默认区域为`trusted`（允许所有访问）：
```bash
firewall-cmd --set-default-zone=trusted
```
设置默认区域为`block`（拒绝所有访问并回复拒绝信息）：
```bash
firewall-cmd --set-default-zone=block
```
设置默认区域为`drop`（丢弃所有访问，不做任何回复）：
```bash
firewall-cmd --set-default-zone=drop
```
`block`和`drop`都拒绝访问，区别在于`block`会明确告知对方被拒绝，而`drop`则 silently 丢弃数据包，后者更常用于应对网络攻击。

![](img/cfd45edcfb88b283313c99a4d5578061_73.png)

![](img/cfd45edcfb88b283313c99a4d5578061_75.png)

![](img/cfd45edcfb88b283313c99a4d5578061_77.png)

查看某个区域（如public）的详细规则：
```bash
firewall-cmd --zone=public --list-all
```

![](img/cfd45edcfb88b283313c99a4d5578061_79.png)

![](img/cfd45edcfb88b283313c99a4d5578061_81.png)

## 配置firewalld规则：放行特定服务

![](img/cfd45edcfb88b283313c99a4d5578061_83.png)

![](img/cfd45edcfb88b283313c99a4d5578061_85.png)

![](img/cfd45edcfb88b283313c99a4d5578061_87.png)

![](img/cfd45edcfb88b283313c99a4d5578061_89.png)

最常用的方式不是切换整个区域，而是在默认区域（通常是`public`）中添加允许特定服务的规则。

![](img/cfd45edcfb88b283313c99a4d5578061_91.png)

![](img/cfd45edcfb88b283313c99a4d5578061_93.png)

![](img/cfd45edcfb88b283313c99a4d5578061_95.png)

![](img/cfd45edcfb88b283313c99a4d5578061_97.png)

![](img/cfd45edcfb88b283313c99a4d5578061_99.png)

将默认区域改回`public`，并添加允许HTTP服务的规则：
```bash
firewall-cmd --set-default-zone=public
firewall-cmd --zone=public --add-service=http --permanent
firewall-cmd --reload
```
*   `--add-service=http`：添加http服务（对应80端口）到规则。
*   `--permanent`：使规则永久生效（否则重启后失效）。
*   `--reload`：重新加载防火墙配置，使永久规则立即生效。

添加规则后，再次使用`firewall-cmd --zone=public --list-all`查看，会发现`services`列表中包含了`http`。此时，外部客户端就可以访问服务器的80端口了。

若要移除规则，可使用`--remove-service`选项：
```bash
firewall-cmd --zone=public --remove-service=http --permanent
firewall-cmd --reload
```

![](img/cfd45edcfb88b283313c99a4d5578061_101.png)

![](img/cfd45edcfb88b283313c99a4d5578061_103.png)

## 总结与前瞻

本节课中我们一起学习了：
1.  **协议与端口**：理解了HTTP/HTTPS等协议的作用，以及端口作为网络服务“门牌号”的概念。
2.  **防火墙基础**：认识了防火墙作为网络安全“安检站”的功能和分类。
3.  **firewalld实践**：掌握了使用`firewall-cmd`命令查看、切换防火墙区域，以及添加/移除服务规则的基本操作。

![](img/cfd45edcfb88b283313c99a4d5578061_105.png)

![](img/cfd45edcfb88b283313c99a4d5578061_107.png)

![](img/cfd45edcfb88b283313c99a4d5578061_109.png)

firewalld提供了更高级的网络配置方式，但在实际生产环境中，传统的`iptables`因其灵活性和广泛的应用知识库，仍然被大量使用。下节课，我们将深入学习功能强大且应用广泛的`iptables`防火墙工具。