# Linux运维RHCSA+RHC培训教程：P54：红帽RHCE-18.系统安全防护之iptables防火墙

在本节课中，我们将要学习iptables防火墙的基本概念和配置方法。iptables是Linux系统中一个功能强大的防火墙工具，它允许我们通过配置规则来控制网络数据包的流入和流出。我们将从安装服务开始，逐步学习如何查看、添加、删除规则，并理解防火墙规则匹配的核心机制。课程内容将涵盖主机型防火墙和网络型防火墙的配置，确保初学者能够掌握iptables的基础操作。

![](img/b22b0b778424416cca1eb7acfc7fc50a_1.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_3.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_4.png)

## 安装iptables服务

![](img/b22b0b778424416cca1eb7acfc7fc50a_6.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_8.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_10.png)

在开始配置iptables之前，我们需要先安装其服务。请注意，iptables与firewalld服务都是控制内核模块的，不能同时启用，否则会发生冲突。

![](img/b22b0b778424416cca1eb7acfc7fc50a_12.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_14.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_15.png)

以下是安装步骤：
1.  首先，停止并禁用firewalld服务。
2.  然后，使用yum命令安装iptables服务。

```bash
systemctl stop firewalld
systemctl disable firewalld
yum -y install iptables-services
systemctl start iptables
```

安装并启动服务后，我们就可以开始学习iptables的命令了。

![](img/b22b0b778424416cca1eb7acfc7fc50a_17.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_19.png)

## 查看防火墙规则

![](img/b22b0b778424416cca1eb7acfc7fc50a_21.png)

上一节我们安装了iptables服务，本节中我们来看看如何查看现有的防火墙规则。查看规则是管理防火墙的第一步。

![](img/b22b0b778424416cca1eb7acfc7fc50a_23.png)

以下是查看规则时常用的命令选项：
*   `-L`：列出指定链中的所有规则。
*   `-n`：以数字形式显示IP地址和端口号，使输出更易读。
*   `--line-numbers`：显示规则的行号，便于后续的规则管理。

![](img/b22b0b778424416cca1eb7acfc7fc50a_25.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_26.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_28.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_30.png)

通常，`-n`和`-L`选项会连用。需要注意的是，选项的顺序有要求，`-n`应放在`-L`左边。

![](img/b22b0b778424416cca1eb7acfc7fc50a_32.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_34.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_36.png)

```bash
iptables -nL
iptables -nL --line-numbers
```

默认情况下，`iptables -L`查看的是`filter`表的规则。iptables有多个内置表，如`nat`、`mangle`、`raw`。如果想查看其他表的规则，可以使用`-t`选项指定表名。

![](img/b22b0b778424416cca1eb7acfc7fc50a_38.png)

```bash
iptables -t nat -nL
iptables -t mangle -nL
```

如果不使用`-t`指定表名，则默认针对`filter`表执行操作，无论是查看、添加还是删除规则。

## 添加防火墙规则

![](img/b22b0b778424416cca1eb7acfc7fc50a_40.png)

了解了如何查看规则后，本节我们来学习如何添加防火墙规则。我们将以实现一个主机型防火墙为例，保护本机的服务。

首先，我们需要理解命令格式中的几个关键部分：
*   **添加规则的选项**：`-A`在链的末尾追加规则；`-I`在链的开头插入规则。
*   **链名**：例如`INPUT`（入站链）、`FORWARD`（转发链）、`OUTPUT`（出站链）。
*   **规则条件**：可以通过协议、IP地址、端口等来匹配数据包。
*   **目标操作**：对匹配的数据包执行的操作，如`ACCEPT`（允许）、`DROP`（丢弃）、`REJECT`（拒绝）。

![](img/b22b0b778424416cca1eb7acfc7fc50a_42.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_44.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_46.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_48.png)

防火墙规则有一个核心特性：**从上到下匹配，一旦匹配成功就停止**。这意味着规则的顺序至关重要。如果第一条规则允许了某个访问，那么即使后面有拒绝规则也不会生效。

现在，我们来实现第一个需求：拒绝所有ICMP协议（即ping）的访问。

```bash
iptables -I INPUT -p icmp -j REJECT
```

命令解读：
*   `-I INPUT`：在`INPUT`链的开头插入一条规则。
*   `-p icmp`：匹配协议为ICMP的数据包。
*   `-j REJECT`：执行“拒绝”操作，并向发送方返回拒绝信息。

![](img/b22b0b778424416cca1eb7acfc7fc50a_50.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_52.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_54.png)

执行后，使用`iptables -nL --line-numbers`查看，可以看到新规则位于`INPUT`链的第一条。此时，其他机器ping本机将会收到“目标端口不可达”的明确拒绝信息。

![](img/b22b0b778424416cca1eb7acfc7fc50a_56.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_58.png)

## 删除与清空防火墙规则

上一节我们学会了添加规则，本节中我们来看看如何删除不再需要的规则。管理规则是防火墙维护的日常工作。

![](img/b22b0b778424416cca1eb7acfc7fc50a_60.png)

删除规则主要使用以下两个选项：
*   `-D`：删除链内指定序号的一条规则。
*   `-F`：清空指定表的所有规则。

![](img/b22b0b778424416cca1eb7acfc7fc50a_62.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_64.png)

例如，要删除`INPUT`链中的第三条规则，可以执行：

![](img/b22b0b778424416cca1eb7acfc7fc50a_66.png)

```bash
iptables -D INPUT 3
```

![](img/b22b0b778424416cca1eb7acfc7fc50a_68.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_70.png)

如果要清空`filter`表的所有规则，可以执行：

![](img/b22b0b778424416cca1eb7acfc7fc50a_72.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_74.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_76.png)

```bash
iptables -F
```

![](img/b22b0b778424416cca1eb7acfc7fc50a_78.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_80.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_82.png)

如果想清空其他表（如`nat`表）的规则，则需要使用`-t`指定表名：

![](img/b22b0b778424416cca1eb7acfc7fc50a_84.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_86.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_88.png)

```bash
iptables -t nat -F
```

![](img/b22b0b778424416cca1eb7acfc7fc50a_90.png)

## 防火墙的默认规则

![](img/b22b0b778424416cca1eb7acfc7fc50a_92.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_93.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_95.png)

在清空所有规则后，你可能会发现某些服务仍然可以访问。这是因为每个链都有一个“默认规则”（Policy）。当数据包不匹配任何一条显式规则时，就会执行默认规则。

![](img/b22b0b778424416cca1eb7acfc7fc50a_97.png)

使用`iptables -nL`命令查看时，`Chain INPUT (policy ACCEPT)`表示`INPUT`链的默认规则是`ACCEPT`（允许）。`FORWARD`和`OUTPUT`链同理。

![](img/b22b0b778424416cca1eb7acfc7fc50a_99.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_101.png)

默认规则是可以修改的，但需谨慎操作。例如，如果将`INPUT`链的默认规则改为`DROP`，那么所有未被显式允许的入站连接都会被丢弃。在修改前，通常需要先放行管理端口（如SSH的22端口），以免将自己锁在服务器外。

![](img/b22b0b778424416cca1eb7acfc7fc50a_103.png)

设置默认规则使用`-P`选项：

![](img/b22b0b778424416cca1eb7acfc7fc50a_105.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_107.png)

```bash
# 先放行SSH端口，保证远程连接
iptables -I INPUT -p tcp --dport 22 -j ACCEPT
# 再将INPUT链的默认规则改为DROP
iptables -P INPUT DROP
```

![](img/b22b0b778424416cca1eb7acfc7fc50a_109.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_111.png)

注意：默认规则不能设置为`REJECT`，但可以设置为`DROP`或`ACCEPT`。如果需要恢复默认允许，可以执行：

![](img/b22b0b778424416cca1eb7acfc7fc50a_113.png)

```bash
iptables -P INPUT ACCEPT
```

![](img/b22b0b778424416cca1eb7acfc7fc50a_115.png)

## 配置规则实例：拒绝访问特定服务

![](img/b22b0b778424416cca1eb7acfc7fc50a_117.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_119.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_121.png)

掌握了规则的基本操作后，我们来看一些更具体的配置实例。本节将演示如何拒绝所有对80端口（HTTP网站服务）的访问。

![](img/b22b0b778424416cca1eb7acfc7fc50a_123.png)

实现这个需求，我们需要匹配目标端口。规则条件中，`--dport`用于指定目标端口。

![](img/b22b0b778424416cca1eb7acfc7fc50a_125.png)

```bash
iptables -I INPUT -p tcp --dport 80 -j REJECT
```

![](img/b22b0b778424416cca1eb7acfc7fc50a_127.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_129.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_131.png)

命令解读：
*   `-p tcp`：匹配TCP协议（因为HTTP服务基于TCP连接）。
*   `--dport 80`：匹配目标端口为80的数据包。
*   `-j REJECT`：拒绝连接。

![](img/b22b0b778424416cca1eb7acfc7fc50a_132.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_134.png)

配置完成后，访问本机的网站服务将会失败，但其他服务（如ping）不受影响。**这里的关键在于指定了`--dport 80`**。如果只指定`-p tcp`而不指定端口，则会拒绝所有TCP连接，包括SSH，这通常不是我们想要的。

## 配置规则实例：拒绝特定IP地址

![](img/b22b0b778424416cca1eb7acfc7fc50a_136.png)

有时，我们可能需要针对某个特定的IP地址进行限制，而不是针对所有用户。例如，阻止一个有恶意行为的IP访问我们的网站。

![](img/b22b0b778424416cca1eb7acfc7fc50a_138.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_140.png)

这需要使用`-s`选项来指定源IP地址。

![](img/b22b0b778424416cca1eb7acfc7fc50a_142.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_143.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_145.png)

```bash
iptables -I INPUT -s 192.168.0.24 -p tcp --dport 80 -j REJECT
```

![](img/b22b0b778424416cca1eb7acfc7fc50a_147.png)

命令解读：
*   `-s 192.168.0.24`：匹配源IP地址为192.168.0.24的数据包。
*   `-p tcp --dport 80`：匹配访问80端口的TCP数据包。
*   `-j REJECT`：拒绝。

![](img/b22b0b778424416cca1eb7acfc7fc50a_149.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_151.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_153.png)

这样配置后，只有IP地址为192.168.0.24的客户端无法访问网站，其他客户端的访问不受影响。我们也可以使用CIDR格式来拒绝整个网段，例如`-s 192.168.1.0/24`。

![](img/b22b0b778424416cca1eb7acfc7fc50a_154.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_155.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_157.png)

## 配置网络型防火墙

![](img/b22b0b778424416cca1eb7acfc7fc50a_159.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_160.png)

之前我们配置的都是主机型防火墙，用于保护本机。本节我们将学习配置网络型防火墙，用于保护内部网络中的其他服务器。你可以将网络型防火墙理解为一台具有包过滤功能的路由器。

![](img/b22b0b778424416cca1eb7acfc7fc50a_162.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_164.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_166.png)

为了演示，我们需要准备三台虚拟机：
1.  **客户端 (Client)**：IP为192.168.1.2，模拟外部用户。
2.  **防火墙 (iptables)**：需要两张网卡。
    *   外网卡 (ens34)：IP为192.168.1.1，与客户端通信。
    *   内网卡 (ens32)：IP为192.168.0.80，与内部服务器通信。
3.  **网站服务器 (Web)**：IP为192.168.0.26，提供网站服务。

![](img/b22b0b778424416cca1eb7acfc7fc50a_168.png)

**核心步骤：开启路由转发**
网络型防火墙需要转发数据包，因此必须开启Linux内核的路由转发功能。

![](img/b22b0b778424416cca1eb7acfc7fc50a_170.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_172.png)

```bash
echo “net.ipv4.ip_forward=1” >> /etc/sysctl.conf
sysctl -p
# 验证是否开启
cat /proc/sys/net/ipv4/ip_forward
```

![](img/b22b0b778424416cca1eb7acfc7fc50a_174.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_176.png)

**配置转发规则**
我们的目标是让客户端能访问内部的网站服务器。这需要在防火墙的`FORWARD`链上配置规则。

![](img/b22b0b778424416cca1eb7acfc7fc50a_178.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_180.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_182.png)

```bash
# 允许转发从外网到内网网站80端口的数据包
iptables -I FORWARD -s 192.168.1.0/24 -d 192.168.0.26 -p tcp --dport 80 -j ACCEPT
# 允许转发网站服务器返回给客户端的响应数据包
iptables -I FORWARD -d 192.168.1.0/24 -s 192.168.0.26 -p tcp --sport 80 -j ACCEPT
```

![](img/b22b0b778424416cca1eb7acfc7fc50a_184.png)

**设置客户端网关**
最后，需要将客户端的默认网关设置为防火墙的外网IP地址（192.168.1.1），这样客户端的请求才会被发送到防火墙进行转发。

![](img/b22b0b778424416cca1eb7acfc7fc50a_186.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_187.png)

![](img/b22b0b778424416cca1eb7acfc7fc50a_188.png)

本节课中我们一起学习了iptables防火墙从基础到应用的完整配置流程。我们从安装服务开始，学习了查看、添加、删除规则的命令，并深入理解了规则匹配的“从上到下，匹配即停止”原则以及默认规则的作用。通过实例，我们掌握了如何配置主机型防火墙来保护本机服务，以及如何配置网络型防火墙来保护内部网络。记住，防火墙规则的管理需要谨慎，错误的规则可能导致服务中断。多加练习，你就能熟练运用iptables来构建安全的网络环境。