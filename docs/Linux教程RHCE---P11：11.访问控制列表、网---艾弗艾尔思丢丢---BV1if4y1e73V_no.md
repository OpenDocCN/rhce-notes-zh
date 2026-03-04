# Linux教程RHCE：P11：访问控制列表、网卡绑定与SSH

## 概述
在本节课中，我们将学习Linux系统中的三个重要主题：访问控制列表（ACL）、网卡绑定技术以及SSH远程连接服务。我们将了解如何通过简单的工具管理防火墙规则，如何实现网卡的高可用与负载均衡，以及如何安全、高效地配置和使用SSH服务进行远程系统管理。

![](img/a06a22e6df22be2565106c15d18de7e5_1.png)

---

## 章节一：访问控制列表（TCP Wrappers）

![](img/a06a22e6df22be2565106c15d18de7e5_3.png)

![](img/a06a22e6df22be2565106c15d18de7e5_5.png)

上一节我们介绍了多种防火墙配置工具。本节中，我们来看看一个基于应用层的简单防火墙工具——TCP Wrappers。

![](img/a06a22e6df22be2565106c15d18de7e5_7.png)

![](img/a06a22e6df22be2565106c15d18de7e5_9.png)

![](img/a06a22e6df22be2565106c15d18de7e5_11.png)

TCP Wrappers是一个基于应用层的访问控制工具，它通过服务名称来限制访问，与我们之前学习的基于协议、端口或IP地址的防火墙工具（如iptables、firewalld）有所不同。它的配置非常简单，主要由两个文件组成。

以下是TCP Wrappers的两个核心配置文件及其作用：

![](img/a06a22e6df22be2565106c15d18de7e5_13.png)

![](img/a06a22e6df22be2565106c15d18de7e5_14.png)

*   **白名单文件 (`/etc/hosts.allow`)**：此文件中写入允许访问的服务名称和IP地址段。
*   **黑名单文件 (`/etc/hosts.deny`)**：此文件中写入拒绝访问的服务名称和IP地址段。

![](img/a06a22e6df22be2565106c15d18de7e5_16.png)

TCP Wrappers的匹配有优先级顺序：**系统会先匹配 `hosts.allow` 文件，再匹配 `hosts.deny` 文件**。如果一个请求在 `hosts.allow` 中被允许，则直接放行，不再检查 `hosts.deny`。

### 配置示例
假设我们想禁止 `192.168.10.0/24` 网段访问SSH服务，但允许其中一台主机 `192.168.10.20` 访问。

1.  首先，在 `hosts.deny` 文件中添加规则，禁止整个网段：
    ```bash
    sshd: 192.168.10.
    ```
    *注：`192.168.10.` 等同于 `192.168.10.0/255.255.255.0`，代表整个网段。*

![](img/a06a22e6df22be2565106c15d18de7e5_18.png)

2.  然后，在 `hosts.allow` 文件中添加例外规则，允许特定主机：
    ```bash
    sshd: 192.168.10.20
    ```
    配置完成后立即生效，无需重启服务。此时，只有 `192.168.10.20` 可以成功SSH连接到服务器。

**核心概念**：TCP Wrappers的规则格式为 **`服务名称: 客户端地址列表`**。

![](img/a06a22e6df22be2565106c15d18de7e5_20.png)

![](img/a06a22e6df22be2565106c15d18de7e5_21.png)

---

![](img/a06a22e6df22be2565106c15d18de7e5_23.png)

![](img/a06a22e6df22be2565106c15d18de7e5_24.png)

![](img/a06a22e6df22be2565106c15d18de7e5_25.png)

## 章节二：网卡绑定技术

在学习了基础的网络配置后，本节我们将探讨一种高级的网卡管理技术——网卡绑定（Bonding），它主要用于提升网络可靠性和带宽。

网卡绑定（或称链路聚合）能将多块物理网卡虚拟成一块逻辑网卡。其主要优势有两点：
1.  **提升带宽**：多块网卡同时工作，实现负载均衡。
2.  **提供冗余**：当其中一块网卡故障时，流量会自动切换到其他网卡，保证网络不间断。

### 配置步骤
以下是在RHEL/CentOS 7系统中配置网卡绑定的基本步骤：

1.  **创建从属网卡配置文件**：需要为参与绑定的每块物理网卡创建配置文件（如 `ifcfg-eno1`， `ifcfg-eno2`），并指定它们的主设备（Master）。
    ```bash
    # 示例 ifcfg-eno1 的部分关键配置
    TYPE=Ethernet
    BOOTPROTO=none
    ONBOOT=yes
    USERCTL=no
    DEVICE=eno1
    MASTER=bond0
    SLAVE=yes
    ```

2.  **创建绑定网卡配置文件**：创建主绑定接口的配置文件（如 `ifcfg-bond0`）。
    ```bash
    # 示例 ifcfg-bond0 的配置
    DEVICE=bond0
    TYPE=Bond
    IPADDR=192.168.10.1
    NETMASK=255.255.255.0
    ONBOOT=yes
    BOOTPROTO=none
    USERCTL=no
    BONDING_OPTS="mode=6 miimon=100"
    ```
    **核心参数解释**：
    *   `mode=6`：绑定模式，6表示自适应负载均衡（balance-alb），既能负载均衡也能故障切换。
    *   `miimon=100`：链路监测间隔（毫秒），每100毫秒检查一次网卡状态。

3.  **加载绑定模块**：确保系统启动时加载绑定驱动。
    ```bash
    # 编辑 /etc/modprobe.d/bonding.conf
    alias bond0 bonding
    options bond0 mode=6 miimon=100
    ```

![](img/a06a22e6df22be2565106c15d18de7e5_27.png)

4.  **重启网络服务**：
    ```bash
    systemctl restart network
    ```
    配置完成后，使用 `ifconfig` 或 `ip addr show` 命令查看，会发现 `bond0` 接口获得IP地址，而原有的物理网卡不再直接配置IP。测试时，断开一块网卡，网络连接仅会出现短暂丢包（约100毫秒）后立即恢复。

**注意**：在RHEL/CentOS 8及更新版本中，推荐使用更新的 **Team（聚合）** 技术替代传统的Bonding，其配置更灵活，管理界面更友好。

![](img/a06a22e6df22be2565106c15d18de7e5_29.png)

![](img/a06a22e6df22be2565106c15d18de7e5_30.png)

![](img/a06a22e6df22be2565106c15d18de7e5_31.png)

---

![](img/a06a22e6df22be2565106c15d18de7e5_33.png)

## 章节三：SSH远程连接服务

![](img/a06a22e6df22be2565106c15d18de7e5_35.png)

网络连通性是基础，本节我们将深入学习最常用的远程管理工具——SSH（Secure Shell），并配置其高级功能以提升安全性和便利性。

SSH协议用于安全地远程登录和管理Linux服务器。其核心是通过加密通道传输数据，防止信息泄露。

### 1. 基本配置与安全加固
SSH服务的主配置文件是 `/etc/ssh/sshd_config`。修改后需重启 `sshd` 服务生效。

![](img/a06a22e6df22be2565106c15d18de7e5_37.png)

![](img/a06a22e6df22be2565106c15d18de7e5_38.png)

以下是两个重要的安全配置示例：

*   **修改默认端口**：将端口从22改为非常用端口（如10000），增加扫描难度。
    ```bash
    Port 10000
    ```
*   **禁止root用户直接登录**：降低被暴力破解的风险。
    ```bash
    PermitRootLogin no
    ```

### 2. 密钥认证
相比于密码，使用密钥对进行认证更安全。密钥认证使用非对称加密，私钥本地保存，公钥上传至服务器。

![](img/a06a22e6df22be2565106c15d18de7e5_40.png)

![](img/a06a22e6df22be2565106c15d18de7e5_41.png)

![](img/a06a22e6df22be2565106c15d18de7e5_42.png)

配置密钥登录的步骤如下：

![](img/a06a22e6df22be2565106c15d18de7e5_44.png)

1.  **在客户端生成密钥对**：
    ```bash
    ssh-keygen
    ```
    默认在 `~/.ssh/` 目录下生成私钥 `id_rsa` 和公钥 `id_rsa.pub`。

2.  **将公钥上传至服务器**：
    ```bash
    ssh-copy-id root@192.168.10.10
    ```

3.  **在服务器上禁用密码认证（可选，但更安全）**：
    编辑 `/etc/ssh/sshd_config`：
    ```bash
    PasswordAuthentication no
    ```
    重启 `sshd` 服务后，只能使用密钥登录。

![](img/a06a22e6df22be2565106c15d18de7e5_46.png)

### 3. 文件传输
SSH协议不仅可以执行命令，还能安全地传输文件，使用 `scp` 命令。

以下是 `scp` 命令的基本用法：

*   **上传文件到远程服务器**：
    ```bash
    scp local_file.txt root@192.168.10.10:/remote/directory/
    ```
*   **从远程服务器下载文件**：
    ```bash
    scp root@192.168.10.10:/remote/file.txt /local/directory/
    ```

### 4. 会话管理工具：`screen`/`tmux`
在进行长时间任务时，网络中断会导致命令终止。使用 `screen` 或 `tmux` 工具可以创建持久化会话。

![](img/a06a22e6df22be2565106c15d18de7e5_48.png)

![](img/a06a22e6df22be2565106c15d18de7e5_49.png)

以 `screen` 为例：

![](img/a06a22e6df22be2565106c15d18de7e5_51.png)

*   **安装**（如需）：
    ```bash
    yum install -y screen
    ```
*   **创建新会话**：
    ```bash
    screen -S my_session
    ```
*   **临时分离会话**：按 `Ctrl+A`，然后按 `D`。
*   **列出所有会话**：
    ```bash
    screen -ls
    ```
*   **恢复会话**：
    ```bash
    screen -r my_session  # 或使用会话ID
    ```
*   **共享会话（协同工作）**：
    用户A创建会话后，用户B可以连接到同一台服务器，通过 `screen -x 会话名` 加入同一会话，实现屏幕共享和协同操作。

---

![](img/a06a22e6df22be2565106c15d18de7e5_53.png)

![](img/a06a22e6df22be2565106c15d18de7e5_54.png)

## 总结
本节课中我们一起学习了三个关键的Linux系统管理技能。

首先，我们掌握了 **TCP Wrappers** 这一简单的基于主机的访问控制工具，能够通过服务名快速管理黑白名单。

其次，我们深入探讨了 **网卡绑定（Bonding）** 技术，理解了如何通过配置将多块物理网卡聚合，以实现网络带宽提升和高可用性，这是构建稳定服务器网络环境的重要实践。

![](img/a06a22e6df22be2565106c15d18de7e5_56.png)

最后，我们全面学习了 **SSH服务**，从修改端口、禁用root登录等基础安全加固，到更安全的密钥认证方式，再到利用 `scp` 进行文件传输，以及使用 `screen` 工具管理持久化会话以防止任务意外中断。这些知识是每一位Linux系统管理员进行日常远程管理和维护的基石。

![](img/a06a22e6df22be2565106c15d18de7e5_58.png)

![](img/a06a22e6df22be2565106c15d18de7e5_60.png)

![](img/a06a22e6df22be2565106c15d18de7e5_62.png)

通过本课的学习，你应该能够更安全、更高效地管理Linux服务器的网络访问和远程连接。