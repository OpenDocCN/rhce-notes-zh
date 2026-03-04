# Linux运维／红帽RHCE零基础入门之DNS域名解析服务的基本应用与主从技术：P1：DNS基础与正向反向解析部署

![](img/33ccb0c499012c5e87b812f3c2f38c7f_1.png)

## 概述

在本节课中，我们将要学习DNS域名解析服务的基本概念、工作原理，并动手实践如何在Linux系统上部署一个DNS服务器，实现正向解析和反向解析。DNS是企业基础服务中较为复杂但至关重要的部分，理解其原理和部署方法是运维工程师的必备技能。

## DNS服务简介与作用

上一节我们介绍了Linux运维的学习路径，本节中我们来看看DNS服务。DNS，即域名系统，其核心作用是将人类易于记忆的域名转换为计算机用于网络通信的IP地址。

这个过程主要分为两个方向：
*   **正向解析**：将域名转换为IP地址。例如，访问 `www.example.com` 时，DNS会将其解析为对应的服务器IP，如 `192.168.1.100`。
*   **反向解析**：将IP地址转换为域名。例如，已知IP地址 `192.168.1.100`，通过反向解析查询其绑定的域名。

**公式**：`域名 <--(DNS解析)--> IP地址`

![](img/33ccb0c499012c5e87b812f3c2f38c7f_3.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_4.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_6.png)

这种对应关系是多对多的，一个域名可以对应多个IP（实现负载均衡），一个IP也可以绑定多个域名。DNS极大地简化了网络访问，避免了记忆复杂数字IP地址的麻烦。

## DNS解析原理

在深入了解部署之前，我们需要理解当你在浏览器输入一个网址后，系统是如何一步步找到目标服务器的。

以下是DNS查询的基本流程：
1.  **本地Hosts文件**：系统首先检查本地的 `hosts` 文件（Windows在 `C:\Windows\System32\drivers\etc\hosts`， Linux在 `/etc/hosts`）。如果文件中存在该域名的记录，则直接使用对应的IP地址，查询结束。
2.  **本地DNS解析器**：如果hosts文件没有记录，系统会查询网卡配置中指定的“本地DNS服务器”地址（例如 `8.8.8.8` 或 `114.114.114.114`）。
3.  **递归/迭代查询**：本地DNS服务器如果没有缓存该域名的记录，则会代表客户端向全球的DNS根服务器发起查询。查询过程从根服务器开始，逐级向下（例如 `.com` 域服务器 -> `example.com` 域服务器），直到找到负责该域名的权威DNS服务器，并获取最终的IP地址。
4.  **返回结果并缓存**：权威DNS服务器将IP地址返回给本地DNS服务器，本地DNS服务器一方面将结果返回给客户端，另一方面会缓存此记录，以便后续相同的查询能快速响应。

## 部署DNS服务器：BIND

理解了原理后，我们开始动手部署。在Linux上，最常用的DNS服务器软件是 **BIND**。我们将使用其增强版本 `bind-chroot`，它提供了一个“牢笼”机制，将BIND服务限制在特定目录下运行，以增强安全性。

部署一个基础的DNS服务器需要配置三个核心文件，它们协同工作：
1.  **主配置文件** (`/etc/named.conf`)：定义DNS服务的基本行为，如监听端口、允许查询的客户端、是否开启转发等。
2.  **区域配置文件** (`/etc/named.rfc1912.zones`)：声明本DNS服务器负责解析哪些“区域”（即域名），以及这些区域对应的数据文件存放在哪里。
3.  **区域数据文件** (`/var/named/` 目录下)：详细记录某个域名下主机名与IP地址的具体对应关系，是解析信息的最终来源。

### 实验环境准备

我们假设有两台服务器：
*   **Master**: `192.168.201.103` (主DNS服务器)
*   **Slave**: `192.168.201.104` (从DNS服务器，本节先搭建主服务器)

首先在主服务器上安装BIND软件包：
```bash
yum install bind-chroot -y
```

### 配置正向解析

正向解析是我们最常用的功能，即通过域名查找IP。

**第一步：修改主配置文件**

编辑 `/etc/named.conf`：
*   将 `listen-on port 53 { 127.0.0.1; };` 改为 `listen-on port 53 { any; };`，允许监听所有IP的53端口请求。
*   将 `allow-query { localhost; };` 改为 `allow-query { any; };`，允许所有客户端查询。

**第二步：定义正向解析区域**

编辑 `/etc/named.rfc1912.zones`，在文件末尾添加以下内容，声明我们要解析的域 `thinkmo.com.cn`：
```
zone "thinkmo.com.cn" IN {
    type master; // 类型为主服务器
    file "thinkmo.com.cn.zone"; // 区域数据文件名
    allow-update { none; }; // 不允许动态更新
};
```

**第三步：创建并编辑正向区域数据文件**

1.  进入区域数据文件目录并复制模板：
    ```bash
    cd /var/named
    cp -a named.localhost thinkmo.com.cn.zone
    ```
    `-a` 参数保留文件的所有属性，包括所属组，这对BIND服务读取文件至关重要。

2.  编辑 `thinkmo.com.cn.zone` 文件：
    ```
    $TTL 1D
    @       IN SOA  ns.thinkmo.com.cn. root.thinkmo.com.cn. (
                                            0       ; serial
                                            1D      ; refresh
                                            1H      ; retry
                                            1W      ; expire
                                            3H )    ; minimum
            NS      ns.thinkmo.com.cn.
    ns      A       192.168.201.103
    www     A       192.168.201.103
    bbs     A       192.168.201.103
    ```
    *   **SOA记录**：起始授权机构记录，每个区域数据文件有且仅有一条，且必须在开头。定义了该区域的全局参数和管理员邮箱（`root.thinkmo.com.cn.` 中的 `@` 用 `.` 代替）。
    *   **NS记录**：域名服务器记录，指明该域名的DNS服务器是谁。
    *   **A记录**：地址记录，将主机名映射到IPv4地址。
    *   **注意**：所有完整的域名（FQDN）末尾必须有一个点 `.`，如 `ns.thinkmo.com.cn.`。

![](img/33ccb0c499012c5e87b812f3c2f38c7f_8.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_10.png)

**第四步：启动服务并测试**

![](img/33ccb0c499012c5e87b812f3c2f38c7f_12.png)

1.  启动（或重启）named服务并设置开机自启：
    ```bash
    systemctl start named
    systemctl enable named
    ```
2.  关闭防火墙或放行53端口：
    ```bash
    systemctl stop firewalld
    # 或
    firewall-cmd --permanent --add-port=53/tcp
    firewall-cmd --permanent --add-port=53/udp
    firewall-cmd --reload
    ```
3.  修改客户端网络配置，将其DNS服务器地址指向 `192.168.201.103`。
4.  使用 `nslookup` 或 `dig` 命令测试：
    ```bash
    nslookup www.thinkmo.com.cn 192.168.201.103
    ```
    应能正确返回IP地址 `192.168.201.103`。

![](img/33ccb0c499012c5e87b812f3c2f38c7f_14.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_16.png)

### 配置反向解析

反向解析是根据IP地址查询对应的域名。

![](img/33ccb0c499012c5e87b812f3c2f38c7f_18.png)

**第一步：定义反向解析区域**

![](img/33ccb0c499012c5e87b812f3c2f38c7f_20.png)

再次编辑 `/etc/named.rfc1912.zones`，添加反向区域声明。这里我们对 `192.168.201` 这个网段进行反向解析：
```
zone "201.168.192.in-addr.arpa" IN {
    type master;
    file "201.168.192.arpa";
    allow-update { none; };
};
```
反向区域的名称格式是：IP网络地址倒序 + `.in-addr.arpa`。

![](img/33ccb0c499012c5e87b812f3c2f38c7f_22.png)

**第二步：创建并编辑反向区域数据文件**

1.  复制模板：
    ```bash
    cd /var/named
    cp -a named.loopback 201.168.192.arpa
    ```
2.  编辑 `201.168.192.arpa` 文件：
    ```
    $TTL 1D
    @       IN SOA  ns.thinkmo.com.cn. root.thinkmo.com.cn. (
                                            0       ; serial
                                            1D      ; refresh
                                            1H      ; retry
                                            1W      ; expire
                                            3H )    ; minimum
            NS      ns.thinkmo.com.cn.
    103     PTR     ns.thinkmo.com.cn.
    103     PTR     www.thinkmo.com.cn.
    103     PTR     bbs.thinkmo.com.cn.
    102     PTR     blog.thinkmo.com.cn.
    ```
    *   **PTR记录**：指针记录，用于反向解析，将IP地址映射到域名。
    *   记录中的 `103` 和 `102` 指的是IP地址的最后一位主机号。

**第三步：重启服务并测试**

1.  重启named服务以加载新配置：
    ```bash
    systemctl restart named
    ```
2.  使用 `nslookup` 测试反向解析：
    ```bash
    nslookup 192.168.201.103 192.168.201.103
    ```
    应能返回 `www.thinkmo.com.cn` 和 `bbs.thinkmo.com.cn`。

![](img/33ccb0c499012c5e87b812f3c2f38c7f_24.png)

## 总结

![](img/33ccb0c499012c5e87b812f3c2f38c7f_26.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_28.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_30.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_32.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_34.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_36.png)

本节课中我们一起学习了DNS服务的基础知识。我们首先了解了DNS正向解析和反向解析的作用与原理，明白了域名与IP地址转换的整个过程。接着，我们重点实践了在CentOS/RHEL系统上使用BIND软件部署DNS服务器，通过配置主配置文件、区域配置文件以及正/反向区域数据文件，成功实现了对指定域名的正向解析和反向解析功能。

![](img/33ccb0c499012c5e87b812f3c2f38c7f_38.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_40.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_41.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_42.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_43.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_44.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_45.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_46.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_47.png)

![](img/33ccb0c499012c5e87b812f3c2f38c7f_49.png)

掌握这些基础是后续学习DNS主从同步、视图（分离解析）、缓存DNS等高级技术的前提。DNS服务虽然配置条目较多，但逻辑清晰，按照“定义区域 -> 编写数据”的步骤，就能逐步构建起可用的域名解析系统。