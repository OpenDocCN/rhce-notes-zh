# RHCE红帽认证工程师培训课程：P10：逻辑卷管理器(LVM)与防火墙基础

![](img/4c655af6f92d47ea8f5ffea39704dd95_1.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_3.png)

## 概述
在本节课中，我们将学习两个核心主题：**逻辑卷管理器（LVM）** 和 **防火墙基础配置**。LVM技术允许我们动态调整磁盘分区大小，而防火墙则是保护系统安全的重要工具。我们将从LVM的基本概念和操作开始，然后介绍几种配置网络和防火墙的方法。

![](img/4c655af6f92d47ea8f5ffea39704dd95_5.png)

---

## 7.2：逻辑卷管理器（LVM）

上一节我们介绍了磁盘分区和RAID技术，本节中我们来看看如何更灵活地管理磁盘空间。

![](img/4c655af6f92d47ea8f5ffea39704dd95_7.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_8.png)

### LVM的核心概念与目的
传统磁盘分区在创建后很难调整大小，尤其是在需要缩小分区时。LVM技术的核心目的就是**动态地、灵活地调整分区大小**。

此外，LVM还能将多块物理硬盘的空间整合成一个大的“资源池”，用户在使用时无需关心底层是由哪些硬盘组成的。

![](img/4c655af6f92d47ea8f5ffea39704dd95_10.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_11.png)

我们可以用融资的例子来理解LVM：将多个来源的资金（硬盘空间）汇集到一个资金池（卷组）中，然后根据需要从这个池子里取出特定金额（逻辑卷）来使用。

![](img/4c655af6f92d47ea8f5ffea39704dd95_13.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_15.png)

### LVM的三个核心组件
LVM的操作流程主要由三个步骤组成，对应三个核心概念：

![](img/4c655af6f92d47ea8f5ffea39704dd95_17.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_18.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_20.png)

1.  **物理卷（Physical Volume, PV）**
    *   **目的**：让物理硬盘设备支持LVM技术。
    *   **类比**：将面粉（硬盘）准备好，使其可以用来和面。

![](img/4c655af6f92d47ea8f5ffea39704dd95_22.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_23.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_25.png)

2.  **卷组（Volume Group, VG）**
    *   **目的**：将多个已支持LVM的PV整合成一个大的存储池。
    *   **类比**：将准备好的多份面粉（多个PV）倒在一起，形成一个大的面粉堆（VG）。

3.  **逻辑卷（Logical Volume, LV）**
    *   **目的**：从卷组（VG）中划分出特定大小的空间来使用，这个空间就是逻辑卷。
    *   **类比**：从大的面粉堆（VG）中，取出需要的一团面（LV）来蒸馒头（格式化并挂载使用）。

![](img/4c655af6f92d47ea8f5ffea39704dd95_27.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_28.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_30.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_31.png)

**总结一下**：LVM技术主要有两大优势：一是可以动态调整分区大小；二是能够整合多块硬盘，用户无需关心底层细节。

![](img/4c655af6f92d47ea8f5ffea39704dd95_33.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_34.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_35.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_37.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_39.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_40.png)

### LVM基本管理命令
以下是围绕PV、VG、LV三个步骤的常用命令摘要：

![](img/4c655af6f92d47ea8f5ffea39704dd95_42.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_43.png)

| 操作对象 | 功能 | 命令示例 |
| :--- | :--- | :--- |
| **物理卷 (PV)** | 创建PV | `pvcreate /dev/sdb` |
| | 查看PV信息 | `pvdisplay` |
| | 删除PV | `pvremove /dev/sdb` |
| **卷组 (VG)** | 创建VG | `vgcreate vg_name /dev/sdb /dev/sdc` |
| | 查看VG信息 | `vgdisplay` |
| | 扩展VG（添加新PV） | `vgextend vg_name /dev/sdd` |
| | 缩减VG（移除PV） | `vgreduce vg_name /dev/sdb` |
| | 删除VG | `vgremove vg_name` |
| **逻辑卷 (LV)** | 创建LV | `lvcreate -n lv_name -L 10G vg_name` |
| | 查看LV信息 | `lvdisplay` |
| | 扩展LV容量 | `lvextend -L +5G /dev/vg_name/lv_name` |
| | 缩减LV容量 | `lvreduce -L -2G /dev/vg_name/lv_name` |
| | 删除LV | `lvremove /dev/vg_name/lv_name` |

**重要提示**：
*   `pvcreate` 等命令只是让硬盘支持LVM，并不能改变其物理容量。
*   红帽考试中常考的是LV的**扩展**操作。
*   创建LV时，可以用 `-L` 指定具体容量（如 `-L 10G`），也可以用 `-l` 指定PE（Physical Extent，默认为4MB）的个数。

![](img/4c655af6f92d47ea8f5ffea39704dd95_45.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_46.png)

### LVM实战：创建、扩展与缩减
以下是LVM的典型操作流程。

**第一步：创建PV、VG和LV**
```bash
# 1. 在新增的硬盘上创建PV
pvcreate /dev/sdb /dev/sdc

# 2. 创建名为`myvg`的卷组，包含上述两个PV
vgcreate myvg /dev/sdb /dev/sdc

![](img/4c655af6f92d47ea8f5ffea39704dd95_48.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_50.png)

# 3. 从`myvg`中创建一个名为`mylv`，大小为10G的逻辑卷
lvcreate -n mylv -L 10G myvg

# 4. 格式化逻辑卷（使用ext4文件系统）
mkfs.ext4 /dev/myvg/mylv

# 5. 创建挂载点并挂载
mkdir /mnt/lvm_test
mount /dev/myvg/mylv /mnt/lvm_test

![](img/4c655af6f92d47ea8f5ffea39704dd95_52.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_54.png)

# 6. 写入/etc/fstab实现开机自动挂载
echo "/dev/myvg/mylv /mnt/lvm_test ext4 defaults 0 0" >> /etc/fstab
```

**第二步：扩展逻辑卷（LV）**
当LV空间不足时，可以对其进行扩展。这是红帽考试的重点。
```bash
# 1. 首先确保VG有足够空间。如果不够，可以先扩展VG：`vgextend myvg /dev/sdd`
# 2. 卸载挂载点（安全起见）
umount /mnt/lvm_test

# 3. 扩展LV的物理边界（例如，增加5G）
lvextend -L +5G /dev/myvg/mylv

# 4. 检查文件系统完整性
e2fsck -f /dev/myvg/mylv

# 5. 扩展文件系统逻辑边界，使系统识别新容量
resize2fs /dev/myvg/mylv

![](img/4c655af6f92d47ea8f5ffea39704dd95_56.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_57.png)

# 6. 重新挂载
mount /dev/myvg/mylv /mnt/lvm_test
# 使用`df -h`查看，容量应已增加
```

![](img/4c655af6f92d47ea8f5ffea39704dd95_59.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_60.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_62.png)

**第三步：缩减逻辑卷（LV）**
缩减操作风险较高，需确保已用空间小于目标容量。
```bash
# 1. 卸载挂载点
umount /mnt/lvm_test

![](img/4c655af6f92d47ea8f5ffea39704dd95_64.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_65.png)

# 2. 检查文件系统
e2fsck -f /dev/myvg/mylv

![](img/4c655af6f92d47ea8f5ffea39704dd95_67.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_68.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_69.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_70.png)

# 3. 先缩减文件系统逻辑边界（例如，缩减到8G）
resize2fs /dev/myvg/mylv 8G

# 4. 再缩减LV的物理边界
lvreduce -L 8G /dev/myvg/mylv

![](img/4c655af6f92d47ea8f5ffea39704dd95_72.png)

# 5. 重新挂载
mount /dev/myvg/mylv /mnt/lvm_test
```

![](img/4c655af6f92d47ea8f5ffea39704dd95_74.png)

### LVM快照卷
LVM快照卷可以快速为逻辑卷创建一个“时间点”副本，常用于备份或测试。

![](img/4c655af6f92d47ea8f5ffea39704dd95_76.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_77.png)

**创建与恢复快照**
```bash
# 1. 创建快照卷。快照卷大小需足够存放原LV的变化数据，通常与原LV大小一致。
lvcreate -L 10G -s -n mylv_snap /dev/myvg/mylv

# 2. 挂载快照卷进行查看或备份
mount /dev/myvg/mylv_snap /mnt/snapshot

# 3. 恢复快照（会覆盖原LV数据，需谨慎！）
# 首先卸载原LV和快照卷
umount /mnt/lvm_test
umount /mnt/snapshot
# 执行恢复
lvconvert --merge /dev/myvg/mylv_snap
# 恢复完成后，快照卷会自动删除
```
**注意**：快照卷是**单次有效**的，恢复后即自动删除。

---

## 8.2：防火墙基础与网络配置

在学习了存储管理后，我们转向系统安全。防火墙是保护系统免受外部网络攻击的第一道防线。在配置防火墙前，我们需要确保网络是连通的。

![](img/4c655af6f92d47ea8f5ffea39704dd95_79.png)

### 配置网络连接的四种方法
以下是配置Linux网络接口的四种方法，你可以选择最顺手的一种。

**方法一：编辑配置文件（最底层）**
*   网卡配置文件位于 `/etc/sysconfig/network-scripts/` 目录下，文件名如 `ifcfg-ens33`。
*   使用 `vi` 或 `vim` 编辑该文件，修改 `IPADDR`、`NETMASK` 等参数。
*   修改后需重启网络服务生效：`systemctl restart network`。

**方法二：使用nmtui文本用户界面**
*   运行 `nmtui` 命令，会打开一个基于文本的配置界面。
*   通过方向键和回车键选择“Edit a connection”，编辑IP地址等信息。
*   保存退出后，同样需要重启网络服务。

![](img/4c655af6f92d47ea8f5ffea39704dd95_81.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_82.png)

**方法三：使用nm-connection-editor图形界面**
*   在桌面环境或支持图形化的远程连接中，运行 `nm-connection-editor`。
*   会弹出图形化网络配置窗口，操作与Windows类似。
*   配置后可能需要重启网络服务。

**方法四：使用系统托盘图标（最简单）**
*   在GNOME等桌面环境中，直接点击右上角的网络图标 -> 有线设置 -> 齿轮图标。
*   在图形化界面中直接修改IP地址，点击“应用”。
*   通常通过界面开关一下网络连接即可生效，无需输入命令。

**考试重要提醒**：无论用哪种方法，配置完网络后**务必确保重启后配置依然生效**（即已写入配置文件）。考试系统会重启验证，如果网络未配通，可能导致整场考试失败。

### 防火墙：iptables基础
iptables是RHEL 5/6及早期RHEL 7版本中默认的防火墙工具。尽管在新版本中已被firewalld取代，但理解其原理对学习防火墙至关重要。

![](img/4c655af6f92d47ea8f5ffea39704dd95_84.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_85.png)

#### 防火墙的核心作用与规则链
防火墙主要工作在**INPUT链**上，用于控制从外部进入内部网络的流量，就像家里的防盗门。

![](img/4c655af6f92d47ea8f5ffea39704dd95_87.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_89.png)

防火墙对匹配的数据包可以执行以下几种**动作（Target）**：
*   **ACCEPT**：允许通过。
*   **REJECT**：拒绝并返回明确拒绝信息（如`connection refused`）。
*   **DROP**：直接丢弃，不回应任何信息（对方感觉像超时）。
*   **LOG**：记录日志。

![](img/4c655af6f92d47ea8f5ffea39704dd95_91.png)

**在红帽考试中，如果需要拒绝流量，推荐使用 `REJECT`**，因为判分脚本能明确识别这是配置生效的结果，而非网络不通（DROP状态与网络断开状态类似）。

![](img/4c655af6f92d47ea8f5ffea39704dd95_93.png)

#### iptables基本命令与策略配置
iptables规则按顺序从上到下匹配，一旦匹配成功就执行相应动作。

以下是常用命令：
```bash
# 查看所有规则
iptables -L

![](img/4c655af6f92d47ea8f5ffea39704dd95_95.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_96.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_98.png)

# 清空所有规则
iptables -F

![](img/4c655af6f92d47ea8f5ffea39704dd95_100.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_102.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_103.png)

# 设置默认策略（例如，INPUT链默认拒绝所有）
iptables -P INPUT DROP

![](img/4c655af6f92d47ea8f5ffea39704dd95_105.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_107.png)

# 添加规则（在链末尾添加）
iptables -A INPUT -p tcp --dport 22 -j ACCEPT  # 允许所有访问22端口(SSH)的流量

![](img/4c655af6f92d47ea8f5ffea39704dd95_109.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_111.png)

# 插入规则（在链开头或指定位置插入）
iptables -I INPUT 1 -s 192.168.1.100 -j REJECT # 在INPUT链第1条插入，拒绝来自192.168.1.100的所有流量

![](img/4c655af6f92d47ea8f5ffea39704dd95_113.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_115.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_117.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_119.png)

# 删除规则
iptables -D INPUT 2 # 删除INPUT链的第2条规则
```

**配置实例**：
1.  **默认允许，拒绝特定服务**：
    ```bash
    iptables -P INPUT ACCEPT # 默认允许所有
    iptables -A INPUT -p tcp --dport 22 -j REJECT # 拒绝所有SSH连接
    ```

2.  **默认拒绝，允许特定来源和端口**：
    ```bash
    iptables -P INPUT DROP # 默认拒绝所有
    iptables -A INPUT -s 192.168.1.0/24 -p tcp --dport 80 -j ACCEPT # 允许192.168.1.0网段访问80端口
    ```

**注意**：iptables规则默认是临时的，重启后失效。如需保存，在RHEL 7中可使用 `service iptables save` 命令（如果安装了iptables-services包）。

---

## 总结
本节课中我们一起学习了两个重要部分：
1.  **逻辑卷管理器（LVM）**：我们理解了PV、VG、LV的概念，掌握了创建、扩展、缩减逻辑卷以及创建快照卷的完整流程。这是实现磁盘空间灵活管理的核心技术。
2.  **网络配置与防火墙基础**：我们学习了四种配置网络的方法，并深入了解了iptables防火墙的工作原理、规则链和基本配置命令。虽然iptables在新系统中逐渐被取代，但其概念是理解任何防火墙的基础。

**课后任务**：
*   **复习**：务必完成LVM的创建和**扩展（lvextend）** 实验，这是考试重点。
*   **预习**：下一节课我们将学习更现代的防火墙管理工具 `firewalld` 以及网络绑定（Teaming）技术。

![](img/4c655af6f92d47ea8f5ffea39704dd95_121.png)

![](img/4c655af6f92d47ea8f5ffea39704dd95_123.png)

---
**注意**：本教程根据视频内容整理，保留了原讲解的技术要点和操作步骤，删除了语气词和闲聊部分，并按照要求进行了结构化排版。文中涉及的实验操作请在测试环境中进行。