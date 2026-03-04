# Linux云计算架构运维基础教程：P1：DNS域名解析服务的基本应用与主从技术 🌐

![](img/79905331b0173310c2d6a73c024d3395_1.png)

## 概述
在本节课中，我们将学习DNS域名解析服务的基本原理、在企业中的应用场景，并动手实践如何部署一个具备正向和反向解析功能的DNS服务器。DNS是互联网的“电话簿”，理解其工作原理是运维工程师的基础技能。

---

## DNS服务简介与作用

上一节我们介绍了Linux运维学习的整体路径，本节中我们来看看企业基础服务中最复杂的服务之一——DNS。

DNS的核心作用是**域名解析**。它主要分为两个方向：
*   **正向解析**：将域名转换为IP地址。例如，在浏览器输入 `www.example.com`，DNS会帮你找到对应的服务器IP。
*   **反向解析**：将IP地址转换为域名。用于确认某个IP地址绑定了哪些域名。

其根本目的是为了方便记忆。IP地址（如 `192.168.1.1`）难以记忆，而域名（如 `www.baidu.com`）则直观得多。此外，一个域名可以对应多个IP（实现负载均衡），一个IP也可以绑定多个域名。

---

![](img/79905331b0173310c2d6a73c024d3395_3.png)

![](img/79905331b0173310c2d6a73c024d3395_4.png)

![](img/79905331b0173310c2d6a73c024d3395_6.png)

## DNS在企业中的应用场景

以下是DNS服务在企业中的几种典型应用：

1.  **大型互联网公司或国企**：这类机构拥有大量服务器和内部网络，为了管理方便、高效和安全，会选择自建DNS服务器来管理内部域名解析，而不是依赖第三方服务。
2.  **运营商与大型企业**：对于业务覆盖全球的公司，用户从不同地区访问同一个域名时，如果都指向远端的同一台DNS服务器，体验会很差。此时会采用 **DNS视图（分离解析）** 技术。简单来说，就是DNS服务器根据请求来源的地区（通过判断请求来自哪块网卡），将同一个域名解析到不同地区的服务器IP，实现就近访问。

---

## DNS解析原理与过程

了解了DNS的作用后，我们来看看当你在浏览器输入一个网址时，背后发生了什么。这是一个典型的迭代查询过程：

1.  **查询本地Hosts文件**：系统首先检查本地的 `hosts` 文件（Windows在 `C:\Windows\System32\drivers\etc\`，Linux在 `/etc/hosts`）。如果文件中存在该域名的记录，则直接使用，**这是优先级最高的解析方式**。
2.  **查询本地DNS服务器**：如果hosts文件没有记录，系统会向网卡配置中指定的 **本地DNS服务器**（如 `8.8.8.8` 或 `114.114.114.114`）发起查询。
3.  **查询根域名服务器**：如果本地DNS服务器没有缓存该域名的记录，它会向全球13台 **根域名服务器** 发起查询。根服务器不会直接告诉你答案，它会根据域名的后缀（如 `.com`），告诉你负责 `.com` 的顶级域名服务器的地址。
4.  **迭代查询**：本地DNS服务器接着向 `.com` 服务器查询，`.com` 服务器再告知负责 `example.com` 的权威DNS服务器的地址。最终，本地DNS服务器从 `example.com` 的权威服务器获得准确的IP地址。
5.  **返回并缓存**：本地DNS服务器将IP地址返回给客户端，并**缓存**这个记录。下次再有相同的查询时，就可以直接回复，无需再次进行完整的查询过程。

**核心概念公式**：
`客户端 -> hosts文件 -> 本地DNS -> 根DNS -> 顶级DNS -> 权威DNS -> 获取IP -> 返回客户端`

---

## 部署DNS服务：BIND

理论清晰后，我们开始动手部署。在Linux上，最常用的DNS服务软件是 **BIND**。我们以部署正向和反向解析为例。

### 实验环境与软件安装
我们使用两台服务器：一台主服务器（Master），一台从服务器（Slave）。本节先完成主服务器的配置。
安装软件包：`bind-chroot`（一个增强了安全性的BIND版本）。
```bash
yum install bind-chroot -y
```

### 核心配置文件
部署DNS需要修改三个核心配置文件，它们各司其职：

1.  **主配置文件 (`/etc/named.conf`)**：定义DNS服务的基本行为，如监听端口、允许查询的客户端等。
2.  **区域配置文件 (`/etc/named.rfc1912.zones`)**：定义DNS服务器要负责解析的“区域”（即域名），并指定对应数据文件的位置。
3.  **数据文件 (`/var/named/` 目录下)**：详细记录域名与IP地址的对应关系，是解析信息的最终来源。

---

### 配置实战：正向解析

以下是配置正向解析 `thinkmo.com.cn` 域名的步骤：

**第一步：修改主配置文件**
编辑 `/etc/named.conf`：
*   将 `listen-on port 53 { 127.0.0.1; };` 改为 `listen-on port 53 { any; };`，允许监听所有IP的53端口请求。
*   将 `allow-query { localhost; };` 改为 `allow-query { any; };`，允许所有客户端查询。
```bash
vim /etc/named.conf
# 修改上述两行后保存退出
```

**第二步：配置区域文件**
编辑 `/etc/named.rfc1912.zones`，在文件末尾添加以下区域定义：
```bash
zone "thinkmo.com.cn" IN {        # 定义要管理的域名
    type master;                   # 类型为主服务器
    file "thinkmo.com.cn.zone";   # 指定数据文件名
    allow-update { none; };        # 不允许动态更新
};
```

**第三步：创建并编辑数据文件**
1.  复制模板文件到目标位置：
    ```bash
    cd /var/named/
    cp -a named.localhost thinkmo.com.cn.zone
    ```
2.  编辑 `thinkmo.com.cn.zone` 文件：
    ```bash
    vim thinkmo.com.cn.zone
    ```
    文件内容修改如下：
    ```
    $TTL 1D
    @       IN SOA  @ root.thinkmo.com.cn. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
            NS      ns.thinkmo.com.cn.   ; 指定DNS服务器本身的主机名
    ns      A       192.168.201.103      ; 为DNS服务器主机名指定IP
    www     A       192.168.201.103      ; 将 www.thinkmo.com.cn 解析到IP
    bbs     A       192.168.201.103      ; 将 bbs.thinkmo.com.cn 解析到同一个IP
    ```
    **关键点**：域名末尾的 `.` 不能省略，它代表根域。

![](img/79905331b0173310c2d6a73c024d3395_8.png)

**第四步：启动服务并测试**
1.  启动DNS服务和HTTP服务（用于Web测试）：
    ```bash
    systemctl start named
    systemctl start httpd
    systemctl stop firewalld # 临时关闭防火墙方便测试
    ```
2.  在客户端（可以是另一台机器或本机）修改DNS服务器地址为 `192.168.201.103`。
3.  使用 `nslookup` 或 `dig` 命令测试：
    ```bash
    nslookup www.thinkmo.com.cn 192.168.201.103
    ```
    应能正确返回IP地址 `192.168.201.103`。

![](img/79905331b0173310c2d6a73c024d3395_10.png)

![](img/79905331b0173310c2d6a73c024d3395_12.png)

---

![](img/79905331b0173310c2d6a73c024d3395_14.png)

### 配置实战：反向解析

![](img/79905331b0173310c2d6a73c024d3395_16.png)

反向解析是根据IP查找域名。

**第一步：添加反向区域定义**
编辑 `/etc/named.rfc1912.zones`，添加：
```bash
zone "201.168.192.in-addr.arpa" IN { # 注意IP段要倒写，并加上 `in-addr.arpa`
    type master;
    file "192.168.201.arpa";          # 指定反向数据文件名
    allow-update { none; };
};
```

![](img/79905331b0173310c2d6a73c024d3395_18.png)

**第二步：创建并编辑反向数据文件**
1.  复制模板：
    ```bash
    cd /var/named/
    cp -a named.loopback 192.168.201.arpa
    ```
2.  编辑 `192.168.201.arpa` 文件：
    ```bash
    vim 192.168.201.arpa
    ```
    内容如下：
    ```
    $TTL 1D
    @       IN SOA  @ root.thinkmo.com.cn. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
            NS      ns.thinkmo.com.cn.
    103     PTR     ns.thinkmo.com.cn.    # 将 103 反向解析为 ns.thinkmo.com.cn
    103     PTR     www.thinkmo.com.cn.   # 将 103 反向解析为 www.thinkmo.com.cn
    102     PTR     blog.thinkmo.com.cn.  # 将 102 反向解析为 blog.thinkmo.com.cn
    ```

**第三步：重启服务并测试**
```bash
systemctl restart named
nslookup 192.168.201.103 192.168.201.103
```
应能返回 `www.thinkmo.com.cn` 和 `ns.thinkmo.com.cn`。

![](img/79905331b0173310c2d6a73c024d3395_20.png)

![](img/79905331b0173310c2d6a73c024d3395_22.png)

---

## 主从DNS同步简介

在实际生产中，为了高可用，通常会部署主从（Master-Slave）DNS服务器。配置非常简单：

1.  **在主服务器的区域配置** (`/etc/named.rfc1912.zones`) 中，`allow-update` 参数可以改为允许从服务器同步：
    ```bash
    allow-update { 192.168.201.104; }; # 允许从服务器IP同步
    ```
2.  **在从服务器的区域配置**中，类型改为 `slave`，并指定主服务器地址和数据文件位置：
    ```bash
    zone "thinkmo.com.cn" IN {
        type slave;                     # 类型为从服务器
        file "slaves/thinkmo.com.cn.zone"; # 文件通常放在 slaves/ 目录下
        masters { 192.168.201.103; };   # 指定主服务器IP
    };
    ```
配置完成后，从服务器会自动从主服务器同步区域数据文件，实现解析记录的备份和负载分担。

---

![](img/79905331b0173310c2d6a73c024d3395_24.png)

![](img/79905331b0173310c2d6a73c024d3395_26.png)

![](img/79905331b0173310c2d6a73c024d3395_28.png)

## 总结

![](img/79905331b0173310c2d6a73c024d3395_30.png)

![](img/79905331b0173310c2d6a73c024d3395_32.png)

![](img/79905331b0173310c2d6a73c024d3395_34.png)

![](img/79905331b0173310c2d6a73c024d3395_36.png)

本节课我们一起学习了DNS域名解析服务的核心知识。我们从DNS的**基本作用**（正向/反向解析）出发，探讨了其在**企业中的不同应用场景**，特别是自建DNS的必要性和高级的分离解析技术。然后，我们深入剖析了**DNS查询的完整原理**，理解了从本地缓存到根服务器的迭代过程。最后，我们通过实战，一步步在Linux上使用BIND软件**部署了具备正向和反向解析功能的DNS服务器**，并简要介绍了**主从同步**的配置思路。

![](img/79905331b0173310c2d6a73c024d3395_38.png)

![](img/79905331b0173310c2d6a73c024d3395_40.png)

![](img/79905331b0173310c2d6a73c024d3395_41.png)

![](img/79905331b0173310c2d6a73c024d3395_42.png)

![](img/79905331b0173310c2d6a73c024d3395_43.png)

![](img/79905331b0173310c2d6a73c024d3395_44.png)

![](img/79905331b0173310c2d6a73c024d3395_45.png)

![](img/79905331b0173310c2d6a73c024d3395_46.png)

![](img/79905331b0173310c2d6a73c024d3395_47.png)

![](img/79905331b0173310c2d6a73c024d3395_49.png)

掌握DNS是构建和维护任何网络服务的基础。建议你根据教程亲手搭建一遍环境，并尝试思考如何配置一个简单的从服务器，这将极大地巩固你的学习成果。