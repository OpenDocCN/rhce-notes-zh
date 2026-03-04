# RHCE8.0 教程：P30：系统启动故障排除 🛠️

![](img/f4b3ae33bca3e3e365b8708310768fac_0.png)

在本节课中，我们将学习当 Linux 系统启动过程中出现故障时，如何进行诊断和修复。我们将探讨五种常见的启动问题及其解决方案，包括忘记密码、引导文件损坏、`/boot` 目录被清空、硬件挂载丢失以及 `/etc/passwd` 文件被删除。通过学习这些方法，你将能够应对多种系统启动失败的情况。

## 系统启动流程回顾 🔄

上一节我们介绍了脚本相关内容，本节中我们来看看系统启动故障的解决方法。首先，我们需要回顾一下 Linux 系统的启动流程，这对于理解后续的故障排除至关重要。

计算机开机后，首先会读取硬盘主引导记录（MBR）或 GUID 分区表（GPT）中的引导程序。对于传统的 MBR 格式，其结构如下：

*   **前 446 字节**：存储引导加载程序（Bootloader），例如 GRUB。
*   **中间 64 字节**：存储分区表信息。
*   **最后 2 字节**：结束标志（0x55AA）。

引导程序（如 GRUB）被加载到内存后，会读取其配置文件（如 `/boot/grub2/grub.cfg`），定位到内核文件（如 `vmlinuz-xxx`）和初始内存盘（`initramfs`），进而启动 Linux 内核。内核启动后，才能识别并挂载根文件系统，最终完成系统启动。

这里存在一个“先有鸡还是先有蛋”的问题：内核文件存储在磁盘上，但需要内核运行后才能识别磁盘文件系统。解决这个问题的关键就是 **引导加载程序**，它是一个独立的小程序，不依赖于复杂的文件系统，可以直接从磁盘的特定位置被读取并执行，从而为加载真正的内核铺平道路。

## GRUB 引导程序配置与加密 🔐

![](img/f4b3ae33bca3e3e365b8708310768fac_2.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_4.png)

GRUB 是 Linux 系统常用的引导加载程序。其主配置文件 `/boot/grub2/grub.cfg` 通常由系统自动生成，不建议直接编辑，因为内核更新后该文件会被覆盖。我们应该修改 `/etc/default/grub` 或 `/etc/grub.d/` 目录下的子配置文件，然后使用命令重新生成主配置。

以下是修改 GRUB 配置的常用命令：
```bash
# 重新生成 grub.cfg 配置文件
grub2-mkconfig -o /boot/grub2/grub.cfg
```

![](img/f4b3ae33bca3e3e365b8708310768fac_6.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_8.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_10.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_12.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_14.png)

### 为 GRUB 菜单设置密码

![](img/f4b3ae33bca3e3e365b8708310768fac_16.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_18.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_20.png)

为了防止非授权用户在启动时修改 GRUB 条目（例如，按 `e` 键进入编辑模式），我们可以为 GRUB 设置密码。密码可以以明文或密文形式存储。

![](img/f4b3ae33bca3e3e365b8708310768fac_22.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_24.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_26.png)

**1. 明文方式（不推荐）**
编辑 `/etc/grub.d/00_header` 文件，在末尾添加：
```bash
set superusers="tkedu"
password tkedu redhat8
```
然后执行 `grub2-mkconfig -o /boot/grub2/grub.cfg` 重新生成配置。

![](img/f4b3ae33bca3e3e365b8708310768fac_28.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_29.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_31.png)

**2. 密文方式（推荐）**
首先生成加密密码：
```bash
grub2-mkpasswd-pbkdf2
# 输入密码后，会生成一串加密字符串，复制它。
```
编辑 `/etc/grub.d/00_header` 文件，在末尾添加：
```bash
set superusers="tkedu"
password_pbkdf2 tkedu <这里粘贴刚才复制的加密字符串>
```
最后重新生成 GRUB 配置。

![](img/f4b3ae33bca3e3e365b8708310768fac_33.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_35.png)

设置后，在启动界面尝试编辑 GRUB 条目时，就需要输入用户名和密码了。

## 故障排除实战：忘记 root 密码 🔑

![](img/f4b3ae33bca3e3e365b8708310768fac_37.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_39.png)

这是最常见的启动问题之一。如果忘记了 root 密码，可以通过以下步骤在单用户模式下重置。

![](img/f4b3ae33bca3e3e365b8708310768fac_41.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_43.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_45.png)

**操作步骤如下：**

![](img/f4b3ae33bca3e3e365b8708310768fac_47.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_49.png)

1.  **重启系统**，在 GRUB 启动菜单界面，选中要启动的条目（通常是第一个），然后按 `e` 键进入编辑模式。
2.  在找到 `linux` 或 `linux16` 开头的那一行，在行末添加 `rd.break` 参数。注意，这是一整行参数的一部分，直接追加在最后即可。
3.  按 `Ctrl + X` 使用修改后的配置启动系统。
4.  系统会启动到一个临时的 `switch_root` 提示符下。此时根文件系统是以只读（`ro`）方式挂载的。
5.  重新以读写方式挂载根文件系统，并切换根环境：
    ```bash
    mount -o remount,rw /sysroot
    chroot /sysroot
    ```
6.  现在可以修改 root 密码了：
    ```bash
    echo “root123” | passwd --stdin root
    ```
7.  如果系统启用了 SELinux，需要创建 `.autorelabel` 文件，以便在下次启动时重新标记文件上下文：
    ```bash
    touch /.autorelabel
    ```
8.  退出 `chroot` 环境并重启系统：
    ```bash
    exit # 第一次退出 chroot
    exit # 第二次退出 switch_root 环境，系统会自动重启
    ```
9.  重启后，即可使用新密码 `root123` 登录。

![](img/f4b3ae33bca3e3e365b8708310768fac_51.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_53.png)

> **注意**：如果 GRUB 设置了密码，则需要先输入 GRUB 密码才能进行第 1 步。因此，GRUB 密码是系统安全的第一道防线。

![](img/f4b3ae33bca3e3e365b8708310768fac_55.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_57.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_59.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_61.png)

## 故障排除实战：引导文件损坏 🚫

![](img/f4b3ae33bca3e3e365b8708310768fac_62.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_64.png)

如果 MBR 的前 446 字节（引导程序）被破坏，系统将无法启动，可能会直接进入安装界面或显示黑屏错误。

![](img/f4b3ae33bca3e3e365b8708310768fac_66.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_68.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_70.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_72.png)

**模拟破坏引导程序：**
```bash
dd if=/dev/zero of=/dev/nvme0n1 bs=1 count=446
```

![](img/f4b3ae33bca3e3e365b8708310768fac_74.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_76.png)

**修复步骤如下：**

![](img/f4b3ae33bca3e3e365b8708310768fac_78.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_80.png)

1.  重启系统，从安装光盘或 USB 启动介质启动。
2.  在安装界面选择 **`Troubleshooting`** （故障排除）。
3.  选择 **`Rescue a Red Hat Enterprise Linux system`** （救援模式）。
4.  按照提示选择语言、键盘布局后，救援程序会尝试查找并挂载现有的 Linux 系统。选择 **`Continue`** （继续）。
5.  程序会提示系统已被挂载在 `/mnt/sysimage` 下，并给出一个 `chroot` 命令示例。
6.  执行 `chroot /mnt/sysimage` 切换到原系统的根环境。
7.  确认引导分区位置（通常是 `/dev/nvme0n1` 或 `/dev/sda`，不包含分区号）。
8.  重新安装 GRUB 到磁盘：
    ```bash
    grub2-install /dev/nvme0n1
    ```
9.  退出 `chroot` 环境并重启：
    ```bash
    exit
    reboot
    ```
10. 在 BIOS/UEFI 设置中将启动顺序改回从硬盘启动。

![](img/f4b3ae33bca3e3e365b8708310768fac_82.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_84.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_86.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_88.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_90.png)

## 故障排除实战：/boot 目录被清空 📂

![](img/f4b3ae33bca3e3e365b8708310768fac_92.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_94.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_96.png)

`/boot` 目录存放着内核和 GRUB 配置文件等重要启动文件。如果此目录被清空，GRUB 将无法找到内核，启动过程会卡在 GRUB 提示符（`grub>`）或类似界面。

![](img/f4b3ae33bca3e3e365b8708310768fac_98.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_100.png)

**修复思路**：需要从救援模式挂载系统，然后从安装介质或备份中恢复 `/boot` 目录下的文件。最直接的方法是重新安装内核包，因为安装内核包会自动在 `/boot` 目录生成所需文件。

![](img/f4b3ae33bca3e3e365b8708310768fac_102.png)

**修复步骤如下：**

1.  如同上一节，从安装介质启动并进入 **救援模式**。
2.  切换根环境到原系统：`chroot /mnt/sysimage`。
3.  挂载安装介质（例如光盘）到某个目录，如 `/mnt/cdrom`。
4.  将安装介质配置为本地 YUM 仓库，或直接使用 `rpm` 命令安装内核包。你需要知道内核包的确切名称，例如 `kernel-core-4.18.0-xxx`。
    ```bash
    # 假设安装介质挂载在 /mnt/cdrom，Packages目录存放rpm包
    rpm -ivh /mnt/cdrom/Packages/kernel-core-*.rpm --force
    ```
    `--force` 参数强制重新安装。
5.  安装内核后，`/boot` 目录下会生成新的内核文件和 `initramfs`。接着需要重新生成 GRUB 配置：
    ```bash
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
6.  如果 `/boot` 是一个独立分区，还需要检查 `/etc/fstab` 中的挂载信息是否正确。
7.  退出 `chroot` 并重启。

> **提示**：在实际操作中，如果系统有 `yum/dnf` 可用，且能访问网络或本地仓库，直接使用 `yum reinstall kernel-core` 会更方便。

![](img/f4b3ae33bca3e3e365b8708310768fac_104.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_106.png)

## 其他故障简介 ⚠️

![](img/f4b3ae33bca3e3e365b8708310768fac_108.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_110.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_112.png)

除了上述三种情况，还有两种常见的启动故障：

![](img/f4b3ae33bca3e3e365b8708310768fac_114.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_116.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_118.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_119.png)

*   **`/etc/passwd` 文件被删除**：此文件包含用户账户信息。如果被删，所有用户（包括 root）将无法登录。修复方法是从救援模式启动，挂载原系统，然后从备份恢复该文件，或者手动创建一个包含 `root` 用户基本信息的 `/etc/passwd` 文件。
*   **挂载点丢失（如 `/home` 独立分区被移除）**：如果 `/etc/fstab` 中配置的某个挂载点对应的设备不存在，系统启动可能会失败或进入紧急模式。修复方法是进入救援模式或单用户模式，编辑 `/etc/fstab` 文件，注释掉或更正有问题的挂载行，然后重启。

---

![](img/f4b3ae33bca3e3e365b8708310768fac_121.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_123.png)

![](img/f4b3ae33bca3e3e365b8708310768fac_125.png)

本节课中我们一起学习了五种常见的 Linux 系统启动故障及其排除方法：1) 通过单用户模式重置遗忘的 root 密码；2) 使用救援模式修复损坏的 MBR 引导程序；3) 在 `/boot` 目录被清空后，从安装介质重新安装内核并重建 GRUB 配置；4) 了解了 `/etc/passwd` 文件丢失和挂载点故障的基本处理思路。掌握这些技能，你就能在面对系统无法启动的紧急情况时，从容地进行修复，恢复系统的正常运行。