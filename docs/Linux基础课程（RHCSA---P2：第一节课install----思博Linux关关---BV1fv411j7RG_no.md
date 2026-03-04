# Linux基础课程（RHCSA）：1：系统安装与基础配置

![](img/c6325deba6f1a0eafad2a88b35d17163_1.png)

在本节课中，我们将学习如何完成Linux系统的安装，并进行网络、防火墙和SELinux的基础配置，为后续的学习搭建好实验环境。

![](img/c6325deba6f1a0eafad2a88b35d17163_3.png)

---

![](img/c6325deba6f1a0eafad2a88b35d17163_5.png)

![](img/c6325deba6f1a0eafad2a88b35d17163_6.png)

## 系统安装完成与重启

![](img/c6325deba6f1a0eafad2a88b35d17163_8.png)

上一节我们介绍了系统安装的初始步骤。系统安装完成后，需要点击 **`Reboot`** 按钮重启系统。

![](img/c6325deba6f1a0eafad2a88b35d17163_10.png)

![](img/c6325deba6f1a0eafad2a88b35d17163_1.png)

重启过程中，系统会要求进行一些额外的配置。

## 首次启动配置

![](img/c6325deba6f1a0eafad2a88b35d17163_12.png)

![](img/c6325deba6f1a0eafad2a88b35d17163_14.png)

系统重启后，会进入首次启动配置界面。

![](img/c6325deba6f1a0eafad2a88b35d17163_16.png)

![](img/c6325deba6f1a0eafad2a88b35d17163_18.png)

![](img/c6325deba6f1a0eafad2a88b35d17163_3.png)

以下是需要完成的配置步骤：

1.  **接受许可协议**：点击接受相关授权协议。
2.  **完成配置**：点击 **`Finish Configuration`** 完成初始设置。

![](img/c6325deba6f1a0eafad2a88b35d17163_20.png)

![](img/c6325deba6f1a0eafad2a88b35d17163_22.png)

![](img/c6325deba6f1a0eafad2a88b35d17163_8.png)

配置完成后，系统将继续启动。

## 登录系统与Kdump设置

系统启动后，会进入图形登录界面。

![](img/c6325deba6f1a0eafad2a88b35d17163_24.png)

![](img/c6325deba6f1a0eafad2a88b35d17163_10.png)

在配置过程中，可能会遇到 **Kdump** 设置。Kdump用于在内核崩溃时收集调试信息。对于学习环境，通常不需要开启。

以下是操作步骤：
*   取消勾选 **`Enable kdump`**。
*   点击前进，并选择 **`No`** 跳过红帽账号注册。
*   系统会再次重启。

![](img/c6325deba6f1a0eafad2a88b35d17163_12.png)

## 以root用户登录

系统再次启动后，进入登录界面。

![](img/c6325deba6f1a0eafad2a88b35d17163_16.png)

默认列表中没有root用户。要使用root用户登录，请按以下步骤操作：
1.  点击登录框下方的 **`Not listed`**。
2.  手动输入用户名 **`root`**。
3.  输入安装时设置的root密码（例如：`123`）。

![](img/c6325deba6f1a0eafad2a88b35d17163_20.png)

登录成功后，即可进入系统桌面。

![](img/c6325deba6f1a0eafad2a88b35d17163_22.png)

## 配置网络（静态IP）

登录系统后，首要任务是配置网络，以便虚拟机可以与宿主机通信。

首先，我们需要确定网卡设备名称。打开终端，输入命令：

```bash
ifconfig
```

![](img/c6325deba6f1a0eafad2a88b35d17163_24.png)

输出中类似 **`eno16777736`** 或 **`ens33`** 的行就是你的网卡设备名（你的设备名可能不同）。

如果网卡没有IP地址，可以先通过DHCP获取一个临时IP：

```bash
dhclient eno16777736
```

再次运行 `ifconfig`，查看获取到的IP地址（例如 `192.168.31.145`）。

![](img/c6325deba6f1a0eafad2a88b35d17163_26.png)

接下来，我们将这个IP设置为静态IP。编辑网卡配置文件，文件路径通常为：

![](img/c6325deba6f1a0eafad2a88b35d17163_28.png)

```
/etc/sysconfig/network-scripts/ifcfg-<网卡名>
```

例如，对于网卡 `eno16777736`，配置文件是 `ifcfg-eno16777736`。

在图形界面下，可以使用 `gedit` 命令方便地编辑：

```bash
gedit /etc/sysconfig/network-scripts/ifcfg-eno16777736
```

在打开的文件中，找到 `BOOTPROTO` 行，并将其值改为 `static`。然后添加或修改以下几行：

```
BOOTPROTO=static
IPADDR=192.168.31.145
NETMASK=255.255.255.0
GATEWAY=192.168.31.1
ONBOOT=yes
```

请将 `IPADDR`、`NETMASK`、`GATEWAY` 的值替换为你网络环境中实际可用的地址。`ONBOOT=yes` 确保系统启动时自动启用该网卡。

保存并关闭文件。

## 重启网络服务

配置文件修改后，需要重启网络服务使配置生效。

![](img/c6325deba6f1a0eafad2a88b35d17163_30.png)

在 **RHEL/CentOS 7** 中，可以使用以下两种方式：

**方式一：重启所有网络服务**
```bash
service network restart
```

![](img/c6325deba6f1a0eafad2a88b35d17163_32.png)

**方式二：重启指定网卡（更精准）**
```bash
ifdown eno16777736 && ifup eno16777736
```

在 **RHEL/CentOS 8** 中，需使用 `nmcli` 命令：
```bash
nmcli connection down eno16777736 && nmcli connection up eno16777736
```

![](img/c6325deba6f1a0eafad2a88b35d17163_26.png)

![](img/c6325deba6f1a0eafad2a88b35d17163_34.png)

再次使用 `ifconfig` 或 `ip addr show` 命令检查IP是否已正确配置。

![](img/c6325deba6f1a0eafad2a88b35d17163_36.png)

![](img/c6325deba6f1a0eafad2a88b35d17163_37.png)

![](img/c6325deba6f1a0eafad2a88b35d17163_39.png)

## 关闭防火墙

为了方便初学阶段的实验，我们先临时关闭防火墙。

![](img/c6325deba6f1a0eafad2a88b35d17163_41.png)

1.  查看防火墙状态：
    ```bash
    systemctl status firewalld
    ```
    输出显示 `active (running)` 表示正在运行，`enabled` 表示开机自启。

2.  停止防火墙服务并禁用开机自启：
    ```bash
    systemctl stop firewalld
    systemctl disable firewalld
    ```

![](img/c6325deba6f1a0eafad2a88b35d17163_43.png)

3.  再次查看状态，确认变为 `inactive (dead)` 和 `disabled`。

![](img/c6325deba6f1a0eafad2a88b35d17163_45.png)

## 关闭SELinux

SELinux是Linux的安全模块，在初学阶段也可能造成困扰，我们先将其设置为宽松模式。

1.  查看当前SELinux状态：
    ```bash
    getenforce
    ```
    默认通常是 `Enforcing`（强制模式）。

![](img/c6325deba6f1a0eafad2a88b35d17163_47.png)

2.  编辑SELinux配置文件：
    ```bash
    gedit /etc/selinux/config
    ```

3.  找到 `SELINUX=` 这一行，将其值修改为 **`permissive`**。
    *   `enforcing`： 强制模式，违反策略会被阻止。
    *   `permissive`： 宽松模式，只记录警告但不阻止。
    *   `disabled`： 完全禁用。

    修改后保存文件。

![](img/c6325deba6f1a0eafad2a88b35d17163_30.png)

**注意**：修改SELinux配置后，需要**重启系统**才能生效。

## 系统重启与验证

使用以下任一命令重启系统：

![](img/c6325deba6f1a0eafad2a88b35d17163_49.png)

```bash
reboot
# 或
init 6
# 或
shutdown -r now
```

![](img/c6325deba6f1a0eafad2a88b35d17163_32.png)

系统重启后，再次以root用户登录。

![](img/c6325deba6f1a0eafad2a88b35d17163_51.png)

现在，可以从宿主机（你的Windows或Mac电脑）上，使用 `ping` 命令测试虚拟机的网络连通性：

```
ping 192.168.31.145
```

![](img/c6325deba6f1a0eafad2a88b35d17163_41.png)

![](img/c6325deba6f1a0eafad2a88b35d17163_53.png)

如果收到回复，说明网络配置成功。

## 图形化配置网络（备选方案）

如果你觉得编辑配置文件比较复杂，也可以使用图形界面配置网络。

1.  点击桌面右上角的网络图标。
2.  选择 **`Wired Settings`** 或 **`网络设置`**。
3.  点击齿轮图标进入设置。
4.  在 **`IPv4`** 选项卡中，将方法改为 **`Manual`（手动）**。
5.  填写地址、子网掩码、网关和DNS。
6.  点击应用。

## 安装其他版本（RHEL8）的差异

安装 **RHEL 8** 或 **CentOS 8** 的过程与7类似，主要注意两点不同：

![](img/c6325deba6f1a0eafad2a88b35d17163_55.png)

1.  **安装界面**：步骤更集成，重启次数可能更少。
2.  **网络重启命令**：必须使用 `nmcli` 命令，而非 `service network restart`。
    ```bash
    nmcli connection down <网卡名> && nmcli connection up <网卡名>
    ```

![](img/c6325deba6f1a0eafad2a88b35d17163_56.png)

![](img/c6325deba6f1a0eafad2a88b35d17163_47.png)

## 网络不通的排查思路

如果配置后网络无法连通，请按以下顺序排查：

1.  **确认网络模式与IP匹配**：确保虚拟机网卡模式（桥接、NAT、仅主机）与配置的IP地址段匹配。
2.  **检查配置文件**：确认配置文件中IP信息无误且已保存。
3.  **检查IP冲突**：确认配置的静态IP在当前网络中未被其他设备占用。
4.  **确认服务重启**：确保已执行网络重启命令，且无报错。
5.  **使用`dhclient`测试**：可先运行 `dhclient <网卡名>` 看是否能自动获取到IP，以验证底层网络是否正常。

---

本节课中我们一起学习了Linux系统的完整安装流程，以及安装后至关重要的网络配置、防火墙和SELinux设置。这是所有后续学习的基础，请务必完成一个带图形界面的RHEL/CentOS 7系统的安装与配置。

**今日作业**：在虚拟机中成功安装一个带图形界面的RHEL/CentOS 7系统，并完成上述所有配置，确保宿主机可以 `ping` 通虚拟机。学有余力者可尝试安装RHEL/CentOS 8系统。

![](img/c6325deba6f1a0eafad2a88b35d17163_58.png)

![](img/c6325deba6f1a0eafad2a88b35d17163_58.png)