# RHCE 8.0 课程：P31：系统引导故障修复实战 🛠️

![](img/bd9a65fb15ca98901f2773a185319675_1.png)

![](img/bd9a65fb15ca98901f2773a185319675_3.png)

![](img/bd9a65fb15ca98901f2773a185319675_5.png)

![](img/bd9a65fb15ca98901f2773a185319675_7.png)

在本节课中，我们将学习如何解决几种常见的系统引导故障。主要内容包括：`/boot`目录文件丢失、`/etc/fstab`挂载信息丢失以及`/etc/passwd`密码文件丢失的修复方法。我们将通过模拟故障场景，一步步演示如何从救援模式恢复系统。

![](img/bd9a65fb15ca98901f2773a185319675_9.png)

![](img/bd9a65fb15ca98901f2773a185319675_11.png)

![](img/bd9a65fb15ca98901f2773a185319675_13.png)

![](img/bd9a65fb15ca98901f2773a185319675_15.png)

![](img/bd9a65fb15ca98901f2773a185319675_17.png)

---

![](img/bd9a65fb15ca98901f2773a185319675_19.png)

上一节我们介绍了GRUB引导程序的修复与加密，本节中我们来看看当系统核心引导文件丢失时该如何处理。

![](img/bd9a65fb15ca98901f2773a185319675_21.png)

![](img/bd9a65fb15ca98901f2773a185319675_23.png)

![](img/bd9a65fb15ca98901f2773a185319675_25.png)

![](img/bd9a65fb15ca98901f2773a185319675_27.png)

## `/boot`目录文件丢失的修复 🚫

![](img/bd9a65fb15ca98901f2773a185319675_29.png)

![](img/bd9a65fb15ca98901f2773a185319675_31.png)

![](img/bd9a65fb15ca98901f2773a185319675_33.png)

![](img/bd9a65fb15ca98901f2773a185319675_35.png)

![](img/bd9a65fb15ca98901f2773a185319675_37.png)

![](img/bd9a65fb15ca98901f2773a185319675_39.png)

`/boot`目录存放着系统启动所必需的内核和引导文件。如果该目录下的文件被误删，系统将无法正常启动。其修复思路是：进入救援模式，重新安装内核软件包以生成丢失的文件，并重新配置GRUB。

![](img/bd9a65fb15ca98901f2773a185319675_40.png)

![](img/bd9a65fb15ca98901f2773a185319675_41.png)

![](img/bd9a65fb15ca98901f2773a185319675_43.png)

![](img/bd9a65fb15ca98901f2773a185319675_45.png)

以下是修复步骤：

![](img/bd9a65fb15ca98901f2773a185319675_47.png)

![](img/bd9a65fb15ca98901f2773a185319675_49.png)

![](img/bd9a65fb15ca98901f2773a185319675_51.png)

![](img/bd9a65fb15ca98901f2773a185319675_53.png)

1.  **进入救援模式**：从安装光盘启动，选择“Troubleshooting”，然后选择“Rescue a Red Hat Enterprise Linux system”。在提示是否挂载系统时，选择“Continue”。
2.  **切换根环境**：在救援模式shell中，执行 `chroot /mnt/sysimage` 切换到硬盘上的系统根目录。
3.  **挂载光盘**：救援环境可能未自动挂载光盘，需要手动挂载以获取安装包。
    ```bash
    mkdir /iso
    mount /dev/sr0 /iso
    ```
4.  **查询并重新安装内核**：在另一台正常机器上查询`/boot`目录下核心文件（如`vmlinuz`）由哪个软件包提供，然后在故障机上强制重新安装该包。
    ```bash
    # 在正常机器上查询
    rpm -qf /boot/vmlinuz-4.18.0-*.x86_64
    # 输出示例：kernel-core-4.18.0-*.x86_64

    # 在故障机救援环境中安装（需先挂载光盘）
    rpm -ivh /iso/BaseOS/Packages/kernel-core-*.rpm --force
    ```
5.  **重新生成GRUB配置**：内核安装后，需要重新生成GRUB配置文件并安装到引导磁盘。
    ```bash
    # 确保/boot/grub2目录存在
    mkdir -p /boot/grub2
    # 生成GRUB配置文件
    grub2-mkconfig -o /boot/grub2/grub.cfg
    # 安装GRUB到引导设备（例如/dev/nvme0n1）
    grub2-install /dev/nvme0n1
    ```
6.  **退出并重启**：执行两次 `exit` 命令退出chroot环境并重启救援模式，然后重启计算机。

![](img/bd9a65fb15ca98901f2773a185319675_55.png)

![](img/bd9a65fb15ca98901f2773a185319675_57.png)

![](img/bd9a65fb15ca98901f2773a185319675_59.png)

![](img/bd9a65fb15ca98901f2773a185319675_61.png)

---

![](img/bd9a65fb15ca98901f2773a185319675_63.png)

![](img/bd9a65fb15ca98901f2773a185319675_65.png)

![](img/bd9a65fb15ca98901f2773a185319675_67.png)

![](img/bd9a65fb15ca98901f2773a185319675_69.png)

解决了`/boot`文件丢失的问题后，我们来看另一种常见故障：系统挂载配置文件出错。

![](img/bd9a65fb15ca98901f2773a185319675_71.png)

![](img/bd9a65fb15ca98901f2773a185319675_73.png)

![](img/bd9a65fb15ca98901f2773a185319675_75.png)

## `/etc/fstab`文件丢失或错误的修复 🔧

![](img/bd9a65fb15ca98901f2773a185319675_77.png)

![](img/bd9a65fb15ca98901f2773a185319675_79.png)

![](img/bd9a65fb15ca98901f2773a185319675_81.png)

![](img/bd9a65fb15ca98901f2773a185319675_83.png)

![](img/bd9a65fb15ca98901f2773a185319675_85.png)

![](img/bd9a65fb15ca98901f2773a185319675_87.png)

`/etc/fstab`文件定义了系统启动时的自动挂载信息。如果此文件配置错误或对应的磁盘标识符（如UUID）发生变化，系统可能无法挂载关键分区（如根目录`/`）而启动失败。修复方法是进入救援模式，手动修正`/etc/fstab`文件。

![](img/bd9a65fb15ca98901f2773a185319675_89.png)

![](img/bd9a65fb15ca98901f2773a185319675_91.png)

![](img/bd9a65fb15ca98901f2773a185319675_93.png)

以下是模拟和修复的步骤：

![](img/bd9a65fb15ca98901f2773a185319675_95.png)

![](img/bd9a65fb15ca98901f2773a185319675_97.png)

![](img/bd9a65fb15ca98901f2773a185319675_99.png)

1.  **模拟故障**：添加新硬盘并分区格式化，将其挂载到`/aa`目录，写入`/etc/fstab`。然后使用`xfs_admin`命令更改该分区的UUID，导致重启后因UUID不匹配而挂载失败。
    ```bash
    # 对新分区/dev/sda1操作
    mkfs.xfs /dev/sda1
    blkid /dev/sda1 # 记录原UUID
    echo "UUID=原UUID /aa xfs defaults 0 0" >> /etc/fstab
    # 更改UUID
    uuidgen > /tmp/new_uuid
    xfs_admin -U `cat /tmp/new_uuid` /dev/sda1
    reboot
    ```
2.  **进入救援模式**：系统重启后因挂载失败可能进入紧急模式。同样需要从光盘启动进入救援模式。
3.  **修正`/etc/fstab`**：切换根环境后，查看当前分区的实际UUID，并更新`/etc/fstab`文件。
    ```bash
    chroot /mnt/sysimage
    blkid /dev/sda1 # 查看新的实际UUID
    vi /etc/fstab
    # 将文件中对应/dev/sda1行的UUID值修改为新的实际UUID
    ```
4.  **退出并重启**：修正后退出chroot并重启系统。

![](img/bd9a65fb15ca98901f2773a185319675_101.png)

![](img/bd9a65fb15ca98901f2773a185319675_103.png)

![](img/bd9a65fb15ca98901f2773a185319675_105.png)

![](img/bd9a65fb15ca98901f2773a185319675_107.png)

![](img/bd9a65fb15ca98901f2773a185319675_109.png)

---

![](img/bd9a65fb15ca98901f2773a185319675_111.png)

![](img/bd9a65fb15ca98901f2773a185319675_112.png)

![](img/bd9a65fb15ca98901f2773a185319675_114.png)

![](img/bd9a65fb15ca98901f2773a185319675_116.png)

最后，我们探讨一个影响用户登录的系统核心文件故障。

![](img/bd9a65fb15ca98901f2773a185319675_118.png)

![](img/bd9a65fb15ca98901f2773a185319675_120.png)

![](img/bd9a65fb15ca98901f2773a185319675_122.png)

![](img/bd9a65fb15ca98901f2773a185319675_124.png)

![](img/bd9a65fb15ca98901f2773a185319675_125.png)

## `/etc/passwd`文件丢失的修复 🔐

![](img/bd9a65fb15ca98901f2773a185319675_127.png)

![](img/bd9a65fb15ca98901f2773a185319675_129.png)

`/etc/passwd`文件存储了用户账户的基本信息。如果此文件丢失，所有用户（包括root）将无法登录系统。修复方法是进入救援模式，从备份或模板中恢复该文件。

![](img/bd9a65fb15ca98901f2773a185319675_131.png)

![](img/bd9a65fb15ca98901f2773a185319675_133.png)

![](img/bd9a65fb15ca98901f2773a185319675_135.png)

![](img/bd9a65fb15ca98901f2773a185319675_137.png)

![](img/bd9a65fb15ca98901f2773a185319675_139.png)

![](img/bd9a65fb15ca98901f2773a185319675_141.png)

修复步骤如下：

![](img/bd9a65fb15ca98901f2773a185319675_143.png)

![](img/bd9a65fb15ca98901f2773a185319675_145.png)

![](img/bd9a65fb15ca98901f2773a185319675_146.png)

![](img/bd9a65fb15ca98901f2773a185319675_148.png)

![](img/bd9a65fb15ca98901f2773a185319675_150.png)

1.  **进入救援模式**：同样从光盘启动进入救援环境。
2.  **检查并恢复文件**：切换根环境后，检查`/etc/passwd`文件是否丢失。系统通常会有备份（如`/etc/passwd-`）或可以通过基础软件包提取。
    ```bash
    chroot /mnt/sysimage
    ls -l /etc/passwd*
    # 如果存在/etc/passwd-备份，直接复制恢复
    cp /etc/passwd- /etc/passwd
    # 如果没有备份，可以从‘setup’或‘filesystem’包中提取
    # 首先挂载光盘，然后查询文件所属包
    rpm -qf /etc/passwd # 查询所属包，例如filesystem-*
    # 强制重新安装该包以恢复文件（注意：这可能会覆盖其他已修改的配置文件）
    rpm -ivh /iso/BaseOS/Packages/filesystem-*.rpm --force
    ```
3.  **验证并重启**：恢复文件后，检查其内容是否正常，然后退出重启。

![](img/bd9a65fb15ca98901f2773a185319675_152.png)

---

![](img/bd9a65fb15ca98901f2773a185319675_154.png)

![](img/bd9a65fb15ca98901f2773a185319675_156.png)

![](img/bd9a65fb15ca98901f2773a185319675_158.png)

![](img/bd9a65fb15ca98901f2773a185319675_160.png)

![](img/bd9a65fb15ca98901f2773a185319675_161.png)

![](img/bd9a65fb15ca98901f2773a185319675_163.png)

本节课中我们一起学习了三种关键的系统故障修复方法：`/boot`引导文件丢失、`/etc/fstab`挂载配置错误以及`/etc/passwd`用户文件丢失。掌握这些从救援模式进行修复的技能，是系统管理员进行故障排查和系统恢复的重要基础。请务必在实验环境中亲自操作，以加深理解。