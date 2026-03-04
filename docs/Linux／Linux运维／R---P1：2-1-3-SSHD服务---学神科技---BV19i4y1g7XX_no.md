# Linux运维：2-1-3：SSHD服务配置和管理 🛠️

在本节课中，我们将学习如何配置和管理SSHD服务。SSHD是Secure Shell Daemon的缩写，它是Linux系统中用于提供安全远程登录服务的守护进程。通过配置SSHD，我们可以增强系统的安全性，并定制远程连接的行为。

## 配置文件概述

上一节我们介绍了SSH的基本概念，本节中我们来看看其核心配置文件。SSHD服务的主要配置文件位于 `/etc/ssh/sshd_config`。文件中的注释行（以 `#` 开头）通常表示默认值或说明。

![](img/ce03e0977efb7e4a012898adf9c0211d_1.png)

## 核心配置项详解

以下是SSHD配置文件中一些关键的配置项及其作用。

### 1. 端口配置 (Port)

![](img/ce03e0977efb7e4a012898adf9c0211d_3.png)

首先，我们设置SSH服务监听的端口号。SSH默认使用22号端口。为了提高安全性，建议修改为其他端口号，以防止被暴力破解。

![](img/ce03e0977efb7e4a012898adf9c0211d_5.png)

**配置示例：**
```bash
Port 22
Port 2222
```
上述配置表示SSH服务同时监听22和2222两个端口。修改端口后，需要重启SSHD服务使配置生效。

![](img/ce03e0977efb7e4a012898adf9c0211d_7.png)

![](img/ce03e0977efb7e4a012898adf9c0211d_9.png)

**验证监听端口命令：**
```bash
netstat -tunlp | grep ssh
```

![](img/ce03e0977efb7e4a012898adf9c0211d_11.png)

### 2. 监听地址 (ListenAddress)

此项用于设置SSHD服务绑定的IP地址。`0.0.0.0` 表示监听所有网络接口的地址。

**配置示例：**
```bash
ListenAddress 192.168.1.100
```
如果服务器不需要从公网接受SSH连接，建议将其设置为内网IP地址，以增强安全性。

### 3. 协议版本 (Protocol)

此项用于选择SSH协议的版本，可选值为1或2。出于安全考虑，建议仅使用更安全的SSHv2协议。

**配置示例：**
```bash
Protocol 2
```

### 4. 主机密钥文件 (HostKey)

![](img/ce03e0977efb7e4a012898adf9c0211d_13.png)

此项指定服务器私钥文件的存放路径。私钥用于加密通信，如果此文件被修改，可能会影响SSH连接。

![](img/ce03e0977efb7e4a012898adf9c0211d_15.png)

**默认配置示例：**
```bash
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key
```

![](img/ce03e0977efb7e4a012898adf9c0211d_17.png)

### 5. 日志记录 (SyslogFacility 与 LogLevel)

![](img/ce03e0977efb7e4a012898adf9c0211d_19.png)

![](img/ce03e0977efb7e4a012898adf9c0211d_21.png)

![](img/ce03e0977efb7e4a012898adf9c0211d_23.png)

当用户通过SSH登录时，系统会记录相关信息。日志的设施类型和级别可以在此配置。

**配置示例：**
```bash
SyslogFacility AUTHPRIV
LogLevel INFO
```
SSHD的认证相关日志通常记录在 `/var/log/secure` 文件中。其存放路径由系统的日志配置文件（如 `/etc/rsyslog.conf`）决定。

### 6. 登录超时设置 (LoginGraceTime)

![](img/ce03e0977efb7e4a012898adf9c0211d_25.png)

![](img/ce03e0977efb7e4a012898adf9c0211d_27.png)

此项定义了客户端成功建立连接后，必须在多少秒内完成认证登录，否则连接将被断开。

![](img/ce03e0977efb7e4a012898adf9c0211d_29.png)

**配置示例：**
```bash
LoginGraceTime 120
```
默认值为120秒（2分钟）。可以根据实际需求调整此值。

![](img/ce03e0977efb7e4a012898adf9c0211d_31.png)

![](img/ce03e0977efb7e4a012898adf9c0211d_33.png)

### 7. 禁止Root用户登录 (PermitRootLogin)

![](img/ce03e0977efb7e4a012898adf9c0211d_35.png)

出于安全考虑，在生产环境中通常禁止root用户直接通过SSH登录。

**配置示例：**
```bash
PermitRootLogin no
```
设置为 `no` 后，用户需先以普通用户身份登录，再通过 `su` 或 `sudo` 命令切换为root用户。

### 8. 密码认证与空密码登录 (PasswordAuthentication 与 PermitEmptyPasswords)

这两项控制是否允许使用密码认证和是否允许空密码用户登录。

**安全配置示例：**
```bash
PasswordAuthentication yes
PermitEmptyPasswords no
```
在高安全级别环境中，可能会禁用密码登录，仅允许使用密钥对认证。

![](img/ce03e0977efb7e4a012898adf9c0211d_37.png)

### 9. 登录提示信息 (Banner)

![](img/ce03e0977efb7e4a012898adf9c0211d_39.png)

可以为SSH登录设置一个自定义的警告或提示信息，该信息存放在 `/etc/motd` 文件中。

![](img/ce03e0977efb7e4a012898adf9c0211d_41.png)

![](img/ce03e0977efb7e4a012898adf9c0211d_43.png)

**设置示例：**
```bash
echo “警告：您已进入受监控的系统，请注意您的操作行为。” > /etc/motd
```
用户登录时，会看到此提示信息。

![](img/ce03e0977efb7e4a012898adf9c0211d_45.png)

### 10. 显示上次登录信息 (PrintLastLog)

此项控制是否在用户登录成功后显示上次登录的详细信息（如时间、来源IP）。

**配置示例：**
```bash
PrintLastLog yes
```

### 11. 客户端反向DNS解析 (UseDNS)

![](img/ce03e0977efb7e4a012898adf9c0211d_47.png)

![](img/ce03e0977efb7e4a012898adf9c0211d_48.png)

此项控制SSH服务器是否尝试对客户端IP进行反向DNS解析以验证主机名。在内网环境中，可以将其关闭以加快连接速度。

![](img/ce03e0977efb7e4a012898adf9c0211d_50.png)

**配置示例：**
```bash
UseDNS no
```

## 总结

![](img/ce03e0977efb7e4a012898adf9c0211d_52.png)

本节课中我们一起学习了SSHD服务的主要配置项及其管理方法。我们了解了如何通过修改 `/etc/ssh/sshd_config` 文件来调整SSH端口、增强认证安全、设置登录超时和提示信息等。合理的SSHD配置是保障Linux服务器远程访问安全的重要基础。请记住，任何配置修改后都需要执行 `systemctl restart sshd` 命令来重启服务，以使更改生效。