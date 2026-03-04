# Linux-RHCE运维实战课程：P12：RHCE-第12课-防火墙管理工具：firewalld 🔥

![](img/d9cd05075626502eb92689c75683872c_1.png)

![](img/d9cd05075626502eb92689c75683872c_3.png)

![](img/d9cd05075626502eb92689c75683872c_4.png)

![](img/d9cd05075626502eb92689c75683872c_6.png)

在本节课中，我们将深入学习Linux系统中的防火墙管理工具，重点对比传统的`iptables`与动态防火墙`firewalld`。我们将通过实战配置，掌握如何保护Web服务器、实现网络地址转换以及使用`firewalld`的区域和服务进行灵活的防火墙管理。

![](img/d9cd05075626502eb92689c75683872c_8.png)

---

## 课程概述 📋

防火墙是保障系统安全的重要组件。本节课程将分为几个部分：首先回顾并补充`iptables`的实战应用，包括Web服务器防护和NAT配置；然后，我们将系统性地介绍`firewalld`动态防火墙的核心概念、区域、服务以及配置方法。课程旨在让初学者能够理解并上手配置Linux防火墙。

---

![](img/d9cd05075626502eb92689c75683872c_10.png)

![](img/d9cd05075626502eb92689c75683872c_12.png)

## 第一部分：课前问题回顾与补充 🔧

上一部分我们介绍了自动化安装中的一些问题，本节我们来看看具体的解决方案和补充说明。

以下是关于自动化安装文件（kickstart）和磁盘空间问题的要点：

1.  **自动化安装文件配置**：
    *   在自动化安装过程中，若卡在软件包选择步骤，可能是因为提前修改了挂载源。正确的做法是在最后阶段修改，或者直接编辑`.repo`文件，将其中的挂载路径改为正确的FTP或NFS地址。
    *   另一种方法是直接使用系统生成的应答文件。在根目录下通常有`anaconda-ks.cfg`文件，可以将其复制到Web或FTP目录下，重命名为`ks.cfg`作为应答文件。
    *   修改此文件时，关键步骤包括：
        *   指定安装源：`url --url=http://IP_ADDRESS`
        *   注释掉`graphical`选项以实现最小化安装。
        *   修改时区，例如`timezone Asia/Shanghai --isUtc`。
        *   注意`root`密码是加密的，在此文件中无法直接修改。
        *   将硬盘清除选项`clearpart`后的`--none`改为`--all`以清理所有硬盘。
        *   在`%packages`部分添加`@minimal`以确保最小化安装。

2.  **磁盘空间不足**：
    *   如果安装过程中提示`No space left`，这表示虚拟机分配的磁盘空间不足。解决方案是扩大虚拟机的硬盘容量，例如从默认的20GB增加到40GB或更多。

---

## 第二部分：iptables 实战应用 ⚔️

上一节我们介绍了`iptables`的基础概念，本节中我们通过实战来巩固其应用。

### iptables 核心概念回顾

![](img/d9cd05075626502eb92689c75683872c_14.png)

`iptables`是Linux上经典的防火墙工具，它通过规则链和表来管理网络数据包。

*   **五表五链**：
    *   **五张表**：`filter`（过滤，最常用）、`nat`（地址转换）、`mangle`（包修改）、`raw`（连接跟踪）、`security`（安全）。
    *   **五条链**：`PREROUTING`、`INPUT`、`FORWARD`、`OUTPUT`、`POSTROUTING`。规则将按此顺序处理。
*   **常用参数**：
    *   `-A`：在链末尾追加规则。
    *   `-I`：在链的指定位置（如`-I INPUT 1`）插入规则。
    *   `-D`：删除指定规则。
    *   `-F`：清空链中的所有规则。
    *   `-L`：列出规则。
    *   `-n`：以数字形式显示地址和端口。
    *   `--line-numbers`：显示规则的行号，便于管理。
    *   `-p`：指定协议（如`tcp`, `udp`）。
    *   `--dport`/`--sport`：指定目的/源端口。
    *   `-s`/`-d`：指定源/目的IP地址。
    *   `-j`：指定动作（`ACCEPT`, `DROP`, `REJECT`等）。

**重要提示**：`iptables`规则回车后**立即生效**。真正起作用的是Linux内核的`netfilter`模块。配置前务必谨慎，建议先放行SSH端口（22）再设置默认拒绝策略，以免将自己锁在服务器外。

### 实战一：配置Web服务器防火墙

![](img/d9cd05075626502eb92689c75683872c_16.png)

![](img/d9cd05075626502eb92689c75683872c_18.png)

假设我们需要保护一台运行Apache的Web服务器（Master），仅放行必要的服务端口。

**需求分析**：
1.  放行本地回环接口（lo）的流量，便于测试。
2.  放行对外提供服务的端口：HTTP（80）、HTTPS（443）、SSH（22）。如果使用LNMP架构，还需放行PHP-FPM端口（9000）。
3.  允许已建立的及相关连接通过，确保正常通信不受影响。
4.  设置默认策略为拒绝（DROP），增强安全性。

![](img/d9cd05075626502eb92689c75683872c_20.png)

**配置命令示例**：
```bash
# 1. 设置默认策略为ACCEPT（临时，防止误操作锁死）
iptables -P INPUT ACCEPT
# 清空现有规则
iptables -F

# 2. 放行本地回环接口
iptables -A INPUT -i lo -j ACCEPT

![](img/d9cd05075626502eb92689c75683872c_22.png)

![](img/d9cd05075626502eb92689c75683872c_24.png)

![](img/d9cd05075626502eb92689c75683872c_26.png)

# 3. 放行指定服务端口（使用multiport模块批量指定）
iptables -A INPUT -p tcp -m multiport --dports 22,80,443,9000 -j ACCEPT

![](img/d9cd05075626502eb92689c75683872c_27.png)

![](img/d9cd05075626502eb92689c75683872c_29.png)

![](img/d9cd05075626502eb92689c75683872c_30.png)

![](img/d9cd05075626502eb92689c75683872c_32.png)

# 4. 允许已建立连接及相关连接通过
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# 5. 设置默认输入策略为DROP（务必在放行SSH后执行！）
iptables -P INPUT DROP

![](img/d9cd05075626502eb92689c75683872c_34.png)

![](img/d9cd05075626502eb92689c75683872c_36.png)

![](img/d9cd05075626502eb92689c75683872c_38.png)

![](img/d9cd05075626502eb92689c75683872c_39.png)

# 6. 通常允许所有出站流量
iptables -P OUTPUT ACCEPT
```
**关键点**：规则顺序至关重要。务必在设置`DROP`前放行`SSH`端口。可以使用`iptables-save > /etc/sysconfig/iptables`命令保存配置，使其重启后依然有效。

![](img/d9cd05075626502eb92689c75683872c_41.png)

![](img/d9cd05075626502eb92689c75683872c_43.png)

### 实战二：配置SNAT实现共享上网

![](img/d9cd05075626502eb92689c75683872c_45.png)

我们将Master服务器配置为路由器，使内部网络（Slave）能够通过它访问外部网络。

![](img/d9cd05075626502eb92689c75683872c_47.png)

**网络拓扑**：
*   Master：两张网卡。
    *   `ens33`：连接外网（如`192.168.1.100`）。
    *   `ens34`：连接内网（如`192.168.2.1`）。
*   Slave：一张网卡`ens34`，IP为`192.168.2.2`，网关指向`192.168.2.1`。

![](img/d9cd05075626502eb92689c75683872c_49.png)

![](img/d9cd05075626502eb92689c75683872c_51.png)

![](img/d9cd05075626502eb92689c75683872c_53.png)

![](img/d9cd05075626502eb92689c75683872c_54.png)

**配置步骤**：
1.  **Master基础配置**：为`ens34`配置IP并启动。
2.  **Slave配置**：配置IP和网关。
3.  **Master放行内部流量**：`iptables -A INPUT -i ens34 -j ACCEPT`
4.  **开启内核IP转发**：
    ```bash
    # 临时生效
    echo 1 > /proc/sys/net/ipv4/ip_forward
    # 永久生效，编辑/etc/sysctl.conf，添加或修改：
    net.ipv4.ip_forward = 1
    # 然后执行
    sysctl -p
    ```
5.  **配置SNAT规则**：在`nat`表的`POSTROUTING`链上添加规则，将来自内网网段（`192.168.2.0/24`）的流量源地址转换为Master的外网地址（`192.168.1.100`）。
    ```bash
    iptables -t nat -A POSTROUTING -s 192.168.2.0/24 -o ens33 -j SNAT --to-source 192.168.1.100
    ```
    配置完成后，Slave即可通过Master访问外网。

![](img/d9cd05075626502eb92689c75683872c_56.png)

![](img/d9cd05075626502eb92689c75683872c_58.png)

### 实战三：配置DNAT实现端口映射

![](img/d9cd05075626502eb92689c75683872c_60.png)

现在，我们希望外部用户访问Master的外网IP和80端口时，能够被映射到内部Slave服务器的80端口。

![](img/d9cd05075626502eb92689c75683872c_62.png)

**需求**：访问 `192.168.1.100:80` -> 实际访问 `192.168.2.2:80`

![](img/d9cd05075626502eb92689c75683872c_64.png)

![](img/d9cd05075626502eb92689c75683872c_66.png)

**配置DNAT规则**：在`nat`表的`PREROUTING`链上添加规则。
```bash
iptables -t nat -A PREROUTING -d 192.168.1.100 -p tcp --dport 80 -j DNAT --to-destination 192.168.2.2:80
```
配置后，外部访问Master的80端口，流量就会被转发到Slave的80端口。

![](img/d9cd05075626502eb92689c75683872c_68.png)

![](img/d9cd05075626502eb92689c75683872c_70.png)

![](img/d9cd05075626502eb92689c75683872c_72.png)

---

![](img/d9cd05075626502eb92689c75683872c_74.png)

## 第三部分：动态防火墙 firewalld 详解 🛡️

上一节我们深入使用了`iptables`，本节中我们来看看更高级的动态防火墙管理工具——`firewalld`。

### firewalld 简介

![](img/d9cd05075626502eb92689c75683872c_76.png)

`firewalld`是RHEL/CentOS 7及以上版本默认的防火墙管理工具。与`iptables`相比，其主要特点是：
*   **动态管理**：规则更新无需重启整个防火墙，使用`--reload`重载即可，不会中断现有连接。
*   **区域（Zone）概念**：预定义了一套规则集合（如`public`, `home`, `internal`），通过为网络接口分配不同的区域来快速切换安全策略。
*   **服务（Service）管理**：通过服务名（如`http`, `ssh`）来管理一组端口，更加直观。

![](img/d9cd05075626502eb92689c75683872c_78.png)

`firewalld`自身分为守护进程（`firewalld.service`）和配置工具（命令行`firewall-cmd`或图形界面`firewall-config`），底层依然依赖内核的`netfilter`。

### 核心概念与配置

![](img/d9cd05075626502eb92689c75683872c_80.png)

#### 1. 区域（Zone）

![](img/d9cd05075626502eb92689c75683872c_82.png)

![](img/d9cd05075626502eb92689c75683872c_84.png)

区域是一套预定义的规则模板。系统默认有9个区域，例如：
*   `public`（默认）：在公共区域使用，不信任网络上的其他计算机。
*   `home`：用于家庭网络，信任大多数计算机。
*   `internal`：用于内部网络，信任网络上的大多数计算机。
*   `trusted`：接受所有网络连接。
*   `block`/`drop`：拒绝所有传入连接。

![](img/d9cd05075626502eb92689c75683872c_86.png)

**查看与设置默认区域**：
```bash
# 查看当前默认区域
firewall-cmd --get-default-zone
# 查看所有活动区域
firewall-cmd --get-active-zones
# 设置默认区域为 internal
firewall-cmd --set-default-zone=internal
# 将接口 ens33 分配到 internal 区域
firewall-cmd --zone=internal --change-interface=ens33
```

#### 2. 服务（Service）

![](img/d9cd05075626502eb92689c75683872c_88.png)

服务是`firewalld`管理的核心，它定义了某个应用（如Web服务器）需要开放的端口和协议。配置文件位于`/usr/lib/firewalld/services/`，格式为XML（例如`ssh.xml`, `http.xml`）。

![](img/d9cd05075626502eb92689c75683872c_90.png)

![](img/d9cd05075626502eb92689c75683872c_92.png)

**服务文件示例 (`ssh.xml`)**：
```xml
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>SSH</short>
  <description>Secure Shell (SSH) is a protocol for logging into and executing commands on remote machines. It provides secure encrypted communications. If you plan on accessing your machine remotely via SSH over a firewalled interface, enable this option. You need the openssh-server package installed for this option to be useful.</description>
  <port protocol="tcp" port="22"/>
</service>
```
**管理服务**：
```bash
# 列出所有预定义服务
firewall-cmd --get-services
# 列出当前区域已放行的服务
firewall-cmd --list-services
# 在 public 区域永久放行 http 和 https 服务
firewall-cmd --zone=public --add-service=http --add-service=https --permanent
firewall-cmd --reload
# 移除服务
firewall-cmd --zone=public --remove-service=http --permanent
firewall-cmd --reload
```

#### 3. 端口管理

除了使用服务，也可以直接管理端口。
```bash
# 在 public 区域永久开放 TCP 8080 端口
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --reload
# 移除端口
firewall-cmd --zone=public --remove-port=8080/tcp --permanent
firewall-cmd --reload
```

![](img/d9cd05075626502eb92689c75683872c_94.png)

![](img/d9cd05075626502eb92689c75683872c_96.png)

#### 4. 富规则（Rich Rules）

对于更复杂的规则（如指定源IP），可以使用富规则。
```bash
# 允许来自 192.168.1.0/24 网段的主机访问 public 区域的 80 端口
firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" port protocol="tcp" port="80" accept' --permanent
firewall-cmd --reload
```

![](img/d9cd05075626502eb92689c75683872c_98.png)

![](img/d9cd05075626502eb92689c75683872c_100.png)

#### 5. NAT配置

![](img/d9cd05075626502eb92689c75683872c_102.png)

![](img/d9cd05075626502eb92689c75683872c_104.png)

`firewalld`同样支持SNAT和DNAT，但配置相对复杂，通常直接使用`iptables`命令或编写富规则更为直接。对于复杂需求，建议参考官方文档或使用`firewall-cmd`的`--add-masquerade`（类似于SNAT）和富规则进行端口转发。

![](img/d9cd05075626502eb92689c75683872c_106.png)

### firewalld 配置流程示例

![](img/d9cd05075626502eb92689c75683872c_108.png)

![](img/d9cd05075626502eb92689c75683872c_110.png)

假设要永久开放Web服务器（80端口）：
1.  **方法一：使用服务**（如果`http`服务已定义且端口为80）。
    ```bash
    firewall-cmd --permanent --add-service=http
    firewall-cmd --reload
    ```
2.  **方法二：自定义端口**。
    ```bash
    firewall-cmd --permanent --add-port=80/tcp
    firewall-cmd --reload
    ```
3.  **方法三：自定义服务**（如果端口非标准或需要更复杂的定义）。
    *   复制默认服务文件：`cp /usr/lib/firewalld/services/http.xml /etc/firewalld/services/myweb.xml`
    *   编辑`/etc/firewalld/services/myweb.xml`，修改端口等信息。
    *   加载新服务：`firewall-cmd --permanent --add-service=myweb`，然后`--reload`。

![](img/d9cd05075626502eb92689c75683872c_112.png)

**重要提示**：`--permanent`参数表示将规则写入永久配置，但**不会立即生效**，必须随后执行`--reload`才能激活。如果不加`--permanent`，规则仅临时生效，重启`firewalld`服务后丢失。

![](img/d9cd05075626502eb92689c75683872c_114.png)

![](img/d9cd05075626502eb92689c75683872c_116.png)

![](img/d9cd05075626502eb92689c75683872c_118.png)

---

![](img/d9cd05075626502eb92689c75683872c_120.png)

## 课程总结 🎯

![](img/d9cd05075626502eb92689c75683872c_122.png)

本节课中我们一起学习了Linux防火墙的深度管理。

1.  **iptables实战**：我们回顾了其五表五链结构，并通过三个实战场景（Web服务器防护、SNAT共享上网、DNAT端口映射）巩固了其配置方法。重点是规则顺序、默认策略设置以及NAT的实现。
2.  **firewalld详解**：我们介绍了这款动态防火墙工具的优势，深入理解了其**区域（Zone）**和**服务（Service）**的核心概念。我们学习了如何通过命令行管理区域、放行服务或端口、使用富规则，并了解了其配置流程。

**核心建议**：
*   `iptables`和`firewalld`掌握其一即可满足生产需求，目前`iptables`因其直接高效在生产环境中仍被广泛使用。
*   `firewalld`的配置逻辑更贴近“服务”和“环境”，适合需要频繁切换策略的场景。**无需死记硬背所有命令**，理解其概念（区域、服务、永久生效需`--permanent`和`--reload`），掌握查询方法（`firewall-cmd --help`， `man firewall-cmd`）是关键。
*   无论使用哪种工具，配置防火墙前，**务必确保已放行SSH（22）等管理端口**，避免被锁在服务器之外。

![](img/d9cd05075626502eb92689c75683872c_124.png)

![](img/d9cd05075626502eb92689c75683872c_126.png)

![](img/d9cd05075626502eb92689c75683872c_127.png)

![](img/d9cd05075626502eb92689c75683872c_129.png)

通过本课学习，你应该能够根据实际需求，为Linux服务器配置合适的防火墙策略，保障网络服务的安全与可控。