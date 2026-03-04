# Linux运维：P62：Ansible批量运维工具介绍及安装 🚀

在本节课中，我们将要学习一款强大的IT自动化工具——Ansible。我们将了解它的基本概念、工作原理、与其他工具的区别，并完成Ansible管理主机的安装与基础环境配置。

---

## 什么是Ansible？ 🤔

Ansible是一款于2013年推出的IT自动化与配置管理软件。它基于Python语言开发，其设计思想符合自动化运维的理念。

Ansible是众多自动化工具中的一员，它属于**批量管理工具**。目前，Ansible归属于红帽公司。

## 批量管理工具的应用场景

在企业环境中，运维工程师通常需要维护成百上千台服务器。如果对每台服务器都进行单独连接和操作，效率将极其低下。

![](img/acd018d6420892164f9150bb42f1c428_1.png)

![](img/acd018d6420892164f9150bb42f1c428_3.png)

批量管理工具就是为了解决这个问题而生的。例如：
*   当需要查看所有服务器的内存使用情况时，只需在管理主机上执行一条命令，所有被管理节点的信息就会汇总返回。
*   当需要为所有服务器安装一个软件包时，也只需在管理主机上执行一条安装命令即可。
*   当需要将一个文件分发到上百台服务器时，同样只需一个拷贝命令。

除了Ansible，行业中曾有过其他批量管理工具，如 **Chef** 和 **Puppet**。但由于它们基于Ruby语言开发，使用相对复杂，目前已在行业中逐渐被淘汰。

## Ansible 与 SaltStack 的差异

上一节我们介绍了批量管理工具的概念，本节中我们来看看Ansible与另一款工具SaltStack的核心差异。

SaltStack（简称Salt）采用的是**C/S架构**（客户端/服务器架构）。这意味着，要使用Salt管理机器，不仅需要在管理节点安装服务端软件，还需要在**每一台**被管理节点上安装对应的客户端代理（Agent）软件。当被管理节点数量庞大时，前期的部署工作会非常繁琐。

而Ansible是一款**轻量级**的批量管理工具。它的“轻量”体现在其工作方式上：Ansible**基于SSH协议**进行通信。只要被管理节点开启了SSH服务，Ansible就能直接连接并进行管理。因此，使用Ansible时，**只需要在管理主机上安装Ansible软件**，被管理节点无需安装任何额外代理程序，这大大简化了环境部署。正因如此，Ansible目前在市场上的占有率远超SaltStack。

![](img/acd018d6420892164f9150bb42f1c428_5.png)

## Ansible 的核心特点

![](img/acd018d6420892164f9150bb42f1c428_7.png)

![](img/acd018d6420892164f9150bb42f1c428_9.png)

了解了Ansible的轻量级优势后，我们再来看看它的一些核心特点。

![](img/acd018d6420892164f9150bb42f1c428_11.png)

![](img/acd018d6420892164f9150bb42f1c428_13.png)

![](img/acd018d6420892164f9150bb42f1c428_15.png)

![](img/acd018d6420892164f9150bb42f1c428_16.png)

1.  **基于Python与Paramiko**：Ansible使用Python编写，并利用其`paramiko`模块实现SSH连接。因此，只要被管理节点开启SSH服务，即可进行管理。
2.  **模块化设计**：Ansible的功能通过**模块**实现。每个模块都是一个独立的、用于完成特定任务的工具。例如：
    *   `yum`模块：用于安装软件包。
    *   `copy`模块：用于拷贝文件。
    *   `shell`模块：用于执行系统命令。
    学习Ansible，主要就是学习如何使用这些模块。
3.  **支持自定义模块**：如果内置模块无法满足需求，Ansible允许用户使用任何语言（包括Shell）开发自定义模块。
4.  **Playbook功能**：Playbook是Ansible的“剧本”，类似于Shell脚本。它基于YAML语法编写，可以将多个模块任务有序地组织在一个文件中，用于完成复杂、重复的自动化工作。
5.  **Role功能**：Role（角色）是比Playbook更高级、组织性更强的功能单元，适合部署非常复杂的环境（如OpenStack平台）。

> **注意**：本阶段课程主要目标是让大家熟悉Ansible的基础使用，为后续可能遇到的考试或学习打下基础。Playbook和Role等高级功能将在后续课程中详细讲解。

![](img/acd018d6420892164f9150bb42f1c428_18.png)

---

![](img/acd018d6420892164f9150bb42f1c428_20.png)

![](img/acd018d6420892164f9150bb42f1c428_22.png)

## 实验环境准备 🛠️

![](img/acd018d6420892164f9150bb42f1c428_24.png)

在开始安装Ansible之前，我们需要准备好实验环境。我们将使用三台虚拟机：
*   **管理节点**：`ansible-server` (IP: 192.168.0.11)
*   **被管理节点1**：`host12` (IP: 192.168.0.12)
*   **被管理节点2**：`host13` (IP: 192.168.0.13)

![](img/acd018d6420892164f9150bb42f1c428_26.png)

![](img/acd018d6420892164f9150bb42f1c428_28.png)

![](img/acd018d6420892164f9150bb42f1c428_30.png)

以下所有操作均在`ansible-server`管理节点上执行。

### 步骤1：配置本地主机名解析

![](img/acd018d6420892164f9150bb42f1c428_32.png)

![](img/acd018d6420892164f9150bb42f1c428_34.png)

为了让Ansible能够通过主机名连接被管理节点，我们需要在管理节点上配置本地解析。

![](img/acd018d6420892164f9150bb42f1c428_36.png)

![](img/acd018d6420892164f9150bb42f1c428_38.png)

编辑`/etc/hosts`文件，添加以下内容：
```bash
192.168.0.12 host12
192.168.0.13 host13
```
保存后，可以使用`ping host12`或`ping host13`测试解析是否生效。

### 步骤2：配置SSH免密登录

![](img/acd018d6420892164f9150bb42f1c428_40.png)

由于Ansible通过SSH连接，为了避免每次连接时输入密码，我们需要配置从管理节点到所有被管理节点的SSH免密登录。

以下是配置SSH免密登录的关键命令：
```bash
# 生成SSH密钥对（如果尚未生成）
ssh-keygen -t rsa

![](img/acd018d6420892164f9150bb42f1c428_42.png)

![](img/acd018d6420892164f9150bb42f1c428_44.png)

# 将公钥拷贝到被管理节点 host12
ssh-copy-id root@host12

![](img/acd018d6420892164f9150bb42f1c428_46.png)

![](img/acd018d6420892164f9150bb42f1c428_48.png)

![](img/acd018d6420892164f9150bb42f1c428_49.png)

# 将公钥拷贝到被管理节点 host13
ssh-copy-id root@host13
```
执行后，尝试`ssh host12`，如果无需密码即可登录，说明配置成功。

![](img/acd018d6420892164f9150bb42f1c428_51.png)

![](img/acd018d6420892164f9150bb42f1c428_53.png)

> **提示**：当需要管理的机器很多时，可以编写一个简单的Shell循环脚本来批量执行`ssh-copy-id`命令。

![](img/acd018d6420892164f9150bb42f1c428_55.png)

![](img/acd018d6420892164f9150bb42f1c428_57.png)

---

## 安装 Ansible 📦

环境准备就绪后，我们现在来安装Ansible软件。Ansible包位于EPEL（Extra Packages for Enterprise Linux）扩展仓库中。

![](img/acd018d6420892164f9150bb42f1c428_59.png)

以下是安装步骤：

1.  配置阿里云的Base源和EPEL源：
    ```bash
    # 下载并安装阿里云Base源
    wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo

    # 下载并安装阿里云EPEL源
    wget -O /etc/yum.repos.d/epel.repo https://mirrors.aliyun.com/repo/epel-7.repo
    ```

![](img/acd018d6420892164f9150bb42f1c428_61.png)

2.  使用yum安装Ansible：
    ```bash
    yum install ansible -y
    ```

![](img/acd018d6420892164f9150bb42f1c428_63.png)

![](img/acd018d6420892164f9150bb42f1c428_65.png)

![](img/acd018d6420892164f9150bb42f1c428_67.png)

3.  验证安装：
    ```bash
    ansible --version
    ```
    该命令会输出Ansible的版本信息（如2.9.27）以及重要的配置路径，例如：
    *   `config file`: 主配置文件路径（`/etc/ansible/ansible.cfg`）
    *   `configured module search path`: 模块搜索路径
    *   `ansible python module location`: Ansible Python模块位置

![](img/acd018d6420892164f9150bb42f1c428_69.png)

---

![](img/acd018d6420892164f9150bb42f1c428_71.png)

![](img/acd018d6420892164f9150bb42f1c428_73.png)

![](img/acd018d6420892164f9150bb42f1c428_75.png)

## 认识清单文件 (Inventory) 📝

![](img/acd018d6420892164f9150bb42f1c428_77.png)

Ansible如何知道它要管理哪些机器呢？答案就是**清单文件**。清单文件是一个文本文件，其中定义了所有被管理主机的信息。

![](img/acd018d6420892164f9150bb42f1c428_79.png)

![](img/acd018d6420892164f9150bb42f1c428_81.png)

![](img/acd018d6420892164f9150bb42f1c428_83.png)

Ansible默认的清单文件路径是：`/etc/ansible/hosts`。

![](img/acd018d6420892164f9150bb42f1c428_85.png)

### 如何定义被管理主机？

![](img/acd018d6420892164f9150bb42f1c428_87.png)

![](img/acd018d6420892164f9150bb42f1c428_89.png)

打开默认的清单文件，你会发现里面有很多注释和示例。定义主机非常简单，只需将主机名或IP地址写入即可。

![](img/acd018d6420892164f9150bb42f1c428_91.png)

![](img/acd018d6420892164f9150bb42f1c428_93.png)

以下是几种常见的定义方式：

**1. 直接使用IP或主机名（不推荐用于多主机）**
```
192.168.0.12
host13
```

**2. 分组定义（推荐）**
这是最常用、最灵活的方式。你可以将主机按功能分组，例如分为`web`组和`db`组。
```
[web]        # 定义名为‘web’的组
host12
host13

![](img/acd018d6420892164f9150bb42f1c428_95.png)

![](img/acd018d6420892164f9150bb42f1c428_97.png)

[db]         # 定义名为‘db’的组
192.168.0.100
192.168.0.101
```
定义好后，可以通过组名来批量操作组内所有主机。

**3. 使用模式匹配**
如果主机名有规律，可以使用`[start:end]`的模式来简化定义。
```
# 这代表 host11, host12, host13, ... , host20
host[11:20]

![](img/acd018d6420892164f9150bb42f1c428_99.png)

![](img/acd018d6420892164f9150bb42f1c428_101.png)

![](img/acd018d6420892164f9150bb42f1c428_103.png)

# 这代表 www01.example.com, www02.example.com, ... , www06.example.com
www[01:06].example.com
```

![](img/acd018d6420892164f9150bb42f1c428_105.png)

### 验证清单配置

使用以下命令可以列出清单文件中定义的所有主机或指定组的主机：
```bash
# 列出所有主机
ansible all --list-hosts

# 列出‘web’组内的所有主机
ansible web --list-hosts
```

---

## 本节课总结 🎯

本节课中我们一起学习了：
1.  **Ansible是什么**：一款基于Python和SSH的轻量级IT自动化与批量配置管理工具。
2.  **Ansible的优势**：无需在被管理节点安装代理，部署简单；模块化设计，功能丰富且支持自定义。
3.  **基础环境搭建**：完成了管理节点的安装，并配置了SSH免密登录与主机名解析。
4.  **核心概念-清单文件**：学会了如何通过编辑`/etc/ansible/hosts`文件来定义和分组管理被管理主机。

![](img/acd018d6420892164f9150bb42f1c428_107.png)

![](img/acd018d6420892164f9150bb42f1c428_109.png)

现在，你已经拥有了一个可工作的Ansible管理环境。在接下来的课程中，我们将开始学习如何使用Ansible的各种模块来执行实际的批量管理任务。