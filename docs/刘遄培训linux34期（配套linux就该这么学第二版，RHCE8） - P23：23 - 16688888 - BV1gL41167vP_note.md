# Linux系统管理：第19章：PXE无人值守批量安装系统

![](img/c7c34d1538b9658a035a51b14bf2c3aa_0.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_2.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_4.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_6.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_8.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_10.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_12.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_14.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_16.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_18.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_20.png)

在本节课中，我们将学习如何使用PXE技术实现无人值守批量安装Linux操作系统。我们将通过整合之前学习的多个服务，构建一个完整的自动化部署架构。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_22.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_24.png)

## 概述

![](img/c7c34d1538b9658a035a51b14bf2c3aa_26.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_28.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_30.png)

在之前的课程中，我们学习了如何手动安装操作系统。然而，当需要为数十台甚至数百台服务器安装系统时，手动操作效率低下。本章将介绍如何通过PXE（Preboot eXecution Environment）技术，结合DHCP、TFTP、HTTP等服务，实现网络引导和自动化系统安装，从而大幅提升工作效率。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_32.png)

## 服务架构与原理

![](img/c7c34d1538b9658a035a51b14bf2c3aa_34.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_36.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_38.png)

上一节我们介绍了课程目标，本节中我们来看看实现PXE批量安装需要哪些服务协同工作。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_40.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_42.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_44.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_46.png)

要实现网络引导安装，客户端计算机在启动时没有操作系统。因此，我们需要一系列服务按顺序为其提供必要的引导信息和安装文件。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_48.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_50.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_52.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_54.png)

以下是实现PXE批量安装所需的核心服务及其作用：

![](img/c7c34d1538b9658a035a51b14bf2c3aa_56.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_58.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_60.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_62.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_64.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_66.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_68.png)

1.  **DHCP服务**：为没有操作系统的客户端自动分配IP地址、子网掩码、网关等网络配置信息，确保网络连通性。这是客户端能够与服务器通信的第一步。
2.  **TFTP服务**：用于向客户端传输初始引导文件（如 `pxelinux.0`）和内核镜像等小文件。它采用无验证的简单文件传输协议，适合在系统引导初期使用。
3.  **HTTP/FTP/NFS服务**：用于向客户端提供完整的操作系统安装源（即光盘镜像中的所有文件）。我们选择使用HTTP（Apache）服务来实现。
4.  **Kickstart应答文件**：一个自动应答脚本，其中预定义了系统安装过程中的所有配置选项（如分区、时区、密码等）。在安装过程中，系统会读取此文件来自动完成所有交互步骤，实现真正的“无人值守”。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_70.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_72.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_74.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_76.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_78.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_80.png)

整个工作流程可以概括为：客户端开机并从DHCP服务器获取网络配置 -> 通过TFTP下载引导程序 -> 引导程序通过HTTP获取安装源并加载Kickstart应答文件 -> 自动完成系统安装。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_82.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_84.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_86.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_88.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_90.png)

## 实验环境搭建

![](img/c7c34d1538b9658a035a51b14bf2c3aa_92.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_94.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_96.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_98.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_100.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_102.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_104.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_106.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_108.png)

理解了基本原理后，我们开始动手搭建实验环境。我们将在一台服务器上配置所有必需的服务。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_110.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_112.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_114.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_116.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_118.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_120.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_122.png)

### 1. 配置DHCP服务

![](img/c7c34d1538b9658a035a51b14bf2c3aa_124.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_126.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_128.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_130.png)

首先，我们需要配置DHCP服务为客户端分配IP地址并指定引导文件。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_132.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_134.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_136.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_138.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_140.png)

*   关闭虚拟机自带的DHCP服务，避免冲突。
*   安装DHCP服务软件包：`dnf install dhcp-server`
*   编辑DHCP主配置文件 `/etc/dhcp/dhcpd.conf`：

![](img/c7c34d1538b9658a035a51b14bf2c3aa_142.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_144.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_146.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_148.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_150.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_152.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_154.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_156.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_157.png)

```bash
# 允许PXE客户端引导
allow booting;
allow bootp;
# 忽略客户端更新
ignore client-updates;
# 设置子网和作用域
subnet 192.168.10.0 netmask 255.255.255.0 {
    range 192.168.10.100 192.168.10.200;
    option subnet-mask 255.255.255.0;
    option routers 192.168.10.10;
    default-lease-time 21600;
    max-lease-time 43200;
    # 指定TFTP服务器地址和引导文件名
    next-server 192.168.10.10;
    filename "pxelinux.0";
}
```

![](img/c7c34d1538b9658a035a51b14bf2c3aa_159.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_161.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_163.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_165.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_167.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_169.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_171.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_173.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_175.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_177.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_179.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_181.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_183.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_185.png)

*   启动DHCP服务并设置开机自启：
    ```bash
    systemctl start dhcpd
    systemctl enable dhcpd
    ```

![](img/c7c34d1538b9658a035a51b14bf2c3aa_187.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_189.png)

### 2. 配置TFTP服务

![](img/c7c34d1538b9658a035a51b14bf2c3aa_191.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_193.png)

引导文件需要通过TFTP服务传输给客户端。

*   安装TFTP服务及相关软件包：`dnf install tftp-server xinetd`
*   TFTP服务由`xinetd`超级守护进程管理，启用TFTP：
    ```bash
    vim /etc/xinetd.d/tftp
    # 将 `disable = yes` 改为 `disable = no`
    ```
*   重启`xinetd`服务以启动TFTP：`systemctl restart xinetd`

![](img/c7c34d1538b9658a035a51b14bf2c3aa_195.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_197.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_199.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_201.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_203.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_205.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_207.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_209.png)

### 3. 准备引导文件

![](img/c7c34d1538b9658a035a51b14bf2c3aa_211.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_213.png)

我们需要从系统安装镜像中获取PXE引导所需的文件。

*   挂载系统安装光盘镜像。
*   安装`syslinux`软件包，它包含了PXE引导文件：`dnf install syslinux`
*   将必要的引导文件复制到TFTP服务的默认目录 `/var/lib/tftpboot/` 中：
    ```bash
    cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/
    cp /media/cdrom/images/pxeboot/{vmlinuz,initrd.img} /var/lib/tftpboot/
    cp /media/cdrom/isolinux/{vesamenu.c32,boot.msg} /var/lib/tftpboot/
    ```
*   在TFTP目录中为PXE引导菜单创建配置文件目录：
    ```bash
    mkdir /var/lib/tftpboot/pxelinux.cfg
    ```
*   将光盘中的引导菜单配置文件复制并重命名为默认配置：
    ```bash
    cp /media/cdrom/isolinux/isolinux.cfg /var/lib/tftpboot/pxelinux.cfg/default
    ```

![](img/c7c34d1538b9658a035a51b14bf2c3aa_215.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_217.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_219.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_221.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_223.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_225.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_227.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_229.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_231.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_233.png)

### 4. 配置HTTP服务提供安装源

![](img/c7c34d1538b9658a035a51b14bf2c3aa_235.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_237.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_239.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_241.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_243.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_245.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_247.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_249.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_251.png)

客户端需要通过HTTP服务下载完整的安装文件。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_253.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_255.png)

*   安装Apache Web服务器：`dnf install httpd`
*   启动并启用HTTP服务：
    ```bash
    systemctl start httpd
    systemctl enable httpd
    ```
*   将挂载的光盘镜像中的所有文件复制到Web服务器的默认文档目录，例如 `/var/www/html/centos8/`：
    ```bash
    cp -r /media/cdrom/* /var/www/html/centos8/
    ```

![](img/c7c34d1538b9658a035a51b14bf2c3aa_257.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_259.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_261.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_263.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_265.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_267.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_269.png)

### 5. 修改PXE引导菜单

![](img/c7c34d1538b9658a035a51b14bf2c3aa_271.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_273.png)

我们需要修改PXE引导菜单，使其指向我们的HTTP安装源和Kickstart应答文件。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_275.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_277.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_279.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_281.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_283.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_285.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_287.png)

编辑 `/var/lib/tftpboot/pxelinux.cfg/default` 文件，找到安装系统的标签（如 `label linux`），修改其 `append` 行：

![](img/c7c34d1538b9658a035a51b14bf2c3aa_289.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_291.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_293.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_295.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_297.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_299.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_301.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_303.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_305.png)

```bash
append initrd=initrd.img ks=http://192.168.10.10/ks.cfg
```

![](img/c7c34d1538b9658a035a51b14bf2c3aa_307.png)

这行配置告诉安装程序从指定的HTTP地址获取Kickstart应答文件。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_309.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_311.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_313.png)

### 6. 创建Kickstart应答文件

![](img/c7c34d1538b9658a035a51b14bf2c3aa_315.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_317.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_319.png)

Kickstart文件定义了自动安装的所有参数。我们可以从现有系统的 `/root` 目录下找到一个名为 `anaconda-ks.cfg` 的模板文件，它记录了当前系统的安装方式。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_321.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_323.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_325.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_327.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_329.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_331.png)

*   将模板文件复制到Web服务器目录：
    ```bash
    cp /root/anaconda-ks.cfg /var/www/html/ks.cfg
    ```
*   根据需求修改 `/var/www/html/ks.cfg` 文件。关键修改项包括：
    *   `url --url="http://192.168.10.10/centos8/"`：指定安装源URL。
    *   `network --onboot=yes`：确保网卡在安装后启用。
    *   检查分区、密码、软件包选择等配置是否符合预期。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_333.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_335.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_337.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_339.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_341.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_343.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_345.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_347.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_349.png)

### 7. 关闭防火墙（实验环境）

![](img/c7c34d1538b9658a035a51b14bf2c3aa_351.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_353.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_355.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_357.png)

为了简化实验，我们暂时关闭防火墙。在生产环境中应配置相应的放行规则。

```bash
systemctl stop firewalld
systemctl disable firewalld
```

![](img/c7c34d1538b9658a035a51b14bf2c3aa_359.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_361.png)

## 测试PXE批量安装

![](img/c7c34d1538b9658a035a51b14bf2c3aa_363.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_365.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_367.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_369.png)

所有服务配置完成后，就可以进行测试了。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_371.png)

1.  新建一台虚拟机，将其网络适配器设置为与PXE服务器相同的网络模式（如“仅主机模式”）。
2.  确保该虚拟机**没有连接任何光盘镜像或ISO文件**。
3.  启动该虚拟机。如果配置正确，虚拟机将自动从网络引导。
4.  观察启动过程：客户端会从DHCP获取IP，从TFTP下载引导文件，然后自动加载HTTP上的安装源和Kickstart文件。
5.  安装过程将完全自动进行，无需人工干预，直至系统安装完毕并重启。

![](img/c7c34d1538b9658a035a51b14bf2c3aa_373.png)

## 总结

![](img/c7c34d1538b9658a035a51b14bf2c3aa_375.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_377.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_379.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_380.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_382.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_384.png)

![](img/c7c34d1538b9658a035a51b14bf2c3aa_385.png)

本节课中我们一起学习了如何搭建一个完整的PXE无人值守批量安装系统环境。我们整合了DHCP、TFTP、HTTP和Kickstart服务，实现了从网络引导到自动化安装的全流程。通过这项技术，我们可以极大地提高在多台服务器上部署操作系统的效率。请务必为配置好的服务器创建一个虚拟机快照，以便日后需要时快速恢复使用。