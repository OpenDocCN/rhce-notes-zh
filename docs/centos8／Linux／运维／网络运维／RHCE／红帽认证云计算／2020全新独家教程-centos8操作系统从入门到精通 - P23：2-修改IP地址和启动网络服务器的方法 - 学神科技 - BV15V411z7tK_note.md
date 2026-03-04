# CentOS 8 操作系统从入门到精通：P23：修改IP地址和启动网络服务器的方法 🖥️

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_1.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_2.png)

在本节课中，我们将学习如何在 CentOS 8 系统中修改主机名和配置网络。主要内容包括永久与临时修改主机名的方法，以及如何正确修改网卡IP地址并启动网络服务。掌握这些是进行系统管理和网络运维的基础。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_4.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_6.png)

## 修改主机名

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_8.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_10.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_12.png)

上一节我们介绍了系统的基本操作，本节中我们来看看如何修改主机名。修改主机名有两种方式：永久修改和临时修改。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_14.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_16.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_18.png)

*   **永久修改主机名**：使用命令 `hostnamectl set-hostname <新主机名>`。例如：
    ```bash
    hostnamectl set-hostname newhost.example.com
    ```
    修改后需要**重新打开一个终端**或**重启系统**才能使新的主机名在提示符中生效。

*   **临时修改主机名**：使用命令 `hostname <临时主机名>`。例如：
    ```bash
    hostname temp-host
    ```
    这种方式修改的主机名在系统重启后会失效。同样，已经打开的终端提示符不会立即变化，需要新开终端才能看到效果。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_20.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_22.png)

**注意**：`hostname` 命令不加参数时，用于查看当前的主机名。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_24.png)

## 修改网络IP地址

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_26.png)

成功修改主机名后，接下来我们学习如何配置网络。很多同学在修改完IP地址后遇到无法上网的问题，通常是因为配置错误或网络服务未正确重启。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_28.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_30.png)

### 1. 配置文件详解

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_32.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_33.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_35.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_36.png)

网络配置的核心文件是 `/etc/sysconfig/network-scripts/ifcfg-<网卡名>`，例如 `ifcfg-ens33`。我们可以使用 `vim` 编辑器打开它进行修改。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_38.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_39.png)

以下是配置文件中的关键参数及其含义：

*   `BOOTPROTO`：定义启动协议。
    *   `dhcp`：表示动态获取IP地址。
    *   `static` 或 `none`：表示使用静态IP地址。
*   `NAME` / `DEVICE`：网卡名称，必须与实际设备名（如 `ens33`）保持一致。
*   `ONBOOT`：是否在系统启动时激活此网卡，通常设为 `yes`。
*   `IPADDR`：静态IP地址。
*   `NETMASK` 或 `PREFIX`：子网掩码（如 `255.255.255.0`）或前缀长度（如 `24`）。
*   `GATEWAY`：默认网关地址。
*   `DNS1`, `DNS2`：主、备DNS服务器地址。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_41.png)

**重要提示**：配置静态IP时，如果同时设置了 `BOOTPROTO=dhcp` 和静态IP参数，系统可能会获取到两个IP地址，造成混乱。建议在配置静态IP前，先改为 `dhcp` 测试网络能否正常获取地址，以确认网段、网关等信息。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_43.png)

### 2. UUID 的作用

在配置文件中，你可能会看到 `UUID` 字段。UUID（通用唯一识别码）是硬件设备的全球唯一标识符。使用UUID而非设备名（如 `/dev/sda1`）来挂载磁盘或识别网卡有一个显著优点：它不会因为硬件接口更换或磁盘顺序调整而改变，从而提高了系统配置的稳定性和可靠性。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_45.png)

### 3. 应用配置并重启网络服务

在 CentOS 8 中，传统的 `service network restart` 命令已不再适用。需要使用 `NetworkManager` 的相关命令来重新加载配置并重启指定网卡。

以下是推荐的操作步骤：

1.  使用 `vim` 编辑并保存网卡配置文件。
2.  执行以下命令序列来应用更改：
    ```bash
    # 重新加载所有网络连接配置
    nmcli connection reload
    # 禁用指定网卡连接（如 ens33）
    nmcli connection down ens33
    # 启用指定网卡连接
    nmcli connection up ens33
    ```
    也可以将后两步合并，使用 `&&`（逻辑与）确保前一个命令成功后才执行下一个：
    ```bash
    nmcli connection down ens33 && nmcli connection up ens33
    ```
    这样做的好处是：只重启指定的网卡，不影响服务器上其他网络接口的服务。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_47.png)

3.  使用 `ifconfig ens33` 或 `ip addr show ens33` 命令验证IP地址是否已修改成功。
4.  使用 `ping` 命令测试网络连通性。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_49.png)

### 故障排查技巧

如果配置静态IP后无法上网，可以尝试以下步骤：

1.  先将 `BOOTPROTO` 改为 `dhcp`，并**注释掉或删除** `IPADDR`、`GATEWAY` 等静态配置行。
2.  重启网络服务，让系统自动获取IP地址，并记录下获取到的网段、网关和DNS信息。
3.  将这些信息作为参考，重新配置静态IP地址。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_51.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_53.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_54.png)

## 关闭自动锁屏（可选）

为了方便练习，你可能希望关闭系统的自动锁屏功能。

操作路径为：点击桌面右上角系统菜单 -> **Settings**（设置） -> **Power**（电源）。将 **Blank screen**（关闭屏幕）选项设置为 **Never**（从不）即可。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_56.png)

## 总结

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_58.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_60.png)

本节课中我们一起学习了 CentOS 8 下的主机名与网络配置管理。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_62.png)

*   我们掌握了使用 `hostnamectl` 永久修改主机名和使用 `hostname` 临时修改主机名的方法。
*   我们详细分析了网络配置文件 `/etc/sysconfig/network-scripts/ifcfg-ens33` 中各参数的作用，特别是 `BOOTPROTO`、`IPADDR`、`GATEWAY` 和 `DNS` 的配置。
*   我们学习了在 CentOS 8 中应用网络配置的正确命令流程：`nmcli connection reload` -> `down` -> `up`，这是与 CentOS 7 的主要区别之一。
*   最后，我们还了解了一个关闭自动锁屏以方便操作的小技巧。

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_64.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_65.png)

![](img/136c01f4a3bbcdad1c4c79070b9a1de6_66.png)

牢记配置后务必重启网络服务使其生效，并通过 `ping` 命令验证连通性，这是保证网络配置成功的关键。