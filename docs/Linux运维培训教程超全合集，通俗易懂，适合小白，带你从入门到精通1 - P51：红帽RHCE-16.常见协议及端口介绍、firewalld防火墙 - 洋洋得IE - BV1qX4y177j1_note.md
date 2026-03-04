# Linux运维培训教程：16：常见协议及端口介绍、firewalld防火墙 🔥

![](img/0df91bc9273a3e99fee1aa855d95acef_1.png)

## 概述
在本节课中，我们将学习网络中的常见协议与端口号的概念，并深入了解Linux系统中的firewalld防火墙。理解这些基础知识对于管理服务器和保障网络安全至关重要。

![](img/0df91bc9273a3e99fee1aa855d95acef_3.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_5.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_7.png)

---

## 常见协议与端口介绍

上一节我们介绍了网络的基本概念，本节中我们来看看数据传输所依赖的“规则”——协议，以及访问服务的“入口”——端口。

![](img/0df91bc9273a3e99fee1aa855d95acef_9.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_11.png)

在互联网中，数据传输需要遵循特定的规则，这些规则被称为**协议**。例如，访问网站时，浏览器与服务器之间通常使用**HTTP**或**HTTPS**协议进行通信。

![](img/0df91bc9273a3e99fee1aa855d95acef_13.png)

*   **HTTP**：超文本传输协议，是一种明文协议。数据在传输过程中未经加密，容易被第三方截获和分析。
*   **HTTPS**：安全的超文本传输协议，在HTTP基础上增加了SSL/TLS加密层。数据在传输过程中是加密的，即使被截获也难以破解，安全性更高。

![](img/0df91bc9273a3e99fee1aa855d95acef_15.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_17.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_19.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_21.png)

**端口**是网络通信中的一个逻辑概念，可以理解为服务器上不同网络服务的“门牌号”。一台服务器可以运行多个服务（如网站、文件共享），每个服务会监听一个或多个特定的端口。客户端通过IP地址找到服务器后，需要通过指定的端口号才能访问到对应的服务。

![](img/0df91bc9273a3e99fee1aa855d95acef_23.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_25.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_27.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_29.png)

以下是部分常见协议及其默认端口：

![](img/0df91bc9273a3e99fee1aa855d95acef_31.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_33.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_35.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_37.png)

*   **HTTP**：端口 **80**
*   **HTTPS**：端口 **443**
*   **SSH** (安全外壳协议，用于远程管理)：端口 **22**
*   **FTP** (文件传输协议)：端口 **20** (数据), **21** (控制)
*   **DNS** (域名解析协议)：端口 **53**

在Linux系统中，可以查看 `/etc/services` 文件来了解更多的协议与端口对应关系。
```bash
cat /etc/services
```

---

## firewalld防火墙基础

![](img/0df91bc9273a3e99fee1aa855d95acef_39.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_41.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_43.png)

理解了协议和端口后，我们来看如何控制对这些服务的访问，这就是防火墙的作用。防火墙工作在主机或网络的边缘，根据预设的规则对进出的数据包进行检查和过滤。

![](img/0df91bc9273a3e99fee1aa855d95acef_45.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_47.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_49.png)

### 防火墙种类与CentOS中的工具
防火墙主要分为**软件防火墙**和**硬件防火墙**。我们学习的是软件防火墙。
在CentOS 7及更高版本中，默认的防火墙管理工具是 **`firewalld`**，它通过 `firewalld` 服务和一个名为 `firewall-cmd` 的命令行工具进行管理。其底层功能由Linux内核的 `netfilter` 模块实现。

![](img/0df91bc9273a3e99fee1aa855d95acef_51.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_53.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_55.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_57.png)

### firewalld的核心概念：区域（Zone）
`firewalld` 引入了“区域”的概念，每个区域定义了一组预设的规则，适用于不同的信任级别。系统有一个**默认区域**，网卡可以绑定到特定区域。

![](img/0df91bc9273a3e99fee1aa855d95acef_59.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_61.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_63.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_65.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_67.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_69.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_71.png)

以下是几个关键区域：
*   **public（默认）**： 仅允许访问本机的SSH、DHCP等少量服务。
*   **trusted**： 允许所有访问。
*   **block**： 拒绝所有传入连接，并回应一个拒绝消息。
*   **drop**： 丢弃所有传入的数据包，不进行任何回应。（对攻击者常用此策略）

![](img/0df91bc9273a3e99fee1aa855d95acef_73.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_75.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_77.png)

### 基本操作演示
我们将通过一个简单的Web服务（`httpd`）来演示防火墙规则的影响。

![](img/0df91bc9273a3e99fee1aa855d95acef_79.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_81.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_83.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_85.png)

1.  **安装并启动Web服务**
    ```bash
    yum install -y httpd
    systemctl start httpd
    systemctl status httpd
    ```
    使用 `ss -ntlp | grep httpd` 命令可以查看到 `httpd` 服务正在监听 **80** 端口。

![](img/0df91bc9273a3e99fee1aa855d95acef_87.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_89.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_91.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_93.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_95.png)

2.  **查看与修改默认区域**
    *   查看当前默认区域：
        ```bash
        firewall-cmd --get-default-zone
        ```
    *   将默认区域修改为 `trusted`（允许所有访问）：
        ```bash
        firewall-cmd --set-default-zone=trusted
        ```
        此时，从浏览器访问服务器的IP地址，应该可以看到Apache的测试页面。

    *   将默认区域修改为 `block`（拒绝所有访问）：
        ```bash
        firewall-cmd --set-default-zone=block
        ```
        再次从浏览器访问，连接会超时或被拒绝。

3.  **为特定区域添加/移除服务（协议）**
    更常见的做法不是切换整个区域，而是在默认的 `public` 区域中放行特定服务。
    *   首先，将默认区域改回 `public`：
        ```bash
        firewall-cmd --set-default-zone=public
        ```
    *   查看 `public` 区域当前的规则：
        ```bash
        firewall-cmd --zone=public --list-all
        ```
    *   允许HTTP协议（即放行80端口）：
        ```bash
        firewall-cmd --zone=public --add-service=http --permanent
        firewall-cmd --reload
        ```
        `--permanent` 表示永久生效，`--reload` 是重载配置使其立即生效。
    *   此时，Web服务又可以正常访问了。
    *   移除规则：
        ```bash
        firewall-cmd --zone=public --remove-service=http --permanent
        firewall-cmd --reload
        ```

---

![](img/0df91bc9273a3e99fee1aa855d95acef_97.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_99.png)

## 总结
本节课中我们一起学习了：
1.  **协议与端口**：理解了HTTP/HTTPS等协议的作用，以及端口作为服务“门牌号”的重要性。
2.  **firewalld防火墙**：了解了防火墙的基本功能，掌握了 `firewalld` 的区域（Zone）概念，并学会了使用 `firewall-cmd` 命令查看、修改默认区域以及为区域添加或移除服务规则的基本操作。

![](img/0df91bc9273a3e99fee1aa855d95acef_101.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_103.png)

![](img/0df91bc9273a3e99fee1aa855d95acef_105.png)

虽然 `firewalld` 是CentOS 7/8的默认工具，但在实际生产环境中，传统的 **`iptables`** 因其直接、灵活的特点仍然被广泛使用。下节课我们将重点学习 `iptables` 的配置与管理。