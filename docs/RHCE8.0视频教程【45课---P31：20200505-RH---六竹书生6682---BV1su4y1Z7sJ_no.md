# RHCE8.0视频教程：P31：系统引导故障修复实战

![](img/a7875718a847c17b843049d0e862ca5a_1.png)

![](img/a7875718a847c17b843049d0e862ca5a_3.png)

![](img/a7875718a847c17b843049d0e862ca5a_5.png)

![](img/a7875718a847c17b843049d0e862ca5a_7.png)

![](img/a7875718a847c17b843049d0e862ca5a_9.png)

![](img/a7875718a847c17b843049d0e862ca5a_11.png)

![](img/a7875718a847c17b843049d0e862ca5a_13.png)

在本节课中，我们将学习如何处理几种常见的系统引导故障。我们将探讨当`/boot`目录文件丢失、挂载信息丢失或关键验证文件丢失时，应如何从救援模式进行修复。这些技能对于系统管理员至关重要。

![](img/a7875718a847c17b843049d0e862ca5a_15.png)

![](img/a7875718a847c17b843049d0e862ca5a_17.png)

上一节我们介绍了GRUB引导程序的修复与加密，本节中我们来看看其他几种系统启动相关的故障场景。

![](img/a7875718a847c17b843049d0e862ca5a_19.png)

![](img/a7875718a847c17b843049d0e862ca5a_21.png)

![](img/a7875718a847c17b843049d0e862ca5a_23.png)

![](img/a7875718a847c17b843049d0e862ca5a_25.png)

![](img/a7875718a847c17b843049d0e862ca5a_27.png)

## 🗂️ 场景一：/boot目录文件丢失

![](img/a7875718a847c17b843049d0e862ca5a_29.png)

![](img/a7875718a847c17b843049d0e862ca5a_31.png)

![](img/a7875718a847c17b843049d0e862ca5a_33.png)

![](img/a7875718a847c17b843049d0e862ca5a_35.png)

![](img/a7875718a847c17b843049d0e862ca5a_37.png)

![](img/a7875718a847c17b843049d0e862ca5a_39.png)

![](img/a7875718a847c17b843049d0e862ca5a_40.png)

`/boot`目录存放着系统启动所需的内核和引导文件。如果该目录下的文件被误删，系统将无法正常启动。这类似于Windows系统中删除了`C:\Windows\System32`下的关键文件。

![](img/a7875718a847c17b843049d0e862ca5a_41.png)

![](img/a7875718a847c17b843049d0e862ca5a_43.png)

![](img/a7875718a847c17b843049d0e862ca5a_45.png)

![](img/a7875718a847c17b843049d0e862ca5a_47.png)

![](img/a7875718a847c17b843049d0e862ca5a_49.png)

以下是修复`/boot`目录文件丢失的步骤：

![](img/a7875718a847c17b843049d0e862ca5a_51.png)

![](img/a7875718a847c17b843049d0e862ca5a_53.png)

![](img/a7875718a847c17b843049d0e862ca5a_55.png)

![](img/a7875718a847c17b843049d0e862ca5a_57.png)

1.  **从安装介质启动**：重启服务器，从RHEL安装光盘或ISO镜像启动。
2.  **进入故障排除模式**：在安装界面选择 **`Troubleshooting`**。
3.  **选择救援选项**：接着选择 **`Rescue a Red Hat Enterprise Linux system`**。
4.  **继续并挂载系统**：在救援环境提示时，选择 **`1) Continue`** 以读写模式挂载现有系统。然后按回车将根环境切换到硬盘系统。
5.  **挂载光盘并重新安装内核**：救援模式下的`/boot`目录是空的。我们需要重新安装内核包来生成文件。首先挂载安装光盘，然后强制重新安装内核包。
    ```bash
    # 挂载光盘（假设为/dev/sr0）
    mkdir /mnt/cdrom
    mount /dev/sr0 /mnt/cdrom

    # 强制重新安装内核包（包名可能因版本而异，例如 kernel-core-4.18.0-xxx）
    rpm -ivh /mnt/cdrom/BaseOS/Packages/kernel-core-*.rpm --force
    ```
6.  **重新生成GRUB2配置**：内核安装后，需要重新生成GRUB2引导配置。
    ```bash
    # 确保/boot/grub2目录存在
    mkdir -p /boot/grub2
    # 生成GRUB2配置文件
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
7.  **将GRUB2安装到引导磁盘**：最后，将GRUB2引导程序写入磁盘的主引导记录（MBR）。
    ```bash
    # 首先确认系统引导磁盘（例如 /dev/nvme0n1 或 /dev/sda）
    # 使用 `fdisk -l` 查看标有 * 的启动分区
    grub2-install /dev/nvme0n1
    ```
8.  **退出并重启**：执行两次 `exit` 命令退出救援环境，系统将自动重启。重启后应能正常进入系统。

![](img/a7875718a847c17b843049d0e862ca5a_59.png)

![](img/a7875718a847c17b843049d0e862ca5a_61.png)

![](img/a7875718a847c17b843049d0e862ca5a_63.png)

![](img/a7875718a847c17b843049d0e862ca5a_65.png)

![](img/a7875718a847c17b843049d0e862ca5a_67.png)

![](img/a7875718a847c17b843049d0e862ca5a_69.png)

**关键技巧**：如果不知道`/boot`目录下文件由哪个软件包提供，可以在另一台正常机器上使用 `rpm -qf /boot/vmlinuz-*` 命令查询。

![](img/a7875718a847c17b843049d0e862ca5a_71.png)

![](img/a7875718a847c17b843049d0e862ca5a_73.png)

![](img/a7875718a847c17b843049d0e862ca5a_75.png)

## 🔗 场景二：/etc/fstab文件丢失或配置错误

![](img/a7875718a847c17b843049d0e862ca5a_77.png)

![](img/a7875718a847c17b843049d0e862ca5a_79.png)

![](img/a7875718a847c17b843049d0e862ca5a_81.png)

![](img/a7875718a847c17b843049d0e862ca5a_83.png)

![](img/a7875718a847c17b843049d0e862ca5a_85.png)

![](img/a7875718a847c17b843049d0e862ca5a_87.png)

![](img/a7875718a847c17b843049d0e862ca5a_89.png)

`/etc/fstab`文件定义了系统启动时的自动挂载信息。如果该文件丢失或其中条目（如UUID错误）配置有误，可能导致系统无法正常挂载分区而启动失败。

![](img/a7875718a847c17b843049d0e862ca5a_91.png)

![](img/a7875718a847c17b843049d0e862ca5a_93.png)

![](img/a7875718a847c17b843049d0e862ca5a_95.png)

![](img/a7875718a847c17b843049d0e862ca5a_97.png)

以下是模拟和修复`/etc/fstab`中UUID错误的方法：

![](img/a7875718a847c17b843049d0e862ca5a_99.png)

![](img/a7875718a847c17b843049d0e862ca5a_101.png)

![](img/a7875718a847c17b843049d0e862ca5a_103.png)

![](img/a7875718a847c17b843049d0e862ca5a_105.png)

![](img/a7875718a847c17b843049d0e862ca5a_107.png)

![](img/a7875718a847c17b843049d0e862ca5a_109.png)

1.  **模拟故障**：为一个新分区（如`/dev/sda1`）创建文件系统并挂载（例如挂载到`/aa`）。将其UUID记录到`/etc/fstab`中实现开机自动挂载。然后，使用`xfs_admin`命令更改该分区的UUID。
    ```bash
    # 格式化分区
    mkfs.xfs /dev/sda1
    # 获取初始UUID
    blkid /dev/sda1
    # 将挂载信息写入/etc/fstab，使用初始UUID
    echo "UUID=初始-UUID-here /aa xfs defaults 0 0" >> /etc/fstab
    # 更改分区的UUID
    xfs_admin -U $(uuidgen) /dev/sda1
    ```
2.  **触发故障**：重启系统。由于`/etc/fstab`中的旧UUID已失效，系统在启动挂载`/aa`时会失败，可能进入紧急救援模式。
3.  **进入救援模式修复**：同样从安装介质启动，进入救援模式并挂载根文件系统。
4.  **修正/etc/fstab**：在救援环境中，编辑硬盘系统上的`/etc/fstab`文件（路径可能是`/mnt/sysimage/etc/fstab`），将出错的UUID修正为当前正确的UUID（可通过`blkid /dev/sda1`查看）。
    ```bash
    # 在救援模式的chroot环境或挂载路径下操作
    vi /etc/fstab
    # 将错误的UUID行修改为正确的UUID
    ```
5.  **退出并重启**：修复完成后，退出救援环境并重启系统。

![](img/a7875718a847c17b843049d0e862ca5a_111.png)

![](img/a7875718a847c17b843049d0e862ca5a_112.png)

![](img/a7875718a847c17b843049d0e862ca5a_114.png)

## 🔐 场景三：/etc/passwd文件丢失

![](img/a7875718a847c17b843049d0e862ca5a_116.png)

![](img/a7875718a847c17b843049d0e862ca5a_118.png)

![](img/a7875718a847c17b843049d0e862ca5a_120.png)

![](img/a7875718a847c17b843049d0e862ca5a_122.png)

![](img/a7875718a847c17b843049d0e862ca5a_124.png)

![](img/a7875718a847c17b843049d0e862ca5a_125.png)

![](img/a7875718a847c17b843049d0e862ca5a_127.png)

`/etc/passwd`文件存储了用户账户的基本信息。该文件丢失将导致所有用户无法登录系统，包括root用户。

![](img/a7875718a847c17b843049d0e862ca5a_129.png)

修复思路是从救援模式恢复此文件。由于`/etc/passwd`文件内容相对固定，可以从其他正常系统复制一个基础版本，或从备份中恢复。在救援模式下，将正确的`/etc/passwd`文件复制到硬盘系统的`/etc/`目录下即可。

![](img/a7875718a847c17b843049d0e862ca5a_131.png)

![](img/a7875718a847c17b843049d0e862ca5a_133.png)

![](img/a7875718a847c17b843049d0e862ca5a_135.png)

![](img/a7875718a847c17b843049d0e862ca5a_137.png)

![](img/a7875718a847c17b843049d0e862ca5a_139.png)

![](img/a7875718a847c17b843049d0e862ca5a_141.png)

![](img/a7875718a847c17b843049d0e862ca5a_143.png)

![](img/a7875718a847c17b843049d0e862ca5a_145.png)

![](img/a7875718a847c17b843049d0e862ca5a_146.png)

**注意**：此操作需谨慎，错误的`passwd`文件可能导致用户无法登录。最好在操作前备份原文件（如果存在）。

![](img/a7875718a847c17b843049d0e862ca5a_148.png)

![](img/a7875718a847c17b843049d0e862ca5a_150.png)

![](img/a7875718a847c17b843049d0e862ca5a_152.png)

---

![](img/a7875718a847c17b843049d0e862ca5a_154.png)

![](img/a7875718a847c17b843049d0e862ca5a_156.png)

![](img/a7875718a847c17b843049d0e862ca5a_158.png)

![](img/a7875718a847c17b843049d0e862ca5a_160.png)

![](img/a7875718a847c17b843049d0e862ca5a_161.png)

![](img/a7875718a847c17b843049d0e862ca5a_163.png)

本节课中我们一起学习了三种系统引导故障的修复方法：`/boot`目录文件丢失、`/etc/fstab`挂载配置错误以及`/etc/passwd`文件丢失。核心修复流程都是**从安装介质启动，进入救援模式，挂载原系统并进行修复**。掌握这些救援技能，能够帮助你在实际工作中应对关键的启动问题，保障系统服务的连续性。