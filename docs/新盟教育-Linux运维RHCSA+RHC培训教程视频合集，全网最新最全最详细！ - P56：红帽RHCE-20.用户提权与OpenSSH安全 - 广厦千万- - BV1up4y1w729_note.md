# Linux运维RHCSA+RHCE培训教程：P56：用户提权与OpenSSH安全 🔐

![](img/1fe2eb8d59cc84a5cd609da848488c30_1.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_2.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_4.png)

在本节课中，我们将学习如何实现Windows与Linux系统之间的SSH密钥认证，掌握SCP远程文件传输命令，并了解如何通过配置OpenSSH服务来提高系统的安全性。

---

![](img/1fe2eb8d59cc84a5cd609da848488c30_6.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_8.png)

## 实现Windows与Linux之间的SSH密钥认证

上一节我们介绍了Linux系统之间的密钥认证。本节中，我们来看看如何通过Windows上的工具（如Xshell）与Linux服务器建立密钥认证。

![](img/1fe2eb8d59cc84a5cd609da848488c30_10.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_11.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_13.png)

在Windows上，我们通常使用Xshell等SSH客户端工具连接Linux服务器。该工具内置了密钥管理功能。

![](img/1fe2eb8d59cc84a5cd609da848488c30_15.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_17.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_19.png)

以下是使用Xshell生成密钥对并与Linux服务器建立认证的步骤：

![](img/1fe2eb8d59cc84a5cd609da848488c30_21.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_23.png)

1.  **生成密钥对**：在Xshell中，点击“工具” -> “新建用户密钥生成向导”。选择密钥类型（如RSA）和长度（如2048位），点击下一步生成密钥对。
2.  **设置私钥**：生成后，可以为私钥设置密码（可选）。如果不设置，直接留空并继续。
3.  **保存公钥**：向导会显示生成的公钥内容。请复制这段公钥文本。
4.  **上传公钥到Linux服务器**：登录目标Linux服务器，编辑或创建对应用户（如root）的授权文件 `~/.ssh/authorized_keys`，将复制的公钥内容粘贴进去并保存。
5.  **使用私钥连接**：在Xshell新建会话，在“用户身份验证”方法中选择“Public Key”，并指定刚才生成的私钥文件。连接时，将使用密钥认证而无需输入密码。

![](img/1fe2eb8d59cc84a5cd609da848488c30_25.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_26.png)

**核心概念公式**：
```
Windows (Xshell) 生成 (私钥 + 公钥)
公钥 -> 放入 Linux 服务器的 ~/.ssh/authorized_keys 文件
连接时，Xshell 使用私钥进行认证
```

![](img/1fe2eb8d59cc84a5cd609da848488c30_28.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_30.png)

---

![](img/1fe2eb8d59cc84a5cd609da848488c30_32.png)

## 使用SCP进行远程文件传输

![](img/1fe2eb8d59cc84a5cd609da848488c30_34.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_36.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_38.png)

除了登录，OpenSSH还提供了安全的文件传输功能。`scp`命令用于在不同主机之间加密拷贝文件，其用法与本地`cp`命令类似。

![](img/1fe2eb8d59cc84a5cd609da848488c30_40.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_42.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_44.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_46.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_48.png)

**命令格式**：
*   **从本地拷贝到远程主机**：
    ```bash
    scp [本地文件路径] [用户名]@[远程主机IP]:[远程目标路径]
    ```
*   **从远程主机拉取到本地**：
    ```bash
    scp [用户名]@[远程主机IP]:[远程文件路径] [本地目标路径]
    ```

![](img/1fe2eb8d59cc84a5cd609da848488c30_50.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_51.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_52.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_54.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_56.png)

**操作示例**：
假设已将本地文件 `hello.txt` 拷贝到远程主机 `192.168.0.112` 的root家目录，并从远程主机拉取 `/etc/fstab` 文件到本地 `/opt` 目录。
```bash
# 本地拷贝到远程
scp /root/hello.txt root@192.168.0.112:/root/

![](img/1fe2eb8d59cc84a5cd609da848488c30_58.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_59.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_61.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_63.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_64.png)

# 从远程拉取到本地
scp root@192.168.0.112:/etc/fstab /opt/
```
如果建立了密钥认证，这些操作将无需输入密码。

![](img/1fe2eb8d59cc84a5cd609da848488c30_65.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_67.png)

---

## 配置OpenSSH服务以提高安全性

![](img/1fe2eb8d59cc84a5cd609da848488c30_69.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_71.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_72.png)

OpenSSH的主配置文件是 `/etc/ssh/sshd_config`。通过修改此文件，可以显著提升SSH服务的安全性。

![](img/1fe2eb8d59cc84a5cd609da848488c30_74.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_76.png)

以下是几个关键的安全配置项及其作用：

![](img/1fe2eb8d59cc84a5cd609da848488c30_78.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_79.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_81.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_83.png)

1.  **修改默认端口**：将 `Port 22` 改为一个非标准端口（如 `Port 2222`），可以减少自动化攻击脚本的扫描。修改后需重启SSH服务并确保防火墙放行新端口。
    ```bash
    systemctl restart sshd
    ```

2.  **禁止root用户远程登录**：找到 `PermitRootLogin` 配置项，将其值改为 `no`。这样可以防止攻击者直接暴力破解root密码。之后应使用普通用户登录，再通过`su`或`sudo`提权管理。
    ```bash
    PermitRootLogin no
    ```

3.  **禁止使用密码认证，强制使用密钥登录**：找到 `PasswordAuthentication` 配置项，将其值改为 `no`。这是最推荐的安全方式，能彻底杜绝密码被暴力破解的风险。
    ```bash
    PasswordAuthentication no
    ```

![](img/1fe2eb8d59cc84a5cd609da848488c30_85.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_87.png)

4.  **使用AllowUsers或DenyUsers限制登录用户**：
    *   `AllowUsers user1 user2@192.168.1.100`：白名单，只允许指定用户（或从指定IP来的用户）登录。
    *   `DenyUsers baduser`：黑名单，禁止指定用户登录。

![](img/1fe2eb8d59cc84a5cd609da848488c30_89.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_91.png)

**注意**：每次修改配置文件后，都需要执行 `systemctl restart sshd` 命令使配置生效。修改前建议备份原文件。

![](img/1fe2eb8d59cc84a5cd609da848488c30_93.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_95.png)

---

![](img/1fe2eb8d59cc84a5cd609da848488c30_97.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_98.png)

## 了解本地主机名解析（/etc/hosts）

![](img/1fe2eb8d59cc84a5cd609da848488c30_100.png)

在进行网络连接或测试时，我们经常需要将IP地址与主机名对应起来。除了DNS，系统还有一个本地的解析文件。

![](img/1fe2eb8d59cc84a5cd609da848488c30_102.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_104.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_105.png)

**文件路径**：
*   **Linux系统**：`/etc/hosts`
*   **Windows系统**：`C:\Windows\System32\drivers\etc\hosts`

**文件格式**：
```
[IP地址] [主机名或域名]
```
例如，在Linux的 `/etc/hosts` 文件中添加一行：
```
192.168.0.112  server01
```
之后，在本机上就可以使用 `ping server01` 或 `ssh server01` 来访问该IP地址对应的主机。Windows系统的hosts文件格式和用途完全相同。

![](img/1fe2eb8d59cc84a5cd609da848488c30_107.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_109.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_111.png)

**核心概念**：系统在进行域名解析时，会优先查询本地的hosts文件，如果找不到记录，再去查询DNS服务器。这在内部网络测试或屏蔽某些网站时非常有用。

---

![](img/1fe2eb8d59cc84a5cd609da848488c30_113.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_115.png)

![](img/1fe2eb8d59cc84a5cd609da848488c30_117.png)

本节课中我们一起学习了如何搭建跨平台的SSH密钥认证体系，使用`scp`命令安全传输文件，并通过配置OpenSSH服务的关键参数来加固系统安全。最后，我们还了解了利用hosts文件进行本地主机名解析的原理与方法。掌握这些技能，是进行安全、高效的Linux系统运维管理的基础。