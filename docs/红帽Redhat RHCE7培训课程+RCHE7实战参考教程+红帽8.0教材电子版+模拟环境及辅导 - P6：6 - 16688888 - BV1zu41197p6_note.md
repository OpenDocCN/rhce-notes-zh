# 红帽Redhat RHCE7培训课程：第11章：网络配置基础

![](img/1ff1e711d8cffba653f72aa3b44b926d_0.png)

在本章中，我们将学习Linux系统网络配置的核心概念和基本操作。我们将从IP地址、子网掩码等基础概念开始，逐步学习如何查看和配置网络参数，包括IP地址、网关、DNS和主机名。

## IP地址与子网掩码

IP地址是网络中设备的唯一标识。我们通常使用十进制表示IP地址，但计算机内部使用二进制进行计算。IP地址与子网掩码进行二进制“与”运算，可以得出网络地址。

**公式：**
`网络地址 = IP地址 & 子网掩码`

例如，IP地址 `172.16.5.3` 与子网掩码 `255.255.0.0` 进行运算，得到网络地址 `172.16.0.0`。网络地址相同的设备属于同一网段，可以直接通信；网络地址不同的设备需要通过路由器才能通信。

![](img/1ff1e711d8cffba653f72aa3b44b926d_2.png)

子网掩码也可以用前缀长度表示，如 `/16` 代表 `255.255.0.0`。划分子网时，借位是连续的，不能跳过。

![](img/1ff1e711d8cffba653f72aa3b44b926d_4.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_6.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_8.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_10.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_12.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_14.png)

## 网络接口与配置

![](img/1ff1e711d8cffba653f72aa3b44b926d_16.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_18.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_20.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_22.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_24.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_26.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_28.png)

在Linux中，网络接口（网卡）有设备名（如 `eth0`）和连接名。从RHEL 7开始，默认命名规则有所变化，但可以通过udev服务创建别名，保持 `eth0` 这样的传统名称。

查看网络接口信息有多种命令。`ip address show` 命令是推荐使用的，因为它是最小化安装系统后依然可用的命令。

![](img/1ff1e711d8cffba653f72aa3b44b926d_30.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_32.png)

**代码：**
```bash
ip address show
ip addr show eth0  # 查看指定网卡
```

![](img/1ff1e711d8cffba653f72aa3b44b926d_34.png)

该命令会显示接口状态、MAC地址、IPv4/IPv6地址、广播地址等信息。

## 网络状态诊断

配置网络后，我们需要验证配置是否正确，并诊断连通性问题。

![](img/1ff1e711d8cffba653f72aa3b44b926d_36.png)

以下是常用的网络诊断命令：

![](img/1ff1e711d8cffba653f72aa3b44b926d_38.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_40.png)

*   **查看路由/网关：** `ip route` 或 `route -n`
*   **测试连通性：** `ping -c 4 目标IP` （`-c` 指定发送包的数量）
*   **路径追踪：** `traceroute 目标IP` 或 `tracepath 目标IP`
*   **查看端口监听状态：** `ss -atunp` 或 `netstat -tunlp`
    *   `-a`：所有
    *   `-t`：TCP协议
    *   `-u`：UDP协议
    *   `-n`：以数字形式显示端口
    *   `-p`：显示进程信息
    *   `-l`：仅显示监听端口

服务与端口对应关系定义在 `/etc/services` 文件中。

## 配置IP地址的四种方法

![](img/1ff1e711d8cffba653f72aa3b44b926d_42.png)

上一节我们介绍了如何查看网络状态，本节中我们来看看如何配置IP地址。主要有四种方法。

### 方法一：使用 `nmcli` 命令

![](img/1ff1e711d8cffba653f72aa3b44b926d_44.png)

`nmcli` 是NetworkManager的命令行工具，用于管理网络连接。配置前需要分清连接名和设备名。

![](img/1ff1e711d8cffba653f72aa3b44b926d_46.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_48.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_50.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_52.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_54.png)

**代码：**
```bash
# 查看连接
nmcli con show
# 查看设备
nmcli dev status
# 修改连接配置（以连接名“eth0”为例，配置静态IP）
nmcli con mod eth0 ipv4.method manual ipv4.addresses “192.168.1.10/24” ipv4.gateway “192.168.1.1” ipv4.dns “8.8.8.8” connection.autoconnect yes
# 使配置生效
nmcli con up eth0
```

![](img/1ff1e711d8cffba653f72aa3b44b926d_56.png)

### 方法二：使用 `nmtui` 命令

![](img/1ff1e711d8cffba653f72aa3b44b926d_58.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_60.png)

`nmtui` 提供了一个基于文本的用户界面（TUI），操作更直观。

![](img/1ff1e711d8cffba653f72aa3b44b926d_62.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_64.png)

**操作流程：**
1.  在终端输入 `nmtui`。
2.  使用方向键选择“Edit a connection”，回车。
3.  选择要编辑的网卡，回车。
4.  在界面中修改IP地址、网关、DNS等信息。
5.  选择“OK”保存，返回主菜单。
6.  选择“Activate a connection”来激活修改后的连接。

### 方法三：使用图形界面

![](img/1ff1e711d8cffba653f72aa3b44b926d_66.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_68.png)

在RHEL 7的图形桌面环境中，可以通过右上角的网络图标进入设置，图形化地修改IP地址、子网掩码、网关和DNS服务器。

### 方法四：直接编辑配置文件

网卡的配置文件位于 `/etc/sysconfig/network-scripts/` 目录下，文件名通常为 `ifcfg-连接名`。

**配置文件示例 (`ifcfg-eth0`)：**
```bash
TYPE=Ethernet
BOOTPROTO=none        # 静态IP，如果是dhcp则写dhcp
DEFROUTE=yes
NAME=eth0             # 连接名
DEVICE=eth0           # 设备名
ONBOOT=yes            # 开机自启
IPADDR=192.168.1.10
PREFIX=24
GATEWAY=192.168.1.1
DNS1=8.8.8.8
```

编辑保存后，需要重启网络服务或重启网卡使配置生效：`systemctl restart network` 或 `ifdown eth0 && ifup eth0`。

## 主机名配置

在RHEL 7中，主机名存储在 `/etc/hostname` 文件中。可以使用 `hostnamectl` 命令来设置。

**代码：**
```bash
# 设置主机名并立即生效
hostnamectl set-hostname server01.example.com
# 查看当前主机名
hostnamectl status
```

![](img/1ff1e711d8cffba653f72aa3b44b926d_70.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_72.png)

## 域名解析

![](img/1ff1e711d8cffba653f72aa3b44b926d_74.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_75.png)

系统进行域名解析时，默认先查询本地的 `/etc/hosts` 文件，再查询DNS服务器。这个顺序由 `/etc/nsswitch.conf` 文件中的 `hosts:` 行定义。

![](img/1ff1e711d8cffba653f72aa3b44b926d_77.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_79.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_81.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_83.png)

**代码：**
```bash
# 编辑hosts文件
vim /etc/hosts
# 添加一行，例如：192.168.1.100 www.mysite.com
```

![](img/1ff1e711d8cffba653f72aa3b44b926d_85.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_87.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_89.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_91.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_93.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_95.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_97.png)

可以使用 `host` 或 `nslookup` 命令进行正向和反向DNS查询。

![](img/1ff1e711d8cffba653f72aa3b44b926d_99.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_101.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_103.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_105.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_106.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_108.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_109.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_111.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_112.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_114.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_116.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_118.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_120.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_122.png)

---

![](img/1ff1e711d8cffba653f72aa3b44b926d_124.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_126.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_127.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_129.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_131.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_133.png)

![](img/1ff1e711d8cffba653f72aa3b44b926d_135.png)

本节课中我们一起学习了Linux网络配置的基础知识。我们理解了IP地址和子网掩码的概念，掌握了查看网络状态（IP、网关、DNS、端口）的命令，并重点练习了四种配置静态IP地址的方法。此外，我们还学会了如何配置主机名，并了解了本地hosts文件在域名解析中的作用。这些是管理Linux服务器网络环境的核心技能。