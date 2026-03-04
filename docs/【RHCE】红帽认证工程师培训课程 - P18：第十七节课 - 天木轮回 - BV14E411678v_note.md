# RHCE红帽认证工程师培训课程：P18：第十七节课

## 概述

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_1.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_3.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_4.png)

在本节课中，我们将学习两个核心服务：**Squid代理服务**和**iSCSI网络存储服务**。Squid服务主要用于网络流量转发和访问控制，而iSCSI服务则用于在网络上共享块存储设备。我们将从原理、配置到实践，一步步掌握这两个在企业环境中非常实用的技术。

---

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_6.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_8.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_9.png)

## 第一部分：Squid代理服务

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_11.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_13.png)

上一节我们学习了网络文件共享，本节中我们来看看如何通过代理服务管理和控制网络访问。

Squid是一个功能强大的代理缓存服务器，支持HTTP、HTTPS、FTP等协议。它主要有三种工作模式，我们将逐一学习。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_15.png)

### 三种代理模式简介

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_17.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_19.png)

Squid服务支持以下三种代理模式：
1.  **标准正向代理模式**：客户端需要显式配置代理服务器地址和端口。
2.  **透明正向代理模式**：客户端无需任何配置，流量被网关透明地转发到代理服务器。
3.  **反向代理模式**：代理服务器代表后端Web服务器接收客户端的请求，常用于负载均衡和加速。

接下来，我们将搭建实验环境并配置前两种正向代理模式。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_21.png)

### 实验环境搭建

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_23.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_24.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_26.png)

我们需要准备两台Linux虚拟机：
*   **服务器A（代理服务器）**：配置两块网卡。
    *   网卡1（`eth0`）：桥接模式，用于连接外部网络（如互联网）。
    *   网卡2（`eth1`）：仅主机模式，IP地址为 `192.168.10.10`，用于连接内部网络。
*   **客户端B**：配置一块网卡。
    *   网卡1（`eth0`）：仅主机模式，IP地址为 `192.168.10.20`，网关指向服务器A的内网IP（`192.168.10.10`）。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_28.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_29.png)

网络拓扑示意如下：
```
[互联网]
    |
[服务器A eth0 (桥接)]
    |
[服务器A eth1 (192.168.10.10)]
    |
[客户端B (192.168.10.20)]
```
此架构下，客户端B本身无法直接访问互联网，所有流量需要通过服务器A进行转发。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_31.png)

### 配置标准正向代理模式

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_33.png)

在这种模式下，用户需要在浏览器等客户端软件中手动设置代理服务器。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_35.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_37.png)

以下是配置步骤：

1.  **在服务器A上安装并启动Squid服务**：
    ```bash
    yum install -y squid
    systemctl start squid
    systemctl enable squid
    ```

2.  **配置防火墙，允许代理服务（端口3128）**：
    ```bash
    firewall-cmd --permanent --add-port=3128/tcp
    firewall-cmd --reload
    # 或者临时清空防火墙规则（实验环境）
    iptables -F
    ```

3.  **在客户端B的浏览器中配置代理**：
    *   打开浏览器（如Firefox）的网络设置。
    *   选择“手动代理配置”。
    *   HTTP代理填写服务器A的内网IP：`192.168.10.10`，端口填写 `3128`。
    *   勾选“为所有协议使用相同代理”。
    *   保存设置。

4.  **验证**：在客户端B的浏览器中访问一个外部网站（如 `www.linuxprobe.com`），如果能够成功访问，说明标准正向代理配置成功。

**修改默认端口**：Squid默认监听3128端口，我们可以修改它。
*   编辑配置文件 `/etc/squid/squid.conf`，找到 `http_port 3128` 这一行。
*   将其修改为其他端口，例如 `http_port 10000`。
*   重启Squid服务：`systemctl restart squid`。
*   别忘了在客户端B的浏览器代理设置中将端口也改为 `10000`。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_39.png)

### 配置透明正向代理模式

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_41.png)

透明代理模式下，用户无需配置浏览器，所有HTTP流量会被自动重定向到代理服务器。这通常需要在网关上通过防火墙规则实现。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_43.png)

以下是配置步骤：

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_45.png)

1.  **在服务器A上配置网络地址转换（SNAT）和DNS转发**：
    为了让客户端B能够上网，首先需要在服务器A上启用IP转发和SNAT。
    ```bash
    # 1. 启用IP转发
    echo “net.ipv4.ip_forward = 1” >> /etc/sysctl.conf
    sysctl -p

    # 2. 设置SNAT和DNS转发规则（假设eth0是外网网卡）
    iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
    iptables -t nat -A PREROUTING -i eth1 -p udp --dport 53 -j DNAT --to-destination 192.168.10.10
    # 注意：此处的DNS转发规则仅为示例，实际环境中应指向正确的DNS服务器或由Squid处理。
    ```
    配置后，客户端B应该已经可以解析域名并访问外部网络了（但还未经过Squid代理）。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_47.png)

2.  **修改Squid配置以支持透明代理**：
    *   编辑 `/etc/squid/squid.conf` 配置文件。
    *   找到 `http_port 3128` 这一行，修改为 `http_port 3128 transparent`。
    *   找到并启用缓存目录设置（大约第62行），取消 `cache_dir` 行的注释。
    *   保存并退出。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_49.png)

3.  **创建缓存目录并重启Squid**：
    ```bash
    # 必须先停止Squid才能创建缓存目录
    systemctl stop squid
    squid -z
    systemctl start squid
    ```

4.  **设置防火墙规则，将客户端流量重定向到Squid**：
    这是实现“透明”的关键步骤，将内网客户端80端口的访问请求重定向到Squid的3128端口。
    ```bash
    iptables -t nat -A PREROUTING -i eth1 -s 192.168.10.0/24 -p tcp --dport 80 -j REDIRECT --to-port 3128
    ```

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_51.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_53.png)

5.  **验证**：
    *   在客户端B上，**无需配置浏览器代理**。
    *   直接打开浏览器访问外部网站。如果能够成功访问，说明透明代理配置成功。
    *   为了验证流量确实经过了Squid，我们可以接下来配置访问控制列表（ACL）。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_55.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_56.png)

### 使用ACL进行访问控制

访问控制列表（ACL）是Squid的核心功能之一，允许管理员基于IP地址、域名、URL关键词、文件类型等条件来允许或拒绝访问。

以下是几个常见的ACL配置示例：

**示例1：基于IP地址限制访问**
只允许IP为 `192.168.10.30` 的客户端上网。
```bash
# 在 /etc/squid/squid.conf 的 ACL 部分定义
acl client1 src 192.168.10.30
acl all src 0.0.0.0/0

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_58.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_59.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_60.png)

# 在 http_access 部分应用
http_access allow client1
http_access deny all
```
修改后重启Squid：`systemctl restart squid`。此时只有IP是 `192.168.10.30` 的客户端能上网。

**示例2：禁止访问包含特定关键词的域名**
禁止访问域名中包含 “linux” 的网站。
```bash
acl deny_domain url_regex -i linux
http_access deny deny_domain
```

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_62.png)

**示例3：禁止访问特定网站**
禁止访问 `www.ceshi.com` 这个网站。
```bash
acl bad_site dstdomain www.ceshi.com
http_access deny bad_site
```

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_64.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_65.png)

**示例4：禁止下载特定类型文件**
禁止下载 `.rar` 和 `.mp3` 文件。
```bash
acl deny_file urlpath_regex -i \.rar$ \.mp3$
http_access deny deny_file
```
**注意**：对于使用HTTPS加密的网站，Squid无法解析其URL内容，因此基于URL关键词、域名和文件类型的ACL对HTTPS流量无效。

配置完ACL后，务必重启Squid服务使规则生效：`systemctl restart squid`。

---

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_67.png)

## 第二部分：iSCSI网络存储服务

上一节我们介绍了网络代理服务，本节中我们来看看另一种重要的网络服务——iSCSI，它可以将存储设备通过网络进行共享。

### iSCSI服务概述

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_69.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_71.png)

iSCSI（Internet Small Computer System Interface）是一种基于IP网络的存储协议，它允许客户端（称为 Initiator）通过网络远程访问服务器（称为 Target）提供的硬盘设备。客户端可以将远程硬盘挂载到本地，像使用本地硬盘一样进行分区、格式化和存储数据。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_73.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_75.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_76.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_77.png)

这项技术实现了存储资源的网络化和集中化管理，是构建企业存储区域网络（SAN）的常用技术之一。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_79.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_81.png)

### 配置iSCSI服务端（Target）

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_83.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_84.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_85.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_86.png)

我们的目标是搭建一个稳定可靠的iSCSI服务端。为了保证存储的可靠性，我们首先使用软RAID（这里以RAID 5为例）创建逻辑卷。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_88.png)

以下是配置步骤：

1.  **准备存储设备**：为服务器添加4块10GB的虚拟硬盘（`/dev/sdb`, `/dev/sdc`, `/dev/sdd`, `/dev/sde`）。
2.  **创建RAID 5阵列**：
    ```bash
    mdadm -Cv /dev/md0 -n 3 -l 5 -x 1 /dev/sdb /dev/sdc /dev/sdd /dev/sde
    # -C: 创建，-v: 显示详情，-n: 活动盘数量，-l: RAID级别，-x: 热备盘数量
    ```
3.  **安装iSCSI服务端软件**：
    ```bash
    yum install -y targetcli
    ```
4.  **启动服务并设置开机自启**：
    ```bash
    systemctl start target
    systemctl enable target
    ```
5.  **使用 `targetcli` 交互式工具配置iSCSI Target**：
    运行 `targetcli` 命令进入管理界面。其配置结构类似于一个文件系统，我们需要在正确的“目录”下创建对象。
    *   **创建存储后台（backstore）**：这定义了实际提供给客户端的存储块。
        ```bash
        cd /backstores/block
        create disk0 /dev/md0 # 创建一个名为disk0的后台，对应/dev/md0设备
        ```
    *   **创建iSCSI Target名称（iqn）**：这是客户端的识别标识。
        ```bash
        cd /iscsi
        create iqn.2024-04.com.linuxprobe:server # 生成一个唯一的Target名称
        ```
    *   **创建ACL**：指定哪些客户端（Initiator）可以连接。
        ```bash
        cd iqn.2024-04.com.linuxprobe:server/tpg1/acls
        create iqn.2024-04.com.linuxprobe:client # 创建客户端标识，需与客户端配置一致
        ```
    *   **创建LUN**：将存储后台映射给Target。
        ```bash
        cd ../luns
        create /backstores/block/disk0
        ```
    *   **设置监听地址和端口**：
        ```bash
        cd ../portals
        create 192.168.10.10 3260 # 3260是iSCSI默认端口
        ```
    *   完成后，输入 `exit` 保存并退出。
6.  **配置防火墙**：
    ```bash
    firewall-cmd --permanent --add-port=3260/tcp
    firewall-cmd --reload
    ```

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_90.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_91.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_92.png)

### 配置Linux客户端（Initiator）

现在，我们从另一台Linux客户端连接并使用这个iSCSI存储。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_94.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_96.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_98.png)

以下是配置步骤：

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_99.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_101.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_103.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_104.png)

1.  **安装客户端软件**：
    ```bash
    yum install -y iscsi-initiator-utils
    ```
2.  **编辑客户端配置文件**，设置 Initiator 名称（必须与服务端ACL中配置的名称一致）：
    ```bash
    vim /etc/iscsi/initiatorname.iscsi
    # 写入：
    InitiatorName=iqn.2024-04.com.linuxprobe:client
    ```
3.  **发现目标存储**：
    ```bash
    iscsiadm -m discovery -t st -p 192.168.10.10
    ```
    此命令会返回服务端提供的Target名称。
4.  **登录目标存储**：
    ```bash
    iscsiadm -m node -T iqn.2024-04.com.linuxprobe:server -p 192.168.10.10 --login
    ```
5.  **验证**：使用 `lsblk` 或 `fdisk -l` 命令查看，会发现多出一块磁盘（例如 `/dev/sdb`）。现在你可以像使用本地磁盘一样对其进行分区（`fdisk /dev/sdb`）、格式化（`mkfs.xfs /dev/sdb1`）和挂载（`mount /dev/sdb1 /mnt`）操作。
6.  **断开连接**：使用完毕后，可以注销登录。
    ```bash
    iscsiadm -m node -T iqn.2024-04.com.linuxprobe:server -p 192.168.10.10 --logout
    ```

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_106.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_108.png)

### 配置Windows客户端（Initiator）

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_110.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_111.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_112.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_113.png)

iSCSI协议是跨平台的，Windows系统也可以方便地连接iSCSI存储。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_115.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_116.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_117.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_118.png)

以下是配置步骤：

1.  **打开iSCSI发起程序**：在控制面板的管理工具中，找到并运行“iSCSI发起程序”。
2.  **配置发起程序名称**：在“配置”选项卡中，将“发起程序名称”修改为与服务端ACL匹配的名称（例如 `iqn.2024-04.com.linuxprobe:client`）。
3.  **发现目标**：在“发现”选项卡中，点击“发现门户”，输入服务端IP地址 `192.168.10.10`，端口 `3260`。
4.  **连接目标**：切换到“目标”选项卡，你会看到发现的Target，选中它并点击“连接”。
5.  **初始化和使用磁盘**：打开“磁盘管理”，你会发现一块新的“未初始化”磁盘。右键点击将其初始化，然后新建简单卷，分配盘符并进行格式化。完成后，在“我的电脑”中就会出现新的硬盘分区，可以像本地磁盘一样使用。

---

## 总结

本节课中我们一起学习了两个重要的网络服务：

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_120.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_121.png)

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_123.png)

1.  **Squid代理服务**：我们理解了正向代理（标准与透明）和反向代理的基本概念。通过实验，我们掌握了Squid的安装、基本配置以及如何使用ACL进行灵活的访问控制，这对于企业网络管理、优化和审计至关重要。
2.  **iSCSI网络存储服务**：我们学习了iSCSI的原理，它实现了块级存储的网络共享。我们实践了在Linux上配置服务端（Target），并分别在Linux和Windows客户端（Initiator）上成功连接和使用了远程存储设备。这项技术是构建集中化、可扩展存储解决方案的基础。

![](img/845ba9a62a88eefc7db7f05d3bdb36bf_125.png)

这两项技术都具有很高的实用价值，希望同学们能通过实验深入理解其工作流程和配置方法。