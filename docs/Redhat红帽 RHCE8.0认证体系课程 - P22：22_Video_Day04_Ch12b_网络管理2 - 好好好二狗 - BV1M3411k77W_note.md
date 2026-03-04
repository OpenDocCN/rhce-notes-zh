# Redhat红帽 RHCE8.0认证体系课程：P22：网络管理2

## 概述
在本节课中，我们将学习如何在红帽企业版Linux 8系统中配置和管理网络。主要内容包括使用NetworkManager命令行工具（NMCLI）修改IP地址、路由和DNS，理解设备与连接的关系，以及直接编辑配置文件的方法。

![](img/e815072d7b963914fac3d6d55c9b83a0_1.png)

---

![](img/e815072d7b963914fac3d6d55c9b83a0_3.png)

## 设备与连接的概念
上一节我们介绍了网络查看的基本命令，本节中我们来看看如何配置网络信息。首先，需要理解NetworkManager中的两个核心概念：**设备**和**连接**。

![](img/e815072d7b963914fac3d6d55c9b83a0_5.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_7.png)

*   **设备**：指物理或虚拟的网络接口，例如 `eth0`、`ens224`。
*   **连接**：是为设备配置的一个设置集合（如IP地址、网关等）。连接名可以与设备名相同或不同。

![](img/e815072d7b963914fac3d6d55c9b83a0_9.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_11.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_13.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_15.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_17.png)

**关键点**：一个设备在同一时间只能有一个**活动**的连接，但可以存在多个连接配置以供切换。

![](img/e815072d7b963914fac3d6d55c9b83a0_19.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_21.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_22.png)

---

## 使用NMCLI管理网络连接
我们将使用 `nmcli` 命令来管理网络。其基本语法结构是 `nmcli connection`（可简写为 `con`）后跟具体操作。

### 查看设备与连接状态
在配置前，需要先查看当前系统的设备与连接状态。

以下是相关命令：
*   `nmcli device status`：查看所有网络设备的状态。
*   `nmcli connection show`：查看所有已配置的连接。
*   `ip addr show`：查看所有网络接口的IP信息。

### 为新设备添加并激活连接
当在系统中新增一块网卡（例如 `ens224`）后，设备会被识别，但初始状态是断开连接（disconnected）的。我们需要为其创建并激活一个连接。

激活连接的步骤如下：
1.  **确认设备存在**：使用 `ip addr show` 或 `nmcli device status` 确认新网卡（如 `ens224`）已被系统识别。
2.  **激活连接**：执行命令 `nmcli device connect ens224`。此操作会为设备生成一个默认的连接配置文件。
3.  **验证连接**：再次使用 `nmcli connection show`，可以看到一个以 `ens224` 命名的连接已处于活动状态。

### 修改连接的网络配置
连接激活后，我们可以修改其IP地址、子网掩码、网关和DNS等配置。

修改配置并应用的命令如下：
```bash
# 1. 修改IP地址和子网掩码，并设置获取方式为手动（static）
nmcli connection modify ens224 ipv4.addresses 192.168.146.128/24 ipv4.method manual

# 2. 修改网关和DNS
nmcli connection modify ens224 ipv4.gateway 192.168.146.1 ipv4.dns 8.8.8.8

# 3. 重新加载连接配置，使修改生效
nmcli connection reload

![](img/e815072d7b963914fac3d6d55c9b83a0_24.png)

# 4. 重新激活该连接（先关闭再开启）
nmcli connection down ens224
nmcli connection up ens224
```
**注意**：`ipv4.method manual` 表示使用静态IP，与之相对的是 `auto`（DHCP自动获取）。

![](img/e815072d7b963914fac3d6d55c9b83a0_26.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_27.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_28.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_30.png)

### 连接名与设备名的关系
连接名只是一个配置集的标识，可以独立于设备名。但为了便于管理，通常建议保持一致。

以下是修改连接名和将其改回的示例：
```bash
# 将连接 ‘ens224‘ 的名字改为 ‘test-connection‘
nmcli connection modify ens224 connection.id ‘test-connection‘
nmcli connection reload
nmcli connection up ‘test-connection‘

# 将连接名改回 ‘ens224‘
nmcli connection modify ‘test-connection‘ connection.id ens224
nmcli connection reload
nmcli connection up ens224
```

### 手动创建连接
除了让系统自动生成连接，也可以手动为设备创建连接配置文件。

手动创建连接的步骤如下：
1.  **添加连接**：使用 `nmcli connection add` 命令。
    ```bash
    nmcli connection add type ethernet con-name ens256 ifname ens256
    ```
    此命令创建了一个类型为以太网、连接名为 `ens256`、绑定设备 `ens256` 的新连接。
2.  **配置并激活**：之后便可使用 `nmcli connection modify` 命令配置该连接，并用 `up` 命令激活它。

---

![](img/e815072d7b963914fac3d6d55c9b83a0_32.png)

## 路由与多网卡配置
系统可以配置多个网卡和网关，但管理路由需要特别注意。

![](img/e815072d7b963914fac3d6d55c9b83a0_34.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_36.png)

### 配置多网卡与网关
可以为不同网卡配置不同网段的IP和网关。例如，为 `ens256` 配置一个内部网络地址：
```bash
nmcli connection modify ens256 ipv4.addresses 10.10.1.1/24 ipv4.gateway 10.10.1.2 ipv4.method manual
nmcli connection reload
nmcli connection up ens256
```

![](img/e815072d7b963914fac3d6d55c9b83a0_38.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_40.png)

### 理解路由表
使用 `ip route` 或 `route -n` 命令查看路由表。输出中会显示所有路由规则，包括默认网关和指向特定网络的路由。

![](img/e815072d7b963914fac3d6d55c9b83a0_42.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_44.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_46.png)

**路由优先级**：当存在多个可能的路由时，系统根据**跃点数**（metric）决定优先级，跃点数越小，路由优先级越高。默认路由（0.0.0.0/0）通常指向主网关。

![](img/e815072d7b963914fac3d6d55c9b83a0_48.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_50.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_52.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_54.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_56.png)

---

![](img/e815072d7b963914fac3d6d55c9b83a0_58.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_59.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_61.png)

## 直接编辑网络配置文件
除了使用 `nmcli`，红帽系统也支持直接编辑位于 `/etc/sysconfig/network-scripts/` 目录下的配置文件（如 `ifcfg-ens256`）。这种方法在红帽8中依然有效。

![](img/e815072d7b963914fac3d6d55c9b83a0_63.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_65.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_67.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_69.png)

### 配置文件详解
一个典型的静态IP配置文件内容如下：
```bash
# 网卡设备名，必须与文件名和实际设备对应
DEVICE=ens256
# 连接名，通常与DEVICE一致
NAME=ens256
# IP获取方式，static或none表示静态，dhcp表示动态
BOOTPROTO=static
# 开机是否自动启动
ONBOOT=yes
# IP地址和子网掩码（使用前缀表示法）
IPADDR=10.10.1.1
PREFIX=24
# 网关
GATEWAY=10.10.1.2
# DNS服务器，注意编号从1开始
DNS1=8.8.8.8
DNS2=114.114.114.114
```
**关键参数说明**：
*   `BOOTPROTO=static` 与 `BOOTPROTO=none` 效果相同，都表示静态配置。
*   `PREFIX=24` 也可以写作传统的 `NETMASK=255.255.255.0`。
*   `DNS` 参数从 `DNS1` 开始编号，系统通常只生效前两个配置。

![](img/e815072d7b963914fac3d6d55c9b83a0_71.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_73.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_74.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_76.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_78.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_79.png)

### 使配置文件生效
修改配置文件后，需要重新加载NetworkManager配置并重启连接：
```bash
nmcli connection reload
nmcli connection down ens256
nmcli connection up ens256
```

![](img/e815072d7b963914fac3d6d55c9b83a0_80.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_81.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_82.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_84.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_86.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_87.png)

---

![](img/e815072d7b963914fac3d6d55c9b83a0_89.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_91.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_93.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_94.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_96.png)

## DNS配置
DNS配置可以针对单个连接设置，也可以在系统全局配置。

![](img/e815072d7b963914fac3d6d55c9b83a0_98.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_99.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_101.png)

### 全局DNS配置
编辑 `/etc/resolv.conf` 文件可以设置全局DNS。但请注意，由NetworkManager管理的连接在激活时可能会覆盖此文件。更推荐的方法是在网卡配置文件或通过 `nmcli` 设置DNS。

![](img/e815072d7b963914fac3d6d55c9b83a0_103.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_105.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_106.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_108.png)

![](img/e815072d7b963914fac3d6d55c9b83a0_110.png)

**注意**：通常只需配置一至两个主备DNS服务器即可。

![](img/e815072d7b963914fac3d6d55c9b83a0_112.png)

---

![](img/e815072d7b963914fac3d6d55c9b83a0_114.png)

## 总结
本节课中我们一起学习了红帽8系统中的网络管理。
1.  我们理解了**设备**与**连接**的核心概念及其关系。
2.  掌握了使用 **`nmcli`** 命令行工具查看状态、激活设备、修改IP、网关、DNS等全套网络配置流程。
3.  学习了如何配置**多网卡**和**路由**，并查看路由表。
4.  介绍了直接编辑 **`/etc/sysconfig/network-scripts/ifcfg-*`** 配置文件的方法及其关键参数。
5.  说明了DNS的配置位置与方法。

通过本节学习，你应能使用命令行熟练地配置和管理红帽8系统的网络连接。