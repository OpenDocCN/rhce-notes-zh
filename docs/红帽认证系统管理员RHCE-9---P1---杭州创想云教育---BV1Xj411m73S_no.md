# RHCE 9.0 课程：第5章：日志管理与网络配置

在本节课中，我们将要学习 Linux 系统中的日志管理和网络配置。日志是系统排错和审计的重要依据，而网络配置是系统与外界通信的基础。我们将从日志的查看、分析、存储，到网络接口的配置和管理，系统地学习这些核心技能。

## 课程回顾

![](img/33ddcabd67a246b0629631a570d107e5_1.png)

![](img/33ddcabd67a246b0629631a570d107e5_2.png)

上一节课我们重点学习了用户、组、权限和进程管理。本节中，我们将回顾并补充一个遗漏的知识点——命令别名，然后进入新的章节。

### 补充：命令别名

![](img/33ddcabd67a246b0629631a570d107e5_4.png)

![](img/33ddcabd67a246b0629631a570d107e5_5.png)

![](img/33ddcabd67a246b0629631a570d107e5_6.png)

![](img/33ddcabd67a246b0629631a570d107e5_7.png)

命令别名（alias）允许用户使用一个简短、自定义的命令来替代一个较长或复杂的命令，这能提高日常操作的效率。

**查看当前所有别名：**
```bash
alias
```

**设置别名（面向当前用户）：**
编辑用户家目录下的 `.bashrc` 文件，添加如下格式的行：
```bash
alias 简短命令='复杂命令'
```
例如：`alias ll='ls -l --color=auto'`。修改后，使用 `source ~/.bashrc` 使配置生效。

**设置别名（面向系统全局）：**
编辑系统配置文件 `/etc/bashrc`，以相同格式添加别名。这样，所有用户都可以使用这个别名。

**取消别名：**
```bash
unalias 别名名称
```

## 第11章：分析和存储日志

上一节我们回顾了系统管理的基础知识，本节中我们来看看如何管理和分析系统日志。日志记录了系统内核、服务和应用程序的运行信息，是故障排查和安全审计的关键。

![](img/33ddcabd67a246b0629631a570d107e5_9.png)

![](img/33ddcabd67a246b0629631a570d107e5_10.png)

![](img/33ddcabd67a246b0629631a570d107e5_12.png)

### 日志系统架构

Linux 系统中的日志主要由两个服务负责记录：
1.  **rsyslog**：传统的系统日志服务，负责记录各种服务和内核消息，日志默认存放在 `/var/log/` 目录下。
2.  **systemd-journald**：从 RHEL 7 开始引入的系统日志服务，它收集来自内核、系统早期启动阶段以及所有 systemd 服务的日志，默认日志存放在 `/run/log/journal/`（内存中）。

为了区分，我们常将 rsyslog 称为传统日志服务，将 systemd-journald 称为系统日志。

### 解读系统日志

以下是 `/var/log/` 目录下一些关键日志文件的用途：
*   **/var/log/messages**：记录系统常规信息，是大多数日志的集中地。
*   **/var/log/secure**：记录与安全认证相关的事件，如用户登录、sudo 提权等。
*   **/var/log/cron**：记录计划任务（cron）的执行情况。
*   **/var/log/boot.log**：记录系统启动过程的信息。
*   **/var/log/maillog**：记录邮件系统的活动。

**查看日志的常用命令：**
*   `less`、`tail`、`cat`：用于查看文本日志文件。例如，`tail -f /var/log/messages` 可以实时跟踪日志尾部的新内容。
*   `journalctl`：专用于查看 systemd-journald 收集的日志。这是一个功能强大的工具。

**使用 `journalctl` 查看日志：**
*   `journalctl`：查看所有日志。
*   `journalctl -b`：查看本次启动后的日志。
*   `journalctl -f`：实时跟踪日志输出（类似 `tail -f`）。
*   `journalctl -p err`：查看错误（err）级别的日志。其他级别包括 `emerg`, `alert`, `crit`, `warning`, `notice`, `info`, `debug`。
*   `journalctl --since “2023-10-27 14:00:00”`：查看指定时间之后的日志。

### 配置系统日志持久化

默认情况下，`systemd-journald` 的日志存储在内存（`/run/log/journal/`）中，系统重启后会丢失。为了永久保存这些日志，需要进行配置。

**配置步骤：**
1.  编辑配置文件 `/etc/systemd/journald.conf`。
2.  找到 `#Storage=auto` 这一行，去掉注释 `#`，并将值改为 `persistent`。
    ```
    Storage=persistent
    ```
3.  保存文件并重启日志服务：
    ```bash
    systemctl restart systemd-journald
    ```
4.  此后，日志将被持久化存储在 `/var/log/journal/` 目录下。

### 日志轮转（Log Rotation）

日志文件会不断增长，为了防止其占满磁盘空间，系统使用 `logrotate` 工具进行日志轮转。它会根据配置，定期对日志进行归档、压缩或删除旧日志。

**主配置文件：** `/etc/logrotate.conf`
**服务特定配置：** `/etc/logrotate.d/` 目录下的文件。

一个典型的配置定义了轮转周期（如每周）、保留的归档数量（如4份）以及是否压缩。

**手动触发日志轮转：**
```bash
logrotate -f /etc/logrotate.conf
```

### 配置时间同步（NTP）

系统时间的一致性对于日志分析、证书验证等至关重要。我们使用 NTP（网络时间协议）来同步时间。

**查看和设置时区：**
```bash
timedatectl # 查看当前时间和时区
timedatectl list-timezones | grep Shanghai # 列出时区并过滤出上海
timedatectl set-timezone Asia/Shanghai # 设置时区为亚洲/上海
```

![](img/33ddcabd67a246b0629631a570d107e5_14.png)

![](img/33ddcabd67a246b0629631a570d107e5_15.png)

**配置 NTP 客户端：**
1.  编辑配置文件 `/etc/chrony.conf`（RHEL 8/9 使用 chrony 替代了 ntpd）。
2.  使用 `server` 指令指定 NTP 服务器。建议配置多个服务器以提高可靠性。
    ```
    server ntp-server-address iburst
    ```
3.  保存并重启 chrony 服务：
    ```bash
    systemctl restart chronyd
    ```
4.  检查同步状态：
    ```bash
    chronyc sources -v
    ```

## 第12章：网络配置管理

上一节我们学习了如何让系统记录和保存运行痕迹，本节中我们来看看如何配置系统的网络，这是系统与外界交互的通道。RHEL 9 使用 **NetworkManager** 服务来管理网络，提供了 `nmtui`（文本用户界面）和 `nmcli`（命令行界面）两种配置工具。

### 网络基础概念回顾

![](img/33ddcabd67a246b0629631a570d107e5_17.png)

![](img/33ddcabd67a246b0629631a570d107e5_19.png)

在配置之前，需要理解一些基础概念：
*   **IP地址**：设备在网络中的唯一标识。IPv4 格式如 `192.168.1.10`。
*   **子网掩码/CIDR**：用于划分网络位和主机位。如 `255.255.255.0` 或 `/24`。
*   **网关**：连接不同网络的设备，通常是路由器的 IP 地址。
*   **DNS**：域名解析服务器，将域名（如 `www.example.com`）转换为 IP 地址。

### 使用 `nmtui` 配置网络（推荐初学者）

`nmtui` 提供了一个简单的文本界面，适合快速配置。

**操作步骤：**
1.  在终端输入 `nmtui` 并回车。
2.  选择 “Edit a connection”。
3.  选择要编辑的网卡连接（如 `Wired connection 1`），或按 `Add` 创建新的。
4.  在配置界面中：
    *   `Profile name`：连接名称（可自定义）。
    *   `Device`：物理网卡设备名（如 `ens160`）。
    *   将 `IPv4 CONFIGURATION` 改为 `Manual`。
    *   填写 `Addresses`（IP地址/前缀，如 `192.168.1.100/24`）、`Gateway`、`DNS servers`。
    *   确保 `Automatically connect` 选项被勾选。
5.  选择 `OK` 保存。
6.  返回主菜单，选择 `Activate a connection`，先`Deactivate`再`Activate`该连接，使配置生效。
7.  使用 `ip addr show` 或 `ping` 命令测试网络连通性。

**警告：** 远程连接（如 SSH）配置网络有断开风险，建议在本地控制台操作。

### 使用 `nmcli` 配置网络（功能强大）

`nmcli` 是更强大和脚本化的管理工具。

**常用命令：**
*   `nmcli device status`：查看网络设备状态。
*   `nmcli connection show`：查看所有网络连接配置。
*   `nmcli connection show --active`：查看活动的连接。

**修改一个现有连接的IP地址（示例）：**
```bash
nmcli connection modify “Wired connection 1” ipv4.addresses 192.168.1.150/24 ipv4.gateway 192.168.1.1 ipv4.dns “8.8.8.8” ipv4.method manual
```
*   `modify`：修改连接。
*   `“Wired connection 1”`：连接名称。
*   `ipv4.addresses`：设置静态IP和前缀。
*   `ipv4.method manual`：设置为手动配置（静态IP）。

**使修改生效：**
```bash
nmcli connection up “Wired connection 1”
```

### 配置主机名

![](img/33ddcabd67a246b0629631a570d107e5_21.png)

主机名是设备在网络中的名称。

![](img/33ddcabd67a246b0629631a570d107e5_23.png)

![](img/33ddcabd67a246b0629631a570d107e5_25.png)

**使用 `hostnamectl` 命令（推荐）：**
```bash
hostnamectl set-hostname server01.example.com # 设置主机名
hostnamectl # 查看当前主机名信息
```
此命令会同时修改运行时的主机名和静态配置文件（`/etc/hostname`）。

![](img/33ddcabd67a246b0629631a570d107e5_27.png)

![](img/33ddcabd67a246b0629631a570d107e5_28.png)

![](img/33ddcabd67a246b0629631a570d107e5_30.png)

![](img/33ddcabd67a246b0629631a570d107e5_32.png)

**使用 `nmtui`：**
在 `nmtui` 主界面中选择 “Set system hostname” 选项进行设置。

![](img/33ddcabd67a246b0629631a570d107e5_34.png)

![](img/33ddcabd67a246b0629631a570d107e5_36.png)

![](img/33ddcabd67a246b0629631a570d107e5_38.png)

### 网络诊断命令

配置后，可以使用以下命令进行测试和诊断：
*   `ip addr show` 或 `ip a`：查看网卡和IP地址信息。
*   `ping <目标IP或域名>`：测试网络连通性。
*   `ss -tulnp`：查看系统监听的端口和对应进程。
*   `ip route show` 或 `route -n`：查看路由表。
*   `nslookup <域名>` 或 `dig <域名>`：测试DNS解析。

![](img/33ddcabd67a246b0629631a570d107e5_40.png)

![](img/33ddcabd67a246b0629631a570d107e5_42.png)

## 课程总结

![](img/33ddcabd67a246b0629631a570d107e5_44.png)

![](img/33ddcabd67a246b0629631a570d107e5_46.png)

本节课中我们一起学习了 Linux 系统中两个非常重要的管理领域：日志和网络。

![](img/33ddcabd67a246b0629631a570d107e5_48.png)

![](img/33ddcabd67a246b0629631a570d107e5_50.png)

![](img/33ddcabd67a246b0629631a570d107e5_52.png)

1.  **日志管理**：我们了解了 `rsyslog` 和 `systemd-journald` 两套日志系统，学会了使用 `journalctl` 查看系统日志，并配置了日志的持久化存储。同时，理解了 `logrotate` 进行日志轮转的必要性，以及通过 `chrony` 配置 NTP 时间同步来保证日志时间戳的准确性。
2.  **网络配置**：我们掌握了在 RHEL 9 中使用 NetworkManager 服务管理网络的方法。通过图形化的 `nmtui` 工具和功能强大的 `nmcli` 命令行工具，可以轻松配置静态IP、网关、DNS等网络参数。此外，还学习了使用 `hostnamectl` 修改主机名，以及一系列常用的网络诊断命令。

![](img/33ddcabd67a246b0629631a570d107e5_54.png)

![](img/33ddcabd67a246b0629631a570d107e5_56.png)

![](img/33ddcabd67a246b0629631a570d107e5_57.png)

这些技能是系统管理员进行日常维护、故障排查和系统配置的基石，请务必通过实验熟练掌握。