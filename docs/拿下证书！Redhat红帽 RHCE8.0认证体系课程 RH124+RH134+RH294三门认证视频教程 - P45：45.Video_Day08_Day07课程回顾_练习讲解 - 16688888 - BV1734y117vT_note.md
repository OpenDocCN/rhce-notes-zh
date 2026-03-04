# Red Hat RHCE 8.0 认证体系课程：P45：Day08 课程回顾与练习讲解

![](img/3e9174ab1a7172072b071856e7e1d3a3_0.png)

![](img/3e9174ab1a7172072b071856e7e1d3a3_2.png)

![](img/3e9174ab1a7172072b071856e7e1d3a3_4.png)

![](img/3e9174ab1a7172072b071856e7e1d3a3_6.png)

## 概述
在本节课中，我们将回顾第七天的核心知识点，并通过讲解相关练习题来巩固所学内容。主要内容包括系统调优、SELinux、磁盘与LVM管理以及Shell脚本编写。

---

## 课程回顾

![](img/3e9174ab1a7172072b071856e7e1d3a3_8.png)

![](img/3e9174ab1a7172072b071856e7e1d3a3_10.png)

上一节我们介绍了系统临时文件管理，本节中我们来看看第七天课程的其他核心内容。

### 1. 系统调优 🛠️
系统调优主要通过 `tuned` 服务实现。以下是关键操作：

*   **列出配置**：`tuned-adm list`
*   **查看当前配置**：`tuned-adm active`
*   **切换配置**：`tuned-adm profile <配置名称>`

例如，将系统调优方案切换为 `virtual-guest`：
```bash
tuned-adm profile virtual-guest
```

### 2. 文件访问控制列表
文件ACL用于为特定用户或组设置精细的文件访问权限，而非传统的用户/组/其他大类控制。

### 3. SELinux 管理 🔐
SELinux 有三种运行模式：
*   **enforcing**：强制模式，拦截并记录违规行为。
*   **permissive**：宽容模式，仅记录违规行为。
*   **disabled**：禁用模式。

以下是模式切换方法：
*   **临时切换**（仅限 enforcing 与 permissive 之间）：
    ```bash
    setenforce 1  # 切换到 enforcing
    setenforce 0  # 切换到 permissive
    ```
*   **永久切换**：修改 `/etc/selinux/config` 文件中的 `SELINUX=` 值。**注意**：涉及 `disabled` 模式的切换必须重启系统才能生效。

### 4. SELinux 安全上下文
安全上下文定义了进程或文件的访问权限。修改方法如下：
*   **更改默认上下文**：使用 `semanage fcontext` 命令。
    ```bash
    semanage fcontext -a -t httpd_sys_content_t "/web(/.*)?"
    restorecon -Rv /web
    ```
*   **临时修改上下文**：使用 `chcon` 命令。
    ```bash
    chcon -t samba_share_t /shared
    # 或参考其他文件上下文
    chcon --reference=/var/www/html /shared
    ```

### 5. 磁盘与 LVM 管理 💾
上一节我们回顾了SELinux，本节中我们来看看磁盘管理的核心步骤。

以下是磁盘管理的主要工具和流程：

**分区工具**
*   `fdisk`
*   `parted`

![](img/3e9174ab1a7172072b071856e7e1d3a3_12.png)

**基本分区操作步骤**
1.  分区：使用 `fdisk` 或 `parted` 创建分区。
2.  格式化：使用 `mkfs` 命令创建文件系统。
3.  挂载：临时挂载使用 `mount` 命令；永久挂载需写入 `/etc/fstab` 文件。

**交换空间创建步骤**
1.  分区：创建类型为 `82` (Linux swap) 的分区。
2.  格式化：使用 `mkswap` 命令。
3.  启用：使用 `swapon` 命令，并写入 `/etc/fstab` 实现开机自动启用。

**LVM 管理步骤**
1.  创建物理卷 (PV)：`pvcreate /dev/sdX1`
2.  创建卷组 (VG)：`vgcreate <vg_name> /dev/sdX1`
3.  创建逻辑卷 (LV)：`lvcreate -n <lv_name> -L <size> <vg_name>`
4.  格式化并挂载逻辑卷。

**LVM 扩容与缩容**
*   **在线扩容逻辑卷**：
    1.  检查卷组是否有足够空间：`vgs`
    2.  扩展逻辑卷：`lvextend -L +<size> /dev/<vg_name>/<lv_name>`
    3.  扩展文件系统：`xfs_growfs /mount_point` (针对 XFS) 或 `resize2fs /dev/<vg_name>/<lv_name>` (针对 ext4)。
*   **离线缩容逻辑卷**（以 ext4 为例）：
    1.  卸载文件系统：`umount /mount_point`
    2.  检查文件系统：`e2fsck -f /dev/<vg_name>/<lv_name>`
    3.  缩小文件系统：`resize2fs /dev/<vg_name>/<lv_name> <new_size>`
    4.  缩小逻辑卷：`lvreduce -L <new_size> /dev/<vg_name>/<lv_name>`
    5.  重新挂载。

**重要提示**：考试中常考 LVM 扩容。建议分配空间时略大于题目要求，为后续操作留出余地。

![](img/3e9174ab1a7172072b071856e7e1d3a3_14.png)

![](img/3e9174ab1a7172072b071856e7e1d3a3_16.png)

---

## 练习讲解

以下是第七天课程的部分练习题讲解。

### 练习1：LVM 逻辑卷扩容
**题目要求**：在 servera 上，将本地 LVM 逻辑卷从 200MB 扩展到 500MB，文件系统为 XFS。

![](img/3e9174ab1a7172072b071856e7e1d3a3_18.png)

![](img/3e9174ab1a7172072b071856e7e1d3a3_20.png)

**操作步骤**：
1.  确保有可用空间。如需添加新磁盘或分区，将其类型标记为 `8e` (Linux LVM)。
2.  创建物理卷：`pvcreate /dev/sdX1`
3.  将物理卷加入卷组（如已存在可跳过）：`vgextend <vg_name> /dev/sdX1`
4.  扩展逻辑卷：`lvextend -L 500M /dev/<vg_name>/<lv_name>`
5.  扩展文件系统：`xfs_growfs /mount_point`

### 练习2：创建交换空间
**题目要求**：创建并启用一个交换分区。

**操作步骤**：
1.  使用 `fdisk` 或 `parted` 创建新分区，并将类型设置为 `82` (Linux swap)。
2.  格式化交换分区：`mkswap /dev/sdX1`
3.  临时启用：`swapon /dev/sdX1`
4.  永久启用：将 `UUID=<swap_uuid> swap swap defaults 0 0` 写入 `/etc/fstab`，然后执行 `swapon -a`。

### 练习3：Shell 脚本 - 等差数列
**题目要求**：编写脚本，输出公差为4、最大数不超过50的等差数列。

**脚本示例**：
```bash
#!/bin/bash
for i in $(seq 2 4 50); do
    echo $i
done
```

### 练习4：Shell 脚本 - 防火墙规则
**题目要求**：编写脚本，从键盘读取服务名，并为其添加永久防火墙规则。

![](img/3e9174ab1a7172072b071856e7e1d3a3_22.png)

![](img/3e9174ab1a7172072b071856e7e1d3a3_24.png)

![](img/3e9174ab1a7172072b071856e7e1d3a3_26.png)

**脚本示例**：
```bash
#!/bin/bash
read -p "Please input the service names: " services
for service in $services; do
    echo "Adding $service service..."
    firewall-cmd --permanent --add-service=$service
done
firewall-cmd --reload
```

![](img/3e9174ab1a7172072b071856e7e1d3a3_28.png)

![](img/3e9174ab1a7172072b071856e7e1d3a3_29.png)

### 练习5：Shell 脚本 - 批量创建用户
**题目要求**：编写脚本，根据提供的用户列表文件批量创建用户（无密码，shell 为 `/bin/false`）。

![](img/3e9174ab1a7172072b071856e7e1d3a3_30.png)

![](img/3e9174ab1a7172072b071856e7e1d3a3_31.png)

![](img/3e9174ab1a7172072b071856e7e1d3a3_33.png)

![](img/3e9174ab1a7172072b071856e7e1d3a3_35.png)

**脚本示例**：
```bash
#!/bin/bash
if [ $# -eq 0 ]; then
    echo "Usage: $0 <userlist_file>"
    exit 1
fi
if [ ! -f "$1" ]; then
    echo "Input file not found."
    exit 2
fi
for username in $(cat $1); do
    useradd -s /bin/false $username
done
```

![](img/3e9174ab1a7172072b071856e7e1d3a3_36.png)

### 练习9：Shell 脚本 - 系统识别
**题目要求**：编写脚本，根据输入参数输出对应的系统名称。

![](img/3e9174ab1a7172072b071856e7e1d3a3_38.png)

**脚本示例**：
```bash
#!/bin/bash
case $1 in
    rhel)
        echo "Red Hat Enterprise Linux"
        ;;
    centos)
        echo "Community Enterprise Operating System"
        ;;
    *)
        echo "Usage: $0 {rhel|centos}"
        ;;
esac
```

![](img/3e9174ab1a7172072b071856e7e1d3a3_40.png)

![](img/3e9174ab1a7172072b071856e7e1d3a3_42.png)

**提示**：练习6、7、8为综合性大作业，涉及系统信息收集、堡垒机环境部署等，鼓励学员自行探索完成。

![](img/3e9174ab1a7172072b071856e7e1d3a3_44.png)

![](img/3e9174ab1a7172072b071856e7e1d3a3_45.png)

---

![](img/3e9174ab1a7172072b071856e7e1d3a3_47.png)

## 总结
本节课中我们一起回顾了系统调优、SELinux、磁盘与LVM管理等核心知识，并通过多个练习脚本加深了对Shell编程和系统管理命令的理解。掌握这些内容是通过RHCE认证考试的重要基础。