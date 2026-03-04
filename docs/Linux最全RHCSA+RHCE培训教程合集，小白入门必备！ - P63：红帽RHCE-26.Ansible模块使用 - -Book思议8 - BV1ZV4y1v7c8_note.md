# Ansible教程：P63：Ansible模块使用

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_1.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_3.png)

## 概述

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_5.png)

在本节课中，我们将学习Ansible的核心组成部分——模块。Ansible的强大功能正是通过其丰富的模块来实现的。我们将了解如何查看模块、理解模块的返回值颜色含义，并学习几个最常用模块的基本用法，包括`command`、`shell`、`yum`、`script`、`service`和`copy`模块。通过本课，你将掌握使用Ansible对多台服务器进行批量管理的基础操作。

## 理解Ansible模块

上一节我们介绍了如何定义主机清单。本节中，我们来看看Ansible执行任务的核心工具——模块。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_7.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_9.png)

你可以将Ansible理解为一个批量的运维管理工具。它本身功能有限，其各种强大的功能（如执行命令、安装软件、管理服务、拷贝文件等）都是通过调用不同的“模块”来完成的。模块就像是Ansible的一条条“命令”。

### 查看模块文档

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_11.png)

Ansible提供了`ansible-doc`命令来查看模块信息。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_13.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_15.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_17.png)

以下是查看模块的两种常用方式：

*   **列出所有可用模块**：使用 `-l` 选项。
    ```bash
    ansible-doc -l
    ```
    执行后会列出所有模块及其简短描述。Ansible模块数量非常庞大（超过3000个），但就像Linux系统命令一样，我们只需要掌握工作中最常用的部分即可。

*   **查看特定模块的详细帮助**：使用 `-s` 选项。
    ```bash
    ansible-doc -s <模块名>
    ```
    例如，查看 `ping` 模块的帮助：
    ```bash
    ansible-doc -s ping
    ```
    这会显示该模块的功能描述、参数列表及说明。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_19.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_21.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_23.png)

### 模块返回值颜色含义

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_25.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_27.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_29.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_31.png)

在使用Ansible执行命令时，返回信息的颜色可以直观地反映操作结果。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_33.png)

以下是不同颜色的含义：

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_35.png)

*   **红色**：表示命令执行失败或出现异常。
*   **绿色**：表示命令执行成功，但**没有**对远程主机造成任何改变（例如，使用 `ping` 模块测试连通性）。
*   **黄色**：表示命令执行成功，并且**对远程主机造成了改变**（例如，创建或删除了文件）。
*   **紫色/粉色**：通常是警告信息，给出一些操作建议，可以忽略。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_37.png)

通过观察颜色，你可以快速判断批量任务在每个主机上的执行状态。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_39.png)

## 常用模块详解

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_41.png)

了解了模块的基本概念后，我们开始学习几个最核心的模块。

### Command模块

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_43.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_45.png)

`command` 模块是Ansible的**默认模块**，用于在远程主机上执行简单的命令。

它的用法非常直接，就是执行你给出的命令。但是，它有一个重要的限制：**它不支持Shell解释器中的特殊符号**，例如管道符 `|`、重定向 `>`、`<`、变量 `$` 以及通配符 `*` 等。

**基本用法示例**：
```bash
# 查看远程主机内存信息（默认模块，可省略 -m command）
ansible webservers -a “free -h”

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_47.png)

# 查看远程主机磁盘空间
ansible webservers -a “df -h”

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_49.png)

# 在远程主机创建目录（注意：不支持 && 等逻辑符号）
ansible webservers -a “mkdir /tmp/testdir”
```
**注意**：`command` 模块无法执行交互式命令（如 `vim`、`passwd`），因为Ansible无法处理交互过程。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_51.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_53.png)

### Shell模块

`shell` 模块也是用于在远程主机执行命令，它与 `command` 模块的关键区别在于：**`shell` 模块会通过远程主机的 `/bin/bash` 解释器来执行命令**。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_55.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_57.png)

这意味着，`shell` 模块支持所有Shell特性，包括管道、重定向、变量和通配符。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_59.png)

**基本用法示例**：
```bash
# 使用管道过滤命令输出
ansible webservers -m shell -a “free -h | grep Mem”

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_61.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_63.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_65.png)

# 使用重定向将命令输出保存到文件
ansible webservers -m shell -a “df -h > /tmp/diskinfo.txt”

# 使用通配符列出文件
ansible webservers -m shell -a “ls /tmp/*.log”

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_67.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_69.png)

# 安装软件包（会收到警告，建议使用yum模块，但功能上可行）
ansible webservers -m shell -a “yum install -y nginx”
```
**总结**：当需要执行的命令包含特殊符号时，使用 `shell` 模块；否则，使用默认的 `command` 模块即可。

### Yum模块

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_71.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_73.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_75.png)

`yum` 模块是专门用于通过Yum包管理器管理软件包（安装、卸载、升级）的模块。使用专门的模块比直接用 `shell` 模块调用 `yum` 命令更规范，也更能发挥Ansible的状态管理优势。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_77.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_79.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_81.png)

**基本用法示例**：
```bash
# 安装软件包（state=present 或 state=installed，可省略，默认为安装）
ansible webservers -m yum -a “name=nginx state=present”

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_83.png)

# 卸载软件包
ansible webservers -m yum -a “name=nginx state=absent”

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_85.png)

# 升级软件包到最新版本
ansible webservers -m yum -a “name=nginx state=latest”
```
虽然 `shell` 模块也能完成软件包管理，但在正式的Ansible使用中，更推荐使用专门的 `yum` 模块。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_87.png)

### Script模块

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_89.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_91.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_93.png)

`script` 模块用于将**管理节点（Ansible控制机）** 上的脚本文件，传输到远程主机上执行。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_95.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_97.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_99.png)

这个模块非常强大，你只需要在本地维护一份脚本，就可以让它在所有被管理主机上运行，无需手动拷贝到每台主机。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_101.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_103.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_105.png)

**基本用法示例**：
1.  在Ansible控制机上创建一个脚本，例如 `/root/deploy.sh`。
    ```bash
    #!/bin/bash
    echo “Hello from Ansible script module!” > /tmp/ansible_test.txt
    ```
2.  使用 `script` 模块在所有目标主机上执行该脚本。
    ```bash
    ansible webservers -m script -a “/root/deploy.sh”
    ```
    执行后，所有 `webservers` 组的主机的 `/tmp/` 目录下都会生成 `ansible_test.txt` 文件。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_107.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_109.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_111.png)

### Service模块

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_113.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_115.png)

`service` 模块用于管理远程主机上的服务（启动、停止、重启、设置开机自启等）。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_117.png)

**基本用法示例**：
```bash
# 启动nginx服务，并设置为开机自启
ansible webservers -m service -a “name=nginx state=started enabled=yes”

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_119.png)

# 停止nginx服务
ansible webservers -m service -a “name=nginx state=stopped”

# 重启nginx服务
ansible webservers -m service -a “name=nginx state=restarted”
```
同样，虽然可以用 `shell` 模块调用 `systemctl` 命令来实现，但使用 `service` 模块是更标准的Ansible做法。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_121.png)

### Copy模块

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_123.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_125.png)

`copy` 模块用于将管理节点上的文件或目录复制到远程主机。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_127.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_129.png)

**基本用法示例**：
```bash
# 将本地的配置文件拷贝到远程主机的指定位置
ansible webservers -m copy -a “src=/etc/nginx/nginx.conf dest=/etc/nginx/nginx.conf”

# 将本地文件拷贝到远程主机，并修改属主和权限
ansible webservers -m copy -a “src=/root/app.tar.gz dest=/opt/ owner=root group=root mode=0644”
```
`copy` 模块是实现批量文件分发的利器，例如统一部署应用、更新配置文件等。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_131.png)

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_133.png)

## 总结

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_135.png)

本节课中我们一起学习了Ansible模块的核心知识。我们首先了解了如何使用 `ansible-doc` 查看模块，并通过返回值颜色快速判断任务执行状态。接着，我们深入学习了六个最常用的模块：
*   `command` 和 `shell` 模块用于执行命令，后者支持Shell特性。
*   `yum` 和 `service` 模块用于规范的软件包和服务管理。
*   `script` 模块能便捷地在所有主机上运行本地脚本。
*   `copy` 模块是进行批量文件分发的标准方式。

![](img/ea05b9bc7f69fbb9c49d9654ac48d736_137.png)

掌握这些模块，你已经能够使用Ansible完成许多常见的批量运维任务。记住，模块是Ansible的基石，熟练运用它们是你迈向自动化运维的关键一步。