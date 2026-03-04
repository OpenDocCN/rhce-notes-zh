# Linux教程RHCE：P15：DHCP与邮件服务器

![](img/de594c69edd6075ca3c25d06e9cc7f0d_1.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_3.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_5.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_6.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_7.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_9.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_10.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_12.png)

在本节课中，我们将要学习两个重要的网络服务：DHCP（动态主机配置协议）和邮件服务器。我们将从DHCP的基本概念和配置开始，然后过渡到邮件服务器的搭建与使用。这两个服务虽然在实际的红帽RHCE考试中不涉及，但在日常运维工作中非常实用。

## 分离解析技术

![](img/de594c69edd6075ca3c25d06e9cc7f0d_14.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_15.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_16.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_18.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_19.png)

上一节我们介绍了DNS服务，本节中我们来看看DNS的一个高级应用——分离解析技术。

![](img/de594c69edd6075ca3c25d06e9cc7f0d_21.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_23.png)

分离解析技术可以让用户访问同一个域名时，得到不同的解析结果。例如，一个大型网站可能在国内和国外都部署了服务器。当中国用户访问时，自动定位到北京的机房；当外国用户访问时，则定位到美国的机房。这样可以降低主站服务器的压力，并加快数据传输速度。

其原理是根据访问者来源的IP地址网段不同，DNS服务器加载不同的区域数据文件，从而返回不同的IP地址。

以下是配置分离解析的关键步骤：

1.  **修改主配置文件**：编辑 `/etc/named.conf` 文件，定义访问控制列表（ACL）来匹配不同来源的网段，并指定对应的区域数据文件。
    ```bash
    acl "china" { 192.168.10.0/24; };
    acl "america" { 10.10.10.0/24; };
    
    view "china" {
        match-clients { "china"; };
        zone "linuxprobe.com" {
            type master;
            file "linuxprobe.com.china";
        };
    };
    
    view "america" {
        match-clients { "america"; };
        zone "linuxprobe.com" {
            type master;
            file "linuxprobe.com.america";
        };
    };
    ```
2.  **创建区域数据文件**：在 `/var/named/` 目录下，为不同视图创建对应的区域数据文件（如 `linuxprobe.com.china` 和 `linuxprobe.com.america`），并在其中为同一域名设置不同的A记录。
    ```bash
    # linuxprobe.com.china 文件内容示例
    @   IN  SOA     linuxprobe.com.   root.linuxprobe.com.  (
                                            2023120901
                                            1D
                                            1H
                                            1W
                                            3H )
            NS      ns.linuxprobe.com.
    ns  IN  A       192.168.10.10
    www IN  A       192.168.10.10
    ```
    ```bash
    # linuxprobe.com.america 文件内容示例
    @   IN  SOA     linuxprobe.com.   root.linuxprobe.com.  (
                                            2023120901
                                            1D
                                            1H
                                            1W
                                            3H )
            NS      ns.linuxprobe.com.
    ns  IN  A       10.10.10.10
    www IN  A       10.10.10.10
    ```
3.  **重启服务**：配置完成后，重启 `named` 服务并设置开机自启。
    ```bash
    systemctl restart named
    systemctl enable named
    ```

![](img/de594c69edd6075ca3c25d06e9cc7f0d_25.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_26.png)

这样，来自不同网段的客户端查询 `www.linuxprobe.com` 时，就会得到不同的IP地址。

![](img/de594c69edd6075ca3c25d06e9cc7f0d_28.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_30.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_32.png)

## DHCP服务

![](img/de594c69edd6075ca3c25d06e9cc7f0d_34.png)

接下来，我们进入第十四章，学习DHCP服务。DHCP协议可以自动为网络中的客户端分配IP地址、子网掩码、网关和DNS等网络配置信息，极大地简化了网络管理。

![](img/de594c69edd6075ca3c25d06e9cc7f0d_36.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_37.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_39.png)

### 核心概念

![](img/de594c69edd6075ca3c25d06e9cc7f0d_41.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_43.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_45.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_47.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_49.png)

在配置DHCP之前，需要理解几个核心概念：

![](img/de594c69edd6075ca3c25d06e9cc7f0d_51.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_53.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_54.png)

*   **作用域**：在DHCP服务器上定义的一个IP地址范围，声明了可供分配的地址资源池。
*   **地址池**：作用域内实际用于分配给客户端的IP地址范围。
*   **排除范围**：作用域内保留、不分配给客户端的IP地址范围。
    *   **关系**：`作用域 = 地址池 + 排除范围`
*   **租约**：客户端从DHCP服务器获取IP地址的使用权有效期。
    *   **默认租约时间**：客户端正常使用地址的默认有效期。
    *   **最大租约时间**：地址可被占用的最长时间，超时后服务器会回收地址。
*   **预约（IP-MAC绑定）**：将特定的IP地址固定分配给指定MAC地址的主机。

### 配置DHCP服务器

以下是配置DHCP服务器的基本流程：

1.  **安装服务**：
    ```bash
    yum install -y dhcp
    ```
2.  **编辑主配置文件**：DHCP的主配置文件是 `/etc/dhcp/dhcpd.conf`。初始文件多为注释，可以清空后自行配置。
    ```bash
    # 清空原配置
    > /etc/dhcp/dhcpd.conf
    # 编辑配置文件
    vim /etc/dhcp/dhcpd.conf
    ```
3.  **编写配置内容**：以下是一个基本的配置示例。
    ```bash
    # 全局参数
    ddns-update-style none; # 禁用动态DNS更新
    ignore client-updates; # 忽略客户端更新
    subnet 192.168.10.0 netmask 255.255.255.0 { # 声明作用域
        range 192.168.10.50 192.168.10.150; # 定义地址池
        option subnet-mask 255.255.255.0; # 分配给客户端的子网掩码
        option routers 192.168.10.1; # 分配给客户端的网关地址
        option domain-name-servers 192.168.10.10; # 分配给客户端的DNS服务器地址
        default-lease-time 21600; # 默认租约时间（秒），6小时
        max-lease-time 43200; # 最大租约时间（秒），12小时
    }
    ```
4.  **IP-MAC绑定（预约）**：在配置文件中添加 `host` 段落，实现固定IP分配。
    ```bash
    host boss { # 为这个预约起个名字，如boss
        hardware ethernet 00:0C:29:27:C6:12; # 客户端的MAC地址
        fixed-address 192.168.10.88; # 要绑定的固定IP地址
    }
    ```
5.  **启动并设置开机自启**：
    ```bash
    systemctl start dhcpd
    systemctl enable dhcpd
    ```
6.  **配置客户端**：将客户端的网络连接设置为“自动获取IP地址（DHCP）”，即可从服务器获得配置。

![](img/de594c69edd6075ca3c25d06e9cc7f0d_56.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_57.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_59.png)

## 邮件服务器

![](img/de594c69edd6075ca3c25d06e9cc7f0d_61.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_62.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_64.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_66.png)

最后，我们学习第十五章的邮件服务器。邮件系统允许异步通信，即使对方离线，邮件也会被保存并在上线后送达。

![](img/de594c69edd6075ca3c25d06e9cc7f0d_68.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_69.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_71.png)

### 邮件系统角色

一个完整的邮件系统通常涉及三个角色：

![](img/de594c69edd6075ca3c25d06e9cc7f0d_73.png)

![](img/de594c69edd6075ca3c25d06e9cc7f0d_75.png)

1.  **MUA**：邮件用户代理，用户用来收发邮件的客户端软件（如Outlook、Foxmail）。
2.  **MTA**：邮件传输代理，负责在不同邮件服务器之间转发邮件（如Postfix、Sendmail）。
3.  **MDA**：邮件投递代理，负责将接收到的邮件保存到用户邮箱的特定目录（如Dovecot）。

### 配置邮件服务器

本实验将配置一个基本的邮件系统，结合之前学过的DNS服务。

1.  **配置DNS解析**：首先需要为邮件域名配置MX记录。
    *   编辑DNS区域数据文件，添加如下记录：
        ```bash
        @       IN      MX  10  mail.linuxprobe.com.
        mail    IN      A       192.168.10.10
        ```
        这表示发往 `@linuxprobe.com` 的邮件，由 `mail.linuxprobe.com`（IP: 192.168.10.10）负责处理。
2.  **安装邮件服务**：
    ```bash
    yum install -y postfix dovecot
    ```
    *   `postfix`：作为MTA，负责发送和接收邮件。
    *   `dovecot`：作为MDA，负责投递和存储邮件，并允许客户端通过POP3/IMAP协议收取。
3.  **配置Postfix (MTA)**：编辑主配置文件 `/etc/postfix/main.cf`。
    ```bash
    myhostname = mail.linuxprobe.com # 设置主机名
    mydomain = linuxprobe.com # 设置域名
    myorigin = $mydomain # 设置发件地址后缀
    inet_interfaces = all # 监听所有网卡
    mydestination = $myhostname, localhost.$mydomain, $mydomain # 定义本机负责投递的域名
    ```
    重启服务：`systemctl restart postfix`
4.  **配置Dovecot (MDA)**：
    *   编辑 `/etc/dovecot/dovecot.conf`，启用协议并允许明文认证（仅实验环境）：
        ```bash
        protocols = imap pop3 lmtp
        disable_plaintext_auth = no
        ```
    *   编辑 `/etc/dovecot/conf.d/10-mail.conf`，定义邮件存储格式和路径（约第25行）：
        ```bash
        mail_location = mbox:~/mail:INBOX=/var/mail/%u
        ```
    *   需要为系统用户创建对应的邮件目录。例如，为用户 `xiaonan`：
        ```bash
        su - xiaonan
        mkdir -p mail/.imap/INBOX
        exit
        ```
    重启服务：`systemctl restart dovecot`
5.  **创建邮件用户**：邮件服务使用系统用户进行认证。需要创建系统用户并设置密码。
    ```bash
    useradd xiaonan
    passwd xiaonan
    ```
6.  **客户端测试**：在客户端（如Windows Outlook）中配置账户。
    *   邮箱地址：`xiaonan@linuxprobe.com`
    *   用户名：`xiaonan`
    *   密码：用户 `xiaonan` 的系统密码
    *   接收(POP3/IMAP)和发送(SMTP)服务器地址：`mail.linuxprobe.com` 或 `192.168.10.10`
    配置完成后，即可进行邮件的发送和接收测试。
7.  **邮件别名**：可以将发送给某个不存在的邮件地址的邮件，自动转发给真实存在的用户。编辑 `/etc/aliases` 文件：
    ```bash
    student: root
    ```
    这表示所有发送给 `student@linuxprobe.com` 的邮件，都会投递给 `root` 用户。执行 `newaliases` 命令使配置生效。

![](img/de594c69edd6075ca3c25d06e9cc7f0d_77.png)

## 总结

![](img/de594c69edd6075ca3c25d06e9cc7f0d_79.png)

本节课中我们一起学习了三个重要的服务。首先，我们深入了解了DNS的分离解析技术，它能根据用户来源提供不同的解析结果。接着，我们掌握了DHCP服务的原理与配置，学会了如何自动分配网络参数以及进行IP-MAC绑定。最后，我们搭建了一个基础的邮件服务器，理解了MUA、MTA、MDA的角色分工，并实践了邮件的收发和别名功能。这些服务是构建企业网络环境的基础，虽然部分内容不在当前RHCE考试范围，但对于提升实际运维能力至关重要。