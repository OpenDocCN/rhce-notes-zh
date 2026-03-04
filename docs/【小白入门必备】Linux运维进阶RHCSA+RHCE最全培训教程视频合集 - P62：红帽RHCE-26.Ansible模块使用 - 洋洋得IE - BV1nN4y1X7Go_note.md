# Ansible教程：P62：Ansible模块使用 🚀

![](img/afdb1dbfe5bb069676afd96039c2dd3f_1.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_3.png)

在本节课中，我们将学习Ansible的核心功能——模块的使用。Ansible的强大功能正是通过其丰富的模块来实现的。我们将从理解模块的基本概念开始，逐步学习如何查看、调用和使用一些最常用的模块，并通过颜色来直观判断命令执行的结果。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_5.png)

---

## 理解Ansible模块 📦

![](img/afdb1dbfe5bb069676afd96039c2dd3f_7.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_9.png)

上一节我们介绍了如何定义主机清单，本节中我们来看看Ansible如何通过模块来执行具体任务。

Ansible本身功能有限，其强大的自动化能力是通过调用各种模块来实现的。你可以将Ansible模块理解为Linux系统中的命令，而模块的参数就是命令的选项。Ansible通过模块来对远程主机执行各种操作，如执行命令、管理文件、安装软件等。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_11.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_13.png)

---

## 查看模块文档与列表 🔍

在开始使用模块前，我们需要知道如何查找和了解模块。Ansible提供了 `ansible-doc` 命令来管理模块文档。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_15.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_17.png)

### 列出所有可用模块

![](img/afdb1dbfe5bb069676afd96039c2dd3f_19.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_21.png)

使用 `ansible-doc -l` 命令可以列出Ansible支持的所有模块。Ansible拥有超过3000个模块，涵盖了从系统管理到云平台集成的方方面面。

```bash
ansible-doc -l
```

**注意**：模块数量虽多，但实际工作中常用的只是其中一部分，就像我们学习Linux命令一样，只需掌握最常用的即可。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_23.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_25.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_27.png)

### 查看特定模块的帮助

![](img/afdb1dbfe5bb069676afd96039c2dd3f_29.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_31.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_33.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_35.png)

如果你知道某个模块的名字，比如 `ping` 模块，可以使用 `-s` 选项来查看该模块的详细说明和参数。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_37.png)

```bash
ansible-doc -s ping
```

该命令会输出 `ping` 模块的描述和所有可用参数，帮助你理解如何使用它。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_39.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_41.png)

---

## 执行第一个模块：Ping 🏓

![](img/afdb1dbfe5bb069676afd96039c2dd3f_43.png)

`ping` 模块用于测试与目标主机的连通性以及验证其Python环境是否可用。这是检查Ansible能否管理目标主机的最简单方法。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_45.png)

以下是执行 `ping` 模块的命令格式：
```bash
ansible <组名> -m ping
```

例如，对清单中定义的 `srvs` 组执行 ping 操作：
```bash
ansible srvs -m ping
```
*   `srvs`：在主机清单文件中定义的主机组名。
*   `-m ping`：指定调用 `ping` 模块。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_47.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_49.png)

命令执行后，你会看到颜色反馈：**绿色**表示成功，**红色**表示失败。成功意味着目标主机可达且Python环境正常。

---

## 理解Ansible命令的返回颜色 🎨

Ansible命令的输出使用颜色来直观地显示执行状态，这对于快速判断结果至关重要。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_51.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_53.png)

以下是不同颜色的含义：
*   **绿色**：命令执行成功，并且**没有**对远程主机造成任何改变（例如 `ping` 模块）。
*   **黄色**：命令执行成功，并且**对远程主机造成了改变**（例如创建或删除了文件）。
*   **红色**：命令执行失败，出现了错误。
*   **粉色（警告）**：命令执行成功，但Ansible给出了优化建议，通常可以忽略。

通过观察颜色，你可以快速了解任务执行的结果，而无需仔细阅读所有输出文本。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_55.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_57.png)

---

## 常用模块详解 🛠️

![](img/afdb1dbfe5bb069676afd96039c2dd3f_59.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_61.png)

了解了模块的基本用法和结果判断后，我们来深入学习几个最常用的模块。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_63.png)

### 1. Command 模块

![](img/afdb1dbfe5bb069676afd96039c2dd3f_65.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_67.png)

`command` 模块是Ansible的**默认模块**，用于在远程主机上执行简单的命令。它不会通过远程主机的shell（如bash）处理命令，因此无法使用管道 `|`、重定向 `>`、变量 `$HOME` 等shell特性。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_69.png)

**基本用法**：
```bash
ansible srvs -a “free -h”
```
*   `-a`：用于指定模块的参数（即要执行的命令）。由于 `command` 是默认模块，所以可以省略 `-m command`。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_71.png)

**注意**：`command` 模块无法执行交互式命令（如 `vim`）或需要shell解析的命令。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_73.png)

### 2. Shell 模块

`shell` 模块与 `command` 模块类似，也是用于执行命令。关键区别在于，`shell` 模块会通过远程主机的默认shell（通常是bash）来解释命令，因此**支持管道、重定向、变量等所有shell特性**。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_75.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_77.png)

**基本用法**：
```bash
ansible srvs -m shell -a “free -h | grep Mem”
```
*   `-m shell`：指定调用 `shell` 模块。
*   `-a “…”`：指定要通过shell执行的命令。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_79.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_81.png)

**何时选择**：当命令中涉及shell特性时，必须使用 `shell` 模块；对于普通命令，两者皆可，`command` 模块稍更高效。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_83.png)

### 3. Copy 模块

![](img/afdb1dbfe5bb069676afd96039c2dd3f_85.png)

`copy` 模块用于将管理节点（运行Ansible的主机）上的文件复制到远程主机，实现批量文件分发。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_87.png)

**基本用法**：
```bash
ansible srvs -m copy -a “src=/root/hello.txt dest=/opt/”
```
*   `src=`：指定源文件在管理节点上的路径。
*   `dest=`：指定目标文件在远程主机上的路径。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_89.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_91.png)

这个模块非常实用，例如批量分发配置文件、安装包或脚本。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_93.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_95.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_97.png)

### 4. Script 模块

![](img/afdb1dbfe5bb069676afd96039c2dd3f_99.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_101.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_103.png)

`script` 模块用于将管理节点上的脚本传输到远程主机并在其上执行。**脚本本身无需预先拷贝到所有远程主机**，Ansible会自动处理。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_105.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_107.png)

**基本用法**：
1.  在管理节点上创建一个脚本，例如 `/root/deploy.sh`。
2.  使用 `script` 模块执行它：
    ```bash
    ansible srvs -m script -a “/root/deploy.sh”
    ```

![](img/afdb1dbfe5bb069676afd96039c2dd3f_109.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_111.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_113.png)

**优势**：只需在管理节点维护一份脚本，即可在所有目标主机上运行，极大简化了批量脚本执行。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_115.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_117.png)

### 5. Yum 模块 (了解)

`yum` 模块是专门用于在RHEL/CentOS系统上管理软件包的模块。虽然使用 `shell` 模块执行 `yum install` 也能达到目的，但 `yum` 模块是Ansible推荐的方式，功能更专一。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_119.png)

**安装软件包**：
```bash
ansible srvs -m yum -a “name=vsftpd state=present”
# 或使用默认状态（安装）
ansible srvs -m yum -a “name=vsftpd”
```
*   `name=`：指定软件包名称。
*   `state=present`：确保软件包已安装（`installed`, `latest` 等效）。

**卸载软件包**：
```bash
ansible srvs -m yum -a “name=vsftpd state=absent”
```
*   `state=absent`：确保软件包已移除（`removed` 等效）。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_121.png)

### 6. Service 模块 (了解)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_123.png)

`service` 模块用于管理远程主机的系统服务（启动、停止、重启等）。同样，使用 `shell` 模块调用 `systemctl` 命令也能实现。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_125.png)

![](img/afdb1dbfe5bb069676afd96039c2dd3f_127.png)

**启动服务**：
```bash
ansible srvs -m service -a “name=vsftpd state=started”
```
**停止服务**：
```bash
ansible srvs -m service -a “name=vsftpd state=stopped”
```

![](img/afdb1dbfe5bb069676afd96039c2dd3f_129.png)

---

![](img/afdb1dbfe5bb069676afd96039c2dd3f_131.png)

## 总结 📝

![](img/afdb1dbfe5bb069676afd96039c2dd3f_133.png)

本节课中我们一起学习了Ansible模块的核心知识：

![](img/afdb1dbfe5bb069676afd96039c2dd3f_135.png)

1.  **模块是Ansible功能的基石**，可将其类比为Linux命令。
2.  使用 `ansible-doc` 可以查看模块列表(`-l`)和详细帮助(`-s`)。
3.  命令执行结果通过**颜色反馈**：绿色（成功无变更）、黄色（成功有变更）、红色（失败）。
4.  掌握了六大常用模块：
    *   **`command` / `shell`**：执行命令，后者支持shell特性。
    *   **`copy`**：批量复制文件到远程主机。
    *   **`script`**：在远程主机上执行管理节点的脚本。
    *   **`yum`** (了解)：专门管理软件包。
    *   **`service`** (了解)：专门管理系统服务。

![](img/afdb1dbfe5bb069676afd96039c2dd3f_137.png)

对于初学者，建议优先熟练掌握 `shell`、`copy` 和 `script` 模块，它们能解决绝大多数日常运维需求。`yum` 和 `service` 模块作为了解，知道Ansible有这种更专业的操作方式即可。记住，实践是学习Ansible的最佳途径，多动手尝试才能更好地掌握。