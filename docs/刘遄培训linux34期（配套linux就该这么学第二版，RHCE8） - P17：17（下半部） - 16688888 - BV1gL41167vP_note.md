# Linux培训：第17章：Apache虚拟主机配置实战 🚀

![](img/a2d95def983c681bbbdad00eead60412_1.png)

![](img/a2d95def983c681bbbdad00eead60412_3.png)

在本节课中，我们将学习Apache网站服务中三种不同类型的虚拟主机配置方法。通过配置虚拟主机，可以让一台服务器同时承载多个网站，并根据用户访问的域名、IP地址或端口号来提供不同的网站内容。

![](img/a2d95def983c681bbbdad00eead60412_5.png)

![](img/a2d95def983c681bbbdad00eead60412_7.png)

![](img/a2d95def983c681bbbdad00eead60412_9.png)

## 概述

![](img/a2d95def983c681bbbdad00eead60412_11.png)

![](img/a2d95def983c681bbbdad00eead60412_13.png)

![](img/a2d95def983c681bbbdad00eead60412_15.png)

Apache HTTP Server（简称Apache）是一款功能强大的Web服务器软件。虚拟主机功能允许我们在单一服务器上运行多个网站，这对于节省硬件资源和简化管理非常有帮助。本节我们将依次学习基于域名、基于IP地址和基于端口号的虚拟主机配置方法。

![](img/a2d95def983c681bbbdad00eead60412_17.png)

![](img/a2d95def983c681bbbdad00eead60412_19.png)

## 基于域名的虚拟主机配置 🌐

![](img/a2d95def983c681bbbdad00eead60412_21.png)

![](img/a2d95def983c681bbbdad00eead60412_23.png)

![](img/a2d95def983c681bbbdad00eead60412_25.png)

上一节我们介绍了Apache服务的基本安装与启动，本节中我们来看看如何配置基于域名的虚拟主机。这种配置方式是最常见的，它通过不同的域名来区分不同的网站。

![](img/a2d95def983c681bbbdad00eead60412_27.png)

![](img/a2d95def983c681bbbdad00eead60412_29.png)

![](img/a2d95def983c681bbbdad00eead60412_31.png)

以下是配置基于域名的虚拟主机的核心步骤：

![](img/a2d95def983c681bbbdad00eead60412_33.png)

![](img/a2d95def983c681bbbdad00eead60412_35.png)

![](img/a2d95def983c681bbbdad00eead60412_37.png)

![](img/a2d95def983c681bbbdad00eead60412_39.png)

1.  **编辑主配置文件**：使用文本编辑器打开Apache的主配置文件 `/etc/httpd/conf/httpd.conf`。
2.  **添加虚拟主机块**：在配置文件末尾（例如第120行之后）添加 `VirtualHost` 配置块。
3.  **指定域名和目录**：在 `VirtualHost` 块内，使用 `ServerName` 指令指定域名，使用 `DocumentRoot` 指令指定该域名对应的网站文件存放目录。
4.  **设置目录权限**：在 `VirtualHost` 块内，使用 `<Directory>` 指令块为网站目录设置访问权限，通常需要添加 `Require all granted` 以允许所有请求。
5.  **复制配置块**：如果有多个域名，复制 `VirtualHost` 配置块并修改其中的 `ServerName` 和 `DocumentRoot` 值。
6.  **重启服务**：保存配置文件后，执行 `systemctl restart httpd` 命令重启Apache服务使配置生效。
7.  **配置SELinux**：如果访问时出现权限错误，可能需要使用 `semanage fcontext` 和 `restorecon` 命令修改网站目录的SELinux安全上下文。

![](img/a2d95def983c681bbbdad00eead60412_41.png)

![](img/a2d95def983c681bbbdad00eead60412_43.png)

![](img/a2d95def983c681bbbdad00eead60412_45.png)

**核心配置示例**：
```apache
<VirtualHost *:80>
    ServerName www.linuxprobe.com
    DocumentRoot /home/wwwroot/linuxprobe
    <Directory /home/wwwroot/linuxprobe>
        Require all granted
    </Directory>
</VirtualHost>
```

配置完成后，在客户端浏览器中访问 `www.linuxprobe.com`，即可看到对应目录下的网站内容。通过这种方式，我们可以轻松地为 `www.linuxcool.com`、`www.linuxdown.com` 等不同域名配置独立的网站。

![](img/a2d95def983c681bbbdad00eead60412_47.png)

![](img/a2d95def983c681bbbdad00eead60412_49.png)

## 基于IP地址的虚拟主机配置 💻

![](img/a2d95def983c681bbbdad00eead60412_51.png)

![](img/a2d95def983c681bbbdad00eead60412_53.png)

![](img/a2d95def983c681bbbdad00eead60412_55.png)

![](img/a2d95def983c681bbbdad00eead60412_57.png)

在掌握了基于域名的配置后，我们来看看基于IP地址的虚拟主机。这种方式为服务器的一个网卡绑定多个IP地址，每个IP地址对应一个独立的网站。

![](img/a2d95def983c681bbbdad00eead60412_59.png)

以下是配置基于IP地址的虚拟主机的核心步骤：

1.  **为网卡添加多个IP地址**：编辑网卡配置文件（如 `/etc/sysconfig/network-scripts/ifcfg-ens160`），添加 `IPADDR1`、`IPADDR2` 等参数来绑定多个IP地址（例如 `192.168.10.10`， `.20`， `.30`）。然后执行 `nmcli connection reload` 和 `nmcli connection up` 命令使配置生效。
2.  **创建网站目录**：为每个IP地址创建独立的网站根目录，例如 `/home/wwwroot/10`， `/home/wwwroot/20`， `/home/wwwroot/30`，并在每个目录中创建测试页面。
3.  **编辑主配置文件**：在Apache主配置文件中，为每个IP地址添加一个 `VirtualHost` 块，将 `VirtualHost` 的参数改为具体的IP地址（如 `192.168.10.10:80`），并指定对应的 `DocumentRoot`。
4.  **重启服务并配置SELinux**：重启Apache服务，并同样需要为新增的网站目录配置正确的SELinux安全上下文。

![](img/a2d95def983c681bbbdad00eead60412_61.png)

![](img/a2d95def983c681bbbdad00eead60412_63.png)

**核心配置示例**：
```apache
<VirtualHost 192.168.10.10:80>
    DocumentRoot /home/wwwroot/10
    ...
</VirtualHost>
<VirtualHost 192.168.10.20:80>
    DocumentRoot /home/wwwroot/20
    ...
</VirtualHost>
```

![](img/a2d95def983c681bbbdad00eead60412_65.png)

![](img/a2d95def983c681bbbdad00eead60412_67.png)

![](img/a2d95def983c681bbbdad00eead60412_69.png)

![](img/a2d95def983c681bbbdad00eead60412_71.png)

![](img/a2d95def983c681bbbdad00eead60412_73.png)

![](img/a2d95def983c681bbbdad00eead60412_75.png)

配置并处理好SELinux后，用户访问 `http://192.168.10.10`、`http://192.168.10.20` 等不同IP地址时，就会看到各自独立的网站内容。这就像在一套三居室里为三个租客（网站）分配了三个独立的门牌号（IP地址）。

![](img/a2d95def983c681bbbdad00eead60412_77.png)

## 基于端口号的虚拟主机配置 🔌

![](img/a2d95def983c681bbbdad00eead60412_79.png)

![](img/a2d95def983c681bbbdad00eead60412_81.png)

![](img/a2d95def983c681bbbdad00eead60412_83.png)

![](img/a2d95def983c681bbbdad00eead60412_85.png)

![](img/a2d95def983c681bbbdad00eead60412_87.png)

最后，我们来学习基于端口号的虚拟主机配置。这种方法让用户通过访问同一个IP地址的不同端口号来获取不同的网站内容，常用于一些需要“隐藏”访问入口的场景。

![](img/a2d95def983c681bbbdad00eead60412_89.png)

![](img/a2d95def983c681bbbdad00eead60412_91.png)

![](img/a2d95def983c681bbbdad00eead60412_93.png)

以下是配置基于端口的虚拟主机的核心步骤：

1.  **在配置文件中监听新端口**：在Apache主配置文件中，找到 `Listen` 指令，除了默认的 `80` 端口外，额外添加需要监听的端口，例如 `Listen 6111`， `Listen 6222`， `Listen 6333`。
2.  **创建网站目录**：创建对应的网站目录，如 `/home/wwwroot/6111`， `/home/wwwroot/6222`， `/home/wwwroot/6333`，并放置测试文件。
3.  **配置虚拟主机块**：添加 `VirtualHost` 块，将端口参数改为 `*:6111` 等形式，并指定对应的 `DocumentRoot`。
4.  **配置SELinux放行端口**：这是关键一步。SELinux默认只允许Apache使用少数常见端口。需要使用 `semanage port` 命令将新添加的端口（6111， 6222， 6333）添加到Apache允许的端口列表中。
5.  **重启服务并配置目录上下文**：重启Apache服务，并确保网站目录的SELinux上下文设置正确。

**核心命令示例**：
```bash
# 允许Apache使用6111端口
semanage port -a -t http_port_t -p tcp 6111
# 重启Apache服务
systemctl restart httpd
```

![](img/a2d95def983c681bbbdad00eead60412_95.png)

完成所有配置后，用户访问 `http://服务器IP:6111`、`:6222`、`:6333` 时，就能访问到三个不同的网站。这种方法增加了访问的“隐蔽性”，因为需要知道特定端口号才能访问相应网站。

## 总结

![](img/a2d95def983c681bbbdad00eead60412_97.png)

![](img/a2d95def983c681bbbdad00eead60412_99.png)

![](img/a2d95def983c681bbbdad00eead60412_101.png)

本节课中我们一起学习了Apache网站服务的三种虚拟主机配置方法：
*   **基于域名**：最常用，通过不同域名区分网站，配置灵活。
*   **基于IP地址**：为服务器绑定多个IP，每个IP对应一个网站，逻辑清晰。
*   **基于端口号**：通过不同端口访问不同网站，具有一定隐蔽性。

![](img/a2d95def983c681bbbdad00eead60412_103.png)

![](img/a2d95def983c681bbbdad00eead60412_104.png)

无论采用哪种方式，配置流程都遵循“修改配置文件 -> 设置目录与权限 -> 处理SELinux -> 重启服务验证”这一通用模式。理解并掌握这些方法，你就能轻松地在一台服务器上部署和管理多个网站了。在实际工作中，最常用的是基于域名的虚拟主机。