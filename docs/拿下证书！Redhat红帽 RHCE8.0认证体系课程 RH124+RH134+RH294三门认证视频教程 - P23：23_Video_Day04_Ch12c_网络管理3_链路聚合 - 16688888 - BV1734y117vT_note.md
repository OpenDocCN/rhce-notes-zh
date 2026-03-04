# 网络管理：04：链路聚合与IPv6补充

![](img/12232fe0455834dafd6a92226aac0c84_0.png)

![](img/12232fe0455834dafd6a92226aac0c84_2.png)

在本节课中，我们将学习网络管理的最后两个重要知识点：IPv6的简单配置和链路聚合的实现。链路聚合能够提升网络带宽和可靠性，而IPv6则是现代网络的基础。我们将通过实际操作来掌握这些技能。

![](img/12232fe0455834dafd6a92226aac0c84_4.png)

![](img/12232fe0455834dafd6a92226aac0c84_6.png)

## IPv6配置补充

![](img/12232fe0455834dafd6a92226aac0c84_8.png)

![](img/12232fe0455834dafd6a92226aac0c84_10.png)

![](img/12232fe0455834dafd6a92226aac0c84_12.png)

上一节我们介绍了IPv4的网络配置，本节中我们来看看IPv6的简单配置方法。IPv6地址长度为128位，通常由冒号分隔的8组十六进制数表示。

![](img/12232fe0455834dafd6a92226aac0c84_14.png)

**核心配置步骤**如下：

1.  在网卡配置文件中，关闭IPv6的自动配置功能。
    ```bash
    IPV6_AUTOCONF=no
    ```
2.  手动指定一个IPv6地址。
    ```bash
    IPV6ADDR=2020::1/64
    ```
    上述地址中，`2020::1`是地址，`/64`表示前64位是网络位。

![](img/12232fe0455834dafd6a92226aac0c84_16.png)

![](img/12232fe0455834dafd6a92226aac0c84_18.png)

![](img/12232fe0455834dafd6a92226aac0c84_19.png)

配置完成后，可以使用 `ping6` 命令测试IPv6网络的连通性，前提是通信双方都配置了IPv6地址。
```bash
ping6 2020::2
```

## 链路聚合（Team）配置

接下来，我们进入本节课的核心内容：链路聚合。链路聚合能将多个物理网络端口捆绑成一个逻辑端口，以实现带宽叠加或链路冗余，从而提高网络吞吐量和可靠性。

### 概念与模式

常见的链路聚合模式有以下几种：
*   **负载均衡 (loadbalance)**：所有网卡同时工作，总带宽叠加。
*   **轮询 (roundrobin)**：数据包依次通过各成员网卡发送。
*   **主备 (activebackup)**：只有一个网卡处于活动状态，其余作为备份。当活动网卡故障时，自动切换到备份网卡，保证网络不中断。

本节我们将以 **主备模式** 为例进行配置。

![](img/12232fe0455834dafd6a92226aac0c84_21.png)

### 实践：配置主备模式链路聚合

![](img/12232fe0455834dafd6a92226aac0c84_23.png)

![](img/12232fe0455834dafd6a92226aac0c84_25.png)

以下是配置一个名为 `team0` 的聚合接口的完整步骤，我们假设使用 `ens161` 和 `ens193` 两块网卡。

![](img/12232fe0455834dafd6a92226aac0c84_27.png)

**第一步：创建Team逻辑接口**
使用 `nmcli` 命令创建一个类型为 `team` 的逻辑接口，并设置其运行模式为 `activebackup`。
```bash
nmcli connection add type team con-name team0 ifname team0 config '{"runner": {"name": "activebackup"}}'
```

**第二步：将物理网卡添加为Team成员**
将两块物理网卡作为“奴隶”接口加入到上一步创建的 `team0` 中。
```bash
nmcli connection add type team-slave con-name team0-port1 ifname ens161 master team0
nmcli connection add type team-slave con-name team0-port2 ifname ens193 master team0
```
**请注意**：物理网卡本身不需要配置IP地址。

![](img/12232fe0455834dafd6a92226aac0c84_29.png)

![](img/12232fe0455834dafd6a92226aac0c84_31.png)

![](img/12232fe0455834dafd6a92226aac0c84_33.png)

![](img/12232fe0455834dafd6a92226aac0c84_35.png)

**第三步：为Team接口配置IP地址**
现在为逻辑接口 `team0` 配置IP地址，网络服务将通过这个接口地址进行通信。
```bash
nmcli connection modify team0 ipv4.method manual ipv4.addresses 192.168.145.200/24
```

**第四步：启动所有连接**
重新加载连接配置并启动它们。
```bash
nmcli connection reload
nmcli connection up team0
nmcli connection up team0-port1
nmcli connection up team0-port2
```

![](img/12232fe0455834dafd6a92226aac0c84_37.png)

![](img/12232fe0455834dafd6a92226aac0c84_38.png)

![](img/12232fe0455834dafd6a92226aac0c84_39.png)

**第五步：验证与测试**
1.  使用 `ip addr show team0` 检查Team接口是否已启动并获取了IP地址。
2.  使用 `teamdctl team0 state` 命令查看聚合组状态，可以看到当前活动的端口。
3.  进行故障转移测试：手动断开当前活动网卡（如 `nmcli device disconnect ens161`），观察 `teamdctl` 状态是否自动切换到备份网卡，并使用 `ping` 命令测试网络连通性是否仅在瞬间丢包后即恢复。

![](img/12232fe0455834dafd6a92226aac0c84_41.png)

![](img/12232fe0455834dafd6a92226aac0c84_43.png)

![](img/12232fe0455834dafd6a92226aac0c84_45.png)

![](img/12232fe0455834dafd6a92226aac0c84_47.png)

### 简化配置方法

对于不熟悉命令的同学，Red Hat 系统提供了配置示例文件，位于 `/usr/share/doc/teamd/example_configs/` 目录下。你可以直接复制对应的模式配置文件（如 `activebackup.conf`）到 `/etc/teamd/` 目录，并修改其中的接口名称，然后通过 `teamd` 服务来启动聚合，这是一种更直观的配置方式。

## 总结

本节课中我们一起学习了两个重要的网络进阶主题。
1.  **IPv6配置**：我们了解了如何通过修改配置文件来禁用自动配置并设置静态IPv6地址，以及使用 `ping6` 进行测试。
2.  **链路聚合**：我们深入探讨了链路聚合的作用，并以“主备模式”为例，完整演示了使用 `nmcli` 命令创建和配置Team聚合接口的过程。通过实际故障切换测试，我们验证了其提供的网络冗余能力。

![](img/12232fe0455834dafd6a92226aac0c84_49.png)

掌握链路聚合技术，能让你在实际工作中有效提升服务器网络连接的带宽和可靠性。