# Ansible 自动化运维：P25：创建运行临时命令的 Shell 脚本 🚀

![](img/7cad8cd7b59ee91d26237c0484ff2736_1.png)

在本节课中，我们将学习如何创建 Shell 脚本，以便批量运行 Ansible 临时命令。虽然临时命令通常用于一次性任务，但将其保存在脚本中可以方便地重复使用，或者通过 Bash 脚本发挥 Ansible 的强大功能。只要您知道要使用的模块和参数，设置起来就非常简单。

![](img/7cad8cd7b59ee91d26237c0484ff2736_3.png)

上一节我们介绍了 Ansible 临时命令的基本用法，本节中我们来看看如何将它们组织成可执行的脚本。

![](img/7cad8cd7b59ee91d26237c0484ff2736_5.png)

## 脚本结构与示例

以下是一个示例 Shell 脚本，其中包含了多个 Ansible 临时命令。该脚本旨在完成一系列任务：创建用户、创建目录、复制文件以及安装并启动服务。

```bash
#!/bin/bash
# 脚本：ad_hoc.sh
# 描述：一个包含多个Ansible临时命令的示例脚本

# 任务1：在 msperson3c 主机上创建用户 mat
ansible msperson3c -i /home/cloud_user/ansible/inventory --become -m user -a "name=mat"

# 任务2：在用户 mat 的主目录下创建 demo 目录
ansible msperson3c -i /home/cloud_user/ansible/inventory --become -m file -a "path=/home/mat/demo state=directory owner=mat group=mat mode=0755"

# 任务3：将测试文件复制到 mat 的主目录
ansible msperson3c -i /home/cloud_user/ansible/inventory --become -m copy -a "src=/home/cloud_user/ansible/testfile dest=/home/mat/testfile mode=0644 owner=mat group=mat"

# 任务4：在 web_servers 主机组上安装最新版 httpd 软件包
ansible web_servers -i /home/cloud_user/ansible/inventory --become -m yum -a "name=httpd state=latest"

# 任务5：在 web_servers 主机组上启动并启用 httpd 服务
ansible web_servers -i /home/cloud_user/ansible/inventory --become -m service -a "name=httpd state=started enabled=yes"
```

## 脚本详解与命令解析

以下是脚本中每个命令的详细解析：

![](img/7cad8cd7b59ee91d26237c0484ff2736_7.png)

**1. 创建用户**
此命令使用 `user` 模块在指定主机上创建一个名为 `mat` 的用户。`--become` 选项表示以 root 权限执行。
```bash
ansible msperson3c -i /path/to/inventory --become -m user -a "name=mat"
```

![](img/7cad8cd7b59ee91d26237c0484ff2736_9.png)

![](img/7cad8cd7b59ee91d26237c0484ff2736_10.png)

![](img/7cad8cd7b59ee91d26237c0484ff2736_12.png)

![](img/7cad8cd7b59ee91d26237c0484ff2736_13.png)

**2. 创建目录**
此命令使用 `file` 模块创建目录。参数 `state=directory` 指定操作类型，`owner`、`group` 和 `mode` 设置权限。
```bash
ansible msperson3c -i /path/to/inventory --become -m file -a "path=/home/mat/demo state=directory owner=mat group=mat mode=0755"
```

**3. 复制文件**
此命令使用 `copy` 模块将文件从控制节点复制到受管节点。`src` 指定源路径，`dest` 指定目标路径。
```bash
ansible msperson3c -i /path/to/inventory --become -m copy -a "src=/home/cloud_user/ansible/testfile dest=/home/mat/testfile mode=0644 owner=mat group=mat"
```

![](img/7cad8cd7b59ee91d26237c0484ff2736_15.png)

![](img/7cad8cd7b59ee91d26237c0484ff2736_17.png)

![](img/7cad8cd7b59ee91d26237c0484ff2736_19.png)

**4. 安装软件包**
此命令使用 `yum` 模块在 `web_servers` 主机组上安装软件包。`state=latest` 确保安装最新版本。
```bash
ansible web_servers -i /path/to/inventory --become -m yum -a "name=httpd state=latest"
```

![](img/7cad8cd7b59ee91d26237c0484ff2736_20.png)

![](img/7cad8cd7b59ee91d26237c0484ff2736_22.png)

**5. 管理服务**
此命令使用 `service` 模块启动服务，并设置其开机自启。`state=started` 启动服务，`enabled=yes` 启用自启。
```bash
ansible web_servers -i /path/to/inventory --become -m service -a "name=httpd state=started enabled=yes"
```

![](img/7cad8cd7b59ee91d26237c0484ff2736_23.png)

![](img/7cad8cd7b59ee91d26237c0484ff2736_25.png)

![](img/7cad8cd7b59ee91d26237c0484ff2736_27.png)

![](img/7cad8cd7b59ee91d26237c0484ff2736_28.png)

## 执行脚本与验证输出

![](img/7cad8cd7b59ee91d26237c0484ff2736_30.png)

![](img/7cad8cd7b59ee91d26237c0484ff2736_31.png)

在运行脚本之前，需要为其添加可执行权限。使用以下命令：
```bash
chmod u+x ad_hoc.sh
```
然后执行脚本：
```bash
./ad_hoc.sh
```
脚本执行后，输出结果会显示每个任务的状态。例如，成功创建用户后，会显示 `"changed": true` 和用户详细信息；成功创建目录后，会显示路径和权限信息。

![](img/7cad8cd7b59ee91d26237c0484ff2736_33.png)

![](img/7cad8cd7b59ee91d26237c0484ff2736_34.png)

## 应用场景与优势

将 Ansible 临时命令写入 Shell 脚本的主要优势在于可重复性和自动化。您可以将复杂的操作序列保存下来，方便日后再次执行或进行修改。这在系统初始化、批量配置更改或定期维护任务中非常有用。

![](img/7cad8cd7b59ee91d26237c0484ff2736_36.png)

![](img/7cad8cd7b59ee91d26237c0484ff2736_38.png)

本节课中我们一起学习了如何将多个 Ansible 临时命令整合到一个 Shell 脚本中。我们分析了脚本的结构，详解了每个命令的模块和参数，并演示了如何执行和验证脚本。掌握这种方法，能让您更高效地利用 Ansible 进行自动化运维管理。在接下来的课程中，我们将介绍更多常用的 Ansible 模块。