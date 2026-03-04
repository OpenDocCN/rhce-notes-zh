# Linux运维培训教程：P59：NTP时间同步 ⏰

![](img/7d4f223a4b4b5a129b708bc43d4b1117_1.png)

## 概述
在本节课中，我们将要学习NTP（网络时间协议）的基本概念及其在企业环境中的重要性。我们将了解为什么需要时间同步，如何配置NTP客户端与服务器，以及如何在内网环境中搭建一个时间同步的桥梁。内容简单直白，适合初学者。

![](img/7d4f223a4b4b5a129b708bc43d4b1117_3.png)

---

## 为什么需要时间同步？
在开始配置之前，我们首先需要理解为什么时间同步如此重要。

![](img/7d4f223a4b4b5a129b708bc43d4b1117_5.png)

![](img/7d4f223a4b4b5a129b708bc43d4b1117_6.png)

现代企业环境通常是集群环境。想象一下，左边有一群服务器提供网站服务，右边有一群服务器提供数据库业务。为了让这些服务器能够协同工作，它们的时间必须保持一致。如果一台服务器的时间是2018年，另一台是2019年，它们就无法有效地协同完成任务。

手动使用 `date` 命令设置时间既不准确也不高效。因此，我们需要一种自动化的方法来确保所有服务器都使用一个标准且精确的时间源。

---

## 什么是NTP？
NTP（Network Time Protocol，网络时间协议）就是实现时间自动同步的解决方案。

![](img/7d4f223a4b4b5a129b708bc43d4b1117_8.png)

NTP服务器能提供极其精确的标准时间。据说，其精度可以达到数百年不差一秒。在企业中，我们让所有服务器都与一个或多个NTP服务器同步，从而保证整个集群的时间一致性。

有许多公共NTP服务器可供选择，例如阿里云、CentOS、清华大学、中科大、腾讯和网易等提供的服务器。

---

![](img/7d4f223a4b4b5a129b708bc43d4b1117_10.png)

![](img/7d4f223a4b4b5a129b708bc43d4b1117_12.png)

## 在CentOS中配置NTP客户端
上一节我们介绍了NTP的基本概念，本节中我们来看看如何在CentOS系统中配置NTP客户端。

![](img/7d4f223a4b4b5a129b708bc43d4b1117_14.png)

在RHEL/CentOS 7系统中，实现NTP协议的软件包是 `chrony`。它是一个开源的自由软件，在最小化安装的系统中可能需要手动安装。

![](img/7d4f223a4b4b5a129b708bc43d4b1117_16.png)

### 安装与启动服务
以下是安装和启动 `chrony` 服务的步骤。

1.  **安装软件包**：
    ```bash
    yum -y install chrony
    ```

2.  **启动并设置开机自启**：
    ```bash
    systemctl start chronyd
    systemctl enable chronyd
    ```

3.  **检查服务状态**：
    ```bash
    systemctl status chronyd
    ```

![](img/7d4f223a4b4b5a129b708bc43d4b1117_18.png)

安装并启动 `chrony` 后，它就会自动开始与配置文件中指定的NTP服务器同步时间。

![](img/7d4f223a4b4b5a129b708bc43d4b1117_20.png)

### 验证时间同步
为了验证同步是否生效，我们可以手动修改系统时间，然后观察它是否被自动纠正。

1.  将系统时间修改为一个错误的值：
    ```bash
    date -s "2021-06-25 18:00:00"
    ```

![](img/7d4f223a4b4b5a129b708bc43d4b1117_22.png)

![](img/7d4f223a4b4b5a129b708bc43d4b1117_23.png)

2.  稍等片刻或重启 `chronyd` 服务后，再次查看时间：
    ```bash
    date
    ```
    你会发现时间已经自动同步回当前的标准时间。

![](img/7d4f223a4b4b5a129b708bc43d4b1117_25.png)

![](img/7d4f223a4b4b5a129b708bc43d4b1117_27.png)

### 配置同步源
默认情况下，`chrony` 会与CentOS项目提供的国外NTP服务器同步。为了获得更快的同步速度，我们通常将其改为国内的服务器，例如阿里云的NTP服务器。

![](img/7d4f223a4b4b5a129b708bc43d4b1117_29.png)

![](img/7d4f223a4b4b5a129b708bc43d4b1117_30.png)

以下是修改配置文件的步骤。

1.  编辑主配置文件 `/etc/chrony.conf`：
    ```bash
    vim /etc/chrony.conf
    ```

2.  找到以 `server` 开头的行，将其注释或替换为阿里云的NTP服务器地址：
    ```bash
    #server 0.centos.pool.ntp.org iburst
    #server 1.centos.pool.ntp.org iburst
    #server 2.centos.pool.ntp.org iburst
    #server 3.centos.pool.ntp.org iburst

    server ntp.aliyun.com iburst
    server ntp1.aliyun.com iburst
    ```

3.  保存退出后，重启 `chronyd` 服务使配置生效：
    ```bash
    systemctl restart chronyd
    ```

### 检查同步状态
要查看当前正在与哪个NTP服务器同步，可以使用以下命令：
```bash
chronyc sources -v
```
在输出列表中，`*` 号标记表示当前正在使用的同步源。你可以通过 `ping` 或 `host` 命令验证该IP地址是否对应你配置的域名（如 `ntp1.aliyun.com`）。

![](img/7d4f223a4b4b5a129b708bc43d4b1117_32.png)

---

## 搭建内网NTP服务器 🖥️
上一节我们学习了如何配置客户端与公共NTP服务器同步。但在某些内网环境中，服务器可能无法直接访问外网。本节中我们来看看如何通过一台能访问外网的服务器，为内网其他机器提供时间同步服务。

架构思路是：让一台能连接外网的服务器（例如公司的负载均衡器或网关）作为内网的“时间桥”。这台服务器与外部的公共NTP服务器（如阿里云）同步，然后内网的其他服务器都向这台“时间桥”服务器同步时间。

![](img/7d4f223a4b4b5a129b708bc43d4b1117_34.png)

### 配置服务端（时间桥）
假设我们有一台IP为 `192.168.0.40` 的服务器可以访问外网，我们将它配置为内网的NTP服务器。

![](img/7d4f223a4b4b5a129b708bc43d4b1117_36.png)

1.  确保该服务器已安装并正确配置了 `chrony`，能够与阿里云等外部服务器同步。
2.  编辑其配置文件 `/etc/chrony.conf`，添加一行配置，允许所有客户端向它同步时间：
    ```bash
    allow 0.0.0.0/0
    ```
    这行配置表示允许来自任何IP地址的客户端进行时间同步。
3.  保存退出，并重启 `chronyd` 服务：
    ```bash
    systemctl restart chronyd
    ```

![](img/7d4f223a4b4b5a129b708bc43d4b1117_38.png)

![](img/7d4f223a4b4b5a129b708bc43d4b1117_40.png)

### 配置客户端（内网服务器）
对于内网中其他无法直接访问外网的服务器（例如IP为 `192.168.0.13`），我们需要修改其NTP同步源。

1.  编辑客户端的配置文件 `/etc/chrony.conf`。
2.  注释掉或删除原有的 `server` 行，添加指向内网NTP服务器（时间桥）的配置：
    ```bash
    #server 0.centos.pool.ntp.org iburst
    server 192.168.0.40 iburst
    ```
3.  保存退出，并重启客户端的 `chronyd` 服务：
    ```bash
    systemctl restart chronyd
    ```

![](img/7d4f223a4b4b5a129b708bc43d4b1117_42.png)

![](img/7d4f223a4b4b5a129b708bc43d4b1117_44.png)

![](img/7d4f223a4b4b5a129b708bc43d4b1117_46.png)

4.  在客户端使用 `chronyc sources -v` 命令验证，应该能看到同步源是 `192.168.0.40`，并且标记为 `*`。

这样，内网客户端 `192.168.0.13` 的时间就通过 `192.168.0.40` 间接地与阿里云保持了同步。

![](img/7d4f223a4b4b5a129b708bc43d4b1117_48.png)

![](img/7d4f223a4b4b5a129b708bc43d4b1117_50.png)

---

![](img/7d4f223a4b4b5a129b708bc43d4b1117_52.png)

![](img/7d4f223a4b4b5a129b708bc43d4b1117_54.png)

## 总结
本节课中我们一起学习了NTP时间同步的核心知识。

我们首先了解了时间同步在集群环境中的必要性。然后，我们学习了如何在CentOS系统上使用 `chrony` 软件包配置NTP客户端，包括安装服务、修改同步源为国内服务器（如阿里云），以及检查同步状态。

![](img/7d4f223a4b4b5a129b708bc43d4b1117_56.png)

![](img/7d4f223a4b4b5a129b708bc43d4b1117_58.png)

最后，我们探讨了在内网环境中搭建NTP服务器的常见场景。通过配置一台能够访问外网的服务器作为“时间桥”，让所有内网服务器向其同步时间，从而间接实现与公共NTP服务器的同步，确保了整个内网环境的时间一致性。

![](img/7d4f223a4b4b5a129b708bc43d4b1117_60.png)

![](img/7d4f223a4b4b5a129b708bc43d4b1117_62.png)

整个过程涉及的关键操作包括修改 `/etc/chrony.conf` 配置文件和熟练使用 `systemctl` 管理 `chronyd` 服务。掌握这些内容，你就能在企业环境中有效地管理和维护服务器的时间同步。