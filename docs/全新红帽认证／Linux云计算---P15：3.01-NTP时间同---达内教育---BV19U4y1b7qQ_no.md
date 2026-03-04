# Linux系统管理：3.01：NTP时间同步 ⏰

![](img/e6f620c40449a987106ea876f882ae5c_1.png)

![](img/e6f620c40449a987106ea876f882ae5c_3.png)

![](img/e6f620c40449a987106ea876f882ae5c_5.png)

![](img/e6f620c40449a987106ea876f882ae5c_7.png)

![](img/e6f620c40449a987106ea876f882ae5c_9.png)

![](img/e6f620c40449a987106ea876f882ae5c_11.png)

在本节课中，我们将学习如何配置Linux系统作为NTP客户端，使其能够与指定的NTP服务器进行时间同步。这是确保多台服务器时间一致性的重要基础。

![](img/e6f620c40449a987106ea876f882ae5c_13.png)

## 概述

![](img/e6f620c40449a987106ea876f882ae5c_15.png)

![](img/e6f620c40449a987106ea876f882ae5c_17.png)

NTP（Network Time Protocol，网络时间协议）用于在网络中同步计算机的时间。提供标准时间的机器称为NTP服务器，而使用该时间的机器称为NTP客户端。从红帽7系统开始，默认的时间同步服务由`ntpd`更换为`chrony`。本节课将重点讲解如何配置`chrony`客户端。

![](img/e6f620c40449a987106ea876f882ae5c_19.png)

## 配置步骤

![](img/e6f620c40449a987106ea876f882ae5c_21.png)

配置NTP客户端主要分为三个步骤：安装软件包、修改配置文件、启动并启用服务。

![](img/e6f620c40449a987106ea876f882ae5c_23.png)

![](img/e6f620c40449a987106ea876f882ae5c_25.png)

以下是具体的操作流程：

![](img/e6f620c40449a987106ea876f882ae5c_27.png)

![](img/e6f620c40449a987106ea876f882ae5c_29.png)

1.  **安装软件包**
    首先，需要确保系统已安装`chrony`软件包。在大多数情况下，系统默认已安装。可以通过以下命令检查和安装：
    ```bash
    yum install -y chrony
    ```

![](img/e6f620c40449a987106ea876f882ae5c_31.png)

![](img/e6f620c40449a987106ea876f882ae5c_33.png)

2.  **修改配置文件**
    配置文件位于`/etc/chrony.conf`。我们需要将其中指向默认时间服务器（如`pool.ntp.org`）的配置行，修改为指向题目或环境要求的特定NTP服务器。
    打开配置文件：
    ```bash
    vim /etc/chrony.conf
    ```
    找到以`pool`或`server`开头的行（通常在文件开头部分），将其修改为：
    ```bash
    server <指定的NTP服务器域名> iburst
    ```
    例如，若服务器域名为`server1.example.com`，则配置为：
    ```bash
    server server1.example.com iburst
    ```
    **注意**：`iburst`参数有助于在服务启动初期快速完成时间同步，建议保留。修改完成后保存并退出。

![](img/e6f620c40449a987106ea876f882ae5c_35.png)

3.  **启动并启用服务**
    修改配置后，需要重启`chronyd`服务以使配置生效，并设置其开机自动启动。
    ```bash
    systemctl restart chronyd
    systemctl enable chronyd
    ```

## 结果验证

配置完成后，可以通过以下几种方式验证时间同步是否成功。

![](img/e6f620c40449a987106ea876f882ae5c_37.png)

以下是几种常用的验证方法：

![](img/e6f620c40449a987106ea876f882ae5c_39.png)

*   **检查时间同步状态**
    使用`timedatectl`命令查看NTP服务状态及同步情况。
    ```bash
    timedatectl
    ```
    关注输出中的`NTP service: active`（表示服务已激活）和`System clock synchronized: yes`（表示已完成同步）。

![](img/e6f620c40449a987106ea876f882ae5c_41.png)

![](img/e6f620c40449a987106ea876f882ae5c_42.png)

*   **检查时间源**
    使用`chronyc`工具查看当前使用的时间源及其状态，这是最推荐的验证方式。
    ```bash
    chronyc sources -v
    ```
    在输出列表中，找到你配置的服务器。如果配置正确且可连接，其前方的标记应为`^*`（一个`^`号和一个`*`号）。`^`表示这是一个指定的服务器源，`*`表示当前正在使用此源进行同步。

![](img/e6f620c40449a987106ea876f882ae5c_44.png)

![](img/e6f620c40449a987106ea876f882ae5c_46.png)

*   **测试时间同步**
    可以手动将系统时间设置为一个错误值，然后观察其是否会被自动纠正。但考试或生产环境中，通常不推荐此方法。
    ```bash
    date -s “2002-02-02 10:00:00” # 设置一个过去的时间
    # 等待片刻或重启chronyd服务后，再次执行`date`命令查看时间是否已恢复正确。
    ```

![](img/e6f620c40449a987106ea876f882ae5c_48.png)

![](img/e6f620c40449a987106ea876f882ae5c_50.png)

![](img/e6f620c40449a987106ea876f882ae5c_52.png)

## 补充知识

![](img/e6f620c40449a987106ea876f882ae5c_54.png)

![](img/e6f620c40449a987106ea876f882ae5c_55.png)

![](img/e6f620c40449a987106ea876f882ae5c_57.png)

*   **硬件时钟同步**：系统时间同步后，可以使用`hwclock -w`命令将正确的系统时间写入硬件时钟（BIOS时间）。反之，`hwclock -s`则用硬件时钟来设置系统时间。
*   **配置文件详解**：如需了解`/etc/chrony.conf`中更多参数（如`minpoll`, `maxpoll`调整同步间隔），可以通过`man chrony.conf`命令查看详细手册。

![](img/e6f620c40449a987106ea876f882ae5c_59.png)

## 总结

![](img/e6f620c40449a987106ea876f882ae5c_61.png)

![](img/e6f620c40449a987106ea876f882ae5c_63.png)

本节课我们一起学习了NTP时间同步的基本概念，并重点掌握了在RHEL/CentOS 8系统中使用`chrony`配置NTP客户端的完整流程。核心步骤可概括为：**修改`/etc/chrony.conf`配置文件，指定正确的`server`地址；然后重启并启用`chronyd`服务**。最后，我们学会了使用`chronyc sources`命令来验证配置是否生效。这是系统管理中保证服务器时间一致性的基础且重要的技能。