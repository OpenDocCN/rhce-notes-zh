# Linux教程RHCE：P14：14.DNS服务

![](img/8b2031f532ec5ae1ad1d04155b91afee_1.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_3.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_4.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_5.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_6.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_8.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_9.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_11.png)

## 概述
在本节课中，我们将学习DNS服务。DNS是互联网的核心服务之一，负责将人类可读的域名转换为机器可识别的IP地址。我们将从DNS的基本概念讲起，逐步学习如何配置主DNS服务器、从属DNS服务器以及缓存DNS服务器，并了解TSIG加密技术如何保障DNS数据传输的安全性。

## DNS基础概念
上一节我们介绍了NFS和AutoFS服务，本节中我们来看看DNS服务。DNS，全称Domain Name System，即域名系统。它的核心功能是建立域名与IP地址之间的映射关系。

![](img/8b2031f532ec5ae1ad1d04155b91afee_13.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_14.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_16.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_18.png)

DNS主要提供两种解析功能：
*   **正向解析**：将域名转换为IP地址。例如，将 `www.example.com` 解析为 `192.168.1.1`。
*   **反向解析**：将IP地址转换为域名。例如，将 `192.168.1.1` 解析为 `www.example.com`。

![](img/8b2031f532ec5ae1ad1d04155b91afee_19.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_21.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_22.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_23.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_25.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_26.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_27.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_29.png)

DNS服务器根据角色可分为三种类型：
*   **主服务器**：管理特定域内域名与IP地址的对应关系，是权威数据的来源。
*   **从属服务器**：从主服务器同步数据，用于分担查询压力和提高可用性。
*   **缓存服务器**：不管理具体域数据，仅转发查询请求并缓存结果，用于加速本地网络查询。

![](img/8b2031f532ec5ae1ad1d04155b91afee_31.png)

## 配置主DNS服务器
接下来，我们将动手配置一个主DNS服务器，实现正向和反向解析。

### 1. 安装BIND软件包
首先，我们需要安装BIND软件包及其安全增强工具。
```bash
yum install -y bind-chroot
```

### 2. 编辑主配置文件
BIND的主配置文件是 `/etc/named.conf`。我们需要修改两个关键参数。
```bash
vim /etc/named.conf
```
找到以下两行并进行修改：
*   第11行 `listen-on port 53`：将其后的IP地址改为 `any`，表示监听所有网络接口。
*   第17行 `allow-query`：将其后的IP地址改为 `any`，表示允许所有客户端查询。

### 3. 配置区域数据文件
区域数据文件定义了具体的域名和IP地址的映射关系。我们需要编辑 `/etc/named.rfc1912.zones` 文件来声明我们的域。
```bash
vim /etc/named.rfc1912.zones
```
在文件末尾添加以下内容，分别定义一个正向解析域和一个反向解析域：
```
zone "linuxprobe.com" IN {
        type master;
        file "linuxprobe.com.zone";
        allow-update { none; };
};
zone "10.168.192.in-addr.arpa" IN {
        type master;
        file "192.168.10.arpa";
        allow-update { none; };
};
```
*   `zone`：定义域名或反向网段。
*   `type master`：表示此服务器是该区域的主服务器。
*   `file`：指定存储具体解析记录的文件名。
*   `allow-update`：定义允许动态更新的客户端，这里设置为 `none`。

### 4. 创建正向解析记录文件
根据上一步的配置，我们需要创建 `linuxprobe.com.zone` 文件。
```bash
cd /var/named
cp -a named.localhost linuxprobe.com.zone
vim linuxprobe.com.zone
```
文件内容修改如下：
```
$TTL 1D
@       IN SOA  linuxprobe.com.  root.linuxprobe.com. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        NS      ns.linuxprobe.com.
ns      IN A    192.168.10.10
www     IN A    192.168.10.10
```
*   `SOA`：起始授权记录，定义了域的基本信息和管理员邮箱（`@`替换为`.`）。
*   `NS`：域名服务器记录，指定该域的DNS服务器。
*   `A`：地址记录，将主机名映射到IPv4地址。

![](img/8b2031f532ec5ae1ad1d04155b91afee_33.png)

### 5. 创建反向解析记录文件
同样，我们需要创建反向解析文件 `192.168.10.arpa`。
```bash
cp -a named.loopback 192.168.10.arpa
vim 192.168.10.arpa
```
文件内容修改如下：
```
$TTL 1D
@       IN SOA  linuxprobe.com. root.linuxprobe.com. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        NS      ns.linuxprobe.com.
10      PTR     ns.linuxprobe.com.
10      PTR     www.linuxprobe.com.
```
*   `PTR`：指针记录，用于反向解析，将IP地址映射到域名。

![](img/8b2031f532ec5ae1ad1d04155b91afee_35.png)

**重要**：创建文件后，务必修改文件的所有组为 `named`，并设置正确的权限，否则BIND服务可能无法读取。
```bash
chown root:named /var/named/linuxprobe.com.zone
chown root:named /var/named/192.168.10.arpa
```

### 6. 启动服务并测试
启动BIND服务，并设置为开机自启。
```bash
systemctl start named
systemctl enable named
```
配置客户端网卡的DNS服务器地址指向本机（`192.168.10.10`），然后使用 `nslookup` 或 `host` 命令测试解析是否成功。
```bash
nslookup www.linuxprobe.com 192.168.10.10
nslookup 192.168.10.10 192.168.10.10
```

## 配置从属DNS服务器
主服务器配置完成后，我们来配置一台从属服务器，它可以从主服务器同步数据。

![](img/8b2031f532ec5ae1ad1d04155b91afee_37.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_39.png)

### 1. 在主服务器上授权
在主服务器的 `/etc/named.rfc1912.zones` 文件中，修改 `allow-update` 字段，添加从属服务器的IP地址。
```
allow-update { 192.168.10.20; };
```
重启主服务器的BIND服务使配置生效。
```bash
systemctl restart named
```

![](img/8b2031f532ec5ae1ad1d04155b91afee_41.png)

### 2. 在从属服务器上配置
在从属服务器上安装BIND，并编辑其 `/etc/named.rfc1912.zones` 文件。
```bash
yum install -y bind-chroot
vim /etc/named.rfc1912.zones
```
添加以下区域配置，注意 `type` 为 `slave`，并指定主服务器地址和文件存放路径。
```
zone "linuxprobe.com" IN {
        type slave;
        masters { 192.168.10.10; };
        file "slaves/linuxprobe.com.zone";
};
zone "10.168.192.in-addr.arpa" IN {
        type slave;
        masters { 192.168.10.10; };
        file "slaves/192.168.10.arpa";
};
```

![](img/8b2031f532ec5ae1ad1d04155b91afee_43.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_45.png)

### 3. 启动从属服务并验证
启动从属服务器的BIND服务，并检查 `/var/named/slaves/` 目录下是否同步到了区域文件。然后将客户端的DNS指向从属服务器（`192.168.10.20`）进行测试。
```bash
systemctl start named
ls /var/named/slaves/
```

## 使用TSIG密钥进行加密传输
为了保证主从服务器之间数据传输的安全性，防止数据在同步过程中被篡改，我们可以使用TSIG密钥进行加密。

![](img/8b2031f532ec5ae1ad1d04155b91afee_47.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_49.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_51.png)

### 1. 在主服务器上生成密钥
在主服务器上，使用 `dnssec-keygen` 命令生成一个密钥对。
```bash
dnssec-keygen -a HMAC-MD5 -b 128 -n HOST master-slave
```
该命令会生成两个文件（`.key` 和 `.private`）。查看 `.private` 文件，获取密钥字符串。
```bash
cat Kmaster-slave*.private
```

![](img/8b2031f532ec5ae1ad1d04155b91afee_53.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_55.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_57.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_59.png)

### 2. 在主服务器上配置密钥
创建一个密钥配置文件，并设置正确的权限。
```bash
vim /etc/named.transfer.key
```
内容如下（将 `Key` 后的字符串替换为刚才生成的密钥）：
```
key "master-slave" {
    algorithm hmac-md5;
    secret "你的密钥字符串";
};
```
```bash
chown root:named /etc/named.transfer.key
chmod 640 /etc/named.transfer.key
```
在主配置文件 `/etc/named.conf` 的开头引入此密钥文件，并在区域配置的 `allow-update` 或 `masters` 语句中使用该密钥。
```
include "/etc/named.transfer.key";
```
修改区域配置，例如：
```
zone "linuxprobe.com" IN {
        type master;
        file "linuxprobe.com.zone";
        allow-update { key "master-slave"; };
};
```

![](img/8b2031f532ec5ae1ad1d04155b91afee_61.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_62.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_63.png)

### 3. 在从属服务器上配置密钥
将主服务器上生成的密钥文件 `/etc/named.transfer.key` 复制到从属服务器的相同位置，并修改所有组和权限。
同样，在从属服务器的 `/etc/named.conf` 开头引入密钥文件，并修改区域配置中的 `masters` 语句。
```
masters { 192.168.10.10 key "master-slave"; };
```

![](img/8b2031f532ec5ae1ad1d04155b91afee_65.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_67.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_69.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_70.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_72.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_74.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_75.png)

### 4. 重启服务验证
重启主从双方的BIND服务。可以尝试删除从属服务器上的区域文件，观察其是否能通过加密通道重新从主服务器同步。

![](img/8b2031f532ec5ae1ad1d04155b91afee_77.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_78.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_79.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_81.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_83.png)

## 配置缓存DNS服务器
缓存DNS服务器不管理任何区域，它只是将客户端的查询请求转发给上游DNS服务器，并将结果缓存起来，以加快内网用户的访问速度。

![](img/8b2031f532ec5ae1ad1d04155b91afee_85.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_87.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_89.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_91.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_93.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_95.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_97.png)

配置缓存服务器非常简单，只需在主配置文件 `/etc/named.conf` 中添加转发规则即可。
```
options {
    ...
    forwarders { 8.8.8.8; 114.114.114.114; };
    forward only;
    ...
};
```
*   `forwarders`：指定上游DNS服务器的地址。
*   `forward only`：表示仅转发，自身不进行递归查询。

![](img/8b2031f532ec5ae1ad1d04155b91afee_99.png)

配置完成后，重启BIND服务。将内网客户端的DNS服务器指向此缓存服务器，即可通过它来解析互联网域名。

![](img/8b2031f532ec5ae1ad1d04155b91afee_101.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_102.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_103.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_104.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_106.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_108.png)

![](img/8b2031f532ec5ae1ad1d04155b91afee_110.png)

## 总结
本节课中我们一起学习了DNS服务的核心概念与配置方法。我们首先了解了DNS的正向与反向解析功能，以及主、从、缓存三种服务器类型。随后，我们逐步实践了如何配置一个具备正向和反向解析能力的主DNS服务器，并在此基础上搭建了从属服务器以实现数据同步和负载分担。为了保障同步过程的安全，我们引入了TSIG密钥加密技术。最后，我们还学习了如何配置简单的缓存DNS服务器来加速内网查询。DNS是网络基础架构的关键部分，掌握其原理和配置对于系统管理员至关重要。