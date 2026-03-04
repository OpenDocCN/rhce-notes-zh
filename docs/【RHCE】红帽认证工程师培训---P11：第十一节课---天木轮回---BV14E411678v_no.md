# RHCE培训课程：P11：防火墙与网络配置实战

![](img/a7f3aba3be3928a910a2f36f8596f078_1.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_3.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_4.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_5.png)

在本节课中，我们将深入学习红帽系统中的防火墙配置工具以及高级网络配置技术。课程将涵盖多种防火墙管理方法，包括命令行和图形化工具，并介绍网卡绑定等实用网络技术，旨在帮助初学者掌握企业环境中网络安全的配置与管理。

## 防火墙工具概览

上一节我们介绍了防火墙的基本概念。本节中，我们来看看红帽系统8中默认的防火墙管理工具：`firewalld`。

`firewalld`防火墙引入了“区域”的概念。区域可以理解为预设的防火墙策略模板，用于快速适应不同的网络环境（如家庭、公司、咖啡厅）。系统安装后默认存在多个区域，其中最重要的是 `public` 区域，它是系统当前默认使用的策略模板。

以下是系统预定义的一些常用区域：
*   **public**： 默认区域，当前正在使用的防火墙策略模板。
*   **trusted**： 允许所有流量。
*   **drop**： 拒绝所有流量（无回复）。
*   **block**： 拒绝所有流量（有拒绝回复）。

配置防火墙时，主要操作对象是 `public` 区域。同时，`trusted` 和 `drop/block` 代表了两种极端的策略，可用于快速开放或切断网络。

## 使用 firewalld-cmd 命令

`firewalld-cmd` 是管理 `firewalld` 的主要命令行工具。其参数支持使用 `Tab` 键补全，这对初学者非常友好。

首先，我们需要确认当前生效的区域：
```bash
firewall-cmd --get-default-zone
```
输出应为 `public`。所有后续配置都应针对此区域进行，否则可能不生效。

`firewalld` 的策略有两种生效模式，理解其区别至关重要：
*   **运行时模式**： 配置立即生效，但重启系统或 `firewalld` 服务后失效。
*   **永久模式**： 配置不会立即生效，需要执行 `firewall-cmd --reload` 或重启系统后才会生效，并且会持久化保存。

在配置生产环境或应对考试时，**务必使用永久模式参数** `--permanent`，以确保配置在重启后依然有效。

![](img/a7f3aba3be3928a910a2f36f8596f078_7.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_8.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_10.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_12.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_13.png)

## 配置防火墙策略实战

![](img/a7f3aba3be3928a910a2f36f8596f078_15.png)

### 1. 管理服务
以下是如何查询、允许和禁止特定服务（以 `http` 服务为例）的步骤。

![](img/a7f3aba3be3928a910a2f36f8596f078_17.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_19.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_21.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_23.png)

查询 `http` 服务当前是否被允许：
```bash
firewall-cmd --zone=public --query-service=http
```
如果返回 `no`，表示该服务流量被阻止。

![](img/a7f3aba3be3928a910a2f36f8596f078_25.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_26.png)

将 `http` 服务永久添加到允许列表：
```bash
firewall-cmd --zone=public --add-service=http --permanent
```
执行此命令后，服务**不会立即被允许**。需要重载配置使其生效：
```bash
firewall-cmd --reload
```
再次查询，返回结果应为 `yes`。

![](img/a7f3aba3be3928a910a2f36f8596f078_28.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_29.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_31.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_32.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_33.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_34.png)

从允许列表中永久移除 `http` 服务：
```bash
firewall-cmd --zone=public --remove-service=http --permanent
firewall-cmd --reload
```

![](img/a7f3aba3be3928a910a2f36f8596f078_36.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_37.png)

### 2. 管理端口
除了服务名，也可以直接操作端口号。

永久放行TCP协议8080端口：
```bash
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --reload
```
永久放行8080-8081范围内的TCP端口：
```bash
firewall-cmd --zone=public --add-port=8080-8081/tcp --permanent
firewall-cmd --reload
```

### 3. 端口转发
端口转发可以将到达本机某端口的流量自动转发到本机或另一台主机的另一个端口。

![](img/a7f3aba3be3928a910a2f36f8596f078_39.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_41.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_43.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_44.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_46.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_48.png)

例如，将到达本机8888端口的TCP流量，转发到本机的22端口（SSH服务）：
```bash
firewall-cmd --zone=public --add-forward-port=port=8888:proto=tcp:toport=22:toaddr=192.168.10.10 --permanent
firewall-cmd --reload
```
配置后，访问 `192.168.10.10:8888` 即相当于访问其SSH服务。

![](img/a7f3aba3be3928a910a2f36f8596f078_50.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_52.png)

### 4. 富规则
富规则允许进行更精细、复杂的流量匹配，例如针对特定源IP地址进行控制。

![](img/a7f3aba3be3928a910a2f36f8596f078_54.png)

永久禁止来自 `192.168.10.0/24` 网段的主机访问本机的SSH服务：
```bash
firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.10.0/24" service name="ssh" reject' --permanent
firewall-cmd --reload
```

![](img/a7f3aba3be3928a910a2f36f8596f078_56.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_58.png)

## 图形化工具：firewall-config

![](img/a7f3aba3be3928a910a2f36f8596f078_60.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_61.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_63.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_64.png)

对于不习惯命令行的用户，红帽提供了图形化配置工具 `firewall-config`。在终端输入此命令即可打开。

该工具提供了直观的界面来管理区域、服务、端口、端口转发和富规则。**其所有操作底层均调用 `firewalld-cmd` 命令**。在图形界面中：
*   可以勾选/取消服务来允许或拒绝。
*   可以添加端口和端口范围。
*   可以通过表单轻松配置端口转发和富规则。

![](img/a7f3aba3be3928a910a2f36f8596f078_66.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_68.png)

在考试或工作中，如果允许使用图形界面，`firewall-config` 是更稳妥、高效的选择。

![](img/a7f3aba3be3928a910a2f36f8596f078_70.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_71.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_72.png)

## 简单防火墙工具：TCP Wrappers

![](img/a7f3aba3be3928a910a2f36f8596f078_74.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_75.png)

`TCP Wrappers` 是一个基于应用层的简单防火墙工具，它只能针对服务名称进行控制，但配置非常简单。

![](img/a7f3aba3be3928a910a2f36f8596f078_77.png)

它通过两个文件进行管理：
*   **允许列表**： `/etc/hosts.allow`
*   **拒绝列表**： `/etc/hosts.deny`

![](img/a7f3aba3be3928a910a2f36f8596f078_79.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_80.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_82.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_83.png)

其规则是：如果一个连接既不在 `allow` 列表，也不在 `deny` 列表，则**默认允许**。如果出现在 `deny` 列表，则拒绝。`allow` 列表的优先级高于 `deny` 列表。

![](img/a7f3aba3be3928a910a2f36f8596f078_85.png)

配置格式为：`服务名: 客户端地址`。

![](img/a7f3aba3be3928a910a2f36f8596f078_87.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_88.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_90.png)

禁止 `192.168.10.0/24` 网段访问 `sshd` 服务：
```bash
# 编辑拒绝文件
echo “sshd: 192.168.10.” >> /etc/hosts.deny
```
此配置会**立即生效**。

![](img/a7f3aba3be3928a910a2f36f8596f078_92.png)

然后，单独允许 `192.168.10.100` 这个IP访问 `sshd` 服务：
```bash
# 编辑允许文件
echo “sshd: 192.168.10.100” >> /etc/hosts.allow
```
这样，只有 `192.168.10.100` 可以连接SSH，同网段其他主机均被拒绝。

![](img/a7f3aba3be3928a910a2f36f8596f078_94.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_95.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_97.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_98.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_100.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_102.png)

## 网络配置进阶

![](img/a7f3aba3be3928a910a2f36f8596f078_104.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_106.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_108.png)

### 网络会话
网络会话功能允许为同一块网卡创建不同的配置模板（会话），以便在不同网络环境（如家庭DHCP、公司静态IP）间快速切换。

![](img/a7f3aba3be3928a910a2f36f8596f078_110.png)

创建名为 `company` 的会话，配置静态IP：
```bash
nmcli connection add con-name company ifname eth0 type ethernet autoconnect no ip4 192.168.10.88/24 gw4 192.168.10.1
```
创建名为 `home` 的会话，配置为DHCP自动获取：
```bash
nmcli connection add con-name home ifname eth0 type ethernet autoconnect no
```
启用 `company` 会话：
```bash
nmcli connection up company
```
启用 `home` 会话：
```bash
nmcli connection up home
```

### 网卡绑定
网卡绑定（Bonding）是将多块物理网卡逻辑上捆绑成一块“虚拟网卡”，以实现**负载均衡**和**冗余备份**。

这里以 `mode 6` (adaptive load balancing) 为例，它同时具备负载均衡和故障切换功能。

假设有两块网卡 `eth0` 和 `eth1`，要绑定为 `bond0`。

![](img/a7f3aba3be3928a910a2f36f8596f078_112.png)

1.  编辑第一块网卡(`eth0`)配置文件 `/etc/sysconfig/network-scripts/ifcfg-eth0`：
    ```
    TYPE=Ethernet
    BOOTPROTO=none
    ONBOOT=yes
    USERCTL=no
    DEVICE=eth0
    MASTER=bond0
    SLAVE=yes
    ```
2.  编辑第二块网卡(`eth1`)配置文件 `/etc/sysconfig/network-scripts/ifcfg-eth1`，内容与 `eth0` 类似，只需修改 `DEVICE=eth1`。
3.  创建绑定网卡配置文件 `/etc/sysconfig/network-scripts/ifcfg-bond0`：
    ```
    TYPE=Bond
    BOOTPROTO=none
    ONBOOT=yes
    USERCTL=no
    DEVICE=bond0
    IPADDR=192.168.10.10
    PREFIX=24
    GATEWAY=192.168.10.1
    BONDING_OPTS=“mode=6 miimon=100”
    ```
    `miimon=100` 表示每100毫秒检查一次链路状态。
4.  加载绑定模块驱动，编辑 `/etc/modprobe.d/bond.conf`：
    ```
    alias bond0 bonding
    options bond0 mode=6 miimon=100
    ```
5.  重启网络服务：
    ```bash
    systemctl restart network
    ```

![](img/a7f3aba3be3928a910a2f36f8596f078_114.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_115.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_116.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_118.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_120.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_122.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_124.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_126.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_128.png)

配置成功后，`bond0` 会获得IP。此时禁用 `eth0` 或 `eth1` 中的任意一块，网络连接只会出现瞬间丢包（通常不超过1个），随后由另一块网卡无缝接管，保证业务不中断。

## 课程总结

![](img/a7f3aba3be3928a910a2f36f8596f078_130.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_132.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_134.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_135.png)

本节课中我们一起学习了红帽系统中多种防火墙配置方法以及高级网络技术。

![](img/a7f3aba3be3928a910a2f36f8596f078_137.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_138.png)

1.  我们深入探讨了 `firewalld` 服务，理解了区域的概念、运行时与永久模式的区别，并实战演练了服务、端口、端口转发和富规则的配置。
2.  我们介绍了图形化工具 `firewall-config`，它提供了更直观的配置界面。
3.  我们学习了简单的 `TCP Wrappers` 工具，它通过 `hosts.allow` 和 `hosts.deny` 文件实现快速访问控制。
4.  在网络配置部分，我们掌握了使用 `nmcli` 管理网络会话，实现配置模板的快速切换。
5.  最后，我们完成了网卡绑定的实验，实现了网络接口的负载均衡与高可用。

![](img/a7f3aba3be3928a910a2f36f8596f078_140.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_142.png)

![](img/a7f3aba3be3928a910a2f36f8596f078_144.png)

对于防火墙管理，**掌握 `firewalld-cmd` 和 `firewall-config` 其中一种即可**，在考试中推荐使用更不易出错的图形化工具。网卡绑定是提升网络可靠性的重要技术，需要理解其原理并熟悉配置流程。