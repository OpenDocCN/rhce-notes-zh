# Linux运维教程：P60：NTP时间同步 ⏰

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_1.png)

在本节课中，我们将学习NTP（网络时间协议）的基本概念及其在企业环境中的重要性。我们将了解如何配置Linux系统，使其能够自动与标准时间服务器同步，并学习如何在内网环境中搭建一个时间同步的桥梁。

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_3.png)

## 为什么需要时间同步？

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_5.png)

上一节我们介绍了NTP的基本概念，本节中我们来看看为什么时间同步在集群环境中至关重要。

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_7.png)

在企业集群环境下，通常有成百上千台服务器协同工作。例如，一组服务器提供网站服务，另一组服务器提供数据库服务。为了让这些服务器能够协同工作，它们的时间必须保持一致。如果每台服务器的时间都不同，例如有的在2018年，有的在2022年，它们就无法有效协同。

手动使用 `date` 命令设置时间既不准确也不现实。NTP协议可以帮助我们实现时间的自动同步，确保所有服务器都使用一个高度精确的标准时间。

## NTP服务与chrony软件

NTP本身是一个协议。在RHEL/CentOS 7及更高版本中，默认使用一个名为 **chrony** 的软件来实现NTP协议。它是一个开源的自由软件，用于保持系统时间与NTP服务器同步。

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_9.png)

首先，我们需要检查并安装chrony软件包。

```bash
# 检查chrony是否已安装
rpm -qa | grep chrony

# 如果未安装（例如最小化安装的系统），则使用yum安装
yum -y install chrony
```

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_11.png)

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_13.png)

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_15.png)

安装完成后，启动chrony服务并设置为开机自启。

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_17.png)

```bash
# 启动chrony服务
systemctl start chronyd

# 设置开机自启
systemctl enable chronyd

# 查看服务状态
systemctl status chronyd
```

chrony服务启动后，默认会自动与配置文件中的NTP服务器同步时间。我们可以通过修改时间并观察其自动同步来验证。

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_19.png)

```bash
# 查看当前时间
date

# 手动修改时间为一个错误值（例如2021年）
date -s "2021-06-25 18:00:00"

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_21.png)

# 再次查看，chrony会自动将其同步回正确时间
date
```

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_23.png)

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_25.png)

## 配置chrony同步源

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_27.png)

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_29.png)

chrony的主配置文件是 `/etc/chrony.conf`。默认情况下，它配置了CentOS项目的公共NTP服务器。这些服务器位于国外，同步速度可能较慢。

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_31.png)

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_33.png)

以下是配置文件的默认服务器部分示例：
```
server 0.centos.pool.ntp.org iburst
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst
```

为了提高同步速度和稳定性，我们通常将其替换为国内的NTP服务器，例如阿里云提供的服务。

以下是修改步骤：
1.  编辑配置文件：`vi /etc/chrony.conf`
2.  注释掉（在行首添加`#`）或删除原有的 `server` 行。
3.  添加新的阿里云NTP服务器地址。
    ```
    server ntp.aliyun.com iburst
    server ntp1.aliyun.com iburst
    ```
4.  保存退出，并重启chrony服务使配置生效。
    ```bash
    systemctl restart chronyd
    ```

我们可以使用以下命令来验证当前正在与哪个时间源同步。

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_35.png)

```bash
# 查看当前同步的时间源
chronyc sources -v
```
在输出中，`*` 符号标记了当前正在使用的同步源。你可以通过解析其域名或IP地址来确认它是否是你配置的阿里云服务器。

```bash
# 解析NTP服务器域名，确认IP
ping ntp1.aliyun.com
# 或使用host命令（如已安装）
host ntp1.aliyun.com
```

## 搭建内网NTP服务器

在企业内网中，可能并非所有服务器都能直接访问外网。一个常见的解决方案是：指定一台可以访问外网的服务器作为“内部NTP服务器”，让它与外网标准时间源（如阿里云）同步；然后让内网的其他服务器都与这台内部服务器同步。

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_37.png)

这个方案分为服务器端（内部NTP服务器）和客户端（内网其他机器）两部分配置。

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_39.png)

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_41.png)

### 服务器端配置（IP: 192.168.0.40）

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_43.png)

在作为内部NTP服务器的机器上，我们需要修改配置文件，允许其他客户端来同步时间。

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_45.png)

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_47.png)

1.  编辑 `/etc/chrony.conf` 文件。
2.  找到 `#allow 192.168.0.0/16` 这行（通常在29行附近），取消注释并将网络段改为 `0.0.0.0/0` 或 `allow 0.0.0.0/0`，表示允许任何IP的客户端进行同步。
    ```
    allow 0.0.0.0/0
    ```
3.  确保 `server` 指向的是外网时间源（如阿里云）。
4.  保存退出，并重启chrony服务。
    ```bash
    systemctl restart chronyd
    ```

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_49.png)

### 客户端配置（内网其他机器）

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_51.png)

在内网的其他服务器上，需要将其NTP源指向我们刚刚配置的内部NTP服务器。

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_53.png)

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_55.png)

1.  确保客户端已安装chrony软件包。
2.  编辑客户端的 `/etc/chrony.conf` 文件。
3.  注释掉或删除所有原有的 `server` 行。
4.  添加一行，指向内部NTP服务器的IP地址。
    ```
    server 192.168.0.40 iburst
    ```
5.  保存退出，并重启chrony服务。
    ```bash
    systemctl restart chronyd
    ```
6.  使用 `chronyc sources -v` 命令验证，应该可以看到同步源是 `192.168.0.40`，并且标记有 `*` 号。

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_57.png)

至此，内网客户端通过内部NTP服务器，间接实现了与阿里云标准时间的同步。

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_59.png)

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_61.png)

## 总结

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_63.png)

![](img/3d1d4a4c342d56db5b6ffcdabce4f21d_65.png)

本节课中我们一起学习了NTP时间同步的核心知识。我们首先理解了在集群环境中保持时间一致性的重要性。然后，我们学习了在RHEL/CentOS系统中如何使用chrony软件实现NTP同步，包括安装服务、修改同步源为国内服务器（如阿里云）。最后，我们掌握了一个实用的企业级方案：通过配置一台能访问外网的内部NTP服务器，让整个内网的所有机器都能间接、高效地同步到标准时间。这套方法简单有效，是Linux运维工作中的必备技能。