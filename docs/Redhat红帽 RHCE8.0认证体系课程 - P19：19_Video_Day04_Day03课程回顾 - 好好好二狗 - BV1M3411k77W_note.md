# Redhat红帽 RHCE8.0认证体系课程：P19：Day04_Day03课程回顾 📚

在本节课中，我们将回顾上一讲（Day03）的核心内容，涵盖权限管理、进程管理、服务管理和SSH远程管理。这些是Linux系统管理的基础，对于后续学习和实际运维至关重要。

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_1.png)

## 第七章回顾：权限管理 🔐

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_3.png)

上一节我们介绍了文件系统的基础操作，本节中我们来看看Linux系统中至关重要的权限管理机制。

### 用户身份与权限位

一个文件或目录的权限由9位字符表示，分为三组，分别对应三种用户身份。

*   **所有者 (User)**：文件或目录的创建者。
*   **所属组 (Group)**：文件或目录所属的用户组。
*   **其他人 (Other)**：既不是所有者，也不在所属组中的其他用户。

每组权限包含三个字符：`r` (读)、`w` (写)、`x` (执行)。权限位前还有一个字符表示文件类型（例如 `-` 表示普通文件，`d` 表示目录）。

### 权限的数值表示法

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_5.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_6.png)

每种权限有对应的数值：
*   `r` (读) = 4
*   `w` (写) = 2
*   `x` (执行) = 1

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_8.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_9.png)

因此，权限组合可以通过数值相加表示。例如，`rwx` 的数值是 `4+2+1=7`，`r-x` 的数值是 `4+0+1=5`。

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_11.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_12.png)

### 常见权限组合

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_14.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_15.png)

以下是文件和目录常见的有效权限组合：

对于目录，通常有以下三种情况：
*   `rwx`：拥有全部权限。
*   `r-x`：可进入目录并列出内容，但不能创建或删除文件。
*   `---`：没有任何权限。

对于文件，通常有以下五种情况：
*   `rwx`：可读、可写、可执行。
*   `rw-`：可读、可写。
*   `r-x`：可读、可执行。
*   `r--`：只读。
*   `---`：没有任何权限。

### 默认权限与反掩码 (umask)

系统创建文件或目录时，会赋予一个默认权限。这个默认权限由“可赋予的最大权限”减去“反掩码”得到。

*   目录的最大权限：`0777`
*   文件的最大权限：`0666`
*   系统管理员 (root) 的默认反掩码：`0022`
*   普通用户的默认反掩码：`0002`

因此，root用户创建的文件默认权限是 `0666 - 0022 = 0644` (`rw-r--r--`)，目录是 `0777 - 0022 = 0755` (`rwxr-xr-x`)。

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_17.png)

### 特殊权限

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_19.png)

除了基本的读写执行权限，还有三种特殊权限位：

*   **SUID (Set User ID)**：千位数为 `4`，例如 `4755`。仅对文件有效，使执行者在执行该文件期间临时获得文件所有者的权限。典型例子：`/usr/bin/passwd`。
*   **SGID (Set Group ID)**：千位数为 `2`，例如 `2755`。
    *   对文件：使执行者以文件所属组的权限执行。
    *   对目录：在该目录下新建的文件或目录，会自动继承该目录的所属组。
*   **Sticky Bit (粘滞位)**：千位数为 `1`，例如 `1755`。仅对目录有效。设置了粘滞位的目录，其下的文件只有**文件所有者**和**root用户**可以删除，其他用户即使有写权限也无法删除。常用于共享目录（如 `/tmp`）。

设置特殊权限的命令：
*   `chmod u+s file`  # 设置SUID
*   `chmod g+s dir`   # 设置SGID
*   `chmod o+t dir`   # 设置粘滞位

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_21.png)

### 隐藏权限 (chattr/lsattr)

隐藏权限提供了更底层的文件保护。

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_23.png)

*   **`a` (append only)**：完全写保护。文件只能追加内容，不能被删除、修改或重命名。类似只读开关。
*   **`i` (immutable)**：部分写保护。文件完全不能被修改（包括追加）。但可以通过重定向 (`>>`) 追加内容（在某些上下文中可通俗理解为“部分写保护”）。

命令示例：
*   `chattr +i file`  # 添加 `i` 属性
*   `lsattr file`     # 查看隐藏属性

### 文件访问控制列表 (FACL)

FACL允许为特定用户或组设置超出常规 `ugo` 模型的精细权限。

*   `getfacl file`：查看文件的ACL。
*   `setfacl -m u:username:rwx file`：为用户 `username` 添加读、写、执行权限。
*   `setfacl -m g:groupname:rx file`：为组 `groupname` 添加读、执行权限。
*   `setfacl -R -m u:username:rwx dir`：递归地对目录 `dir` 及其内容设置ACL。

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_25.png)

---

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_27.png)

## 第八章回顾：进程管理 ⚙️

理解了如何控制文件访问后，我们来看看如何管理系统中的运行实体——进程。

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_29.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_30.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_31.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_32.png)

### 进程基础

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_33.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_34.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_35.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_36.png)

进程是正在执行的程序的实例。每个进程都有所有者，通常是启动它的用户，或者是配置文件（如Apache的 `httpd`）中指定的专用用户。

### 查看进程

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_38.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_39.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_40.png)

以下是常用的进程查看命令：

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_41.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_42.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_43.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_44.png)

*   `ps aux`：列出系统所有进程的详细信息。
*   `pstree`：以树状图形式显示进程间的派生关系。
*   `top`：动态、实时地查看系统进程状态和资源占用。

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_45.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_47.png)

**关于 `top` 命令的CPU使用率说明**：`top` 显示的是所有CPU核心的平均使用率百分比。由于现代CPU是多核心的，单个进程的CPU使用率可能超过100%，这表示它占用了超过一个核心的算力。系统负载（load average）的1分钟、5分钟、15分钟值如果持续高于CPU核心数，则表明系统可能过载。

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_49.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_50.png)

### 作业控制 (Job Control)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_51.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_52.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_53.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_54.png)

在单个终端中，可以管理前台和后台作业。

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_56.png)

*   `command &`：直接在后台启动命令。
*   `Ctrl + Z`：挂起（暂停）当前前台进程。
*   `bg %jobid`：将挂起的作业放到后台继续运行。
*   `fg %jobid`：将后台作业调至前台运行。
*   `nohup command &`：使命令在用户退出登录后仍继续运行，输出默认重定向到 `nohup.out`。

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_58.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_59.png)

### 信号处理

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_60.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_61.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_62.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_63.png)

可以使用 `kill` 命令向进程发送信号来管理它们。

*   `kill -9 PID` 或 `killall -9 process_name`：强制终止进程（`SIGKILL`），不可被捕获或忽略。
*   `kill -15 PID`：正常终止进程（`SIGTERM`），允许进程进行清理工作。
*   `kill -0 PID`：检查进程是否存在。
*   `kill -1 PID`：让进程重新加载配置（`SIGHUP`）。

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_65.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_66.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_67.png)

---

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_69.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_70.png)

## 第九章回顾：服务管理 🚀

对于系统服务，有更规范的管理方式，即通过 `systemd`。

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_72.png)

### systemd 简介

从RHEL/CentOS 7开始，`systemd` 取代了传统的 `SysV init`，成为默认的初始化系统和服务管理器。它使用 `systemctl` 命令进行管理。

### 管理服务单元

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_74.png)

服务单元文件通常位于 `/usr/lib/systemd/system/` 目录下，以 `.service` 结尾。

以下是常用的服务管理操作：
*   `systemctl start service_name`：启动服务。
*   `systemctl stop service_name`：停止服务。
*   `systemctl restart service_name`：重启服务。
*   `systemctl reload service_name`：重新加载服务配置（不重启）。
*   `systemctl enable service_name`：设置服务开机自启。
*   `systemctl disable service_name`：禁止服务开机自启。
*   `systemctl status service_name`：查看服务状态。

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_76.png)

### 管理运行目标 (Target)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_78.png)

运行目标相当于旧系统的运行级别，但更灵活。

*   `systemctl get-default`：获取默认运行目标。
*   `systemctl set-default multi-user.target`：设置默认运行为多用户文本模式。
*   `systemctl isolate graphical.target`：切换到图形界面（临时）。
*   `systemctl isolate rescue.target`：切换到救援模式。

**注意**：在RHEL 8中，传统的 `init` 命令（如 `init 0`, `init 6`）已被移除，建议使用 `systemctl poweroff`、`systemctl reboot` 等命令。

---

## 第十章回顾：SSH远程管理 🌐

最后，我们回顾如何安全地远程管理Linux服务器。

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_80.png)

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_82.png)

### 基本连接

使用 `ssh` 命令连接远程服务器：
```bash
ssh username@remote_host_ip
```
或直接执行单条命令：
```bash
ssh username@remote_host_ip "command"
```
首次连接时，客户端会保存服务器的公钥指纹到 `~/.ssh/known_hosts` 文件中。

### 密钥对认证

为了实现免密码登录（常用于自动化运维），可以配置公钥认证。

1.  在**客户端**生成密钥对：
    ```bash
    ssh-keygen -t rsa -b 2048
    ```
    默认保存在 `~/.ssh/id_rsa` (私钥) 和 `~/.ssh/id_rsa.pub` (公钥)。

2.  将**客户端公钥**复制到**服务器的对应用户**下：
    ```bash
    ssh-copy-id username@remote_host_ip
    ```
    或手动将公钥内容添加到服务器的 `~/.ssh/authorized_keys` 文件中。

此后，从该客户端连接服务器该用户时，就不再需要输入密码。

### 安全配置

通过修改 `/etc/ssh/sshd_config` 文件可以增强SSH安全性。

*   **禁止root直接登录**：
    ```
    PermitRootLogin no
    ```
*   **修改默认端口**：
    ```
    Port 2222  # 改为1024-65535之间未被占用的端口
    ```
    **重要提示**：修改配置文件后，必须重启 `sshd` 服务才能生效：
    ```bash
    systemctl restart sshd
    ```

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_84.png)

---

![](img/f436fdbb6fb2692c2c37b0e20bb5a8dc_86.png)

本节课中我们一起学习了Linux系统管理中四个核心模块的回顾：权限管理（用户身份、基本与特殊权限、FACL）、进程管理（查看、控制与信号）、服务管理（systemctl的使用）以及SSH远程管理（连接、密钥认证与安全配置）。这些知识是构建RHCE技能体系的基石，请务必理解并掌握。