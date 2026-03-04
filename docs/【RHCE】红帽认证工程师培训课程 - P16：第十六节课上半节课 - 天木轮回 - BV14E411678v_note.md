# RHCE红帽认证工程师培训课程：P16：第十六节课上半节课

![](img/5418f9dd632c07f0dbd0bc91606183e9_1.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_3.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_4.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_5.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_7.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_8.png)

## 概述

![](img/5418f9dd632c07f0dbd0bc91606183e9_10.png)

在本节课中，我们将学习三个核心的网络服务：DHCP、邮件服务和OpenLDAP。DHCP服务用于自动分配网络配置信息；邮件服务（Postfix和Dovecot）用于实现电子邮件的收发；OpenLDAP则是一个用于集中管理用户账户信息的目录服务。我们将从原理讲解开始，然后进行实战配置，确保大家能够理解并掌握这些服务的基本部署方法。

![](img/5418f9dd632c07f0dbd0bc91606183e9_12.png)

---

## DHCP服务配置 🖧

![](img/5418f9dd632c07f0dbd0bc91606183e9_14.png)

上一节我们介绍了课程的整体安排，本节中我们来看看第一个服务——DHCP。

![](img/5418f9dd632c07f0dbd0bc91606183e9_16.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_18.png)

DHCP（动态主机配置协议）服务的主要功能是为网络中的客户端自动分配IP地址、子网掩码、网关、DNS服务器等网络配置信息。这对于管理拥有大量主机的网络环境至关重要，可以避免手动配置的繁琐和错误。

下图展示了DHCP服务的基本工作模式：多台客户端通过网线连接到服务器，由DHCP服务器集中为它们分配网络配置信息。

![](img/5418f9dd632c07f0dbd0bc91606183e9_1.png)

### 核心概念与术语

![](img/5418f9dd632c07f0dbd0bc91606183e9_20.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_21.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_22.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_23.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_24.png)

在配置DHCP之前，需要理解几个关键术语：

![](img/5418f9dd632c07f0dbd0bc91606183e9_26.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_27.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_28.png)

*   **作用域**：声明DHCP服务可管理的IP地址范围，这是一个较大的网段。
*   **排除范围**：在作用域内，指定不分配给客户端的IP地址范围。
*   **地址池**：在作用域内，实际分配给客户端的IP地址范围。**地址池 = 作用域 - 排除范围**。
*   **预约**：将特定的IP地址与客户端的MAC地址进行绑定，确保该客户端总是获得同一个IP地址。
*   **租约**：客户端从DHCP服务器获取IP地址的使用期限。分为**默认租约时间**和**最大租约时间**。当租约到期时，服务器会尝试回收IP地址以供其他客户端使用。

![](img/5418f9dd632c07f0dbd0bc91606183e9_30.png)

### 实战配置DHCP服务器

![](img/5418f9dd632c07f0dbd0bc91606183e9_32.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_33.png)

以下是配置DHCP服务器的具体步骤。

![](img/5418f9dd632c07f0dbd0bc91606183e9_35.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_36.png)

1.  **安装服务**：首先安装DHCP服务软件包。
    ```bash
    yum install -y dhcp
    ```
2.  **编辑主配置文件**：DHCP的主配置文件是 `/etc/dhcp/dhcpd.conf`。初始文件多为注释，我们可以清空后自行编写。
    ```bash
    vim /etc/dhcp/dhcpd.conf
    ```
    以下是配置文件的核心内容示例：
    ```
    # 忽略客户端更新
    ignore client-updates;
    # 定义作用域（网段）
    subnet 192.168.10.0 netmask 255.255.255.0 {
        # 定义地址池（实际分配范围）
        range 192.168.10.30 192.168.10.100;
        # 为客户端分配的默认网关
        option routers 192.168.10.1;
        # 为客户端分配的DNS服务器地址
        option domain-name-servers 192.168.10.1;
        # 默认租约时间（秒），6小时
        default-lease-time 21600;
        # 最大租约时间（秒），12小时
        max-lease-time 43200;
    }
    ```
3.  **关闭冲突服务并启动**：确保虚拟网络编辑器中的DHCP功能已关闭，然后启动服务并加入开机自启。
    ```bash
    systemctl start dhcpd
    systemctl enable dhcpd
    ```
4.  **客户端测试**：将客户端（如Windows）的网络适配器设置为“自动获得IP地址”，即可从DHCP服务器获取到配置的IP地址。

![](img/5418f9dd632c07f0dbd0bc91606183e9_38.png)

### 配置IP地址预约（绑定）

![](img/5418f9dd632c07f0dbd0bc91606183e9_40.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_42.png)

如果需要为特定设备（如老板的电脑）固定分配一个IP地址（例如192.168.10.88），可以进行MAC地址绑定。

1.  **获取客户端MAC地址**：在客户端使用 `ipconfig /all` (Windows) 或 `ifconfig` (Linux) 查看，或查看DHCP服务器的日志文件 `/var/log/messages`。
2.  **在配置文件中添加预约**：在 `/etc/dhcp/dhcpd.conf` 的作用域内添加以下配置（注意MAC地址格式）：
    ```
    host laoban-pc {
        hardware ethernet 00:0C:29:XX:XX:XX; # 客户端的MAC地址
        fixed-address 192.168.10.88; # 要绑定的固定IP
    }
    ```
3.  **重启服务**：重启DHCP服务使配置生效。
    ```bash
    systemctl restart dhcpd
    ```
    此后，该MAC地址的客户端将总是获得192.168.10.88这个IP地址。

---

## 邮件服务配置 📧

上一节我们配置了自动分配IP的DHCP服务，本节中我们来看看如何搭建一个简单的邮件系统。

邮件服务允许用户在不同时间、不同地点异步收发信息。一个完整的邮件系统通常包含三个角色：
*   **MUA**：邮件用户代理，用户用来收发邮件的客户端软件（如Outlook, Foxmail）。
*   **MTA**：邮件传输代理，负责在不同邮件服务器之间转发邮件（如Postfix）。
*   **MDA**：邮件投递代理，负责将接收到的邮件保存到用户的邮箱目录中（如Dovecot）。

### 配置DNS解析

![](img/5418f9dd632c07f0dbd0bc91606183e9_44.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_45.png)

邮件服务依赖DNS的MX记录来定位邮件服务器。我们首先配置一个简单的DNS服务。

![](img/5418f9dd632c07f0dbd0bc91606183e9_47.png)

1.  **安装并配置BIND（DNS服务）**：
    ```bash
    yum install -y bind
    vim /etc/named.conf
    ```
    在 `options` 部分确保监听所有IP并允许查询：
    ```
    listen-on port 53 { any; };
    allow-query     { any; };
    ```
    在文件末尾添加区域声明：
    ```
    zone "linuxprobe.com" IN {
        type master;
        file "linuxprobe.com.zone";
        allow-update { none; };
    };
    ```
2.  **创建区域数据文件**：
    ```bash
    vim /var/named/linuxprobe.com.zone
    ```
    文件内容如下：
    ```
    $TTL 1D
    @       IN SOA  ns.linuxprobe.com.  root.linuxprobe.com. (
                                            0       ; serial
                                            1D      ; refresh
                                            1H      ; retry
                                            1W      ; expire
                                            3H )    ; minimum
            NS      ns.linuxprobe.com.
    ns      A       192.168.10.10
    @       MX 10   mail.linuxprobe.com.
    mail    A       192.168.10.10
    ```
3.  **启动DNS服务**：
    ```bash
    systemctl start named
    systemctl enable named
    ```

![](img/5418f9dd632c07f0dbd0bc91606183e9_49.png)

### 配置Postfix（MTA）

Postfix是Linux下默认的MTA服务，负责发送邮件。

![](img/5418f9dd632c07f0dbd0bc91606183e9_51.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_53.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_54.png)

1.  **编辑主配置文件**：
    ```bash
    vim /etc/postfix/main.cf
    ```
    修改以下关键参数：
    ```
    myhostname = mail.linuxprobe.com
    mydomain = linuxprobe.com
    myorigin = $mydomain
    inet_interfaces = all
    mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
    ```
2.  **重启服务**：
    ```bash
    systemctl restart postfix
    systemctl enable postfix
    ```

![](img/5418f9dd632c07f0dbd0bc91606183e9_56.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_58.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_60.png)

### 配置Dovecot（MDA）

Dovecot服务负责接收邮件并将其投递到用户的邮箱。

1.  **安装并配置Dovecot**：
    ```bash
    yum install -y dovecot
    vim /etc/dovecot/dovecot.conf
    ```
    启用IMAP协议并允许明文认证：
    ```
    protocols = imap
    disable_plaintext_auth = no
    ```
    编辑邮件存储配置：
    ```bash
    vim /etc/dovecot/conf.d/10-mail.conf
    ```
    确保邮件存储格式正确（注意隐藏目录 `.Maildir`）：
    ```
    mail_location = maildir:~/Maildir
    ```
2.  **创建系统用户和邮箱目录**：
    ```bash
    useradd zhuzhu
    passwd zhuzhu # 设置密码
    su - zhuzhu
    mkdir -p ~/Maildir/.imap/INBOX
    exit
    ```
3.  **启动Dovecot服务**：
    ```bash
    systemctl start dovecot
    systemctl enable dovecot
    ```

![](img/5418f9dd632c07f0dbd0bc91606183e9_62.png)

### 客户端测试

![](img/5418f9dd632c07f0dbd0bc91606183e9_64.png)

在客户端（如Windows Outlook）中配置邮件账户：
*   邮箱地址：`zhuzhu@linuxprobe.com`
*   接收服务器类型：IMAP
*   接收/发送服务器地址：`mail.linuxprobe.com` (或服务器IP `192.168.10.10`)
*   用户名：`zhuzhu`
*   密码：你为zhuzhu用户设置的密码

![](img/5418f9dd632c07f0dbd0bc91606183e9_66.png)

配置完成后，即可在服务器和客户端之间互相发送测试邮件。

### 配置邮件别名

邮件别名允许将一个收件地址的邮件自动转发给另一个用户。例如，将所有发送给 `xiaoguo@linuxprobe.com` 的邮件都投递给 `root` 用户。

编辑别名配置文件：
```bash
vim /etc/aliases
```
添加一行：
```
xiaoguo: root
```
更新别名数据库：
```bash
newaliases
```
现在，发送给 `xiaoguo@linuxprobe.com` 的邮件将会出现在 `root` 用户的邮箱中。

---

![](img/5418f9dd632c07f0dbd0bc91606183e9_68.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_70.png)

## OpenLDAP目录服务简介 📁

![](img/5418f9dd632c07f0dbd0bc91606183e9_72.png)

上一节我们完成了邮件服务的搭建，本节中我们简要了解一下更高级的OpenLDAP服务。

OpenLDAP是一个开源的轻量级目录访问协议实现。它常用于集中存储和管理用户账户、密码等结构化信息，实现单点登录和统一身份认证。其数据特点是**读取频繁、修改较少、数据条目短小**，类似于一个电话簿。

### 核心概念

*   **目录信息树**：OpenLDAP中的数据以树状结构组织，类似于文件系统。
*   **条目**：DIT中的每个节点称为一个条目，代表一个对象（如一个用户）。
*   **DN**：唯一标识名，用于在DIT中唯一标识一个条目。例如：`uid=zhangsan,ou=People,dc=linuxprobe,dc=com`。
*   **属性**：条目包含的键值对数据，如 `uid=zhangsan`, `cn=张三`。

### OpenLDAP的基本工作流程

![](img/5418f9dd632c07f0dbd0bc91606183e9_74.png)

1.  部署一台OpenLDAP服务器，在其中创建所有用户账户。
2.  网络中的其他Linux客户端配置为使用OpenLDAP进行身份验证。
3.  用户在任何一台客户端上尝试登录时，客户端会向OpenLDAP服务器查询该用户的凭证。
4.  验证通过后，用户即可登录系统。这样实现了账户的集中管理。

![](img/5418f9dd632c07f0dbd0bc91606183e9_76.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_77.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_79.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_80.png)

> **注意**：OpenLDAP服务端的配置非常复杂，涉及证书生成、数据库初始化、数据导入和模式修改等多个步骤，通常在生产环境中会使用管理工具或预制模板。在RHCE考试中，主要考察的是**OpenLDAP客户端的配置**，即如何将一台Linux主机加入到已有的OpenLDAP域中进行认证。服务端的详细配置属于进阶内容，初学者了解其概念和工作原理即可。

![](img/5418f9dd632c07f0dbd0bc91606183e9_82.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_84.png)

---

## 总结

本节课我们一起学习了三个重要的网络服务：
1.  **DHCP服务**：我们理解了其自动分配网络参数的作用，并动手配置了DHCP服务器，包括定义作用域、地址池以及为特定主机绑定IP地址。
2.  **邮件服务**：我们搭建了一个包含Postfix（发件）和Dovecot（收件）的简易邮件系统，并配合DNS的MX记录，实现了邮件的发送、接收和别名转发功能。
3.  **OpenLDAP服务**：我们了解了这款目录服务的基本概念和集中化管理用户账户的用途，为后续学习客户端配置打下了基础。

![](img/5418f9dd632c07f0dbd0bc91606183e9_86.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_87.png)

![](img/5418f9dd632c07f0dbd0bc91606183e9_88.png)

这些服务是构建企业IT基础设施的常见组件，理解它们的原理和基本配置方法，是成为一名合格系统工程师的重要一步。下节课我们将继续学习其他网络服务。