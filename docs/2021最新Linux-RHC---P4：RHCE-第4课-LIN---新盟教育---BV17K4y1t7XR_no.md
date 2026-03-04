# Linux服务管理：P4：SSHD服务配置与管理

![](img/59d20826bc78f9402174d07b6e8ef608_1.png)

在本节课中，我们将学习Linux下服务管理的基本三步法，并重点掌握SSHD服务的安装、配置、启动与安全调优。SSHD服务是远程连接Linux服务器的核心桥梁，理解其工作原理和配置方法至关重要。

## 服务管理三步法

在Linux系统中部署任何软件或服务，通常遵循三个核心步骤：**安装**、**调试**和**启动**。这套方法论适用于绝大多数场景。

### 安装软件包

安装软件主要有两种方式：

1.  **使用包管理器**：例如 `yum` 或 `rpm` 命令。这是最常用、最便捷的方式，能自动处理依赖关系。
    ```bash
    yum install -y [软件包名]
    ```
2.  **编译源码包**：下载软件的源代码，通过 `configure`、`make`、`make install` 三部曲进行编译安装。这种方式更灵活，但步骤相对复杂。

### 调试与配置

这是最能体现管理员价值的环节。Linux遵循“一切皆文件”的哲学，服务的配置主要通过修改其配置文件来完成。你需要掌握：
*   配置文件的位置和结构。
*   关键参数的含义和作用。
*   在不同场景下如何调整这些参数。

### 启动与自启

![](img/59d20826bc78f9402174d07b6e8ef608_3.png)

配置完成后，使用命令启动服务。更重要的是，需要将服务设置为开机自动启动，以确保服务器重启后服务能自动恢复。
```bash
systemctl start [服务名]
systemctl enable [服务名]
```

![](img/59d20826bc78f9402174d07b6e8ef608_4.png)

![](img/59d20826bc78f9402174d07b6e8ef608_6.png)

![](img/59d20826bc78f9402174d07b6e8ef608_8.png)

**请注意**：软件包名和服务名有时相同（如 `httpd`），有时不同（如 `openssh` 软件包对应的服务是 `sshd`）。需要区分清楚。

![](img/59d20826bc78f9402174d07b6e8ef608_10.png)

## SSHD服务详解

![](img/59d20826bc78f9402174d07b6e8ef608_11.png)

![](img/59d20826bc78f9402174d07b6e8ef608_13.png)

![](img/59d20826bc78f9402174d07b6e8ef608_15.png)

![](img/59d20826bc78f9402174d07b6e8ef608_17.png)

上一节我们介绍了服务管理的通用方法，本节中我们来看看一个具体且至关重要的服务——SSHD。

![](img/59d20826bc78f9402174d07b6e8ef608_19.png)

![](img/59d20826bc78f9402174d07b6e8ef608_21.png)

### 什么是SSHD？

![](img/59d20826bc78f9402174d07b6e8ef608_22.png)

SSHD是Secure Shell Daemon的缩写，即安全Shell守护进程。它用于在客户端和服务器之间建立一个加密的网络协议层，实现安全的远程登录、命令执行和文件传输。

![](img/59d20826bc78f9402174d07b6e8ef608_24.png)

*   **默认端口**：`22`
*   **登录方式**：支持密码认证和密钥对认证。
*   **对比Telnet**：Telnet是明文传输协议（默认端口`23`），安全性远低于加密传输的SSH。

### 端口知识补充

![](img/59d20826bc78f9402174d07b6e8ef608_26.png)

端口是网络服务的逻辑入口，范围是 `0-65535`。

![](img/59d20826bc78f9402174d07b6e8ef608_28.png)

以下是常见的端口分类：

*   **知名端口 (0-1023)**：通常分配给系统级服务。
    *   `20/21` - FTP
    *   `22` - SSH
    *   `23` - Telnet
    *   `53` - DNS
    *   `80` - HTTP
    *   `443` - HTTPS
    *   `110` - POP3
*   **动态/私有端口 (1024-65535)**：可供用户自定义或应用程序使用。
    *   `3306` - MySQL
    *   `3389` - Windows远程桌面(RDP)
    *   `8080` - 常用HTTP代理或应用端口

![](img/59d20826bc78f9402174d07b6e8ef608_30.png)

![](img/59d20826bc78f9402174d07b6e8ef608_31.png)

在生产环境中，出于安全考虑，常将SSH等服务的默认端口改为非知名端口。

![](img/59d20826bc78f9402174d07b6e8ef608_33.png)

![](img/59d20826bc78f9402174d07b6e8ef608_35.png)

### 安装SSHD

![](img/59d20826bc78f9402174d07b6e8ef608_37.png)

SSHD服务通常由 `openssh` 软件包组提供。核心包包括：
*   `openssh`：元数据包。
*   `openssh-clients`：客户端工具包（包含 `ssh` 命令）。
*   `openssh-server`：服务器端守护进程包（包含 `sshd` 服务）。

使用yum安装：
```bash
yum install -y openssh openssh-clients openssh-server
```

![](img/59d20826bc78f9402174d07b6e8ef608_39.png)

![](img/59d20826bc78f9402174d07b6e8ef608_41.png)

安装后，可以通过以下命令查看已安装的相关包：
```bash
yum list installed | grep openssh
```

![](img/59d20826bc78f9402174d07b6e8ef608_43.png)

### 配置文件与启动

![](img/59d20826bc78f9402174d07b6e8ef608_45.png)

![](img/59d20826bc78f9402174d07b6e8ef608_46.png)

SSHD的主要配置文件位于 `/etc/ssh/` 目录下：
*   **`sshd_config`**：**服务器端**配置文件。这是我们配置的重点。
*   **`ssh_config`**：客户端配置文件。

启动服务并设置开机自启（RHEL/CentOS 7+）：
```bash
systemctl start sshd
systemctl enable sshd
```
对于兼容RHEL/CentOS 6的命令，可以使用：
```bash
service sshd start
chkconfig sshd on
```

![](img/59d20826bc78f9402174d07b6e8ef608_48.png)

![](img/59d20826bc78f9402174d07b6e8ef608_49.png)

### 基础连接测试

使用 `ssh` 命令从客户端连接服务器。首次连接时会提示确认服务器指纹信息。
```bash
ssh username@server_ip
# 例如：ssh root@192.168.1.100
```
首次连接的信息会保存在客户端的 `~/.ssh/known_hosts` 文件中。

## SSHD服务安全调优实战

仅仅安装和启动SSHD是不够的。为了服务器安全，必须进行一系列调优配置。我们将逐一修改 `/etc/ssh/sshd_config` 文件中的关键参数。

**重要提示**：修改配置文件前，建议先备份。每次修改后，需要重启 `sshd` 服务使配置生效。
```bash
systemctl restart sshd
```

### 1. 修改默认端口

将默认的22端口改为一个非知名端口，可以大幅减少自动化扫描和攻击。
```
# 找到 #Port 22 这一行，取消注释并修改端口号
Port 2222
# 如果需要监听多个端口，可以添加多行
# Port 22
# Port 2222
```
**注意**：修改端口后，连接时必须使用 `-p` 参数指定端口。
```bash
ssh -p 2222 username@server_ip
```

### 2. 禁止Root用户直接登录

![](img/59d20826bc78f9402174d07b6e8ef608_51.png)

![](img/59d20826bc78f9402174d07b6e8ef608_52.png)

强制攻击者必须首先获得一个普通用户账号，增加了攻击难度。
```
# 找到 #PermitRootLogin yes 这一行，修改为
PermitRootLogin no
```
修改后，管理员应先以普通用户登录，再使用 `su` 或 `sudo` 切换至root权限。

### 3. 使用密钥对认证并禁用密码认证

密钥对认证比密码认证更安全。配置完成后，可以完全禁用密码登录。

**首先，在客户端生成密钥对**：
```bash
ssh-keygen -t rsa -b 2048
# 默认会在 ~/.ssh/ 目录下生成 id_rsa（私钥）和 id_rsa.pub（公钥）
```

**然后，将公钥上传到服务器**：
```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub -p 2222 username@server_ip
# 或者手动将公钥内容添加到服务器的 ~/.ssh/authorized_keys 文件中
```

**最后，修改服务器配置**：
```
# 确保公钥认证开启
PubkeyAuthentication yes

# 指定公钥文件位置（通常保持默认）
AuthorizedKeysFile .ssh/authorized_keys

![](img/59d20826bc78f9402174d07b6e8ef608_54.png)

# 完成密钥配置并测试成功后，禁用密码认证
PasswordAuthentication no
# 同时确保不允许空密码登录
PermitEmptyPasswords no
```
**警告**：在禁用 `PasswordAuthentication` 前，务必确保密钥认证已配置成功并可正常登录，否则可能导致自己也无法远程连接服务器。

![](img/59d20826bc78f9402174d07b6e8ef608_56.png)

![](img/59d20826bc78f9402174d07b6e8ef608_58.png)

### 4. 其他重要安全选项

![](img/59d20826bc78f9402174d07b6e8ef608_60.png)

![](img/59d20826bc78f9402174d07b6e8ef608_62.png)

![](img/59d20826bc78f9402174d07b6e8ef608_64.png)

```
# 限制监听IP（如果服务器有多个IP，可指定内网IP）
ListenAddress 192.168.1.100

# 设置登录超时时间（单位秒）
LoginGraceTime 60

![](img/59d20826bc78f9402174d07b6e8ef608_66.png)

# 限制最大认证尝试次数
MaxAuthTries 3

![](img/59d20826bc78f9402174d07b6e8ef608_67.png)

![](img/59d20826bc78f9402174d07b6e8ef608_68.png)

![](img/59d20826bc78f9402174d07b6e8ef608_70.png)

# 限制最大会话数
MaxSessions 10

# 禁用DNS反向解析，加速连接（内网环境可设置）
UseDNS no
```

![](img/59d20826bc78f9402174d07b6e8ef608_72.png)

![](img/59d20826bc78f9402174d07b6e8ef608_73.png)

### 5. 登录提示与日志监控

设置登录前后的提示信息，并学会查看登录日志。

**设置登录后提示信息**：
编辑 `/etc/motd` 文件，内容将在用户登录后显示。常用于发布系统公告或警告。
```
echo "警告：此系统仅供授权用户使用，所有操作将被记录！" > /etc/motd
```

**查看SSH登录日志**：
SSH的认证日志记录在 `/var/log/secure` 文件中。通过查看此日志，可以监控登录成功、失败以及来源IP等信息，是排查安全事件的重要依据。
```bash
tail -f /var/log/secure
# 或使用 grep 过滤SSH相关记录
grep "sshd" /var/log/secure
```

![](img/59d20826bc78f9402174d07b6e8ef608_75.png)

## 总结与安全建议

![](img/59d20826bc78f9402174d07b6e8ef608_76.png)

![](img/59d20826bc78f9402174d07b6e8ef608_78.png)

本节课中我们一起学习了SSHD服务的完整管理流程。从通用的服务管理三步法（安装、调试、启动），到SSHD的具体配置，特别是多项关键的安全加固措施。

为了有效防御暴力破解等攻击，请务必在生产环境中实施以下组合策略：

1.  **使用强密码策略**：密码长度应大于12位，包含大小写字母、数字和特殊字符，并定期更换。
2.  **修改默认SSH端口**：这是最简单有效的防护措施之一。
3.  **禁止Root直接登录**：降低特权账户暴露的风险。
4.  **采用密钥对认证，禁用密码认证**：从根本上杜绝密码被暴力破解的可能。
5.  **配置防火墙**：使用 `firewalld` 或 `iptables` 限制仅允许可信IP地址访问SSH端口。
6.  **使用Fail2ban等工具**：自动监控日志，将多次尝试失败IP加入黑名单。

![](img/59d20826bc78f9402174d07b6e8ef608_80.png)

![](img/59d20826bc78f9402174d07b6e8ef608_82.png)

通过以上配置，你的SSHD服务安全性将得到显著提升。记住，系统安全是一个持续的过程，需要定期审查和更新策略。