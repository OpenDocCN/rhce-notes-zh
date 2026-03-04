# Linux运维进阶：P53：系统安全防护之iptables防火墙

![](img/a0f0f3517a63bb4bb4782441152864ce_1.png)

在本节课中，我们将要学习Linux系统中一个重要的安全工具——iptables防火墙。我们将从基础概念开始，逐步学习如何查看、添加、删除防火墙规则，并最终实现主机型和网络型防火墙的配置。课程内容力求简单直白，让初学者能够轻松跟上。

![](img/a0f0f3517a63bb4bb4782441152864ce_3.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_4.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_6.png)

## 命令格式与安装

![](img/a0f0f3517a63bb4bb4782441152864ce_8.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_10.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_12.png)

上一节我们介绍了防火墙的基本概念，本节中我们来看看iptables的具体命令格式以及如何安装它。

![](img/a0f0f3517a63bb4bb4782441152864ce_14.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_16.png)

命令格式如下：

```
iptables [选项] [链名] [条件匹配] [目标操作]
```

![](img/a0f0f3517a63bb4bb4782441152864ce_18.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_20.png)

在开始配置之前，我们需要先安装iptables服务。请注意，为了避免冲突，在安装iptables之前，需要先关闭并禁用系统自带的firewalld防火墙。

![](img/a0f0f3517a63bb4bb4782441152864ce_22.png)

以下是安装步骤：
1.  停止firewalld服务：`systemctl stop firewalld`
2.  禁止firewalld开机自启：`systemctl disable firewalld`
3.  安装iptables服务：`yum install -y iptables-services`
4.  启动iptables服务：`systemctl start iptables`

![](img/a0f0f3517a63bb4bb4782441152864ce_24.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_26.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_28.png)

## 查看防火墙规则

![](img/a0f0f3517a63bb4bb4782441152864ce_30.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_32.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_34.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_36.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_38.png)

安装好服务后，我们首先需要学习如何查看现有的防火墙规则。这对于了解当前系统的安全策略至关重要。

查看规则主要使用 `-L`（列出规则）和 `-n`（以数字形式显示IP和端口）选项。它们通常一起使用，以获得更清晰的信息。

![](img/a0f0f3517a63bb4bb4782441152864ce_40.png)

以下是查看规则的相关命令示例：
*   `iptables -L`：查看默认filter表的规则（IP和端口会显示为服务名）。
*   `iptables -L -n`：以数字形式查看filter表的规则（更直观）。
*   `iptables -L -n --line-numbers`：查看规则并显示行号（便于后续管理）。
*   `iptables -t nat -L -n`：查看指定表（如nat表）的规则。`-t` 选项用于指定表名。

如果不使用 `-t` 指定表名，默认操作的都是 **filter**（数据过滤）表。

![](img/a0f0f3517a63bb4bb4782441152864ce_42.png)

## 配置防火墙规则（主机型）

了解了如何查看规则后，本节我们来看看如何配置规则。首先，我们以实现一个主机型防火墙为目标，即保护本机上的服务。

![](img/a0f0f3517a63bb4bb4782441152864ce_44.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_46.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_48.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_50.png)

主机型防火墙主要操作 **INPUT** 链，它决定了外部数据包能否进入本机。

### 防火墙的核心特性

在添加规则前，必须理解防火墙的一个核心特性：**从上到下匹配即停止**。这意味着防火墙会按顺序逐条检查规则，一旦数据包匹配了某条规则，就会执行该规则定义的操作（如允许或拒绝），并且不再检查后续规则。因此，规则的顺序至关重要。

### 添加规则

![](img/a0f0f3517a63bb4bb4782441152864ce_52.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_54.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_56.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_58.png)

添加规则使用 `-A`（追加到链末尾）或 `-I`（插入到链开头）选项。

![](img/a0f0f3517a63bb4bb4782441152864ce_60.png)

例如，我们想拒绝所有ICMP协议（即ping）的访问：
```
iptables -I INPUT -p icmp -j REJECT
```
*   `-I INPUT`：在INPUT链的开头插入一条规则。
*   `-p icmp`：匹配条件为ICMP协议。
*   `-j REJECT`：目标操作为“拒绝”，并会向发送方返回拒绝信息。

执行后，其他主机将无法ping通本机。

![](img/a0f0f3517a63bb4bb4782441152864ce_62.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_64.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_66.png)

### 规则解读

![](img/a0f0f3517a63bb4bb4782441152864ce_68.png)

使用 `iptables -L -n --line-numbers` 查看规则，输出类似：
```
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination
1    REJECT     icmp --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-port-unreachable
```
解读：所有源IP（0.0.0.0/0）通过ICMP协议访问本机任何目标IP时，都将被拒绝（REJECT）。

![](img/a0f0f3517a63bb4bb4782441152864ce_70.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_72.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_74.png)

### 目标操作

![](img/a0f0f3517a63bb4bb4782441152864ce_76.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_78.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_80.png)

常用的目标操作有以下几种：
*   **ACCEPT**：允许数据包通过。
*   **DROP**：丢弃数据包，不给予任何回应。
*   **REJECT**：拒绝数据包，并返回拒绝信息。

![](img/a0f0f3517a63bb4bb4782441152864ce_82.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_84.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_85.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_87.png)

### 基于端口和IP的规则

![](img/a0f0f3517a63bb4bb4782441152864ce_89.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_91.png)

更常见的需求是针对特定服务（端口）或IP地址设置规则。

![](img/a0f0f3517a63bb4bb4782441152864ce_93.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_95.png)

**1. 拒绝所有主机访问本机的Web服务（80端口）：**
```
iptables -I INPUT -p tcp --dport 80 -j REJECT
```
*   `-p tcp`：指定TCP协议（Web服务基于TCP）。
*   `--dport 80`：指定目标端口为80。

![](img/a0f0f3517a63bb4bb4782441152864ce_97.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_99.png)

**2. 仅拒绝特定IP（如192.168.0.24）访问本机的Web服务：**
```
iptables -I INPUT -s 192.168.0.24 -p tcp --dport 80 -j REJECT
```
*   `-s 192.168.0.24`：匹配源IP地址为192.168.0.24。

![](img/a0f0f3517a63bb4bb4782441152864ce_101.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_103.png)

**3. 拒绝整个网段（如192.168.1.0/24）的访问：**
```
iptables -I INPUT -s 192.168.1.0/24 -p tcp --dport 80 -j REJECT
```

![](img/a0f0f3517a63bb4bb4782441152864ce_105.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_107.png)

## 删除与清空规则

![](img/a0f0f3517a63bb4bb4782441152864ce_109.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_111.png)

配置过程中可能需要修改或清除规则。以下是管理规则的方法。

![](img/a0f0f3517a63bb4bb4782441152864ce_113.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_115.png)

删除规则使用 `-D`（删除指定规则）或 `-F`（清空所有规则）选项。

![](img/a0f0f3517a63bb4bb4782441152864ce_117.png)

以下是相关操作：
*   `iptables -D INPUT 3`：删除INPUT链中序号为3的规则。
*   `iptables -F`：清空默认filter表的所有规则。
*   `iptables -t nat -F`：清空指定表（如nat表）的所有规则。

![](img/a0f0f3517a63bb4bb4782441152864ce_119.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_121.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_123.png)

## 默认规则

![](img/a0f0f3517a63bb4bb4782441152864ce_125.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_127.png)

当数据包不匹配任何一条规则时，将执行链的**默认规则（Policy）**。使用 `iptables -L -n` 可以看到每条链的默认策略，通常是ACCEPT。

![](img/a0f0f3517a63bb4bb4782441152864ce_129.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_131.png)

你可以修改默认规则，但需极其谨慎。例如，将INPUT链的默认规则改为DROP会拒绝所有未明确允许的入站连接，可能导致服务器无法远程管理。
```
iptables -P INPUT DROP
```
**重要提示**：在修改默认规则为DROP之前，务必先添加允许SSH（22端口）等必要服务的规则，以免将自己锁在服务器外。
```
iptables -I INPUT -p tcp --dport 22 -j ACCEPT
iptables -P INPUT DROP
```

![](img/a0f0f3517a63bb4bb4782441152864ce_133.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_135.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_137.png)

## 配置网络型防火墙

主机型防火墙用于保护自身，而网络型防火墙则用于保护网络中的其他服务器，它像路由器一样负责转发数据包。本节我们将搭建一个简单的网络环境来演示。

![](img/a0f0f3517a63bb4bb4782441152864ce_139.png)

网络型防火墙主要操作 **FORWARD** 链，它控制着经过本机转发的数据包。

![](img/a0f0f3517a63bb4bb4782441152864ce_141.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_143.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_145.png)

### 实验环境准备

![](img/a0f0f3517a63bb4bb4782441152864ce_147.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_149.png)

我们需要三台虚拟机：
1.  **Client (192.168.1.24)**：代表外部的客户端。
2.  **iptables-FW (192.168.1.80, 192.168.0.80)**：防火墙设备，配备两块网卡，分别连接外网和内网。
3.  **Web Server (192.168.0.26)**：内部提供Web服务的服务器。

![](img/a0f0f3517a63bb4bb4782441152864ce_151.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_153.png)

### 关键配置步骤

![](img/a0f0f3517a63bb4bb4782441152864ce_155.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_157.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_158.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_159.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_161.png)

**1. 在防火墙设备上开启Linux内核的路由转发功能：**
这是实现数据包转发的核心。
```
echo “net.ipv4.ip_forward=1” >> /etc/sysctl.conf
sysctl -p
```
执行后检查 `/proc/sys/net/ipv4/ip_forward` 文件内容是否为1。

![](img/a0f0f3517a63bb4bb4782441152864ce_163.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_165.png)

**2. 在防火墙上配置FORWARD链规则：**
假设我们只允许外部客户端访问内部Web服务器的80端口。
```
# 清空现有FORWARD规则（谨慎操作）
iptables -F FORWARD

![](img/a0f0f3517a63bb4bb4782441152864ce_167.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_169.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_171.png)

# 允许从外网到内网Web服务器80端口的请求及返回的响应
iptables -I FORWARD -i ens34 -o ens32 -p tcp --dport 80 -j ACCEPT
iptables -I FORWARD -i ens32 -o ens34 -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT

![](img/a0f0f3517a63bb4bb4782441152864ce_173.png)

# 设置FORWARD链的默认策略为DROP，拒绝其他所有转发流量
iptables -P FORWARD DROP
```
*   `-i ens34`：指定数据包进入的网卡（外网卡）。
*   `-o ens32`：指定数据包出去的网卡（内网卡）。
*   `-m state --state ESTABLISHED`：匹配已建立的连接，用于允许返回的响应数据包。

![](img/a0f0f3517a63bb4bb4782441152864ce_175.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_177.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_179.png)

**3. 配置各服务器路由：**
确保Client的默认网关指向防火墙的外网IP（192.168.1.80），Web Server的默认网关指向防火墙的内网IP（192.168.0.80）。

![](img/a0f0f3517a63bb4bb4782441152864ce_181.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_183.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_185.png)

配置完成后，Client（192.168.1.24）应该能够访问Web Server（192.168.0.26）的80端口服务，但无法访问其他端口，从而实现了网络层的访问控制。

![](img/a0f0f3517a63bb4bb4782441152864ce_187.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_189.png)

## 总结

![](img/a0f0f3517a63bb4bb4782441152864ce_191.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_192.png)

![](img/a0f0f3517a63bb4bb4782441152864ce_193.png)

本节课中我们一起学习了iptables防火墙的配置与管理。我们从安装和查看规则开始，逐步深入到如何添加、删除基于协议、端口和IP地址的规则，并理解了“从上到下匹配即停止”这一核心特性。我们还探讨了默认规则的风险与配置方法。最后，通过搭建一个实验环境，我们实践了如何配置一个网络型防火墙，利用FORWARD链来保护内部网络中的服务器。掌握iptables是Linux系统安全管理的重要基础，希望本教程能帮助你建立起清晰的理解。