# Linux网络管理：1：网络管理概述与RHEL8新特性

在本节课中，我们将要学习Linux系统中的网络管理，特别是在RHEL8版本中引入的重要变化。网络是系统运行的基础，掌握其配置方法至关重要。

## RHEL8网络管理的主要变动

RHEL8在网络管理层面进行了重大改动，主要体现在以下两个方面。

### 前端配置工具的变动

在RHEL8中，官方推荐使用 `nmcli` 命令行工具进行网络配置，取代了旧版本中直接编辑配置文件（如 `/etc/sysconfig/network-scripts/` 下的文件）的方式。虽然旧方法仍然可用，但使用 `nmcli` 是红帽官方推荐的做法。

### 网络接口命名规则的变动

![](img/74aec29a26ad0e819a0f8494aa24550a_1.png)

旧版本Linux（如RHEL7）的网络接口通常命名为 `eth0`、`eth1` 等。RHEL8采用了新的、基于固件信息的命名规则，使得接口名称更长且更具描述性。

![](img/74aec29a26ad0e819a0f8494aa24550a_3.png)

新的命名规则基于以下信息组合：
*   **固件信息**
*   **PCI总线位置**
*   **网络设备类型**

![](img/74aec29a26ad0e819a0f8494aa24550a_5.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_7.png)

以下是常见的命名前缀及其含义：
*   **以太网接口** 以 `en` 开头。
*   **无线网卡接口** 以 `wl` 开头。
*   **广域网接口** 以 `ww` 开头。

接口名称示例解析：
*   **`eno1`**：`en` 代表以太网，`o1` 代表板载（onboard）设备编号1。因此，`eno1` 表示**板载的1号以太网接口**。
*   **`ens3`**：`en` 代表以太网，`s3` 代表PCI热插拔插槽（slot）编号3。因此，`ens3` 表示**PCI插槽3中的以太网接口**。
*   **`wlp4s0`**：`wl` 代表无线局域网，`p4` 代表PCI总线4，`s0` 代表插槽0。因此，`wlp4s0` 表示**插槽0中、PCI总线4上的无线网卡接口**。
*   **`enp0s1f0`**：`en` 代表以太网，`p0` 代表PCI域0，`s1` 代表插槽1，`f0` 代表功能（function）0。因此，`enp0s1f0` 表示**PCI域0、插槽1、功能0的以太网接口**。

![](img/74aec29a26ad0e819a0f8494aa24550a_9.png)

可以使用 `ip address` 命令查看系统当前的网络接口及其新式命名。

![](img/74aec29a26ad0e819a0f8494aa24550a_11.png)

---

上一节我们介绍了RHEL8网络管理的新特性，本节中我们来看看具体的网络配置方法。

![](img/74aec29a26ad0e819a0f8494aa24550a_13.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_15.png)

## 网络配置方法

![](img/74aec29a26ad0e819a0f8494aa24550a_17.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_19.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_21.png)

RHEL8提供了多种网络配置方式，主要分为命令行和图形化界面两种。

![](img/74aec29a26ad0e819a0f8494aa24550a_23.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_24.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_26.png)

### 图形化界面配置：`nmtui`

![](img/74aec29a26ad0e819a0f8494aa24550a_28.png)

对于不熟悉命令的用户，可以使用 `nmtui` 工具。这是一个基于终端的图形化界面，无需额外安装软件。

![](img/74aec29a26ad0e819a0f8494aa24550a_30.png)

使用方法非常简单，在终端直接输入命令 `nmtui` 即可启动。在界面中，你可以通过方向键选择“编辑一个连接”，然后配置IP地址、子网掩码、网关和DNS等信息。

![](img/74aec29a26ad0e819a0f8494aa24550a_32.png)

### 命令行配置：`nmcli`

![](img/74aec29a26ad0e819a0f8494aa24550a_34.png)

`nmcli` 是功能强大的命令行工具，但命令较长。我们可以通过查看帮助文档来学习常用命令。

![](img/74aec29a26ad0e819a0f8494aa24550a_36.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_38.png)

查看 `nmcli` 配置示例的命令是：
```bash
man nmcli | grep -A 20 -B 5 "example"
```
在帮助文档的 `example 7` 和 `example 9` 部分，可以找到添加和修改网络连接的典型命令模板。

以下是使用 `nmcli` 管理网络连接的核心步骤和命令：

**1. 查看网络连接信息**
*   查看所有连接的概要信息：`nmcli connection`
*   查看指定网卡（如 `ens192`）的详细信息：`nmcli connection show ens192`

![](img/74aec29a26ad0e819a0f8494aa24550a_40.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_42.png)

**2. 删除现有网络配置**
如果需要重新配置某块网卡，可以先删除其现有配置（此操作会断开连接）：
```bash
nmcli connection delete ens192
```
此命令仅删除配置，不会移除物理网卡。

**3. 添加新的网络连接**
根据帮助文档中的示例，添加一个以太网连接：
```bash
nmcli connection add type ethernet ifname ens192 con-name my-ens192
```
*   `ifname ens192`：指定设备名，必须与实际接口名（如 `ens192`）一致。
*   `con-name my-ens192`：为此连接配置指定一个名称（如 `my-ens192`），便于管理。

![](img/74aec29a26ad0e819a0f8494aa24550a_44.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_46.png)

**4. 修改网络连接参数**
使用 `modify` 子命令配置IP地址、网关和DNS：
```bash
nmcli connection modify my-ens192 ipv4.addresses 192.168.1.100/24 ipv4.gateway 192.168.1.1 ipv4.dns 8.8.8.8 ipv4.method manual
```
*   `ipv4.addresses 192.168.1.100/24`：设置静态IP地址和子网掩码。
*   `ipv4.gateway 192.168.1.1`：设置默认网关。
*   `ipv4.dns 8.8.8.8`：设置DNS服务器。
*   `ipv4.method manual`：设置为手动配置（静态IP）。若需DHCP，则设为 `auto`。

**5. 重启网络连接使配置生效**
修改配置后，需要重新加载连接：
```bash
nmcli connection reload
```
然后激活（启动）该连接：
```bash
nmcli connection up my-ens192
```

![](img/74aec29a26ad0e819a0f8494aa24550a_48.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_50.png)

**6. 验证配置**
配置完成后，可以使用以下命令验证：
*   `ip address show ens192` 或 `ip a s ens192`：查看接口IP信息。
*   `ping 8.8.8.8`：测试网络连通性。
*   `nmcli connection show my-ens192`：查看该连接的详细配置。

![](img/74aec29a26ad0e819a0f8494aa24550a_52.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_54.png)

---

![](img/74aec29a26ad0e819a0f8494aa24550a_56.png)

上一节我们学习了使用 `nmcli` 配置网络，本节中我们来看看其他相关的网络配置文件和工具。

![](img/74aec29a26ad0e819a0f8494aa24550a_58.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_60.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_62.png)

## 相关配置文件与工具

![](img/74aec29a26ad0e819a0f8494aa24550a_64.png)

除了 `nmcli`，了解一些关键的配置文件和辅助命令也很有帮助。

![](img/74aec29a26ad0e819a0f8494aa24550a_66.png)

### 重要的网络配置文件

![](img/74aec29a26ad0e819a0f8494aa24550a_68.png)

*   **DNS客户端配置**：`/etc/resolv.conf`
    该文件用于配置系统的DNS服务器。虽然可以通过 `nmcli` 的 `ipv4.dns` 参数设置，但直接查看此文件可以确认当前的DNS配置。

![](img/74aec29a26ad0e819a0f8494aa24550a_70.png)

*   **本地主机名解析**：`/etc/hosts`
    此文件用于本地的主机名到IP地址的映射，其解析优先级高于DNS服务器。例如，可以在其中添加 `192.168.1.10 server-a`，这样就能通过 `server-a` 这个主机名访问该IP。

![](img/74aec29a26ad0e819a0f8494aa24550a_72.png)

*   **传统网络配置文件（不推荐直接修改）**：`/etc/sysconfig/network-scripts/ifcfg-<接口名>`
    这是旧版本中使用的网卡配置文件。在RHEL8中，此文件由 `nmcli` 工具自动生成和管理，红帽官方不建议直接手动编辑，以免与 `nmcli` 的管理产生冲突。

![](img/74aec29a26ad0e819a0f8494aa24550a_74.png)

### 其他常用命令

![](img/74aec29a26ad0e819a0f8494aa24550a_76.png)

*   **查看及修改主机名**：
    *   查看详细系统信息（包括主机名）：`hostnamectl`
    *   修改主机名（如改为 `new-server`）：`hostnamectl set-hostname new-server`
    *   简单的查看主机名命令：`hostname` 或 `uname -n`

*   **网络诊断常用命令**：
    *   `ip address` 或 `ip a`：查看IP地址信息（推荐）。
    *   `ip link`：查看网络链路层状态。
    *   `ifconfig`：查看网络接口配置（旧命令，部分新系统可能未默认安装）。

![](img/74aec29a26ad0e819a0f8494aa24550a_78.png)

---

![](img/74aec29a26ad0e819a0f8494aa24550a_80.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_82.png)

## Web图形化管理界面：Cockpit

RHEL8引入了Cockpit，这是一个基于Web的图形化服务器管理工具。通过它，可以在浏览器中完成许多系统管理任务，包括网络配置。

![](img/74aec29a26ad0e819a0f8494aa24550a_84.png)

**安装与启用Cockpit：**
1.  安装软件包：`yum install cockpit -y`
2.  启用并立即启动服务：`systemctl enable --now cockpit.socket`

![](img/74aec29a26ad0e819a0f8494aa24550a_86.png)

![](img/74aec29a26ad0e819a0f8494aa24550a_88.png)

**访问Cockpit：**
安装启用后，在浏览器中访问 `https://<你的服务器IP>:9090`，使用系统用户（如root）登录即可。在Cockpit的“网络”模块，可以直观地配置网络接口。

![](img/74aec29a26ad0e819a0f8494aa24550a_90.png)

**关于图形化界面的建议：**
Cockpit等图形化工具极大简化了初级运维人员对系统的监控和基础操作。然而，对于高级运维和复杂故障排查，命令行工具（`nmcli`等）更为高效和强大。建议初学者在理解原理的基础上，逐步掌握命令行操作。

---

![](img/74aec29a26ad0e819a0f8494aa24550a_92.png)

## 总结

![](img/74aec29a26ad0e819a0f8494aa24550a_94.png)

本节课中我们一起学习了RHEL8下的网络管理知识。我们首先了解了RHEL8在网络接口命名和配置工具上的重要变化。然后，重点掌握了使用 `nmcli` 命令行工具进行网络连接查看、添加、修改和删除的全流程。此外，我们还认识了 `/etc/resolv.conf`、`/etc/hosts` 等关键配置文件，以及 `hostnamectl`、`ip` 等辅助命令。最后，我们了解了Cockpit这一Web图形化管理工具。掌握这些内容，是进行Linux系统网络配置和故障排除的基础。