# Linux运维：RHCE：firewalld配置 - P1：iptables配置举例01

在本节课中，我们将学习如何使用 `iptables` 配置防火墙规则，包括放行特定服务端口、设置默认策略以及实现网络地址转换。我们将通过一个Web服务器的配置实例和一个NAT转换的实例来详细讲解。

## 概述

![](img/0b019a673d5567cf1f48e6d62117efa4_1.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_3.png)

`iptables` 是Linux系统中一个强大的防火墙工具，它允许管理员定义规则来控制网络数据包的流入、流出和转发。理解并掌握 `iptables` 的配置是Linux系统管理员和RHCE认证的重要技能。本节我们将通过具体实例，学习如何为Web服务器配置防火墙，以及如何实现SNAT和DNAT。

![](img/0b019a673d5567cf1f48e6d62117efa4_5.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_7.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_9.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_11.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_13.png)

## Web服务器防火墙配置实例

![](img/0b019a673d5567cf1f48e6d62117efa4_15.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_17.png)

上一节我们回顾了FTP服务的防火墙配置要点。本节中，我们来看看如何为一台运行Web服务的服务器配置 `iptables` 规则。

假设服务器需要提供SSH（端口22）和HTTP（端口80）服务，并且需要允许ICMP（ping）请求。同时，必须放行回环接口的流量，并允许已建立的或相关的连接状态。

![](img/0b019a673d5567cf1f48e6d62117efa4_19.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_21.png)

以下是配置Web服务器防火墙的关键步骤：

![](img/0b019a673d5567cf1f48e6d62117efa4_23.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_24.png)

1.  **放行回环接口流量**：许多本地服务需要与回环接口通信。
    ```bash
    iptables -A INPUT -i lo -j ACCEPT
    ```

![](img/0b019a673d5567cf1f48e6d62117efa4_26.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_28.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_30.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_32.png)

2.  **放行SSH和HTTP端口**：使用 `-m multiport` 模块可以一次性放行多个端口。
    ```bash
    iptables -A INPUT -p tcp -m multiport --dports 22,80 -j ACCEPT
    ```

![](img/0b019a673d5567cf1f48e6d62117efa4_34.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_36.png)

3.  **放行ICMP协议**：允许外部ping通服务器以检测网络连通性。
    ```bash
    iptables -A INPUT -p icmp -j ACCEPT
    ```

![](img/0b019a673d5567cf1f48e6d62117efa4_38.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_39.png)

4.  **放行已建立或相关的连接**：允许由服务器主动发起或已接受的连接继续通信。
    ```bash
    iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
    ```

![](img/0b019a673d5567cf1f48e6d62117efa4_41.png)

5.  **设置默认策略为拒绝**：将所有未匹配上述规则的数据包丢弃。
    ```bash
    iptables -P INPUT DROP
    ```

![](img/0b019a673d5567cf1f48e6d62117efa4_43.png)

**配置验证**：配置完成后，可以使用 `iptables -L -n -v` 命令查看规则列表。使用 `telnet` 或浏览器测试80端口，使用SSH客户端测试22端口，均应能正常访问。而未放行的端口（如FTP的21端口）将无法访问。

![](img/0b019a673d5567cf1f48e6d62117efa4_45.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_47.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_49.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_50.png)

**注意**：`iptables` 规则默认是临时的，重启后会失效。如需永久保存，在CentOS/RHEL 6及以前可使用 `service iptables save` 命令，在CentOS/RHEL 7+中，`iptables` 服务可能被 `firewalld` 替代，需注意区分。

![](img/0b019a673d5567cf1f48e6d62117efa4_52.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_54.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_56.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_58.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_59.png)

## SNAT（源地址转换）配置

![](img/0b019a673d5567cf1f48e6d62117efa4_61.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_63.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_65.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_67.png)

在了解了基础服务端口放行后，我们进一步学习网络地址转换。首先来看SNAT，它通常用于让内网主机共享一个公网IP访问互联网。

![](img/0b019a673d5567cf1f48e6d62117efa4_69.png)

在我们的实验环境中，`192.168.2.63` 主机配置了两张网卡：
*   `ens32`: `192.168.2.63` （桥接模式，可上外网）
*   `ens34`: `192.168.1.63` （仅主机模式，内网）

![](img/0b019a673d5567cf1f48e6d62117efa4_71.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_73.png)

另一台主机 `192.168.1.64` 的网关指向 `192.168.1.63`。目标是让 `1.64` 主机通过 `2.63` 主机上网。

以下是实现SNAT的步骤：

1.  **在 `2.63` 上开启内核IP转发**：
    ```bash
    echo 1 > /proc/sys/net/ipv4/ip_forward
    ```
    永久生效需编辑 `/etc/sysctl.conf`，设置 `net.ipv4.ip_forward = 1`，然后执行 `sysctl -p`。

2.  **在 `2.63` 上添加SNAT规则**：将来自 `192.168.1.0/24` 网段的数据包源地址转换为 `192.168.2.63`。
    ```bash
    iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -j SNAT --to-source 192.168.2.63
    ```
    如果出口IP是动态获取的（如PPPoE拨号），可以使用 `MASQUERADE`（伪装）动作：
    ```bash
    iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o ens32 -j MASQUERADE
    ```

![](img/0b019a673d5567cf1f48e6d62117efa4_75.png)

3.  **验证**：在 `1.64` 主机上执行 `ping 8.8.8.8` 或 `curl` 访问外网，应能成功。使用 `traceroute` 命令可以追踪到数据包首先经过网关 `192.168.1.63`。

## DNAT（目标地址转换）配置

接下来我们学习DNAT，它常用于将到达防火墙公网IP的请求，转发到内网的服务器上，即端口映射。

![](img/0b019a673d5567cf1f48e6d62117efa4_77.png)

继续使用上述环境，目标是将到达 `2.63` 主机80端口的访问，转发到内网 `1.64` 主机的80端口（假设 `1.64` 已运行Web服务）。

![](img/0b019a673d5567cf1f48e6d62117efa4_79.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_81.png)

以下是实现DNAT的步骤：

![](img/0b019a673d5567cf1f48e6d62117efa4_83.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_85.png)

1.  **在 `2.63` 上添加DNAT规则**：
    ```bash
    iptables -t nat -A PREROUTING -d 192.168.2.63 -p tcp --dport 80 -j DNAT --to-destination 192.168.1.64:80
    ```
    也可以指定入口网卡来配置，这样即使公网IP变化，规则也依然有效：
    ```bash
    iptables -t nat -A PREROUTING -i ens32 -p tcp --dport 80 -j DNAT --to-destination 192.168.1.64:80
    ```

![](img/0b019a673d5567cf1f48e6d62117efa4_87.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_89.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_91.png)

2.  **验证**：在外部客户端浏览器访问 `http://192.168.2.63`，实际显示的内容是内网 `192.168.1.64` 主机Web服务的内容。

![](img/0b019a673d5567cf1f48e6d62117efa4_93.png)

![](img/0b019a673d5567cf1f48e6d62117efa4_95.png)

## iptables 命令小结

![](img/0b019a673d5567cf1f48e6d62117efa4_97.png)

本节课中我们一起学习了 `iptables` 的核心配置。以下是关键命令的总结：

![](img/0b019a673d5567cf1f48e6d62117efa4_99.png)

*   **链（Chain）**：主要处理 `INPUT`（入站）、`OUTPUT`（出站）、`FORWARD`（转发）。
*   **表（Table）**：常用 `filter`（过滤，默认） 和 `nat`（地址转换）。
*   **动作（Target）**：`ACCEPT`（接受）、`DROP`（丢弃）、`REJECT`（拒绝）、`SNAT`（源地址转换）、`DNAT`（目标地址转换）、`MASQUERADE`（IP伪装）。
*   **匹配参数**：
    *   `-s`：源地址
    *   `-d`：目标地址
    *   `-p`：协议（如 `tcp`, `udp`, `icmp`）
    *   `--dport`：目标端口
    *   `-m`：扩展模块（如 `state`, `multiport`）
*   **查看规则**：`iptables -L -n -v`
*   **清空规则**：`iptables -F`
*   **保存规则**（系统服务方式）：`service iptables save` （RHEL/CentOS 6）

![](img/0b019a673d5567cf1f48e6d62117efa4_101.png)

## 总结

![](img/0b019a673d5567cf1f48e6d62117efa4_103.png)

本节课中，我们通过两个实例深入学习了 `iptables` 的配置。首先，我们为Web服务器配置了防火墙规则，放行了必要的服务端口并设置了安全默认策略。接着，我们探讨了网络地址转换，实现了SNAT让内网主机上网，以及DNAT进行端口映射将公网请求转发至内网服务器。掌握这些配置，是构建安全、可控网络环境的基础。