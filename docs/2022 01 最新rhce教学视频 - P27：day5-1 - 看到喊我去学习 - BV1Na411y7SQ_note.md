# RHCE课程：P27：Day5-1 - 软件包管理与系统调优 🚀

![](img/e7f2ac466de3bd80f68b31e110bf16fb_1.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_3.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_4.png)

在本节课中，我们将要学习Linux系统中的软件包管理基础以及系统性能调优的初步概念。这是RHCE认证考试中非常重要的两个部分，掌握它们对于系统管理至关重要。

## 软件包管理 📦

![](img/e7f2ac466de3bd80f68b31e110bf16fb_6.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_8.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_10.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_12.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_13.png)

上一节我们介绍了课程的整体安排，本节中我们来看看软件包管理。

开源软件最初只提供打包好的源代码（例如 `.tar.gz` 文件），用户需要自行编译才能在系统上运行。为了提供更便利的软件管理方式，出现了软件包的概念。红帽系统主要使用 **RPM** 包进行管理，你可以将其想象成Windows系统中的 `.exe` 安装程序。

![](img/e7f2ac466de3bd80f68b31e110bf16fb_15.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_16.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_18.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_19.png)

### RPM包基础

一个RPM包包含了程序的二进制文件、元数据（如名称、版本、依赖关系）以及可能的安装/卸载脚本。其命名通常遵循特定格式，例如 `nginx-1.20.1-1.el8.x86_64.rpm`。

![](img/e7f2ac466de3bd80f68b31e110bf16fb_21.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_23.png)

以下是RPM包管理的基本命令：

*   **查询已安装的包**：`rpm -qa | grep [软件名]`
*   **安装本地RPM包**：`rpm -ivh [包文件名.rpm]`
*   **卸载软件包**：`rpm -e [软件名]`

直接使用 `rpm` 命令安装时，常常会遇到依赖关系问题，需要手动逐个解决，过程较为繁琐。

### YUM/DNF 包管理器

为了解决依赖问题，红帽系统引入了 **YUM** 及其在RHEL 8中的后继者 **DNF**。它们通过配置软件仓库（Repository），自动解决软件安装时的依赖关系。

软件仓库的配置文件通常位于 `/etc/yum.repos.d/` 目录下。一个基本的本地仓库配置示例如下：

```bash
[BaseOS]
name=BaseOS
baseurl=file:///mnt/BaseOS
enabled=1
gpgcheck=0

![](img/e7f2ac466de3bd80f68b31e110bf16fb_25.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_27.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_29.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_31.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_33.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_35.png)

[AppStream]
name=AppStream
baseurl=file:///mnt/AppStream
enabled=1
gpgcheck=0
```

![](img/e7f2ac466de3bd80f68b31e110bf16fb_37.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_38.png)

> **注意**：`baseurl` 中使用 `file://` 协议时，路径需要三个斜杠，例如 `file:///mnt/BaseOS`。

![](img/e7f2ac466de3bd80f68b31e110bf16fb_40.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_42.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_43.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_45.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_47.png)

配置好仓库后，可以使用以下DNF命令：

![](img/e7f2ac466de3bd80f68b31e110bf16fb_49.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_50.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_52.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_54.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_56.png)

*   **清理并重建缓存**：`dnf clean all && dnf makecache`
*   **安装软件**：`dnf install [软件名] -y`
*   **卸载软件**：`dnf remove [软件名]`
*   **重新安装软件**：`dnf reinstall [软件名]`
*   **列出所有可用包**：`dnf list available`
*   **列出已配置的仓库**：`dnf repolist`

![](img/e7f2ac466de3bd80f68b31e110bf16fb_58.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_60.png)

### 模块流（Module Streams）

RHEL 8引入了模块流的概念，它将一组相关的RPM包（如特定版本的编程语言或应用）组织在一起。每个模块可以有多个流（代表不同版本），但一次只能启用一个流。

![](img/e7f2ac466de3bd80f68b31e110bf16fb_62.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_63.png)

*   **列出所有模块**：`dnf module list`
*   **启用模块流并安装**：`dnf module install [模块名:流名]`

![](img/e7f2ac466de3bd80f68b31e110bf16fb_65.png)

## 系统调优 ⚙️

![](img/e7f2ac466de3bd80f68b31e110bf16fb_67.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_69.png)

上一节我们学习了如何管理软件，本节中我们来看看如何优化系统性能。

![](img/e7f2ac466de3bd80f68b31e110bf16fb_71.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_73.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_75.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_77.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_79.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_80.png)

RHEL系统提供了 `tuned` 守护进程来进行系统调优。它提供了一系列针对不同工作负载（如虚拟化主机、数据库服务器、桌面环境等）优化过的配置文件。

![](img/e7f2ac466de3bd80f68b31e110bf16fb_82.png)

调优分为静态和动态两种：
*   **静态调优**：在系统启动或切换配置文件时应用一组预设的内核参数。
*   **动态调优**：`tuned` 守护进程会监控系统活动，并根据运行时行为动态调整参数。

![](img/e7f2ac466de3bd80f68b31e110bf16fb_84.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_86.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_88.png)

### 常用Tuned命令

![](img/e7f2ac466de3bd80f68b31e110bf16fb_90.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_92.png)

以下是管理 `tuned` 服务的核心命令：

![](img/e7f2ac466de3bd80f68b31e110bf16fb_94.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_96.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_98.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_100.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_102.png)

*   **检查tuned状态**：`systemctl status tuned`
*   **列出所有可用配置文件**：`tuned-adm list`
*   **查看当前活动的配置文件**：`tuned-adm active`
*   **切换配置文件**：`tuned-adm profile [配置文件名]`
    *   例如，切换到平衡模式：`tuned-adm profile balanced`
*   **关闭调优**：`tuned-adm off`

在RHCE考试中，可能会要求你将系统从一种优化模式（如 `virtual-guest`）切换到另一种模式（如 `balanced`），只需使用 `tuned-adm profile` 命令即可完成。

![](img/e7f2ac466de3bd80f68b31e110bf16fb_104.png)

---

![](img/e7f2ac466de3bd80f68b31e110bf16fb_105.png)

![](img/e7f2ac466de3bd80f68b31e110bf16fb_107.png)

本节课中我们一起学习了Linux的软件包管理机制，包括RPM包的基本操作和利用YUM/DNF仓库自动解决依赖的高级管理方法。同时，我们也初步了解了如何使用 `tuned` 工具对系统进行性能调优。这些是日常系统管理和RHCE认证考试中的基础且重要的技能。