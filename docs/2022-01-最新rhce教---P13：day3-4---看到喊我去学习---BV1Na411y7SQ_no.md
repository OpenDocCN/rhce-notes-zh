# Linux系统管理：P13：Systemd Unit文件与SSH服务详解

在本节课中，我们将要学习Systemd系统中Unit文件的基本结构、核心配置选项，以及SSH（Secure Shell）服务的工作原理、配置方法和安全实践。课程内容将分为两个主要部分，帮助你理解如何管理系统服务和实现安全的远程访问。

## Systemd Unit文件解析 🔧

上一节我们介绍了Systemd的基础概念，本节中我们来看看其核心配置文件——Unit文件的具体格式和选项。

打开一个Systemd的service unit文件，可以看到其结构。文件内容通常包含几个部分，我们先讲解`[Unit]`部分。

以井号`#`开头的行，其后的内容被认为是注释。

`[Service]`部分用于配置服务本身的行为。`ExecStart`选项用于指定启动服务时要执行的命令或脚本。其值通常为`yes`或`true`以开启服务，`0`、`no`、`off`或`false`则用于关闭服务。

关于时间单位的设置，默认单位是秒。因此，若要使用毫秒或分钟，需要显式指定单位后缀。例如，毫秒用`ms`，分钟用`m`。若不指定，则默认为秒。

所有Unit文件通常由三部分组成：
1.  `[Unit]`：定义与Unit类型无关的通用选项，以及Unit的描述信息和依赖关系。
2.  `[Service]`、`[Socket]`等：根据Unit类型提供特定的配置选项。
3.  `[Install]`：定义Unit的安装信息，例如如何将其启用。

以下是`[Unit]`部分一些常用选项的说明：
*   **`Description=`**：提供此Unit的描述信息。
*   **`After=`**：定义启动顺序，指明本Unit应在哪些Unit之后启动。可以列出多个Unit，如果一行写不下可以换行。
*   **`Requires=`**：定义强依赖关系。被依赖的Unit如果无法激活，本Unit也将无法启动。
*   **`Wants=`**：定义弱依赖关系。被依赖的Unit启动失败不影响本Unit的启动。
*   **`Conflicts=`**：定义Unit间的冲突关系。

`[Service]`部分有几个关键选项：
*   **`Type=`**：定义服务的启动类型。常见类型有：
    *   `simple`（默认）：服务进程为主进程。
    *   `forking`：服务进程通过`fork()`系统调用启动子进程，然后父进程退出。
    *   `oneshot`：进程在执行完工作后退出，不常驻内存。
    *   `idle`：在所有任务顺利执行完毕后才会执行，类似于开机最后执行的服务。
*   **`Environment=`**：设置服务运行时的环境变量。
*   **`Restart=`**：定义服务退出后的重启策略。
    *   `no`（默认）：不重启。
    *   `on-success`：仅在成功退出时重启。
    *   `on-failure`：仅在失败退出时重启。
    *   `always`：总是重启。
*   **`RestartSec=`**：设置重启服务前的等待秒数。
*   **`ExecStop=`**：指定停止Unit时要运行的命令或脚本。
*   **`PrivateTmp=`**：设置为`yes`时，服务会获得私有的`/tmp`和`/var/tmp`目录。

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_1.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_3.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_5.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_7.png)

Unit文件可以使用别名。例如，`httpd.service`除了可以通过`systemctl start httpd`操作，也可以使用`systemctl start httpd.service`。

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_9.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_11.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_13.png)

修改Unit文件后，必须执行以下命令重新加载Systemd配置，否则修改不会生效：
```bash
systemctl daemon-reload
```

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_15.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_17.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_19.png)

Systemd还管理系统的运行级别（target）。`systemctl isolate`命令用于切换运行级别，类似于旧的`init`命令。

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_21.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_23.png)

可以使用`systemctl list-dependencies`命令查看服务的依赖关系，了解系统启动时需要加载的所有模块和服务。

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_25.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_26.png)

禁用服务默认启动可以使用：
```bash
systemctl disable <service_name>
```

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_28.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_30.png)

## SSH服务配置与安全 🛡️

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_32.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_34.png)

上一节我们介绍了Systemd服务管理，本节中我们来看看如何配置和保护SSH服务。

SSH（Secure Shell）是一种用于安全远程登录的协议，它通过加密通信替代了传统的Telnet等不安全的协议。具体的软件实现有OpenSSH（SSH协议的开源实现，Linux发行版默认安装）和Dropbear（一个轻量级实现）。

SSH协议有两个主要版本。V1版本基于CRC-32进行MAC校验，已被证实不安全。V2版本使用双方协商的安全MAC算法，基于DH算法进行密钥交换，并支持RSA或DSA密钥进行身份认证，是目前推荐使用的版本。

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_36.png)

SSH公钥交换的原理如下（基于V2版本）：
1.  客户端向服务器发起连接请求。
2.  服务器将自己的公钥和一个会话ID发送给客户端。
3.  客户端生成一个密钥对，并用服务器的公钥和会话ID计算出一个值，再用服务器的公钥加密此值后发送给服务器。
4.  服务器使用自己的私钥解密，得到该值。
5.  服务器利用解密后的值，计算出客户端的公钥。
6.  最终，双方都拥有自己的密钥对和对方的公钥，之后的所有通信都基于这些密钥进行加密。

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_38.png)

所有通信的密文都通过此类加密-解密方式进行传输。

OpenSSH服务是SSH协议的免费开源实现。其服务器主配置文件是`/etc/ssh/sshd_config`，对应的Systemd Unit文件是`sshd.service`。

客户端使用`ssh`命令进行远程连接。当用户远程连接SSH服务器时，会复制服务器的公钥（通常位于`/etc/ssh/ssh_host_*.pub`）到客户端的`~/.ssh/known_hosts`文件中，用于后续的身份验证。

以下是`ssh`客户端命令的常见用法：
*   连接远程主机：`ssh user@host_ip`
*   指定端口连接：`ssh -p port_num user@host_ip`
*   启用压缩（可能加快网络传输）：`ssh -C user@host_ip`
*   指定用于身份认证的私钥文件：`ssh -i /path/to/private_key user@host_ip`

可以在不登录远程Shell的情况下直接执行命令：
```bash
ssh user@host_ip "ls -l /tmp"
```

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_40.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_42.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_44.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_46.png)

SSH主要有两种登录验证方式：
1.  **基于口令的验证**：服务器将公钥发给客户端，客户端用该公钥加密密码后传回，服务器用私钥解密验证。
2.  **基于密钥的验证（免密登录）**：
    *   在客户端生成密钥对：`ssh-keygen -t rsa`
    *   将客户端的公钥上传至服务器：`ssh-copy-id user@host_ip`
    *   之后登录时，服务器会用存储的公钥加密一个挑战码发送给客户端，客户端用私钥解密后发回，验证通过即可登录，无需输入密码。

以下是SSH服务器（`sshd_config`）的一些重要安全配置选项：
*   **`Port`**：更改默认端口（22）。
*   **`ListenAddress`**：指定监听的IP地址，`0.0.0.0`表示监听所有地址。
*   **`PermitRootLogin`**：是否允许root用户直接登录，建议设置为`no`。
*   **`PubkeyAuthentication`**：是否允许公钥认证，默认为`yes`。
*   **`PasswordAuthentication`**：是否允许密码认证，可设置为`no`以强制使用密钥登录。
*   **`PermitEmptyPasswords`**：是否允许空密码，必须为`no`。
*   **`ClientAliveInterval`** 和 **`ClientAliveCountMax`**：组合设置连接会话超时时间。
*   **`AllowUsers`** / **`DenyUsers`**：允许或拒绝特定用户登录。
*   **`AllowGroups`** / **`DenyGroups`**：允许或拒绝特定用户组登录。

为了提高SSH连接速度，可以禁用DNS反向解析和GSSAPI认证：
```
UseDNS no
GSSAPIAuthentication no
```

SSH服务安全最佳实践包括：
*   使用非默认端口。
*   禁止root用户直接远程登录。
*   限制可登录的用户或用户组。
*   设置空闲会话超时时间。
*   利用防火墙限制SSH访问来源IP。
*   若使用密码认证，强制使用强密码策略。
*   考虑使用Fail2ban等工具防止暴力破解。

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_48.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_50.png)

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_52.png)

---

![](img/8c5c8f2fb8a04ba1c0fef6c2bbf7f432_54.png)

本节课中我们一起学习了Systemd Unit文件的结构与关键配置项，掌握了通过Systemd管理服务生命周期的方法。同时，我们深入探讨了SSH服务的工作原理、客户端连接方式、基于密钥的免密登录配置，以及一系列增强SSH服务器安全性的配置策略和实践。这些知识是进行Linux系统管理和维护的基础技能。