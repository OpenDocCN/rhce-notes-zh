# Linux运维全套培训课程：P62：红帽RHCE-25.Ansible批量运维工具介绍及安装 🚀

在本节课中，我们将要学习一款强大的IT自动化工具——Ansible。我们将了解它的基本概念、工作原理、与其他工具的区别，并完成从环境准备到工具安装的完整流程。

---

## 什么是Ansible？ 🤔

Ansible是一款于2013年推出的IT自动化与配置管理软件。它符合自动化思想，基于Python语言开发。在后续的DevOps学习阶段，我们会接触到许多自动化工具，它们能让运维工作变得更高效。

Ansible是众多自动化工具中非常出色的一款**批量管理工具**，目前隶属于红帽公司。

## 批量管理工具的应用场景

![](img/d2ee1fa311c1794e412115bba3baf00c_1.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_3.png)

在企业中，运维人员常常需要维护成百上千台服务器。如果需要对这一群服务器进行统一操作，例如安装软件包、备份数据或查看系统状态，逐台手动操作效率极低。

批量管理工具就是为了解决这个问题而生的。使用Ansible，你可以在**管理主机**上执行一个命令，该命令就会自动在**所有被管理节点**上执行。例如：
*   查看所有服务器的内存使用情况：`free -h`
*   为所有服务器安装一个软件包：`yum -y install [包名]`
*   将一个文件同时拷贝到上百台服务器

这极大地提升了运维效率。

## 常见的批量管理工具

除了Ansible，行业内还有其他批量管理工具，例如 **Chef** 和 **Puppet**。然而，这两款工具由于基于相对冷门的Ruby语言，使用较为复杂，目前在企业中的占有率已经很低，逐渐被淘汰。因此，我们只需要知道它们的存在即可，无需深入。

![](img/d2ee1fa311c1794e412115bba3baf00c_5.png)

接下来，我们重点对比一下 **SaltStack** 和 **Ansible** 的差异。

![](img/d2ee1fa311c1794e412115bba3baf00c_7.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_9.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_11.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_13.png)

### SaltStack vs. Ansible

![](img/d2ee1fa311c1794e412115bba3baf00c_15.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_16.png)

上一节我们介绍了批量管理工具的概念，本节中我们来看看两款主流工具的核心区别。

**SaltStack** 采用 **C/S（客户端/服务器）架构**。这意味着：
*   需要在**管理节点**安装服务端软件。
*   需要在**每一台被管理节点**安装客户端软件（Agent）。
*   当被管理节点数量庞大时，前期的环境部署工作量会非常大。

![](img/d2ee1fa311c1794e412115bba3baf00c_18.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_20.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_22.png)

**Ansible** 则是一款**轻量级**的批量管理工具，其“轻量”体现在：
*   它基于 **SSH协议** 进行通信。
*   只需要在**管理节点**安装Ansible。
*   **被管理节点**只需要开启SSH服务即可，无需安装任何额外代理程序。

![](img/d2ee1fa311c1794e412115bba3baf00c_24.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_26.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_28.png)

这就像我们平时使用Xshell通过SSH连接Linux服务器一样。只要服务器开启了SSH服务，Ansible就能连接并管理它。因此，Ansible的部署和上手难度远低于SaltStack，这也是其目前市场占有率最高的原因。

![](img/d2ee1fa311c1794e412115bba3baf00c_30.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_32.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_34.png)

## Ansible的核心特点

![](img/d2ee1fa311c1794e412115bba3baf00c_36.png)

了解了Ansible的架构优势后，我们来看看它的一些核心特点。

![](img/d2ee1fa311c1794e412115bba3baf00c_38.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_40.png)

1.  **基于Python与Paramiko**：Ansible使用Python编写，并利用Python的 `paramiko` 模块实现SSH连接。这意味着只要被管理节点开启SSH服务，Ansible就能进行管理。
2.  **模块化设计**：Ansible的所有功能都通过**模块**实现。模块就是Ansible执行特定任务的命令。例如：
    *   `yum` 模块：用于安装软件包。
    *   `copy` 模块：用于拷贝文件。
    *   `shell` 模块：用于执行系统命令。
    学习Ansible，主要就是学习如何使用这些模块。
3.  **支持自定义模块**：如果内置模块无法满足需求，Ansible允许用户用任何语言（包括Shell）开发自定义模块。
4.  **支持Playbook**：Playbook是Ansible的“剧本”，类似于Shell脚本。它使用YAML语法编写，可以将多个模块任务组织在一个文件中，用于完成复杂、重复的自动化工作。我们将在后续阶段深入学习Playbook。

![](img/d2ee1fa311c1794e412115bba3baf00c_42.png)

## 实验环境准备 🛠️

![](img/d2ee1fa311c1794e412115bba3baf00c_44.png)

理论部分介绍完毕，现在开始动手实践。我们将搭建一个简单的Ansible实验环境。

我们的环境包括三台CentOS 7虚拟机：
*   **管理节点**：`ansible-server`
*   **被管理节点**：`host12`， `host13`

![](img/d2ee1fa311c1794e412115bba3baf00c_46.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_48.png)

以下所有操作，如无特别说明，均在 **`ansible-server`** 管理节点上执行。

![](img/d2ee1fa311c1794e412115bba3baf00c_50.png)

### 步骤一：配置主机名与本地解析

![](img/d2ee1fa311c1794e412115bba3baf00c_52.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_54.png)

首先，我们需要为管理节点设置一个易于识别的主机名，并建立主机名到IP地址的本地映射。

1.  修改管理节点主机名：
    ```bash
    hostnamectl set-hostname ansible-server --static
    ```
    执行后退出重新登录，主机名生效。

![](img/d2ee1fa311c1794e412115bba3baf00c_56.png)

2.  配置本地hosts文件，实现主机名解析：
    ```bash
    vi /etc/hosts
    ```
    在文件末尾添加以下内容（请根据你的实际IP修改）：
    ```
    192.168.0.12 host12
    192.168.0.13 host13
    ```
    保存退出后，测试解析是否成功：
    ```bash
    ping host12
    ping host13
    ```

### 步骤二：配置SSH免密登录

![](img/d2ee1fa311c1794e412115bba3baf00c_58.png)

由于Ansible通过SSH连接被管理节点，为了避免每次执行命令时都输入密码，我们需要配置从管理节点到所有被管理节点的SSH免密登录。

![](img/d2ee1fa311c1794e412115bba3baf00c_60.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_62.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_64.png)

1.  在`ansible-server`上生成SSH密钥对（如果已有则跳过）：
    ```bash
    ssh-keygen
    ```
2.  将公钥拷贝到两台被管理节点：
    ```bash
    ssh-copy-id root@host12
    ssh-copy-id root@host13
    ```
    执行命令后，输入对应主机的`root`密码。
3.  验证免密登录：
    ```bash
    ssh host12  # 应能直接登录，无需密码
    exit
    ssh host13  # 应能直接登录，无需密码
    exit
    ```
    **小技巧**：如果机器很多，可以使用`for`循环批量拷贝密钥：
    ```bash
    for i in host12 host13; do ssh-copy-id root@$i; done
    ```

![](img/d2ee1fa311c1794e412115bba3baf00c_66.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_68.png)

## 安装Ansible 📦

![](img/d2ee1fa311c1794e412115bba3baf00c_70.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_72.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_74.png)

环境准备就绪，现在可以在管理节点上安装Ansible了。Ansible包位于EPEL扩展仓库中。

![](img/d2ee1fa311c1794e412115bba3baf00c_76.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_78.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_80.png)

以下是安装步骤：

![](img/d2ee1fa311c1794e412115bba3baf00c_82.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_84.png)

1.  配置阿里云的Base源和EPEL源：
    ```bash
    # 安装wget工具（如果未安装）
    yum install -y wget
    # 下载Base源
    wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
    # 下载EPEL源
    wget -O /etc/yum.repos.d/epel.repo https://mirrors.aliyun.com/repo/epel-7.repo
    ```
2.  清理yum缓存并安装Ansible：
    ```bash
    yum clean all
    yum makecache
    yum install -y ansible
    ```
3.  验证安装：
    ```bash
    ansible --version
    ```
    命令会输出Ansible的版本、配置文件路径、模块路径等信息。例如，可以看到默认的清单文件是 `/etc/ansible/hosts`。

![](img/d2ee1fa311c1794e412115bba3baf00c_86.png)

## 认识Ansible清单文件 📋

![](img/d2ee1fa311c1794e412115bba3baf00c_88.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_90.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_92.png)

安装完成后，最关键的一步是配置**清单文件**。清单文件用于定义Ansible需要管理的所有主机。

*   **位置**：默认位于 `/etc/ansible/hosts`
*   **作用**：Ansible执行任务时，会读取这个文件，才知道应该去连接和管理哪些机器。

以下是定义清单的几种常用方式：

1.  **直接使用IP或主机名**（适合少量主机）：
    ```
    host12
    host13
    ```

![](img/d2ee1fa311c1794e412115bba3baf00c_94.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_96.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_98.png)

2.  **使用分组**（**推荐**，适合管理多台主机）：
    ```
    [web_servers]  # 定义一个名为‘web_servers’的组
    host12
    host13

    [db_servers]   # 可以定义多个组
    host14
    host15
    ```
    在命令中，可以通过组名 `web_servers` 来批量操作该组内所有主机。

![](img/d2ee1fa311c1794e412115bba3baf00c_100.png)

3.  **使用模式匹配**（适合主机名有规律的情况）：
    ```
    [web_servers]
    host[11:13]  # 这将匹配 host11, host12, host13 三台主机
    ```

我们采用第二种分组方式。编辑清单文件：
```bash
vi /etc/ansible/hosts
```
在文件末尾添加：
```
[my_hosts]
host12
host13
```
保存并退出。

现在，我们可以测试Ansible是否能识别清单中的主机：
```bash
ansible all --list-hosts       # 列出所有主机
ansible my_hosts --list-hosts  # 列出指定组内的主机
```
如果能看到 `host12` 和 `host13`，说明清单配置成功。

---

本节课中我们一起学习了Ansible批量运维工具的基础知识。我们了解了它的诞生背景、解决了什么问题，以及相较于SaltStack等工具的轻量级优势。通过动手实验，我们完成了Ansible管理节点的环境准备、软件安装，并学会了如何编写核心的清单文件来定义被管理主机。

![](img/d2ee1fa311c1794e412115bba3baf00c_102.png)

![](img/d2ee1fa311c1794e412115bba3baf00c_104.png)

下一节，我们将开始学习Ansible的核心——模块的使用，真正开始体验批量运维的强大功能。