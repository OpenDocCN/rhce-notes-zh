# Linux网络与安全：P52：常见协议、端口与firewalld防火墙

## 概述

在本节课中，我们将学习网络通信的基础概念，包括常见的网络协议及其对应的端口号。随后，我们将深入探讨Linux系统中的防火墙技术，重点介绍CentOS 7默认的防火墙管理工具——firewalld。通过学习，你将理解协议和端口在网络通信中的作用，并掌握如何使用firewalld进行基本的防火墙规则配置。

![](img/02e4cf742d3fbd981a07ef713d842434_1.png)

![](img/02e4cf742d3fbd981a07ef713d842434_3.png)

![](img/02e4cf742d3fbd981a07ef713d842434_5.png)

---

![](img/02e4cf742d3fbd981a07ef713d842434_7.png)

![](img/02e4cf742d3fbd981a07ef713d842434_9.png)

## 网络协议与端口

上一节我们介绍了课程的整体内容，本节中我们来看看网络通信的基础：协议与端口。

在互联网中，计算机之间的通信不像人类社会那样依赖法律和道德规则，而是依赖于**网络协议**。协议规定了在网络中数据传输的格式和规则。例如，当你使用浏览器访问淘宝网站时，浏览器与淘宝服务器之间的数据传输就是基于特定的协议进行的。

常见的网站访问协议是**HTTP**（超文本传输协议）和**HTTPS**（安全的超文本传输协议）。HTTPS在HTTP的基础上增加了SSL/TLS加密层，使得数据传输过程被加密，从而更安全。HTTP协议传输的数据是明文的，容易被第三方截获和分析；而HTTPS传输的数据是加密的，即使被截获，看到的也是乱码。

**协议**与**端口**紧密相关。端口是网络通信中的一个逻辑概念，可以理解为服务器上不同网络服务的“门牌号”。一台服务器可以同时运行多个网络服务（如网站、文件共享），每个服务会监听一个或多个特定的端口。客户端通过IP地址找到服务器后，需要通过指定的端口号来访问特定的服务。

![](img/02e4cf742d3fbd981a07ef713d842434_11.png)

例如：
*   HTTP协议默认使用 **80** 端口。
*   HTTPS协议默认使用 **443** 端口。
*   SSH协议默认使用 **22** 端口。

![](img/02e4cf742d3fbd981a07ef713d842434_13.png)

![](img/02e4cf742d3fbd981a07ef713d842434_15.png)

当你在浏览器输入 `www.taobao.com` 时，浏览器会自动使用HTTPS协议（端口443）去连接淘宝的服务器，你无需手动指定端口号。

以下是初学者需要重点记住的几个常见协议及其默认端口：

![](img/02e4cf742d3fbd981a07ef713d842434_17.png)

![](img/02e4cf742d3fbd981a07ef713d842434_19.png)

*   **HTTP**: **80** - 用于未加密的网页访问。
*   **HTTPS**: **443** - 用于加密的网页访问。
*   **SSH**: **22** - 用于安全的远程登录和管理服务器。
*   **FTP**: **20, 21** - 用于文件传输（现在较少使用，多被SFTP替代）。

![](img/02e4cf742d3fbd981a07ef713d842434_21.png)

![](img/02e4cf742d3fbd981a07ef713d842434_23.png)

![](img/02e4cf742d3fbd981a07ef713d842434_25.png)

在Linux系统中，你可以查看 `/etc/services` 文件来了解更多的协议与端口对应关系。
```bash
cat /etc/services | grep -E ‘(80/tcp|443/tcp|22/tcp)‘
```

![](img/02e4cf742d3fbd981a07ef713d842434_27.png)

![](img/02e4cf742d3fbd981a07ef713d842434_29.png)

---

![](img/02e4cf742d3fbd981a07ef713d842434_31.png)

![](img/02e4cf742d3fbd981a07ef713d842434_33.png)

![](img/02e4cf742d3fbd981a07ef713d842434_35.png)

## 防火墙基础概念

![](img/02e4cf742d3fbd981a07ef713d842434_37.png)

![](img/02e4cf742d3fbd981a07ef713d842434_39.png)

了解了网络通信的入口——端口后，我们来看看如何守卫这些入口，即防火墙。

防火墙工作在主机或网络的边缘，对进出的网络数据包根据预设的规则进行检查和过滤。只有符合规则的数据包才被允许通过，不符合的则被拒绝或丢弃。这就像地铁站的安检，只有通过检查的旅客才能进入。

防火墙主要分为两类：
1.  **软件防火墙**：通过安装在操作系统上的软件实现包过滤功能。例如Linux系统中的 `firewalld` 和 `iptables`。
2.  **硬件防火墙**：一个独立的物理设备，专门用于执行网络安全策略，性能更强，配置通常通过Web界面进行。

根据防护范围，又可分为：
*   **主机防火墙**：保护单台主机，规则仅应用于本机。适用于小型环境。
*   **网络防火墙**：保护整个网络，通常部署在网络入口处。适用于中大型企业网络。

在CentOS/RHEL 7及以后版本中，默认的防火墙管理工具是 **firewalld**，它相较于之前的 `iptables`，引入了“区域”（zone）的概念，使管理更加动态和便捷。而 `iptables` 则是更底层、功能强大的规则管理工具，在实际生产环境中仍被广泛使用。

---

![](img/02e4cf742d3fbd981a07ef713d842434_41.png)

## firewalld防火墙管理

![](img/02e4cf742d3fbd981a07ef713d842434_43.png)

![](img/02e4cf742d3fbd981a07ef713d842434_45.png)

![](img/02e4cf742d3fbd981a07ef713d842434_47.png)

本节我们将动手学习如何使用firewalld。首先，我们需要一个用于测试的网络服务。我们安装并启动一个简单的Web服务器（Apache HTTPD）。

![](img/02e4cf742d3fbd981a07ef713d842434_49.png)

![](img/02e4cf742d3fbd981a07ef713d842434_51.png)

![](img/02e4cf742d3fbd981a07ef713d842434_53.png)

![](img/02e4cf742d3fbd981a07ef713d842434_55.png)

```bash
# 安装httpd软件包
yum -y install httpd
# 启动httpd服务
systemctl start httpd
# 设置开机自启
systemctl enable httpd
# 查看服务状态及监听端口
ss -ntlp | grep httpd
```
命令 `ss -ntlp` 可以查看当前系统监听的网络端口。你应该能看到 `httpd` 进程正在监听 **80** 端口。

![](img/02e4cf742d3fbd981a07ef713d842434_57.png)

![](img/02e4cf742d3fbd981a07ef713d842434_59.png)

此时，如果从其他机器访问这台服务器的IP地址，会发现无法打开网页（连接超时），这是因为默认的防火墙规则阻止了外部对80端口的访问。

![](img/02e4cf742d3fbd981a07ef713d842434_61.png)

![](img/02e4cf742d3fbd981a07ef713d842434_63.png)

![](img/02e4cf742d3fbd981a07ef713d842434_65.png)

### firewalld的核心：区域（Zone）

![](img/02e4cf742d3fbd981a07ef713d842434_67.png)

![](img/02e4cf742d3fbd981a07ef713d842434_69.png)

firewalld通过**区域**来管理规则。每个区域是一套预设的规则集，定义了不同的信任级别。我们可以将不同的网络接口（网卡）分配给不同的区域。

![](img/02e4cf742d3fbd981a07ef713d842434_71.png)

![](img/02e4cf742d3fbd981a07ef713d842434_73.png)

![](img/02e4cf742d3fbd981a07ef713d842434_75.png)

![](img/02e4cf742d3fbd981a07ef713d842434_77.png)

以下是几个重要的默认区域：
*   **public（默认）**： 仅允许访问本机的SSH、DHCP等少量必需服务。
*   **trusted**： 允许所有传入连接，最宽松。
*   **block**： 拒绝所有传入连接，但有明确的拒绝回应。
*   **drop**： 丢弃所有传入连接，没有任何回应（静默丢弃）。

![](img/02e4cf742d3fbd981a07ef713d842434_79.png)

查看和修改默认区域：
```bash
# 查看当前默认区域
firewall-cmd --get-default-zone
# 将默认区域修改为 trusted（允许所有访问）
firewall-cmd --set-default-zone=trusted
```
将区域改为 `trusted` 后，刷新浏览器，即可成功访问之前安装的Apache测试页面。

![](img/02e4cf742d3fbd981a07ef713d842434_81.png)

![](img/02e4cf742d3fbd981a07ef713d842434_83.png)

![](img/02e4cf742d3fbd981a07ef713d842434_85.png)

### 管理区域内的规则

![](img/02e4cf742d3fbd981a07ef713d842434_87.png)

![](img/02e4cf742d3fbd981a07ef713d842434_89.png)

每个区域内部包含允许的服务、端口、源地址等规则。我们可以针对特定区域添加或移除规则。

![](img/02e4cf742d3fbd981a07ef713d842434_91.png)

![](img/02e4cf742d3fbd981a07ef713d842434_93.png)

查看`public`区域的详细规则：
```bash
firewall-cmd --zone=public --list-all
```
输出会显示该区域允许的服务（如ssh, dhcpv6-client），以及绑定的网络接口。

![](img/02e4cf742d3fbd981a07ef713d842434_95.png)

![](img/02e4cf742d3fbd981a07ef713d842434_97.png)

![](img/02e4cf742d3fbd981a07ef713d842434_99.png)

![](img/02e4cf742d3fbd981a07ef713d842434_101.png)

![](img/02e4cf742d3fbd981a07ef713d842434_103.png)

为`public`区域添加允许HTTP服务的规则：
```bash
# 添加规则（--add-service）
firewall-cmd --zone=public --add-service=http
# 添加规则后需要重载配置或使用--permanent参数永久生效
firewall-cmd --reload
# 永久添加规则（重启后依然有效）
firewall-cmd --zone=public --add-service=http --permanent
firewall-cmd --reload
```
添加规则后，即使默认区域是`public`，外部主机也能访问Web服务了。

移除规则：
```bash
# 移除规则
firewall-cmd --zone=public --remove-service=http
firewall-cmd --reload
```

除了基于服务名（对应一组端口），也可以直接针对端口操作：
```bash
# 允许TCP 8080端口
firewall-cmd --zone=public --add-port=8080/tcp
# 允许UDP 123端口
firewall-cmd --zone=public --add-port=123/udp
```

**`block` 与 `drop` 区域的区别**：
*   **block**： 拒绝连接时会向客户端返回一个“目标不可达”的响应。客户端能立刻知道自己被拒绝。
*   **drop**： 直接丢弃连接请求，不返回任何信息。客户端会一直等待直到超时。从安全角度，`drop` 更能消耗攻击者的资源，是更推荐的方式。

![](img/02e4cf742d3fbd981a07ef713d842434_105.png)

![](img/02e4cf742d3fbd981a07ef713d842434_107.png)

---

## 总结

本节课中我们一起学习了网络基础与Linux防火墙。
1.  我们理解了**网络协议**（如HTTP/HTTPS）是数据传输的规则，而**端口**是服务器上不同网络服务的逻辑入口。
2.  我们认识了防火墙的作用——在网络边缘过滤数据包，并了解了软件防火墙与硬件防火墙、主机防火墙与网络防火墙的区别。
3.  我们重点实践了CentOS 7的默认防火墙工具 **firewalld**，学习了其核心概念**区域**，并掌握了如何查看、修改默认区域，以及如何为区域添加或移除服务/端口规则。

![](img/02e4cf742d3fbd981a07ef713d842434_109.png)

![](img/02e4cf742d3fbd981a07ef713d842434_111.png)

![](img/02e4cf742d3fbd981a07ef713d842434_113.png)

虽然firewalld提供了更易用的管理方式，但在许多生产环境和高级场景中，更底层的 **iptables** 因其灵活性和强大功能仍然不可或缺。下节课我们将深入探讨iptables的使用。