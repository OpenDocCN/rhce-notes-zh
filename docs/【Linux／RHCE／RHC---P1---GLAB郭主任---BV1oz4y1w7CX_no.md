# Linux运维与红帽认证：36：Ansible介绍

在本节课中，我们将要学习自动化运维工具Ansible的核心概念、工作原理以及基础环境的配置方法。Ansible是现代IT运维中不可或缺的工具，掌握它将极大提升管理多台服务器的效率。

## 概述

Ansible是一个开源的自动化平台，用于实现IT基础架构的自动化管理。它采用无代理架构，通过SSH协议和YAML脚本语言，让一台控制节点能够管理成百上千台受控节点，从而实现“机器管理机器”的运维理念。

## Ansible的核心概念与工作原理

上一节我们概述了Ansible，本节中我们来看看它的核心工作原理。

Ansible的自动化理念转变了传统的运维方式。在没有自动化工具之前，管理员需要登录到每一台服务器上重复执行安装软件、配置防火墙等操作。当服务器数量庞大时，这种工作方式效率极低。

Ansible提出了新的管理架构：**控制节点** 和 **受控节点**。控制节点是执行管理操作的机器，上面编写并运行Ansible脚本；受控节点则是被管理的服务器。Ansible通过在控制节点上运行脚本，来批量管理所有受控节点。

那么，Ansible是如何具体实现对受控节点的管理的呢？其核心流程如下：

1.  在控制节点上，Ansible引擎将编写的YAML格式脚本（Playbook）转换为Python代码。
2.  通过**SSH协议**将生成的Python代码推送到目标受控节点。
3.  受控节点执行接收到的Python代码，完成指定的管理任务（如安装软件、修改配置）。
4.  任务执行完毕后，受控节点会自动清理掉临时生成的Python模块。

因此，使用Ansible有两个基本前提：
*   控制节点与所有受控节点之间必须配置好**SSH免密登录**。
*   所有受控节点都需要安装**Python解释器**（现代Linux发行版通常已预装）。

## Ansible的主要特点

以下是Ansible的一些重要优点：

*   **无代理架构**：无需在受控节点上安装任何代理软件，直接通过SSH管理，部署简单。
*   **简单易用**：使用**YAML**语言编写自动化脚本（Playbook），语法清晰，易于阅读和维护。
*   **幂等性**：Playbook可以安全地多次执行。Ansible会先判断目标状态，如果任务已完成，则不会重复执行，避免了资源浪费和潜在错误。
*   **模块化设计**：拥有超过400个核心模块，覆盖软件包管理、文件操作、服务管理等各类运维任务。用户也可以使用Python、Shell等语言自定义模块。
*   **跨平台支持**：不仅能管理Linux/Unix服务器，还能管理Windows主机以及网络设备（如路由器、交换机）。

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_1.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_3.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_5.png)

## 配置Ansible基础环境

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_7.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_8.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_10.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_12.png)

理解了Ansible的工作原理后，本节我们将动手配置一个基础的Ansible工作环境。主要任务包括安装Ansible软件、配置主文件、清单文件以及设置SSH免密登录。

我们的实验环境包含一台控制节点（`workstation`）和四台受控节点（`serverA` 到 `serverD`）。所有操作将在`workstation`上进行。

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_14.png)

### 1. 安装Ansible

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_16.png)

在控制节点上，使用包管理器安装Ansible软件。

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_18.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_20.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_22.png)

```bash
sudo yum install ansible
```
安装完成后，可以查看版本信息以确认安装成功。
```bash
ansible --version
```

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_24.png)

### 2. 创建Ansible配置文件

Ansible的主配置文件默认位于`/etc/ansible/ansible.cfg`。我们通常会在项目目录中创建自己的配置文件，优先级更高，也更灵活。

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_26.png)

首先，创建一个项目目录并进入。
```bash
mkdir ~/ansible-guide
cd ~/ansible-guide
```
然后，创建主配置文件`ansible.cfg`，并写入基本配置。
```ini
[defaults]
inventory = ./inventory
remote_user = devops
private_key_file = /home/devops/.ssh/id_rsa

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_28.png)

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
```
此配置指明了清单文件位置、远程连接用户、私钥路径以及提权（sudo）方式。

### 3. 创建清单文件

清单文件（Inventory）定义了Ansible需要管理的所有主机。创建文件`inventory`。

以下是清单文件内容的示例：
```ini
# 定义单个主机（IP或域名）
192.168.1.100
web01.example.com

# 定义主机范围
192.168.1.[101:105]

# 定义主机组
[web_servers]
web01.example.com
web02.example.com
192.168.1.110

[db_servers]
db01.example.com
db02.example.com

# 嵌套组：all_servers 组包含 web_servers 和 db_servers 两个组
[all_servers:children]
web_servers
db_servers
```
配置好后，可以使用以下命令测试清单是否能被正确读取：
```bash
# 列出所有主机
ansible all --list-hosts

# 列出特定组的主机
ansible web_servers --list-hosts

# 使用模式匹配列出主机
ansible ‘192.168.1.*’ --list-hosts
```

### 4. 配置SSH免密登录与sudo提权

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_30.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_32.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_34.png)

为了让控制节点能无缝管理受控节点，需要配置SSH密钥认证，并为运维用户（如`devops`）配置无需密码的sudo权限。

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_36.png)

**在控制节点（workstation）上操作：**

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_38.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_40.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_42.png)

1.  生成SSH密钥对（如果尚未生成）。
    ```bash
    ssh-keygen
    ```
2.  将公钥分发到所有受控节点（包括自己，可选）。
    ```bash
    ssh-copy-id devops@serverA
    ssh-copy-id devops@serverB
    ssh-copy-id devops@serverC
    ssh-copy-id devops@serverD
    ```

**在每台受控节点上操作（以root用户）：**

1.  编辑sudoers文件，确保`devops`用户有权执行所有命令且无需密码。
    ```bash
    visudo
    ```
2.  在文件中添加或确保存在如下行：
    ```bash
    %devops ALL=(ALL) NOPASSWD: ALL
    ```

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_44.png)

### 5. 验证Ansible连接

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_46.png)

环境配置完成后，可以通过Ansible的`ping`模块或`command`模块测试到受控节点的连接是否正常。

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_48.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_50.png)

```bash
# 使用ping模块测试所有节点的连通性
ansible all -m ping

# 使用command模块在所有节点上执行‘hostname’命令
ansible all -m command -a “hostname”
```
如果命令返回成功（绿色或黄色输出），则说明Ansible基础环境配置完成。

## AD-Hoc命令简介

在深入Playbook之前，我们先了解一种快速的命令执行方式：AD-Hoc命令。

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_52.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_54.png)

AD-Hoc命令用于在命令行中快速执行单个任务，类似于在Shell中直接输入命令。例如，我们之前测试连接的`ansible all -m ping`就是一个AD-Hoc命令。它的基本结构是：
```bash
ansible <模式或主机组> -m <模块名> -a “<模块参数>”
```
例如，使用`yum`模块安装软件：
```bash
ansible web_servers -m yum -a “name=httpd state=present”
```
AD-Hoc命令适合执行一些简单的、一次性的任务，而复杂的、需要重复执行的自动化流程则应该编写成Playbook脚本。

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_56.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_58.png)

## 总结

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_60.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_62.png)

![](img/bb6dfa5b0b8ad4595529dbcf0f47ade9_64.png)

本节课中我们一起学习了自动化运维工具Ansible的入门知识。我们首先了解了Ansible通过**控制节点**和**受控节点**的架构实现“机器管理机器”，其核心是利用**SSH协议**和**Python**来执行任务。接着，我们探讨了Ansible**无代理**、**幂等性**、**模块化**等重要特点。最后，我们动手完成了Ansible基础环境的搭建，包括安装软件、编写主配置与清单文件，以及配置关键的SSH免密登录和sudo权限，并通过AD-Hoc命令验证了连接。这为后续学习编写复杂的Ansible Playbook脚本打下了坚实基础。