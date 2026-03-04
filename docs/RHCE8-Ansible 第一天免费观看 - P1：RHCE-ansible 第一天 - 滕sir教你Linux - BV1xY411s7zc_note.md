# RHCE8-Ansible 课程：第1天：RHCE-Ansible 第一天

![](img/abd35968e93e24fc3b7f090064744c10_1.png)

## 概述

在本节课中，我们将要学习 Ansible 自动化的基础概念、环境搭建、核心架构以及如何编写和运行第一个 Ansible Playbook。课程内容从理论到实践，旨在让初学者能够理解 Ansible 的工作原理并上手操作。

![](img/abd35968e93e24fc3b7f090064744c10_3.png)

---

![](img/abd35968e93e24fc3b7f090064744c10_5.png)

![](img/abd35968e93e24fc3b7f090064744c10_7.png)

## 环境介绍与准备

![](img/abd35968e93e24fc3b7f090064744c10_9.png)

![](img/abd35968e93e24fc3b7f090064744c10_11.png)

上一节我们介绍了课程的整体目标，本节中我们来看看本次学习所需的实验环境。

我们的实验环境基于红帽提供的标准实验室，包含多台虚拟机，用于模拟真实的管理场景。

以下是环境中的关键机器及其角色：

*   **classroom**: 环境中的总服务器，提供 DNS、软件仓库等基础服务。
*   **bastion**: 充当跳板机或路由器，连接不同网段，在模拟考试时也可作为第五个被管理节点。
*   **workstation**: **控制节点**。我们将在这台机器上安装 Ansible，并执行所有管理操作。
*   **serverA, serverB, serverC, serverD**: **被控节点**。这四台机器是被 Ansible 管理的对象。

**核心概念**：在 Ansible 架构中，**控制节点** 是发出指令的管理端，**被控节点** 是接收并执行指令的受管主机。

### 环境切换与启动

由于本课程需要使用特定的实验环境，我们需要从之前的课程环境切换到 Ansible 课程环境。

以下是切换和启动环境的步骤：

1.  **清理当前环境**：在 foundation 物理机上，执行命令 `lab clean`，关闭所有正在运行的虚拟机。
2.  **切换课程环境**：执行命令 `set I294`，将环境切换到 Ansible 课程（课程代码 I294）。这个过程需要一些时间来完成虚拟机的配置。
3.  **启动环境**：环境切换完成后，首先启动 `classroom` 虚拟机，并使用 `ping` 命令确认其网络连通性。这是启动其他虚拟机的先决条件。
4.  **启动所有虚拟机**：使用命令 `start all` 或分别启动 `bastion`、`workstation`、`serverA`、`serverB`、`serverC`、`serverD`。

**注意**：确保你的主机内存至少为 8GB，以保证所有虚拟机能够流畅运行。

---

## 第一章：Ansible 核心概念

![](img/abd35968e93e24fc3b7f090064744c10_13.png)

上一节我们准备好了实验环境，本节中我们来深入理解 Ansible 是什么以及它的核心特性。

![](img/abd35968e93e24fc3b7f090064744c10_15.png)

![](img/abd35968e93e24fc3b7f090064744c10_16.png)

Ansible 是一款开源的自动化平台，用于配置管理、应用部署和任务编排。它使用人类可读的语言（YAML）编写“剧本”（Playbook）来描述自动化任务。

![](img/abd35968e93e24fc3b7f090064744c10_18.png)

![](img/abd35968e93e24fc3b7f090064744c10_19.png)

![](img/abd35968e93e24fc3b7f090064744c10_21.png)

![](img/abd35968e93e24fc3b7f090064744c10_23.png)

![](img/abd35968e93e24fc3b7f090064744c10_25.png)

![](img/abd35968e93e24fc3b7f090064744c10_27.png)

### Ansible 的主要优势

以下是 Ansible 的几个关键优势：

![](img/abd35968e93e24fc3b7f090064744c10_29.png)

![](img/abd35968e93e24fc3b7f090064744c10_30.png)

*   **简单易学**：Ansible 使用 YAML 格式的 Playbook，语法清晰，即使没有深厚编程背景也能快速上手。
*   **功能强大**：它可以管理服务器、网络设备、云资源和容器，覆盖了现代 IT 基础设施的各个方面。
*   **无需代理**：这是 Ansible 最突出的特点之一。它通过 SSH（Linux）或 WinRM（Windows）协议与被管理节点通信，**被控节点上无需安装任何 Ansible 客户端或代理**。这大大简化了部署和维护工作。
*   **幂等性**：大多数 Ansible 模块具有幂等性。这意味着无论你将一个 Playbook 运行多少次，系统最终都会达到并保持 Playbook 所描述的期望状态。如果目标状态已达成，Ansible 将不会执行任何操作。

**公式/概念**：**幂等性** 是 Ansible 的一个重要设计原则，它确保操作的可重复执行不会导致意外结果。

### Ansible 与 DevOps

Ansible 是实现 **DevOps** 理念和 **CI/CD**（持续集成/持续部署）流水线的关键技术工具之一。它帮助开发、测试和运维团队更高效地协作，自动化软件交付流程，缩短上线周期。

![](img/abd35968e93e24fc3b7f090064744c10_32.png)

![](img/abd35968e93e24fc3b7f090064744c10_33.png)

![](img/abd35968e93e24fc3b7f090064744c10_35.png)

---

## 第二章：部署 Ansible 与核心文件

![](img/abd35968e93e24fc3b7f090064744c10_37.png)

![](img/abd35968e93e24fc3b7f090064744c10_39.png)

上一节我们了解了 Ansible 的基本概念，本节中我们来看看如何部署 Ansible 并理解其三大核心配置文件。

![](img/abd35968e93e24fc3b7f090064744c10_41.png)

![](img/abd35968e93e24fc3b7f090064744c10_42.png)

![](img/abd35968e93e24fc3b7f090064744c10_43.png)

![](img/abd35968e93e24fc3b7f090064744c10_45.png)

![](img/abd35968e93e24fc3b7f090064744c10_47.png)

![](img/abd35968e93e24fc3b7f090064744c10_49.png)

![](img/abd35968e93e24fc3b7f090064744c10_51.png)

![](img/abd35968e93e24fc3b7f090064744c10_53.png)

### 安装 Ansible

![](img/abd35968e93e24fc3b7f090064744c10_55.png)

![](img/abd35968e93e24fc3b7f090064744c10_57.png)

![](img/abd35968e93e24fc3b7f090064744c10_58.png)

![](img/abd35968e93e24fc3b7f090064744c10_60.png)

Ansible 需要安装在**控制节点**上。在我们的环境中，就是在 `workstation` 虚拟机上安装。

![](img/abd35968e93e24fc3b7f090064744c10_62.png)

![](img/abd35968e93e24fc3b7f090064744c10_64.png)

![](img/abd35968e93e24fc3b7f090064744c10_65.png)

![](img/abd35968e93e24fc3b7f090064744c10_67.png)

安装命令非常简单：
```bash
sudo yum install ansible
```
安装完成后，可以使用以下命令验证安装和查看版本信息：
```bash
ansible --version
```
这条命令会输出 Ansible 的版本、配置文件路径以及所依赖的 Python 版本等信息。

![](img/abd35968e93e24fc3b7f090064744c10_69.png)

![](img/abd35968e93e24fc3b7f090064744c10_71.png)

![](img/abd35968e93e24fc3b7f090064744c10_72.png)

![](img/abd35968e93e24fc3b7f090064744c10_74.png)

### 理解三大核心文件

![](img/abd35968e93e24fc3b7f090064744c10_76.png)

![](img/abd35968e93e24fc3b7f090064744c10_78.png)

![](img/abd35968e93e24fc3b7f090064744c10_80.png)

要有效使用 Ansible，需要理解三个核心文件：**清单文件**、**配置文件** 和 **剧本文件**。最佳实践是在项目工作目录中自定义这些文件，而不是使用系统默认路径，以实现项目间的隔离。

![](img/abd35968e93e24fc3b7f090064744c10_82.png)

#### 1. 清单文件

![](img/abd35968e93e24fc3b7f090064744c10_84.png)

![](img/abd35968e93e24fc3b7f090064744c10_86.png)

清单文件定义了 Ansible 要管理的主机列表。你可以将主机按逻辑分组，方便批量操作。

![](img/abd35968e93e24fc3b7f090064744c10_88.png)

![](img/abd35968e93e24fc3b7f090064744c10_90.png)

**示例**：一个简单的静态清单文件 (`inventory`)
```
[webservers]
serverA.lab.example.com
serverB.lab.example.com

![](img/abd35968e93e24fc3b7f090064744c10_92.png)

![](img/abd35968e93e24fc3b7f090064744c10_94.png)

![](img/abd35968e93e24fc3b7f090064744c10_95.png)

![](img/abd35968e93e24fc3b7f090064744c10_97.png)

![](img/abd35968e93e24fc3b7f090064744c10_99.png)

![](img/abd35968e93e24fc3b7f090064744c10_100.png)

[databases]
serverC.lab.example.com
```
在这个例子中，我们定义了两个组：`webservers` 和 `databases`。

![](img/abd35968e93e24fc3b7f090064744c10_102.png)

![](img/abd35968e93e24fc3b7f090064744c10_104.png)

你可以使用以下命令测试清单：
```bash
# 列出所有主机
ansible all --list-hosts

![](img/abd35968e93e24fc3b7f090064744c10_106.png)

![](img/abd35968e93e24fc3b7f090064744c10_108.png)

![](img/abd35968e93e24fc3b7f090064744c10_110.png)

# 列出 webservers 组的主机
ansible webservers --list-hosts
```
**注意**：运行命令时需要指定清单文件路径，例如 `ansible all -i /path/to/inventory --list-hosts`。我们稍后会在配置文件中解决这个问题。

![](img/abd35968e93e24fc3b7f090064744c10_111.png)

![](img/abd35968e93e24fc3b7f090064744c10_113.png)

#### 2. 配置文件

![](img/abd35968e93e24fc3b7f090064744c10_115.png)

![](img/abd35968e93e24fc3b7f090064744c10_117.png)

Ansible 配置文件 (`ansible.cfg`) 用于设置 Ansible 的各种行为参数，例如默认的清单文件路径、远程连接用户、特权升级设置等。

![](img/abd35968e93e24fc3b7f090064744c10_119.png)

**示例**：一个基本的 `ansible.cfg`
```ini
[defaults]
inventory = ./inventory
remote_user = student
ask_pass = False

![](img/abd35968e93e24fc3b7f090064744c10_121.png)

![](img/abd35968e93e24fc3b7f090064744c10_123.png)

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
```
*   `inventory`: 指定默认清单文件的位置（此处为当前目录下的 `inventory` 文件）。
*   `remote_user`: 连接被控节点时使用的用户名。
*   `ask_pass`: 连接时是否询问密码（设为 `False` 意味着已配置 SSH 密钥认证，无需密码）。
*   `become` 相关参数：定义了如何提升权限以执行需要特权的任务。这里配置为使用 `sudo` 提升为 `root` 用户，且不询问 `sudo` 密码。

**重要前提**：要使 `ask_pass = False` 和 `become_ask_pass = False` 生效，必须在控制节点和被控节点之间预先配置好 **SSH 密钥认证**，并在被控节点上为远程用户配置好 **无需密码的 sudo 权限**。这些是 RHCSA 的基础知识，实验环境已为我们配置好。

![](img/abd35968e93e24fc3b7f090064744c10_125.png)

#### 3. 剧本文件

![](img/abd35968e93e24fc3b7f090064744c10_127.png)

![](img/abd35968e93e24fc3b7f090064744c10_129.png)

![](img/abd35968e93e24fc3b7f090064744c10_131.png)

![](img/abd35968e93e24fc3b7f090064744c10_133.png)

![](img/abd35968e93e24fc3b7f090064744c10_135.png)

![](img/abd35968e93e24fc3b7f090064744c10_137.png)

![](img/abd35968e93e24fc3b7f090064744c10_138.png)

![](img/abd35968e93e24fc3b7f090064744c10_140.png)

![](img/abd35968e93e24fc3b7f090064744c10_142.png)

剧本文件是 Ansible 自动化的核心，它使用 YAML 格式编写，描述了一系列要在目标主机上执行的任务。我们将在下一章详细学习。

![](img/abd35968e93e24fc3b7f090064744c10_143.png)

![](img/abd35968e93e24fc3b7f090064744c10_145.png)

### 临时命令

在深入 Playbook 之前，可以使用临时命令快速执行单个任务，无需编写完整的剧本。

**语法**：
```bash
ansible <主机或组> -m <模块名> -a “<模块参数>”
```
**示例**：在所有主机上使用 `ping` 模块测试连通性
```bash
ansible all -m ping
```
**示例**：在 `webservers` 组上创建一个用户
```bash
ansible webservers -m user -a “name=testuser state=present”
```
要查看某个模块的用法，可以使用 `ansible-doc` 命令：
```bash
ansible-doc user
```

---

## 第三章：编写第一个 Playbook

![](img/abd35968e93e24fc3b7f090064744c10_147.png)

![](img/abd35968e93e24fc3b7f090064744c10_149.png)

![](img/abd35968e93e24fc3b7f090064744c10_151.png)

![](img/abd35968e93e24fc3b7f090064744c10_153.png)

上一节我们学习了 Ansible 的基础操作和核心文件，本节中我们将开始编写并运行自己的 Ansible Playbook。

![](img/abd35968e93e24fc3b7f090064744c10_155.png)

![](img/abd35968e93e24fc3b7f090064744c10_157.png)

![](img/abd35968e93e24fc3b7f090064744c10_159.png)

![](img/abd35968e93e24fc3b7f090064744c10_161.png)

Playbook 是 Ansible 的自动化脚本，它由一个或多个“剧本”组成，每个“剧本”又包含了一系列要在指定主机上执行的任务。

![](img/abd35968e93e24fc3b7f090064744c10_163.png)

![](img/abd35968e93e24fc3b7f090064744c10_165.png)

### Playbook 结构解析

![](img/abd35968e93e24fc3b7f090064744c10_167.png)

![](img/abd35968e93e24fc3b7f090064744c10_169.png)

一个最简单的 Playbook 结构如下：
```yaml
---
- name: 第一个 Play
  hosts: webservers
  tasks:
    - name: 确保用户 tom 存在
      user:
        name: tom
        state: present
...
```
**结构分解**：
1.  `---`：YAML 文件开始标记。
2.  `- name: 第一个 Play`：定义一个 Play，并为其命名。
3.  `hosts: webservers`：指定这个 Play 在哪些主机（或主机组）上执行。
4.  `tasks:`：任务列表的开始。
5.  `- name: 确保用户 tom 存在`：定义第一个任务，并命名。
6.  `user:`：指定使用 `user` 模块。
7.  `name: tom` 和 `state: present`：是 `user` 模块的参数，表示创建用户 `tom`（如果不存在）。
8.  `...`：YAML 文件结束标记（通常可省略）。

![](img/abd35968e93e24fc3b7f090064744c10_171.png)

![](img/abd35968e93e24fc3b7f090064744c10_173.png)

![](img/abd35968e93e24fc3b7f090064744c10_175.png)

![](img/abd35968e93e24fc3b7f090064744c10_177.png)

![](img/abd35968e93e24fc3b7f090064744c10_178.png)

![](img/abd35968e93e24fc3b7f090064744c10_180.png)

**关键规则**：
*   **缩进**：YAML 严格依赖缩进来表示层级关系。同一层级的元素必须左对齐，子元素比父元素多缩进（通常两个空格）。
*   **冒号**：键后面的冒号必须紧跟一个空格。

![](img/abd35968e93e24fc3b7f090064744c10_182.png)

![](img/abd35968e93e24fc3b7f090064744c10_184.png)

### 运行与检查 Playbook

![](img/abd35968e93e24fc3b7f090064744c10_186.png)

![](img/abd35968e93e24fc3b7f090064744c10_188.png)

![](img/abd35968e93e24fc3b7f090064744c10_190.png)

![](img/abd35968e93e24fc3b7f090064744c10_192.png)

保存 Playbook 文件（通常以 `.yml` 或 `.yaml` 结尾），然后使用以下命令运行：
```bash
ansible-playbook my_first_playbook.yml
```
在运行前，建议先进行语法检查或模拟运行：
```bash
# 语法检查
ansible-playbook my_first_playbook.yml --syntax-check

![](img/abd35968e93e24fc3b7f090064744c10_194.png)

![](img/abd35968e93e24fc3b7f090064744c10_196.png)

![](img/abd35968e93e24fc3b7f090064744c10_198.png)

![](img/abd35968e93e24fc3b7f090064744c10_199.png)

# 模拟运行（不实际执行任何更改）
ansible-playbook my_first_playbook.yml -C
```
可以使用 `-v`、`-vv`、`-vvv` 来增加输出信息的详细程度，便于调试。

![](img/abd35968e93e24fc3b7f090064744c10_201.png)

![](img/abd35968e93e24fc3b7f090064744c10_203.png)

![](img/abd35968e93e24fc3b7f090064744c10_204.png)

### 多 Play 示例

![](img/abd35968e93e24fc3b7f090064744c10_206.png)

![](img/abd35968e93e24fc3b7f090064744c10_208.png)

![](img/abd35968e93e24fc3b7f090064744c10_210.png)

![](img/abd35968e93e24fc3b7f090064744c10_212.png)

![](img/abd35968e93e24fc3b7f090064744c10_213.png)

一个 Playbook 可以包含多个 Play，每个 Play 可以针对不同的主机组执行不同的任务。
```yaml
---
- name: 配置 Web 服务器
  hosts: webservers
  tasks:
    - name: 安装 Apache
      yum:
        name: httpd
        state: latest

- name: 测试 Web 服务
  hosts: localhost
  tasks:
    - name: 访问测试页面
      uri:
        url: http://serverA.lab.example.com
        status_code: 200
...
```

---

![](img/abd35968e93e24fc3b7f090064744c10_215.png)

![](img/abd35968e93e24fc3b7f090064744c10_217.png)

![](img/abd35968e93e24fc3b7f090064744c10_219.png)

![](img/abd35968e93e24fc3b7f090064744c10_221.png)

## 总结

![](img/abd35968e93e24fc3b7f090064744c10_223.png)

![](img/abd35968e93e24fc3b7f090064744c10_225.png)

![](img/abd35968e93e24fc3b7f090064744c10_226.png)

![](img/abd35968e93e24fc3b7f090064744c10_228.png)

![](img/abd35968e93e24fc3b7f090064744c10_230.png)

本节课中我们一起学习了 Ansible 自动化入门的关键知识。

![](img/abd35968e93e24fc3b7f090064744c10_232.png)

![](img/abd35968e93e24fc3b7f090064744c10_234.png)

![](img/abd35968e93e24fc3b7f090064744c10_236.png)

![](img/abd35968e93e24fc3b7f090064744c10_238.png)

我们首先了解了 Ansible 的基本概念、优势（尤其是无需代理和幂等性）以及它在 DevOps 中的角色。接着，我们搭建了实验环境，并在控制节点上安装了 Ansible。

![](img/abd35968e93e24fc3b7f090064744c10_240.png)

![](img/abd35968e93e24fc3b7f090064744c10_242.png)

![](img/abd35968e93e24fc3b7f090064744c10_244.png)

![](img/abd35968e93e24fc3b7f090064744c10_246.png)

然后，我们深入探讨了 Ansible 的三大核心文件：**清单文件** 用于定义管理对象，**配置文件** 用于定制 Ansible 行为，**剧本文件** 是自动化的蓝图。我们还学习了如何使用 **临时命令** 快速执行单次任务。

![](img/abd35968e93e24fc3b7f090064744c10_248.png)

![](img/abd35968e93e24fc3b7f090064744c10_250.png)

![](img/abd35968e93e24fc3b7f090064744c10_252.png)

![](img/abd35968e93e24fc3b7f090064744c10_254.png)

最后，我们掌握了 **Playbook 的基本结构和编写规则**，学会了如何定义 Play、指定目标主机、编排任务列表，并成功运行了第一个自动化剧本。

![](img/abd35968e93e24fc3b7f090064744c10_256.png)

![](img/abd35968e93e24fc3b7f090064744c10_258.png)

![](img/abd35968e93e24fc3b7f090064744c10_260.png)

![](img/abd35968e93e24fc3b7f090064744c10_262.png)

这些内容是后续所有 Ansible 学习的基础，请务必理解透彻并完成相关实验。下一节课，我们将学习更高级的主题，如变量、循环和条件判断。