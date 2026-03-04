# Redhat红帽 RHCE8.0认证体系课程：P20：日志管理 📝

![](img/6b31b8840197941b8283e89be49cbee2_1.png)

![](img/6b31b8840197941b8283e89be49cbee2_3.png)

![](img/6b31b8840197941b8283e89be49cbee2_5.png)

![](img/6b31b8840197941b8283e89be49cbee2_7.png)

![](img/6b31b8840197941b8283e89be49cbee2_9.png)

![](img/6b31b8840197941b8283e89be49cbee2_11.png)

在本节课中，我们将要学习Linux系统中的日志管理。日志是系统运行的“摄像头”，它详细记录了用户的操作、服务的状态以及系统的各种事件。理解日志的生成、分类、查看和管理，是系统运维和故障排查的核心技能。我们将从日志的基本概念讲起，逐步深入到日志服务、日志轮转和时间同步等具体操作。

![](img/6b31b8840197941b8283e89be49cbee2_12.png)

![](img/6b31b8840197941b8283e89be49cbee2_14.png)

![](img/6b31b8840197941b8283e89be49cbee2_16.png)

![](img/6b31b8840197941b8283e89be49cbee2_18.png)

## 日志系统概述

![](img/6b31b8840197941b8283e89be49cbee2_20.png)

日志系统的作用是记录系统中发生的各种事件，例如哪个用户在什么时间执行了什么操作。一个完备的操作系统都具备日志系统，并且为了满足信息安全等级保护的要求，通常还具备日志审计功能。

在红帽RHEL系统中，日志主要通过**syslog**协议来记录。syslog协议定义了日志消息的标准格式和传输方式。然而，syslog记录的是原始的、未经整理的信息。要将这些信息整理成人类可读的内容，就需要其他服务的支持。

## 核心日志服务：systemd-journald 与 rsyslog

上一节我们介绍了日志的基本概念，本节中我们来看看RHEL8中两个核心的日志服务：`systemd-journald` 和 `rsyslog`，以及它们之间的关系。

![](img/6b31b8840197941b8283e89be49cbee2_22.png)

![](img/6b31b8840197941b8283e89be49cbee2_24.png)

![](img/6b31b8840197941b8283e89be49cbee2_26.png)

![](img/6b31b8840197941b8283e89be49cbee2_28.png)

*   **systemd-journald**：这是由systemd提供的日志服务。它主要记录与**系统启动、服务和内核**相关的日志。这个服务只在相关进程运行时才会记录日志。它的日志以**二进制**形式**保存在内存**中（路径通常为 `/run/log/journal/`）。因此，系统重启后，这些日志会被清空。
*   **rsyslog**：这是一个更传统的、功能强大的系统日志守护进程。它的一个重要职责就是读取 `systemd-journald` 保存在内存中的二进制日志，然后根据预定义的规则，将其**分类、格式化并转储**为**文本文件**，保存在 `/var/log/` 目录下。

![](img/6b31b8840197941b8283e89be49cbee2_30.png)

![](img/6b31b8840197941b8283e89be49cbee2_32.png)

![](img/6b31b8840197941b8283e89be49cbee2_34.png)

![](img/6b31b8840197941b8283e89be49cbee2_36.png)

![](img/6b31b8840197941b8283e89be49cbee2_38.png)

![](img/6b31b8840197941b8283e89be49cbee2_40.png)

简单来说，`systemd-journald` 负责“记流水账”（原始记录），而 `rsyslog` 负责“整理归档”（分类存储）。我们日常在 `/var/log/` 下看到的各种日志文件，基本都是 `rsyslog.service` 服务整理输出的结果。

![](img/6b31b8840197941b8283e89be49cbee2_42.png)

![](img/6b31b8840197941b8283e89be49cbee2_44.png)

![](img/6b31b8840197941b8283e89be49cbee2_46.png)

## 日志文件分类与格式

![](img/6b31b8840197941b8283e89be49cbee2_48.png)

![](img/6b31b8840197941b8283e89be49cbee2_50.png)

了解了日志服务的分工后，我们来看看 `/var/log/` 目录下常见的日志文件分类。

![](img/6b31b8840197941b8283e89be49cbee2_52.png)

![](img/6b31b8840197941b8283e89be49cbee2_54.png)

![](img/6b31b8840197941b8283e89be49cbee2_56.png)

![](img/6b31b8840197941b8283e89be49cbee2_58.png)

![](img/6b31b8840197941b8283e89be49cbee2_60.png)

![](img/6b31b8840197941b8283e89be49cbee2_62.png)

以下是主要的系统日志文件及其作用：

![](img/6b31b8840197941b8283e89be49cbee2_64.png)

![](img/6b31b8840197941b8283e89be49cbee2_66.png)

![](img/6b31b8840197941b8283e89be49cbee2_68.png)

![](img/6b31b8840197941b8283e89be49cbee2_70.png)

![](img/6b31b8840197941b8283e89be49cbee2_72.png)

![](img/6b31b8840197941b8283e89be49cbee2_74.png)

*   **`/var/log/messages`**：这是**最通用**的日志文件，记录了大多数系统事件消息。但有几个例外不会记录在这里。
*   **`/var/log/secure`**：记录所有与**安全和身份验证**相关的日志，例如用户登录成功或失败、`sudo` 提权等。这类似于Windows系统中的“安全”日志。
*   **`/var/log/maillog`**：记录与**邮件服务器**（如Postfix）相关的日志。
*   **`/var/log/cron`**：记录**计划任务**（cron job）的执行结果。
*   **`/var/log/boot.log`**：记录**系统启动过程**中的消息。
*   **`/var/log/kern.log`**：记录**内核**相关的消息。

![](img/6b31b8840197941b8283e89be49cbee2_76.png)

![](img/6b31b8840197941b8283e89be49cbee2_78.png)

![](img/6b31b8840197941b8283e89be49cbee2_80.png)

除了系统日志，许多应用程序（如Apache HTTPD, Nginx）也有自己的日志格式和存储路径，这些通常在应用程序的配置文件中定义。例如，Apache的日志格式在 `/etc/httpd/conf/httpd.conf` 中通过 `LogFormat` 指令定义。

![](img/6b31b8840197941b8283e89be49cbee2_82.png)

![](img/6b31b8840197941b8283e89be49cbee2_84.png)

![](img/6b31b8840197941b8283e89be49cbee2_86.png)

![](img/6b31b8840197941b8283e89be49cbee2_88.png)

![](img/6b31b8840197941b8283e89be49cbee2_90.png)

一条典型的系统日志格式如下：
```
Jul 3 21:13:01 localhost sshd[36698]: Accepted password for root from 192.168.1.100 port 5000 ssh2
```
其结构为：`时间戳 主机名 服务/进程[PID]： 具体事件描述`。

![](img/6b31b8840197941b8283e89be49cbee2_92.png)

![](img/6b31b8840197941b8283e89be49cbee2_94.png)

![](img/6b31b8840197941b8283e89be49cbee2_96.png)

![](img/6b31b8840197941b8283e89be49cbee2_98.png)

![](img/6b31b8840197941b8283e89be49cbee2_100.png)

![](img/6b31b8840197941b8283e89be49cbee2_102.png)

![](img/6b31b8840197941b8283e89be49cbee2_104.png)

## 如何查看日志

![](img/6b31b8840197941b8283e89be49cbee2_106.png)

![](img/6b31b8840197941b8283e89be49cbee2_108.png)

![](img/6b31b8840197941b8283e89be49cbee2_110.png)

![](img/6b31b8840197941b8283e89be49cbee2_112.png)

![](img/6b31b8840197941b8283e89be49cbee2_114.png)

在Linux运维中，查看日志是常态。你需要知道两件事：**看什么**和**怎么看**。

![](img/6b31b8840197941b8283e89be49cbee2_116.png)

![](img/6b31b8840197941b8283e89be49cbee2_118.png)

![](img/6b31b8840197941b8283e89be49cbee2_120.png)

![](img/6b31b8840197941b8283e89be49cbee2_121.png)

![](img/6b31b8840197941b8283e89be49cbee2_123.png)

1.  **查看特定服务日志**：如果问题与某个具体服务（如 `httpd`, `mysql`）相关，应首先查看该服务自定义的日志文件，路径通常在服务的配置文件中指定。
2.  **查看系统通用日志**：对于系统级问题或服务初始化问题，应查看 `/var/log/` 目录下的相应文件，如 `messages` 或 `secure`。
3.  **使用 systemctl 查看服务日志**：你可以使用 `systemctl status 服务名` 命令来查看某个服务的状态及其最近的日志条目（通常显示最后10行）。例如：
    ```bash
    systemctl status sshd
    ```
4.  **查看内存日志 (journald)**：要查看 `systemd-journald` 收集的原始日志，需使用 `journalctl` 命令。它功能强大，支持按时间、服务、优先级等多种条件筛选。例如：
    ```bash
    journalctl -u sshd --since “2023-10-01” --until “2023-10-02”
    journalctl -p err -b # 查看本次启动以来的所有错误日志
    ```

![](img/6b31b8840197941b8283e89be49cbee2_125.png)

![](img/6b31b8840197941b8283e89be49cbee2_127.png)

![](img/6b31b8840197941b8283e89be49cbee2_129.png)

![](img/6b31b8840197941b8283e89be49cbee2_131.png)

![](img/6b31b8840197941b8283e89be49cbee2_133.png)

![](img/6b31b8840197941b8283e89be49cbee2_135.png)

对于更复杂的场景，如集中式日志管理，企业会使用ELK（Elasticsearch, Logstash, Kibana）或Splunk等专业日志平台。

![](img/6b31b8840197941b8283e89be49cbee2_137.png)

![](img/6b31b8840197941b8283e89be49cbee2_139.png)

![](img/6b31b8840197941b8283e89be49cbee2_141.png)

![](img/6b31b8840197941b8283e89be49cbee2_143.png)

![](img/6b31b8840197941b8283e89be49cbee2_145.png)

![](img/6b31b8840197941b8283e89be49cbee2_147.png)

![](img/6b31b8840197941b8283e89be49cbee2_149.png)

## 配置 rsyslog 日志规则

![](img/6b31b8840197941b8283e89be49cbee2_151.png)

![](img/6b31b8840197941b8283e89be49cbee2_153.png)

![](img/6b31b8840197941b8283e89be49cbee2_155.png)

![](img/6b31b8840197941b8283e89be49cbee2_157.png)

![](img/6b31b8840197941b8283e89be49cbee2_159.png)

![](img/6b31b8840197941b8283e89be49cbee2_161.png)

`rsyslog` 的强大之处在于其灵活的配置能力。其主配置文件是 `/etc/rsyslog.conf`，我们可以在其中定义日志的过滤和存储规则。

配置文件中的规则通常如下形式：
```
authpriv.*                                              /var/log/secure
```
这条规则的意思是：所有设施（facility）为 `authpriv`（认证权限）、任何优先级（`*`）的日志消息，都保存到 `/var/log/secure` 文件中。

![](img/6b31b8840197941b8283e89be49cbee2_163.png)

![](img/6b31b8840197941b8283e89be49cbee2_165.png)

![](img/6b31b8840197941b8283e89be49cbee2_167.png)

![](img/6b31b8840197941b8283e89be49cbee2_169.png)

![](img/6b31b8840197941b8283e89be49cbee2_171.png)

日志消息有**8个优先级（从高到低）**：
*   **0 - emerg**：系统不可用。
*   **1 - alert**：必须立即采取措施。
*   **2 - crit**：严重情况。
*   **3 - err**：错误。
*   **4 - warning**：警告。
*   **5 - notice**：正常但重要的事件。
*   **6 - info**：一般信息。
*   **7 - debug**：调试信息。

![](img/6b31b8840197941b8283e89be49cbee2_173.png)

![](img/6b31b8840197941b8283e89be49cbee2_175.png)

![](img/6b31b8840197941b8283e89be49cbee2_177.png)

![](img/6b31b8840197941b8283e89be49cbee2_179.png)

![](img/6b31b8840197941b8283e89be49cbee2_181.png)

![](img/6b31b8840197941b8283e89be49cbee2_182.png)

在运维中，我们通常重点关注 **error (3)** 及以上级别的日志。你可以在 `/etc/rsyslog.conf` 中自定义规则，例如将不同级别的认证日志存放到不同文件。修改配置后，需要重启 `rsyslog` 服务使其生效：
```bash
systemctl restart rsyslog
```

![](img/6b31b8840197941b8283e89be49cbee2_184.png)

![](img/6b31b8840197941b8283e89be49cbee2_186.png)

![](img/6b31b8840197941b8283e89be49cbee2_188.png)

![](img/6b31b8840197941b8283e89be49cbee2_190.png)

![](img/6b31b8840197941b8283e89be49cbee2_192.png)

![](img/6b31b8840197941b8283e89be49cbee2_194.png)

![](img/6b31b8840197941b8283e89be49cbee2_196.png)

## 日志轮转 (Log Rotation)

![](img/6b31b8840197941b8283e89be49cbee2_198.png)

![](img/6b31b8840197941b8283e89be49cbee2_200.png)

![](img/6b31b8840197941b8283e89be49cbee2_202.png)

![](img/6b31b8840197941b8283e89be49cbee2_203.png)

![](img/6b31b8840197941b8283e89be49cbee2_205.png)

![](img/6b31b8840197941b8283e89be49cbee2_207.png)

如果日志只写入一个文件，该文件会无限增大，最终耗尽磁盘空间。**日志轮转**就是为了解决这个问题，它按照时间或大小等规则，自动对日志文件进行归档、压缩或清理。

![](img/6b31b8840197941b8283e89be49cbee2_209.png)

![](img/6b31b8840197941b8283e89be49cbee2_211.png)

![](img/6b31b8840197941b8283e89be49cbee2_213.png)

![](img/6b31b8840197941b8283e89be49cbee2_215.png)

![](img/6b31b8840197941b8283e89be49cbee2_217.png)

![](img/6b31b8840197941b8283e89be49cbee2_219.png)

RHEL系统使用 `logrotate` 工具管理日志轮转。其主配置文件是 `/etc/logrotate.conf`，而针对特定服务的轮转配置则放在 `/etc/logrotate.d/` 目录下。

![](img/6b31b8840197941b8283e89be49cbee2_221.png)

![](img/6b31b8840197941b8283e89be49cbee2_223.png)

![](img/6b31b8840197941b8283e89be49cbee2_225.png)

![](img/6b31b8840197941b8283e89be49cbee2_227.png)

查看默认的轮转配置：
```bash
cat /etc/logrotate.conf
```
常见的配置参数包括：
*   `weekly`：每周轮转一次。
*   `rotate 4`：保留4个归档日志副本。
*   `create`：轮转后创建新的空日志文件。
*   `compress`：压缩旧的日志文件。
*   `size 10M`：当日志文件达到10MB时进行轮转。

![](img/6b31b8840197941b8283e89be49cbee2_229.png)

![](img/6b31b8840197941b8283e89be49cbee2_231.png)

![](img/6b31b8840197941b8283e89be49cbee2_232.png)

![](img/6b31b8840197941b8283e89be49cbee2_234.png)

![](img/6b31b8840197941b8283e89be49cbee2_235.png)

![](img/6b31b8840197941b8283e89be49cbee2_236.png)

例如，Apache的轮转配置可能在 `/etc/logrotate.d/httpd` 中，它定义了如何轮转 `access_log` 和 `error_log`。

## 配置 systemd-journald 永久存储

默认情况下，`systemd-journald` 的日志存储在内存中。我们可以配置它，将日志持久化保存到磁盘上。

1.  创建存储目录（如果不存在）：
    ```bash
    mkdir -p /var/log/journal
    ```
2.  编辑配置文件 `/etc/systemd/journald.conf`，找到 `#Storage=auto` 这一行，取消注释并将其值改为 `persistent`：
    ```
    Storage=persistent
    ```
3.  重启 `systemd-journald` 服务以应用更改：
    ```bash
    systemctl restart systemd-journald
    ```
之后，二进制日志就会持久化存储在 `/var/log/journal/` 目录下，但查看这些日志仍需使用 `journalctl` 命令。

![](img/6b31b8840197941b8283e89be49cbee2_238.png)

## 系统时间与时钟同步 (Chrony/NTP)

系统时间的准确性对日志分析、计划任务、分布式系统等都至关重要。Linux使用 `timedatectl` 命令管理时间和时区。

*   **查看当前时间和时区**：
    ```bash
    timedatectl status
    ```
*   **设置时区（如设置为上海时间）**：
    ```bash
    timedatectl set-timezone Asia/Shanghai
    ```

为了保证所有服务器时间一致，我们需要进行网络时间同步。在RHEL8中，这由 **`chronyd`** 服务（替代了旧的 `ntpd`）完成。

![](img/6b31b8840197941b8283e89be49cbee2_240.png)

1.  编辑其配置文件 `/etc/chrony.conf`。
2.  添加或修改 `server` 指令，指向你的时间服务器。例如，指向红帽的公共NTP池：
    ```
    server pool.ntp.org iburst
    ```
    如果是内部环境，则指向内部的时间服务器，如：
    ```
    server classroom.example.com iburst
    ```
    `iburst` 参数可以在服务启动时快速进行初始同步。
3.  重启 `chronyd` 服务并设置开机自启：
    ```bash
    systemctl restart chronyd
    systemctl enable chronyd
    ```
4.  检查时间同步状态：
    ```bash
    chronyc sources -v
    ```
    这个命令会列出当前使用的时间源及其同步状态。

![](img/6b31b8840197941b8283e89be49cbee2_242.png)

![](img/6b31b8840197941b8283e89be49cbee2_244.png)

![](img/6b31b8840197941b8283e89be49cbee2_246.png)

**注意**：如果系统启用了NTP同步，手动使用 `timedatectl set-time` 修改时间可能会失败。你需要先禁用NTP同步：`timedatectl set-ntp false`，修改时间后再重新启用。

![](img/6b31b8840197941b8283e89be49cbee2_248.png)

---

![](img/6b31b8840197941b8283e89be49cbee2_250.png)

![](img/6b31b8840197941b8283e89be49cbee2_252.png)

本节课中我们一起学习了Linux日志管理的核心知识。我们了解了 `systemd-journald` 和 `rsyslog` 两个服务如何协同工作来记录和管理日志；学会了查看 `/var/log/` 下的各类日志文件和使用 `journalctl` 工具；掌握了如何通过配置 `rsyslog.conf` 来定制日志规则，以及如何利用 `logrotate` 实现日志的自动轮转以节省磁盘空间。最后，我们还学习了使用 `timedatectl` 管理时区和配置 `chronyd` 服务进行网络时间同步，确保系统时间的准确性。这些技能是进行系统监控、故障诊断和安全审计的基础。