# Linux运维培训教程：P11：查看系统信息与修改主机名 🔧

![](img/fa6ea5a331d221451c360041be2b730d_1.png)

![](img/fa6ea5a331d221451c360041be2b730d_3.png)

在本节课中，我们将学习如何查看Linux系统中的核心硬件与配置信息，包括内核版本、CPU、内存、网卡以及主机名。同时，我们也会掌握如何修改主机名。这些是系统管理和故障排查的基础技能。

![](img/fa6ea5a331d221451c360041be2b730d_5.png)

![](img/fa6ea5a331d221451c360041be2b730d_7.png)

## 查看内核信息 🧠

![](img/fa6ea5a331d221451c360041be2b730d_9.png)

![](img/fa6ea5a331d221451c360041be2b730d_11.png)

上一节我们介绍了系统的基本操作，本节中我们来看看如何查看系统的内核信息。内核是Linux操作系统的核心，了解其版本对于软件兼容性和系统维护很重要。

使用 `uname` 命令可以查看内核相关信息。如果只输入 `uname`，则仅显示内核名称。

![](img/fa6ea5a331d221451c360041be2b730d_13.png)

![](img/fa6ea5a331d221451c360041be2b730d_15.png)

![](img/fa6ea5a331d221451c360041be2b730d_17.png)

```bash
uname
```

![](img/fa6ea5a331d221451c360041be2b730d_19.png)

![](img/fa6ea5a331d221451c360041be2b730d_21.png)

![](img/fa6ea5a331d221451c360041be2b730d_23.png)

![](img/fa6ea5a331d221451c360041be2b730d_25.png)

![](img/fa6ea5a331d221451c360041be2b730d_27.png)

要查看内核版本，需要添加 `-r` 选项。

![](img/fa6ea5a331d221451c360041be2b730d_29.png)

![](img/fa6ea5a331d221451c360041be2b730d_31.png)

![](img/fa6ea5a331d221451c360041be2b730d_33.png)

```bash
uname -r
```

![](img/fa6ea5a331d221451c360041be2b730d_35.png)

![](img/fa6ea5a331d221451c360041be2b730d_37.png)

如果想同时查看内核名称和完整版本信息，可以结合 `-s` 和 `-r` 选项。`-s` 选项默认就是显示内核名称。

![](img/fa6ea5a331d221451c360041be2b730d_39.png)

![](img/fa6ea5a331d221451c360041be2b730d_41.png)

![](img/fa6ea5a331d221451c360041be2b730d_43.png)

![](img/fa6ea5a331d221451c360041be2b730d_45.png)

```bash
uname -rs
```
这条命令会显示内核名称（如 Linux）、版本号（如 3.10.0）、适用的发行版和CPU架构。

![](img/fa6ea5a331d221451c360041be2b730d_47.png)

![](img/fa6ea5a331d221451c360041be2b730d_49.png)

## 查看CPU信息 💻

![](img/fa6ea5a331d221451c360041be2b730d_51.png)

![](img/fa6ea5a331d221451c360041be2b730d_53.png)

![](img/fa6ea5a331d221451c360041be2b730d_55.png)

了解CPU信息有助于评估系统性能。查看CPU信息通常有两种方法。

![](img/fa6ea5a331d221451c360041be2b730d_57.png)

![](img/fa6ea5a331d221451c360041be2b730d_59.png)

第一种方法是查看 `/proc` 目录下的 `cpuinfo` 文件。这个文件存储了CPU的详细信息。

![](img/fa6ea5a331d221451c360041be2b730d_61.png)

![](img/fa6ea5a331d221451c360041be2b730d_63.png)

```bash
cat /proc/cpuinfo
```
文件内容很多，我们主要关注以下几点：
*   `processor`：逻辑处理核心的编号，可以理解为CPU的核心数（从0开始计数）。
*   `vendor_id`：CPU制造商，如 `GenuineIntel` 代表英特尔。
*   `model name`：CPU的具体型号和频率。

第二种方法是使用 `lscpu` 命令，它能以更结构化的方式显示CPU信息。

```bash
lscpu
```
使用这条命令可以快速查看CPU架构、位数、核心数（`CPU(s)`）、插槽数等信息。对于服务器，这些信息在采购硬件时已确定，日常运维中更多是确认状态。

## 查看内存信息 🧠

内存资源直接影响系统运行效率。查看内存信息也有两种常用方式。

![](img/fa6ea5a331d221451c360041be2b730d_65.png)

![](img/fa6ea5a331d221451c360041be2b730d_67.png)

第一种是查看 `/proc` 目录下的 `meminfo` 文件。

![](img/fa6ea5a331d221451c360041be2b730d_69.png)

```bash
cat /proc/meminfo
```
我们最关心的是总内存和剩余内存，通常看文件的前两行即可。

![](img/fa6ea5a331d221451c360041be2b730d_71.png)

![](img/fa6ea5a331d221451c360041be2b730d_73.png)

更常用的是 `free` 命令，它可以动态查看内存使用情况。默认显示的单位是KB，不够直观。

![](img/fa6ea5a331d221451c360041be2b730d_75.png)

![](img/fa6ea5a331d221451c360041be2b730d_77.png)

```bash
free
```
添加 `-h` 选项可以以人类易读的格式（K, M, G）显示。

```bash
free -h
```
以下是 `free -h` 命令输出中需要关注的部分：
*   `Mem` 行代表物理内存信息。
    *   `total`：内存总量。
    *   `used`：已使用内存量。
    *   `free`：空闲内存量。
*   `Swap` 行代表交换分区信息。交换分区是在物理内存不足时，用磁盘空间临时充当内存使用，但会严重降低性能，现代服务器通常将其关闭。

可以使用以下命令临时关闭交换分区功能：

```bash
swapoff -a
```
这只是临时生效，重启后功能会恢复。永久关闭需要修改系统配置文件，我们将在后续课程中学习。

![](img/fa6ea5a331d221451c360041be2b730d_79.png)

## 查看网卡信息 🌐

![](img/fa6ea5a331d221451c360041be2b730d_81.png)

![](img/fa6ea5a331d221451c360041be2b730d_83.png)

网卡是服务器连接网络的桥梁。查看网卡信息主要涉及配置文件和命令。

![](img/fa6ea5a331d221451c360041be2b730d_85.png)

![](img/fa6ea5a331d221451c360041be2b730d_87.png)

网卡的配置文件路径为 `/etc/sysconfig/network-scripts/ifcfg-<网卡名>`（例如 `ifcfg-ens32`）。使用 `cat` 命令查看：

![](img/fa6ea5a331d221451c360041be2b730d_89.png)

![](img/fa6ea5a331d221451c360041be2b730d_91.png)

```bash
cat /etc/sysconfig/network-scripts/ifcfg-ens32
```
配置文件中需要关注的关键参数有：
*   `BOOTPROTO`：获取IP地址的方式，`static`/`none` 为静态IP，`dhcp` 为动态获取。
*   `ONBOOT`：是否在系统启动时激活网卡，必须为 `yes`。
*   `IPADDR`：IP地址。
*   `NETMASK`：子网掩码。
*   `GATEWAY`：网关地址。
*   `DNS1`：主DNS服务器地址。

![](img/fa6ea5a331d221451c360041be2b730d_93.png)

![](img/fa6ea5a331d221451c360041be2b730d_95.png)

另一种方式是使用 `ifconfig` 命令（如果系统未安装，可使用 `yum -y install net-tools` 安装）。

![](img/fa6ea5a331d221451c360041be2b730d_97.png)

```bash
ifconfig
```
该命令会显示所有网卡的详细信息，我们需要关注：
*   网卡名称（如 `ens32`）。
*   `inet` 后的IPV4地址和 `netmask` 子网掩码。
*   `RX packets`（接收）和 `TX packets`（发送）的数据包统计，错误计数（如 `errors`, `dropped`）应为0。
*   `lo` 是回环网卡，IP固定为 `127.0.0.1`，用于本机网络测试。

![](img/fa6ea5a331d221451c360041be2b730d_99.png)

还有一个简洁的命令 `ip a`，可以快速查看IP地址信息。

![](img/fa6ea5a331d221451c360041be2b730d_101.png)

![](img/fa6ea5a331d221451c360041be2b730d_102.png)

```bash
ip a
```

## 查看与修改主机名 🏷️

![](img/fa6ea5a331d221451c360041be2b730d_104.png)

主机名是服务器在网络中的标识。查看主机名有两种方法。

![](img/fa6ea5a331d221451c360041be2b730d_106.png)

第一种是查看主机名配置文件。

```bash
cat /etc/hostname
```
第二种是直接使用 `hostname` 命令。

![](img/fa6ea5a331d221451c360041be2b730d_108.png)

![](img/fa6ea5a331d221451c360041be2b730d_110.png)

```bash
hostname
```
修改主机名同样重要。使用 `hostname` 命令可以临时修改主机名，重启后失效。

![](img/fa6ea5a331d221451c360041be2b730d_112.png)

![](img/fa6ea5a331d221451c360041be2b730d_114.png)

```bash
hostname new-hostname
```
退出当前终端重新登录即可看到新主机名生效，但配置文件未变。

若要永久修改主机名，并使更改立即生效（无需重启，重新登录即可），可以使用 `hostnamectl` 命令。

![](img/fa6ea5a331d221451c360041be2b730d_116.png)

```bash
hostnamectl set-hostname new-hostname
```
执行此命令后，`/etc/hostname` 文件的内容也会同步更新。

![](img/fa6ea5a331d221451c360041be2b730d_118.png)

---

![](img/fa6ea5a331d221451c360041be2b730d_120.png)

![](img/fa6ea5a331d221451c360041be2b730d_122.png)

本节课中我们一起学习了如何查看Linux系统的内核、CPU、内存、网卡等核心硬件信息，并掌握了查看及永久修改主机名的方法。这些命令和文件是日常系统监控和维护的基础，请务必熟练掌握。接下来，我们将进入文本编辑器VIM的学习。