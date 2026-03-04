# Redhat红帽 RHCE8.0认证体系课程：P17：服务管理

![](img/81d99981eb9b3b9c04525611819dc4d3_0.png)

在本节课中，我们将要学习Linux系统中的服务管理，特别是`systemd`和`systemctl`工具。我们将了解为什么需要服务管理、`systemd`的作用、服务单元配置文件以及如何使用`systemctl`命令来管理服务。

## 服务管理的概念

上一节我们介绍了进程管理，本节中我们来看看服务管理。在Linux系统中，我们熟知的许多功能（如HTTPD）是通过进程来处理的。例如，`/usr/sbin/httpd`程序可以创建进程来处理Web请求。

通过进程管理命令（如`kill`）可以停止或重启进程，但这无法实现服务的开机自动启动。同时，手动管理大量进程也不现实。因此，系统需要一个更高级的管理机制，这就是服务管理。

![](img/81d99981eb9b3b9c04525611819dc4d3_2.png)

## Systemd 简介

![](img/81d99981eb9b3b9c04525611819dc4d3_4.png)

在红帽7以前的版本中，通常使用`service`和`init`脚本来管理服务。从红帽7开始，系统引入了`systemd`作为新的初始化系统和服务管理器。

![](img/81d99981eb9b3b9c04525611819dc4d3_6.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_7.png)

`systemd`是系统启动的第一个进程（PID 1），它负责启动和管理系统中的所有其他服务，相当于一个“管家”。管理`systemd`及其服务的命令行工具是`systemctl`。

![](img/81d99981eb9b3b9c04525611819dc4d3_9.png)

## 单元配置文件

![](img/81d99981eb9b3b9c04525611819dc4d3_11.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_12.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_14.png)

`systemd`通过读取“单元配置文件”来管理服务。每个服务、挂载点、设备等都有一个对应的单元配置文件。

![](img/81d99981eb9b3b9c04525611819dc4d3_16.png)

这些配置文件通常存放在以下目录中：
*   `/usr/lib/systemd/system/`：软件包安装的默认单元文件。
*   `/etc/systemd/system/`：系统管理员创建和修改的单元文件，优先级更高。

单元文件的后缀表明了其类型，例如：
*   **`.service`**：通用服务单元（如`httpd.service`, `sshd.service`）。
*   **`.target`**：目标单元，用于对单元进行分组或定义系统状态（如`multi-user.target`）。
*   **`.socket`**：套接字单元，用于按需激活服务。

![](img/81d99981eb9b3b9c04525611819dc4d3_18.png)

以下是一个服务单元文件（如`httpd.service`）的片段示例，它定义了服务的描述、启动命令和依赖关系：

```ini
[Unit]
Description=The Apache HTTP Server
After=network.target remote-fs.target nss-lookup.target
Documentation=man:httpd(8)
Documentation=man:apachectl(8)

![](img/81d99981eb9b3b9c04525611819dc4d3_20.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_21.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_23.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_24.png)

[Service]
Type=notify
EnvironmentFile=/etc/sysconfig/httpd
ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND
ExecReload=/usr/sbin/httpd $OPTIONS -k graceful
...
```

![](img/81d99981eb9b3b9c04525611819dc4d3_26.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_27.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_28.png)

在RHCSA阶段，我们主要需要学会查看和理解这些配置文件，而不要求编写复杂的单元文件。

![](img/81d99981eb9b3b9c04525611819dc4d3_30.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_31.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_33.png)

## 使用 Systemctl 管理服务

![](img/81d99981eb9b3b9c04525611819dc4d3_35.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_37.png)

`systemctl`是管理`systemd`服务和系统状态的核心工具。它的基本语法是：
`systemctl [选项] [命令] [单元名称]`

![](img/81d99981eb9b3b9c04525611819dc4d3_39.png)

以下是常用的服务管理操作：

![](img/81d99981eb9b3b9c04525611819dc4d3_41.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_42.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_43.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_44.png)

**启动、停止、重启和重载服务**
*   `systemctl start <服务名>`：启动一个服务。
*   `systemctl stop <服务名>`：停止一个服务。
*   `systemctl restart <服务名>`：重启一个服务。
*   `systemctl reload <服务名>`：重新加载服务的配置文件（不中断服务）。

![](img/81d99981eb9b3b9c04525611819dc4d3_45.png)

**查看服务状态**
*   `systemctl status <服务名>`：查看服务的详细状态和日志。
*   `systemctl is-active <服务名>`：检查服务是否正在运行。

![](img/81d99981eb9b3b9c04525611819dc4d3_47.png)

**设置开机自启**
*   `systemctl enable <服务名>`：启用服务开机自动启动。
*   `systemctl disable <服务名>`：禁用服务开机自动启动。
*   `systemctl is-enabled <服务名>`：检查服务是否设置为开机启动。

![](img/81d99981eb9b3b9c04525611819dc4d3_49.png)

**列出系统单元**
*   `systemctl list-units`：列出所有已加载的单元。
*   `systemctl list-unit-files`：列出所有已安装的单元文件。
*   `systemctl list-dependencies <服务名>`：查看指定服务的依赖关系。

**其他实用命令**
*   `systemctl poweroff`：关机。
*   `systemctl reboot`：重启。
*   `systemctl get-default` / `systemctl set-default <target>`：获取/设置默认启动目标（如图形界面`graphical.target`或多用户命令行`multi-user.target`）。

![](img/81d99981eb9b3b9c04525611819dc4d3_51.png)

**注意**：在使用`systemctl`管理服务时，通常可以省略单元文件的后缀`.service`。例如，`systemctl restart httpd` 等价于 `systemctl restart httpd.service`。

![](img/81d99981eb9b3b9c04525611819dc4d3_53.png)

![](img/81d99981eb9b3b9c04525611819dc4d3_55.png)

## 总结

![](img/81d99981eb9b3b9c04525611819dc4d3_57.png)

本节课中我们一起学习了Linux的服务管理。我们了解到`systemd`是现代Linux系统的初始化系统和服务管理器，它通过单元配置文件来定义和管理各种系统组件。我们重点掌握了使用`systemctl`命令进行服务的启动、停止、状态查看以及设置开机自启等日常操作。理解并熟练运用`systemctl`是进行系统管理和维护的基础技能。