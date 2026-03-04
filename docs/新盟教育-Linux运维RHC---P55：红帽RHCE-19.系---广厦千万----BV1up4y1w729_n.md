# Linux系统安全防护：19：iptables防火墙配置实战 🛡️

![](img/bf13c8ac8da02bf7416d902f6a501924_1.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_3.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_4.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_6.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_8.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_10.png)

在本节课中，我们将学习如何使用iptables配置网络防火墙，实现数据包过滤、地址转换等核心安全功能。课程将涵盖环境准备、规则配置、扩展模块使用以及共享上网（SNAT）的实现。

![](img/bf13c8ac8da02bf7416d902f6a501924_12.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_13.png)

## 环境准备与IP规划

![](img/bf13c8ac8da02bf7416d902f6a501924_15.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_17.png)

上一节我们介绍了防火墙的基本概念，本节中我们来看看如何为实验搭建网络环境。实验需要三台虚拟机：防火墙主机、内部网站服务器和外部客户端。

![](img/bf13c8ac8da02bf7416d902f6a501924_19.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_21.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_23.png)

以下是网络拓扑与IP规划：
*   **防火墙主机 (iptables)**:
    *   内网网卡 (ens33): `192.168.0.80`
    *   外网网卡 (ens34): `192.168.1.100`
*   **内部网站服务器 (web26)**:
    *   IP地址: `192.168.0.26`
    *   网关: `192.168.0.80` (指向防火墙内网IP)
*   **外部客户端 (client)**:
    *   IP地址: `192.168.1.24`
    *   网关: `192.168.1.100` (指向防火墙外网IP)

![](img/bf13c8ac8da02bf7416d902f6a501924_25.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_27.png)

## 配置网络与部署服务

![](img/bf13c8ac8da02bf7416d902f6a501924_29.png)

首先，我们需要为防火墙主机新添加的外网网卡配置一个永久IP地址。

![](img/bf13c8ac8da02bf7416d902f6a501924_31.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_33.png)

### 使用nmtui配置IP地址

![](img/bf13c8ac8da02bf7416d902f6a501924_35.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_37.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_38.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_40.png)

对于新添加且没有配置文件的网卡，可以使用`nmtui`命令进行交互式配置，它会自动生成配置文件。

![](img/bf13c8ac8da02bf7416d902f6a501924_42.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_44.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_46.png)

1.  运行命令：`nmtui`
2.  选择“编辑连接”。
3.  选择对应的“有线连接”，将连接名称改为网卡名（如`ens34`）。
4.  在“IPv4配置”中选择“手动”。
5.  添加IP地址（如`192.168.1.100`），无需配置网关。
6.  保存并退出，然后重启网络服务：`systemctl restart network`

![](img/bf13c8ac8da02bf7416d902f6a501924_47.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_49.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_51.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_53.png)

配置完成后，使用`ip addr`命令检查IP，并确认配置文件`/etc/sysconfig/network-scripts/ifcfg-ens34`已生成。

![](img/bf13c8ac8da02bf7416d902f6a501924_55.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_56.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_58.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_60.png)

### 配置服务器与客户端

![](img/bf13c8ac8da02bf7416d902f6a501924_62.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_64.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_66.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_68.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_69.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_71.png)

按照IP规划，分别修改网站服务器和客户端的IP地址与网关。

![](img/bf13c8ac8da02bf7416d902f6a501924_72.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_74.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_76.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_78.png)

*   **网站服务器 (web26)**:
    *   编辑文件：`/etc/sysconfig/network-scripts/ifcfg-ens33`
    *   设置`GATEWAY=192.168.0.80`
*   **客户端 (client)**:
    *   编辑文件：`/etc/sysconfig/network-scripts/ifcfg-ens33`
    *   设置`IPADDR=192.168.1.24`，`GATEWAY=192.168.1.100`

![](img/bf13c8ac8da02bf7416d902f6a501924_80.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_82.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_84.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_86.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_88.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_90.png)

修改后均需重启网络服务。

![](img/bf13c8ac8da02bf7416d902f6a501924_92.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_94.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_96.png)

### 部署Web服务

![](img/bf13c8ac8da02bf7416d902f6a501924_98.png)

在网站服务器上安装并启动Apache服务，创建一个测试页面。

![](img/bf13c8ac8da02bf7416d902f6a501924_99.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_101.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_103.png)

```bash
yum -y install httpd
systemctl start httpd
systemctl enable httpd
echo "web26" > /var/www/html/index.html
```

![](img/bf13c8ac8da02bf7416d902f6a501924_105.png)

## 配置iptables转发与过滤规则

环境就绪后，我们开始配置防火墙的核心功能。首先确保防火墙主机开启了内核的IP转发功能。

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

![](img/bf13c8ac8da02bf7416d902f6a501924_107.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_108.png)

### 配置FORWARD链规则

![](img/bf13c8ac8da02bf7416d902f6a501924_110.png)

我们的目标是让外部客户端（`192.168.1.24`）的请求能通过防火墙转发到内部服务器（`192.168.0.26:80`），然后通过规则进行控制。

![](img/bf13c8ac8da02bf7416d902f6a501924_112.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_114.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_115.png)

1.  **清空现有规则**：`iptables -F`
2.  **测试基础连通性**：此时客户端应能访问到内部网站。
3.  **添加拒绝规则**：在`FORWARD`链上添加规则，拒绝特定IP访问Web服务。
    ```bash
    iptables -I FORWARD -s 192.168.1.24 -p tcp --dport 80 -j REJECT
    ```
    此规则生效后，客户端`192.168.1.24`将无法访问网站。

![](img/bf13c8ac8da02bf7416d902f6a501924_117.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_119.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_121.png)

### 使用扩展模块

iptables的扩展模块提供了更灵活的匹配条件。

![](img/bf13c8ac8da02bf7416d902f6a501924_123.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_124.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_126.png)

*   **基于MAC地址过滤**：
    即使客户端更改IP，只要网卡不变，仍可通过MAC地址进行限制。首先使用`nmap`扫描客户端MAC地址。
    ```bash
    yum -y install nmap
    nmap -sn 192.168.1.24
    ```
    然后使用`mac`模块添加规则：
    ```bash
    iptables -I FORWARD -m mac --mac-source 客户端MAC地址 -p tcp --dport 80 -j REJECT
    ```
*   **基于多端口匹配**：
    使用`multiport`模块可以一条规则匹配多个目标端口。
    ```bash
    iptables -I FORWARD -p tcp -m multiport --dports 21,80,443,3306 -j REJECT
    ```
*   **基于IP范围匹配**：
    使用`iprange`模块可以匹配一个连续的IP地址范围。
    ```bash
    iptables -I FORWARD -m iprange --src-range 192.168.1.20-192.168.1.30 -j REJECT
    ```

![](img/bf13c8ac8da02bf7416d902f6a501924_128.png)

## 配置SNAT实现共享上网

![](img/bf13c8ac8da02bf7416d902f6a501924_130.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_132.png)

SNAT（源地址转换）可使内网私有IP通过防火墙的公网IP访问互联网。

我们调整实验角色：让`client (192.168.1.25)`作为内网主机，`web26`作为外网服务器，防火墙`ens33 (192.168.0.80)`作为公网出口。

![](img/bf13c8ac8da02bf7416d902f6a501924_134.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_136.png)

在防火墙上配置SNAT规则：

```bash
iptables -t nat -I POSTROUTING -s 192.168.1.0/24 -p tcp -j SNAT --to-source 192.168.0.80
```

![](img/bf13c8ac8da02bf7416d902f6a501924_138.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_140.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_142.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_144.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_145.png)

**规则解读**：
*   `-t nat`：操作`nat`表。
*   `-I POSTROUTING`：在`POSTROUTING`链（路由后处理）插入规则。
*   `-s 192.168.1.0/24`：匹配源地址为`192.168.1.0/24`网段的数据包。
*   `-j SNAT --to-source 192.168.0.80`：进行SNAT，将源地址转换为`192.168.0.80`。

![](img/bf13c8ac8da02bf7416d902f6a501924_147.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_149.png)

配置后，内网主机访问外网服务器时，外网服务器日志中看到的访问源IP将是防火墙的公网IP（`192.168.0.80`）。

![](img/bf13c8ac8da02bf7416d902f6a501924_151.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_153.png)

## 规则持久化与清理

![](img/bf13c8ac8da02bf7416d902f6a501924_155.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_157.png)

iptables规则默认是临时的。如需永久保存，请执行：

![](img/bf13c8ac8da02bf7416d902f6a501924_159.png)

```bash
service iptables save
```
规则将保存至`/etc/sysconfig/iptables`文件。如需彻底清空所有规则，可清空所有链并删除此保存文件。

![](img/bf13c8ac8da02bf7416d902f6a501924_161.png)

```bash
iptables -F
iptables -t nat -F
rm -f /etc/sysconfig/iptables
```

![](img/bf13c8ac8da02bf7416d902f6a501924_163.png)

## 总结

![](img/bf13c8ac8da02bf7416d902f6a501924_165.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_167.png)

![](img/bf13c8ac8da02bf7416d902f6a501924_169.png)

本节课中我们一起学习了iptables防火墙的实战配置。我们从实验环境搭建开始，逐步配置了网络地址、部署了服务，并重点讲解了如何在`FORWARD`链上设置过滤规则来控制网络访问。此外，我们还探讨了使用MAC地址、多端口、IP范围等扩展模块进行更精细的控制，最后实现了SNAT功能使内网主机能够共享一个公网IP访问外部网络。记住，复杂的防火墙规则通常不需要死记硬背，理解其原理并在需要时查阅文档或笔记是关键。