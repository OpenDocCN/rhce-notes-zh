# Linux运维全套培训课程：P56：红帽RHCE-20.用户提权与OpenSSH安全 🔐

![](img/fa08ff423f4675d8c15b320b8c3896b4_1.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_3.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_5.png)

在本节课中，我们将学习如何实现Windows与Linux系统之间的SSH密钥认证，掌握远程文件传输命令SCP，并了解如何通过配置OpenSSH服务来提高系统的安全性。

![](img/fa08ff423f4675d8c15b320b8c3896b4_7.png)

---

![](img/fa08ff423f4675d8c15b320b8c3896b4_9.png)

上一节我们介绍了Linux系统之间的SSH密钥认证。本节中，我们来看看如何实现Windows与Linux系统之间的密钥认证。

![](img/fa08ff423f4675d8c15b320b8c3896b4_11.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_13.png)

Windows系统通常使用Xshell等终端工具连接Linux服务器。Xshell工具内置了密钥管理功能，可以方便地生成和管理密钥对。

![](img/fa08ff423f4675d8c15b320b8c3896b4_15.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_17.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_19.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_21.png)

以下是使用Xshell生成密钥对并与Linux服务器建立免密连接的步骤：

![](img/fa08ff423f4675d8c15b320b8c3896b4_23.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_25.png)

1.  **打开Xshell的“用户密钥管理者”**：在工具菜单中找到“用户密钥管理者”或“新建用户密钥生成向导”。
2.  **生成密钥对**：点击“生成”，选择密钥类型（如RSA）和长度（如2048位），这与在Linux上使用 `ssh-keygen` 命令类似。
3.  **保存私钥**：生成后，系统会提示你保存私钥文件。可以为私钥设置密码，也可以留空。
4.  **复制公钥**：密钥对生成后，工具会显示公钥内容。请复制这段公钥文本。
5.  **将公钥部署到Linux服务器**：登录目标Linux服务器，将复制的公钥内容追加到对应用户家目录下的 `~/.ssh/authorized_keys` 文件中。如果该文件不存在，可以创建它。
    ```bash
    # 示例：将公钥添加到root用户的授权文件
    echo “你的公钥内容” >> /root/.ssh/authorized_keys
    ```
6.  **使用私钥连接**：在Xshell新建会话时，在“用户身份验证”方法中选择“Public Key”，并指定之前保存的私钥文件。连接时即可免密登录。

![](img/fa08ff423f4675d8c15b320b8c3896b4_27.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_29.png)

通过以上步骤，就建立了从Windows客户端到Linux服务器的免密SSH连接。

![](img/fa08ff423f4675d8c15b320b8c3896b4_31.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_33.png)

---

![](img/fa08ff423f4675d8c15b320b8c3896b4_35.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_37.png)

除了密钥认证，OpenSSH还提供了一个非常实用的远程文件传输工具：SCP。它的功能类似于本地拷贝命令 `cp`，但用于在网络上的不同主机间传输文件。

![](img/fa08ff423f4675d8c15b320b8c3896b4_39.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_41.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_43.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_45.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_47.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_49.png)

以下是SCP命令的基本用法：

![](img/fa08ff423f4675d8c15b320b8c3896b4_51.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_53.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_54.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_55.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_57.png)

*   **从本地拷贝文件到远程主机**：
    ```bash
    scp /本地/文件/路径 root@远程主机IP:/远程/目标/路径
    ```
    例如：`scp /home/test.txt root@192.168.1.100:/tmp/`

![](img/fa08ff423f4675d8c15b320b8c3896b4_59.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_61.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_63.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_65.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_67.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_69.png)

*   **从远程主机拉取文件到本地**：
    ```bash
    scp root@远程主机IP:/远程/文件/路径 /本地/目标/路径
    ```
    例如：`scp root@192.168.1.100:/etc/passwd /opt/`

![](img/fa08ff423f4675d8c15b320b8c3896b4_71.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_73.png)

**注意**：命令中的 `:` 符号是分隔主机和路径的关键。如果已配置密钥认证，SCP传输过程将无需输入密码。

---

![](img/fa08ff423f4675d8c15b320b8c3896b4_75.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_77.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_79.png)

为了提升服务器安全性，我们经常需要调整OpenSSH服务的配置。其主配置文件位于 `/etc/ssh/sshd_config`。

![](img/fa08ff423f4675d8c15b320b8c3896b4_81.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_83.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_85.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_87.png)

以下是几个常见的安全配置项及其作用：

![](img/fa08ff423f4675d8c15b320b8c3896b4_89.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_91.png)

1.  **修改默认端口**：将 `Port 22` 改为其他端口（如 `Port 2222`），可以减少自动化攻击脚本的扫描。修改后需重启SSH服务并确保防火墙放行新端口。
    ```bash
    systemctl restart sshd
    ```

2.  **禁止root用户远程登录**：找到 `PermitRootLogin` 配置项，将其值改为 `no`。这样可以防止攻击者直接暴力破解root密码。之后可通过普通用户登录，再使用 `su` 或 `sudo` 提权。
    ```bash
    PermitRootLogin no
    ```

![](img/fa08ff423f4675d8c15b320b8c3896b4_93.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_95.png)

3.  **禁止使用密码认证，强制使用密钥登录**：找到 `PasswordAuthentication` 配置项，将其值改为 `no`。这是提高安全性的有效方法，但前提是必须已配置好密钥认证。
    ```bash
    PasswordAuthentication no
    ```

![](img/fa08ff423f4675d8c15b320b8c3896b4_97.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_99.png)

4.  **设置用户访问控制**：
    *   **白名单**：使用 `AllowUsers` 指令，只允许指定的用户登录。例如 `AllowUsers alice bob@192.168.1.0/24` 表示允许用户alice从任何IP登录，而用户bob只能从指定网段登录。
    *   **黑名单**：使用 `DenyUsers` 指令，禁止指定的用户登录。例如 `DenyUsers hacker`。

![](img/fa08ff423f4675d8c15b320b8c3896b4_101.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_103.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_105.png)

**重要提示**：修改配置文件前建议备份。每次修改后，都需要执行 `systemctl restart sshd` 重启服务以使配置生效。

![](img/fa08ff423f4675d8c15b320b8c3896b4_107.png)

---

![](img/fa08ff423f4675d8c15b320b8c3896b4_109.png)

最后，我们介绍一个与网络连接相关的基础文件：`/etc/hosts`。这个文件用于本地的主机名解析，其格式为 `IP地址 主机名`。

![](img/fa08ff423f4675d8c15b320b8c3896b4_111.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_113.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_115.png)

例如，在文件中添加一行：
```
192.168.1.100 web-server
```
保存后，在本机上就可以使用 `ping web-server` 或 `ssh web-server` 来访问IP为192.168.1.100的服务器，系统会自动将其解析为对应的IP地址。

![](img/fa08ff423f4675d8c15b320b8c3896b4_117.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_119.png)

**Windows系统**也有类似的本地解析文件，路径通常为 `C:\Windows\System32\drivers\etc\hosts`，格式与Linux相同。这在开发测试环境中用于模拟域名解析非常方便。

![](img/fa08ff423f4675d8c15b320b8c3896b4_121.png)

---

![](img/fa08ff423f4675d8c15b320b8c3896b4_123.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_125.png)

![](img/fa08ff423f4675d8c15b320b8c3896b4_127.png)

本节课中我们一起学习了三个核心内容：如何配置Windows到Linux的SSH密钥认证以实现免密登录；如何使用SCP命令在主机间安全传输文件；以及如何通过修改SSH配置文件来增强服务器的访问安全性。此外，还了解了 `/etc/hosts` 文件在本地网络解析中的作用。掌握这些技能，是进行安全、高效的Linux系统运维管理的基础。