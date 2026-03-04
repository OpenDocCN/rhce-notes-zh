# 红帽RHCE7培训课程：P7：文件系统管理与无人值守安装

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_0.png)

## 概述
在本节课中，我们将学习Linux系统管理中的两个核心部分：文件系统挂载与搜索，以及无人值守安装的配置文件。我们将掌握如何使用`mount`和`find`命令，并理解Kickstart文件的结构与作用。

---

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_2.png)

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_4.png)

## 文件系统管理

上一节我们介绍了系统基础命令，本节中我们来看看如何管理文件系统，特别是挂载设备和查找文件。

### 查看存储设备与空间
在Linux中，所有设备都以文件形式存在于`/dev`目录下。

*   **列出块设备**：使用`lsblk`命令可以列出硬盘等块设备。
    ```bash
    lsblk
    ```
*   **查看磁盘空间使用情况**：使用`df`命令，`-h`选项以人类易读的格式显示。
    ```bash
    df -h
    ```
*   **查看文件或目录大小**：使用`du`命令，`-sh`选项汇总显示指定目录的总大小。
    ```bash
    du -sh /root
    ```

### 挂载文件系统
挂载是将存储设备（如分区、光盘镜像）关联到目录树的过程。

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_6.png)

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_8.png)

挂载的基本命令格式是：`mount 设备源 挂载点`。

以下是挂载的几种方式：
1.  **使用设备名挂载**
    ```bash
    mkdir /mnt/partition
    mount /dev/vda1 /mnt/partition
    ```
2.  **使用UUID挂载**：每个格式化后的分区都有唯一的UUID。
    ```bash
    blkid /dev/vda1 # 查看UUID
    mount UUID="你的-UUID-值" /mnt/partition
    ```
3.  **使用卷标挂载**（如果已设置卷标）。
    ```bash
    mount LABEL=你的卷标名 /mnt/partition
    ```

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_10.png)

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_12.png)

**查看当前挂载信息**：
```bash
mount | grep /mnt
```

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_14.png)

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_16.png)

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_18.png)

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_19.png)

**卸载文件系统**：使用`umount`命令，后接设备名或挂载点。
```bash
umount /mnt/partition
```
> **注意**：如果提示“设备忙”，可能是因为你当前正在挂载点的目录内。需要切换到其他目录再卸载。

### 挂载光盘镜像与网络共享
*   **挂载本地ISO文件**：
    ```bash
    mkdir /mnt/iso
    mount -o loop /path/to/your.iso /mnt/iso
    ```
    > 在RHEL7中，`-o loop`选项通常可省略。
*   **挂载网络共享（NFS）**：
    ```bash
    mkdir /mnt/nfs
    mount 172.25.254.250:/content /mnt/nfs
    ```

### 文件链接
Linux中有两种链接：硬链接和软链接（符号链接）。
*   **硬链接**：多个文件名指向同一个inode（数据块）。删除一个文件名不影响其他硬链接访问数据。**不能跨分区创建**。
    ```bash
    ln 源文件 硬链接文件
    ```
*   **软链接**：类似于Windows的快捷方式，是一个独立的文件，内容是指向目标文件的路径。**可以跨分区**。
    ```bash
    ln -s 源文件或目录 软链接文件
    ```
**查看inode号以区分**：
```bash
ls -i 文件名
```
硬链接的inode号相同，软链接则不同。

### 查找文件
`find`命令用于实时搜索文件，功能强大。

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_21.png)

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_22.png)

以下是`find`命令的一些常用选项示例：
*   **按文件名查找**：`-name`（区分大小写），`-iname`（不区分大小写）。
    ```bash
    find / -name "*.iso"
    ```
*   **按文件属主查找**：`-user`。
    ```bash
    find / -user student
    ```
*   **查找并执行操作**：`-exec`。例如，查找属于student的文件并拷贝到指定目录。
    ```bash
    find / -user student -exec cp -a {} /mnt/backup \;
    ```
    *   `{}` 代表`find`找到的每个文件。
    *   `\;` 是`-exec`参数的结束标志，反斜杠用于转义。

> 另一个查找命令`locate`速度更快，但它基于数据库，新创建的文件可能需要更新数据库(`updatedb`)后才能找到。

---

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_24.png)

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_26.png)

## 无人值守安装与Kickstart

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_28.png)

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_30.png)

上一节我们学习了手动管理文件系统，本节中我们来看看如何自动化系统安装与初始配置，这依赖于Kickstart文件。

### 什么是Kickstart文件
Kickstart文件（`.cfg`）是一个自动化安装脚本，定义了RHEL系统安装过程中的所有选项和安装后要执行的命令。系统安装后，会在`/root`目录下生成一个`anaconda-ks.cfg`文件，这是本机安装时的记录。

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_32.png)

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_34.png)

### 解读Kickstart文件
以下是一个`anaconda-ks.cfg`文件的关键部分解析：

1.  **系统类型与密码**：
    ```
    #version=RHEL7
    rootpw --iscrypted $6$加密的密码字符串
    ```
    定义了Root用户的加密密码。

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_36.png)

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_38.png)

2.  **安装后动作与安装源**：
    ```
    reboot
    url --url="http://classroom.example.com/content/rhel7.0/x86_64/dvd"
    ```
    `reboot`指定安装后重启。`url`指定了网络安装源的位置。

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_40.png)

3.  **系统配置**：
    ```
    firewall --enabled --service=ssh
    selinux --enforcing
    timezone Asia/Shanghai --isUtc
    ```
    这里配置了防火墙（启用并开放SSH服务）、SELinux模式（强制）和时区。

4.  **磁盘分区**：这是核心部分，定义了磁盘布局。
    ```
    zerombr
    clearpart --all --initlabel
    part / --fstype="xfs" --size=6144
    ```
    *   `zerombr`：清除主引导记录（MBR）。
    *   `clearpart --all --initlabel`：清除所有分区并初始化磁盘标签。
    *   `part / ...`：创建一个大小为6GB的根分区，使用XFS文件系统。

5.  **引导加载程序**：
    ```
    bootloader --location=mbr --boot-drive=vda
    ```
    将GRUB2引导程序安装到第一块硬盘(`vda`)的MBR。

6.  **软件包选择**：
    ```
    %packages
    @base
    @core
    kexec-tools
    -dracut-config-rescue
    %end
    ```
    *   `%packages` 和 `%end` 之间定义要安装的软件。
    *   `@base` 和 `@core` 是**软件包组**。
    *   `kexec-tools` 是单个软件包。
    *   `-dracut-config-rescue` 前的减号表示**不安装**此包。

7.  **安装后脚本**：
    ```
    %post
    # 这里可以写入任意Shell命令
    useradd alice
    echo "redhat" | passwd --stdin alice
    %end
    ```
    系统安装完成后，会自动执行`%post`和`%end`之间的命令。常用于创建用户、配置服务、部署应用等。

### 使用图形工具创建Kickstart文件
手动编写Kickstart文件容易出错，可以使用系统提供的图形工具`system-config-kickstart`来生成。
```bash
yum install system-config-kickstart -y
system-config-kickstart # 需要图形界面运行
```
该工具会引导你完成各项配置，并生成一个`.cfg`文件。

---

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_42.png)

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_44.png)

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_46.png)

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_48.png)

## 虚拟化管理简介
在培训环境中，我们使用KVM进行虚拟化。
*   **图形化管理工具**：可以通过`Applications` -> `System Tools` -> `Virtual Machine Manager`打开。需要root权限。
*   **命令行管理**：`virsh`命令是管理虚拟机的原生工具，但通常需要root权限。
    ```bash
    virsh list --all # 列出所有虚拟机
    virsh start desktop # 启动名为desktop的虚拟机
    virsh shutdown desktop # 关闭虚拟机
    ```
    在考试环境中，可能提供封装好的脚本（如`rht-vmctl`）来简化虚拟机控制。

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_50.png)

---

## 总结
本节课中我们一起学习了：
1.  **文件系统管理**：使用`df`/`du`查看空间，使用`mount`/`umount`挂载卸载设备，理解硬链接与软链接的区别，以及使用强大的`find`命令搜索文件。
2.  **Kickstart无人值守安装**：理解了Kickstart配置文件的结构与各部分含义，特别是磁盘分区、软件包选择和安装后脚本，这是实现Linux系统自动化部署的基石。
3.  **虚拟化基础**：了解了KVM虚拟化的基本管理方式。

![](img/37c14f237e61bfd7ef31ad7ca9a28e22_52.png)

掌握这些内容，你不仅能够熟练管理Linux服务器的存储和文件，也为大规模、标准化的系统部署打下了坚实基础。