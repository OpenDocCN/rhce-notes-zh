# Linux运维：P53：系统安全防护之iptables防火墙 🔥

![](img/d5feda1551c3f961f522ff4bb712c9eb_1.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_3.png)

在本节课中，我们将要学习Linux系统中一个非常重要的安全工具——iptables防火墙。我们将从基础概念入手，学习如何查看、添加、删除防火墙规则，并分别实践主机型防火墙和网络型防火墙的配置。通过本节课的学习，你将能够使用iptables来保护你的服务器安全。

![](img/d5feda1551c3f961f522ff4bb712c9eb_4.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_6.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_8.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_10.png)

## 命令格式与基本操作

![](img/d5feda1551c3f961f522ff4bb712c9eb_12.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_14.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_15.png)

上一节我们介绍了防火墙的基本概念，本节中我们来看看iptables的具体命令格式和基本操作。

命令格式如下：
```
iptables [-t 表名] 选项 [链名] [条件] [-j 目标操作]
```

![](img/d5feda1551c3f961f522ff4bb712c9eb_17.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_19.png)

### 安装与启动服务

![](img/d5feda1551c3f961f522ff4bb712c9eb_21.png)

在开始配置之前，我们需要先安装iptables服务，并确保它与系统自带的firewalld服务不冲突。

![](img/d5feda1551c3f961f522ff4bb712c9eb_23.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_24.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_26.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_27.png)

以下是安装与启动步骤：
1.  停止并禁用firewalld服务。
    ```
    systemctl stop firewalld
    systemctl disable firewalld
    ```
2.  安装iptables服务。
    ```
    yum -y install iptables-services
    ```
3.  启动iptables服务并设置为开机自启。
    ```
    systemctl start iptables
    systemctl enable iptables
    ```

![](img/d5feda1551c3f961f522ff4bb712c9eb_29.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_31.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_33.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_35.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_37.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_39.png)

### 查看防火墙规则

了解如何查看现有规则是配置防火墙的第一步。我们使用 `-L` 选项来列出规则。

![](img/d5feda1551c3f961f522ff4bb712c9eb_41.png)

以下是查看规则的常用命令组合：
*   `iptables -L`：查看默认filter表的规则。
*   `iptables -L -n`：以数字形式显示IP和端口，避免反向解析主机名。
*   `iptables -L -n --line-numbers`：显示规则时，同时显示规则的行号，便于后续管理。
*   `iptables -t nat -L -n`：查看指定表（如nat表）的规则。`-t` 选项用于指定表名。

**注意**：选项 `-n` 必须放在 `-L` 之前，即 `iptables -nL` 是正确的，而 `iptables -Ln` 会报错。

![](img/d5feda1551c3f961f522ff4bb712c9eb_43.png)

## 配置主机型防火墙

![](img/d5feda1551c3f961f522ff4bb712c9eb_45.png)

上一节我们学会了如何查看规则，本节中我们来看看如何为保护本机服务配置规则，即主机型防火墙。这主要通过在 `INPUT` 链上添加规则来实现。

![](img/d5feda1551c3f961f522ff4bb712c9eb_47.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_49.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_51.png)

### 添加与删除规则

防火墙规则的核心是“条件匹配”和“目标操作”。规则按顺序从上到下匹配，一旦匹配成功就执行相应操作并停止后续匹配。

以下是添加与删除规则的选项：
*   **添加规则**：
    *   `-A`：在指定链的末尾追加一条规则。
    *   `-I`：在指定链的开头（或指定行号前）插入一条规则。规则的顺序至关重要。
*   **删除规则**：
    *   `-D`：删除链内指定序号的一条规则。
    *   `-F`：清空指定表的所有规则。

![](img/d5feda1551c3f961f522ff4bb712c9eb_53.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_55.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_57.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_59.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_61.png)

目标操作主要有以下几种：
*   `ACCEPT`：允许数据包通过。
*   `DROP`：丢弃数据包，不给予任何回应。
*   `REJECT`：拒绝数据包，并返回一个拒绝响应的数据包。

### 实践一：拒绝所有ICMP请求（禁止Ping）

![](img/d5feda1551c3f961f522ff4bb712c9eb_63.png)

ICMP协议常用于`ping`命令。如果我们不希望别人ping通我们的服务器，可以拒绝ICMP协议的数据包。

![](img/d5feda1551c3f961f522ff4bb712c9eb_65.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_67.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_69.png)

配置命令如下：
```
iptables -I INPUT -p icmp -j REJECT
```
命令解读：在`INPUT`链的首条插入一条规则，所有协议为`icmp`的数据包，都执行`REJECT`拒绝操作。

![](img/d5feda1551c3f961f522ff4bb712c9eb_71.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_73.png)

添加后，使用 `iptables -nL --line-numbers` 查看，第一条规则即为刚添加的ICMP拒绝规则。此时，从其他机器ping本机，会收到“目标不可达”的明确拒绝消息。

![](img/d5feda1551c3f961f522ff4bb712c9eb_75.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_77.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_79.png)

### 实践二：拒绝特定服务的访问

![](img/d5feda1551c3f961f522ff4bb712c9eb_81.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_83.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_85.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_87.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_89.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_91.png)

有时我们只想禁止对某个特定服务（如Web服务）的访问，而允许其他服务。这需要通过协议和端口来精确匹配条件。

![](img/d5feda1551c3f961f522ff4bb712c9eb_93.png)

配置命令如下（以拒绝HTTP的80端口为例）：
```
iptables -I INPUT -p tcp --dport 80 -j REJECT
```
命令解读：在`INPUT`链的首条插入规则，所有使用`tcp`协议且目标端口`--dport`为`80`的数据包，都执行`REJECT`拒绝操作。

![](img/d5feda1551c3f961f522ff4bb712c9eb_95.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_96.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_98.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_100.png)

添加此规则后，外部将无法访问本机的Web服务，但SSH、Ping等其他服务不受影响。

![](img/d5feda1551c3f961f522ff4bb712c9eb_102.png)

### 实践三：针对特定IP地址设置规则

![](img/d5feda1551c3f961f522ff4bb712c9eb_104.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_106.png)

我们还可以针对某个具体的IP地址或整个网段设置规则，实现更精细的控制。

![](img/d5feda1551c3f961f522ff4bb712c9eb_108.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_110.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_112.png)

以下是针对IP地址设置规则的示例：
1.  **拒绝单个IP访问Web服务**：
    ```
    iptables -I INPUT -s 192.168.0.100 -p tcp --dport 80 -j REJECT
    ```
    命令解读：拒绝源IP `-s` 为 `192.168.0.100` 的机器通过TCP协议访问本机的80端口。
2.  **拒绝整个网段访问**：
    ```
    iptables -I INPUT -s 192.168.1.0/24 -p tcp --dport 80 -j REJECT
    ```
    命令解读：拒绝整个 `192.168.1.0/24` 网段的所有IP访问本机的80端口。

![](img/d5feda1551c3f961f522ff4bb712c9eb_114.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_116.png)

### 默认规则与持久化

![](img/d5feda1551c3f961f522ff4bb712c9eb_118.png)

当数据包不匹配任何一条规则时，将执行链的默认策略（Policy）。默认策略可以通过 `-P` 选项修改。

![](img/d5feda1551c3f961f522ff4bb712c9eb_120.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_122.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_124.png)

查看并修改默认策略的命令如下：
```
iptables -L # 查看，Policy列显示默认策略
iptables -P INPUT DROP # 将INPUT链的默认策略改为DROP
```
**重要提示**：将默认策略改为`DROP`或`REJECT`前，务必先添加允许SSH（22端口）等管理服务的规则，否则可能导致无法远程连接服务器。`REJECT`不能作为默认策略。

![](img/d5feda1551c3f961f522ff4bb712c9eb_126.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_128.png)

使用 `service iptables save` 命令可以将当前内存中的规则保存到 `/etc/sysconfig/iptables` 文件，实现重启后规则不丢失。

![](img/d5feda1551c3f961f522ff4bb712c9eb_130.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_132.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_134.png)

## 配置网络型防火墙

![](img/d5feda1551c3f961f522ff4bb712c9eb_135.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_137.png)

上一节我们配置了保护本机的主机型防火墙，本节中我们来看看如何配置网络型防火墙，让它像路由器一样，保护内部网络中的其他服务器。

![](img/d5feda1551c3f961f522ff4bb712c9eb_139.png)

### 实验环境搭建

![](img/d5feda1551c3f961f522ff4bb712c9eb_141.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_143.png)

网络型防火墙需要至少两块网卡，分别连接外网和内网。我们需要准备三台虚拟机来模拟这个环境。

![](img/d5feda1551c3f961f522ff4bb712c9eb_145.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_146.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_148.png)

以下是实验的主机规划：
*   **客户端 (Client)**: IP: 192.168.1.100， 模拟外网用户。
*   **防火墙 (iptables)**: 
    *   外网卡 (ens34): IP: 192.168.1.1
    *   内网卡 (ens32): IP: 192.168.0.1
*   **内部Web服务器 (Web)**: IP: 192.168.0.100， 提供网站服务。

![](img/d5feda1551c3f961f522ff4bb712c9eb_150.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_152.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_153.png)

确保各主机IP配置正确，并且防火墙主机开启了Linux内核的IP转发功能，这是它能转发数据包的关键。

![](img/d5feda1551c3f961f522ff4bb712c9eb_155.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_157.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_158.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_159.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_161.png)

开启IP转发功能的命令如下：
```
echo “net.ipv4.ip_forward=1” >> /etc/sysctl.conf
sysctl -p
```
执行后，检查 `/proc/sys/net/ipv4/ip_forward` 文件内容是否为 `1`。

![](img/d5feda1551c3f961f522ff4bb712c9eb_163.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_165.png)

### 配置转发规则

![](img/d5feda1551c3f961f522ff4bb712c9eb_167.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_169.png)

网络型防火墙的核心是 `FORWARD` 链。我们需要在防火墙主机上配置规则，控制哪些数据包可以被转发到内部服务器。

![](img/d5feda1551c3f961f522ff4bb712c9eb_171.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_173.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_175.png)

一个基本的配置示例如下：
1.  允许内网访问外网（状态跟踪，更安全）：
    ```
    iptables -A FORWARD -i ens32 -o ens34 -m state --state ESTABLISHED,RELATED -j ACCEPT
    iptables -A FORWARD -i ens32 -o ens34 -j ACCEPT
    ```
2.  允许外网访问内网的Web服务：
    ```
    iptables -A FORWARD -i ens34 -o ens32 -d 192.168.0.100 -p tcp --dport 80 -j ACCEPT
    ```
3.  设置默认转发策略为拒绝，增强安全性：
    ```
    iptables -P FORWARD DROP
    ```

![](img/d5feda1551c3f961f522ff4bb712c9eb_177.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_179.png)

配置完成后，外网的客户端（192.168.1.100）访问防火墙的外网IP（192.168.1.1）时，请求会被转发到内网的Web服务器（192.168.0.100）。同样，别忘了保存规则。

![](img/d5feda1551c3f961f522ff4bb712c9eb_181.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_183.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_185.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_187.png)

## 总结

![](img/d5feda1551c3f961f522ff4bb712c9eb_189.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_190.png)

![](img/d5feda1551c3f961f522ff4bb712c9eb_191.png)

本节课中我们一起学习了iptables防火墙的全面配置。
我们首先学习了iptables命令的基本格式，以及如何查看、添加和删除规则。
接着，我们深入实践了主机型防火墙的配置，包括如何拒绝特定协议、端口和IP地址的访问，并了解了默认策略的作用。
最后，我们搭建了一个模拟环境，配置了网络型防火墙，实现了通过防火墙保护内部网络服务器的功能。
iptables功能强大，是Linux系统安全不可或缺的工具，熟练掌握其用法对运维工作至关重要。