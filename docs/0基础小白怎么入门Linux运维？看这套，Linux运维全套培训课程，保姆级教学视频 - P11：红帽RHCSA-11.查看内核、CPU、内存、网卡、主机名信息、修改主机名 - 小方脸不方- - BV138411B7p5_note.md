# Linux运维全套培训课程：P11：查看系统信息与修改主机名 🔧

![](img/bb98f2f4e611d4315da4296b70f5b85e_1.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_3.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_5.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_7.png)

在本节课中，我们将学习如何查看Linux系统中的关键硬件和配置信息，包括内核版本、CPU、内存、网卡以及主机名。同时，我们也会掌握如何修改主机名。这些是系统管理和故障排查的基础技能。

![](img/bb98f2f4e611d4315da4296b70f5b85e_9.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_11.png)

## 查看内核信息 🧠

![](img/bb98f2f4e611d4315da4296b70f5b85e_13.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_15.png)

上一节我们介绍了系统的基本概念，本节中我们来看看如何查看系统的核心——内核信息。

![](img/bb98f2f4e611d4315da4296b70f5b85e_17.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_19.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_21.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_23.png)

Linux系统运行在Linux内核之上。要查看内核的版本及相关信息，可以使用 `uname` 命令。

![](img/bb98f2f4e611d4315da4296b70f5b85e_25.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_27.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_29.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_31.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_33.png)

*   **查看内核名称**：直接运行 `uname` 命令，会显示内核名称（如 Linux）。
*   **查看内核版本**：使用 `uname -r` 选项，可以显示内核的详细版本号（如 3.10.0）。
*   **查看完整内核信息**：结合 `-s`（系统名称）和 `-r`（版本）选项，使用 `uname -sr` 可以同时显示内核名称和版本。

![](img/bb98f2f4e611d4315da4296b70f5b85e_35.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_37.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_39.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_41.png)

**命令示例**：
```bash
uname
uname -r
uname -sr
```
执行 `uname -sr` 可能会输出类似 `Linux 3.10.0-1160.el7.x86_64` 的结果，这表示系统使用的是Linux内核，版本是3.10.0，适用于Red Hat Enterprise Linux 7，CPU架构是x86_64位。

![](img/bb98f2f4e611d4315da4296b70f5b85e_43.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_45.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_47.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_49.png)

## 查看CPU信息 💻

![](img/bb98f2f4e611d4315da4296b70f5b85e_51.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_53.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_55.png)

了解了内核信息后，接下来我们查看系统的“大脑”——CPU。

![](img/bb98f2f4e611d4315da4296b70f5b85e_57.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_59.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_61.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_63.png)

查看CPU信息主要有两种方法。

以下是两种查看CPU信息的方法：
1.  **查看 `/proc/cpuinfo` 文件**：这个文件存储了CPU的详细信息。可以使用 `cat /proc/cpuinfo` 命令查看。文件内容很多，初学者主要关注以下几点：
    *   `processor`：逻辑处理器的编号，可以大致理解为CPU的核心数（注意是逻辑核心，对于支持超线程的CPU，核心数会翻倍）。
    *   `vendor_id`：CPU制造商（如 GenuineIntel 代表英特尔）。
    *   `model name`：CPU型号名称。
2.  **使用 `lscpu` 命令**：这条命令能以更结构化的方式显示CPU信息，更易于阅读。它会显示架构、CPU核心数、线程数、型号、缓存大小等。

**命令示例**：
```bash
cat /proc/cpuinfo
lscpu
```
对于服务器管理，通常我们更关心CPU的核心数、型号和主频。这些信息在采购硬件时已确定，日常运维中主要用于核对和监控。

## 查看内存信息 🧠

![](img/bb98f2f4e611d4315da4296b70f5b85e_65.png)

系统运行离不开内存。现在我们来学习如何查看内存使用情况。

![](img/bb98f2f4e611d4315da4296b70f5b85e_67.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_69.png)

查看内存信息也有两种常用方法。

![](img/bb98f2f4e611d4315da4296b70f5b85e_71.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_73.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_75.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_77.png)

以下是查看内存信息的方法：
1.  **查看 `/proc/meminfo` 文件**：此文件包含了详细的内存统计数据。使用 `cat /proc/meminfo` 命令查看，我们最关心的是前两行：
    *   `MemTotal`：物理内存总量。
    *   `MemFree`：空闲内存量。
2.  **使用 `free` 命令**：这是运维中最常用的查看内存的命令。默认显示的单位是KB，可读性较差。建议加上 `-h` 选项，以人类易读的格式（K, M, G）显示。

**命令示例**：
```bash
cat /proc/meminfo
free -h
```
执行 `free -h` 后，关注 `Mem` 行：
*   `total`：内存总量。
*   `used`：已使用内存。
*   `free`：空闲内存。
*   `available`：可用内存（估算的可用内存，比 `free` 更准确）。

**关于 Swap 交换分区**：
输出中还有 `Swap` 行。Swap是硬盘上的一块空间，当物理内存不足时，系统会将部分数据暂时移到Swap中。但这会严重降低性能，因此现代企业服务器通常内存充足，会**禁用Swap**。
*   **临时禁用Swap**：`swapoff -a`
*   **启用Swap**：`swapon -a`
> **注意**：`swapoff -a` 是临时生效，重启后恢复。永久禁用需要修改配置文件，我们将在后续课程中学习。

## 查看网卡信息 🌐

![](img/bb98f2f4e611d4315da4296b70f5b85e_79.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_81.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_83.png)

网络是服务器的生命线。本节我们学习如何查看和了解网卡配置。

![](img/bb98f2f4e611d4315da4296b70f5b85e_85.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_87.png)

查看网卡信息主要涉及配置文件和命令。

![](img/bb98f2f4e611d4315da4296b70f5b85e_89.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_91.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_93.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_95.png)

以下是查看网卡信息的关键点：
1.  **网卡配置文件**：网卡的IP地址、子网掩码、网关等配置保存在 `/etc/sysconfig/network-scripts/` 目录下，文件名通常为 `ifcfg-ens33` 或 `ifcfg-eth0`。使用 `cat` 命令查看，重点关注：
    *   `BOOTPROTO`：获取IP方式，`static`/`none` 为静态IP，`dhcp` 为动态获取。
    *   `ONBOOT`：是否开机自启，必须为 `yes`。
    *   `IPADDR`：IP地址。
    *   `NETMASK`：子网掩码。
    *   `GATEWAY`：网关。
    *   `DNS1`：DNS服务器地址。
2.  **使用 `ifconfig` 命令**：此命令可以显示所有活跃网卡的详细信息。如果系统未安装，可使用 `yum -y install net-tools` 安装。输出信息包括：
    *   网卡名（如 `ens33`）。
    *   IP地址（`inet`）。
    *   子网掩码（`netmask`）。
    *   MAC地址（`ether`）。
    *   接收/发送的数据包和流量统计（RX/TX packets/bytes）。
3.  **使用 `ip addr` 命令**：这是更现代的命令，缩写为 `ip a`。它简洁地显示IP地址信息，但不显示流量统计。
4.  **回环网卡（lo）**：这是一个虚拟网卡，IP地址固定为 `127.0.0.1`，用于本机内部通信和测试。

![](img/bb98f2f4e611d4315da4296b70f5b85e_97.png)

**命令示例**：
```bash
cat /etc/sysconfig/network-scripts/ifcfg-ens33
ifconfig
ip a
ping 127.0.0.1
```

![](img/bb98f2f4e611d4315da4296b70f5b85e_99.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_101.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_102.png)

## 查看与修改主机名 🏷️

最后，我们来管理服务器的“名字”——主机名。

![](img/bb98f2f4e611d4315da4296b70f5b85e_104.png)

查看和修改主机名非常简单。

![](img/bb98f2f4e611d4315da4296b70f5b85e_106.png)

以下是操作主机名的方法：
1.  **查看主机名**：
    *   **查看配置文件**：主机名保存在 `/etc/hostname` 文件中，使用 `cat /etc/hostname` 查看。
    *   **使用命令**：直接运行 `hostname` 命令。
2.  **修改主机名**：
    *   **临时修改**：使用 `hostname 新主机名`，立即生效，但**重启服务器后失效**。
    *   **永久修改**：使用 `hostnamectl set-hostname 新主机名`，此命令会同时修改配置文件并立即生效（需要重新打开终端或登录会话才能看到变化），且重启后依然有效。

![](img/bb98f2f4e611d4315da4296b70f5b85e_108.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_110.png)

**命令示例**：
```bash
# 查看
hostname
cat /etc/hostname

![](img/bb98f2f4e611d4315da4296b70f5b85e_112.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_114.png)

# 临时修改
hostname myserver-temp

# 永久修改
hostnamectl set-hostname myserver-permanent
# 修改后，退出当前终端并重新登录，即可看到新主机名
```

![](img/bb98f2f4e611d4315da4296b70f5b85e_116.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_118.png)

---

![](img/bb98f2f4e611d4315da4296b70f5b85e_120.png)

![](img/bb98f2f4e611d4315da4296b70f5b85e_122.png)

本节课中我们一起学习了如何查看Linux系统的内核、CPU、内存、网卡等核心硬件与配置信息，并掌握了永久修改主机名的方法。这些命令是日常系统管理和监控的基础，请务必熟练掌握。下一节，我们将开始学习强大的文本编辑器——Vim。