# 红帽RHCE 8.0培训：02：红帽认证和RHEL8系统安装 🚀

![](img/f0be760e5b33e8e6fde57f44b8ccb555_1.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_3.png)

在本节课中，我们将要学习红帽认证体系的基本知识，并掌握如何在虚拟机中安装红帽企业版Linux 8操作系统。课程内容涵盖红帽认证的等级、考试流程以及详细的系统安装步骤。

## 红帽认证体系介绍

上一节我们介绍了Linux运维工程师的职责和前景，本节中我们来看看红帽公司的认证体系。红帽认证分为三个等级。

以下是三个认证等级：
*   **RHCSA**：红帽认证系统管理员。主要负责Linux系统的安装、基础配置、网络设置和安全性维护。
*   **RHCE**：红帽认证工程师。这是本课程的目标，主要负责Linux服务器的维护、网络设备管理、网络安全以及实际业务故障的诊断与处理。
*   **RHCA**：红帽认证架构师。这是最高级别，负责从数据中心到桌面终端的Linux系统设计、规划、部署和全面管理，常被称为“抢救工程师”。

![](img/f0be760e5b33e8e6fde57f44b8ccb555_5.png)

红帽认证采用金字塔式的层级架构。要考取RHCE，必须先通过RHCSA考试。而要考取RHCA，则必须先获得RHCE认证。

![](img/f0be760e5b33e8e6fde57f44b8ccb555_7.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_9.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_11.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_13.png)

## RHCE考试详情

了解了认证体系后，我们来看看RHCE考试的具体细节。

![](img/f0be760e5b33e8e6fde57f44b8ccb555_15.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_17.png)

以下是关于RHCE考试的关键信息：
*   **考试形式**：RHCE考试包含两门，需在一天内完成。上午考RHCSA（2.5小时），下午考RHCE（3小时）。所有考试均为上机实操，没有选择题。
*   **成绩与证书**：成绩通常在考后3个工作日内通过邮件发出。若两门均通过，将获得RHCSA和RHCE两个证书。若仅通过下午的RHCE而未通过上午的RHCSA，则无法获得任何证书。若仅通过上午的RHCSA，则可获得该系统管理员证书。
*   **考试语言**：考试界面为中文。
*   **有效期与续期**：RHCE证书有效期为3年。到期前，可以通过参加并通过一门RHCA的考试来刷新RHCE的有效期。RHCA本身由5门独立的考试组成，获得5个证书后可合成RHCA架构师认证。RHCA的每个子证书也需在3年内通过考取新的CA方向证书来刷新，以维持RHCA身份的有效性。

![](img/f0be760e5b33e8e6fde57f44b8ccb555_19.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_21.png)

## 实验环境准备

在开始安装系统前，我们需要准备好实验环境。我们将使用虚拟机软件来模拟计算机硬件。

我们主要需要三个文件：
1.  **`RHEL8.0-x86_64-dvd.iso`**：红帽企业级操作系统的安装镜像文件。
2.  **`rht-lab-XXXX.vmdk`**：红帽练习环境的虚拟磁盘文件（本阶段暂不需要）。
3.  **`satup.iso`**：红帽练习环境的安装工具（本阶段暂不需要）。

我们将使用 **VMware Workstation Pro** 软件来创建虚拟机。该软件可以虚拟出CPU、内存、硬盘、网卡等硬件，我们称之为“虚拟机”。操作系统将安装在这个虚拟的计算机中。

![](img/f0be760e5b33e8e6fde57f44b8ccb555_23.png)

## 创建虚拟机

![](img/f0be760e5b33e8e6fde57f44b8ccb555_25.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_27.png)

现在，我们开始创建一台用于安装RHEL 8的虚拟机。

![](img/f0be760e5b33e8e6fde57f44b8ccb555_29.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_31.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_33.png)

以下是创建虚拟机的关键步骤：
1.  打开VMware Workstation，选择“文件”->“新建虚拟机”。
2.  选择“自定义（高级）”配置。
3.  硬件兼容性选择“Workstation 15.x”。
4.  选择“稍后安装操作系统”。
5.  客户机操作系统选择“Linux”，版本选择“Red Hat Enterprise Linux 8 64位”。
6.  为虚拟机命名（如`rhel8`），并选择将其存储在固态硬盘上以获得更好性能。
7.  处理器数量保持默认（如1个处理器，1个内核）。
8.  内存大小使用推荐值（如2GB）。
9.  网络连接选择“使用网络地址转换（NAT）”。
10. I/O控制器和磁盘类型保持默认推荐（如NVMe）。
11. 选择“创建新虚拟磁盘”，大小使用推荐的20GB。**切勿勾选“立即分配所有磁盘空间”**。磁盘存储建议选择“单个文件”。
12. 指定磁盘文件名，然后完成创建。

![](img/f0be760e5b33e8e6fde57f44b8ccb555_35.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_37.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_39.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_41.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_43.png)

## 安装RHEL 8操作系统

![](img/f0be760e5b33e8e6fde57f44b8ccb555_45.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_47.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_49.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_51.png)

虚拟机创建完成后，我们开始安装操作系统。

![](img/f0be760e5b33e8e6fde57f44b8ccb555_53.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_55.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_57.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_59.png)

首先，编辑虚拟机设置，在“CD/DVD”选项中，选择使用ISO映像文件，并指向下载好的`RHEL8.0-x86_64-dvd.iso`文件，确保勾选“启动时连接”。然后启动虚拟机。

![](img/f0be760e5b33e8e6fde57f44b8ccb555_61.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_63.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_65.png)

启动后，会进入安装引导界面。选择“Test this media & install Red Hat Enterprise Linux 8.0.0”（测试并安装），按`ESC`键可跳过介质检测，直接进入安装界面。

![](img/f0be760e5b33e8e6fde57f44b8ccb555_67.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_69.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_71.png)

安装界面分为三排设置项，以下是核心配置步骤：

![](img/f0be760e5b33e8e6fde57f44b8ccb555_73.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_75.png)

**第一排设置（基础配置）**
*   **语言**：选择“English (United States)”。这是安装过程显示的语言。
*   **键盘**：保持默认的“English (US)”。
*   **安装源**：保持默认，即从本地光盘安装。
*   **软件选择**：选择“Server with GUI”，以安装带图形界面的操作系统。
*   **安装目标**：即磁盘分区，**选择“Custom”进行手动分区**。
    *   在分区方案中，选择“Standard Partition”（标准分区）。
    *   点击“+”号添加挂载点。**必须创建的分区有**：
        *   `/boot`：启动分区，分配 **200MB**。
        *   `swap`：交换分区，分配 **2GB**。
        *   `/`：根分区，使用剩余所有空间（约12.8GB）。
    *   （可选）可以添加`/home`分区，例如分配5GB，用于存储用户数据。
    *   文件系统类型保持默认的`xfs`。点击“Done”并接受更改。

![](img/f0be760e5b33e8e6fde57f44b8ccb555_77.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_79.png)

**第二排设置（系统配置）**
*   **语言**：再次选择“English (United States)”。这是系统安装完成后的默认语言。
*   **时间和日期**：选择“Asia/Shanghai”时区，并设置正确时间。
*   **网络和主机名**：
    *   打开网络连接开关（如`ens160`网卡）。
    *   在主机名处输入名称，如`example.com`，点击“Apply”。
*   **安全策略**：可以暂时不启用，直接跳过。
*   **系统用途**：此为新功能，用于系统调优。可根据角色选择（如服务器），或保持默认。

![](img/f0be760e5b33e8e6fde57f44b8ccb555_81.png)

**第三排设置（用户配置）**
完成上述设置后，点击右下角的“Begin Installation”开始安装。在安装过程中，需要设置密码：
*   **Root密码**：设置root管理员密码（如`redhat`）。如果密码过于简单，需要点击两次“Done”确认。
*   **创建用户**：创建一个普通用户（如`user1`），并设置密码。建议保持其“普通用户”身份。

设置完成后，等待系统安装完毕，点击“Reboot”重启即可进入全新的RHEL 8系统。

![](img/f0be760e5b33e8e6fde57f44b8ccb555_83.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_85.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_87.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_89.png)

## 总结

![](img/f0be760e5b33e8e6fde57f44b8ccb555_91.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_93.png)

![](img/f0be760e5b33e8e6fde57f44b8ccb555_95.png)

本节课中我们一起学习了红帽认证的三大等级（RHCSA、RHCE、RHCA）及其考试与续期规则。随后，我们详细演练了如何在VMware虚拟机中手动安装红帽企业版Linux 8操作系统，涵盖了从创建虚拟机、配置安装参数（特别是手动分区）到设置用户密码的完整流程。掌握这些是成为一名合格Linux系统工程师的第一步。