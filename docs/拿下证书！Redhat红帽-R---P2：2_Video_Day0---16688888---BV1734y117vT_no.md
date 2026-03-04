# Redhat红帽 RHCE8.0认证体系课程：P2：课程开篇及RHEL8.0安装

![](img/833093b66c4c45a5fb7bee08ba710d0d_0.png)

## 概述
在本节课中，我们将学习红帽认证体系（RHCE 8.0）的课程开篇内容，并掌握如何安装Red Hat Enterprise Linux 8.0操作系统。课程将从基础概念讲起，逐步引导初学者完成系统安装和初始配置。

## 讲师介绍与课程目标
我叫夏若轩，是腾科的专职讲师。我持有HCIP认证，目前正在考取更高级别的认证。我的工作经历包括在唯品会、新一代数据中心、东方思维和广州地铁担任运维工程师，主要专注于服务器、存储、数据库和操作系统等基础架构领域。

本课程面向零基础学员，将从最简单的虚拟机安装、命令使用、VI编辑器开始，逐步深入到各种系统管理模块。课程结合了书本外的实用内容，如链路聚合和Web服务（Apache）配置。我们的目标是帮助大家顺利通过红帽认证，并将所学知识与实际工作需求相结合。

![](img/833093b66c4c45a5fb7bee08ba710d0d_2.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_3.png)

## Linux系统的重要性
在IT领域，Linux系统广泛应用于服务器、云计算、自动化运维和大数据等方向。与Windows系统相比，Linux更加稳定、高效，且开源免费，因此在生产环境中被大量采用。特别是在当前国产化和自动化云方向的发展趋势下，掌握Linux技能显得尤为重要。

红帽认证体系从RHCE 8.0开始，更加注重自动化运维能力的考察，这与行业发展趋势相符。学习Linux不仅有助于通过认证，更能为未来的职业发展打下坚实基础。

## 红帽认证体系与考试须知
红帽认证体系包括RHCSA（系统管理员）和RHCE（工程师）等多个级别。RHCE 8.0的考试内容主要针对系统管理员，强调Ansible自动化运维和Shell脚本能力。

考试形式为100%实操，没有选择题或判断题。考生需要在虚拟机环境中完成一系列任务，考试结束后系统会自动评分。考试成绩达到210分（满分300分）即为通过。

考试注意事项：
*   考试时需携带身份证，可自带鼠标。
*   所有操作必须在虚拟机中完成，切勿改动物理机。
*   考试结束后，红帽会通过邮件发送成绩和证书。

## 课程学习建议
为了确保学习效果，建议大家：
*   课堂认真听讲，做好笔记。
*   积极动手操作，不要只看不练。
*   课后多做练习，熟练掌握各项技能。
*   保持耐心和信心，Linux学习是一个循序渐进的过程。

## 安装Red Hat Enterprise Linux 8.0
上一节我们介绍了课程的基本情况和学习目标，本节中我们来看看如何安装RHEL 8.0操作系统。我们将使用VMware Workstation创建虚拟机并完成系统安装。

![](img/833093b66c4c45a5fb7bee08ba710d0d_5.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_7.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_8.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_9.png)

### 创建虚拟机
以下是创建虚拟机的步骤：

1.  打开VMware Workstation，点击“文件” -> “新建虚拟机”。
2.  选择“自定义（高级）”配置，点击“下一步”。
3.  虚拟机硬件兼容性选择“Workstation 15.x”，点击“下一步”。
4.  选择“稍后安装操作系统”，点击“下一步”。
5.  客户机操作系统选择“Linux”，版本选择“Red Hat Enterprise Linux 8 64位”，点击“下一步”。
6.  为虚拟机命名（例如：RHEL8-ServerA），并选择保存位置，点击“下一步”。
7.  处理器配置：数量1，每个处理器的核心数量2，点击“下一步”。
8.  内存配置：建议至少2048 MB（2GB），点击“下一步”。
9.  网络类型：选择“使用网络地址转换（NAT）”，点击“下一步”。
10. I/O控制器类型：选择默认的“LSI Logic”，点击“下一步”。
11. 磁盘类型：选择“NVMe”，点击“下一步”。
12. 选择“创建新虚拟磁盘”，点击“下一步”。
13. 磁盘容量：建议20GB，选择“将虚拟磁盘拆分成多个文件”，点击“下一步”。
14. 指定磁盘文件名称，点击“下一步”。
15. 点击“自定义硬件”，在“处理器”选项中勾选“虚拟化Intel VT-x/EPT或AMD-V/RVI”，在“新CD/DVD”选项中选择“使用ISO映像文件”，并指定RHEL 8.0的ISO文件路径，点击“关闭”。
16. 点击“完成”，虚拟机创建完毕。

![](img/833093b66c4c45a5fb7bee08ba710d0d_11.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_13.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_15.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_16.png)

### 安装操作系统
虚拟机创建完成后，启动虚拟机并开始安装操作系统：

![](img/833093b66c4c45a5fb7bee08ba710d0d_18.png)

1.  启动虚拟机，在引导界面选择第一项“Install Red Hat Enterprise Linux 8.0”，按回车键。
2.  选择安装语言（建议选择英文），点击“Continue”。
3.  在“INSTALLATION SUMMARY”界面，进行以下配置：
    *   **SOFTWARE SELECTION**：选择“Server with GUI”（带图形界面的服务器）。
    *   **INSTALLATION DESTINATION**：选择自动分区，点击“Done”。
    *   其他选项如网络和主机名、时间和日期等可暂时保持默认，后续再配置。
4.  点击“Begin Installation”开始安装。
5.  安装过程中，设置root用户密码（例如：redhat）并创建普通用户（例如：student，密码：student）。
6.  安装完成后，点击“Reboot”重启系统。
7.  首次启动时，接受许可协议，完成初始配置。

![](img/833093b66c4c45a5fb7bee08ba710d0d_20.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_22.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_24.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_26.png)

### 配置网络与主机名
系统安装完成后，我们需要配置网络和主机名，以便虚拟机能够正常通信。

以下是配置主机名的命令：
```bash
hostnamectl set-hostname serverA.lab.example.com
```

![](img/833093b66c4c45a5fb7bee08ba710d0d_28.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_29.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_31.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_33.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_35.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_37.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_39.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_41.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_43.png)

配置网络需要编辑网卡配置文件。首先查看网卡名称：
```bash
nmcli device status
```

![](img/833093b66c4c45a5fb7bee08ba710d0d_45.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_47.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_49.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_51.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_53.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_54.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_56.png)

通常网卡名称为`ens160`或类似。编辑其配置文件：
```bash
vim /etc/sysconfig/network-scripts/ifcfg-ens160
```

![](img/833093b66c4c45a5fb7bee08ba710d0d_58.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_60.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_62.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_64.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_66.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_68.png)

配置文件内容示例如下：
```bash
TYPE=Ethernet
BOOTPROTO=static
NAME=ens160
DEVICE=ens160
ONBOOT=yes
IPADDR=192.168.123.101
PREFIX=24
GATEWAY=192.168.123.2
DNS1=114.114.114.114
```

保存并退出后，重新加载网络配置并重启网卡：
```bash
nmcli connection reload
nmcli connection down ens160
nmcli connection up ens160
```

![](img/833093b66c4c45a5fb7bee08ba710d0d_70.png)

使用`ping`命令测试网络连通性：
```bash
ping 192.168.123.2
```

![](img/833093b66c4c45a5fb7bee08ba710d0d_72.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_74.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_76.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_78.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_80.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_82.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_84.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_86.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_88.png)

### 克隆虚拟机
为了节省时间，我们可以将配置好的虚拟机克隆一份，作为第二台机器（例如：serverB）。在VMware中右键点击虚拟机，选择“管理” -> “克隆”，按照向导完成克隆操作。克隆完成后，修改第二台虚拟机的主机名和IP地址即可。

![](img/833093b66c4c45a5fb7bee08ba710d0d_90.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_92.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_94.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_96.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_98.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_100.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_102.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_104.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_106.png)

![](img/833093b66c4c45a5fb7bee08ba710d0d_108.png)

## 总结
本节课我们一起学习了红帽RHCE 8.0认证体系的开篇内容，包括Linux系统的重要性、红帽认证体系介绍以及RHEL 8.0操作系统的安装与初始配置。通过动手实践，我们成功创建了虚拟机并安装了系统，为后续的深入学习打下了基础。下节课我们将开始学习Linux的基本命令和操作。