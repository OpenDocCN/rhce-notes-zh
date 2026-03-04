# RHCE8.0视频教程：P21：防火墙高级配置与管理

![](img/b7873626a8c9b9aa1e78307e1975afa4_0.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_2.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_4.png)

在本节课中，我们将深入学习防火墙的高级配置，包括如何管理端口、服务，以及如何通过防火墙规则实现更精细的访问控制，例如禁止Ping攻击和基于源地址的访问策略。

上一节我们介绍了防火墙的基本概念和区域管理，本节中我们来看看如何配置更具体的防火墙规则。

## 查看防火墙规则

![](img/b7873626a8c9b9aa1e78307e1975afa4_6.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_8.png)

使用 `firewall-cmd --list-all` 命令可以查看当前区域的所有规则，包括服务、端口和接口等信息。

## 管理服务与端口

防火墙规则的核心是控制对特定服务或端口的访问。服务通常对应着众所周知的端口，例如HTTP服务对应80端口。

### 添加与移除服务

以下是添加和移除HTTP服务的命令示例：
```bash
# 临时添加HTTP服务
firewall-cmd --add-service=http
# 临时移除HTTP服务
firewall-cmd --remove-service=http
```
添加服务后，外部主机即可访问该服务。移除后，访问将被阻止。

![](img/b7873626a8c9b9aa1e78307e1975afa4_10.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_12.png)

### 管理非标准端口

![](img/b7873626a8c9b9aa1e78307e1975afa4_14.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_16.png)

如果服务运行在非标准端口（例如HTTP运行在8080端口），则需要直接管理端口。

以下是添加和移除特定端口（如8080/TCP）的命令：
```bash
# 临时添加8080/TCP端口
firewall-cmd --add-port=8080/tcp
# 临时移除8080/TCP端口
firewall-cmd --remove-port=8080/tcp
```
通过这种方式，可以灵活控制对任何端口的访问。

![](img/b7873626a8c9b9aa1e78307e1975afa4_18.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_20.png)

## 禁止ICMP Ping请求

为了防止服务器受到Ping洪水攻击，可以通过防火墙阻止ICMP Echo Request（回显请求）包。

以下是阻止和放行ICMP Echo Request的命令：
```bash
# 阻止ICMP Echo Request（禁Ping）
firewall-cmd --add-icmp-block=echo-request
# 放行ICMP Echo Request（允许Ping）
firewall-cmd --remove-icmp-block=echo-request
```
阻止 `echo-request` 后，外部主机将无法Ping通本机，因为服务器不会回复ICMP Echo Reply包。

## 查询特定规则

![](img/b7873626a8c9b9aa1e78307e1975afa4_22.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_24.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_26.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_28.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_30.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_32.png)

有时需要查询某个服务或端口是否在特定区域被允许。

![](img/b7873626a8c9b9aa1e78307e1975afa4_33.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_34.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_36.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_38.png)

以下是查询命令示例：
```bash
# 查询HTTP服务在public区域的状态
firewall-cmd --zone=public --query-service=http
# 查询8080/tcp端口在public区域的状态
firewall-cmd --zone=public --query-port=8080/tcp
```
查询结果会返回 `yes` 或 `no`，表示是否允许。

## 基于源地址（Source）的规则

![](img/b7873626a8c9b9aa1e78307e1975afa4_40.png)

![](img/b7873626a8c9b9aa1e78307e1975afa4_42.png)

可以设置规则，仅允许或拒绝来自特定IP地址或网段的流量。这比全局规则更精细。

以下是添加基于源网段规则的基本命令格式：
```bash
# 允许来自192.168.1.0/24网段的所有流量（临时）
firewall-cmd --add-source=192.168.1.0/24
```
此规则意味着只有来自 `192.168.1.0/24` 网段的流量会被当前区域的规则处理，其他来源的流量默认会被拒绝。

## 富规则（Rich Rules）

富规则提供了极其强大和灵活的配置能力，允许在单条规则中组合源地址、目标地址、端口、协议和行为。

以下是创建一条富规则的示例，它允许来自 `192.168.2.0/24` 网段的主机进行SSH访问：
```bash
firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.2.0/24" service name="ssh" accept'
```
相应地，可以创建规则拒绝来自其他网段（如 `192.168.3.0/24`）的SSH访问：
```bash
firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.3.0/24" service name="ssh" reject'
```
富规则的语法复杂但功能全面，是实现复杂安全策略的关键工具。

## 接口与区域管理回顾

一个网络接口只能属于一个防火墙区域。如果接口不属于任何区域，它将遵循默认区域的规则。

以下是关键的管理命令总结：
*   **查看所有区域规则**：`firewall-cmd --list-all`
*   **查看默认区域**：`firewall-cmd --get-default-zone`
*   **查看接口所属区域**：`firewall-cmd --get-zone-of-interface=<接口名>`
*   **修改接口所属区域**：`firewall-cmd --zone=<新区域> --change-interface=<接口名>`
*   **将接口从区域中移除**：`firewall-cmd --zone=<区域> --remove-interface=<接口名>`
*   **将接口添加到区域**：`firewall-cmd --zone=<区域> --add-interface=<接口名>` （仅当接口不属于任何区域时有效）

![](img/b7873626a8c9b9aa1e78307e1975afa4_44.png)

本节课中我们一起学习了防火墙的高级配置。我们掌握了如何通过管理服务和端口来控制访问，如何通过禁止ICMP请求来增强服务器安全性，以及如何利用基于源地址的规则和功能强大的富规则来实现精细化的流量控制。这些技能对于构建安全的服务器环境至关重要。