# CentOS8 操作系统从入门到精通：P24：3-关闭防火墙-SELinux-开机自动挂载镜像-配置本地YUM源-创建可用实验快照 🔧

![](img/7bc0a42c51973f544f13f23bea1a9595_1.png)

![](img/7bc0a42c51973f544f13f23bea1a9595_3.png)

![](img/7bc0a42c51973f544f13f23bea1a9595_4.png)

在本节课中，我们将学习如何为后续的实验准备一个干净、可用的CentOS 8系统环境。主要内容包括关闭防火墙和SELinux、设置开机自动挂载光盘镜像、配置本地YUM源，并最终创建一个系统快照。

## 关闭防火墙 🛡️

上一节我们完成了网络配置，本节中我们来看看如何关闭防火墙，以确保实验环境中的网络访问不受限制。

![](img/7bc0a42c51973f544f13f23bea1a9595_6.png)

防火墙（firewalld）是CentOS 8默认的防火墙管理工具。在实验环境中，我们通常会关闭它以简化操作。

![](img/7bc0a42c51973f544f13f23bea1a9595_8.png)

以下是关闭防火墙并禁止其开机启动的步骤：

![](img/7bc0a42c51973f544f13f23bea1a9595_10.png)

![](img/7bc0a42c51973f544f13f23bea1a9595_12.png)

1.  **查看防火墙状态**：首先，我们可以检查防火墙当前是否正在运行。
    ```bash
    systemctl status firewalld
    ```
    如果显示 `active (running)`，则表示防火墙正在运行。

2.  **临时关闭防火墙**：使用以下命令可以立即停止防火墙服务。
    ```bash
    systemctl stop firewalld
    ```

![](img/7bc0a42c51973f544f13f23bea1a9595_14.png)

3.  **永久关闭防火墙**：为了确保系统重启后防火墙不会自动启动，需要禁用该服务。
    ```bash
    systemctl disable firewalld
    ```

4.  **验证设置**：可以检查防火墙是否已被禁用。
    ```bash
    systemctl is-enabled firewalld
    ```
    如果返回 `disabled`，则表示设置成功。

此外，`systemctl` 命令还支持其他常用操作，例如 `start`（启动）、`restart`（重启）和 `list-units --type=service`（列出所有服务）。

![](img/7bc0a42c51973f544f13f23bea1a9595_16.png)

![](img/7bc0a42c51973f544f13f23bea1a9595_18.png)

## 关闭 SELinux 🔒

![](img/7bc0a42c51973f544f13f23bea1a9595_20.png)

关闭防火墙后，我们还需要处理SELinux。SELinux（Security-Enhanced Linux）是一个安全增强模块，它通过为进程和文件设置精细的访问控制规则来提升系统安全性。但对于初学者，其复杂的“上下文”规则可能会带来困扰，因此在实验环境中我们通常选择关闭它。

以下是关闭SELinux的步骤：

1.  **查看SELinux状态**：
    ```bash
    getenforce
    ```
    如果返回 `Enforcing`，表示SELinux处于强制启用状态。

![](img/7bc0a42c51973f544f13f23bea1a9595_22.png)

2.  **临时关闭SELinux**：此设置仅在本次系统运行期间有效。
    ```bash
    setenforce 0
    ```
    再次执行 `getenforce` 命令，会返回 `Permissive`，表示SELinux处于宽容模式（仅记录违规，不阻止）。

![](img/7bc0a42c51973f544f13f23bea1a9595_24.png)

![](img/7bc0a42c51973f544f13f23bea1a9595_26.png)

3.  **永久关闭SELinux**：需要修改配置文件以使设置永久生效。
    ```bash
    vi /etc/selinux/config
    ```
    找到 `SELINUX=` 这一行，将其值修改为 `disabled`。
    ```bash
    SELINUX=disabled
    ```
    **注意**：此修改需要重启系统后才能生效。

![](img/7bc0a42c51973f544f13f23bea1a9595_27.png)

## 设置开机自动挂载光盘镜像 💿

为了配置本地软件源，我们需要确保系统光盘镜像在开机时能自动挂载。这通过修改 `/etc/fstab` 文件实现。

![](img/7bc0a42c51973f544f13f23bea1a9595_29.png)

**重要提示**：修改 `/etc/fstab` 文件需格外谨慎，错误的配置可能导致系统无法正常启动。

![](img/7bc0a42c51973f544f13f23bea1a9595_31.png)

![](img/7bc0a42c51973f544f13f23bea1a9595_33.png)

以下是配置步骤：

![](img/7bc0a42c51973f544f13f23bea1a9595_35.png)

1.  **编辑 `/etc/fstab` 文件**：
    ```bash
    vi /etc/fstab
    ```

2.  **添加挂载信息**：在文件末尾添加一行，指定光盘设备、挂载点、文件系统类型和挂载选项。
    ```bash
    /dev/cdrom /mnt iso9660 defaults 0 0
    ```
    *   `/dev/cdrom`: 光盘设备文件。
    *   `/mnt`: 挂载点目录。
    *   `iso9660`: 光盘的文件系统类型。
    *   `defaults`: 默认挂载选项。
    *   最后的两个 `0`: 表示 dump 备份和 fsck 检查顺序。

![](img/7bc0a42c51973f544f13f23bea1a9595_37.png)

3.  **测试配置**：无需重启，可以立即测试配置是否正确。
    ```bash
    # 卸载当前挂载（如果已挂载）
    umount /mnt
    # 根据 /etc/fstab 的内容自动挂载所有设备
    mount -a
    # 检查是否挂载成功
    ls /mnt
    ```
    如果能看到光盘中的文件，说明配置成功。光盘是只读介质，无法在其中创建或修改文件。

![](img/7bc0a42c51973f544f13f23bea1a9595_39.png)

![](img/7bc0a42c51973f544f13f23bea1a9595_41.png)

## 配置本地 YUM 源 📦

![](img/7bc0a42c51973f544f13f23bea1a9595_43.png)

![](img/7bc0a42c51973f544f13f23bea1a9595_45.png)

![](img/7bc0a42c51973f544f13f23bea1a9595_47.png)

YUM是CentOS中的软件包管理器。默认情况下，系统会使用在线的镜像源。但在无网络环境或需要特定版本软件时，配置本地YUM源（即使用系统安装光盘中的软件包）非常有用。

![](img/7bc0a42c51973f544f13f23bea1a9595_49.png)

在CentOS 8中，软件包被分为两个主要仓库：`BaseOS`（基础操作系统组件）和 `AppStream`（应用程序和开发工具）。

以下是配置本地YUM源的步骤：

![](img/7bc0a42c51973f544f13f23bea1a9595_51.png)

![](img/7bc0a42c51973f544f13f23bea1a9595_53.png)

1.  **进入YUM源配置目录**：
    ```bash
    cd /etc/yum.repos.d/
    ```

![](img/7bc0a42c51973f544f13f23bea1a9595_55.png)

![](img/7bc0a42c51973f544f13f23bea1a9595_57.png)

2.  **备份现有源**（可选但建议）：为防止冲突，可以将现有的在线源配置文件移走。
    ```bash
    mkdir -p /opt/repo_backup
    mv *.repo /opt/repo_backup/
    ```

![](img/7bc0a42c51973f544f13f23bea1a9595_59.png)

3.  **创建本地源配置文件**：我们需要为 `BaseOS` 和 `AppStream` 分别创建配置。
    *   创建 `BaseOS` 本地源配置：
        ```bash
        vi CentOS8-Local-BaseOS.repo
        ```
        输入以下内容：
        ```ini
        [Local-BaseOS]
        name=CentOS 8 Local BaseOS
        baseurl=file:///mnt/BaseOS
        enabled=1
        gpgcheck=0
        ```
    *   创建 `AppStream` 本地源配置：
        ```bash
        vi CentOS8-Local-AppStream.repo
        ```
        输入以下内容：
        ```ini
        [Local-AppStream]
        name=CentOS 8 Local AppStream
        baseurl=file:///mnt/AppStream
        enabled=1
        gpgcheck=0
        ```
    **关键点**：`baseurl` 的路径必须指向光盘中 `repodata` 目录所在的父目录。`gpgcheck=0` 表示跳过GPG密钥检查，简化本地源使用。

4.  **清除并重建YUM缓存**：
    ```bash
    yum clean all
    yum makecache
    ```

![](img/7bc0a42c51973f544f13f23bea1a9595_61.png)

5.  **测试本地源**：尝试安装一个软件包，例如 `httpd`。
    ```bash
    yum install httpd -y
    ```
    如果能够正常列出依赖并开始安装，说明本地YUM源配置成功。

6.  **恢复在线源**（可选）：实验完成后，可以将之前备份的在线源配置文件移回。
    ```bash
    mv /opt/repo_backup/*.repo /etc/yum.repos.d/
    ```
    系统会优先使用更新、更全的在线源。

![](img/7bc0a42c51973f544f13f23bea1a9595_63.png)

![](img/7bc0a42c51973f544f13f23bea1a9595_65.png)

## 创建可用实验快照 📸

![](img/7bc0a42c51973f544f13f23bea1a9595_67.png)

![](img/7bc0a42c51973f544f13f23bea1a9595_69.png)

完成以上所有配置后，我们得到了一个“干净”且“就绪”的实验环境。为了在后续学习过程中，当操作失误导致系统问题时能快速恢复，强烈建议在虚拟机中创建一个系统快照。

![](img/7bc0a42c51973f544f13f23bea1a9595_71.png)

快照功能可以保存虚拟机在某个时间点的完整状态（包括内存、磁盘数据等）。当需要时，可以一键恢复到创建快照时的状态。

![](img/7bc0a42c51973f544f13f23bea1a9595_73.png)

![](img/7bc0a42c51973f544f13f23bea1a9595_75.png)

**操作建议**：在VMware Workstation或VirtualBox等虚拟化软件中，找到“快照”或“Snapshot”功能，为当前状态的虚拟机创建一个快照，并为其命名，例如 “Base_CentOS8_Ready”。

![](img/7bc0a42c51973f544f13f23bea1a9595_77.png)

---

![](img/7bc0a42c51973f544f13f23bea1a9595_79.png)

本节课中我们一起学习了如何为CentOS 8实验环境进行基础准备。我们关闭了可能带来访问限制的防火墙和SELinux，设置了光盘镜像的开机自动挂载，配置了离线的本地YUM软件源，并最终创建了一个系统快照作为我们的“后悔药”。这个准备好的环境将是后续所有实验的起点，请务必妥善保存快照。