# 红帽RHCE认证考试视频：P4：RHCE-4

## 概述
在本节课中，我们将要学习如何为服务的新端口配置SELinux端口标记，以及DNS服务的基本概念、配置和故障排除方法。课程内容分为三个主要部分：SELinux端口标记管理、DNS基础知识和DNS故障排查。

![](img/52d10344067754b62fca86820da9f169_1.png)

![](img/52d10344067754b62fca86820da9f169_3.png)

![](img/52d10344067754b62fca86820da9f169_5.png)

## 第一部分：SELinux端口标记管理 🔐

### 1.1：SELinux端口标记简介
SELinux端口标记的主要功能是为服务使用的端口添加安全标签。每个服务通常有默认的监听端口。当服务需要使用非默认端口时，必须为这个新端口添加相应的SELinux端口标记，前提是SELinux处于开启状态。

例如，HTTP协议默认使用TCP的80端口。如果改为使用TCP的2000端口，就需要为TCP 2000端口添加一个SELinux端口标记，服务才能正常监听。

![](img/52d10344067754b62fca86820da9f169_7.png)

![](img/52d10344067754b62fca86820da9f169_9.png)

![](img/52d10344067754b62fca86820da9f169_10.png)

### 1.2：查看端口标签
要查看系统中已定义的SELinux端口标签，可以使用以下命令：
```bash
semanage port -l
```
这条命令会列出所有端口及其对应的SELinux标签。

### 1.3：管理端口标签
管理端口标签主要包括添加、删除和修改操作。以下是相关的命令格式：

![](img/52d10344067754b62fca86820da9f169_12.png)

![](img/52d10344067754b62fca86820da9f169_13.png)

![](img/52d10344067754b62fca86820da9f169_14.png)

![](img/52d10344067754b62fca86820da9f169_15.png)

![](img/52d10344067754b62fca86820da9f169_16.png)

![](img/52d10344067754b62fca86820da9f169_18.png)

*   **添加端口标签**：使用 `semanage port -a` 命令。
    *   命令格式：`semanage port -a -t <端口标签名> -p <协议> <端口号>`
    *   示例：允许服务监听TCP的71端口。
        ```bash
        semanage port -a -t http_port_t -p tcp 71
        ```

![](img/52d10344067754b62fca86820da9f169_20.png)

*   **删除端口标签**：使用 `semanage port -d` 命令。
    *   命令格式：`semanage port -d -t <端口标签名> -p <协议> <端口号>`
    *   示例：删除TCP 71端口的标签。
        ```bash
        semanage port -d -t http_port_t -p tcp 71
        ```

*   **修改端口标签**：使用 `semanage port -m` 命令。
    *   命令格式：`semanage port -m -t <新端口标签名> -p <协议> <端口号>`
    *   示例：将TCP 71端口的标签修改为 `http_port_t`。
        ```bash
        semanage port -m -t http_port_t -p tcp 71
        ```

![](img/52d10344067754b62fca86820da9f169_22.png)

![](img/52d10344067754b62fca86820da9f169_23.png)

![](img/52d10344067754b62fca86820da9f169_25.png)

![](img/52d10344067754b62fca86820da9f169_26.png)

![](img/52d10344067754b62fca86820da9f169_27.png)

![](img/52d10344067754b62fca86820da9f169_28.png)

上一节我们介绍了SELinux端口标记的管理命令，本节中我们通过实验来巩固这些操作。

### 1.4：实验14 - 管理SELinux端口标记
本实验的目标是配置Apache HTTP服务使用非标准端口（TCP 82），并为其添加SELinux端口标记。

![](img/52d10344067754b62fca86820da9f169_30.png)

![](img/52d10344067754b62fca86820da9f169_31.png)

![](img/52d10344067754b62fca86820da9f169_32.png)

**实验步骤：**

1.  **准备环境**：登录 `server` 主机，运行 `lab selinux-port setup` 脚本初始化环境。该脚本会安装并配置Apache。
2.  **尝试启动服务**：使用 `systemctl restart httpd` 启动Apache服务，会发现启动失败。
3.  **排查原因**：使用 `systemctl status -l httpd` 查看详细日志，会发现错误信息为“无法绑定到端口82：权限被拒绝”。
4.  **分析SELinux日志**：检查 `/var/log/audit/audit.log` 文件，日志会提示需要为端口82设置SELinux类型，并建议使用 `http_port_t` 标签。
5.  **添加端口标签**：执行命令为TCP 82端口添加标签。
    ```bash
    semanage port -a -t http_port_t -p tcp 82
    ```
6.  **再次启动服务**：执行 `systemctl restart httpd`，此时服务应能成功启动。
7.  **配置防火墙**：为了让客户端能够访问，需要在防火墙中开放TCP 82端口。
    ```bash
    firewall-cmd --permanent --add-port=82/tcp
    firewall-cmd --reload
    ```
8.  **测试访问**：在客户端使用 `curl http://server0.example.com:82` 命令测试，应能成功访问网页。
9.  **验证实验**：运行 `lab selinux-port grade` 脚本检查实验是否成功。

### 1.5：实验15 - 网络端口安全性
本实验是上一个实验的扩展，综合了SELinux端口标记和防火墙配置，目标是为SSH服务添加一个额外的监听端口（TCP 999）。

**实验步骤：**

1.  **准备环境**：登录 `server` 主机，运行 `lab custom-port setup` 脚本初始化环境。
2.  **尝试启动SSH服务**：使用 `systemctl restart sshd` 启动服务，使用 `systemctl status -l sshd` 查看状态，会发现绑定999端口失败。
3.  **分析SELinux日志**：检查 `/var/log/audit/audit.log`，日志会提示需要为端口999设置SELinux类型，建议使用 `ssh_port_t` 标签。
4.  **添加端口标签**：执行命令为TCP 999端口添加标签。
    ```bash
    semanage port -a -t ssh_port_t -p tcp 999
    ```
5.  **重启服务**：执行 `systemctl restart sshd`，服务应成功监听22和999端口。
6.  **配置防火墙区域和规则**：
    *   将来自 `172.25.0.0/24` 网段的流量默认路由到 `work` 区域。
        ```bash
        firewall-cmd --permanent --zone=work --add-source=172.25.0.0/24
        ```
    *   在 `work` 区域开放TCP 999端口。
        ```bash
        firewall-cmd --permanent --zone=work --add-port=999/tcp
        firewall-cmd --reload
        ```
7.  **验证实验**：运行 `lab custom-ssh grade` 脚本，检查是否可以通过22和999端口成功SSH连接到服务器。

## 第二部分：DNS基础知识 🌐
上一部分我们完成了服务端口的SELinux和防火墙配置，本节中我们来看看网络基础服务——DNS。

### 2.1：域名系统（DNS）概述
计算机通过IP地址通信，但IP地址难以记忆。域名系统（DNS）的作用就是将人类可读的域名（如 `www.example.com`）解析为机器可读的IP地址。

![](img/52d10344067754b62fca86820da9f169_34.png)

![](img/52d10344067754b62fca86820da9f169_35.png)

一个完整的域名（FQDN）从右到左结构如下：
*   **根域**：最右侧的 `.`（通常省略）。
*   **顶级域**：如 `.com`、`.org`、`.cn`。
*   **二级域**：如 `example`。
*   **主机名**：如 `www`。

![](img/52d10344067754b62fca86820da9f169_37.png)

![](img/52d10344067754b62fca86820da9f169_38.png)

![](img/52d10344067754b62fca86820da9f169_40.png)

![](img/52d10344067754b62fca86820da9f169_41.png)

![](img/52d10344067754b62fca86820da9f169_43.png)

### 2.2：DNS查询方式
DNS查询主要有两种方式：

*   **迭代查询**：DNS服务器收到查询请求后，若本地无记录，则返回一个可能知道答案的其他DNS服务器地址给客户端，由客户端继续查询。
*   **递归查询**：DNS服务器收到查询请求后，若本地无记录，则会代替客户端向其他DNS服务器发起查询，直到获得最终结果，再返回给客户端。

### 2.3：DNS记录类型
DNS数据库中的条目称为资源记录，常见类型包括：

![](img/52d10344067754b62fca86820da9f169_45.png)

![](img/52d10344067754b62fca86820da9f169_47.png)

![](img/52d10344067754b62fca86820da9f169_49.png)

*   **A记录**：将主机名映射到IPv4地址（正向解析）。
*   **AAAA记录**：将主机名映射到IPv6地址。
*   **PTR记录**：将IP地址映射到主机名（反向解析）。
*   **CNAME记录**：为主机名设置别名。
*   **MX记录**：指定域名的邮件服务器。
*   **NS记录**：指定域名的权威DNS服务器。

### 2.4：DNS服务器类型
以下是几种主要的DNS服务器类型：

![](img/52d10344067754b62fca86820da9f169_51.png)

*   **主DNS服务器**：存储原始的、权威的区域数据文件。
*   **辅DNS服务器**：从主DNS服务器同步区域数据，提供冗余和负载均衡。
*   **转发DNS服务器**：将客户端的查询请求转发给其他DNS服务器，并将结果返回给客户端。
*   **缓存DNS服务器**：不管理任何区域，仅缓存来自其他服务器的查询结果以提高效率。

![](img/52d10344067754b62fca86820da9f169_53.png)

## 第三部分：配置与故障排除 🛠️
了解了DNS的基础知识后，本节中我们通过实验来配置一台缓存DNS服务器，并学习常见的DNS故障排除方法。

### 3.1：实验16 - 配置缓存DNS服务器
本实验使用 `unbound` 软件将 `server` 主机配置为一台缓存DNS服务器，其上游转发至 `classroom` 主机。

**实验步骤：**

![](img/52d10344067754b62fca86820da9f169_55.png)

![](img/52d10344067754b62fca86820da9f169_57.png)

![](img/52d10344067754b62fca86820da9f169_59.png)

![](img/52d10344067754b62fca86820da9f169_61.png)

1.  **安装软件**：在 `server` 上安装 `unbound`。
    ```bash
    yum install -y unbound
    ```
2.  **编辑配置文件**：修改 `/etc/unbound/unbound.conf`。
    *   指定监听接口：`interface: 172.25.0.11`
    *   允许查询的客户端网段：`access-control: 172.25.0.0/24 allow`
    *   设置转发规则，将所有查询转发至 `classroom` (172.25.254.254)：
        ```
        forward-zone:
            name: "."
            forward-addr: 172.25.254.254
        ```
3.  **检查配置并启动服务**：
    ```bash
    unbound-checkconf # 检查语法
    systemctl enable --now unbound # 设置开机自启并启动
    ```
4.  **配置防火墙**：开放DNS服务。
    ```bash
    firewall-cmd --permanent --add-service=dns
    firewall-cmd --reload
    ```
5.  **测试解析**：在客户端 `desktop` 上，使用 `dig` 命令指定 `server0` 为DNS服务器进行查询，应能成功解析域名。
6.  **管理缓存**：在 `server` 上可以使用 `unbound-control` 命令查看或清除缓存。
    ```bash
    unbound-control dump_cache # 查看缓存
    unbound-control flush example.com # 清除特定域名缓存
    ```

### 3.2：DNS故障排除
DNS故障可能由多种原因引起，以下是一些常见场景及排查命令。

**常见故障点：**

![](img/52d10344067754b62fca86820da9f169_63.png)

![](img/52d10344067754b62fca86820da9f169_64.png)

1.  **解析顺序**：系统默认先查询 `/etc/hosts` 文件，再查询DNS。顺序由 `/etc/nsswitch.conf` 文件中的 `hosts` 行定义。
2.  **DNS服务器配置**：客户端使用的DNS服务器地址在 `/etc/resolv.conf` 文件中定义。
3.  **DNS响应代码**：
    *   `SERVFAIL`：服务器内部错误或无法通信。
    *   `NXDOMAIN`：查询的域名不存在。
    *   `REFUSED`：服务器拒绝查询（可能由于防火墙或SELinux限制）。

![](img/52d10344067754b62fca86820da9f169_66.png)

![](img/52d10344067754b62fca86820da9f169_67.png)

![](img/52d10344067754b62fca86820da9f169_69.png)

![](img/52d10344067754b62fca86820da9f169_71.png)

![](img/52d10344067754b62fca86820da9f169_72.png)

![](img/52d10344067754b62fca86820da9f169_73.png)

![](img/52d10344067754b62fca86820da9f169_75.png)

**排查命令：**
*   `dig @<DNS服务器IP> <域名>`：指定DNS服务器进行查询，查看详细响应。
*   `nslookup <域名>`：交互式查询工具。
*   `systemctl status network` 或 `nmcli`：检查网络服务和DNS配置获取状态。

![](img/52d10344067754b62fca86820da9f169_77.png)

![](img/52d10344067754b62fca86820da9f169_79.png)

### 3.3：实验17 & 18 - DNS故障排除
这两个实验模拟了DNS解析失败的场景，引导我们一步步排查并解决问题。

**实验17核心问题**：客户端 `/etc/resolv.conf` 中的DNS服务器地址错误（通过DHCP获取错误）。
**解决方案**：重启 `NetworkManager` 服务以重新获取正确的DNS配置。
```bash
systemctl restart NetworkManager
```

**实验18核心问题**：`unbound` 缓存DNS服务器配置不当，导致无法为客户端提供服务。
**分步解决：**
1.  **问题现象**：客户端解析失败，服务器本地解析成功。
2.  **排查1**：检查发现 `unbound` 只监听 `127.0.0.1`。修改配置文件，监听所有接口：`interface: 0.0.0.0`。
3.  **排查2**：修改后客户端仍收到 `REFUSED` 响应。检查发现配置未允许客户端网段查询。添加配置：`access-control: 172.25.0.0/24 allow`。
4.  **重启服务**：每次修改配置后，需执行 `systemctl restart unbound` 使配置生效。
5.  **最终测试**：客户端和服务器本地解析均应成功。

![](img/52d10344067754b62fca86820da9f169_81.png)

![](img/52d10344067754b62fca86820da9f169_83.png)

## 总结
本节课中我们一起学习了两个核心主题。首先，我们掌握了如何为变更端口的服务配置SELinux端口标记和防火墙规则，以确保服务的安全访问。其次，我们深入了解了DNS的工作原理、记录类型、服务器角色，并通过实践配置了一台缓存DNS服务器，同时学习了系统化的DNS故障诊断与排除流程。这些技能对于管理安全的、可靠的网络服务至关重要。