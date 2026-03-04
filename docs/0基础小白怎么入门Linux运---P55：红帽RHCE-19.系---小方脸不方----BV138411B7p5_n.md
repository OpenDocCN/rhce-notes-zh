# Linux运维全套培训课程：P55：红帽RHCE-19.系统安全防护之iptables防火墙 🔥

![](img/651d5323ce5b843cde2f085a378cf936_1.png)

![](img/651d5323ce5b843cde2f085a378cf936_3.png)

![](img/651d5323ce5b843cde2f085a378cf936_4.png)

![](img/651d5323ce5b843cde2f085a378cf936_6.png)

![](img/651d5323ce5b843cde2f085a378cf936_8.png)

![](img/651d5323ce5b843cde2f085a378cf936_10.png)

在本节课中，我们将学习iptables防火墙的配置，特别是如何配置网络型防火墙以实现数据包转发和地址转换。我们将通过一个模拟实验环境，从网络规划开始，逐步配置防火墙规则，并学习一些高级扩展功能。

![](img/651d5323ce5b843cde2f085a378cf936_12.png)

![](img/651d5323ce5b843cde2f085a378cf936_14.png)

## 实验环境准备与网络规划

![](img/651d5323ce5b843cde2f085a378cf936_16.png)

为了演示iptables的网络防火墙功能，我们需要准备一个包含三台主机的实验环境：一台防火墙主机、一台内部网站服务器和一台外部客户端。网络规划如下：

![](img/651d5323ce5b843cde2f085a378cf936_18.png)

![](img/651d5323ce5b843cde2f085a378cf936_20.png)

*   **防火墙主机 (iptables)**：
    *   内网网卡 (ens32): `192.168.0.80`
    *   外网网卡 (ens34): `192.168.1.100`
*   **内部网站服务器 (web26)**：
    *   网卡 (ens32): `192.168.0.26`
    *   网关: `192.168.0.80` (指向防火墙内网IP)
*   **外部客户端 (client)**：
    *   网卡 (ens32): `192.168.1.24` (初始IP)
    *   网关: `192.168.1.100` (指向防火墙外网IP)

![](img/651d5323ce5b843cde2f085a378cf936_22.png)

![](img/651d5323ce5b843cde2f085a378cf936_24.png)

![](img/651d5323ce5b843cde2f085a378cf936_26.png)

![](img/651d5323ce5b843cde2f085a378cf936_28.png)

这个规划模拟了典型的企业网络场景：外部客户端通过互联网（`1.0`网段模拟）访问防火墙，防火墙将请求转发给内部服务器（`0.0`网段）。

![](img/651d5323ce5b843cde2f085a378cf936_30.png)

![](img/651d5323ce5b843cde2f085a378cf936_32.png)

---

### 配置防火墙主机的网络

![](img/651d5323ce5b843cde2f085a378cf936_34.png)

![](img/651d5323ce5b843cde2f085a378cf936_36.png)

![](img/651d5323ce5b843cde2f085a378cf936_38.png)

上一节我们介绍了实验的网络拓扑，本节中我们来看看如何具体配置防火墙主机的网络地址。

![](img/651d5323ce5b843cde2f085a378cf936_40.png)

![](img/651d5323ce5b843cde2f085a378cf936_42.png)

![](img/651d5323ce5b843cde2f085a378cf936_44.png)

![](img/651d5323ce5b843cde2f085a378cf936_46.png)

防火墙主机新添加的外网网卡 `ens34` 默认没有配置文件，使用 `ifconfig` 配置的IP是临时的。因此，我们使用 `nmtui` 命令进行交互式配置，它会自动生成永久配置文件。

![](img/651d5323ce5b843cde2f085a378cf936_48.png)

![](img/651d5323ce5b843cde2f085a378cf936_50.png)

以下是配置步骤：
1.  在终端输入 `nmtui` 命令并回车。
2.  选择“编辑连接”。
3.  选择“有线连接 1”（即新添加的网卡）。
4.  将连接名称改为 `ens34`，与网卡设备名保持一致。
5.  在“IPv4配置”处，将“自动”改为“手动”。
6.  点击“显示”按钮，添加IP地址 `192.168.1.100`，子网掩码通常为 `24` (`255.255.255.0`)。
7.  **外网网卡通常不需要配置网关**，因为互联网路由复杂，此处仅作模拟。
8.  一路选择“确定”或“返回”直至退出。

![](img/651d5323ce5b843cde2f085a378cf936_52.png)

![](img/651d5323ce5b843cde2f085a378cf936_54.png)

![](img/651d5323ce5b843cde2f085a378cf936_56.png)

![](img/651d5323ce5b843cde2f085a378cf936_58.png)

![](img/651d5323ce5b843cde2f085a378cf936_60.png)

配置完成后，重启网络服务并检查：
```bash
systemctl restart network
ip addr show ens34
```
此时，`/etc/sysconfig/network-scripts/` 目录下会生成 `ifcfg-ens34` 配置文件，IP地址永久生效。

![](img/651d5323ce5b843cde2f085a378cf936_62.png)

![](img/651d5323ce5b843cde2f085a378cf936_64.png)

![](img/651d5323ce5b843cde2f085a378cf936_66.png)

---

![](img/651d5323ce5b843cde2f085a378cf936_68.png)

![](img/651d5323ce5b843cde2f085a378cf936_70.png)

![](img/651d5323ce5b843cde2f085a378cf936_72.png)

![](img/651d5323ce5b843cde2f085a378cf936_74.png)

![](img/651d5323ce5b843cde2f085a378cf936_76.png)

### 配置内部服务器与客户端

![](img/651d5323ce5b843cde2f085a378cf936_77.png)

![](img/651d5323ce5b843cde2f085a378cf936_79.png)

![](img/651d5323ce5b843cde2f085a378cf936_80.png)

![](img/651d5323ce5b843cde2f085a378cf936_82.png)

网络规划的核心是让数据流经防火墙。因此，内部服务器和客户端的网关必须指向防火墙对应的网卡IP。

![](img/651d5323ce5b843cde2f085a378cf936_84.png)

![](img/651d5323ce5b843cde2f085a378cf936_86.png)

![](img/651d5323ce5b843cde2f085a378cf936_88.png)

![](img/651d5323ce5b843cde2f085a378cf936_90.png)

![](img/651d5323ce5b843cde2f085a378cf936_92.png)

![](img/651d5323ce5b843cde2f085a378cf936_93.png)

**配置内部网站服务器 (web26)：**
1.  编辑网卡配置文件：
    ```bash
    vi /etc/sysconfig/network-scripts/ifcfg-ens32
    ```
2.  修改 `GATEWAY` 的值为 `192.168.0.80`。
3.  保存退出，并重启网络服务：`systemctl restart network`。

![](img/651d5323ce5b843cde2f085a378cf936_95.png)

![](img/651d5323ce5b843cde2f085a378cf936_97.png)

![](img/651d5323ce5b843cde2f085a378cf936_99.png)

![](img/651d5323ce5b843cde2f085a378cf936_101.png)

![](img/651d5323ce5b843cde2f085a378cf936_103.png)

**配置外部客户端 (client)：**
1.  编辑网卡配置文件：
    ```bash
    vi /etc/sysconfig/network-scripts/ifcfg-ens32
    ```
2.  修改 `IPADDR` 的值为 `192.168.1.24`。
3.  修改 `GATEWAY` 的值为 `192.168.1.100`。
4.  保存退出，并重启网络服务。重启后，客户端的IP将变为 `192.168.1.24`。

![](img/651d5323ce5b843cde2f085a378cf936_105.png)

**部署网站服务：**
在 `web26` 服务器上安装并启动Web服务，创建一个测试页面。
```bash
yum install -y httpd
systemctl stop firewalld # 关闭系统防火墙，因为已有外部防火墙
systemctl start httpd
echo "web26" > /var/www/html/index.html
```

![](img/651d5323ce5b843cde2f085a378cf936_107.png)

![](img/651d5323ce5b843cde2f085a378cf936_109.png)

---

![](img/651d5323ce5b843cde2f085a378cf936_111.png)

![](img/651d5323ce5b843cde2f085a378cf936_113.png)

### 配置防火墙转发与基础规则

![](img/651d5323ce5b843cde2f085a378cf936_115.png)

环境准备就绪后，我们开始在防火墙主机上配置核心功能。首先，需要开启Linux内核的IP转发功能，这是实现路由转发的前提。
```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```
此命令临时生效。若需永久生效，需编辑 `/etc/sysctl.conf` 文件，设置 `net.ipv4.ip_forward = 1`，然后执行 `sysctl -p`。

此时，未配置任何拒绝规则前，客户端 (`1.24`) 应能直接访问内部服务器 (`0.26`) 的网站。这是因为请求路径为：`客户端 -> 防火墙(外网卡接收 -> 内网卡转发) -> 内部服务器`。

接下来，我们配置一条防火墙规则，拒绝该客户端的访问。
```bash
iptables -I FORWARD -s 192.168.1.24 -p tcp --dport 80 -j REJECT
```
*   `-I FORWARD`: 在 `FORWARD` 链（负责转发的数据链）中插入规则。
*   `-s 192.168.1.24`: 匹配源IP地址为 `192.168.1.24` 的数据包。
*   `-p tcp --dport 80`: 匹配使用TCP协议且目标端口为80（HTTP）的数据包。
*   `-j REJECT`: 对匹配的数据包执行`REJECT`操作（拒绝并返回拒绝信息）。

![](img/651d5323ce5b843cde2f085a378cf936_117.png)

![](img/651d5323ce5b843cde2f085a378cf936_119.png)

配置后，客户端再次访问网站将被拒绝。这实现了网络型防火墙保护内部服务器的基本功能。

![](img/651d5323ce5b843cde2f085a378cf936_121.png)

---

![](img/651d5323ce5b843cde2f085a378cf936_123.png)

![](img/651d5323ce5b843cde2f085a378cf936_125.png)

### iptables扩展功能应用

![](img/651d5323ce5b843cde2f085a378cf936_127.png)

![](img/651d5323ce5b843cde2f085a378cf936_129.png)

基础规则基于IP地址进行过滤。但如果客户端更换了IP地址，规则就会失效。为了解决这个问题，我们可以使用iptables的扩展模块。

![](img/651d5323ce5b843cde2f085a378cf936_131.png)

![](img/651d5323ce5b843cde2f085a378cf936_133.png)

**1. 基于MAC地址过滤**
MAC地址是网卡的物理标识，更换IP通常不会改变MAC地址（除非特殊修改）。首先，我们需要获取客户端的MAC地址。在防火墙主机上使用 `nmap` 工具扫描：
```bash
yum install -y nmap
nmap -sn 192.168.1.24
```
在输出信息中找到 `MAC Address`。然后，配置基于MAC地址的过滤规则：
```bash
iptables -I FORWARD -p tcp --dport 80 -m mac --mac-source 客户端MAC地址 -j REJECT
```
*   `-m mac`: 调用 `mac` 扩展模块。
*   `--mac-source`: 指定源MAC地址。

![](img/651d5323ce5b843cde2f085a378cf936_135.png)

这样，无论客户端如何更换IP，只要其网卡MAC地址被匹配，访问就会被拒绝。

![](img/651d5323ce5b843cde2f085a378cf936_137.png)

![](img/651d5323ce5b843cde2f085a378cf936_139.png)

**2. 基于多端口过滤**
当需要同时控制对多个端口的访问时（如FTP的21端口、HTTPS的443端口、MySQL的3306端口），可以使用 `multiport` 模块。
```bash
iptables -I FORWARD -p tcp -m multiport --dports 21,80,443,3306 -j REJECT
```
*   `-m multiport`: 调用 `multiport` 扩展模块。
*   `--dports`: 指定多个目标端口，用逗号分隔。

![](img/651d5323ce5b843cde2f085a378cf936_141.png)

![](img/651d5323ce5b843cde2f085a378cf936_143.png)

**3. 基于IP范围过滤**
如果需要拒绝或允许一个连续的IP地址段，可以使用 `iprange` 模块。
```bash
iptables -I FORWARD -p tcp --dport 80 -m iprange --src-range 192.168.1.20-192.168.1.30 -j REJECT
```
*   `-m iprange`: 调用 `iprange` 扩展模块。
*   `--src-range`: 指定源IP地址范围。

![](img/651d5323ce5b843cde2f085a378cf936_145.png)

---

### 配置SNAT实现共享上网

![](img/651d5323ce5b843cde2f085a378cf936_147.png)

![](img/651d5323ce5b843cde2f085a378cf936_149.png)

![](img/651d5323ce5b843cde2f085a378cf936_150.png)

在企业中，内部私有地址的机器需要通过防火墙访问互联网。这可以通过配置SNAT（源地址转换）实现，将内部地址转换为防火墙的公网地址。

我们调整实验角色：
*   **内部主机 (client)**: IP为 `192.168.1.25`，网关指向 `192.168.1.100`（防火墙“内网”卡）。
*   **防火墙 (iptables)**: `ens34` (`192.168.1.100`) 作为内网卡，`ens32` (`192.168.0.80`) 作为外网卡。
*   **外部服务器 (web26)**: IP为 `192.168.0.26`，模拟互联网上的服务器。

![](img/651d5323ce5b843cde2f085a378cf936_152.png)

![](img/651d5323ce5b843cde2f085a378cf936_154.png)

![](img/651d5323ce5b843cde2f085a378cf936_156.png)

在防火墙上配置SNAT规则：
```bash
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -p tcp -j SNAT --to-source 192.168.0.80
```
*   `-t nat`: 指定操作 `nat` 表，该表用于地址转换。
*   `-A POSTROUTING`: 在 `POSTROUTING` 链（路由后处理，数据包即将发出时）添加规则。
*   `-s 192.168.1.0/24`: 匹配来自 `192.168.1.0/24` 这个网段的所有源IP。
*   `-j SNAT`: 执行SNAT操作。
*   `--to-source 192.168.0.80`: 将源IP转换为 `192.168.0.80`。

![](img/651d5323ce5b843cde2f085a378cf936_158.png)

![](img/651d5323ce5b843cde2f085a378cf936_159.png)

![](img/651d5323ce5b843cde2f085a378cf936_161.png)

![](img/651d5323ce5b843cde2f085a378cf936_163.png)

配置后，内部主机 (`1.25`) 访问外部服务器 (`0.26`) 时，在外部服务器的访问日志中，看到的访问者IP将是防火墙的外网IP (`0.80`)，从而实现了地址转换和共享上网。

![](img/651d5323ce5b843cde2f085a378cf936_165.png)

![](img/651d5323ce5b843cde2f085a378cf936_167.png)

---

![](img/651d5323ce5b843cde2f085a378cf936_169.png)

![](img/651d5323ce5b843cde2f085a378cf936_171.png)

### 规则的保存与清除

![](img/651d5323ce5b843cde2f085a378cf936_173.png)

![](img/651d5323ce5b843cde2f085a378cf936_175.png)

iptables配置的规则默认是临时的，重启后会失效。需要手动保存。
```bash
service iptables save
```
规则会被保存到 `/etc/sysconfig/iptables` 文件中。如需永久修改或删除规则，可以直接编辑此文件，或者清空所有规则后重新保存。
*   **清空所有规则**:
    ```bash
    iptables -F        # 清空filter表规则
    iptables -t nat -F # 清空nat表规则
    iptables -t mangle -F # 清空mangle表规则
    ```
*   **删除保存的规则文件**:
    ```bash
    rm -f /etc/sysconfig/iptables
    ```

![](img/651d5323ce5b843cde2f085a378cf936_177.png)

![](img/651d5323ce5b843cde2f085a378cf936_179.png)

---

## 课程总结

![](img/651d5323ce5b843cde2f085a378cf936_181.png)

本节课中我们一起学习了iptables防火墙的高级应用。我们从零开始规划并搭建了一个模拟企业网络环境，然后逐步配置：
1.  **网络地址与网关**，确保数据流经防火墙。
2.  **内核转发与FORWARD链规则**，实现了基本的网络层访问控制。
3.  多种**扩展模块**（`mac`, `multiport`, `iprange`）的使用，增强了过滤的灵活性和精准性。
4.  **SNAT源地址转换**，使内部私有网络能够通过防火墙访问外部网络。

![](img/651d5323ce5b843cde2f085a378cf936_183.png)

![](img/651d5323ce5b843cde2f085a378cf936_184.png)

![](img/651d5323ce5b843cde2f085a378cf936_186.png)

iptables功能强大且配置灵活，其规则语法需要结合实践来熟悉。在实际工作中，复杂的规则通常通过编写脚本或参考笔记来配置。掌握这些核心概念和操作，是构建Linux系统安全防护体系的重要基础。