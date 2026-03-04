# Linux运维进阶：P61：Ansible批量运维工具介绍及安装 🚀

在本节课中，我们将要学习一款强大的IT自动化工具——Ansible。我们将了解它的基本概念、特点、工作原理，并完成从环境准备到软件安装的完整过程。

---

## 什么是Ansible？🤔

Ansible是一款于2013年推出的IT自动化与配置管理软件。它基于Python语言开发，其核心设计思想是**符合自动化运维理念**。

在后续的DevOps学习阶段，我们会接触到众多自动化工具，它们能极大地提升运维工作的效率。Ansible正是其中一款非常出色的**批量管理工具**，目前隶属于红帽公司。

## 批量管理工具的应用场景

设想一个场景：企业中有成百上千台服务器需要维护。如果需要对这一群服务器进行统一操作，例如安装软件包、备份数据或检查系统状态，传统的一台台手动操作方式效率极低。

批量管理工具就是为了解决这个问题而生的。使用Ansible，管理员可以在**一台管理主机**上执行一个命令，就能让所有被管理的服务器同时完成指定任务。

![](img/f8fe04752a40e81ddf223f4192b86bdf_1.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_3.png)

例如：
*   查看所有服务器的内存使用情况：`free -h`
*   为所有服务器安装一个软件包：`yum -y install [包名]`
*   将一个文件同时拷贝到所有服务器上

这就是批量运维的魅力。

## 常见批量管理工具对比

除了Ansible，行业内也曾有其他批量管理工具，例如 **Chef** 和 **Puppet**。

然而，Chef和Puppet基于Ruby语言编写，学习曲线相对陡峭，且前期环境部署较为复杂（属于C/S架构，需要在每台被管理节点上安装客户端Agent），因此目前在行业内的应用已逐渐减少。

相比之下，Ansible凭借其**轻量级**和**易用性**，成为了市场占有率最高的选择。

## Ansible的核心特点与优势 🎯

![](img/f8fe04752a40e81ddf223f4192b86bdf_5.png)

上一节我们介绍了批量管理工具的概念，本节我们来看看Ansible具体有哪些优势。

![](img/f8fe04752a40e81ddf223f4192b86bdf_7.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_9.png)

Ansible的“轻量级”主要体现在其工作方式上。它通过**SSH协议**连接和管理被控主机。只要被管理主机开启了SSH服务，Ansible就能进行连接，而**无需在被管理端安装任何额外的客户端程序**。

![](img/f8fe04752a40e81ddf223f4192b86bdf_11.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_13.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_15.png)

这大大简化了前期的环境部署工作，运维人员只需要配置好管理主机即可。

![](img/f8fe04752a40e81ddf223f4192b86bdf_16.png)

### 模块化设计

Ansible的功能通过**模块**实现。模块可以理解为Ansible的一条条专用命令，每个模块负责完成一项特定任务。

![](img/f8fe04752a40e81ddf223f4192b86bdf_18.png)

例如：
*   **yum模块**：用于安装、卸载软件包。
*   **copy模块**：用于拷贝文件。
*   **shell模块**：用于在远程主机执行Shell命令。

![](img/f8fe04752a40e81ddf223f4192b86bdf_20.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_22.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_24.png)

Ansible提供了丰富的内置模块，同时也支持用户用任何语言**自定义模块**以满足特定需求。

![](img/f8fe04752a40e81ddf223f4192b86bdf_26.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_28.png)

### Playbook剧本

![](img/f8fe04752a40e81ddf223f4192b86bdf_30.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_32.png)

对于复杂的、需要重复执行的任务，Ansible提供了 **Playbook** 功能。你可以将一系列模块操作按照顺序写入一个基于YAML语法的文件中，这个文件就是“剧本”。执行这个剧本，Ansible就会自动按步骤完成任务。

![](img/f8fe04752a40e81ddf223f4192b86bdf_34.png)

Playbook类似于Shell脚本，但结构更清晰，更适合描述复杂的部署流程。

![](img/f8fe04752a40e81ddf223f4192b86bdf_36.png)

---

![](img/f8fe04752a40e81ddf223f4192b86bdf_38.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_40.png)

## 实验环境准备与安装 🛠️

![](img/f8fe04752a40e81ddf223f4192b86bdf_42.png)

理论部分介绍完毕，接下来我们进入实战环节，亲手搭建一个Ansible实验环境。

我们的实验环境包含三台CentOS 7虚拟机：
1.  **管理节点**：`ansible-server`
2.  **被管理节点**：`host12`， `host13`

![](img/f8fe04752a40e81ddf223f4192b86bdf_44.png)

所有后续操作都将在`ansible-server`这台管理主机上执行。

### 步骤一：配置主机名与本地解析

![](img/f8fe04752a40e81ddf223f4192b86bdf_46.png)

首先，我们需要修改管理节点的主机名，并配置本地主机名解析，以便后续通过主机名进行访问。

![](img/f8fe04752a40e81ddf223f4192b86bdf_48.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_50.png)

```bash
# 在 ansible-server 上执行
# 修改主机名（--static选项允许使用大写）
hostnamectl set-hostname ansible-server --static
# 退出重新连接，使主机名生效

![](img/f8fe04752a40e81ddf223f4192b86bdf_52.png)

# 编辑本地hosts文件，添加被管理节点的IP和主机名
vim /etc/hosts
```
在 `/etc/hosts` 文件中添加以下内容：
```
192.168.0.12 host12
192.168.0.13 host13
```
保存退出后，测试解析是否成功：
```bash
ping host12
ping host13
```

![](img/f8fe04752a40e81ddf223f4192b86bdf_54.png)

### 步骤二：配置SSH免密登录

由于Ansible通过SSH连接，为了避免每次连接时输入密码，我们需要配置从管理节点到所有被管理节点的SSH免密登录。

```bash
# 在 ansible-server 上执行
# 生成SSH密钥对（如果已有则跳过）
ssh-keygen -t rsa -P ‘’

![](img/f8fe04752a40e81ddf223f4192b86bdf_56.png)

# 将公钥拷贝到被管理节点
ssh-copy-id root@host12
ssh-copy-id root@host13
# 执行命令后，输入对应主机的root密码

# 验证免密登录是否成功
ssh host12 ‘exit’
ssh host13 ‘exit’
```
如果机器数量多，可以使用for循环脚本批量操作。

### 步骤三：安装Ansible软件包

![](img/f8fe04752a40e81ddf223f4192b86bdf_58.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_60.png)

Ansible软件包位于EPEL扩展源中。我们需要先配置EPEL源，然后安装。

![](img/f8fe04752a40e81ddf223f4192b86bdf_62.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_64.png)

```bash
# 1. 配置基础的Base源和EPEL扩展源（使用阿里云镜像）
# 下载Base源repo文件
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
# 下载EPEL源repo文件
wget -O /etc/yum.repos.d/epel.repo https://mirrors.aliyun.com/repo/epel-7.repo

![](img/f8fe04752a40e81ddf223f4192b86bdf_66.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_68.png)

# 2. 安装Ansible
yum install ansible -y

![](img/f8fe04752a40e81ddf223f4192b86bdf_70.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_72.png)

# 3. 验证安装
ansible --version
```
命令会输出Ansible的版本、配置文件路径、模块路径等信息。

![](img/f8fe04752a40e81ddf223f4192b86bdf_74.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_76.png)

---

![](img/f8fe04752a40e81ddf223f4192b86bdf_78.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_80.png)

## 认识Ansible清单文件 📋

![](img/f8fe04752a40e81ddf223f4192b86bdf_82.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_84.png)

安装完成后，我们需要告诉Ansible它要管理哪些机器。这个信息记录在 **清单文件** 中。

![](img/f8fe04752a40e81ddf223f4192b86bdf_86.png)

Ansible的主配置文件是 `/etc/ansible/ansible.cfg`，其中定义了默认的清单文件路径为 `/etc/ansible/hosts`。

![](img/f8fe04752a40e81ddf223f4192b86bdf_88.png)

### 如何定义被管理主机？

![](img/f8fe04752a40e81ddf223f4192b86bdf_90.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_92.png)

打开清单文件，你会发现里面有很多注释示例。定义主机非常简单，只需将主机名或IP地址写入即可。

**定义方式一：直接列出主机**
```
host12
host13
192.168.0.100
```

**定义方式二：使用主机组（推荐）**
将功能类似的主机放入一个组，方便批量管理。
```
[web_servers]    # 定义一个名为 web_servers 的组
host12
host13

[db_servers]     # 定义另一个组
192.168.0.100
192.168.0.101
```

**定义方式三：模式匹配**
如果主机名有规律，可以使用更简洁的方式。
```
[web_servers]
host[11:12]      # 这等价于 host11, host12
www[01:06].example.com # 匹配 www01 到 www06
```

![](img/f8fe04752a40e81ddf223f4192b86bdf_94.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_96.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_98.png)

编辑 `/etc/ansible/hosts` 文件，我们采用分组方式定义：
```
[myhosts]
host12
host13
```
保存并退出。

![](img/f8fe04752a40e81ddf223f4192b86bdf_100.png)

### 测试清单配置

使用以下命令可以列出清单中定义的所有主机或指定组的主机。

```bash
# 列出所有主机
ansible all --list-hosts

# 列出指定组（myhosts）的主机
ansible myhosts --list-hosts
```
如果能看到我们定义的 `host12` 和 `host13`，说明清单文件配置正确。

---

## 总结 📚

本节课中我们一起学习了：
1.  **Ansible是什么**：一款基于Python的、轻量级的IT自动化与批量配置管理工具，通过SSH协议工作，无需在被管理端安装Agent。
2.  **Ansible的核心概念**：包括**模块**（实现具体功能的命令）和**Playbook**（用于编排复杂任务的剧本）。
3.  **搭建Ansible实验环境**：我们完成了管理节点的准备，包括配置主机名、本地解析、SSH免密登录，以及通过EPEL源安装Ansible软件包。
4.  **配置Ansible清单文件**：学会了如何在 `/etc/ansible/hosts` 文件中定义被管理主机，包括直接定义、分组定义和模式匹配等常用方法。

![](img/f8fe04752a40e81ddf223f4192b86bdf_102.png)

![](img/f8fe04752a40e81ddf223f4192b86bdf_104.png)

环境已经就绪，在接下来的课程中，我们将开始学习如何使用Ansible的各种模块来执行实际的批量管理任务。