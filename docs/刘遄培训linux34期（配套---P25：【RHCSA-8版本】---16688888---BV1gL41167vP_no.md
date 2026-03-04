# Linux培训：RHCSA 8版本考前辅导：概述

在本节课中，我们将对红帽认证系统管理员（RHCSA）8版本的考试内容进行串讲。课程将结合《Linux就该这么学》书籍的知识点、课堂学习内容以及红帽官方考题，进行专项练习，帮助大家熟悉考试流程和题型。

---

## Linux培训：RHCSA 8版本考前辅导：课程资源介绍

上一节我们介绍了课程的整体目标，本节中我们来看看备考所需的资源。作为付费学员，您将获得以下四样核心备考材料，它们相辅相成，缺一不可。

以下是提供的四样备考材料：
1.  **红帽考试原题**：与真实考试题目基本一致，非模拟题。
2.  **考试标准答案**：由讲师编写，旨在帮助达到满分。
3.  **考前辅导视频**：即本视频，带您逐一操作和讲解考题。
4.  **考试模拟环境**：用于练习的虚拟机环境，非真实考试环境。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_1.png)

这相当于在高考前两个月拿到了真题、标准答案，并有高手带您练习，因此通过率一直很高。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_3.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_5.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_7.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_9.png)

---

## Linux培训：RHCSA 8版本考前辅导：红帽认证简介

在深入考题之前，我们先简要了解红帽认证的价值。这有助于理解为何选择此认证。

以下是红帽认证的三点核心优势：
1.  **行业地位**：红帽是领先的开源解决方案公司，已被IBM收购，强强联合。
2.  **市场认可**：在Linux认证领域，红帽一家独大，是业界公认的标准。
3.  **经济影响**：红帽公司是标普500成分股，其表现被视为美国宏观经济的风向标之一。

因此，对于Linux系统管理员而言，红帽认证是含金量最高且最值得投入的选择。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_11.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_13.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_15.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_17.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_19.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_21.png)

---

![](img/d0f1f440c4a23e81f2a995160d3d73b9_23.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_25.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_27.png)

## Linux培训：RHCSA 8版本考前辅导：考试环境准备

![](img/d0f1f440c4a23e81f2a995160d3d73b9_29.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_31.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_33.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_35.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_37.png)

现在，我们开始实际操作。首先需要正确设置考试模拟环境。请确保您已下载并解压了全部十个压缩包。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_39.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_41.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_43.png)

以下是环境设置步骤：
1.  解压所有压缩包到指定目录。
2.  打开VMware Workstation（建议版本15.5），选择“文件”->“打开”，找到解压后的虚拟机文件（例如 `rhel8`）。
3.  加载虚拟机后，在管理界面选择对应的考试快照（如 `RHCSA 8`）。
4.  启动虚拟机，使用root用户登录，密码为 `flectrag`。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_45.png)

**注意**：如果操作中遇到文件错误，可能是下载损坏，请重新下载。由于环境较大（约30GB），操作每一步后请稍作等待。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_47.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_49.png)

---

![](img/d0f1f440c4a23e81f2a995160d3d73b9_51.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_53.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_55.png)

## Linux培训：RHCSA 8版本考前辅导：考题概览与注意事项

![](img/d0f1f440c4a23e81f2a995160d3d73b9_57.png)

成功进入考试环境后，我们来看一下考试界面和通用规则。RHCSA考试时长2.5小时，主要考核系统管理能力。

考试界面是一个网页，显示所有题目（无需翻页）。请注意以下关键信息：
*   **密码规则**：除非特别说明，所有需要设置或输入的密码均为 `flectrag`。
*   **网络配置**：通常需要为虚拟机配置指定的静态IP地址。
*   **配置持久性**：所有配置必须在系统重启后依然生效，否则可能无法得分。
*   **题目标记**：可以勾选题目前的复选框，但这仅用于个人标记，不影响评分。

本视频定位为考前串讲，预设您已学完所有技术点。我们将专注于讲解如何将这些技术应用于考试题目。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_59.png)

---

## Linux培训：RHCSA 8版本考前辅导：第一台虚拟机操作（上）

我们从第一台虚拟机（`mars.lab.example.com`）开始。首先完成基础配置。

**1. 配置主机名与网络**
*   **命令**：`hostnamectl set-hostname mars.lab.example.com`
*   **网络配置**：使用 `nmtui` 工具编辑网卡，将IP地址设置为 `172.25.250.100/24`，网关为 `172.25.250.254`。
*   **验证**：配置后重启网络服务或直接重启虚拟机使配置生效。

**2. 配置Yum软件仓库**
考试环境已提供仓库地址，只需创建配置文件。
*   **配置文件路径**：`/etc/yum.repos.d/rhel8.repo`
*   **配置内容**：
    ```ini
    [BaseOS]
    name=BaseOS
    baseurl=http://content.example.com/rhel8.0/x86_64/dvd/BaseOS
    enabled=1
    gpgcheck=0

    [AppStream]
    name=AppStream
    baseurl=http://content.example.com/rhel8.0/x86_64/dvd/AppStream
    enabled=1
    gpgcheck=0
    ```
*   **验证**：执行 `yum list` 查看软件包列表是否正常显示。

---

![](img/d0f1f440c4a23e81f2a995160d3d73b9_61.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_63.png)

## Linux培训：RHCSA 8版本考前辅导：第一台虚拟机操作（中）

![](img/d0f1f440c4a23e81f2a995160d3d73b9_65.png)

接下来处理用户、权限和计划任务等系统管理题目。

**3. 调试SELinux**
题目要求让Web服务在82端口运行。
*   **命令**：`semanage port -a -t http_port_t -p tcp 82`
*   **设置服务开机自启**：`systemctl enable --now httpd`

![](img/d0f1f440c4a23e81f2a995160d3d73b9_67.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_69.png)

**4. 创建用户与组**
*   **创建组**：`groupadd sysmgrs`
*   **创建用户**：
    *   `useradd -G sysmgrs natasha`
    *   `useradd harry`
    *   `useradd -s /sbin/nologin sarah`
*   **设置密码**：为 `natasha`, `harry`, `sarah` 设置密码为 `flectrag`。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_71.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_73.png)

**5. 配置计划任务**
为natasha用户配置每两分钟执行一次 `/bin/echo hello`。
*   **命令**：`crontab -e -u natasha`
*   **内容**：`*/2 * * * * /bin/echo hello`

![](img/d0f1f440c4a23e81f2a995160d3d73b9_75.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_77.png)

---

![](img/d0f1f440c4a23e81f2a995160d3d73b9_79.png)

## Linux培训：RHCSA 8版本考前辅导：第一台虚拟机操作（下）

![](img/d0f1f440c4a23e81f2a995160d3d73b9_81.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_83.png)

继续完成第一台虚拟机的剩余题目，涉及权限、查找和打包等。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_85.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_87.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_89.png)

**6. 创建协作目录**
*   **创建目录**：`mkdir /home/managers`
*   **设置权限**：
    *   所有组为 `sysmgrs`：`chgrp sysmgrs /home/managers`
    *   权限为770：`chmod 770 /home/managers`
    *   设置SGID权限，使新建文件自动继承组：`chmod g+s /home/managers` (或 `chmod 2770 /home/managers`)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_91.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_93.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_95.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_97.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_99.png)

**7. 配置NTP时间同步**
*   **命令**：`timedatectl set-timezone Asia/Shanghai`
*   **配置文件**：编辑 `/etc/chrony.conf`，添加服务器 `server classroom.example.com iburst`

![](img/d0f1f440c4a23e81f2a995160d3d73b9_101.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_103.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_105.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_107.png)

**8. 配置autofs自动挂载**
*   **安装**：`yum install autofs`
*   **主配置**：编辑 `/etc/auto.master`，添加 `/home/guests /etc/auto.demo`
*   **子配置**：创建 `/etc/auto.demo`，内容为 `* -rw,sync classroom.example.com:/home/guests/&`
*   **启用**：`systemctl enable --now autofs`

![](img/d0f1f440c4a23e81f2a995160d3d73b9_109.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_111.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_113.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_115.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_117.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_119.png)

**9. 文件权限高级控制**
将 `/etc/fstab` 复制到 `/var/tmp/fstab`，并设置权限。
*   **复制**：`cp /etc/fstab /var/tmp/fstab`
*   **设置ACL**：
    *   `setfacl -m u:natasha:rw /var/tmp/fstab`
    *   `setfacl -m u:harry:- /var/tmp/fstab`

![](img/d0f1f440c4a23e81f2a995160d3d73b9_121.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_123.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_125.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_127.png)

**10. 查找与归档**
*   **查找并复制文件**：`find / -user jacques -exec cp -a {} /root/findresults \;`
*   **过滤文件内容**：`grep ng /usr/share/xml/iso-codes/iso_639_3.xml > /root/list.txt`
*   **创建压缩包**：`tar czvf /root/backup.tar.gz /usr/share`

![](img/d0f1f440c4a23e81f2a995160d3d73b9_129.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_131.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_133.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_135.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_137.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_139.png)

完成以上操作后，建议重启第一台虚拟机，确保所有配置持久生效。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_141.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_143.png)

---

## Linux培训：RHCSA 8版本考前辅导：第二台虚拟机与系统修复

现在切换到第二台虚拟机（`venus.lab.example.com`）。首先需要破解其root密码。

**1. 破解root密码**
1.  重启虚拟机，在GRUB菜单按 `e` 进入编辑模式。
2.  找到以 `linux` 开头的行，在行末添加 `rd.break`，按 `Ctrl+X` 启动。
3.  进入紧急模式后，重新挂载根目录为可写：`mount -o remount,rw /sysroot`
4.  切换根环境：`chroot /sysroot`
5.  修改密码：`echo flectrag | passwd --stdin root`
6.  创建SELinux重标记文件：`touch /.autorelabel`
7.  退出并重启：输入 `exit` 两次。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_145.png)

**2. 基础配置**
登录后，配置主机名和网络（IP: `172.25.250.200`），并配置Yum仓库（同第一台虚拟机）。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_147.png)

---

![](img/d0f1f440c4a23e81f2a995160d3d73b9_149.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_151.png)

## Linux培训：RHCSA 8版本考前辅导：第二台虚拟机存储管理

![](img/d0f1f440c4a23e81f2a995160d3d73b9_153.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_155.png)

第二台虚拟机的重点在于存储管理，包括逻辑卷、交换分区和VDO。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_157.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_159.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_161.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_163.png)

**3. 调整逻辑卷大小**
将现有逻辑卷 `vo` 的大小从175MiB调整到230MiB。
*   **扩展逻辑卷**：`lvextend -L 230M /dev/myvol/vo`
*   **扩展文件系统**：`resize2fs /dev/myvol/vo`

**4. 添加交换分区**
*   **分区**：在 `/dev/vdb` 上新建一个756MiB的分区（如 `/dev/vdb1`）。
*   **格式化**：`mkswap /dev/vdb1`
*   **激活并持久化**：编辑 `/etc/fstab`，添加 `/dev/vdb1 swap swap defaults 0 0`，然后执行 `swapon -a`。

**5. 创建LVM逻辑卷**
*   **创建物理卷**：`pvcreate /dev/vdb2`
*   **创建卷组**：`vgcreate -s 16M qagroup /dev/vdb2`
*   **创建逻辑卷**：`lvcreate -l 60 -n qa qagroup`
*   **格式化与挂载**：
    *   `mkfs.ext3 /dev/qagroup/qa`
    *   编辑 `/etc/fstab` 添加挂载项，将 `/dev/qagroup/qa` 挂载到 `/mnt/qa`。

**6. 创建VDO卷**
*   **创建VDO卷**：`vdo create --name vdough --device /dev/vdc --vdoLogicalSize 50G`
*   **格式化**：`mkfs.xfs /dev/mapper/vdough`
*   **持久化挂载**：编辑 `/etc/fstab`，添加 `/dev/mapper/vdough /vbread xfs defaults,_netdev 0 0`

![](img/d0f1f440c4a23e81f2a995160d3d73b9_165.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_166.png)

**7. 调整系统性能配置文件**
*   **查看推荐配置**：`tuned-adm recommend`
*   **应用配置**：`tuned-adm profile virtual-guest`

![](img/d0f1f440c4a23e81f2a995160d3d73b9_168.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_170.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_172.png)

完成所有操作后，重启第二台虚拟机以确保配置生效。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_174.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_176.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_178.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_180.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_182.png)

---

![](img/d0f1f440c4a23e81f2a995160d3d73b9_184.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_186.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_188.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_190.png)

## Linux培训：RHCSA 8版本考前辅导：判分与总结

![](img/d0f1f440c4a23e81f2a995160d3d73b9_192.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_194.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_196.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_198.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_200.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_202.png)

操作完成后，考官会使用评分脚本自动判分。在我们的模拟环境中，由于部分服务缺失或环境差异，某些题目可能无法得分，但已完整演示了所有操作步骤。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_204.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_206.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_208.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_210.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_212.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_214.png)

**模拟判分结果说明**：
*   最终得分可能在240-250分之间（通过线为210分）。
*   未得分项通常是因为模拟环境缺少对应服务（如NFS）或特定文件，并非操作错误。
*   在真实考试环境中，只要按照上述步骤正确操作，即可获得相应分数。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_216.png)

**本节课总结**：
本节课我们一起学习了RHCSA 8版本考试的全部核心操作。我们串讲了从系统基础配置、用户权限管理、计划任务、SELinux调试，到复杂的存储管理（包括逻辑卷、交换分区、VDO）等所有考点。请记住，考试时务必仔细阅读题目，按照要求一步步操作，并确保配置在重启后依然有效。打好基础，沉着应对，通过考试并非难事。

![](img/d0f1f440c4a23e81f2a995160d3d73b9_218.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_220.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_222.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_224.png)

![](img/d0f1f440c4a23e81f2a995160d3d73b9_225.png)

祝您考试顺利！