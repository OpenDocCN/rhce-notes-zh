# Linux运维：RHCE：MySQL介绍与LAMP环境搭建02

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_1.png)

在本节课中，我们将继续完成LAMP环境的搭建，安装网站的前台页面，并学习如何配置Apache虚拟主机以及进行简单的网站压力测试。

## 安装前台页面

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_3.png)

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_5.png)

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_6.png)

上一节我们完成了用户后台管理中心的安装。本节中，我们来看看如何安装网站的前台页面。

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_8.png)

首先，我们需要将前台页面的源码包解压到指定目录。

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_10.png)

```bash
tar -zxvf uc_home.tar.gz -C /path/to/uc_home/
```

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_12.png)

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_14.png)

解压完成后，进入解压目录的`upload`文件夹，这里存放了网站的前台源码。

```bash
cd /path/to/uc_home/upload
```

接下来，将这些源码文件复制到Apache的网站根目录。在复制前，建议先删除根目录下原有的测试文件（如`index.php`），以避免覆盖冲突。

```bash
rm -f /var/www/html/index.php
cp -a * /var/www/html/
```

文件复制完成后，重启Apache服务以使新代码生效。

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_16.png)

```bash
systemctl restart httpd
```

现在，通过浏览器访问服务器的IP地址，会自动跳转到安装页面。安装程序会提示进行一些初始配置。

以下是安装过程中需要执行的步骤：

1.  **重命名配置文件**：根据提示，将网站根目录下的`config.new.php`文件重命名为`config.php`。
    ```bash
    mv /var/www/html/config.new.php /var/www/html/config.php
    ```
2.  **设置文件权限**：安装程序会检测几个目录的读写权限。我们需要将这些目录的所有者改为Apache运行用户（如`apache`或`www-data`），或直接赋予其可写权限。
    ```bash
    chown -R apache:apache /var/www/html/data/
    chown -R apache:apache /var/www/html/uc_client/data/cache/
    ```
3.  **填写安装信息**：在安装向导页面中，依次填写：
    *   **Ucenter地址**：之前安装的后台管理中心地址（如 `http://192.168.2.63/ucenter`）。
    *   **Ucenter创始人密码**：安装后台时设置的密码。
    *   **数据库连接信息**：数据库服务器地址（本地为`localhost`）、用户名（`root`）、密码、字符集（`utf8`）和数据库名。
    *   **设置管理员账号**：为前台站点创建一个管理员账号和密码。

完成以上步骤后，前台页面即安装成功。访问网站根目录即可看到类似博客或空间的前台界面。

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_18.png)

## 配置Apache虚拟主机

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_20.png)

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_22.png)

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_24.png)

当前我们的所有网站都放在同一个目录下。接下来，我们学习如何配置Apache虚拟主机，实现使用不同域名访问不同网站内容。

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_26.png)

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_28.png)

Apache的虚拟主机配置文件通常存放在`/etc/httpd/conf.d/`目录下。主配置文件`/etc/httpd/conf/httpd.conf`会自动包含该目录下所有以`.conf`结尾的文件。

我们可以创建一个新的虚拟主机配置文件，例如`vhost.conf`。

以下是配置两个基于域名的虚拟主机的示例：

```apache
# 第一个虚拟主机：后台管理
<VirtualHost *:80>
    ServerName admin.xuegod63.cn
    DocumentRoot /var/www/html/ucenter
    <Directory /var/www/html/ucenter>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_30.png)

# 第二个虚拟主机：前台网站
<VirtualHost *:80>
    ServerName www.xuegod63.cn
    DocumentRoot /var/www/html
    <Directory /var/www/html>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

*   **`ServerName`**：指定该虚拟主机对应的域名。
*   **`DocumentRoot`**：指定该域名访问时对应的网站文件目录。

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_32.png)

保存配置文件后，需要重启Apache服务。

```bash
systemctl restart httpd
```

由于我们使用的是测试域名，需要在客户端（如Windows）的`hosts`文件（`C:\Windows\System32\drivers\etc\hosts`）中添加域名解析，将域名指向服务器IP。

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_34.png)

```
192.168.2.63 admin.xuegod63.cn
192.168.2.63 www.xuegod63.cn
```

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_36.png)

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_38.png)

配置完成后，即可通过不同的域名访问不同的网站内容。

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_40.png)

虚拟主机主要有三种配置类型：
*   **基于域名**：如上例所示，使用不同的`ServerName`。
*   **基于端口**：使用相同的IP，但监听不同的端口（如8080）。
*   **基于IP**：服务器有多个IP地址，为每个IP配置不同的`DocumentRoot`。

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_42.png)

## 网站压力测试简介

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_44.png)

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_45.png)

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_46.png)

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_48.png)

最后，我们简单了解如何使用工具对新建的网站进行压力测试，模拟高并发访问，检验网站的承载能力。

**注意：此类工具仅可用于对自己拥有权限的服务器进行测试，切勿用于攻击他人网站，这是违法行为。**

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_50.png)

我们可以使用一些现成的工具进行简单的测试。例如，某个测试工具界面允许设置目标URL、协议、线程数等参数。

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_52.png)

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_54.png)

进行测试时，可以观察服务器端的资源使用情况（如使用`top`命令查看CPU和内存负载），以及客户端工具显示的并发连接数。

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_55.png)

压力测试的目的是：
1.  了解网站在不同并发压力下的表现。
2.  找出服务器的性能瓶颈。
3.  为优化服务器配置提供依据。

更专业的压力测试工具还有`ab`（Apache Bench）、`wrk`等，后续课程可能会涉及。

---

![](img/8d067341f153f5853b6ff0cc8ea2ff9c_57.png)

本节课中我们一起学习了如何完成LAMP环境下的前台页面安装，掌握了配置Apache虚拟主机来实现多站点访问的方法，并初步了解了网站压力测试的概念与简单操作。这些技能是Linux运维中搭建和维护Web服务的基础。