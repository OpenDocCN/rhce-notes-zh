# Linux就该这么学：第10章：网站服务 🖥️

![](img/0a5980438ebb75ab5f430626fd35c7be_1.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_2.png)

在本节课中，我们将要学习如何在Linux系统中搭建网站服务。我们将从最基础的软件安装开始，逐步学习如何配置网站目录、处理SELinux安全策略，并最终实现一个可访问的网站。

![](img/0a5980438ebb75ab5f430626fd35c7be_4.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_6.png)

---

![](img/0a5980438ebb75ab5f430626fd35c7be_7.png)

## 概述

网站服务是互联网中最基础的服务之一。在Linux系统中，我们通常使用Apache HTTP服务器（其软件包名为`httpd`）来搭建网站。本节课将指导您完成从安装到配置的完整流程，并解释过程中可能遇到的关键概念，如SELinux。

---

![](img/0a5980438ebb75ab5f430626fd35c7be_9.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_10.png)

## 安装网站服务程序

![](img/0a5980438ebb75ab5f430626fd35c7be_12.png)

上一节我们介绍了课程概述，本节中我们来看看如何安装网站服务程序。

![](img/0a5980438ebb75ab5f430626fd35c7be_14.png)

首先，我们需要配置好系统的软件仓库，以便安装软件。配置软件仓库需要四个要素：唯一的标识符、描述信息、软件仓库的路径地址，以及是否启用和校验。

![](img/0a5980438ebb75ab5f430626fd35c7be_16.png)

以下是配置软件仓库的基本步骤：

![](img/0a5980438ebb75ab5f430626fd35c7be_18.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_19.png)

1.  将系统安装光盘镜像连接到虚拟机。
2.  将光盘设备挂载到系统的一个目录（例如 `/media/cdrom`）。
3.  编辑 `/etc/yum.repos.d/` 目录下的仓库配置文件，添加正确的仓库信息。

![](img/0a5980438ebb75ab5f430626fd35c7be_20.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_21.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_23.png)

配置完成后，就可以使用 `yum` 或 `dnf` 命令来安装软件。两者功能类似，`dnf` 是 `yum` 的下一代版本，效率更高。

现在，我们来安装网站服务程序。请注意，软件包名称是 `httpd`，而不是服务名称“Apache”。

![](img/0a5980438ebb75ab5f430626fd35c7be_25.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_26.png)

```bash
yum install httpd
# 或
dnf install httpd
```

![](img/0a5980438ebb75ab5f430626fd35c7be_28.png)

安装过程中，系统会提示确认，输入 `y` 并按回车即可。

![](img/0a5980438ebb75ab5f430626fd35c7be_30.png)

安装完成后，需要启动服务并将其设置为开机自启：

```bash
systemctl start httpd
systemctl enable httpd
```

![](img/0a5980438ebb75ab5f430626fd35c7be_32.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_34.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_35.png)

---

![](img/0a5980438ebb75ab5f430626fd35c7be_36.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_38.png)

## 配置网站内容与访问

![](img/0a5980438ebb75ab5f430626fd35c7be_40.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_42.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_43.png)

软件安装并启动后，我们来配置基本的网站内容。

![](img/0a5980438ebb75ab5f430626fd35c7be_45.png)

默认情况下，网站的根目录位于 `/var/www/html/`。网站的首页文件通常命名为 `index.html`。

以下是创建并访问一个简单网页的步骤：

1.  进入网站根目录。
2.  创建首页文件并写入内容。
3.  通过浏览器访问服务器IP地址查看效果。

![](img/0a5980438ebb75ab5f430626fd35c7be_47.png)

具体操作如下：

![](img/0a5980438ebb75ab5f430626fd35c7be_48.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_49.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_50.png)

```bash
cd /var/www/html/
echo "Welcome to My Website" > index.html
```

![](img/0a5980438ebb75ab5f430626fd35c7be_52.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_54.png)

完成上述操作后，在浏览器中输入服务器的IP地址（例如 `http://192.168.10.10`），即可看到显示的欢迎信息。

![](img/0a5980438ebb75ab5f430626fd35c7be_56.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_57.png)

---

![](img/0a5980438ebb75ab5f430626fd35c7be_58.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_59.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_60.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_61.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_62.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_64.png)

## 修改网站默认目录

![](img/0a5980438ebb75ab5f430626fd35c7be_66.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_67.png)

有时，我们可能需要将网站文件存放在其他目录。这需要修改服务的主配置文件。

![](img/0a5980438ebb75ab5f430626fd35c7be_69.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_70.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_71.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_72.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_73.png)

Apache HTTP服务器的主配置文件是 `/etc/httpd/conf/httpd.conf`。我们需要修改其中的 `DocumentRoot` 参数来指定新的网站根目录。

![](img/0a5980438ebb75ab5f430626fd35c7be_75.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_76.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_77.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_78.png)

例如，将网站目录改为 `/home/wwwroot`：

![](img/0a5980438ebb75ab5f430626fd35c7be_80.png)

1.  创建新目录。
2.  编辑主配置文件，找到 `DocumentRoot` 和对应的 `<Directory>` 标签，将其路径改为新目录。
3.  重启服务使配置生效。

具体命令如下：

![](img/0a5980438ebb75ab5f430626fd35c7be_82.png)

```bash
mkdir /home/wwwroot
# 编辑 /etc/httpd/conf/httpd.conf， 修改 DocumentRoot 和对应的 Directory 路径
systemctl restart httpd
```

![](img/0a5980438ebb75ab5f430626fd35c7be_84.png)

修改并重启服务后，需要将网站首页文件 `index.html` 放入新的目录 `/home/wwwroot` 中。

![](img/0a5980438ebb75ab5f430626fd35c7be_86.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_87.png)

---

![](img/0a5980438ebb75ab5f430626fd35c7be_89.png)

## 理解并配置SELinux

![](img/0a5980438ebb75ab5f430626fd35c7be_91.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_92.png)

修改网站目录后，通过浏览器访问可能会失败。这通常是由于SELinux安全策略的限制。

![](img/0a5980438ebb75ab5f430626fd35c7be_94.png)

SELinux（安全增强式Linux）是一个安全子系统，其核心思想是“让程序只能做它该做的事”。它通过两种机制进行控制：
*   **域（Domain）**：限制服务进程的功能。
*   **安全上下文（Security Context）**：给文件打上标签，限制其能被哪些服务访问。

![](img/0a5980438ebb75ab5f430626fd35c7be_96.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_97.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_98.png)

我们的网站无法访问新目录，是因为新目录的安全上下文与网站服务不匹配。

首先，检查SELinux的状态：

![](img/0a5980438ebb75ab5f430626fd35c7be_100.png)

```bash
getenforce
```

![](img/0a5980438ebb75ab5f430626fd35c7be_101.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_102.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_104.png)

如果状态是 `Enforcing`（强制模式），则会阻止不符合策略的访问。我们可以临时将其设置为 `Permissive`（警告模式）或 `Disabled`（禁用模式）来测试：

![](img/0a5980438ebb75ab5f430626fd35c7be_105.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_107.png)

```bash
setenforce 0 # 临时设置为 Permissive
```

![](img/0a5980438ebb75ab5f430626fd35c7be_109.png)

如果设置为 `Permissive` 后网站可以访问，则问题确实出在SELinux。

![](img/0a5980438ebb75ab5f430626fd35c7be_111.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_112.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_114.png)

**解决方法是为新网站目录设置正确的安全上下文。**

![](img/0a5980438ebb75ab5f430626fd35c7be_115.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_117.png)

1.  查看原始网站目录的安全上下文：
    ```bash
    ls -Zd /var/www/html/
    ```
2.  使用 `semanage` 命令将新目录的上下文修改为与原始目录一致：
    ```bash
    semanage fcontext -a -t httpd_sys_content_t "/home/wwwroot(/.*)?"
    ```
3.  使用 `restorecon` 命令让新的上下文设置立即生效：
    ```bash
    restorecon -Rv /home/wwwroot/
    ```
4.  将SELinux重新设置为强制模式：
    ```bash
    setenforce 1
```

![](img/0a5980438ebb75ab5f430626fd35c7be_119.png)

完成这些步骤后，网站应该可以正常访问。这个流程展示了如何通过修改文件的安全上下文来解决SELinux导致的访问问题。

![](img/0a5980438ebb75ab5f430626fd35c7be_120.png)

---

![](img/0a5980438ebb75ab5f430626fd35c7be_122.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_123.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_124.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_125.png)

## 实现基于域名的虚拟主机

![](img/0a5980438ebb75ab5f430626fd35c7be_127.png)

一台服务器可以承载多个网站，这可以通过“虚拟主机”功能实现。其中，基于域名的虚拟主机是最常见的方式，它允许用户通过访问不同的域名来看到不同的网站内容。

![](img/0a5980438ebb75ab5f430626fd35c7be_128.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_129.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_131.png)

假设我们有三个域名：`www.linuxprobe.com`， `www.linuxcool.com`， `www.linuxdown.com`。我们希望它们指向同一台服务器，但显示不同内容。

**实现步骤如下：**

![](img/0a5980438ebb75ab5f430626fd35c7be_133.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_134.png)

1.  **为每个网站创建独立的目录和首页文件：**
    ```bash
    mkdir -p /home/wwwroot/{linuxprobe,linuxcool,linuxdown}
    echo "Welcome to www.linuxprobe.com" > /home/wwwroot/linuxprobe/index.html
    echo "Welcome to www.linuxcool.com" > /home/wwwroot/linuxcool/index.html
    echo "Welcome to www.linuxdown.com" > /home/wwwroot/linuxdown/index.html
    ```

2.  **配置本地主机解析（在没有真实DNS的情况下用于测试）：**
    编辑 `/etc/hosts` 文件，添加以下行，将域名解析到本机IP。
    ```
    192.168.10.10 www.linuxprobe.com
    192.168.10.10 www.linuxcool.com
    192.168.10.10 www.linuxdown.com
    ```
    *注意：`/etc/hosts` 文件的解析优先级高于外部DNS服务器。*

3.  **配置Apache虚拟主机：**
    在 `/etc/httpd/conf.d/` 目录下创建一个新的配置文件，例如 `vhost.conf`。
    在文件中为每个域名配置一个 `<VirtualHost>` 段，指定其对应的网站目录。
    ```apache
    <VirtualHost *:80>
        ServerName www.linuxprobe.com
        DocumentRoot /home/wwwroot/linuxprobe
        <Directory /home/wwwroot/linuxprobe>
            AllowOverride All
            Require all granted
        </Directory>
    </VirtualHost>

    <VirtualHost *:80>
        ServerName www.linuxcool.com
        DocumentRoot /home/wwwroot/linuxcool
        <Directory /home/wwwroot/linuxcool>
            AllowOverride All
            Require all granted
        </Directory>
    </VirtualHost>

    <VirtualHost *:80>
        ServerName www.linuxdown.com
        DocumentRoot /home/wwwroot/linuxdown
        <Directory /home/wwwroot/linuxdown>
            AllowOverride All
            Require all granted
        </Directory>
    </VirtualHost>
    ```

4.  **重启服务并测试：**
    ```bash
    systemctl restart httpd
    ```
    分别访问 `http://www.linuxprobe.com`， `http://www.linuxcool.com`， `http://www.linuxdown.com`， 应该能看到各自不同的欢迎页面。

![](img/0a5980438ebb75ab5f430626fd35c7be_136.png)

---

![](img/0a5980438ebb75ab5f430626fd35c7be_137.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_138.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_140.png)

## 总结

本节课中我们一起学习了Linux下网站服务（Apache `httpd`）的完整搭建与配置流程。

![](img/0a5980438ebb75ab5f430626fd35c7be_142.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_143.png)

我们从安装 `httpd` 软件包开始，学会了如何部署简单的静态网站。接着，我们探索了如何通过修改主配置文件来变更网站的根目录，并在此过程中遇到了SELinux安全策略的阻拦。通过学习和实践，我们掌握了如何使用 `semanage` 和 `restorecon` 命令来修改文件的安全上下文，从而解决访问问题。

![](img/0a5980438ebb75ab5f430626fd35c7be_144.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_146.png)

最后，我们实现了基于域名的虚拟主机功能，让一台服务器能够承载多个独立的网站。这个过程涉及了目录规划、本地主机解析和虚拟主机配置等多个环节。

![](img/0a5980438ebb75ab5f430626fd35c7be_148.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_149.png)

![](img/0a5980438ebb75ab5f430626fd35c7be_151.png)

通过本章的学习，您应该已经具备了在RHEL/CentOS系统上搭建和配置基础Web服务的能力。记住，理解SELinux的工作原理和配置方法是管理红帽系列Linux系统的关键技能之一。