# Linux就该这么学：第34期：第18章：Apache网站服务配置实战

![](img/d31064b3dd3ab3fce0e791a9180643c5_1.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_3.png)

在本节课中，我们将学习如何配置Apache HTTP服务器，实现基于域名、IP地址和端口号的虚拟主机功能。通过这三个实验，你将掌握如何让一台服务器承载多个独立的网站。

![](img/d31064b3dd3ab3fce0e791a9180643c5_5.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_7.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_9.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_11.png)

## 概述

![](img/d31064b3dd3ab3fce0e791a9180643c5_13.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_15.png)

Apache HTTP服务器是Linux平台上最流行的Web服务器软件之一。通过配置虚拟主机，我们可以让一台物理服务器同时运行多个网站，并根据用户访问的域名、IP地址或端口号，将请求导向不同的网站内容。本节课程将分步讲解这三种配置方法。

![](img/d31064b3dd3ab3fce0e791a9180643c5_17.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_19.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_21.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_23.png)

## 基于域名的虚拟主机配置

![](img/d31064b3dd3ab3fce0e791a9180643c5_25.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_27.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_29.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_31.png)

上一节我们介绍了Apache服务的基本安装与启动。本节中，我们来看看如何配置基于域名的虚拟主机。这种配置允许用户通过访问不同的域名（如 `www.linuxprobe.com` 和 `www.linuxcool.com`）来访问同一台服务器上的不同网站。

![](img/d31064b3dd3ab3fce0e791a9180643c5_33.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_35.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_37.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_39.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_41.png)

以下是配置基于域名虚拟主机的核心步骤：

1.  **编辑Apache主配置文件**：找到并编辑 `/etc/httpd/conf/httpd.conf` 文件。
2.  **添加虚拟主机配置块**：在文件末尾（例如第120行之后）添加类似以下的配置。每个 `<VirtualHost>` 块对应一个域名。
    ```apache
    <VirtualHost 192.168.10.10:80>
        ServerName www.linuxprobe.com
        DocumentRoot /home/wwwroot/linuxprobe
        <Directory /home/wwwroot/linuxprobe>
            AllowOverride None
            Require all granted
        </Directory>
    </VirtualHost>
    ```
    *   `ServerName`：指定该虚拟主机对应的域名。
    *   `DocumentRoot`：指定该域名网站文件的存放根目录。
    *   `<Directory>` 块：用于设置对网站目录的访问权限。`Require all granted` 表示允许所有请求。
3.  **创建网站目录和内容**：为每个域名创建对应的网站根目录，并在其中放置网页文件（如 `index.html`）。
    ```bash
    mkdir -p /home/wwwroot/linuxprobe
    echo "Welcome to www.linuxprobe.com" > /home/wwwroot/linuxprobe/index.html
    ```
4.  **配置SELinux安全上下文**：Apache服务在SELinux开启时，需要为网站目录设置正确的安全上下文标签，否则会提示权限不足。
    ```bash
    semanage fcontext -a -t httpd_sys_content_t "/home/wwwroot(/.*)?"
    restorecon -Rv /home/wwwroot/
    ```
    *   `semanage fcontext`：修改文件默认安全上下文。
    *   `restorecon`：恢复文件的安全上下文。
5.  **重启Apache服务并测试**：重启服务使配置生效，然后在浏览器中访问配置的域名进行测试。
    ```bash
    systemctl restart httpd
    ```

![](img/d31064b3dd3ab3fce0e791a9180643c5_43.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_45.png)

**注意**：为了让域名解析生效，你需要在客户端（或测试机）的 `/etc/hosts` 文件中添加域名与服务器IP的映射关系，例如：
```
192.168.10.10 www.linuxprobe.com www.linuxcool.com www.linuxdown.com
```

![](img/d31064b3dd3ab3fce0e791a9180643c5_47.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_49.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_51.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_53.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_55.png)

## 基于IP地址的虚拟主机配置

在掌握了基于域名的配置后，我们来看看基于IP地址的虚拟主机。这种方法要求服务器拥有多个IP地址，用户通过访问不同的IP来访问不同的网站。

![](img/d31064b3dd3ab3fce0e791a9180643c5_57.png)

以下是配置基于IP地址虚拟主机的核心步骤：

![](img/d31064b3dd3ab3fce0e791a9180643c5_59.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_61.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_63.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_65.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_67.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_69.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_71.png)

1.  **为网卡添加多个IP地址**：编辑网卡配置文件（如 `/etc/sysconfig/network-scripts/ifcfg-ens160`），添加多个 `IPADDR` 字段。
    ```ini
    IPADDR0=192.168.10.10
    IPADDR1=192.168.10.20
    IPADDR2=192.168.10.30
    ```
    然后重启网络服务或重新加载连接。
    ```bash
    nmcli connection reload
    nmcli connection up ens160
    ```
2.  **创建网站目录和内容**：为每个IP地址创建对应的网站目录和内容。
    ```bash
    mkdir /home/wwwroot/{10,20,30}
    echo "IP 192.168.10.10" > /home/wwwroot/10/index.html
    echo "IP 192.168.10.20" > /home/wwwroot/20/index.html
    echo "IP 192.168.10.30" > /home/wwwroot/30/index.html
    ```
3.  **编辑Apache主配置文件**：在配置文件中为每个IP地址添加一个 `<VirtualHost>` 块。
    ```apache
    <VirtualHost 192.168.10.10:80>
        DocumentRoot /home/wwwroot/10
        <Directory /home/wwwroot/10>
            Require all granted
        </Directory>
    </VirtualHost>
    <!-- 为 192.168.10.20 和 192.168.10.30 添加类似配置 -->
    ```
4.  **配置SELinux安全上下文**：同样需要为新的网站目录设置安全上下文。
    ```bash
    semanage fcontext -a -t httpd_sys_content_t "/home/wwwroot/10(/.*)?"
    semanage fcontext -a -t httpd_sys_content_t "/home/wwwroot/20(/.*)?"
    semanage fcontext -a -t httpd_sys_content_t "/home/wwwroot/30(/.*)?"
    restorecon -Rv /home/wwwroot/
    ```
5.  **重启服务并测试**：重启Apache服务，然后在浏览器中直接访问不同的IP地址进行测试。

![](img/d31064b3dd3ab3fce0e791a9180643c5_73.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_75.png)

## 基于端口号的虚拟主机配置

![](img/d31064b3dd3ab3fce0e791a9180643c5_77.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_78.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_80.png)

最后，我们来学习基于端口号的虚拟主机配置。这种方法允许用户通过访问同一个IP地址的不同端口（如6111、6222、6333）来访问不同的网站，常用于内部服务或特定应用。

![](img/d31064b3dd3ab3fce0e791a9180643c5_82.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_84.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_86.png)

以下是配置基于端口号虚拟主机的核心步骤：

1.  **编辑Apache主配置文件**：首先，需要让Apache监听额外的端口。在配置文件中找到 `Listen` 指令，添加需要监听的端口。
    ```apache
    Listen 80
    Listen 6111
    Listen 6222
    Listen 6333
    ```
2.  **添加虚拟主机配置块**：为每个端口添加一个 `<VirtualHost>` 块，指定不同的 `DocumentRoot`。
    ```apache
    <VirtualHost 192.168.10.10:6111>
        DocumentRoot /home/wwwroot/6111
        <Directory /home/wwwroot/6111>
            Require all granted
        </Directory>
    </VirtualHost>
    <!-- 为端口 6222 和 6333 添加类似配置 -->
    ```
3.  **创建网站目录和内容**：为每个端口创建对应的网站目录和内容。
    ```bash
    mkdir /home/wwwroot/{6111,6222,6333}
    echo "Port 6111" > /home/wwwroot/6111/index.html
    ```
4.  **配置SELinux放行端口**：SELinux默认只允许Apache使用少数几个端口（如80、443）。我们需要手动添加对新端口的放行策略。
    ```bash
    semanage port -a -t http_port_t -p tcp 6111
    semanage port -a -t http_port_t -p tcp 6222
    semanage port -a -t http_port_t -p tcp 6333
    ```
    *   `semanage port`：管理SELinux端口策略。
5.  **配置SELinux安全上下文**：同样需要为网站目录设置安全上下文。
    ```bash
    semanage fcontext -a -t httpd_sys_content_t "/home/wwwroot/6111(/.*)?"
    restorecon -Rv /home/wwwroot/
    ```
6.  **重启服务并测试**：重启Apache服务，然后在浏览器中通过 `http://服务器IP:端口号` 的形式访问不同网站。

![](img/d31064b3dd3ab3fce0e791a9180643c5_88.png)

## 总结

![](img/d31064b3dd3ab3fce0e791a9180643c5_90.png)

![](img/d31064b3dd3ab3fce0e791a9180643c5_92.png)

本节课中我们一起学习了Apache HTTP服务器的三种虚拟主机配置方法：
*   **基于域名**：最常用的方式，通过不同的域名区分网站，共享同一个IP地址。
*   **基于IP地址**：通过服务器绑定的不同IP地址来区分网站。
*   **基于端口号**：通过访问同一个IP的不同端口来区分网站，适用于特定场景。

![](img/d31064b3dd3ab3fce0e791a9180643c5_94.png)

无论采用哪种方式，配置流程都遵循相似的步骤：**修改主配置文件、创建网站目录与内容、处理SELinux策略（安全上下文和端口）、重启服务、测试验证**。理解这个通用流程，对于未来配置其他Linux服务也大有裨益。