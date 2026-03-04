# RHCE8 自动化课程：P16：第九天 Ansible 介绍及临时命令

![](img/f49409a9acfbcacb18a372ff1b25dfaf_0.png)

在本节课中，我们将要学习 Ansible 自动化平台的基础知识，包括其核心概念、架构、环境配置以及如何使用临时命令执行简单的自动化任务。

## 概述

从红帽 RHCE8 开始，认证考试的重点从传统的手动服务器搭建（如 FTP、邮件服务器）转向了自动化运维。本课程（RH294）将系统性地介绍 Ansible 这一强大的自动化工具。今天，我们将首先了解 Ansible 的基本概念、环境搭建，并学习使用其临时命令。

## 环境介绍

上一节我们介绍了课程背景，本节中我们来看看本次学习所使用的实验环境。

我们的实验环境包含多台虚拟机，用于模拟真实的自动化管理场景：

*   **控制节点**：`workstation`。这是我们执行所有 Ansible 命令和编写剧本的机器。**从今天起，所有操作都将在 `workstation` 上以 `student` 用户身份进行，不再使用 `root` 管理员**。
*   **受管节点**：`servera`、`serverb`、`serverc`、`serverd`。这些是被 Ansible 管理的机器，模拟考试和生产环境中的服务器。
*   **服务节点**：`classroom`。提供 DNS、Yum 仓库等基础服务。
*   **堡垒机**：`bastion`。在后续课程中，我们会用它来模拟额外的受管节点 `servere`。

![](img/f49409a9acfbcacb18a372ff1b25dfaf_2.png)

**注意**：此环境需要较多内存。如果您的电脑只有 8GB 内存，运行可能会比较卡顿。

## 第一章：Ansible 介绍

![](img/f49409a9acfbcacb18a372ff1b25dfaf_4.png)

上一节我们熟悉了实验环境，本节中我们来深入了解 Ansible 是什么以及它能做什么。

Ansible 是一个开源的自动化平台，后被红帽收购。它特别适合管理大型服务器集群，能够将管理员从繁琐的重复性工作中解放出来，并减少人为错误。

Ansible 的核心特点包括：

1.  **简单易读**：使用 YAML 格式编写剧本，即使没有编程背景也易于理解和编写。
2.  **功能强大**：支持配置管理、应用部署、任务编排，并能管理服务器、网络设备（如交换机、路由器）甚至 Windows 主机。
3.  **无代理架构**：这是 Ansible 最突出的优势。管理受管节点时，**无需在目标机器上安装任何客户端或代理**，仅需通过 SSH（Linux）或 WinRM（Windows）协议能够连通即可。
4.  **幂等性**：大部分 Ansible 模块具有幂等性。这意味着无论执行多少次，系统的最终状态都是一致的。例如，创建用户的命令在用户已存在时不会重复创建。

![](img/f49409a9acfbcacb18a372ff1b25dfaf_6.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_8.png)

Ansible 的版本迭代主要体现在模块的增删和更新上。RHCE8 考试基于 Ansible 2.8 版本。

### Ansible 架构

![](img/f49409a9acfbcacb18a372ff1b25dfaf_10.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_12.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_14.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_16.png)

Ansible 的架构可以简化为三大核心组成部分：

![](img/f49409a9acfbcacb18a372ff1b25dfaf_18.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_20.png)

1.  **清单**：定义 Ansible 需要管理的主机集合。可以是一个包含 IP 地址或主机名的静态文件，也可以是通过脚本动态生成的列表。
2.  **模块**：Ansible 执行任务的具体工具。每个模块负责完成一项特定工作，例如安装软件包（`yum` 模块）、管理用户（`user` 模块）、操作文件（`file` 模块）等。学习 Ansible 很大程度上就是学习如何使用这些模块。
3.  **剧本**：一组按特定逻辑组织起来的任务集合，使用 YAML 格式编写。它是 Ansible 自动化能力的核心体现。

![](img/f49409a9acfbcacb18a372ff1b25dfaf_22.png)

**公式**：`Ansible 引擎 + 清单 + 模块 (+ API/插件) = 自动化能力`

## 第二章：配置 Ansible

上一节我们介绍了 Ansible 的核心概念，本节中我们来看看如何配置 Ansible 的工作环境，重点是清单文件和配置文件。

### 1. 清单文件

清单文件定义了 Ansible 管理的对象。分为静态清单和动态清单。

**静态清单**：手动编写受管主机的信息。
**动态清单**：通过脚本（通常是 Python）从外部系统（如云平台 API）动态获取主机信息。

以下是一个静态清单文件的示例：

```ini
# 定义单个主机
servera.lab.example.com

![](img/f49409a9acfbcacb18a372ff1b25dfaf_24.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_26.png)

# 定义主机组 [组名]
[webservers]
servera.lab.example.com
serverb.lab.example.com

![](img/f49409a9acfbcacb18a372ff1b25dfaf_28.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_30.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_32.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_34.png)

[dbservers]
serverc.lab.example.com

![](img/f49409a9acfbcacb18a372ff1b25dfaf_36.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_38.png)

# 嵌套组（使用 `:children` 关键字）
[usa]
servera
serverb

![](img/f49409a9acfbcacb18a372ff1b25dfaf_40.png)

[canada]
serverc

![](img/f49409a9acfbcacb18a372ff1b25dfaf_42.png)

[northamerica:children]
usa
canada
```

![](img/f49409a9acfbcacb18a372ff1b25dfaf_44.png)

**重要内置组**：
*   `all`：代表清单中的所有主机。
*   `ungrouped`：代表不属于任何自定义组的主机。

![](img/f49409a9acfbcacb18a372ff1b25dfaf_46.png)

**验证清单**：可以使用以下命令查看特定组包含的主机。
```bash
ansible webservers --list-hosts
```

### 2. 配置文件

![](img/f49409a9acfbcacb18a372ff1b25dfaf_48.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_50.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_52.png)

Ansible 的配置文件用于设置默认行为，例如指定默认的清单文件、远程连接用户、提权方式等。

![](img/f49409a9acfbcacb18a372ff1b25dfaf_54.png)

配置文件的优先级（从高到低）：
1.  当前工作目录下的 `ansible.cfg`
2.  用户家目录下的 `~/.ansible.cfg`
3.  系统级的 `/etc/ansible/ansible.cfg`

![](img/f49409a9acfbcacb18a372ff1b25dfaf_56.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_58.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_60.png)

**最佳实践**：为每个项目创建一个独立的工作目录，并在其中创建自己的 `ansible.cfg` 和清单文件。

![](img/f49409a9acfbcacb18a372ff1b25dfaf_62.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_64.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_66.png)

一个典型的项目目录结构和工作流程如下：
1.  以 `student` 用户登录 `workstation`。
2.  在家目录下为项目创建专属目录，例如 `mkdir automation_project`。
3.  进入该目录 `cd automation_project`，此目录即为“当前工作目录”。
4.  在此目录下创建 `ansible.cfg` 和清单文件（如 `inventory`）。

一个基础的 `ansible.cfg` 配置示例：

![](img/f49409a9acfbcacb18a372ff1b25dfaf_68.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_70.png)

```ini
[defaults]
# 指定清单文件路径（相对于当前配置文件，或绝对路径）
inventory = ./inventory
# 指定连接受管节点时使用的远程用户
remote_user = student

[privilege_escalation]
# 是否提权
become = True
# 提权方式（sudo/su等）
become_method = sudo
# 提权后成为哪个用户（通常是root）
become_user = root
# 提权时是否需要密码（需与受管节点sudo配置匹配）
become_ask_pass = False
```

**关键点**：
*   `remote_user`：指定 **SSH 连接到受管节点时使用的用户名**，需要在受管节点上存在。
*   `become` 相关配置：当远程用户权限不足时（如安装软件需 root 权限），通过 `sudo` 等方式提权。这要求受管节点上已为该用户配置了无需密码的 `sudo` 权限（考试环境已配置好）。

配置完成后，可以使用 `ansible --version` 命令检查当前生效的配置文件路径和 Ansible 版本。

![](img/f49409a9acfbcacb18a372ff1b25dfaf_72.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_74.png)

## 第三章：使用临时命令

![](img/f49409a9acfbcacb18a372ff1b25dfaf_76.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_78.png)

![](img/f49409a9acfbcacb18a372ff1b25dfaf_80.png)

上一节我们配置好了 Ansible 的基础环境，本节中我们来学习如何执行最简单的自动化操作——临时命令。

临时命令适用于在受管节点上执行单一的、简单的任务，无需编写完整的剧本。其语法结构如下：

```bash
ansible <主机或组> -m <模块名> -a “<模块参数>” [其他选项]
```

![](img/f49409a9acfbcacb18a372ff1b25dfaf_82.png)

**代码**：
*   `<主机或组>`：指定操作对象，如 `webservers`， `all`， `servera`。
*   `-m <模块名>`：指定要使用的 Ansible 模块。
*   `-a “<模块参数>”`：传递给模块的参数，需用单引号括起来。
*   `[其他选项]`：常用 `-b` 或 `--become` 进行提权。

![](img/f49409a9acfbcacb18a372ff1b25dfaf_84.png)

**示例1：在 `webservers` 组的所有主机上创建用户**
```bash
ansible webservers -m user -a ‘name=tom state=present‘ -b
```
*   `-m user`：使用用户管理模块。
*   `-a ‘name=tom state=present‘`：模块参数，创建名为 `tom` 的用户（`present` 表示确保存在）。
*   `-b`：提权（因为创建用户需要 root 权限）。

![](img/f49409a9acfbcacb18a372ff1b25dfaf_86.png)

**示例2：查看 `webservers` 组所有主机的磁盘使用情况**
```bash
ansible webservers -m shell -a ‘df -h‘
```
*   `-m shell`：使用 shell 模块执行原始命令。
*   **注意**：`shell` 和 `command` 模块**缺乏幂等性**，且无法利用 Ansible 的一些高级特性（如变量）。通常仅用于执行查询或幂等性不重要的命令。

![](img/f49409a9acfbcacb18a372ff1b25dfaf_88.png)

### 模块文档查询

Ansible 模块众多，不可能全部记住。必须掌握如何使用 `ansible-doc` 命令查看模块的详细用法和示例。

```bash
# 列出所有模块
ansible-doc -l

# 查看特定模块的文档，如 user 模块
ansible-doc user

# 查看模块的简短描述和示例
ansible-doc -s user
```
在考试和日常工作中，应随时打开一个终端使用 `ansible-doc` 来查询模块的正确用法和参数。

## 总结

本节课中我们一起学习了 Ansible 自动化入门的关键知识：

1.  **Ansible 基础**：了解了其无代理、幂等性等核心优势，以及由清单、模块、剧本构成的简单架构。
2.  **环境配置**：掌握了如何设置项目工作目录，并正确配置 `ansible.cfg`（指定清单和提权）与静态清单文件。
3.  **临时命令**：学会了使用 `ansible` 临时命令语法，配合 `-m` 指定模块和 `-a` 指定参数来执行简单任务，并知道如何用 `ansible-doc` 查询帮助。

![](img/f49409a9acfbcacb18a372ff1b25dfaf_90.png)

这些内容是后续学习编写复杂 Ansible 剧本的基石。请确保理解配置文件与清单文件的作用，以及临时命令的基本用法。下午我们将开始学习编写 YAML 格式的 Ansible 剧本。