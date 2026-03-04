# RHCE课程：P17：网络配置基础与管理

在本节课中，我们将要学习Red Hat Enterprise Linux（RHEL）系统中的网络配置基础知识，包括网络服务的变化、网卡命名规则、配置文件结构以及使用NetworkManager工具进行网络管理。

## 网络服务演变 🕰️

上一节我们介绍了Linux系统的基础知识，本节中我们来看看网络服务的变化。

![](img/9b10b2cedeba18554ab23c61b4690551_1.png)

在RHEL 6及更早版本中，网络服务由`network`服务管理。从RHEL 7开始，系统引入了`NetworkManager`服务来管理网络。在RHEL 8中，`NetworkManager`服务是默认且不允许关闭的，它完全取代了旧的`network`服务。主要区别在于，`NetworkManager`提供了更动态的网络服务管理能力。

## 网卡命名规则 🔤

![](img/9b10b2cedeba18554ab23c61b4690551_3.png)

了解了网络服务的演变后，我们来看看网卡设备命名的变化。

在RHEL 6及之前，网卡名称通常为`eth0`、`eth1`等形式。从RHEL 7开始，网卡命名规则发生了变化，采用了基于硬件信息的命名方式，例如`ens160`。

以下是网卡命名前缀的含义：
*   **en**： 代表以太网（Ethernet）卡。
*   **wl**： 代表无线局域网（WLAN）卡。
*   **ww**： 代表无线广域网（WWAN）卡。

网卡名称的其余部分通常包含硬件位置信息，例如PCI插槽索引号。网卡的名称是可以更改的，通常可以根据其功能进行修改。

## 网络配置信息查看 🔍

![](img/9b10b2cedeba18554ab23c61b4690551_5.png)

![](img/9b10b2cedeba18554ab23c61b4690551_7.png)

![](img/9b10b2cedeba18554ab23c61b4690551_8.png)

现在，我们学习如何查看系统的网络配置信息。

![](img/9b10b2cedeba18554ab23c61b4690551_9.png)

使用`ip addr show`命令可以查看网络接口的详细信息。以下是一个输出示例的关键部分解析：

![](img/9b10b2cedeba18554ab23c61b4690551_11.png)

```
2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    inet 192.168.1.100/24 brd 192.168.1.255 ...
```

![](img/9b10b2cedeba18554ab23c61b4690551_13.png)

*   **`ens160`**： 网卡名称。
*   **`mtu 1500`**： 最大传输单元。
*   **`inet 192.168.1.100/24`**： IPv4地址和子网掩码（`/24`表示子网掩码为`255.255.255.0`）。
*   **`brd 192.168.1.255`**： 广播地址。

## 网卡配置文件详解 📄

上一节我们查看了运行时的网络信息，本节中我们来看看其持久化的配置文件。

网卡配置文件通常位于`/etc/sysconfig/network-scripts/`目录下，文件名如`ifcfg-ens160`。其内容结构如下：

![](img/9b10b2cedeba18554ab23c61b4690551_15.png)

![](img/9b10b2cedeba18554ab23c61b4690551_17.png)

```bash
TYPE=Ethernet
DEVICE=ens160
HWADDR=00:0C:29:XX:XX:XX
UUID=xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
ONBOOT=yes
BOOTPROTO=static
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=192.168.1.1
```

以下是关键参数说明：
*   **`TYPE`**： 接口类型，如`Ethernet`。
*   **`DEVICE`**： 设备名称。
*   **`BOOTPROTO`**： 启动协议，`static`（静态）、`dhcp`（动态）或`none`。
*   **`ONBOOT`**： 系统启动时是否激活此设备，`yes`或`no`。
*   **`IPADDR`**： 静态IP地址。
*   **`NETMASK`**： 子网掩码。
*   **`GATEWAY`**： 默认网关。
*   **`DNS1`**： 主DNS服务器地址。

![](img/9b10b2cedeba18554ab23c61b4690551_19.png)

![](img/9b10b2cedeba18554ab23c61b4690551_20.png)

![](img/9b10b2cedeba18554ab23c61b4690551_22.png)

![](img/9b10b2cedeba18554ab23c61b4690551_24.png)

其中，`IPADDR`、`NETMASK`、`GATEWAY`、`DNS1`和`ONBOOT=yes`是配置静态IP时最重要的部分。

## 使用NetworkManager命令行工具管理网络 🛠️

![](img/9b10b2cedeba18554ab23c61b4690551_26.png)

![](img/9b10b2cedeba18554ab23c61b4690551_28.png)

除了直接编辑配置文件，我们还可以使用`nmcli`命令来管理网络，这是NetworkManager的命令行工具。

![](img/9b10b2cedeba18554ab23c61b4690551_30.png)

以下是常用的`nmcli`命令操作：

![](img/9b10b2cedeba18554ab23c61b4690551_32.png)

**查看连接信息**
```bash
nmcli connection show
```

**修改现有连接的IP地址（设置为静态）**
```bash
nmcli connection modify “ens160” ipv4.method manual ipv4.addresses “192.168.1.100/24” ipv4.gateway “192.168.1.1” ipv4.dns “192.168.1.1” connection.autoconnect yes
```
此命令将`ens160`连接的IPv4配置方法改为手动（`manual`），并设置地址、网关和DNS。

![](img/9b10b2cedeba18554ab23c61b4690551_34.png)

![](img/9b10b2cedeba18554ab23c61b4690551_36.png)

![](img/9b10b2cedeba18554ab23c61b4690551_38.png)

**使配置更改生效**
```bash
nmcli connection up “ens160”
```

![](img/9b10b2cedeba18554ab23c61b4690551_39.png)

![](img/9b10b2cedeba18554ab23c61b4690551_41.png)

**添加一个新的连接（例如，基于DHCP）**
```bash
nmcli connection add type ethernet con-name “my-eth” ifname ens160 ipv4.method auto
```

![](img/9b10b2cedeba18554ab23c61b4690551_42.png)

## 网络组简介 🔗

最后，我们简单了解一个高级功能——网络组。

![](img/9b10b2cedeba18554ab23c61b4690551_44.png)

![](img/9b10b2cedeba18554ab23c61b4690551_46.png)

网络组（Network Teaming）是将多个物理网卡聚合为一个逻辑接口的方法，旨在提供链路冗余和增加带宽。它不同于旧版的`bonding`技术。网络组接口启动时不会自动启动其成员接口，而启动成员接口则会自动启动网络组接口。

![](img/9b10b2cedeba18554ab23c61b4690551_48.png)

![](img/9b10b2cedeba18554ab23c61b4690551_50.png)

![](img/9b10b2cedeba18554ab23c61b4690551_51.png)

创建一个网络组的基本命令示例如下：
```bash
nmcli connection add type team con-name team0 ifname team0 config ‘{“runner”: {“name”: “activebackup”}}’
nmcli connection add type team-slave con-name team0-port1 ifname ens160 master team0
nmcli connection add type team-slave con-name team0-port2 ifname ens224 master team0
```

![](img/9b10b2cedeba18554ab23c61b4690551_53.png)

![](img/9b10b2cedeba18554ab23c61b4690551_55.png)

![](img/9b10b2cedeba18554ab23c61b4690551_56.png)

---

![](img/9b10b2cedeba18554ab23c61b4690551_58.png)

![](img/9b10b2cedeba18554ab23c61b4690551_60.png)

本节课中我们一起学习了RHEL 8中的网络管理基础。我们了解了从`network`服务到`NetworkManager`的演变，掌握了新的网卡命名规则，学会了查看网络配置信息和解读网卡配置文件。同时，我们也实践了使用`nmcli`命令行工具来修改和创建网络连接，并对网络组的概念有了初步认识。这些是进行Linux系统网络管理和故障排查的核心技能。