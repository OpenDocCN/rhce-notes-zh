# Linux服务管理：15-1：服务与守护进程基础 🚀

在本节课中，我们将学习Linux系统中服务与守护进程的基本概念，以及如何使用 `systemctl` 命令来管理它们。理解服务的管理是系统运维的核心技能之一。

## 概述

系统运行的所有进程，最终都以服务的形式存在。在Linux 8及以后的版本中，服务通过 **systemd** 系统和服务管理器进行管理，并以 **单元** 的形式组织。所有服务进程都是从 `systemd` 这个初始进程 `fork` 出来的。

因此，系统服务的名称通常以 `d` 结尾，例如 `autofsd`、`httpd`。这些服务的配置文件则以 `.service` 为扩展名，存放在特定的单元目录中。

## 服务单元类型

![](img/876feeb68652f7a8d6dd86d7d76cb506_1.png)

![](img/876feeb68652f7a8d6dd86d7d76cb506_3.png)

在systemd体系中，服务被分为几种单元类型。以下是主要的单元类型及其后缀：

![](img/876feeb68652f7a8d6dd86d7d76cb506_5.png)

![](img/876feeb68652f7a8d6dd86d7d76cb506_7.png)

*   **服务单元**：以 `.service` 结尾，对应系统服务。
*   **套接字单元**：以 `.socket` 结尾，用于管理网络套接字或进程间通信套接字。
*   **路径单元**：以 `.path` 结尾，用于根据文件系统上的变化来激活其他服务。

![](img/876feeb68652f7a8d6dd86d7d76cb506_9.png)

管理这些服务的核心命令是 `systemctl`。

![](img/876feeb68652f7a8d6dd86d7d76cb506_11.png)

## 使用 systemctl 管理服务

![](img/876feeb68652f7a8d6dd86d7d76cb506_13.png)

![](img/876feeb68652f7a8d6dd86d7d76cb506_15.png)

上一节我们介绍了服务的基本概念，本节中我们来看看如何使用 `systemctl` 命令来查看和管理这些服务单元。

![](img/876feeb68652f7a8d6dd86d7d76cb506_17.png)

![](img/876feeb68652f7a8d6dd86d7d76cb506_19.png)

### 查看单元类型与状态

![](img/876feeb68652f7a8d6dd86d7d76cb506_21.png)

![](img/876feeb68652f7a8d6dd86d7d76cb506_23.png)

![](img/876feeb68652f7a8d6dd86d7d76cb506_25.png)

以下是用于查看系统服务状态的一系列 `systemctl` 命令。

![](img/876feeb68652f7a8d6dd86d7d76cb506_27.png)

![](img/876feeb68652f7a8d6dd86d7d76cb506_29.png)

**1. 显示所有可用的单元类型**
这个命令列出systemd支持的所有单元类型。
```bash
systemctl -t help
```

![](img/876feeb68652f7a8d6dd86d7d76cb506_31.png)

**2. 列出所有已加载的服务单元**
这个命令专门列出当前系统中所有类型为“服务”的单元。
```bash
systemctl list-units --type=service
```

![](img/876feeb68652f7a8d6dd86d7d76cb506_33.png)

**3. 列出所有已加载和活动的单元**
这个命令列出所有当前已加载且处于活动状态的单元，不限于服务类型。
```bash
systemctl list-units
```

![](img/876feeb68652f7a8d6dd86d7d76cb506_35.png)

**4. 查看指定服务的详细状态**
这是最常用的命令之一，用于查看某个具体服务的运行状态、日志片段等信息。
```bash
systemctl status sshd.service
```

**5. 检查服务的活跃状态与启用状态**
这两个命令用于快速检查服务当前是否运行，以及是否配置为开机自启。这是两个关键且易混淆的概念。
*   `is-active`：检查服务**当前**是否处于运行状态。
*   `is-enabled`：检查服务是否配置为在系统启动时**自动启动**。
```bash
systemctl is-active sshd.service
systemctl is-enabled sshd.service
```
> **重要提示**：一个服务当前是 `active` 状态，并不意味着它开机时会自动启动。务必在配置服务后，确认其 `enabled` 状态。

![](img/876feeb68652f7a8d6dd86d7d76cb506_37.png)

**6. 列出所有运行失败的服务**
当系统出现问题时，此命令能快速定位出故障的服务单元。
```bash
systemctl --failed --type=service
```

![](img/876feeb68652f7a8d6dd86d7d76cb506_39.png)

### 控制服务生命周期

![](img/876feeb68652f7a8d6dd86d7d76cb506_41.png)

![](img/876feeb68652f7a8d6dd86d7d76cb506_43.png)

了解服务状态后，我们需要掌握如何控制服务的启动、停止等操作。

以下是控制服务运行状态的核心命令：
*   `systemctl start <服务名>`：启动一个服务。
*   `systemctl stop <服务名>`：停止一个服务。
*   `systemctl restart <服务名>`：重启一个服务。此操作会先停止服务，再从头启动它及其所有相关进程。
*   `systemctl reload <服务名>`：重新加载服务的配置文件。此操作不会重启服务进程，仅使其应用新的配置。
*   `systemctl enable <服务名>`：设置服务开机自动启动。
*   `systemctl disable <服务名>`：取消服务开机自动启动。

### 查看服务依赖关系

服务之间可能存在依赖。例如，某些服务启动前需要其他服务或资源就绪。以下命令用于分析这种依赖关系。

![](img/876feeb68652f7a8d6dd86d7d76cb506_45.png)

**列出指定单元所依赖的其他单元**
这个命令可以显示启动某个服务前，需要先确保哪些其他单元已就绪。
```bash
systemctl list-dependencies sshd.service
```

![](img/876feeb68652f7a8d6dd86d7d76cb506_47.png)

![](img/876feeb68652f7a8d6dd86d7d76cb506_49.png)

## 总结

![](img/876feeb68652f7a8d6dd86d7d76cb506_51.png)

![](img/876feeb68652f7a8d6dd86d7d76cb506_53.png)

本节课中我们一起学习了Linux服务管理的基础知识。我们明确了服务、守护进程与 `systemd` 的关系，认识了以 `.service` 结尾的服务单元。我们重点掌握了使用 `systemctl` 命令来查看服务状态、控制服务运行（`start`， `stop`， `restart`， `reload`）以及配置开机自启（`enable`， `disable`）。特别要注意区分服务的“当前活动状态”和“开机启用状态”。最后，我们还了解了如何查看服务间的依赖关系，这对于排查复杂问题非常有帮助。熟练掌握这些命令是进行日常系统运维和故障排查的基础。