# RHCE8.0视频教程：P5：进程优先级与服务管理

![](img/8c8b0e8617494fd3928ab85a84fa7515_0.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_2.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_4.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_6.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_8.png)

## 概述
在本节课中，我们将学习两个核心主题：**进程优先级管理**与**系统服务管理**。首先，我们将了解如何在系统资源紧张时，通过调整进程的优先级来确保关键任务优先执行。随后，我们将深入探讨如何管理系统服务，包括服务的启动、停止、开机自启等配置，并重点讲解远程管理服务SSH的原理与配置。

![](img/8c8b0e8617494fd3928ab85a84fa7515_10.png)

---

## 进程优先级管理

上一节我们介绍了如何查看系统进程。本节中我们来看看，当系统比较繁忙时，如何保证一些重点进程能优先执行。

![](img/8c8b0e8617494fd3928ab85a84fa7515_12.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_14.png)

系统进程的优先级由两部分组成：
1.  **优先系数**：这部分需要修改内核参数，在RHCA的系统调优课程中会涉及。
2.  **Nice值**：这是我们**可以通过命令修改**的部分，也是本节的重点。

![](img/8c8b0e8617494fd3928ab85a84fa7515_16.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_18.png)

优先级的范围是 **-20 到 19**。其中，**-20 优先级最高，19 优先级最低**。数值越小，进程越优先。

![](img/8c8b0e8617494fd3928ab85a84fa7515_20.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_22.png)

### 图形化界面调整优先级
在图形化界面的系统监视器中，可以右键点击进程来调整优先级。它通常分为几个等级：非常高、高、正常、低、非常低，分别对应不同的Nice值范围。

![](img/8c8b0e8617494fd3928ab85a84fa7515_24.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_26.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_28.png)

### 命令行调整优先级
如果系统没有图形界面，我们需要使用命令来调整。首先，为了演示优先级的效果，我们需要启动两个高资源占用的进程。

![](img/8c8b0e8617494fd3928ab85a84fa7515_30.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_32.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_34.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_36.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_38.png)

以下是操作步骤：

![](img/8c8b0e8617494fd3928ab85a84fa7515_40.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_42.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_44.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_46.png)

1.  **启动高资源占用进程**：使用 `cat` 命令不断读取 `/dev/zero`（空设备）并丢弃输出，以模拟高CPU占用。
    ```bash
    cat /dev/zero > /dev/null &
    ```
    使用 `top` 命令查看进程ID和资源占用情况。

2.  **查看默认优先级**：默认情况下，所有进程的Nice值都是0，CPU资源会大致平均分配。

3.  **调整进程优先级**：使用 `renice` 命令修改指定进程的Nice值。
    ```bash
    renice -n -20 [PID]
    ```
    例如，将PID为7517的进程优先级调整为最高（-20）。调整后，在 `top` 中观察，该进程将获得绝大部分CPU资源，而另一个进程的占用率会显著下降。

4.  **在top界面中直接调整**：在 `top` 界面中，按 `r` 键，然后输入进程PID和新的Nice值，即可实时调整。

5.  **关闭测试进程**：使用 `pkill` 命令关闭所有相关的测试进程。
    ```bash
    pkill -9 cat
    ```

### 启动时指定优先级
如果系统已经很卡，但有一个重要进程需要启动并优先执行，可以在启动命令前加上 `nice`。
```bash
nice -n -5 cat /dev/zero > /dev/null &
```
这条命令会以Nice值为-5的优先级启动进程。

**总结**：本节我们学习了进程优先级的查看与调整。通过 `renice` 命令可以调整运行中进程的优先级，而 `nice` 命令可以在启动时直接指定优先级。

![](img/8c8b0e8617494fd3928ab85a84fa7515_48.png)

---

## 系统服务管理

![](img/8c8b0e8617494fd3928ab85a84fa7515_50.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_52.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_54.png)

在RHEL 7及之前的版本中，服务管理是考试的重点。在RHEL 8中，它依然非常重要，因为如果服务没有设置为开机自启，重启后服务失效，相关考题将被视为未完成。

服务管理主要关注两个方面：
1.  **开机是否自动启动**。
2.  **当前运行状态**（运行中或已停止）。

管理服务的主要命令是 `systemctl`。

![](img/8c8b0e8617494fd3928ab85a84fa7515_56.png)

### 查看服务状态
*   **查看服务是否开机自启**：
    ```bash
    systemctl is-enabled sshd
    ```
    返回 `enabled` 表示开机自启，`disabled` 表示不会。
*   **查看服务当前运行状态**：
    ```bash
    systemctl is-active sshd
    ```
    返回 `active` 表示正在运行，`inactive` 表示已停止。
*   **查看服务的详细信息**（推荐）：
    ```bash
    systemctl status sshd
    ```
    这条命令可以同时看到**是否启用**和**是否活动**的状态，以及服务的日志片段，在服务启动失败时用于排查问题非常有用。

### 查找服务
系统中有很多服务，可以使用以下命令列出并过滤：
```bash
systemctl list-unit-files | grep ssh
```

### 服务状态管理
服务有三种启用状态和两种运行状态。

**启用状态（是否开机自启）**：
*   `enable`：开机自启。
*   `disable`：开机不自启。
*   `mask`：**禁用**。服务不仅开机不自启，而且**无法被手动启动**，除非先解除禁用(`unmask`)。

**设置启用状态命令**：
```bash
systemctl enable vsftpd    # 设置开机自启
systemctl disable vsftpd   # 取消开机自启
systemctl mask vsftpd      # 禁用服务
systemctl unmask vsftpd    # 解除禁用
```

**运行状态（当前是否活动）**：
*   `start`：启动服务。
*   `stop`：停止服务。
*   `restart`：重启服务（先stop，再start，服务会中断）。
*   `reload`：重新加载配置文件（服务不中断，但并非所有服务都支持）。
*   `reload-or-restart`：尝试重新加载配置，如果失败则执行重启（更安全）。

**设置运行状态命令**：
```bash
systemctl start vsftpd
systemctl stop vsftpd
systemctl restart vsftpd
systemctl reload vsftpd
systemctl reload-or-restart vsftpd
```

**重要提示**：执行 `enable`/`disable`/`start`/`stop` 等命令后，**系统通常没有明显提示**。因此，操作完成后务必使用 `systemctl status [服务名]` 来确认状态是否设置成功。

![](img/8c8b0e8617494fd3928ab85a84fa7515_58.png)

**总结**：本节我们学习了如何使用 `systemctl` 命令全面管理系统服务，包括查询、设置开机自启、启动、停止、重启等操作，这是Linux系统管理的基础技能。

![](img/8c8b0e8617494fd3928ab85a84fa7515_60.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_62.png)

---

![](img/8c8b0e8617494fd3928ab85a84fa7515_64.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_66.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_68.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_70.png)

## OpenSSH 远程管理

![](img/8c8b0e8617494fd3928ab85a84fa7515_72.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_74.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_76.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_78.png)

在实际工作或考试环境中，我们经常需要通过一台机器远程控制另一台机器。SSH（Secure Shell）是一种加密的远程登录协议，比Telnet等明文协议更安全。

![](img/8c8b0e8617494fd3928ab85a84fa7515_80.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_82.png)

### SSH 基本登录
默认情况下，SSH服务（`sshd`）是开机自启且正在运行的。

![](img/8c8b0e8617494fd3928ab85a84fa7515_84.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_86.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_87.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_89.png)

**从Linux登录到另一台Linux**：
```bash
ssh username@host_ip_address
```
例如：`ssh redhat@192.168.127.128`
*   首次连接时，会询问是否保存远程主机的指纹密钥信息，必须输入 `yes`。
*   接着，输入**远程主机**上对应用户的密码。

![](img/8c8b0e8617494fd3928ab85a84fa7515_91.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_93.png)

**注意事项**：用于登录的用户必须在远程主机上已存在，否则即使密码正确也无法登录。

![](img/8c8b0e8617494fd3928ab85a84fa7515_95.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_97.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_99.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_101.png)

### SSH 密钥对认证（免密登录）
为了避免每次登录都输入密码，可以设置密钥对认证。

**原理简述**：
1.  在客户端生成一对密钥：公钥（`id_rsa.pub`）和私钥（`id_rsa`）。
2.  将公钥上传到服务器对应用户的 `~/.ssh/authorized_keys` 文件中。
3.  后续登录时，客户端和服务器通过加密算法验证私钥和公钥的匹配关系，匹配成功则无需密码。

**操作步骤**：
1.  **在客户端生成密钥对**：
    ```bash
    ssh-keygen -t rsa
    ```
    执行后按回车使用默认设置，并设置空密码（或根据需要设置密钥密码）。
2.  **将公钥上传到服务器**：
    ```bash
    ssh-copy-id username@host_ip_address
    ```
    例如：`ssh-copy-id user2@192.168.127.128`。此命令会要求输入一次服务器用户的密码。
3.  **测试免密登录**：
    ```bash
    ssh username@host_ip_address
    ```
    此时应该可以直接登录，无需输入密码。

### SSH 常见问题与配置

**常见问题**：
*   **“WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!”**：此错误表示远程主机的指纹信息与本地存储（`~/.ssh/known_hosts`）的记录不一致。可能原因是远程主机密钥重置或遭遇中间人攻击。解决方法：删除 `~/.ssh/known_hosts` 文件中对应主机的记录行，重新连接。
*   **“Connection closed by remote host”**：可能是本地SSH的加解密相关文件（`/etc/ssh/ssh_host_*`）被误删。解决方法：重启 `sshd` 服务以重新生成这些文件。

![](img/8c8b0e8617494fd3928ab85a84fa7515_103.png)

**服务器端配置文件** (`/etc/ssh/sshd_config`)：
*   `Port 22`：修改SSH服务监听的端口。
*   `PermitRootLogin yes`：改为 `no` 可以禁止root用户直接SSH登录，提升安全性。如需root权限，可先登录普通用户再切换。
*   `PasswordAuthentication yes`：改为 `no` 可以禁用密码登录，强制使用密钥认证，进一步提升安全性。
修改配置后需重启服务：`systemctl restart sshd`

![](img/8c8b0e8617494fd3928ab85a84fa7515_105.png)

**客户端配置文件** (`/etc/ssh/ssh_config` 或 `~/.ssh/config`)：
*   可以配置如默认端口、是否自动接受新主机密钥等行为。

**总结**：本节我们深入学习了SSH远程管理。我们掌握了基本的密码登录方式，并重点实践了更安全、便捷的密钥对免密登录配置。同时，了解了常见的连接问题排查方法以及关键的安全配置选项。

---

## 系统日志管理 (rsyslog)

![](img/8c8b0e8617494fd3928ab85a84fa7515_107.png)

系统日志记录了系统运行的详细信息，是故障排查的重要依据。RHEL 8 使用 `rsyslog` 服务来管理系统日志。

### 日志服务与配置
*   **服务名称**：`rsyslog`
*   **主配置文件**：`/etc/rsyslog.conf`
*   **自定义配置目录**：`/etc/rsyslog.d/*.conf`

### 配置文件规则
配置文件中的规则决定了哪些日志记录到哪里。规则格式如下：
```
设施.优先级        动作
```
*   **设施**：指定产生日志的子系统，如 `auth`（认证）、`cron`（计划任务）、`kern`（内核）、`mail`（邮件）、`*`（所有设施）等。还可以使用 `local0` 到 `local7` 供用户自定义程序使用。
*   **优先级**：定义日志的严重等级，从低到高依次为：`debug`, `info`, `notice`, `warning`, `err`, `crit`, `alert`, `emerg`。使用 `*` 表示所有优先级。规则匹配 **大于等于** 指定优先级的日志。
*   **动作**：定义日志的去向，可以是文件路径（如 `/var/log/messages`）、远程主机（如 `@192.168.1.100`）或用户终端等。

![](img/8c8b0e8617494fd3928ab85a84fa7515_109.png)

![](img/8c8b0e8617494fd3928ab85a84fa7515_111.png)

**示例规则**：
```
*.info;mail.none;authpriv.none;cron.none    /var/log/messages
# 记录所有设施的info及以上级别日志，但排除mail, authpriv, cron设施。
# 记录到 /var/log/messages 文件。

![](img/8c8b0e8617494fd3928ab85a84fa7515_113.png)

authpriv.*                                /var/log/secure
# 记录authpriv设施的所有级别日志到 /var/log/secure 文件。
```

### 测试日志生成
可以使用 `logger` 命令模拟生成日志。
```bash
logger -p local5.info "This is a test log message from local5 facility."
```
这条命令会以 `local5` 设施、`info` 级别生成一条日志。如果配置了相应的规则（例如将 `local5.*` 记录到特定文件），就可以在对应文件中看到这条记录。

### 配置远程日志服务器
可以将多台服务器的日志集中发送到一台日志服务器上。

1.  **在客户端配置**：修改 `/etc/rsyslog.conf`，添加规则将日志发送到服务器。
    ```
    *.* @192.168.127.128:514
    # 将所有日志通过UDP(默认)发送到192.168.127.128的514端口
    # 使用 @@ 则表示TCP传输
    ```
    确保 `$ModLoad imudp` 或 `$ModLoad imtcp` 模块被启用。重启 `rsyslog` 服务。

2.  **在服务器端配置**：修改 `/etc/rsyslog.conf`，启用相应的接收模块（如 `$ModLoad imudp`），并添加规则来处理接收到的日志，例如根据发送方IP或设施将其记录到不同文件。
    ```
    $template RemoteLogs, "/var/log/remote/%HOSTNAME%/%PROGRAMNAME%.log"
    *.* ?RemoteLogs
    ```
    重启 `rsyslog` 服务。

**总结**：本节我们介绍了 `rsyslog` 日志服务的基本管理。通过理解其配置规则，我们可以控制日志的记录位置和级别。同时，也了解了如何配置简单的集中式日志收集，这对于管理多台服务器至关重要。

---

![](img/8c8b0e8617494fd3928ab85a84fa7515_115.png)

## 课程总结
本节课中我们一起学习了：
1.  **进程优先级管理**：理解了Nice值的概念，掌握了使用 `nice`、`renice` 和 `top` 命令调整进程优先级的方法。
2.  **系统服务管理**：熟练运用 `systemctl` 命令来查看、启动、停止、启用、禁用系统服务，这是管理Linux系统的基础。
3.  **OpenSSH远程管理**：掌握了SSH密码登录和更安全的密钥对免密登录配置，并学习了基本的服务端安全配置。
4.  **系统日志管理**：了解了 `rsyslog` 服务的工作原理和配置方法，能够进行基本的日志路由和集中管理配置。

![](img/8c8b0e8617494fd3928ab85a84fa7515_117.png)

这些技能是成为一名合格的Linux系统管理员的核心能力，请务必多加练习。