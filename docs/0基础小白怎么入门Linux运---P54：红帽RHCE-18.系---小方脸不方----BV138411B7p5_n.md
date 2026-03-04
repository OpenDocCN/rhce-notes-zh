# Linux运维全套培训课程：P54：红帽RHCE-18.系统安全防护之iptables防火墙 🔥

在本节课中，我们将要学习iptables防火墙的基本概念、命令格式以及如何配置主机型和网络型防火墙规则。通过实际操作，你将掌握使用iptables保护Linux系统安全的核心技能。

![](img/21262be3635b08422aa2ea75692e30be_1.png)

![](img/21262be3635b08422aa2ea75692e30be_3.png)

![](img/21262be3635b08422aa2ea75692e30be_4.png)

![](img/21262be3635b08422aa2ea75692e30be_6.png)

## 命令格式与安装

![](img/21262be3635b08422aa2ea75692e30be_8.png)

![](img/21262be3635b08422aa2ea75692e30be_10.png)

上一节我们介绍了防火墙的基本概念，本节中我们来看看iptables的具体命令格式以及如何安装。

![](img/21262be3635b08422aa2ea75692e30be_12.png)

![](img/21262be3635b08422aa2ea75692e30be_14.png)

![](img/21262be3635b08422aa2ea75692e30be_16.png)

命令格式如下：

```
iptables [选项] [链名] [条件匹配] [目标操作]
```

![](img/21262be3635b08422aa2ea75692e30be_18.png)

在开始配置iptables之前，需要先安装其服务。安装前请注意，iptables与firewalld服务控制相同的内核模块，不能同时启用，因此需要先关闭firewalld。

![](img/21262be3635b08422aa2ea75692e30be_20.png)

以下是安装步骤：

![](img/21262be3635b08422aa2ea75692e30be_22.png)

1.  停止并禁用firewalld服务：
    ```bash
    systemctl stop firewalld
    systemctl disable firewalld
    ```
2.  安装iptables服务：
    ```bash
    yum install -y iptables-services
    ```
3.  启动iptables服务并设置为开机自启：
    ```bash
    systemctl start iptables
    systemctl enable iptables
    ```

![](img/21262be3635b08422aa2ea75692e30be_24.png)

![](img/21262be3635b08422aa2ea75692e30be_26.png)

![](img/21262be3635b08422aa2ea75692e30be_28.png)

## 查看防火墙规则

![](img/21262be3635b08422aa2ea75692e30be_30.png)

![](img/21262be3635b08422aa2ea75692e30be_32.png)

![](img/21262be3635b08422aa2ea75692e30be_34.png)

![](img/21262be3635b08422aa2ea75692e30be_36.png)

安装好服务后，我们首先学习如何查看现有的防火墙规则。

![](img/21262be3635b08422aa2ea75692e30be_38.png)

以下是查看规则时常用的选项组合：

*   `-L`：列出指定链或所有链的规则。
*   `-n`：以数字形式显示IP地址和端口号，避免反向域名解析。
*   `--line-numbers`：显示规则的行号，便于后续的删除或修改操作。

![](img/21262be3635b08422aa2ea75692e30be_40.png)

默认情况下，`iptables -L`命令查看的是**filter表**的规则。如果想查看其他表（如nat、mangle、raw）的规则，需要使用`-t`选项指定表名。

例如，查看nat表的规则：
```bash
iptables -t nat -L -n --line-numbers
```

![](img/21262be3635b08422aa2ea75692e30be_42.png)

## 配置防火墙规则（主机型）

了解如何查看规则后，本节我们来看看如何配置规则。首先从主机型防火墙开始，它主要用于保护本机服务，核心是操作**INPUT链**。

![](img/21262be3635b08422aa2ea75692e30be_44.png)

![](img/21262be3635b08422aa2ea75692e30be_46.png)

![](img/21262be3635b08422aa2ea75692e30be_48.png)

![](img/21262be3635b08422aa2ea75692e30be_50.png)

### 规则匹配机制

在添加规则前，必须理解iptables的核心特性：**从上到下匹配即停止**。这意味着数据包会按顺序与规则列表进行匹配，一旦匹配成功，就会执行该规则定义的操作（如允许或拒绝），并停止后续规则的匹配。因此，规则的**顺序至关重要**。

### 添加规则

添加规则主要使用两个选项：

![](img/21262be3635b08422aa2ea75692e30be_52.png)

![](img/21262be3635b08422aa2ea75692e30be_54.png)

![](img/21262be3635b08422aa2ea75692e30be_56.png)

*   `-A`：在指定链的**末尾追加**一条新规则。
*   `-I`：在指定链的**开头插入**一条新规则（也可指定插入到第几行）。

![](img/21262be3635b08422aa2ea75692e30be_58.png)

![](img/21262be3635b08422aa2ea75692e30be_60.png)

目标操作（`-j`参数）常用有以下几种：

*   `ACCEPT`：允许数据包通过。
*   `DROP`：丢弃数据包，不给予任何回应。
*   `REJECT`：拒绝数据包，并返回一个明确的拒绝响应（如`Destination Port Unreachable`）。

![](img/21262be3635b08422aa2ea75692e30be_62.png)

### 实践：拒绝ICMP（Ping）请求

![](img/21262be3635b08422aa2ea75692e30be_64.png)

![](img/21262be3635b08422aa2ea75692e30be_66.png)

假设我们想阻止其他主机ping通本机，即拒绝ICMP协议的数据包。

![](img/21262be3635b08422aa2ea75692e30be_68.png)

![](img/21262be3635b08422aa2ea75692e30be_70.png)

配置命令如下：
```bash
iptables -I INPUT -p icmp -j REJECT
```
*   `-I INPUT`：在INPUT链的开头插入规则。
*   `-p icmp`：匹配协议为ICMP的数据包。
*   `-j REJECT`：执行拒绝操作，并返回拒绝响应。

![](img/21262be3635b08422aa2ea75692e30be_72.png)

![](img/21262be3635b08422aa2ea75692e30be_74.png)

执行后，其他主机再次ping本机，将会收到“目标端口不可达”的响应，规则生效。

![](img/21262be3635b08422aa2ea75692e30be_76.png)

![](img/21262be3635b08422aa2ea75692e30be_78.png)

![](img/21262be3635b08422aa2ea75692e30be_80.png)

### 解读规则

![](img/21262be3635b08422aa2ea75692e30be_82.png)

![](img/21262be3635b08422aa2ea75692e30be_84.png)

![](img/21262be3635b08422aa2ea75692e30be_85.png)

![](img/21262be3635b08422aa2ea75692e30be_87.png)

使用`iptables -L -n --line-numbers`查看规则，输出类似：
```
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination
1    REJECT     icmp --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-port-unreachable
```
解读：对于来自任意IP（0.0.0.0/0），去往本机任意IP，且协议为ICMP的数据包，执行REJECT操作。

![](img/21262be3635b08422aa2ea75692e30be_89.png)

## 删除与清空规则

![](img/21262be3635b08422aa2ea75692e30be_91.png)

![](img/21262be3635b08422aa2ea75692e30be_93.png)

![](img/21262be3635b08422aa2ea75692e30be_95.png)

配置规则后，有时需要修改或清理。以下是管理规则的方法。

![](img/21262be3635b08422aa2ea75692e30be_97.png)

![](img/21262be3635b08422aa2ea75692e30be_99.png)

删除规则主要有两个选项：

![](img/21262be3635b08422aa2ea75692e30be_101.png)

*   `-D`：删除链内指定序号的一条规则。
*   `-F`：清空指定表的所有规则。

![](img/21262be3635b08422aa2ea75692e30be_103.png)

![](img/21262be3635b08422aa2ea75692e30be_105.png)

例如：
1.  删除INPUT链的第3条规则：
    ```bash
    iptables -D INPUT 3
    ```
2.  清空filter表的所有规则：
    ```bash
    iptables -F
    ```
3.  清空nat表的所有规则：
    ```bash
    iptables -t nat -F
    ```

![](img/21262be3635b08422aa2ea75692e30be_107.png)

## 默认规则策略

![](img/21262be3635b08422aa2ea75692e30be_109.png)

![](img/21262be3635b08422aa2ea75692e30be_111.png)

![](img/21262be3635b08422aa2ea75692e30be_113.png)

当数据包不匹配任何一条规则时，将执行链的**默认规则（Policy）**。使用`iptables -L`命令时，每条链后面显示的`(policy ACCEPT)`就是默认策略。

![](img/21262be3635b08422aa2ea75692e30be_115.png)

默认策略也可以修改，使用`-P`选项。**但修改默认策略为DROP或REJECT前务必谨慎**，可能导致远程连接中断。

![](img/21262be3635b08422aa2ea75692e30be_117.png)

例如，先将SSH端口（22）放行，再将INPUT链默认策略改为DROP：
```bash
# 放行SSH端口，确保远程连接不会中断
iptables -I INPUT -p tcp --dport 22 -j ACCEPT
# 修改INPUT链默认策略为DROP
iptables -P INPUT DROP
```
修改后，除了SSH服务，其他所有入站连接都将被丢弃。

![](img/21262be3635b08422aa2ea75692e30be_119.png)

![](img/21262be3635b08422aa2ea75692e30be_121.png)

![](img/21262be3635b08422aa2ea75692e30be_123.png)

## 进阶规则配置

![](img/21262be3635b08422aa2ea75692e30be_125.png)

在实际运维中，规则条件可以更精细。以下是常见场景。

![](img/21262be3635b08422aa2ea75692e30be_127.png)

![](img/21262be3635b08422aa2ea75692e30be_129.png)

### 拒绝访问特定服务（如Web）

![](img/21262be3635b08422aa2ea75692e30be_131.png)

![](img/21262be3635b08422aa2ea75692e30be_133.png)

![](img/21262be3635b08422aa2ea75692e30be_135.png)

若只想禁止访问本机的Web服务（80端口），但允许其他服务，可以配置基于端口的规则。
```bash
iptables -I INPUT -p tcp --dport 80 -j REJECT
```
*   `-p tcp`：匹配TCP协议。
*   `--dport 80`：匹配目标端口为80的数据包。

![](img/21262be3635b08422aa2ea75692e30be_137.png)

### 拒绝特定IP地址

如果只想拒绝某个特定IP（如192.168.0.24）的所有访问，可以配置基于源IP的规则。
```bash
iptables -I INPUT -s 192.168.0.24 -j REJECT
```
*   `-s 192.168.0.24`：匹配源IP地址为192.168.0.24的数据包。

![](img/21262be3635b08422aa2ea75692e30be_139.png)

![](img/21262be3635b08422aa2ea75692e30be_141.png)

### 拒绝整个网段

![](img/21262be3635b08422aa2ea75692e30be_143.png)

![](img/21262be3635b08422aa2ea75692e30be_145.png)

拒绝一个网段（如192.168.1.0/24）的访问，可以使用CIDR格式。
```bash
iptables -I INPUT -s 192.168.1.0/24 -j REJECT
```

![](img/21262be3635b08422aa2ea75692e30be_147.png)

![](img/21262be3635b08422aa2ea75692e30be_149.png)

## 配置网络型防火墙

![](img/21262be3635b08422aa2ea75692e30be_151.png)

![](img/21262be3635b08422aa2ea75692e30be_153.png)

网络型防火墙用于保护内部网络中的其他服务器，核心是操作**FORWARD链**。它像一台路由器，负责在不同网络间转发数据包。

![](img/21262be3635b08422aa2ea75692e30be_155.png)

![](img/21262be3635b08422aa2ea75692e30be_157.png)

![](img/21262be3635b08422aa2ea75692e30be_158.png)

![](img/21262be3635b08422aa2ea75692e30be_159.png)

### 实验环境准备

![](img/21262be3635b08422aa2ea75692e30be_161.png)

需要三台虚拟机模拟环境：
1.  **Client (192.168.1.24)**：外部客户端。
2.  **iptables FW (192.168.1.80, 192.168.0.80)**：防火墙设备，需双网卡。
    *   ens32: 192.168.0.80 (内网)
    *   ens34: 192.168.1.80 (外网)
3.  **Web Server (192.168.0.26)**：内部Web服务器。

![](img/21262be3635b08422aa2ea75692e30be_163.png)

![](img/21262be3635b08422aa2ea75692e30be_165.png)

![](img/21262be3635b08422aa2ea75692e30be_167.png)

在防火墙设备上，必须开启Linux内核的IP转发功能：
```bash
echo “net.ipv4.ip_forward=1” >> /etc/sysctl.conf
sysctl -p
```
执行后，检查`/proc/sys/net/ipv4/ip_forward`文件内容是否为1。

![](img/21262be3635b08422aa2ea75692e30be_169.png)

![](img/21262be3635b08422aa2ea75692e30be_171.png)

### 配置转发与过滤规则

![](img/21262be3635b08422aa2ea75692e30be_173.png)

![](img/21262be3635b08422aa2ea75692e30be_175.png)

目标是让外部Client能访问内部Web Server，但可以通过iptables控制访问。

![](img/21262be3635b08422aa2ea75692e30be_177.png)

![](img/21262be3635b08422aa2ea75692e30be_179.png)

![](img/21262be3635b08422aa2ea75692e30be_181.png)

1.  在iptables FW上，配置基本的转发规则并限制访问：
    ```bash
    # 允许转发已建立连接及相关连接的数据包（保证回包）
    iptables -A FORWARD -m state --state ESTABLISHED,RELATED -j ACCEPT
    # 允许从外网(ens34)到内网Web Server 80端口的转发
    iptables -A FORWARD -i ens34 -o ens32 -p tcp --dport 80 -d 192.168.0.26 -j ACCEPT
    # 设置FORWARD链默认策略为DROP（按需设置，谨慎操作）
    iptables -P FORWARD DROP
    ```
2.  在Client上，将网关设置为防火墙的外网IP（192.168.1.80），即可测试访问内部Web Server（192.168.0.26）。

![](img/21262be3635b08422aa2ea75692e30be_183.png)

![](img/21262be3635b08422aa2ea75692e30be_185.png)

![](img/21262be3635b08422aa2ea75692e30be_187.png)

通过配置FORWARD链的规则，可以实现复杂的网络间访问控制。

![](img/21262be3635b08422aa2ea75692e30be_189.png)

## 总结

![](img/21262be3635b08422aa2ea75692e30be_191.png)

![](img/21262be3635b08422aa2ea75692e30be_192.png)

![](img/21262be3635b08422aa2ea75692e30be_193.png)

本节课中我们一起学习了iptables防火墙的全面配置。
我们首先学习了iptables的命令格式、服务安装与规则查看方法。
接着，深入探讨了主机型防火墙的配置，包括规则的添加、删除、默认策略管理，以及如何基于协议、端口、IP地址和网段设置精细的过滤条件。
最后，我们搭建实验环境，实践了网络型防火墙的配置，理解了如何通过FORWARD链和路由转发功能来保护内部网络。
掌握iptables是Linux系统安全管理的重要基础，需要多加练习以熟悉各种场景下的规则配置。