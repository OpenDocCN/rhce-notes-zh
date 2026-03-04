# Linux运维：2-1-4：SSHD服务防止暴力破解 🔒

在本节课中，我们将学习如何保护SSHD服务，防止其遭受暴力破解攻击。我们将探讨两种主要方法：通过配置SSH服务自身的安全选项，以及使用一个名为`fail2ban`的开源防御软件。

![](img/48121de0488d56abdacdace3e4eeb06e_1.png)

![](img/48121de0488d56abdacdace3e4eeb06e_3.png)

![](img/48121de0488d56abdacdace3e4eeb06e_5.png)

## 配置安全的SSH服务

上一节我们介绍了SSHD服务的基本管理，本节中我们来看看如何通过配置SSH服务本身来提升安全性。这是一种基础且有效的防护手段。

![](img/48121de0488d56abdacdace3e4eeb06e_7.png)

以下是几种增强SSH服务安全性的配置方法：

1.  **使用强密码**
    *   密码长度应大于8位，最好大于20位。
    *   密码复杂度应包含数字、大小写字母和特殊字符的混合。

2.  **修改默认端口**
    *   将SSH默认的22端口修改为其他不常用的端口。

3.  **禁止root账号直接登录**
    *   添加普通账号，并通过`sudo`命令赋予其管理员权限。
    *   注意：不能完全禁止root身份，因为有些系统程序需要以root权限运行。系统通过用户ID（UID）来识别超级管理员，即使修改用户名，只要UID为0，内核仍会将其视为管理员。

4.  **使用密钥认证代替密码登录**
    *   禁用密码登录，仅允许通过公钥/私钥对进行身份验证。

![](img/48121de0488d56abdacdace3e4eeb06e_9.png)

接下来，我们重点演示如何设置密钥认证。

![](img/48121de0488d56abdacdace3e4eeb06e_11.png)

![](img/48121de0488d56abdacdace3e4eeb06e_13.png)

### 设置SSH密钥认证

![](img/48121de0488d56abdacdace3e4eeb06e_15.png)

![](img/48121de0488d56abdacdace3e4eeb06e_17.png)

![](img/48121de0488d56abdacdace3e4eeb06e_19.png)

密钥认证的原理是：在客户端生成一对密钥（公钥和私钥），将公钥上传到服务器。登录时，服务器用公钥挑战，客户端用私钥应答，从而完成认证。

![](img/48121de0488d56abdacdace3e4eeb06e_21.png)

![](img/48121de0488d56abdacdace3e4eeb06e_23.png)

**操作步骤如下：**

1.  **在客户端生成密钥对**
    使用 `ssh-keygen` 命令生成密钥。执行过程中会提示保存路径和设置密钥密码，为了演示方便，我们全部按回车使用默认值。
    ```bash
    ssh-keygen
    ```
    生成的密钥默认保存在 `~/.ssh/` 目录下，其中 `id_rsa` 是私钥，`id_rsa.pub` 是公钥。

2.  **将公钥上传到服务器**
    使用 `ssh-copy-id` 命令将客户端的公钥传输到服务器。你需要输入服务器用户的密码。
    ```bash
    ssh-copy-id -p [端口号] user@server_ip
    ```
    **注意**：如果之前修改过SSH端口，请在此命令中使用 `-p` 参数指定正确的端口号。

![](img/48121de0488d56abdacdace3e4eeb06e_25.png)

3.  **在服务器上禁用密码登录（可选但推荐）**
    公钥上传成功后，即可通过密钥免密登录。为了更安全，可以编辑服务器的 `/etc/ssh/sshd_config` 文件，将 `PasswordAuthentication` 设置为 `no`，然后重启SSH服务。
    ```bash
    PasswordAuthentication no
    ```

通过以上配置，SSH服务本身的安全性得到了显著提升。

## 使用Fail2ban防御暴力破解

![](img/48121de0488d56abdacdace3e4eeb06e_27.png)

虽然配置强密码和密钥能提高门槛，但服务器日志中仍可能充斥大量的暴力破解尝试，消耗系统资源。本节我们将介绍第二种方法：使用`fail2ban`软件动态屏蔽攻击源。

`fail2ban` 是一个开源防御程序，它通过监控系统日志（如`/var/log/secure`），使用正则表达式匹配登录失败等错误信息。一旦某个IP在设定时间内失败次数达到阈值，`fail2ban`就会调用防火墙（如`iptables`）规则，临时禁止该IP访问所有端口，并可以发送邮件通知管理员。

### 安装与配置Fail2ban

我们将使用Yum包管理器来安装`fail2ban`，这非常简单。`fail2ban`需要Python环境，CentOS 7.5默认已安装Python 2.7.5，满足要求。

![](img/48121de0488d56abdacdace3e4eeb06e_29.png)

![](img/48121de0488d56abdacdace3e4eeb06e_31.png)

1.  **安装Fail2ban**
    ```bash
    yum install -y fail2ban
    ```

![](img/48121de0488d56abdacdace3e4eeb06e_33.png)

2.  **了解配置文件**
    `fail2ban`的主要配置文件位于 `/etc/fail2ban/` 目录下：
    *   `fail2ban.conf`： 主配置文件，包含日志级别、日志目标等全局设置。
    *   `jail.conf`： 核心配置文件，定义监控哪些服务、监控规则（过滤条件）、检测周期、最大失败次数和惩罚动作（`banaction`）等。**不建议直接修改此文件**。
    *   `jail.d/`： 存放局部配置文件的目录。我们的自定义配置通常放在这里，例如 `jail.local`。
    *   `action.d/`： 存放动作定义文件（如调用`iptables`、发送邮件）。
    *   `filter.d/`： 存放日志过滤规则文件。

    当全局配置（`jail.conf`）和局部配置（`jail.local`）冲突时，**范围更小的局部配置生效**。

![](img/48121de0488d56abdacdace3e4eeb06e_35.png)

![](img/48121de0488d56abdacdace3e4eeb06e_36.png)

![](img/48121de0488d56abdacdace3e4eeb06e_38.png)

3.  **配置Fail2ban防护SSHD**
    我们的目标是：**5分钟内SSH登录失败3次，则禁止该IP访问1小时**。
    由于`filter.d/sshd.conf`（日志过滤规则）和`action.d/iptables.conf`（屏蔽动作）已默认存在，我们只需启用SSHD的监控并设置阈值即可。

    *   创建或编辑局部配置文件：
        ```bash
        vi /etc/fail2ban/jail.d/sshd.local
        ```
    *   添加以下内容：
        ```ini
        [sshd]
        enabled = true
        filter = sshd
        action = iptables[name=SSH, port=ssh, protocol=tcp]
        logpath = /var/log/secure
        maxretry = 3
        findtime = 300
        bantime = 3600
        ```
        **参数说明**：
        *   `enabled = true`： 启用对SSH的监控。
        *   `filter = sshd`： 使用`/etc/fail2ban/filter.d/sshd.conf`中的规则过滤日志。
        *   `action`： 触发后执行的动作。`port=ssh`需根据实际情况修改，如果SSH端口不是22，则应改为 `port=你的端口号`。
        *   `logpath`： 指定SSH日志文件路径。
        *   `maxretry = 3`： 最大尝试次数。
        *   `findtime = 300`： 在300秒（5分钟）内达到失败次数则触发。
        *   `bantime = 3600`： 禁止访问3600秒（1小时）。

    **重要提示**：如果修改过SSH端口，务必在`action`行的`port=`参数中更正，并在`filter.d/sshd.conf`文件中，将`port=ssh`的规则也修改为你的端口号，否则`fail2ban`可能无法正确识别攻击。

![](img/48121de0488d56abdacdace3e4eeb06e_40.png)

![](img/48121de0488d56abdacdace3e4eeb06e_42.png)

4.  **启动Fail2ban服务**
    ```bash
    systemctl start fail2ban
    systemctl enable fail2ban
    ```

![](img/48121de0488d56abdacdace3e4eeb06e_44.png)

### 测试Fail2ban效果

配置完成后，我们可以进行测试。

1.  **查看防火墙规则**
    启动`fail2ban`后，它会自动在`iptables`中创建一条链。可以使用以下命令查看：
    ```bash
    iptables -L -n
    ```
    你应该能看到一条名为 `f2b-SSH` 的链。

2.  **模拟暴力破解**
    从另一台客户端（测试机）尝试用错误密码SSH登录服务器3次。
    ```bash
    ssh -p [端口号] user@server_ip
    ```
    （连续输入错误密码）

![](img/48121de0488d56abdacdace3e4eeb06e_46.png)

![](img/48121de0488d56abdacdace3e4eeb06e_48.png)

3.  **验证屏蔽效果**
    *   第3次失败后，再次尝试连接，会发现连接被拒绝或超时。
    *   在服务器上再次运行 `iptables -L -n`，可以在 `f2b-SSH` 链下看到被禁止的客户端IP地址。
    *   查看`fail2ban`的状态和日志：
        ```bash
        fail2ban-client status sshd # 查看sshd监禁区状态
        tail -f /var/log/fail2ban.log # 查看实时日志
        ```

![](img/48121de0488d56abdacdace3e4eeb06e_50.png)

4.  **管理被禁止的IP**
    *   查看当前被禁止的IP列表：
        ```bash
        fail2ban-client status sshd
        ```
    *   手动解除某个IP的禁止：
        ```bash
        fail2ban-client set sshd unbanip IP地址
        ```

![](img/48121de0488d56abdacdace3e4eeb06e_52.png)

![](img/48121de0488d56abdacdace3e4eeb06e_54.png)

### 注意事项

![](img/48121de0488d56abdacdace3e4eeb06e_56.png)

![](img/48121de0488d56abdacdace3e4eeb06e_57.png)

1.  **防火墙重启**：如果系统重启或`iptables`规则被清空，需要重启`fail2ban`服务来重新应用规则：`systemctl restart fail2ban`。
2.  **修改SSH端口**：如果修改了SSH服务端口，必须同步更新`fail2ban`配置文件中`action`行的`port`参数以及`filter.d/sshd.conf`文件中的相关端口定义，否则防护会失效。

## 总结

![](img/48121de0488d56abdacdace3e4eeb06e_59.png)

![](img/48121de0488d56abdacdace3e4eeb06e_60.png)

![](img/48121de0488d56abdacdace3e4eeb06e_62.png)

![](img/48121de0488d56abdacdace3e4eeb06e_64.png)

本节课中我们一起学习了两种防止SSHD服务暴力破解的方法：

![](img/48121de0488d56abdacdace3e4eeb06e_65.png)

1.  **强化SSH服务自身配置**：包括使用强密码/密钥认证、修改默认端口、禁止root直接登录等。这是安全加固的基础。
2.  **使用Fail2ban动态防御**：通过监控日志，自动将短时间内多次尝试失败的IP地址加入防火墙黑名单，实现主动、动态的防护。这种方法能有效减轻系统负载并提升安全性。

![](img/48121de0488d56abdacdace3e4eeb06e_67.png)

![](img/48121de0488d56abdacdace3e4eeb06e_68.png)

![](img/48121de0488d56abdacdace3e4eeb06e_69.png)

![](img/48121de0488d56abdacdace3e4eeb06e_71.png)

![](img/48121de0488d56abdacdace3e4eeb06e_72.png)

结合使用这两种方法，可以极大地增强Linux服务器SSH访问的安全性。