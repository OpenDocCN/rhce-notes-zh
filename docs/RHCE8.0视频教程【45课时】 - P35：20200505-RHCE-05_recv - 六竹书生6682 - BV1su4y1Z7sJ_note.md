# Ansible教程：P35：Ansible简介与基础安装

在本节课中，我们将要学习自动化运维工具Ansible的基础知识，包括其核心概念、特点、架构以及如何安装和进行最基本的连接测试。

## 概述

Ansible是一款开源的自动化运维工具，主要用于配置管理、应用部署和任务编排。它基于Python开发，采用无代理架构，通过SSH协议进行通信，具有简单易用、功能强大等特点，非常适合管理多台服务器。

## Ansible简介

![](img/a00d6ab45a9368920ab4da909eb9f4a9_1.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_3.png)

上一节我们介绍了自动化运维的背景，本节中我们来看看Ansible这款工具的具体情况。

Ansible于2012年3月9日发布第一个版本，后于2015年10月17日被红帽公司收购。它基于Python语言开发，这使其在国内拥有广泛的用户基础，因为Python语言易于理解和进行二次开发，可以更好地适应不同公司的业务需求。

### Ansible的主要特点

![](img/a00d6ab45a9368920ab4da909eb9f4a9_5.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_7.png)

以下是Ansible区别于其他运维工具的几个核心特点：

![](img/a00d6ab45a9368920ab4da909eb9f4a9_9.png)

1.  **无代理架构**：Ansible不需要在被管理的节点（称为被控端）上预先安装任何代理软件。它直接通过SSH协议连接到目标主机并执行任务。这使得部署和管理变得非常轻量级。
    *   **代码示例**：控制节点通过SSH直接连接被控端，无需安装额外守护进程。
2.  **模块化设计**：Ansible的所有功能都通过模块实现。模块可以看作是执行特定任务的独立单元，例如管理用户、安装软件包、操作文件等。用户也可以根据需求编写自定义模块。
    *   **公式/概念**：`任务执行 = 调用模块 + 传递参数`
3.  **使用声明式语言**：Ansible使用YAML语言编写“剧本”，这种语言结构清晰、易于阅读和编写，用于描述系统的期望状态，而非具体的执行步骤。
4.  **基于SSH的安全通信**：所有数据传输都通过加密的SSH通道进行，保证了操作的安全性。
5.  **幂等性**：这是Ansible一个非常重要的特性。一个任务无论执行多少次，最终的结果都是一致的。例如，使用Ansible创建一个目录，如果目录已存在，则不会重复创建或报错，而是直接返回成功状态。
    *   **示例对比**：
        *   **普通Linux命令**：`mkdir /tmp/aa` 执行第二次时，若目录已存在则会报错。
        *   **Ansible模块**：`ansible all -m file -a "path=/tmp/aa state=directory"` 无论执行多少次，最终都会确保 `/tmp/aa` 目录存在，且不会报错。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_11.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_13.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_15.png)

### 与其他运维工具对比

![](img/a00d6ab45a9368920ab4da909eb9f4a9_17.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_19.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_21.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_23.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_25.png)

在自动化运维领域，除了Ansible，还有几款常见的工具：

![](img/a00d6ab45a9368920ab4da909eb9f4a9_27.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_29.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_31.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_33.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_35.png)

*   **SaltStack**：同样基于Python开发，但**需要**在被控端部署代理（Minion）。由于其有代理常驻内存，执行效率和实时性更高，功能也更强大，适合超大型环境。
*   **Puppet**：基于Ruby语言开发，功能强大但配置复杂，软件相对“重量级”，在国内的普及度和二次开发便利性上不如基于Python的工具。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_37.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_39.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_41.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_43.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_45.png)

## Ansible架构与工作原理

![](img/a00d6ab45a9368920ab4da909eb9f4a9_47.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_49.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_51.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_53.png)

了解了Ansible的特点后，我们来看看它的体系架构是如何工作的。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_55.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_57.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_59.png)

Ansible架构主要包含以下组件：
*   **控制节点**：安装并运行Ansible的主机，用于发起管理操作。
*   **被控节点**：被Ansible管理的主机列表。
*   **清单**：一个INI格式的文件（默认位于 `/etc/ansible/hosts`），用于定义被管理的主机或主机组。
*   **模块**：Ansible执行任务的具体工具。
*   **剧本**：将多个任务组织在一起的YAML文件。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_61.png)

其基本工作流程如下：
1.  用户通过Ansible命令或剧本发起任务。
2.  Ansible读取清单文件，确定目标主机。
3.  控制节点通过SSH连接到被控节点。
4.  控制节点将所需的模块（通常是Python脚本）推送到被控节点的临时目录。
5.  在被控节点上执行模块，并返回结果给控制节点。
6.  清理被控节点上的临时文件。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_63.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_65.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_67.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_69.png)

## 安装Ansible

![](img/a00d6ab45a9368920ab4da909eb9f4a9_71.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_73.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_75.png)

理论部分介绍完毕，本节中我们动手搭建一个基础的Ansible实验环境。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_77.png)

### 环境准备

![](img/a00d6ab45a9368920ab4da909eb9f4a9_79.png)

我们将搭建一个简单的实验环境：
*   **控制节点**：1台，主机名 `control.xtt.com`，IP `192.168.127.100`
*   **被控节点**：3台
    *   `web1.xtt.com` (IP: `192.168.127.201`)
    *   `web2.xtt.com` (IP: `192.168.127.202`)
    *   `db1.xtt.com` (IP: `192.168.127.211`)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_81.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_83.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_85.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_87.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_89.png)

> 注意：请确保所有主机之间网络互通，且控制节点可以通过SSH密码方式登录到所有被控节点（例如，密码均为 `root123`）。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_91.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_93.png)

### 在控制节点上安装Ansible

![](img/a00d6ab45a9368920ab4da909eb9f4a9_95.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_97.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_98.png)

在RHEL 8/CentOS 8系统上，可以通过配置EPEL源或本地Yum源来安装Ansible。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_100.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_102.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_104.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_106.png)

以下是配置本地Yum源并安装的步骤：

![](img/a00d6ab45a9368920ab4da909eb9f4a9_108.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_110.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_112.png)

1.  **挂载系统镜像并配置Yum源文件**：
    ```bash
    # 挂载ISO镜像
    mount /dev/sr0 /mnt

    # 创建本地源配置文件
    cat > /etc/yum.repos.d/local.repo << EOF
    [BaseOS]
    name=Local BaseOS
    baseurl=file:///mnt/BaseOS
    enabled=1
    gpgcheck=0

    [AppStream]
    name=Local AppStream
    baseurl=file:///mnt/AppStream
    enabled=1
    gpgcheck=0
    EOF
    ```

![](img/a00d6ab45a9368920ab4da909eb9f4a9_114.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_116.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_118.png)

2.  **安装Ansible软件包**：
    ```bash
    yum install ansible -y
    ```

![](img/a00d6ab45a9368920ab4da909eb9f4a9_120.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_122.png)

3.  **验证安装**：
    ```bash
    ansible --version
    ```
    此命令将输出Ansible的版本、配置路径等信息。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_124.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_126.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_128.png)

### 基础配置

![](img/a00d6ab45a9368920ab4da909eb9f4a9_130.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_132.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_134.png)

安装完成后，需要进行一些基础配置以便使用。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_136.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_138.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_140.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_142.png)

1.  **配置主机清单**：编辑 `/etc/ansible/hosts` 文件，添加被控节点。
    ```ini
    # 定义单个主机
    192.168.127.201

    # 定义主机组
    [web_servers]
    192.168.127.201
    192.168.127.202

    [db_servers]
    192.168.127.211

    # 定义嵌套组（包含其他组的组）
    [all_servers:children]
    web_servers
    db_servers
    ```

![](img/a00d6ab45a9368920ab4da909eb9f4a9_144.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_146.png)

2.  **禁用SSH主机密钥检查**（实验环境为方便起见）：编辑Ansible主配置文件 `/etc/ansible/ansible.cfg`，找到并修改以下行：
    ```ini
    host_key_checking = False
    ```
    这样可以避免首次连接时手动确认SSH指纹。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_148.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_150.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_152.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_154.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_156.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_158.png)

## 首次连接测试

现在，让我们使用Ansible进行最简单的连通性测试。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_160.png)

我们将使用 `ping` 模块。请注意，此模块并非ICMP ping，而是通过SSH登录到目标主机，然后执行一个简单的操作来测试其是否可被Ansible管理。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_162.png)

以下是测试命令：

![](img/a00d6ab45a9368920ab4da909eb9f4a9_164.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_166.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_168.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_170.png)

*   **测试单个主机**：
    ```bash
    ansible 192.168.127.201 -m ping -k
    ```
    `-k` 参数表示提示输入SSH登录密码。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_172.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_174.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_176.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_177.png)

*   **测试整个主机组**：
    ```bash
    ansible web_servers -m ping -k
    ```

![](img/a00d6ab45a9368920ab4da909eb9f4a9_179.png)

*   **测试所有主机**：
    ```bash
    ansible all -m ping -k
    ```

![](img/a00d6ab45a9368920ab4da909eb9f4a9_181.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_183.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_185.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_187.png)

如果连接成功，你会看到类似 `SUCCESS` 和 `"ping": "pong"` 的绿色输出。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_189.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_191.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_193.png)

### Ansible命令常用选项

![](img/a00d6ab45a9368920ab4da909eb9f4a9_195.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_197.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_199.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_201.png)

以下是执行Ad-Hoc命令（临时单条命令）时的一些常用选项：

![](img/a00d6ab45a9368920ab4da909eb9f4a9_203.png)

*   `-m MODULE_NAME`: 指定要使用的模块，如 `ping`, `shell`, `copy` 等。
*   `-a ‘ARGUMENTS’`: 传递给模块的参数。
*   `-k`: 询问SSH登录密码。
*   `--become` 或 `-b`: 在远程主机上使用权限提升（如sudo）。
*   `--become-user BECOME_USER`: 指定权限提升后的用户（默认为root）。
*   `-u REMOTE_USER`: 指定连接远程主机的用户名（默认为当前用户）。
*   `-i INVENTORY`: 指定自定义的主机清单文件。
*   `-v`, `-vv`, `-vvv`: 显示更详细的执行过程信息，用于调试。

![](img/a00d6ab45a9368920ab4da909eb9f4a9_205.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_207.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_209.png)

## 总结

![](img/a00d6ab45a9368920ab4da909eb9f4a9_211.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_213.png)

![](img/a00d6ab45a9368920ab4da909eb9f4a9_215.png)

本节课中我们一起学习了自动化运维工具Ansible的基础知识。我们了解了它的核心特点，如无代理、模块化、幂等性；剖析了其架构和工作原理；并亲手完成了Ansible的安装、基础配置和首次连接测试。通过本课的学习，你已经搭建起了一个可用的Ansible实验环境，并掌握了使用Ad-Hoc命令进行主机管理的基本方法。在接下来的课程中，我们将深入学习各种常用模块的使用以及如何编写功能强大的Playbook。