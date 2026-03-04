# Linux系统安全防护：19：iptables防火墙配置实战 🛡️

![](img/e41bb8c3673360fbba6507455e418aac_1.png)

![](img/e41bb8c3673360fbba6507455e418aac_3.png)

![](img/e41bb8c3673360fbba6507455e418aac_4.png)

![](img/e41bb8c3673360fbba6507455e418aac_6.png)

![](img/e41bb8c3673360fbba6507455e418aac_8.png)

![](img/e41bb8c3673360fbba6507455e418aac_10.png)

![](img/e41bb8c3673360fbba6507455e418aac_12.png)

![](img/e41bb8c3673360fbba6507455e418aac_14.png)

在本节课中，我们将学习iptables防火墙的核心配置，包括网络地址规划、数据包转发控制、基于MAC地址和IP范围的访问限制，以及实现共享上网（SNAT）功能。通过模拟企业网络环境，我们将一步步配置防火墙规则，以保护内部服务器并控制网络访问。

## 环境准备与IP规划

![](img/e41bb8c3673360fbba6507455e418aac_16.png)

![](img/e41bb8c3673360fbba6507455e418aac_18.png)

![](img/e41bb8c3673360fbba6507455e418aac_20.png)

上一节我们介绍了防火墙的基本概念，本节中我们来看看如何为实验搭建网络环境。我们需要准备三台虚拟机：一台作为防火墙主机，一台作为内部Web服务器，一台作为外部客户端。

![](img/e41bb8c3673360fbba6507455e418aac_22.png)

![](img/e41bb8c3673360fbba6507455e418aac_24.png)

![](img/e41bb8c3673360fbba6507455e418aac_26.png)

![](img/e41bb8c3673360fbba6507455e418aac_28.png)

以下是实验环境的IP地址规划：
*   **防火墙主机 (iptables)**:
    *   内网网卡 (ens33): `192.168.0.80`
    *   外网网卡 (ens34): `192.168.1.100`
*   **内部Web服务器 (web26)**:
    *   网卡 (ens33): `192.168.0.26`
    *   网关: `192.168.0.80` (指向防火墙内网IP)
*   **外部客户端 (client)**:
    *   网卡 (ens33): `192.168.1.24`
    *   网关: `192.168.1.100` (指向防火墙外网IP)

![](img/e41bb8c3673360fbba6507455e418aac_30.png)

这个规划模拟了典型的企业网络拓扑：外部客户端通过互联网（`1.0`网段）访问防火墙，防火墙将请求转发到内部网络（`0.0`网段）的服务器。

![](img/e41bb8c3673360fbba6507455e418aac_32.png)

![](img/e41bb8c3673360fbba6507455e418aac_34.png)

## 配置网络接口

![](img/e41bb8c3673360fbba6507455e418aac_36.png)

![](img/e41bb8c3673360fbba6507455e418aac_38.png)

![](img/e41bb8c3673360fbba6507455e418aac_40.png)

![](img/e41bb8c3673360fbba6507455e418aac_42.png)

首先，我们需要为防火墙主机新添加的外网网卡配置一个永久IP地址。由于新添加的网卡没有配置文件，使用`nmcli`命令可以交互式地配置并自动生成配置文件。

![](img/e41bb8c3673360fbba6507455e418aac_44.png)

![](img/e41bb8c3673360fbba6507455e418aac_46.png)

![](img/e41bb8c3673360fbba6507455e418aac_48.png)

![](img/e41bb8c3673360fbba6507455e418aac_50.png)

```bash
nmcli
```
在交互界面中，依次选择“编辑连接” -> “有线连接 1” -> 将连接名称改为`ens34` -> IPv4配置选择“手动” -> 添加地址`192.168.1.100`（无需网关）-> 保存退出。

![](img/e41bb8c3673360fbba6507455e418aac_52.png)

![](img/e41bb8c3673360fbba6507455e418aac_54.png)

![](img/e41bb8c3673360fbba6507455e418aac_56.png)

![](img/e41bb8c3673360fbba6507455e418aac_58.png)

![](img/e41bb8c3673360fbba6507455e418aac_60.png)

配置完成后，重启网络并检查IP：
```bash
systemctl restart network
ip addr show ens34
```
此时，配置文件`/etc/sysconfig/network-scripts/ifcfg-ens34`已自动生成，IP地址永久生效。

![](img/e41bb8c3673360fbba6507455e418aac_62.png)

![](img/e41bb8c3673360fbba6507455e418aac_64.png)

![](img/e41bb8c3673360fbba6507455e418aac_66.png)

![](img/e41bb8c3673360fbba6507455e418aac_68.png)

![](img/e41bb8c3673360fbba6507455e418aac_70.png)

接着，配置内部服务器`web26`的网关指向防火墙：
```bash
# 编辑网络配置文件
vi /etc/sysconfig/network-scripts/ifcfg-ens33
# 将GATEWAY修改为 192.168.0.80
systemctl restart network
```

![](img/e41bb8c3673360fbba6507455e418aac_72.png)

![](img/e41bb8c3673360fbba6507455e418aac_73.png)

![](img/e41bb8c3673360fbba6507455e418aac_75.png)

![](img/e41bb8c3673360fbba6507455e418aac_76.png)

![](img/e41bb8c3673360fbba6507455e418aac_78.png)

![](img/e41bb8c3673360fbba6507455e418aac_80.png)

最后，配置客户端`client`的IP和网关：
```bash
# 编辑网络配置文件
vi /etc/sysconfig/network-scripts/ifcfg-ens33
# 修改IPADDR为 192.168.1.24，GATEWAY为 192.168.1.100
systemctl restart network
```

![](img/e41bb8c3673360fbba6507455e418aac_82.png)

![](img/e41bb8c3673360fbba6507455e418aac_84.png)

![](img/e41bb8c3673360fbba6507455e418aac_86.png)

![](img/e41bb8c3673360fbba6507455e418aac_88.png)

![](img/e41bb8c3673360fbba6507455e418aac_89.png)

## 部署Web服务与基础连通性测试

![](img/e41bb8c3673360fbba6507455e418aac_91.png)

![](img/e41bb8c3673360fbba6507455e418aac_93.png)

![](img/e41bb8c3673360fbba6507455e418aac_95.png)

![](img/e41bb8c3673360fbba6507455e418aac_97.png)

![](img/e41bb8c3673360fbba6507455e418aac_99.png)

在内部服务器`web26`上安装并启动Web服务，创建一个测试页面。
```bash
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "web26" > /var/www/html/index.html
```

![](img/e41bb8c3673360fbba6507455e418aac_101.png)

在防火墙上，需要开启内核的IP转发功能，这是实现网络地址转换（NAT）和数据包转发的关键。
```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
# 永久生效需编辑 /etc/sysctl.conf，设置 net.ipv4.ip_forward = 1
```

![](img/e41bb8c3673360fbba6507455e418aac_103.png)

![](img/e41bb8c3673360fbba6507455e418aac_105.png)

![](img/e41bb8c3673360fbba6507455e418aac_107.png)

配置完成后，在客户端`client`上尝试访问内部服务器。此时，由于防火墙尚未设置任何拦截规则，且IP转发已开启，访问应该成功。
```bash
curl 192.168.0.26
```
这证明了网络通路正常，数据包可以通过防火墙进行转发。

![](img/e41bb8c3673360fbba6507455e418aac_109.png)

## 配置iptables转发规则

现在，我们开始在防火墙上配置iptables规则，以实现访问控制。我们的第一个目标是：拒绝特定客户端（`192.168.1.24`）访问内部的Web服务。

![](img/e41bb8c3673360fbba6507455e418aac_111.png)

我们需要在`FORWARD`链（负责处理转发的数据包）上添加规则。
```bash
iptables -A FORWARD -s 192.168.1.24 -p tcp --dport 80 -j REJECT
```
*   `-A FORWARD`: 在FORWARD链末尾追加规则。
*   `-s 192.168.1.24`: 匹配源IP地址。
*   `-p tcp --dport 80`: 匹配TCP协议且目标端口为80（HTTP）。
*   `-j REJECT`: 执行拒绝操作。

![](img/e41bb8c3673360fbba6507455e418aac_113.png)

![](img/e41bb8c3673360fbba6507455e418aac_115.png)

添加此规则后，客户端再次访问Web服务器将被拒绝。这演示了网络型防火墙如何保护后端服务器。

![](img/e41bb8c3673360fbba6507455e418aac_117.png)

![](img/e41bb8c3673360fbba6507455e418aac_119.png)

![](img/e41bb8c3673360fbba6507455e418aac_121.png)

## 使用扩展模块进行高级控制

![](img/e41bb8c3673360fbba6507455e418aac_123.png)

![](img/e41bb8c3673360fbba6507455e418aac_125.png)

![](img/e41bb8c3673360fbba6507455e418aac_127.png)

基本的IP过滤可能被绕过，例如客户端更换IP地址。下面介绍几种使用iptables扩展模块进行更精确控制的方法。

**1. 基于MAC地址过滤**
MAC地址绑定在网卡上，不易更改。首先，在防火墙上安装`nmap`来扫描客户端的MAC地址。
```bash
yum install -y nmap
nmap -sn 192.168.1.24
```
获取到MAC地址（例如`00:0C:29:XX:XX:XX`）后，使用`mac`模块创建规则：
```bash
iptables -A FORWARD -p tcp --dport 80 -m mac --mac-source 00:0C:29:XX:XX:XX -j REJECT
```
这样，无论该客户端如何更换IP，只要网卡不变，就无法访问Web服务。

![](img/e41bb8c3673360fbba6507455e418aac_129.png)

![](img/e41bb8c3673360fbba6507455e418aac_131.png)

![](img/e41bb8c3673360fbba6507455e418aac_133.png)

**2. 基于多端口过滤**
使用`multiport`模块可以一条规则控制多个端口。
```bash
iptables -A FORWARD -p tcp -m multiport --dports 21,80,443,3306 -j DROP
```
这条规则会丢弃访问FTP(21)、HTTP(80)、HTTPS(443)、MySQL(3306)服务的所有数据包。

![](img/e41bb8c3673360fbba6507455e418aac_135.png)

![](img/e41bb8c3673360fbba6507455e418aac_137.png)

**3. 基于IP地址范围过滤**
使用`iprange`模块可以针对一个连续的IP地址段设置规则。
```bash
iptables -A FORWARD -p tcp --dport 80 -m iprange --src-range 192.168.1.20-192.168.1.30 -j REJECT
```
这条规则会拒绝IP段`192.168.1.20`到`192.168.1.30`内所有主机访问Web服务。

![](img/e41bb8c3673360fbba6507455e418aac_139.png)

## 配置SNAT实现共享上网

![](img/e41bb8c3673360fbba6507455e418aac_141.png)

![](img/e41bb8c3673360fbba6507455e418aac_143.png)

SNAT（源地址转换）可以让内部使用私有IP地址的主机通过防火墙的公网IP访问互联网。我们调整一下实验角色：
*   防火墙内网IP: `192.168.1.100`
*   内部主机(client): `192.168.1.25` (网关指向`192.168.1.100`)
*   外部服务器(web26): `192.168.0.26`

目标：让内部主机`192.168.1.25`能够访问外部服务器`192.168.0.26`，并且在外部服务器上看到的访问源IP是防火墙的公网IP`192.168.0.80`。

![](img/e41bb8c3673360fbba6507455e418aac_145.png)

![](img/e41bb8c3673360fbba6507455e418aac_147.png)

![](img/e41bb8c3673360fbba6507455e418aac_149.png)

![](img/e41bb8c3673360fbba6507455e418aac_151.png)

![](img/e41bb8c3673360fbba6507455e418aac_152.png)

在防火墙上配置SNAT规则，需要操作`nat`表的`POSTROUTING`链。
```bash
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -p tcp -j SNAT --to-source 192.168.0.80
```
*   `-t nat`: 指定操作`nat`表。
*   `-A POSTROUTING`: 在POSTROUTING链（路由后，数据包发出前）添加规则。
*   `-s 192.168.1.0/24`: 对来自`192.168.1.0/24`网段的数据包进行转换。
*   `-j SNAT --to-source 192.168.0.80`: 执行SNAT，将源IP转换为`192.168.0.80`。

![](img/e41bb8c3673360fbba6507455e418aac_154.png)

![](img/e41bb8c3673360fbba6507455e418aac_156.png)

![](img/e41bb8c3673360fbba6507455e418aac_158.png)

配置后，在内部主机上访问外部Web服务器，然后在外部服务器上查看访问日志：
```bash
# 在web26上执行
tail -f /var/log/httpd/access_log
```
日志中显示的访问者IP将是`192.168.0.80`（防火墙IP），而非内部主机的真实IP`192.168.1.25`。

![](img/e41bb8c3673360fbba6507455e418aac_160.png)

![](img/e41bb8c3673360fbba6507455e418aac_162.png)

## 规则的保存与清除

![](img/e41bb8c3673360fbba6507455e418aac_164.png)

![](img/e41bb8c3673360fbba6507455e418aac_166.png)

iptables规则默认是临时的，重启后会失效。需要手动保存。
```bash
service iptables save
```
规则会被保存到`/etc/sysconfig/iptables`文件中。若要清除所有规则并恢复默认状态，可以清空规则链并删除这个保存文件。
```bash
iptables -F
iptables -t nat -F
rm -f /etc/sysconfig/iptables
```

![](img/e41bb8c3673360fbba6507455e418aac_168.png)

## 总结

![](img/e41bb8c3673360fbba6507455e418aac_170.png)

本节课中我们一起学习了iptables防火墙的实战配置。我们从网络环境规划开始，逐步配置了IP地址、网关，并部署了基础服务。核心部分包括：
1.  在`FORWARD`链上配置规则，控制经过防火墙的数据包转发，实现对内部服务的访问控制。
2.  利用`mac`、`multiport`、`iprange`等扩展模块，实现基于MAC地址、多端口和IP范围的高级过滤策略。
3.  在`nat`表的`POSTROUTING`链上配置`SNAT`规则，实现企业内部私有IP地址通过单一公网IP共享上网的功能。
4.  掌握了使用`service iptables save`命令永久保存规则的方法。

![](img/e41bb8c3673360fbba6507455e418aac_172.png)

![](img/e41bb8c3673360fbba6507455e418aac_173.png)

![](img/e41bb8c3673360fbba6507455e418aac_175.png)

这些技能是构建Linux系统网络安全防线的基础，理解数据包的流向和iptables各链、表的作用是进行有效配置的关键。课后请务必根据提供的网络拓扑图搭建实验环境，反复练习这些命令以加深理解。