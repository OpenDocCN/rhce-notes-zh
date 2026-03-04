# Linux最全RHCSA+RHCE培训教程合集，小白入门必备！ - P60：红帽RHCE-23.NTP时间同步

![](img/0ae96cec8618037cb536696ea52f21d0_1.png)

## 📚 概述
在本节课中，我们将要学习NTP（网络时间协议）的基本概念及其在企业环境中的重要性。我们将了解如何配置Linux系统，使其能够自动与标准时间服务器同步，并学习如何在内网环境中搭建一个NTP时间同步的桥梁。

![](img/0ae96cec8618037cb536696ea52f21d0_3.png)

---

## 🕐 什么是NTP？
NTP，全称网络时间协议，是实现计算机系统时间同步的标准协议。在企业集群环境中，确保所有服务器的时间保持一致至关重要。如果服务器时间不一致，它们将无法协同工作，可能导致服务中断或数据不一致等问题。

![](img/0ae96cec8618037cb536696ea52f21d0_5.png)

![](img/0ae96cec8618037cb536696ea52f21d0_7.png)

手动使用 `date` 命令设置时间既不准确也不高效。NTP服务可以自动与高精度的时间服务器同步，确保时间的准确性和一致性。

---

## 🖥️ NTP服务器与客户端
NTP采用客户端/服务器模型。我们需要一个提供标准时间的NTP服务器，其他作为客户端的服务器则向它同步时间。

在RHEL/CentOS 7系统中，默认使用 `chrony` 软件包来实现NTP功能。它是一个开源的自由软件，能够保持系统时间与NTP服务器同步。

![](img/0ae96cec8618037cb536696ea52f21d0_9.png)

### 安装与启动chrony服务
如果你的系统是最小化安装，可能需要手动安装 `chrony` 软件包。

```bash
yum -y install chrony
```

安装完成后，启动并设置服务开机自启。

![](img/0ae96cec8618037cb536696ea52f21d0_11.png)

```bash
systemctl start chronyd
systemctl enable chronyd
systemctl status chronyd
```

![](img/0ae96cec8618037cb536696ea52f21d0_13.png)

![](img/0ae96cec8618037cb536696ea52f21d0_15.png)

---

![](img/0ae96cec8618037cb536696ea52f21d0_17.png)

## ⚙️ 配置chrony客户端
上一节我们介绍了NTP的基本概念，本节中我们来看看如何配置客户端与公共NTP服务器同步。

`chrony` 的主配置文件是 `/etc/chrony.conf`。安装后，它会默认配置几个公共的NTP服务器（如CentOS项目提供的服务器）。

```bash
vim /etc/chrony.conf
```

在配置文件中，你会看到类似以下的 `server` 行：

![](img/0ae96cec8618037cb536696ea52f21d0_19.png)

```
server 0.centos.pool.ntp.org iburst
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst
```

这些服务器位于国外。为了提高同步速度和稳定性，我们通常将其替换为国内的NTP服务器，例如阿里云提供的：

![](img/0ae96cec8618037cb536696ea52f21d0_21.png)

```
server ntp.aliyun.com iburst
server ntp1.aliyun.com iburst
```

![](img/0ae96cec8618037cb536696ea52f21d0_23.png)

![](img/0ae96cec8618037cb536696ea52f21d0_25.png)

修改后保存退出，并重启 `chronyd` 服务使配置生效。

![](img/0ae96cec8618037cb536696ea52f21d0_27.png)

![](img/0ae96cec8618037cb536696ea52f21d0_29.png)

```bash
systemctl restart chronyd
```

![](img/0ae96cec8618037cb536696ea52f21d0_31.png)

![](img/0ae96cec8618037cb536696ea52f21d0_33.png)

---

## 🔍 验证时间同步
配置完成后，如何验证我们的时间是否真的在与指定服务器同步呢？

以下是验证同步状态的命令：

```bash
chronyc sources -v
```

![](img/0ae96cec8618037cb536696ea52f21d0_35.png)

在输出列表中，关注 `S` 列。`*` 号表示当前正在使用的同步源，`+` 号表示可接受的备用源。你可以通过此命令确认同步的服务器地址。

你还可以使用 `host` 或 `ping` 命令来解析你配置的NTP服务器域名，确认其IP地址与 `chronyc` 命令输出中的地址一致。

```bash
host ntp1.aliyun.com
```

---

![](img/0ae96cec8618037cb536696ea52f21d0_37.png)

## 🏢 企业内网时间同步方案
在纯粹的内网环境中，服务器可能无法直接访问外部的公共NTP服务器。这时，我们可以采用“桥接”方案。

![](img/0ae96cec8618037cb536696ea52f21d0_39.png)

**方案思路**：指定一台可以访问外网的服务器（例如公司的负载均衡器或网关）作为“内部NTP服务器”。这台服务器与外部的公共NTP服务器（如阿里云）同步。然后，内网的其他所有服务器都指向这台内部NTP服务器进行时间同步。

![](img/0ae96cec8618037cb536696ea52f21d0_41.png)

![](img/0ae96cec8618037cb536696ea52f21d0_43.png)

### 配置内部NTP服务器（服务端）
在可以访问外网的服务器上，我们需要修改其 `chrony` 配置，允许内网其他客户端来同步时间。

1.  编辑配置文件 `/etc/chrony.conf`。
2.  找到并取消注释（或添加）以下行，允许所有客户端连接：

    ```
    allow 0.0.0.0/0
    ```
    这行配置表示允许来自任何IP地址的客户端进行时间同步。
3.  保存并重启 `chronyd` 服务。

![](img/0ae96cec8618037cb536696ea52f21d0_45.png)

![](img/0ae96cec8618037cb536696ea52f21d0_47.png)

![](img/0ae96cec8618037cb536696ea52f21d0_49.png)

```bash
systemctl restart chronyd
```

### 配置内网客户端
在内网的其他服务器上，我们需要将其NTP源指向我们刚刚配置的内部NTP服务器。

![](img/0ae96cec8618037cb536696ea52f21d0_51.png)

![](img/0ae96cec8618037cb536696ea52f21d0_53.png)

1.  编辑客户端服务器的 `/etc/chrony.conf` 文件。
2.  注释掉或删除原有的 `server` 行。
3.  添加指向内部NTP服务器IP地址的新 `server` 行，例如：

    ```
    server 192.168.0.40 iburst
    ```
    （请将 `192.168.0.40` 替换为你内部NTP服务器的实际IP地址。）
4.  保存退出，并重启客户端的 `chronyd` 服务。

![](img/0ae96cec8618037cb536696ea52f21d0_55.png)

![](img/0ae96cec8618037cb536696ea52f21d0_57.png)

```bash
systemctl restart chronyd
```

配置完成后，在客户端使用 `chronyc sources -v` 命令验证，应该能看到同步源已变为内部NTP服务器的IP地址。

![](img/0ae96cec8618037cb536696ea52f21d0_59.png)

![](img/0ae96cec8618037cb536696ea52f21d0_61.png)

---

![](img/0ae96cec8618037cb536696ea52f21d0_63.png)

![](img/0ae96cec8618037cb536696ea52f21d0_65.png)

## 📝 总结
本节课中我们一起学习了NTP时间同步的核心知识。我们首先了解了NTP在企业集群环境中的必要性，然后学习了如何使用 `chrony` 软件包配置系统与公共NTP服务器同步。最后，我们探讨了在企业内网环境中，如何通过搭建一台“桥接”服务器，让所有内网机器间接地与标准时间保持同步。掌握这些技能，对于维护一个稳定、协同工作的服务器集群至关重要。