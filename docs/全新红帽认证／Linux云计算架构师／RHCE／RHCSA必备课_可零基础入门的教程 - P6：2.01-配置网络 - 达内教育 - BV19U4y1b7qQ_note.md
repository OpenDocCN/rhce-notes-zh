# Linux认证课程：P6：2.01-配置网络 🌐

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_1.png)

在本节课中，我们将学习如何为RHCSA考试中的虚拟机配置网络。这是考试的第一个关键步骤，涉及设置主机名、IP地址、默认网关和DNS服务器。我们将使用图形化工具`nmtui`来完成配置，并补充一些相关的命令行知识，以应对各种情况。

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_2.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_3.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_5.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_6.png)

## 考试环境概述

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_8.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_9.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_10.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_12.png)

RHCSA上午考试通常涉及两台虚拟机，分别命名为`red`和`blue`。考试时，你需要登录到这些虚拟机并完成一系列任务。`red`虚拟机上的任务通常包括网络配置、软件仓库设置、SELinux调试、用户账户创建等基础操作。

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_14.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_16.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_18.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_19.png)

为了高效练习，建议在每次开始前将虚拟机重置到初始状态。在练习环境中，可以使用命令 `rht-vmctl reset red` 或 `rht-vmctl reset blue` 来重置对应的虚拟机。

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_21.png)

## 访问虚拟机控制台

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_23.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_24.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_26.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_27.png)

要开始配置，首先需要访问虚拟机的图形界面。

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_29.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_31.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_33.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_34.png)

以下是访问虚拟机控制台的步骤：
1.  在练习环境的桌面，点击左上角的“Activities”。
2.  在下拉菜单中找到并点击“Virtual Machine Manager”图标。
3.  在打开的窗口中，双击名为 `red` 的虚拟机。
4.  在虚拟机窗口出现后，点击右下角的“Send Key”菜单，选择“Ctrl+Alt+Del”来唤醒登录界面。
5.  使用 `root` 账号和密码 `redhat` 登录系统。

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_36.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_38.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_40.png)

**注意**：在真实考试环境中，会通过一个名为“VM Control”的窗口来启动、还原和控制虚拟机，操作逻辑类似。

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_42.png)

## 配置网络地址与主机名

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_44.png)

登录系统后，首要任务是配置网络。我们使用 `nmtui`（NetworkManager Text User Interface）工具，这是一个简单直观的文本界面配置工具。

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_46.png)

以下是使用 `nmtui` 配置网络的详细步骤：
1.  在虚拟机终端中，输入命令 `nmtui` 并回车。
2.  使用上下箭头选择 **“Edit a connection”**，然后回车。
3.  在连接列表中选择你的网络连接（通常是 `ens160` 或类似名称），回车进入编辑界面。
4.  将 **IPv4 CONFIGURATION** 从 “Automatic” 改为 **“Manual”**。
5.  点击右侧的 **“Show”** 按钮，展开详细设置。
6.  在 **Addresses** 栏，点击 **“Add”**，然后输入题目要求的IP地址和子网掩码，例如：`172.25.0.25/24`。
7.  在 **Gateway** 栏，输入默认网关地址，例如：`172.25.0.254`。
8.  在 **DNS servers** 栏，点击 **“Add”**，输入DNS服务器地址，例如：`172.25.0.254`。
9.  确保勾选了 **“Automatically connect”** 和 **“Available to all users”** 选项。
10. 点击右下角的 **“OK”** 保存配置。
11. 返回主菜单，选择 **“Activate a connection”**，找到你的连接，先点击 **“Deactivate”** 停用，再点击 **“Activate”** 重新激活，以使新配置生效。
12. 再次返回主菜单，选择 **“Set system hostname”**，输入要求的主机名（例如 `red.net0.example.com`），点击 **“OK”**。
13. 最后，选择 **“Quit”** 退出 `nmtui`。

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_48.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_49.png)

配置完成后，可以使用 `ip address` 命令来验证IP地址是否设置正确。

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_51.png)

## 建立远程连接

网络配置正确后，为了获得更好的操作体验（如复制粘贴、字体清晰），建议从考试环境的“真机”（即你面前的物理电脑）通过SSH远程连接到虚拟机。

在真机上打开终端，输入以下命令：
```bash
ssh root@red
```
或使用IP地址：
```bash
ssh root@172.25.0.25
```
首次连接时需要输入 `yes` 接受密钥，然后输入虚拟机 `root` 用户的密码。成功后，后续所有操作都可以在这个更便捷的远程终端中进行。

## 网络管理相关知识补充

上一节我们完成了基础配置，本节中我们来了解一些底层的网络管理知识，以便更灵活地应对问题。

在RHEL 8中，网络由 **NetworkManager** 服务管理，而非旧版本中的 `network` 服务。

**服务管理命令**：
*   启动服务：`systemctl start NetworkManager`
*   停止服务：`systemctl stop NetworkManager`
*   设置开机自启：`systemctl enable NetworkManager`
*   查看状态：`systemctl status NetworkManager`

**主机名管理**：
*   查看完整主机名信息：`hostnamectl`
*   设置并永久保存主机名：`hostnamectl set-hostname <新主机名>`
*   主机名配置文件位于：`/etc/hostname`

**命令行配置工具（备用）**：
如果系统支持命令补全，可以使用 `nmcli` 命令进行配置，这在编写脚本时非常有用。
*   查看设备状态：`nmcli device`
*   查看连接配置：`nmcli connection show`
*   修改连接为手动配置并设置IP：`nmcli connection modify “ens160” ipv4.method manual ipv4.addresses “172.25.0.25/24”`
*   设置网关和DNS：`nmcli connection modify “ens160” ipv4.gateway “172.25.0.254” ipv4.dns “172.25.0.254”`
*   设置自动连接：`nmcli connection modify “ens160” connection.autoconnect yes`
*   激活连接：`nmcli connection up “ens160”`

**常用网络诊断命令**：
一些命令需要安装 `net-tools` 或 `bind-utils` 软件包后才可用。
*   查看IP（推荐）：`ip address` 或 `ip a`
*   测试DNS解析：`host red.net0.example.com`
*   查看路由：`ip route`

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_53.png)

## 总结

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_55.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_57.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_58.png)

![](img/03e2b72f8bc0ed20053f7f06797c6ed8_60.png)

本节课中我们一起学习了RHCSA考试中配置网络的核心步骤。我们首先了解了考试环境，然后使用 `nmtui` 工具逐步配置了IP地址、网关、DNS和主机名。配置成功后，我们建立了从真机到虚拟机的SSH远程连接，以便于后续操作。最后，我们补充了关于NetworkManager服务、主机名管理以及 `nmcli` 命令行工具的相关知识，为你提供了更全面的技能储备。掌握这些内容，是顺利通过考试并胜任Linux系统管理工作的坚实基础。