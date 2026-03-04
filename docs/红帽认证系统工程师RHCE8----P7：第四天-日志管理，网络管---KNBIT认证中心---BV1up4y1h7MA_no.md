# 红帽认证系统工程师RHCE8：P7：第四天 日志管理与网络管理 📚

在本节课中，我们将要学习两个核心的系统管理模块：日志管理和网络管理。日志是系统管理员进行故障排查和系统监控的重要工具，而网络配置则是确保系统能够正常通信的基础。我们将从日志的收集、查看、配置讲起，然后深入探讨如何在红帽8系统中配置和管理网络连接。

![](img/a3884851156ba7265edb5515b42dc0b9_1.png)

![](img/a3884851156ba7265edb5515b42dc0b9_3.png)

---

## 第11章：日志管理 📝

![](img/a3884851156ba7265edb5515b42dc0b9_5.png)

![](img/a3884851156ba7265edb5515b42dc0b9_7.png)

![](img/a3884851156ba7265edb5515b42dc0b9_9.png)

![](img/a3884851156ba7265edb5515b42dc0b9_11.png)

上一节我们介绍了课程的整体安排，本节中我们来看看日志管理。对于系统管理员而言，日志是检测和发现问题的重要手段。与昨天学习的性能监控工具不同，日志主要用于排除故障。例如，当服务无法登录或系统出现问题时，就需要通过日志来定位原因。因此，熟悉日志系统对管理员至关重要。

![](img/a3884851156ba7265edb5515b42dc0b9_13.png)

![](img/a3884851156ba7265edb5515b42dc0b9_15.png)

![](img/a3884851156ba7265edb5515b42dc0b9_17.png)

> **注意**：本章内容在RHCE8考试中不作为考点，但它是系统管理员必须掌握的实践技能。

在红帽8系统中，管理日志的服务主要有两个：`rsyslog` 和 `systemd-journald`。此外，本章还会讲解如何配置NTP时间同步，这是一道必考题，但操作非常简单。

### 两个日志服务

![](img/a3884851156ba7265edb5515b42dc0b9_19.png)

首先，我们来认识一下这两个服务：
*   **`rsyslog`**：一个比较“老”的服务，从红帽7开始默认安装并启用。它负责在后台收集系统日志，并将其持久化存储在 `/var/log` 目录下。
*   **`systemd-journald`**：红帽7/8中新增的日志服务。它将日志数据存储在带有索引结构的二进制文件中，默认情况下，这些日志是临时的（存储在 `/run/log` 目录），系统重启后会被清除。

![](img/a3884851156ba7265edb5515b42dc0b9_21.png)

![](img/a3884851156ba7265edb5515b42dc0b9_23.png)

![](img/a3884851156ba7265edb5515b42dc0b9_25.png)

![](img/a3884851156ba7265edb5515b42dc0b9_27.png)

![](img/a3884851156ba7265edb5515b42dc0b9_29.png)

![](img/a3884851156ba7265edb5515b42dc0b9_31.png)

#### 1. 使用 `rsyslog` 服务

![](img/a3884851156ba7265edb5515b42dc0b9_33.png)

![](img/a3884851156ba7265edb5515b42dc0b9_35.png)

![](img/a3884851156ba7265edb5515b42dc0b9_37.png)

`rsyslog` 服务将收集到的日志分类存储到 `/var/log` 目录下的不同文件中。

![](img/a3884851156ba7265edb5515b42dc0b9_39.png)

![](img/a3884851156ba7265edb5515b42dc0b9_41.png)

![](img/a3884851156ba7265edb5515b42dc0b9_43.png)

![](img/a3884851156ba7265edb5515b42dc0b9_45.png)

![](img/a3884851156ba7265edb5515b42dc0b9_47.png)

以下是 `/var/log` 目录下一些重要的日志文件及其作用：

![](img/a3884851156ba7265edb5515b42dc0b9_49.png)

![](img/a3884851156ba7265edb5515b42dc0b9_51.png)

![](img/a3884851156ba7265edb5515b42dc0b9_53.png)

*   **`/var/log/messages`**：记录大多数系统日志信息，是排查系统问题时最常查看的日志文件。
*   **`/var/log/secure`**：记录与安全相关的事件，如用户身份验证（登录成功/失败）。如果发现大量失败的登录尝试，可能意味着存在暴力破解。
*   **`/var/log/maillog`**：记录与电子邮件服务相关的日志。
*   **`/var/log/cron`**：记录计划任务（cron job）相关的日志。在生产环境中，计划任务常用于定时执行脚本（如数据库备份）。
*   **`/var/log/boot.log`**：记录系统启动过程中的信息。如果启动时看不到详细过程，可以按 `F1` 键调出启动详情界面，或直接查看此日志文件。

![](img/a3884851156ba7265edb5515b42dc0b9_55.png)

![](img/a3884851156ba7265edb5515b42dc0b9_57.png)

![](img/a3884851156ba7265edb5515b42dc0b9_59.png)

![](img/a3884851156ba7265edb5515b42dc0b9_61.png)

> **注意**：`last` 命令查看的用户登录记录，并非直接读取 `/var/log/wtmp` 文件（该文件是二进制格式），而是通过 `last` 命令本身进行解析。查看最近的用户登录尝试，应使用 `lastlog` 命令。

![](img/a3884851156ba7265edb5515b42dc0b9_63.png)

##### 配置 `rsyslog`

`rsyslog` 通过其配置文件 `/etc/rsyslog.conf` 来定义日志的收集规则。其核心是 **`RULES`** 部分。

![](img/a3884851156ba7265edb5515b42dc0b9_65.png)

一条规则的语法格式如下：
```
[设备/服务名] . [日志等级]    [目标文件]
```

![](img/a3884851156ba7265edb5515b42dc0b9_67.png)

![](img/a3884851156ba7265edb5515b42dc0b9_69.png)

*   **设备/服务名**：指定产生日志的服务或设备，如 `authpriv`（身份验证）、`mail`（邮件）。使用 `*` 表示所有服务。
*   **日志等级**：定义记录的严重程度。从低到高常见的有：`debug`, `info`, `notice`, `warning`, `err`, `crit`, `alert`, `emerg`。使用 `.` 表示“大于等于该等级”，`.=` 表示“等于该等级”，`.!` 表示“不等于该等级”。
*   **目标文件**：指定日志写入的文件路径，如 `/var/log/messages`。

**示例分析**：
配置文件中的一行规则：
```
* .info;mail.none;authpriv.none;cron.none                /var/log/messages
```
这条规则的含义是：将所有服务中等级大于等于 `info` 的日志，都记录到 `/var/log/messages` 文件中，但排除 `mail`、`authpriv`、`cron` 这三个服务的日志（因为它们有自己独立的日志文件）。

![](img/a3884851156ba7265edb5515b42dc0b9_71.png)

![](img/a3884851156ba7265edb5515b42dc0b9_73.png)

![](img/a3884851156ba7265edb5515b42dc0b9_75.png)

**自定义配置**：
红帽8建议不要在主配置文件 `/etc/rsyslog.conf` 中直接修改，而是在 `/etc/rsyslog.d/` 目录下创建新的 `.conf` 文件（例如 `my_log.conf`）。主配置文件会通过 `$IncludeConfig /etc/rsyslog.d/*.conf` 指令自动包含该目录下的所有配置。这样做的好处是避免破坏主配置，管理更清晰。

![](img/a3884851156ba7265edb5515b42dc0b9_77.png)

![](img/a3884851156ba7265edb5515b42dc0b9_79.png)

##### 常用命令

*   **`tail -f /var/log/secure`**：实时跟踪（`-f` 参数）安全日志文件的尾部，常用于监控登录尝试。
*   **`logger “This is a test log”`**：手动发送一条测试日志到系统。默认等级为 `user.notice`。可以使用 `-p` 参数指定优先级，如 `logger -p user.err “Error message”`。

![](img/a3884851156ba7265edb5515b42dc0b9_81.png)

#### 2. 使用 `systemd-journald` 服务

`systemd-journald` 的日志是二进制的，不能直接用 `cat` 或 `vi` 查看，必须使用 `journalctl` 命令。

**基本用法**：
*   `journalctl`：查看所有日志。
*   `journalctl -p err`：只查看错误（`err`）等级的日志。
*   `journalctl -n 5`：查看最后5行日志。
*   `journalctl -f`：实时跟踪日志输出（类似 `tail -f`）。
*   `journalctl --since “2021-01-01 00:00:00” --until “2021-01-02 12:00:00”`：查看指定时间范围内的日志。这个功能对于定位特定时间段发生的问题非常有用。

![](img/a3884851156ba7265edb5515b42dc0b9_83.png)

**让日志永久保存**：
默认情况下，`journald` 的日志是临时的。若要永久保存，需要修改其配置文件 `/etc/systemd/journald.conf`，将 `Storage=` 选项的值从默认的 `auto` 改为 `persistent`。
```bash
# 编辑配置文件
vim /etc/systemd/journald.conf
# 找到并修改
Storage=persistent
```
然后重启服务 `systemctl restart systemd-journald`。之后，日志就会持久化存储在 `/var/log/journal/` 目录下。

![](img/a3884851156ba7265edb5515b42dc0b9_85.png)

#### 3. 配置NTP时间同步 ⏰

![](img/a3884851156ba7265edb5515b42dc0b9_87.png)

![](img/a3884851156ba7265edb5515b42dc0b9_89.png)

时间同步是RHCE8的必考题目，通常要求将系统与考官提供的时间服务器进行同步。

![](img/a3884851156ba7265edb5515b42dc0b9_91.png)

![](img/a3884851156ba7265edb5515b42dc0b9_93.png)

![](img/a3884851156ba7265edb5515b42dc0b9_95.png)

**配置步骤**：
1.  使用 `vim` 编辑时间同步客户端配置文件：`/etc/chrony.conf`。
2.  在 `server` 配置行中，将服务器地址修改为题目提供的时间服务器地址（例如 `server classroom.example.com iburst`）。
3.  保存并退出编辑器。
4.  重启 `chronyd` 服务以使配置生效：`systemctl restart chronyd`。
5.  使用 `chronyc sources -v` 命令验证是否成功与指定服务器同步。

![](img/a3884851156ba7265edb5515b42dc0b9_97.png)

---

![](img/a3884851156ba7265edb5515b42dc0b9_99.png)

![](img/a3884851156ba7265edb5515b42dc0b9_101.png)

![](img/a3884851156ba7265edb5515b42dc0b9_103.png)

## 网络管理 🌐

![](img/a3884851156ba7265edb5515b42dc0b9_105.png)

![](img/a3884851156ba7265edb5515b42dc0b9_107.png)

![](img/a3884851156ba7265edb5515b42dc0b9_109.png)

上一节我们学习了如何通过日志来洞察系统状态，本节中我们来看看如何配置系统的网络，这是确保服务器能够正常通信的基石。网络配置是RHCE考试的重点和必考点，虽然相关命令较长，但属于基础性操作，通过练习很容易掌握。

![](img/a3884851156ba7265edb5515b42dc0b9_111.png)

![](img/a3884851156ba7265edb5515b42dc0b9_113.png)

![](img/a3884851156ba7265edb5515b42dc0b9_115.png)

![](img/a3884851156ba7265edb5515b42dc0b9_117.png)

### 网络基础概念

![](img/a3884851156ba7265edb5515b42dc0b9_119.png)

![](img/a3884851156ba7265edb5515b42dc0b9_121.png)

![](img/a3884851156ba7265edb5515b42dc0b9_123.png)

![](img/a3884851156ba7265edb5515b42dc0b9_125.png)

在开始配置前，需要了解一些基础概念：
*   **IP地址与子网掩码**：IP地址（如IPv4）由网络部分和主机部分组成，子网掩码用于区分这两部分。常见的表示法有 `255.255.255.0` 或 `/24`。
*   **网关**：连接不同网络的设备（通常是路由器）的IP地址，是本网络通向其他网络的“大门”。
*   **DNS**：域名系统，用于将域名（如 `www.redhat.com`）解析为IP地址。

![](img/a3884851156ba7265edb5515b42dc0b9_127.png)

![](img/a3884851156ba7265edb5515b42dc0b9_129.png)

![](img/a3884851156ba7265edb5515b42dc0b9_131.png)

在红帽7/8中，默认的网络设备命名规则不再是传统的 `eth0`、`eth1`，而是根据固件、拓扑等信息生成的更复杂的名字（如 `ens33`）。但我们的实验环境通常已改回 `eth0` 这样的传统命名。

![](img/a3884851156ba7265edb5515b42dc0b9_133.png)

![](img/a3884851156ba7265edb5515b42dc0b9_135.png)

![](img/a3884851156ba7265edb5515b42dc0b9_137.png)

![](img/a3884851156ba7265edb5515b42dc0b9_139.png)

### 使用 `nmcli` 配置网络

![](img/a3884851156ba7265edb5515b42dc0b9_141.png)

红帽8使用 `NetworkManager` 服务来管理网络，其命令行工具是 `nmcli`。所有配置最终会写入 `/etc/sysconfig/network-scripts/` 目录下的文件。

![](img/a3884851156ba7265edb5515b42dc0b9_143.png)

![](img/a3884851156ba7265edb5515b42dc0b9_145.png)

#### 查看网络连接

首先，查看当前所有的网络连接：
```bash
nmcli connection show
```
或者查看活动的连接：
```bash
nmcli connection show --active
```
**关键区分**：命令输出中的 **`NAME`** 列是“连接名”（一个逻辑标识），而 **`DEVICE`** 列才是真实的“网卡名”。在后续配置命令中，务必分清你操作的是连接名还是设备名。

![](img/a3884851156ba7265edb5515b42dc0b9_147.png)

![](img/a3884851156ba7265edb5515b42dc0b9_149.png)

![](img/a3884851156ba7265edb5515b42dc0b9_151.png)

#### 添加新的网络连接

![](img/a3884851156ba7265edb5515b42dc0b9_153.png)

![](img/a3884851156ba7265edb5515b42dc0b9_155.png)

![](img/a3884851156ba7265edb5515b42dc0b9_157.png)

可以为同一块物理网卡创建多个逻辑连接（每个连接配置不同的IP），但同一时间只能有一个连接处于活动状态。

![](img/a3884851156ba7265edb5515b42dc0b9_159.png)

![](img/a3884851156ba7265edb5515b42dc0b9_161.png)

![](img/a3884851156ba7265edb5515b42dc0b9_163.png)

**添加连接命令示例**：
```bash
nmcli connection add con-name my-eth0 ifname eth0 type ethernet ipv4.addresses 192.168.1.100/24 ipv4.gateway 192.168.1.1 ipv4.dns 8.8.8.8 ipv4.method manual autoconnect yes
```
*   `con-name my-eth0`：指定新连接的名称。
*   `ifname eth0`：指定该连接绑定的真实网卡设备。
*   `type ethernet`：连接类型为以太网。
*   `ipv4.addresses 192.168.1.100/24`：设置IPv4地址和子网掩码。
*   `ipv4.gateway 192.168.1.1`：设置网关。
*   `ipv4.dns 8.8.8.8`：设置DNS服务器。
*   `ipv4.method manual`：IP获取方式为手动（静态）。如果是 `dhcp` 则为自动获取。
*   `autoconnect yes`：设置开机自动连接。

![](img/a3884851156ba7265edb5515b42dc0b9_165.png)

添加后，需要使用 `nmcli connection up my-eth0` 来激活这个新连接。

#### 修改现有网络连接

![](img/a3884851156ba7265edb5515b42dc0b9_167.png)

考试中更常见的是修改一个已存在但未配置或配置错误的连接。

![](img/a3884851156ba7265edb5515b42dc0b9_169.png)

**修改连接命令示例**：
```bash
nmcli connection modify “Wired connection 1” ipv4.addresses 172.25.250.10/24 ipv4.gateway 172.25.250.254 ipv4.dns 172.25.250.254 ipv4.method manual autoconnect yes
```
*   `modify`：表示修改操作。
*   `“Wired connection 1”`：需要修改的**连接名**（如果名称包含空格，必须用引号括起来）。
*   后面的参数与添加连接时类似，用于设置新的地址、网关、DNS等。

![](img/a3884851156ba7265edb5515b42dc0b9_171.png)

![](img/a3884851156ba7265edb5515b42dc0b9_173.png)

修改配置后，需要**重启网络连接**或**重启 `NetworkManager` 服务**才能生效：
```bash
nmcli connection down “Wired connection 1” && nmcli connection up “Wired connection 1”
# 或者重启服务（更彻底）
systemctl restart NetworkManager
```
> **提示**：如果 `up` 操作失败导致网络断开，在考试环境中可以考虑直接重启系统。因为网络通常是第一道题，重启不会影响后续操作。

![](img/a3884851156ba7265edb5515b42dc0b9_175.png)

#### 删除网络连接

![](img/a3884851156ba7265edb5515b42dc0b9_177.png)

如果配置错误，可以删除连接：
```bash
nmcli connection delete my-eth0
```
删除前请确保该连接未被激活。

![](img/a3884851156ba7265edb5515b42dc0b9_179.png)

### 其他网络命令

![](img/a3884851156ba7265edb5515b42dc0b9_181.png)

*   **`ip addr show` (或 `ip a`)**：查看所有网络接口的IP地址信息，这是最常用的命令。
*   **`ping -c 3 8.8.8.8`**：测试到目标IP地址的网络连通性，`-c 3` 表示只发送3个数据包。

![](img/a3884851156ba7265edb5515b42dc0b9_183.png)

![](img/a3884851156ba7265edb5515b42dc0b9_185.png)

![](img/a3884851156ba7265edb5515b42dc0b9_187.png)

---

## 总结 🎯

本节课中我们一起学习了RHCE8中两个重要的管理模块。

在**日志管理**部分，我们掌握了：
1.  系统中有 `rsyslog` 和 `systemd-journald` 两个日志服务。
2.  `rsyslog` 将日志持久化存储在 `/var/log`，需要学会查看 `/var/log/messages`、`secure` 等关键日志文件，并理解其配置文件 `/etc/rsyslog.conf` 中规则的写法。
3.  `systemd-journald` 使用 `journalctl` 命令查看二进制日志，并学习了如何通过修改 `/etc/systemd/journald.conf` 使其日志永久保存。
4.  使用 `chronyd` 配置NTP时间同步是必考技能，关键在于正确修改 `/etc/chrony.conf` 文件中的 `server` 行并重启服务。

![](img/a3884851156ba7265edb5515b42dc0b9_189.png)

![](img/a3884851156ba7265edb5515b42dc0b9_191.png)

![](img/a3884851156ba7265edb5515b42dc0b9_193.png)

![](img/a3884851156ba7265edb5515b42dc0b9_195.png)

在**网络管理**部分，我们掌握了：
1.  红帽8使用 `NetworkManager` 和 `nmcli` 命令进行网络配置。
2.  核心是区分“连接名”（`NAME`）和“设备名”（`DEVICE`）。
3.  重点练习了使用 `nmcli connection modify` 命令修改现有连接的IP地址、网关、DNS等参数，并使用 `nmcli connection up` 激活配置。
4.  熟悉了 `ip addr` 和 `ping` 等基础网络诊断命令。

![](img/a3884851156ba7265edb5515b42dc0b9_197.png)

![](img/a3884851156ba7265edb5515b42dc0b9_199.png)

![](img/a3884851156ba7265edb5515b42dc0b9_201.png)

![](img/a3884851156ba7265edb5515b42dc0b9_203.png)

![](img/a3884851156ba7265edb5515b42dc0b9_205.png)

![](img/a3884851156ba7265edb5515b42dc0b9_207.png)

这些知识是系统管理员日常工作的基础，也是通过RHCE认证必须熟练掌握的内容。请务必完成实验，加深理解。