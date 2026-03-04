# RHCSA精讲教程：P6：2.01-配置网络 🌐

![](img/d7a9fddd6b53ea96f495582494abdcf7_1.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_2.png)

在本节课中，我们将学习RHCSA考试中关于网络配置的核心考点。我们将从考试环境介绍开始，逐步讲解如何为虚拟机配置主机名、IP地址、网关和DNS服务器，并补充相关的网络管理知识，确保初学者也能轻松掌握。

![](img/d7a9fddd6b53ea96f495582494abdcf7_3.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_5.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_6.png)

## 考试环境概述

![](img/d7a9fddd6b53ea96f495582494abdcf7_8.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_9.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_10.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_12.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_14.png)

RHCSA考试分为上午和下午两个部分。上午考试通常使用两台虚拟机，一台名为`red`，另一台名为`blue`。考试环境均在虚拟机中进行。

![](img/d7a9fddd6b53ea96f495582494abdcf7_16.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_18.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_19.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_21.png)

在`red`虚拟机上，大约会考察14个题目，涵盖网络设置、软件仓库配置、SELinux调试、用户账户与目录管理、NTP时间同步以及文件系统权限修改等基础操作。

![](img/d7a9fddd6b53ea96f495582494abdcf7_23.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_24.png)

`blue`虚拟机则涉及更高级的设置，包括磁盘分区、逻辑卷管理、交换分区配置、VDO卷创建以及系统调优配置。

![](img/d7a9fddd6b53ea96f495582494abdcf7_26.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_27.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_29.png)

## 准备练习环境 🛠️

![](img/d7a9fddd6b53ea96f495582494abdcf7_31.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_33.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_34.png)

在开始练习前，建议先将虚拟机恢复到初始的干净状态，以模拟真实的考试环境。

![](img/d7a9fddd6b53ea96f495582494abdcf7_36.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_38.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_40.png)

以下是重置虚拟机的方法：
*   使用命令 `rht-vmctl reset red` 重置 `red` 虚拟机。
*   使用命令 `rht-vmctl reset blue` 重置 `blue` 虚拟机。

![](img/d7a9fddd6b53ea96f495582494abdcf7_42.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_44.png)

重置后，可以通过图形界面工具 `Virtual Machine Manager` 打开并控制虚拟机。在考试环境中，则通过桌面上的 `VM Control` 图标来启动、还原和控制虚拟机界面。

登录虚拟机后，管理员账号为 `root`，密码在考试时会提供。在我们的练习环境中，`red`虚拟机的密码是 `redhat`。

![](img/d7a9fddd6b53ea96f495582494abdcf7_46.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_48.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_49.png)

## 配置网络地址与主机名

![](img/d7a9fddd6b53ea96f495582494abdcf7_51.png)

网络配置通常是考试的第一道题目，要求为虚拟机设置指定的主机名、静态IP地址、默认网关和DNS服务器。

上一节我们介绍了考试环境和准备工作，本节中我们来看看如何具体配置网络。最推荐的方法是使用 `nmtui` 工具，这是一个基于文本的用户界面，操作直观。

以下是使用 `nmtui` 配置网络的详细步骤：

1.  **启动工具**：在虚拟机终端中输入命令 `nmtui` 并回车。
2.  **设置主机名**：
    *   选择 `Set system hostname`。
    *   输入题目要求的主机名（例如 `red.net0.example.com`）。
    *   按 `Tab` 键切换到 `OK` 并回车确认。
3.  **编辑网络连接**：
    *   返回主菜单，选择 `Edit a connection`。
    *   选择唯一的网络连接（通常是 `ens160` 或类似名称），回车进入编辑。
4.  **配置IPv4地址**：
    *   找到 `IPv4 CONFIGURATION`，将模式从 `Automatic` 改为 `Manual`。
    *   选择 `Show` 以显示详细设置。
    *   在 `Addresses` 处按 `Add`，输入指定的IP地址和子网掩码（例如 `172.25.0.25/24`）。
    *   在 `Gateway` 处输入默认网关（例如 `172.25.0.254`）。
    *   在 `DNS servers` 处输入DNS服务器地址（例如 `172.25.0.254`）。
5.  **启用设置**：
    *   确保勾选 `Require IPv4 addressing for this connection` 和 `Automatically connect`。
    *   按 `Tab` 键切换到 `OK` 并回车保存。
6.  **激活连接**：
    *   返回主菜单，选择 `Activate a connection`。
    *   选中当前连接，先选择 `Deactivate` 停用，再选择 `Activate` 重新激活。
    *   按 `Back` 返回并选择 `Quit` 退出 `nmtui`。

配置完成后，可以从考试环境的主机（真机）通过SSH远程连接到虚拟机进行后续操作，验证网络是否通畅。

## 网络管理知识补充 📚

在Red Hat Enterprise Linux 8中，网络由 `NetworkManager` 服务管理，而非旧版本中的 `network` 服务。

以下是关于网络服务管理和常用命令的要点：

*   **服务管理**：使用 `systemctl` 命令控制 `NetworkManager` 服务。
    *   启动服务：`systemctl start NetworkManager`
    *   设置开机自启：`systemctl enable --now NetworkManager`
*   **查看网络信息**：
    *   查看IP地址：`ip address show` 或 `ip a`
    *   查看主机名：`hostnamectl status`
*   **设置主机名**：使用 `hostnamectl` 命令可以永久设置主机名。
    *   命令格式：`hostnamectl set-hostname <新主机名>`
    *   主机名配置文件位于 `/etc/hostname`。
*   **命令行配置工具**：`nmcli` 是 `NetworkManager` 的命令行工具，适合脚本编写或熟练后使用。
    *   查看设备状态：`nmcli device status`
    *   查看连接配置：`nmcli connection show`
    *   修改连接IP（示例）：
        ```bash
        nmcli connection modify “ens160” ipv4.method manual ipv4.addresses “172.25.0.25/24” ipv4.gateway “172.25.0.254” ipv4.dns “172.25.0.254”
        ```
    *   激活连接：`nmcli connection up “ens160”`

一些传统的网络命令（如 `ifconfig`, `route -n`, `host`）需要安装 `net-tools` 或 `bind-utils` 软件包后才可使用。在考试中，若无法使用，应优先采用 `nmtui` 或 `nmcli`。

![](img/d7a9fddd6b53ea96f495582494abdcf7_53.png)

## 总结

![](img/d7a9fddd6b53ea96f495582494abdcf7_55.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_57.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_58.png)

![](img/d7a9fddd6b53ea96f495582494abdcf7_60.png)

本节课中我们一起学习了RHCSA考试网络配置部分的核心内容。我们首先了解了考试的整体环境和两台虚拟机（`red`和`blue`）的职责。然后，详细演练了使用 `nmtui` 工具配置静态IP地址、网关、DNS和主机名的完整流程，这是考试中最稳妥的方法。最后，我们补充了关于 `NetworkManager` 服务、`hostnamectl` 和 `nmcli` 命令的相关知识，帮助你更深入地理解Linux网络管理，并应对可能出现的各种情况。掌握这些基础，是顺利通过后续考题的第一步。