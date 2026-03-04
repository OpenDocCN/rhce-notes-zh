# 红帽企业Linux RHEL 9精通课程：P60：06-06-002 Ansible 技巧

![](img/7e85f38192ba36bcc94b89b19780353c_1.png)

![](img/7e85f38192ba36bcc94b89b19780353c_3.png)

![](img/7e85f38192ba36bcc94b89b19780353c_5.png)

![](img/7e85f38192ba36bcc94b89b19780353c_7.png)

![](img/7e85f38192ba36bcc94b89b19780353c_9.png)

![](img/7e85f38192ba36bcc94b89b19780353c_10.png)

![](img/7e85f38192ba36bcc94b89b19780353c_12.png)

在本节课程中，我们将学习如何使用 Ansible 的即席命令，并探索一些有用的命令行选项。这些技巧对于在 RHCE 考试中验证配置非常有用。

![](img/7e85f38192ba36bcc94b89b19780353c_14.png)

![](img/7e85f38192ba36bcc94b89b19780353c_15.png)

![](img/7e85f38192ba36bcc94b89b19780353c_17.png)

## 概述

![](img/7e85f38192ba36bcc94b89b19780353c_19.png)

我们将通过一个迭代的过程，学习在不使用 `ansible.cfg` 配置文件的情况下，如何通过命令行标志来连接和管理节点。我们还将编写一个简单的脚本，演示如何将即席命令整合到自动化流程中。

![](img/7e85f38192ba36bcc94b89b19780353c_21.png)

![](img/7e85f38192ba36bcc94b89b19780353c_23.png)

![](img/7e85f38192ba36bcc94b89b19780353c_25.png)

## 创建项目目录

首先，我们创建一个新的项目目录 `project2` 并进入该目录。

![](img/7e85f38192ba36bcc94b89b19780353c_27.png)

![](img/7e85f38192ba36bcc94b89b19780353c_29.png)

```bash
mkdir project2
cd project2
```

![](img/7e85f38192ba36bcc94b89b19780353c_31.png)

![](img/7e85f38192ba36bcc94b89b19780353c_33.png)

![](img/7e85f38192ba36bcc94b89b19780353c_35.png)

## 编写清单文件

接下来，我们创建一个简单的清单文件 `inventory.ini`。在这个文件中，我们可以使用 `ansible_host` 变量来为主机设置别名，指向其 IP 地址或完全限定域名。

![](img/7e85f38192ba36bcc94b89b19780353c_37.png)

![](img/7e85f38192ba36bcc94b89b19780353c_39.png)

![](img/7e85f38192ba36bcc94b89b19780353c_41.png)

![](img/7e85f38192ba36bcc94b89b19780353c_42.png)

![](img/7e85f38192ba36bcc94b89b19780353c_43.png)

![](img/7e85f38192ba36bcc94b89b19780353c_45.png)

![](img/7e85f38192ba36bcc94b89b19780353c_46.png)

```ini
[deploy]
appserver4.labnet
appserver5.labnet ansible_host=10.0.0.13
```

## 使用命令行标志运行即席命令

在不使用 `ansible.cfg` 文件的情况下，我们需要通过命令行标志来指定连接参数。

### 1. 指定清单文件

![](img/7e85f38192ba36bcc94b89b19780353c_48.png)

使用 `-i` 标志来指定清单文件。

![](img/7e85f38192ba36bcc94b89b19780353c_50.png)

```bash
ansible all -i inventory.ini -m ping
```

### 2. 配置 SSH 连接选项

![](img/7e85f38192ba36bcc94b89b19780353c_52.png)

![](img/7e85f38192ba36bcc94b89b19780353c_54.png)

如果遇到 SSH 连接问题，例如主机密钥检查，我们可以通过 `--ssh-extra-args` 标志来传递额外的 SSH 参数。

![](img/7e85f38192ba36bcc94b89b19780353c_56.png)

![](img/7e85f38192ba36bcc94b89b19780353c_57.png)

![](img/7e85f38192ba36bcc94b89b19780353c_59.png)

![](img/7e85f38192ba36bcc94b89b19780353c_61.png)

```bash
ansible all -i inventory.ini -m ping --ssh-extra-args="-o StrictHostKeyChecking=no"
```

### 3. 启用密码提示

![](img/7e85f38192ba36bcc94b89b19780353c_63.png)

![](img/7e85f38192ba36bcc94b89b19780353c_65.png)

如果尚未设置基于密钥的身份验证，需要使用 `--ask-pass` 标志来启用 SSH 密码提示。

```bash
ansible all -i inventory.ini -m ping --ssh-extra-args="-o StrictHostKeyChecking=no" --ask-pass
```

### 4. 指定远程用户

使用 `-u` 标志来指定远程登录的用户名。

![](img/7e85f38192ba36bcc94b89b19780353c_67.png)

![](img/7e85f38192ba36bcc94b89b19780353c_69.png)

![](img/7e85f38192ba36bcc94b89b19780353c_71.png)

![](img/7e85f38192ba36bcc94b89b19780353c_73.png)

![](img/7e85f38192ba36bcc94b89b19780353c_74.png)

![](img/7e85f38192ba36bcc94b89b19780353c_76.png)

![](img/7e85f38192ba36bcc94b89b19780353c_78.png)

![](img/7e85f38192ba36bcc94b89b19780353c_80.png)

![](img/7e85f38192ba36bcc94b89b19780353c_82.png)

```bash
ansible all -i inventory.ini -m ping -u admin --ssh-extra-args="-o StrictHostKeyChecking=no" --ask-pass
```

![](img/7e85f38192ba36bcc94b89b19780353c_84.png)

![](img/7e85f38192ba36bcc94b89b19780353c_86.png)

![](img/7e85f38192ba36bcc94b89b19780353c_88.png)

![](img/7e85f38192ba36bcc94b89b19780353c_89.png)

### 5. 配置权限提升

如果需要执行需要特权（如 `sudo`）的操作，可以使用 `--become`（或 `-b`）和 `--ask-become-pass`（或 `-K`）标志。

```bash
ansible all -i inventory.ini -m shell -a 'echo "Hello" > /root/greetings' -u admin --ssh-extra-args="-o StrictHostKeyChecking=no" --ask-pass -b -K
```

![](img/7e85f38192ba36bcc94b89b19780353c_91.png)

![](img/7e85f38192ba36bcc94b89b19780353c_93.png)

![](img/7e85f38192ba36bcc94b89b19780353c_95.png)

![](img/7e85f38192ba36bcc94b89b19780353c_97.png)

## 验证配置的示例

![](img/7e85f38192ba36bcc94b89b19780353c_99.png)

![](img/7e85f38192ba36bcc94b89b19780353c_100.png)

![](img/7e85f38192ba36bcc94b89b19780353c_102.png)

![](img/7e85f38192ba36bcc94b89b19780353c_103.png)

![](img/7e85f38192ba36bcc94b89b19780353c_105.png)

![](img/7e85f38192ba36bcc94b89b19780353c_107.png)

![](img/7e85f38192ba36bcc94b89b19780353c_109.png)

![](img/7e85f38192ba36bcc94b89b19780353c_111.png)

![](img/7e85f38192ba36bcc94b89b19780353c_112.png)

![](img/7e85f38192ba36bcc94b89b19780353c_114.png)

![](img/7e85f38192ba36bcc94b89b19780353c_116.png)

即席命令非常适合快速验证配置。例如，检查所有主机上的 `sshd` 服务是否启用。

![](img/7e85f38192ba36bcc94b89b19780353c_118.png)

![](img/7e85f38192ba36bcc94b89b19780353c_120.png)

![](img/7e85f38192ba36bcc94b89b19780353c_122.png)

![](img/7e85f38192ba36bcc94b89b19780353c_124.png)

![](img/7e85f38192ba36bcc94b89b19780353c_126.png)

![](img/7e85f38192ba36bcc94b89b19780353c_127.png)

![](img/7e85f38192ba36bcc94b89b19780353c_129.png)

![](img/7e85f38192ba36bcc94b89b19780353c_131.png)

![](img/7e85f38192ba36bcc94b89b19780353c_133.png)

```bash
ansible all -i inventory.ini -m command -a 'systemctl is-enabled sshd' -u admin --ssh-extra-args="-o StrictHostKeyChecking=no" --ask-pass -b -K
```

![](img/7e85f38192ba36bcc94b89b19780353c_135.png)

![](img/7e85f38192ba36bcc94b89b19780353c_136.png)

![](img/7e85f38192ba36bcc94b89b19780353c_138.png)

![](img/7e85f38192ba36bcc94b89b19780353c_140.png)

![](img/7e85f38192ba36bcc94b89b19780353c_142.png)

![](img/7e85f38192ba36bcc94b89b19780353c_144.png)

## 编写使用即席命令的脚本

![](img/7e85f38192ba36bcc94b89b19780353c_146.png)

![](img/7e85f38192ba36bcc94b89b19780353c_148.png)

我们可以将一系列即席命令组合成一个脚本，以实现简单的自动化任务。以下是一个示例脚本 `ad_hoc_script.sh`，它使用 `copy` 模块向 `/etc/motd` 文件写入内容，然后使用 `command` 模块验证写入的内容。

```bash
#!/bin/bash
# 这是一个使用 Ansible 即席命令的脚本示例

![](img/7e85f38192ba36bcc94b89b19780353c_150.png)

![](img/7e85f38192ba36bcc94b89b19780353c_152.png)

# 设置 forks 为 2，控制并行执行的任务数
FORKS=2
HOST_PATTERN="deploy"
MESSAGE="$*"

![](img/7e85f38192ba36bcc94b89b19780353c_154.png)

![](img/7e85f38192ba36bcc94b89b19780353c_156.png)

# 使用 copy 模块写入 /etc/motd
ansible $HOST_PATTERN -i inventory.ini -m copy -a "content=\"$MESSAGE\" dest=/etc/motd" -u admin --ssh-extra-args="-o StrictHostKeyChecking=no" --ask-pass -b -K --forks=$FORKS

# 使用 command 模块验证 /etc/motd 的内容
ansible $HOST_PATTERN -i inventory.ini -m command -a "cat /etc/motd" -u admin --ssh-extra-args="-o StrictHostKeyChecking=no" --ask-pass -b -K --forks=$FORKS
```

运行脚本：

![](img/7e85f38192ba36bcc94b89b19780353c_158.png)

![](img/7e85f38192ba36bcc94b89b19780353c_160.png)

```bash
bash ad_hoc_script.sh "一些参数"
```

## 在本地主机上执行命令

![](img/7e85f38192ba36bcc94b89b19780353c_162.png)

![](img/7e85f38192ba36bcc94b89b19780353c_164.png)

![](img/7e85f38192ba36bcc94b89b19780353c_166.png)

![](img/7e85f38192ba36bcc94b89b19780353c_168.png)

我们可以使用隐式的 `localhost` 组在控制节点上执行 Ansible 命令。使用 `-c local` 指定本地连接方式。

![](img/7e85f38192ba36bcc94b89b19780353c_170.png)

```bash
ansible localhost -u $USER -c local -m ping
ansible localhost -u $USER -c local -m setup
```

## 总结

![](img/7e85f38192ba36bcc94b89b19780353c_172.png)

在本节课程中，我们一起学习了 Ansible 即席命令的各种命令行选项，包括如何指定清单、配置 SSH、启用密码提示和权限提升。我们还探讨了如何编写脚本将即席命令整合到自动化流程中，以及如何在本地主机上执行命令。掌握这些技巧有助于在考试和实际工作中快速验证和测试配置。