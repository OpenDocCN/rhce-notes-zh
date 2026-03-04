# Linux运维：P60：Ansible批量运维工具介绍及安装 🚀

## 概述
在本节课中，我们将学习一款强大的IT自动化工具——Ansible。我们将了解它的基本概念、工作原理、与其他工具的区别，并完成从环境准备到软件安装的完整过程，为后续的批量运维操作打下基础。

![](img/8d2655df71ed1d311d4c3c551655e245_1.png)

![](img/8d2655df71ed1d311d4c3c551655e245_3.png)

## 什么是Ansible？🤔
Ansible是一款于2013年推出的IT自动化与DevOps软件。它基于Python语言开发，是一款符合自动化思想的批量管理工具。目前，Ansible属于红帽公司。

## 批量管理工具的应用场景
上一节我们介绍了Ansible是什么，本节中我们来看看它的应用场景。当企业中有成百上千台服务器需要维护时，逐台登录操作效率极低。批量管理工具可以解决这个问题。

![](img/8d2655df71ed1d311d4c3c551655e245_5.png)

![](img/8d2655df71ed1d311d4c3c551655e245_7.png)

![](img/8d2655df71ed1d311d4c3c551655e245_9.png)

以下是批量管理工具的典型应用场景：
*   **批量查看信息**：在管理主机上执行一个命令（如 `free -h`），所有被管理服务器的内存信息都会返回。
*   **批量安装软件**：在管理主机上执行一个命令（如 `yum -y install [包名]`），所有节点都会自动安装指定的软件包。
*   **批量分发文件**：在管理主机上执行一个拷贝命令，可以将一个文件同时拷贝到上百台服务器。

![](img/8d2655df71ed1d311d4c3c551655e245_11.png)

![](img/8d2655df71ed1d311d4c3c551655e245_13.png)

![](img/8d2655df71ed1d311d4c3c551655e245_15.png)

![](img/8d2655df71ed1d311d4c3c551655e245_16.png)

## 常见的批量管理工具
除了Ansible，行业内还有其他批量管理工具。

![](img/8d2655df71ed1d311d4c3c551655e245_18.png)

![](img/8d2655df71ed1d311d4c3c551655e245_20.png)

![](img/8d2655df71ed1d311d4c3c551655e245_22.png)

![](img/8d2655df71ed1d311d4c3c551655e245_24.png)

![](img/8d2655df71ed1d311d4c3c551655e245_26.png)

![](img/8d2655df71ed1d311d4c3c551655e245_28.png)

![](img/8d2655df71ed1d311d4c3c551655e245_30.png)

![](img/8d2655df71ed1d311d4c3c551655e245_32.png)

以下是曾经或现在存在的批量管理工具：
*   **Puppet** 和 **Chef**：这两款工具基于Ruby语言开发，属于CS架构（客户端-服务器架构），前期部署复杂，目前在行业中已逐渐被淘汰。
*   **SaltStack**：同样采用CS架构，需要在被管理节点安装客户端代理（Agent），前期环境部署工作量较大。
*   **Ansible**：采用无代理架构，通过SSH协议进行通信，只需在管理节点安装软件，被管理节点只需开启SSH服务，部署非常轻量。

![](img/8d2655df71ed1d311d4c3c551655e245_34.png)

![](img/8d2655df71ed1d311d4c3c551655e245_36.png)

![](img/8d2655df71ed1d311d4c3c551655e245_38.png)

![](img/8d2655df71ed1d311d4c3c551655e245_40.png)

## Ansible的核心特点与优势 🏆
了解了其他工具后，我们重点分析Ansible的优势。Ansible的轻量级特性体现在它通过SSH协议连接被管理主机。只要目标主机开启了SSH服务，Ansible就能管理它，无需安装任何客户端代理程序。

![](img/8d2655df71ed1d311d4c3c551655e245_42.png)

![](img/8d2655df71ed1d311d4c3c551655e245_44.png)

Ansible的其他核心特点包括：
*   **模块化设计**：Ansible的功能由模块实现。例如，`yum`模块用于安装软件包，`copy`模块用于拷贝文件，`shell`模块用于执行系统命令。学习Ansible主要就是学习其模块的使用。
*   **模块丰富且可扩展**：Ansible提供了大量内置模块，同时也支持用户用任何语言（包括Shell）自定义模块以满足特定需求。
*   **支持Playbook**：Playbook是Ansible的“剧本”，基于YAML语法编写。它类似于Shell脚本，可以将多个任务编排在一个文件中，自动、重复地执行复杂工作。
*   **易于上手**：由于其无代理和基于SSH的特性，Ansible的学习曲线相对平缓，且拥有活跃的社区和良好的中文文档支持。

![](img/8d2655df71ed1d311d4c3c551655e245_46.png)

![](img/8d2655df71ed1d311d4c3c551655e245_48.png)

![](img/8d2655df71ed1d311d4c3c551655e245_50.png)

![](img/8d2655df71ed1d311d4c3c551655e245_52.png)

![](img/8d2655df71ed1d311d4c3c551655e245_54.png)

## 实验环境准备 🖥️
在开始安装Ansible之前，我们需要准备好实验环境。本节将搭建一个由三台虚拟机组成的简单环境：一台作为Ansible管理节点，另外两台作为被管理节点。

![](img/8d2655df71ed1d311d4c3c551655e245_56.png)

以下是环境准备的步骤：
1.  **规划主机与角色**：
    *   管理节点：主机名设置为 `ansible-server`
    *   被管理节点1：主机名设置为 `host12`
    *   被管理节点2：主机名设置为 `host13`
2.  **修改管理节点主机名**（支持大写）：
    ```bash
    hostnamectl set-hostname ansible-server --static
    ```
    退出并重新连接终端以生效。
3.  **配置本地主机名解析**：在管理节点的 `/etc/hosts` 文件中添加以下内容，以便通过主机名访问被管理节点。
    ```
    192.168.0.12 host12
    192.168.0.13 host13
    ```
4.  **配置SSH免密登录**：这是关键步骤，让Ansible管理节点可以无需密码连接到被管理节点。
    *   在管理节点生成SSH密钥（如果尚未生成）：`ssh-keygen`
    *   将公钥拷贝到两台被管理节点：
        ```bash
        ssh-copy-id root@host12
        ssh-copy-id root@host13
        ```
    *   也可以使用for循环批量操作：
        ```bash
        for i in host12 host13; do ssh-copy-id root@$i; done
        ```

![](img/8d2655df71ed1d311d4c3c551655e245_58.png)

![](img/8d2655df71ed1d311d4c3c551655e245_60.png)

![](img/8d2655df71ed1d311d4c3c551655e245_62.png)

![](img/8d2655df71ed1d311d4c3c551655e245_64.png)

## 安装Ansible 📦
环境准备就绪后，我们就可以安装Ansible了。由于Ansible包位于EPEL扩展仓库中，我们需要先配置EPEL源。

![](img/8d2655df71ed1d311d4c3c551655e245_66.png)

![](img/8d2655df71ed1d311d4c3c551655e245_68.png)

![](img/8d2655df71ed1d311d4c3c551655e245_70.png)

![](img/8d2655df71ed1d311d4c3c551655e245_72.png)

![](img/8d2655df71ed1d311d4c3c551655e245_74.png)

以下是安装Ansible的具体步骤：
1.  配置基础Yum源和EPEL源（以阿里云镜像为例）：
    ```bash
    # 备份原有repo文件（可选）
    cd /etc/yum.repos.d/ && mkdir bak && mv *.repo bak/

    # 下载阿里云Base源和EPEL源
    wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
    wget -O /etc/yum.repos.d/epel.repo https://mirrors.aliyun.com/repo/epel-7.repo
    ```
2.  清理并重建Yum缓存：
    ```bash
    yum clean all && yum makecache
    ```
3.  安装Ansible：
    ```bash
    yum -y install ansible
    ```
4.  验证安装：
    ```bash
    ansible --version
    ```
    此命令会显示Ansible的版本、配置文件路径、模块路径等重要信息。

![](img/8d2655df71ed1d311d4c3c551655e245_76.png)

![](img/8d2655df71ed1d311d4c3c551655e245_78.png)

![](img/8d2655df71ed1d311d4c3c551655e245_80.png)

![](img/8d2655df71ed1d311d4c3c551655e245_82.png)

![](img/8d2655df71ed1d311d4c3c551655e245_84.png)

## 认识Ansible配置文件与清单 📄
安装完成后，我们需要了解两个核心文件：配置文件和清单文件。主配置文件 `/etc/ansible/ansible.cfg` 定义了Ansible的默认行为，例如远程连接端口（默认为SSH的22端口）、连接用户（默认为root）等。在初始阶段，我们通常使用其默认配置。

![](img/8d2655df71ed1d311d4c3c551655e245_86.png)

![](img/8d2655df71ed1d311d4c3c551655e245_88.png)

清单文件 `/etc/ansible/hosts` 是定义被管理主机的地方。没有清单文件，Ansible就不知道要管理谁。

![](img/8d2655df71ed1d311d4c3c551655e245_90.png)

![](img/8d2655df71ed1d311d4c3c551655e245_92.png)

![](img/8d2655df71ed1d311d4c3c551655e245_94.png)

![](img/8d2655df71ed1d311d4c3c551655e245_96.png)

## 定义Ansible主机清单 🗒️
清单文件的定义非常灵活。我们可以直接使用IP地址，也可以使用主机名（前提是已做好本地解析，如我们之前在 `/etc/hosts` 中所做）。

以下是定义主机清单的几种常用方式：
*   **直接列出主机**：
    ```
    host12
    host13
    192.168.0.100
    ```
*   **分组管理（推荐）**：将功能相同的主机放入一个组，方便批量操作。
    ```
    [web_servers]
    host12
    host13

    [db_servers]
    192.168.0.100
    ```
*   **使用模式匹配**：当主机名有规律时，可以使用范围简化定义。
    ```
    [web_servers]
    host[11:13] # 这等价于 host11, host12, host13
    ```
定义好清单后，可以使用以下命令测试Ansible是否能识别清单中的主机：
```bash
# 列出所有主机
ansible all --list-hosts
# 列出指定组的主机
ansible web_servers --list-hosts
```

![](img/8d2655df71ed1d311d4c3c551655e245_98.png)

![](img/8d2655df71ed1d311d4c3c551655e245_100.png)

## 总结
本节课中我们一起学习了批量运维工具Ansible。我们从其定义和应用场景入手，对比了它与Puppet、SaltStack等工具的区别，并重点讲解了其无代理、基于SSH的轻量级优势。随后，我们一步步完成了实验环境搭建、SSH免密认证、软件安装以及核心的主机清单定义。现在，你已经拥有了一个可以开始进行批量运维操作的Ansible基础环境。在接下来的课程中，我们将深入探索Ansible强大模块的使用。