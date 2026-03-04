# Linux系统管理基础：P11：查看与修改系统信息

![](img/cae650ece7bab995ca1547853994e2d3_1.png)

在本节课中，我们将学习如何查看Linux系统中的核心硬件与配置信息，包括内核版本、CPU、内存、网卡以及主机名，并掌握修改主机名的方法。这些是系统管理员日常维护和排错的基础技能。

![](img/cae650ece7bab995ca1547853994e2d3_3.png)

![](img/cae650ece7bab995ca1547853994e2d3_5.png)

![](img/cae650ece7bab995ca1547853994e2d3_7.png)

## 查看内核信息 🖥️

![](img/cae650ece7bab995ca1547853994e2d3_9.png)

![](img/cae650ece7bab995ca1547853994e2d3_11.png)

上一节我们介绍了系统的基本操作，本节中我们来看看如何查看系统的内核信息。内核是Linux操作系统的核心，了解其版本对于软件兼容性和系统维护至关重要。

使用 `uname` 命令可以查看内核相关信息。直接执行该命令会显示内核名称。

![](img/cae650ece7bab995ca1547853994e2d3_13.png)

```bash
uname
```

![](img/cae650ece7bab995ca1547853994e2d3_15.png)

![](img/cae650ece7bab995ca1547853994e2d3_17.png)

![](img/cae650ece7bab995ca1547853994e2d3_18.png)

若想查看内核版本，需要添加 `-r` 选项。

![](img/cae650ece7bab995ca1547853994e2d3_19.png)

![](img/cae650ece7bab995ca1547853994e2d3_21.png)

![](img/cae650ece7bab995ca1547853994e2d3_23.png)

![](img/cae650ece7bab995ca1547853994e2d3_25.png)

![](img/cae650ece7bab995ca1547853994e2d3_27.png)

```bash
uname -r
```

![](img/cae650ece7bab995ca1547853994e2d3_29.png)

![](img/cae650ece7bab995ca1547853994e2d3_31.png)

要查看完整的系统信息，包括内核名称和版本，可以结合 `-s`（显示内核名称，此为默认选项）和 `-r` 选项。

![](img/cae650ece7bab995ca1547853994e2d3_33.png)

```bash
uname -s -r
# 或简写为
uname -sr
```

![](img/cae650ece7bab995ca1547853994e2d3_35.png)

![](img/cae650ece7bab995ca1547853994e2d3_37.png)

![](img/cae650ece7bab995ca1547853994e2d3_38.png)

![](img/cae650ece7bab995ca1547853994e2d3_40.png)

执行上述命令后，将显示类似 `Linux 3.10.0` 的信息，其中包含内核名称、主版本号、次版本号、修订版本号以及适用的CPU架构（如x86_64）。

![](img/cae650ece7bab995ca1547853994e2d3_41.png)

![](img/cae650ece7bab995ca1547853994e2d3_43.png)

![](img/cae650ece7bab995ca1547853994e2d3_45.png)

## 查看CPU信息 ⚙️

![](img/cae650ece7bab995ca1547853994e2d3_47.png)

![](img/cae650ece7bab995ca1547853994e2d3_49.png)

了解CPU信息有助于评估系统性能。查看CPU信息主要有两种方法。

![](img/cae650ece7bab995ca1547853994e2d3_51.png)

![](img/cae650ece7bab995ca1547853994e2d3_52.png)

第一种方法是查看 `/proc/cpuinfo` 文件，该文件存储了CPU的详细信息。

![](img/cae650ece7bab995ca1547853994e2d3_54.png)

![](img/cae650ece7bab995ca1547853994e2d3_55.png)

![](img/cae650ece7bab995ca1547853994e2d3_57.png)

![](img/cae650ece7bab995ca1547853994e2d3_59.png)

```bash
cat /proc/cpuinfo
```

该文件内容较多，初学者可关注以下关键信息：
*   `processor`：逻辑处理器的编号，可以理解为CPU的核心数（注意是核心数，不是物理CPU颗数）。
*   `vendor_id`：CPU制造商，如 `GenuineIntel`。
*   `model name`：CPU型号名称。

第二种方法是使用 `lscpu` 命令，它能以更清晰的结构化格式显示CPU信息。

```bash
lscpu
```

该命令输出信息中，我们主要关注：
*   `Architecture`：CPU架构（如x86_64）。
*   `CPU(s)`：逻辑CPU核心总数。
*   `Core(s) per socket`：每个物理CPU插槽的核心数。
*   `Socket(s)`：物理CPU插槽数量（即CPU颗数）。

## 查看内存信息 💾

![](img/cae650ece7bab995ca1547853994e2d3_61.png)

内存是系统运行的关键资源。查看内存信息同样有两种常用方式。

![](img/cae650ece7bab995ca1547853994e2d3_63.png)

第一种方法是查看 `/proc/meminfo` 文件。

![](img/cae650ece7bab995ca1547853994e2d3_65.png)

![](img/cae650ece7bab995ca1547853994e2d3_67.png)

![](img/cae650ece7bab995ca1547853994e2d3_68.png)

```bash
cat /proc/meminfo
```

![](img/cae650ece7bab995ca1547853994e2d3_70.png)

![](img/cae650ece7bab995ca1547853994e2d3_71.png)

我们最关心的是总内存和可用内存，通常查看文件前两行即可，即 `MemTotal` 和 `MemFree`。

更常用且直观的命令是 `free`。直接使用 `free` 命令会以字节为单位显示，可读性较差。

```bash
free
```

建议使用 `-h` 选项，以人类易读的格式（K, M, G）显示内存使用情况。

```bash
free -h
```

![](img/cae650ece7bab995ca1547853994e2d3_73.png)

以下是 `free -h` 命令输出中需要关注的部分：
*   `total`：物理内存总量。
*   `used`：已使用的内存量。
*   `free`：空闲的内存量。
*   `available`：估算的可用内存量（包含缓存和缓冲区），此值通常比 `free` 更大，更贴近实际可用情况。
*   `Swap`：交换分区信息。当物理内存不足时，系统会使用磁盘空间作为虚拟内存，但这会严重影响性能。现代服务器通常建议关闭或仅保留少量Swap。

![](img/cae650ece7bab995ca1547853994e2d3_75.png)

![](img/cae650ece7bab995ca1547853994e2d3_77.png)

**临时关闭Swap功能**（重启后失效）：
```bash
swapoff -a
```
执行后，使用 `free -h` 查看，`Swap` 行将显示为0。

![](img/cae650ece7bab995ca1547853994e2d3_79.png)

## 查看网卡信息 🌐

![](img/cae650ece7bab995ca1547853994e2d3_81.png)

网卡是服务器连接网络的桥梁。查看网卡信息主要涉及配置文件和命令。

![](img/cae650ece7bab995ca1547853994e2d3_83.png)

![](img/cae650ece7bab995ca1547853994e2d3_85.png)

![](img/cae650ece7bab995ca1547853994e2d3_87.png)

![](img/cae650ece7bab995ca1547853994e2d3_89.png)

网卡的配置文件路径为 `/etc/sysconfig/network-scripts/ifcfg-<网卡名>`，例如 `ifcfg-ens32`。查看其内容：

![](img/cae650ece7bab995ca1547853994e2d3_91.png)

```bash
cat /etc/sysconfig/network-scripts/ifcfg-ens32
```

配置文件中需要关注的关键参数：
*   `TYPE`：网络类型，通常为 `Ethernet`（以太网）。
*   `BOOTPROTO`：IP地址获取方式。`static` 或 `none` 表示静态IP，`dhcp` 表示动态获取。
*   `NAME`：网卡名称。
*   `ONBOOT`：是否在系统启动时激活网卡，必须为 `yes`。
*   `IPADDR`：IP地址。
*   `NETMASK` 或 `PREFIX`：子网掩码或前缀长度。
*   `GATEWAY`：网关地址。
*   `DNS1`：首选DNS服务器地址。

![](img/cae650ece7bab995ca1547853994e2d3_93.png)

![](img/cae650ece7bab995ca1547853994e2d3_95.png)

![](img/cae650ece7bab995ca1547853994e2d3_96.png)

使用 `ifconfig` 命令可以查看当前活跃的网卡状态和网络配置。如果系统未安装该命令，可使用 `yum` 安装 `net-tools` 软件包。

```bash
yum -y install net-tools
ifconfig
```

`ifconfig` 命令输出信息丰富，我们主要关注：
*   网卡名称（如 `ens32`）。
*   `inet`：IPv4地址。
*   `netmask`：子网掩码。
*   `ether`：MAC地址。
*   `RX packets` 和 `TX packets`：分别表示接收和发送的数据包数量及流量，用于监控网络活动。

此外，`ip` 命令是更现代的网络配置工具，查看IP地址的简写方式为：
```bash
ip a
# 或
ip addr
```

![](img/cae650ece7bab995ca1547853994e2d3_98.png)

![](img/cae650ece7bab995ca1547853994e2d3_100.png)

## 查看与修改主机名 🏷️

主机名用于在网络中标识一台机器。查看主机名有两种方式。

![](img/cae650ece7bab995ca1547853994e2d3_102.png)

![](img/cae650ece7bab995ca1547853994e2d3_104.png)

第一种是查看主机名配置文件：
```bash
cat /etc/hostname
```

![](img/cae650ece7bab995ca1547853994e2d3_106.png)

第二种是使用 `hostname` 命令：
```bash
hostname
```

![](img/cae650ece7bab995ca1547853994e2d3_108.png)

修改主机名也分为临时修改和永久修改。

**临时修改主机名**（重启后失效）：
```bash
hostname new-hostname
```
修改后需要重新登录终端会话才能看到变化。

![](img/cae650ece7bab995ca1547853994e2d3_110.png)

**永久修改主机名**（立即生效且重启后保持）：
```bash
hostnamectl set-hostname new-hostname
```
执行此命令后，系统会同时更新 `/etc/hostname` 文件。同样，需要重新登录终端或开启新的会话窗口才能看到新的主机名提示符。

![](img/cae650ece7bab995ca1547853994e2d3_112.png)

---

![](img/cae650ece7bab995ca1547853994e2d3_114.png)

![](img/cae650ece7bab995ca1547853994e2d3_116.png)

本节课中我们一起学习了如何查看Linux系统的内核、CPU、内存、网卡和主机名信息，并掌握了临时与永久修改主机名的方法。这些命令和文件是系统管理和故障排查的基础，请务必熟练掌握。接下来，我们将进入文本编辑器VIM的学习。