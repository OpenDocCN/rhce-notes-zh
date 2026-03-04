# RHCE红帽认证全套入门教程：P15：3.01-NTP时间同步

![](img/3e2ded04015f48c6fe34414a3cccadd4_1.png)

![](img/3e2ded04015f48c6fe34414a3cccadd4_3.png)

在本节课中，我们将要学习如何配置NTP时间客户端。这是RHCE考试中的一个常见题目，要求将你的系统配置为指定NTP服务器的客户端，以实现时间同步。

![](img/3e2ded04015f48c6fe34414a3cccadd4_5.png)

![](img/3e2ded04015f48c6fe34414a3cccadd4_7.png)

上一节我们介绍了用户账号管理和计划任务等基础操作，本节中我们来看看如何配置系统的时间同步服务。

![](img/3e2ded04015f48c6fe34414a3cccadd4_9.png)

## NTP时间同步概述

![](img/3e2ded04015f48c6fe34414a3cccadd4_11.png)

![](img/3e2ded04015f48c6fe34414a3cccadd4_13.png)

NTP指的是**Network Time Protocol**，即网络时间协议。通过此协议，可以将一台服务器（NTP服务器）的标准时间分发给网络中的其他计算机（NTP客户端），使所有设备的时间保持一致。

从RHEL 7开始，红帽系统默认使用`chrony`软件包及其服务`chronyd`来替代旧的`ntpd`服务。`chronyd`既可以作为NTP服务器，也可以作为NTP客户端。本教程重点讲解如何配置NTP客户端。

![](img/3e2ded04015f48c6fe34414a3cccadd4_15.png)

## 配置NTP客户端步骤

配置NTP客户端主要分为三个步骤：安装软件包、修改配置文件、启动服务。以下是详细操作流程。

### 1. 安装chrony软件包

![](img/3e2ded04015f48c6fe34414a3cccadd4_17.png)

首先，需要确认`chrony`软件包是否已安装。在大多数情况下，系统默认已安装此包。

![](img/3e2ded04015f48c6fe34414a3cccadd4_19.png)

```bash
yum install -y chrony
```

![](img/3e2ded04015f48c6fe34414a3cccadd4_21.png)

如果系统提示已安装，则可跳过此步骤。

### 2. 修改配置文件

NTP客户端的主要配置文件是`/etc/chrony.conf`。我们需要修改此文件，指定要同步的NTP服务器地址。

使用文本编辑器打开配置文件：

![](img/3e2ded04015f48c6fe34414a3cccadd4_23.png)

```bash
vim /etc/chrony.conf
```

![](img/3e2ded04015f48c6fe34414a3cccadd4_25.png)

在文件开头部分，你会看到类似以下的默认配置行：

![](img/3e2ded04015f48c6fe34414a3cccadd4_27.png)

```
pool 2.rhel.pool.ntp.org iburst
```

考试或实际工作中，需要将此行修改为题目或环境要求的NTP服务器地址。有两种配置方式：
*   **`pool`**：适用于一个域名对应多个NTP服务器IP地址的情况。
*   **`server`**：适用于一个域名对应单个NTP服务器IP地址的情况。

对于考试题目（通常指定单个服务器），更规范的做法是使用`server`指令。请将默认的`pool`行注释掉或修改为`server`指令。

![](img/3e2ded04015f48c6fe34414a3cccadd4_29.png)

例如，题目要求配置为`server1.example.com`的客户端，则应修改为：

![](img/3e2ded04015f48c6fe34414a3cccadd4_31.png)

```
server server1.example.com iburst
```

![](img/3e2ded04015f48c6fe34414a3cccadd4_33.png)

**参数说明**：
*   `server`：指定NTP服务器地址。
*   `iburst`：此参数允许客户端在初始同步时快速发送多个数据包，以加速首次时间同步过程。建议保留此参数。

![](img/3e2ded04015f48c6fe34414a3cccadd4_35.png)

修改完成后，保存并退出编辑器。

### 3. 启动并启用chronyd服务

![](img/3e2ded04015f48c6fe34414a3cccadd4_37.png)

配置文件修改完成后，需要重启`chronyd`服务以使更改生效，并设置其开机自动启动。

```bash
systemctl restart chronyd
systemctl enable chronyd
```

## 验证配置结果

配置完成后，需要验证时间同步是否成功。以下是几种验证方法。

### 方法一：使用`timedatectl`命令

`timedatectl`命令可以查看系统时间和日期相关的状态。

```bash
timedatectl
```

在输出信息中，关注以下两行：
*   `NTP service: active` — 表示NTP服务正在运行。
*   `NTP synchronized: yes` — 表示系统时间已与NTP服务器成功同步。如果显示`no`，可能需要等待片刻或重启服务。

![](img/3e2ded04015f48c6fe34414a3cccadd4_39.png)

### 方法二：使用`chronyc`命令检查时间源

`chronyc`是`chrony`的命令行控制工具。使用`sources`子命令可以查看当前配置的时间源状态。

```bash
chronyc sources -v
```

![](img/3e2ded04015f48c6fe34414a3cccadd4_41.png)

在输出列表中，关注每个时间源前的**状态标记**：
*   `^*`：这是最理想的状态。`^`表示该源被选为同步源，`*`表示该源是当前正在使用的同步源，且状态良好。
*   `^?`：表示已配置该源，但暂时无法联系上（可能由于网络或DNS问题）。
*   如果列表中没有出现你配置的服务器地址，则说明配置未生效。

这是最直接、最推荐的验证方法。

![](img/3e2ded04015f48c6fe34414a3cccadd4_43.png)

### 方法三：手动修改时间并观察同步

![](img/3e2ded04015f48c6fe34414a3cccadd4_45.png)

为了直观地看到同步效果，可以手动将系统时间改为一个错误值，然后观察其是否被自动纠正。

![](img/3e2ded04015f48c6fe34414a3cccadd4_47.png)

![](img/3e2ded04015f48c6fe34414a3cccadd4_49.png)

1.  设置一个过去的时间：
    ```bash
    date -s “2002-02-02 10:00:00”
    ```
2.  重启`chronyd`服务或等待一段时间（通常几分钟内）：
    ```bash
    systemctl restart chronyd
    ```
3.  再次查看当前时间：
    ```bash
    date
    ```
    如果时间恢复为正确的当前时间，说明NTP同步成功。

**注意**：考试时为了节省时间，通常只需使用方法二（`chronyc sources`）进行验证即可。

![](img/3e2ded04015f48c6fe34414a3cccadd4_51.png)

## 补充知识：同步硬件时钟

![](img/3e2ded04015f48c6fe34414a3cccadd4_53.png)

系统时间同步后，还可以选择将正确的时间写入硬件时钟（BIOS时间）。

![](img/3e2ded04015f48c6fe34414a3cccadd4_55.png)

![](img/3e2ded04015f48c6fe34414a3cccadd4_57.png)

*   将系统时间同步到硬件时钟：
    ```bash
    hwclock -w
    ```
*   根据硬件时钟设置系统时间：
    ```bash
    hwclock -s
    ```

![](img/3e2ded04015f48c6fe34414a3cccadd4_59.png)

![](img/3e2ded04015f48c6fe34414a3cccadd4_61.png)

此操作在工作环境中可能用到，RHCE考试中通常不涉及。

![](img/3e2ded04015f48c6fe34414a3cccadd4_63.png)

## 配置文件详解（进阶）

如果你想深入了解`chrony.conf`的配置选项，可以查看其手册。

![](img/3e2ded04015f48c6fe34414a3cccadd4_65.png)

```bash
man chrony.conf
```

手册中详细解释了`server`、`pool`、`iburst`、`minpoll`、`maxpoll`等所有参数的含义，方便你进行更精细化的调整。

## 总结

本节课中我们一起学习了NTP时间同步客户端的配置。核心要点总结如下：
1.  **服务与软件包**：RHEL 8使用`chrony`软件包和`chronyd`服务进行时间同步。
2.  **配置核心**：编辑`/etc/chrony.conf`文件，使用`server`指令指定NTP服务器地址，并保留`iburst`参数。
3.  **服务管理**：配置完成后，需执行`systemctl restart chronyd`使配置生效。
4.  **关键验证**：使用`chronyc sources -v`命令验证时间源状态，出现`^*`标记即表示配置成功且同步正常。

![](img/3e2ded04015f48c6fe34414a3cccadd4_67.png)

![](img/3e2ded04015f48c6fe34414a3cccadd4_69.png)

掌握以上步骤，你就能顺利完成RHCE考试中关于NTP时间客户端配置的题目，并能在实际工作中进行基础的时间同步管理。