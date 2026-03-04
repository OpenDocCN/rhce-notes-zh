# Linux运维培训教程：P54：红帽RHCE-19.系统安全防护之iptables防火墙 🔥

![](img/3cd86472d388e54c317bc692ae7430fb_1.png)

![](img/3cd86472d388e54c317bc692ae7430fb_3.png)

![](img/3cd86472d388e54c317bc692ae7430fb_4.png)

![](img/3cd86472d388e54c317bc692ae7430fb_6.png)

![](img/3cd86472d388e54c317bc692ae7430fb_8.png)

在本节课中，我们将学习iptables防火墙的配置与应用。我们将通过一个模拟企业网络环境的实验，从环境准备开始，逐步配置防火墙规则，实现网络访问控制、地址转换等核心功能，帮助你理解网络型防火墙的工作原理。

![](img/3cd86472d388e54c317bc692ae7430fb_10.png)

![](img/3cd86472d388e54c317bc692ae7430fb_12.png)

![](img/3cd86472d388e54c317bc692ae7430fb_14.png)

## 环境准备与IP规划

上一节我们介绍了防火墙的基本概念，本节中我们来看看如何搭建一个实验环境。我们需要准备三台虚拟机：一台作为防火墙主机，一台作为内部网站服务器，一台作为外部客户端。

![](img/3cd86472d388e54c317bc692ae7430fb_16.png)

![](img/3cd86472d388e54c317bc692ae7430fb_18.png)

以下是实验环境的网络拓扑与IP规划：

![](img/3cd86472d388e54c317bc692ae7430fb_20.png)

*   **防火墙主机 (iptables)**
    *   内网网卡 (ens32): `192.168.0.80`
    *   外网网卡 (ens34): `192.168.1.100`
*   **内部网站服务器 (web26)**
    *   网卡 (ens32): `192.168.0.26`
    *   网关: `192.168.0.80` (指向防火墙内网IP)
*   **外部客户端 (client)**
    *   网卡 (ens32): `192.168.1.24`
    *   网关: `192.168.1.100` (指向防火墙外网IP)

![](img/3cd86472d388e54c317bc692ae7430fb_22.png)

![](img/3cd86472d388e54c317bc692ae7430fb_24.png)

![](img/3cd86472d388e54c317bc692ae7430fb_26.png)

这个规划模拟了外部客户端通过防火墙访问内部服务器的场景。

![](img/3cd86472d388e54c317bc692ae7430fb_28.png)

## 配置网络环境

![](img/3cd86472d388e54c317bc692ae7430fb_30.png)

首先，我们需要为防火墙主机新添加的外网网卡配置IP地址。由于新网卡没有配置文件，我们使用`nmtui`命令进行交互式配置，它会自动生成永久配置文件。

![](img/3cd86472d388e54c317bc692ae7430fb_32.png)

```bash
nmtui
```
在图形界面中选择“编辑连接”，找到对应的网卡（如有线连接 1），将其名称改为`ens34`，并手动设置IPv4地址为`192.168.1.100`，无需设置网关。配置完成后，重启网络服务使配置生效。

![](img/3cd86472d388e54c317bc692ae7430fb_34.png)

![](img/3cd86472d388e54c317bc692ae7430fb_36.png)

![](img/3cd86472d388e54c317bc692ae7430fb_38.png)

```bash
systemctl restart network
ip addr show ens34 # 确认IP地址
```

![](img/3cd86472d388e54c317bc692ae7430fb_40.png)

![](img/3cd86472d388e54c317bc692ae7430fb_42.png)

![](img/3cd86472d388e54c317bc692ae7430fb_44.png)

接下来，配置内部服务器`web26`的网关，使其指向防火墙的内网IP。

![](img/3cd86472d388e54c317bc692ae7430fb_46.png)

```bash
vi /etc/sysconfig/network-scripts/ifcfg-ens32
# 修改或添加 GATEWAY=192.168.0.80
systemctl restart network
```

![](img/3cd86472d388e54c317bc692ae7430fb_48.png)

![](img/3cd86472d388e54c317bc692ae7430fb_50.png)

![](img/3cd86472d388e54c317bc692ae7430fb_52.png)

最后，配置客户端`client`的IP地址和网关，使其指向防火墙的外网IP。

![](img/3cd86472d388e54c317bc692ae7430fb_54.png)

![](img/3cd86472d388e54c317bc692ae7430fb_56.png)

![](img/3cd86472d388e54c317bc692ae7430fb_58.png)

![](img/3cd86472d388e54c317bc692ae7430fb_60.png)

```bash
vi /etc/sysconfig/network-scripts/ifcfg-ens32
# 修改 IPADDR=192.168.1.24
# 修改 GATEWAY=192.168.1.100
systemctl restart network
```

![](img/3cd86472d388e54c317bc692ae7430fb_62.png)

![](img/3cd86472d388e54c317bc692ae7430fb_64.png)

环境配置完成后，需要在防火墙主机上开启内核的IP转发功能，这是实现数据包转发的关键。

![](img/3cd86472d388e54c317bc692ae7430fb_66.png)

![](img/3cd86472d388e54c317bc692ae7430fb_68.png)

![](img/3cd86472d388e54c317bc692ae7430fb_70.png)

![](img/3cd86472d388e54c317bc692ae7430fb_72.png)

![](img/3cd86472d388e54c317bc692ae7430fb_74.png)

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

![](img/3cd86472d388e54c317bc692ae7430fb_75.png)

![](img/3cd86472d388e54c317bc692ae7430fb_77.png)

![](img/3cd86472d388e54c317bc692ae7430fb_78.png)

## 部署Web服务并测试连通性

![](img/3cd86472d388e54c317bc692ae7430fb_80.png)

![](img/3cd86472d388e54c317bc692ae7430fb_82.png)

![](img/3cd86472d388e54c317bc692ae7430fb_84.png)

![](img/3cd86472d388e54c317bc692ae7430fb_86.png)

在内部服务器`web26`上安装并启动Web服务，创建一个测试页面。

![](img/3cd86472d388e54c317bc692ae7430fb_88.png)

![](img/3cd86472d388e54c317bc692ae7430fb_90.png)

![](img/3cd86472d388e54c317bc692ae7430fb_91.png)

![](img/3cd86472d388e54c317bc692ae7430fb_93.png)

```bash
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "web26" > /var/www/html/index.html
```

![](img/3cd86472d388e54c317bc692ae7430fb_95.png)

![](img/3cd86472d388e54c317bc692ae7430fb_97.png)

![](img/3cd86472d388e54c317bc692ae7430fb_99.png)

![](img/3cd86472d388e54c317bc692ae7430fb_101.png)

![](img/3cd86472d388e54c317bc692ae7430fb_103.png)

现在，从客户端尝试访问内部服务器的Web服务。由于防火墙已开启转发功能，且尚未设置任何拦截规则，访问应该成功。

```bash
# 在客户端执行
curl http://192.168.0.26
```
如果能看到“web26”的页面内容，说明网络通路正常，防火墙正在转发数据包。

![](img/3cd86472d388e54c317bc692ae7430fb_105.png)

## 配置iptables防火墙规则

![](img/3cd86472d388e54c317bc692ae7430fb_107.png)

![](img/3cd86472d388e54c317bc692ae7430fb_109.png)

![](img/3cd86472d388e54c317bc692ae7430fb_111.png)

上一节我们完成了环境搭建和基础测试，本节中我们来看看如何使用iptables配置具体的访问控制规则。

![](img/3cd86472d388e54c317bc692ae7430fb_113.png)

### 1. 拒绝特定客户端访问

我们的目标是阻止IP为`192.168.1.24`的客户端访问内部Web服务器。规则需要添加在`FORWARD`链上，因为数据包是经过防火墙转发的。

```bash
# 在防火墙主机上执行
iptables -A FORWARD -s 192.168.1.24 -p tcp --dport 80 -j REJECT
```
**命令解读**：
*   `-A FORWARD`: 在`FORWARD`链末尾追加规则。
*   `-s 192.168.1.24`: 匹配源IP地址。
*   `-p tcp --dport 80`: 匹配TCP协议且目标端口为80（HTTP）。
*   `-j REJECT`: 执行拒绝操作，并向客户端返回拒绝信息。

配置后，客户端再次访问Web服务将被拒绝。

![](img/3cd86472d388e54c317bc692ae7430fb_115.png)

### 2. 基于MAC地址的访问控制

![](img/3cd86472d388e54c317bc692ae7430fb_117.png)

如果客户端更换了IP地址，上述基于IP的规则就会失效。此时可以使用基于网卡MAC地址的规则，因为MAC地址通常不会改变。

![](img/3cd86472d388e54c317bc692ae7430fb_119.png)

首先，在防火墙主机上安装`nmap`工具来扫描客户端的MAC地址。

![](img/3cd86472d388e54c317bc692ae7430fb_121.png)

![](img/3cd86472d388e54c317bc692ae7430fb_123.png)

![](img/3cd86472d388e54c317bc692ae7430fb_125.png)

```bash
yum install -y nmap
nmap -sn 192.168.1.24
```
从扫描结果中获取客户端的MAC地址（例如：`00:0C:29:XX:XX:XX`）。然后，使用`mac`模块添加规则。

![](img/3cd86472d388e54c317bc692ae7430fb_127.png)

```bash
iptables -A FORWARD -p tcp --dport 80 -m mac --mac-source 00:0C:29:XX:XX:XX -j REJECT
```
这样，无论该客户端如何更换IP，只要其网卡不变，就无法访问Web服务。

![](img/3cd86472d388e54c317bc692ae7430fb_129.png)

![](img/3cd86472d388e54c317bc692ae7430fb_131.png)

### 3. 基于多端口和IP范围的访问控制

iptables还支持更灵活的匹配条件。

![](img/3cd86472d388e54c317bc692ae7430fb_133.png)

![](img/3cd86472d388e54c317bc692ae7430fb_135.png)

![](img/3cd86472d388e54c317bc692ae7430fb_137.png)

![](img/3cd86472d388e54c317bc692ae7430fb_139.png)

以下是同时拒绝访问FTP(21)、HTTP(80)、HTTPS(443)、MySQL(3306)服务的规则示例：

```bash
iptables -A FORWARD -p tcp -m multiport --dports 21,80,443,3306 -j REJECT
```

![](img/3cd86472d388e54c317bc692ae7430fb_141.png)

![](img/3cd86472d388e54c317bc692ae7430fb_143.png)

以下是拒绝一个IP段（`192.168.1.20`到`192.168.1.30`）的所有访问的规则示例：

![](img/3cd86472d388e54c317bc692ae7430fb_145.png)

```bash
iptables -A FORWARD -m iprange --src-range 192.168.1.20-192.168.1.30 -j REJECT
```

## 配置SNAT实现共享上网

![](img/3cd86472d388e54c317bc692ae7430fb_147.png)

![](img/3cd86472d388e54c317bc692ae7430fb_149.png)

在企业中，内部私有地址的机器需要通过防火墙访问互联网。这可以通过配置源地址转换（SNAT）来实现，将内部地址转换为防火墙的公网地址。

我们将实验环境角色互换：
*   `client (192.168.1.25)` 作为内部主机。
*   `web26 (192.168.0.26)` 作为外部网络（模拟互联网）。
*   防火墙`ens34 (192.168.1.100)`作为内网卡，`ens32 (192.168.0.80)`作为外网卡。

目标是让内部主机`192.168.1.25`能够通过防火墙的外网IP`192.168.0.80`访问外部网络`192.168.0.26`。

![](img/3cd86472d388e54c317bc692ae7430fb_151.png)

![](img/3cd86472d388e54c317bc692ae7430fb_153.png)

![](img/3cd86472d388e54c317bc692ae7430fb_155.png)

配置SNAT规则，该规则需要添加到`nat`表的`POSTROUTING`链。

![](img/3cd86472d388e54c317bc692ae7430fb_157.png)

![](img/3cd86472d388e54c317bc692ae7430fb_159.png)

![](img/3cd86472d388e54c317bc692ae7430fb_160.png)

![](img/3cd86472d388e54c317bc692ae7430fb_162.png)

![](img/3cd86472d388e54c317bc692ae7430fb_164.png)

```bash
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -p tcp -j SNAT --to-source 192.168.0.80
```
**命令解读**：
*   `-t nat`: 指定操作`nat`表。
*   `-A POSTROUTING`: 在`POSTROUTING`链（路由后，数据包发出前）追加规则。
*   `-s 192.168.1.0/24`: 匹配源地址为`192.168.1.0`网段的所有主机。
*   `-j SNAT --to-source 192.168.0.80`: 执行SNAT，将源地址转换为`192.168.0.80`。

![](img/3cd86472d388e54c317bc692ae7430fb_166.png)

![](img/3cd86472d388e54c317bc692ae7430fb_168.png)

配置完成后，从内部主机`192.168.1.25`访问外部服务器`192.168.0.26`。在外部服务器上查看Web访问日志，会发现访问来源IP是防火墙的外网IP`192.168.0.80`，而不是内部主机的真实IP，这证明了SNAT生效。

![](img/3cd86472d388e54c317bc692ae7430fb_170.png)

```bash
# 在外部服务器 web26 上查看访问日志
tail -f /var/log/httpd/access_log
```

![](img/3cd86472d388e54c317bc692ae7430fb_172.png)

## 规则的保存与清除

![](img/3cd86472d388e54c317bc692ae7430fb_174.png)

![](img/3cd86472d388e54c317bc692ae7430fb_176.png)

iptables的规则默认是临时的，重启后会丢失。如果需要永久保存，请执行以下命令：

![](img/3cd86472d388e54c317bc692ae7430fb_178.png)

```bash
service iptables save
```
该命令会将当前规则保存到`/etc/sysconfig/iptables`文件中。系统重启时会自动加载此文件中的规则。

![](img/3cd86472d388e54c317bc692ae7430fb_180.png)

若要清空所有规则并恢复默认状态，可以使用以下命令，并删除保存的规则文件：

![](img/3cd86472d388e54c317bc692ae7430fb_182.png)

```bash
iptables -F # 清空filter表规则
iptables -t nat -F # 清空nat表规则
# ... 清空其他表
rm -f /etc/sysconfig/iptables # 删除保存的规则文件
```

## 课程总结

![](img/3cd86472d388e54c317bc692ae7430fb_184.png)

![](img/3cd86472d388e54c317bc692ae7430fb_185.png)

![](img/3cd86472d388e54c317bc692ae7430fb_187.png)

本节课中我们一起学习了iptables防火墙的实战配置。我们从搭建一个模拟的企业网络环境开始，逐步配置了防火墙的转发功能、基于IP和MAC地址的访问控制、多端口及IP范围匹配等高级规则，最后实现了SNAT共享上网功能。通过本课的学习，你应该能够理解网络型防火墙的基本工作流程，并掌握使用iptables进行常见网络访问控制和地址转换的配置方法。记住，复杂的命令无需死记硬背，理解其原理和结构，在实际需要时参考笔记或手册即可。