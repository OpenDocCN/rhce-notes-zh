# Linux培训第34期（配套《Linux就该这么学》第二版，RHCE8）：P16：17（上半部） - 网站服务基础配置与SELinux管理

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_1.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_3.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_5.png)

## 概述

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_7.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_9.png)

在本节课中，我们将要学习如何在Linux系统中搭建和配置Apache网站服务。课程将涵盖从软件包安装、基本配置、网站目录变更，到处理SELinux安全子系统对网站服务访问限制的完整流程。我们还会学习个人用户主页功能和为网站添加密码认证的方法。

---

## 网站服务简介与安装

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_11.png)

上一节我们回顾了软件仓库的配置。本节中我们来看看如何安装网站服务。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_13.png)

网站服务是能够让用户通过浏览器访问到文本、视频或图片等资源的服务程序。在Linux系统中，我们通常使用Apache（其软件包名称为`httpd`）来搭建网站服务。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_15.png)

安装网站服务前，需要确保软件仓库已正确配置。安装命令如下：

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_17.png)

```bash
dnf install httpd
```

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_19.png)

安装完成后，需要启动服务并将其加入开机自启：

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_21.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_23.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_25.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_27.png)

```bash
systemctl restart httpd
systemctl enable httpd
```

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_29.png)

此时，在浏览器中输入服务器的IP地址（例如 `http://192.168.10.10`），如果看到Apache的测试页面，说明服务已成功运行。这个页面是**默认页面**，而非报错页面。它出现的原因可能是网站目录内没有数据，或当前用户没有访问权限。

---

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_31.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_33.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_35.png)

## 配置网站根目录与默认首页

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_37.png)

上一节我们启动了网站服务。本节中我们来看看如何修改网站的默认存储位置和首页文件。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_39.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_41.png)

Apache服务的网站数据默认存放在 `/var/www/html/` 目录下。网站的首页文件通常命名为 `index.html`。

以下是创建并访问一个简单网站的基本步骤：

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_43.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_45.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_47.png)

1.  进入默认网站目录。
2.  创建首页文件 `index.html` 并写入内容。
3.  通过浏览器访问服务器IP查看效果。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_49.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_51.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_53.png)

操作示例：
```bash
cd /var/www/html/
echo "Welcome to linuxprobe.com" > index.html
```
完成上述操作后，刷新浏览器页面即可看到自定义内容。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_55.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_57.png)

---

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_59.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_61.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_63.png)

## 变更网站根目录及遇到的问题

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_65.png)

上一节我们学会了创建简单的静态网站。本节中我们来看看如果希望将网站数据存放在其他目录（例如 `/home/wwwroot`），应该如何配置，以及会遇到什么问题。

变更网站根目录需要修改Apache的主配置文件 `/etc/httpd/conf/httpd.conf`。

1.  **创建新目录**：
    ```bash
    mkdir /home/wwwroot
    ```

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_67.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_69.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_71.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_73.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_75.png)

2.  **修改配置文件**：
    找到文件中 `DocumentRoot` 参数（约在第122行），将其值修改为新目录的路径：
    ```
    DocumentRoot "/home/wwwroot"
    ```
    同时，需要找到紧随其后的 `<Directory>` 区块，将其中的路径也修改为 `/home/wwwroot`。

3.  **重启服务使配置生效**：
    ```bash
    systemctl restart httpd
    ```

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_77.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_79.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_81.png)

4.  **在新目录创建首页文件**：
    ```bash
    echo "Welcome to linuxcool.com" > /home/wwwroot/index.html
    ```

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_83.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_85.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_87.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_89.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_91.png)

完成以上步骤后访问网站，可能会发现页面无法显示，并提示“Forbidden”。这通常不是文件权限问题（已设置`755`），而是由于**SELinux**的安全限制。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_93.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_95.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_97.png)

---

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_99.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_101.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_103.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_105.png)

## 初识SELinux安全子系统

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_107.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_109.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_111.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_113.png)

上一节我们遇到了因变更目录导致网站无法访问的问题。本节中我们来看看这个“幕后黑手”——SELinux。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_115.png)

SELinux（Security-Enhanced Linux）是一个由美国国家安全局和开源社区联合开发的安全子系统。它在传统的文件权限（读、写、执行）之外，提供了更细粒度的强制访问控制。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_117.png)

其核心思想是**让程序只能做它该做的事情**。主要通过两种机制实现：
1.  **域（Domain）限制**：限制服务进程的功能，例如网站服务只能访问网络端口和特定类型的文件。
2.  **安全上下文（Security Context）**：给文件、目录等资源打上标签，规定哪些服务域可以访问它。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_119.png)

SELinux有三种运行模式：
*   **enforcing**：强制模式。违反策略的行为将被阻止并记录。
*   **permissive**：宽容模式。违反策略的行为只会被记录，不会被阻止。
*   **disabled**：关闭模式。SELinux本身被禁用。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_121.png)

查看当前模式：
```bash
getenforce
```
临时切换模式（重启后失效）：
```bash
setenforce 0 # 临时设置为 permissive
setenforce 1 # 临时设置为 enforcing
```

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_123.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_125.png)

在我们的实验场景中，默认的 `/var/www/html/` 目录拥有允许`httpd`服务访问的安全上下文标签，而新建的 `/home/wwwroot` 目录则没有，因此被SELinux阻止访问。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_127.png)

---

## 管理SELinux安全上下文

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_129.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_131.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_133.png)

上一节我们了解了SELinux导致访问失败的原因。本节中我们来看看如何通过修改安全上下文来解决这个问题。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_135.png)

我们可以使用 `semanage` 命令来修改文件或目录的安全上下文，使其与原始网站目录保持一致。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_137.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_139.png)

1.  **查看原始目录的安全上下文**：
    ```bash
    ls -ldZ /var/www/html/
    ```
    记录输出中的安全上下文信息（例如 `httpd_sys_content_t`）。

2.  **修改新目录的安全上下文**：
    ```bash
    semanage fcontext -a -t httpd_sys_content_t "/home/wwwroot(/.*)?"
    ```
    此命令将 `/home/wwwroot` 目录及其下所有文件的安全上下文类型设置为 `httpd_sys_content_t`。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_141.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_143.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_145.png)

3.  **使新的安全上下文立即生效**：
    ```bash
    restorecon -Rv /home/wwwroot
    ```

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_147.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_149.png)

执行以上操作后，再次刷新浏览器，网站内容应该可以正常显示了。这个过程演示了如何通过管理SELinux安全上下文，让服务能够访问非默认路径下的资源。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_151.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_153.png)

---

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_155.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_157.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_159.png)

## 配置个人用户主页功能

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_161.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_163.png)

上一节我们处理了SELinux对文件上下文的限制。本节中我们来看看SELinux如何限制服务功能，并通过配置“个人用户主页功能”来学习如何放行。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_165.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_167.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_169.png)

个人用户主页功能允许系统用户在自己的家目录中（如 `~/public_html`）存放网站数据，并通过 `http://服务器IP/~用户名` 的格式进行访问。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_171.png)

1.  **启用个人用户主页功能**：
    编辑配置文件 `/etc/httpd/conf.d/userdir.conf`，将 `UserDir disabled` 注释掉，并取消 `UserDir public_html` 的注释。
    ```bash
    #UserDir disabled
    UserDir public_html
    ```

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_173.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_175.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_177.png)

2.  **重启Apache服务**：
    ```bash
    systemctl restart httpd
    ```

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_179.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_181.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_183.png)

3.  **创建用户及网站目录**：
    ```bash
    useradd linuxprobe
    su - linuxprobe
    mkdir ~/public_html
    echo "Welcome to linuxdown.com" > ~/public_html/index.html
    exit
    chmod 755 /home/linuxprobe/
    ```

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_185.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_187.png)

4.  **尝试访问并排除SELinux问题**：
    此时访问 `http://192.168.10.10/~linuxprobe` 很可能失败。可以临时关闭SELinux (`setenforce 0`) 测试，若关闭后能访问，则确认是SELinux域策略限制。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_189.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_191.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_193.png)

5.  **放行SELinux策略**：
    我们需要放行Apache服务对用户家目录的访问权限。
    ```bash
    getsebool -a | grep httpd_user
    ```
    找到类似 `httpd_enable_homedirs` 的布尔值，其状态应为 `off`。
    使用 `setsebool` 命令将其永久开启：
    ```bash
    setsebool -P httpd_enable_homedirs on
    ```

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_195.png)

再次访问 `http://192.168.10.10/~linuxprobe`，页面应该可以正常显示。这个实验展示了如何使用 `setsebool` 命令修改SELinux的布尔值策略，从而控制服务的具体功能。

---

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_197.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_199.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_201.png)

## 为网站目录添加密码认证

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_203.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_205.png)

上一节我们配置了个人用户主页。本节中我们来看看如何为这个目录增加一层简单的密码认证，以控制访问权限。

Apache提供了 `htpasswd` 工具来创建管理用于网站认证的用户密码文件，该认证独立于系统用户。

1.  **创建认证密码文件**：
    ```bash
    htpasswd -c /etc/httpd/passwd xiaogao
    ```
    执行后会提示为名为 `xiaogao` 的用户设置密码。密码文件保存在 `/etc/httpd/passwd`。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_207.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_209.png)

2.  **配置目录访问控制**：
    编辑对应用户主页的配置文件（或新建一个），例如 `/etc/httpd/conf.d/userdir_auth.conf`，添加如下内容：
    ```
    <Directory "/home/*/public_html">
        AuthName "Please Input Your Account and Password"
        AuthType Basic
        AuthUserFile /etc/httpd/passwd
        Require user xiaogao
    </Directory>
    ```
    *   `AuthName`：浏览器弹出的认证对话框标题。
    *   `AuthUserFile`：指定密码文件路径。
    *   `Require user`：指定允许访问的用户。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_211.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_213.png)

3.  **重启Apache服务**：
    ```bash
    systemctl restart httpd
    ```

现在，当访问 `http://192.168.10.10/~linuxprobe` 时，浏览器会弹出认证对话框，只有输入正确的用户名 (`xiaogao`) 和密码才能访问。这是一种轻量级的访问控制方法。

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_215.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_217.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_219.png)

---

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_221.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_223.png)

## 总结

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_225.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_227.png)

![](img/d2a8c4e67e0eb4adffebd2a77e1e82cd_229.png)

本节课中我们一起学习了Linux下Apache网站服务的基础配置与管理。我们从安装`httpd`软件包开始，逐步完成了网站根目录的变更、默认首页的创建。随后，我们深入探讨了SELinux安全子系统的工作原理，学习了如何通过`semanage`和`restorecon`命令管理安全上下文，以及如何使用`setsebool`命令控制服务的布尔值策略，从而解决因SELinux导致的网站访问问题。最后，我们还实践了个人用户主页功能的配置，并为其添加了基于密码文件的访问认证。这些知识是构建和管理Web服务器的重要基础。