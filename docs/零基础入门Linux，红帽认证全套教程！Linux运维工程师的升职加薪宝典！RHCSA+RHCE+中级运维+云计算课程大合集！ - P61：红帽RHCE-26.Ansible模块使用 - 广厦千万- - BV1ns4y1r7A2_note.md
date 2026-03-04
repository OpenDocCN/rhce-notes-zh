# 红帽RHCE课程：P61：Ansible模块使用

![](img/d45aa5b5b7d70d553fa65314de4acb9a_1.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_3.png)

在本节课中，我们将学习Ansible模块的基础知识。Ansible的强大功能正是通过其丰富的模块来实现的。我们将了解如何查看模块、理解模块的基本用法，并通过几个核心模块的实践来掌握批量管理服务器的基本操作。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_5.png)

## 理解Ansible：批量运维工具

上一节我们介绍了Ansible的主机清单定义。本节中，我们来看看Ansible的核心——模块。

首先，请将Ansible理解为一个**批量运维管理工具**。它的核心作用是让你能够实现对多台服务器的批量操作和管理，无需将其概念复杂化为“自动化”。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_7.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_9.png)

## 查看与学习模块

Ansible采用模块化设计，其本身功能有限，所有具体操作都通过调用模块来完成。因此，学习模块的使用至关重要。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_11.png)

我们可以使用 `ansible-doc` 命令来查看模块的文档。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_13.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_15.png)

以下是查看模块的两种常用方法：

![](img/d45aa5b5b7d70d553fa65314de4acb9a_17.png)

*   **列出所有可用模块**：使用 `-l` 选项可以列出Ansible当前支持的所有模块。
    ```bash
    ansible-doc -l
    ```
    执行后会看到大量模块（例如超过3000个）。无需担心，就像Linux系统命令一样，我们只需要掌握工作中最常用的部分即可。

*   **查看特定模块的详细帮助**：使用 `-s` 选项可以查看指定模块的简要说明和参数。
    ```bash
    ansible-doc -s ping
    ```
    这将显示 `ping` 模块的功能描述和可用参数。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_19.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_21.png)

**模块**可以理解为Ansible的“命令”，而**模块参数**则相当于命令的“选项”，用于调整模块的具体行为。有些模块可以不加参数直接使用。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_23.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_25.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_27.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_29.png)

## 执行你的第一条Ansible命令

![](img/d45aa5b5b7d70d553fa65314de4acb9a_31.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_33.png)

在开始使用模块前，请确保已按照之前课程所讲，在 `/etc/ansible/hosts` 清单文件中定义好了主机组（例如 `webservers`）。

现在，让我们使用 `ping` 模块来测试与清单中主机的连通性。`ping` 模块用于测试目标主机是否具有可用的Python环境（Ansible运行所必需）。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_35.png)

```bash
ansible webservers -m ping
```
*   `webservers`: 你在清单文件中定义的主机组名。
*   `-m ping`: 指定调用 `ping` 模块。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_37.png)

命令执行后，观察返回结果的颜色：
*   **绿色**：表示命令执行成功，且未对远程主机造成任何改变（例如 `ping` 测试）。
*   **红色**：表示命令执行失败或出现错误。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_39.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_41.png)

## 理解Ansible的返回结果颜色

Ansible通过返回信息的颜色直观地反馈操作结果，这对于快速判断至关重要。

让我们通过另一个模块来理解更多颜色含义。使用 `shell` 模块在远程主机上执行命令：

![](img/d45aa5b5b7d70d553fa65314de4acb9a_43.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_45.png)

```bash
ansible webservers -m shell -a "rm -rf /opt/*"
```
*   `-m shell`: 调用 `shell` 模块。
*   `-a "..."`: 指定模块要执行的具体操作（参数）。

观察这次返回的颜色：
*   **黄色**：表示命令执行成功，并且**对远程主机造成了改变**（例如删除了文件）。
*   **紫色/粉色（警告）**：Ansible给出的建议，例如提示有更专门的模块来完成此操作。通常可以忽略，但值得了解。

**总结颜色含义**：
*   **绿色**：成功，未改变远程主机状态。
*   **黄色**：成功，且改变了远程主机状态。
*   **红色**：失败。
*   **紫色**：警告或建议。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_47.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_49.png)

## 核心模块详解

上一节我们了解了如何调用模块和解读结果。本节中，我们来深入学习几个最常用、最核心的Ansible模块。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_51.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_53.png)

### 1. command 模块

`command` 模块是Ansible的**默认模块**，用于在远程主机上执行简单的命令。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_55.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_57.png)

**基本用法**：
```bash
ansible webservers -a "uptime"
```
由于 `command` 是默认模块，可以省略 `-m command`，直接使用 `-a` 指定要运行的命令。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_59.png)

**特点与限制**：
`command` 模块执行命令时，**不会经过远程主机的bash shell解释**。这意味着它无法解析管道 `|`、重定向 `>`、`<`、变量 `$HOME` 等bash特有的符号和功能。
```bash
# 以下命令可能无法按预期工作
ansible webservers -a "ls /opt/*"
ansible webservers -a "free -h | grep Mem"
```

![](img/d45aa5b5b7d70d553fa65314de4acb9a_61.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_63.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_65.png)

### 2. shell 模块

`shell` 模块同样用于在远程主机执行命令，但它是**通过远程主机的bash shell来解释命令**的。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_67.png)

**基本用法**：
```bash
ansible webservers -m shell -a “ls /opt/*”
ansible webservers -m shell -a “free -h | grep Mem”
ansible webservers -m shell -a “yum install -y vsftpd”
```

![](img/d45aa5b5b7d70d553fa65314de4acb9a_69.png)

**与command模块的对比**：
`shell` 模块支持所有bash特性（管道、重定向、通配符等），功能更强大。在需要这些特性时，应使用 `shell` 模块而非 `command` 模块。两者在执行普通命令时效果相同。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_71.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_73.png)

**注意**：`shell` 模块**无法执行交互式命令**，如 `vim`、`passwd`（不加`--stdin`时）等。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_75.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_77.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_79.png)

### 3. copy 模块

![](img/d45aa5b5b7d70d553fa65314de4acb9a_81.png)

`copy` 模块用于将**管理节点（运行Ansible的主机）上的文件复制到远程主机**，实现批量分发。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_83.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_85.png)

**基本用法**：
```bash
ansible webservers -m copy -a “src=/root/package.tar.gz dest=/opt/”
```
*   `src`: 源文件路径（在管理节点上）。
*   `dest`: 目标路径（在远程主机上）。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_87.png)

这个模块在批量部署软件包、配置文件时极其有用。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_89.png)

### 4. script 模块

![](img/d45aa5b5b7d70d553fa65314de4acb9a_91.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_93.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_95.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_97.png)

`script` 模块用于在**远程主机上执行管理节点本地的脚本**。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_99.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_101.png)

**基本用法**：
1.  在管理节点上编写一个脚本，例如 `/root/setup.sh`。
2.  使用 `script` 模块运行它。
    ```bash
    ansible webservers -m script -a “/root/setup.sh”
    ```

![](img/d45aa5b5b7d70d553fa65314de4acb9a_103.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_104.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_106.png)

**优势**：脚本只需在管理节点上存放一份，无需手动拷贝到每个远程主机。Ansible会自动将脚本传输到远程主机执行，然后清理临时文件。这对于执行复杂的初始化任务非常高效。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_108.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_110.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_112.png)

### 5. yum 模块 (了解)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_114.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_116.png)

`yum` 模块是专门用于管理RHEL/CentOS系统软件包的模块。

**基本用法**：
```bash
# 安装软件包
ansible webservers -m yum -a “name=vsftpd state=present”
# 或 state=installed
# 卸载软件包
ansible webservers -m yum -a “name=vsftpd state=absent”
# 或 state=removed
```
*   `name`: 指定软件包名称。
*   `state`: 指定期望状态（`present`/`installed` 安装，`absent`/`removed` 卸载）。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_118.png)

**说明**：虽然存在专门的模块，但使用 `shell` 模块执行 `yum install/remove` 命令通常更直接、更容易记忆。`yum` 模块可作为了解内容。

### 6. service 模块 (了解)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_120.png)

`service` 模块用于管理远程主机的系统服务。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_122.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_124.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_126.png)

**基本用法**：
```bash
# 启动服务
ansible webservers -m service -a “name=vsftpd state=started”
# 停止服务
ansible webservers -m service -a “name=vsftpd state=stopped”
# 重启服务
ansible webservers -m service -a “name=vsftpd state=restarted”
```

![](img/d45aa5b5b7d70d553fa65314de4acb9a_128.png)

**说明**：与 `yum` 模块类似，使用 `shell` 模块执行 `systemctl start/stop/restart` 命令可能对初学者更为直观。此模块也作为了解内容。

## 总结

![](img/d45aa5b5b7d70d553fa65314de4acb9a_130.png)

![](img/d45aa5b5b7d70d553fa65314de4acb9a_132.png)

本节课中，我们一起学习了Ansible模块的核心知识：

![](img/d45aa5b5b7d70d553fa65314de4acb9a_134.png)

1.  **模块是Ansible功能的基石**，通过 `ansible-doc` 可以查看和学习它们。
2.  **理解执行结果的颜色**（绿/黄/红/紫）是快速诊断操作成败的关键。
3.  掌握了五大核心模块的用法：
    *   `command` / `shell`: 用于执行命令，后者支持完整的Shell特性。
    *   `copy`: 用于向远程主机批量分发文件。
    *   `script`: 用于在远程主机批量执行本地脚本。
    *   `yum` / `service`: 用于软件包和服务管理（了解即可）。

![](img/d45aa5b5b7d70d553fa65314de4acb9a_136.png)

对于初学者，重点掌握 `shell`、`copy` 和 `script` 模块，已能覆盖大量的日常批量运维场景。记住，Ansible是一个工具，选择最直接、你最能理解的方式去使用它即可。