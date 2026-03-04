# 备考红帽认证必修课：P6：配置网络 📡

在本节课中，我们将学习如何为红帽认证考试（RHCSA/RHCE）中的虚拟机配置网络。这是考试中的第一个关键步骤，涉及设置主机名、IP地址、网关和DNS服务器。我们将使用图形化工具 `nmtui` 来完成配置，并补充一些相关的命令行知识，以应对各种情况。

![](img/4579ab5001b07a3bec4e19a5ca872bf4_1.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_2.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_3.png)

## 考试环境概述

![](img/4579ab5001b07a3bec4e19a5ca872bf4_5.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_6.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_8.png)

RHCSA考试通常涉及两台虚拟机，分别命名为 `red` 和 `blue`。考试时，您需要从考场提供的真机远程连接到这些虚拟机进行操作。`red` 虚拟机通常包含约14个基础操作题目，网络配置是其中的第一项。

![](img/4579ab5001b07a3bec4e19a5ca872bf4_9.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_10.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_12.png)

## 准备工作

![](img/4579ab5001b07a3bec4e19a5ca872bf4_14.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_16.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_18.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_19.png)

在开始配置前，请确保您的练习环境已准备就绪。

![](img/4579ab5001b07a3bec4e19a5ca872bf4_21.png)

以下是启动和连接虚拟机的步骤：

![](img/4579ab5001b07a3bec4e19a5ca872bf4_23.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_24.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_26.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_27.png)

1.  在真机终端中，使用 `rht-vmctl` 命令重置虚拟机到初始状态。
    ```bash
    rht-vmctl reset red
    ```
2.  通过图形界面（Activities -> 九宫格图标 -> Virtual Machine Manager）打开虚拟机管理器。
3.  双击 `red` 虚拟机图标打开其控制台。
4.  单击控制台右下角的“Send Key”菜单，选择“Ctrl+Alt+Del”以获取控制权。
5.  使用 `root` 账户和密码（练习环境通常为 `redhat`）登录系统。

![](img/4579ab5001b07a3bec4e19a5ca872bf4_29.png)

## 配置网络地址

![](img/4579ab5001b07a3bec4e19a5ca872bf4_31.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_33.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_34.png)

登录虚拟机后，首要任务是配置网络。我们推荐使用 `nmtui`（Network Manager Text User Interface）工具，它在考试环境中稳定且易用。

![](img/4579ab5001b07a3bec4e19a5ca872bf4_36.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_38.png)

以下是使用 `nmtui` 配置网络的详细流程：

![](img/4579ab5001b07a3bec4e19a5ca872bf4_40.png)

1.  在虚拟机终端中输入命令启动工具：
    ```bash
    nmtui
    ```
2.  选择 **“Edit a connection”** 并回车。
3.  在连接列表中选择唯一的以太网连接（通常是 `ens160` 或类似名称），回车进入编辑界面。
4.  将 **IPv4 Configuration** 从 `Automatic` 改为 `Manual`。
5.  选择 **“Show”** 以显示详细配置区域。
6.  在 **Addresses** 区域，选择 **“Add”**，然后根据考题要求输入IP地址和子网掩码（例如：`172.25.0.25/24`）。
7.  在 **Gateway** 字段输入网关地址（例如：`172.25.0.254`）。
8.  在 **DNS servers** 字段输入DNS服务器地址（通常与网关相同，例如：`172.25.0.254`）。
9.  确保勾选 **“Require IPv4 addressing for this connection”** 和 **“Automatically connect”** 选项。
10. 选择 **“OK”** 保存配置，然后选择 **“Back”** 返回主菜单。
11. 在主菜单选择 **“Activate a connection”**。
12. 找到刚才配置的连接，先选择 **“Deactivate”** 停用它，再选择 **“Activate”** 重新激活，以使新配置生效。
13. 返回主菜单，选择 **“Set system hostname”**。
14. 根据考题要求设置主机名（例如：`red.net0.example.com`），选择 **“OK”**。
15. 最后，选择 **“Quit”** 退出 `nmtui`。

![](img/4579ab5001b07a3bec4e19a5ca872bf4_42.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_44.png)

## 验证与远程连接

配置完成后，需要验证网络是否通畅，并从真机进行远程连接。

![](img/4579ab5001b07a3bec4e19a5ca872bf4_46.png)

以下是验证和连接的步骤：

![](img/4579ab5001b07a3bec4e19a5ca872bf4_48.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_49.png)

1.  在虚拟机中，可以使用 `ip address` 命令查看配置的IP地址是否生效。
    ```bash
    ip address show
    ```
2.  在考场提供的真机上，打开终端，使用 `ssh` 命令尝试远程连接。
    ```bash
    ssh root@red.net0.example.com
    ```
    或使用IP地址：
    ```bash
    ssh root@172.25.0.25
    ```
3.  首次连接时需要接受密钥，然后输入 `root` 密码。连接成功后，后续所有操作都可以在这个远程终端中进行，更加方便。

![](img/4579ab5001b07a3bec4e19a5ca872bf4_51.png)

## 相关知识补充

上一节我们完成了基础的网络配置，本节中我们来看看一些相关的系统知识和备用命令，以加深理解并应对意外情况。

### 网络管理服务

在RHEL 8中，负责网络管理的核心服务是 **NetworkManager**，而非旧版本中的 `network` 服务。

使用 `systemctl` 命令管理该服务：
*   启动服务：`systemctl start NetworkManager`
*   设置开机自启：`systemctl enable NetworkManager`
*   查看状态：`systemctl status NetworkManager`

### 主机名管理

设置永久主机名推荐使用 `hostnamectl` 命令：
```bash
hostnamectl set-hostname red.net0.example.com
```
此命令会同时修改当前主机名并写入配置文件 `/etc/hostname`。

### 命令行配置工具 `nmcli`

如果环境支持命令补全，使用 `nmcli` 命令行工具配置网络效率更高。

以下是 `nmcli` 的常用操作：

*   **查看设备与连接状态**
    ```bash
    nmcli device status
    nmcli connection show
    ```
*   **修改连接配置**（以连接 `ens160` 为例）
    ```bash
    # 设置手动配置模式
    nmcli connection modify ens160 ipv4.method manual
    # 配置IP地址和网关
    nmcli connection modify ens160 ipv4.addresses 172.25.0.25/24 ipv4.gateway 172.25.0.254
    # 配置DNS服务器
    nmcli connection modify ens160 ipv4.dns 172.25.0.254
    # 设置自动连接
    nmcli connection modify ens160 connection.autoconnect yes
    ```
*   **激活连接**
    ```bash
    nmcli connection up ens160
    ```

### 常用网络工具包

一些熟悉的命令（如 `ifconfig`, `netstat`, `route -n`）包含在 `net-tools` 软件包中。如果系统未安装，在配置好软件源后，可以通过以下命令安装：
```bash
dnf install net-tools -y
```

![](img/4579ab5001b07a3bec4e19a5ca872bf4_53.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_55.png)

## 总结

![](img/4579ab5001b07a3bec4e19a5ca872bf4_57.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_58.png)

![](img/4579ab5001b07a3bec4e19a5ca872bf4_60.png)

本节课中我们一起学习了RHCSA考试中配置网络的核心步骤。我们首先使用 `nmtui` 工具完成了主机名、静态IP地址、网关和DNS服务器的配置，并学会了从真机远程连接到虚拟机。此外，我们还补充了关于NetworkManager服务、`hostnamectl` 和 `nmcli` 命令的相关知识，为您应对考试和实际工作提供了更全面的准备。请务必在练习环境中反复操作，直至熟练掌握。