# RHCSA认证：2.01：配置网络

在本节课中，我们将学习如何为RHCSA考试中的虚拟机配置网络。这是考试的第一个关键步骤，确保你的系统能够正常通信是完成后续所有任务的基础。我们将使用图形化工具和命令行两种方式进行配置，并了解相关的核心概念。

![](img/b0345068e08def2d6426e4ef64ab8009_1.png)

![](img/b0345068e08def2d6426e4ef64ab8009_2.png)

![](img/b0345068e08def2d6426e4ef64ab8009_3.png)

![](img/b0345068e08def2d6426e4ef64ab8009_5.png)

![](img/b0345068e08def2d6426e4ef64ab8009_6.png)

## 考试环境概述

![](img/b0345068e08def2d6426e4ef64ab8009_8.png)

![](img/b0345068e08def2d6426e4ef64ab8009_9.png)

![](img/b0345068e08def2d6426e4ef64ab8009_10.png)

![](img/b0345068e08def2d6426e4ef64ab8009_12.png)

RHCSA考试通常涉及两台虚拟机，分别命名为`red`和`blue`。考试时，你需要在`red`虚拟机上完成约14个基础操作题目，其中第一项就是配置网络。

![](img/b0345068e08def2d6426e4ef64ab8009_14.png)

![](img/b0345068e08def2d6426e4ef64ab8009_16.png)

![](img/b0345068e08def2d6426e4ef64ab8009_18.png)

![](img/b0345068e08def2d6426e4ef64ab8009_19.png)

为了练习，你需要从真机通过远程连接（如SSH）来操作虚拟机。在开始练习前，建议使用`rht-vmctl reset red`命令将虚拟机重置到初始干净状态。

![](img/b0345068e08def2d6426e4ef64ab8009_21.png)

## 配置网络地址与主机名

![](img/b0345068e08def2d6426e4ef64ab8009_23.png)

![](img/b0345068e08def2d6426e4ef64ab8009_24.png)

![](img/b0345068e08def2d6426e4ef64ab8009_26.png)

![](img/b0345068e08def2d6426e4ef64ab8009_27.png)

上一节我们介绍了考试环境，本节中我们来看看如何具体配置网络。我们将使用`nmtui`工具，这是一个在终端中运行的图形化网络配置工具，非常适合在考试环境中使用。

![](img/b0345068e08def2d6426e4ef64ab8009_29.png)

### 使用 `nmtui` 配置网络

![](img/b0345068e08def2d6426e4ef64ab8009_31.png)

![](img/b0345068e08def2d6426e4ef64ab8009_33.png)

![](img/b0345068e08def2d6426e4ef64ab8009_34.png)

![](img/b0345068e08def2d6426e4ef64ab8009_36.png)

以下是使用`nmtui`配置IP地址、网关、DNS和主机名的步骤：

![](img/b0345068e08def2d6426e4ef64ab8009_38.png)

![](img/b0345068e08def2d6426e4ef64ab8009_40.png)

1.  **启动工具**：在虚拟机终端中输入命令 `nmtui` 并回车。
2.  **编辑连接**：
    *   选择 **“Edit a connection”** 并回车。
    *   在连接列表中选择你的网络连接（通常是`ens160`或类似名称），回车进入编辑界面。
3.  **配置IPv4**：
    *   将光标移动到 **“IPv4 CONFIGURATION”** 行，将模式从 **“Automatic”** 改为 **“Manual”**。
    *   选择 **“Show”** 以显示详细配置区域。
    *   在 **“Addresses”** 部分，按 **“Add”** 添加IP地址，格式为 `IP地址/子网掩码前缀`，例如 `172.25.0.25/24`。
    *   在 **“Gateway”** 行填写网关地址，例如 `172.25.0.254`。
    *   在 **“DNS servers”** 行填写DNS服务器地址，例如 `172.25.0.254`。
4.  **启用设置**：
    *   确保 **“Require IPv4 addressing for this connection”** 选项被选中（按空格键）。
    *   确保 **“Automatically connect”** 选项被选中。
    *   移动到底部，选择 **“OK”** 保存配置。
5.  **激活连接**：
    *   返回主菜单，选择 **“Activate a connection”**。
    *   找到你刚配置的连接，先选择 **“Deactivate”** 停用它，再选择 **“Activate”** 重新激活，以使新配置生效。
6.  **设置主机名**：
    *   返回主菜单，选择 **“Set system hostname”**。
    *   输入考试要求的主机名，例如 `red.net0.example.com`，然后选择 **“OK”**。
7.  **退出工具**：最后选择 **“Quit”** 退出`nmtui`。

![](img/b0345068e08def2d6426e4ef64ab8009_42.png)

![](img/b0345068e08def2d6426e4ef64ab8009_44.png)

配置完成后，你可以从考试环境的真机打开终端，使用SSH连接到虚拟机进行后续操作，命令格式为 `ssh root@虚拟机IP地址或主机名`。

## 网络管理核心知识

![](img/b0345068e08def2d6426e4ef64ab8009_46.png)

![](img/b0345068e08def2d6426e4ef64ab8009_48.png)

![](img/b0345068e08def2d6426e4ef64ab8009_49.png)

在红帽企业Linux 8中，网络由 **NetworkManager** 服务管理，这与旧版本（如RHEL 6/7）中使用的`network`服务不同。

![](img/b0345068e08def2d6426e4ef64ab8009_51.png)

### 管理 NetworkManager 服务

使用 `systemctl` 命令控制服务：
*   启动服务：`systemctl start NetworkManager`
*   停止服务：`systemctl stop NetworkManager`
*   查看状态：`systemctl status NetworkManager`
*   设置开机自启：`systemctl enable --now NetworkManager`

### 常用网络命令

以下是一些有用的网络诊断和配置命令：
*   **查看IP地址**：`ip address show` 或 `ip a`
*   **查看及设置主机名**：
    *   查看完整主机名信息：`hostnamectl status`
    *   设置永久主机名：`hostnamectl set-hostname 新主机名`
*   **测试网络连通性**：`ping 目标IP或域名`
*   **检查DNS解析**：`host 域名` (需要安装 `bind-utils` 包)

### 命令行配置工具 `nmcli`

`nmcli` 是NetworkManager的命令行客户端，适合在脚本中使用。以下是一些常用操作：

查看网络设备状态：
```bash
nmcli device status
```

查看连接配置：
```bash
nmcli connection show
```

修改一个连接的IP地址配置（例如连接名为`ens160`）：
```bash
nmcli connection modify ens160 ipv4.addresses 172.25.0.25/24 ipv4.gateway 172.25.0.254 ipv4.dns 172.25.0.254 ipv4.method manual
```

激活一个连接：
```bash
nmcli connection up ens160
```

停用一个连接：
```bash
nmcli connection down ens160
```

![](img/b0345068e08def2d6426e4ef64ab8009_53.png)

![](img/b0345068e08def2d6426e4ef64ab8009_55.png)

## 总结

![](img/b0345068e08def2d6426e4ef64ab8009_57.png)

![](img/b0345068e08def2d6426e4ef64ab8009_58.png)

![](img/b0345068e08def2d6426e4ef64ab8009_60.png)

本节课中我们一起学习了RHCSA考试中配置网络的核心技能。我们首先使用直观的`nmtui`工具完成了IP地址、网关、DNS和主机名的配置，这是考试中最推荐的方法。接着，我们深入了解了红帽8系统的网络管理服务**NetworkManager**以及相关的命令行工具`nmcli`和`ip`。掌握这些知识不仅能帮助你顺利通过考试，也是日常Linux系统管理中的必备能力。配置好网络后，我们就可以为下一项任务——配置软件仓库做好准备了。