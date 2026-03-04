# Red Hat RHCE 8.0 认证体系课程：P50：管理网络安全

![](img/fe795e34b1251580e74064c35a1db551_0.png)

## 概述
在本节课中，我们将学习 Linux 系统中的防火墙管理，特别是 `firewalld` 服务。我们将了解防火墙区域、规则、服务以及如何配置它们来控制网络流量。

---

## 防火墙基础概念
防火墙用于控制网络流量。在 Linux 中，`firewalld` 服务负责执行包过滤操作。要使用 `firewalld`，需要理解两个核心概念：如何编写规则以及如何匹配规则。

防火墙包含不同的区域，数据包会根据其源地址被关联到特定区域，并在该区域内匹配规则。这与物理防火墙类似，每个区域可以配置不同的规则集，以适应不同的网段。

![](img/fe795e34b1251580e74064c35a1db551_2.png)

在配置防火墙之前，需要将源地址或网卡与相应的区域关联起来。默认情况下，所有网卡都属于 `public` 区域。

![](img/fe795e34b1251580e74064c35a1db551_4.png)

![](img/fe795e34b1251580e74064c35a1db551_5.png)

---

![](img/fe795e34b1251580e74064c35a1db551_7.png)

![](img/fe795e34b1251580e74064c35a1db551_9.png)

![](img/fe795e34b1251580e74064c35a1db551_11.png)

## 防火墙区域
在 RHEL 7 中有 9 个区域，RHEL 8 增加了一个 `libvirt` 区域（专用于虚拟机）。以下是部分区域的默认行为：

![](img/fe795e34b1251580e74064c35a1db551_12.png)

*   **trusted**：允许所有传输流量，表示完全信任。
*   **block**：拒绝所有传入流量，除非与传出流量相关。
*   **dmz**：用于隔离的边界网络（非军事区）。
*   **home** / **internal**：用于家庭或内部网络，信任度较高。
*   **work**：用于工作场所网络。
*   **public**：通用公共区域。默认情况下，除了 SSH 和 DHCPv6 等少数服务外，会拒绝其他所有传入连接。

![](img/fe795e34b1251580e74064c35a1db551_14.png)

`firewalld` 服务在 Linux 系统中默认已安装。RHEL 8 的 `firewalld` 支持更灵活的规则匹配。

---

## 常用防火墙命令操作
上一节我们介绍了防火墙区域的概念，本节中我们来看看如何使用 `firewall-cmd` 命令进行常见操作。

`firewall-cmd` 命令功能丰富，可以使用 `firewall-cmd --help` 查看所有选项。

![](img/fe795e34b1251580e74064c35a1db551_16.png)

以下是常用操作示例：

![](img/fe795e34b1251580e74064c35a1db551_18.png)

![](img/fe795e34b1251580e74064c35a1db551_20.png)

*   **列出所有区域及默认区域**：
    ```bash
    firewall-cmd --list-all-zones
    firewall-cmd --get-default-zone
    ```
    默认区域通常是 `public`。未特别指定时，所有服务都应用默认区域的规则。

![](img/fe795e34b1251580e74064c35a1db551_22.png)

![](img/fe795e34b1251580e74064c35a1db551_24.png)

*   **列出预定义的服务**：
    ```bash
    firewall-cmd --get-services
    ```
    这会列出 `firewalld` 已知的所有服务（如 `ssh`, `http`），每个服务对应一个或多个端口。

![](img/fe795e34b1251580e74064c35a1db551_26.png)

![](img/fe795e34b1251580e74064c35a1db551_28.png)

*   **查看服务定义文件**：
    预定义的服务文件位于 `/usr/lib/firewalld/services/` 目录下。每个文件定义一个网络服务及其使用的 TCP/UDP 端口。
    自定义服务文件应放在 `/etc/firewalld/services/` 目录中。使用服务名称管理规则更人性化、高效。

![](img/fe795e34b1251580e74064c35a1db551_30.png)

![](img/fe795e34b1251580e74064c35a1db551_32.png)

![](img/fe795e34b1251580e74064c35a1db551_34.png)

---

![](img/fe795e34b1251580e74064c35a1db551_36.png)

## 管理防火墙区域与源
现在，我们来学习如何管理防火墙区域以及如何将源地址或网卡关联到特定区域。

![](img/fe795e34b1251580e74064c35a1db551_38.png)

![](img/fe795e34b1251580e74064c35a1db551_40.png)

![](img/fe795e34b1251580e74064c35a1db551_41.png)

![](img/fe795e34b1251580e74064c35a1db551_43.png)

### 将源地址关联到区域
以下是将一个网段添加到 `home` 区域的命令：
```bash
firewall-cmd --permanent --add-source=192.168.145.0/24 --zone=home
firewall-cmd --reload
```
*   `--permanent` 使规则永久生效。
*   `--add-source` 添加数据源（IP 或网段）。
*   `--zone` 指定目标区域。
*   `--reload` 重新加载配置使其生效。

![](img/fe795e34b1251580e74064c35a1db551_45.png)

![](img/fe795e34b1251580e74064c35a1db551_47.png)

![](img/fe795e34b1251580e74064c35a1db551_49.png)

执行后，来自 `192.168.145.0/24` 网段的数据包将进入 `home` 区域并匹配该区域的规则。

![](img/fe795e34b1251580e74064c35a1db551_51.png)

![](img/fe795e34b1251580e74064c35a1db551_53.png)

以下是管理源地址的其他操作：

![](img/fe795e34b1251580e74064c35a1db551_55.png)

![](img/fe795e34b1251580e74064c35a1db551_57.png)

![](img/fe795e34b1251580e74064c35a1db551_58.png)

*   **列出区域内的所有源**：
    ```bash
    firewall-cmd --list-sources --zone=home
    ```

![](img/fe795e34b1251580e74064c35a1db551_59.png)

![](img/fe795e34b1251580e74064c35a1db551_60.png)

*   **查询源是否在某个区域内**：
    ```bash
    firewall-cmd --query-source=192.168.145.0/24 --zone=home
    ```
    命令返回 `yes` 或 `no`。

![](img/fe795e34b1251580e74064c35a1db551_62.png)

![](img/fe795e34b1251580e74064c35a1db551_64.png)

![](img/fe795e34b1251580e74064c35a1db551_66.png)

*   **将源更改到另一个区域**：
    ```bash
    firewall-cmd --permanent --change-source=192.168.145.0/24 --zone=public
    firewall-cmd --reload
    ```

![](img/fe795e34b1251580e74064c35a1db551_68.png)

### 管理网卡关联的区域
默认情况下，网卡必须属于一个区域。通常使用 `--change-interface` 来更改网卡所属区域，而不是添加。

![](img/fe795e34b1251580e74064c35a1db551_70.png)

![](img/fe795e34b1251580e74064c35a1db551_71.png)

![](img/fe795e34b1251580e74064c35a1db551_73.png)

*   **将网卡关联到指定区域**：
    ```bash
    firewall-cmd --permanent --change-interface=ens160 --zone=home
    firewall-cmd --reload
    ```

![](img/fe795e34b1251580e74064c35a1db551_74.png)

![](img/fe795e34b1251580e74064c35a1db551_76.png)

![](img/fe795e34b1251580e74064c35a1db551_78.png)

*   **将网卡从区域中移除并恢复至默认区域**：
    ```bash
    firewall-cmd --permanent --remove-interface=ens160 --zone=home
    # 或者更简单地，将其区域设置为默认值
    firewall-cmd --permanent --zone=home --remove-interface=ens160
    firewall-cmd --reload
    ```
    移除后，网卡会自动回到默认区域（如 `public`）。

![](img/fe795e34b1251580e74064c35a1db551_80.png)

![](img/fe795e34b1251580e74064c35a1db551_81.png)

**小结**：所有网卡默认都在默认区域内。如果想对不同源或网卡应用不同规则，就需要将它们分配到不同的区域中。

![](img/fe795e34b1251580e74064c35a1db551_83.png)

![](img/fe795e34b1251580e74064c35a1db551_85.png)

---

![](img/fe795e34b1251580e74064c35a1db551_87.png)

![](img/fe795e34b1251580e74064c35a1db551_89.png)

## 配置防火墙规则
我们通常直接使用 `firewall-cmd` 命令操作，而不是手动编辑配置文件。防火墙规则主要分为基本规则和富规则。

![](img/fe795e34b1251580e74064c35a1db551_91.png)

![](img/fe795e34b1251580e74064c35a1db551_93.png)

### 基本规则
基本规则允许基于源、服务或端口来放行流量，这类似于白名单模式：只有明确允许的流量才能通过。

![](img/fe795e34b1251580e74064c35a1db551_94.png)

以下是配置基本规则的几种方式：

![](img/fe795e34b1251580e74064c35a1db551_96.png)

*   **基于源地址放行**：允许特定网段的所有流量。
    ```bash
    firewall-cmd --permanent --add-source=192.168.146.0/24 --zone=trusted
    firewall-cmd --reload
    ```

*   **基于服务放行**：允许某个服务（如 `smtp`）的流量。
    ```bash
    firewall-cmd --permanent --add-service=smtp --zone=public
    firewall-cmd --reload
    ```

*   **基于端口放行**：允许特定端口（如 TCP 80 端口）的流量。
    ```bash
    firewall-cmd --permanent --add-port=80/tcp --zone=public
    firewall-cmd --reload
    ```
    注意：指定端口时必须注明协议（`tcp` 或 `udp`）。

### 富规则 (Rich Rules)
富规则提供了更强大的表达能力，用于实现基本语法无法涵盖的复杂防火墙规则。例如，仅允许单个 IP 而非整个网段访问特定服务。

富规则的基本语法结构为：
```
rule [family="ipv4|ipv6"]
     [source|destination address="address[/mask]"]
     [service name="service名"]
     [port port="端口号" protocol="tcp|udp"]
     [log|audit]
     [accept|reject|drop]
```

![](img/fe795e34b1251580e74064c35a1db551_98.png)

![](img/fe795e34b1251580e74064c35a1db551_100.png)

规则的处理顺序如下：
1.  为该区域设置的端口转发和伪装规则。
2.  为该区域的任何记录规则（log）。
3.  允许规则（accept）。
4.  拒绝规则（reject/drop）。

![](img/fe795e34b1251580e74064c35a1db551_102.png)

![](img/fe795e34b1251580e74064c35a1db551_104.png)

管理富规则的命令选项：
*   `--add-rich-rule`：添加富规则。
*   `--remove-rich-rule`：移除富规则。
*   `--query-rich-rule`：查询富规则是否存在。
*   `--list-rich-rules`：列出所有富规则。

![](img/fe795e34b1251580e74064c35a1db551_106.png)

**示例1：拒绝特定IP的SSH访问**
```bash
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.145.129" service name="ssh" reject'
firewall-cmd --reload
```
此规则拒绝了 IP `192.168.145.129` 的 SSH 连接。`reject` 会返回拒绝原因，而 `drop` 会直接丢弃数据包不作响应。通常对友好网络使用 `reject`，对恶意网络使用 `drop`。

**示例2：允许特定网段访问自定义端口**
```bash
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.145.0/24" port port="8080" protocol="tcp" accept'
firewall-cmd --reload
```
此规则允许 `192.168.145.0/24` 网段访问本机的 TCP 8080 端口。

![](img/fe795e34b1251580e74064c35a1db551_108.png)

要移除富规则，将 `--add-rich-rule` 替换为 `--remove-rich-rule` 并重新加载即可。

---

## 高级功能：伪装与端口转发
除了基本的允许和拒绝，防火墙还能实现更高级的网络功能。

### 伪装 (Masquerading)
伪装是一种网络地址转换（NAT）形式，用于隐藏内部网络的实际 IP 地址。例如，内网主机 `192.168.40.2` 访问外网时，其源地址会被伪装成防火墙的外网地址（如 `202.202.202.202`）。

启用伪装的命令：
```bash
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.40.0/24" masquerade'
firewall-cmd --reload
```

### 端口转发 (Port Forwarding)
端口转发将到达某个端口（转发端口）的流量重定向到另一个端口（目标端口）或另一台机器。这常用于隐藏真实服务端口，增强安全性。

![](img/fe795e34b1251580e74064c35a1db551_110.png)

![](img/fe795e34b1251580e74064c35a1db551_112.png)

**示例：将本机 5423 端口的流量转发到 80 端口**
```bash
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" forward-port port="5423" protocol="tcp" to-port="80"'
firewall-cmd --reload
```
配置后，访问本机的 5423 端口就相当于访问 80 端口。注意，需要确保转发端口（5423）在防火墙中是放行的。

端口转发与端口映射不同：转发是集中式处理（如保安分发快递），而映射是点到点对应（如每人一个快递柜）。

![](img/fe795e34b1251580e74064c35a1db551_114.png)

![](img/fe795e34b1251580e74064c35a1db551_116.png)

---

![](img/fe795e34b1251580e74064c35a1db551_118.png)

## 管理 SELinux 端口上下文
当使用非标准端口（如 8080 用于 HTTP）时，除了配置防火墙，还需要考虑 SELinux 的端口上下文。如果 SELinux 处于 enforcing 模式，即使防火墙放行，服务也可能因端口上下文不正确而无法访问。

*   **列出已定义的端口上下文**：
    ```bash
    semanage port -l | grep http
    ```
    这会列出所有被 SELinux 允许用于 HTTP 服务的端口（如 80, 443, 8000, 8080 等）。

![](img/fe795e34b1251580e74064c35a1db551_120.png)

*   **添加自定义端口到 HTTP 服务上下文**：
    ```bash
    semanage port -a -t http_port_t -p tcp 8909
    ```
    此命令将 TCP 8909 端口添加到 `http_port_t` 上下文中，允许 HTTP 服务绑定到此端口。
    *   `-a`：添加。
    *   `-t`：指定类型（`http_port_t`）。
    *   `-p`：指定协议。

![](img/fe795e34b1251580e74064c35a1db551_122.png)

*   **修改或移除端口上下文**：
    使用 `-m` 选项修改，使用 `-d` 选项删除。

---

## 总结
本节课我们一起学习了 RHEL 8 中 `firewalld` 防火墙的全面管理。

1.  **基础概念**：理解了防火墙区域的作用和默认区域（如 `public`）。
2.  **区域管理**：学会了如何将源 IP 地址或网络接口关联到不同的防火墙区域。
3.  **规则配置**：
    *   **基本规则**：掌握了基于源地址、预定义服务或特定端口来放行流量的方法。
    *   **富规则**：学习了使用更强大的富规则语法来实现复杂的过滤策略，如精确到 IP 的允许或拒绝。
4.  **高级功能**：了解了伪装（NAT）和端口转发的概念与配置方法，用于实现地址隐藏和服务端口隐藏。
5.  **SELinux 集成**：认识到在启用 SELinux 时，为自定义端口添加上下文是确保服务可访问的必要步骤。

![](img/fe795e34b1251580e74064c35a1db551_124.png)

![](img/fe795e34b1251580e74064c35a1db551_125.png)

通过本章的学习，你已具备使用 `firewall-cmd` 工具管理 Linux 系统网络安全的基本能力。