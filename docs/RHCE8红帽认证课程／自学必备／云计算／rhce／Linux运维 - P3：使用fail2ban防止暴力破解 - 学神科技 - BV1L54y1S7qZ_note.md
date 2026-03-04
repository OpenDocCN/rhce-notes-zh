# RHCE8红帽认证课程：P3：使用fail2ban防止暴力破解 🔒

在本节课中，我们将学习如何保护Linux服务器，特别是SSH服务，免受暴力破解攻击。我们将探讨多种防护策略，并重点介绍一个名为fail2ban的强大工具，它可以自动监控日志并封锁恶意IP地址。

## 什么是暴力破解？💥

暴力破解是指攻击者通过系统性地尝试大量用户名和密码组合，试图非法访问系统的一种攻击方式。对于SSH服务而言，由于其是远程管理的主要入口，一旦被攻破，服务器可能被用于挖矿、充当“肉鸡”等恶意活动，因此防护至关重要。

## 基础防护策略 🛡️

在引入自动化工具之前，我们可以通过配置SSH服务本身来提升安全性。以下是几种有效的基础防护方法：

*   **设置强密码**：密码长度应大于8位，并混合使用数字、大写字母、小写字母和特殊符号中的至少三种。例如：`P@ssw0rd`。
*   **修改默认端口**：将SSH默认的22端口改为一个非标准端口，可以减少自动化扫描工具的攻击。
*   **禁止root用户远程登录**：创建一个普通用户，并通过`su`或`sudo`命令来获取root权限，增加攻击难度。
*   **使用密钥对认证**：用公钥/私钥对代替密码登录，安全性更高。公钥相当于“锁”，放置在服务器上；私钥相当于“钥匙”，由用户保管。

![](img/b21a623cf4f2f63ab1bf097be71580f1_1.png)

上一节我们介绍了基础防护策略，本节中我们来看看如何具体配置密钥对登录，这是一种非常推荐的安全实践。

### 配置密钥对登录 🔑

![](img/b21a623cf4f2f63ab1bf097be71580f1_3.png)

![](img/b21a623cf4f2f63ab1bf097be71580f1_5.png)

以下是配置SSH密钥对登录的步骤：

1.  **生成密钥对**：在客户端机器上执行命令 `ssh-keygen`。全程按回车使用默认选项即可，它会在用户家目录的 `.ssh/` 文件夹下生成私钥 (`id_rsa`) 和公钥 (`id_rsa.pub`)。
    ```bash
    ssh-keygen
    ```

![](img/b21a623cf4f2f63ab1bf097be71580f1_7.png)

![](img/b21a623cf4f2f63ab1bf097be71580f1_9.png)

2.  **传输公钥到服务器**：使用 `ssh-copy-id` 命令将公钥传输到目标服务器。
    ```bash
    ssh-copy-id user@server_ip
    ```
    首次连接时需要输入服务器用户的密码。

![](img/b21a623cf4f2f63ab1bf097be71580f1_11.png)

3.  **测试无密码登录**：公钥传输成功后，即可直接使用 `ssh user@server_ip` 登录，无需再输入密码。

## 使用fail2ban进行高级防护 🚨

即使采取了上述措施，持续的暴力破解尝试仍会消耗系统资源。fail2ban可以监控系统日志（如 `/var/log/secure`），根据定义的规则（如短时间内多次密码错误）自动调用防火墙（如iptables）封锁IP地址。

### 安装与配置fail2ban

首先，确保系统已配置好Yum源，然后安装fail2ban。

```bash
yum install fail2ban -y
```

![](img/b21a623cf4f2f63ab1bf097be71580f1_13.png)

![](img/b21a623cf4f2f63ab1bf097be71580f1_15.png)

fail2ban的主要配置文件是 `/etc/fail2ban/jail.conf`。通常我们不直接修改它，而是创建一个局部配置文件 `/etc/fail2ban/jail.local` 来覆盖默认设置。

![](img/b21a623cf4f2f63ab1bf097be71580f1_17.png)

接下来，我们来为SSH服务配置一个防护策略。假设我们的规则是：**在5分钟（300秒）内，如果来自同一IP的密码认证失败次数达到3次，则禁止该IP访问1小时（3600秒）**。

![](img/b21a623cf4f2f63ab1bf097be71580f1_19.png)

![](img/b21a623cf4f2f63ab1bf097be71580f1_21.png)

以下是需要添加到 `/etc/fail2ban/jail.local` 的配置内容：

```ini
[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/secure
maxretry = 3
bantime = 3600
findtime = 300
action = %(action_mwl)s
```

![](img/b21a623cf4f2f63ab1bf097be71580f1_23.png)

*   `[sshd]`: 定义针对SSH服务的监控规则。
*   `enabled = true`: 启用此规则。
*   `port = ssh`: 监控的端口，默认为22。如果修改了SSH端口，此处需相应更改。
*   `filter = sshd`: 使用的过滤规则名称，对应 `/etc/fail2ban/filter.d/sshd.conf`。
*   `logpath = /var/log/secure`: SSH日志文件路径。
*   `maxretry = 3`: 最大重试次数。
*   `bantime = 3600`: 禁止访问的时长（秒）。
*   `findtime = 300`: 统计失败次数的时间窗口（秒）。
*   `action = %(action_mwl)s`: 执行的动作，这里表示禁止IP并发送邮件通知（需配置邮件）。

### 启动fail2ban并测试

配置完成后，启动fail2ban服务并设置为开机自启。

```bash
systemctl start fail2ban
systemctl enable fail2ban
```

![](img/b21a623cf4f2f63ab1bf097be71580f1_25.png)

现在，我们可以从另一台机器故意输错密码进行测试。在达到3次失败后，再次尝试连接会发现连接被拒绝。

![](img/b21a623cf4f2f63ab1bf097be71580f1_27.png)

### 管理fail2ban

我们可以使用fail2ban-client命令来查看状态和管理被封禁的IP。

*   **查看被禁止的IP**：
    ```bash
    fail2ban-client status sshd
    ```

![](img/b21a623cf4f2f63ab1bf097be71580f1_29.png)

*   **手动解封某个IP**：
    ```bash
    fail2ban-client set sshd unbanip IP_ADDRESS
    ```

![](img/b21a623cf4f2f63ab1bf097be71580f1_31.png)

*   **手动禁止某个IP**：
    ```bash
    fail2ban-client set sshd banip IP_ADDRESS
    ```

![](img/b21a623cf4f2f63ab1bf097be71580f1_33.png)

**重要提示**：如果系统重启了防火墙（如firewalld或iptables），通常也需要重启fail2ban服务 (`systemctl restart fail2ban`) 以使其规则重新生效。

![](img/b21a623cf4f2f63ab1bf097be71580f1_35.png)

![](img/b21a623cf4f2f63ab1bf097be71580f1_37.png)

## 总结 📝

![](img/b21a623cf4f2f63ab1bf097be71580f1_39.png)

![](img/b21a623cf4f2f63ab1bf097be71580f1_41.png)

本节课中我们一起学习了如何防御SSH暴力破解攻击。我们首先了解了暴力破解的概念与危害，然后学习了修改端口、禁用root远程登录、使用密钥对等基础加固方法。最后，我们重点介绍了fail2ban这款工具，它能够自动监控日志、识别攻击行为并动态更新防火墙规则，从而极大地提升了服务器的主动防御能力。结合基础防护与fail2ban的自动封锁，可以为你的Linux服务器构建一道坚实的安全防线。