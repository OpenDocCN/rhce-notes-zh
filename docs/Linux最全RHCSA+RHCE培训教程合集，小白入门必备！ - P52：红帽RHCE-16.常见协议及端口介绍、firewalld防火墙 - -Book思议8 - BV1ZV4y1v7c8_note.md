# Linux最全RHCSA+RHCE培训教程合集：P52：红帽RHCE-16.常见协议及端口介绍、firewalld防火墙 🔥

![](img/f13866fba1095e163c8d0a8e0533aa75_1.png)

## 概述
在本节课中，我们将要学习网络中的常见协议与端口号，并深入了解Linux系统中的firewalld防火墙。理解这些概念是管理服务器和保障网络安全的基础。

![](img/f13866fba1095e163c8d0a8e0533aa75_3.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_5.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_7.png)

## 常见协议与端口介绍

上一节我们介绍了网络的基本概念，本节中我们来看看数据传输所依赖的规则——协议，以及访问服务的入口——端口。

在互联网中，数据传输需要遵守特定的规则，这些规则被称为协议。协议规定了数据如何打包、传输和接收。例如，访问网站时，浏览器与服务器之间通常使用HTTP或HTTPS协议进行通信。

*   **HTTP**：超文本传输协议，用于传输网页数据。它是明文协议，意味着传输的数据可以被第三方截获并解读。
    *   **默认端口**：`80`
*   **HTTPS**：安全的超文本传输协议，在HTTP基础上增加了SSL/TLS加密层，确保数据传输的安全性。
    *   **默认端口**：`443`

![](img/f13866fba1095e163c8d0a8e0533aa75_9.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_11.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_13.png)

一台服务器上可能运行着多个应用程序（服务）。为了区分这些服务，系统为每个服务分配了一个数字标识，即端口号。当用户访问服务器时，需要通过指定的端口号来连接到目标服务。

![](img/f13866fba1095e163c8d0a8e0533aa75_15.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_17.png)

以下是其他一些常见协议及其默认端口：

![](img/f13866fba1095e163c8d0a8e0533aa75_19.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_21.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_23.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_25.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_27.png)

*   **FTP**：文件传输协议，用于在网络上进行文件传输。
    *   **默认端口**：`20` (数据), `21` (控制)
*   **SSH**：安全外壳协议，用于远程安全地管理服务器。
    *   **默认端口**：`22`
*   **DNS**：域名系统协议，用于将域名解析为IP地址。
    *   **默认端口**：`53`
*   **Telnet**：远程登录协议，由于传输不加密，现已较少使用。
    *   **默认端口**：`23`

![](img/f13866fba1095e163c8d0a8e0533aa75_29.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_31.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_33.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_35.png)

在Linux系统中，可以查看 `/etc/services` 文件来了解更多的协议与端口对应关系。

![](img/f13866fba1095e163c8d0a8e0533aa75_37.png)

```bash
cat /etc/services | grep -E ‘^(http|https|ssh|ftp)’
```

## firewalld防火墙基础

了解了协议和端口后，我们来看看如何控制对这些服务的访问，这就是防火墙的作用。防火墙工作在主机或网络的边缘，根据预设的规则对进出的数据包进行检查和过滤。

![](img/f13866fba1095e163c8d0a8e0533aa75_39.png)

### 防火墙种类与firewalld简介
防火墙主要分为软件防火墙和硬件防火墙。在RHEL/CentOS 7及更高版本中，默认的软件防火墙管理工具是 **firewalld**。它通过 `firewalld` 服务进行管理，使用 `firewall-cmd` 命令进行配置。

![](img/f13866fba1095e163c8d0a8e0533aa75_41.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_43.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_45.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_47.png)

首先，我们需要一个服务来演示防火墙规则。我们安装并启动一个Web服务器（httpd）。

![](img/f13866fba1095e163c8d0a8e0533aa75_49.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_51.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_53.png)

```bash
# 安装httpd软件包
yum install -y httpd
# 启动httpd服务
systemctl start httpd
# 设置开机自启
systemctl enable httpd
# 查看服务状态及监听端口
ss -ntlp | grep httpd
```

![](img/f13866fba1095e163c8d0a8e0533aa75_55.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_57.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_59.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_61.png)

### firewalld的区域（Zone）概念
firewalld引入了“区域”的概念，每个区域定义了一组预设的信任级别和规则。常见的区域有：

![](img/f13866fba1095e163c8d0a8e0533aa75_63.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_65.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_67.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_69.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_71.png)

*   **public（默认）**：仅允许访问本机的SSH、DHCP等少量服务。
*   **trusted**：允许所有访问。
*   **block**：拒绝所有来访请求，但有明确拒绝回应。
*   **drop**：丢弃所有来访的数据包，没有任何回应。

![](img/f13866fba1095e163c8d0a8e0533aa75_73.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_75.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_77.png)

以下是管理默认区域的命令：

![](img/f13866fba1095e163c8d0a8e0533aa75_79.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_81.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_83.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_85.png)

```bash
# 查看当前默认区域
firewall-cmd --get-default-zone
# 将默认区域设置为trusted（允许所有访问）
firewall-cmd --set-default-zone=trusted
# 将默认区域设置为block（拒绝所有访问）
firewall-cmd --set-default-zone=block
```

![](img/f13866fba1095e163c8d0a8e0533aa75_87.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_89.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_91.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_93.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_95.png)

### 管理区域内的规则
每个区域内部可以定义更细致的规则。我们可以查看特定区域的规则，并为其添加或删除允许的服务（协议）。

```bash
# 查看public区域的详细规则
firewall-cmd --zone=public --list-all
# 为public区域永久添加HTTP服务（协议）的访问规则
firewall-cmd --zone=public --add-service=http --permanent
# 为public区域永久添加HTTPS服务的访问规则
firewall-cmd --zone=public --add-service=https --permanent
# 重载防火墙配置使永久规则生效
firewall-cmd --reload
# 从public区域移除HTTPS服务规则
firewall-cmd --zone=public --remove-service=https --permanent
firewall-cmd --reload
```

> **注意**：`--permanent` 选项表示规则永久生效，但需要执行 `--reload` 重载或重启firewalld服务后才会应用于当前运行的环境。不加此选项的规则仅临时生效，重启后失效。

![](img/f13866fba1095e163c8d0a8e0533aa75_97.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_99.png)

## 总结
本节课我们一起学习了网络中的核心概念。我们首先了解了常见的网络协议（如HTTP、HTTPS、SSH）及其对应的端口号，明白了端口是访问服务器上特定服务的“门户”。接着，我们深入探讨了firewalld防火墙，学习了其区域（Zone）模型，并掌握了如何使用 `firewall-cmd` 命令查看、修改默认区域以及管理区域内的服务访问规则。理解这些知识，是后续配置服务器安全和网络服务的基础。

![](img/f13866fba1095e163c8d0a8e0533aa75_101.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_103.png)

![](img/f13866fba1095e163c8d0a8e0533aa75_105.png)

> 下一节我们将学习功能更强大、在企业中应用更广泛的 `iptables` 防火墙。