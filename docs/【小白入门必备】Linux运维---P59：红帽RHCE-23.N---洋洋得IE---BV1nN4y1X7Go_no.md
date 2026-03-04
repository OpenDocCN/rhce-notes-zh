# Linux运维进阶：P59：NTP时间同步 ⏰

在本节课中，我们将学习NTP（网络时间协议）的基本概念及其在企业环境中的重要性。我们将了解如何配置Linux系统，使其能够自动与标准时间服务器同步，并掌握在企业内网环境中搭建时间同步架构的方法。

![](img/b5b70146763def03e74643ff659c1390_1.png)

![](img/b5b70146763def03e74643ff659c1390_3.png)

## 为什么需要时间同步？

上一节我们介绍了NTP的基本概念，本节中我们来看看为什么时间同步在集群环境中至关重要。

现代企业环境通常是集群环境。例如，左侧可能是一群提供网站服务的服务器，右侧是一群提供数据库服务的服务器。为了让这些服务器能够协同工作，共同对外提供服务，必须保证每台服务器的时间是一致的。如果服务器时间不一致，例如有的机器时间是2018年，有的是2019年，它们将无法有效协同工作。

![](img/b5b70146763def03e74643ff659c1390_5.png)

![](img/b5b70146763def03e74643ff659c1390_7.png)

手动使用 `date` 命令设置时间既不准确也不高效。NTP协议可以实现时间的自动、精确同步。

## NTP协议与Chrony服务

NTP（Network Time Protocol）是网络时间协议，用于实现时间同步。在RHEL/CentOS 7及更高版本中，默认使用 **Chrony** 软件来实现NTP协议。Chrony是一个开源的自由软件，它能保持系统时间与NTP服务器时间一致。

首先，我们需要检查并安装Chrony服务。最小化安装的系统可能没有预装该软件包。

```bash
# 检查是否已安装chrony
rpm -qa | grep chrony

# 如果未安装，则使用yum进行安装
yum -y install chrony
```

![](img/b5b70146763def03e74643ff659c1390_9.png)

安装完成后，启动并启用chronyd服务。

```bash
# 启动chronyd服务
systemctl start chronyd

# 设置开机自启
systemctl enable chronyd

# 查看服务状态
systemctl status chronyd
```

![](img/b5b70146763def03e74643ff659c1390_11.png)

## 配置Chrony客户端

![](img/b5b70146763def03e74643ff659c1390_13.png)

![](img/b5b70146763def03e74643ff659c1390_15.png)

Chrony安装后，默认就会尝试与预配置的NTP服务器同步时间。我们可以通过修改其配置文件来指定更优的时间源（例如国内的阿里云NTP服务器）。

![](img/b5b70146763def03e74643ff659c1390_17.png)

Chrony的主配置文件是 `/etc/chrony.conf`。默认配置中，`server` 指令指定了时间服务器的地址。

```bash
# 打开配置文件进行编辑
vi /etc/chrony.conf
```

在配置文件中，你会看到类似以下的默认服务器行（可能已被注释）：

```
# server 0.centos.pool.ntp.org iburst
# server 1.centos.pool.ntp.org iburst
# server 2.centos.pool.ntp.org iburst
# server 3.centos.pool.ntp.org iburst
```

以下是修改步骤：
1.  注释掉或删除默认的国外服务器地址。
2.  添加国内更稳定的NTP服务器地址，例如阿里云的NTP服务器。

修改后的配置示例：

![](img/b5b70146763def03e74643ff659c1390_19.png)

```
# 使用阿里云的NTP服务器
server ntp.aliyun.com iburst
server ntp1.aliyun.com iburst
```

保存退出后，重启chronyd服务使配置生效。

![](img/b5b70146763def03e74643ff659c1390_21.png)

```bash
systemctl restart chronyd
```

## 验证时间同步状态

![](img/b5b70146763def03e74643ff659c1390_23.png)

![](img/b5b70146763def03e74643ff659c1390_25.png)

配置完成后，如何验证时间同步是否成功，以及当前正在与哪个服务器同步呢？

![](img/b5b70146763def03e74643ff659c1390_27.png)

![](img/b5b70146763def03e74643ff659c1390_29.png)

使用 `chronyc sources` 命令可以查看当前的时间同步源状态。

![](img/b5b70146763def03e74643ff659c1390_31.png)

![](img/b5b70146763def03e74643ff659c1390_33.png)

```bash
chronyc sources -v
```

命令输出中，`^*` 符号标记的服务器表示当前正在使用的同步源。你可以通过解析该服务器的主机名或IP地址来确认它是否是你配置的阿里云服务器。

```bash
# 解析NTP服务器域名，确认IP地址
host ntp1.aliyun.com
```

你还可以手动修改系统时间，然后观察Chrony是否会自动将其纠正为标准时间，这是一个简单的功能测试。

```bash
# 手动设置一个错误的时间
date -s “2021-06-25 18:00:00”

# 等待片刻或重启chronyd服务后，查看当前时间是否已被自动纠正
date
systemctl restart chronyd
date
```

![](img/b5b70146763def03e74643ff659c1390_35.png)

## 搭建企业内网NTP服务器

在企业的内网环境中，可能并非所有服务器都能直接访问外网。此时，可以搭建一台内网NTP服务器作为“时间中转站”。

架构思路如下：
1.  指定一台能访问外网的服务器作为“内网NTP服务器”。
2.  配置该服务器与公网NTP源（如阿里云）同步。
3.  配置该服务器允许内网其他客户端来同步时间。
4.  内网其他客户端配置与此内网NTP服务器同步。

**第一步：配置内网NTP服务器（能访问外网的机器）**

编辑其 `/etc/chrony.conf` 文件，完成两项配置：
1.  指定上游的公网NTP服务器（如阿里云）。
2.  添加 `allow` 指令，允许内网网段来同步时间。

![](img/b5b70146763def03e74643ff659c1390_37.png)

配置示例：

![](img/b5b70146763def03e74643ff659c1390_39.png)

```
# 指定上游时间服务器
server ntp.aliyun.com iburst

![](img/b5b70146763def03e74643ff659c1390_41.png)

# 允许所有IP地址的客户端来同步时间（或指定如192.168.0.0/24的内网网段）
allow 0.0.0.0/0
```

![](img/b5b70146763def03e74643ff659c1390_43.png)

保存并重启chronyd服务。

![](img/b5b70146763def03e74643ff659c1390_45.png)

```bash
systemctl restart chronyd
```

![](img/b5b70146763def03e74643ff659c1390_47.png)

**第二步：配置内网客户端（不能访问外网的机器）**

![](img/b5b70146763def03e74643ff659c1390_49.png)

编辑客户端的 `/etc/chrony.conf` 文件。
1.  注释掉或删除所有默认的 `server` 行。
2.  添加指向内网NTP服务器的 `server` 行。

![](img/b5b70146763def03e74643ff659c1390_51.png)

配置示例：

![](img/b5b70146763def03e74643ff659c1390_53.png)

```
# 指向内网NTP服务器的IP地址
server 192.168.0.40 iburst
```

![](img/b5b70146763def03e74643ff659c1390_55.png)

![](img/b5b70146763def03e74643ff659c1390_57.png)

保存配置，启动并启用chronyd服务。

```bash
systemctl restart chronyd
systemctl enable chronyd
```

![](img/b5b70146763def03e74643ff659c1390_59.png)

在客户端使用 `chronyc sources -v` 命令验证，应能看到同步源是内网NTP服务器（如192.168.0.40），并且标记为 `^*`。

![](img/b5b70146763def03e74643ff659c1390_61.png)

## 总结

![](img/b5b70146763def03e74643ff659c1390_63.png)

![](img/b5b70146763def03e74643ff659c1390_65.png)

本节课中我们一起学习了NTP时间同步的核心知识。我们首先理解了在集群环境中保持时间一致性的重要性。然后，我们掌握了在RHEL/CentOS系统上使用Chrony服务进行时间同步的方法，包括安装服务、修改配置文件指向更优的NTP源（如阿里云），以及验证同步状态。最后，我们学习了如何在内网环境中搭建一个NTP中转服务器，使得所有内网机器都能间接地与公网标准时间保持同步。这套方法简单有效，是企业IT基础架构中保证服务协同稳定的重要环节。