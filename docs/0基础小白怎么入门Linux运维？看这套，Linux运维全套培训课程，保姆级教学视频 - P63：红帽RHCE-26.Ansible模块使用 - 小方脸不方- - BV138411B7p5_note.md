# Linux运维全套培训课程：P63：红帽RHCE-26.Ansible模块使用 🚀

![](img/59f0ae3fb7e41c7d5896b01d1add018d_1.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_3.png)

在本节课中，我们将要学习Ansible模块的核心概念与使用方法。Ansible的强大功能正是通过其丰富的模块来实现的，理解模块是掌握自动化运维的关键。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_5.png)

## 概述

Ansible本质上是一个批量运维管理工具。它通过模块化设计，将各种运维操作封装成独立的模块，从而实现对多台服务器的统一、批量管理。本节我们将重点学习如何查看、理解并使用这些模块。

---

## 理解Ansible模块

![](img/59f0ae3fb7e41c7d5896b01d1add018d_7.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_9.png)

上一节我们介绍了如何定义主机清单，本节中我们来看看Ansible功能的核心——模块。

Ansible本身功能有限，其强大的批量管理能力是通过调用各种模块来实现的。你可以将模块理解为Ansible的“命令”，而模块的参数则相当于命令的“选项”。

### 查看所有模块

![](img/59f0ae3fb7e41c7d5896b01d1add018d_11.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_13.png)

我们可以使用 `ansible-doc` 命令来探索Ansible的模块世界。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_15.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_17.png)

```bash
ansible-doc -l
```

执行上述命令会列出Ansible所有可用的模块，数量非常庞大（例如3387个）。对于初学者而言，无需恐慌，就像学习Linux系统时我们只关注 `ls`、`cd`、`cat` 等常用命令一样，Ansible我们也只需掌握工作中最常用的一部分模块即可。

### 查看特定模块帮助

![](img/59f0ae3fb7e41c7d5896b01d1add018d_19.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_21.png)

如果我们知道某个模块的名字，想了解其具体功能和使用方法，可以使用 `-s` 选项。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_23.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_25.png)

```bash
ansible-doc -s ping
```

![](img/59f0ae3fb7e41c7d5896b01d1add018d_27.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_29.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_31.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_33.png)

此命令会输出 `ping` 模块的详细说明，包括其功能和可用的参数。

---

![](img/59f0ae3fb7e41c7d5896b01d1add018d_35.png)

## 模块的调用与执行

![](img/59f0ae3fb7e41c7d5896b01d1add018d_37.png)

### 基本调用语法

调用Ansible模块的基本命令格式如下：

![](img/59f0ae3fb7e41c7d5896b01d1add018d_39.png)

```bash
ansible [组名] -m [模块名] -a “[模块参数]”
```

![](img/59f0ae3fb7e41c7d5896b01d1add018d_41.png)

*   `[组名]`: 在主机清单文件中定义的主机组。
*   `-m`: 指定要调用的模块（model）。
*   `-a`: 指定传递给模块的参数（arguments）。

### 执行结果的颜色含义

![](img/59f0ae3fb7e41c7d5896b01d1add018d_43.png)

Ansible命令执行后，会以不同颜色显示结果，这是判断操作成功与否最直观的方式：

![](img/59f0ae3fb7e41c7d5896b01d1add018d_45.png)

*   **红色**：表示命令执行失败或出现错误。
*   **绿色**：表示命令执行成功，但**未对远程主机造成任何改变**（例如，使用 `ping` 模块测试连通性）。
*   **黄色**：表示命令执行成功，并且**对远程主机造成了改变**（例如，创建或删除了文件）。
*   **紫色/粉色**：通常是警告信息，提示你有更好的实践方式，一般可以忽略。

---

## 常用模块详解

![](img/59f0ae3fb7e41c7d5896b01d1add018d_47.png)

以下是几个最核心、最常用的Ansible模块介绍。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_49.png)

### 1. command 模块

`command` 模块是Ansible的**默认模块**，用于在远程主机上执行简单的命令。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_51.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_53.png)

**特点与限制**：
*   不支持管道符 `|`、重定向 `>`、`<`、环境变量 `$HOME` 等Bash shell的特殊字符和语法。
*   不能执行交互式命令（如 `vim`、`passwd`）。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_55.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_57.png)

**基本用法**：
```bash
# 查看远程主机内存信息（默认模块，可省略 -m command）
ansible webservers -a “free -h”

# 查看远程主机磁盘空间
ansible webservers -a “df -h”
```

![](img/59f0ae3fb7e41c7d5896b01d1add018d_59.png)

**模块参数示例（了解即可）**：
`command` 模块有一些参数，但通常直接执行命令本身更简单。
*   `creates`：如果指定路径的文件已存在，则不执行命令。
    ```bash
    ansible webservers -a “creates=/opt/hello.txt touch /opt/hello.txt”
    ```
*   `removes`：如果指定路径的文件已存在，则执行命令。
    ```bash
    ansible webservers -a “removes=/opt/hello.txt rm -f /opt/hello.txt”
    ```

![](img/59f0ae3fb7e41c7d5896b01d1add018d_61.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_63.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_65.png)

### 2. shell 模块

`shell` 模块同样用于在远程主机执行命令，但它是通过远程主机的 `/bin/bash` 解释器来执行命令的。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_67.png)

**特点**：
*   支持所有Shell特性，如管道符、重定向、环境变量等。
*   在需要复杂Shell操作时，应优先使用 `shell` 模块而非 `command` 模块。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_69.png)

**基本用法**：
```bash
# 使用管道符过滤输出
ansible webservers -m shell -a “free -h | grep Mem”

# 将命令输出重定向到文件
ansible webservers -m shell -a “free -h > /opt/free.txt”

![](img/59f0ae3fb7e41c7d5896b01d1add018d_71.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_73.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_75.png)

# 列出带通配符的文件
ansible webservers -m shell -a “ls /opt/*.txt”

![](img/59f0ae3fb7e41c7d5896b01d1add018d_77.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_79.png)

# 安装软件包
ansible webservers -m shell -a “yum install -y vsftpd”

![](img/59f0ae3fb7e41c7d5896b01d1add018d_81.png)

# 管理服务
ansible webservers -m shell -a “systemctl start vsftpd”
```

![](img/59f0ae3fb7e41c7d5896b01d1add018d_83.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_85.png)

### 3. copy 模块

![](img/59f0ae3fb7e41c7d5896b01d1add018d_87.png)

`copy` 模块用于将Ansible管理机上的文件复制到远程主机。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_89.png)

**基本用法**：
```bash
ansible webservers -m copy -a “src=/root/hello.txt dest=/opt/”
```
*   `src`：源文件路径（在Ansible控制机上）。
*   `dest`：目标路径（在远程主机上）。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_91.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_93.png)

这个模块在批量分发配置文件、脚本或安装包时非常有用。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_95.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_97.png)

### 4. script 模块

![](img/59f0ae3fb7e41c7d5896b01d1add018d_99.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_101.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_103.png)

`script` 模块用于将Ansible控制机上的脚本传输到远程主机并执行。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_105.png)

**优势**：
*   脚本只需在Ansible控制机上维护一份，无需手动拷贝到每个远程主机。
*   脚本在远程主机执行，拥有该主机的环境上下文。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_107.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_109.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_111.png)

**基本用法**：
```bash
# 假设在控制机 /root/ 下有一个 setup.sh 脚本
ansible webservers -m script -a “/root/setup.sh”
```

![](img/59f0ae3fb7e41c7d5896b01d1add018d_113.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_115.png)

### 5. yum 模块 (了解)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_117.png)

`yum` 模块是专门用于管理RHEL/CentOS系统软件包的工具。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_119.png)

**基本用法**：
```bash
# 安装软件包（state=present 可省略，默认即为安装）
ansible webservers -m yum -a “name=vsftpd state=present”

# 卸载软件包
ansible webservers -m yum -a “name=vsftpd state=absent”
```
虽然功能专一，但其语法对于初学者可能不如直接使用 `shell` 模块执行 `yum install` 直观，可作为了解内容。

### 6. service 模块 (了解)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_121.png)

`service` 模块用于管理远程主机的系统服务。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_123.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_125.png)

**基本用法**：
```bash
# 启动服务
ansible webservers -m service -a “name=vsftpd state=started”

![](img/59f0ae3fb7e41c7d5896b01d1add018d_127.png)

# 停止服务
ansible webservers -m service -a “name=vsftpd state=stopped”
```
同样，对于已熟悉 `systemctl` 命令的用户，使用 `shell` 模块可能更直接。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_129.png)

---

![](img/59f0ae3fb7e41c7d5896b01d1add018d_131.png)

## 总结

![](img/59f0ae3fb7e41c7d5896b01d1add018d_133.png)

![](img/59f0ae3fb7e41c7d5896b01d1add018d_135.png)

本节课中我们一起学习了Ansible模块的核心知识：

1.  **模块是Ansible功能的基石**，可将其理解为Ansible的“命令”。
2.  使用 `ansible-doc -l` 查看所有模块，使用 `ansible-doc -s [模块名]` 查看特定模块帮助。
3.  掌握模块调用的基本语法：`ansible [组名] -m [模块名] -a “[参数]”`。
4.  **通过颜色快速判断执行结果**：红（失败）、绿（成功无变更）、黄（成功有变更）。
5.  重点掌握了六大常用模块：
    *   **`command`**：执行简单命令（默认模块）。
    *   **`shell`**：执行复杂Shell命令（支持管道、重定向）。
    *   **`copy`**：复制文件到远程主机。
    *   **`script`**：在远程主机执行控制机上的脚本。
    *   **`yum`**：管理软件包（了解）。
    *   **`service`**：管理系统服务（了解）。

![](img/59f0ae3fb7e41c7d5896b01d1add018d_137.png)

对于初学者，**`shell` 和 `copy` 模块足以应对绝大多数日常批量运维场景**。请多加练习，体会Ansible批量操作的便捷性。