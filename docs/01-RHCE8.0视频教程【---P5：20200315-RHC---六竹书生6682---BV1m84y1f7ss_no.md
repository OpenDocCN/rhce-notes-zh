# RHCE8.0视频教程：02：进程优先级与服务管理

![](img/53ba6e7a02b637d78ef9d3b78426dd52_0.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_2.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_4.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_6.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_8.png)

在本节课中，我们将要学习如何管理系统进程的优先级，以及如何对系统服务进行基本的管理操作。这些技能对于优化系统性能和确保关键服务正常运行至关重要。

---

![](img/53ba6e7a02b637d78ef9d3b78426dd52_10.png)

## 进程优先级管理

上一节我们介绍了如何查看系统进程，本节中我们来看看如何在系统资源紧张时，确保重要进程能够优先执行。这涉及到进程的优先级调整。

系统优先级由两部分组成：优先系数和nice值。优先系数需要修改内核参数，这通常在RHCA的系统调优课程中学习。而nice值则可以通过命令直接修改，是我们学习的重点。

优先级的范围是 **-20 到 19**。数值越小，优先级越高。因此，**-20** 表示最高优先级，**19** 表示最低优先级。

### 图形化界面调整优先级

![](img/53ba6e7a02b637d78ef9d3b78426dd52_12.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_14.png)

在图形化界面的系统监视器中，可以选中某个进程，右键调整其优先级。优先级被分为几个等级：
*   非常高 (-20 到 -8)
*   高 (-7 到 -3)
*   正常 (-2 到 2)
*   低 (3 到 6)
*   非常低 (7 到 19)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_16.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_18.png)

### 命令行调整优先级

如果系统没有图形界面，或者希望使用命令操作，可以按以下步骤进行。

![](img/53ba6e7a02b637d78ef9d3b78426dd52_20.png)

首先，为了演示优先级的效果，我们需要启动两个高资源占用的进程。以下命令会持续从 `/dev/zero`（一个空设备）读取数据并丢弃，从而大量消耗CPU资源。

![](img/53ba6e7a02b637d78ef9d3b78426dd52_22.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_24.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_26.png)

```bash
cat /dev/zero > /dev/null &
```

![](img/53ba6e7a02b637d78ef9d3b78426dd52_28.png)

使用 `top` 命令查看进程，可以观察到两个 `cat` 进程（例如PID 7517和7573）的CPU使用率大致相当，因为它们的默认nice值都是0。

![](img/53ba6e7a02b637d78ef9d3b78426dd52_30.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_32.png)

现在，我们将PID为7517的进程优先级调高。使用 `renice` 命令：

![](img/53ba6e7a02b637d78ef9d3b78426dd52_34.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_36.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_38.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_40.png)

```bash
renice -n -20 7517
```

![](img/53ba6e7a02b637d78ef9d3b78426dd52_42.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_44.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_46.png)

再次使用 `top` 命令观察，会发现PID 7517的进程占用了绝大部分CPU资源，而另一个进程的资源占用则大幅下降。这证明了优先级调整在资源争抢时生效。

在 `top` 界面中，也可以直接按 `r` 键，然后输入进程PID和新的nice值来实时调整优先级。

调整完毕后，可以使用以下命令关闭所有相关的 `cat` 进程：

```bash
pkill -9 cat
```

### 启动时设置优先级

如果希望在进程启动时就赋予其较高的优先级，可以使用 `nice` 命令。这在系统本身负载较高，但又有重要进程需要运行时非常有用。

```bash
nice -n -5 cat /dev/zero > /dev/null &
```

这样启动的 `cat` 进程会直接拥有较高的优先级（nice值为-5）。

---

## 系统服务管理

![](img/53ba6e7a02b637d78ef9d3b78426dd52_48.png)

接下来，我们进入系统服务管理的学习。在RHEL 7及之前的版本中，服务管理是考试的重点。在RHEL 8中，它依然非常重要，因为许多网络服务（如NFS、Samba）都需要配置为开机自动启动，否则重启后服务失效会导致考试丢分。

服务管理主要关注两个方面：
1.  **开机是否自动启动** (Enabled/Disabled)
2.  **当前运行状态** (Active/Inactive)

### 查看服务状态

管理服务的主要命令是 `systemctl`。

![](img/53ba6e7a02b637d78ef9d3b78426dd52_50.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_52.png)

*   查看服务是否开机自启：
    ```bash
    systemctl is-enabled sshd
    ```
    输出 `enabled` 表示开机自启，`disabled` 表示不会。

![](img/53ba6e7a02b637d78ef9d3b78426dd52_54.png)

*   查看服务当前运行状态：
    ```bash
    systemctl is-active sshd
    ```
    输出 `active` 表示正在运行，`inactive` 表示已停止。

*   查看服务的详细信息（包含以上两种状态）：
    ```bash
    systemctl status vsftpd
    ```

### 查找与列出服务

![](img/53ba6e7a02b637d78ef9d3b78426dd52_56.png)

系统中有很多服务，可以使用以下命令查找或列出。

*   列出所有服务的详细信息：
    ```bash
    systemctl list-unit-files
    ```

*   列出所有服务（简洁信息）：
    ```bash
    systemctl list-units
    ```

*   结合 `grep` 命令查找特定服务（例如包含`ftp`的）：
    ```bash
    systemctl list-unit-files | grep ftp
    ```

### 管理服务状态

以下是管理服务状态的核心命令。

*   **设置开机自启**：
    ```bash
    systemctl enable vsftpd
    ```

*   **取消开机自启**：
    ```bash
    systemctl disable vsftpd
    ```

*   **启动服务**：
    ```bash
    systemctl start vsftpd
    ```

*   **停止服务**：
    ```bash
    systemctl stop vsftpd
    ```

*   **重启服务**（服务会中断）：
    ```bash
    systemctl restart vsftpd
    ```

*   **重新加载配置**（不中断服务，仅更新配置）：
    ```bash
    systemctl reload vsftpd
    ```
    更安全的做法是使用 `reload-or-restart`，它会尝试重新加载配置，如果失败则执行重启。
    ```bash
    systemctl reload-or-restart vsftpd
    ```

**重要提示**：执行 `enable`、`disable`、`start`、`stop` 等命令后，系统可能没有明确提示。为了确保操作成功，务必随后使用 `is-enabled` 或 `is-active` 命令进行验证。

![](img/53ba6e7a02b637d78ef9d3b78426dd52_58.png)

### 服务的特殊状态：Mask（禁用）

服务还有一种特殊状态叫 `mask`（禁用）。处于此状态的服务，既不能开机自启，也无法被手动启动，相当于被“锁死”。

![](img/53ba6e7a02b637d78ef9d3b78426dd52_60.png)

*   **禁用服务**：
    ```bash
    systemctl mask vsftpd
    ```
    此时尝试启动服务会报错。

![](img/53ba6e7a02b637d78ef9d3b78426dd52_62.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_64.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_66.png)

*   **解除禁用**：
    ```bash
    systemctl unmask vsftpd
    ```
    解除禁用后，服务可以恢复为 `enabled` 或 `disabled` 状态，并能被正常启动或停止。

![](img/53ba6e7a02b637d78ef9d3b78426dd52_68.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_70.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_72.png)

---

![](img/53ba6e7a02b637d78ef9d3b78426dd52_74.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_76.png)

## OpenSSH 远程管理

![](img/53ba6e7a02b637d78ef9d3b78426dd52_78.png)

现在，我们开始学习OpenSSH，这是进行远程系统管理的核心工具。SSH（Secure Shell）提供了加密的远程登录会话，比Telnet等明文协议安全得多。

![](img/53ba6e7a02b637d78ef9d3b78426dd52_80.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_82.png)

### SSH 基本登录

![](img/53ba6e7a02b637d78ef9d3b78426dd52_84.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_86.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_87.png)

默认情况下，SSH服务（`sshd`）是开机自启并正在运行的。

![](img/53ba6e7a02b637d78ef9d3b78426dd52_89.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_91.png)

从一台Linux主机登录到另一台Linux主机，基本命令格式如下：

![](img/53ba6e7a02b637d78ef9d3b78426dd52_93.png)

```bash
ssh username@host_ip_address
```

![](img/53ba6e7a02b637d78ef9d3b78426dd52_95.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_97.png)

例如，以用户 `redhat` 登录到 `192.168.127.128`：
```bash
ssh redhat@192.168.127.128
```

![](img/53ba6e7a02b637d78ef9d3b78426dd52_99.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_101.png)

第一次连接某台主机时，系统会询问是否保存该主机的指纹密钥信息，必须输入 `yes` 才能继续。然后，输入**目标主机**上对应用户的密码即可登录。

如果省略用户名，则会以当前本地用户名尝试登录远程主机：
```bash
ssh 192.168.127.128
```

**重要原则**：远程登录时，使用的用户名必须在目标主机上已存在，否则即使密码正确也无法登录。

### SSH 主机密钥认证

首次连接时保存的远程主机指纹信息，存储在当前用户家目录的 `~/.ssh/known_hosts` 文件中。如果远程主机的密钥发生变更（例如重装系统），本地再次连接时会报错“WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED”。

此时，需要手动编辑 `~/.ssh/known_hosts` 文件，删除对应主机的记录行，然后重新连接并接受新的密钥。

### SSH 密钥对认证（免密登录）

为了更安全和便捷，可以配置密钥对认证，实现免密码登录。其原理是使用非对称加密。

![](img/53ba6e7a02b637d78ef9d3b78426dd52_103.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_105.png)

**操作流程如下：**

假设主机A上的用户`user1`希望免密登录到主机B上的用户`user2`。

1.  **在主机A（客户端）生成密钥对**：
    ```bash
    ssh-keygen
    ```
    执行后会生成私钥 `~/.ssh/id_rsa` 和公钥 `~/.ssh/id_rsa.pub`。可以设置密钥的密码（增加一层保护），也可以直接回车留空。

2.  **将公钥传输到主机B（服务器）**：
    使用 `ssh-copy-id` 命令可以自动完成传输和配置。
    ```bash
    ssh-copy-id -i ~/.ssh/id_rsa.pub user2@192.168.127.128
    ```
    首次传输需要输入 `user2` 在主机B上的密码。成功后，公钥会被添加到主机B上 `user2` 用户家目录的 `~/.ssh/authorized_keys` 文件中。

3.  **测试免密登录**：
    完成以上步骤后，从主机A的`user1`登录主机B的`user2`就不再需要输入密码了。
    ```bash
    ssh user2@192.168.127.128
    ```

### SSH 服务端配置

SSH服务的配置文件位于 `/etc/ssh/sshd_config`。修改此文件后，需要重启 `sshd` 服务才能生效。

![](img/53ba6e7a02b637d78ef9d3b78426dd52_107.png)

两个常用的安全配置选项：

*   **禁止root用户远程登录**：
    将 `PermitRootLogin` 的值改为 `no`。这样即使知道root密码，也无法直接通过SSH登录root账户。管理员可以先登录普通用户，再使用 `su` 或 `sudo` 切换权限。
    ```bash
    # 修改配置文件
    PermitRootLogin no
    # 重启服务
    systemctl restart sshd
    ```

*   **禁用密码认证，强制使用密钥登录**：
    将 `PasswordAuthentication` 的值改为 `no`。这可以极大提升安全性，防止暴力破解密码。
    ```bash
    PasswordAuthentication no
    ```

---

## 系统日志管理 (rsyslog)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_109.png)

![](img/53ba6e7a02b637d78ef9d3b78426dd52_111.png)

最后，我们简要了解系统日志服务 `rsyslog`。它负责收集和存储系统及各种应用程序的日志。

![](img/53ba6e7a02b637d78ef9d3b78426dd52_113.png)

### 日志配置文件

`rsyslog` 的主配置文件是 `/etc/rsyslog.conf`，它还可以包含 `/etc/rsyslog.d/` 目录下以 `.conf` 结尾的配置文件。

配置文件的核心是“规则”，定义了哪些服务的什么级别的日志，记录到何处。
规则格式通常为：
```
facility.level    action
```
*   **facility（设施）**：指定日志消息的来源，如 `auth`（认证）、`cron`（计划任务）、`kern`（内核）、`mail`（邮件）、`*`（所有）。
*   **level（级别）**：指定日志的严重等级，从低到高依次为：`debug`, `info`, `notice`, `warning`, `err`, `crit`, `alert`, `emerg`。使用 `*` 表示所有级别。
*   **action（动作）**：指定日志的去向，如文件路径（`/var/log/messages`）、远程主机（`@192.168.1.100`）、或用户终端等。

例如，规则 `*.info;mail.none;authpriv.none;cron.none /var/log/messages` 表示将所有设施的 `info` 及以上级别日志（除了mail、authpriv、cron）记录到 `/var/log/messages` 文件。

### 测试日志生成

可以使用 `logger` 命令模拟生成日志。

```bash
# 生成一条设施为 local5，级别为 info 的日志
logger -p local5.info “This is a test log message”
```
然后可以去配置文件中 `local5.info` 指定的文件（如果已配置）或默认的 `/var/log/messages` 中查看这条记录。

### 配置远程日志

可以将一台主机（客户端）的日志发送到另一台主机（服务器）进行集中存储。

1.  **在客户端** (`/etc/rsyslog.conf`)：
    *   启用TCP/UDP传输模块（取消对应行的注释）。
    *   添加规则，将日志发送到服务器。例如，将所有日志发送到服务器 192.168.127.128：
        ```
        *.* @192.168.127.128
        ```
        (`@`表示UDP，`@@`表示TCP)

2.  **在服务器** (`/etc/rsyslog.conf`)：
    *   启用TCP/UDP接收模块。
    *   添加规则，定义如何处理接收到的客户端日志。例如，将来自所有主机的日志存放到 `/var/log/from_remote`：
        ```
        *.* /var/log/from_remote
        ```

![](img/53ba6e7a02b637d78ef9d3b78426dd52_115.png)

3.  修改配置后，**重启**客户端和服务器的 `rsyslog` 服务。

---

![](img/53ba6e7a02b637d78ef9d3b78426dd52_117.png)

本节课中我们一起学习了进程优先级的调整方法、系统服务（`systemctl`）的完整管理流程、OpenSSH远程登录与密钥认证配置，以及系统日志服务 `rsyslog` 的基本原理和配置。这些都是Linux系统管理员日常维护和优化系统时必须掌握的核心技能。