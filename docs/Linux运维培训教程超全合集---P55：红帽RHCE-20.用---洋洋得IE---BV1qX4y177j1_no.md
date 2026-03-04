# Linux运维培训教程：1.5：用户提权与OpenSSH安全 🔐

![](img/81a3229a90d93892b1f44b958efff073_1.png)

![](img/81a3229a90d93892b1f44b958efff073_2.png)

![](img/81a3229a90d93892b1f44b958efff073_4.png)

在本节课中，我们将学习如何通过密钥认证实现不同系统间的安全连接，以及如何配置OpenSSH服务以提高系统安全性。主要内容包括Windows与Linux之间的密钥认证、SCP远程文件传输、SSH服务的安全配置以及本地主机名解析。

---

![](img/81a3229a90d93892b1f44b958efff073_6.png)

![](img/81a3229a90d93892b1f44b958efff073_8.png)

## 跨系统密钥认证 🔑

![](img/81a3229a90d93892b1f44b958efff073_10.png)

![](img/81a3229a90d93892b1f44b958efff073_11.png)

上一节我们介绍了Linux系统之间的密钥认证。本节中，我们来看看如何实现Windows与Linux系统之间的密钥认证。

![](img/81a3229a90d93892b1f44b958efff073_13.png)

![](img/81a3229a90d93892b1f44b958efff073_15.png)

![](img/81a3229a90d93892b1f44b958efff073_17.png)

我们通常使用Xshell这类工具连接Linux服务器。Xshell内置了密钥管理功能，可以方便地生成和管理密钥对。

![](img/81a3229a90d93892b1f44b958efff073_19.png)

![](img/81a3229a90d93892b1f44b958efff073_21.png)

以下是使用Xshell生成密钥对并配置免密登录的步骤：

![](img/81a3229a90d93892b1f44b958efff073_23.png)

![](img/81a3229a90d93892b1f44b958efff073_25.png)

![](img/81a3229a90d93892b1f44b958efff073_26.png)

1.  **生成密钥对**：在Xshell中，点击“工具” -> “新建用户密钥生成向导”。选择密钥类型（如RSA）和长度（如2048位），点击下一步生成密钥对。
2.  **设置私钥**：生成后，可以为私钥设置密码（可选）。如果留空，则私钥无密码。
3.  **保存公钥**：向导会显示生成的公钥内容。请复制这段公钥文本。
4.  **配置Linux服务器**：登录目标Linux服务器，编辑对应用户家目录下的 `~/.ssh/authorized_keys` 文件（如果不存在则创建）。
5.  **粘贴公钥**：将复制的公钥内容粘贴到 `authorized_keys` 文件中，保存退出。
6.  **使用私钥连接**：在Xshell新建会话，在“用户身份验证”方法中选择“Public Key”，并浏览指定刚才生成的私钥文件。连接时即可免密登录。

![](img/81a3229a90d93892b1f44b958efff073_28.png)

![](img/81a3229a90d93892b1f44b958efff073_30.png)

**核心概念**：密钥认证的原理是，将客户端的公钥 `id_rsa.pub` 放置到服务端的 `~/.ssh/authorized_keys` 文件中。连接时，客户端用私钥 `id_rsa` 证明身份。

![](img/81a3229a90d93892b1f44b958efff073_32.png)

---

![](img/81a3229a90d93892b1f44b958efff073_34.png)

![](img/81a3229a90d93892b1f44b958efff073_36.png)

![](img/81a3229a90d93892b1f44b958efff073_38.png)

## 远程文件传输：SCP 📁

![](img/81a3229a90d93892b1f44b958efff073_40.png)

![](img/81a3229a90d93892b1f44b958efff073_42.png)

![](img/81a3229a90d93892b1f44b958efff073_44.png)

![](img/81a3229a90d93892b1f44b958efff073_46.png)

![](img/81a3229a90d93892b1f44b958efff073_48.png)

OpenSSH不仅提供安全的远程登录，还提供了安全的文件传输命令 `scp`。`cp` 命令用于本地文件拷贝，而 `scp` 用于不同主机之间的远程拷贝。

![](img/81a3229a90d93892b1f44b958efff073_50.png)

![](img/81a3229a90d93892b1f44b958efff073_51.png)

![](img/81a3229a90d93892b1f44b958efff073_52.png)

![](img/81a3229a90d93892b1f44b958efff073_54.png)

![](img/81a3229a90d93892b1f44b958efff073_56.png)

![](img/81a3229a90d93892b1f44b958efff073_58.png)

![](img/81a3229a90d93892b1f44b958efff073_59.png)

**从本地拷贝文件到远程主机**：
命令格式为 `scp [本地文件路径] [用户名]@[远程主机IP]:[远程目标路径]`。
```bash
scp /root/hello.txt root@192.168.0.112:/root/
```

![](img/81a3229a90d93892b1f44b958efff073_61.png)

![](img/81a3229a90d93892b1f44b958efff073_63.png)

![](img/81a3229a90d93892b1f44b958efff073_64.png)

![](img/81a3229a90d93892b1f44b958efff073_65.png)

**从远程主机拉取文件到本地**：
命令格式为 `scp [用户名]@[远程主机IP]:[远程文件路径] [本地目标路径]`。
```bash
scp root@192.168.0.112:/etc/fstab /opt/
```
如果已配置密钥认证，`scp` 传输过程将无需输入密码。

![](img/81a3229a90d93892b1f44b958efff073_67.png)

---

![](img/81a3229a90d93892b1f44b958efff073_69.png)

## 提高SSH服务安全性 🛡️

![](img/81a3229a90d93892b1f44b958efff073_71.png)

![](img/81a3229a90d93892b1f44b958efff073_72.png)

![](img/81a3229a90d93892b1f44b958efff073_74.png)

![](img/81a3229a90d93892b1f44b958efff073_76.png)

了解了基本连接后，我们需要关注如何加固SSH服务。OpenSSH的主配置文件是 `/etc/ssh/sshd_config`，通过修改此文件可以提升安全性。

![](img/81a3229a90d93892b1f44b958efff073_78.png)

![](img/81a3229a90d93892b1f44b958efff073_79.png)

![](img/81a3229a90d93892b1f44b958efff073_81.png)

![](img/81a3229a90d93892b1f44b958efff073_83.png)

以下是几个常用的安全配置项：

*   **修改默认端口**：找到 `#Port 22` 行，取消注释并将 `22` 改为其他端口号（如 `2222`）。修改后需重启 `sshd` 服务并确保防火墙开放新端口。
*   **禁止Root用户远程登录**：找到 `#PermitRootLogin yes` 行，取消注释并将 `yes` 改为 `no`。重启服务后，`root` 用户将无法直接通过SSH登录。企业通常新建一个普通用户，然后通过 `sudo` 授权管理权限。
*   **禁止使用密码认证**：找到 `#PasswordAuthentication yes` 行，取消注释并将 `yes` 改为 `no`。重启服务后，所有用户只能通过密钥认证登录，彻底杜绝密码爆破风险。
*   **用户访问控制**：
    *   **允许特定用户**：添加 `AllowUsers [用户名]`，例如 `AllowUsers test` 表示只允许 `test` 用户登录。
    *   **拒绝特定用户**：添加 `DenyUsers [用户名]`，例如 `DenyUsers test` 表示拒绝 `test` 用户登录。
    *   可以指定IP，如 `AllowUsers test@192.168.0.0/24` 表示只允许 `test` 用户从指定网段登录。

**注意**：每次修改 `/etc/ssh/sshd_config` 后，都需要执行 `systemctl restart sshd` 命令使配置生效。进行关键修改（如禁用密码）前，请确保已配置好密钥认证，否则可能导致自己无法登录。

![](img/81a3229a90d93892b1f44b958efff073_85.png)

![](img/81a3229a90d93892b1f44b958efff073_87.png)

---

![](img/81a3229a90d93892b1f44b958efff073_89.png)

![](img/81a3229a90d93892b1f44b958efff073_91.png)

![](img/81a3229a90d93892b1f44b958efff073_93.png)

![](img/81a3229a90d93892b1f44b958efff073_95.png)

## 本地主机名解析 🌐

![](img/81a3229a90d93892b1f44b958efff073_97.png)

![](img/81a3229a90d93892b1f44b958efff073_98.png)

最后，我们介绍一个与网络连接相关的小技巧——本地主机名解析。系统在解析域名时，会首先查询本地的 `/etc/hosts` 文件。

这个文件的格式很简单：
```
[IP地址]    [主机名或域名]
```
例如：
```
192.168.0.112   centos-server
```
保存后，在本机就可以直接使用 `ping centos-server` 或 `ssh centos-server` 来访问该IP地址对应的主机。

![](img/81a3229a90d93892b1f44b958efff073_100.png)

![](img/81a3229a90d93892b1f44b958efff073_102.png)

**应用场景**：
1.  **Linux间互访**：在集群环境中，为所有节点互相配置 `hosts` 文件，便于使用主机名管理。
2.  **Windows访问测试**：在Windows上，`hosts` 文件路径通常为 `C:\Windows\System32\drivers\etc\hosts`。你可以手动添加记录，让浏览器用自定义域名访问你内网的测试服务器（如 `192.168.0.40 www.mydev.com`）。

![](img/81a3229a90d93892b1f44b958efff073_104.png)

![](img/81a3229a90d93892b1f44b958efff073_105.png)

---

![](img/81a3229a90d93892b1f44b958efff073_107.png)

![](img/81a3229a90d93892b1f44b958efff073_109.png)

## 总结 📝

![](img/81a3229a90d93892b1f44b958efff073_111.png)

本节课中我们一起学习了：
1.  使用Xshell工具实现 **Windows到Linux的SSH密钥认证**。
2.  使用 **`scp` 命令** 在主机间安全地传输文件。
3.  通过修改 `/etc/ssh/sshd_config` 配置文件来 **提高SSH服务安全性**，包括改端口、禁Root登录、强制密钥认证等。
4.  利用 **`/etc/hosts` 文件** 进行本地主机名解析，简化网络访问。

![](img/81a3229a90d93892b1f44b958efff073_113.png)

![](img/81a3229a90d93892b1f44b958efff073_115.png)

![](img/81a3229a90d93892b1f44b958efff073_117.png)

这些技能是Linux系统管理和运维中保障访问安全与效率的基础，请务必掌握。