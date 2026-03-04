# Linux-RHCSA入门实战课：P10：RHCSA-第9课-linux下网络管理 📡

![](img/b7b7fa61a552e408ded828cb03294673_1.png)

## 概述
在本节课中，我们将要学习Linux系统下的网络管理。网络管理是系统运维中的核心技能之一，它涉及到网卡配置、网络状态诊断、路由管理等多个方面。我们将从基础概念入手，逐步掌握在Linux环境中管理和排查网络问题的实用命令与方法。

![](img/b7b7fa61a552e408ded828cb03294673_3.png)

![](img/b7b7fa61a552e408ded828cb03294673_5.png)

## 网卡基础与硬件识别

![](img/b7b7fa61a552e408ded828cb03294673_7.png)

在开始配置之前，我们需要了解服务器上的网卡。一台服务器通常配备多个物理网卡，它们各有用途，例如有的用于连接公网，有的用于内部管理。

![](img/b7b7fa61a552e408ded828cb03294673_9.png)

![](img/b7b7fa61a552e408ded828cb03294673_11.png)

网卡名称通常以 `eth` 开头，后面跟着数字编号，例如 `eth0` 代表第一块网卡。了解网卡的传输速率也很重要：
*   **FE**：快速以太网，100兆。
*   **GE**：千兆以太网，1000兆。
*   **TE**：万兆以太网。

![](img/b7b7fa61a552e408ded828cb03294673_12.png)

![](img/b7b7fa61a552e408ded828cb03294673_14.png)

![](img/b7b7fa61a552e408ded828cb03294673_16.png)

![](img/b7b7fa61a552e408ded828cb03294673_18.png)

![](img/b7b7fa61a552e408ded828cb03294673_19.png)

### 查看网卡信息
以下是查看网卡信息的常用命令。

**使用 `ifconfig` 命令**
`ifconfig` 是最常用的查看网络接口信息的命令。
```bash
ifconfig
```
该命令会显示所有激活网卡的IP地址、子网掩码、MAC地址等基本信息。

**使用 `lspci` 命令**
`lspci` 命令可以列出所有PCI设备，通过过滤可以查看物理网卡信息。
```bash
lspci | grep -i eth
```
参数 `-i` 表示忽略大小写，因为Linux系统严格区分大小写。

**检测网卡物理连接状态**
要检测网卡是否物理连接正常，可以使用 `mii-tool` 命令。
```bash
mii-tool ens33
```
如果输出中包含 `link ok`，则表示网卡连接正常。这条命令在网络排查中非常重要。

## 网卡配置与管理

### 临时修改IP地址
有时我们需要临时更换IP地址，例如更换办公环境。可以使用 `ifconfig` 命令直接设置。
```bash
ifconfig ens33 192.168.1.10
```
如果不指定子网掩码，系统会根据IP地址的类别自动分配（例如，192.168.1.10属于C类地址，掩码默认为255.255.255.0）。这种修改是临时的，重启后会失效。

![](img/b7b7fa61a552e408ded828cb03294673_21.png)

![](img/b7b7fa61a552e408ded828cb03294673_23.png)

### 激活与禁用网卡
为了避免影响其他服务，我们通常只重启单个网卡，而不是整个网络服务。
*   **禁用网卡**：`ifdown ens33`
*   **启用网卡**：`ifup ens33`

### 配置虚拟子接口
服务器可以像路由器一样创建虚拟子接口，这在一些网络架构（如早期的单臂路由）中会用到。
```bash
ifconfig ens33:1 192.168.1.1 netmask 255.255.255.0
```
这条命令会在 `ens33` 网卡上创建一个名为 `ens33:1` 的子接口，并分配IP地址。

## 网络连通性测试

### Ping命令详解
`ping` 命令基于ICMP协议，用于测试网络主机的连通性。
*   **基本用法**：`ping www.baidu.com`
*   **指定次数**：`ping -c 4 www.baidu.com`
*   **指定间隔**：`ping -i 0.1 www.baidu.com` （设置发送间隔为0.1秒）
*   **指定包大小**：`ping -l 1000 www.baidu.com` （设置数据包大小为1000字节）

![](img/b7b7fa61a552e408ded828cb03294673_25.png)

![](img/b7b7fa61a552e408ded828cb03294673_27.png)

![](img/b7b7fa61a552e408ded828cb03294673_29.png)

![](img/b7b7fa61a552e408ded828cb03294673_31.png)

![](img/b7b7fa61a552e408ded828cb03294673_33.png)

**注意**：Linux下默认`ping`会一直发送，直到手动停止（Ctrl+C）。Windows下默认发送4个包。Linux默认包大小为64字节，Windows为32字节。

![](img/b7b7fa61a552e408ded828cb03294673_35.png)

![](img/b7b7fa61a552e408ded828cb03294673_37.png)

### Traceroute命令
`traceroute`（Windows中为`tracert`）命令用于追踪数据包到达目标主机所经过的路由路径，帮助定位网络故障点。
```bash
traceroute www.baidu.com
```

![](img/b7b7fa61a552e408ded828cb03294673_39.png)

### 禁止被Ping
出于安全考虑，生产环境通常会禁止服务器被外部Ping。可以通过修改内核参数实现。
```bash
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all
```
将值设为 `1` 表示忽略所有ICMP回显请求，即禁止被Ping。这只是临时生效，如需永久生效，需修改 `/etc/sysctl.conf` 文件。

![](img/b7b7fa61a552e408ded828cb03294673_41.png)

![](img/b7b7fa61a552e408ded828cb03294673_42.png)

![](img/b7b7fa61a552e408ded828cb03294673_44.png)

![](img/b7b7fa61a552e408ded828cb03294673_46.png)

![](img/b7b7fa61a552e408ded828cb03294673_48.png)

## 永久网络配置

### 网卡配置文件
永久修改网络配置需要通过编辑网卡配置文件实现，通常位于 `/etc/sysconfig/network-scripts/` 目录下，文件名类似 `ifcfg-ens33`。

![](img/b7b7fa61a552e408ded828cb03294673_50.png)

![](img/b7b7fa61a552e408ded828cb03294673_52.png)

一个典型的静态IP配置示例如下：
```bash
TYPE=Ethernet
BOOTPROTO=static
NAME=ens33
DEVICE=ens33
ONBOOT=yes
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
```
*   `BOOTPROTO=static`：表示使用静态IP，如果是`dhcp`则表示动态获取。
*   `ONBOOT=yes`：表示系统启动时激活该网卡。
*   修改后需要重启网络服务或重启网卡才能生效：`systemctl restart network` 或 `ifdown ens33 && ifup ens33`。

![](img/b7b7fa61a552e408ded828cb03294673_54.png)

![](img/b7b7fa61a552e408ded828cb03294673_56.png)

### 主机名管理
*   **查看主机名**：`hostname`
*   **临时修改主机名**：`hostname newname`，重新登录后生效，重启失效。
*   **永久修改主机名**：编辑 `/etc/hostname` 文件，写入新主机名并重启系统。

### DNS配置优先级
Linux系统中DNS解析的优先级顺序为：
1.  **本地hosts文件** (`/etc/hosts`)：手动配置，优先级最高。
2.  **网卡配置文件中的DNS设置** (`/etc/sysconfig/network-scripts/ifcfg-*`)。
3.  **系统全局DNS配置文件** (`/etc/resolv.conf`)：通常由网络服务管理，手动修改可能被覆盖。

## 路由管理

![](img/b7b7fa61a552e408ded828cb03294673_58.png)

![](img/b7b7fa61a552e408ded828cb03294673_60.png)

服务器本身具有路由功能，可以配置静态路由。

*   **查看路由表**：`route -n` （`-n` 参数表示以数字形式显示，不解析主机名）
*   **添加默认网关**：`route add default gw 192.168.1.1`
*   **添加静态路由**：`route add -net 10.0.0.0/24 gw 192.168.1.254 dev ens33`
*   **删除路由**：`route del -net 10.0.0.0/24`

## 网络故障排查思路

当网络出现问题时，遵循清晰的排查思路至关重要：
1.  **检查物理连接**：使用 `mii-tool` 确认网卡链路是否正常。
2.  **检查本机IP配置**：使用 `ifconfig` 确认IP地址、子网掩码配置正确。
3.  **测试网关连通性**：`ping` 网关地址，确认内网出口是否通畅。
4.  **测试DNS解析**：`ping` 一个域名，看是否能解析出IP地址。如果不行，检查DNS配置。
5.  **测试远端服务**：使用 `telnet IP 端口` 测试具体服务的端口是否开放，例如 `telnet www.baidu.com 80`。
6.  **分析Ping结果**：
    *   `Request timeout`：请求超时，可能是防火墙拦截或目标主机未响应。
    *   `Destination Host Unreachable`：目标主机不可达，通常是本地路由问题或ARP失败。
    *   延迟(`ms`值)高且波动大：网络拥塞或不稳定，可能导致应用卡顿。

## 网络模型补充（OSI/TCP-IP）

理解网络模型有助于从原理层面分析问题。
*   **OSI七层模型**：理论模型，分层清晰，但上层（应用层、表示层、会话层）在实际中难以严格分离。
*   **TCP/IP五层模型**：更实用的模型。
    *   **应用层**：我们管理的HTTP、FTP、MySQL等服务就在这一层。
    *   **传输层**：TCP（可靠，如网页、文件传输）和UDP（不可靠，如视频流、DNS查询）。
    *   **网络层**：IP协议、路由。
    *   **数据链路层**：MAC地址、交换机、ARP协议（工作在2.5层，负责IP到MAC的转换）。
    *   **物理层**：网线、光纤、调制解调器（猫）等硬件设备。

![](img/b7b7fa61a552e408ded828cb03294673_62.png)

## 总结
本节课我们一起学习了Linux下网络管理的核心知识。我们从识别和查看网卡信息开始，掌握了使用`ifconfig`、`mii-tool`等命令。接着，我们学习了如何临时和永久地配置IP地址、网关和DNS。在路由管理部分，我们了解了如何使用`route`命令配置静态路由。最后，我们梳理了一套清晰的网络故障排查流程，并回顾了OSI/TCP-IP网络模型来巩固理论基础。记住，扎实的网络管理能力是成为一名优秀系统运维工程师的基石。