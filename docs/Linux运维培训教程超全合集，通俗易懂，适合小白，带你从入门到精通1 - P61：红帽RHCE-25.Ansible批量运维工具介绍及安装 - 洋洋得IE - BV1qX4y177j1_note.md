# Linux运维培训教程：1：Ansible批量运维工具介绍及安装 🚀

在本节课中，我们将要学习一款强大的IT自动化工具——Ansible。我们将了解它的基本概念、特点、工作原理，并完成从环境准备到软件安装的完整过程。

---

## 什么是Ansible？🤔

Ansible是2013年推出的一款IT自动化DevOps软件。它是一款符合自动化思想的批量管理工具，基于Python语言开发。Ansible现属于红帽公司，因为它已被红帽收购。

![](img/b804509aa2694a399f62f311ef1c0e0a_1.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_3.png)

## 批量管理工具的应用场景

在企业中，运维人员常常需要维护成百上千台服务器。如果对每台服务器都进行单独操作，效率会非常低下。批量管理工具就是为了解决这个问题而生的。

以下是批量管理工具能做的事情：
*   只需在管理主机上执行一个命令（如 `free -h`），所有被管理节点的内存信息都会反馈回来。
*   只需在管理主机上执行一个命令（如 `yum -y install`），所有被管理节点都会自动安装指定的软件包。
*   只需在管理主机上执行一个命令，一个文件可以同时拷贝到上百台服务器。

除了Ansible，行业中还有其他批量管理工具，如Chef和Puppet。但由于它们基于Ruby语言且相对复杂，目前在企业中已逐渐被淘汰。

![](img/b804509aa2694a399f62f311ef1c0e0a_5.png)

## Ansible与其他工具的对比

![](img/b804509aa2694a399f62f311ef1c0e0a_7.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_9.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_11.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_13.png)

上一节我们介绍了批量管理工具的概念，本节中我们来看看Ansible与另一款工具SaltStack的差异。

![](img/b804509aa2694a399f62f311ef1c0e0a_15.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_16.png)

SaltStack（简称Salt）采用C/S架构。这意味着，管理节点需要安装服务端软件，而**每一台**被管理节点都需要安装客户端软件。前期环境部署工作量较大。

![](img/b804509aa2694a399f62f311ef1c0e0a_18.png)

Ansible则是一款轻量级的批量管理工具。它的“轻量”体现在其工作方式上。

![](img/b804509aa2694a399f62f311ef1c0e0a_20.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_22.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_24.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_26.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_28.png)

**Ansible通过SSH协议连接被管理主机。**

![](img/b804509aa2694a399f62f311ef1c0e0a_30.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_32.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_34.png)

就像我们平时用Xshell连接虚拟机一样，只要目标主机开启了SSH服务，Ansible就能连接并管理它。因此，使用Ansible只需要在管理主机上进行部署，被管理主机无需安装任何额外代理程序，非常便捷。

![](img/b804509aa2694a399f62f311ef1c0e0a_36.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_38.png)

## Ansible的核心特点

![](img/b804509aa2694a399f62f311ef1c0e0a_40.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_42.png)

了解了Ansible的轻量级优势后，我们再来看看它的其他核心特点。

![](img/b804509aa2694a399f62f311ef1c0e0a_44.png)

*   **基于Python与Paramiko模块**：Ansible使用Python编写，并利用其Paramiko模块实现SSH连接。你只需要了解，只要被管理主机开启SSH，Ansible就能管理它。
*   **模块化设计，模块丰富**：模块是Ansible实现功能的核心。每个模块负责一项具体任务。
    *   例如，`yum`模块用于安装软件包。
    *   例如，`copy`模块用于拷贝文件。
    *   例如，`shell`模块用于执行系统命令。
    Ansible提供了大量现成模块，也允许用户用任何语言（包括Shell）自定义模块。
*   **支持Playbook**：Playbook是Ansible的“剧本”，类似于Shell脚本。它基于YAML语法编写，可以将多个模块任务组织在一个文件中，用于完成复杂、重复的自动化工作。
*   **强大的角色功能**：Ansible的Roles（角色）功能比Playbook更强大、结构化，适合部署非常复杂的应用环境（如OpenStack云平台）。

## 实验环境准备 🛠️

![](img/b804509aa2694a399f62f311ef1c0e0a_46.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_48.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_50.png)

理论部分介绍完毕，现在开始动手实践。本节我们将准备一个由三台虚拟机组成的实验环境。

![](img/b804509aa2694a399f62f311ef1c0e0a_52.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_53.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_55.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_57.png)

我们的实验环境规划如下：
*   **管理主机**：`ansible-server` （IP: 192.168.0.11）
*   **被管理主机1**：`host12` （IP: 192.168.0.12）
*   **被管理主机2**：`host13` （IP: 192.168.0.13）

> **注意**：以下所有操作均在`ansible-server`这台管理主机上执行。

![](img/b804509aa2694a399f62f311ef1c0e0a_59.png)

以下是具体的准备步骤：

1.  **修改主机名**：为三台机器分别设置好主机名。
2.  **关闭防火墙**：为避免网络连接问题，暂时关闭所有主机的防火墙。
3.  **配置本地主机解析**：在管理主机上，编辑 `/etc/hosts` 文件，添加被管理主机的IP和主机名对应关系。
    ```bash
    192.168.0.12 host12
    192.168.0.13 host13
    ```
    配置后，在管理主机上应能通过`ping host12`和`ping host13`成功解析并通信。
4.  **配置SSH免密登录**：这是关键一步。为了让Ansible能够无密码连接被管理主机，需要在管理主机上生成SSH密钥对，并将公钥分发到`host12`和`host13`。
    ```bash
    # 生成密钥对（如果已有可跳过）
    ssh-keygen
    # 将公钥拷贝到被管理主机
    ssh-copy-id root@host12
    ssh-copy-id root@host13
    ```
    也可以使用循环命令批量操作：
    ```bash
    for i in host12 host13; do ssh-copy-id root@$i; done
    ```
    完成后，测试`ssh host12`应能直接登录，无需密码。

![](img/b804509aa2694a399f62f311ef1c0e0a_61.png)

## 安装Ansible 📦

![](img/b804509aa2694a399f62f311ef1c0e0a_63.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_65.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_67.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_69.png)

环境准备就绪后，我们开始在管理主机上安装Ansible。

![](img/b804509aa2694a399f62f311ef1c0e0a_71.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_73.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_75.png)

Ansible软件包位于EPEL（Extra Packages for Enterprise Linux）扩展仓库中。我们需要先配置EPEL源。

![](img/b804509aa2694a399f62f311ef1c0e0a_77.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_79.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_81.png)

1.  **配置阿里云Base源和EPEL源**：
    ```bash
    # 下载并安装Base源
    wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
    # 下载并安装EPEL源
    wget -O /etc/yum.repos.d/epel.repo https://mirrors.aliyun.com/repo/epel-7.repo
    ```
2.  **安装Ansible**：
    ```bash
    yum -y install ansible
    ```
3.  **验证安装**：
    ```bash
    ansible --version
    ```
    此命令会输出Ansible的版本信息（如2.9.27）以及重要的配置路径，如配置文件位置、模块搜索路径等。

![](img/b804509aa2694a399f62f311ef1c0e0a_83.png)

## 认识Ansible配置文件与清单

![](img/b804509aa2694a399f62f311ef1c0e0a_85.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_87.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_89.png)

安装完成后，我们来认识两个核心文件：配置文件和主机清单文件。

![](img/b804509aa2694a399f62f311ef1c0e0a_91.png)

*   **主配置文件**：`/etc/ansible/ansible.cfg`
    此文件包含Ansible的所有运行配置。初始时文件内大多是注释说明。重要的默认配置包括：
    *   `remote_port = 22`：指定连接被管理主机的SSH端口。
    *   `remote_user = root`：指定连接被管理主机时使用的默认用户。
*   **主机清单文件**：`/etc/ansible/hosts`
    这是**定义被管理主机**的文件。Ansible通过读取这个文件才知道需要管理哪些机器。

## 定义主机清单 📝

![](img/b804509aa2694a399f62f311ef1c0e0a_93.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_95.png)

主机清单文件是Ansible工作的基础。本节我们学习如何灵活地定义被管理主机。

![](img/b804509aa2694a399f62f311ef1c0e0a_97.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_99.png)

打开清单文件 `/etc/ansible/hosts`。你可以看到许多注释掉的示例。定义主机主要有以下几种方式：

1.  **直接定义IP或主机名**（适合少量主机）：
    ```
    host12
    host13
    # 或
    192.168.0.12
    192.168.0.13
    ```
2.  **分组定义**（**推荐，适合管理大量主机**）：
    使用`[组名]`的格式定义一个主机组，组名下缩进写入该组的主机。
    ```
    [webservers]
    host12
    host13

    [dbservers]
    db01.example.com
    db02.example.com
    ```
    之后在命令中可以使用组名（如`webservers`）来指代该组所有主机。
3.  **使用模式匹配**（适合有规律的主机名）：
    如果主机名有数字序列规律，可以使用`[start:end]`的格式简化定义。
    ```
    [webcluster]
    www[01:06].example.com # 代表 www01 到 www06 共6台主机
    ```
    在我们的实验中，也可以这样定义：
    ```
    [myhosts]
    host[12:13]
    ```

定义好清单后，可以使用以下命令测试，查看清单中的所有主机：
```bash
ansible all --list-hosts
```
或查看特定组的主机：
```bash
ansible webservers --list-hosts
```

---

![](img/b804509aa2694a399f62f311ef1c0e0a_101.png)

![](img/b804509aa2694a399f62f311ef1c0e0a_103.png)

本节课中我们一起学习了Ansible的基本概念、优势、实验环境搭建、软件安装以及核心的主机清单配置。你已经掌握了让Ansible“知道”它需要管理哪些机器的方法。在接下来的课程中，我们将开始学习如何使用Ansible的各种模块来执行实际的批量管理任务。