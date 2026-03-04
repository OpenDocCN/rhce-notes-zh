# Linux服务管理：P9：HTTP服务（Apache）配置与管理 🚀

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_1.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_3.png)

在本节课中，我们将学习如何在Linux系统上部署、配置和管理Apache HTTP服务器。我们将从基础安装开始，逐步深入到虚拟主机配置、访问控制、别名设置等核心功能，确保初学者能够掌握构建和管理Web服务的基本技能。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_5.png)

---

## 一、Apache HTTP服务器概述 🌐

上一节我们介绍了Linux中常见的服务类型，本节中我们来看看最常用的Web服务器之一：Apache。

Apache HTTP Server（简称Apache）是全球使用最广泛的Web服务器软件之一。它基于HTTP协议，用于托管网站和提供Web内容。与Nginx等轻量级服务器相比，Apache以其稳定性和丰富的模块化特性著称。

常见的Web服务器架构有以下几种：
*   **LAMP**：Linux + Apache + MySQL/MariaDB + PHP/Python/Perl
*   **LNMP/LEMP**：Linux + Nginx + MySQL/MariaDB + PHP/Python/Perl

网站类型主要分为：
*   **静态网站**：内容固定，后缀通常为 `.html` 或 `.htm`。修改内容需直接更改源代码。
*   **动态网站**：内容可根据用户交互或数据库查询动态生成，使用如 `.php`、`.jsp`、`.asp` 等后缀。
*   **伪静态网站**：将动态网站伪装成静态页面，便于搜索引擎收录。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_7.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_8.png)

---

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_10.png)

## 二、Apache的安装与启动 ⚙️

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_12.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_13.png)

首先，我们需要在系统上安装Apache软件包。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_15.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_17.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_19.png)

### 安装Apache
在基于RPM的系统（如CentOS/RHEL）上，可以使用Yum包管理器进行安装。安装`httpd`软件包时会自动解决依赖关系。
```bash
yum install -y httpd httpd-devel
```
其中，`httpd-devel`是开发工具包，包含了一些有用的工具和库。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_21.png)

### 查看安装文件
安装完成后，可以查看Apache生成的主要文件和目录。
```bash
rpm -ql httpd | head -20
```
关键目录包括：
*   `/etc/httpd/`：主配置目录。
*   `/etc/httpd/conf/` 和 `/etc/httpd/conf.d/`：主配置文件及其附加配置文件所在目录。
*   `/var/www/html/`：默认的网站根目录。
*   `/var/log/httpd/`：日志文件目录。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_23.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_25.png)

### 启动服务并设置开机自启
安装后，启动Apache服务并设置为开机自动启动。
```bash
systemctl start httpd
systemctl enable httpd
```
检查服务状态和监听的端口（默认为80）。
```bash
systemctl status httpd
netstat -tunlp | grep :80
```

---

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_27.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_28.png)

## 三、Apache基础配置 🛠️

上一节我们启动了Apache，本节中我们来了解其核心配置文件并进行基础调整。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_30.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_31.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_33.png)

Apache的主配置文件是 `/etc/httpd/conf/httpd.conf`。配置文件主要分为三部分：
1.  **注释信息**：以 `#` 开头。
2.  **全局配置**：对整个服务器有效的设置。
3.  **区域配置**：针对特定目录或虚拟主机的设置。

以下是需要关注的关键参数及其说明：

| 参数 | 说明 | 示例/默认值 |
| :--- | :--- | :--- |
| `ServerRoot` | 服务根目录 | `/etc/httpd` |
| `Listen` | 监听端口 | `80` |
| `User` / `Group` | 运行服务的用户/组 | `apache` |
| `ServerAdmin` | 管理员邮箱 | `root@localhost` |
| **`ServerName`** | 服务器域名 | `www.example.com:80` |
| **`DocumentRoot`** | 网站根目录 | `/var/www/html` |
| **`DirectoryIndex`** | 默认索引页面 | `index.html index.html.var` |

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_35.png)

**重要调整**：
*   **修改 `ServerName`**：为避免启动时进行DNS解析导致延迟，建议将其设置为本地地址。
    ```apache
    ServerName 127.0.0.1:80
    ```
*   **修改 `DocumentRoot`**：可以更改默认的网站文件存放路径。例如，改为 `/home/www/root`。
    1.  修改 `DocumentRoot` 参数。
    2.  找到对应的 `<Directory>` 区域，将其路径也修改为新目录。
    3.  创建新目录并设置适当的权限。
    ```bash
    mkdir -p /home/www/root
    chmod 755 /home/www/root
    ```
*   **关闭SELinux**：SELinux可能会阻止Apache访问非默认目录，在实验环境中可以临时或永久关闭。
    ```bash
    # 临时关闭
    setenforce 0
    # 永久关闭（修改配置文件）
    vi /etc/selinux/config
    # 将 SELINUX=enforcing 改为 SELINUX=disabled
    ```

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_37.png)

修改配置后，必须重启Apache服务使更改生效。
```bash
systemctl restart httpd
# 或使用平滑重启，不影响现有连接
systemctl reload httpd
```

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_39.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_40.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_42.png)

---

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_44.png)

## 四、虚拟主机配置 🏠

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_46.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_47.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_49.png)

虚拟主机允许在一台服务器上运行多个网站。Apache支持三种类型的虚拟主机。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_51.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_53.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_55.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_56.png)

### 1. 基于IP的虚拟主机
为服务器配置多个IP地址，每个IP对应一个独立的网站。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_58.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_60.png)

以下是配置步骤：
1.  **添加网卡并配置IP**（在虚拟机中操作）。
2.  **在主配置目录下创建虚拟主机配置文件**，例如 `/etc/httpd/conf.d/vhost.conf`。
3.  **编写配置**，为每个IP定义不同的 `DocumentRoot`。
    ```apache
    <VirtualHost 192.168.1.100:80>
        ServerName www.site1.com
        DocumentRoot /home/www/site1
        <Directory "/home/www/site1">
            Options Indexes FollowSymLinks
            AllowOverride None
            Require all granted
        </Directory>
    </VirtualHost>

    <VirtualHost 192.168.1.110:80>
        ServerName bbs.site1.com
        DocumentRoot /home/www/bbs
        <Directory "/home/www/bbs">
            Options Indexes FollowSymLinks
            AllowOverride None
            Require all granted
        </Directory>
    </VirtualHost>
    ```
4.  **创建对应的网站目录和测试页面**。
    ```bash
    mkdir -p /home/www/{site1,bbs}
    echo "This is Site1" > /home/www/site1/index.html
    echo "This is BBS" > /home/www/bbs/index.html
    ```
5.  **重启Apache服务**，然后即可通过不同IP访问不同的网站。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_62.png)

### 2. 基于域名的虚拟主机
多个域名解析到同一个IP，Apache根据请求中的 `Host` 头来区分网站。

以下是配置步骤：
1.  **在服务器或客户端配置本地域名解析**（`/etc/hosts`）。
    ```
    192.168.1.100 www.site.com bbs.site.com blog.site.com
    ```
2.  **编写虚拟主机配置**，使用相同的IP但不同的 `ServerName`。
    ```apache
    <VirtualHost 192.168.1.100:80>
        ServerName www.site.com
        DocumentRoot /home/www/site
        # ... 目录权限配置同上
    </VirtualHost>

    <VirtualHost 192.168.1.100:80>
        ServerName bbs.site.com
        DocumentRoot /home/www/bbs
        # ... 目录权限配置同上
    </VirtualHost>
    ```
3.  创建目录、页面并重启服务后，即可通过不同域名访问不同内容。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_64.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_66.png)

### 3. 基于端口的虚拟主机
同一个IP，通过不同的端口号来区分网站。

以下是配置步骤：
1.  在主配置文件 `httpd.conf` 中添加额外的 `Listen` 指令。
    ```apache
    Listen 80
    Listen 8080
    Listen 8088
    ```
2.  **编写虚拟主机配置**，指定不同的端口。
    ```apache
    <VirtualHost 192.168.1.100:8080>
        ServerName www.site.com
        DocumentRoot /home/www/site
        # ...
    </VirtualHost>
    ```
3.  配置防火墙允许对应端口，然后即可通过 `IP:端口` 形式访问。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_68.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_70.png)

---

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_72.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_73.png)

## 五、用户个人主页与访问控制 🔐

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_75.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_76.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_78.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_80.png)

### 用户个人主页
Apache可以为系统上的每个用户提供个人主页空间，访问格式为 `http://服务器IP/~用户名/`。

以下是启用和配置步骤：
1.  启用用户目录模块。编辑 `/etc/httpd/conf.d/userdir.conf`，将 `UserDir disabled` 注释掉，并取消 `UserDir public_html` 的注释。
2.  为用户创建个人主页目录。
    ```bash
    useradd tom
    su - tom
    mkdir public_html
    echo "Tom's Homepage" > public_html/index.html
    exit
    ```
3.  重启Apache后，即可通过 `http://服务器IP/~tom/` 访问。

**添加访问认证**：可以为个人主页添加密码保护。
1.  使用 `htpasswd` 命令创建密码文件。
    ```bash
    htpasswd -c /etc/httpd/passwd tom
    ```
2.  在用户目录配置中，添加认证指令。
    ```apache
    <Directory "/home/*/public_html">
        AuthType Basic
        AuthName "Private Homepage"
        AuthUserFile /etc/httpd/passwd
        Require user tom jerry # 允许访问的用户列表
    </Directory>
    ```

### 目录访问控制
可以基于客户端IP地址或浏览器类型来限制对特定目录的访问。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_82.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_84.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_86.png)

以下是基于IP的访问控制示例，在 `<Directory>` 区域中添加：
```apache
<Directory "/var/www/html/restricted">
    Order deny,allow # 先处理deny规则，再处理allow规则
    Deny from all # 默认拒绝所有
    Allow from 192.168.1.0/24 # 允许该网段
</Directory>
```
在Apache 2.4中，语法有所变化，使用 `Require` 指令：
```apache
<Directory "/var/www/html/restricted">
    Require ip 192.168.1.0/24
</Directory>
```

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_88.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_90.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_91.png)

---

## 六、别名与目录列表 🗂️

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_93.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_95.png)

### 设置别名（Alias）
别名允许将URL路径映射到文件系统中的任意位置，而非网站根目录下。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_97.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_98.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_100.png)

例如，将URL路径 `/data` 映射到 `/usr/local/data`：
```apache
Alias /data "/usr/local/data/"
<Directory "/usr/local/data">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
```
配置后，访问 `http://服务器IP/data` 实际上显示的是 `/usr/local/data/` 目录下的内容。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_102.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_104.png)

### 控制目录列表显示
当访问一个没有默认索引文件（如index.html）的目录时，Apache可能会显示该目录的文件列表。这在内部网络共享文件时有用，但在生产环境存在安全风险。

**禁用目录列表**：在对应目录的 `<Directory>` 区域中，移除或注释掉 `Options` 指令中的 `Indexes` 参数。
```apache
<Directory "/usr/local/data">
    # Options Indexes FollowSymLinks # 启用目录列表
    Options FollowSymLinks # 禁用目录列表
    ...
</Directory>
```
禁用后，访问此类目录将返回 `403 Forbidden` 错误。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_106.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_108.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_110.png)

---

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_112.png)

## 七、总结 📚

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_114.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_116.png)

本节课中我们一起学习了Apache HTTP服务器的核心配置与管理。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_118.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_120.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_122.png)

我们首先完成了Apache的安装与基础服务管理。然后，深入探讨了其主配置文件的各项关键参数，并学会了如何修改网站根目录等基本设置。

课程的重点是**虚拟主机**的配置，我们实践了基于IP、域名和端口三种方式来实现一台服务器托管多个网站。此外，我们还配置了**用户个人主页**，并为其添加了基本的访问认证。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_124.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_126.png)

在安全与优化方面，我们学习了如何使用**访问控制列表**限制特定IP或客户端的访问，如何通过**别名功能**灵活映射URL路径，以及如何**禁用目录列表**以增强服务器安全性。

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_128.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_130.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_132.png)

![](img/5658e6f87c6b2ce3b882ad2d4890c61c_134.png)

通过本课的学习，你应该已经具备了在Linux系统上部署、配置和管理一个功能完整的Apache Web服务器的能力。这些技能是构建更复杂Web应用架构（如LAMP）的基础。