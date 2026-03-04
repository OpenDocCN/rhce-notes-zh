# Linux运维培训教程：P62：Ansible模块使用 🚀

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_1.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_3.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_5.png)

## 概述
在本节课中，我们将学习Ansible模块的核心概念与使用方法。Ansible是一个强大的批量运维管理工具，通过调用各种模块来实现对远程服务器的批量操作。我们将从模块的基本概念入手，逐步学习几个最常用模块的用法，帮助你快速上手。

---

## 模块文档命令 📖

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_7.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_9.png)

上一节我们介绍了Ansible的基本概念和主机清单定义。本节中，我们来看看如何查询和使用Ansible的模块。

Ansible采用模块化设计，其核心功能较弱，主要通过模块来实现丰富的功能。因此，理解和使用模块至关重要。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_11.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_13.png)

我们可以使用 `ansible-doc` 命令来查看模块信息。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_15.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_17.png)

*   **列出所有模块**：使用 `-l` 选项可以列出Ansible支持的所有模块。
    ```bash
    ansible-doc -l
    ```
    执行后会显示模块名和简短描述。Ansible拥有超过3000个模块，但无需担心，我们只需掌握工作中最常用的部分即可。

*   **查看特定模块帮助**：使用 `-s` 选项可以查看指定模块的详细帮助信息，包括其功能和参数。
    ```bash
    ansible-doc -s ping
    ```

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_19.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_21.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_23.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_25.png)

---

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_27.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_29.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_31.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_33.png)

## 模块的基本使用与返回值 🎨

了解了如何查询模块后，我们来学习如何调用模块并理解其执行结果。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_35.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_37.png)

调用模块的基本命令格式是：`ansible <组名> -m <模块名> [-a ‘参数’]`。其中 `-m` 指定模块，`-a` 指定模块执行的具体操作或参数。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_39.png)

Ansible命令执行后，会通过颜色清晰地反馈结果状态：
*   **红色**：表示命令执行失败或出现异常。
*   **绿色**：表示命令执行成功，且**未对远程主机造成任何改变**（例如，使用 `ping` 模块测试连通性）。
*   **黄色**：表示命令执行成功，并且**对远程主机造成了改变**（例如，创建或删除了文件）。
*   **紫色/粉色**：通常是警告信息，提示可能有更好的实践方式，一般可以忽略。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_41.png)

通过观察颜色，可以快速判断任务执行情况。

---

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_43.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_45.png)

## 常用模块详解 ⚙️

以下是几个在工作中最常使用的Ansible模块及其用法。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_47.png)

### Command 模块

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_49.png)

`command` 模块是Ansible的**默认模块**，用于在远程主机上执行命令。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_51.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_53.png)

*   **基本用法**：直接使用 `-a` 指定要执行的命令即可。因为它是默认模块，所以可以省略 `-m command`。
    ```bash
    ansible webservers -a “uptime”
    ansible webservers -a “df -h”
    ```
*   **注意事项**：`command` 模块执行命令时，**不会经过远程主机的shell（如bash）解释**。这意味着它**无法使用管道 `|`、重定向 `>`、变量 `$HOME` 等shell特有的语法和符号**。
    ```bash
    # 以下命令可能无法按预期工作或会报错
    ansible webservers -a “ls /opt/*”
    ansible webservers -a “free -h | grep Mem”
    ```

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_55.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_57.png)

### Shell 模块

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_59.png)

`shell` 模块同样用于在远程主机执行命令，它与 `command` 模块的关键区别在于：**`shell` 模块会通过远程主机的默认shell（如/bin/bash）来解释命令**。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_61.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_63.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_65.png)

*   **基本用法**：需要显式使用 `-m` 指定 `shell` 模块。
    ```bash
    ansible webservers -m shell -a “ls /opt/*”
    ansible webservers -m shell -a “free -h | grep Mem”
    ansible webservers -m shell -a “yum install -y vsftpd”
    ```
*   **与Command模块对比**：当需要执行包含管道、重定向、通配符等复杂shell特性的命令时，必须使用 `shell` 模块。对于简单的命令，两者效果相同。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_67.png)

### Script 模块

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_69.png)

`script` 模块用于将**管理节点（Ansible控制机）上的脚本**传输到远程主机并在其上执行。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_71.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_73.png)

*   **基本用法**：指定本地脚本的路径即可。
    ```bash
    ansible webservers -m script -a “/root/hello.sh”
    ```
*   **核心优势**：脚本只需存放在Ansible控制机上**一份**，无需手动拷贝到每个被管理主机。执行时，Ansible会自动将脚本分发到所有目标主机并运行，非常适合批量执行初始化、部署等任务。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_75.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_77.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_79.png)

### Copy 模块

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_81.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_83.png)

`copy` 模块用于将**管理节点上的文件或目录**复制到远程主机。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_85.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_87.png)

*   **基本用法**：需要指定源文件（`src`）和目标路径（`dest`）。
    ```bash
    ansible webservers -m copy -a “src=/root/package.tar.gz dest=/opt/”
    ```
*   **应用场景**：批量分发配置文件、安装包、脚本等，是进行软件部署和配置管理的利器。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_89.png)

---

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_91.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_93.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_95.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_97.png)

## 了解性模块 ℹ️

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_99.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_101.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_103.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_105.png)

除了上述核心模块，Ansible还为特定任务提供了专用模块。它们功能明确，但通常可以用更通用的 `shell` 模块替代，因此作为了解即可。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_107.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_109.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_111.png)

### Yum 模块

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_113.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_115.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_117.png)

`yum` 模块专门用于通过yum包管理器管理软件包（安装、卸载、更新）。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_119.png)

*   **基本用法**：
    ```bash
    # 安装软件包
    ansible webservers -m yum -a “name=vsftpd state=present”
    # 卸载软件包
    ansible webservers -m yum -a “name=vsftpd state=absent”
    ```
*   **对比Shell**：使用 `shell` 模块执行 `yum install -y vsftpd` 同样可以完成安装，并且对于已经熟悉Linux命令的用户来说更直观。`yum` 模块的优势在于其声明式的语法和更精确的状态管理。

### Service 模块

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_121.png)

`service` 模块用于管理远程主机的系统服务（启动、停止、重启、设置开机自启等）。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_123.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_125.png)

*   **基本用法**：
    ```bash
    # 启动服务
    ansible webservers -m service -a “name=vsftpd state=started”
    # 停止服务
    ansible webservers -m service -a “name=vsftpd state=stopped”
    # 设置开机自启
    ansible webservers -m service -a “name=vsftpd enabled=yes”
    ```
*   **对比Shell**：同样，使用 `shell` 模块执行 `systemctl start vsftpd` 也能达到相同目的。`service` 模块提供了跨不同初始化系统（System V, Upstart, systemd）的统一接口。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_127.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_129.png)

---

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_131.png)

## 总结 🎯

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_133.png)

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_135.png)

本节课中我们一起学习了Ansible模块的核心知识：
1.  使用 `ansible-doc` 查询模块信息和帮助。
2.  理解了Ansible命令返回值的颜色含义（红/绿/黄/紫）。
3.  深入掌握了四个最常用的核心模块：
    *   `command`：执行简单命令（默认模块）。
    *   `shell`：执行需要shell解释的复杂命令。
    *   `script`：在远程主机批量执行本地脚本。
    *   `copy`：批量拷贝文件到远程主机。
4.  了解了 `yum` 和 `service` 等专用模块，知道它们可以用更灵活的 `shell` 模块替代。

![](img/f32cb3c2b9d7c3d556f1aa35b2702613_137.png)

记住，**`shell` 模块是功能最强大、最常用的模块之一**，绝大多数批量操作都可以通过它结合熟悉的Linux命令来完成。掌握好这几个模块，你就能应对日常工作中大量的自动化运维任务。