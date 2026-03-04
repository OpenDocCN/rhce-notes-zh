# Linux运维进阶：P55：用户提权与OpenSSH安全 🔐

![](img/805ea43d56a2c8441cb9acf1ca702108_1.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_3.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_5.png)

在本节课中，我们将学习如何实现Windows与Linux系统之间的SSH密钥认证，掌握远程文件传输命令SCP，并了解如何通过配置OpenSSH服务来提高系统的安全性。

![](img/805ea43d56a2c8441cb9acf1ca702108_7.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_9.png)

## 实现Windows与Linux之间的SSH密钥认证

![](img/805ea43d56a2c8441cb9acf1ca702108_11.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_13.png)

上一节我们介绍了Linux系统之间的密钥认证。本节中，我们来看看如何通过Windows上的工具（如Xshell）与Linux服务器建立密钥认证连接。

![](img/805ea43d56a2c8441cb9acf1ca702108_15.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_17.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_19.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_21.png)

以下是使用Xshell生成密钥对并配置免密登录的步骤：

![](img/805ea43d56a2c8441cb9acf1ca702108_23.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_25.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_27.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_29.png)

1.  **打开Xshell的“用户密钥管理者”**：在工具菜单中找到此功能。
2.  **生成新的密钥对**：点击“新建用户密钥生成向导”，选择密钥类型（如RSA）和长度（如2048位），生成公钥和私钥。
3.  **保存公钥**：将生成的公钥内容复制下来。
4.  **将公钥部署到Linux服务器**：登录目标Linux服务器，将复制的公钥内容追加到 `~/.ssh/authorized_keys` 文件中。如果该文件不存在，可以创建它。
    ```bash
    echo “你的公钥内容” >> ~/.ssh/authorized_keys
    ```
5.  **在Xshell中使用私钥连接**：新建会话，在“用户身份验证”方法中选择“Public Key”，并指定之前生成的私钥文件。连接时即可免密登录。

![](img/805ea43d56a2c8441cb9acf1ca702108_31.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_33.png)

通过以上步骤，Windows客户端就可以使用密钥对安全地连接到Linux服务器，无需每次输入密码。

![](img/805ea43d56a2c8441cb9acf1ca702108_35.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_37.png)

## 使用SCP进行远程文件传输

![](img/805ea43d56a2c8441cb9acf1ca702108_39.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_41.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_43.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_45.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_47.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_49.png)

除了登录，OpenSSH还提供了安全的文件传输功能。SCP命令可以在不同主机之间拷贝文件，其用法与本地CP命令类似，但需要指定远程主机信息。

![](img/805ea43d56a2c8441cb9acf1ca702108_51.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_53.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_54.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_55.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_57.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_59.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_61.png)

以下是SCP命令的基本用法：

![](img/805ea43d56a2c8441cb9acf1ca702108_63.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_65.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_67.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_69.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_71.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_73.png)

*   **从本地拷贝文件到远程主机**：
    ```bash
    scp /本地/文件/路径 root@远程主机IP:/远程/目标/路径
    ```
*   **从远程主机拉取文件到本地**：
    ```bash
    scp root@远程主机IP:/远程/文件/路径 /本地/目标/路径
    ```

![](img/805ea43d56a2c8441cb9acf1ca702108_75.png)

**注意**：如果已经配置了密钥认证，SCP传输过程也将是免密的。否则，需要输入远程主机的用户密码。

![](img/805ea43d56a2c8441cb9acf1ca702108_77.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_79.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_81.png)

## 配置OpenSSH服务以提高安全性

![](img/805ea43d56a2c8441cb9acf1ca702108_83.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_85.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_87.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_89.png)

OpenSSH的主配置文件是 `/etc/ssh/sshd_config`。通过修改此文件，可以显著提升服务器的访问安全性。我们不需要记住所有配置项，但需要知道如何实现常见的安全需求。

![](img/805ea43d56a2c8441cb9acf1ca702108_91.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_93.png)

以下是几个关键的安全配置项及其作用：

![](img/805ea43d56a2c8441cb9acf1ca702108_95.png)

1.  **修改默认端口**：将 `Port 22` 改为其他端口（如 `Port 2222`），可以减少自动化攻击脚本的扫描。修改后需重启SSH服务并确保防火墙开放新端口。
2.  **禁止Root用户直接登录**：将 `PermitRootLogin yes` 改为 `PermitRootLogin no`。这样可以防止针对root账户的暴力破解。之后可以通过普通用户登录，再使用 `su` 或 `sudo` 提权。
3.  **禁止使用密码认证**：将 `PasswordAuthentication yes` 改为 `PasswordAuthentication no`。这强制所有用户必须使用密钥对登录，极大增强了安全性。
4.  **设置用户访问控制**：
    *   **白名单**：使用 `AllowUsers 用户名1 用户名2` 指定允许登录的用户。
    *   **黑名单**：使用 `DenyUsers 用户名1 用户名2` 指定禁止登录的用户。
    可以结合IP地址进行更精细的控制，例如 `AllowUsers admin@192.168.1.0/24`。

![](img/805ea43d56a2c8441cb9acf1ca702108_97.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_99.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_101.png)

**重要提示**：修改配置文件后，必须执行 `systemctl restart sshd` 命令重启SSH服务才能使配置生效。在应用“禁止密码登录”或“禁止root登录”等配置前，请务必确保已配置好可用的密钥对或备用管理账户，否则可能导致自己无法登录服务器。

![](img/805ea43d56a2c8441cb9acf1ca702108_103.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_105.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_107.png)

## 了解本地主机名解析（/etc/hosts）

![](img/805ea43d56a2c8441cb9acf1ca702108_109.png)

在进行网络连接或服务测试时，我们经常需要将IP地址与易记的主机名对应起来。`/etc/hosts` 文件提供了本地的静态主机名解析。

![](img/805ea43d56a2c8441cb9acf1ca702108_111.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_113.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_115.png)

该文件的格式非常简单：
```
IP地址    主机名或域名
```
例如，添加一行 `192.168.1.100    mywebserver` 后，在本机上就可以使用 `ping mywebserver` 或 `ssh mywebserver` 来访问该IP地址对应的主机。

![](img/805ea43d56a2c8441cb9acf1ca702108_117.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_119.png)

**Windows系统**也有类似的主机文件，路径通常为 `C:\Windows\System32\drivers\etc\hosts`，格式与Linux相同。这在开发测试环境中非常有用，可以模拟域名解析。

![](img/805ea43d56a2c8441cb9acf1ca702108_121.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_123.png)

---

![](img/805ea43d56a2c8441cb9acf1ca702108_125.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_127.png)

![](img/805ea43d56a2c8441cb9acf1ca702108_129.png)

本节课中我们一起学习了跨平台的SSH密钥认证配置、使用SCP进行安全文件传输、通过修改SSHD配置强化服务器安全，以及利用hosts文件进行本地主机名解析。这些技能是Linux系统管理和运维工作中保障访问安全与效率的基础。