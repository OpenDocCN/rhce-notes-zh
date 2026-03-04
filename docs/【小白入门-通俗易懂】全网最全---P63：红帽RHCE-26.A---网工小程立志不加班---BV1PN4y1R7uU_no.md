# Ansible教程：P63：Ansible模块使用 🚀

![](img/f72fb26331825f491687b091adf674b1_1.png)

![](img/f72fb26331825f491687b091adf674b1_3.png)

在本节课中，我们将学习Ansible的核心组成部分——模块。Ansible的强大功能正是通过其丰富的模块来实现的。我们将了解如何查看模块、使用模块，并掌握几个最常用模块的基本用法。

![](img/f72fb26331825f491687b091adf674b1_5.png)

## 概述

Ansible本质上是一个批量运维管理工具，用于实现对多台服务器的集中、批量管理。它采用模块化设计，通过调用不同的模块来完成各种任务，例如执行命令、管理文件、安装软件等。

上一节我们介绍了如何定义主机清单，本节中我们来看看如何使用Ansible的模块来对这些主机进行操作。

![](img/f72fb26331825f491687b091adf674b1_7.png)

![](img/f72fb26331825f491687b091adf674b1_9.png)

## 查看模块文档

Ansible的功能通过模块实现，因此了解模块至关重要。我们可以使用 `ansible-doc` 命令来查看模块信息。

![](img/f72fb26331825f491687b091adf674b1_11.png)

### 列出所有模块

![](img/f72fb26331825f491687b091adf674b1_13.png)

![](img/f72fb26331825f491687b091adf674b1_15.png)

使用 `-l` 选项可以列出Ansible所有可用的模块。

![](img/f72fb26331825f491687b091adf674b1_17.png)

```bash
ansible-doc -l
```

执行上述命令后，会显示一个很长的列表，左边是模块名称，右边是模块的简要描述。Ansible拥有超过3000个模块，功能覆盖了系统管理、云平台、数据库、网络设备等各个领域。

![](img/f72fb26331825f491687b091adf674b1_19.png)

![](img/f72fb26331825f491687b091adf674b1_21.png)

**注意**：我们无需记忆所有模块，就像学习Linux系统命令一样，只需掌握工作中最常用的部分即可。

![](img/f72fb26331825f491687b091adf674b1_23.png)

![](img/f72fb26331825f491687b091adf674b1_25.png)

![](img/f72fb26331825f491687b091adf674b1_27.png)

![](img/f72fb26331825f491687b091adf674b1_29.png)

### 查看特定模块帮助

![](img/f72fb26331825f491687b091adf674b1_31.png)

![](img/f72fb26331825f491687b091adf674b1_33.png)

如果我们知道某个模块的名字，例如 `ping` 模块，可以使用 `-s` 选项来查看该模块的详细帮助信息。

```bash
ansible-doc -s ping
```

![](img/f72fb26331825f491687b091adf674b1_35.png)

![](img/f72fb26331825f491687b091adf674b1_37.png)

命令输出会显示该模块的描述、参数等信息。模块的参数可以理解为命令的选项，用于调整模块的具体行为。有些模块可以不带参数直接使用。

## Ansible命令返回值颜色解读

![](img/f72fb26331825f491687b091adf674b1_39.png)

![](img/f72fb26331825f491687b091adf674b1_41.png)

在使用Ansible执行任务时，返回信息的颜色可以直观地告诉我们任务执行的状态。

以下是颜色代表的含义：
*   **红色**：表示命令执行失败或出现错误。
*   **绿色**：表示命令执行成功，但**未对远程主机造成任何改变**（例如使用 `ping` 模块测试连通性）。
*   **黄色**：表示命令执行成功，并且**对远程主机造成了改变**（例如创建或删除了文件）。
*   **紫色/粉色**：通常是警告信息，给出一些操作建议，一般可以忽略。

![](img/f72fb26331825f491687b091adf674b1_43.png)

通过观察颜色，我们可以快速判断任务执行结果，这比阅读详细的返回文本更高效。

![](img/f72fb26331825f491687b091adf674b1_45.png)

## 常用模块详解

了解了模块的基本概念后，我们开始学习几个最核心、最常用的Ansible模块。

### 1. command 模块

![](img/f72fb26331825f491687b091adf674b1_47.png)

![](img/f72fb26331825f491687b091adf674b1_49.png)

`command` 模块是Ansible的**默认模块**，用于在远程主机上执行简单的命令。

**基本语法**：
```bash
ansible <组名> -m command -a “<要执行的命令>”
```
由于 `command` 是默认模块，`-m command` 通常可以省略。

![](img/f72fb26331825f491687b091adf674b1_51.png)

![](img/f72fb26331825f491687b091adf674b1_53.png)

**示例**：
```bash
# 查看远程主机的内存使用情况
ansible webservers -a “free -h”
# 查看远程主机的磁盘空间
ansible webservers -a “df -h”
```

![](img/f72fb26331825f491687b091adf674b1_55.png)

![](img/f72fb26331825f491687b091adf674b1_57.png)

**特点与限制**：
*   它直接通过SSH执行命令，**不经过远程主机的shell（如bash）解释**。
*   因此，它**无法解析**管道符 `|`、重定向 `>` `<`、变量 `$` 以及通配符 `*` 等shell特有的符号。
*   不能用于执行交互式命令（如 `vim`, `passwd`）。

![](img/f72fb26331825f491687b091adf674b1_59.png)

### 2. shell 模块

![](img/f72fb26331825f491687b091adf674b1_61.png)

`shell` 模块同样用于在远程主机执行命令，但它与 `command` 模块的关键区别在于：**`shell` 模块会通过远程主机的默认shell（通常是bash）来解释命令**。

![](img/f72fb26331825f491687b091adf674b1_63.png)

![](img/f72fb26331825f491687b091adf674b1_65.png)

**基本语法**：
```bash
ansible <组名> -m shell -a “<要执行的命令>”
```

![](img/f72fb26331825f491687b091adf674b1_67.png)

**示例**：
```bash
# 使用管道符过滤输出
ansible webservers -m shell -a “free -h | grep Mem”
# 使用重定向将命令输出保存到文件
ansible webservers -m shell -a “df -h > /opt/disk_info.txt”
# 使用通配符列出文件
ansible webservers -m shell -a “ls /opt/*.txt”
```

![](img/f72fb26331825f491687b091adf674b1_69.png)

**总结**：当需要执行的命令包含管道、重定向、通配符等shell特性时，必须使用 `shell` 模块。对于不包含这些特性的简单命令，`shell` 和 `command` 模块效果相同。

![](img/f72fb26331825f491687b091adf674b1_71.png)

### 3. copy 模块

![](img/f72fb26331825f491687b091adf674b1_73.png)

![](img/f72fb26331825f491687b091adf674b1_75.png)

![](img/f72fb26331825f491687b091adf674b1_77.png)

`copy` 模块用于将管理节点（运行Ansible的主机）上的文件复制到远程被管理主机上，实现批量文件分发。

![](img/f72fb26331825f491687b091adf674b1_79.png)

![](img/f72fb26331825f491687b091adf674b1_81.png)

**基本语法**：
```bash
ansible <组名> -m copy -a “src=<源文件路径> dest=<目标路径>”
```
*   `src`：指定要复制的源文件在管理节点上的路径。
*   `dest`：指定文件要复制到远程主机的目标路径。

![](img/f72fb26331825f491687b091adf674b1_83.png)

![](img/f72fb26331825f491687b091adf674b1_85.png)

**示例**：
```bash
# 将本地的配置文件复制到远程主机的 /etc/ 目录下
ansible webservers -m copy -a “src=/root/myapp.conf dest=/etc/”
# 将软件包复制到远程主机的 /opt/ 目录
ansible webservers -m copy -a “src=/root/software.tar.gz dest=/opt/”
```

![](img/f72fb26331825f491687b091adf674b1_87.png)

### 4. script 模块

![](img/f72fb26331825f491687b091adf674b1_89.png)

`script` 模块用于在管理节点上执行一个脚本，然后将该脚本传输到远程主机并执行。**脚本本身不需要预先拷贝到每个远程主机**。

![](img/f72fb26331825f491687b091adf674b1_91.png)

![](img/f72fb26331825f491687b091adf674b1_93.png)

![](img/f72fb26331825f491687b091adf674b1_95.png)

**基本语法**：
```bash
ansible <组名> -m script -a “<脚本在管理节点上的路径>”
```

![](img/f72fb26331825f491687b091adf674b1_97.png)

![](img/f72fb26331825f491687b091adf674b1_99.png)

![](img/f72fb26331825f491687b091adf674b1_101.png)

**示例**：
假设在管理节点上有一个初始化脚本 `/root/init.sh`。
```bash
ansible webservers -m script -a “/root/init.sh”
```
执行后，Ansible会将 `init.sh` 脚本传到 `webservers` 组中的所有主机上并运行。

![](img/f72fb26331825f491687b091adf674b1_103.png)

![](img/f72fb26331825f491687b091adf674b1_105.png)

**优势**：非常适合执行复杂的、需要多步操作的环境初始化或部署任务，只需在本地维护一份脚本即可。

![](img/f72fb26331825f491687b091adf674b1_107.png)

![](img/f72fb26331825f491687b091adf674b1_109.png)

![](img/f72fb26331825f491687b091adf674b1_111.png)

### 5. yum 模块 (了解)

![](img/f72fb26331825f491687b091adf674b1_113.png)

![](img/f72fb26331825f491687b091adf674b1_115.png)

![](img/f72fb26331825f491687b091adf674b1_117.png)

`yum` 模块是专门用于在RHEL/CentOS系统上管理软件包的模块。

**基本语法**：
```bash
# 安装软件包
ansible webservers -m yum -a “name=<软件包名> state=present”
# 或使用默认状态（安装）
ansible webservers -m yum -a “name=<软件包名>”

![](img/f72fb26331825f491687b091adf674b1_119.png)

# 卸载软件包
ansible webservers -m yum -a “name=<软件包名> state=absent”
```

**注意**：在实际工作中，使用 `shell` 模块执行 `yum install/remove` 命令通常更直接、更容易记忆。`yum` 模块的优势在于其声明式的语法和更精确的状态管理，适合在复杂的Playbook中使用。对于初学者，用 `shell` 模块完成软件包管理即可。

![](img/f72fb26331825f491687b091adf674b1_121.png)

### 6. service 模块 (了解)

![](img/f72fb26331825f491687b091adf674b1_123.png)

![](img/f72fb26331825f491687b091adf674b1_125.png)

`service` 模块用于管理远程主机的系统服务（启动、停止、重启、查看状态等）。

![](img/f72fb26331825f491687b091adf674b1_127.png)

![](img/f72fb26331825f491687b091adf674b1_129.png)

**基本语法**：
```bash
# 启动服务
ansible webservers -m service -a “name=<服务名> state=started”
# 停止服务
ansible webservers -m service -a “name=<服务名> state=stopped”
# 重启服务
ansible webservers -m service -a “name=<服务名> state=restarted”
```

**同样注意**：使用 `shell` 模块执行 `systemctl start/stop/restart <服务名>` 是更直观和常用的方式。`service` 模块在Playbook中更有用。

![](img/f72fb26331825f491687b091adf674b1_131.png)

![](img/f72fb26331825f491687b091adf674b1_133.png)

## 总结

![](img/f72fb26331825f491687b091adf674b1_135.png)

本节课中我们一起学习了Ansible模块的核心知识：
1.  使用 `ansible-doc` 命令查看模块列表和帮助信息。
2.  理解了Ansible命令返回值的颜色含义（红/绿/黄/紫），这是快速判断任务状态的关键。
3.  深入学习了六大常用模块：
    *   **`command`**：执行简单命令（默认模块）。
    *   **`shell`**：执行需要shell解释的复杂命令（支持管道、重定向等）。
    *   **`copy`**：将文件从管理节点复制到远程主机。
    *   **`script`**：在远程主机上执行管理节点上的脚本。
    *   **`yum`** 和 **`service`**：用于软件包和服务管理，作为了解内容，实践中用 `shell` 模块替代通常更便捷。

![](img/f72fb26331825f491687b091adf674b1_137.png)

记住，对于初学者，**`shell` 和 `copy` 模块是使用频率最高、最需要优先掌握的**。它们能解决绝大多数日常批量运维的需求。通过灵活运用这些模块，你已经可以开始使用Ansible高效地管理你的服务器集群了。