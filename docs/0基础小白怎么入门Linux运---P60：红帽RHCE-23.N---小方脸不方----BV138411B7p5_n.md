# Linux运维全套培训课程：P60：红帽RHCE-23.NTP时间同步 ⏰

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_1.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_3.png)

在本节课中，我们将要学习NTP（网络时间协议）的基本概念及其在企业环境中的重要性。我们将了解为什么服务器集群需要保持时间同步，并掌握如何在CentOS/Red Hat系统中配置和使用`chrony`服务来实现自动时间同步。课程内容从基础概念入手，逐步深入到实际配置，确保初学者能够理解并操作。

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_5.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_7.png)

## 概述

NTP是网络时间协议的缩写，用于实现计算机之间的时间同步。在企业集群环境中，确保所有服务器时间一致是它们能够协同工作的基础。手动设置时间既不准确也不高效，因此我们需要使用NTP服务来自动与可靠的时间源进行同步。

## 为什么需要时间同步？

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_9.png)

上一节我们介绍了NTP的基本概念，本节中我们来看看为什么时间同步如此重要。

现代企业通常采用集群环境。例如，一群服务器提供网站服务，另一群服务器提供数据库服务。这些机器需要协同工作，共同对外提供服务。

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_11.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_13.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_15.png)

如果集群中每台服务器的时间不一致，例如有的停留在2018年，有的在2022年，它们将无法有效协同。因此，保证所有服务器处于一个统一、标准的时间点是集群正常工作的前提。手动使用`date`命令设置时间既不准确也不现实，而NTP服务可以实现时间的自动、精确同步。

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_17.png)

## NTP与Chrony简介

理解了时间同步的必要性后，我们来认识实现这一功能的具体工具。

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_19.png)

NTP协议本身需要软件来实现。在RHEL 7和CentOS 7系统中，默认使用一个名为`chrony`的软件包来实现NTP功能。`chrony`是一个开源的自由软件，它能保持系统时间与NTP服务器时间一致。

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_21.png)

首先，我们需要检查系统是否已安装`chrony`：
```bash
rpm -qa | grep chrony
```
如果是最小化安装的系统，可能需要手动安装：
```bash
yum -y install chrony
```
安装后，启动并设置服务开机自启：
```bash
systemctl start chronyd
systemctl enable chronyd
systemctl status chronyd
```

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_23.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_25.png)

## 配置NTP时间源

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_27.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_29.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_31.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_33.png)

安装好服务后，下一步就是配置它从哪里获取标准时间。

`chrony`的主配置文件是`/etc/chrony.conf`。默认情况下，它配置了几台CentOS项目提供的公共NTP服务器（位于国外）。我们可以修改此文件，使用国内的NTP服务器以获得更好的同步效果，例如阿里云的NTP服务器。

以下是修改配置文件的步骤：
1.  编辑配置文件：`vi /etc/chrony.conf`
2.  注释掉（在行首添加`#`）默认的`server`开头的行。
3.  添加阿里云的NTP服务器地址，例如：
    ```
    server ntp.aliyun.com iburst
    server ntp1.aliyun.com iburst
    ```
4.  保存退出后，重启服务使配置生效：`systemctl restart chronyd`

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_35.png)

修改后，可以使用命令检查当前正在同步的时间源：
```bash
chronyc sources -v
```
在输出中，`^*`符号指示当前正在使用的同步源，可以确认是否已成功切换到阿里云服务器。

## 构建企业内部NTP服务器

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_37.png)

在拥有多台服务器的内网环境中，让每台服务器都直接访问外网NTP源可能不现实。更常见的做法是搭建一台内部NTP服务器。

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_39.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_41.png)

这个方案的架构如下：
1.  指定一台能访问外网的服务器作为“内部NTP服务器”。
2.  将这台服务器配置为与阿里云等外部可靠源同步。
3.  内网的其他服务器则配置为与这台“内部NTP服务器”同步。

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_43.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_45.png)

**配置内部NTP服务器（服务端）：**
在能访问外网的服务器上，除了像上一节那样配置外部时间源，还需在`/etc/chrony.conf`中添加一行配置，允许其他客户端来同步时间：
```
allow 0.0.0.0/0
```
这行配置表示允许所有IP地址的客户端连接。保存并重启`chronyd`服务。

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_47.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_49.png)

**配置内部客户端：**
在内网的其他服务器上，安装`chrony`后，编辑其`/etc/chrony.conf`文件。注释掉默认的服务器地址，添加指向内部NTP服务器的IP地址：
```
server 192.168.0.40 iburst
```
（请将`192.168.0.40`替换为你内部NTP服务器的实际IP）
保存退出后，启动客户端`chronyd`服务，并设置为开机自启。

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_51.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_53.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_55.png)

至此，内网客户端的时间会与内部NTP服务器同步，而内部NTP服务器的时间则与外部标准时间源同步，从而实现了整个内网环境的精确时间同步。

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_57.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_59.png)

## 总结

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_61.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_63.png)

![](img/93d5cb2daeae6088f1fcee15bd2a06b1_65.png)

本节课中我们一起学习了NTP时间同步的核心知识。我们首先了解了时间同步对于服务器集群的重要性，然后学习了在CentOS/Red Hat系统中使用`chrony`软件包来实现NTP同步。我们掌握了如何修改配置文件以使用更优的国内时间源，以及如何在内网环境中搭建一台NTP服务器作为中转，使所有内网机器都能间接同步到准确的标准时间。这些技能是Linux运维工作中保证系统稳定协同的基础。