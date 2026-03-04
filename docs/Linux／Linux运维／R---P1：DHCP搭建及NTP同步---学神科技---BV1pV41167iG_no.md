# Linux运维：RHCE：DHCP搭建及NTP同步时间

在本节课中，我们将要学习DHCP（动态主机配置协议）的工作原理、如何搭建DHCP服务器为局域网分配IP地址、如何为特定主机分配固定IP，以及如何使用NTP（网络时间协议）同步系统时间。这些是网络管理和系统运维中的基础且重要的技能。

## DHCP工作原理

上一节我们介绍了课程概述，本节中我们来看看DHCP的核心工作原理。

DHCP，全称动态主机配置协议，是一个局域网的网络协议。它使用**UDP协议**工作，主要用途有两个：
1.  为内部网络或网络服务供应商自动分配IP地址、主机名、DNS服务器及域名。
2.  配合其他服务实现集成化管理功能，例如无人值守安装。

DHCP采用C/S（客户端/服务器）模式。服务器端运行`dhcpd`服务，监听**67端口**；客户端运行`dhcp`服务，监听**68端口**。

![](img/d3c2f6954e8038a6a32142d483ef4016_1.png)

DHCP的工作过程包含四个阶段：
1.  **发现阶段**：客户端以广播形式发送`DHCPDISCOVER`报文寻找服务器。
2.  **提供阶段**：收到请求的DHCP服务器响应一个`DHCPOFFER`报文，其中包含可供租用的IP地址等信息。
3.  **请求阶段**：客户端通常选择第一个收到的`DHCPOFFER`，并广播`DHCPREQUEST`报文进行回应。
4.  **确认阶段**：被选中的服务器发送`DHCPACK`报文进行最终确认，客户端开始使用该IP地址。

![](img/d3c2f6954e8038a6a32142d483ef4016_3.png)

DHCP分配的IP地址有**租约**限制。租约过半时，客户端会尝试续租；若失败，会在租约达到75%和87.5%时再次尝试；若最终续租失败，租约到期后IP地址将被回收。

![](img/d3c2f6954e8038a6a32142d483ef4016_5.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_7.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_9.png)

## 搭建DHCP服务器

![](img/d3c2f6954e8038a6a32142d483ef4016_11.png)

理解了DHCP的工作原理后，本节我们将动手搭建一个DHCP服务器。

![](img/d3c2f6954e8038a6a32142d483ef4016_13.png)

首先，需要在服务器上安装DHCP服务端软件包。
```bash
yum install -y dhcp
```
安装完成后，主配置文件位于`/etc/dhcp/dhcpd.conf`。初始状态下该文件为空，我们可以复制示例模板进行修改。
```bash
cp /usr/share/doc/dhcp-*/dhcpd.conf.example /etc/dhcp/dhcpd.conf
```
一个基础的DHCP服务器配置需要定义一个子网作用域。以下是配置项说明：

![](img/d3c2f6954e8038a6a32142d483ef4016_15.png)

以下是关键配置参数说明：
*   `subnet`：声明要分配IP地址的网络。
*   `netmask`：定义该网络的子网掩码。
*   `range`：定义可供动态分配的IP地址池范围。
*   `option routers`：指定客户端的默认网关。
*   `option domain-name-servers`：指定客户端使用的DNS服务器。
*   `default-lease-time`：默认租约时间（秒）。
*   `max-lease-time`：最大租约时间（秒）。

![](img/d3c2f6954e8038a6a32142d483ef4016_17.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_19.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_21.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_23.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_25.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_26.png)

配置示例：为一个`192.168.1.0/24`的网络分配地址，地址池为`192.168.1.100`到`192.168.1.200`，网关为`192.168.1.1`。
```
subnet 192.168.1.0 netmask 255.255.255.0 {
  range 192.168.1.100 192.168.1.200;
  option routers 192.168.1.1;
  option domain-name-servers 8.8.8.8, 8.8.4.4;
  default-lease-time 600;
  max-lease-time 7200;
}
```
配置完成后，启动DHCP服务并设置为开机自启。
```bash
systemctl start dhcpd
systemctl enable dhcpd
```
可以通过查看租约数据库文件`/var/lib/dhcpd/dhcpd.leases`来确认是否有客户端成功获取地址。

![](img/d3c2f6954e8038a6a32142d483ef4016_28.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_30.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_32.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_34.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_36.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_38.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_40.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_42.png)

## 分配固定IP地址

![](img/d3c2f6954e8038a6a32142d483ef4016_44.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_46.png)

除了动态分配，DHCP服务器还可以根据客户端的MAC地址为其分配固定的IP地址。

这需要使用`host`声明来实现。`host`声明需要放置在对应的`subnet`作用域内部。

配置示例：为MAC地址为`00:0C:29:EF:66:A1`的主机固定分配IP地址`192.168.1.251`。
```
host xuegai64 {
  hardware ethernet 00:0C:29:EF:66:A1;
  fixed-address 192.168.1.251;
}
```
修改配置文件后，重启DHCP服务使配置生效。
```bash
systemctl restart dhcpd
```
客户端需要释放并重新获取IP地址（例如重启网络服务），即可获得配置的固定IP。

![](img/d3c2f6954e8038a6a32142d483ef4016_48.png)

## NTP时间同步

![](img/d3c2f6954e8038a6a32142d483ef4016_50.png)

网络中的服务器时间不一致会导致诸多问题，因此时间同步至关重要。NTP就是用来同步网络中各个计算机时间的协议。

![](img/d3c2f6954e8038a6a32142d483ef4016_52.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_54.png)

首先，安装NTP时间同步工具。
```bash
yum install -y ntpdate
```
安装后，可以使用`ntpdate`命令手动同步时间。例如，同步到阿里云的NTP服务器。
```bash
ntpdate ntp1.aliyun.com
```
为了保证时间持续准确，可以将NTP同步任务加入计划任务`crontab`，实现定期自动同步。

![](img/d3c2f6954e8038a6a32142d483ef4016_56.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_58.png)

以下是配置每天中午12点自动同步时间的示例：
1.  编辑当前用户的计划任务。
    ```bash
    crontab -e
    ```
2.  在文件中添加以下行。
    ```
    0 12 * * * /usr/sbin/ntpdate ntp1.aliyun.com
    ```
默认情况下，NTP只同步系统时间。如果需要将系统时间同步到硬件时钟（BIOS时间），可以编辑`/etc/sysconfig/ntpdate`文件，将`SYNC_HWCLOCK`参数改为`yes`。也可以使用`hwclock`命令手动同步。
```bash
# 将系统时间写入硬件时钟
hwclock -w
# 将硬件时钟时间读取到系统时间
hwclock -s
```

![](img/d3c2f6954e8038a6a32142d483ef4016_60.png)

## 总结

![](img/d3c2f6954e8038a6a32142d483ef4016_62.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_64.png)

![](img/d3c2f6954e8038a6a32142d483ef4016_66.png)

本节课中我们一起学习了DHCP与NTP两大服务。我们了解了DHCP如何通过四个阶段为客户端动态分配IP地址，并动手搭建了DHCP服务器，实现了动态分配和固定IP绑定。随后，我们学习了NTP协议的重要性，掌握了使用`ntpdate`命令及计划任务进行时间同步的方法，并了解了系统时间与硬件时间的同步操作。这些知识是构建稳定、可控的网络环境的基础。