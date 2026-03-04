# Linux运维教程：P62：Ansible批量运维工具介绍及安装 🚀

在本节课中，我们将要学习一款强大的IT自动化工具——Ansible。我们将了解它的基本概念、工作原理、与其他工具的区别，并完成从环境准备到安装配置的完整过程。通过本课，你将能够搭建一个基础的Ansible管理环境。

## 什么是Ansible？🤔

Ansible是2013年推出的一款IT自动化运维软件。它符合自动化思想，基于Python语言开发。Ansible是DevOps工具链中非常重要的一环，它能让运维工作变得更高效。

Ansible是一款出色的批量管理工具，目前属于红帽公司，因为它已被红帽收购。

## 批量管理工具的应用场景

在企业中，通常有大量服务器需要维护。例如，需要对一群服务器进行备份、安装软件包或执行配置。如果一台一台地手动操作，效率极低，尤其当运维人员需要管理数百台服务器时。

批量管理工具可以很好地解决这个问题。它允许你从一台管理主机上，同时向所有被管理服务器发送指令。

![](img/e1abab649d701c56b9a24316bb413c4c_1.png)

![](img/e1abab649d701c56b9a24316bb413c4c_3.png)

例如：
*   查看所有服务器的内存使用情况：只需在管理主机执行 `free -h` 命令。
*   为所有服务器安装一个软件包：只需在管理主机执行 `yum -y install [包名]`。
*   将一个文件同时拷贝到上百台服务器。

这就是批量管理工具的威力。

## 常见的批量管理工具

除了Ansible，行业中还有如 **Chef** 和 **Puppet** 等批量管理工具。然而，这两款工具基于Ruby语言，相对冷门且配置复杂，目前在企业中已逐渐被淘汰，因此我们无需深入了解了。

下面，我们重点对比 **Ansible** 和 **SaltStack**。

### Ansible 与 SaltStack 的差异

![](img/e1abab649d701c56b9a24316bb413c4c_5.png)

SaltStack（简称Salt）采用 **C/S架构**（客户端/服务器架构）。这意味着，如果使用Salt搭建管理环境，不仅需要在管理节点安装服务端软件，还需要在**每一台**被管理节点上安装客户端代理（Agent）软件。当被管理节点数量庞大时，前期的环境部署工作量会非常繁重。

![](img/e1abab649d701c56b9a24316bb413c4c_7.png)

![](img/e1abab649d701c56b9a24316bb413c4c_9.png)

![](img/e1abab649d701c56b9a24316bb413c4c_11.png)

而Ansible是一款**轻量级**的批量管理工具。它的“轻量”体现在：它通过 **SSH协议** 来连接和管理被管理主机。就像我们使用Xshell通过SSH连接虚拟机一样。

![](img/e1abab649d701c56b9a24316bb413c4c_13.png)

![](img/e1abab649d701c56b9a24316bb413c4c_15.png)

![](img/e1abab649d701c56b9a24316bb413c4c_16.png)

**核心特点**：
*   管理主机只需安装Ansible软件。
*   被管理主机**无需安装任何额外代理软件**，只要其开启了SSH服务，Ansible就能连接并管理它。

这使得Ansible的前期环境准备非常简单省事，因此其市场占有率远高于SaltStack。

![](img/e1abab649d701c56b9a24316bb413c4c_18.png)

## Ansible 的核心特点

![](img/e1abab649d701c56b9a24316bb413c4c_20.png)

![](img/e1abab649d701c56b9a24316bb413c4c_22.png)

上一节我们介绍了Ansible的轻量级特性，本节中我们来看看它的其他核心设计。

![](img/e1abab649d701c56b9a24316bb413c4c_24.png)

1.  **基于Python与Paramiko模块**：Ansible使用Python编写，并利用名为`Paramiko`的Python模块来实现SSH协议连接。因此，只要被管理节点开启SSH服务，Ansible即可管理它，无需安装客户端程序（Agent）。

![](img/e1abab649d701c56b9a24316bb413c4c_26.png)

![](img/e1abab649d701c56b9a24316bb413c4c_28.png)

![](img/e1abab649d701c56b9a24316bb413c4c_30.png)

2.  **模块化设计，模块丰富**：Ansible的功能通过**模块**实现。每个模块都是一个特定功能的命令。
    *   **yum模块**：用于安装软件包。
    *   **copy模块**：用于拷贝文件。
    *   **shell模块**：用于执行系统命令。
    学习Ansible，主要就是学习如何使用这些模块。Ansible提供了大量内置模块，也允许用户用任何语言（包括Shell）自定义模块。

![](img/e1abab649d701c56b9a24316bb413c4c_32.png)

3.  **支持Playbook**：Playbook是Ansible的“剧本”，类似于Shell脚本。它基于YAML语法编写，可以将多个Ansible模块按顺序组织在一个文件中，用来完成复杂的、重复性的任务。执行一个Playbook文件，Ansible就会自动按顺序执行其中的所有任务。

![](img/e1abab649d701c56b9a24316bb413c4c_34.png)

![](img/e1abab649d701c56b9a24316bb413c4c_36.png)

4.  **支持Role（角色）**：Role是比Playbook更强大的功能单元，适合部署非常复杂的环境（如OpenStack云平台）。它难度较高，目前仅作了解即可。

![](img/e1abab649d701c56b9a24316bb413c4c_38.png)

## 实验环境准备 🛠️

在开始动手安装之前，我们需要准备好实验环境。我们将使用三台虚拟机：
*   **管理节点**：`ansible-server` (IP: 192.168.0.11)
*   **被管理节点1**：`host12` (IP: 192.168.0.12)
*   **被管理节点2**：`host13` (IP: 192.168.0.13)

![](img/e1abab649d701c56b9a24316bb413c4c_40.png)

以下所有操作均在`ansible-server`管理节点上执行。

### 步骤1：配置本地主机名解析

![](img/e1abab649d701c56b9a24316bb413c4c_42.png)

![](img/e1abab649d701c56b9a24316bb413c4c_44.png)

![](img/e1abab649d701c56b9a24316bb413c4c_46.png)

为了让Ansible能够通过主机名找到被管理机器，我们需要在管理节点的`/etc/hosts`文件中添加解析记录。

```bash
vim /etc/hosts
```
在文件末尾添加：
```
192.168.0.12 host12
192.168.0.13 host13
```
保存退出后，测试解析是否成功：
```bash
ping host12
ping host13
```

![](img/e1abab649d701c56b9a24316bb413c4c_48.png)

![](img/e1abab649d701c56b9a24316bb413c4c_50.png)

### 步骤2：配置SSH免密登录

Ansible通过SSH连接被管理主机，为了避免每次连接都输入密码，我们需要配置从管理节点到被管理节点的SSH密钥认证。

首先生成密钥对（如果已有可跳过）：
```bash
ssh-keygen
```
然后将公钥拷贝到两台被管理主机：
```bash
ssh-copy-id root@host12
ssh-copy-id root@host13
```
执行命令后，输入对应主机的root密码。完成后，即可实现免密登录。
```bash
ssh host12 # 测试免密登录
exit
```
**小技巧**：如果主机很多，可以使用for循环批量拷贝：
```bash
for i in host12 host13; do ssh-copy-id root@$i; done
```

![](img/e1abab649d701c56b9a24316bb413c4c_52.png)

## 安装Ansible 📦

环境准备就绪后，我们就可以安装Ansible软件了。Ansible包位于EPEL（Extra Packages for Enterprise Linux）扩展软件仓库中。

![](img/e1abab649d701c56b9a24316bb413c4c_54.png)

### 步骤1：配置EPEL源

![](img/e1abab649d701c56b9a24316bb413c4c_56.png)

![](img/e1abab649d701c56b9a24316bb413c4c_58.png)

![](img/e1abab649d701c56b9a24316bb413c4c_60.png)

我们使用阿里云提供的镜像源。
1.  下载基础仓库文件（包含依赖）：
    ```bash
    wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
    ```
2.  下载EPEL仓库文件：
    ```bash
    wget -O /etc/yum.repos.d/epel.repo https://mirrors.aliyun.com/repo/epel-7.repo
    ```

![](img/e1abab649d701c56b9a24316bb413c4c_62.png)

![](img/e1abab649d701c56b9a24316bb413c4c_64.png)

### 步骤2：安装Ansible

![](img/e1abab649d701c56b9a24316bb413c4c_66.png)

![](img/e1abab649d701c56b9a24316bb413c4c_68.png)

```bash
yum install ansible -y
```

![](img/e1abab649d701c56b9a24316bb413c4c_70.png)

![](img/e1abab649d701c56b9a24316bb413c4c_72.png)

![](img/e1abab649d701c56b9a24316bb413c4c_74.png)

![](img/e1abab649d701c56b9a24316bb413c4c_76.png)

### 步骤3：验证安装

![](img/e1abab649d701c56b9a24316bb413c4c_78.png)

![](img/e1abab649d701c56b9a24316bb413c4c_80.png)

安装完成后，查看Ansible版本以确认安装成功。
```bash
ansible --version
```
输出信息中会显示版本号（如2.9.27）、配置文件路径、模块搜索路径等。

![](img/e1abab649d701c56b9a24316bb413c4c_82.png)

![](img/e1abab649d701c56b9a24316bb413c4c_84.png)

## 认识Ansible清单文件 📋

![](img/e1abab649d701c56b9a24316bb413c4c_86.png)

Ansible如何知道要管理哪些机器呢？这需要通过**清单文件**来定义。清单文件就像一个主机列表，里面记录了所有被管理主机的信息。

![](img/e1abab649d701c56b9a24316bb413c4c_88.png)

![](img/e1abab649d701c56b9a24316bb413c4c_90.png)

默认的清单文件路径是：`/etc/ansible/hosts`

![](img/e1abab649d701c56b9a24316bb413c4c_92.png)

### 查看并编辑清单文件

```bash
vim /etc/ansible/hosts
```
这个文件里有很多注释示例，告诉我们如何定义主机。

#### 定义方式1：直接列出主机（适合少量主机）
在文件中任意位置（例如在某个示例下方）添加：
```
host12
host13
# 或者直接使用IP
# 192.168.0.12
# 192.168.0.13
```

#### 定义方式2：使用主机组（推荐，适合管理大量主机）
主机组使用`[组名]`格式定义，组名下缩进写入该组的主机。
```
[web_servers] # 定义一个名为 web_servers 的组
host12
host13

![](img/e1abab649d701c56b9a24316bb413c4c_94.png)

![](img/e1abab649d701c56b9a24316bb413c4c_96.png)

![](img/e1abab649d701c56b9a24316bb413c4c_98.png)

[db_servers] # 可以定义多个组
# 192.168.0.100
# 192.168.0.101
```

![](img/e1abab649d701c56b9a24316bb413c4c_100.png)

#### 定义方式3：使用模式匹配（适合有规律的主机名）
如果主机名有规律，可以用更简洁的方式定义。
```
[web_servers]
host[11:13] # 这等价于 host11, host12, host13
```

保存并退出编辑器。

### 测试清单文件

使用以下命令列出所有已定义的主机：
```bash
ansible all --list-hosts
```
列出特定组内的主机：
```bash
ansible web_servers --list-hosts
```
如果能看到你定义的主机名，说明清单文件配置正确。

## 总结 🎉

本节课中我们一起学习了：
1.  **Ansible是什么**：一款基于Python、通过SSH工作的轻量级IT自动化与批量运维工具。
2.  **核心优势**：无需在被管理端安装Agent，简单易用。
3.  **核心概念**：模块化设计，通过**模块**执行具体任务；支持**Playbook**编排复杂任务。
4.  **环境搭建**：完成了管理节点的环境准备（主机解析、SSH免密）、Ansible安装以及核心配置文件——**清单文件**的配置。

![](img/e1abab649d701c56b9a24316bb413c4c_102.png)

![](img/e1abab649d701c56b9a24316bb413c4c_104.png)

现在，你已经拥有了一个可以工作的Ansible管理环境。在接下来的课程中，我们将开始学习如何使用Ansible的各种模块来执行实际的运维任务。