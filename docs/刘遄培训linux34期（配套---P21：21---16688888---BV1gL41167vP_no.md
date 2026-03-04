# Linux培训第34期：P21：DHCP与邮件系统部署教程 📚

![](img/d4151cdb4bdfe5695e38ce29cad69830_1.png)

在本节课中，我们将学习两个重要的网络服务：DHCP（动态主机配置协议）和邮件系统。DHCP服务能够自动为网络中的主机分配IP地址等网络配置信息，而邮件系统则允许我们在不同主机之间异步发送和接收信息。通过本教程，你将掌握如何配置DHCP服务器以及搭建一个基础的邮件收发环境。

![](img/d4151cdb4bdfe5695e38ce29cad69830_3.png)

---

## 课程回顾与安排 📅

上一节课程中，我们主要进行了RHCE考试的预约工作。本节我们将正式进入技术学习。

![](img/d4151cdb4bdfe5695e38ce29cad69830_5.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_7.png)

首先，对昨天的考试预约情况进行总结：
*   我们已成功为学员预约了北京、上海、广州、深圳等主要城市的考试。
*   部分城市（如济南、武汉）也已成功预约。
*   天津、成都、郑州、杭州、合肥等城市的考位仍在协调中，预计下周会有进一步通知。
*   目前仅剩深圳6月22日尚有少量考位。
*   如需报名，请按“城市+期望时间”的格式发送信息。

接下来，我们开始今天的正式课程。

---

## 第14章节：DHCP动态主机配置协议 🌐

![](img/d4151cdb4bdfe5695e38ce29cad69830_9.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_11.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_13.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_15.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_17.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_19.png)

本节中，我们来看看DHCP服务。DHCP全称为**动态主机配置协议**，其核心功能是自动为网络中的客户端主机分配**网卡配置信息**。

![](img/d4151cdb4bdfe5695e38ce29cad69830_21.png)

**核心公式**：
`分配的网卡信息 = IP地址 + 子网掩码 + 网关 + DNS服务器地址`

![](img/d4151cdb4bdfe5695e38ce29cad69830_23.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_25.png)

DHCP服务的两大主要用途是：
1.  **批量分配**：为大量主机自动配置网络参数，避免手动设置的繁琐。
2.  **地址回收**：管理IP地址的租期，实现地址的高效复用。例如，咖啡馆的客人离店后，其使用的Wi-Fi地址可被回收并分配给下一位客人。

![](img/d4151cdb4bdfe5695e38ce29cad69830_27.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_29.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_31.png)

### 关键术语解析

![](img/d4151cdb4bdfe5695e38ce29cad69830_33.png)

在配置前，我们需要理解几个核心概念：

![](img/d4151cdb4bdfe5695e38ce29cad69830_35.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_37.png)

*   **作用域**：一个声明的、较大的IP地址范围（例如一个网段）。它并非直接分配给客户端的地址池。
*   **排除范围**：作用域内**不**分配给客户端的IP地址范围。
*   **地址池**：真正可分配给客户端的IP地址范围。
    **核心关系**：`地址池 = 作用域 - 排除范围`
    地址池可以等于或小于作用域，但绝不能大于作用域。
*   **地址保留/绑定**：将特定的IP地址与特定主机（通过MAC地址标识）永久绑定。
*   **租约**：DHCP服务器分配IP地址的时效机制，包含两个时间：
    *   **默认租约时间**：到达此时间后，服务器会标记该地址，但不会立即回收。
    *   **最大租约时间**：到达此时间后，服务器将**强制回收**该IP地址。
    **核心关系**：`默认租约时间 <= 最大租约时间`

![](img/d4151cdb4bdfe5695e38ce29cad69830_39.png)

### 实验环境准备

![](img/d4151cdb4bdfe5695e38ce29cad69830_41.png)

在开始配置DHCP服务器前，需要确保实验环境纯净。

![](img/d4151cdb4bdfe5695e38ce29cad69830_43.png)

以下是需要进行的准备工作：
1.  关闭VMware虚拟网络编辑器中的DHCP服务，避免与自建服务冲突。
2.  配置防火墙，放行DHCP服务端口。
    ```bash
    firewall-cmd --permanent --add-service=dhcp
    firewall-cmd --reload
    ```
3.  安装DHCP服务器软件包。在RHEL 8中，软件包名称为 `dhcp-server`。
    ```bash
    yum install -y dhcp-server
    ```

![](img/d4151cdb4bdfe5695e38ce29cad69830_45.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_47.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_49.png)

**小知识**：为什么很多服务名称以 `d` 结尾？  
`d` 代表 **daemon**（守护进程）。守护进程是一种长期运行、在系统启动时随开机启动、关机时随系统关闭的进程，它会持续监听并响应请求（如Web服务、DHCP服务）。这与执行完就结束的普通进程不同。

### 配置DHCP服务器

DHCP服务的主配置文件是 `/etc/dhcp/dhcpd.conf`。初始文件可能是空的或包含示例。

![](img/d4151cdb4bdfe5695e38ce29cad69830_51.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_53.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_55.png)

我们将手动创建配置。一个基础的配置示例如下：

![](img/d4151cdb4bdfe5695e38ce29cad69830_57.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_59.png)

```bash
# 编辑主配置文件
vim /etc/dhcp/dhcpd.conf
```

![](img/d4151cdb4bdfe5695e38ce29cad69830_61.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_63.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_65.png)

```bash
# 全局配置
ddns-update-style none;            # 禁用动态DNS更新
ignore client-updates;             # 忽略客户端更新

![](img/d4151cdb4bdfe5695e38ce29cad69830_67.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_69.png)

# 定义一个作用域（子网）
subnet 192.168.10.0 netmask 255.255.255.0 {
    range 192.168.10.100 192.168.10.200;    # 地址池范围
    option subnet-mask 255.255.255.0;       # 分配给客户端的子网掩码
    option routers 192.168.10.1;            # 分配给客户端的网关地址
    option domain-name-servers 192.168.10.10, 8.8.8.8; # 分配给客户端的DNS服务器
    default-lease-time 21600;               # 默认租约时间（秒），6小时
    max-lease-time 43200;                   # 最大租约时间（秒），12小时
}
```

![](img/d4151cdb4bdfe5695e38ce29cad69830_71.png)

**配置说明**：
*   `subnet` 定义了一个作用域。
*   `range` 定义了真正的地址池。
*   `option` 开头的行定义了分配给客户端的各种网络参数。
*   租约时间以秒为单位。

保存并退出后，启动并设置服务开机自启：

```bash
systemctl restart dhcpd
systemctl enable dhcpd
```

### 测试DHCP服务

使用另一台虚拟机（如Windows）作为客户端，将其网卡设置为“自动获得IP地址”。重启网卡后，在命令行输入 `ipconfig /all` 或查看网络连接详细信息，应能看到从服务器获取到的IP地址（如192.168.10.100）及其他配置信息。

![](img/d4151cdb4bdfe5695e38ce29cad69830_73.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_75.png)

### IP地址与MAC地址绑定

![](img/d4151cdb4bdfe5695e38ce29cad69830_77.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_79.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_81.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_83.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_85.png)

如果需要为特定主机（如老板的电脑）固定分配一个特定IP（如192.168.10.188），可以进行绑定。

1.  获取客户端的MAC地址。可以在客户端使用 `ipconfig /all`（Windows）或 `ifconfig`（Linux）查看，也可以通过DHCP服务器的日志文件 `/var/log/messages` 查看分配记录。
2.  在 `/etc/dhcp/dhcpd.conf` 的作用域配置中添加 `host` 声明：

```bash
host boss-computer {                      # 为绑定主机起个名字
    hardware ethernet 00:0C:29:XX:XX:XX;  # 客户端的MAC地址
    fixed-address 192.168.10.188;         # 要绑定的固定IP地址
}
```

3.  重启DHCP服务使配置生效：
    ```bash
    systemctl restart dhcpd
    ```
4.  客户端重启网卡后，将始终获得绑定的IP地址。

![](img/d4151cdb4bdfe5695e38ce29cad69830_87.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_89.png)

---

![](img/d4151cdb4bdfe5695e38ce29cad69830_91.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_93.png)

## 第15章节：部署邮局系统 📧

![](img/d4151cdb4bdfe5695e38ce29cad69830_95.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_97.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_99.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_101.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_103.png)

上一节我们介绍了自动化的网络配置服务，本节中我们来看看如何搭建一个异步通信的邮件系统。邮件系统解决了通信双方不在线时无法即时传递信息的问题，它像邮局一样，发送方将邮件投递到服务器，接收方在方便时再收取。

![](img/d4151cdb4bdfe5695e38ce29cad69830_105.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_107.png)

邮件系统通常由以下几个角色协同工作：
1.  **MUA**：邮件用户代理，用户用来收发邮件的客户端软件（如Outlook, Thunderbird）。
2.  **MTA**：邮件传输代理，负责邮件的**转发和发送**（如Postfix）。
3.  **MDA**：邮件投递代理，负责将接收到的邮件**保存**到用户邮箱（如Dovecot）。
4.  **DNS**：域名解析服务，将邮件域名解析为服务器IP地址，并通过MX记录指明邮件服务器。

### 实验环境搭建

![](img/d4151cdb4bdfe5695e38ce29cad69830_109.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_111.png)

本次实验将综合运用多个服务：
*   **DHCP/DNS**：为实验环境提供基础网络和域名解析。
*   **Postfix**：作为MTA，负责发送邮件。
*   **Dovecot**：作为MDA，负责接收并保存邮件。
*   **客户端软件**：用于测试（如Windows Outlook或Linux Thunderbird）。

![](img/d4151cdb4bdfe5695e38ce29cad69830_113.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_115.png)

首先，确保DNS服务已正确配置，能够解析我们的邮件域名（例如 `mail.linuxprobe.com` 指向服务器IP `192.168.10.10`）。

![](img/d4151cdb4bdfe5695e38ce29cad69830_117.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_119.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_121.png)

### 配置Postfix (MTA)

![](img/d4151cdb4bdfe5695e38ce29cad69830_123.png)

1.  安装Postfix软件包：
    ```bash
    yum install -y postfix
    ```
2.  修改主配置文件 `/etc/postfix/main.cf`，关键参数如下：
    ```bash
    myhostname = mail.linuxprobe.com      # 服务器主机名
    mydomain = linuxprobe.com             # 邮件域
    myorigin = $mydomain                  # 外发邮件地址的后缀
    inet_interfaces = all                 # 监听所有网卡
    mydestination = $myhostname, localhost.$mydomain, $mydomain # 本机负责投递的域名
    ```
3.  启动并设置开机自启：
    ```bash
    systemctl restart postfix
    systemctl enable postfix
    ```

### 配置Dovecot (MDA)

![](img/d4151cdb4bdfe5695e38ce29cad69830_125.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_127.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_129.png)

1.  安装Dovecot软件包：
    ```bash
    yum install -y dovecot
    ```
2.  修改主配置文件 `/etc/dovecot/dovecot.conf`：
    ```bash
    protocols = imap pop3 lmtp          # 启用支持的协议
    ssl = no                            # 本次实验禁用SSL加密
    ```
3.  修改认证配置文件 `/etc/dovecot/conf.d/10-auth.conf`：
    ```bash
    disable_plaintext_auth = no         # 允许明文密码认证
    ```
4.  修改邮件存储配置文件 `/etc/dovecot/conf.d/10-mail.conf`：
    ```bash
    mail_location = mbox:~/mail:INBOX=/var/mail/%u # 邮件存储格式和路径
    ```
    **注意**：需要为用户手动创建邮件目录，例如为用户 `dongdong` 创建：
    ```bash
    su - dongdong
    mkdir -p ~/mail/.imap/INBOX
    exit
    ```
5.  配置防火墙，放行邮件服务端口：
    ```bash
    firewall-cmd --permanent --add-service={smtp,pop3,imap}
    firewall-cmd --reload
    ```
6.  启动并设置开机自启：
    ```bash
    systemctl restart dovecot
    systemctl enable dovecot
    ```

![](img/d4151cdb4bdfe5695e38ce29cad69830_131.png)

### 创建测试用户

![](img/d4151cdb4bdfe5695e38ce29cad69830_133.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_135.png)

添加一个系统用户，该用户的账号密码将用于邮件客户端登录（通过PAM模块认证）：

```bash
useradd dongdong
echo "redhat" | passwd --stdin dongdong
```

### 配置邮件别名

![](img/d4151cdb4bdfe5695e38ce29cad69830_137.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_139.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_141.png)

别名功能允许发送给一个不存在的用户（如 `xixi`）的邮件，实际投递到另一个真实用户（如 `dongdong`）的邮箱。

![](img/d4151cdb4bdfe5695e38ce29cad69830_143.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_145.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_147.png)

编辑别名文件 `/etc/aliases`，添加一行：
```bash
xixi: dongdong
```
然后执行 `newaliases` 命令使别名生效，并重启Postfix和Dovecot服务。

![](img/d4151cdb4bdfe5695e38ce29cad69830_149.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_151.png)

### 测试邮件系统

![](img/d4151cdb4bdfe5695e38ce29cad69830_153.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_155.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_157.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_159.png)

**1. 使用命令行工具发送测试邮件**

![](img/d4151cdb4bdfe5695e38ce29cad69830_161.png)

在服务器上安装 `mailx` 工具并发送邮件：
```bash
yum install -y mailx
echo "邮件正文内容" | mail -s "邮件主题" dongdong@linuxprobe.com
```

![](img/d4151cdb4bdfe5695e38ce29cad69830_163.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_165.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_167.png)

**2. 使用客户端软件收发邮件**

![](img/d4151cdb4bdfe5695e38ce29cad69830_169.png)

*   **Windows客户端**：可以使用Outlook。配置账户时，输入用户名（`dongdong@linuxprobe.com`）、密码（`redhat`），服务器地址填写邮件服务器IP或域名。首次连接可能提示非加密连接，选择允许即可。
*   **Linux客户端**：推荐使用Thunderbird。安装后配置方式类似，同样需要允许非加密连接。

配置完成后，即可使用客户端给 `dongdong@linuxprobe.com` 或 `xixi@linuxprobe.com` 发送邮件，并在客户端成功接收。

![](img/d4151cdb4bdfe5695e38ce29cad69830_171.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_173.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_175.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_177.png)

---

![](img/d4151cdb4bdfe5695e38ce29cad69830_179.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_181.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_183.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_185.png)

## 课程总结 🎯

本节课中我们一起学习了两个重要的服务：
1.  **DHCP服务**：我们理解了其自动分配和回收IP地址的原理，掌握了如何配置DHCP服务器、定义地址池、设置租约时间以及实现IP-MAC地址绑定。
2.  **邮件系统**：我们了解了MUA、MTA、MDA的分工，并动手搭建了一个由Postfix、Dovecot和DNS服务构成的完整邮件收发环境。通过这个综合实验，我们看到了多个服务如何协同工作，完成一个复杂的业务需求。

![](img/d4151cdb4bdfe5695e38ce29cad69830_187.png)

![](img/d4151cdb4bdfe5695e38ce29cad69830_189.png)

这些内容虽然不在RHCE考试范围内，但极大地增强了大家在真实环境中配置复杂服务的能力。下节课我们将学习更深入的自动化运维内容。