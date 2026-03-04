# Linux运维进阶：P54：系统安全防护之iptables防火墙 🔥

![](img/aed24f05d061d95ab298d7767ffa68ba_1.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_3.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_4.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_6.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_8.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_10.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_12.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_14.png)

在本节课中，我们将学习iptables防火墙的核心概念与配置方法，包括网络地址转换（NAT）、数据包转发以及基于MAC地址、多端口和IP范围的访问控制规则。课程将通过一个模拟的企业网络环境，演示如何配置防火墙以实现内部服务器的保护和共享上网功能。

![](img/aed24f05d061d95ab298d7767ffa68ba_16.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_18.png)

## 环境准备与网络规划 🌐

![](img/aed24f05d061d95ab298d7767ffa68ba_20.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_22.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_24.png)

上一节我们介绍了防火墙的基本概念，本节中我们来看看如何搭建实验环境。实验需要三台虚拟机：一台作为防火墙主机，一台作为内部Web服务器，一台作为外部客户端。

![](img/aed24f05d061d95ab298d7767ffa68ba_26.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_28.png)

以下是网络拓扑规划：
*   **防火墙主机** (`iptables`)：
    *   内网网卡 (`ens32`)：`192.168.0.80`
    *   外网网卡 (`ens34`)：`192.168.1.100`
*   **内部Web服务器** (`web26`)：
    *   网卡 (`ens32`)：`192.168.0.26`
    *   网关：`192.168.0.80` (指向防火墙内网IP)
*   **外部客户端** (`client`)：
    *   网卡 (`ens32`)：`192.168.1.24` (初始IP)
    *   网关：`192.168.1.100` (指向防火墙外网IP)

![](img/aed24f05d061d95ab298d7767ffa68ba_30.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_32.png)

## 配置网络与部署服务 ⚙️

![](img/aed24f05d061d95ab298d7767ffa68ba_34.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_36.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_38.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_40.png)

环境规划好后，我们需要为每台机器配置正确的IP地址并部署Web服务。

![](img/aed24f05d061d95ab298d7767ffa68ba_42.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_44.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_46.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_48.png)

### 配置防火墙主机的网卡

![](img/aed24f05d061d95ab298d7767ffa68ba_50.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_52.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_54.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_56.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_58.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_60.png)

防火墙主机的`ens34`网卡是新添加的，默认没有配置文件。使用`nmtui`命令可以交互式地配置IP并自动生成配置文件。

![](img/aed24f05d061d95ab298d7767ffa68ba_62.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_64.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_66.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_68.png)

```bash
nmtui
```
在图形界面中，选择“编辑连接”，找到“有线连接 1”，将其名称改为`ens34`，IPv4配置改为手动，并添加IP地址`192.168.1.100`。配置完成后，重启网络服务使配置生效。

![](img/aed24f05d061d95ab298d7767ffa68ba_70.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_72.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_73.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_75.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_76.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_78.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_80.png)

### 配置内部服务器与客户端

![](img/aed24f05d061d95ab298d7767ffa68ba_82.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_84.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_86.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_88.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_89.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_91.png)

在`web26`服务器上，需要修改网关指向防火墙。
```bash
# 编辑网络配置文件
vi /etc/sysconfig/network-scripts/ifcfg-ens32
# 将GATEWAY修改为 192.168.0.80
systemctl restart network
```
同时，在`web26`上安装并启动Apache服务，创建一个测试页面。
```bash
yum -y install httpd
systemctl start httpd
echo "web26" > /var/www/html/index.html
```

![](img/aed24f05d061d95ab298d7767ffa68ba_93.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_95.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_97.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_99.png)

在客户端上，将其IP修改为`192.168.1.24`，网关指向`192.168.1.100`。

![](img/aed24f05d061d95ab298d7767ffa68ba_101.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_103.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_105.png)

## 配置防火墙转发与基础规则 🛡️

![](img/aed24f05d061d95ab298d7767ffa68ba_107.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_109.png)

网络连通后，我们开始在防火墙主机上配置核心功能。首先，需要开启Linux内核的IP转发功能。
```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```
此时，客户端应能通过防火墙访问到内部服务器`192.168.0.26`的Web页面。

接下来，我们在防火墙的`FORWARD`链（负责转发数据包）上添加规则，拒绝特定客户端的访问。
```bash
# 拒绝IP为192.168.1.24的客户端访问内部Web服务（80端口）
iptables -A FORWARD -s 192.168.1.24 -p tcp --dport 80 -j REJECT
```
添加此规则后，该客户端将无法再访问Web服务器。

## 防火墙扩展功能 🔧

![](img/aed24f05d061d95ab298d7767ffa68ba_111.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_113.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_115.png)

基础规则可能被绕过，例如客户端更换IP地址。因此，iptables提供了一些扩展模块来实现更精细的控制。

![](img/aed24f05d061d95ab298d7767ffa68ba_117.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_119.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_121.png)

### 基于MAC地址过滤

![](img/aed24f05d061d95ab298d7767ffa68ba_123.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_125.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_127.png)

MAC地址绑定在网卡硬件上，更换IP后通常不变。我们可以先使用`nmap`扫描客户端的MAC地址，然后基于此进行过滤。
```bash
# 在防火墙上安装nmap并扫描客户端
yum -y install nmap
nmap -sn 192.168.1.25
# 输出中会包含类似 `MAC Address: 00:0C:29:XX:XX:XX (VMware)` 的信息
```
获取MAC地址后，添加基于MAC地址的拒绝规则。
```bash
iptables -A FORWARD -p tcp --dport 80 -m mac --mac-source 00:0C:29:XX:XX:XX -j REJECT
```
这样，无论客户端如何更换IP，只要网卡不变，访问都会被拒绝。

### 基于多端口和IP范围过滤

![](img/aed24f05d061d95ab298d7767ffa68ba_129.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_131.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_133.png)

以下是其他有用的扩展规则示例：
*   **多端口匹配**：一条规则控制多个端口。
    ```bash
    iptables -A FORWARD -p tcp -m multiport --dports 21,80,443,3306 -j REJECT
    ```
*   **IP范围匹配**：拒绝一个连续IP段的主机。
    ```bash
    iptables -A FORWARD -p tcp -m iprange --src-range 192.168.1.20-192.168.1.30 -j REJECT
    ```

![](img/aed24f05d061d95ab298d7767ffa68ba_135.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_137.png)

## 配置SNAT实现共享上网 🌍

![](img/aed24f05d061d95ab298d7767ffa68ba_139.png)

在企业中，内部私有IP地址的机器需要访问互联网。这可以通过防火墙的SNAT（源地址转换）功能实现。我们将实验环境角色互换，让`client`作为内部主机，`web26`作为外部服务器。

![](img/aed24f05d061d95ab298d7767ffa68ba_141.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_143.png)

配置思路是：当内部主机（`192.168.1.0/24`网段）访问外网时，将其源IP转换为防火墙的外网IP（`192.168.0.80`）。

![](img/aed24f05d061d95ab298d7767ffa68ba_145.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_147.png)

在防火墙主机上，添加NAT表规则。
```bash
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -p tcp -j SNAT --to-source 192.168.0.80
```
配置后，内部主机`client`访问外部服务器`web26`时，在`web26`的访问日志中看到的来源IP将是防火墙的`192.168.0.80`，从而实现了地址转换和共享上网。

![](img/aed24f05d061d95ab298d7767ffa68ba_149.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_151.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_152.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_154.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_156.png)

## 规则保存与持久化 💾

![](img/aed24f05d061d95ab298d7767ffa68ba_158.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_160.png)

iptables规则默认是临时的，重启后会丢失。需要将当前规则保存到配置文件中。
```bash
service iptables save
```
此命令将规则保存至`/etc/sysconfig/iptables`文件。如需永久删除规则，可以清空内存中的规则后再次保存，或直接编辑该文件。

![](img/aed24f05d061d95ab298d7767ffa68ba_162.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_164.png)

要清空所有规则，可使用以下命令：
```bash
iptables -F
iptables -t nat -F
iptables -t mangle -F
```

![](img/aed24f05d061d95ab298d7767ffa68ba_166.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_168.png)

## 总结 📚

![](img/aed24f05d061d95ab298d7767ffa68ba_170.png)

本节课中我们一起学习了iptables防火墙的实战配置。我们从实验环境搭建开始，逐步配置了网络地址、部署了服务，并重点讲解了：
1.  如何通过`FORWARD`链配置网络型防火墙，保护内部服务器。
2.  如何使用扩展模块（如`mac`、`multiport`、`iprange`）实现更强大的访问控制。
3.  如何在`NAT`表中配置`SNAT`规则，实现企业内部私有地址共享一个公网IP访问互联网。
4.  如何保存防火墙规则以实现配置持久化。

![](img/aed24f05d061d95ab298d7767ffa68ba_172.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_173.png)

![](img/aed24f05d061d95ab298d7767ffa68ba_175.png)

iptables功能强大且灵活，其规则语法需要结合实践反复练习。在实际工作中，应根据具体的网络架构和安全需求来设计和实施防火墙策略。