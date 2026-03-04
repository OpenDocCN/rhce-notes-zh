# Redhat红帽 RHCE8.0认证体系课程：P23：链路聚合与IPv6配置

![](img/535166570127dfefac2801fc55015a5f_1.png)

![](img/535166570127dfefac2801fc55015a5f_3.png)

![](img/535166570127dfefac2801fc55015a5f_5.png)

在本节课中，我们将要学习网络管理的最后两个重要知识点：IPv6地址的配置和链路聚合技术。链路聚合能够提升网络带宽和可靠性，而IPv6则是现代网络的基础。我们将通过简单的命令和配置文件修改来掌握这些技能。

![](img/535166570127dfefac2801fc55015a5f_7.png)

![](img/535166570127dfefac2801fc55015a5f_9.png)

## IPv6配置补充 📝

![](img/535166570127dfefac2801fc55015a5f_11.png)

![](img/535166570127dfefac2801fc55015a5f_13.png)

![](img/535166570127dfefac2801fc55015a5f_15.png)

![](img/535166570127dfefac2801fc55015a5f_17.png)

上一节我们介绍了IPv4的网络配置，本节中我们来看看IPv6的配置方法。IPv6地址长度为128位，配置过程与IPv4类似，但有一些特定的参数。

![](img/535166570127dfefac2801fc55015a5f_19.png)

IPv6地址通常由冒号分隔的8组十六进制数表示，每组16位。例如：`2001:0db8:85a3:0000:0000:8a2e:0370:7334`。在配置文件中，我们可以进行简写。

![](img/535166570127dfefac2801fc55015a5f_21.png)

配置IPv6地址的核心步骤如下，主要涉及修改网络接口配置文件：

![](img/535166570127dfefac2801fc55015a5f_23.png)

1.  禁用IPv6的自动配置功能。
2.  在配置文件中手动添加静态IPv6地址。

![](img/535166570127dfefac2801fc55015a5f_25.png)

![](img/535166570127dfefac2801fc55015a5f_26.png)

![](img/535166570127dfefac2801fc55015a5f_28.png)

![](img/535166570127dfefac2801fc55015a5f_30.png)

![](img/535166570127dfefac2801fc55015a5f_31.png)

以下是配置示例，假设我们要为网卡配置地址 `2020::1/64`：
```bash
# 编辑网卡配置文件，例如 ifcfg-ens192
IPV6_AUTOCONF=no
IPV6ADDR=2020::1/64
```
配置完成后，需要重启网络服务或重新激活网卡以使配置生效。

![](img/535166570127dfefac2801fc55015a5f_32.png)

测试IPv6连通性的命令是 `ping6`，其用法与 `ping` 类似：
```bash
ping6 2020::2
```
请注意，通信双方都需要正确配置IPv6地址。

## 链路聚合技术 🔗

在了解了基础的IP地址配置后，我们进入提升网络可靠性与性能的环节——链路聚合。链路聚合能将多个物理网络端口捆绑成一个逻辑端口，以实现带宽叠加或故障冗余。

### 链路聚合核心概念

链路聚合主要有两种实现方式：**team** 和 **bond**。本课程以 **team** 为例进行讲解。Team支持多种运行模式，以下是三种常见模式：
*   **activebackup（主备模式）**：同一时间只有一个网卡活跃，提供故障冗余。
*   **loadbalance（负载均衡模式）**：多个网卡同时工作，均衡流量，提升带宽。
*   **roundrobin（轮询模式）**：在多个网卡间轮流转发数据包。

![](img/535166570127dfefac2801fc55015a5f_34.png)

### 配置Team链路聚合（主备模式）

![](img/535166570127dfefac2801fc55015a5f_36.png)

![](img/535166570127dfefac2801fc55015a5f_38.png)

![](img/535166570127dfefac2801fc55015a5f_40.png)

我们将使用两台物理网卡（例如 `ens161` 和 `ens193`）创建一个名为 `team0` 的逻辑聚合端口。

![](img/535166570127dfefac2801fc55015a5f_42.png)

首先，确保系统已安装 `teamd` 服务包。然后，按顺序执行以下命令：

![](img/535166570127dfefac2801fc55015a5f_44.png)

**第一步：创建Team逻辑接口**
此命令创建了一个运行在 `activebackup` 模式下的Team接口框架。
```bash
nmcli connection add type team con-name team0 ifname team0 config '{"runner": {"name": "activebackup"}}'
```

![](img/535166570127dfefac2801fc55015a5f_46.png)

![](img/535166570127dfefac2801fc55015a5f_48.png)

![](img/535166570127dfefac2801fc55015a5f_50.png)

**第二步：将物理网卡添加为Team成员**
以下命令将两块物理网卡设置为 `team0` 的从属接口。
```bash
nmcli connection add type team-slave con-name team0-port1 ifname ens161 master team0
nmcli connection add type team-slave con-name team0-port2 ifname ens193 master team0
```
**注意**：物理网卡本身不需要配置IP地址。

![](img/535166570127dfefac2801fc55015a5f_52.png)

![](img/535166570127dfefac2801fc55015a5f_54.png)

**第三步：为Team接口配置IP地址**
现在为逻辑接口 `team0` 配置IP地址。
```bash
nmcli connection modify team0 ipv4.method manual ipv4.addresses 192.168.145.200/24
```

![](img/535166570127dfefac2801fc55015a5f_56.png)

![](img/535166570127dfefac2801fc55015a5f_58.png)

**第四步：激活所有连接**
最后，启动所有相关的连接。
```bash
nmcli connection up team0
nmcli connection up team0-port1
nmcli connection up team0-port2
```

![](img/535166570127dfefac2801fc55015a5f_60.png)

![](img/535166570127dfefac2801fc55015a5f_61.png)

![](img/535166570127dfefac2801fc55015a5f_62.png)

![](img/535166570127dfefac2801fc55015a5f_63.png)

![](img/535166570127dfefac2801fc55015a5f_64.png)

### 验证与测试

![](img/535166570127dfefac2801fc55015a5f_66.png)

![](img/535166570127dfefac2801fc55015a5f_68.png)

![](img/535166570127dfefac2801fc55015a5f_70.png)

![](img/535166570127dfefac2801fc55015a5f_72.png)

配置完成后，可以进行以下验证：

![](img/535166570127dfefac2801fc55015a5f_74.png)

1.  **检查Team状态**：使用 `teamdctl` 命令查看 `team0` 的状态和当前活跃端口。
    ```bash
    teamdctl team0 state
    ```
2.  **测试连通性**：从网络内的其他机器 `ping` Team接口的IP地址（如 `192.168.145.200`），应能成功通信。
3.  **测试故障切换**：
    *   手动断开当前活跃的物理网卡（例如 `sudo nmcli connection down team0-port1`）。
    *   观察 `teamdctl team0 state` 的输出，确认活跃端口已切换到另一块网卡（如 `ens193`）。
    *   此时，持续的 `ping` 测试可能会丢失少量数据包，但连接会迅速恢复，证明冗余生效。
    *   重新启用之前断开的网卡，观察状态恢复。

### 通过配置文件快速配置

![](img/535166570127dfefac2801fc55015a5f_76.png)

![](img/535166570127dfefac2801fc55015a5f_77.png)

![](img/535166570127dfefac2801fc55015a5f_79.png)

对于不熟悉命令的操作者，Red Hat系统提供了配置示例文件，可以直接修改使用。示例文件路径通常为：`/usr/share/doc/teamd/example_configs/`。您可以选择对应的模式配置文件（如 `activebackup.conf`）复制并修改，然后将其应用到Team接口的配置中。

![](img/535166570127dfefac2801fc55015a5f_81.png)

## 总结

![](img/535166570127dfefac2801fc55015a5f_83.png)

![](img/535166570127dfefac2801fc55015a5f_85.png)

本节课中我们一起学习了两个重要的网络进阶主题。
首先，我们补充了**IPv6**的静态地址配置方法，包括修改配置文件和使用 `ping6` 命令进行测试。
接着，我们深入探讨了**链路聚合**技术，重点演示了如何使用 `team` 和 `nmcli` 命令配置 **activebackup（主备）** 模式的聚合链路，以实现网络接口的故障冗余，并学会了如何验证其故障切换功能。
掌握这些技能，将有助于你构建更健壮、更高效的网络环境。