# Linux最全RHCSA+RHCE培训教程合集：P55：红帽RHCE-19.系统安全防护之iptables防火墙 🔥

![](img/63e51ab6a0d29b969032681dfe4be5ea_1.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_3.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_4.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_6.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_8.png)

在本节课中，我们将学习如何配置和使用iptables防火墙，以实现网络访问控制、数据包转发和网络地址转换（NAT）等功能。我们将通过一个模拟的企业网络环境，从环境准备开始，逐步配置防火墙规则，并理解其工作原理。

![](img/63e51ab6a0d29b969032681dfe4be5ea_10.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_12.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_14.png)

## 环境准备与IP规划

上一节我们介绍了iptables的基本概念和链。本节中，我们来看看如何为防火墙实验搭建一个模拟的网络环境。

![](img/63e51ab6a0d29b969032681dfe4be5ea_16.png)

我们需要准备三台虚拟机：
1.  **防火墙主机**：拥有两块网卡，分别连接内部网络和外部网络。
2.  **内部网站服务器**：位于内部网络，提供Web服务。
3.  **外部客户端**：位于外部网络，用于模拟外部用户访问。

![](img/63e51ab6a0d29b969032681dfe4be5ea_18.png)

以下是具体的IP地址规划与配置步骤。

![](img/63e51ab6a0d29b969032681dfe4be5ea_20.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_22.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_24.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_26.png)

### 配置防火墙主机的网卡

![](img/63e51ab6a0d29b969032681dfe4be5ea_28.png)

防火墙主机需要配置两块网卡。内网网卡（ens32）的IP地址为 `192.168.0.80`。外网网卡（ens34）是新添加的，我们需要为其配置IP地址并生成永久配置文件。

![](img/63e51ab6a0d29b969032681dfe4be5ea_30.png)

最简单的方法是使用 `nmtui` 命令进行交互式配置。

```bash
nmtui
```

![](img/63e51ab6a0d29b969032681dfe4be5ea_32.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_34.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_36.png)

执行命令后，按以下步骤操作：
1.  选择“编辑连接”。
2.  选择“有线连接 1”（对应ens34网卡）。
3.  将连接名称改为 `ens34`。
4.  在“IPv4配置”处，将“自动”改为“手动”。
5.  点击“显示”按钮，添加IP地址 `192.168.1.100`。外网网卡通常不需要配置网关。
6.  一路选择“确定”或“返回”直至退出。

![](img/63e51ab6a0d29b969032681dfe4be5ea_38.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_40.png)

配置完成后，重启网络服务并检查。

![](img/63e51ab6a0d29b969032681dfe4be5ea_42.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_44.png)

```bash
systemctl restart network
ip a
```

![](img/63e51ab6a0d29b969032681dfe4be5ea_46.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_48.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_50.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_52.png)

此时，`/etc/sysconfig/network-scripts/` 目录下会自动生成 `ifcfg-ens34` 配置文件，IP地址永久生效。

![](img/63e51ab6a0d29b969032681dfe4be5ea_54.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_56.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_58.png)

### 配置内部网站服务器

![](img/63e51ab6a0d29b969032681dfe4be5ea_60.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_62.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_64.png)

内部网站服务器（web26）的网关需要指向防火墙的内网网卡IP，这样它的数据包才能通过防火墙转发。

![](img/63e51ab6a0d29b969032681dfe4be5ea_66.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_68.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_70.png)

编辑其网卡配置文件：
```bash
vim /etc/sysconfig/network-scripts/ifcfg-ens32
```
将 `GATEWAY` 修改为 `192.168.0.80`，保存退出后重启网络。

![](img/63e51ab6a0d29b969032681dfe4be5ea_72.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_74.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_75.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_77.png)

接着，安装并启动Web服务（httpd），并创建一个测试页面。
```bash
yum install -y httpd
systemctl stop firewalld
systemctl disable firewalld
systemctl start httpd
echo "web26" > /var/www/html/index.html
```

![](img/63e51ab6a0d29b969032681dfe4be5ea_78.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_80.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_82.png)

### 配置外部客户端

![](img/63e51ab6a0d29b969032681dfe4be5ea_84.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_86.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_88.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_90.png)

外部客户端需要与防火墙的外网网卡在同一个网段，并将其网关指向防火墙的外网IP。

![](img/63e51ab6a0d29b969032681dfe4be5ea_91.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_93.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_95.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_97.png)

编辑其网卡配置文件：
```bash
vim /etc/sysconfig/network-scripts/ifcfg-ens32
```
将 `IPADDR` 修改为 `192.168.1.24`，将 `GATEWAY` 修改为 `192.168.1.100`。保存退出后重启网络。

![](img/63e51ab6a0d29b969032681dfe4be5ea_99.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_101.png)

至此，环境搭建完成。拓扑逻辑为：客户端（1.24） -> 防火墙外网卡（1.100） -> 防火墙内网卡（0.80） -> 内部服务器（0.26）。

## 配置防火墙转发与基础规则

![](img/63e51ab6a0d29b969032681dfe4be5ea_103.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_105.png)

上一节我们完成了网络环境的搭建。本节中，我们来看看如何开启防火墙的路由转发功能，并配置基础的访问控制规则。

![](img/63e51ab6a0d29b969032681dfe4be5ea_107.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_109.png)

首先，在防火墙主机上开启内核的IP转发功能。
```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```
或者永久生效：
```bash
echo “net.ipv4.ip_forward = 1” >> /etc/sysctl.conf
sysctl -p
```

![](img/63e51ab6a0d29b969032681dfe4be5ea_111.png)

此时，从客户端（1.24）应该可以访问到内部服务器（0.26）的Web页面。这证明了数据包可以通过防火墙成功转发。

接下来，我们配置一条iptables规则，拒绝该客户端访问我们的Web服务。这条规则需要添加在 `FORWARD` 链上，因为数据包是经过防火墙转发的。

```bash
iptables -I FORWARD -s 192.168.1.24 -p tcp --dport 80 -j REJECT
```

规则解释：
*   `-I FORWARD`：在FORWARD链的首部插入规则。
*   `-s 192.168.1.24`：匹配源IP地址为192.168.1.24的数据包。
*   `-p tcp --dport 80`：匹配使用TCP协议且目标端口为80（HTTP）的数据包。
*   `-j REJECT`：对此类数据包执行REJECT操作（拒绝并返回拒绝信息）。

![](img/63e51ab6a0d29b969032681dfe4be5ea_113.png)

配置后，客户端再次访问Web服务器将会收到“拒绝连接”的提示。这就是网络型防火墙保护内部服务器的基本应用。

![](img/63e51ab6a0d29b969032681dfe4be5ea_115.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_117.png)

## iptables扩展模块应用

上一节我们配置了基于IP地址的基础过滤规则。本节中，我们来看看如何使用iptables的扩展模块实现更精细的控制。

![](img/63e51ab6a0d29b969032681dfe4be5ea_119.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_121.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_123.png)

### 基于MAC地址过滤

![](img/63e51ab6a0d29b969032681dfe4be5ea_125.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_127.png)

如果客户端更换了IP地址（例如从1.24改为1.25），之前的IP过滤规则就会失效。此时，可以基于其网卡的物理地址（MAC地址）进行过滤，因为MAC地址通常不会改变。

![](img/63e51ab6a0d29b969032681dfe4be5ea_129.png)

首先，在防火墙主机上安装 `nmap` 工具来扫描客户端的MAC地址。
```bash
yum install -y nmap
nmap -sn 192.168.1.25
```
在扫描结果中找到 `MAC Address` 字段。

然后，使用 `mac` 模块添加规则：
```bash
iptables -I FORWARD -p tcp --dport 80 -m mac --mac-source 客户端MAC地址 -j REJECT
```
将命令中的“客户端MAC地址”替换为实际扫描到的值。这样，无论该客户端如何更换IP，只要网卡不变，就无法访问Web服务。

![](img/63e51ab6a0d29b969032681dfe4be5ea_131.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_133.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_135.png)

### 基于多端口过滤

如果需要一次性控制对多个端口的访问（例如FTP的21端口、HTTP的80端口、HTTPS的443端口、MySQL的3306端口），可以使用 `multiport` 模块。

![](img/63e51ab6a0d29b969032681dfe4be5ea_137.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_139.png)

```bash
iptables -I FORWARD -p tcp -m multiport --dports 21,80,443,3306 -j REJECT
```
这条规则会拒绝访问目标端口为21,80,443,3306的所有TCP数据包。

![](img/63e51ab6a0d29b969032681dfe4be5ea_141.png)

### 基于IP地址范围过滤

如果需要拒绝一个连续IP地址范围内的所有主机，可以使用 `iprange` 模块。

![](img/63e51ab6a0d29b969032681dfe4be5ea_143.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_145.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_146.png)

```bash
iptables -I FORWARD -p tcp --dport 80 -m iprange --src-range 192.168.1.21-192.168.1.30 -j REJECT
```
这条规则会拒绝源IP在 `192.168.1.21` 到 `192.168.1.30` 这个范围内的所有主机访问Web服务。

## 配置SNAT实现共享上网

上一节我们学习了各种过滤规则。本节中，我们来看看如何利用iptables的NAT功能，让内部私有地址的主机能够访问外部网络，即共享上网（SNAT）。

![](img/63e51ab6a0d29b969032681dfe4be5ea_148.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_150.png)

我们调整一下实验角色：
*   **内部主机**：之前的客户端（IP: 192.168.1.25），网关指向防火墙的“内网”IP（现在指1.100）。
*   **防火墙**：ens32（IP: 192.168.0.80）作为“外网”卡；ens34（IP: 192.168.1.100）作为“内网”卡。
*   **外部服务器**：之前的Web服务器（IP: 192.168.0.26）。

![](img/63e51ab6a0d29b969032681dfe4be5ea_152.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_154.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_155.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_157.png)

目标：让内部主机（1.25）能够访问外部服务器（0.26），并且在外部服务器看来，访问者是防火墙的外网IP（0.80）。

![](img/63e51ab6a0d29b969032681dfe4be5ea_159.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_161.png)

配置SNAT规则，需要操作 `nat` 表的 `POSTROUTING` 链（在数据包路由之后，即将发出之前进行地址转换）。

![](img/63e51ab6a0d29b969032681dfe4be5ea_163.png)

```bash
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -p tcp -j SNAT --to-source 192.168.0.80
```

![](img/63e51ab6a0d29b969032681dfe4be5ea_165.png)

规则解释：
*   `-t nat`：指定操作 `nat` 表。
*   `-A POSTROUTING`：在POSTROUTING链末尾追加规则。
*   `-s 192.168.1.0/24`：匹配源地址为 `192.168.1.0/24` 网段的数据包（即所有内部主机）。
*   `-j SNAT`：执行源地址转换。
*   `--to-source 192.168.0.80`：将源地址转换为 `192.168.0.80`。

![](img/63e51ab6a0d29b969032681dfe4be5ea_167.png)

配置完成后，从内部主机（1.25）访问外部Web服务器（0.26）。然后在外部服务器上查看Apache的访问日志：
```bash
tail -f /var/log/httpd/access_log
```
你会发现，日志中记录的访问者IP是 `192.168.0.80`（防火墙的外网IP），而不是内部主机的真实IP `192.168.1.25`。这证明了SNAT转换成功。

![](img/63e51ab6a0d29b969032681dfe4be5ea_169.png)

## 规则保存与清理

![](img/63e51ab6a0d29b969032681dfe4be5ea_171.png)

本节课中我们一起学习了iptables防火墙的各种配置。最后，需要了解如何永久保存规则以及如何清理环境。

![](img/63e51ab6a0d29b969032681dfe4be5ea_173.png)

iptables规则默认是临时的，重启后会丢失。要永久保存，请执行：
```bash
service iptables save
```
此命令会将当前规则保存到 `/etc/sysconfig/iptables` 文件中。系统重启时会自动加载此文件中的规则。

如果需要清空所有规则（包括filter, nat, mangle表），可以依次清空各表：
```bash
iptables -F
iptables -t nat -F
iptables -t mangle -F
```
或者，直接删除保存的规则文件，然后重启iptables服务：
```bash
rm -f /etc/sysconfig/iptables
systemctl restart iptables
```

![](img/63e51ab6a0d29b969032681dfe4be5ea_175.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_176.png)

![](img/63e51ab6a0d29b969032681dfe4be5ea_178.png)

**总结**：在本节课中，我们系统地实践了iptables防火墙的配置。我们从搭建一个模拟的企业网络环境开始，逐步配置了数据包转发（FORWARD链）、基于IP/MAC/端口范围的访问控制、以及实现共享上网的源地址转换（SNAT）。这些技能是构建安全、可控网络环境的基础，需要结合实验反复练习以加深理解。