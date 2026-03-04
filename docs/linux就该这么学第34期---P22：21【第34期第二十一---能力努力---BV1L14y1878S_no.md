# Linux就该这么学：第34期：第21节课：DHCP与邮件系统部署

![](img/4a96289522f967d7e8a90ac254796560_1.png)

在本节课中，我们将学习两个重要的网络服务：动态主机配置协议（DHCP）和邮件系统。DHCP服务能够自动为网络中的主机分配IP地址等网络配置信息，而邮件系统则允许我们在不同时间、不同地点进行异步通信。通过本课的学习，你将掌握如何搭建和配置这两个服务，并理解它们在实际工作环境中的应用。

![](img/4a96289522f967d7e8a90ac254796560_3.png)

![](img/4a96289522f967d7e8a90ac254796560_5.png)

## DHCP服务配置

![](img/4a96289522f967d7e8a90ac254796560_7.png)

上一节我们介绍了网络基础配置，本节中我们来看看如何自动化地管理网络配置。

![](img/4a96289522f967d7e8a90ac254796560_9.png)

DHCP（动态主机配置协议）可以自动为网络中的主机分配网卡信息，这不仅限于IP地址，还包括子网掩码、网关和DNS服务器等。其核心价值在于批量分配和高效回收IP地址资源。

![](img/4a96289522f967d7e8a90ac254796560_11.png)

![](img/4a96289522f967d7e8a90ac254796560_13.png)

![](img/4a96289522f967d7e8a90ac254796560_15.png)

### 核心概念与术语

以下是理解DHCP配置所需的核心术语：

![](img/4a96289522f967d7e8a90ac254796560_17.png)

![](img/4a96289522f967d7e8a90ac254796560_19.png)

*   **作用域**：一个声明的大范围IP地址段（例如 `192.168.10.0/24`），表示服务器可能分配的地址范围。
*   **排除范围**：作用域内明确不会分配给客户端的IP地址范围。
*   **地址池**：真正可供客户端分配的IP地址范围。**地址池 = 作用域 - 排除范围**。地址池可以小于或等于作用域。
*   **租约**：客户端获得IP地址后的使用期限，分为两个时间：
    *   **默认租约时间**：到达此时间后，服务器会标记该地址，但不会强制回收，客户端可以尝试续租。
    *   **最大租约时间**：到达此时间后，服务器会强制回收IP地址。**默认租约时间 ≤ 最大租约时间**。
*   **绑定**：将特定的IP地址与某台主机的MAC地址永久关联，确保该主机每次都能获得相同的IP。

### 实验环境准备

![](img/4a96289522f967d7e8a90ac254796560_21.png)

![](img/4a96289522f967d7e8a90ac254796560_23.png)

![](img/4a96289522f967d7e8a90ac254796560_25.png)

![](img/4a96289522f967d7e8a90ac254796560_27.png)

![](img/4a96289522f967d7e8a90ac254796560_29.png)

在开始配置前，需要关闭虚拟机自带的DHCP服务，以避免冲突。

![](img/4a96289522f967d7e8a90ac254796560_31.png)

![](img/4a96289522f967d7e8a90ac254796560_33.png)

![](img/4a96289522f967d7e8a90ac254796560_35.png)

1.  打开VMware，点击“编辑” -> “虚拟网络编辑器”。
2.  选择使用的网络连接（如VMnet8/NAT模式），取消勾选“使用本地DHCP服务将IP地址分配给虚拟机”。
3.  点击“确定”保存。

![](img/4a96289522f967d7e8a90ac254796560_37.png)

![](img/4a96289522f967d7e8a90ac254796560_39.png)

### 安装与配置DHCP服务

![](img/4a96289522f967d7e8a90ac254796560_41.png)

![](img/4a96289522f967d7e8a90ac254796560_43.png)

![](img/4a96289522f967d7e8a90ac254796560_45.png)

![](img/4a96289522f967d7e8a90ac254796560_47.png)

![](img/4a96289522f967d7e8a90ac254796560_49.png)

以下是配置DHCP服务器的步骤：

![](img/4a96289522f967d7e8a90ac254796560_51.png)

1.  **安装软件包**：
    ```bash
    yum install -y dhcp-server
    ```
    > **提示**：许多Linux服务的名称以 `d` 结尾，这代表 **daemon**（守护进程）。守护进程会随系统启动而启动，持续运行以提供服务，这与执行完就结束的普通进程不同。

![](img/4a96289522f967d7e8a90ac254796560_53.png)

![](img/4a96289522f967d7e8a90ac254796560_55.png)

2.  **编辑主配置文件**：
    DHCP的主配置文件是 `/etc/dhcp/dhcpd.conf`。初始文件是空的，但提供了示例路径。
    ```bash
    vim /etc/dhcp/dhcpd.conf
    ```
    将以下配置内容写入文件：
    ```bash
    # 全局配置
    ddns-update-style none;                     # 禁用动态DNS更新
    ignore client-updates;                      # 忽略客户端更新
    # 子网声明（作用域）
    subnet 192.168.10.0 netmask 255.255.255.0 {
        range 192.168.10.100 192.168.10.200;    # 地址池范围
        option subnet-mask 255.255.255.0;       # 分配给客户端的子网掩码
        option routers 192.168.10.1;            # 分配给客户端的网关地址
        option domain-name-servers 192.168.10.10; # 分配给客户端的DNS服务器地址
        default-lease-time 21600;               # 默认租约时间（6小时，单位秒）
        max-lease-time 43200;                   # 最大租约时间（12小时，单位秒）
    }
    ```
    > **注意**：每行配置以分号 `;` 结尾，但最后一个花括号 `}` 后没有分号。

3.  **启动服务并设置开机自启**：
    ```bash
    systemctl start dhcpd
    systemctl enable dhcpd
    ```

![](img/4a96289522f967d7e8a90ac254796560_57.png)

![](img/4a96289522f967d7e8a90ac254796560_59.png)

4.  **配置防火墙**：
    ```bash
    firewall-cmd --permanent --add-service=dhcp
    firewall-cmd --reload
    ```

### 客户端测试

![](img/4a96289522f967d7e8a90ac254796560_61.png)

![](img/4a96289522f967d7e8a90ac254796560_63.png)

![](img/4a96289522f967d7e8a90ac254796560_65.png)

![](img/4a96289522f967d7e8a90ac254796560_67.png)

![](img/4a96289522f967d7e8a90ac254796560_69.png)

将另一台虚拟机（或Windows主机）的网络模式设置为与DHCP服务器同一网络（如NAT模式），并将其网卡配置为“自动获得IP地址”。重启网卡后，即可看到自动获取到了配置文件中定义的IP地址（如 `192.168.10.100`）及其他网络参数。

![](img/4a96289522f967d7e8a90ac254796560_71.png)

### IP地址与MAC地址绑定

![](img/4a96289522f967d7e8a90ac254796560_73.png)

![](img/4a96289522f967d7e8a90ac254796560_75.png)

如果需要为特定主机（如老板的电脑）固定分配一个“吉利”的IP地址（如 `192.168.10.188`），可以进行绑定操作。

![](img/4a96289522f967d7e8a90ac254796560_77.png)

![](img/4a96289522f967d7e8a90ac254796560_79.png)

![](img/4a96289522f967d7e8a90ac254796560_81.png)

1.  **获取客户端的MAC地址**：
    *   可以在客户端系统上查看。
    *   也可以在DHCP服务器的日志文件中查看：`tail -f /var/log/messages`，当客户端获取IP时，日志会记录其MAC地址。

![](img/4a96289522f967d7e8a90ac254796560_83.png)

![](img/4a96289522f967d7e8a90ac254796560_85.png)

2.  **在DHCP配置文件中添加绑定**：
    在 `/etc/dhcp/dhcpd.conf` 的 `subnet` 声明块内，添加以下内容：
    ```bash
    host boss-pc {
        hardware ethernet 00:0C:29:XX:XX:XX; # 替换为实际的MAC地址
        fixed-address 192.168.10.188;        # 指定要绑定的IP地址
    }
    ```

![](img/4a96289522f967d7e8a90ac254796560_87.png)

![](img/4a96289522f967d7e8a90ac254796560_89.png)

![](img/4a96289522f967d7e8a90ac254796560_91.png)

3.  **重启DHCP服务**：
    ```bash
    systemctl restart dhcpd
    ```
    此后，该MAC地址的主机每次都将获得 `192.168.10.188` 这个IP地址。

![](img/4a96289522f967d7e8a90ac254796560_93.png)

![](img/4a96289522f967d7e8a90ac254796560_95.png)

![](img/4a96289522f967d7e8a90ac254796560_97.png)

## 邮件系统部署

![](img/4a96289522f967d7e8a90ac254796560_99.png)

![](img/4a96289522f967d7e8a90ac254796560_101.png)

上一节我们实现了网络的自动化管理，本节中我们来构建一个异步通信系统——邮件系统。

邮件系统解决了通信双方不在线时的信息传递问题。一个完整的邮件系统通常涉及多个角色和服务协同工作。

### 邮件系统组成

1.  **邮件用户代理（MUA）**：用户用来收发邮件的客户端软件，如Outlook、Thunderbird。
2.  **邮件传输代理（MTA）**：负责邮件的转发和传输。在RHEL 7/8中，我们使用 `postfix`。
3.  **邮件投递代理（MDA）**：负责将接收到的邮件保存到用户的邮箱目录。在RHEL中，我们使用 `dovecot`。
4.  **DNS服务**：提供域名解析，使邮件地址（如 `user@linuxprobe.com`）能够被正确路由。

### 基础服务准备

![](img/4a96289522f967d7e8a90ac254796560_103.png)

![](img/4a96289522f967d7e8a90ac254796560_105.png)

![](img/4a96289522f967d7e8a90ac254796560_107.png)

![](img/4a96289522f967d7e8a90ac254796560_109.png)

为了实验更完整，我们先配置好DNS服务（假设域名为 `linuxprobe.com`）。

![](img/4a96289522f967d7e8a90ac254796560_111.png)

![](img/4a96289522f967d7e8a90ac254796560_113.png)

![](img/4a96289522f967d7e8a90ac254796560_115.png)

![](img/4a96289522f967d7e8a90ac254796560_117.png)

![](img/4a96289522f967d7e8a90ac254796560_119.png)

![](img/4a96289522f967d7e8a90ac254796560_121.png)

1.  **安装DNS服务**：
    ```bash
    yum install -y bind bind-chroot
    ```

![](img/4a96289522f967d7e8a90ac254796560_123.png)

![](img/4a96289522f967d7e8a90ac254796560_125.png)

2.  **配置主配置文件** (`/etc/named.conf`)：
    修改第11和17行，将 `localhost` 改为 `any`，允许所有网络接口提供服务，并允许所有客户端查询。
    ```bash
    listen-on port 53 { any; };
    allow-query     { any; };
    ```

3.  **配置区域文件**：
    *   编辑 `/etc/named.rfc1912.zones`，添加正向区域声明。
    *   创建并编辑区域数据文件 `/var/named/linuxprobe.com.zone`，内容示例如下：
        ```bash
        $TTL 1D
        @       IN SOA  linuxprobe.com. root.linuxprobe.com. (
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
        > **关键记录**：`MX` 记录指向邮件服务器 `mail.linuxprobe.com`。

4.  **启动DNS服务**：
    ```bash
    systemctl start named
    systemctl enable named
    ```

![](img/4a96289522f967d7e8a90ac254796560_127.png)

5.  **配置防火墙**：
    ```bash
    firewall-cmd --permanent --add-service=dns
    firewall-cmd --reload
    ```

![](img/4a96289522f967d7e8a90ac254796560_129.png)

![](img/4a96289522f967d7e8a90ac254796560_131.png)

![](img/4a96289522f967d7e8a90ac254796560_133.png)

### 配置Postfix (MTA)

![](img/4a96289522f967d7e8a90ac254796560_135.png)

![](img/4a96289522f967d7e8a90ac254796560_137.png)

![](img/4a96289522f967d7e8a90ac254796560_139.png)

1.  **安装Postfix**：
    ```bash
    yum install -y postfix
    ```

![](img/4a96289522f967d7e8a90ac254796560_141.png)

![](img/4a96289522f967d7e8a90ac254796560_143.png)

![](img/4a96289522f967d7e8a90ac254796560_145.png)

![](img/4a96289522f967d7e8a90ac254796560_147.png)

![](img/4a96289522f967d7e8a90ac254796560_149.png)

2.  **修改主配置文件** (`/etc/postfix/main.cf`)：
    ```bash
    myhostname = mail.linuxprobe.com        # 服务器主机名
    mydomain = linuxprobe.com               # 邮件域名
    myorigin = $mydomain                    # 外发邮件地址后缀
    inet_interfaces = all                   # 监听所有网卡
    mydestination = $myhostname, localhost.$mydomain, $mydomain # 本地投递域名
    ```
    可以使用 `hostnamectl set-hostname mail.linuxprobe.com` 命令设置主机名。

3.  **启动Postfix**：
    ```bash
    systemctl start postfix
    systemctl enable postfix
    ```

![](img/4a96289522f967d7e8a90ac254796560_151.png)

![](img/4a96289522f967d7e8a90ac254796560_153.png)

![](img/4a96289522f967d7e8a90ac254796560_155.png)

### 配置Dovecot (MDA)

![](img/4a96289522f967d7e8a90ac254796560_157.png)

![](img/4a96289522f967d7e8a90ac254796560_159.png)

1.  **安装Dovecot**：
    ```bash
    yum install -y dovecot
    ```

![](img/4a96289522f967d7e8a90ac254796560_161.png)

![](img/4a96289522f967d7e8a90ac254796560_163.png)

2.  **修改主配置文件** (`/etc/dovecot/dovecot.conf`)：
    启用协议（第24行附近）：
    ```bash
    protocols = imap pop3 lmtp
    ```
    禁用强制SSL（非加密连接也可用，仅用于测试）：
    ```bash
    ssl = no
    disable_plaintext_auth = no
    ```

![](img/4a96289522f967d7e8a90ac254796560_165.png)

![](img/4a96289522f967d7e8a90ac254796560_167.png)

3.  **配置邮件存储路径** (`/etc/dovecot/conf.d/10-mail.conf`)：
    修改邮件存储位置（约第30行）：
    ```bash
    mail_location = mbox:~/mail:INBOX=/var/mail/%u
    ```
    > **注意**：这里有一个“坑”。配置中提到的路径 `~/mail` 下，实际需要存在一个隐藏目录 `.imap`，并且其下需要有 `INBOX` 等子目录。例如，为用户 `dongdong` 创建：
    ```bash
    su - dongdong
    mkdir -p mail/.imap/INBOX
    exit
    ```

4.  **配置用户认证** (`/etc/dovecot/conf.d/10-auth.conf`)：
    可以限制登录的IP段（约第49行）：
    ```bash
    auth_allow_networks = 192.168.10.0/24
    ```

![](img/4a96289522f967d7e8a90ac254796560_169.png)

![](img/4a96289522f967d7e8a90ac254796560_171.png)

![](img/4a96289522f967d7e8a90ac254796560_173.png)

![](img/4a96289522f967d7e8a90ac254796560_175.png)

5.  **启动Dovecot并放行防火墙**：
    ```bash
    systemctl start dovecot
    systemctl enable dovecot
    firewall-cmd --permanent --add-service={pop3,imap,smtp}
    firewall-cmd --reload
    ```

### 邮件别名

![](img/4a96289522f967d7e8a90ac254796560_177.png)

![](img/4a96289522f967d7e8a90ac254796560_179.png)

可以为不存在的用户设置别名，将其邮件投递到真实用户邮箱。

![](img/4a96289522f967d7e8a90ac254796560_181.png)

![](img/4a96289522f967d7e8a90ac254796560_183.png)

![](img/4a96289522f967d7e8a90ac254796560_185.png)

1.  **编辑别名文件** (`/etc/aliases`)：
    ```bash
    xixi: dongdong
    ```
    表示发送给 `xixi@linuxprobe.com` 的邮件，实际会投递给 `dongdong` 用户。

2.  **使别名生效**：
    ```bash
    newaliases
    systemctl restart postfix dovecot
    ```

![](img/4a96289522f967d7e8a90ac254796560_187.png)

![](img/4a96289522f967d7e8a90ac254796560_189.png)

### 测试邮件系统

![](img/4a96289522f967d7e8a90ac254796560_191.png)

![](img/4a96289522f967d7e8a90ac254796560_193.png)

![](img/4a96289522f967d7e8a90ac254796560_195.png)

#### 服务器端命令行测试

![](img/4a96289522f967d7e8a90ac254796560_197.png)

![](img/4a96289522f967d7e8a90ac254796560_199.png)

1.  **安装邮件客户端工具**：
    ```bash
    yum install -y mailx
    ```

![](img/4a96289522f967d7e8a90ac254796560_201.png)

![](img/4a96289522f967d7e8a90ac254796560_203.png)

![](img/4a96289522f967d7e8a90ac254796560_205.png)

![](img/4a96289522f967d7e8a90ac254796560_207.png)

![](img/4a96289522f967d7e8a90ac254796560_209.png)

![](img/4a96289522f967d7e8a90ac254796560_211.png)

2.  **发送测试邮件**：
    ```bash
    echo "邮件正文内容" | mailx -s "邮件主题" dongdong@linuxprobe.com
    ```
    或者交互式发送：
    ```bash
    mailx -s "Test to Dongdong" dongdong@linuxprobe.com
    （输入正文后，按 Ctrl+D 发送）
    ```

![](img/4a96289522f967d7e8a90ac254796560_213.png)

![](img/4a96289522f967d7e8a90ac254796560_215.png)

3.  **用户查看邮件**：
    ```bash
    su - dongdong
    mail
    ```

![](img/4a96289522f967d7e8a90ac254796560_217.png)

![](img/4a96289522f967d7e8a90ac254796560_219.png)

![](img/4a96289522f967d7e8a90ac254796560_221.png)

#### 使用图形化客户端 (Thunderbird)

![](img/4a96289522f967d7e8a90ac254796560_223.png)

![](img/4a96289522f967d7e8a90ac254796560_225.png)

![](img/4a96289522f967d7e8a90ac254796560_227.png)

对于Linux桌面环境，可以使用开源的Thunderbird客户端。

1.  **安装Thunderbird**：
    ```bash
    yum install -y thunderbird
    ```

![](img/4a96289522f967d7e8a90ac254796560_229.png)

![](img/4a96289522f967d7e8a90ac254796560_231.png)

![](img/4a96289522f967d7e8a90ac254796560_233.png)

![](img/4a96289522f967d7e8a90ac254796560_235.png)

2.  **配置账户**：
    *   启动Thunderbird，添加邮件账户。
    *   姓名：`dongdong`
    *   邮箱地址：`dongdong@linuxprobe.com`
    *   密码：系统用户 `dongdong` 的密码（例如 `redhat`）。
    *   服务器类型：POP3或IMAP（根据Dovecot配置）。
    *   收件服务器：`mail.linuxprobe.com`
    *   发件服务器：`mail.linuxprobe.com`
    *   在出现SSL证书警告时，选择“手动配置”，并接受非加密连接（仅测试环境）。

![](img/4a96289522f967d7e8a90ac254796560_237.png)

![](img/4a96289522f967d7e8a90ac254796560_239.png)

![](img/4a96289522f967d7e8a90ac254796560_241.png)

配置完成后，即可在Thunderbird中收发邮件。

![](img/4a96289522f967d7e8a90ac254796560_243.png)

![](img/4a96289522f967d7e8a90ac254796560_245.png)

## 总结

![](img/4a96289522f967d7e8a90ac254796560_247.png)

本节课中我们一起学习了两个重要的网络服务。首先，我们详细介绍了DHCP服务的原理与配置，包括作用域、地址池、租约和IP-MAC绑定等核心概念，实现了网络配置的自动化分配与管理。随后，我们构建了一个完整的邮件系统，整合了DNS、Postfix (MTA)、Dovecot (MDA) 等多个服务，理解了邮件收发、转发和存储的流程，并掌握了在命令行和图形界面下测试邮件功能的方法。通过本课的学习，你将具备搭建和维护企业内网基础服务的能力。