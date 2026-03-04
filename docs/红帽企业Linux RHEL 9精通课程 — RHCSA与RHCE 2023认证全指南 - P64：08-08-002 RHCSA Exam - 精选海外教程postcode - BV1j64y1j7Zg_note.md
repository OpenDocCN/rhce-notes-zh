# 红帽企业Linux RHEL 9精通课程：P64：RHCSA 8 考试实战演练

在本节课中，我们将一起完成一份RHCSA 8（EX200）认证考试的模拟实战演练。我们将按照考试要求，逐步完成从系统启动、网络配置、存储管理到服务部署等20个任务，确保每个步骤都符合考试规范，并能经受系统重启的考验。

## 考试概述与实验环境搭建

在开始考试之前，我们先了解一些关于RHCSA 8考试的基本信息。这是一门完全基于实践的考试，总分为300分，及格线为210分，考试时长为2.5小时（150分钟）。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_1.png)

为了在个人电脑上练习此考试，你可以搭建RHCSA实验环境。主要有两种方法：

![](img/07ba9fc813a9ae4e4070e5b426af3e39_3.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_4.png)

*   **自动方法**：使用Vagrant工具自动部署。我已经提供了一个详细的视频教程，你可以轻松地在PC上搭建RHCSA 8实验室。
*   **手动方法**：手动创建虚拟机并配置所有设置，如静态IP、DNS和其他服务器。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_6.png)

你可以选择其中任何一种方法搭建练习环境。由于我们已经使用Vagrant搭建好了实验环境，所有服务都已配置完毕。以下是服务器的详细信息。

实验环境包含三台虚拟机：`server1`、`server2`和`master repo server`。在某些情况下，如果你的C盘空间不足，可能只会创建两台机器，但这并不影响考试，你可以在那两台机器上完成所有任务。如果你想创建所有机器，请确保有足够的磁盘空间，然后再次在PowerShell中执行`vagrant up`命令，它将自动部署包括所有服务在内的每台机器。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_8.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_10.png)

现在，让我们看一下模拟试卷。这是RHCSA 8考试的样题，请记住，在执行考试任务时，所有操作都应在防火墙和SELinux启用的环境下进行，并且你的服务器配置必须能在重启后依然生效。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_12.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_14.png)

事不宜迟，让我们开始考试。我们将启动虚拟机，登录到`server1`，并同时启动`master repo server`。

## 考题1：中断启动过程并重置root密码

![](img/07ba9fc813a9ae4e4070e5b426af3e39_16.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_17.png)

第一个任务是中断启动过程，重置root密码，并将其改为`redhat`以获取系统访问权限。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_19.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_21.png)

我们将操作`server1`。可以直接重启机器。当GRUB菜单出现时，在第一行按键盘上的`E`键进行编辑。然后，找到内核参数行，移除`rhgb quiet`，并添加`rd.break enforcing=0`。这样机器将以单用户模式启动，并且由于设置了`enforcing=0`，SELinux将处于宽容模式，之后我们无需重新标记文件。接着，按`Ctrl+X`启动。

系统将提供一个可以执行命令的`switch_root`提示符。在此处，我们执行以下命令重新挂载根文件系统为读写模式：
```bash
mount -o remount,rw /sysroot
```
然后切换根目录：
```bash
chroot /sysroot
```
现在，我们已处于root用户账户，无需密码即可直接执行`passwd`命令更改root密码，新密码设为`redhat`：
```bash
passwd root
```
更改完成后，退出`chroot`环境并重启，即可使用新密码`redhat`以root用户登录。另一台机器如需操作，步骤相同。

登录后，我们可以切换到外部终端（如MobaXterm）以获得更好的命令操作视图。`server1`的IP地址是`192.168.55.150`。使用`su -`命令切换到root用户。至此，第一个问题完成。

## 考题2：配置软件仓库

![](img/07ba9fc813a9ae4e4070e5b426af3e39_23.png)

接下来，我们需要配置软件仓库。仓库位于`repo server`上，以下是URL地址。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_25.png)

首先检查当前机器是否已配置仓库。执行`dnf repolist`命令，或查看`/etc/yum.repos.d/`目录，可以看到没有仓库配置文件。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_27.png)

有两种方法配置仓库：手动创建文件并填写内容，或使用`dnf config-manager`命令。我们将使用命令方式，因为它更简便。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_29.png)

执行以下命令添加基础OS仓库和AppStream仓库：
```bash
dnf config-manager --add-repo http://repo-server/BaseOS
dnf config-manager --add-repo http://repo-server/AppStream
```
执行`ls /etc/yum.repos.d/`命令，现在可以看到两个仓库文件。执行`yum repolist all`命令，可以确认仓库已成功启用。我们可以在另一台机器上重复此过程。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_31.png)

## 考题3：配置系统时间与NTP同步

![](img/07ba9fc813a9ae4e4070e5b426af3e39_33.png)

第三个任务是设置系统时区为你所在的时区，并确保配置了NTP同步。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_35.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_37.png)

执行`timedatectl`命令查看当前状态。NTP已激活，时钟也已同步。但我们需要根据要求手动检查并确认所有设置正确。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_39.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_41.png)

首先设置本地时区（例如亚洲/加尔各答）：
```bash
timedatectl set-timezone Asia/Kolkata
```
然后确保NTP启用：
```bash
timedatectl set-ntp yes
```
接着检查`chrony`包是否已安装：
```bash
rpm -qa | grep chrony
```
或使用`dnf info chrony`。该包已安装。验证`chrony`服务状态：
```bash
systemctl status chronyd.service
```
服务运行正常。我们还可以验证`/etc/chrony.conf`文件是否配置了NTP服务器。一切配置正确。

再次执行`timedatectl`命令验证：时区已更新，时钟已同步，NTP处于活动状态。所有设置无误，可以继续。

## 考题4：验证网络配置

![](img/07ba9fc813a9ae4e4070e5b426af3e39_43.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_45.png)

第四个任务是验证`server1`使用的网络IP、DNS和网关设置是否与上述说明一致，如果不一致，请进行必要的更正。

根据说明，`server1`的IP地址、DNS和网关信息已给出。我们使用`ifconfig`命令检查网络适配器。这里有两个适配器：`eth0`和`eth1`。`eth1`是活动适配器。

查看其配置文件：
```bash
cat /etc/sysconfig/network-scripts/ifcfg-eth1
```
文件中的IP地址、DNS等信息与文档要求一致。因此，无需任何更改，可以继续。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_47.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_49.png)

## 考题5：添加辅助IP地址

![](img/07ba9fc813a9ae4e4070e5b426af3e39_51.png)

第五个任务是为当前运行的接口静态添加以下辅助IP地址，且添加方式不得影响现有设置。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_53.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_55.png)

我们需要将指定的IPv4和IPv6地址添加到活动接口`eth1`上。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_57.png)

首先查看连接详情：
```bash
nmcli connection show
```
`eth1`是活动连接。现在添加IPv4地址：
```bash
nmcli connection modify "System eth1" +ipv4.addresses 192.168.55.151/24
```
重新加载连接使更改生效：
```bash
nmcli connection reload
```
再次检查连接详情，确认IPv4地址已添加：
```bash
nmcli connection show "System eth1"
```
接下来添加IPv6地址：
```bash
nmcli connection modify "System eth1" ipv6.method manual +ipv6.addresses 2001:db8:0:1::c000:207/64
```
重新加载连接并验证：
```bash
nmcli connection reload
nmcli connection show "System eth1"
```
可以看到IPv6地址已成功设置。我们已成功在机器上配置了IPv4和IPv6辅助地址。

## 考题6：启用IP包转发

第六个任务是在`server1`上启用数据包转发，并且此设置必须在重启后持久生效。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_59.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_61.png)

我们在`server1`上操作。要启用IP转发，可以编辑`/etc/sysctl.conf`文件，添加以下行：
```bash
net.ipv4.ip_forward = 1
```
保存文件。检查当前IP转发是否已启用：
```bash
cat /proc/sys/net/ipv4/ip_forward
```
显示为`0`，表示之前未启用。由于我们已在配置文件中添加了条目，系统重启后会读取该文件。我们也可以使用`sysctl -p`命令立即生效，但为了确保设置能经受重启，我们选择重启机器。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_63.png)

重启后，再次切换为root用户，检查IP转发是否成功启用：
```bash
cat /proc/sys/net/ipv4/ip_forward
```
现在显示为`1`，表示已成功启用。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_65.png)

## 考题7：配置默认启动目标与启动消息

![](img/07ba9fc813a9ae4e4070e5b426af3e39_67.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_69.png)

第七个任务是服务器应默认启动到`multi-user`目标，并且启动消息应显示，不应被静默。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_71.png)

首先，设置默认启动目标为`multi-user.target`：
```bash
systemctl set-default multi-user.target
```
验证设置：
```bash
systemctl get-default
```
成功将`multi-user.target`设置为默认运行级别。接下来，启用启动消息。编辑`/etc/default/grub`文件，找到以`GRUB_CMDLINE_LINUX`开头的行，移除行尾的`rhgb quiet`参数。保存文件。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_73.png)

由于修改了GRUB配置，需要重新生成`grub.cfg`文件：
```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
```
现在重启机器，检查是否能看见启动消息。机器启动时，我们可以看到启动消息。同时，验证机器是否启动到`multi-user`目标模式。登录后执行：
```bash
systemctl get-default
```
确认启动模式为`multi-user.target`。

## 考题8：创建卷组

第八个任务是创建一个名为`vg_exam`、大小为3GB的新卷组。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_75.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_77.png)

登录到机器，检查是否有额外的磁盘可用。执行`lsblk`命令，可以看到`sdb`和`sdc`两块磁盘。我们可以使用其中一块（例如`sdb`）来创建卷组。

首先，使用`fdisk`在`sdb`上创建分区：
```bash
fdisk /dev/sdb
```
输入`n`创建新主分区，分区号设为`1`，设置大小为`+3G`。然后输入`t`更改分区类型为`Linux LVM`（代码`8e`）。最后输入`w`写入更改。

执行`partprobe`让内核识别分区变化。现在，使用这个分区创建物理卷：
```bash
pvcreate /dev/sdb1
```
使用`pvs`命令查看物理卷详情。接着，创建名为`vg_exam`的卷组：
```bash
vgcreate vg_exam /dev/sdb1
```
使用`vgs`命令确认已成功创建大小为3GB的`vg_exam`卷组，在`pvs`输出中也能看到相应条目。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_79.png)

## 考题9：创建逻辑卷

![](img/07ba9fc813a9ae4e4070e5b426af3e39_81.png)

第九个任务是在`vg_exam`卷组上创建一个名为`lv_exam`、大小为1GB的新逻辑卷。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_83.png)

在终端执行`lvcreate`命令：
```bash
lvcreate -L 1G -n lv_exam vg_exam
```
逻辑卷创建成功。使用`lvs`命令查看详情：`lv_exam`逻辑卷已在`vg_exam`卷组上创建。`lsblk`命令也能显示其层次结构：磁盘`sdb`、物理卷`sdb1`、卷组`vg_exam`和逻辑卷`lv_exam`。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_85.png)

## 考题10：格式化并挂载逻辑卷

第十个任务是将`lv_exam`逻辑卷格式化为XFS文件系统，并将其持久挂载到`/mnt/lv_exam`目录。

首先，使用`mkfs.xfs`命令将其格式化为XFS：
```bash
mkfs.xfs /dev/vg_exam/lv_exam
```
格式化后，使用`lsblk -f`查看文件系统类型和UUID。接下来，创建挂载点目录：
```bash
mkdir /mnt/lv_exam
```
为了持久挂载，需要在`/etc/fstab`文件中添加条目。使用文件系统的UUID进行添加：
```bash
UUID=<你的UUID> /mnt/lv_exam xfs defaults 0 0
```
保存文件。现在，执行`mount -a`命令挂载`/etc/fstab`中所有未挂载的文件系统。使用`df -h`命令检查，可以看到逻辑卷已成功挂载到`/mnt/lv_exam`，且配置能经受系统重启。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_87.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_89.png)

## 考题11：扩展XFS文件系统

第十一个任务是将`lv_exam`上的XFS文件系统扩展1GB。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_91.png)

首先检查卷组中的可用空间。`vg_exam`卷组有2GB可用空间。当前`lv_exam`大小为1GB，我们需要将其扩展到2GB，卷组空间充足。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_93.png)

扩展逻辑卷大小：
```bash
lvextend -L +1G /dev/vg_exam/lv_exam
```
使用`lvs`查看，逻辑卷已显示为2GB。但`df -h`输出仍显示为1GB，因为文件系统尚未扩展。执行`xfs_growfs`命令扩展文件系统：
```bash
xfs_growfs /mnt/lv_exam
```
再次执行`df -h`，现在大小显示为2GB。此问题完成。

## 考题12：创建精简配置卷

![](img/07ba9fc813a9ae4e4070e5b426af3e39_95.png)

第十二个任务是创建一个4TB的精简配置卷。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_97.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_99.png)

检查额外磁盘`sdc`。发现有一个目录挂载在`sdc`上，先卸载它：
```bash
umount /dev/sdc
```
再次检查`lsblk`，磁盘可用。查看`/dev/sdc`详情，没有分区。我们可以使用它创建精简配置卷。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_101.png)

首先安装所需软件包：
```bash
dnf install -y vdo kmod-kvdo
```
包安装完成后，执行`vdo create`命令创建名为`vdo1`、大小为4TB的精简配置卷：
```bash
vdo create --name vdo1 --device /dev/sdc --vdoLogicalSize 4T --writePolicy auto --force
```
创建成功。使用`fdisk -l /dev/mapper/vdo1`查看详情，可以看到4TB的精简配置卷。`lsblk`命令也能显示其信息。

## 考题13：配置基础Web服务器

![](img/07ba9fc813a9ae4e4070e5b426af3e39_103.png)

第十三个任务是创建一个基础Web服务器，在连接时显示“Welcome to Neha Classes”。确保防火墙允许HTTP和HTTPS服务。

首先安装`httpd`包：
```bash
dnf install -y httpd
```
启动`httpd`服务：
```bash
systemctl start httpd
```
设置服务开机自启：
```bash
systemctl enable httpd
```
验证服务状态：
```bash
systemctl status httpd
```
服务已启用并正常运行。接下来，在`/var/www/html/`目录下创建`index.html`文件，并写入内容“Welcome to Neha Classes”。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_105.png)

检查防火墙规则，发现HTTP和HTTPS服务未允许。添加这些服务到防火墙：
```bash
firewall-cmd --permanent --add-service=http --add-service=https
```
重新加载防火墙配置使规则生效：
```bash
firewall-cmd --reload
```
再次列出所有防火墙规则，确认HTTP和HTTPS服务已添加。

现在测试Web服务器功能。使用`curl localhost`或`wget localhost`命令，可以获取到“Welcome to Neha Classes”页面。在浏览器中输入服务器IP地址`192.168.55.151`，也能成功访问该页面。Web服务器已按要求配置完成。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_107.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_109.png)

## 考题14：查找并复制大文件

第十四个任务是查找`/etc`目录中所有大于4MB的文件，并将它们复制到`/find/large_files`。

首先创建目录：
```bash
mkdir /find
```
使用`find`命令查找`/etc`目录中大于4MB的文件：
```bash
find /etc -type f -size +4M
```
找到两个文件。我们可以再次使用`find`命令，并将输出重定向到`/find/large_files`文件：
```bash
find /etc -type f -size +4M > /find/large_files
```
使用`cat /find/large_files`查看文件内容，确认已记录符合条件的文件路径。此问题完成。

## 考题15：编写Shell脚本

![](img/07ba9fc813a9ae4e4070e5b426af3e39_111.png)

第十五个任务是编写一个Shell脚本程序。创建一个名为`neha_classes.sh`的脚本在根目录下。该脚本根据不同的参数打印不同的输出。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_113.png)

创建脚本文件并编辑：
```bash
vi /neha_classes.sh
```
脚本内容如下：
```bash
#!/bin/bash
if [ "$1" = "neha classes" ]; then
    echo "neha classes are awesome"
elif [ "$1" = "subscribers" ]; then
    echo "our subscribers are great"
else
    echo "please use ./neha_classes.sh then define the argument to get the output"
fi
```
保存文件后，为其添加执行权限：
```bash
chmod 700 /neha_classes.sh
```
现在测试脚本：
*   不带参数执行：打印预设的提示信息。
*   带参数`"neha classes"`执行：打印“neha classes are awesome”。
*   带参数`"subscribers"`执行：打印“our subscribers are great”。
*   带其他参数（如`"linux"`）执行：打印预设的提示信息。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_115.png)

脚本在所有条件下均能按预期工作。此问题完成。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_117.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_119.png)

## 考题16：创建用户与组

![](img/07ba9fc813a9ae4e4070e5b426af3e39_121.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_123.png)

第十六个任务是创建四个用户：`vikas`、`harsh`、`john`和`andrew`。需满足以下条件：所有新用户的家目录中在账户创建后应有一个名为`welcome`的文件；用户密码应在45天后过期，且至少8个字符；`vikas`和`harsh`应属于`indian`组；`john`和`andrew`应属于`uk`组。

首先，在`/etc/skel`目录下创建一个文件，该目录下的文件会在创建新用户时自动复制到其家目录。
```bash
echo "Welcome to Neha classes" > /etc/skel/welcome
```
接着，在`/etc/login.defs`文件中定义密码策略。找到`PASS_MAX_DAYS`和`PASS_MIN_LEN`选项，分别设置为`45`和`8`。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_125.png)

现在创建用户账户并设置密码：
```bash
useradd vikas && passwd vikas
useradd harsh && passwd harsh
useradd john && passwd john
useradd andrew && passwd andrew
```
验证用户已添加。检查用户家目录，确认`welcome`文件已存在。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_127.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_129.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_131.png)

接下来处理组。检查`/etc/group`文件，发现`indian`和`uk`组不存在。先创建它们：
```bash
groupadd indian
groupadd uk
```
将用户添加到相应组：
```bash
usermod -aG indian vikas
usermod -aG indian harsh
usermod -aG uk john
usermod -aG uk andrew
```
使用`id`命令验证用户组关系，或查看`/etc/group`文件确认。用户和组已按要求设置完成。

## 考题17：配置目录权限（Indian组）

第十七个任务是只有`indian`组的成员才能访问`/indian`目录。使`vikas`成为该目录的所有者，并使`indian`组成为该目录的属组。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_133.png)

首先创建目录：
```bash
mkdir /indian
```
更改所有者和属组：
```bash
chown vikas:indian /indian
```
检查目录详情，`vikas`是所有者，`indian`是属组。根据要求，只有`indian`组成员应有访问权限。查看当前权限，所有者和组有读写执行权，其他用户只有读权限。我们需要更改权限，使组有完全访问权，其他用户无权限：
```bash
chmod 770 /indian
```
再次验证权限，符合要求。此问题完成。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_135.png)

## 考题18：配置目录权限（UK组）

![](img/07ba9fc813a9ae4e4070e5b426af3e39_137.png)

第十八个任务是只有`uk`组的成员才能访问`/uk`目录。使`john`成为该目录的所有者，并使`uk`组成为该目录的属组。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_139.png)

创建目录：
```bash
mkdir /uk
```
设置权限，使所有者和组有完全访问权，其他用户无权限：
```bash
chmod 770 /uk
```
更改所有者和属组：
```bash
chown john:uk /uk
```
验证权限和所有权，`john`是所有者，`uk`是属组。此问题完成。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_141.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_143.png)

## 考题19：设置特殊权限位

![](img/07ba9fc813a9ae4e4070e5b426af3e39_145.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_147.png)

第十九个任务是新创建的文件应归目录的属组所有，并且只有文件创建者才能删除自己的文件。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_149.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_151.png)

首先，我们需要设置`setgid`位，以确保新文件继承目录的属组。检查`/indian`和`/uk`目录当前无特殊权限。为它们设置`setgid`：
```bash
chmod g+s /indian
chmod g+s /uk
```
验证权限，`setgid`已设置。

其次，需要设置`sticky`位，以确保只有文件所有者能删除文件。为两个目录设置`sticky`位：
```bash
chmod +t /indian
chmod +t /uk
```
验证权限，`sticky`位已设置。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_153.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_155.png)

现在进行测试。切换到`vikas`用户，进入`/indian`目录创建一个文件。检查文件权限，所有者是`vikas`，属组是`indian`（继承了父目录的属组，这得益于`setgid`位）。切换到`harsh`用户，尝试删除`vikas`创建的文件，操作被拒绝（得益于`sticky`位）。`harsh`自己创建文件，其属组也是`indian`。所有条件满足，此问题完成。

## 考题20：创建Cron作业

最后一个任务是创建一个cron作业，将句子“The practical exam was very easy and I am ready to clear the RHCSA certification thanks Neha”附加到`/var/log/messages`文件中，仅在工作日（周一到周五）下午1点执行。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_157.png)

首先检查系统当前时间。使用`crontab -e`命令编辑root用户的cron表。添加以下行：
```bash
0 13 * * 1-5 echo "The practical exam was very easy and I am ready to clear the RHCSA certification thanks Neha" >> /var/log/messages
```
这表示在每周一到周五（1-5）的13:00（下午1点）执行该命令。保存crontab。

![](img/07ba9fc813a9ae4e4070e5b426af3e39_159.png)

等待任务执行时间（接近下午1点）。检查cron日志`/var/log/cron`，可以看到作业已成功执行的记录。同时，查看crontab条目确认。此作业将在工作日下午1点执行。

## 总结

![](img/07ba9fc813a9ae4e4070e5b426af3e39_161.png)

![](img/07ba9fc813a9ae4e4070e5b426af3e39_163.png)

在本节课中，我们一起完成了RHCSA 8模拟考试的全部20个任务。我们从系统启动修复、网络配置、存储管理（LVM、VDO）、服务部署（Web服务器）、用户与权限管理，到自动化任务（Shell脚本、Cron作业）进行了全面的实战演练。每个步骤都确保了配置的持久性和安全性（防火墙、SELinux），严格遵循了红帽认证系统管理员的标准操作流程。通过这次练习，你应该对RHCSA考试所要求的核心技能有了更深入的理解和掌握。