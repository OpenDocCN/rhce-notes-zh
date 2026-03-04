# RHCE 8.0 视频教程：P30：系统启动故障排除

![](img/17df006fde0547fa27d64b703b8615c5_0.png)

在本节课中，我们将学习当 Linux 系统在启动过程中出现故障时，如何进行诊断和修复。我们将涵盖五个常见的启动问题及其解决方案，包括忘记密码、引导文件损坏、/boot 目录被清空、硬件挂载丢失以及 /etc/passwd 文件被删除。课程内容将尽可能简单直白，以便初学者能够理解和掌握。

## 系统启动流程回顾

上一节我们介绍了脚本相关内容，本节中我们来看看系统启动故障的解决方法。首先，我们需要理解系统是如何启动的。

系统启动时，首先会读取硬盘主引导记录（MBR）中的引导程序。MBR 的前 446 字节存储着引导加载器（bootloader），例如 GRUB。这个引导程序是一个微型系统，它没有文件系统，直接被加载到内存中运行。它的作用是定位并加载内核文件。

内核文件（如 `/boot/vmlinuz-4.18.0`）存储在文件系统中。引导程序会读取分区表，定位到引导分区（通常是第一个分区），然后在该分区中找到 GRUB 的配置文件 `/boot/grub2/grub.cfg`。这个配置文件告诉引导程序内核文件的位置以及其他启动参数。

引导程序加载内核后，内核才能识别并挂载完整的文件系统，系统得以正常启动。这个过程解决了“先有鸡还是先有蛋”的问题：一个简单的引导程序先运行，然后由它来加载复杂的、能识别文件系统的内核。

## GRUB 配置与管理

上一节我们回顾了启动流程，本节中我们来看看如何管理和定制 GRUB 引导程序。

我们通常不直接编辑 `/boot/grub2/grub.cfg` 文件，因为系统更新内核时，此文件会被覆盖，导致自定义设置丢失。正确的方法是编辑 `/etc/grub.d/` 目录下的子配置文件，然后重新生成主配置文件。

![](img/17df006fde0547fa27d64b703b8615c5_2.png)

![](img/17df006fde0547fa27d64b703b8615c5_4.png)

以下是生成 GRUB 配置文件的命令：
```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
```

![](img/17df006fde0547fa27d64b703b8615c5_6.png)

![](img/17df006fde0547fa27d64b703b8615c5_8.png)

例如，要修改默认启动等待时间，可以编辑 `/etc/default/grub` 文件，修改 `GRUB_TIMEOUT` 参数，然后执行上述命令重新生成配置。

![](img/17df006fde0547fa27d64b703b8615c5_10.png)

![](img/17df006fde0547fa27d64b703b8615c5_12.png)

![](img/17df006fde0547fa27d64b703b8615c5_14.png)

![](img/17df006fde0547fa27d64b703b8615c5_16.png)

![](img/17df006fde0547fa27d64b703b8615c5_18.png)

## 为 GRUB 引导菜单加密

![](img/17df006fde0547fa27d64b703b8615c5_20.png)

![](img/17df006fde0547fa27d64b703b8615c5_22.png)

上一节我们学习了如何配置 GRUB，本节中我们来看看如何为 GRUB 引导菜单设置密码，防止未经授权的修改。

![](img/17df006fde0547fa27d64b703b8615c5_24.png)

![](img/17df006fde0547fa27d64b703b8615c5_26.png)

在系统启动时，任何人按下 `e` 键都可以编辑启动参数，这存在安全风险。为 GRUB 设置密码后，只有输入正确的凭据才能进行编辑。

![](img/17df006fde0547fa27d64b703b8615c5_28.png)

![](img/17df006fde0547fa27d64b703b8615c5_29.png)

![](img/17df006fde0547fa27d64b703b8615c5_31.png)

设置 GRUB 密码有两种方式：明文和密文。

![](img/17df006fde0547fa27d64b703b8615c5_33.png)

![](img/17df006fde0547fa27d64b703b8615c5_35.png)

**1. 明文方式：**
编辑 `/etc/grub.d/00_header` 文件，在末尾添加：
```
set superusers="tkedu"
password tkedu redhat8
```
然后执行 `grub2-mkconfig -o /boot/grub2/grub.cfg` 生成新配置。

**2. 密文方式（推荐）：**
首先生成加密密码：
```bash
grub2-mkpasswd-pbkdf2
```
输入密码（如 `root123`）后，会得到一串加密字符。编辑 `/etc/grub.d/00_header` 文件，添加：
```
set superusers="tkedu"
password_pbkdf2 tkedu <这里粘贴上一步生成的加密字符串>
```
最后重新生成 GRUB 配置。

设置完成后，重启系统，在 GRUB 菜单按 `e` 尝试编辑时，会提示输入用户名和密码。

![](img/17df006fde0547fa27d64b703b8615c5_37.png)

![](img/17df006fde0547fa27d64b703b8615c5_39.png)

![](img/17df006fde0547fa27d64b703b8615c5_41.png)

## 破解 root 用户密码

![](img/17df006fde0547fa27d64b703b8615c5_43.png)

![](img/17df006fde0547fa27d64b703b8615c5_45.png)

上一节我们为 GRUB 加了锁，本节中我们来看看如果忘记了 root 密码，如何利用 GRUB 解锁系统。

![](img/17df006fde0547fa27d64b703b8615c5_47.png)

![](img/17df006fde0547fa27d64b703b8615c5_49.png)

如果忘记了 root 密码，且 GRUB 没有设置密码保护，可以按照以下步骤重置密码：

![](img/17df006fde0547fa27d64b703b8615c5_51.png)

![](img/17df006fde0547fa27d64b703b8615c5_53.png)

![](img/17df006fde0547fa27d64b703b8615c5_55.png)

1.  重启系统，在 GRUB 引导菜单界面，选中要启动的条目（通常是第一个），按 `e` 键进入编辑模式。
2.  在 Linux 启动参数的行末（通常以 `linux16` 或 `linux` 开头），添加 `rd.break` 参数。注意这是一整行参数的一部分，直接追加在末尾即可。
3.  按 `Ctrl + x` 使用修改后的参数启动系统。
4.  系统会进入一个临时的 `switch_root` 提示符环境。此时根文件系统是以只读方式挂载的，需要重新挂载为读写权限：
    ```bash
    mount -o remount,rw /sysroot
    ```
5.  切换根目录到原来的系统环境：
    ```bash
    chroot /sysroot
    ```
6.  现在可以修改 root 密码了：
    ```bash
    echo “root123” | passwd --stdin root
    ```
7.  如果系统启用了 SELinux，需要创建自动重新标记的文件，否则重启后可能无法正常登录：
    ```bash
    touch /.autorelabel
    ```
8.  退出 `chroot` 环境并重启系统：
    ```bash
    exit
    exit
    ```
    第一个 `exit` 退出 `chroot`，第二个 `exit` 重启系统。

![](img/17df006fde0547fa27d64b703b8615c5_56.png)

![](img/17df006fde0547fa27d64b703b8615c5_58.png)

系统重启后，就可以使用新密码 `root123` 登录了。

![](img/17df006fde0547fa27d64b703b8615c5_60.png)

![](img/17df006fde0547fa27d64b703b8615c5_62.png)

![](img/17df006fde0547fa27d64b703b8615c5_64.png)

## 修复损坏的引导扇区

![](img/17df006fde0547fa27d64b703b8615c5_66.png)

![](img/17df006fde0547fa27d64b703b8615c5_68.png)

![](img/17df006fde0547fa27d64b703b8615c5_70.png)

上一节我们解决了密码问题，本节中我们来看看如果引导扇区（MBR）被破坏，该如何修复。

![](img/17df006fde0547fa27d64b703b8615c5_72.png)

模拟破坏引导扇区：
```bash
dd if=/dev/zero of=/dev/nvme0n1 bs=1 count=446
```
此命令会清空 MBR 的前 446 字节。

![](img/17df006fde0547fa27d64b703b8615c5_74.png)

![](img/17df006fde0547fa27d64b703b8615c5_76.png)

![](img/17df006fde0547fa27d64b703b8615c5_78.png)

![](img/17df006fde0547fa27d64b703b8615c5_80.png)

重启后，系统将无法找到引导程序，可能会直接进入安装界面。此时需要进入“救援模式”进行修复：

![](img/17df006fde0547fa27d64b703b8615c5_82.png)

![](img/17df006fde0547fa27d64b703b8615c5_84.png)

![](img/17df006fde0547fa27d64b703b8615c5_86.png)

![](img/17df006fde0547fa27d64b703b8615c5_88.png)

1.  在安装界面选择 `Troubleshooting`（排错）。
2.  选择 `Rescue a Red Hat Enterprise Linux system`（救援模式）。
3.  按照提示选择 `Continue`（继续）。
4.  救援环境会尝试挂载原系统。根据提示，执行 `chroot /mnt/sysimage` 切换到原系统的根环境。
5.  查看引导分区位置（通常是第一个分区）：
    ```bash
    fdisk -l /dev/nvme0n1
    ```
6.  重新安装 GRUB2 到引导设备（不是分区）：
    ```bash
    grub2-install /dev/nvme0n1
    ```
7.  退出 `chroot` 并重启：
    ```bash
    exit
    exit
    ```

![](img/17df006fde0547fa27d64b703b8615c5_90.png)

系统应该可以正常引导了。

![](img/17df006fde0547fa27d64b703b8615c5_92.png)

![](img/17df006fde0547fa27d64b703b8615c5_94.png)

## 修复被清空的 /boot 目录

![](img/17df006fde0547fa27d64b703b8615c5_96.png)

上一节我们修复了引导扇区，本节中我们来看看如果 `/boot` 目录下的内核和引导文件被误删，该如何恢复。

模拟删除 `/boot` 目录：
```bash
rm -rf /boot/*
```
重启后，GRUB 会因为找不到内核文件而启动失败，进入 `grub rescue>` 提示符。

![](img/17df006fde0547fa27d64b703b8615c5_98.png)

修复步骤比引导扇区损坏更复杂，需要从安装介质（光盘或USB）启动：

![](img/17df006fde0547fa27d64b703b8615c5_100.png)

1.  重启虚拟机，在 BIOS 启动阶段按 `Esc` 键（具体按键因虚拟机软件而异）进入启动菜单。
2.  选择从 `CD/DVD` 或安装镜像启动。
3.  进入安装界面后，再次选择 `Troubleshooting` -> `Rescue a Red Hat Enterprise Linux system`。
4.  按照提示操作，最终会进入救援模式的命令行。
5.  切换根目录到原系统：`chroot /mnt/sysimage`。
6.  此时，原系统的 `/boot` 目录是空的。我们需要重新安装内核包来恢复文件。首先挂载安装镜像作为软件源：
    ```bash
    mount /dev/sr0 /mnt
    ```
    （设备名可能是 `/dev/cdrom`，请用 `lsblk` 命令确认）
7.  配置一个临时 YUM 源指向挂载点，或者直接使用 `rpm` 命令安装内核包。找到镜像中内核 RPM 包的路径并安装，例如：
    ```bash
    rpm -ivh /mnt/BaseOS/Packages/kernel-*.rpm --force
    ```
8.  安装完成后，重新生成 GRUB 配置：
    ```bash
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
9.  退出 `chroot` 并重启。在 BIOS 中记得将启动顺序改回从硬盘启动。

![](img/17df006fde0547fa27d64b703b8615c5_102.png)

![](img/17df006fde0547fa27d64b703b8615c5_104.png)

![](img/17df006fde0547fa27d64b703b8615c5_106.png)

![](img/17df006fde0547fa27d64b703b8615c5_108.png)

系统将恢复正常。

![](img/17df006fde0547fa27d64b703b8615c5_110.png)

![](img/17df006fde0547fa27d64b703b8615c5_112.png)

![](img/17df006fde0547fa27d64b703b8615c5_113.png)

## 课程总结

本节课中我们一起学习了五种常见的 Linux 系统启动故障及其排除方法：
1.  **管理 GRUB**：通过编辑 `/etc/grub.d/` 下的文件并重新生成配置来定制启动项。
2.  **GRUB 加密**：为 GRUB 菜单设置密码，防止未经授权的编辑，提升系统安全性。
3.  **破解 root 密码**：在 GRUB 未加密的情况下，通过添加 `rd.break` 参数进入单用户模式重置密码。
4.  **修复引导扇区**：当 MBR 中的引导程序损坏时，通过救援模式使用 `grub2-install` 命令重新安装。
5.  **恢复 /boot 目录**：当 `/boot` 目录被清空时，从安装介质启动进入救援模式，重新安装内核包并重建 GRUB 配置。

![](img/17df006fde0547fa27d64b703b8615c5_115.png)

![](img/17df006fde0547fa27d64b703b8615c5_117.png)

![](img/17df006fde0547fa27d64b703b8615c5_119.png)

掌握这些故障排除技能，对于维护 Linux 系统的稳定运行至关重要。