# Linux运维：RHCE：Apache配置功能限制与虚拟主机 🚀

![](img/f8cee4f9929faaacc37d793925e03736_1.png)

![](img/f8cee4f9929faaacc37d793925e03736_3.png)

在本节课中，我们将学习如何修改Apache的配置文件，以实现多种高级功能，包括修改网站根目录、设置访问权限控制、配置别名（虚拟目录）、实现用户认证，以及基于不同IP、域名和端口搭建虚拟主机。

![](img/f8cee4f9929faaacc37d793925e03736_5.png)

![](img/f8cee4f9929faaacc37d793925e03736_7.png)

---

## 修改网站根目录与访问控制 🔧

上一节我们介绍了Apache的基本安装，本节中我们来看看如何修改其核心配置。

首先，我们可以修改网站的默认根目录。这通过编辑Apache的主配置文件 `/etc/httpd/conf/httpd.conf` 实现。

![](img/f8cee4f9929faaacc37d793925e03736_9.png)

找到第119行附近的 `DocumentRoot` 指令，将其值修改为你想要的目录路径，例如 `/var/www/html/bbs`。

![](img/f8cee4f9929faaacc37d793925e03736_11.png)

```apache
DocumentRoot "/var/www/html/bbs"
```

![](img/f8cee4f9929faaacc37d793925e03736_13.png)

修改后，需要确保新目录存在且Apache进程对其有读取权限。

![](img/f8cee4f9929faaacc37d793925e03736_15.png)

接下来，我们需要对该目录进行访问控制。配置通常位于 `<Directory>` 区块内。

![](img/f8cee4f9929faaacc37d793925e03736_17.png)

![](img/f8cee4f9929faaacc37d793925e03736_19.png)

以下是常见的访问控制参数：
*   **`Options -Indexes`**：禁止列出目录内容（防止目录浏览）。
*   **`AllowOverride None`**：禁止使用 `.htaccess` 文件覆盖此处的配置。
*   **`Require all granted`**：允许所有访问（默认）。
*   **`Order deny,allow`** 与 **`Deny from ...`** / **`Allow from ...`**：用于2.2版本的基于来源IP的访问控制（在2.4版本中已被 `Require` 指令取代）。

在2.4及以上版本中，推荐使用 `Require` 指令进行更清晰的访问控制：
*   **`Require ip 192.168.2.0/24`**：仅允许指定IP网段访问。
*   **`Require host .baidu.com`**：仅允许来自特定域名的访问。
*   **`Require all denied`**：拒绝所有访问。

![](img/f8cee4f9929faaacc37d793925e03736_21.png)

![](img/f8cee4f9929faaacc37d793925e03736_23.png)

**注意**：当 `Allow` 和 `Deny` 指令（2.2风格）同时存在时，默认以 `Order` 指令定义的顺序和最终规则为准。在2.4版本中，规则冲突时，通常以后出现的 `Require` 指令为准。

![](img/f8cee4f9929faaacc37d793925e03736_25.png)

![](img/f8cee4f9929faaacc37d793925e03736_27.png)

![](img/f8cee4f9929faaacc37d793925e03736_29.png)

配置完成后，需要重启Apache服务使更改生效。

---

## 配置别名（虚拟目录）与目录浏览 📁

![](img/f8cee4f9929faaacc37d793925e03736_31.png)

别名（Alias）功能允许你将一个URL路径映射到文件系统上的任意目录，这常被称为虚拟目录。

配置别名需要在配置文件中（通常在虚拟主机配置或主配置的特定区域）添加 `Alias` 指令。

例如，将URL路径 `/phpdata` 映射到物理目录 `/usr/local/phpdata`：

```apache
Alias /phpdata "/usr/local/phpdata"
<Directory "/usr/local/phpdata">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
```

在上述 `<Directory>` 区块中，`Options Indexes` 参数允许当目录中没有默认首页文件（如 `index.html`）时，显示目录列表。如果移除 `Indexes`，访问此类目录将返回“403 Forbidden”错误。

![](img/f8cee4f9929faaacc37d793925e03736_33.png)

虚拟目录非常有用，它允许你将不同物理路径下的内容整合到同一个网站中，而无需移动文件。

![](img/f8cee4f9929faaacc37d793925e03736_35.png)

---

![](img/f8cee4f9929faaacc37d793925e03736_37.png)

![](img/f8cee4f9929faaacc37d793925e03736_38.png)

![](img/f8cee4f9929faaacc37d793925e03736_40.png)

## 实现用户认证 🔐

![](img/f8cee4f9929faaacc37d793925e03736_42.png)

我们可以为特定目录配置用户认证，要求访问者输入用户名和密码。

首先，在对应目录的 `<Directory>` 区块中启用认证。以下是一个配置示例：

```apache
<Directory "/var/www/html/secure-area">
    AuthType Basic
    AuthName "Restricted Area"
    AuthUserFile /etc/httpd/conf/.htpasswd
    Require valid-user
</Directory>
```

![](img/f8cee4f9929faaacc37d793925e03736_44.png)

参数说明：
*   **`AuthType Basic`**：指定使用基本认证。
*   **`AuthName`**：设置认证领域的提示信息。
*   **`AuthUserFile`**：指定存储用户名和密码的文件路径。
*   **`Require valid-user`**：要求有效用户才能访问。你也可以指定具体用户，如 `Require user tom jerry`。

接下来，使用 `htpasswd` 工具创建密码文件并添加用户。首次创建需使用 `-c` 参数：

![](img/f8cee4f9929faaacc37d793925e03736_46.png)

```bash
htpasswd -c /etc/httpd/conf/.htpasswd tom
```

系统会提示为用户 `tom` 设置密码。添加第二个用户时，无需 `-c` 参数，以免覆盖原文件：

![](img/f8cee4f9929faaacc37d793925e03736_48.png)

```bash
htpasswd /etc/httpd/conf/.htpasswd jerry
```

![](img/f8cee4f9929faaacc37d793925e03736_50.png)

密码文件中的密码是加密存储的。配置完成后，重启Apache。访问该目录时，浏览器会弹出登录框要求认证。

![](img/f8cee4f9929faaacc37d793925e03736_52.png)

![](img/f8cee4f9929faaacc37d793925e03736_54.png)

![](img/f8cee4f9929faaacc37d793925e03736_56.png)

---

![](img/f8cee4f9929faaacc37d793925e03736_58.png)

![](img/f8cee4f9929faaacc37d793925e03736_60.png)

## 配置虚拟主机 🌐

虚拟主机技术允许在一台Apache服务器上运行多个网站。有三种主要类型：基于IP、基于端口和基于域名。

![](img/f8cee4f9929faaacc37d793925e03736_62.png)

![](img/f8cee4f9929faaacc37d793925e03736_64.png)

### 基于IP的虚拟主机
此方法为每个网站分配独立的IP地址。

1.  为服务器网卡添加额外的IP地址。
2.  在 `/etc/httpd/conf.d/` 目录下创建配置文件（如 `vhost-ip.conf`）。
3.  配置如下：

```apache
<VirtualHost 192.168.2.63:80>
    DocumentRoot /var/www/html/site1
    ServerName www.site1.com
    ErrorLog logs/site1-error_log
    CustomLog logs/site1-access_log common
</VirtualHost>

![](img/f8cee4f9929faaacc37d793925e03736_66.png)

<VirtualHost 192.168.2.36:80>
    DocumentRoot /var/www/html/site2
    ServerName www.site2.com
    ErrorLog logs/site2-error_log
    CustomLog logs/site2-access_log common
</VirtualHost>
```

访问不同IP将显示不同网站的内容。

### 基于域名的虚拟主机（最常用）
此方法使用同一个IP地址，通过不同的域名来区分网站。

![](img/f8cee4f9929faaacc37d793925e03736_68.png)

1.  配置DNS或本地hosts文件，将不同域名解析到服务器IP。
2.  创建配置文件（如 `vhost-name.conf`）：

```apache
<VirtualHost *:80>
    DocumentRoot /var/www/html/site1
    ServerName www.learnit.com
</VirtualHost>

<VirtualHost *:80>
    DocumentRoot /var/www/html/site2
    ServerName bbs.learnit.com
</VirtualHost>
```

在客户端电脑的 `hosts` 文件（Windows: `C:\Windows\System32\drivers\etc\hosts`）中添加映射：
```
192.168.2.63 www.learnit.com
192.168.2.63 bbs.learnit.com
```

访问不同域名将显示对应的网站。

![](img/f8cee4f9929faaacc37d793925e03736_70.png)

![](img/f8cee4f9929faaacc37d793925e03736_72.png)

### 基于端口的虚拟主机
此方法通过不同的端口号来区分网站。

修改配置文件，为不同虚拟主机指定不同的监听端口：

![](img/f8cee4f9929faaacc37d793925e03736_74.png)

```apache
<VirtualHost *:80>
    DocumentRoot /var/www/html/site1
    ServerName www.learnit.com
</VirtualHost>

<VirtualHost *:8080>
    DocumentRoot /var/www/html/site2
    ServerName www.learnit.com
</VirtualHost>
```

![](img/f8cee4f9929faaacc37d793925e03736_76.png)

![](img/f8cee4f9929faaacc37d793925e03736_78.png)

同时，需要在主配置文件中使用 `Listen` 指令确保Apache监听 `8080` 端口。访问时需在URL中指定端口号，如 `http://www.learnit.com:8080`。

![](img/f8cee4f9929faaacc37d793925e03736_80.png)

---

![](img/f8cee4f9929faaacc37d793925e03736_82.png)

## 总结 📝

![](img/f8cee4f9929faaacc37d793925e03736_84.png)

本节课中我们一起学习了Apache Web服务器的高级配置管理。我们掌握了如何修改网站根目录并设置精细的访问控制策略；学会了使用别名功能创建虚拟目录，并控制目录浏览行为；实现了基本的用户认证，以保护特定目录；最后，深入探讨了基于IP、域名和端口三种方式配置虚拟主机，从而实现在单台服务器上托管多个网站。这些技能是Linux运维，特别是RHCE认证考核中Web服务管理的重要组成部分。