# Linux运维教程：P51：常见协议、端口与firewalld防火墙

## 概述
在本节课中，我们将学习网络中的核心概念——协议与端口，并深入了解Linux系统上的一款重要安全工具——firewalld防火墙。理解这些知识是管理服务器和保障网络安全的基础。

---

![](img/d78f0d19528867fa5040f07c79370d1a_1.png)

![](img/d78f0d19528867fa5040f07c79370d1a_2.png)

![](img/d78f0d19528867fa5040f07c79370d1a_4.png)

![](img/d78f0d19528867fa5040f07c79370d1a_6.png)

## 协议与端口介绍

![](img/d78f0d19528867fa5040f07c79370d1a_8.png)

上一节我们概述了课程内容，本节中我们来看看网络通信的基础：协议与端口。

在互联网中，计算机之间的通信不像人与人的社交，它需要遵循严格的规则，这些规则被称为**协议**。协议规定了数据在网络中如何传输。例如，当你访问淘宝网站时，浏览器与淘宝服务器之间的数据传输就是基于**HTTPS协议**进行的。

协议主要分为两类：
*   **明文协议**：如HTTP，传输的数据可以被第三方截获并解读。
*   **加密协议**：如HTTPS，传输的数据经过加密，即使被截获也难以破解。

**端口**是网络通信的另一个关键概念。你可以将一台服务器想象成一个提供多种服务（如网站、文件共享）的大型商场。每个服务（应用程序）都有一个唯一的“门牌号”，这就是端口。客户端通过指定不同的端口号来访问服务器上不同的服务。

![](img/d78f0d19528867fa5040f07c79370d1a_10.png)

![](img/d78f0d19528867fa5040f07c79370d1a_12.png)

以下是常见协议及其默认端口对照表，初学者需要重点记忆：

![](img/d78f0d19528867fa5040f07c79370d1a_14.png)

| 协议/服务 | 默认端口 | 主要用途 |
| :--- | :--- | :--- |
| **HTTP** | **80** | 超文本传输协议，用于访问普通网站。 |
| **HTTPS** | **443** | 安全的超文本传输协议，用于访问加密网站。 |
| **SSH** | **22** | 安全的远程连接协议，用于管理服务器。 |
| **FTP** | **21** | 文件传输协议，用于文件上传下载。 |
| **DNS** | **53** | 域名解析协议，将域名转换为IP地址。 |

![](img/d78f0d19528867fa5040f07c79370d1a_16.png)

![](img/d78f0d19528867fa5040f07c79370d1a_18.png)

**补充说明**：
*   FTP与TFTP：FTP使用TCP协议，建立连接更可靠；TFTP使用UDP协议，传输更简单快速。
*   Telnet（端口23）：早期的远程管理协议，因为传输明文不安全，现已基本被SSH取代。
*   在Linux系统中，`/etc/services` 文件记录了系统已知的所有服务及其对应的端口和协议。

![](img/d78f0d19528867fa5040f07c79370d1a_20.png)

![](img/d78f0d19528867fa5040f07c79370d1a_22.png)

---

![](img/d78f0d19528867fa5040f07c79370d1a_24.png)

![](img/d78f0d19528867fa5040f07c79370d1a_25.png)

![](img/d78f0d19528867fa5040f07c79370d1a_27.png)

![](img/d78f0d19528867fa5040f07c79370d1a_29.png)

## firewalld防火墙基础

![](img/d78f0d19528867fa5040f07c79370d1a_31.png)

![](img/d78f0d19528867fa5040f07c79370d1a_33.png)

![](img/d78f0d19528867fa5040f07c79370d1a_35.png)

理解了协议和端口如何工作后，我们来看看如何控制对它们的访问，这就是防火墙的作用。

![](img/d78f0d19528867fa5040f07c79370d1a_37.png)

防火墙工作在主机或网络的边缘，根据预设的规则检查所有进出的数据包，并对匹配规则的数据包执行相应操作（允许或拒绝）。防火墙主要分为两类：
*   **软件防火墙**：通过安装软件包并配置规则来实现，如Linux上的firewalld和iptables。
*   **硬件防火墙**：独立的物理设备，通常通过图形界面配置，性能更强，常用于大型企业。

在RHEL/CentOS 7及以后版本中，默认的防火墙管理工具是 **firewalld**。

### firewalld的核心概念：区域（Zone）

firewalld通过“区域”来管理规则。每个区域定义了一组预设的信任级别和规则。系统有一个默认区域，所有网络接口通常都绑定在此区域上。

以下是几个关键区域及其含义：

![](img/d78f0d19528867fa5040f07c79370d1a_39.png)

![](img/d78f0d19528867fa5040f07c79370d1a_41.png)

| 区域名称 | 默认行为 | 说明 |
| :--- | :--- | :--- |
| **public** | 拒绝（除明确允许的服务） | 默认区域。仅允许访问SSH、DHCP等少数必要服务。适合公共环境。 |
| **trusted** | 允许 | 接受所有网络连接，完全信任。适合内部完全可信的网络。 |
| **block** | 拒绝（有回应） | 拒绝所有传入连接，并以icmp-host-prohibited消息明确回复。 |
| **drop** | 拒绝（无回应） | 丢弃所有传入的数据包，不给出任何响应。对攻击者最有效。 |

![](img/d78f0d19528867fa5040f07c79370d1a_43.png)

![](img/d78f0d19528867fa5040f07c79370d1a_45.png)

![](img/d78f0d19528867fa5040f07c79370d1a_47.png)

![](img/d78f0d19528867fa5040f07c79370d1a_48.png)

![](img/d78f0d19528867fa5040f07c79370d1a_49.png)

**`block` 与 `drop` 的区别**：`block`会明确告知访问者“被拒绝”，而`drop`则像石沉大海，没有任何回复。后者更节省服务器资源，并能更有效地应对网络扫描和攻击。

![](img/d78f0d19528867fa5040f07c79370d1a_51.png)

![](img/d78f0d19528867fa5040f07c79370d1a_53.png)

![](img/d78f0d19528867fa5040f07c79370d1a_55.png)

### firewalld的基本操作

![](img/d78f0d19528867fa5040f07c79370d1a_57.png)

![](img/d78f0d19528867fa5040f07c79370d1a_59.png)

![](img/d78f0d19528867fa5040f07c79370d1a_60.png)

首先，确保firewalld服务正在运行：
```bash
systemctl start firewalld    # 启动防火墙
systemctl status firewalld   # 查看状态
```

![](img/d78f0d19528867fa5040f07c79370d1a_62.png)

![](img/d78f0d19528867fa5040f07c79370d1a_63.png)

![](img/d78f0d19528867fa5040f07c79370d1a_65.png)

![](img/d78f0d19528867fa5040f07c79370d1a_67.png)

**1. 查看与设置默认区域**
```bash
# 查看当前默认区域
firewall-cmd --get-default-zone

![](img/d78f0d19528867fa5040f07c79370d1a_69.png)

![](img/d78f0d19528867fa5040f07c79370d1a_71.png)

![](img/d78f0d19528867fa5040f07c79370d1a_73.png)

# 将默认区域设置为 trusted（允许所有访问）
firewall-cmd --set-default-zone=trusted
```

![](img/d78f0d19528867fa5040f07c79370d1a_75.png)

**2. 查看指定区域的规则**
```bash
# 查看 public 区域的详细规则
firewall-cmd --zone=public --list-all
```
输出会显示该区域允许的服务（services）、端口、源地址等规则。

![](img/d78f0d19528867fa5040f07c79370d1a_77.png)

![](img/d78f0d19528867fa5040f07c79370d1a_79.png)

![](img/d78f0d19528867fa5040f07c79370d1a_81.png)

**3. 在区域中添加或移除服务（协议）**
假设我们想在 `public` 区域中允许HTTP访问（即开放80端口）：
```bash
# 添加 http 服务（协议）到 public 区域（临时生效，重启后失效）
firewall-cmd --zone=public --add-service=http

![](img/d78f0d19528867fa5040f07c79370d1a_83.png)

![](img/d78f0d19528867fa5040f07c79370d1a_85.png)

![](img/d78f0d19528867fa5040f07c79370d1a_87.png)

# 添加 http 服务并使其永久生效（需重载防火墙）
firewall-cmd --zone=public --add-service=http --permanent
firewall-cmd --reload

![](img/d78f0d19528867fa5040f07c79370d1a_89.png)

![](img/d78f0d19528867fa5040f07c79370d1a_91.png)

![](img/d78f0d19528867fa5040f07c79370d1a_93.png)

![](img/d78f0d19528867fa5040f07c79370d1a_95.png)

![](img/d78f0d19528867fa5040f07c79370d1a_97.png)

# 从 public 区域移除 http 服务
firewall-cmd --zone=public --remove-service=http

# 查看是否添加成功
firewall-cmd --zone=public --list-services
```

**4. 直接开放或关闭特定端口**
除了通过服务名，也可以直接操作端口：
```bash
# 在 public 区域永久开放 8080/TCP 端口
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --reload

# 关闭 8080/TCP 端口
firewall-cmd --zone=public --remove-port=8080/tcp --permanent
firewall-cmd --reload
```

![](img/d78f0d19528867fa5040f07c79370d1a_99.png)

![](img/d78f0d19528867fa5040f07c79370d1a_101.png)

---

## 总结
本节课中我们一起学习了：
1.  **网络协议与端口**：理解了HTTP/HTTPS、SSH、FTP等常见协议的作用，以及端口作为服务“门牌号”的概念。这是网络通信的基石。
2.  **firewalld防火墙**：掌握了firewalld作为软件防火墙的基本工作原理，特别是其**区域（Zone）** 管理模型。我们学习了如何查看、更改默认区域，以及如何在区域中添加或移除服务/端口规则，从而控制对服务器上特定应用的访问。

![](img/d78f0d19528867fa5040f07c79370d1a_103.png)

![](img/d78f0d19528867fa5040f07c79370d1a_104.png)

![](img/d78f0d19528867fa5040f07c79370d1a_106.png)

虽然firewalld是RHEL 7+的默认工具，但在实际生产环境中，传统的 **iptables** 因其灵活性和广泛的应用知识库，仍然被大量使用。下节课我们将深入探讨功能强大的iptables防火墙。