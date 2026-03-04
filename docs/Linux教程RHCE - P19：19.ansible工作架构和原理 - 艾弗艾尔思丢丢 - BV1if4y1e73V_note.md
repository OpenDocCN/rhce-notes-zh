# Linux教程RHCE：P19：Ansible工作架构和原理 🚀

## 概述
在本节课中，我们将学习Ansible自动化运维工具的核心工作架构和基本原理。我们将了解Ansible如何实现无代理的远程管理，其关键特性，以及如何通过简单的配置开始使用它。

![](img/384e7c7d23600952bc68c34b962ebe1e_1.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_3.png)

---

![](img/384e7c7d23600952bc68c34b962ebe1e_5.png)

## 自动化运维的需求演变
上一节我们介绍了通过Kickstart应答文件实现操作系统的自动化安装和初始化。然而，生产环境是动态变化的，后期可能需要更新软件或部署新服务。此时，仅靠安装阶段的自动化就不够了，我们需要更灵活的自动化运维工具。

![](img/384e7c7d23600952bc68c34b962ebe1e_7.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_9.png)

Ansible正是为此而生。它是一种基于Python开发的配置管理和应用部署工具，能够帮助管理员高效地管理多台服务器。

---

![](img/384e7c7d23600952bc68c34b962ebe1e_11.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_13.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_15.png)

## Ansible的核心概念与特性

![](img/384e7c7d23600952bc68c34b962ebe1e_17.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_19.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_20.png)

### 无代理架构
Ansible的一个显著特点是**无代理（Agentless）**。它不需要在被管理节点上安装任何专用客户端（代理程序），而是直接通过**SSH协议**进行通信和管理。这大大简化了部署和后期维护工作。

**代码示例：基于SSH的远程命令**
```bash
# 通过SSH在远程主机192.168.30.6上执行`hostname`命令
ssh root@192.168.30.6 hostname
```
Ansible的核心就是基于这种SSH机制，但将其功能进行了大规模扩展和自动化封装。

### 幂等性（Idempotency）
Ansible设计遵循**幂等性**原则。这意味着一个任务无论执行一次还是多次，最终的系统状态都是相同的，不会因为重复执行而产生错误或额外变更。

**概念对比：**
*   **非幂等操作**：在Linux中，重复执行 `useradd alice` 命令，第二次会因用户已存在而报错。
*   **幂等操作**：使用Ansible的`user`模块创建用户，如果用户已存在，模块将不会执行任何操作，也不会报错。

![](img/384e7c7d23600952bc68c34b962ebe1e_22.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_24.png)

这个特性使得剧本（Playbook）的编写和调试更加安全、方便。

### 模块化设计
Ansible的功能由超过1000个**模块（Module）** 构成。每个模块就像一个专用的Linux命令，用于执行特定任务，例如管理用户、安装软件包、复制文件等。

![](img/384e7c7d23600952bc68c34b962ebe1e_26.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_27.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_29.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_31.png)

**主要组成部分：**
1.  **连接插件（Connection Plugins）**：负责与受管节点通信，默认使用SSH。
2.  **主机清单（Inventory）**：定义Ansible需要管理的主机列表。
3.  **模块（Modules）**：执行具体任务的核心单元。
4.  **剧本（Playbooks）**：使用YAML语言编写的自动化脚本，将多个任务有序组合。
5.  **角色（Roles）**：一种更高级的组织方式，将剧本、变量、文件等按特定结构打包，便于复用和分享。

![](img/384e7c7d23600952bc68c34b962ebe1e_33.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_35.png)

---

## Ansible的工作架构 🏗️
Ansible采用主控端（Control Node）管理多个被控端（Managed Nodes）的架构。

**架构流程：**
1.  **用户/系统** 通过Ansible命令或Playbook发起任务。
2.  **Ansible主控端** 读取**主机清单**，确定任务目标主机。
3.  通过**连接插件（SSH）** 连接到目标主机。
4.  将需要执行的**模块**代码推送到目标主机。
5.  目标主机在本地执行模块代码，并将结果返回给主控端。
6.  主控端汇总并呈现执行结果。

这种架构使得一个管理员可以轻松管理成百上千台服务器。

---

![](img/384e7c7d23600952bc68c34b962ebe1e_37.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_38.png)

## 安装与初步配置

![](img/384e7c7d23600952bc68c34b962ebe1e_40.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_42.png)

### 安装Ansible
在RHEL/CentOS系统上，可以通过配置EPEL源，使用YUM工具轻松安装。

![](img/384e7c7d23600952bc68c34b962ebe1e_44.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_46.png)

**安装命令：**
```bash
yum install ansible -y
```

![](img/384e7c7d23600952bc68c34b962ebe1e_48.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_50.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_52.png)

### 配置主机清单（Inventory）
主机清单文件默认位于 `/etc/ansible/hosts`。你需要在此文件中列出所有需要管理的主机。

![](img/384e7c7d23600952bc68c34b962ebe1e_54.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_56.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_58.png)

**清单文件示例：**
```ini
# 定义单个主机
192.168.30.101
192.168.30.102

![](img/384e7c7d23600952bc68c34b962ebe1e_60.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_62.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_64.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_66.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_67.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_69.png)

# 定义主机组 [group_name]
[web_servers]
192.168.30.101
192.168.30.102

![](img/384e7c7d23600952bc68c34b962ebe1e_71.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_73.png)

[db_servers]
192.168.30.103

# 支持数字和字母序列
[app_servers]
192.168.30.[101:103]  # 代表101, 102, 103

![](img/384e7c7d23600952bc68c34b962ebe1e_75.png)

# 指定非标准SSH端口
some_host ansible_ssh_port=2222
```

### 配置SSH密钥认证
为了避免每次执行任务时手动输入密码，必须在主控端和被控端之间配置SSH密钥认证。

![](img/384e7c7d23600952bc68c34b962ebe1e_77.png)

**配置步骤：**
1.  在主控端生成SSH密钥对（如果尚未生成）：
    ```bash
    ssh-keygen
    ```
2.  将公钥分发到所有被控端主机：
    ```bash
    ssh-copy-id root@192.168.30.101
    ssh-copy-id root@192.168.30.102
    ssh-copy-id root@192.168.30.103
    ```
    此步骤需要输入各被控端root用户的密码。

---

## 运行第一个Ansible命令 🎯
完成上述配置后，就可以使用`ansible`命令来执行临时任务了。

**命令基本格式：**
```bash
ansible <主机或主机组> -m <模块名> [-a ‘模块参数‘]
```

**示例：测试主机组的连通性**
Ansible有一个专用的`ping`模块（非ICMP ping），用于测试SSH连接和Python环境是否正常。
```bash
# 测试 ‘web_servers‘ 组内所有主机的连通性
ansible web_servers -m ping
```
如果配置正确，你将看到类似 `“ping“: “pong“` 的成功返回信息。

![](img/384e7c7d23600952bc68c34b962ebe1e_79.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_81.png)

---

![](img/384e7c7d23600952bc68c34b962ebe1e_83.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_85.png)

## 总结
本节课我们一起学习了Ansible自动化运维工具的基础知识。我们了解了其**无代理**、**幂等性**和**模块化**的核心特性，剖析了它的工作架构，并完成了从安装、配置主机清单、设置SSH密钥认证到运行第一个`ansible`命令的完整入门流程。

![](img/384e7c7d23600952bc68c34b962ebe1e_86.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_88.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_90.png)

![](img/384e7c7d23600952bc68c34b962ebe1e_91.png)

掌握这些基础是后续学习编写复杂Playbook和Roles的关键。在接下来的课程中，我们将深入探索Ansible模块的使用和Playbook的编写。