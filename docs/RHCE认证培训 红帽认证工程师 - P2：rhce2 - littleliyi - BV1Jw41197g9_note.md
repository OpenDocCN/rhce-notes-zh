# RHCE认证培训：P2：SSH密钥、免密登录与系统日志管理 🔑

![](img/2725d80c2178d31a992b78dc12855dc3_1.png)

![](img/2725d80c2178d31a992b78dc12855dc3_3.png)

在本节课中，我们将要学习SSH服务中的核心安全机制——非对称加密与密钥对，并实践配置免密登录。同时，我们也将了解Linux系统中日志服务的工作原理、存放位置以及如何管理和轮转日志文件，这对于系统维护和故障排查至关重要。

## 理论：SSH加密与密钥对 🔐

![](img/2725d80c2178d31a992b78dc12855dc3_5.png)

![](img/2725d80c2178d31a992b78dc12855dc3_6.png)

![](img/2725d80c2178d31a992b78dc12855dc3_8.png)

![](img/2725d80c2178d31a992b78dc12855dc3_10.png)

![](img/2725d80c2178d31a992b78dc12855dc3_12.png)

![](img/2725d80c2178d31a992b78dc12855dc3_13.png)

![](img/2725d80c2178d31a992b78dc12855dc3_15.png)

上一节我们介绍了RHEL8的安装和基础工具使用，本节中我们来看看SSH服务的安全基础。

SSH协议采用密文传输数据，其安全性依赖于加密技术。加密主要分为对称加密和非对称加密。

*   **对称加密**：加密和解密使用**同一个密钥**。安全性较低，一旦密钥泄露，通信即被破解。
*   **非对称加密**：使用一对密钥，即**公钥**和**私钥**。公钥可以公开给任何人，私钥必须严格保密。这是SSH默认且常用的加密方式。

**公钥与私钥的关系**：
*   **公钥**：对外公开，用于**加密**数据或验证签名。
*   **私钥**：私人持有，用于**解密**数据或创建签名。

**通信过程示例**：
假设主机A要与主机B通信。
1.  A使用**B的公钥**加密数据，然后发送给B。
2.  B收到加密数据后，使用**自己的私钥**进行解密，从而获得原始信息。
反之，如果B要发送数据给A，则使用**A的公钥**加密，A再用自己的私钥解密。

虽然加解密过程会比明文传输稍慢，但安全性得到了极大保障。

## 实践：SSH连接与密钥存储 📁

![](img/2725d80c2178d31a992b78dc12855dc3_17.png)

当我们第一次通过SSH连接一台新服务器时，客户端会提示是否保存该服务器的公钥。

![](img/2725d80c2178d31a992b78dc12855dc3_18.png)

![](img/2725d80c2178d31a992b78dc12855dc3_20.png)

![](img/2725d80c2178d31a992b78dc12855dc3_22.png)

![](img/2725d80c2178d31a992b78dc12855dc3_24.png)

![](img/2725d80c2178d31a992b78dc12855dc3_26.png)

![](img/2725d80c2178d31a992b78dc12855dc3_28.png)

```
The authenticity of host ‘192.168.131.129 (192.168.131.129)’ can’t be established.
ECDSA key fingerprint is SHA256:xxxxxxxxxx.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

![](img/2725d80c2178d31a992b78dc12855dc3_30.png)

![](img/2725d80c2178d31a992b78dc12855dc3_32.png)

![](img/2725d80c2178d31a992b78dc12855dc3_33.png)

输入 `yes` 确认后，该服务器的公钥就会被保存在客户端用户的 `~/.ssh/known_hosts` 文件中。此后的连接将不再提示。

![](img/2725d80c2178d31a992b78dc12855dc3_35.png)

**密钥的存放位置**：
每台服务器自身也拥有一对密钥。
*   **公钥存放路径**：`/etc/ssh/ssh_host_*.pub`
*   **私钥存放路径**：`/etc/ssh/ssh_host_*` (无 `.pub` 后缀)

公钥文件通常带有 `.pub` 后缀以便区分。如果服务器重装系统或手动更新了密钥，客户端的 `known_hosts` 文件中旧的记录就会失效，导致连接报错。此时，需要删除 `~/.ssh/known_hosts` 文件中对应服务器IP的那一行记录，重新连接并接受新的公钥。

![](img/2725d80c2178d31a992b78dc12855dc3_37.png)

![](img/2725d80c2178d31a992b78dc12855dc3_38.png)

## 实践：配置SSH免密登录 ⚡

免密登录的原理是：将客户端的公钥预先放置到服务器的指定文件中。这样，当客户端连接时，服务器验证通过即允许登录，无需输入密码。

![](img/2725d80c2178d31a992b78dc12855dc3_40.png)

![](img/2725d80c2178d31a992b78dc12855dc3_42.png)

![](img/2725d80c2178d31a992b78dc12855dc3_44.png)

以下是配置免密登录的步骤：

![](img/2725d80c2178d31a992b78dc12855dc3_46.png)

![](img/2725d80c2178d31a992b78dc12855dc3_48.png)

1.  **在客户端生成密钥对** (如果尚未生成)：
    ```bash
    ssh-keygen
    ```
    连续回车使用默认选项即可，生成的密钥对默认保存在 `~/.ssh/id_rsa` (私钥) 和 `~/.ssh/id_rsa.pub` (公钥)。

![](img/2725d80c2178d31a992b78dc12855dc3_50.png)

![](img/2725d80c2178d31a992b78dc12855dc3_52.png)

![](img/2725d80c2178d31a992b78dc12855dc3_54.png)

2.  **将客户端的公钥传输到目标服务器**：
    ```bash
    ssh-copy-id user@remote_host_ip
    ```
    例如：`ssh-copy-id root@192.168.131.129`
    执行此命令后，需要输入一次目标服务器用户的密码。

3.  **验证免密登录**：
    再次使用 `ssh user@remote_host_ip` 命令连接，应该可以直接登录，无需密码。

![](img/2725d80c2178d31a992b78dc12855dc3_56.png)

![](img/2725d80c2178d31a992b78dc12855dc3_57.png)

![](img/2725d80c2178d31a992b78dc12855dc3_59.png)

![](img/2725d80c2178d31a992b78dc12855dc3_61.png)

![](img/2725d80c2178d31a992b78dc12855dc3_63.png)

![](img/2725d80c2178d31a992b78dc12855dc3_65.png)

![](img/2725d80c2178d31a992b78dc12855dc3_66.png)

![](img/2725d80c2178d31a992b78dc12855dc3_68.png)

![](img/2725d80c2178d31a992b78dc12855dc3_70.png)

![](img/2725d80c2178d31a992b78dc12855dc3_72.png)

**重要提示**：免密登录是基于**用户**的，而不是整台机器。例如，将客户端 `root` 用户的公钥传到服务器的 `root` 用户下，则只有客户端的 `root` 用户能免密登录服务器的 `root` 账户。

![](img/2725d80c2178d31a992b78dc12855dc3_74.png)

![](img/2725d80c2178d31a992b78dc12855dc3_76.png)

![](img/2725d80c2178d31a992b78dc12855dc3_78.png)

![](img/2725d80c2178d31a992b78dc12855dc3_80.png)

![](img/2725d80c2178d31a992b78dc12855dc3_82.png)

![](img/2725d80c2178d31a992b78dc12855dc3_84.png)

![](img/2725d80c2178d31a992b78dc12855dc3_86.png)

![](img/2725d80c2178d31a992b78dc12855dc3_87.png)

## 管理：SSH服务安全配置 🛡️

![](img/2725d80c2178d31a992b78dc12855dc3_89.png)

![](img/2725d80c2178d31a992b78dc12855dc3_91.png)

![](img/2725d80c2178d31a992b78dc12855dc3_93.png)

![](img/2725d80c2178d31a992b78dc12855dc3_94.png)

![](img/2725d80c2178d31a992b78dc12855dc3_96.png)

![](img/2725d80c2178d31a992b78dc12855dc3_98.png)

![](img/2725d80c2178d31a992b78dc12855dc3_99.png)

为了提高安全性，生产环境中通常禁止直接以root用户通过SSH登录。

![](img/2725d80c2178d31a992b78dc12855dc3_100.png)

![](img/2725d80c2178d31a992b78dc12855dc3_101.png)

![](img/2725d80c2178d31a992b78dc12855dc3_103.png)

![](img/2725d80c2178d31a992b78dc12855dc3_104.png)

![](img/2725d80c2178d31a992b78dc12855dc3_106.png)

**修改SSH服务端配置**：
配置文件路径为：`/etc/ssh/sshd_config`

![](img/2725d80c2178d31a992b78dc12855dc3_108.png)

![](img/2725d80c2178d31a992b78dc12855dc3_110.png)

1.  找到 `#PermitRootLogin yes` 这一行。
2.  取消注释，并将 `yes` 改为 `no`。
    ```bash
    PermitRootLogin no
    ```
3.  重启SSH服务使配置生效：
    ```bash
    systemctl restart sshd
    ```
配置后，应先使用普通用户（如 `student`）登录，再通过 `su -` 或 `sudo` 切换为root用户。

![](img/2725d80c2178d31a992b78dc12855dc3_112.png)

**修改SSH默认端口** (可选)：
在 `sshd_config` 中找到 `#Port 22`，取消注释并修改为其他端口（如 `61104`）。修改后需重启 `sshd` 服务，并在防火墙中放行新端口。
```bash
# 添加防火墙规则
firewall-cmd --permanent --add-port=61104/tcp
firewall-cmd --reload
```
连接时需指定端口：`ssh -p 61104 user@host_ip`

![](img/2725d80c2178d31a992b78dc12855dc3_114.png)

## 理论：系统日志服务与查看 📝

![](img/2725d80c2178d31a992b78dc12855dc3_116.png)

系统日志记录了系统运行的各种事件，是排查故障、分析入侵行为的重要依据。

**日志服务**：`rsyslog`
这是Red Hat系统默认的日志守护进程，负责收集和分类日志信息。

**常见日志文件位置**：`/var/log/`
*   `/var/log/messages`：常规系统消息（内核、服务等）的主要日志文件。
*   `/var/log/secure`：安全和身份验证相关日志（登录、sudo、SSH等）。
*   `/var/log/cron`：计划任务 (`cron`/`at`) 的日志。
*   `/var/log/boot.log`：系统启动日志。
*   `/var/log/audit/audit.log`：审计日志（如果启用了`auditd`服务）。

**日志等级**：
日志按严重程度分级，从高到低常见的有：
*   `emerg`/`panic`：系统不可用。
*   `alert`：必须立即采取措施。
*   `err`：错误条件。
*   `warning`：警告条件。
*   `notice`：正常但重要的事件。
*   `info`：信息性事件。
*   `debug`：调试级信息。

**查看日志工具**：
*   `tail -f /var/log/secure`：实时查看安全日志。
*   `journalctl`：查询 `systemd-journald` 收集的日志（内存日志服务）。
*   `dmesg`：查看内核环形缓冲区消息，常用于诊断硬件问题。

![](img/2725d80c2178d31a992b78dc12855dc3_118.png)

![](img/2725d80c2178d31a992b78dc12855dc3_119.png)

![](img/2725d80c2178d31a992b78dc12855dc3_120.png)

![](img/2725d80c2178d31a992b78dc12855dc3_122.png)

## 管理：日志轮转配置 🔄

![](img/2725d80c2178d31a992b78dc12855dc3_124.png)

![](img/2725d80c2178d31a992b78dc12855dc3_126.png)

![](img/2725d80c2178d31a992b78dc12855dc3_128.png)

![](img/2725d80c2178d31a992b78dc12855dc3_130.png)

为了防止日志文件无限增大占满磁盘，系统使用 `logrotate` 工具进行日志轮转。

![](img/2725d80c2178d31a992b78dc12855dc3_132.png)

![](img/2725d80c2178d31a992b78dc12855dc3_134.png)

![](img/2725d80c2178d31a992b78dc12855dc3_136.png)

![](img/2725d80c2178d31a992b78dc12855dc3_138.png)

![](img/2725d80c2178d31a992b78dc12855dc3_139.png)

**轮转配置文件**：`/etc/logrotate.conf` 以及 `/etc/logrotate.d/` 目录下的文件。

![](img/2725d80c2178d31a992b78dc12855dc3_141.png)

![](img/2725d80c2178d31a992b78dc12855dc3_142.png)

![](img/2725d80c2178d31a992b78dc12855dc3_144.png)

![](img/2725d80c2178d31a992b78dc12855dc3_146.png)

**默认轮转策略** (在 `/etc/logrotate.conf` 中常见)：
```bash
weekly    # 每周轮转一次
rotate 4  # 保留4个旧的日志文件
create    # 轮转后创建新的空日志文件
dateext   # 使用日期作为轮转文件的后缀
# compress # 是否压缩旧日志，默认注释掉即不压缩
```

![](img/2725d80c2178d31a992b78dc12855dc3_148.png)

**自定义应用日志轮转**：
可以在 `/etc/logrotate.d/` 下为特定服务创建配置文件。例如，为Nginx配置轮转：
```bash
# /etc/logrotate.d/nginx
/var/log/nginx/*.log {
    daily
    missingok
    rotate 14
    compress
    delaycompress
    notifempty
    create 0640 nginx adm
    sharedscripts
    postrotate
        /bin/kill -USR1 `cat /run/nginx.pid 2>/dev/null` 2>/dev/null || true
    endscript
}
```

修改轮转配置后，通常不需要重启服务。`logrotate` 通常由 `cron` 任务定期执行（如每天），也可以手动强制执行测试：`logrotate -f /etc/logrotate.conf`

![](img/2725d80c2178d31a992b78dc12855dc3_150.png)

![](img/2725d80c2178d31a992b78dc12855dc3_151.png)

![](img/2725d80c2178d31a992b78dc12855dc3_153.png)

![](img/2725d80c2178d31a992b78dc12855dc3_155.png)

---

![](img/2725d80c2178d31a992b78dc12855dc3_157.png)

![](img/2725d80c2178d31a992b78dc12855dc3_158.png)

本节课中我们一起学习了SSH的非对称加密原理，并动手配置了免密登录，这是自动化运维的基础。同时，我们也深入了解了系统日志的管理机制，包括日志的存放、查看和关键的轮转配置，这些技能对于维护系统稳定和安全至关重要。