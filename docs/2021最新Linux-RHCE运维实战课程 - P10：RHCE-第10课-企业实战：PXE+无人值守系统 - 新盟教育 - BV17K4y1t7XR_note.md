# Linux运维实战课程：P10：RHCE-第10课-企业实战：PXE+无人值守系统 🚀

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_1.png)

在本节课中，我们将学习如何构建一个企业级的PXE（预启动执行环境）无人值守安装系统。通过结合DHCP、TFTP、FTP和Kickstart等技术，实现服务器的批量自动化部署，极大地提升运维效率。

## 概述 📋

PXE本身并非一种安装方式，而是一种网络引导方式。它允许客户端计算机在没有本地操作系统的情况下，通过网络从服务器获取引导文件并启动。要实现无人值守的自动化安装，我们需要将PXE与Kickstart应答文件结合使用。Kickstart通过记录安装过程中所需的所有参数（如分区、密码、时区等），生成一个配置文件，从而实现安装过程的全自动化。

## PXE工作原理与流程 🔄

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_3.png)

上一节我们介绍了PXE的基本概念，本节中我们来看看其具体的工作流程。

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_5.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_6.png)

PXE的整个工作流程可以概括为以下六个步骤：

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_8.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_9.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_10.png)

1.  **客户端请求IP地址**：没有系统的客户端开机后，其网卡中的PXE芯片会向同一局域网内的DHCP服务器发送请求，以获取IP地址、子网掩码、网关等信息。
2.  **DHCP服务器响应并指引**：DHCP服务器在回应（`OFFER`）中，除了分配IP地址，还会通过 `next-server` 参数指明TFTP服务器的地址，并告知引导文件名（`filename “pxelinux.0”`）。
3.  **客户端下载引导文件**：客户端根据DHCP提供的信息，通过TFTP协议从指定服务器下载引导文件（如 `pxelinux.0`）以及相关的内核（`vmlinuz`）和初始化镜像文件（`initrd.img`）。
4.  **加载引导菜单**：引导文件执行后，会从TFTP服务器获取并显示一个启动菜单配置文件（如 `default`），供用户选择安装模式。
5.  **获取Kickstart应答文件**：根据菜单配置，客户端会去指定的FTP/HTTP/NFS服务器读取Kickstart应答文件（`ks.cfg`）。该文件包含了所有安装预设参数。
6.  **自动安装系统**：客户端根据Kickstart文件的配置，自动完成系统安装、软件包选择、分区设置等所有步骤，无需人工干预，安装完成后自动重启。

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_12.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_14.png)

## 服务部署与配置 ⚙️

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_16.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_18.png)

理解了原理后，接下来我们开始部署和配置所需的各项服务。我们将在一台服务器上集成DHCP、TFTP、FTP和Kickstart服务。

### 1. 安装与配置FTP服务

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_20.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_22.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_24.png)

首先，我们需要一个服务来提供系统安装镜像和Kickstart文件。这里我们使用vsftpd。

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_26.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_28.png)

以下是安装和启动FTP服务的步骤：
*   `yum install -y vsftpd`：安装vsftpd软件包。
*   `systemctl start vsftpd`：启动FTP服务。
*   `systemctl enable vsftpd`：设置FTP服务开机自启。
*   默认的FTP共享目录是 `/var/ftp/pub/`，我们将把系统镜像和Kickstart文件放在这里。

### 2. 安装与配置TFTP服务

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_30.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_32.png)

TFTP用于传输PXE引导阶段所需的小文件。

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_34.png)

以下是安装和配置TFTP服务的步骤：
*   `yum install -y tftp-server`：安装TFTP服务器。
*   编辑配置文件 `/etc/xinetd.d/tftp`：
    *   将 `disable = yes` 改为 `disable = no`。
    *   将 `server_args = -s /var/lib/tftpboot` 修改为 `server_args = -s /var/tftp -c`。`-s` 指定根目录，`-c` 允许上传新文件。
*   `mkdir /var/tftp`：创建TFTP服务目录。
*   `systemctl start tftp` 和 `systemctl enable tftp`：启动并启用TFTP服务（通过xinetd管理）。

### 3. 安装与配置DHCP服务

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_36.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_38.png)

DHCP服务为PXE客户端分配IP地址并指引其找到TFTP服务器。

以下是安装和配置DHCP服务的步骤：
*   `yum install -y dhcp`：安装DHCP服务器。
*   编辑主配置文件 `/etc/dhcp/dhcpd.conf`，以下是一个基本配置示例：
    ```bash
    subnet 192.168.1.0 netmask 255.255.255.0 {
      range 192.168.1.150 192.168.1.200;
      option routers 192.168.1.1;
      option subnet-mask 255.255.255.0;
      option domain-name “internal.example.com”;
      option domain-name-servers 8.8.8.8;
      default-lease-time 600;
      max-lease-time 7200;
      next-server 192.168.1.100; # 指向TFTP服务器IP
      filename “pxelinux.0”;     # 指定的PXE引导文件
    }
    ```
*   配置完成后，使用 `systemctl start dhcpd` 和 `systemctl enable dhcpd` 启动并启用服务。

### 4. 准备PXE引导文件

现在，我们需要将必要的引导文件放入TFTP目录。

以下是准备PXE引导文件的步骤：
*   `yum install -y syslinux`：安装syslinux，它提供了 `pxelinux.0` 文件。
*   `cp /usr/share/syslinux/pxelinux.0 /var/tftp/`：复制引导文件到TFTP根目录。
*   `mkdir /var/tftp/pxelinux.cfg`：创建菜单配置目录。
*   挂载系统安装镜像，并复制内核和初始化文件：
    ```bash
    mount /dev/cdrom /mnt
    cp /mnt/images/pxeboot/{vmlinuz,initrd.img} /var/tftp/
    cp /mnt/isolinux/isolinux.cfg /var/tftp/pxelinux.cfg/default
    ```
*   编辑菜单文件 `/var/tftp/pxelinux.cfg/default`，确保默认启动项指向 `label linux`，并可在其中添加Kickstart参数，例如：
    ```bash
    label linux
      menu label ^Install CentOS 7 via PXE
      kernel vmlinuz
      append initrd=initrd.img inst.repo=ftp://192.168.1.100/pub inst.ks=ftp://192.168.1.100/pub/ks.cfg quiet
    ```

### 5. 制作Kickstart应答文件

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_40.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_42.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_44.png)

Kickstart文件是无人值守安装的核心。我们可以使用系统提供的图形化工具 `system-config-kickstart` 来生成。

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_46.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_48.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_49.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_50.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_52.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_54.png)

以下是生成Kickstart文件的步骤：
*   `yum install -y system-config-kickstart`：安装图形化配置工具。
*   在图形界面中配置：基本配置（语言、时区）、安装方法（选择FTP，填写服务器地址和/pub目录）、分区方案（如 `/boot` 300MB，`/` 20GB，`swap` 4GB）、网络配置（设置网卡为DHCP）、防火墙设置、软件包选择等。
*   在“安装后脚本”部分，可以添加自动化任务，例如配置Yum源、安装常用软件等。
*   配置完成后，将文件保存为 `/var/ftp/pub/ks.cfg`。

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_56.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_58.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_59.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_60.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_62.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_64.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_66.png)

### 6. 提供安装镜像

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_68.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_70.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_72.png)

最后，需要将完整的系统安装镜像内容提供给FTP服务。

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_74.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_76.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_78.png)

以下是提供安装镜像的步骤：
*   将安装ISO镜像的所有内容解压或挂载到FTP共享目录：
    ```bash
    mount /dev/cdrom /var/ftp/pub/
    ```
*   或者，直接将ISO内的文件复制到 `/var/ftp/pub/` 目录下。

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_80.png)

## 客户端测试与验证 🖥️

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_82.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_84.png)

所有服务配置完成后，就可以进行测试了。

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_86.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_88.png)

1.  确保服务器防火墙已关闭或放行了相关服务端口（DHCP:67, TFTP:69, FTP:21）。
2.  在VMware/VirtualBox中创建一个新的虚拟机，不加载任何ISO镜像，将网络适配器设置为“桥接模式”或与PXE服务器在同一网络的“自定义网络”。
3.  启动虚拟机，客户端将自动从DHCP获取IP，然后通过TFTP下载引导文件，接着根据 `default` 菜单配置，从FTP服务器读取Kickstart文件并开始全自动安装。
4.  观察安装过程，如果一切配置正确，系统将无需任何手动操作，直至安装完成并重启。

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_90.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_91.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_93.png)

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_95.png)

## 知识扩展：自动挂载服务（autofs） 🔗

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_97.png)

在运维中，我们经常需要挂载网络存储（如NFS）。如果写入 `/etc/fstab` 实现开机挂载，无论是否使用都会占用资源。`autofs` 服务可以实现“用时挂载，闲时卸载”的自动管理。

以下是配置autofs服务的基本步骤：
*   `yum install -y autofs`：安装autofs。
*   编辑主配置文件 `/etc/auto.master`，指定挂载点和子配置文件：
    ```bash
    /media /etc/auto.iso
    ```
*   创建并编辑子配置文件 `/etc/auto.iso`，定义挂载细节：
    ```bash
    cdrom -fstype=iso9660,ro,nosuid :/dev/cdrom
    ```
*   `systemctl start autofs` 和 `systemctl enable autofs`：启动并启用服务。
*   当访问 `/media/cdrom` 时，系统会自动将 `/dev/cdrom` 挂载到该目录；一段时间不访问后，会自动卸载。

## 总结 📝

![](img/cd6f89d509598a19a4f3f1b0f69c76ce_99.png)

本节课中我们一起学习了企业级PXE无人值守安装系统的完整搭建过程。我们从PXE和Kickstart的工作原理入手，逐步部署了DHCP、TFTP、FTP等核心服务，准备了必要的引导文件，并使用工具生成了Kickstart应答文件，最终实现了客户端的全自动安装。此外，我们还简单介绍了 `autofs` 自动挂载服务作为补充知识。掌握这项技能，能够帮助你在面对大批量服务器部署任务时，从繁琐的手动操作中解放出来，实现高效、规范的自动化运维。