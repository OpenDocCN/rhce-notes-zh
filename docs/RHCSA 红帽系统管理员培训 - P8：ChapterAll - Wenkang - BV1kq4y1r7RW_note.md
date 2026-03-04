# RHCSA 红帽系统管理员培训：P8：核心系统管理任务

在本节课中，我们将学习一系列RHCSA认证考试中涉及的核心系统管理任务，包括SSH密钥认证、系统时间配置、网络管理、文件压缩与传输、软件包管理以及文件系统操作。这些技能是日常管理Linux系统的基础。

## SSH密钥认证 🔑

上一节我们介绍了系统管理的基础概念，本节中我们来看看如何配置SSH免密码登录，这是实现安全远程管理的关键。

首先，使用 `ssh-keygen` 命令生成密钥对。默认情况下，密钥会存储在用户家目录的 `.ssh/` 文件夹中。

![](img/00a07543b68df0cc6ad3040bf76dc6ea_1.png)

```bash
ssh-keygen
```

执行命令后，系统会询问密钥的保存位置和是否设置密码短语。生成后，在 `.ssh/` 目录下会看到两个文件：`id_rsa`（私钥）和 `id_rsa.pub`（公钥）。私钥必须妥善保管，公钥则可以分发给需要登录的服务器。

为了让客户端能免密码登录服务器，需要将客户端的公钥内容添加到服务器的授权文件中。以下是具体步骤：

1.  在服务器上，编辑SSH服务配置文件 `/etc/ssh/sshd_config`，找到 `PasswordAuthentication` 选项并将其改为 `no`，以禁用密码登录，增强安全性。
2.  将客户端的公钥（`id_rsa.pub` 文件内容）追加到服务器对应用户家目录下的 `~/.ssh/authorized_keys` 文件中。

![](img/00a07543b68df0cc6ad3040bf76dc6ea_3.png)

完成配置后，使用 `ssh` 命令连接时，将自动使用密钥进行认证，无需输入密码。

![](img/00a07543b68df0cc6ad3040bf76dc6ea_5.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_7.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_9.png)

如果需要管理多台服务器或使用不同的密钥，可以在生成密钥时指定不同的文件名，并在连接时使用 `-i` 选项指定对应的私钥文件。

```bash
ssh -i ~/.ssh/id_rsa_server2 user@server2
```

## 系统时间与日志管理 ⏰

上一节我们介绍了SSH安全登录，本节中我们来看看如何管理系统时间和日志。

![](img/00a07543b68df0cc6ad3040bf76dc6ea_11.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_13.png)

系统时间由 `systemd` 下的 `timedatectl` 命令管理。直接运行该命令可以查看当前系统的多种时间：

![](img/00a07543b68df0cc6ad3040bf76dc6ea_15.png)

*   **本地时间 (Local time)**：根据系统时区显示的当前时间。
*   **协调世界时 (Universal time, UTC)**：全球标准时间基准。
*   **硬件时间 (RTC time)**：主板BIOS中存储的时间。

![](img/00a07543b68df0cc6ad3040bf76dc6ea_17.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_19.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_21.png)

为了获得最精确的时间，通常需要配置系统使用网络时间协议（NTP）进行同步。以下是配置步骤：

1.  确保NTP服务已启用：`timedatectl set-ntp true`
2.  安装并启动 `chronyd` 服务（RHEL 8默认的NTP客户端）。
3.  编辑其配置文件 `/etc/chrony.conf`，添加或修改 `server` 指令指向可靠的时间服务器，例如中国的 `ntp.ntsc.ac.cn`。
4.  重启 `chronyd` 服务使配置生效。

系统日志记录了所有服务和内核的活动信息。除了直接查看 `/var/log/` 目录下的日志文件（如 `/var/log/messages`），还可以使用 `journalctl` 命令。该命令基于 `systemd-journald` 服务，提供了更强大的日志查询功能，例如按时间、服务单元或日志级别进行筛选。

```bash
# 查看指定服务的日志
journalctl -u sshd

# 查看特定时间段的日志
journalctl --since "2021-12-01" --until "2021-12-02"

# 仅查看错误级别的日志
journalctl -p err
```

![](img/00a07543b68df0cc6ad3040bf76dc6ea_23.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_25.png)

默认情况下，`journald` 的日志存储在内存中（`/run/log/journal/`），重启后会丢失。若要持久化保存，需要手动创建 `/var/log/journal/` 目录。

![](img/00a07543b68df0cc6ad3040bf76dc6ea_27.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_29.png)

## 网络配置与主机名 🌐

![](img/00a07543b68df0cc6ad3040bf76dc6ea_31.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_33.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_35.png)

上一节我们学习了时间同步，本节中我们来看看如何配置网络和主机名。

在RHEL 8中，网络由 `NetworkManager` 服务管理。确保该服务处于运行状态后，可以使用文本用户界面工具 `nmtui` 进行直观配置。

```bash
systemctl status NetworkManager
nmtui
```

在 `nmtui` 界面中，可以“编辑连接”来设置IP地址（静态或DHCP）、网关和DNS服务器。配置完成后，需要“激活”连接。

![](img/00a07543b68df0cc6ad3040bf76dc6ea_37.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_39.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_41.png)

查看网络接口信息，可以使用 `ip addr` 命令。若要修改主机名，有两种方法：

![](img/00a07543b68df0cc6ad3040bf76dc6ea_43.png)

1.  使用 `hostnamectl` 命令：`hostnamectl set-hostname newhostname.example.com`
2.  直接编辑配置文件 `/etc/hostname`。

![](img/00a07543b68df0cc6ad3040bf76dc6ea_45.png)

这两种方法最终都是修改 `/etc/hostname` 文件的内容。

![](img/00a07543b68df0cc6ad3040bf76dc6ea_47.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_49.png)

## 文件压缩与归档 📦

上一节我们配置了网络，本节中我们来看看如何压缩、归档文件以方便存储和传输。

Linux下常见的压缩工具有 `gzip` 和 `bzip2`，它们通常与归档工具 `tar` 结合使用。`tar` 命令可以将多个文件或目录打包成一个文件（归档），再配合压缩选项进行压缩。

以下是使用 `tar` 进行归档和压缩的常见命令：

```bash
# 1. 仅归档，不压缩（生成 .tar 文件）
tar -cvf archive.tar /path/to/directory

# 2. 归档并用 gzip 压缩（生成 .tar.gz 或 .tgz 文件）
tar -czvf archive.tar.gz /path/to/directory

# 3. 归档并用 bzip2 压缩（生成 .tar.bz2 文件）
tar -cjvf archive.tar.bz2 /path/to/directory

# 4. 解压 .tar.gz 文件
tar -xzvf archive.tar.gz

# 5. 解压 .tar.bz2 文件
tar -xjvf archive.tar.bz2
```

**命令选项说明：**
*   `-c`：创建归档。
*   `-x`：解压归档。
*   `-z`：使用 `gzip` 过滤（压缩/解压）。
*   `-j`：使用 `bzip2` 过滤。
*   `-v`：显示处理过程。
*   `-f`：指定归档文件名。

## 文件传输与同步 🔄

上一节我们处理了本地文件的压缩，本节中我们来看看如何在系统间安全地传输和同步文件。

常用的安全文件传输命令都基于SSH协议：

*   **`scp`**：用于在两台主机之间复制文件。它可以在本地与远程、或两个远程主机之间传输。
    ```bash
    scp local_file user@remote_host:/remote/directory
    scp user1@host1:/file user2@host2:/directory
    ```
*   **`sftp`**：提供一个交互式的文件传输会话，类似于FTP，但更安全。通常用于本地与单个远程主机之间的文件交换。
*   **`rsync`**：强大的文件同步工具，以其增量传输算法著称，只传输文件中变化的部分，效率极高。常用于备份和镜像同步。
    ```bash
    # 同步本地目录到远程（备份）
    rsync -av /local/dir/ user@remote_host:/backup/

    # 从远程同步到本地
    rsync -av user@remote_host:/remote/dir/ /local/copy/

    # 显示传输进度
    rsync -av --progress source destination

    # 双向同步（删除对方已不存在的文件，慎用）
    rsync -av --delete source destination
    ```

## 软件包管理 📦

上一节我们实现了文件传输，本节中我们来看看如何使用 `yum` 或 `dnf` 管理软件包。

RHEL 8使用 `dnf`（或兼容的 `yum`）作为包管理器，它从配置的软件仓库（repository）中获取、安装、更新和删除软件包。仓库配置文件位于 `/etc/yum.repos.d/` 目录下。

以下是核心的软件包管理操作：

```bash
# 1. 安装软件包
dnf install package_name

![](img/00a07543b68df0cc6ad3040bf76dc6ea_51.png)

# 2. 删除软件包（保留依赖）
dnf remove package_name
# 删除软件包及其未被其他包使用的依赖
rpm -e --nodeps package_name

![](img/00a07543b68df0cc6ad3040bf76dc6ea_53.png)

# 3. 更新所有软件包
dnf update

# 4. 搜索软件包
dnf search keyword

# 5. 列出已安装的软件包
dnf list installed
# 或使用rpm查询
rpm -qa | grep keyword

# 6. 下载软件包（不安装）
dnf download package_name

# 7. 安装本地rpm包
dnf install /path/to/package.rpm

# 8. 管理软件包组
dnf group list
dnf group install "Group Name"
dnf group remove "Group Name"
```

有时需要安装图形化桌面环境，这可以通过安装软件包组来实现，例如安装“Server with GUI”组。

## 文件系统与磁盘管理 💾

上一节我们管理了软件，本节中我们来看看如何管理文件系统和磁盘。

使用 `lsblk` 命令可以清晰地查看所有块设备（磁盘、分区）的树状结构和挂载点。

```bash
lsblk
```

磁盘分区和文件系统创建后，需要挂载到目录树才能使用。挂载信息记录在 `/etc/fstab` 文件中，系统启动时会自动挂载其中列出的设备。该文件中每一行代表一个挂载项，各列含义如下：

![](img/00a07543b68df0cc6ad3040bf76dc6ea_55.png)

```
UUID=xxxxxx  /mount/point  filesystem_type  defaults  0 0
```

![](img/00a07543b68df0cc6ad3040bf76dc6ea_57.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_59.png)

*   **第1列**：设备标识（如UUID或 `/dev/sda1`）。
*   **第2列**：挂载点目录。
*   **第3列**：文件系统类型（如ext4, xfs）。
*   **第4列**：挂载选项，`defaults` 包含常用选项如读写权限。
*   **第5列**：是否被 `dump` 备份工具使用（0表示否）。
*   **第6列**：开机时 `fsck` 磁盘检查的顺序（根目录为1，其他为2，0表示不检查）。

要查看目录或文件的磁盘使用情况，使用 `du` 命令。`-h` 选项以人类可读格式显示，`-s` 显示总大小。

```bash
du -sh /etc
```

![](img/00a07543b68df0cc6ad3040bf76dc6ea_61.png)

当需要查找系统中某个文件但不知道其具体位置时，可以使用 `find` 命令。

```bash
# 在根目录下查找名为 ifcfg-ens160 的文件，忽略大小写
find / -iname "ifcfg-ens160" 2>/dev/null
```

## 虚拟化管理（简介） 🖥️

最后，我们简要了解如何在RHEL系统上使用 `libvirt` 工具集管理虚拟机（VM）。这提供了在单一物理服务器上运行多个独立操作系统的能力。

![](img/00a07543b68df0cc6ad3040bf76dc6ea_63.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_65.png)

*   **命令行工具**：`virsh` 是管理虚拟机的强大命令行接口，可以完成虚拟机的创建、启动、停止、删除等全生命周期管理。
*   **图形化工具**：`virt-manager` 提供了一个直观的图形界面来管理本地或远程的虚拟机，大大简化了操作。

通过 `virt-manager`，可以方便地连接到远程开启了SSH和libvirt服务的Hypervisor，从而集中管理多台物理机上的虚拟机。

---

![](img/00a07543b68df0cc6ad3040bf76dc6ea_67.png)

![](img/00a07543b68df0cc6ad3040bf76dc6ea_69.png)

本节课中我们一起学习了RHCSA认证所需的多项核心系统管理任务：从配置安全的SSH密钥登录、同步系统时间、管理网络和主机名，到文件的压缩归档、安全传输与同步。我们还掌握了使用 `dnf/yum` 管理软件包、操作文件系统以及查找文件的方法，并简要了解了虚拟化管理的基础。熟练掌握这些技能，是成为一名合格Linux系统管理员的重要一步。