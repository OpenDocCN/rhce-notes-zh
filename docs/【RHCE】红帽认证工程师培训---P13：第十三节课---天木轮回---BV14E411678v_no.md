# RHCE红帽认证工程师培训课程：P13：配置Apache网站服务与虚拟主机

![](img/7691a5b2fb136a8ce26f042eed7e093b_1.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_3.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_4.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_5.png)

## 概述

在本节课中，我们将深入学习Apache网站服务（HTTPD）的进阶配置。课程将涵盖如何为系统用户创建个人主页、如何为网站添加密码验证，以及如何通过多种方式（基于IP地址、域名、端口号）在同一台服务器上部署多个网站（虚拟主机）。我们还将学习如何配置SELinux策略以允许这些服务正常运行。这些知识对于构建灵活、安全的Web服务器环境至关重要。

---

![](img/7691a5b2fb136a8ce26f042eed7e093b_7.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_9.png)

## 10.4：配置个人用户主页

上一节我们介绍了如何安装Apache服务并修改其默认网站目录。本节中，我们来看看如何为系统中的每个用户创建独立的个人主页。

![](img/7691a5b2fb136a8ce26f042eed7e093b_11.png)

这个功能允许用户在自己的家目录下存放网页文件，并通过特定的URL格式进行访问。虽然在实际生产环境中应用较少，但它是一个很好的练习，能帮助我们深入理解Apache的配置方法和SELinux安全上下文的设置。

以下是配置个人用户主页的步骤：

![](img/7691a5b2fb136a8ce26f042eed7e093b_13.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_15.png)

1.  **安装Apache服务**：首先确保`httpd`服务已安装。
    ```bash
    yum install -y httpd
    ```

2.  **修改主配置文件**：Apache的主配置文件是`/etc/httpd/conf/httpd.conf`，它包含了服务最重要的参数。但更常见的做法是修改其包含的子配置文件。
    *   我们需要编辑`/etc/httpd/conf.d/userdir.conf`文件。
    *   找到第17行 `UserDir disabled`，将其注释掉（在行首添加`#`），以启用个人用户主页功能。
    *   找到第24行 `UserDir public_html`，这定义了用户存放网站数据的目录名称（默认为`public_html`）。

3.  **创建网站目录并设置权限**：
    *   切换到普通用户（例如`linuxprobe`）。
    ```bash
    su - linuxprobe
    ```
    *   在用户家目录下创建`public_html`目录。
    ```bash
    mkdir public_html
    ```
    *   在该目录下创建首页文件，例如`index.html`。
    ```bash
    echo "This is linuxprobe's homepage" > public_html/index.html
    ```
    *   为家目录设置权限，允许其他用户访问。
    ```bash
    chmod -R 755 /home/linuxprobe
    ```

![](img/7691a5b2fb136a8ce26f042eed7e093b_17.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_19.png)

4.  **配置SELinux**：默认情况下，SELinux会阻止httpd服务访问用户家目录。
    *   首先查看所有与httpd相关的SELinux布尔值策略。
    ```bash
    getsebool -a | grep http
    ```
    *   开启允许httpd访问用户家目录的策略（策略名可能类似`httpd_enable_homedirs`）。
    ```bash
    setsebool -P httpd_enable_homedirs on
    ```
    *   `-P`参数表示永久生效，即使重启后依然有效。

![](img/7691a5b2fb136a8ce26f042eed7e093b_21.png)

5.  **重启服务并测试**：
    *   重启`httpd`服务并设置开机自启。
    ```bash
    systemctl restart httpd
    systemctl enable httpd
    ```
    *   在浏览器中访问 `http://服务器IP地址/~用户名/`（例如 `http://192.168.10.10/~linuxprobe/`），即可看到用户的个人主页。

---

## 10.5：为网站目录添加密码认证

为了保护特定网站目录的内容，我们可以为其添加基础的账号密码认证。这常用于内部网站或需要临时限制访问的场景。

这个功能通过Apache的`htpasswd`工具创建密码文件，并在网站配置中指定认证方式来实现。

以下是配置密码认证的步骤：

1.  **创建密码文件**：使用`htpasswd`命令生成密码文件。`-c`参数表示创建新文件。
    ```bash
    htpasswd -c /etc/httpd/passwd zhangshan
    ```
    *   系统会提示为用户`zhangshan`输入并确认密码。
    *   密码文件路径和用户名可以自定义。

2.  **配置目录访问控制**：在需要保护的目录配置中（可以在主配置文件`httpd.conf`中针对特定`<Directory>`块配置，或创建单独的`.conf`文件），添加以下内容：
    ```apache
    <Directory "/var/www/html/secure">
        AllowOverride None
        AuthType Basic
        AuthName "Private Area, Unauthorized Access Will Trigger Countermeasures."
        AuthUserFile /etc/httpd/passwd
        Require user zhangshan
    </Directory>
    ```
    *   `AuthType Basic`：指定使用基础认证（账号密码）。
    *   `AuthName`：设置浏览器弹出的认证对话框标题。
    *   `AuthUserFile`：指定上一步创建的密码文件路径。
    *   `Require user zhangshan`：指定允许访问的用户。如需允许多个用户，可使用`Require valid-user`。

3.  **重启服务并测试**：
    *   重启`httpd`服务。
    ```bash
    systemctl restart httpd
    ```
    *   访问受保护的目录URL（例如 `http://服务器IP地址/secure/`），浏览器会弹出认证窗口，输入正确的用户名和密码后才能访问。

---

![](img/7691a5b2fb136a8ce26f042eed7e093b_23.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_24.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_26.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_27.png)

## 10.6：基于IP地址的虚拟主机

在实际运维中，一台服务器经常需要承载多个网站。虚拟主机技术可以实现这一目标。第一种方式是基于不同的IP地址来区分网站。

这类似于合租，服务器（大房子）分配不同的IP地址（房间号）给不同的租户（网站）。当用户访问不同IP时，服务器就提供对应的网站内容。

![](img/7691a5b2fb136a8ce26f042eed7e093b_29.png)

以下是配置基于IP地址虚拟主机的步骤：

1.  **为网卡配置多个IP地址**：编辑网卡配置文件（如`/etc/sysconfig/network-scripts/ifcfg-ens160`），添加多个`IPADDR`条目。
    ```bash
    IPADDR0=192.168.10.10
    IPADDR1=192.168.10.20
    IPADDR2=192.168.10.30
    ```
    *   下标从0开始。配置后重启网络服务。

2.  **为每个网站创建独立的目录和内容**：
    ```bash
    mkdir -p /home/wwwroot/ip10 /home/wwwroot/ip20 /home/wwwroot/ip30
    echo "Welcome to 192.168.10.10" > /home/wwwroot/ip10/index.html
    echo "Welcome to 192.168.10.20" > /home/wwwroot/ip20/index.html
    echo "Welcome to 192.168.10.30" > /home/wwwroot/ip30/index.html
    ```

3.  **配置虚拟主机**：在`httpd.conf`主配置文件的合适位置（如约129行后），为每个IP地址添加一个`<VirtualHost>`块。
    ```apache
    <VirtualHost 192.168.10.10>
        DocumentRoot "/home/wwwroot/ip10"
        ServerName www.ip10.com
        <Directory "/home/wwwroot/ip10">
            AllowOverride None
            Require all granted
        </Directory>
    </VirtualHost>
    ```
    *   为`192.168.10.20`和`192.168.10.30`创建类似的配置块，修改对应的`IP地址`、`DocumentRoot`和`ServerName`。

4.  **配置SELinux上下文**：新目录需要正确的SELinux文件上下文标签。
    *   查看默认的网站目录上下文。
    ```bash
    ls -Zd /var/www/html
    ```
    *   将新目录的上下文设置为与默认目录一致。
    ```bash
    semanage fcontext -a -t httpd_sys_content_t "/home/wwwroot(/.*)?"
    restorecon -Rv /home/wwwroot
    ```

5.  **重启服务并测试**：重启`httpd`后，分别访问 `http://192.168.10.10`、`http://192.168.10.20` 和 `http://192.168.10.30`，将看到不同的网页内容。

![](img/7691a5b2fb136a8ce26f042eed7e093b_31.png)

---

![](img/7691a5b2fb136a8ce26f042eed7e093b_33.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_34.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_36.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_37.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_38.png)

## 10.7：基于域名的虚拟主机

![](img/7691a5b2fb136a8ce26f042eed7e093b_40.png)

更常见的方式是基于不同的域名来区分网站。所有网站共享同一个服务器IP地址，Apache根据HTTP请求头中的`Host`字段来决定提供哪个网站的内容。

这就像快递员根据收件人名字（域名）将包裹（HTTP请求）分发给合租屋里的不同租客（网站）。

以下是配置基于域名虚拟主机的步骤：

1.  **本地主机名解析**：在测试环境中，可以通过修改本机的`/etc/hosts`文件，将域名临时指向服务器IP。
    ```bash
    192.168.10.10 www.linuxprobe.com
    192.168.10.10 bbs.linuxprobe.com
    192.168.10.10 tech.linuxprobe.com
    ```

![](img/7691a5b2fb136a8ce26f042eed7e093b_42.png)

2.  **为每个网站创建独立的目录和内容**：
    ```bash
    mkdir -p /home/wwwroot/www /home/wwwroot/bbs /home/wwwroot/tech
    echo "Welcome to www.linuxprobe.com" > /home/wwwroot/www/index.html
    echo "Welcome to bbs.linuxprobe.com" > /home/wwwroot/bbs/index.html
    echo "Welcome to tech.linuxprobe.com" > /home/wwwroot/tech/index.html
    ```

![](img/7691a5b2fb136a8ce26f042eed7e093b_44.png)

3.  **配置虚拟主机**：在`httpd.conf`中为每个域名添加`<VirtualHost>`块。注意，这里的IP地址都是相同的，但`ServerName`不同。
    ```apache
    <VirtualHost 192.168.10.10>
        DocumentRoot "/home/wwwroot/www"
        ServerName www.linuxprobe.com
        <Directory "/home/wwwroot/www">
            AllowOverride None
            Require all granted
        </Directory>
    </VirtualHost>
    ```
    *   为`bbs.linuxprobe.com`和`tech.linuxprobe.com`创建类似的配置块。

![](img/7691a5b2fb136a8ce26f042eed7e093b_46.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_47.png)

4.  **配置SELinux上下文并重启服务**：同样需要为新目录设置正确的SELinux上下文，然后重启`httpd`服务。

![](img/7691a5b2fb136a8ce26f042eed7e093b_49.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_50.png)

5.  **测试**：在浏览器中分别访问 `http://www.linuxprobe.com`、`http://bbs.linuxprobe.com` 和 `http://tech.linuxprobe.com`，将看到不同的网站内容。

---

## 10.8：基于端口号的虚拟主机

![](img/7691a5b2fb136a8ce26f042eed7e093b_52.png)

我们还可以让不同的网站监听不同的端口号。用户通过访问同一个IP地址的不同端口来访问不同的网站。

这个实验综合了修改服务配置和SELinux策略，是本章最具挑战性的部分。

![](img/7691a5b2fb136a8ce26f042eed7e093b_54.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_56.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_58.png)

以下是配置基于端口号虚拟主机的步骤：

![](img/7691a5b2fb136a8ce26f042eed7e093b_60.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_62.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_64.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_66.png)

1.  **修改Apache监听端口**：编辑`httpd.conf`，找到`Listen 80`这一行，在其下方添加新的监听端口。
    ```apache
    Listen 80
    Listen 6111
    Listen 6222
    ```

![](img/7691a5b2fb136a8ce26f042eed7e093b_67.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_69.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_70.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_71.png)

2.  **为每个端口创建网站目录和内容**：
    ```bash
    mkdir -p /home/wwwroot/6111 /home/wwwroot/6222
    echo "Welcome to Port 6111" > /home/wwwroot/6111/index.html
    echo "Welcome to Port 6222" > /home/wwwroot/6222/index.html
    ```

![](img/7691a5b2fb136a8ce26f042eed7e093b_73.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_74.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_75.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_77.png)

3.  **配置虚拟主机**：在`httpd.conf`中为每个端口配置`<VirtualHost>`块，注意在IP地址后需要标明端口号。
    ```apache
    <VirtualHost 192.168.10.10:6111>
        DocumentRoot "/home/wwwroot/6111"
        ServerName www.port6111.com
        <Directory "/home/wwwroot/6111">
            AllowOverride None
            Require all granted
        </Directory>
    </VirtualHost>
    ```
    *   为端口`6222`创建类似的配置块。

4.  **配置SELinux**：这里需要两步操作。
    *   **文件上下文**：为新目录设置正确的SELinux标签。
    ```bash
    semanage fcontext -a -t httpd_sys_content_t "/home/wwwroot/6111(/.*)?"
    semanage fcontext -a -t httpd_sys_content_t "/home/wwwroot/6222(/.*)?"
    restorecon -Rv /home/wwwroot
    ```
    *   **端口策略**：默认SELinux不允许`httpd`监听非标准端口（如6111, 6222）。需要将这两个端口添加到`httpd`服务的允许端口列表中。
    ```bash
    semanage port -l | grep http # 查看当前允许的端口
    semanage port -a -t http_port_t -p tcp 6111
    semanage port -a -t http_port_t -p tcp 6222
    ```

5.  **重启服务并测试**：重启`httpd`服务（如果之前因为SELinux端口策略失败，现在应该可以成功启动）。然后访问 `http://192.168.10.10:6111` 和 `http://192.168.10.10:6222` 进行测试。

---

## 10.9：基于客户端的访问控制

Apache可以基于客户端的一些特征（如浏览器类型、来源IP）进行简单的访问控制。例如，可以只允许特定的浏览器（如Firefox）访问某个目录。

这个功能在实际中通常由专业的Web应用防火墙（WAF）实现，但了解Apache自带的基础功能也有益处。

以下是配置基于浏览器类型访问控制的步骤：

1.  **创建受控目录和内容**：
    ```bash
    mkdir /var/www/html/secure_client
    echo "This directory is for Firefox only." > /var/www/html/secure_client/index.html
    ```

![](img/7691a5b2fb136a8ce26f042eed7e093b_79.png)

2.  **配置访问控制**：在`httpd.conf`中，为特定目录（`<Directory>`）或虚拟主机中添加`<If>`条件判断。
    ```apache
    <Directory "/var/www/html/secure_client">
        AllowOverride None
        SetEnvIf User-Agent ".*Firefox.*" ffclient
        Order deny,allow
        Deny from all
        Allow from env=ffclient
    </Directory>
    ```
    *   `SetEnvIf`：根据`User-Agent`请求头（包含浏览器信息）判断，如果包含`Firefox`则设置一个环境变量`ffclient`。
    *   `Order deny,allow`：指定处理顺序，先拒绝所有，再允许特定条件。
    *   `Deny from all`：默认拒绝所有访问。
    *   `Allow from env=ffclient`：允许设置了`ffclient`环境变量的请求（即使用Firefox浏览器的请求）。

3.  **重启服务并测试**：重启`httpd`后，使用Firefox浏览器访问该目录可以正常显示，而使用其他浏览器（如Chrome, IE）访问则会收到`403 Forbidden`错误。

![](img/7691a5b2fb136a8ce26f042eed7e093b_81.png)

---

## 总结

![](img/7691a5b2fb136a8ce26f042eed7e093b_83.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_84.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_86.png)

![](img/7691a5b2fb136a8ce26f042eed7e093b_88.png)

本节课中我们一起学习了Apache网站服务的多项高级配置。我们从配置个人用户主页开始，逐步深入到为网站添加密码保护、以及通过三种不同的方式（基于IP、域名、端口）实现虚拟主机来承载多个网站。在整个过程中，我们反复实践了SELinux的配置，包括修改文件的安全上下文标签和调整服务的布尔值策略与端口策略。最后，我们还了解了如何基于客户端信息进行基础的访问控制。这些技能是构建和管理复杂Web服务器环境的基础，希望大家通过实验熟练掌握。