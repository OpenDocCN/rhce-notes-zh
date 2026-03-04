# Redhat红帽 RHCE8.0认证体系课程：P28：Day05课程回顾 📚

在本节课中，我们将回顾Day04课程的核心内容，包括日志管理、网络配置、文件归档与传输以及软件包管理。这些是RHCE认证体系中的基础且重要的知识点。

## 日志管理 📝

![](img/aab94431ee6d33a3b832e05f37f55955_1.png)

上一节我们介绍了系统初始化，本节中我们来看看如何管理系统日志。日志是排查系统问题的重要依据。

![](img/aab94431ee6d33a3b832e05f37f55955_3.png)

### 系统日志服务：systemd-journald

`systemd-journald` 服务负责收集和管理系统启动后的所有日志。日志默认以二进制格式存储在内存中，路径为 `/run/log/journal/`。

查看日志的主要命令是 `journalctl`。以下是常用参数：
*   `journalctl -f`：实时跟踪日志末尾。
*   `journalctl -n [行数]`：显示指定行数的日志。
*   `journalctl --since [时间] --until [时间]`：显示特定时间段的日志。

若要查看单个服务的日志，可以使用：
```bash
systemctl status [服务名]
```
此命令会显示服务的状态及其最近几行日志。

![](img/aab94431ee6d33a3b832e05f37f55955_5.png)

![](img/aab94431ee6d33a3b832e05f37f55955_6.png)

### 重要日志文件

除了 `journald`，系统还有其他重要的文本日志文件：
*   `/var/log/messages`：记录除系统启动外的大部分常规日志。
*   `/var/log/dmesg`：记录从系统引导开始到加载完毕的内核日志。
*   `/var/log/secure`：记录与身份验证和安全相关的日志，如用户登录。
*   `/var/log/boot.log`：记录系统启动时屏幕显示的信息。

### 配置日志持久化

![](img/aab94431ee6d33a3b832e05f37f55955_8.png)

![](img/aab94431ee6d33a3b832e05f37f55955_10.png)

默认情况下，`journald` 的日志存储在内存中。若要永久保存，需要编辑配置文件 `/etc/systemd/journald.conf`，将 `Storage=` 选项设置为 `persistent`，然后重启 `systemd-journald` 服务。持久化后的日志存储在 `/var/log/journal/` 目录下，但仍需使用 `journalctl` 命令查看。

### 日志轮转：logrotate

![](img/aab94431ee6d33a3b832e05f37f55955_12.png)

系统使用 `logrotate` 工具自动管理日志文件，防止其无限增大。默认规则通常是每周轮转一次，并保留最近四周的日志。轮转后，旧日志会被添加日期后缀并压缩，然后服务会重新打开一个新日志文件。

### 时间同步 ⏰

![](img/aab94431ee6d33a3b832e05f37f55955_14.png)

![](img/aab94431ee6d33a3b832e05f37f55955_16.png)

准确的时间对于日志分析至关重要。在RHEL 8中，时间同步服务是 `chronyd`。

![](img/aab94431ee6d33a3b832e05f37f55955_18.png)

![](img/aab94431ee6d33a3b832e05f37f55955_20.png)

![](img/aab94431ee6d33a3b832e05f37f55955_22.png)

查看和设置时间的命令是 `timedatectl`。例如，设置时区为上海：
```bash
timedatectl set-timezone Asia/Shanghai
```
若要手动设置时间，需先关闭网络时间同步：
```bash
timedatectl set-ntp false
timedatectl set-time “YYYY-MM-DD HH:MM:SS”
```

![](img/aab94431ee6d33a3b832e05f37f55955_24.png)

![](img/aab94431ee6d33a3b832e05f37f55955_26.png)

配置 `chronyd` 同步远程时间服务器，需编辑 `/etc/chrony.conf` 文件，添加或修改 `server` 行，例如：
```bash
server classroom.example.com iburst
```
配置完成后，重启 `chronyd` 服务并检查同步状态：
```bash
systemctl restart chronyd
chronyc sources -v
```

## 网络管理 🌐

网络配置是系统管理员的日常任务。在RHEL 8中，网络由 `NetworkManager` 服务管理。

### 网络接口配置

![](img/aab94431ee6d33a3b832e05f37f55955_28.png)

添加或配置网络接口主要有两种方法。

**方法一：使用 `nmcli` 命令**
使用 `nmcli` 可以快速添加和配置连接。例如，为网卡 `ens256` 添加一个名为 `ens256` 的连接并配置静态IP：
```bash
nmcli connection add type ethernet ifname ens256 con-name ens256
nmcli connection modify ens256 ipv4.addresses 192.168.1.100/24 ipv4.gateway 192.168.1.1 ipv4.dns 8.8.8.8 ipv4.method manual
nmcli connection up ens256
```
关键是将 `ipv4.method` 设置为 `manual`（手动）。

![](img/aab94431ee6d33a3b832e05f37f55955_30.png)

![](img/aab94431ee6d33a3b832e05f37f55955_31.png)

![](img/aab94431ee6d33a3b832e05f37f55955_33.png)

![](img/aab94431ee6d33a3b832e05f37f55955_35.png)

**方法二：直接编辑配置文件**
更直接的方法是编辑网卡配置文件，路径为 `/etc/sysconfig/network-scripts/ifcfg-[连接名]`。
以下是一个配置示例：
```bash
TYPE=Ethernet
BOOTPROTO=none
DEFROUTE=yes
NAME=ens256
DEVICE=ens256
ONBOOT=yes
IPADDR=192.168.1.100
PREFIX=24
GATEWAY=192.168.1.1
DNS1=8.8.8.8
```
**特别注意**：务必准确拼写这些配置项（如 `IPADDR`， `GATEWAY`），拼写错误会导致网络不通。

![](img/aab94431ee6d33a3b832e05f37f55955_37.png)

![](img/aab94431ee6d33a3b832e05f37f55955_39.png)

配置完成后，重新加载连接使配置生效：
```bash
nmcli connection reload
nmcli connection down ens256 && nmcli connection up ens256
```

### 链路聚合（Team）

![](img/aab94431ee6d33a3b832e05f37f55955_41.png)

![](img/aab94431ee6d33a3b832e05f37f55955_43.png)

![](img/aab94431ee6d33a3b832e05f37f55955_45.png)

链路聚合可以将多个物理网卡绑定为一个逻辑接口，实现负载均衡或冗余。虽然RHCE 8.0考试不考，但在企业环境中非常重要。配置时可以参考 `/usr/share/doc/teamd/` 目录下的示例配置文件。

![](img/aab94431ee6d33a3b832e05f37f55955_47.png)

![](img/aab94431ee6d33a3b832e05f37f55955_49.png)

## 文件归档与传输 📦

![](img/aab94431ee6d33a3b832e05f37f55955_51.png)

文件打包、压缩和传输是常见的运维操作。

![](img/aab94431ee6d33a3b832e05f37f55955_53.png)

![](img/aab94431ee6d33a3b832e05f37f55955_55.png)

### 归档与压缩

![](img/aab94431ee6d33a3b832e05f37f55955_57.png)

![](img/aab94431ee6d33a3b832e05f37f55955_59.png)

![](img/aab94431ee6d33a3b832e05f37f55955_60.png)

常用的压缩格式有三种：`gzip` (`.gz`)， `bzip2` (`.bz2`) 和 `xz` (`.xz`)。其中 `xz` 压缩率通常最高。
使用 `tar` 命令进行打包和压缩：
*   **打包并压缩**：`tar -czvf archive.tar.gz /path/to/dir` (使用gzip)
*   **解压到指定目录**：`tar -xzvf archive.tar.gz -C /target/path`
*   **仅列出内容**：`tar -tzvf archive.tar.gz`

![](img/aab94431ee6d33a3b832e05f37f55955_62.png)

![](img/aab94431ee6d33a3b832e05f37f55955_64.png)

### 文件传输

![](img/aab94431ee6d33a3b832e05f37f55955_66.png)

*   **`scp`**：基于SSH的安全拷贝，用于在主机间传输文件。例如：`scp file.txt user@remotehost:/path/`。
*   **`rsync`**：支持增量同步，效率更高，常用于备份。例如：`rsync -avz /local/dir/ user@remotehost:/remote/dir/`。

## 软件包管理 📦

RHEL 8使用 `dnf`（或 `yum`）作为主要的软件包管理器。

![](img/aab94431ee6d33a3b832e05f37f55955_68.png)

![](img/aab94431ee6d33a3b832e05f37f55955_70.png)

### RPM包基础

RPM是软件包格式。查询已安装RPM包信息的常用命令：
*   `rpm -qa`：列出所有已安装的包。
*   `rpm -qc [包名]`：列出包的配置文件。
*   `rpm -qf [文件名]`：查询文件由哪个包安装。
*   `rpm -qi [包名]`：查询包的详细信息。

![](img/aab94431ee6d33a3b832e05f37f55955_72.png)

![](img/aab94431ee6d33a3b832e05f37f55955_74.png)

### 配置软件仓库（Yum/DNF Repository）

![](img/aab94431ee6d33a3b832e05f37f55955_75.png)

![](img/aab94431ee6d33a3b832e05f37f55955_77.png)

![](img/aab94431ee6d33a3b832e05f37f55955_78.png)

要使用 `dnf` 安装软件，需要先配置软件源。对于本地镜像源，配置文件（如 `/etc/yum.repos.d/local.repo`）内容如下：
```bash
[BaseOS]
name=BaseOS
baseurl=file:///mnt/BaseOS
enabled=1
gpgcheck=0

![](img/aab94431ee6d33a3b832e05f37f55955_80.png)

[AppStream]
name=AppStream
baseurl=file:///mnt/AppStream
enabled=1
gpgcheck=0
```
**注意**：RHEL 8的仓库分为 `BaseOS`（基础系统包）和 `AppStream`（应用流包），通常需要同时配置。`baseurl` 中使用 `file://` 协议时，路径需以三个斜杠开头。

![](img/aab94431ee6d33a3b832e05f37f55955_82.png)

![](img/aab94431ee6d33a3b832e05f37f55955_84.png)

![](img/aab94431ee6d33a3b832e05f37f55955_86.png)

### DNF基本用法

`dnf` 和 `yum` 命令在RHEL 8中通用。
*   `dnf makecache`：建立元数据缓存。
*   `dnf repolist`：列出已配置的仓库。
*   `dnf install [包名]`：安装软件包。
*   `dnf remove [包名]`：移除软件包。
*   `dnf provides [命令或文件]`：查找哪个包提供特定命令或文件。

![](img/aab94431ee6d33a3b832e05f37f55955_88.png)

### 模块化应用流

RHEL 8引入了模块（Module）的概念，用于管理应用软件的不同版本流（Stream）。这允许你在同一系统上安装和维护同一个软件的不同主要版本。例如，可以同时拥有 `PostgreSQL 10` 和 `PostgreSQL 12` 的模块。管理模块的命令也是 `dnf`：
```bash
dnf module list [模块名] # 列出可用模块
dnf module install [模块名]:[流]/[profile] # 安装特定模块流
```

![](img/aab94431ee6d33a3b832e05f37f55955_90.png)

本节课中我们一起学习了RHCE 8.0课程中关于日志管理、网络配置、文件操作和软件包管理的核心知识。掌握这些内容是成为一名合格系统管理员的基础，也为后续更深入的学习和服务配置打下了坚实的基础。请务必通过实验巩固这些操作。