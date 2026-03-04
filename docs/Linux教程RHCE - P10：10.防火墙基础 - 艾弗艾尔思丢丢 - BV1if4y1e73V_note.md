# Linux教程RHCE：P10：防火墙基础 🔥

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_1.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_2.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_4.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_5.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_6.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_7.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_9.png)

在本节课中，我们将学习Linux防火墙的基础知识。防火墙是保护服务器安全的重要工具，它通过定义规则来控制网络流量的进出。我们将从配置网络开始，然后深入探讨防火墙的核心概念、策略配置以及多种配置方法，确保初学者也能轻松掌握。

---

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_11.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_13.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_14.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_16.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_18.png)

## 网络配置基础

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_20.png)

在配置防火墙之前，我们需要确保服务器网络配置正确，以便后续的服务测试能够顺利进行。网络配置是服务能够被访问的基础。

以下是四种配置网卡的方法，您可以选择其中一种最适合自己的方式。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_22.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_24.png)

1.  **方法一：直接编辑配置文件**
    这是最底层的方法，通过修改网卡的配置文件来设置网络参数。配置文件位于 `/etc/sysconfig/network-scripts/` 目录下，文件名通常为 `ifcfg-` 后接网卡名称（例如 `ifcfg-ens33`）。
    *   **步骤**：使用文本编辑器（如 `vi` 或 `nano`）打开对应网卡的配置文件，修改 `BOOTPROTO`（启动协议，如 `static` 表示静态IP）、`IPADDR`（IP地址）、`NETMASK`（子网掩码）等参数，然后保存退出。
    *   **生效**：修改后需要重启网络服务（`systemctl restart network`）或重启网卡（`ifdown ens33 && ifup ens33`）才能使配置生效。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_26.png)

2.  **方法二：使用 `nmtui` 命令**
    这是一个基于文本用户界面的网络配置工具，在红帽7及以后版本中提供，操作直观。
    *   **步骤**：在终端输入 `nmtui` 命令，会打开一个图形化界面。选择“Edit a connection”，然后选择要配置的网卡，按照提示填写IP地址、网关等信息，最后保存退出。
    *   **生效**：同样需要重启网络服务或网卡。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_28.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_30.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_32.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_33.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_34.png)

3.  **方法三：使用 `nm-connection-editor` 命令**
    这是一个基于图形化界面的配置工具，操作最为简单直观。
    *   **步骤**：在桌面环境的终端输入 `nm-connection-editor`，会弹出网络连接设置窗口。选择网卡进行编辑，填写网络参数后保存。
    *   **生效**：配置后通常会自动生效，或需要重启网络服务。

4.  **方法四：通过虚拟机软件界面配置**
    如果您使用的是虚拟机（如VMware），可以直接在虚拟机设置中配置网络适配器，并设置IP地址。
    *   **步骤**：在虚拟机设置中，选择网络适配器，设置为“仅主机模式”或“桥接模式”，然后在虚拟机系统内通过图形界面或上述命令设置IP地址。
    *   **生效**：设置后可能需要重启虚拟机网络。

**核心概念**：Linux中一切皆文件，配置服务本质上是修改其配置文件。修改配置后，通常需要重启相关服务才能应用新配置。

---

## 防火墙核心概念

上一节我们介绍了网络配置，这是服务通信的基础。本节中，我们来看看防火墙如何作为网络的“防盗门”，控制流量的进出。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_36.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_37.png)

防火墙的核心功能是依据预设的规则，对进出网络的数据包进行过滤。它主要处理三种方向的流量：

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_39.png)

*   **INPUT**：处理**从外部进入本机**的流量。例如，外部用户访问本机的Web服务。
*   **OUTPUT**：处理**从本机发出到外部**的流量。例如，本机用户访问外部的网站。
*   **FORWARD**：处理**经过本机转发**的流量。例如，本机作为路由器或网关时，转发其他设备间的数据。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_41.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_43.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_44.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_46.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_47.png)

防火墙规则按照**从上到下的顺序逐条匹配**。一旦数据包匹配到某条规则，就会执行该规则定义的动作，并**停止后续规则的匹配**。因此，应将最常用或最严格的规则放在前面。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_49.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_51.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_53.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_55.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_57.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_58.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_59.png)

防火墙规则可以执行以下几种**动作**：

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_61.png)

*   **ACCEPT**：**允许**数据包通过。
*   **REJECT**：**拒绝**数据包通过，并向发送方返回一个明确的拒绝通知。
*   **DROP**：**丢弃**数据包，不给予任何回应，发送方会认为超时。
*   **LOG**：将数据包信息记录到日志中，通常与其他动作配合使用。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_63.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_65.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_67.png)

**重要区别**：`REJECT` 和 `DROP` 都能阻止访问，但 `REJECT` 会告知对方“被拒绝”，而 `DROP` 则 silently 丢弃，对方不知道是目标不存在还是被阻止。在考试或需要明确反馈的场景中，通常使用 `REJECT`。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_69.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_70.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_71.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_72.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_74.png)

---

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_75.png)

## 配置防火墙的三种方法

了解了防火墙的基本概念后，本节我们将学习三种配置防火墙的工具。每种工具各有特点，您可以根据自己的喜好和场景选择使用。

### 方法一：使用 `iptables` 命令（传统方式）

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_77.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_79.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_81.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_82.png)

`iptables` 是Linux上历史悠久的防火墙配置工具，基于命令行，功能强大但配置稍显复杂。请注意，在较新的红帽8等系统中，它可能默认未被安装。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_84.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_85.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_87.png)

**基本操作命令**：

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_89.png)

*   查看规则：`iptables -L`
*   清空所有规则：`iptables -F`
*   设置默认策略（如INPUT链默认丢弃）：`iptables -P INPUT DROP`
*   添加规则（允许特定IP访问SSH）：
    ```bash
    iptables -I INPUT -s 192.168.10.0/24 -p tcp --dport 22 -j ACCEPT
    ```
    *   `-I INPUT`：在INPUT链的**开头**插入规则。
    *   `-s 192.168.10.0/24`：匹配源IP地址为该网段。
    *   `-p tcp`：匹配TCP协议。
    *   `--dport 22`：匹配目标端口为22（SSH）。
    *   `-j ACCEPT`：执行的动作是允许。
*   删除特定规则：需要先 `iptables -L --line-numbers` 查看规则编号，然后 `iptables -D INPUT <规则编号>` 删除。
*   保存规则（否则重启失效）：`service iptables save`（红帽6）或安装 `iptables-services` 后使用 `systemctl save iptables`（红帽7）。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_91.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_93.png)

### 方法二：使用 `firewall-cmd` 命令（推荐）

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_95.png)

`firewall-cmd` 是红帽7/8及以上版本默认的防火墙管理工具 `firewalld` 的命令行客户端。它引入了“区域”概念，管理更灵活，规则是动态的，修改后无需重启服务即可生效（部分配置需重载）。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_97.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_98.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_99.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_100.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_102.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_103.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_105.png)

**核心概念：区域**：区域可以理解为一套预定义的防火墙规则模板（如 `public`、`home`、`internal`）。不同的网卡可以绑定到不同的区域，从而快速切换安全策略。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_107.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_109.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_110.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_112.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_113.png)

**基本操作命令**：

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_115.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_116.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_118.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_119.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_120.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_122.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_124.png)

*   查看默认区域：`firewall-cmd --get-default-zone`
*   查看当前活动区域：`firewall-cmd --get-active-zones`
*   永久修改默认区域：`firewall-cmd --set-default-zone=home --permanent`
*   查看区域中已放行的服务：`firewall-cmd --zone=public --list-services`
*   永久放行/移除某个服务（如HTTP）：
    ```bash
    firewall-cmd --zone=public --add-service=http --permanent
    firewall-cmd --zone=public --remove-service=http --permanent
    ```
*   永久放行特定端口：
    ```bash
    firewall-cmd --zone=public --add-port=8080/tcp --permanent
    ```
*   配置端口转发（将访问本机8888端口的流量转发到22端口）：
    ```bash
    firewall-cmd --zone=public --add-forward-port=port=8888:proto=tcp:toport=22 --permanent
    ```
*   添加富规则（更复杂的规则，如拒绝特定IP访问）：
    ```bash
    firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.10.100" service name="ssh" reject' --permanent
    ```
*   使所有永久配置立即生效：`firewall-cmd --reload`
*   紧急模式（切断所有网络连接）：`firewall-cmd --panic-on`；关闭紧急模式：`firewall-cmd --panic-off`

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_125.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_127.png)

### 方法三：使用 `firewall-config` 图形化工具

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_129.png)

如果您使用的是带有图形界面的Linux系统，可以使用 `firewall-config` 工具。它是 `firewalld` 的图形化前端，所有操作都与 `firewall-cmd` 命令一一对应，非常适合不熟悉命令的用户。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_131.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_133.png)

*   **启动**：在终端输入 `firewall-config` 或从系统菜单中查找“防火墙”。
*   **操作**：在界面中，您可以直观地选择区域、勾选需要放行的服务、添加端口、配置端口转发和富规则等。配置完成后，通常需要点击“重载”或“应用”按钮使配置生效。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_135.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_137.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_138.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_139.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_141.png)

**选择建议**：对于初学者和日常管理，**推荐使用 `firewall-cmd`**。它结合了灵活性和便利性。`firewall-config` 适合图形界面环境下的快速操作。`iptables` 适合需要深度控制或维护老系统的场景。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_143.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_144.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_146.png)

---

## 总结

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_148.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_150.png)

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_152.png)

本节课中，我们一起学习了Linux防火墙的基础知识。

1.  我们首先了解了**配置网络的四种方法**，为后续服务测试打下基础。
2.  接着，我们深入探讨了防火墙的**核心概念**，包括流量方向（INPUT/OUTPUT/FORWARD）、规则的匹配顺序以及允许（ACCEPT）、拒绝（REJECT）、丢弃（DROP）等关键动作。
3.  最后，我们重点讲解了配置防火墙的**三种主要工具**：传统的 `iptables` 命令、红帽系推荐的 `firewall-cmd` 命令以及图形化的 `firewall-config` 工具。我们特别强调了 `firewall-cmd` 中“区域”的概念和 `--permanent`（永久生效）与 `--reload`（重载配置）参数的使用。

![](img/be4cf60014a1f38a93ee9da3bbbb13e8_154.png)

防火墙是Linux系统安全的重要组成部分，熟练掌握其配置方法对于系统管理员至关重要。建议课后多加练习，尝试使用 `firewall-cmd` 为不同的服务（如HTTP、SSH）设置放行规则，并理解其背后的原理。