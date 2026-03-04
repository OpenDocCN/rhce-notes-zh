# RHCE8.0视频教程：P21：防火墙高级配置与管理

![](img/8f3655473ef5983b57c29509a9aafe21_0.png)

在本节课中，我们将深入学习Linux防火墙（firewalld）的高级配置，包括端口管理、ICMP协议控制、基于源地址的规则以及复杂的富规则（rich rule）应用。通过本课，你将掌握如何精确控制网络流量，以满足复杂的安全需求。

![](img/8f3655473ef5983b57c29509a9aafe21_2.png)

## 查看防火墙信息

![](img/8f3655473ef5983b57c29509a9aafe21_4.png)

上一节我们介绍了防火墙的基本概念和区域管理，本节中我们来看看如何查看详细的防火墙配置信息。

以下是查看防火墙信息的常用命令：

*   `firewall-cmd --list-all`：查看当前默认区域的所有配置（服务、端口、接口等）。
*   `firewall-cmd --get-default-zone`：查看系统当前的默认区域。
*   `firewall-cmd --get-zone-of-interface=<接口名>`：查看指定网络接口（如ens160）所属的区域。
*   `firewall-cmd --zone=<区域名> --list-all`：查看指定区域的详细配置规则。

## 服务与端口管理

![](img/8f3655473ef5983b57c29509a9aafe21_6.png)

![](img/8f3655473ef5983b57c29509a9aafe21_8.png)

防火墙的核心功能之一是控制对服务的访问。`firewalld`预定义了许多常见服务（如http、ssh），它们对应着标准的监听端口。

### 添加与移除服务

当我们需要允许外部访问某个服务时，可以将其添加到防火墙规则中。例如，允许HTTP服务：

```bash
# 临时添加HTTP服务（重启后失效）
firewall-cmd --add-service=http
# 永久添加HTTP服务
firewall-cmd --add-service=http --permanent
# 重载防火墙配置使永久规则生效
firewall-cmd --reload
```

移除服务的命令与之类似：

```bash
# 临时移除HTTP服务
firewall-cmd --remove-service=http
# 永久移除HTTP服务
firewall-cmd --remove-service=http --permanent
firewall-cmd --reload
```

### 管理非标准端口

如果服务使用的不是默认端口（例如，HTTP服务运行在8080端口），则不能直接添加服务，而需要添加具体的端口规则。

![](img/8f3655473ef5983b57c29509a9aafe21_10.png)

![](img/8f3655473ef5983b57c29509a9aafe21_12.png)

以下是管理特定端口的步骤：

![](img/8f3655473ef5983b57c29509a9aafe21_14.png)

![](img/8f3655473ef5983b57c29509a9aafe21_16.png)

1.  **修改服务配置**：首先，确保服务监听在目标端口（如8080）。以Apache HTTP服务为例，修改其配置文件 `/etc/httpd/conf/httpd.conf` 中的 `Listen` 指令。
2.  **重启服务**：`systemctl restart httpd`
3.  **验证端口**：使用 `netstat -tulnp | grep httpd` 或 `ss -tulnp | grep :8080` 确认服务已在8080端口监听。
4.  **添加端口规则**：在防火墙中开放该端口。
    ```bash
    # 临时添加8080/tcp端口
    firewall-cmd --add-port=8080/tcp
    # 永久添加
    firewall-cmd --add-port=8080/tcp --permanent
    firewall-cmd --reload
    ```
5.  **移除端口规则**：
    ```bash
    firewall-cmd --remove-port=8080/tcp
    ```

## ICMP协议控制

ICMP协议常用于网络诊断（如`ping`命令），但有时出于安全考虑，需要禁止外部主机对服务器执行ping操作。

![](img/8f3655473ef5983b57c29509a9aafe21_18.png)

![](img/8f3655473ef5983b57c29509a9aafe21_20.png)

`ping`命令的工作原理涉及两种ICMP报文：`Echo Request`（请求）和`Echo Reply`（回复）。要禁止被ping，最有效的方法是阻止`Echo Request`报文进入服务器。

以下是控制ICMP报文的命令：

```bash
# 阻止所有进入的Echo Request报文（实现禁ping）
firewall-cmd --add-icmp-block=echo-request
# 取消阻止
firewall-cmd --remove-icmp-block=echo-request

# 注意：阻止Echo Reply报文通常无效，因为它是出站响应。
# firewall-cmd --add-icmp-block=echo-reply
```

使用 `firewall-cmd --list-icmp-blocks` 可以查看当前被阻止的ICMP类型。

## 查询规则

![](img/8f3655473ef5983b57c29509a9aafe21_22.png)

![](img/8f3655473ef5983b57c29509a9aafe21_24.png)

当需要确认某个服务、端口或ICMP类型是否在规则中时，可以使用查询命令。

![](img/8f3655473ef5983b57c29509a9aafe21_26.png)

![](img/8f3655473ef5983b57c29509a9aafe21_28.png)

![](img/8f3655473ef5983b57c29509a9aafe21_30.png)

以下是常用的查询命令示例：

![](img/8f3655473ef5983b57c29509a9aafe21_32.png)

![](img/8f3655473ef5983b57c29509a9aafe21_33.png)

![](img/8f3655473ef5983b57c29509a9aafe21_34.png)

![](img/8f3655473ef5983b57c29509a9aafe21_36.png)

```bash
# 查询HTTP服务在public区域是否被允许
firewall-cmd --zone=public --query-service=http
# 查询8080/tcp端口在默认区域是否被允许
firewall-cmd --query-port=8080/tcp
# 查询echo-request是否被阻止
firewall-cmd --query-icmp-block=echo-request
```
命令返回 `yes` 表示允许/存在，`no` 表示不允许/不存在。

![](img/8f3655473ef5983b57c29509a9aafe21_38.png)

## 基于源地址的限制

除了基于服务和端口，防火墙还可以基于流量的源IP地址进行控制。

![](img/8f3655473ef5983b57c29509a9aafe21_40.png)

### 简单源地址规则

![](img/8f3655473ef5983b57c29509a9aafe21_42.png)

可以将一个源IP地址或网段与某个区域绑定，该区域的所有规则将应用于来自此源地址的流量。

```bash
# 将192.168.1.0/24网段的流量绑定到trusted区域（该区域默认允许所有流量）
firewall-cmd --zone=trusted --add-source=192.168.1.0/24
# 移除源地址绑定
firewall-cmd --zone=trusted --remove-source=192.168.1.0/24
```

### 富规则 (Rich Rules)

对于更复杂的场景（例如：允许A网段访问SSH，但拒绝B网段），需要使用功能更强大的**富规则**。富规则允许在单条规则中组合源地址、目标地址、服务、端口、动作等元素。

以下是添加富规则的示例：

```bash
# 允许192.168.2.0/24网段访问SSH服务
firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.2.0/24" service name="ssh" accept'
# 拒绝192.168.3.0/24网段访问SSH服务
firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.3.0/24" service name="ssh" reject'
# 查看所有富规则
firewall-cmd --list-rich-rules
# 移除富规则（需要完整复制规则内容）
firewall-cmd --remove-rich-rule='rule family="ipv4" source address="192.168.2.0/24" service name="ssh" accept'
```

**注意**：富规则的语法较为复杂，建议先通过 `--add-rich-rule` 测试，确认无误后再使用 `--permanent` 参数保存并重载。

## 接口与区域管理回顾

一个网络接口**只能属于一个区域**。如果接口不属于任何区域，它将遵循**默认区域**的规则。

以下是接口与区域管理的命令总结：

*   **修改接口所属区域**：`firewall-cmd --zone=<新区域> --change-interface=<接口名>`
*   **将接口添加到区域**（如果接口原本不属于任何区域）：`firewall-cmd --zone=<区域> --add-interface=<接口名>`
*   **将接口从区域中移除**：`firewall-cmd --zone=<区域> --remove-interface=<接口名>`
*   **设置默认区域**：`firewall-cmd --set-default-zone=<区域名>`

**重要原则**：使用 `--change-interface` 是将接口从一个区域移动到另一个区域。而试图使用 `--add-interface` 将一个已属于某区域的接口添加到另一个区域会导致错误。

---

![](img/8f3655473ef5983b57c29509a9aafe21_44.png)

本节课中我们一起学习了`firewalld`防火墙的高级配置。我们掌握了如何管理非标准端口、控制ICMP协议以增强安全性、查询现有规则，以及使用基于源地址的简单规则和功能强大的富规则来实现精细化的访问控制。最后，我们回顾并明确了网络接口与防火墙区域之间的关系及管理方法。这些技能对于构建安全、可控的Linux服务器网络环境至关重要。