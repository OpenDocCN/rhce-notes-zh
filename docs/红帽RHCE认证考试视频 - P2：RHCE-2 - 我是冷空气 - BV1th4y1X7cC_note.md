# 红帽RHCE认证考试视频：P2：RHCE-2

在本节课中，我们将要学习IPv6地址的基础知识、配置方法，以及网络接口聚合（Team）和软件网桥（Bridge）的配置。这些是RHCE考试中的核心网络技能。

## 什么是IPv6？ 🤔

上一节我们介绍了课程概述，本节中我们来看看什么是IPv6。

IPv6是互联网协议（IP）的第六版。它出现的主要原因是IPv4地址的枯竭。IPv4地址由32位二进制数组成，最多可提供约43亿个地址（2^32）。随着全球联网设备数量的激增，IPv4地址已不敷使用。

IPv6地址由128位二进制数组成，其地址空间极其庞大（2^128）。形象地说，IPv6可以为地球上的每一粒沙子分配一个独立的IP地址。

## IPv6地址的组成与表示 🔢

了解了IPv6出现的原因后，本节我们来学习它的地址结构和表示方法。

![](img/7acee2ee9eb9bdb37717e8763854e094_1.png)

IPv6地址由128位二进制数组成。为了便于书写，通常每16位（即4个十六进制数）分为一组，共8组，组间用冒号（`:`）分隔。例如：
`2001:0db8:0000:0000:0000:ff00:0042:8329`

![](img/7acee2ee9eb9bdb37717e8763854e094_3.png)

为了进一步简化，IPv6地址支持两种简写规则：
1.  **省略每组中的前导零**：例如，`2001:0db8:0000:0000...` 可以简写为 `2001:db8:0:0...`。
2.  **用双冒号`::`替换连续的一组或多组零**：但一个地址中**只能使用一次**双冒号。例如，`2001:db8:0:0:0:0:2:1` 可以简写为 `2001:db8::2:1`。

IPv6地址的标准子网掩码是 `/64`，这意味着前64位是网络位，后64位是主机位。

## IPv6地址配置 🛠️

掌握了IPv6地址的表示方法后，本节我们进入核心实践环节：如何为网络接口配置IPv6地址。

在RHCE考试中，为网络接口配置IPv6地址是必考内容。配置方式主要分为静态（手动）配置和动态获取（如DHCPv6、SLAAC）。我们重点学习静态配置。

![](img/7acee2ee9eb9bdb37717e8763854e094_5.png)

配置命令主要使用 `nmcli`。以下是关键步骤和命令：

![](img/7acee2ee9eb9bdb37717e8763854e094_7.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_8.png)

**1. 查看现有连接**
```bash
nmcli connection show
```

**2. 为指定接口（如 `eno1`）添加IPv6地址和网关**
```bash
nmcli connection modify eno1 ipv6.addresses "fd00::1/64"
nmcli connection modify eno1 ipv6.gateway "fd00::fe"
nmcli connection modify eno1 ipv6.method manual
```
*   `ipv6.addresses`: 设置IPv6地址和前缀。
*   `ipv6.gateway`: 设置IPv6默认网关。
*   `ipv6.method manual`: 将地址获取方式设置为手动。

**3. 重新激活连接以使配置生效**
```bash
nmcli connection down eno1
nmcli connection up eno1
```

**4. 验证配置**
```bash
ip -6 addr show eno1        # 查看IPv6地址
ip -6 route show           # 查看IPv6路由表
ping6 fd00::fe             # 测试IPv6连通性
```

除了使用 `nmcli` 命令，也可以直接编辑网络配置文件（位于 `/etc/sysconfig/network-scripts/` 目录下，文件名如 `ifcfg-eno1`）进行配置。

## 网络接口聚合（Team）介绍 🤝

在配置好基础网络后，我们有时需要更高的可靠性或带宽。本节介绍网络接口聚合（Team）。

网络接口聚合（Teaming）是一种将多个物理网络接口卡（NIC）逻辑上捆绑在一起的技术。其主要目的有两个：
*   **故障转移（Fault Tolerance）**：当主接口故障时，流量自动切换到备用接口，保证网络不中断。
*   **负载均衡（Load Balancing）**：在多条链路上分发流量，提高总体吞吐量。

常见的聚合模式（Runner）有：
*   **`activebackup`**：主备模式。只有一个接口处于活动状态，其余作为备份。
*   **`roundrobin`**：轮询模式。在所有活动接口间轮流发送数据包。
*   **`broadcast`**：广播模式。在所有接口上发送每个数据包。
*   **`lacp`**：基于IEEE 802.3ad标准的链路聚合控制协议，需要交换机支持。

![](img/7acee2ee9eb9bdb37717e8763854e094_10.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_12.png)

## 配置网络接口聚合（Team） 🔧

![](img/7acee2ee9eb9bdb37717e8763854e094_14.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_16.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_18.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_20.png)

理解了Team的概念后，本节我们动手配置一个Team设备。

配置Team设备也主要使用 `nmcli` 命令，其逻辑是：先创建虚拟的Team接口，再将其与物理接口绑定。

**1. 创建Team接口 `team0`，并设置运行模式为 `activebackup`**
```bash
nmcli connection add type team con-name team0 ifname team0 config '{"runner": {"name": "activebackup"}}'
```

**2. 为Team接口配置IP地址**
```bash
nmcli connection modify team0 ipv4.addresses 192.168.1.100/24
nmcli connection modify team0 ipv4.method manual
```

**3. 将物理接口（如 `eno1`, `eno2`）作为端口（port）加入Team**
```bash
nmcli connection add type team-slave con-name team0-port1 ifname eno1 master team0
nmcli connection add type team-slave con-name team0-port2 ifname eno2 master team0
```

**4. 激活所有连接**
```bash
nmcli connection up team0
nmcli connection up team0-port1
nmcli connection up team0-port2
```

![](img/7acee2ee9eb9bdb37717e8763854e094_22.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_23.png)

**5. 查看Team状态**
```bash
teamdctl team0 state
```

你可以通过 `nmcli dev dis eno1` 断开主端口，观察 `teamdctl team0 state` 的输出变化，并测试网络连通性，以验证故障转移功能。

![](img/7acee2ee9eb9bdb37717e8763854e094_25.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_26.png)

## 软件网桥（Bridge）介绍 🌉

![](img/7acee2ee9eb9bdb37717e8763854e094_28.png)

除了聚合接口，另一种重要的虚拟网络设备是网桥。本节我们来认识软件网桥。

![](img/7acee2ee9eb9bdb37717e8763854e094_30.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_32.png)

软件网桥（Bridge）是一个工作在数据链路层（OSI第二层）的虚拟设备。它的功能类似于物理交换机，能够基于MAC地址在不同网络段之间转发流量。

在Linux虚拟化（如KVM）和云计算（如OpenStack）环境中，网桥至关重要。它用于连接虚拟机或云实例，使它们能够相互通信或访问外部网络。

## 配置软件网桥（Bridge） 🌐

了解了网桥的作用，本节我们学习如何配置它。

配置网桥同样可以使用 `nmcli` 命令或编辑配置文件。

**使用 `nmcli` 配置网桥：**

**1. 创建网桥接口 `br1`**
```bash
nmcli connection add type bridge con-name br1 ifname br1
```

**2. 为网桥配置IP地址**
```bash
nmcli connection modify br1 ipv4.addresses 192.168.1.100/24
nmcli connection modify br1 ipv4.method manual
```

**3. 将物理接口（如 `eno1`）接入网桥**
```bash
nmcli connection add type bridge-slave con-name br1-port0 ifname eno1 master br1
```

**4. 激活连接**
```bash
nmcli connection up br1
nmcli connection up br1-port0
```

![](img/7acee2ee9eb9bdb37717e8763854e094_34.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_36.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_38.png)

**5. 查看网桥信息**
```bash
brctl show
```

## 综合实验：配置链路聚合与网桥 ⚙️

学习了独立的Team和Bridge配置后，本节我们进行一个综合实验，将Team设备连接到网桥上。

![](img/7acee2ee9eb9bdb37717e8763854e094_40.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_41.png)

这个实验模拟了一个更复杂的场景：首先创建高可用的Team设备，然后将其作为一个整体端口接入一个网桥，最后由网桥提供统一的网络接入点。

![](img/7acee2ee9eb9bdb37717e8763854e094_43.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_45.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_47.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_48.png)

**实验关键步骤概述：**

1.  **创建并配置Team设备** `team0`（模式为 `activebackup`），并将 `eno1`, `eno2` 加入。
2.  **停止NetworkManager服务**，因为某些版本的NetworkManager对“网桥连接Team”的支持不佳。
    ```bash
    systemctl stop NetworkManager
    systemctl disable NetworkManager
    ```
3.  **通过配置文件关联Team与网桥**：
    *   编辑Team设备的配置文件（`/etc/sysconfig/network-scripts/ifcfg-team0`），在末尾添加 `BRIDGE=brteam0`。
    *   清理Team端口的配置文件（`ifcfg-team0-port1`, `port2`），移除IP相关配置，只保留基础绑定信息。
4.  **创建网桥配置文件**（`/etc/sysconfig/network-scripts/ifcfg-brteam0`），指定设备类型为 `bridge`，并配置IP地址（如 `192.168.1.100/24`）。
5.  **重启网络服务**，使所有配置生效。
    ```bash
    systemctl restart network
    ```
6.  最终，网络流量路径为：应用程序 -> 网桥 `brteam0` -> Team聚合链路 `team0` -> 物理接口 `eno1`/`eno2`。

## 总结 📚

本节课中我们一起学习了RHCE认证中重要的网络配置部分。

![](img/7acee2ee9eb9bdb37717e8763854e094_50.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_52.png)

我们首先从IPv6的基础知识入手，了解了其出现背景、地址结构，并重点掌握了使用 `nmcli` 为接口静态配置IPv6地址的方法。这是考试的必备技能。

接着，我们深入探讨了两种高级网络虚拟化技术：
*   **网络接口聚合（Team）**：用于提高网络可靠性和带宽。我们学会了创建Team设备、配置聚合模式（如 `activebackup`），并将物理接口绑定到Team。
*   **软件网桥（Bridge）**：用于在二层连接网络段，常见于虚拟化环境。我们学会了创建网桥、为其配置IP，并将物理或虚拟接口接入网桥。

最后，通过一个综合实验，我们将Team和Bridge技术结合应用，实现了更复杂、更健壮的网络拓扑。

![](img/7acee2ee9eb9bdb37717e8763854e094_54.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_56.png)

![](img/7acee2ee9eb9bdb37717e8763854e094_58.png)

请务必多加练习这些实验，特别是IPv6地址配置和Team的配置，确保在考试中能够熟练、准确地完成相关任务。