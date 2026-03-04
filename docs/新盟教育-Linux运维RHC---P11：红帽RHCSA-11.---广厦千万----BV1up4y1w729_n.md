# Linux运维基础：P11：查看系统信息与修改主机名 🔧

![](img/5178536214f5d0224b61c81d820df1ef_1.png)

在本节课中，我们将学习如何查看Linux系统中的核心硬件与配置信息，包括内核版本、CPU、内存、网卡以及主机名。同时，我们也会掌握如何临时或永久地修改主机名。这些是系统管理和故障排查的基础技能。

![](img/5178536214f5d0224b61c81d820df1ef_3.png)

![](img/5178536214f5d0224b61c81d820df1ef_5.png)

![](img/5178536214f5d0224b61c81d820df1ef_7.png)

## 查看内核信息 🖥️

![](img/5178536214f5d0224b61c81d820df1ef_9.png)

上一节我们介绍了系统的基本概念，本节中我们来看看如何查看系统的内核信息。内核是Linux操作系统的核心。

![](img/5178536214f5d0224b61c81d820df1ef_11.png)

使用 `uname` 命令可以查看内核相关信息。如果直接执行该命令，默认只显示内核名称。

```bash
uname
```

![](img/5178536214f5d0224b61c81d820df1ef_13.png)

![](img/5178536214f5d0224b61c81d820df1ef_15.png)

若想查看内核版本，需要添加 `-r` 选项。

![](img/5178536214f5d0224b61c81d820df1ef_17.png)

![](img/5178536214f5d0224b61c81d820df1ef_18.png)

![](img/5178536214f5d0224b61c81d820df1ef_19.png)

![](img/5178536214f5d0224b61c81d820df1ef_21.png)

```bash
uname -r
```

![](img/5178536214f5d0224b61c81d820df1ef_23.png)

![](img/5178536214f5d0224b61c81d820df1ef_25.png)

![](img/5178536214f5d0224b61c81d820df1ef_27.png)

![](img/5178536214f5d0224b61c81d820df1ef_29.png)

要查看完整的系统信息，包括内核名称和版本，可以结合 `-s`（系统名称）和 `-r`（内核版本）选项。

![](img/5178536214f5d0224b61c81d820df1ef_31.png)

```bash
uname -sr
```

![](img/5178536214f5d0224b61c81d820df1ef_33.png)

![](img/5178536214f5d0224b61c81d820df1ef_35.png)

![](img/5178536214f5d0224b61c81d820df1ef_37.png)

这条命令会输出类似 `Linux 3.10.0` 的信息，其中包含了内核名称、主版本号、次版本号、修订版本号以及适用的CPU架构（如x86_64）。

![](img/5178536214f5d0224b61c81d820df1ef_38.png)

![](img/5178536214f5d0224b61c81d820df1ef_40.png)

![](img/5178536214f5d0224b61c81d820df1ef_41.png)

![](img/5178536214f5d0224b61c81d820df1ef_43.png)

## 查看CPU信息 💻

![](img/5178536214f5d0224b61c81d820df1ef_45.png)

了解了内核信息后，我们来看看如何查看CPU的详细信息。通常有两种方法。

![](img/5178536214f5d0224b61c81d820df1ef_47.png)

第一种方法是查看 `/proc/cpuinfo` 文件，该文件存储了CPU的详细信息。

![](img/5178536214f5d0224b61c81d820df1ef_49.png)

![](img/5178536214f5d0224b61c81d820df1ef_50.png)

```bash
cat /proc/cpuinfo
```

![](img/5178536214f5d0224b61c81d820df1ef_52.png)

![](img/5178536214f5d0224b61c81d820df1ef_53.png)

![](img/5178536214f5d0224b61c81d820df1ef_55.png)

![](img/5178536214f5d0224b61c81d820df1ef_57.png)

该文件内容较多，初学者可以关注以下几个关键字段：
*   `processor`：逻辑处理器的编号，可以理解为CPU的核心数（从0开始计数）。
*   `vendor_id`：CPU制造商，如 `GenuineIntel` 代表英特尔。
*   `model name`：CPU型号名称。
*   `cpu MHz`：CPU主频。

第二种方法是使用 `lscpu` 命令，它能以更清晰的结构化方式显示CPU信息。

```bash
lscpu
```

这条命令会显示CPU架构、核心数、线程数、型号等关键信息。对于服务器管理，了解核心数（`CPU(s)`）和架构（`Architecture`）尤为重要。

## 查看内存信息 🧠

接下来，我们学习如何查看系统的内存使用情况。与CPU类似，也有两种常用方法。

第一种方法是查看 `/proc/meminfo` 文件。

![](img/5178536214f5d0224b61c81d820df1ef_59.png)

![](img/5178536214f5d0224b61c81d820df1ef_61.png)

```bash
cat /proc/meminfo
```

![](img/5178536214f5d0224b61c81d820df1ef_63.png)

![](img/5178536214f5d0224b61c81d820df1ef_65.png)

该文件信息详尽，但我们最关心的是总内存和可用内存，通常查看前两行即可。

![](img/5178536214f5d0224b61c81d820df1ef_66.png)

![](img/5178536214f5d0224b61c81d820df1ef_68.png)

更常用且直观的命令是 `free`。直接使用 `free` 命令会以字节为单位显示，可读性较差。

![](img/5178536214f5d0224b61c81d820df1ef_69.png)

```bash
free
```

因此，我们通常加上 `-h` 选项，以人类易读的单位（K, M, G）显示。

```bash
free -h
```

以下是 `free -h` 命令输出中需要关注的部分：
*   `Mem` 行代表物理内存。
    *   `total`：内存总量。
    *   `used`：已使用内存。
    *   `free`：空闲内存。这是我们最需要关注的指标，它表示当前可用的内存量。
*   `Swap` 行代表交换分区（虚拟内存）。当物理内存不足时，系统会使用磁盘空间来模拟内存。但这会严重影响性能，因此在生产环境中通常建议关闭或仅做备用。

临时关闭Swap功能的命令如下：

![](img/5178536214f5d0224b61c81d820df1ef_71.png)

![](img/5178536214f5d0224b61c81d820df1ef_73.png)

```bash
swapoff -a
```

![](img/5178536214f5d0224b61c81d820df1ef_75.png)

执行后，使用 `free -h` 查看，`Swap` 行的值会变为0。请注意，这是临时关闭，重启系统后会恢复。永久关闭需要修改系统配置文件，我们将在后续课程中学习。

![](img/5178536214f5d0224b61c81d820df1ef_77.png)

![](img/5178536214f5d0224b61c81d820df1ef_79.png)

## 查看网卡信息 🌐

现在，我们来看看如何查看和管理网络配置。网卡信息主要存储在配置文件中，也可以通过命令查看。

![](img/5178536214f5d0224b61c81d820df1ef_81.png)

![](img/5178536214f5d0224b61c81d820df1ef_83.png)

![](img/5178536214f5d0224b61c81d820df1ef_85.png)

![](img/5178536214f5d0224b61c81d820df1ef_87.png)

网卡的主要配置文件路径为 `/etc/sysconfig/network-scripts/ifcfg-<网卡名>`，例如 `ifcfg-ens32`。

![](img/5178536214f5d0224b61c81d820df1ef_89.png)

```bash
cat /etc/sysconfig/network-scripts/ifcfg-ens32
```

配置文件中需要关注的关键参数如下：
*   `TYPE`：网络类型，通常是 `Ethernet`（以太网）。
*   `BOOTPROTO`：获取IP地址的方式。`static` 或 `none` 代表静态IP，`dhcp` 代表动态获取。
*   `NAME` / `DEVICE`：网卡设备名称。
*   `ONBOOT`：是否在系统启动时激活网卡，必须为 `yes`。
*   `IPADDR`：IP地址。
*   `NETMASK`：子网掩码。
*   `GATEWAY`：网关地址。
*   `DNS1` / `DNS2`：DNS服务器地址。

![](img/5178536214f5d0224b61c81d820df1ef_91.png)

![](img/5178536214f5d0224b61c81d820df1ef_93.png)

除了查看文件，我们还可以使用 `ifconfig` 命令（如果系统未安装，可使用 `yum install net-tools -y` 安装）来查看更详细的实时网络状态。

![](img/5178536214f5d0224b61c81d820df1ef_94.png)

```bash
ifconfig
```

该命令会显示所有网卡信息。对于每块网卡（如 `ens32`），需要关注：
*   `inet`：IPv4地址。
*   `netmask`：子网掩码。
*   `ether`：MAC地址（硬件地址）。
*   `RX packets` / `TX packets`：接收/发送的数据包数量及流量，用于监控网络活动。
*   以 `RX errors` / `TX errors` 开头的行：接收/发送的错误数据包统计，正常情况下应为0。

此外，`ip addr` 或简写 `ip a` 命令可以快速查看IP地址信息。

![](img/5178536214f5d0224b61c81d820df1ef_96.png)

![](img/5178536214f5d0224b61c81d820df1ef_98.png)

```bash
ip a
```

## 查看与修改主机名 🏷️

![](img/5178536214f5d0224b61c81d820df1ef_100.png)

最后，我们来学习如何查看和修改系统的主机名。主机名是设备在网络中的标识。

![](img/5178536214f5d0224b61c81d820df1ef_102.png)

查看主机名有两种方式。一是查看配置文件 `/etc/hostname`。

![](img/5178536214f5d0224b61c81d820df1ef_104.png)

```bash
cat /etc/hostname
```

![](img/5178536214f5d0224b61c81d820df1ef_106.png)

二是直接使用 `hostname` 命令。

```bash
hostname
```

![](img/5178536214f5d0224b61c81d820df1ef_108.png)

![](img/5178536214f5d0224b61c81d820df1ef_110.png)

修改主机名也分临时和永久两种。
*   **临时修改**：使用 `hostname <新主机名>` 命令，立即生效，但重启系统后失效。
    ```bash
    hostname newhost
    ```
    修改后需要重新登录终端才能看到变化。
*   **永久修改**：使用 `hostnamectl set-hostname <新主机名>` 命令。此命令会同时修改配置文件并立即生效，且重启后依然有效。
    ```bash
    hostnamectl set-hostname newhost
    ```
    执行后，退出当前终端并重新登录即可看到新主机名。可以检查 `/etc/hostname` 文件确认修改已持久化。

---

![](img/5178536214f5d0224b61c81d820df1ef_112.png)

![](img/5178536214f5d0224b61c81d820df1ef_114.png)

本节课中我们一起学习了如何查看Linux系统的内核、CPU、内存、网卡等核心硬件信息，并掌握了查看及修改主机名的方法。这些命令和文件是日常系统管理和维护的基础，请务必熟练掌握。