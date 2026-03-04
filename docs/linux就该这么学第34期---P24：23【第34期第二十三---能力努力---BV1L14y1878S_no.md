# Linux就该这么学：第34期：第23节课：红帽RHCE认证培训课程-Linux就该这么学

![](img/1288ed6dae38dd7017a3068edab1487a_1.png)

![](img/1288ed6dae38dd7017a3068edab1487a_3.png)

![](img/1288ed6dae38dd7017a3068edab1487a_4.png)

![](img/1288ed6dae38dd7017a3068edab1487a_6.png)

![](img/1288ed6dae38dd7017a3068edab1487a_8.png)

## 概述

![](img/1288ed6dae38dd7017a3068edab1487a_9.png)

![](img/1288ed6dae38dd7017a3068edab1487a_10.png)

![](img/1288ed6dae38dd7017a3068edab1487a_12.png)

![](img/1288ed6dae38dd7017a3068edab1487a_13.png)

在本节课中，我们将学习如何利用PXE和Kickstart技术实现无人值守批量安装Linux系统，以及如何通过编译源代码的方式部署一个完整的LNMP动态网站架构。我们将通过实践，将之前学习的多个服务（如DHCP、TFTP、HTTP等）整合起来，完成一个综合性的项目。

![](img/1288ed6dae38dd7017a3068edab1487a_14.png)

---

## 第19章：PXE + Kickstart 无人值守批量安装系统

![](img/1288ed6dae38dd7017a3068edab1487a_16.png)

上一节我们介绍了多种网络服务，本节中我们来看看如何将它们组合起来，实现一个实用的功能：批量自动化安装操作系统。

![](img/1288ed6dae38dd7017a3068edab1487a_18.png)

![](img/1288ed6dae38dd7017a3068edab1487a_19.png)

### 1. 实验架构与思路

首先，我们来理解整个批量安装系统的流程。我们需要搭建一个服务器，为没有系统的客户端通过网络自动安装操作系统。

![](img/1288ed6dae38dd7017a3068edab1487a_21.png)

![](img/1288ed6dae38dd7017a3068edab1487a_23.png)

![](img/1288ed6dae38dd7017a3068edab1487a_24.png)

以下是实现此功能所需的核心服务及其作用：

![](img/1288ed6dae38dd7017a3068edab1487a_26.png)

*   **DHCP服务**：为客户端自动分配IP地址、子网掩码、网关等网络信息，确保网络连通。
*   **TFTP服务**：用于向客户端传输系统引导文件（如PXE引导文件）和内核驱动文件。它无需验证，传输简单快捷。
*   **HTTP/FTP服务**：用于存放完整的操作系统安装镜像（即所有软件包文件），客户端从此处下载系统文件。
*   **Kickstart应答文件**：一个预先编写好的配置文件，其中包含了安装过程中所有需要手动确认的选项（如分区、密码、软件包选择等）。系统安装时会自动读取此文件，实现无人值守安装。

整个流程可以概括为：客户端开机后，通过DHCP获取IP -> 通过TFTP下载引导文件 -> 引导文件指示客户端从HTTP服务器获取安装镜像和Kickstart应答文件 -> 系统根据Kickstart文件自动完成安装。

### 2. 配置DHCP服务

首先，我们需要配置DHCP服务，为客户端分配网络信息并告知其引导文件的位置。

**步骤1：安装DHCP服务包并关闭虚拟机自带的DHCP。**

![](img/1288ed6dae38dd7017a3068edab1487a_28.png)

![](img/1288ed6dae38dd7017a3068edab1487a_30.png)

```bash
dnf install -y dhcp-server
# 在VMware虚拟网络编辑器中，关闭“仅主机模式”网卡的DHCP服务。
```

![](img/1288ed6dae38dd7017a3068edab1487a_32.png)

**步骤2：编辑DHCP主配置文件 `/etc/dhcp/dhcpd.conf`。**

![](img/1288ed6dae38dd7017a3068edab1487a_34.png)

```bash
vim /etc/dhcp/dhcpd.conf
```

在配置文件中添加以下内容：

![](img/1288ed6dae38dd7017a3068edab1487a_36.png)

![](img/1288ed6dae38dd7017a3068edab1487a_37.png)

```bash
# 允许客户端通过BOOTP协议获取地址
allow booting;
allow bootp;
# 忽略客户端更新
ignore client-updates;
# 设置IP地址租约时间为6小时
default-lease-time 21600;
max-lease-time 43200;
# 定义日志记录方式
option log-servers 192.168.10.10;
# 定义子网和作用域
subnet 192.168.10.0 netmask 255.255.255.0 {
    range 192.168.10.100 192.168.10.200;
    option subnet-mask 255.255.255.0;
    option routers 192.168.10.10;
    # 指定TFTP服务器地址和引导文件名
    next-server 192.168.10.10;
    filename "pxelinux.0";
}
```

![](img/1288ed6dae38dd7017a3068edab1487a_39.png)

![](img/1288ed6dae38dd7017a3068edab1487a_40.png)

![](img/1288ed6dae38dd7017a3068edab1487a_42.png)

**步骤3：启动DHCP服务并设置为开机自启。**

![](img/1288ed6dae38dd7017a3068edab1487a_44.png)

```bash
systemctl restart dhcpd
systemctl enable dhcpd
```

![](img/1288ed6dae38dd7017a3068edab1487a_46.png)

### 3. 配置TFTP服务

![](img/1288ed6dae38dd7017a3068edab1487a_48.png)

![](img/1288ed6dae38dd7017a3068edab1487a_49.png)

![](img/1288ed6dae38dd7017a3068edab1487a_50.png)

接下来，配置TFTP服务来提供引导文件。

![](img/1288ed6dae38dd7017a3068edab1487a_52.png)

![](img/1288ed6dae38dd7017a3068edab1487a_53.png)

![](img/1288ed6dae38dd7017a3068edab1487a_55.png)

**步骤1：安装TFTP服务及相关软件包。**

![](img/1288ed6dae38dd7017a3068edab1487a_56.png)

```bash
dnf install -y tftp-server xinetd syslinux
```

![](img/1288ed6dae38dd7017a3068edab1487a_58.png)

![](img/1288ed6dae38dd7017a3068edab1487a_59.png)

![](img/1288ed6dae38dd7017a3068edab1487a_60.png)

**步骤2：启用TFTP服务。**

![](img/1288ed6dae38dd7017a3068edab1487a_62.png)

![](img/1288ed6dae38dd7017a3068edab1487a_63.png)

![](img/1288ed6dae38dd7017a3068edab1487a_64.png)

编辑 `/etc/xinetd.d/tftp` 文件，将 `disable = yes` 改为 `disable = no`。

```bash
vim /etc/xinetd.d/tftp
```

![](img/1288ed6dae38dd7017a3068edab1487a_66.png)

![](img/1288ed6dae38dd7017a3068edab1487a_67.png)

**步骤3：启动xinetd服务（TFTP依赖于此服务）。**

![](img/1288ed6dae38dd7017a3068edab1487a_69.png)

```bash
systemctl restart xinetd
systemctl enable xinetd
```

![](img/1288ed6dae38dd7017a3068edab1487a_71.png)

![](img/1288ed6dae38dd7017a3068edab1487a_72.png)

![](img/1288ed6dae38dd7017a3068edab1487a_74.png)

![](img/1288ed6dae38dd7017a3068edab1487a_75.png)

![](img/1288ed6dae38dd7017a3068edab1487a_76.png)

**步骤4：部署引导文件。**

![](img/1288ed6dae38dd7017a3068edab1487a_77.png)

![](img/1288ed6dae38dd7017a3068edab1487a_78.png)

![](img/1288ed6dae38dd7017a3068edab1487a_80.png)

![](img/1288ed6dae38dd7017a3068edab1487a_81.png)

将必要的引导文件复制到TFTP服务的默认目录 `/var/lib/tftpboot/`。

![](img/1288ed6dae38dd7017a3068edab1487a_83.png)

![](img/1288ed6dae38dd7017a3068edab1487a_84.png)

```bash
# 复制PXE引导文件
cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/
# 复制引导菜单背景图（可选）
cp /media/cdrom/images/pxeboot/{vmlinuz,initrd.img} /var/lib/tftpboot/
# 复制内核和驱动文件
cp /media/cdrom/isolinux/{vesamenu.c32,boot.msg} /var/lib/tftpboot/
# 创建引导菜单目录
mkdir /var/lib/tftpboot/pxelinux.cfg
# 复制并重命名引导菜单配置文件
cp /media/cdrom/isolinux/isolinux.cfg /var/lib/tftpboot/pxelinux.cfg/default
```

![](img/1288ed6dae38dd7017a3068edab1487a_86.png)

![](img/1288ed6dae38dd7017a3068edab1487a_87.png)

![](img/1288ed6dae38dd7017a3068edab1487a_88.png)

### 4. 配置HTTP服务（提供系统镜像）

![](img/1288ed6dae38dd7017a3068edab1487a_90.png)

我们将使用Apache HTTP服务来存放系统安装镜像。

![](img/1288ed6dae38dd7017a3068edab1487a_92.png)

![](img/1288ed6dae38dd7017a3068edab1487a_93.png)

**步骤1：安装Apache服务。**

![](img/1288ed6dae38dd7017a3068edab1487a_95.png)

![](img/1288ed6dae38dd7017a3068edab1487a_97.png)

```bash
dnf install -y httpd
```

![](img/1288ed6dae38dd7017a3068edab1487a_98.png)

**步骤2：将系统安装光盘镜像挂载到HTTP的网站目录。**

![](img/1288ed6dae38dd7017a3068edab1487a_100.png)

![](img/1288ed6dae38dd7017a3068edab1487a_102.png)

假设光盘已挂载在 `/media/cdrom`。

```bash
# 创建网站子目录，用于存放镜像
mkdir /var/www/html/rh8
# 将光盘所有内容复制到网站目录（或使用软链接/挂载）
cp -r /media/cdrom/* /var/www/html/rh8/
```

![](img/1288ed6dae38dd7017a3068edab1487a_104.png)

![](img/1288ed6dae38dd7017a3068edab1487a_106.png)

**步骤3：启动HTTP服务并设置为开机自启。**

![](img/1288ed6dae38dd7017a3068edab1487a_108.png)

![](img/1288ed6dae38dd7017a3068edab1487a_109.png)

![](img/1288ed6dae38dd7017a3068edab1487a_111.png)

![](img/1288ed6dae38dd7017a3068edab1487a_113.png)

```bash
systemctl restart httpd
systemctl enable httpd
```

![](img/1288ed6dae38dd7017a3068edab1487a_114.png)

![](img/1288ed6dae38dd7017a3068edab1487a_116.png)

![](img/1288ed6dae38dd7017a3068edab1487a_118.png)

![](img/1288ed6dae38dd7017a3068edab1487a_119.png)

![](img/1288ed6dae38dd7017a3068edab1487a_120.png)

### 5. 配置Kickstart应答文件

![](img/1288ed6dae38dd7017a3068edab1487a_122.png)

![](img/1288ed6dae38dd7017a3068edab1487a_123.png)

Kickstart文件定义了自动安装的所有参数。

![](img/1288ed6dae38dd7017a3068edab1487a_125.png)

![](img/1288ed6dae38dd7017a3068edab1487a_126.png)

**步骤1：获取模板文件。**

![](img/1288ed6dae38dd7017a3068edab1487a_128.png)

![](img/1288ed6dae38dd7017a3068edab1487a_129.png)

![](img/1288ed6dae38dd7017a3068edab1487a_130.png)

系统安装后，会在root家目录生成一个名为 `anaconda-ks.cfg` 的文件，这是一个很好的模板。

![](img/1288ed6dae38dd7017a3068edab1487a_132.png)

![](img/1288ed6dae38dd7017a3068edab1487a_134.png)

![](img/1288ed6dae38dd7017a3068edab1487a_135.png)

```bash
cp ~/anaconda-ks.cfg /var/www/html/ks.cfg
```

**步骤2：编辑Kickstart文件 `/var/www/html/ks.cfg`。**

需要修改的关键行包括：

```bash
# 指定安装源为HTTP服务器
url --url="http://192.168.10.10/rh8"
# 设置root密码
rootpw --plaintext redhat
# 设置网络为DHCP获取
network --bootproto=dhcp --device=ens160 --onboot=on
# 设置系统时区
timezone Asia/Shanghai --isUtc
```

![](img/1288ed6dae38dd7017a3068edab1487a_137.png)

### 6. 修改引导菜单配置

![](img/1288ed6dae38dd7017a3068edab1487a_139.png)

编辑TFTP目录中的引导菜单文件 `/var/lib/tftpboot/pxelinux.cfg/default`，使其指向我们的HTTP安装源和Kickstart文件。

```bash
vim /var/lib/tftpboot/pxelinux.cfg/default
```

找到类似以下的行并进行修改：

```bash
label linux
  menu label ^Install Red Hat Enterprise Linux 8.0.0
  kernel vmlinuz
  append initrd=initrd.img inst.stage2=http://192.168.10.10/rh8 inst.ks=http://192.168.10.10/ks.cfg quiet
```

### 7. 创建客户端虚拟机并测试

![](img/1288ed6dae38dd7017a3068edab1487a_141.png)

![](img/1288ed6dae38dd7017a3068edab1487a_142.png)

1.  在VMware中新建一台虚拟机。
2.  内存建议1GB以上，硬盘20GB。
3.  **移除CD/DVD驱动器、USB控制器等所有可能用于安装的介质**。
4.  网络适配器设置为“仅主机模式”，与服务器在同一网络。
5.  启动虚拟机，它将自动从网络获取IP，加载引导文件，并从HTTP服务器下载镜像，最后根据Kickstart文件自动完成安装。

![](img/1288ed6dae38dd7017a3068edab1487a_144.png)

![](img/1288ed6dae38dd7017a3068edab1487a_145.png)

![](img/1288ed6dae38dd7017a3068edab1487a_146.png)

![](img/1288ed6dae38dd7017a3068edab1487a_147.png)

![](img/1288ed6dae38dd7017a3068edab1487a_149.png)

---

![](img/1288ed6dae38dd7017a3068edab1487a_151.png)

## 第20章：源码编译部署LNMP动态网站架构

上一节我们实现了系统的批量安装，本节中我们来看看如何不依赖系统软件仓库，通过编译源代码的方式，手动搭建一个主流的LNMP网站架构。

### 1. 源码编译安装概述

通过源码编译安装软件，可以带来更好的性能调优和灵活性，但需要自行解决依赖关系。其通用步骤如下：

1.  **下载与解压**：获取软件的源代码压缩包并解压。
    ```bash
    tar xzvf software.tar.gz
    ```
2.  **配置（Configure）**：运行源码目录中的 `configure` 脚本。此步骤会检查系统环境，生成编译规则（Makefile）。
    ```bash
    ./configure --prefix=/usr/local/software
    ```
3.  **编译（Make）**：根据上一步生成的Makefile，将源代码编译成二进制可执行文件。此步骤最耗时。
    ```bash
    make
    ```
4.  **安装（Make Install）**：将编译好的文件安装到系统中指定的位置（如 `--prefix` 定义的路径）。
    ```bash
    make install
    ```
5.  **清理（可选）**：删除编译过程中产生的临时文件。
    ```bash
    make clean
    ```

### 2. 部署LNMP环境

![](img/1288ed6dae38dd7017a3068edab1487a_153.png)

![](img/1288ed6dae38dd7017a3068edab1487a_154.png)

![](img/1288ed6dae38dd7017a3068edab1487a_155.png)

![](img/1288ed6dae38dd7017a3068edab1487a_156.png)

![](img/1288ed6dae38dd7017a3068edab1487a_157.png)

LNMP代表Linux、Nginx、MySQL、PHP。我们将逐一编译安装Nginx、MySQL和PHP。

![](img/1288ed6dae38dd7017a3068edab1487a_159.png)

**准备工作：安装编译环境及依赖包。**

![](img/1288ed6dae38dd7017a3068edab1487a_161.png)

![](img/1288ed6dae38dd7017a3068edab1487a_162.png)

![](img/1288ed6dae38dd7017a3068edab1487a_163.png)

![](img/1288ed6dae38dd7017a3068edab1487a_165.png)

![](img/1288ed6dae38dd7017a3068edab1487a_167.png)

![](img/1288ed6dae38dd7017a3068edab1487a_169.png)

![](img/1288ed6dae38dd7017a3068edab1487a_170.png)

```bash
dnf install -y gcc gcc-c++ make openssl-devel pcre-devel expat-devel perl autoconf libtool
```

![](img/1288ed6dae38dd7017a3068edab1487a_172.png)

![](img/1288ed6dae38dd7017a3068edab1487a_174.png)

![](img/1288ed6dae38dd7017a3068edab1487a_175.png)

#### 2.1 编译安装Nginx

![](img/1288ed6dae38dd7017a3068edab1487a_177.png)

![](img/1288ed6dae38dd7017a3068edab1487a_178.png)

![](img/1288ed6dae38dd7017a3068edab1487a_179.png)

![](img/1288ed6dae38dd7017a3068edab1487a_180.png)

**步骤1：创建运行Nginx的系统用户。**

```bash
useradd -M -s /sbin/nologin nginx
```

**步骤2：下载并解压Nginx源码包。**

![](img/1288ed6dae38dd7017a3068edab1487a_182.png)

![](img/1288ed6dae38dd7017a3068edab1487a_184.png)

```bash
wget http://nginx.org/download/nginx-1.20.2.tar.gz
tar xzvf nginx-1.20.2.tar.gz -C /usr/src/
cd /usr/src/nginx-1.20.2/
```

![](img/1288ed6dae38dd7017a3068edab1487a_186.png)

**步骤3：配置、编译、安装。**

![](img/1288ed6dae38dd7017a3068edab1487a_188.png)

![](img/1288ed6dae38dd7017a3068edab1487a_189.png)

![](img/1288ed6dae38dd7017a3068edab1487a_190.png)

![](img/1288ed6dae38dd7017a3068edab1487a_192.png)

```bash
./configure \
--prefix=/usr/local/nginx \
--user=nginx \
--group=nginx \
--with-http_ssl_module \
--with-http_stub_status_module

make
make install
```

![](img/1288ed6dae38dd7017a3068edab1487a_194.png)

**步骤4：启动Nginx并测试。**

![](img/1288ed6dae38dd7017a3068edab1487a_196.png)

```bash
/usr/local/nginx/sbin/nginx
# 在浏览器访问服务器IP，应能看到Nginx欢迎页。
```

![](img/1288ed6dae38dd7017a3068edab1487a_198.png)

![](img/1288ed6dae38dd7017a3068edab1487a_199.png)

![](img/1288ed6dae38dd7017a3068edab1487a_201.png)

#### 2.2 编译安装MySQL 8.0

![](img/1288ed6dae38dd7017a3068edab1487a_203.png)

MySQL 8.0提供了预编译的二进制包，安装更简便。

![](img/1288ed6dae38dd7017a3068edab1487a_205.png)

![](img/1288ed6dae38dd7017a3068edab1487a_206.png)

![](img/1288ed6dae38dd7017a3068edab1487a_207.png)

**步骤1：下载并解压MySQL二进制包。**

![](img/1288ed6dae38dd7017a3068edab1487a_209.png)

![](img/1288ed6dae38dd7017a3068edab1487a_210.png)

![](img/1288ed6dae38dd7017a3068edab1487a_211.png)

![](img/1288ed6dae38dd7017a3068edab1487a_212.png)

![](img/1288ed6dae38dd7017a3068edab1487a_214.png)

![](img/1288ed6dae38dd7017a3068edab1487a_216.png)

```bash
wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.28-linux-glibc2.12-x86_64.tar.xz
tar xJvf mysql-8.0.28-linux-glibc2.12-x86_64.tar.xz -C /usr/local/
cd /usr/local/
mv mysql-8.0.28-linux-glibc2.12-x86_64 mysql
```

![](img/1288ed6dae38dd7017a3068edab1487a_218.png)

**步骤2：创建MySQL用户和数据目录。**

![](img/1288ed6dae38dd7017a3068edab1487a_220.png)

![](img/1288ed6dae38dd7017a3068edab1487a_222.png)

```bash
useradd -M -s /sbin/nologin mysql
mkdir /usr/local/mysql/data
chown -R mysql:mysql /usr/local/mysql
```

**步骤3：初始化数据库。**

![](img/1288ed6dae38dd7017a3068edab1487a_224.png)

```bash
cd /usr/local/mysql
./bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
# 注意记录输出信息中的临时root密码。
```

![](img/1288ed6dae38dd7017a3068edab1487a_226.png)

**步骤4：配置启动脚本并启动MySQL。**

```bash
cp support-files/mysql.server /etc/init.d/mysqld
chmod +x /etc/init.d/mysqld
/etc/init.d/mysqld start
```

![](img/1288ed6dae38dd7017a3068edab1487a_228.png)

![](img/1288ed6dae38dd7017a3068edab1487a_230.png)

**步骤5：修改root密码并登录。**

```bash
# 使用初始化时获得的临时密码登录
/usr/local/mysql/bin/mysql -u root -p
# 登录后修改密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';
```

![](img/1288ed6dae38dd7017a3068edab1487a_232.png)

#### 2.3 编译安装PHP

![](img/1288ed6dae38dd7017a3068edab1487a_234.png)

**步骤1：下载并解压PHP源码包。**

```bash
wget https://www.php.net/distributions/php-7.4.28.tar.gz
tar xzvf php-7.4.28.tar.gz -C /usr/src/
cd /usr/src/php-7.4.28/
```

![](img/1288ed6dae38dd7017a3068edab1487a_236.png)

![](img/1288ed6dae38dd7017a3068edab1487a_237.png)

![](img/1288ed6dae38dd7017a3068edab1487a_239.png)

![](img/1288ed6dae38dd7017a3068edab1487a_240.png)

**步骤2：配置、编译、安装。** 这里需要指定与Nginx和MySQL的集成参数。

![](img/1288ed6dae38dd7017a3068edab1487a_242.png)

![](img/1288ed6dae38dd7017a3068edab1487a_243.png)

```bash
./configure \
--prefix=/usr/local/php \
--with-config-file-path=/usr/local/php/etc \
--enable-fpm \
--with-fpm-user=nginx \
--with-fpm-group=nginx \
--with-mysqli \
--with-pdo-mysql \
--with-openssl \
--with-zlib

![](img/1288ed6dae38dd7017a3068edab1487a_244.png)

![](img/1288ed6dae38dd7017a3068edab1487a_245.png)

![](img/1288ed6dae38dd7017a3068edab1487a_247.png)

make
make install
```

![](img/1288ed6dae38dd7017a3068edab1487a_249.png)

![](img/1288ed6dae38dd7017a3068edab1487a_251.png)

![](img/1288ed6dae38dd7017a3068edab1487a_252.png)

**步骤3：配置PHP-FPM。**

![](img/1288ed6dae38dd7017a3068edab1487a_254.png)

![](img/1288ed6dae38dd7017a3068edab1487a_255.png)

![](img/1288ed6dae38dd7017a3068edab1487a_256.png)

![](img/1288ed6dae38dd7017a3068edab1487a_258.png)

```bash
cp php.ini-production /usr/local/php/etc/php.ini
cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf
```

**步骤4：启动PHP-FPM。**

```bash
/usr/local/php/sbin/php-fpm
```

### 3. 配置Nginx支持PHP

编辑Nginx的配置文件 `/usr/local/nginx/conf/nginx.conf`，找到处理PHP请求的部分（或类似 `location ~ \.php$` 的配置），修改为：

```nginx
location ~ \.php$ {
    root           /usr/local/nginx/html; # 你的网站根目录
    fastcgi_pass   127.0.0.1:9000; # PHP-FPM监听地址
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    include        fastcgi_params;
}
```

重启Nginx使配置生效。

```bash
/usr/local/nginx/sbin/nginx -s reload
```

### 4. 部署网站程序（例如WordPress）

**步骤1：下载并解压WordPress。**

![](img/1288ed6dae38dd7017a3068edab1487a_260.png)

![](img/1288ed6dae38dd7017a3068edab1487a_261.png)

```bash
wget https://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz -C /usr/local/nginx/html/
chown -R nginx:nginx /usr/local/nginx/html/wordpress
```

**步骤2：在MySQL中为WordPress创建数据库和用户。**

![](img/1288ed6dae38dd7017a3068edab1487a_263.png)

![](img/1288ed6dae38dd7017a3068edab1487a_265.png)

```bash
mysql -u root -p
CREATE DATABASE wordpress;
CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'wppassword';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

![](img/1288ed6dae38dd7017a3068edab1487a_267.png)

![](img/1288ed6dae38dd7017a3068edab1487a_268.png)

![](img/1288ed6dae38dd7017a3068edab1487a_269.png)

**步骤3：通过浏览器完成WordPress安装。**

在浏览器中访问 `http://你的服务器IP/wordpress`，按照向导填写数据库信息（数据库名：wordpress，用户名：wpuser，密码：wppassword，主机：localhost）即可完成安装。

![](img/1288ed6dae38dd7017a3068edab1487a_271.png)

---

## 总结

本节课中我们一起学习了两个综合性很强的项目。

![](img/1288ed6dae38dd7017a3068edab1487a_273.png)

![](img/1288ed6dae38dd7017a3068edab1487a_275.png)

1.  **PXE + Kickstart无人值守安装**：我们整合了DHCP、TFTP、HTTP等多个服务，构建了一个能够为大量客户端自动部署操作系统的环境。这极大地提升了系统管理员在面临批量部署任务时的工作效率。
2.  **源码编译LNMP架构**：我们跳过了系统自带的软件包管理器，通过手动编译源代码的方式，部署了Nginx、MySQL和PHP，并成功搭建了一个动态网站。这个过程让我们深入理解了软件从源码到运行的完整步骤，并具备了在特殊环境下部署软件的能力。

![](img/1288ed6dae38dd7017a3068edab1487a_277.png)

![](img/1288ed6dae38dd7017a3068edab1487a_278.png)

![](img/1288ed6dae38dd7017a3068edab1487a_280.png)

![](img/1288ed6dae38dd7017a3068edab1487a_282.png)

这两个实验是对前期所学网络服务、系统管理、软件安装等知识的综合运用与巩固。希望大家通过实践，不仅掌握了具体的技术，更培养了解决复杂问题的系统化思维。