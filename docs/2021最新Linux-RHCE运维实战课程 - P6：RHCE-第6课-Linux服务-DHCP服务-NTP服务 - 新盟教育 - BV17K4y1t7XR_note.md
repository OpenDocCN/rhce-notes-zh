# Linux运维实战课程：P6：DHCP服务与NTP服务 🖥️

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_1.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_3.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_4.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_6.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_8.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_9.png)

在本节课中，我们将学习两个重要的网络服务：DHCP（动态主机配置协议）和NTP（网络时间协议）。DHCP用于自动分配IP地址，而NTP用于同步系统时间。我们将从原理入手，逐步学习如何在Linux系统上配置和管理这些服务。

## DHCP服务详解 🌐

上一节我们介绍了Linux网络基础，本节中我们来看看如何自动管理IP地址分配。

### DHCP概述与工作原理

DHCP，全称**Dynamic Host Configuration Protocol**（动态主机配置协议），是一个局域网协议，基于UDP协议工作。它的主要作用是自动为网络内的主机分配IP地址、子网掩码、网关和DNS服务器等信息，便于集中管理，避免手动配置的繁琐和错误。

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_11.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_13.png)

DHCP采用**C/S（客户端/服务器）** 模式。服务器端端口是**67**，客户端端口是**68**。

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_15.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_17.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_19.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_21.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_23.png)

DHCP的工作原理包含四个主要阶段，通常称为 **DORA 过程**：

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_25.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_27.png)

1.  **Discover（发现）**：客户端启动后，以广播形式发送`DHCPDISCOVER`报文，寻找网络中的DHCP服务器。
2.  **Offer（提供）**：DHCP服务器收到发现报文后，从地址池中选取一个可用IP，以广播或单播形式向客户端发送`DHCPOFFER`报文。
3.  **Request（请求）**：客户端可能收到多个服务器的Offer，它选择最先到达或优先级最高的一个，并广播`DHCPREQUEST`报文进行确认请求。
4.  **Acknowledge（确认）**：被选中的DHCP服务器收到请求后，发送`DHCPACK`报文进行最终确认，客户端正式获得IP地址租约。

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_29.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_31.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_33.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_34.png)

IP地址有**租约**概念，分为默认租约和最大租约。客户端会在租期达到**50%** 和**87.5%** 时尝试向服务器续租。如果续租失败，租期结束后IP地址将被回收。

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_36.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_38.png)

### 在Linux上配置DHCP服务器

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_39.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_41.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_43.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_45.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_47.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_49.png)

接下来，我们将在CentOS系统上安装和配置DHCP服务器。

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_51.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_52.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_54.png)

首先，安装DHCP服务软件包：
```bash
yum install -y dhcp
```

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_56.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_58.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_60.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_62.png)

DHCP的主配置文件是 `/etc/dhcp/dhcpd.conf`。初始安装后，该文件可能只有注释，我们需要从模板复制或自行编写配置。

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_64.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_66.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_68.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_70.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_71.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_72.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_74.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_76.png)

一个基本的DHCP服务器配置示例如下：
```bash
subnet 192.168.1.0 netmask 255.255.255.0 {  # 定义要分配的网络段
  range 192.168.1.100 192.168.1.200;        # 可分配的IP地址范围
  option routers 192.168.1.1;               # 默认网关
  option subnet-mask 255.255.255.0;         # 子网掩码
  option domain-name "example.com";         # 域名
  option domain-name-servers 192.168.1.1, 8.8.8.8; # DNS服务器
  default-lease-time 600;                   # 默认租约时间（秒）
  max-lease-time 7200;                      # 最大租约时间（秒）
}
```

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_78.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_80.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_82.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_83.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_85.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_87.png)

配置完成后，启动DHCP服务并设置为开机自启：
```bash
systemctl start dhcpd
systemctl enable dhcpd
```

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_89.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_91.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_92.png)

### DHCP高级功能：固定IP分配

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_94.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_95.png)

对于需要固定IP的设备（如服务器），可以将其MAC地址与IP绑定。

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_97.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_99.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_101.png)

在 `/etc/dhcp/dhcpd.conf` 的 `subnet` 块内添加 `host` 声明：
```bash
host server01 {                     # 主机标识名
  hardware ethernet 00:0c:29:xx:xx:xx; # 客户端的MAC地址
  fixed-address 192.168.1.50;       # 要分配的固定IP地址
}
```

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_103.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_105.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_107.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_109.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_111.png)

重启DHCP服务使配置生效：
```bash
systemctl restart dhcpd
```

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_113.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_115.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_117.png)

### DHCP客户端配置

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_119.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_121.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_123.png)

在Linux客户端上，配置网卡通过DHCP获取地址非常简单。编辑网卡配置文件（如 `/etc/sysconfig/network-scripts/ifcfg-ens33`），确保有以下关键配置：
```bash
BOOTPROTO=dhcp    # 协议类型为dhcp
ONBOOT=yes        # 开机自启
```
然后重启网络服务：
```bash
systemctl restart network
```
使用 `ip addr` 或 `ifconfig` 命令即可查看获取到的IP地址。

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_125.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_127.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_129.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_131.png)

---

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_133.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_134.png)

## NTP服务详解 ⏰

上一节我们实现了IP的自动管理，本节中我们来看看如何保证网络中所有设备的时间一致性。

### NTP概述与重要性

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_136.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_138.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_140.png)

NTP，全称 **Network Time Protocol**（网络时间协议），用于同步网络中所有计算机的时间。在集群、日志分析、数据库等场景中，时间不一致会导致各种诡异问题，因此时间同步至关重要。

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_142.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_143.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_145.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_146.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_148.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_150.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_152.png)

Linux系统中有两个时间：
*   **系统时间**：由Linux内核维护。
*   **硬件时间**（BIOS时间）：主板CMOS芯片中的时间。
系统启动时会读取硬件时间，之后通过NTP同步网络时间。我们也可以将系统时间写回硬件时间。

### 配置NTP时间同步

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_154.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_156.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_158.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_160.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_161.png)

在Linux上，我们通常使用 `ntpdate` 命令进行一次性同步，或使用 `chronyd`/`ntpd` 服务进行持续同步。

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_162.png)

首先，可以安装NTP相关工具：
```bash
yum install -y ntp ntpdate
```

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_164.png)

使用 `ntpdate` 手动同步时间（以阿里云NTP服务器为例）：
```bash
ntpdate ntp1.aliyun.com
```

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_165.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_167.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_169.png)

为了持续同步，更推荐启用并配置 `chronyd` 服务（CentOS 7/8默认）：
1.  启动并启用服务：
    ```bash
    systemctl start chronyd
    systemctl enable chronyd
    ```
2.  配置NTP服务器：编辑 `/etc/chrony.conf`，添加或修改 `server` 行：
    ```bash
    server ntp1.aliyun.com iburst
    ```
3.  重启服务并检查同步状态：
    ```bash
    systemctl restart chronyd
    chronyc sources -v
    ```

### 同步系统时间与硬件时间

查看硬件时间：
```bash
hwclock -r
```

将当前的系统时间写入硬件时间：
```bash
hwclock -w
```

### 配置定时时间同步任务

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_171.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_172.png)

为确保时间持续准确，可以设置定时任务。编辑root用户的crontab：
```bash
crontab -e
```
添加以下行，表示每天凌晨3点同步一次时间（请使用绝对路径）：
```bash
0 3 * * * /usr/sbin/ntpdate ntp1.aliyun.com > /dev/null 2>&1
```

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_174.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_175.png)

---

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_177.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_178.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_180.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_182.png)

## 课程总结 📚

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_184.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_186.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_187.png)

本节课中我们一起学习了两个核心的Linux网络服务。

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_189.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_191.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_193.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_195.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_197.png)

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_199.png)

首先，我们深入探讨了**DHCP服务**，理解了其DORA工作原理和租约机制，并动手在Linux服务器上完成了安装、基础配置、地址池分配以及为特定主机绑定固定IP地址的全过程。同时，也学会了如何将客户端配置为自动获取IP。

其次，我们学习了**NTP服务**，明确了时间同步在运维工作中的重要性。我们掌握了使用 `ntpdate` 命令进行手动时间同步，以及配置 `chronyd` 服务实现持续自动同步的方法。此外，还了解了系统时间与硬件时间的区别及同步方式。

![](img/95bcf8a49ec1a376209cc6fddf25b8d5_201.png)

通过本课的学习，你已掌握了构建自动化网络基础环境的关键技能，能够确保网络内设备的IP地址和时间得到有效、统一的管理，为后续更复杂的服务搭建和集群管理打下坚实基础。