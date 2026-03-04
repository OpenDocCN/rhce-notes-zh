# Linux服务管理：P5：RHCE-第5课-Linux服务-rsync服务 🔄

![](img/7c175c15c5f46490cfe440ea38952cab_1.png)

在本节课中，我们将要学习一个在企业集群架构中至关重要的数据同步服务——rsync。我们将从基础概念入手，逐步深入到其守护进程模式的配置，并最终结合自动化脚本实现实时同步，确保您能掌握其核心原理与实战应用。

---

## 概述

rsync，全称 Remote Sync，即远程同步。它是一款高效的数据备份与同步工具，其核心优势在于支持**增量备份**，即只传输发生变化的部分，从而极大地节省了带宽和时间。在企业环境中，rsync 常以守护进程（daemon）模式运行，提供持续的数据同步服务。

---

## rsync 基础概念

上一节我们介绍了课程概述，本节中我们来看看 rsync 的核心特性及其与其他工具的对比。

![](img/7c175c15c5f46490cfe440ea38952cab_3.png)

![](img/7c175c15c5f46490cfe440ea38952cab_5.png)

### rsync 与 SCP 的对比

rsync 与常见的 SCP 命令都能实现本地与远程之间的数据传输，但两者有本质区别：
*   **SCP**：始终进行**全量备份**。即使源文件中只有 1MB 的数据发生变化，SCP 也会将整个文件（例如 10GB）重新传输一次。
*   **rsync**：支持**增量备份**。它通过算法比较源和目标的差异，仅传输发生变化的数据块，效率极高。

![](img/7c175c15c5f46490cfe440ea38952cab_7.png)

![](img/7c175c15c5f46490cfe440ea38952cab_8.png)

![](img/7c175c15c5f46490cfe440ea38952cab_10.png)

![](img/7c175c15c5f46490cfe440ea38952cab_11.png)

### 备份类型

![](img/7c175c15c5f46490cfe440ea38952cab_13.png)

![](img/7c175c15c5f46490cfe440ea38952cab_14.png)

理解不同的备份策略有助于我们更好地应用 rsync。以下是常见的三种备份方式：

![](img/7c175c15c5f46490cfe440ea38952cab_16.png)

![](img/7c175c15c5f46490cfe440ea38952cab_18.png)

![](img/7c175c15c5f46490cfe440ea38952cab_20.png)

*   **完整备份**：每次备份都复制所有数据。消耗资源多，但恢复简单。
*   **增量备份**：基于**上一次备份**（无论是完整还是增量备份），只备份自上次备份以来发生变化的数据。恢复时需要按顺序应用所有增量备份。
*   **差异备份**：基于**上一次完整备份**，备份自那次完整备份以来所有发生变化的数据。恢复时只需要完整备份和最后一次差异备份。

![](img/7c175c15c5f46490cfe440ea38952cab_22.png)

![](img/7c175c15c5f46490cfe440ea38952cab_24.png)

**rsync 的核心优势正是实现了高效的增量备份。**

### rsync 的特点

除了增量备份，rsync 还具备以下特点：
*   **保持文件属性**：可以同步保留文件的权限 (`rwx`)、属主、属组、时间戳甚至软硬链接。
*   **目录同步**：支持递归同步整个目录树。
*   **压缩传输**：支持在传输过程中进行压缩，以节省带宽。
*   **灵活的使用模式**：既可以通过类似 SCP 的命令行方式直接使用，也可以配置为守护进程服务（CS 模式），监听在 **873** 端口。

### 同步方向：推 (Push) 与拉 (Pull)

![](img/7c175c15c5f46490cfe440ea38952cab_26.png)

在客户端-服务器模型中，同步有两个方向：
*   **推 (Push)**：由源服务器主动将数据发送到目标服务器。适用于下游服务器数量不多的情况。
*   **拉 (Pull)**：由目标服务器主动从源服务器拉取数据。当有大量客户端时，采用拉模式可以避免源服务器负载过高。

![](img/7c175c15c5f46490cfe440ea38952cab_28.png)

![](img/7c175c15c5f46490cfe440ea38952cab_30.png)

![](img/7c175c15c5f46490cfe440ea38952cab_31.png)

![](img/7c175c15c5f46490cfe440ea38952cab_32.png)

两者的命令格式与 SCP 类似：`rsync [选项] 源路径 目标路径`。推和拉的方向由源和目标的定义决定。

---

## rsync 守护进程模式实战

了解了基本概念后，本节我们将进行实战，配置 rsync 以守护进程模式运行。这是生产环境中最常用的方式。

### 实验环境准备

我们假设有两台主机：
*   **Master (主服务器)**：IP 为 `192.168.1.100`，作为数据源。
*   **Slave (从服务器)**：IP 为 `192.168.1.220`，作为数据备份目标。

![](img/7c175c15c5f46490cfe440ea38952cab_34.png)

![](img/7c175c15c5f46490cfe440ea38952cab_36.png)

**第一步：在双方服务器上安装 rsync**
```bash
yum install -y rsync
```
在较老的 RHEL/CentOS 6 中，rsync 可能由 `xinetd` 超级守护进程管理，可以一并安装 `xinetd`。在 7 及以上版本，rsync 可以独立运行。

![](img/7c175c15c5f46490cfe440ea38952cab_38.png)

**第二步：创建用于同步的专用用户（可选但推荐）**
为了避免直接使用 root 用户，我们在两台服务器上创建一个专门用于 rsync 同步的系统用户。
```bash
useradd rsync_user -s /sbin/nologin
echo “123456” | passwd --stdin rsync_user
```

**第三步：准备同步目录并设置权限**
在 Master 上准备要同步的 web 数据目录，并为其设置 ACL 权限，确保 `rsync_user` 有访问权。
```bash
mkdir -p /var/www/html
setfacl -m u:rsync_user:rwx /var/www/html
# 可选：设置默认 ACL，使新建文件也继承权限
# setfacl -m d:u:rsync_user:rwx /var/www/html
```
在 Slave 上创建备份目录。
```bash
mkdir -p /web_backup
```

### 配置 rsync 服务端 (Slave)

rsync 守护进程的配置文件为 `/etc/rsyncd.conf`。我们需要创建并编辑它。

**以下是配置文件 `/etc/rsyncd.conf` 的核心内容及说明：**
```bash
# 全局配置
port = 873
uid = root
gid = root
use chroot = yes
max connections = 5
pid file = /var/run/rsyncd.pid
lock file = /var/run/rsyncd.lock
log file = /var/log/rsyncd.log
motd file = /etc/rsyncd.motd

# 模块配置 - 可以定义多个模块
[web_backup]          # 模块名称，客户端通过此名称访问
    comment = for web data backup
    path = /web_backup # 服务器上该模块对应的真实路径
    read only = no     # 非只读，允许上传
    list = yes         # 允许列出模块
    auth users = rsync_backup_user # 允许访问该模块的虚拟用户（非系统用户）
    secrets file = /etc/rsyncd.password # 虚拟用户密码文件路径
```

**创建密码文件 `/etc/rsyncd.password`：**
```bash
echo “rsync_backup_user:123456” > /etc/rsyncd.password
chmod 600 /etc/rsyncd.password # 必须设置为600权限，否则同步会失败
```
**注意**：这里的 `rsync_backup_user` 是配置文件 `auth users` 中定义的虚拟用户，与系统用户 `rsync_user` 无关。

![](img/7c175c15c5f46490cfe440ea38952cab_40.png)

![](img/7c175c15c5f46490cfe440ea38952cab_42.png)

**创建欢迎信息文件（可选）：**
```bash
echo “Welcome to Rsync Server!” > /etc/rsyncd.motd
```

**启动 rsync 守护进程：**
```bash
rsync --daemon --config=/etc/rsyncd.conf
```
检查端口是否监听：
```bash
netstat -lntup | grep 873
```

### 客户端 (Master) 进行同步测试

![](img/7c175c15c5f46490cfe440ea38952cab_44.png)

![](img/7c175c15c5f46490cfe440ea38952cab_46.png)

现在，我们可以从 Master 向 Slave 的守护进程推送数据。

![](img/7c175c15c5f46490cfe440ea38952cab_48.png)

![](img/7c175c15c5f46490cfe440ea38952cab_49.png)

**方式一：交互式输入密码**
```bash
rsync -avz /var/www/html/ rsync_backup_user@192.168.1.220::web_backup
# 系统会提示输入 /etc/rsyncd.password 中为 rsync_backup_user 设置的密码
```
**参数解释**：
*   `-a`：归档模式，相当于 `-rlptgoD`，保持所有属性。
*   `-v`：详细输出。
*   `-z`：传输时压缩。
*   `/var/www/html/`：源路径，注意后面的 `/` 表示同步目录内的内容，而非目录本身。
*   `rsync_backup_user@192.168.1.220::web_backup`：`用户@服务器IP::模块名`

**方式二：使用密码文件（用于自动化脚本）**
1.  在 Master 上创建仅包含密码的文件：
    ```bash
    echo “123456” > /etc/rsync.password
    chmod 600 /etc/rsync.password
    ```
2.  使用 `--password-file` 参数进行同步，无需交互：
    ```bash
    rsync -avz --password-file=/etc/rsync.password /var/www/html/ rsync_backup_user@192.168.1.220::web_backup
    ```

---

![](img/7c175c15c5f46490cfe440ea38952cab_51.png)

## 结合脚本实现自动化

上一节我们完成了手动同步，本节中我们来看看如何通过脚本实现自动化备份，并探讨其局限性。

### 使用 Cron 定时任务

最简单的自动化方式是使用 Linux 的 `cron` 定时任务。例如，每天凌晨 3 点执行同步。

![](img/7c175c15c5f46490cfe440ea38952cab_53.png)

![](img/7c175c15c5f46490cfe440ea38952cab_54.png)

![](img/7c175c15c5f46490cfe440ea38952cab_56.png)

**创建同步脚本 `/root/backup.sh`：**
```bash
#!/bin/bash
# 这是一个简单的 rsync 备份脚本
rsync -avz --delete --password-file=/etc/rsync.password /var/www/html/ rsync_backup_user@192.168.1.220::web_backup
```
**给脚本添加执行权限并加入 cron：**
```bash
chmod +x /root/backup.sh
crontab -e
# 在打开的编辑器中添加一行：
0 3 * * * /root/backup.sh &
# & 表示后台运行
```

![](img/7c175c15c5f46490cfe440ea38952cab_58.png)

![](img/7c175c15c5f46490cfe440ea38952cab_59.png)

**定时任务的局限性**：
*   **精度问题**：最小执行单位是分钟，无法实现秒级实时同步。
*   **效率问题**：如果同步间隔内文件频繁变化，定时任务可能造成阻塞或数据丢失风险。
*   **资源浪费**：无论文件是否变化，到点就会执行一次完整的差异比较和可能的传输。

![](img/7c175c15c5f46490cfe440ea38952cab_61.png)

![](img/7c175c15c5f46490cfe440ea38952cab_63.png)

### 实现触发式实时同步

为了解决定时任务的不足，我们需要一种“触发式”同步：一旦源文件发生变化，立即触发同步。这需要借助文件系统监控工具。

![](img/7c175c15c5f46490cfe440ea38952cab_65.png)

![](img/7c175c15c5f46490cfe440ea38952cab_67.png)

![](img/7c175c15c5f46490cfe440ea38952cab_68.png)

**1. 内核工具 `inotify`**
`inotify` 可以监控文件或目录的元数据变化（如创建、修改、删除），但它本身不记录具体哪个文件变了，通常需要结合脚本，监控整个目录，一旦有变化就触发一次 `rsync` 全目录同步。对于大目录，效率依然不高。

![](img/7c175c15c5f46490cfe440ea38952cab_70.png)

![](img/7c175c15c5f46490cfe440ea38952cab_71.png)

**2. 推荐工具：`sersync`**
`sersync` 是基于 `inotify` 开发的增强工具，它利用 `inotify` 机制，但会**记录发生变化的文件或目录的具体路径**。在同步时，只针对这些变化的路径调用 `rsync` 命令，实现了真正的“增量中的增量”同步，效率极高。

![](img/7c175c15c5f46490cfe440ea38952cab_73.png)

**`sersync` 部署流程简介（在 Master 上操作）：**
1.  **下载并解压** `sersync` 工具包。
2.  **编辑配置文件 `confxml.xml`**：主要配置监控的本地目录 (`watch`)、远程服务器IP、模块名、用户名、密码文件路径等。
3.  **启动 `sersync`**：它会以后台守护进程运行，持续监控指定目录。
    ```bash
    ./sersync2 -d -r -o ./confxml.xml
    ```
4.  **验证**：在 Master 的监控目录内创建、修改或删除文件，可以立即在 Slave 的备份目录中看到对应变化。

![](img/7c175c15c5f46490cfe440ea38952cab_75.png)

![](img/7c175c15c5f46490cfe440ea38952cab_76.png)

![](img/7c175c15c5f46490cfe440ea38952cab_78.png)

![](img/7c175c15c5f46490cfe440ea38952cab_80.png)

**`sersync` 的优势**：
*   **精准同步**：只同步变化文件，资源占用少。
*   **多实例监控**：可以通过多个配置文件，轻松监控多个不同的目录。
*   **结合 rsync**：完美调用 rsync 进行传输，复用已有的认证和配置。

![](img/7c175c15c5f46490cfe440ea38952cab_82.png)

![](img/7c175c15c5f46490cfe440ea38952cab_84.png)

![](img/7c175c15c5f46490cfe440ea38952cab_86.png)

我们可以为 `sersync` 编写一个监控脚本，检查其进程是否存在，若不存在则自动启动，从而确保实时同步服务的高可用性。

---

## 总结

本节课中我们一起学习了 Linux 下强大的数据同步服务 rsync。

我们首先理解了其**增量备份**的核心价值，以及它与 SCP 工具的区别。接着，我们重点演练了如何在生产环境中配置 **rsync 守护进程模式**，包括服务端配置、虚拟用户认证以及客户端的各种同步方法。

为了解放人力，我们探讨了通过 **Cron 定时任务**实现自动化备份，并分析了其时间精度和效率上的局限。最后，我们引入了更先进的 **`sersync` 工具**，它通过监控文件系统事件，实现了真正的**触发式实时同步**，这是构建高效、自动化数据备份方案的推荐实践。

![](img/7c175c15c5f46490cfe440ea38952cab_88.png)

通过本课的学习，您应该能够独立搭建一个基于 rsync 的数据同步环境，并根据实际需求选择合适的自动化策略，为维护集群数据的一致性和可靠性打下坚实基础。