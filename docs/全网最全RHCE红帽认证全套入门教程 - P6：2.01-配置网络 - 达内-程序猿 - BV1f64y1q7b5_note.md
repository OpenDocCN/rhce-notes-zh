# RHCE红帽认证全套入门教程：P6：2.01-配置网络 🌐

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_1.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_2.png)

在本节课中，我们将要学习如何在红帽企业Linux 8系统中配置网络。这是RHCE考试上午部分的基础操作，也是后续所有任务能够顺利进行的前提。我们将从考试环境介绍开始，逐步讲解如何配置主机名、IP地址、网关和DNS服务器，并补充一些相关的网络管理知识。

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_3.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_5.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_6.png)

## 考试环境与网络配置概述

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_8.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_9.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_10.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_12.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_14.png)

上一节我们介绍了RHCE考试的基本框架，本节中我们来看看具体的网络配置任务。考试时，上午部分通常有两台虚拟机，分别命名为`red`和`blue`。第一台`red`机器会考察约14个基础操作题目，其中第一个就是配置网络。

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_16.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_18.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_19.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_21.png)

配置网络的目标是让虚拟机能够被远程访问，以便后续操作。主要任务包括：
*   设置静态主机名。
*   配置静态IP地址、子网掩码、默认网关和DNS服务器。

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_23.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_24.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_26.png)

以下是配置网络的核心步骤：
1.  使用 `nmtui` 工具进行图形化配置。
2.  配置完成后，从考试环境的真机通过SSH远程连接到虚拟机进行验证。

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_27.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_29.png)

## 详细配置步骤

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_31.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_33.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_34.png)

### 1. 启动并登录虚拟机
首先，需要启动并登录到`red`虚拟机。在考试或练习环境中，通常可以通过虚拟机管理器或考试控制台界面进行操作。使用root账户和给定的密码（例如`redhat`）登录。

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_36.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_38.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_40.png)

### 2. 使用 `nmtui` 配置网络
`nmtui` (NetworkManager Text User Interface) 是一个基于文本的图形化工具，非常适合在命令行环境下配置网络。

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_42.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_44.png)

以下是使用 `nmtui` 配置网络的流程：
*   在虚拟机终端中输入命令 `nmtui` 并回车。
*   选择 **“Edit a connection”** 编辑连接。
*   选择唯一的以太网连接（通常是 `ens160` 或类似名称），回车进入编辑界面。
*   在 **IPv4 CONFIGURATION** 处，将方法从 “Automatic” 改为 **“Manual”**。
*   选择 **“Show”** 以显示详细配置区域。
*   在 **Addresses** 处，选择 **“Add”**，然后按题目要求输入IP地址和子网掩码（例如 `172.25.0.25/24`）。
*   在 **Gateway** 处输入默认网关（例如 `172.25.0.254`）。
*   在 **DNS servers** 处输入DNS服务器地址（例如 `172.25.0.254`）。
*   确保勾选 **“Automatically connect”** 和 **“Require IPv4 addressing for this connection”** 选项。
*   选择 **“OK”** 保存配置，然后返回主菜单。
*   在主菜单选择 **“Activate a connection”**，先停用（Deactivate）再重新激活（Activate）刚才修改的连接，使新配置生效。
*   返回主菜单，选择 **“Set system hostname”**，按题目要求设置主机名（例如 `red.net0.example.com`），选择 **“OK”**。
*   最后，选择 **“Quit”** 退出 `nmtui`。

### 3. 验证网络连通性
配置完成后，可以从考试环境的真机打开终端，使用SSH命令测试连接是否成功。命令格式如下：
```bash
ssh root@<虚拟机IP地址或主机名>
```
例如：
```bash
ssh root@172.25.0.25
```
如果能够成功连接并输入密码后登录，说明网络配置正确。

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_46.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_48.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_49.png)

## 网络管理相关知识补充

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_51.png)

为了让大家更好地理解和管理网络，这里补充一些红帽8系统中的网络管理知识。

### 网络管理服务
在RHEL 8中，负责管理网络的核心服务是 **NetworkManager**。传统的 `network` 服务在RHEL 8中已不再使用。

可以使用 `systemctl` 命令来管理此服务：
*   启动服务：`systemctl start NetworkManager`
*   停止服务：`systemctl stop NetworkManager`
*   设置开机自启：`systemctl enable NetworkManager`
*   查看状态：`systemctl status NetworkManager`

### 常用网络命令
以下是一些在配置和排查网络问题时常用的命令：

**查看IP地址：**
*   `ip address show` 或简写 `ip a`
*   `ifconfig` (需要安装 `net-tools` 软件包)

**查看和设置主机名：**
*   查看完整主机名信息：`hostnamectl status`
*   设置主机名并永久生效：`hostnamectl set-hostname <新主机名>`
*   主机名配置文件位于：`/etc/hostname`

**测试DNS解析：**
*   `host <域名>` (需要安装 `bind-utils` 软件包)

### 命令行配置工具 `nmcli`
`nmcli` 是 NetworkManager 的命令行客户端，适合在脚本或熟练后快速配置。以下是一些常用操作示例：

**查看网络设备与连接状态：**
```bash
nmcli device status
nmcli connection show
```

**修改一个现有连接的配置（例如连接名为`ens160`）：**
```bash
# 设置静态IP和网关
nmcli connection modify ens160 ipv4.method manual ipv4.addresses 172.25.0.25/24 ipv4.gateway 172.25.0.254
# 设置DNS服务器
nmcli connection modify ens160 ipv4.dns 172.25.0.254
# 设置连接自动激活
nmcli connection modify ens160 connection.autoconnect yes
# 重新激活连接使配置生效
nmcli connection up ens160
```

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_53.png)

## 总结

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_55.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_57.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_58.png)

![](img/36b013fe4f055c9e3911fcf0dcfd8c12_60.png)

本节课中我们一起学习了在RHCE考试环境中配置网络的基础操作。我们首先了解了考试环境的结构，然后通过 `nmtui` 工具逐步配置了主机名、静态IP地址、网关和DNS服务器，并验证了网络连通性。此外，我们还补充了关于 NetworkManager 服务、常用网络命令以及 `nmcli` 命令行工具的知识，这些将帮助大家更深入地理解Linux网络管理，并应对考试或工作中可能遇到的各种情况。掌握好网络配置是顺利完成后续所有RHCE考试任务的第一步。