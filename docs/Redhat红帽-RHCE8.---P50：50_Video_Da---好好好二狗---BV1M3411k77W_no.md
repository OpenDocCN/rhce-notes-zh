# Redhat红帽 RHCE8.0认证体系课程：P50：管理网络安全 🔒

在本节课中，我们将要学习Linux系统中的防火墙管理，这是保障系统网络安全的核心技能。我们将了解防火墙的基本概念、区域管理、规则配置以及高级功能，如富规则和端口转发。

## 防火墙基础概念

![](img/00c106272be475a2ac60df2647b98a59_1.png)

![](img/00c106272be475a2ac60df2647b98a59_3.png)

![](img/00c106272be475a2ac60df2647b98a59_5.png)

防火墙规则用于控制网络流量，例如允许特定IP地址访问服务。在Linux中，由`netfilter`组件执行数据包过滤操作。要配置防火墙，需要掌握两点：如何编写规则以及如何匹配规则。

![](img/00c106272be475a2ac60df2647b98a59_6.png)

![](img/00c106272be475a2ac60df2647b98a59_8.png)

![](img/00c106272be475a2ac60df2647b98a59_10.png)

防火墙包含多个区域，数据包会根据其来源地址或进入的网卡被关联到特定区域，并在该区域内匹配规则。这与物理防火墙的逻辑类似。

![](img/00c106272be475a2ac60df2647b98a59_12.png)

![](img/00c106272be475a2ac60df2647b98a59_13.png)

![](img/00c106272be475a2ac60df2647b98a59_15.png)

![](img/00c106272be475a2ac60df2647b98a59_17.png)

## 区域管理

![](img/00c106272be475a2ac60df2647b98a59_18.png)

![](img/00c106272be475a2ac60df2647b98a59_20.png)

通过划分不同区域，可以为不同网段配置不同的防火墙规则。在配置防火墙规则前，需要先配置好源地址和网卡与区域的关联。

![](img/00c106272be475a2ac60df2647b98a59_22.png)

默认情况下，所有网卡都属于`public`区域。RHEL 7有9个区域，RHEL 8增加了一个`libvirt`区域（供虚拟机专用）。以下是部分默认区域及其策略：
*   `trusted`: 允许所有传输流量，表示完全信任。
*   `block`: 拒绝所有传入流量，除非与传出流量相关。
*   `dmz`: 用于隔离外部网络（非受信区域）。
*   `home` / `internal`: 用于家庭或内部网络，信任度较高。
*   `work`: 用于工作场所网络。
*   `public`: 通用公共区域，默认只允许SSH和DHCPv6-client等服务，拒绝其他所有传入连接。

`firewalld`是RHEL 8默认的防火墙服务，它相比旧版本（如`iptables`服务）功能更强大，支持更灵活的规则匹配。

![](img/00c106272be475a2ac60df2647b98a59_24.png)

## 常用命令与操作

![](img/00c106272be475a2ac60df2647b98a59_26.png)

![](img/00c106272be475a2ac60df2647b98a59_27.png)

![](img/00c106272be475a2ac60df2647b98a59_29.png)

以下是使用`firewall-cmd`命令进行防火墙管理的基础操作。

![](img/00c106272be475a2ac60df2647b98a59_31.png)

![](img/00c106272be475a2ac60df2647b98a59_33.png)

![](img/00c106272be475a2ac60df2647b98a59_34.png)

![](img/00c106272be475a2ac60df2647b98a59_36.png)

### 查看区域与默认配置

![](img/00c106272be475a2ac60df2647b98a59_38.png)

![](img/00c106272be475a2ac60df2647b98a59_40.png)

![](img/00c106272be475a2ac60df2647b98a59_42.png)

列出所有区域及其详细信息：
```bash
firewall-cmd --list-all-zones
```
获取当前默认区域：
```bash
firewall-cmd --get-default-zone
```
通常，在没有特殊说明的情况下，所有服务都放置在`public`区域。

![](img/00c106272be475a2ac60df2647b98a59_44.png)

![](img/00c106272be475a2ac60df2647b98a59_46.png)

![](img/00c106272be475a2ac60df2647b98a59_48.png)

![](img/00c106272be475a2ac60df2647b98a59_49.png)

![](img/00c106272be475a2ac60df2647b98a59_50.png)

### 管理预定义服务

![](img/00c106272be475a2ac60df2647b98a59_52.png)

![](img/00c106272be475a2ac60df2647b98a59_54.png)

![](img/00c106272be475a2ac60df2647b98a59_56.png)

![](img/00c106272be475a2ac60df2647b98a59_57.png)

![](img/00c106272be475a2ac60df2647b98a59_59.png)

列出所有预定义的服务（这些服务对应了特定的协议和端口）：
```bash
firewall-cmd --get-services
```
预定义服务的配置文件位于`/usr/lib/firewalld/services/`目录下，每个文件对应一个网络服务。在RHEL 8.0中，已有超过600项预定义服务。

![](img/00c106272be475a2ac60df2647b98a59_61.png)

![](img/00c106272be475a2ac60df2647b98a59_63.png)

如果预定义服务不满足需求，可以将自定义的`.service`文件放置在`/etc/firewalld/services/`目录下。使用服务名称管理规则更加人性化和高效。

![](img/00c106272be475a2ac60df2647b98a59_65.png)

![](img/00c106272be475a2ac60df2647b98a59_67.png)

![](img/00c106272be475a2ac60df2647b98a59_69.png)

![](img/00c106272be475a2ac60df2647b98a59_71.png)

![](img/00c106272be475a2ac60df2647b98a59_73.png)

## 管理源地址与网卡

![](img/00c106272be475a2ac60df2647b98a59_74.png)

![](img/00c106272be475a2ac60df2647b98a59_76.png)

![](img/00c106272be475a2ac60df2647b98a59_78.png)

![](img/00c106272be475a2ac60df2647b98a59_79.png)

上一节我们介绍了区域的概念，本节中我们来看看如何将具体的源地址或网卡关联到特定区域。

![](img/00c106272be475a2ac60df2647b98a59_81.png)

![](img/00c106272be475a2ac60df2647b98a59_83.png)

![](img/00c106272be475a2ac60df2647b98a59_84.png)

![](img/00c106272be475a2ac60df2647b98a59_86.png)

![](img/00c106272be475a2ac60df2647b98a59_88.png)

### 关联源地址到区域

![](img/00c106272be475a2ac60df2647b98a59_90.png)

![](img/00c106272be475a2ac60df2647b98a59_91.png)

![](img/00c106272be475a2ac60df2647b98a59_92.png)

![](img/00c106272be475a2ac60df2647b98a59_93.png)

将特定源IP段（如`192.168.145.0/24`）添加到`home`区域：
```bash
firewall-cmd --permanent --add-source=192.168.145.0/24 --zone=home
firewall-cmd --reload
```
*   `--permanent`: 使规则永久生效。
*   `--add-source`: 添加数据源（IP地址/段）。
*   `--zone`: 指定目标区域。
*   `--reload`: 重新加载防火墙配置，使更改生效。

![](img/00c106272be475a2ac60df2647b98a59_95.png)

![](img/00c106272be475a2ac60df2647b98a59_97.png)

![](img/00c106272be475a2ac60df2647b98a59_98.png)

执行后，来自`192.168.145.0/24`网段的数据包将进入`home`区域匹配规则。

![](img/00c106272be475a2ac60df2647b98a59_100.png)

![](img/00c106272be475a2ac60df2647b98a59_102.png)

查询`home`区域的所有源地址：
```bash
firewall-cmd --list-sources --zone=home
```
查询某个源地址是否在特定区域内：
```bash
firewall-cmd --query-source=192.168.145.0/24 --zone=home
```
更改源地址到另一个区域（如`public`）：
```bash
firewall-cmd --permanent --change-source=192.168.145.0/24 --zone=public
firewall-cmd --reload
```
从区域中移除源地址（使其恢复到默认区域）：
```bash
firewall-cmd --permanent --remove-source=192.168.145.0/24 --zone=public
firewall-cmd --reload
```

![](img/00c106272be475a2ac60df2647b98a59_104.png)

![](img/00c106272be475a2ac60df2647b98a59_106.png)

![](img/00c106272be475a2ac60df2647b98a59_107.png)

![](img/00c106272be475a2ac60df2647b98a59_109.png)

![](img/00c106272be475a2ac60df2647b98a59_110.png)

### 管理网卡到区域

![](img/00c106272be475a2ac60df2647b98a59_112.png)

![](img/00c106272be475a2ac60df2647b98a59_114.png)

![](img/00c106272be475a2ac60df2647b98a59_116.png)

查询网卡关联的区域：
```bash
firewall-cmd --list-all
```
更改网卡（如`ens160`）到`home`区域：
```bash
firewall-cmd --permanent --change-interface=ens160 --zone=home
firewall-cmd --reload
```
将网卡从指定区域移除，恢复到默认区域：
```bash
firewall-cmd --permanent --remove-interface=ens160 --zone=home
firewall-cmd --reload
```
也可以直接将网卡添加到指定区域：
```bash
firewall-cmd --permanent --add-interface=ens160 --zone=work
firewall-cmd --reload
```
**小结**：在正常情况下，若无需区分策略，所有网卡可置于默认区域（如`public`）。若需分开设置，则需将数据源或网卡分配到对应的区域。

![](img/00c106272be475a2ac60df2647b98a59_118.png)

![](img/00c106272be475a2ac60df2647b98a59_120.png)

![](img/00c106272be475a2ac60df2647b98a59_122.png)

![](img/00c106272be475a2ac60df2647b98a59_124.png)

## 配置防火墙规则

![](img/00c106272be475a2ac60df2647b98a59_126.png)

![](img/00c106272be475a2ac60df2647b98a59_128.png)

我们通常使用`firewall-cmd`命令直接操作，避免手动编辑复杂配置文件。

![](img/00c106272be475a2ac60df2647b98a59_130.png)

![](img/00c106272be475a2ac60df2647b98a59_131.png)

![](img/00c106272be475a2ac60df2647b98a59_133.png)

### 基本规则

![](img/00c106272be475a2ac60df2647b98a59_135.png)

![](img/00c106272be475a2ac60df2647b98a59_137.png)

![](img/00c106272be475a2ac60df2647b98a59_138.png)

基本规则操作包括添加源地址、服务或端口。

![](img/00c106272be475a2ac60df2647b98a59_139.png)

![](img/00c106272be475a2ac60df2647b98a59_141.png)

添加新的源网段到`public`区域（相当于白名单）：
```bash
firewall-cmd --permanent --add-source=192.168.146.0/24 --zone=public
firewall-cmd --reload
```
添加预定义服务（如`SMTP`）到`public`区域：
```bash
firewall-cmd --permanent --add-service=smtp --zone=public
firewall-cmd --reload
```
添加特定端口（如TCP 80端口）到`public`区域：
```bash
firewall-cmd --permanent --add-port=80/tcp --zone=public
firewall-cmd --reload
```
> 注意：指定端口时必须注明协议（`tcp`或`udp`）。

### 富规则 (Rich Rules)

富规则提供了更精细、更强大的控制能力，可以表达基本语法未涵盖的复杂防火墙规则。例如，仅允许单个IP而非整个网段访问特定服务。

![](img/00c106272be475a2ac60df2647b98a59_143.png)

![](img/00c106272be475a2ac60df2647b98a59_145.png)

![](img/00c106272be475a2ac60df2647b98a59_146.png)

富规则的基本语法结构为：
```
rule [family="ipv4|ipv6"]
     [source|destination address="address[/mask]"]
     [service name="service name"]
     [port port="number" protocol="tcp|udp"]
     [log|audit]
     [accept|reject|drop]
```
更多详细信息可通过`man 5 firewalld.richlanguage`查看。

![](img/00c106272be475a2ac60df2647b98a59_148.png)

![](img/00c106272be475a2ac60df2647b98a59_150.png)

![](img/00c106272be475a2ac60df2647b98a59_152.png)

![](img/00c106272be475a2ac60df2647b98a59_153.png)

富规则的执行顺序为：
1.  为该区域设置的端口转发及伪装规则。
2.  为该区域的任何记录规则。
3.  允许 (`accept`) 规则。
4.  拒绝 (`reject`) 或丢弃 (`drop`) 规则。

![](img/00c106272be475a2ac60df2647b98a59_155.png)

![](img/00c106272be475a2ac60df2647b98a59_156.png)

![](img/00c106272be475a2ac60df2647b98a59_158.png)

![](img/00c106272be475a2ac60df2647b98a59_160.png)

管理富规则的命令选项：
*   `--add-rich-rule`: 添加富规则。
*   `--remove-rich-rule`: 移除富规则。
*   `--query-rich-rule`: 查询富规则是否存在。
*   所有配置的富规则会在`--list-all`或`--list-all-zones`的输出中显示。

![](img/00c106272be475a2ac60df2647b98a59_162.png)

![](img/00c106272be475a2ac60df2647b98a59_164.png)

**示例1：拒绝特定IP的SSH访问**
拒绝IP `192.168.145.129` 的SSH连接：
```bash
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.145.129" service name="ssh" reject'
firewall-cmd --reload
```
要撤销此规则，将`--add-rich-rule`改为`--remove-rich-rule`即可。

![](img/00c106272be475a2ac60df2647b98a59_166.png)

**`reject` 与 `drop` 的区别**：
*   `reject`: 会返回一个ICMP错误包，告知对方连接被拒绝。
*   `drop`: 直接丢弃数据包，不做任何响应。对于友好网络可使用`reject`，对于恶意网络建议使用`drop`。

![](img/00c106272be475a2ac60df2647b98a59_168.png)

![](img/00c106272be475a2ac60df2647b98a59_170.png)

**示例2：允许特定网段访问非标准端口**
允许`192.168.145.0/24`网段访问本机的TCP 8080端口：
```bash
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.145.0/24" port port="8080" protocol="tcp" accept'
firewall-cmd --reload
```

## 高级功能：伪装与端口转发

### 端口伪装 (Masquerading)

伪装是一种网络地址转换(NAT)形式，用于隐藏内部网络的实际IP地址。例如，内网主机`192.168.40.2`访问外网时，其源地址被伪装成网关的公网IP（如`202.202.202.202`）。

![](img/00c106272be475a2ac60df2647b98a59_172.png)

![](img/00c106272be475a2ac60df2647b98a59_174.png)

![](img/00c106272be475a2ac60df2647b98a59_176.png)

为区域启用伪装：
```bash
firewall-cmd --permanent --add-masquerade --zone=public
firewall-cmd --reload
```

![](img/00c106272be475a2ac60df2647b98a59_178.png)

### 端口转发 (Port Forwarding)

![](img/00c106272be475a2ac60df2647b98a59_180.png)

![](img/00c106272be475a2ac60df2647b98a59_182.png)

端口转发将到达某个端口（备用端口）的流量转发到另一个端口（通常是服务真实端口）或另一台机器，常用于隐藏真实服务端口，增强安全性。

![](img/00c106272be475a2ac60df2647b98a59_184.png)

**示例：将5423端口转发到本机80端口**
允许`192.168.145.0/24`网段访问5423端口，并将其流量转发到本机的80端口：
```bash
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.145.0/24" forward-port port="5423" protocol="tcp" to-port="80"'
firewall-cmd --reload
```
配置后，访问`192.168.145.128:5423`即相当于访问该主机的80端口。

![](img/00c106272be475a2ac60df2647b98a59_186.png)

## 管理SELinux端口上下文

![](img/00c106272be475a2ac60df2647b98a59_188.png)

![](img/00c106272be475a2ac60df2647b98a59_190.png)

![](img/00c106272be475a2ac60df2647b98a59_191.png)

当为服务使用非标准端口（如将HTTP服务运行在8080端口）时，除了配置防火墙，还需注意SELinux的端口上下文。如果端口未被SELinux策略允许，服务可能仍无法访问。

![](img/00c106272be475a2ac60df2647b98a59_193.png)

![](img/00c106272be475a2ac60df2647b98a59_194.png)

![](img/00c106272be475a2ac60df2647b98a59_196.png)

列出所有预定义的端口上下文：
```bash
semanage port -l
```
为TCP协议的8909端口添加`http_port_t`类型上下文，以便HTTPD服务可以绑定到此端口：
```bash
semanage port -a -t http_port_t -p tcp 8909
```
*   `-a`: 添加。
*   `-t`: 类型。
*   `-p`: 协议。

---

![](img/00c106272be475a2ac60df2647b98a59_198.png)

![](img/00c106272be475a2ac60df2647b98a59_199.png)

![](img/00c106272be475a2ac60df2647b98a59_200.png)

本节课中我们一起学习了RHEL 8中`firewalld`防火墙的全面管理。我们从基础概念和区域管理入手，逐步掌握了如何配置基本规则和更强大的富规则，以实现精细的流量控制。最后，我们还探讨了端口伪装、转发等高级功能以及SELinux端口上下文的配置。这些技能是构建安全Linux系统环境的重要组成部分。