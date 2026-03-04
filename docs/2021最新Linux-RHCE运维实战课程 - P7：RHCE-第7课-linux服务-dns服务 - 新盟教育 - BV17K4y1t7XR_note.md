# Linux-RHCE运维实战课程：P7：RHCE-第7课-linux服务-dns服务

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_1.png)

## 概述
在本节课中，我们将要学习Linux系统中一个核心且有一定难度的基础服务——DNS（域名系统）。我们将从DNS的基本概念入手，逐步学习其工作原理、安装配置、主从同步以及安全加密传输等核心内容，最终能够独立搭建和管理一个功能完整的DNS服务器。

---

## DNS基础概念

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_3.png)

DNS，全称为Domain Name System（域名系统），也称作Domain Name Service（域名解析服务）。它的主要功能是将人类可读的域名（如 `www.baidu.com`）转换为机器可识别的IP地址。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_5.png)

### 端口与协议
DNS服务默认使用**53**端口。需要注意的是，它同时基于**UDP**和**TCP**两种协议。通常，查询请求使用UDP，而区域传输等大数据量操作则使用TCP。

### 分布式数据库与树状结构
DNS本质上是一个**分布式数据库系统**。它的命名空间采用一种**倒树状结构**，从根域开始，向下分为顶级域、二级域、三级域等。

*   **根域**：用一个小点 `.` 表示。
*   **顶级域**：如 `.com`（商业组织）、`.edu`（教育机构）、`.gov`（政府部门）、`.cn`（中国国家域）等。
*   **二级域/子域**：在顶级域下的划分，例如 `baidu.com` 中的 `baidu` 就是 `.com` 的一个子域。

**重要规则**：
*   树中每个节点（域名标签）最多可存储 **63** 个字符。
*   域名的最大深度不超过 **127** 层。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_7.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_9.png)

### 域、域名与FQDN
*   **域**：名称空间的一部分，例如 `cn` 域。
*   **域名**：一个完整的名称，例如 `www.baidu.com`。
*   **FQDN**：完全合格域名。它能准确表示出主机在域名树中的绝对位置，例如 `www.baidu.com.`（注意末尾的点代表根域）。在后续配置Apache等服务时，调整FQDN参数可以优化启动速度。

---

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_11.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_12.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_14.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_16.png)

## DNS解析过程

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_18.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_20.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_22.png)

上一节我们介绍了DNS的静态结构，本节中我们来看看动态的解析过程。当客户端需要访问一个域名时，解析大致遵循以下步骤：

1.  **本地缓存**：首先检查操作系统和浏览器等本地缓存中是否有该域名的解析记录。
2.  **Hosts文件**：如果缓存中没有，则查询本地的 `hosts` 文件。
    *   **Windows路径**：`C:\Windows\System32\drivers\etc\hosts`
    *   **Linux路径**：`/etc/hosts`
    *   文件格式为 `IP地址 域名`。
3.  **本地DNS服务器**：如果 `hosts` 文件也未定义，则向网卡配置中指定的**本地DNS服务器**发起查询。常见的公共DNS有 `114.114.114.114` 或 `8.8.8.8`（Google DNS）。
4.  **根域名服务器**：如果本地DNS服务器没有缓存该记录，它会向全球13台**根域名服务器**发起查询。
5.  **迭代查询**：根服务器会返回负责该顶级域（如 `.com`）的服务器地址。本地DNS服务器再向顶级域服务器查询，得到负责二级域（如 `baidu.com`）的服务器地址，如此迭代，直到最终获得目标主机（如 `www.baidu.com`）的IP地址。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_24.png)

这个过程涉及两种查询方式：
*   **递归查询**：客户端向本地DNS服务器发出请求后，就等待最终结果。本地DNS服务器会“热心”地代为向其他服务器查询，并将最终答案返回给客户端。
*   **迭代查询**：DNS服务器在无法直接回答时，不会代为查询，而是告诉客户端“你去问另一台服务器吧”，并给出下一级服务器的地址。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_26.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_28.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_30.png)

**面试常考点**：请描述递归查询与迭代查询的区别。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_32.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_33.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_35.png)

### 解析类型
*   **正向解析**：通过域名查找对应的IP地址，这是最常见的网络访问方式。
*   **反向解析**：通过IP地址查找对应的域名，常用于日志分析、邮件服务器验证等场景。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_37.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_39.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_41.png)

你可以使用在线工具（如 `dns.aizhan.com`）进行IP反查，验证一个IP地址绑定了哪些域名。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_43.png)

---

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_45.png)

## BIND服务安装与核心配置

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_47.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_48.png)

了解了理论之后，我们开始动手实践。我们将使用 **BIND** 软件来搭建DNS服务器。BIND是Linux下最常用的DNS服务软件。

### 安装BIND
以下是安装BIND服务端和客户端工具的命令：
```bash
yum install -y bind-chroot bind-utils
```
*   `bind-chroot`：服务端软件。`chroot` 代表“牢笼机制”，它会为BIND服务创建一个虚拟的根目录，即使服务被攻陷，攻击者也无法访问真实的系统文件，增强了安全性。
*   `bind-utils`：客户端工具包，提供了 `nslookup`、`dig` 等常用的DNS测试和查询工具。

### 核心配置文件
BIND主要有三个核心配置文件：

1.  **主配置文件**：`/etc/named.conf`
    *   定义DNS服务器的全局参数，如监听地址、允许查询的客户端、递归查询设置等。
2.  **区域配置文件**：`/etc/named.rfc1912.zones`
    *   定义本服务器负责解析哪些“区域”（即域名），以及这些区域对应的数据文件在哪里。
3.  **区域数据文件**：位于 `/var/named/` 目录下
    *   存储具体的域名与IP地址的映射关系，即解析记录。

### 配置主配置文件
首先，我们需要编辑主配置文件 `/etc/named.conf`，使其能够对外提供服务。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_50.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_52.png)

需要修改的关键参数：
*   `listen-on port 53 { any; };`：监听所有IP的53端口。
*   `listen-on-v6 port 53 { any; };`：监听IPv6地址（可选）。
*   `allow-query { any; };`：允许任何客户端向本服务器发起查询。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_54.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_55.png)

同时，确保 `recursion yes;` 这一行存在，它表示允许递归查询。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_57.png)

### 区域类型
在区域配置文件中，`type` 参数定义了区域的类型，主要有以下几种：
*   **master**：主DNS服务器，拥有该区域的原始数据文件，并负责数据的更新。
*   **slave**：从DNS服务器，拥有主服务器区域数据文件的副本，数据从主服务器同步而来。
*   **hint**：根区域类型，用于初始化根域名服务器列表。
*   **forward**：转发区域。当本服务器没有记录时，将查询请求转发给指定的其他DNS服务器。可配合 `forwarders { 8.8.8.8; };` 使用。

---

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_59.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_61.png)

## 配置正向与反向解析区域

上一节我们配置了服务器的全局行为，本节我们来定义它具体要解析的域名。

### 1. 定义区域
编辑区域配置文件 `/etc/named.rfc1912.zones`，在文件末尾添加我们自己的区域定义。

**正向解析区域**（域名 -> IP）：
```bash
zone “xinmeng.com” IN { // 定义要解析的域名
    type master; // 类型为主服务器
    file “xinmeng.com.zone”; // 对应的数据文件名
    allow-update { none; }; // 不允许动态更新，安全考虑
};
```

**反向解析区域**（IP -> 域名）：
```bash
zone “1.168.192.in-addr.arpa” IN { // 注意IP要倒着写，代表192.168.1.0/24网段
    type master;
    file “192.168.1.arpa”; // 反向区域数据文件名
    allow-update { none; };
};
```

### 2. 创建区域数据文件
区域数据文件存放在 `/var/named/` 目录下。我们可以复制模板文件来创建。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_63.png)

```bash
cd /var/named
cp -a named.localhost xinmeng.com.zone # 注意使用 -a 参数保持权限
cp -a named.loopback 192.168.1.arpa
```
`-a` 参数非常重要，因为它能保持文件的属主和权限。BIND服务默认以 `named` 用户身份运行，必须保证它能读取这些数据文件。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_65.png)

### 3. 编辑区域数据文件
区域数据文件由各种**资源记录**组成。以下是核心记录类型：

*   **SOA记录**：起始授权记录，每个区域文件有且仅有一条，定义了区域的全局参数。
*   **NS记录**：域名服务器记录，指明该区域由哪台DNS服务器负责解析。
*   **A记录**：将主机名映射到一个IPv4地址。
*   **PTR记录**：将IP地址映射到主机名（用于反向解析）。
*   **CNAME记录**：别名记录，为一个主机名设置别名。
*   **MX记录**：邮件交换记录，指定负责接收邮件的服务器。

**编辑正向解析文件 `xinmeng.com.zone`**：
```bash
$TTL 1D ; 默认缓存时间
@ IN SOA ns.xinmeng.com. admin.xinmeng.com. (
                    2024032001 ; 序列号，主从同步依据
                    1D ; 刷新时间
                    1H ; 重试时间
                    1W ; 过期时间
                    3H ) ; 否定答案的TTL
        NS ns.xinmeng.com. ; 指定本区域的DNS服务器
ns IN A 192.168.1.100 ; NS记录对应的IP地址
www IN A 192.168.1.100 ; 将 www.xinmeng.com 解析到 192.168.1.100
bbs IN A 192.168.1.100
web IN CNAME www.xinmeng.com. ; 为 www 设置别名 web.xinmeng.com
```
**注意**：域名末尾的 `.` 代表根域，在配置中非常重要，不要遗漏。

**编辑反向解析文件 `192.168.1.arpa`**：
```bash
$TTL 1D
@ IN SOA ns.xinmeng.com. admin.xinmeng.com. (
                    2024032001
                    1D
                    1H
                    1W
                    3H )
        NS ns.xinmeng.com.
100 IN PTR ns.xinmeng.com. ; 将 192.168.1.100 反向解析为 ns.xinmeng.com
100 IN PTR www.xinmeng.com.
100 IN PTR bbs.xinmeng.com.
```

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_67.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_69.png)

### 4. 启动服务并测试
```bash
systemctl restart named # 重启BIND服务
systemctl enable named # 设置开机自启
```
将测试客户端的DNS服务器地址设置为 `192.168.1.100`，然后使用 `nslookup` 或 `ping` 命令测试解析是否成功。

---

## 配置主从DNS服务器同步

为了提高DNS服务的可用性和负载能力，通常会配置主从（Master-Slave）服务器。

### 在主服务器上配置
1.  修改主服务器的区域配置文件 `/etc/named.rfc1912.zones`，在正向和反向区域块中，添加 `allow-transfer` 指令，允许从服务器同步数据。
    ```bash
    zone “xinmeng.com” IN {
        type master;
        file “xinmeng.com.zone”;
        allow-update { none; };
        allow-transfer { 192.168.1.200; }; // 允许IP为200的从服务器同步
    };
    ```
2.  重启主服务器的 `named` 服务。

### 在从服务器上配置
1.  安装BIND软件。
2.  编辑从服务器的区域配置文件 `/etc/named.rfc1912.zones`。
    ```bash
    zone “xinmeng.com” IN {
        type slave; // 类型为从服务器
        file “slaves/xinmeng.com.zone”; // 文件将自动保存在 /var/named/slaves/ 目录下
        masters { 192.168.1.100; }; // 指定主服务器的IP
    };
    ```
    *   **注意**：`file` 路径中的 `slaves/` 是相对路径，基于BIND的工作目录（通常是 `/var/named`）。BIND进程对 `slaves` 目录有写权限。
3.  重启从服务器的 `named` 服务。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_71.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_73.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_75.png)

配置完成后，从服务器会自动联系主服务器，同步区域数据文件。你可以在 `/var/named/slaves/` 目录下查看到同步过来的文件。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_76.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_78.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_80.png)

---

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_82.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_84.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_85.png)

## 配置DNS安全加密传输

默认的主从同步数据是明文传输的，存在安全风险。我们可以使用TSIG（事务签名）技术对区域传输进行加密。

### 在主服务器上操作
1.  **生成密钥对**：
    ```bash
    dnssec-keygen -a HMAC-MD5 -b 128 -n HOST master-slave
    ```
    *   `-a HMAC-MD5`：指定加密算法。
    *   `-b 128`：指定密钥长度。
    *   `-n HOST`：密钥类型与主机相关。
    *   `master-slave`：密钥名称。
    命令执行后会在当前目录生成两个文件：`Kmaster-slave.+xxx+.private`（私钥）和 `Kmaster-slave.+xxx+.key`（公钥）。

2.  **查看并复制密钥**：
    ```bash
    cat Kmaster-slave.+xxx+.private
    ```
    在输出中找到 `Key:` 后面的一串字符，这就是共享密钥，复制它。

3.  **创建密钥文件**：
    在BIND的chroot目录下创建密钥文件 `/var/named/chroot/etc/transfer.key`。
    ```bash
    key “master-slave” { // 密钥名称，与生成时一致
        algorithm hmac-md5;
        secret “粘贴刚才复制的密钥字符串”; // 注意前后不要有空格
    };
    ```
    设置正确的权限：
    ```bash
    chown root:named /var/named/chroot/etc/transfer.key
    chmod 640 /var/named/chroot/etc/transfer.key
    ```

4.  **在主配置文件中引用密钥并启用**：
    编辑 `/etc/named.conf`，在开头引用密钥文件：
    ```bash
    include “/etc/transfer.key”; // 注意，这里写的是chroot外的路径，BIND会自动映射
    ```
    在对应的区域配置中，修改 `allow-transfer` 指令，使用密钥进行认证：
    ```bash
    allow-transfer { key master-slave; };
    ```

5.  重启主服务器的 `named` 服务。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_87.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_89.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_91.png)

### 在从服务器上操作
1.  **创建相同的密钥文件**：
    将主服务器上 `/var/named/chroot/etc/transfer.key` 文件的内容**完全相同地**复制到从服务器的相同路径下。并设置相同的权限。
2.  **在从服务器主配置文件中引用密钥**：
    编辑从服务器的 `/etc/named.conf`，同样在开头添加：
    ```bash
    include “/etc/transfer.key”;
    ```
3.  **指定主服务器并使用密钥**：
    在从服务器的区域配置文件或主配置文件的 `options` 部分，指定主服务器时关联密钥。
    ```bash
    server 192.168.1.100 { // 主服务器IP
        keys { master-slave; }; // 使用的密钥名称
    };
    ```
4.  重启从服务器的 `named` 服务。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_93.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_95.png)

配置完成后，主从服务器之间的区域数据传输将通过密钥进行加密和验证，大大提升了安全性。

---

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_97.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_99.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_101.png)

## 总结与拓展

本节课中我们一起学习了Linux下DNS服务的完整搭建与管理流程。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_103.png)

我们首先理解了DNS的**树状分布式结构**和**递归/迭代查询过程**。然后，我们动手安装了**BIND**软件，并逐步配置了：
1.  基本的**正向解析**和**反向解析**。
2.  通过定义各种资源记录（**A、NS、CNAME、PTR**等）来实现具体的域名映射。
3.  搭建**主从DNS服务器**，实现数据同步和负载分担。
4.  使用**TSIG密钥**对主从之间的区域传输进行加密，保障通信安全。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_105.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_107.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_109.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_111.png)

### 拓展知识
*   **DNS负载均衡**：通过为一个域名配置多个A记录并设置不同的权重，可以实现简单的流量分发。
*   **智能DNS/解析**：根据访问者的来源（如运营商、地理位置）返回不同的IP地址，使访问者能连接到最优的服务器。许多云服务商（如腾讯云DNSPod）都提供此类高级DNS服务。
*   **DNS缓存服务器**：仅提供递归查询和缓存功能，不管理任何区域，常用于企业内网加速外部域名解析。

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_113.png)

![](img/4cc1900dc9cfe9c4c506fc61abf67d77_115.png)

DNS是互联网的基石，熟练掌握其原理和配置是运维工程师的核心技能之一。建议课后在实验环境中反复练习主从配置和加密传输，并尝试研究智能DNS的实现方案。