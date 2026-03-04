# Linux运维进阶：P11：查看系统信息与修改主机名 🔧

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_1.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_3.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_5.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_7.png)

在本节课中，我们将学习如何查看Linux系统中的关键硬件和配置信息，包括内核版本、CPU、内存、网卡以及主机名，并掌握修改主机名的方法。这些是系统管理和故障排查的基础技能。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_9.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_11.png)

## 查看内核信息 🧠

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_13.png)

上一节我们介绍了系统的基本操作，本节中我们来看看如何查看系统的核心——内核信息。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_15.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_17.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_19.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_21.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_23.png)

使用 `uname` 命令可以查看内核的相关信息，包括版本和名称。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_25.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_27.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_29.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_31.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_33.png)

**命令格式：**
```bash
uname [选项]
```

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_35.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_37.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_39.png)

以下是常用选项及其作用：
*   `-s`：显示内核名称（这是默认选项）。
*   `-r`：显示内核版本。
*   结合使用 `-sr` 可以同时显示内核名称和版本。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_41.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_43.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_45.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_47.png)

执行 `uname -sr` 会输出类似 `Linux 3.10.0-xxx` 的信息，表明这是Linux内核，版本是3.10.0。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_49.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_51.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_53.png)

## 查看CPU信息 💻

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_55.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_57.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_59.png)

了解了内核信息后，我们来看看如何查看CPU的详细信息。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_61.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_63.png)

查看CPU信息通常有两种方法。

**方法一：查看 `/proc/cpuinfo` 文件**
这个文件存储了CPU的详细信息。可以使用 `cat /proc/cpuinfo` 命令查看。文件内容较多，我们主要关注以下几点：
*   `processor`：逻辑处理器的编号，可以大致反映CPU的核心数量。
*   `vendor_id`：CPU制造商，如 `GenuineIntel`。
*   `model name`：CPU型号和频率。

**方法二：使用 `lscpu` 命令**
这条命令能以更结构化的方式显示CPU信息，包括架构、核心数、线程数等。直接输入 `lscpu` 即可。

## 查看内存信息 🧠

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_65.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_67.png)

接下来，我们学习如何查看系统的内存使用情况。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_69.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_71.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_73.png)

查看内存信息也有两种常用方法。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_75.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_77.png)

**方法一：查看 `/proc/meminfo` 文件**
执行 `cat /proc/meminfo` 可以查看详细的内存信息。我们最关心的是前两行：`MemTotal`（总内存）和 `MemFree`（空闲内存）。

**方法二：使用 `free` 命令**
这是更常用的命令，用于查看内存和交换空间（swap）的使用情况。

**命令格式：**
```bash
free -h
```
选项 `-h` 表示以人类易读的单位（K, M, G）显示大小。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_79.png)

命令输出中，我们主要关注 `Mem`（内存）行：
*   `total`：物理内存总量。
*   `used`：已使用的内存量。
*   `free`：空闲的内存量。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_81.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_83.png)

**关于交换空间（Swap）**
Swap是当物理内存不足时，用磁盘空间来临时充当内存的机制。但它会降低系统性能，因此在内存充足的生产环境中常被关闭。
*   **临时关闭Swap**：`swapoff -a`
*   **临时开启Swap**：`swapon -a`

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_85.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_87.png)

## 查看网卡信息 🌐

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_89.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_91.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_93.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_95.png)

现在，我们来看看如何查看和管理网络接口卡（网卡）的信息。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_97.png)

**方法一：查看网卡配置文件**
网卡的配置信息保存在 `/etc/sysconfig/network-scripts/` 目录下，文件名通常为 `ifcfg-ens33` 或类似。使用 `cat` 命令查看该文件，需要关注以下配置项：
*   `BOOTPROTO`：获取IP地址的方式，`static`/`none` 表示静态IP，`dhcp` 表示动态获取。
*   `ONBOOT`：是否在系统启动时激活网卡，应为 `yes`。
*   `IPADDR`：IP地址。
*   `NETMASK`：子网掩码。
*   `GATEWAY`：网关地址。
*   `DNS1`：DNS服务器地址。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_99.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_101.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_102.png)

**方法二：使用 `ifconfig` 或 `ip` 命令**
`ifconfig` 命令可以显示所有网卡的详细信息，包括IP地址、MAC地址、接收/发送的数据包统计等。
如果系统没有此命令，可以使用 `yum -y install net-tools` 安装。

更现代的替代命令是 `ip`，例如 `ip addr show` 或简写为 `ip a`，它主要显示IP地址相关配置。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_104.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_106.png)

在 `ifconfig` 的输出中，`lo` 表示回环网卡（IP为127.0.0.1），用于本机网络测试；`ens33` 或 `eth0` 等则表示物理或虚拟网卡。

## 查看与修改主机名 🏷️

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_108.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_110.png)

最后，我们来学习如何查看和修改系统的主机名。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_112.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_114.png)

**查看主机名**
有两种方式：
1.  查看配置文件：`cat /etc/hostname`
2.  使用命令：`hostname`

**修改主机名**
修改主机名也分为临时修改和永久修改。
*   **临时修改**：使用 `hostname 新主机名` 命令。立即生效，但重启系统后会失效。
*   **永久修改**：使用 `hostnamectl set-hostname 新主机名` 命令。此命令会修改 `/etc/hostname` 文件，并立即生效（需要重新登录Shell以在提示符中看到变化），重启后依然有效。

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_116.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_118.png)

---

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_120.png)

![](img/0ea52045e3a0380b3486d4f8e4b43a8e_122.png)

本节课中我们一起学习了如何查看Linux系统的内核、CPU、内存和网卡信息，这是运维工作的基础。同时，我们也掌握了查看和永久修改系统主机名的方法。熟练掌握这些命令，将帮助你更好地了解和维护Linux服务器。