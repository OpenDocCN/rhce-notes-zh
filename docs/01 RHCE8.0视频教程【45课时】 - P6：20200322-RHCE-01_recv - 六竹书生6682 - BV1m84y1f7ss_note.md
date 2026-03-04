# RHCE8.0视频教程：01：日志查看、时间同步与网络管理

![](img/0827808e8114a71d76350da7f3c42873_0.png)

在本节课中，我们将要学习Linux系统中的日志查看、时间同步服务配置以及网络管理的基础知识。这些是系统管理员日常维护和RHCE认证考试中的重要技能。

## 日志查看

![](img/0827808e8114a71d76350da7f3c42873_2.png)

![](img/0827808e8114a71d76350da7f3c42873_3.png)

![](img/0827808e8114a71d76350da7f3c42873_5.png)

![](img/0827808e8114a71d76350da7f3c42873_6.png)

上一节我们介绍了系统服务的管理，本节中我们来看看如何查看系统日志。日志记录了系统运行的各种事件，是排查问题的重要依据。

![](img/0827808e8114a71d76350da7f3c42873_8.png)

查看日志的命令有多种，以下是几种常用的方法：

*   **`tail -f` 命令**：用于实时查看日志文件末尾新增的内容。例如，`tail -f /var/log/messages` 可以实时监控系统消息日志。按 `Ctrl+C` 结束查看。
*   **`journalctl` 命令**：这是systemd日志系统（journal）的管理工具，可以查看所有由systemd管理的服务日志。直接运行 `journalctl` 会显示所有日志信息。
*   **`journalctl -f` 命令**：与 `tail -f` 类似，用于实时显示所有系统日志。
*   **`journalctl -p` 命令**：根据事件优先级（级别）过滤日志。例如，`journalctl -p err` 只显示错误级别的日志。
*   **`journalctl --since` 命令**：查看从指定时间开始的日志。例如，`journalctl --since "2020-03-22 09:30:00"`。
*   **`journalctl --until` 命令**：查看直到指定时间结束的日志。可以组合使用，例如 `journalctl --since "09:30" --until "09:40"` 查看该时间段内的日志。

## 时间同步

在分布式系统或集群环境中，保持各节点时间一致至关重要。Linux系统通常使用NTP（网络时间协议）服务进行时间同步。

### 查看与设置时间

首先，我们可以查看当前系统时间：

![](img/0827808e8114a71d76350da7f3c42873_10.png)

![](img/0827808e8114a71d76350da7f3c42873_12.png)

```bash
date
```

要查看更详细的时间信息，包括时区和NTP同步状态，可以使用：

![](img/0827808e8114a71d76350da7f3c42873_14.png)

![](img/0827808e8114a71d76350da7f3c42873_16.png)

![](img/0827808e8114a71d76350da7f3c42873_18.png)

![](img/0827808e8114a71d76350da7f3c42873_20.png)

![](img/0827808e8114a71d76350da7f3c42873_22.png)

![](img/0827808e8114a71d76350da7f3c42873_24.png)

![](img/0827808e8114a71d76350da7f3c42873_26.png)

```bash
timedatectl
```

![](img/0827808e8114a71d76350da7f3c42873_28.png)

![](img/0827808e8114a71d76350da7f3c42873_29.png)

![](img/0827808e8114a71d76350da7f3c42873_31.png)

![](img/0827808e8114a71d76350da7f3c42873_33.png)

![](img/0827808e8114a71d76350da7f3c42873_35.png)

![](img/0827808e8114a71d76350da7f3c42873_37.png)

![](img/0827808e8114a71d76350da7f3c42873_39.png)

如果需要修改时区，例如设置为亚洲/上海时区，步骤如下：

![](img/0827808e8114a71d76350da7f3c42873_41.png)

![](img/0827808e8114a71d76350da7f3c42873_43.png)

1.  查看系统支持的时区列表：`timedatectl list-timezones | grep Asia`
2.  设置时区：`timedatectl set-timezone Asia/Shanghai`

![](img/0827808e8114a71d76350da7f3c42873_45.png)

![](img/0827808e8114a71d76350da7f3c42873_47.png)

### 配置 Chrony 服务

在RHEL 8中，默认的时间同步服务是 `chronyd`，而非传统的 `ntpd`。

![](img/0827808e8114a71d76350da7f3c42873_49.png)

配置NTP服务器端（假设本机作为时间源）：
1.  编辑配置文件 `/etc/chrony.conf`。
2.  允许特定网段的客户端访问本机时间服务，添加或修改以下行：
    ```
    allow 192.168.127.0/24
    ```
3.  保存并退出编辑器。

![](img/0827808e8114a71d76350da7f3c42873_51.png)

![](img/0827808e8114a71d76350da7f3c42873_53.png)

配置NTP客户端（客户端与服务器端同步）：
1.  编辑配置文件 `/etc/chrony.conf`。
2.  指定要同步的NTP服务器地址，例如：
    ```
    server 192.168.127.128 iburst
    ```
    其中 `iburst` 选项可以加速初始同步。
3.  保存并退出编辑器。

完成配置后，需要启用并启动服务，然后检查状态：

```bash
systemctl enable --now chronyd
systemctl status chronyd
timedatectl # 查看NTP同步状态是否为“yes”
```

> **注意**：在考试或实验环境中，如果只有单机，可以配置为自己同步自己（`server 127.0.0.1`），但实际生产环境应指向可靠的外部或内部时间服务器。

## 网络管理

网络配置是系统管理的基础。本节我们将学习在RHEL 8中配置网络地址的多种方法。

### 网络配置要素与查看

一个基本的网络连接通常需要四个要素：**IP地址**、**子网掩码**、**网关** 和 **DNS服务器**。

使用 `ip addr` 或 `ifconfig`（需安装 `net-tools`）命令可以查看网络接口信息。在RHEL 8中，网络接口的命名规则变为类似 `ens160` 的格式。

### 配置网络连接

![](img/0827808e8114a71d76350da7f3c42873_55.png)

在RHEL 8中，推荐使用 `NetworkManager` 服务及其命令行工具 `nmcli` 进行网络配置，它修改的配置是永久生效的。

![](img/0827808e8114a71d76350da7f3c42873_57.png)

以下是使用 `nmcli` 管理网络连接的基本操作：

*   **查看所有连接**：`nmcli connection show`
*   **查看连接详细信息**：`nmcli connection show <连接名>`
*   **激活/停用连接**：
    ```bash
    nmcli connection down <连接名> # 停用
    nmcli connection up <连接名>   # 激活
    ```
*   **修改连接属性**（例如IP地址、网关、DNS）：
    ```bash
    nmcli connection modify <连接名> ipv4.addresses 192.168.127.130/24
    nmcli connection modify <连接名> ipv4.gateway 192.168.127.2
    nmcli connection modify <连接名> ipv4.dns "8.8.8.8"
    nmcli connection modify <连接名> ipv4.method manual # 设置为手动配置
    ```
    > **提示**：输入 `ipv4.` 后按 `Tab` 键可以自动补全可用属性。
*   **添加多个IP地址到同一接口**：
    ```bash
    nmcli connection modify <连接名> +ipv4.addresses 192.168.127.188/24
    ```
*   **创建新的网络连接**：
    ```bash
    nmcli connection add type ethernet con-name <新连接名> ifname <网卡名> ipv4.method auto
    ```

![](img/0827808e8114a71d76350da7f3c42873_59.png)

![](img/0827808e8114a71d76350da7f3c42873_61.png)

![](img/0827808e8114a71d76350da7f3c42873_63.png)

修改配置后，需要重新激活连接或使用 `nmcli connection reload` 重载所有配置使其生效。

### 配置链路聚合（Team）

链路聚合（Teaming）能将多个物理网卡绑定成一个逻辑网卡，实现带宽叠加或冗余备份。

配置步骤示例（创建主动备份模式 `activebackup` 的聚合接口）：
1.  **准备网卡**：确保有两个物理网卡（如 `ens224`, `ens256`），并清空其现有配置。
2.  **创建聚合组**：
    ```bash
    nmcli connection add type team con-name team0 ifname team0 config '{"runner": {"name": "activebackup"}}'
    ```
3.  **配置聚合组的IP地址**：
    ```bash
    nmcli connection modify team0 ipv4.addresses 192.168.127.111/24
    nmcli connection modify team0 ipv4.gateway 192.168.127.2
    nmcli connection modify team0 ipv4.method manual
    ```
4.  **将物理网卡加入聚合组**：
    ```bash
    nmcli connection add type ethernet slave-type team con-name team0-port1 ifname ens224 master team0
    nmcli connection add type ethernet slave-type team con-name team0-port2 ifname ens256 master team0
    ```
5.  **启动聚合接口**：`nmcli connection up team0`

配置完成后，可以使用 `teamdctl team0 state` 查看聚合接口状态。在主动备份模式下，当主用网卡故障时，备用网卡会自动接管，保障网络连通性。

![](img/0827808e8114a71d76350da7f3c42873_65.png)

![](img/0827808e8114a71d76350da7f3c42873_67.png)

---

![](img/0827808e8114a71d76350da7f3c42873_69.png)

![](img/0827808e8114a71d76350da7f3c42873_71.png)

![](img/0827808e8114a71d76350da7f3c42873_73.png)

![](img/0827808e8114a71d76350da7f3c42873_75.png)

本节课中我们一起学习了如何查看系统日志、配置Chrony时间同步服务，以及使用 `nmcli` 工具进行网络接口配置和管理，包括IP地址设置和链路聚合。这些是构建稳定、可靠Linux系统环境的核心技能，请务必熟练掌握。