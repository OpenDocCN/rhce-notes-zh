# Linux入门课程：14：kickstart自动化安装脚本和DHCP搭建

## 概述
在本节课中，我们将学习如何实现Linux系统的自动化安装部署。主要内容包括理解kickstart自动化安装脚本的原理与编写方法，以及搭建DHCP服务器来配合网络启动（PXE）环境。通过本课的学习，你将能够批量、高效地部署操作系统。

![](img/66442635a94852bba8a31800bb30a041_1.png)

![](img/66442635a94852bba8a31800bb30a041_2.png)

![](img/66442635a94852bba8a31800bb30a041_4.png)

![](img/66442635a94852bba8a31800bb30a041_5.png)

---

## 自动化安装简介
上一节我们介绍了虚拟化，本节中我们来看看自动化安装部署。自动化安装可以让你无需手动点击每一步，系统便能自动完成安装过程，就像你重装系统时选择从网络启动，然后去吃个饭回来系统就装好了一样。

## kickstart脚本基础
要实现自动化安装，我们需要一个记录安装过程中所有配置的文件。这个文件通常位于家目录下，名为 `anaconda-ks.cfg`。

### 理解anaconda-ks.cfg文件
这个文件记录了操作系统安装过程中的所有设置，例如语言、安装源、root密码、分区方案、安装的软件包等。每安装好一个系统，都会生成这样一个文件。

以下是该文件核心内容的解析：
*   **认证与加密**：`auth --enableshadow --passalgo=sha512` 表示开启密码认证并使用sha512加密算法。
*   **安装后行为**：`reboot` 指令使系统安装完成后自动重启。
*   **安装源**：`url --url="..."` 指定了安装介质的来源，可以是本地光盘或网络地址。
*   **防火墙**：`firewall --enabled --service=ssh` 表示启用防火墙并放行SSH服务。
*   **首次启动设置**：`firstboot --disable` 表示跳过安装后的首次启动设置（如kdump和订阅注册）。
*   **系统设置**：`keyboard`、`lang` 分别设置了键盘布局和系统语言。
*   **网络配置**：`network --bootproto=dhcp` 表示通过DHCP自动获取IP地址。
*   **root密码**：`rootpw --iscrypted ...` 设置了加密的root密码。
*   **SELinux**：`selinux --enforcing` 表示启用SELinux安全机制。
*   **服务管理**：`services` 部分指定了哪些服务需要开启或关闭。
*   **时区**：`timezone` 设置了系统时区。
*   **引导程序**：`bootloader` 部分配置了GRUB引导程序的参数和安装位置。
*   **磁盘与分区**：`zerombr` 和 `clearpart` 用于清除磁盘原有信息；`part` 指令定义了新的分区方案。
*   **安装后脚本**：`%post` 和 `%end` 之间的内容会在系统安装完成后执行，用于进行额外的配置，如配置YUM仓库、安装软件包等。
*   **软件包选择**：`%packages` 和 `%end` 之间列出了需要安装的软件包。

手动编写这样一个复杂的文件非常困难，因此我们使用工具来生成和编辑它。

![](img/66442635a94852bba8a31800bb30a041_7.png)

![](img/66442635a94852bba8a31800bb30a041_9.png)

![](img/66442635a94852bba8a31800bb30a041_11.png)

![](img/66442635a94852bba8a31800bb30a041_13.png)

![](img/66442635a94852bba8a31800bb30a041_15.png)

![](img/66442635a94852bba8a31800bb30a041_17.png)

![](img/66442635a94852bba8a31800bb30a041_19.png)

![](img/66442635a94852bba8a31800bb30a041_21.png)

## 使用system-config-kickstart工具
我们可以使用 `system-config-kickstart` 这个图形化工具来生成kickstart脚本。

![](img/66442635a94852bba8a31800bb30a041_23.png)

![](img/66442635a94852bba8a31800bb30a041_25.png)

以下是使用该工具配置kickstart脚本的关键步骤：
1.  **安装工具**：首先需要安装 `system-config-kickstart` 软件包。
    ```bash
    yum install system-config-kickstart
    ```
2.  **基本配置**：运行 `system-config-kickstart` 命令打开工具，依次配置基本设置（语言、键盘、时区）、root密码、安装介质来源（例如HTTP网络安装源）、引导加载程序选项等。
3.  **分区配置**：在分区部分，可以添加诸如 `/boot` 和 `/` 分区。可以使用 `--grow` 参数让根分区占满磁盘剩余空间。
4.  **网络配置**：添加网络设备（如eth0），并设置为DHCP获取地址。
5.  **认证与安全**：选择密码加密方式，并设置SELinux和防火墙的状态。
6.  **显示配置**：选择安装过程是图形界面还是文本界面。
7.  **软件包选择**：该工具可能无法直接选择软件包，需要后续手动编辑脚本添加 `%packages` 部分。
8.  **预安装与后安装脚本**：在“预安装脚本”和“后安装脚本”区域，可以写入Shell命令。例如，在后安装脚本中配置YUM仓库并安装httpd服务。
    ```bash
    cat > /etc/yum.repos.d/dvd.repo << EOF
    [DVD]
    name=RHEL7.0
    baseurl=http://172.25.254.250/rhel7.0/x86_64/dvd
    enabled=1
    gpgcheck=0
    EOF
    yum -y install httpd
    systemctl start httpd
    systemctl enable httpd
    ```
9.  **保存脚本**：配置完成后，将脚本保存为一个文件，例如 `/root/ks01.cfg`。

![](img/66442635a94852bba8a31800bb30a041_27.png)

![](img/66442635a94852bba8a31800bb30a041_29.png)

![](img/66442635a94852bba8a31800bb30a041_31.png)

![](img/66442635a94852bba8a31800bb30a041_33.png)

![](img/66442635a94852bba8a31800bb30a041_35.png)

![](img/66442635a94852bba8a31800bb30a041_37.png)

![](img/66442635a94852bba8a31800bb30a041_39.png)

![](img/66442635a94852bba8a31800bb30a041_41.png)

![](img/66442635a94852bba8a31800bb30a041_42.png)

![](img/66442635a94852bba8a31800bb30a041_44.png)

![](img/66442635a94852bba8a31800bb30a041_45.png)

![](img/66442635a94852bba8a31800bb30a041_47.png)

![](img/66442635a94852bba8a31800bb30a041_48.png)

![](img/66442635a94852bba8a31800bb30a041_50.png)

![](img/66442635a94852bba8a31800bb30a041_52.png)

生成脚本后，我们还可以借鉴其他模板（如教室服务器上的示例）来补充内容，例如添加创建普通用户、安装特定软件包组（如虚拟化套件`@virtualization`）等。

![](img/66442635a94852bba8a31800bb30a041_54.png)

![](img/66442635a94852bba8a31800bb30a041_56.png)

![](img/66442635a94852bba8a31800bb30a041_58.png)

![](img/66442635a94852bba8a31800bb30a041_60.png)

![](img/66442635a94852bba8a31800bb30a041_62.png)

![](img/66442635a94852bba8a31800bb30a041_64.png)

![](img/66442635a94852bba8a31800bb30a041_65.png)

![](img/66442635a94852bba8a31800bb30a041_67.png)

### 验证kickstart脚本
使用 `ksvalidator` 工具可以检查kickstart脚本的语法是否正确。
```bash
ksvalidator /root/ks01.cfg
```

![](img/66442635a94852bba8a31800bb30a041_69.png)

![](img/66442635a94852bba8a31800bb30a041_71.png)

## 搭建自动化安装环境（DHCP + TFTP + HTTP）
仅有kickstart脚本还不够，我们需要搭建一个完整的网络安装环境。这通常需要DHCP、TFTP和HTTP（或FTP/NFS）服务的配合。

![](img/66442635a94852bba8a31800bb30a041_73.png)

![](img/66442635a94852bba8a31800bb30a041_75.png)

![](img/66442635a94852bba8a31800bb30a041_77.png)

### 1. DHCP服务器搭建
DHCP（动态主机配置协议）负责为客户端分配IP地址，并告知其网络引导信息。

![](img/66442635a94852bba8a31800bb30a041_79.png)

![](img/66442635a94852bba8a31800bb30a041_81.png)

![](img/66442635a94852bba8a31800bb30a041_82.png)

![](img/66442635a94852bba8a31800bb30a041_84.png)

![](img/66442635a94852bba8a31800bb30a041_85.png)

![](img/66442635a94852bba8a31800bb30a041_87.png)

**DHCP工作流程简述**：
1.  **发现**：客户端广播“DHCP Discover”报文寻找DHCP服务器。
2.  **提供**：DHCP服务器回应“DHCP Offer”报文，提供可用的IP地址。
3.  **请求**：客户端发送“DHCP Request”报文请求使用该IP。
4.  **确认**：DHCP服务器发送“DHCP Ack”报文确认分配，完成租约。

![](img/66442635a94852bba8a31800bb30a041_89.png)

![](img/66442635a94852bba8a31800bb30a041_91.png)

![](img/66442635a94852bba8a31800bb30a041_93.png)

![](img/66442635a94852bba8a31800bb30a041_94.png)

**配置DHCP服务**：
1.  安装DHCP服务器软件包。
    ```bash
    yum install dhcp
    ```
2.  配置DHCP主配置文件 `/etc/dhcp/dhcpd.conf`。可以从模板复制一个基础配置。
    ```bash
    cp /usr/share/doc/dhcp-*/dhcpd.conf.example /etc/dhcp/dhcpd.conf
    ```
3.  编辑配置文件，关键配置项如下：
    ```bash
    option domain-name "example.com";        # 域名
    option domain-name-servers 172.25.254.250; # DNS服务器
    default-lease-time 600;                  # 默认租约时间
    max-lease-time 7200;                     # 最大租约时间
    subnet 172.25.254.0 netmask 255.255.255.0 { # 定义子网
      range 172.25.254.100 172.25.254.200;   # IP地址池范围
      option domain-name-servers 172.25.254.250;
      option domain-name "example.com";
      option routers 172.25.254.254;         # 网关
      option broadcast-address 172.25.254.255; # 广播地址
      default-lease-time 600;
      max-lease-time 7200;
      next-server 172.25.254.239;            # TFTP服务器地址（重要）
      filename "pxelinux.0";                 # 网络引导文件（重要）
    }
    ```
    `next-server` 和 `filename` 是支持PXE启动的关键，它们告诉客户端从哪里获取引导程序。
4.  启动并启用DHCP服务。
    ```bash
    systemctl start dhcpd
    systemctl enable dhcpd
    ```

![](img/66442635a94852bba8a31800bb30a041_96.png)

![](img/66442635a94852bba8a31800bb30a041_98.png)

![](img/66442635a94852bba8a31800bb30a041_100.png)

![](img/66442635a94852bba8a31800bb30a041_102.png)

![](img/66442635a94852bba8a31800bb30a041_104.png)

![](img/66442635a94852bba8a31800bb30a041_106.png)

### 2. TFTP服务器搭建
TFTP（简单文件传输协议）用于向客户端传输小文件，如网络引导程序及其配置文件。

![](img/66442635a94852bba8a31800bb30a041_108.png)

![](img/66442635a94852bba8a31800bb30a041_110.png)

**配置TFTP服务**：
1.  安装TFTP服务器软件包。
    ```bash
    yum install tftp-server
    ```
2.  启用TFTP服务。编辑 `/etc/xinetd.d/tftp`，将 `disable = yes` 改为 `disable = no`。
3.  重启xinetd服务以应用更改。
    ```bash
    systemctl restart xinetd
    ```
4.  TFTP的根目录默认为 `/var/lib/tftpboot/`。我们需要将PXE引导所需的文件放入此目录。
    *   复制引导程序 `pxelinux.0`（由`syslinux`包提供）到TFTP根目录。
        ```bash
        cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/
        ```
    *   创建目录 `pxelinux.cfg`，并将启动菜单配置文件（如从安装光盘复制的`isolinux.cfg`）放入，并重命名为 `default`。
        ```bash
        mkdir /var/lib/tftpboot/pxelinux.cfg
        cp /mnt/isolinux/isolinux.cfg /var/lib/tftpboot/pxelinux.cfg/default
        ```
    *   将启动菜单所需的辅助文件（如内核`vmlinuz`、初始化内存盘`initrd.img`、背景图片等）从安装光盘复制到TFTP根目录。
        ```bash
        cp /mnt/isolinux/vmlinuz /mnt/isolinux/initrd.img /mnt/isolinux/*.png /var/lib/tftpboot/
        ```

![](img/66442635a94852bba8a31800bb30a041_112.png)

![](img/66442635a94852bba8a31800bb30a041_114.png)

![](img/66442635a94852bba8a31800bb30a041_116.png)

![](img/66442635a94852bba8a31800bb30a041_118.png)

### 3. 配置PXE启动菜单
PXE启动菜单文件就是上面提到的 `/var/lib/tftpboot/pxelinux.cfg/default`。我们需要修改它，使其指向我们的kickstart脚本。

![](img/66442635a94852bba8a31800bb30a041_120.png)

![](img/66442635a94852bba8a31800bb30a041_122.png)

![](img/66442635a94852bba8a31800bb30a041_123.png)

![](img/66442635a94852bba8a31800bb30a041_125.png)

![](img/66442635a94852bba8a31800bb30a041_127.png)

![](img/66442635a94852bba8a31800bb30a041_129.png)

![](img/66442635a94852bba8a31800bb30a041_131.png)

![](img/66442635a94852bba8a31800bb30a041_133.png)

![](img/66442635a94852bba8a31800bb30a041_135.png)

![](img/66442635a94852bba8a31800bb30a041_136.png)

编辑 `default` 文件，在启动项（`label linux`）的 `append` 行末尾添加 `ks` 参数：
```bash
label linux
  menu label ^Install RHEL7.0
  kernel vmlinuz
  append initrd=initrd.img ks=http://172.25.254.250/ks/ks01.cfg
```
这样，当客户端选择此菜单项时，就会自动从指定的HTTP地址获取kickstart脚本并执行自动化安装。

![](img/66442635a94852bba8a31800bb30a041_138.png)

![](img/66442635a94852bba8a31800bb30a041_140.png)

### 4. HTTP服务器提供安装源和脚本
我们需要一个HTTP服务器（如Apache httpd）来存放操作系统安装文件（安装源）和kickstart脚本。
1.  确保安装源（如DVD光盘内容）可以通过HTTP访问，例如地址为 `http://172.25.254.250/rhel7.0/x86_64/dvd`。
2.  将编写好的kickstart脚本（如`ks01.cfg`）放在HTTP服务器的特定目录下，例如 `http://172.25.254.250/ks/ks01.cfg`。

![](img/66442635a94852bba8a31800bb30a041_142.png)

![](img/66442635a94852bba8a31800bb30a041_144.png)

![](img/66442635a94852bba8a31800bb30a041_146.png)

![](img/66442635a94852bba8a31800bb30a041_148.png)

![](img/66442635a94852bba8a31800bb30a041_149.png)

![](img/66442635a94852bba8a31800bb30a041_151.png)

![](img/66442635a94852bba8a31800bb30a041_153.png)

![](img/66442635a94852bba8a31800bb30a041_155.png)

## 总结
本节课我们一起学习了Linux系统自动化安装的完整流程。
1.  **核心是kickstart脚本**：它定义了安装的所有配置，可以使用 `system-config-kickstart` 工具生成和编辑。
2.  **环境搭建是关键**：需要部署DHCP、TFTP和HTTP服务。
    *   **DHCP**：为客户端分配IP，并指引其找到TFTP服务器和引导文件。
    *   **TFTP**：提供网络引导程序（pxelinux.0）和启动菜单配置文件。
    *   **HTTP**：提供操作系统安装源和kickstart脚本本身。
3.  **PXE引导流程**：客户端开机从网络启动 → 通过DHCP获取IP和TFTP服务器信息 → 从TFTP下载引导程序 → 引导程序读取TFTP上的菜单配置 → 根据菜单配置，加载内核并从HTTP安装源启动 → 读取HTTP上的kickstart脚本 → 执行全自动安装。

![](img/66442635a94852bba8a31800bb30a041_157.png)

![](img/66442635a94852bba8a31800bb30a041_158.png)

![](img/66442635a94852bba8a31800bb30a041_160.png)

![](img/66442635a94852bba8a31800bb30a041_162.png)

![](img/66442635a94852bba8a31800bb30a041_164.png)

![](img/66442635a94852bba8a31800bb30a041_166.png)

通过这套体系，可以实现大批量服务器的快速、统一部署，极大地提升了运维效率。