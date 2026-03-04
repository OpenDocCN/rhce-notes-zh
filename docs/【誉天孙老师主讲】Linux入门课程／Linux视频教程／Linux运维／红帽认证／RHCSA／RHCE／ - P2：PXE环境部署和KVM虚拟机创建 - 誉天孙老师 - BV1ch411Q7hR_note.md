# Linux入门课程：P2：PXE环境部署和KVM虚拟机创建 🖥️

![](img/8badced2fde1621b438318161d5cc3f4_0.png)

![](img/8badced2fde1621b438318161d5cc3f4_2.png)

![](img/8badced2fde1621b438318161d5cc3f4_3.png)

![](img/8badced2fde1621b438318161d5cc3f4_5.png)

![](img/8badced2fde1621b438318161d5cc3f4_7.png)

在本节课中，我们将要学习如何搭建一个完整的PXE（预启动执行环境）网络安装系统，以及如何使用KVM（基于内核的虚拟机）技术创建和管理虚拟机。我们将从PXE的工作原理讲起，逐步完成DHCP、TFTP、HTTP等服务的配置，最后学习KVM虚拟化的三种网络模式。

![](img/8badced2fde1621b438318161d5cc3f4_9.png)

![](img/8badced2fde1621b438318161d5cc3f4_11.png)

![](img/8badced2fde1621b438318161d5cc3f4_13.png)

## PXE启动过程回顾 🔄

![](img/8badced2fde1621b438318161d5cc3f4_15.png)

![](img/8badced2fde1621b438318161d5cc3f4_17.png)

上一节我们介绍了使用kickstart脚本安装系统。本节中，我们来看看PXE启动的完整过程。

PXE（预启动执行环境）允许计算机通过网络启动并安装操作系统。其启动过程如下：

1.  客户端网卡启动，向网络中的DHCP服务器请求IP地址。
2.  DHCP服务器不仅分配IP地址，还告知客户端引导程序（bootloader）的文件名和TFTP服务器的IP地址。
3.  客户端根据获得的信息，连接到指定的TFTP服务器，下载引导程序文件（如 `pxelinux.0`）。
4.  客户端执行引导程序，该程序会读取其配置文件（通常位于TFTP服务器的 `pxelinux.cfg` 目录下）。
5.  配置文件定义了启动菜单，并指定了内核（`vmlinuz`）、初始化内存盘（`initrd.img`）以及kickstart自动安装脚本的位置。
6.  客户端下载内核和初始化内存盘文件，并按照kickstart脚本的指示自动完成系统安装。

![](img/8badced2fde1621b438318161d5cc3f4_19.png)

![](img/8badced2fde1621b438318161d5cc3f4_21.png)

这个过程的核心是网络引导和自动化安装。

## 搭建PXE服务器 🛠️

![](img/8badced2fde1621b438318161d5cc3f4_23.png)

![](img/8badced2fde1621b438318161d5cc3f4_25.png)

![](img/8badced2fde1621b438318161d5cc3f4_27.png)

![](img/8badced2fde1621b438318161d5cc3f4_29.png)

理解了PXE的原理后，本节我们将动手搭建自己的PXE服务器。这需要配置DHCP、TFTP和HTTP服务。

![](img/8badced2fde1621b438318161d5cc3f4_31.png)

![](img/8badced2fde1621b438318161d5cc3f4_33.png)

以下是搭建PXE服务器的具体步骤：

### 1. 安装并配置DHCP服务

![](img/8badced2fde1621b438318161d5cc3f4_35.png)

![](img/8badced2fde1621b438318161d5cc3f4_37.png)

![](img/8badced2fde1621b438318161d5cc3f4_39.png)

![](img/8badced2fde1621b438318161d5cc3f4_41.png)

![](img/8badced2fde1621b438318161d5cc3f4_43.png)

DHCP服务为客户端分配IP地址并告知引导信息。

![](img/8badced2fde1621b438318161d5cc3f4_45.png)

*   **安装软件包**：`yum install dhcp -y`
*   **复制配置文件模板**：
    ```bash
    cp /usr/share/doc/dhcp*/dhcpd.conf.example /etc/dhcp/dhcpd.conf
    ```
*   **编辑配置文件 `/etc/dhcp/dhcpd.conf`**：
    需要配置以下关键部分：
    *   `domain-name`：设置域名。
    *   `domain-name-servers`：设置DNS服务器地址。
    *   `next-server`：指定TFTP服务器的IP地址。
    *   `filename`：指定引导程序文件名（如 `"pxelinux.0"`）。
    *   配置子网、地址池、网关等信息。
    ```bash
    # 示例配置片段
    option domain-name "example.com";
    option domain-name-servers 172.25.254.239;
    next-server 172.25.254.239;
    filename "pxelinux.0";
    subnet 172.25.254.0 netmask 255.255.255.0 {
      range 172.25.254.100 172.25.254.200;
      option routers 172.25.254.239;
    }
    ```
*   **启动服务**：`systemctl start dhcpd && systemctl enable dhcpd`

![](img/8badced2fde1621b438318161d5cc3f4_47.png)

![](img/8badced2fde1621b438318161d5cc3f4_49.png)

![](img/8badced2fde1621b438318161d5cc3f4_50.png)

![](img/8badced2fde1621b438318161d5cc3f4_52.png)

### 2. 安装并配置TFTP服务

TFTP服务用于提供引导程序、内核等小文件。

*   **安装软件包**：`yum install tftp-server -y`
*   **启用服务**：编辑 `/etc/xinetd.d/tftp`，将 `disable = yes` 改为 `disable = no`。
*   **启动服务**：`systemctl restart xinetd`
*   **准备TFTP根目录文件**：
    1.  复制引导程序：`cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/`
    2.  创建配置目录：`mkdir /var/lib/tftpboot/pxelinux.cfg`
    3.  从系统安装光盘复制内核和启动菜单配置文件：
        ```bash
        # 假设光盘已挂载在 /mnt
        cp /mnt/isolinux/vmlinuz /var/lib/tftpboot/
        cp /mnt/isolinux/initrd.img /var/lib/tftpboot/
        cp /mnt/isolinux/isolinux.cfg /var/lib/tftpboot/pxelinux.cfg/default
        ```
*   **编辑启动菜单 `/var/lib/tftpboot/pxelinux.cfg/default`**：
    可以修改默认菜单项，并添加kickstart自动安装选项。
    ```bash
    label rhce-auto
      menu label ^Install RHEL7 via Kickstart
      kernel vmlinuz
      append initrd=initrd.img ks=http://172.25.254.239/ks/ks01.cfg
    ```

### 3. 配置HTTP安装源和Kickstart脚本

HTTP服务用于提供系统安装所需的软件包（安装源）和kickstart脚本。

*   **安装软件包**：`yum install httpd -y`
*   **准备HTTP根目录**：
    1.  在 `/var/www/html` 下创建目录，例如 `pub`（存放安装源）和 `ks`（存放kickstart脚本）。
    2.  将系统安装光盘内容挂载或复制到 `/var/www/html/pub` 目录下，作为安装源。
        ```bash
        mount /dev/cdrom /var/www/html/pub
        ```
    3.  将编写好的kickstart脚本（如 `ks01.cfg`）复制到 `/var/www/html/ks/` 目录下。
*   **修改kickstart脚本**：确保脚本中的安装源路径指向HTTP服务器（例如 `url --url="http://172.25.254.239/pub"`）。
*   **启动服务**：`systemctl start httpd && systemctl enable httpd`

至此，一个完整的PXE自动化安装环境就搭建好了。客户端设置为网络启动后，即可自动获取IP并开始安装。

## KVM虚拟化技术 💻

完成了PXE环境的搭建，我们具备了快速部署系统的能力。本节中，我们来看看如何利用KVM虚拟化技术，在一台物理服务器上运行多个独立的虚拟机。

![](img/8badced2fde1621b438318161d5cc3f4_54.png)

![](img/8badced2fde1621b438318161d5cc3f4_56.png)

KVM（Kernel-based Virtual Machine）是Linux内核集成的虚拟化模块，它将Linux内核转变为一个裸机管理程序（Hypervisor）。每个KVM虚拟机都是一个普通的Linux进程，由标准Linux调度程序进行调度。

![](img/8badced2fde1621b438318161d5cc3f4_58.png)

### KVM与虚拟化产品

*   **虚拟化技术**：如KVM、Xen，是底层的核心实现。
*   **虚拟化产品/平台**：如VMware vSphere、Red Hat RHEV、Microsoft Hyper-V，是集成了管理、迁移、高可用等高级功能的企业级解决方案。
*   **云计算**：如阿里云、AWS，是在大规模虚拟化基础上提供的自助服务门户。

### 创建KVM虚拟机

在已安装KVM虚拟化组件的Linux系统上，可以使用 `virt-manager`（图形工具）或 `virt-install`（命令行工具）创建虚拟机。

![](img/8badced2fde1621b438318161d5cc3f4_60.png)

使用 `virt-manager` 创建虚拟机的大致步骤：
1.  启动 `virt-manager`。
2.  点击创建新虚拟机。
3.  选择安装方式（如“网络安装-PXE”）。
4.  选择操作系统类型和版本。
5.  分配CPU、内存资源。
6.  创建或选择虚拟磁盘。
7.  **配置虚拟网络**（这是关键，下文详述）。
8.  开始安装。

### KVM虚拟网络模式详解 🌐

为虚拟机配置网络时，有三种主要模式，理解其区别至关重要。

#### 1. 桥接模式

![](img/8badced2fde1621b438318161d5cc3f4_62.png)

桥接模式将虚拟机的网络接口直接连接到物理网络。
*   **原理**：在宿主机上创建一个虚拟网桥（如 `br0`），将物理网卡（如 `eth0`）作为该网桥的一个端口。虚拟机的虚拟网卡也连接到这个网桥。
*   **效果**：虚拟机就像物理网络中的一台独立主机，拥有和宿主机同网段的IP地址，可以直接与外部网络通信。
*   **配置桥接网卡示例**：
    ```bash
    # 创建桥接接口配置文件 /etc/sysconfig/network-scripts/ifcfg-br0
    DEVICE=br0
    TYPE=Bridge
    BOOTPROTO=static
    IPADDR=172.25.254.239
    NETMASK=255.255.255.0
    ONBOOT=yes

    # 修改物理网卡配置文件 /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    TYPE=Ethernet
    BRIDGE=br0 # 关键：指定桥接到 br0
    ONBOOT=yes
    ```
    重启网络服务后，在 `virt-manager` 中即可选择 `br0` 作为虚拟机的网络源。

![](img/8badced2fde1621b438318161d5cc3f4_64.png)

![](img/8badced2fde1621b438318161d5cc3f4_66.png)

#### 2. NAT模式

![](img/8badced2fde1621b438318161d5cc3f4_68.png)

![](img/8badced2fde1621b438318161d5cc3f4_70.png)

NAT（网络地址转换）模式是默认模式。
*   **原理**：宿主机创建一个虚拟网络（如 `192.168.122.0/24`）和一个虚拟NAT设备（通常是 `virbr0`）。虚拟机连接到这个虚拟网络。当虚拟机访问外部网络时，数据包通过宿主机进行地址转换后发出。
*   **效果**：虚拟机可以访问外部网络，但外部网络无法直接访问虚拟机，因为虚拟机的IP是私有地址。虚拟机之间可以互通。
*   **特点**：无需额外配置，开箱即用，适合让虚拟机上网的场景。

![](img/8badced2fde1621b438318161d5cc3f4_72.png)

#### 3. 仅主机模式

![](img/8badced2fde1621b438318161d5cc3f4_74.png)

![](img/8badced2fde1621b438318161d5cc3f4_76.png)

仅主机模式创建一个完全隔离的私有网络。
*   **原理**：宿主机创建一个虚拟网络，但不提供NAT或桥接功能。虚拟机连接到这个网络。
*   **效果**：虚拟机之间可以互相通信，虚拟机也可以与宿主机通信，但**完全不能访问外部网络**。
*   **用途**：用于构建封闭的测试环境。

![](img/8badced2fde1621b438318161d5cc3f4_78.png)

![](img/8badced2fde1621b438318161d5cc3f4_80.png)

**简单总结**：
*   **桥接**：虚拟机是“宿舍楼里的另一台电脑”，有独立门牌号（IP），大家都能找到它。
*   **NAT**：虚拟机是“你电脑后面用路由器分出来的设备”，可以上网，但外人不能直接访问它。
*   **仅主机**：虚拟机是“你电脑上几个互相连着的虚拟设备”，与世隔绝，自己玩。

![](img/8badced2fde1621b438318161d5cc3f4_82.png)

![](img/8badced2fde1621b438318161d5cc3f4_84.png)

![](img/8badced2fde1621b438318161d5cc3f4_86.png)

![](img/8badced2fde1621b438318161d5cc3f4_88.png)

![](img/8badced2fde1621b438318161d5cc3f4_90.png)

![](img/8badced2fde1621b438318161d5cc3f4_91.png)

## 总结 📚

![](img/8badced2fde1621b438318161d5cc3f4_93.png)

![](img/8badced2fde1621b438318161d5cc3f4_95.png)

![](img/8badced2fde1621b438318161d5cc3f4_96.png)

![](img/8badced2fde1621b438318161d5cc3f4_98.png)

![](img/8badced2fde1621b438318161d5cc3f4_100.png)

![](img/8badced2fde1621b438318161d5cc3f4_101.png)

![](img/8badced2fde1621b438318161d5cc3f4_103.png)

![](img/8badced2fde1621b438318161d5cc3f4_105.png)

本节课中我们一起学习了两个核心主题：
1.  **PXE自动化部署**：我们剖析了PXE网络启动的流程，并一步步搭建了由DHCP、TFTP、HTTP服务构成的PXE服务器，实现了操作系统的无人值守安装。
2.  **KVM虚拟化**：我们了解了KVM作为Linux内核虚拟化技术的特点，学习了使用 `virt-manager` 创建虚拟机，并深入探讨了桥接、NAT和仅主机三种虚拟网络模式的工作原理与配置方法。

![](img/8badced2fde1621b438318161d5cc3f4_107.png)

![](img/8badced2fde1621b438318161d5cc3f4_109.png)

![](img/8badced2fde1621b438318161d5cc3f4_111.png)

![](img/8badced2fde1621b438318161d5cc3f4_113.png)

![](img/8badced2fde1621b438318161d5cc3f4_115.png)

![](img/8badced2fde1621b438318161d5cc3f4_117.png)

![](img/8badced2fde1621b438318161d5cc3f4_118.png)

![](img/8badced2fde1621b438318161d5cc3f4_120.png)

通过本章学习，你掌握了快速批量部署系统（PXE）和创建隔离实验环境（KVM）的重要技能，这是Linux运维和红帽认证（如RHCSA/RHCE）中的实用知识。