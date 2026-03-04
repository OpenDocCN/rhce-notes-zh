# Redhat红帽 RHCE8.0认证体系课程：P52：安装与初始化Ansible 🚀

在本节课中，我们将要学习如何在Red Hat Enterprise Linux 8.0环境中安装和初始化Ansible自动化工具。我们将从环境准备开始，逐步完成Ansible的安装，并进行初步验证。

## 概述

Ansible是一款强大的自动化工具，用于配置管理、应用部署和任务编排。本节课程将指导您完成Ansible控制节点的安装与基础配置。我们只需要在一台主机上安装Ansible作为控制节点，其他主机作为被管理节点。

## 环境准备

在开始安装之前，需要确保您的实验环境满足以下要求。

以下是环境配置的具体要求：

*   **物理环境**：
    *   **内存**：练习环境建议8GB至16GB内存。若使用三台裸机，8GB内存足够。
    *   **硬盘**：至少100GB，建议使用固态硬盘以获得更好的性能。
*   **虚拟机配置**：
    *   **数量**：至少三台虚拟机。
    *   **系统**：统一使用RHEL 8.0。RHEL 7与8的主要区别在于Python版本（7使用2.7.5，8使用3.6.8）。
*   **主机预配置**：
    *   三台主机的主机名和IP地址需提前设置完毕。示例如下：
        *   `controller: 192.168.1.10`
        *   `node1: 192.168.1.11`
        *   `node2: 192.168.1.12`
*   **所需软件包**：
    *   **Ansible**：版本 `ansible-2.9.10-1`。
    *   **Python**：RHEL 8自带Python 3。Ansible依赖的Python组件包含在系统镜像中。

请注意，Ansible软件包不在标准的BaseOS和AppStream仓库中，因此我们需要通过配置额外的仓库来安装。

![](img/a435094ab0e1f651c85683abc265697b_1.png)

## 安装步骤

![](img/a435094ab0e1f651c85683abc265697b_3.png)

上一节我们介绍了安装前的环境准备，本节中我们来看看具体的安装操作流程。

![](img/a435094ab0e1f651c85683abc265697b_5.png)

![](img/a435094ab0e1f651c85683abc265697b_6.png)

### 1. 挂载本地镜像并配置Yum仓库

首先，我们需要在作为控制节点的主机（例如 `controller`）上挂载RHEL 8的安装镜像，以获取必要的软件包依赖。

![](img/a435094ab0e1f651c85683abc265697b_8.png)

以下是操作命令：

```bash
# 创建挂载点目录
mkdir -p /mnt/iso

# 挂载ISO镜像（请将 /dev/sr0 替换为您的实际光驱设备）
mount /dev/sr0 /mnt/iso

![](img/a435094ab0e1f651c85683abc265697b_10.png)

# 配置本地Yum仓库文件
vi /etc/yum.repos.d/local.repo
```

在 `/etc/yum.repos.d/local.repo` 文件中添加以下内容（注意方括号内不能有空格）：

![](img/a435094ab0e1f651c85683abc265697b_12.png)

```ini
[BaseOS]
name=BaseOS
baseurl=file:///mnt/iso/BaseOS
enabled=1
gpgcheck=0

![](img/a435094ab0e1f651c85683abc265697b_14.png)

[AppStream]
name=AppStream
baseurl=file:///mnt/iso/AppStream
enabled=1
gpgcheck=0
```

![](img/a435094ab0e1f651c85683abc265697b_16.png)

保存并退出编辑器。

![](img/a435094ab0e1f651c85683abc265697b_18.png)

![](img/a435094ab0e1f651c85683abc265697b_19.png)

### 2. 安装EPEL仓库并更新缓存

Ansible包位于EPEL（Extra Packages for Enterprise Linux）仓库中。我们需要先安装EPEL仓库。

![](img/a435094ab0e1f651c85683abc265697b_21.png)

![](img/a435094ab0e1f651c85683abc265697b_22.png)

以下是操作命令：

![](img/a435094ab0e1f651c85683abc265697b_23.png)

```bash
# 安装EPEL仓库包
dnf install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

# 更新Yum元数据缓存
dnf makecache
```

安装成功后，您可以通过 `dnf repolist` 命令查看到名为 `epel` 的仓库。

### 3. 安装Ansible

现在，我们可以安装Ansible及其依赖了。请确保在已挂载本地镜像并配置好仓库的情况下执行此命令。

```bash
# 安装Ansible
dnf install -y ansible
```

安装完成后，您可以通过以下命令验证安装的版本：

```bash
ansible --version
```

## 验证安装

安装完成后，我们需要验证Ansible是否能正常工作。

![](img/a435094ab0e1f651c85683abc265697b_25.png)

以下是两种验证方法：

![](img/a435094ab0e1f651c85683abc265697b_26.png)

![](img/a435094ab0e1f651c85683abc265697b_27.png)

1.  **查看Ansible配置信息**：
    运行 `ansible --version` 命令，可以查看Ansible的版本、配置文件路径、Python模块位置等详细信息。

![](img/a435094ab0e1f651c85683abc265697b_28.png)

2.  **运行临时命令**：
    我们可以尝试对本地主机（`localhost`）运行一个简单的Ansible临时命令，使用 `setup` 模块收集主机信息。
    ```bash
    ansible localhost -m setup
    ```
    如果命令成功执行并返回了主机的详细事实信息（如IPv4地址、主机名等），则证明Ansible安装成功并可以正常运行。

![](img/a435094ab0e1f651c85683abc265697b_30.png)

## 总结

![](img/a435094ab0e1f651c85683abc265697b_31.png)

![](img/a435094ab0e1f651c85683abc265697b_32.png)

本节课中我们一起学习了Ansible的安装与初始化。我们首先准备了包含三台RHEL 8主机的实验环境，然后在控制节点上通过挂载本地镜像、配置EPEL仓库，最终成功安装了Ansible 2.9.10-1，并通过运行测试命令验证了其功能。安装完成后，控制节点就可以开始管理其他主机了。

![](img/a435094ab0e1f651c85683abc265697b_33.png)

接下来，我们将进入下一章节，学习如何配置Ansible的**清单（Inventory）**，这是定义和管理被控主机列表的关键步骤。