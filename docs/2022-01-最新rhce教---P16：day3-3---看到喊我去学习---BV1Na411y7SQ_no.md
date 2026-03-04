# RHCE 课程：P16：Day3-3 - Cockpit 界面与系统服务管理 🖥️

![](img/57aadb1ad70d6f1193309ca16d1d3f83_1.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_3.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_5.png)

在本节课中，我们将学习 Red Hat Enterprise Linux 8 的两个核心特性：Cockpit 网页管理界面和 systemd 服务管理系统。我们将从配置本地软件仓库开始，安装并访问 Cockpit，然后深入探讨进程信号、作业管理以及 systemd 服务的管理方法。

![](img/57aadb1ad70d6f1193309ca16d1d3f83_7.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_9.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_11.png)

## 概述：Cockpit 网页管理界面

![](img/57aadb1ad70d6f1193309ca16d1d3f83_13.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_15.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_17.png)

Cockpit 是 RHEL 8 引入的一个基于 Web 的服务器管理工具。它是一个 Web 应用程序，可以通过浏览器访问，提供直观的图形界面来执行多种系统管理任务。

![](img/57aadb1ad70d6f1193309ca16d1d3f83_19.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_21.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_23.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_25.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_27.png)

Cockpit 主要提供以下功能：
*   监视系统活动，例如 CPU、内存、磁盘 I/O 和网络流量。
*   查看系统日志、磁盘和用户信息。
*   检查系统服务的状态。

![](img/57aadb1ad70d6f1193309ca16d1d3f83_29.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_31.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_32.png)

访问方式是通过 HTTP 协议，使用服务器的 IP 地址和特定端口（默认为 9090）在浏览器中打开。

![](img/57aadb1ad70d6f1193309ca16d1d3f83_34.png)

## 配置本地软件仓库与安装 Cockpit

要安装 Cockpit，首先需要配置一个本地软件仓库，以便从安装镜像中获取软件包。

![](img/57aadb1ad70d6f1193309ca16d1d3f83_36.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_37.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_39.png)

以下是配置本地软件仓库的步骤：

![](img/57aadb1ad70d6f1193309ca16d1d3f83_41.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_42.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_44.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_46.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_48.png)

1.  **挂载安装镜像**：在虚拟机设置中，将 RHEL 8 的 ISO 镜像文件连接到虚拟机的 CD/DVD 驱动器。
2.  **创建仓库配置文件**：在系统中创建软件仓库的配置文件，指向挂载的镜像。
    *   仓库文件通常位于 `/etc/yum.repos.d/` 目录下。
    *   配置文件需要指定仓库名称（`name`）、仓库地址（`baseurl`）以及是否启用（`enabled=1`）。
    *   仓库地址应指向镜像中存储软件包的两个关键目录：`BaseOS` 和 `AppStream`。

![](img/57aadb1ad70d6f1193309ca16d1d3f83_49.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_51.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_52.png)

一个示例仓库配置文件内容如下：
```ini
[local_baseos]
name=Local BaseOS Repository
baseurl=file:///mnt/BaseOS
enabled=1
gpgcheck=0

![](img/57aadb1ad70d6f1193309ca16d1d3f83_54.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_55.png)

[local_appstream]
name=Local AppStream Repository
baseurl=file:///mnt/AppStream
enabled=1
gpgcheck=0
```
3.  **清理并更新缓存**：创建仓库后，需要清理旧的 YUM/DNF 缓存并生成新的缓存。
    ```bash
    dnf clean all
    dnf makecache
    ```

![](img/57aadb1ad70d6f1193309ca16d1d3f83_57.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_58.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_60.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_62.png)

配置好仓库后，即可使用 `dnf` 命令安装 `cockpit` 软件包。
```bash
sudo dnf install cockpit -y
```

![](img/57aadb1ad70d6f1193309ca16d1d3f83_64.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_65.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_67.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_69.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_71.png)

安装完成后，启动 Cockpit 服务并设置开机自启。
```bash
sudo systemctl enable --now cockpit.socket
```

![](img/57aadb1ad70d6f1193309ca16d1d3f83_73.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_75.png)

## 访问与使用 Cockpit

![](img/57aadb1ad70d6f1193309ca16d1d3f83_77.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_79.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_81.png)

服务启动后，即可在浏览器中通过 `https://<服务器IP地址>:9090` 进行访问。可以使用系统上的用户（如 `root` 或普通用户）及其密码登录。

![](img/57aadb1ad70d6f1193309ca16d1d3f83_83.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_85.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_86.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_88.png)

登录后，你将看到 Cockpit 的主界面，其中包含：
*   **系统概览**：实时显示 CPU、内存、磁盘和网络的使用情况。
*   **日志查看器**：集中查看系统日志。
*   **存储管理**：查看磁盘驱动器、存储池和文件系统。
*   **网络管理**：查看和配置网络接口。
*   **账户管理**：可以直接在界面中创建、删除用户或修改用户密码。
*   **服务管理**：查看和控制 systemd 服务的状态。

![](img/57aadb1ad70d6f1193309ca16d1d3f83_90.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_92.png)

Cockpit 提供了一个比命令行更直观的方式来执行常见的系统管理任务。

![](img/57aadb1ad70d6f1193309ca16d1d3f83_94.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_96.png)

上一节我们介绍了通过图形界面管理系统的 Cockpit，本节中我们来看看如何在命令行下管理进程和系统服务。

## 进程信号管理：kill 与 killall

![](img/57aadb1ad70d6f1193309ca16d1d3f83_98.png)

在 Linux 中，`kill` 命令用于向进程发送信号，以控制其行为。最常用的信号是终止进程。

![](img/57aadb1ad70d6f1193309ca16d1d3f83_100.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_102.png)

以下是常用的信号及其作用：
*   `kill -9` 或 `kill -KILL`：强制立即终止进程。
*   `kill -15` 或 `kill -TERM`：请求进程正常终止（默认信号）。
*   `kill -1` 或 `kill -HUP`：让进程重新读取其配置文件，而无需重启。
*   `kill -2` 或 `kill -INT`：中断进程（相当于在终端按 `Ctrl+C`）。
*   `kill -18` 或 `kill -CONT`：继续运行一个被暂停的进程。
*   `kill -19` 或 `kill -STOP`：暂停一个进程（相当于在终端按 `Ctrl+Z`）。

![](img/57aadb1ad70d6f1193309ca16d1d3f83_104.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_105.png)

使用 `kill -l` 命令可以列出所有可用的信号。

![](img/57aadb1ad70d6f1193309ca16d1d3f83_107.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_108.png)

`killall` 命令与 `kill` 功能类似，但它通过进程名来发送信号，而无需先查找进程 ID (PID)。例如，`killall -9 ping` 会终止所有名为 `ping` 的进程。

特殊信号 `0` 用于检查进程是否存在，而不对其做任何操作。例如，`kill -0 <PID>` 如果进程存在则命令成功返回，否则失败。

## 作业管理：前台与后台

在 Linux 终端中，作业分为前台作业和后台作业。
*   **前台作业**：启动后占据当前终端，在它结束或挂起前，无法在同一终端执行其他命令。
*   **后台作业**：启动后即转入后台运行，释放当前终端，允许用户继续输入其他命令。

管理作业的常用方法如下：
*   在命令末尾添加 `&` 符号，可以使命令在后台启动。例如：`ping example.com &`。
*   使用 `Ctrl+Z` 可以将当前前台作业暂停，并放入后台。
*   使用 `jobs` 命令可以查看当前终端的所有后台作业。
*   使用 `fg %<作业编号>` 可以将指定的后台作业调回前台运行。
*   使用 `bg %<作业编号>` 可以让一个被暂停的后台作业继续在后台运行。
*   使用 `kill %<作业编号>` 可以终止指定的后台作业。

## 系统服务管理：systemd

RHEL 7 及更高版本使用 `systemd` 作为初始进程和服务管理器，取代了旧的 `SysV init` 系统。

`systemd` 的核心概念是 **单元（Unit）**。单元由配置文件定义，用于描述和管理各种系统资源。单元文件有不同的类型，以扩展名区分：
*   **`.service`**：定义系统服务。
*   **`.socket`**：定义进程间通信的套接字，可用于按需启动服务。
*   **`.target`**：定义一组单元，用于模拟传统的运行级别（如 multi-user.target 对应级别 3，graphical.target 对应级别 5）。

![](img/57aadb1ad70d6f1193309ca16d1d3f83_110.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_111.png)

单元配置文件主要存放在以下位置：
*   `/usr/lib/systemd/system/`：系统安装的软件包提供的单元文件。
*   `/etc/systemd/system/`：系统管理员创建和管理的单元文件（优先级更高）。

### 管理 systemd 服务

![](img/57aadb1ad70d6f1193309ca16d1d3f83_113.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_114.png)

使用 `systemctl` 命令来管理 systemd 服务。

![](img/57aadb1ad70d6f1193309ca16d1d3f83_116.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_118.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_120.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_122.png)

以下是常用的服务管理命令：
*   **启动服务**：`sudo systemctl start <服务名>`
*   **停止服务**：`sudo systemctl stop <服务名>`
*   **重启服务**：`sudo systemctl restart <服务名>`
*   **重新加载配置**：`sudo systemctl reload <服务名>` （不重启服务，仅重新加载配置文件）
*   **查看服务状态**：`sudo systemctl status <服务名>`
*   **设置开机自启**：`sudo systemctl enable <服务名>`
*   **禁用开机自启**：`sudo systemctl disable <服务名>`
*   **查看服务是否启用**：`sudo systemctl is-enabled <服务名>`
*   **列出所有已激活的服务**：`systemctl list-units --type=service --state=active`
*   **列出所有失败的服务**：`systemctl --failed`

![](img/57aadb1ad70d6f1193309ca16d1d3f83_124.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_126.png)

例如，管理 `httpd` (Apache) 服务：
```bash
# 查看状态
sudo systemctl status httpd
# 启动服务
sudo systemctl start httpd
# 设置开机自启
sudo systemctl enable httpd
# 停止服务并禁用开机自启
sudo systemctl disable --now httpd
```

![](img/57aadb1ad70d6f1193309ca16d1d3f83_128.png)

![](img/57aadb1ad70d6f1193309ca16d1d3f83_130.png)

## 总结

![](img/57aadb1ad70d6f1193309ca16d1d3f83_132.png)

本节课中我们一起学习了 RHEL 8 的两个重要管理工具。首先，我们配置了本地 YUM 仓库，安装并使用了 **Cockpit** 网页管理界面，它提供了监控系统、管理存储、用户和服务的图形化方式。接着，我们深入命令行，学习了如何使用 **`kill`** 和 **`killall`** 命令向进程发送不同的控制信号，以及如何管理 **前台和后台作业**。最后，我们探讨了现代 Linux 系统的服务管理器 **`systemd`**，掌握了使用 **`systemctl`** 命令启动、停止、启用、禁用和查看系统服务状态的核心技能。这些知识是进行日常系统管理和故障排查的基础。