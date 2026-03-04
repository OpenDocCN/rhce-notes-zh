# Linux教程RHCE：16：squid与iSCSI

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_1.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_3.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_5.png)

## 概述
在本节课中，我们将要学习两种重要的网络服务：squid代理服务和iSCSI网络存储服务。我们将了解squid的正向与反向代理模式，以及iSCSI如何通过网络共享硬盘设备。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_7.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_8.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_10.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_12.png)

## 16.1 squid代理服务简介
squid代理服务有两种主要的工作模式：正向代理模式和反向代理模式。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_14.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_16.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_18.png)

### 正向代理模式
正向代理模式允许局域网内的多个用户通过单一的网关地址访问互联网。这类似于家庭或公司网络中的路由器功能。其目的有两个：
1.  让内网用户能够访问外网。
2.  对用户的上网行为进行审计和管理，例如限制访问某些网站。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_19.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_21.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_23.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_25.png)

正向代理又分为两种子模式：
*   **标准正向代理**：用户需要在浏览器等软件中手动配置代理服务器地址和端口。
*   **透明正向代理**：用户无需进行任何配置，其网络流量会被自动重定向到代理服务器。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_27.png)

### 反向代理模式
反向代理模式通常用于网站加速和负载均衡。它可以将用户的请求转发到后端的多个服务器，并将结果返回给用户。主要好处包括：
1.  **加快用户访问速度**：用户可以从距离最近的缓存节点获取静态资源。
2.  **减轻主服务器压力**：主服务器只需处理动态请求，静态资源由边缘节点提供。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_29.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_31.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_33.png)

## 16.2 配置标准正向代理
上一节我们介绍了squid的基本概念，本节中我们来看看如何配置标准的正向代理模式。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_35.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_36.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_38.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_40.png)

安装squid服务非常简单：
```bash
yum install -y squid
```
安装后，squid默认就支持正向代理。我们需要启动服务并设置开机自启：
```bash
systemctl start squid
systemctl enable squid
```
同时，需要确保防火墙放行squid的默认端口（3128）：
```bash
firewall-cmd --permanent --add-port=3128/tcp
firewall-cmd --reload
```
配置完成后，客户端用户需要在浏览器中设置代理。以Firefox浏览器为例，以下是配置步骤：
1.  进入“选项” -> “高级” -> “网络” -> “设置”。
2.  选择“手动配置代理”。
3.  输入代理服务器的IP地址（例如 `192.168.10.10`）和端口号（`3128`）。
4.  勾选“为所有协议使用相同代理”。
5.  保存设置后，客户端即可通过代理服务器上网。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_42.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_44.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_45.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_47.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_48.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_50.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_52.png)

## 16.3 配置透明正向代理
标准代理需要用户手动配置，对于计算机技能较弱的用户可能造成困难。透明代理可以解决这个问题，用户无需任何配置即可通过代理上网。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_54.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_56.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_58.png)

透明代理的实现需要结合squid和系统的网络地址转换功能。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_60.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_61.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_63.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_65.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_67.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_68.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_69.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_71.png)

首先，编辑squid的主配置文件 `/etc/squid/squid.conf`，进行以下关键修改：
1.  在 `http_port` 行添加 `transparent` 参数，例如：
    ```
    http_port 3128 transparent
    ```
2.  启用缓存目录（大约第62行）：
    ```
    cache_dir ufs /var/spool/squid 100 16 256
    ```
保存配置文件后，需要初始化缓存目录并重启服务：
```bash
squid -k shutdown
squid -z
systemctl restart squid
```
接下来，配置系统的网络转发和NAT规则，将客户端的Web流量重定向到squid端口。假设对外的网卡是 `ens33`，IP是 `192.168.1.3`，内网网段是 `192.168.10.0/24`：
```bash
# 开启系统IP转发功能
echo “net.ipv4.ip_forward = 1” >> /etc/sysctl.conf
sysctl -p

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_73.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_74.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_76.png)

# 添加iptables规则，将内网80端口的流量重定向到squid的3128端口
iptables -t nat -A PREROUTING -i 内网网卡名 -s 192.168.10.0/24 -p tcp --dport 80 -j REDIRECT --to-port 3128

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_78.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_80.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_82.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_83.png)

# 设置NAT，允许内网地址通过伪装上网
iptables -t nat -A POSTROUTING -o ens33 -s 192.168.10.0/24 -j MASQUERADE

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_85.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_87.png)

# 保存iptables规则（根据系统不同，命令可能为`iptables-save`或使用firewalld的direct规则）
```
最后，客户端只需将其网关设置为squid服务器的内网IP地址（例如 `192.168.10.10`），即可透明地通过代理上网。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_89.png)

## 16.4 配置访问控制列表（ACL）
为了验证流量确实经过了squid服务，并且实现管理目的，我们可以在squid上配置访问控制列表。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_91.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_92.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_94.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_96.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_98.png)

编辑 `/etc/squid/squid.conf` 文件，我们可以定义ACL规则。例如，只允许特定IP（`192.168.10.20`）上网：
```
acl allowed_client src 192.168.10.20
http_access allow allowed_client
http_access deny all
```
重启squid服务后，只有IP为 `192.168.10.20` 的客户端可以上网，其他客户端将被拒绝。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_100.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_102.png)

ACL的功能非常强大，还可以基于域名、关键词等进行限制：
*   **禁止访问特定网站**：`acl banned_site dstdomain .example.com`
*   **屏蔽包含特定关键词的网页**：`acl banned_keyword url_regex -i linux`
*   **阻止下载特定类型文件**：`acl banned_file urlpath_regex -i \.mp3$ \.avi$`

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_104.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_106.png)

配置好相应的ACL规则并应用后，squid就能有效地管理和审计网络流量。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_108.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_109.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_111.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_113.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_115.png)

## 16.5 配置反向代理
反向代理模式常用于搭建CDN（内容分发网络）节点或负载均衡器，但它也可能被用于制作钓鱼网站，因此正规网站通常会限制此功能。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_116.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_118.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_120.png)

配置反向代理，同样需要修改 `/etc/squid/squid.conf`。假设我们要缓存一个目标网站（例如 `58.247.138.211`），并让我们自己的服务器（`192.168.1.14`）提供这些内容：
```
# 定义反向代理监听的端口和自身IP
http_port 192.168.1.14:80 vhost

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_122.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_124.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_125.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_126.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_128.png)

# 定义缓存对等体（原始服务器）
cache_peer 58.247.138.211 parent 80 0 no-query originserver

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_130.png)

# 指定所有请求都转发到这个对等体
cache_peer_access 58.247.138.211 allow all
```
保存并重启squid服务后，用户访问 `http://192.168.1.14` 时，看到的内容将是来自 `58.247.138.211` 的缓存内容。这直观地演示了反向代理的工作原理。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_132.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_133.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_135.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_137.png)

## 16.6 iSCSI网络存储服务简介
iSCSI（Internet Small Computer System Interface）技术，实现了通过网络传输SCSI指令，允许客户端像使用本地硬盘一样使用远程存储设备。这与NFS/Samba共享文件或目录不同，iSCSI共享的是**块设备**。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_139.png)

iSCSI架构包含两个角色：
*   **服务端（Target）**：提供存储设备的服务器。
*   **客户端（Initiator）**：使用远程存储的客户端。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_141.png)

它的优势在于提供了灵活的远程存储方案，但性能受网络带宽和延迟的影响。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_143.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_145.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_147.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_148.png)

## 16.7 配置iSCSI服务端（Target）
为了提供更可靠的存储，我们首先在服务端创建一个RAID 10设备作为后端存储。
```bash
mdadm -Cv /dev/md0 -n 4 -l 10 /dev/sdb /dev/sdc /dev/sdd /dev/sde
```
接下来，安装iSCSI服务端软件包：
```bash
yum install -y targetcli
```
启动服务并进入其交互式配置界面：
```bash
systemctl start target
systemctl enable target
targetcli
```
在 `targetcli` 交互界面中，我们需要按照特定流程创建iSCSI target：
1.  **创建后端存储块**：进入 `/backstores/block`，创建一个块设备，指向我们的RAID设备 `/dev/md0`，并给它一个别名，如 `disk0`。
    ```
    create disk0 /dev/md0
    ```
2.  **创建iSCSI Target**：进入 `/iscsi`，创建一个Target，系统会自动生成一个唯一的IQN名称。
    ```
    create
    ```
3.  **设置访问控制**：进入新创建的Target下的 `acls` 目录，创建一个访问控制列表，名称建议使用客户端的标识，如 `iqn.1994-05.com.redhat:client`。
    ```
    create iqn.1994-05.com.redhat:client
    ```
4.  进入 `luns` 目录，将之前创建的块设备 `disk0` 添加进来。
    ```
    create /backstores/block/disk0
    ```
5.  进入 `portals` 目录，设置服务端监听地址和端口（默认3260）。
    ```
    create 192.168.10.10
    ```
6.  最后，输入 `exit` 保存并退出配置界面。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_150.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_152.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_153.png)

不要忘记在防火墙中开放iSCSI端口：
```bash
firewall-cmd --permanent --add-port=3260/tcp
firewall-cmd --reload
```

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_155.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_156.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_158.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_160.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_161.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_163.png)

## 16.8 配置Linux iSCSI客户端（Initiator）
在客户端，首先安装启动器软件包：
```bash
yum install -y iscsi-initiator-utils
```
配置客户端的名称，必须与服务端ACL中设置的名称一致。编辑 `/etc/iscsi/initiatorname.iscsi`：
```
InitiatorName=iqn.1994-05.com.redhat:client
```
然后启动服务：
```bash
systemctl start iscsid
systemctl enable iscsid
```
使用 `iscsiadm` 命令发现并登录Target：
```bash
# 发现目标
iscsiadm -m discovery -t st -p 192.168.10.10

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_165.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_166.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_168.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_170.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_172.png)

# 登录目标（使用发现结果中的Target IQN）
iscsiadm -m node -T iqn.2003-01.org.linux-iscsi.localhost.x8664:sn.df97fbc0c5d7 -p 192.168.10.10 --login
```
登录成功后，客户端系统会出现一个新的磁盘设备（如 `/dev/sdb`）。你可以像使用本地磁盘一样对其进行分区、格式化和挂载。
当不再需要时，可以登出Target：
```bash
iscsiadm -m node -T iqn.2003-01.org.linux-iscsi.localhost.x8664:sn.df97fbc0c5d7 -p 192.168.10.10 --logout
```

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_174.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_176.png)

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_178.png)

## 16.9 配置Windows iSCSI客户端
Windows系统也内置了iSCSI启动器。
1.  打开“控制面板” -> “管理工具” -> “iSCSI发起程序”。
2.  在“配置”选项卡中，将“发起程序名称”修改为与服务端ACL匹配的名称（如 `iqn.1994-05.com.redhat:client`）。
3.  在“发现”选项卡中，添加服务端门户地址 `192.168.10.10`。
4.  在“目标”选项卡中，选择发现到的目标，点击“连接”。
连接成功后，打开“磁盘管理”，你会看到一个新的“未知磁盘”。可以对其进行初始化、新建卷（分区）、格式化并分配盘符，之后就可以像本地磁盘一样使用了。

![](img/ca26e8e94d9b25590090d4c1c68fb8e1_180.png)

## 总结
本节课中我们一起学习了squid代理服务和iSCSI网络存储服务。我们实践了squid正向代理（标准和透明模式）的配置，了解了如何通过ACL管理网络访问，并演示了反向代理的原理。随后，我们学习了iSCSI的架构，完成了从服务端创建Target到Linux及Windows客户端连接并使用远程磁盘的完整流程。这些服务在实际的企业网络和存储环境中有着广泛的应用。