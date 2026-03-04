# Linux运维培训教程：P53：红帽RHCE-18.系统安全防护之iptables防火墙

![](img/e05fa1fe63d3b543013b9aac28f81414_1.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_3.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_4.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_6.png)

在本节课中，我们将要学习iptables防火墙的基本概念和操作。iptables是Linux系统中一个功能强大的防火墙工具，用于配置和管理网络数据包的过滤规则。我们将从安装iptables开始，逐步学习如何查看、添加、删除规则，并理解防火墙规则匹配的核心机制。通过本节课的学习，你将能够使用iptables配置主机型和网络型防火墙，实现对网络访问的基本控制。

![](img/e05fa1fe63d3b543013b9aac28f81414_8.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_10.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_12.png)

## 安装与启动iptables服务

![](img/e05fa1fe63d3b543013b9aac28f81414_14.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_16.png)

在开始配置iptables之前，需要先安装其服务。由于iptables与firewalld服务都用于控制内核的netfilter模块，因此它们不能同时运行。在安装iptables前，需要先停止并禁用firewalld服务。

![](img/e05fa1fe63d3b543013b9aac28f81414_18.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_20.png)

以下是安装和启动iptables的步骤：

![](img/e05fa1fe63d3b543013b9aac28f81414_22.png)

1.  停止并禁用firewalld服务。
    ```bash
    systemctl stop firewalld
    systemctl disable firewalld
    ```
2.  使用yum包管理器安装iptables服务。
    ```bash
    yum install -y iptables-services
    ```
3.  启动iptables服务并设置为开机自启。
    ```bash
    systemctl start iptables
    systemctl enable iptables
    ```

![](img/e05fa1fe63d3b543013b9aac28f81414_24.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_26.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_28.png)

## 查看防火墙规则

![](img/e05fa1fe63d3b543013b9aac28f81414_30.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_32.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_34.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_36.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_38.png)

安装并启动服务后，我们可以使用`iptables`命令来查看当前的防火墙规则。在查看规则时，有几个常用的选项可以帮助我们更清晰地理解规则内容。

以下是查看规则时常用的命令选项：

![](img/e05fa1fe63d3b543013b9aac28f81414_40.png)

*   `-L`：列出指定链中的所有规则。
*   `-n`：以数字形式显示IP地址和端口号，而不是尝试解析为主机名或服务名。
*   `--line-numbers`：显示规则的行号，便于后续对特定规则进行操作。

![](img/e05fa1fe63d3b543013b9aac28f81414_42.png)

默认情况下，`iptables -L`命令查看的是`filter`（过滤）表中的规则。如果想查看其他表（如`nat`、`mangle`、`raw`）的规则，需要使用`-t`选项指定表名。

![](img/e05fa1fe63d3b543013b9aac28f81414_44.png)

例如，查看`filter`表所有规则（带行号和数字格式）：
```bash
iptables -L -n --line-numbers
```
查看`nat`表的规则：
```bash
iptables -t nat -L -n
```

![](img/e05fa1fe63d3b543013b9aac28f81414_46.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_48.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_50.png)

## 配置防火墙规则：添加与删除

上一节我们介绍了如何查看规则，本节中我们来看看如何添加和删除防火墙规则。这是iptables防火墙配置的核心部分。

![](img/e05fa1fe63d3b543013b9aac28f81414_52.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_54.png)

### 添加规则

![](img/e05fa1fe63d3b543013b9aac28f81414_56.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_58.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_60.png)

添加规则主要使用`-A`（追加到链末尾）和`-I`（插入到链开头）选项。规则通常由匹配条件（如协议、IP地址、端口）和目标动作（如允许`ACCEPT`、拒绝`REJECT`、丢弃`DROP`）组成。

![](img/e05fa1fe63d3b543013b9aac28f81414_62.png)

防火墙规则有一个非常重要的特性：**从上到下匹配，匹配即停止**。这意味着数据包会按顺序与规则进行比对，一旦匹配到某条规则，就会执行该规则定义的动作，后续规则不再检查。因此，规则的顺序至关重要。

![](img/e05fa1fe63d3b543013b9aac28f81414_64.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_66.png)

例如，我们想拒绝所有ICMP协议（即ping请求）访问本机。由于希望这条规则优先生效，我们使用`-I`选项将其插入到`INPUT`链的开头。

![](img/e05fa1fe63d3b543013b9aac28f81414_68.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_70.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_72.png)

```bash
iptables -I INPUT -p icmp -j REJECT
```
*   `-I INPUT`：在`INPUT`链的开头插入规则。
*   `-p icmp`：匹配条件为ICMP协议。
*   `-j REJECT`：目标动作为拒绝，并向发送方返回拒绝信息。

![](img/e05fa1fe63d3b543013b9aac28f81414_74.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_76.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_78.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_80.png)

执行后，其他主机将无法ping通本机。

![](img/e05fa1fe63d3b543013b9aac28f81414_82.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_84.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_85.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_87.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_89.png)

### 删除规则

![](img/e05fa1fe63d3b543013b9aac28f81414_91.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_93.png)

删除规则使用`-D`（删除指定规则）和`-F`（清空所有规则）选项。

![](img/e05fa1fe63d3b543013b9aac28f81414_95.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_97.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_99.png)

若要删除`INPUT`链中的第三条规则，可以先使用`--line-numbers`查看行号，然后执行：
```bash
iptables -D INPUT 3
```
若要清空`filter`表的所有规则，可以执行：
```bash
iptables -F
```
若要清空其他表（如`nat`表）的规则，则需要指定表名：
```bash
iptables -t nat -F
```

![](img/e05fa1fe63d3b543013b9aac28f81414_101.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_103.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_105.png)

## 防火墙的默认规则

![](img/e05fa1fe63d3b543013b9aac28f81414_107.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_109.png)

当数据包与链中的所有规则都不匹配时，防火墙会执行该链的**默认规则**（Policy）。我们可以使用`iptables -L`命令查看每个链的默认规则，通常初始状态为`ACCEPT`（允许）。

![](img/e05fa1fe63d3b543013b9aac28f81414_111.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_113.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_115.png)

默认规则也可以修改，使用`-P`选项。例如，将`INPUT`链的默认规则改为`DROP`（丢弃）：
```bash
iptables -P INPUT DROP
```
**注意**：在将默认规则改为`DROP`之前，务必先添加允许远程管理（如SSH端口22）的规则，否则可能导致无法远程连接服务器。
```bash
iptables -I INPUT -p tcp --dport 22 -j ACCEPT
```

![](img/e05fa1fe63d3b543013b9aac28f81414_117.png)

## 主机型防火墙配置实例

![](img/e05fa1fe63d3b543013b9aac28f81414_119.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_121.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_123.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_125.png)

主机型防火墙主要保护本机服务，规则配置在`INPUT`链上。下面通过几个常见需求来实践规则配置。

![](img/e05fa1fe63d3b543013b9aac28f81414_127.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_129.png)

### 拒绝访问特定服务

![](img/e05fa1fe63d3b543013b9aac28f81414_131.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_133.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_135.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_137.png)

假设我们运行了Web服务（端口80），现在想拒绝所有对该服务的访问。
```bash
iptables -I INPUT -p tcp --dport 80 -j REJECT
```
*   `-p tcp`：匹配TCP协议。
*   `--dport 80`：匹配目标端口为80。

### 拒绝特定IP地址访问

![](img/e05fa1fe63d3b543013b9aac28f81414_139.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_141.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_143.png)

如果只想拒绝某个特定IP地址（例如192.168.0.24）访问本机的Web服务，可以添加源IP地址作为匹配条件。
```bash
iptables -I INPUT -s 192.168.0.24 -p tcp --dport 80 -j REJECT
```
*   `-s 192.168.0.24`：匹配源IP地址为192.168.0.24。

![](img/e05fa1fe63d3b543013b9aac28f81414_145.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_147.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_149.png)

### 拒绝整个网段访问

![](img/e05fa1fe63d3b543013b9aac28f81414_151.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_153.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_155.png)

同样，可以拒绝整个网段的访问。例如，拒绝192.168.1.0/24网段的所有主机。
```bash
iptables -I INPUT -s 192.168.1.0/24 -p tcp --dport 80 -j REJECT
```

![](img/e05fa1fe63d3b543013b9aac28f81414_157.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_158.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_159.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_161.png)

## 网络型防火墙配置准备

![](img/e05fa1fe63d3b543013b9aac28f81414_163.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_165.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_167.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_169.png)

网络型防火墙用于保护内部网络中的其他服务器，它充当路由器的角色，对经过的数据包进行过滤，规则主要配置在`FORWARD`链上。

![](img/e05fa1fe63d3b543013b9aac28f81414_171.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_173.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_175.png)

要配置网络型防火墙，需要准备至少三台虚拟机来模拟环境：
1.  **客户端 (Client)**：IP地址为192.168.1.24，模拟外部访问者。
2.  **防火墙 (iptables)**：需要两张网卡。
    *   外网卡 (ens34)：IP地址为192.168.1.80，与客户端通信。
    *   内网卡 (ens32)：IP地址为192.168.0.80，与内部服务器通信。
3.  **内部Web服务器 (Web)**：IP地址为192.168.0.26，运行Web服务。

![](img/e05fa1fe63d3b543013b9aac28f81414_177.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_179.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_181.png)

在防火墙服务器上，必须开启Linux内核的IP转发功能，否则数据包无法在不同网卡间转发。
```bash
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
sysctl -p
```
执行后，可以检查是否生效：
```bash
cat /proc/sys/net/ipv4/ip_forward
```
输出为`1`即表示开启成功。

![](img/e05fa1fe63d3b543013b9aac28f81414_183.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_185.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_187.png)

## 总结

![](img/e05fa1fe63d3b543013b9aac28f81414_189.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_191.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_192.png)

![](img/e05fa1fe63d3b543013b9aac28f81414_193.png)

本节课中我们一起学习了iptables防火墙的基础知识。我们从安装和启动iptables服务开始，学习了如何使用`-L`、`-n`、`--line-numbers`等选项查看防火墙规则。接着，我们深入探讨了如何添加（`-A`, `-I`）和删除（`-D`, `-F`）规则，并理解了防火墙“从上到下匹配即停止”的核心特性。我们还了解了默认规则（Policy）的概念及其修改方法。通过配置拒绝ICMP、特定端口、特定IP及网段访问的实例，我们掌握了主机型防火墙的配置方法。最后，我们为网络型防火墙的配置做好了环境准备，包括规划多台主机、配置双网卡以及开启系统的IP转发功能。这些知识是构建Linux系统网络安全防护的基础。