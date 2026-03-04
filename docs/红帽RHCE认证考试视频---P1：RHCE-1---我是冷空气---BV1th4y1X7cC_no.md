# RHCE认证课程：P1：RHCE-1 课程介绍与基础环境搭建 🚀

在本节课中，我们将要学习RHCE认证的课程概述、实验环境的搭建，以及使用`systemctl`命令管理服务和配置IPv4网络的基础知识。这些内容是后续所有实验和考试的基础，请务必掌握。

## 课程概述与认证体系

上一节我们介绍了课程的整体安排，本节中我们来看看红帽认证的具体构成。

![](img/2ed40a0ba74dec3123f681a46ff6006e_1.png)

红帽认证是红帽公司推出的Linux资格认证体系，主要包含三个部分：

*   **RHCSA (红帽认证系统管理员)**：认证内容涵盖文件管理、本地存储、系统服务维护、用户与组管理以及基础安全配置。
*   **RHCE (红帽认证工程师)**：本课程的核心，认证内容包含防火墙配置、网络路由、SSH服务、Shell脚本、Web服务器等高级系统管理技能。
*   **RHCA (红帽认证架构师)**：更高级别的认证，涉及微服务、集群、虚拟化、性能调优和云平台等。

本课程将聚焦于**RHCE**认证的11个核心课时（不含第12章容器技术），这些内容均为考试重点。

![](img/2ed40a0ba74dec3123f681a46ff6006e_3.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_5.png)

## 实验环境详解

理解实验环境是顺利进行所有实验的前提。我们主要使用三台虚拟机：

1.  **classroom.example.com (172.25.254.254)**：
    *   核心服务器，需**最先启动**。
    *   提供DHCP服务，为其他两台机器分配IP地址。
    *   提供DNS域名解析服务。
    *   存放验证用的文件和网页。

2.  **server0.example.com (172.25.254.10)**：
    *   主机名为 `server0`。
    *   在大多数实验中作为**服务器端**使用。

3.  **desktop0.example.com (172.25.254.20)**：
    *   主机名为 `desktop0`。
    *   在大多数实验中作为**客户端**使用。

![](img/2ed40a0ba74dec3123f681a46ff6006e_7.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_9.png)

**关键点**：实验前需通过虚拟机快照功能将系统还原到初始状态。实验准备和验证通常通过运行特定脚本来完成：
*   准备实验环境：`lab <实验名> setup`
*   验证实验结果：`lab <实验名> grade`

![](img/2ed40a0ba74dec3123f681a46ff6006e_11.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_13.png)

## 使用 systemctl 管理服务

`systemctl` 是管理系统服务（守护进程）的核心命令，由 `systemd` 初始化系统提供。

### 核心概念与命令格式

命令基本格式为：`systemctl [动作] [单元名称]`

![](img/2ed40a0ba74dec3123f681a46ff6006e_15.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_17.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_19.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_21.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_23.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_25.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_26.png)

其中，**单元名称** 最常用的是带 `.service` 后缀的服务单元（如 `sshd.service`）。**关键动作** 包括：

![](img/2ed40a0ba74dec3123f681a46ff6006e_28.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_29.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_31.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_33.png)

*   `start`：启动服务。
*   `stop`：停止服务。
*   `restart`：重启服务（先停止再启动，进程PID会改变）。
*   `reload`：重新加载配置文件（不重启进程，PID不变）。
*   `status`：查看服务状态。
*   `enable`：设置服务开机自启。
*   `disable`：禁止服务开机自启。
*   `is-active`：检查服务是否正在运行。
*   `is-enabled`：检查服务是否设置为开机自启。

查看服务状态时，需关注几个**关键状态**：
*   `active (running)`：服务正在运行。
*   `inactive (dead)`：服务未运行。
*   `enabled`：服务已设置为开机自启。
*   `disabled`：服务未设置为开机自启。

### 实验：管理服务示例

![](img/2ed40a0ba74dec3123f681a46ff6006e_35.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_37.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_39.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_41.png)

以下是验证 `systemctl` 基本用法的实验步骤：

![](img/2ed40a0ba74dec3123f681a46ff6006e_43.png)

1.  **查看服务状态与PID**：
    ```bash
    systemctl status sshd
    ```
    记录下 `sshd` 服务的进程ID (PID)。

![](img/2ed40a0ba74dec3123f681a46ff6006e_45.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_47.png)

2.  **比较 restart 与 reload**：
    ```bash
    systemctl restart sshd
    systemctl status sshd # 观察PID是否改变
    systemctl reload sshd
    systemctl status sshd # 观察PID是否保持不变
    ```
    这验证了 `restart` 会改变进程PID，而 `reload` 不会。

3.  **设置服务开机自启**：
    ```bash
    systemctl stop chronyd          # 停止服务
    systemctl disable chronyd       # 禁止开机自启
    systemctl is-enabled chronyd    # 检查状态，应为 disabled
    # 重启系统后，chronyd 服务将不会自动启动。
    systemctl enable chronyd        # 重新设置为开机自启
    systemctl start chronyd         # 启动服务
    ```

![](img/2ed40a0ba74dec3123f681a46ff6006e_49.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_51.png)

## 管理系统运行级别（Target）

![](img/2ed40a0ba74dec3123f681a46ff6006e_53.png)

`systemd` 使用 **target** 来定义系统的运行状态，类似于传统的运行级别。

![](img/2ed40a0ba74dec3123f681a46ff6006e_55.png)

### 常用 Target

![](img/2ed40a0ba74dec3123f681a46ff6006e_57.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_58.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_60.png)

*   `graphical.target`：多用户图形界面（默认级别之一）。
*   `multi-user.target`：多用户字符界面（最常用的服务器级别）。
*   `rescue.target`：急救模式，用于系统修复。
*   `emergency.target`：紧急模式，用于更严重的故障恢复。

![](img/2ed40a0ba74dec3123f681a46ff6006e_62.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_64.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_66.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_68.png)

### 关键操作

*   **查看默认启动目标**：`systemctl get-default`
*   **设置默认启动目标**：`systemctl set-default multi-user.target`
*   **临时切换目标**：`systemctl isolate multi-user.target` （切换到字符界面）
*   **在GRUB启动时临时进入特定目标**：在GRUB菜单编辑启动项，在 `linux16` 行末尾添加 `systemd.unit=rescue.target`

### 实验：修改默认启动目标

![](img/2ed40a0ba74dec3123f681a46ff6006e_70.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_71.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_73.png)

1.  将默认启动目标设置为字符界面：
    ```bash
    systemctl set-default multi-user.target
    ```
2.  重启系统验证：
    ```bash
    reboot
    ```
3.  系统启动后将直接进入字符界面。登录后，可再将其改回图形界面：
    ```bash
    systemctl set-default graphical.target
    reboot
    ```

![](img/2ed40a0ba74dec3123f681a46ff6006e_75.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_76.png)

## 使用 nmcli 配置 IPv4 网络

`nmcli` 是 NetworkManager 的命令行工具，用于配置网络连接。

![](img/2ed40a0ba74dec3123f681a46ff6006e_78.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_80.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_82.png)

### 核心概念

*   **设备 (Device)**：物理或虚拟网络接口（如 `eth0`）。
*   **连接 (Connection)**：配置设置的集合，可以应用到设备上。一个设备同一时间只能有一个激活的连接。
*   **配置文件**：持久化连接配置存储在 `/etc/sysconfig/network-scripts/ifcfg-<连接名>` 中。

### 关键命令示例

以下是配置网络的基本流程：

1.  **查看设备与连接**：
    ```bash
    nmcli device status          # 查看网络设备状态
    nmcli connection show        # 查看所有连接
    ```

2.  **创建新连接并配置静态IP**：
    ```bash
    # 为设备 eno1 创建一个名为 “eno1” 的连接
    nmcli connection add con-name eno1 type ethernet ifname eno1

    # 为该连接配置静态IP地址、子网掩码和网关
    nmcli connection modify eno1 ipv4.addresses '192.168.1.100/24'
    nmcli connection modify eno1 ipv4.gateway '192.168.1.1'
    nmcli connection modify eno1 ipv4.method manual

    # 配置DNS服务器
    nmcli connection modify eno1 ipv4.dns '8.8.8.8'

    # 激活连接（启用配置）
    nmcli connection down eno1 && nmcli connection up eno1
    ```

![](img/2ed40a0ba74dec3123f681a46ff6006e_84.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_86.png)

3.  **验证配置**：
    ```bash
    ip addr show eno1            # 查看IP地址
    ping 192.168.1.1             # 测试网络连通性
    cat /etc/sysconfig/network-scripts/ifcfg-eno1 # 查看配置文件
    ```

4.  **修改主机名**：
    ```bash
    hostnamectl set-hostname server0.example.com  # 永久修改主机名
    hostnamectl status                            # 查看主机名信息
    ```

### 实验：配置静态IPv4地址

1.  准备实验环境（如果需要）并查看现有网络接口。
2.  使用 `nmcli` 为指定网卡创建连接、配置静态IP、网关和DNS。
3.  激活连接并使用 `ping` 命令测试网络连通性。
4.  编辑 `/etc/hosts` 文件，添加本地主机名解析记录。

![](img/2ed40a0ba74dec3123f681a46ff6006e_88.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_90.png)

## 总结

![](img/2ed40a0ba74dec3123f681a46ff6006e_92.png)

本节课中我们一起学习了RHCE课程的总体框架和实验环境，这是所有后续学习的基础。我们重点掌握了两个核心工具：
1.  **`systemctl`**：用于管理系统服务的生命周期（启动、停止、重启、状态查看）和设置开机自启，以及管理系统运行级别（target）。
2.  **`nmcli`**：用于在命令行中配置网络连接，包括创建连接、设置静态IP地址、网关、DNS等。

![](img/2ed40a0ba74dec3123f681a46ff6006e_94.png)

![](img/2ed40a0ba74dec3123f681a46ff6006e_95.png)

请务必在课下反复练习实验，熟悉命令和操作步骤，为后续更复杂的学习内容打下坚实基础。