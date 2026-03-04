# RHCE认证培训：P3：网络聚合、软件包管理与进程管理

在本节课中，我们将要学习Linux系统中的几个核心管理技能：网络链路聚合的配置、软件包管理（包括YUM源配置和RPM包操作）以及进程的查看与管理。这些是系统管理员日常工作中必须掌握的基础操作。

![](img/2e51dfc46cfb10338a95ab4c377c733a_1.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_2.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_3.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_4.png)

## 网络链路聚合配置回顾与排错

上一节我们介绍了链路聚合的基本概念和配置命令。本节中我们来看看在配置过程中可能遇到的问题及其解决方法。

在配置链路聚合时，一个常见的错误是添加的网卡显示为“未连接”状态。首先，你需要检查链路聚合组的状态。

![](img/2e51dfc46cfb10338a95ab4c377c733a_6.png)

以下是检查链路聚合状态的命令：
```bash
teamdctl team0 state
```
此命令会显示`team0`聚合组中所有网卡的状态。你需要确保所有添加的网卡都处于`up`状态，并且其中一张被标记为主链路。

![](img/2e51dfc46cfb10338a95ab4c377c733a_8.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_10.png)

如果状态显示异常，可能是添加网卡（slave）到聚合组时出现了问题。此时，你需要检查并清理可能残留的错误配置文件。

![](img/2e51dfc46cfb10338a95ab4c377c733a_12.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_14.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_16.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_17.png)

以下是排查和清理的步骤：
1.  检查`/etc/sysconfig/network-scripts/`目录下是否存在类似`ifcfg-ens224-1`这样的冗余配置文件。
2.  使用`rm`命令删除这些错误的配置文件。
3.  重新执行创建聚合组和添加网卡的命令。

![](img/2e51dfc46cfb10338a95ab4c377c733a_19.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_21.png)

如果问题依然存在，最彻底的方法是移除虚拟机中的网卡，并删除所有相关的配置文件，然后从头开始配置。这能确保一个干净的实验环境。

![](img/2e51dfc46cfb10338a95ab4c377c733a_23.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_24.png)

## 软件包管理：YUM源配置

掌握了网络配置后，我们来看看如何管理系统中的软件。软件包管理是系统维护的核心，而配置正确的软件源（YUM Repository）是安装软件的第一步。

![](img/2e51dfc46cfb10338a95ab4c377c733a_26.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_27.png)

在RHEL 8中，配置YUM源有时会遇到一个特殊现象：直接使用`yum list`可能无法加载仓库列表，需要先尝试安装一个不存在的包来触发仓库加载。

![](img/2e51dfc46cfb10338a95ab4c377c733a_28.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_29.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_30.png)

配置YUM源主要有两种方式：使用本地镜像文件或配置网络源。

### 配置本地镜像源

![](img/2e51dfc46cfb10338a95ab4c377c733a_32.png)

对于无法连接互联网的环境，挂载系统安装镜像是最常用的方法。

![](img/2e51dfc46cfb10338a95ab4c377c733a_33.png)

以下是挂载镜像并配置本地源的完整步骤：
1.  在虚拟机设置中加载ISO镜像文件，并确保勾选“已连接”。
2.  在系统中创建一个挂载点目录，例如`/mnt/dvd`。
3.  使用`mount`命令将光驱设备（如`/dev/sr0`）挂载到该目录。
4.  将挂载信息写入`/etc/fstab`文件以实现开机自动挂载。
5.  在`/etc/yum.repos.d/`目录下创建`.repo`文件，并正确指向镜像中的`BaseOS`和`AppStream`目录。

### 配置网络源

![](img/2e51dfc46cfb10338a95ab4c377c733a_35.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_37.png)

对于可以访问互联网的系统，配置网络源更为便捷，可以获取更丰富的软件包。

![](img/2e51dfc46cfb10338a95ab4c377c733a_39.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_41.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_43.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_45.png)

以下是配置网络源（以阿里云Docker源为例）的步骤：
1.  在`/etc/yum.repos.d/`目录下创建`.repo`文件（如`docker.repo`）。
2.  编辑文件，填入仓库名称、地址（`baseurl`）、是否进行GPG检查（`gpgcheck=0`）以及是否启用（`enabled=1`）。

![](img/2e51dfc46cfb10338a95ab4c377c733a_47.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_48.png)

配置完成后，使用`yum repolist`命令验证源是否可用，然后就可以使用`yum install`安装软件了。

## 软件包管理：YUM与RPM命令

软件源配置好后，我们就可以使用包管理工具来安装、查询和卸载软件了。

![](img/2e51dfc46cfb10338a95ab4c377c733a_50.png)

最常用的工具是`yum`，它能够自动处理依赖关系。

以下是`yum`的常用命令：
*   **安装软件包**：`yum install -y 包名`
*   **卸载软件包**：`yum remove 包名`
*   **搜索软件包**：`yum search 关键字`
*   **列出已安装包**：`yum list installed`

有时我们需要直接操作`rpm`软件包文件，例如安装从官网下载的特定版本软件。

![](img/2e51dfc46cfb10338a95ab4c377c733a_52.png)

以下是`rpm`命令的常见用法：
*   **安装本地RPM包**：`rpm -ivh 包名.rpm`
*   **升级本地RPM包**：`rpm -Uvh 包名.rpm`
*   **查询已安装的包**：`rpm -qa | grep 关键字`
*   **卸载软件包**：`rpm -e 包名`

## 进程查看与管理

系统运行时，所有任务都以进程的形式存在。管理和监控进程是保证系统稳定运行的关键。

### 查看进程

`ps`命令是查看进程信息最常用的工具，配合不同参数可以获取不同详细程度的信息。

![](img/2e51dfc46cfb10338a95ab4c377c733a_54.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_55.png)

以下是`ps`命令的几种常用格式：
*   `ps -ef`：以标准格式列出所有进程的完整信息。
*   `ps aux`：以BSD格式列出所有进程，包含CPU和内存使用率。
*   `ps -lf`：以长格式列出更详细的进程信息。

![](img/2e51dfc46cfb10338a95ab4c377c733a_57.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_59.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_60.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_62.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_64.png)

要理解`ps`命令输出中各列的含义（如`PID`进程ID、`PPID`父进程ID、`STAT`进程状态），可以使用`man ps`命令查看详细的手册说明。

![](img/2e51dfc46cfb10338a95ab4c377c733a_66.png)

`top`命令用于动态实时查看系统资源占用情况和进程列表。它默认按CPU使用率排序，能够快速定位资源消耗异常高的进程。

![](img/2e51dfc46cfb10338a95ab4c377c733a_68.png)

### 管理进程

对于异常或需要停止的进程，我们使用`kill`命令向其发送信号来终止它。

最常用的信号是`9`（SIGKILL），代表强制终止。

以下是终止进程的命令：
```bash
kill -9 进程PID
```
你可以先用`ps`或`top`命令找到目标进程的PID，然后再用`kill`命令终止它。

![](img/2e51dfc46cfb10338a95ab4c377c733a_70.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_72.png)

此外，在命令行中，我们可以使用`&`符号将命令放到后台运行，使用`jobs`命令查看后台作业，使用`fg`将后台作业切换到前台。

![](img/2e51dfc46cfb10338a95ab4c377c733a_74.png)

## 实战示例：部署简易HTTP服务

![](img/2e51dfc46cfb10338a95ab4c377c733a_76.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_78.png)

现在，让我们综合运用软件包管理和服务管理知识，快速部署一个简易的HTTP网站。

![](img/2e51dfc46cfb10338a95ab4c377c733a_80.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_82.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_84.png)

以下是部署步骤：
1.  安装HTTPD软件包：`yum install -y httpd`
2.  启动HTTPD服务并设置开机自启：
    ```bash
    systemctl start httpd
    systemctl enable httpd
    ```
3.  创建默认网页。编辑`/var/www/html/index.html`文件，写入任意内容（例如“Hello World”）。
4.  重启HTTPD服务使配置生效：`systemctl restart httpd`
5.  使用`curl http://服务器IP`命令测试网页是否可以正常访问。

---

![](img/2e51dfc46cfb10338a95ab4c377c733a_86.png)

![](img/2e51dfc46cfb10338a95ab4c377c733a_88.png)

本节课中我们一起学习了Linux系统的三项核心管理任务：网络链路聚合的配置与排错、通过YUM和RPM进行软件包管理，以及使用`ps`、`top`、`kill`等命令查看和管理系统进程。最后，我们通过部署一个简易的HTTP服务完成了实战练习。请务必在实验环境中反复操作这些命令，以加深理解和记忆。