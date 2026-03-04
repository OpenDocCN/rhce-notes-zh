# RHCE8 总复习：P15：第八天 RHCSA总复习 📚

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_0.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_2.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_4.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_6.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_8.png)

## 概述
在本节课中，我们将对RHCSA认证考试的核心知识点进行系统性复习。课程将模拟真实考试环境，涵盖从系统基础配置到高级存储管理的所有关键技能点。我们将通过实际操作演示，帮助大家巩固知识，掌握考试技巧。

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_10.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_12.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_14.png)

---

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_16.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_18.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_20.png)

## 第一部分：Server A 操作 (第1-15题) 🖥️

### 1. 配置主机名
主机名是系统在网络中的唯一标识。永久配置主机名需要修改特定文件。

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_22.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_23.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_25.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_27.png)

**操作步骤：**
1.  使用 `vi` 编辑器打开 `/etc/hostname` 文件。
2.  在文件中写入指定的主机名，例如 `servera.example.com`。
3.  保存并退出编辑器。
4.  关闭当前终端并重新打开，或使用 `hostnamectl set-hostname` 命令临时生效，以验证主机名已更改。

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_29.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_31.png)

### 2. 配置网络连接
网络配置是系统与外界通信的基础。考试中需要为系统配置静态IP地址、子网掩码、网关和DNS。

**操作步骤：**
1.  使用 `nmcli` 命令修改现有网络连接。首先，通过 `nmcli connection show` 查看当前连接名。
2.  执行以下命令进行配置（假设连接名为 `Wired connection 1`，IP为 `172.25.250.10/24`，网关为 `172.25.250.254`）：
    ```bash
    nmcli connection modify "Wired connection 1" ipv4.addresses 172.25.250.10/24 ipv4.gateway 172.25.250.254 ipv4.method manual connection.autoconnect yes
    ```
3.  激活修改后的连接：`nmcli connection up "Wired connection 1"`。
4.  配置DNS：编辑 `/etc/resolv.conf` 文件，添加 `nameserver 172.25.250.254`。
5.  重启网络服务或系统，并使用 `ping` 命令测试与网关或考试服务器的连通性。

### 3. 配置软件仓库 (YUM/DNF)
软件仓库是安装、更新软件包的基础。考试需要配置指向指定服务器的仓库。

**操作步骤：**
1.  清理 `/etc/yum.repos.d/` 目录下所有现有仓库配置文件：`rm -f /etc/yum.repos.d/*.repo`。
2.  新建仓库配置文件，例如 `abc.repo`。
3.  在文件中添加以下内容（URL需替换为考试提供的地址）：
    ```ini
    [BaseOS]
    name=BaseOS
    baseurl=http://content.example.com/rhel8.2/x86_64/dvd/BaseOS
    enabled=1
    gpgcheck=0

    [AppStream]
    name=AppStream
    baseurl=http://content.example.com/rhel8.2/x86_64/dvd/AppStream
    enabled=1
    gpgcheck=0
    ```
4.  运行 `dnf repolist` 验证仓库配置成功。

### 4. 调试 SELinux 与 Apache 服务
此题目综合考察SELinux上下文管理和服务故障排除能力。需要修复一个配置了非标准端口（82）且SELinux上下文错误的Apache服务。

**核心概念与操作：**
1.  **设置 SELinux 为 enforcing 模式**：这是触发服务故障的前提。编辑 `/etc/selinux/config` 文件，将 `SELINUX=` 的值改为 `enforcing`，并使用 `setenforce 1` 命令临时切换。
2.  **修复 Apache 服务端口**：SELinux默认不允许Apache使用82端口。需要将82端口添加到 `http_port_t` 类型中。
    ```bash
    semanage port -a -t http_port_t -p tcp 82
    ```
3.  **修复文件上下文**：确保Apache的网页目录 `/var/www/html` 及其下的 `index.html` 文件具有正确的SELinux上下文 `httpd_sys_content_t`。
    ```bash
    semanage fcontext -a -t httpd_sys_content_t '/var/www/html(/.*)?'
    restorecon -Rv /var/www/html/
    ```
4.  重启Apache服务 (`systemctl restart httpd`) 并通过浏览器访问 `http://<服务器IP>:82` 进行验证。

### 5. 用户、组与计划任务管理
这部分考察基本的用户、组管理和 `cron` 计划任务设置。

**操作列表：**
*   **创建组**：`groupadd admin`
*   **创建用户**：
    *   `useradd -G admin harry`
    *   `useradd -s /sbin/nologin natasha` (禁止交互登录)
*   **设置密码**：为所有新用户设置密码，例如 `echo redhat | passwd --stdin harry`。
*   **配置计划任务**：为特定用户设置定时任务，例如让 `harry` 用户每5分钟执行一次命令。
    ```bash
    crontab -u harry -e
    # 在编辑器中添加：*/5 * * * * /bin/echo hello
    ```

### 6. 设置目录的SGID权限与组继承
此题考察特殊权限 `SGID` 的应用。当目录设置 `SGID` 位后，在该目录下创建的任何新文件，其所属组会自动继承目录的所属组。

**操作步骤：**
1.  创建目录 `/home/admins` 并将其所属组改为 `admin`：`chgrp admin /home/admins`。
2.  设置目录权限为 `770`，确保 `admin` 组成员有读写执行权限，其他人无权限：`chmod 770 /home/admins`。
3.  为目录添加 `SGID` 权限（`2`代表SGID）：`chmod 2770 /home/admins`。
4.  **测试**：切换到 `admin` 组内用户，在 `/home/admins` 下创建新文件，检查其所属组是否自动为 `admin`。

### 7. 配置系统时间同步 (Chrony)
在RHEL8中，使用 `chronyd` 服务进行时间同步。

**操作步骤：**
1.  安装 `chrony` 软件包（如果未安装）。
2.  编辑 `/etc/chrony.conf` 配置文件。
3.  找到 `pool` 或 `server` 开头的行，将其修改或添加为指向考试提供的时间服务器，例如：`server classroom.example.com iburst`。
4.  重启并启用 `chronyd` 服务：`systemctl restart chronyd; systemctl enable chronyd`。
5.  使用 `chronyc sources` 命令验证时间同步状态。

### 8. 配置 Autofs 自动挂载 NFS 共享
`autofs` 服务可以实现按需自动挂载远程文件系统，如NFS共享。

**操作步骤：**
1.  在Server A上安装 `autofs` 软件包。
2.  编辑主配置文件 `/etc/auto.master`，添加一行，指定挂载点父目录和子配置文件：
    ```
    /home/guests /etc/auto.misc
    ```
3.  编辑子配置文件 `/etc/auto.misc`，添加具体的挂载规则。例如，将Server B的 `/home/guests` 目录挂载到本地 `/home/guests`：
    ```
    ldapuser0 -rw,sync serverb.example.com:/home/guests/ldapuser0
    ```
4.  启动并启用 `autofs` 服务：`systemctl restart autofs; systemctl enable autofs`。
5.  **测试**：访问 `/home/guests/ldapuser0` 目录，`autofs` 会自动触发挂载。

### 9. 配置文件访问控制列表 (ACL)
ACL提供了比传统Unix权限更精细的权限控制。

**操作步骤：**
1.  确保文件系统已挂载时启用了ACL支持（通常默认已启用）。
2.  为指定文件设置ACL，授予特定用户读写权限，例如给用户 `natasha` 对文件 `/var/tmp/fstab` 的读写权：
    ```bash
    setfacl -m u:natasha:rw /var/tmp/fstab
    ```
3.  使用 `getfacl /var/tmp/fstab` 命令查看ACL设置。
4.  **删除ACL**：如果设置错误，可以使用 `setfacl -b /var/tmp/fstab` 清除所有ACL条目，或 `setfacl -x u:natasha /var/tmp/fstab` 删除特定条目。

### 10. 使用 find 命令查找并处理文件
`find` 命令功能强大，可以根据多种条件（大小、时间、类型等）查找文件，并对找到的文件执行操作。

**操作示例：**
题目要求查找 `/etc` 目录下大于5MB的文件，并将其复制到 `/root/findfiles` 目录。
```bash
find /etc -size +5M -exec cp -a {} /root/findfiles/ \;
```
*   `-size +5M`：查找大小超过5MB的文件。
*   `-exec ... \;`：对每个找到的文件执行后续命令。
*   `{}`：代表 `find` 命令找到的每个文件路径。
*   `cp -a`：复制时保留所有属性。

### 11. 使用 grep 进行文本搜索
`grep` 命令用于在文件中搜索包含指定模式的行。

**操作示例：**
题目要求在 `/etc/man_db.conf` 文件中查找包含字符串 `sbin` 的行，并将结果追加到 `/root/man.conf` 文件。
```bash
grep sbin /etc/man_db.conf >> /root/man.conf
```
*   `>>` 表示追加输出，避免覆盖目标文件原有内容。

### 12. 创建归档文件 (tar)
`tar` 命令用于将多个文件或目录打包成一个归档文件，常用于备份。

**操作示例：**
题目要求将 `/usr/local` 目录归档并压缩为 `/root/backup.tar.bz2` 文件。
```bash
tar -cjf /root/backup.tar.bz2 /usr/local
```
*   `-c`：创建归档。
*   `-j`：使用 `bzip2` 压缩。
*   `-f`：指定归档文件名。
*   可以使用 `file /root/backup.tar.bz2` 和 `tar -tf /root/backup.tar.bz2` 进行验证。

### 13. 管理容器作为系统服务 (Podman)
RHEL8 使用 `podman` 管理容器，并可将容器配置为 `systemd` 服务。

**核心操作流程：**
1.  **准备环境**：登录到容器注册服务器，拉取镜像。
    ```bash
    podman login -u admin -p redhat321 registry.example.com
    podman pull registry.example.com/rhel8/httpd-24:1-105
    ```
2.  **创建并运行容器**，映射端口和目录，并命名为 `httpd-server`。
    ```bash
    podman run -d --name httpd-server -v /home/student/httpd/logs:/var/log/httpd:Z -p 8080:80 registry.example.com/rhel8/httpd-24:1-105
    ```
3.  **生成 systemd 服务单元文件**：在普通用户家目录下生成服务配置文件，以便用户管理自己的容器服务。
    ```bash
    mkdir -p ~/.config/containers/systemd
    cd ~/.config/containers/systemd
    podman generate systemd --name httpd-server --files --new
    ```
4.  **启用服务自启动**：以普通用户身份启用服务，使其在主机启动时自动运行容器。
    ```bash
    systemctl --user enable container-httpd-server.service
    systemctl --user start container-httpd-server.service
    loginctl enable-linger $USER  # 确保用户服务在用户未登录时也能运行
    ```

### 14. 配置容器日志持久化
确保容器产生的日志在主机重启后得以保留。

**操作步骤：**
1.  在主机上创建一个用于存储持久化日志的目录，例如 `/home/student/httpd/logs`。
2.  在运行容器时，使用 `-v` 选项将主机目录挂载到容器的日志目录（如 `/var/log/httpd`）。
3.  配置容器的日志驱动为 `journald` 或确保挂载的卷是持久的。
4.  通过 `podman logs` 命令或直接查看挂载的主机目录来验证日志。

### 15. 重置 root 密码 (Server B)
当忘记root密码时，可以通过单用户模式重置。

**操作步骤：**
1.  重启Server B，在GRUB引导菜单界面，按 `e` 键编辑启动参数。
2.  找到以 `linux` 或 `linux16` 开头的行，在行末添加 `rd.break`。按 `Ctrl+X` 启动。
3.  系统会进入紧急模式。重新以读写方式挂载根文件系统：`mount -o remount,rw /sysroot`。
4.  切换根环境：`chroot /sysroot`。
5.  修改root密码：`passwd root`，输入新密码。
6.  如果SELinux是enforcing模式，需要创建标记文件，以便系统在重启后重新标记文件上下文：`touch /.autorelabel`。
7.  退出并重启：`exit` 两次，然后执行 `reboot`。

---

## 第二部分：Server B 操作 (磁盘与存储管理) 💾

上一节我们完成了Server A上的综合配置，本节中我们来看看Server B上的任务，主要集中在磁盘分区、逻辑卷管理和高级存储技术上。

### 16. 配置软件仓库 (同Server A)
在Server B上重复第3步的操作，配置YUM/DNF软件仓库。

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_33.png)

### 17. 创建和管理交换分区 (Swap)
交换分区用于在物理内存不足时提供虚拟内存空间。

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_35.png)

**操作步骤：**
1.  使用 `fdisk` 或 `gdisk` 在一个空闲磁盘（如 `/dev/vdb`）上创建新分区，类型设置为 `Linux swap`。
2.  格式化新分区为交换空间：`mkswap /dev/vdb1`。
3.  临时激活交换分区：`swapon /dev/vdb1`。
4.  为了永久生效，编辑 `/etc/fstab` 文件，添加一行：
    ```
    /dev/vdb1 swap swap defaults 0 0
    ```
5.  使用 `swapon -s` 或 `free -h` 命令验证交换分区已添加并生效。

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_37.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_39.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_41.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_43.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_45.png)

### 18. 调整逻辑卷大小
此题目要求扩大一个已存在的逻辑卷（LV）及其文件系统。

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_47.png)

**操作步骤：**
1.  首先查看逻辑卷的当前大小和卷组（VG）的剩余空间：`lvs` 和 `vgs`。
2.  扩展逻辑卷的大小（例如扩展到300M）：`lvextend -L 300M /dev/myvg/mylv`。
3.  扩展文件系统以使用新增的空间。**此步取决于文件系统类型**：
    *   **对于 ext2/3/4**：`resize2fs /dev/myvg/mylv`
    *   **对于 XFS**：`xfs_growfs /mount-point` （需要在挂载状态下执行）
4.  使用 `df -h` 命令验证文件系统大小已更新。

### 19. 创建和管理逻辑卷 (LVM)
LVM提供了灵活的磁盘管理方式。

**操作步骤：**
1.  **创建物理卷 (PV)**：`pvcreate /dev/vdb2`
2.  **创建卷组 (VG)**，并指定物理扩展块(PE)大小为16MB：`vgcreate -s 16M myvg /dev/vdb2`
3.  **创建逻辑卷 (LV)**，使用50个PE（即800MB）：`lvcreate -l 50 -n mylv myvg`
4.  **创建文件系统**：`mkfs.ext4 /dev/myvg/mylv`
5.  **创建挂载点并永久挂载**：
    *   `mkdir /mnt/data`
    *   编辑 `/etc/fstab`，添加：`/dev/myvg/mylv /mnt/data ext4 defaults 0 0`
    *   `mount -a`

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_49.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_51.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_53.png)

### 20. 配置虚拟数据优化器 (VDO)
VDO提供内联数据去重和压缩，以节省存储空间。

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_55.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_57.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_59.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_61.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_63.png)

**操作步骤：**
1.  安装VDO软件包：`dnf install vdo kmod-kvdo`
2.  在指定磁盘（如 `/dev/vdc`）上创建VDO卷，逻辑大小为50G：
    ```bash
    vdo create --name=myvdo --device=/dev/vdc --vdoLogicalSize=50G
    ```
3.  在VDO卷上创建文件系统：`mkfs.xfs -K /dev/mapper/myvdo`
4.  创建挂载点并配置永久挂载：
    *   `mkdir /mnt/vdo`
    *   编辑 `/etc/fstab`，添加：`/dev/mapper/myvdo /mnt/vdo xfs defaults,x-systemd.requires=vdo.service 0 0`
5.  使用 `vdostats --human-readable` 查看VDO卷状态。

### 21. 调整系统性能配置文件 (Tuned)
`tuned` 服务提供了一系列针对不同工作负载优化的系统性能配置集。

**操作步骤：**
1.  确保 `tuned` 软件包已安装并启动服务：`systemctl start tuned; systemctl enable tuned`
2.  查看当前活动的配置集：`tuned-adm active`
3.  获取优化建议：`tuned-adm recommend`
4.  根据建议或题目要求，切换到指定的配置集（如 `throughput-performance`）：
    ```bash
    tuned-adm profile throughput-performance
    ```
5.  再次使用 `tuned-adm active` 验证配置集已切换。

---

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_65.png)

![](img/7e9fc774a5ec724a4c7d7bd7154c2785_67.png)

## 总结
本节课中我们一起学习了RHCSA考试的核心复习内容。我们从Server A的基础配置（网络、仓库、SELinux、服务、用户、计划任务、权限、挂载、ACL、文件查找、容器管理）开始，到Server B的磁盘与存储高级管理（密码重置、交换分区、LVM扩展与创建、VDO配置、性能调优）。关键在于理解每个操作背后的原理，而不仅仅是记忆命令。考试时务必仔细阅读题目描述，抓住每个关键词，并养成完成后立即验证的好习惯。祝大家考试顺利！