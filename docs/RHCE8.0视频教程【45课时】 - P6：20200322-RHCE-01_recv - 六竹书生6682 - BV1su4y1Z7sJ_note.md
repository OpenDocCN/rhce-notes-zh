# RHCE8.0 视频教程：P6：日志、时间同步与网络管理

![](img/3cabf1f287e09d6c34148d7908ec6fa6_0.png)

在本节课中，我们将学习三个核心的系统管理任务：日志查看、时间同步配置以及网络管理。这些技能对于维护Linux系统的稳定性和连通性至关重要。

![](img/3cabf1f287e09d6c34148d7908ec6fa6_2.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_4.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_6.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_8.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_10.png)

## 日志查看 🔍

上一节我们介绍了日志管理的基础，本节中我们来看看如何具体查看和分析系统日志。系统日志记录了系统运行的各种事件，是故障排查的重要依据。

以下是几个常用的日志查看命令：

*   **`journalctl`**：这是Systemd日志系统的主要查看工具。直接运行 `journalctl` 会显示从系统启动到当前的所有日志。
*   **`journalctl -f`**：此命令用于实时跟踪日志的更新。它会持续显示新产生的日志条目，直到你按下 `Ctrl+C` 终止。
*   **`journalctl -p err`**：此命令用于根据事件级别（优先级）过滤日志。`-p err` 表示只显示错误（error）级别的日志。其他级别包括 `info`、`warning`、`crit` 等。
*   **`journalctl --since "09:30" --until "09:40"`**：此命令用于查看特定时间范围内的日志。`--since` 指定开始时间，`--until` 指定结束时间。时间格式可以是 `"YYYY-MM-DD HH:MM:SS"` 或相对时间如 `"yesterday"`。

这些命令可以组合使用，例如 `journalctl -p err --since today` 可以查看今天产生的所有错误日志。

![](img/3cabf1f287e09d6c34148d7908ec6fa6_12.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_14.png)

## 时间同步 ⏰

![](img/3cabf1f287e09d6c34148d7908ec6fa6_16.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_18.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_20.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_22.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_24.png)

在分布式系统或集群环境中，保持所有设备时间一致非常重要。本节我们将学习如何配置系统的时间同步服务。

![](img/3cabf1f287e09d6c34148d7908ec6fa6_26.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_28.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_30.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_32.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_34.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_36.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_38.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_40.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_42.png)

首先，我们可以查看当前时间信息：
```bash
date
timedatectl
```
`timedatectl` 命令会显示更详细的信息，包括本地时间、时区以及NTP同步是否启用。

![](img/3cabf1f287e09d6c34148d7908ec6fa6_44.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_46.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_48.png)

要修改时区，可以使用以下命令：
```bash
timedatectl list-timezones | grep Asia  # 查看支持的亚洲时区
timedatectl set-timezone Asia/Shanghai  # 设置时区为亚洲/上海
```

![](img/3cabf1f287e09d6c34148d7908ec6fa6_50.png)

在RHEL 8中，默认的时间同步服务是 `chronyd`，而不是传统的 `ntpd`。其配置文件位于 `/etc/chrony.conf`。

![](img/3cabf1f287e09d6c34148d7908ec6fa6_52.png)

配置时间同步主要涉及两个角色：
1.  **NTP服务器端**：为其他设备提供时间源。需要在配置文件中设置允许访问的客户端网段。
    ```
    allow 192.168.127.0/24
    ```
2.  **NTP客户端**：向指定的服务器同步时间。需要在配置文件中指定要同步的服务器地址（注释掉原有的 `pool` 行）。
    ```
    server 192.168.127.128 iburst
    ```

![](img/3cabf1f287e09d6c34148d7908ec6fa6_54.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_56.png)

修改配置文件后，需要启用并启动服务：
```bash
systemctl enable --now chronyd
```
最后，使用 `timedatectl` 检查 `NTP synchronized` 是否变为 `yes`，表示同步成功。

## 网络管理 🌐

网络配置是系统管理的基础。本节我们将学习在RHEL 8中如何使用 `nmcli` 命令管理网络连接。

### 查看网络连接

首先，查看所有网络连接和设备：
```bash
nmcli connection show
nmcli device status
```
`connection` 是逻辑上的配置文件，而 `device` 是物理网卡。一个 `device` 可以关联多个 `connection`，但同一时间只有一个 `connection` 处于活动状态。

### 配置IP地址

在RHEL 8中，推荐使用 `nmcli` 进行网络配置，它修改的是永久配置。

![](img/3cabf1f287e09d6c34148d7908ec6fa6_58.png)

**添加一个新的以太网连接并配置静态IP：**
```bash
nmcli connection add con-name eth-static ifname ens256 type ethernet ip4 192.168.127.100/24 gw4 192.168.127.2
nmcli connection modify eth-static ipv4.dns "8.8.8.8"
nmcli connection modify eth-static ipv4.method manual
nmcli connection up eth-static
```
**修改现有连接的IP地址：**
```bash
nmcli connection modify eth-static ipv4.addresses "192.168.127.200/24"
nmcli connection down eth-static && nmcli connection up eth-static
```
**为一个网卡添加多个IP地址：**
```bash
nmcli connection modify eth-static +ipv4.addresses "192.168.127.201/24"
```
使用 `+` 表示添加属性，使用 `-` 表示删除属性。

![](img/3cabf1f287e09d6c34148d7908ec6fa6_60.png)

### 链路聚合（Team）

![](img/3cabf1f287e09d6c34148d7908ec6fa6_62.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_64.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_66.png)

链路聚合可以将多个物理网卡绑定成一个逻辑网卡，以实现带宽叠加或冗余备份。

配置步骤如下：
1.  **准备网卡**：确保有两块物理网卡（如 `ens224` 和 `ens256`），并清空其现有配置。
2.  **创建聚合组（Team）**：创建一个逻辑聚合接口。
    ```bash
    nmcli connection add con-name team0 ifname team0 type team config '{"runner": {"name": "activebackup"}}'
    ```
    其中 `runner` 模式 `activebackup` 表示主备模式，一个网卡工作，另一个备用。
3.  **配置聚合组的IP地址**：
    ```bash
    nmcli connection modify team0 ipv4.addresses 192.168.127.111/24 ipv4.gateway 192.168.127.2 ipv4.method manual
    ```
4.  **将物理网卡加入聚合组**：
    ```bash
    nmcli connection add con-name team0-port1 ifname ens224 type team-slave master team0
    nmcli connection add con-name team0-port2 ifname ens256 type team-slave master team0
    ```
5.  **启动聚合组**：
    ```bash
    nmcli connection up team0
    ```

配置完成后，使用 `teamdctl team0 state` 查看聚合组状态。当主网卡故障时，备网卡会自动接管，保障网络连通性。

---

![](img/3cabf1f287e09d6c34148d7908ec6fa6_68.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_70.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_72.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_74.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_76.png)

![](img/3cabf1f287e09d6c34148d7908ec6fa6_78.png)

本节课中我们一起学习了如何查看系统日志、配置NTP时间同步服务，以及使用 `nmcli` 命令进行复杂的网络配置，包括IP地址管理和链路聚合。这些是RHCE认证和日常系统运维中必须掌握的核心技能。