# Linux就该这么学：第34期：P13：13【第34期第十三节课】红帽RHCE认证培训课程-Linux就该这么学

![](img/f8ed3c0f0701fef84260b7bde57b3432_1.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_3.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_5.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_7.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_9.png)

## 概述

![](img/f8ed3c0f0701fef84260b7bde57b3432_11.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_13.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_15.png)

在本节课中，我们将学习如何通过图形化界面配置防火墙，并深入了解软件仓库的配置与管理。课程将涵盖从挂载光盘、配置软件仓库到安装和使用图形化防火墙工具 `firewall-config` 的全过程。此外，我们还将介绍一个强大的系统管理工具 `cockpit`，以及网络配置的高级技巧，如网卡会话和绑定。

![](img/f8ed3c0f0701fef84260b7bde57b3432_17.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_19.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_21.png)

---

## 配置软件仓库

上一节我们介绍了通过命令行配置防火墙的方法，本节中我们来看看如何通过图形化界面进行配置。首先，我们需要配置软件仓库来安装必要的工具。

### 挂载光盘

![](img/f8ed3c0f0701fef84260b7bde57b3432_23.png)

第一步是设置并挂载系统安装光盘，以获取软件包。

![](img/f8ed3c0f0701fef84260b7bde57b3432_25.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_27.png)

1.  在虚拟机设置中，确保CD/DVD设备已连接，并选择系统安装镜像文件（例如，RHEL 8的ISO文件）。
2.  在Linux系统中，创建一个挂载点目录：
    ```bash
    mkdir -p /media/cdrom
    ```
3.  将光盘设备挂载到该目录：
    ```bash
    mount /dev/cdrom /media/cdrom
    ```
4.  为了使挂载在系统重启后依然有效，需要将挂载信息写入 `/etc/fstab` 文件：
    ```bash
    echo "/dev/cdrom /media/cdrom iso9660 defaults 0 0" >> /etc/fstab
    ```

![](img/f8ed3c0f0701fef84260b7bde57b3432_29.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_31.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_33.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_35.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_37.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_39.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_41.png)

### 配置仓库文件

![](img/f8ed3c0f0701fef84260b7bde57b3432_43.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_45.png)

接下来，我们需要创建YUM或DNF的仓库配置文件。

![](img/f8ed3c0f0701fef84260b7bde57b3432_47.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_49.png)

1.  进入仓库配置目录：
    ```bash
    cd /etc/yum.repos.d/
    ```
2.  由于RHEL 8的镜像包含两个主要的软件目录（`BaseOS` 和 `AppStream`），我们需要为它们分别创建仓库配置。创建一个新的 `.repo` 文件，例如 `local.repo`：
    ```bash
    vim local.repo
    ```
3.  在文件中输入以下内容，注意根据你的实际挂载路径修改 `baseurl`：
    ```ini
    [BaseOS]
    name=BaseOS
    baseurl=file:///media/cdrom/BaseOS
    enabled=1
    gpgcheck=0

    [AppStream]
    name=AppStream
    baseurl=file:///media/cdrom/AppStream
    enabled=1
    gpgcheck=0
    ```
    *   `[BaseOS]` 和 `[AppStream]` 是仓库的唯一标识。
    *   `name` 是仓库的描述信息。
    *   `baseurl` 指定软件仓库的路径，`file://` 表示本地文件系统。
    *   `enabled=1` 表示启用该仓库。
    *   `gpgcheck=0` 表示不进行GPG签名校验（适用于本地源）。

![](img/f8ed3c0f0701fef84260b7bde57b3432_51.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_53.png)

4.  保存并退出编辑器。
5.  清除旧的缓存并建立新缓存，以确保软件列表是最新的：
    ```bash
    dnf clean all
    dnf makecache
    ```

![](img/f8ed3c0f0701fef84260b7bde57b3432_55.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_57.png)

---

![](img/f8ed3c0f0701fef84260b7bde57b3432_59.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_61.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_63.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_65.png)

## 安装与使用 firewall-config

![](img/f8ed3c0f0701fef84260b7bde57b3432_67.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_69.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_71.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_73.png)

软件仓库配置完成后，我们就可以安装图形化的防火墙配置工具了。

![](img/f8ed3c0f0701fef84260b7bde57b3432_75.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_77.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_79.png)

### 安装 firewall-config

![](img/f8ed3c0f0701fef84260b7bde57b3432_81.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_83.png)

使用DNF命令安装 `firewall-config` 软件包：
```bash
dnf install -y firewall-config
```

![](img/f8ed3c0f0701fef84260b7bde57b3432_85.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_87.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_89.png)

### 启动 firewall-config

![](img/f8ed3c0f0701fef84260b7bde57b3432_91.png)

安装完成后，可以通过以下两种方式启动该工具：

![](img/f8ed3c0f0701fef84260b7bde57b3432_93.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_95.png)

1.  在终端中直接输入命令：
    ```bash
    firewall-config
    ```
2.  在图形界面的应用程序菜单中找到“防火墙”图标并点击。

![](img/f8ed3c0f0701fef84260b7bde57b3432_97.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_99.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_101.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_103.png)

### 图形界面功能简介

![](img/f8ed3c0f0701fef84260b7bde57b3432_105.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_107.png)

`firewall-config` 界面主要包含以下区域和功能：

![](img/f8ed3c0f0701fef84260b7bde57b3432_109.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_111.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_113.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_115.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_117.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_119.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_121.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_123.png)

*   **区域（Zones）**：可以为不同的网络接口（如ens160）分配不同的安全区域（如public、home），每个区域有预定义的规则集。
*   **服务（Services）**：列表显示了常见的网络服务（如ssh、dns、http）。勾选服务前面的复选框，即表示允许该服务的流量通过防火墙。
*   **端口（Ports）**：可以手动添加需要放行的特定端口号（如12345），并指定协议（TCP/UDP）。
*   **伪装（Masquerade）**：用于启用源地址转换（SNAT），通常用于网关设备让内网主机共享一个公网IP上网。
*   **端口转发（Port Forwarding）**：可以将到达本机某个端口的流量转发到本机或另一台主机的另一个端口。例如，将888端口的流量转发到22端口。
*   **富规则（Rich Rules）**：允许进行更精细的流量控制，例如基于源IP地址、协议、端口等组合条件来允许或拒绝流量。
*   **接口（Interfaces）**：将网络接口与特定的防火墙区域绑定。
*   **来源（Sources）**：可以定义IP地址或网段，方便在规则中引用。

![](img/f8ed3c0f0701fef84260b7bde57b3432_125.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_127.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_129.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_131.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_133.png)

**操作流程**：
1.  在界面左上方选择需要配置的区域（如 `public`）。
2.  在“服务”选项卡中，勾选需要放行的服务。
3.  如果需要永久生效，请确保勾选界面上的“运行时”或“永久”模式选项为“永久”。
4.  所有修改完成后，必须点击工具栏上的“重新加载”按钮（或选择“选项”->“重新加载防火墙”），才能使新的永久配置立即生效。

![](img/f8ed3c0f0701fef84260b7bde57b3432_135.png)

**验证**：在 `firewall-config` 中进行的配置与使用 `firewall-cmd` 命令行的配置是实时同步的。你可以在图形界面中操作，同时在终端使用 `firewall-cmd --list-all` 命令查看变化，反之亦然。

![](img/f8ed3c0f0701fef84260b7bde57b3432_137.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_139.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_141.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_143.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_145.png)

---

![](img/f8ed3c0f0701fef84260b7bde57b3432_147.png)

## 系统管理工具 Cockpit

![](img/f8ed3c0f0701fef84260b7bde57b3432_149.png)

`cockpit` 是一个基于Web的图形化服务器管理工具，提供了对系统状态、服务、用户、存储、网络等的集中管理界面。

![](img/f8ed3c0f0701fef84260b7bde57b3432_151.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_153.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_155.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_157.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_159.png)

### 安装与启用 Cockpit

![](img/f8ed3c0f0701fef84260b7bde57b3432_161.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_163.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_165.png)

1.  **安装**：`cockpit` 在RHEL 8中通常已默认安装。如果未安装，可以使用配置好的软件仓库进行安装：
    ```bash
    dnf install -y cockpit
    ```
2.  **启用并启动服务**：
    ```bash
    systemctl enable --now cockpit.socket
    ```
    `cockpit.socket` 是一个按需启动的服务，只有在有连接请求时才会激活 `cockpit` 服务进程。

![](img/f8ed3c0f0701fef84260b7bde57b3432_167.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_169.png)

### 访问 Cockpit

![](img/f8ed3c0f0701fef84260b7bde57b3432_171.png)

1.  打开浏览器，访问 `https://<你的服务器IP地址>:9090`。
2.  由于使用的是自签名证书，浏览器会提示安全风险，点击“高级”并选择“继续前往”即可。
3.  使用系统的root用户或具有sudo权限的用户名和密码登录。

![](img/f8ed3c0f0701fef84260b7bde57b3432_173.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_175.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_177.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_179.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_181.png)

### Cockpit 主要功能

![](img/f8ed3c0f0701fef84260b7bde57b3432_183.png)

登录后，你将看到一个功能丰富的仪表盘，主要包括：

![](img/f8ed3c0f0701fef84260b7bde57b3432_185.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_187.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_189.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_191.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_193.png)

*   **系统概览**：查看CPU、内存、磁盘、网络的使用情况。
*   **日志信息**：查看和筛选系统日志。
*   **存储管理**：对磁盘进行分区、格式化、挂载，以及管理RAID和LVM。
*   **网络配置**：配置网络接口、IP地址、绑定（Bonding）和成组（Teaming）。
*   **用户账户**：添加、删除用户，修改用户密码和权限。
*   **服务管理**：启动、停止、启用、禁用系统服务。
*   **软件更新**：检查并安装系统更新（需要订阅）。
*   **终端访问**：提供一个在浏览器中运行的命令行终端。

![](img/f8ed3c0f0701fef84260b7bde57b3432_195.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_197.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_199.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_201.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_203.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_205.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_207.png)

**提示**：`cockpit` 功能强大，在RHCSA考试中，如果对某些命令行操作不熟悉，可以尝试使用 `cockpit` 的图形界面来完成部分任务（如修改运行级别、管理用户、磁盘分区等），这通常是被允许的。

![](img/f8ed3c0f0701fef84260b7bde57b3432_209.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_211.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_213.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_215.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_217.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_219.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_221.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_223.png)

---

![](img/f8ed3c0f0701fef84260b7bde57b3432_225.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_227.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_229.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_231.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_233.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_235.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_237.png)

## 网络配置进阶

![](img/f8ed3c0f0701fef84260b7bde57b3432_239.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_241.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_243.png)

### 网络连接会话（Connection Profiles）

![](img/f8ed3c0f0701fef84260b7bde57b3432_245.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_247.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_249.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_251.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_253.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_255.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_257.png)

网络会话功能允许你为同一块网络接口创建多个配置模板（例如，用于公司静态IP和家庭DHCP的不同环境），并快速切换。

![](img/f8ed3c0f0701fef84260b7bde57b3432_259.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_261.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_263.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_265.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_267.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_269.png)

以下是创建和切换会话的命令行示例：

![](img/f8ed3c0f0701fef84260b7bde57b3432_271.png)

1.  **查看现有连接**：
    ```bash
    nmcli connection show
    ```
2.  **创建“家庭”会话（使用DHCP）**：
    ```bash
    nmcli connection add con-name home type ethernet ifname ens160
    ```
3.  **创建“公司”会话（使用静态IP）**：
    ```bash
    nmcli connection add con-name company type ethernet ifname ens160 ipv4.addresses 192.168.10.88/24 ipv4.gateway 192.168.10.1 ipv4.method manual
    ```
4.  **切换会话**：
    ```bash
    nmcli connection up home   # 切换到家庭配置
    nmcli connection up company # 切换到公司配置
    ```
    切换后，使用 `ip addr show ens160` 可以立即看到IP地址的变化。

### 网络接口绑定（Bonding）

网络绑定（Bonding）能将多个物理网卡聚合为一个逻辑网卡，以实现负载均衡和故障转移。

以下示例演示如何创建名为 `bond0` 的绑定接口，模式为 `balance-rr`（轮询）：

![](img/f8ed3c0f0701fef84260b7bde57b3432_273.png)

1.  **创建绑定接口**：
    ```bash
    nmcli connection add type bond con-name bond0 ifname bond0 bond.options "mode=balance-rr"
    ```
2.  **将物理网卡（如ens160, ens192）添加为从属接口**：
    ```bash
    nmcli connection add type bond-slave con-name bond0-port1 ifname ens160 master bond0
    nmcli connection add type bond-slave con-name bond0-port2 ifname ens192 master bond0
    ```
3.  **为绑定接口配置IP地址**：
    ```bash
    nmcli connection modify bond0 ipv4.addresses 192.168.10.10/24 ipv4.gateway 192.168.10.1 ipv4.method manual
    ```
4.  **激活绑定和从属连接**：
    ```bash
    nmcli connection up bond0
    nmcli connection up bond0-port1
    nmcli connection up bond0-port2
    ```
5.  **验证**：使用 `ip addr show bond0` 查看绑定接口的IP。你可以通过断开一块物理网卡来测试故障转移，使用 `ping` 命令观察是否仅出现短暂丢包后即恢复连通。

![](img/f8ed3c0f0701fef84260b7bde57b3432_275.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_277.png)

---

## 总结

本节课中我们一起学习了多个核心主题：
1.  **软件仓库管理**：我们掌握了如何挂载本地光盘镜像并配置YUM/DNF仓库文件，这是安装软件的基础。
2.  **图形化防火墙配置**：通过安装和使用 `firewall-config` 工具，我们学会了以更直观的方式管理防火墙规则，包括放行服务、端口、设置端口转发和富规则等。
3.  **全能管理工具Cockpit**：我们介绍了 `cockpit` 这个基于Web的强大管理界面，它能够通过浏览器完成对Linux服务器大部分日常管理工作，是运维工作的得力助手。
4.  **高级网络配置**：我们深入探讨了网络连接会话的创建与切换，以及通过网络绑定技术实现链路聚合和冗余，提升了网络的可靠性和性能。

![](img/f8ed3c0f0701fef84260b7bde57b3432_279.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_281.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_283.png)

![](img/f8ed3c0f0701fef84260b7bde57b3432_285.png)

通过结合命令行与图形化工具的学习，你不仅能够应对红帽认证考试，也能更高效地完成实际的系统运维工作。