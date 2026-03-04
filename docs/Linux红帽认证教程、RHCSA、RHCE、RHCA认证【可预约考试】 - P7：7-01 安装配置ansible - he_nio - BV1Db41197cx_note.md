# Linux红帽认证教程：RHCE：安装配置Ansible 🚀

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_1.png)

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_3.png)

在本节课中，我们将学习如何在RHCE考试环境中完成Ansible的安装与基础配置。这是RHCE考试的第一道大题，包含多个关键步骤和需要注意的“暗坑”。我们将一步步解析题目要求，确保配置正确无误。

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_5.png)

## 概述

本节内容将指导你完成以下任务：
1.  在控制节点上安装Ansible。
2.  创建符合要求的主机清单文件。
3.  配置Ansible的主配置文件，并修改关键参数。
4.  理解并处理题目中隐含的重要要求（“暗坑”）。

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_7.png)

---

## 7-01：安装Ansible

首先，我们需要登录到控制节点并安装Ansible软件包。

上一节我们介绍了课程目标，本节中我们来看看如何开始安装。

1.  使用SSH连接到控制节点。
    ```bash
    ssh greg@control
    ```
    密码为 `flectrag`。

2.  安装Ansible。由于普通用户权限不足，需要使用 `sudo` 提权。
    ```bash
    sudo yum install ansible -y
    ```

---

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_9.png)

## 7-02：创建指定工作目录

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_11.png)

根据题目要求，所有Ansible相关的文件都必须存放在指定目录中，并且目录的属主必须是 `greg` 用户。

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_13.png)

以下是创建目录的步骤：

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_15.png)

*   使用 `mkdir -p` 命令创建目录 `/home/greg/ansible`。
*   创建后，可以使用 `ll -d` 命令验证目录的属主和属组是否正确。

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_17.png)

```bash
mkdir -p /home/greg/ansible
ll -d /home/greg/ansible
```

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_19.png)

---

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_21.png)

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_23.png)

## 7-03：创建主机清单文件

主机清单文件定义了Ansible将要管理的所有主机及其分组信息。

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_25.png)

上一节我们创建了工作目录，本节中我们来看看如何根据题目要求构建主机清单。

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_27.png)

题目要求创建文件 `/home/greg/ansible/inventory`，并按照以下分组添加主机：
*   `dev` 组包含主机 `node1`
*   `test` 组包含主机 `node2`
*   `prod` 组包含主机 `node3` 和 `node4`
*   `balancers` 组包含主机 `node5`
*   `webservers` 组是一个父组，其子组为 `prod`

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_29.png)

以下是具体操作步骤：

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_31.png)

1.  创建空的清单文件。
    ```bash
    touch /home/greg/ansible/inventory
    ```

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_33.png)

2.  编辑该文件，写入以下内容：
    ```ini
    [dev]
    node1

    [test]
    node2

    [prod]
    node3
    node4

    [balancers]
    node5

    [webservers:children]
    prod
    ```
    注意：`[webservers:children]` 表示 `webservers` 是一个父组，`prod` 是其成员。

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_35.png)

3.  使用 `cat` 命令检查文件内容是否正确。
    ```bash
    cat /home/greg/ansible/inventory
    ```

---

## 7-04：配置Ansible主配置文件

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_37.png)

Ansible的主配置文件控制着其核心行为。我们需要从默认位置复制一份到工作目录，并进行修改。

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_39.png)

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_41.png)

上一节我们配置了主机清单，本节中我们来看看如何设置Ansible的主配置文件。

以下是配置步骤：

1.  将系统默认的Ansible配置文件复制到工作目录。
    ```bash
    cp /etc/ansible/ansible.cfg /home/greg/ansible/
    ```

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_43.png)

2.  根据题目要求，还需要创建角色存放目录。
    ```bash
    mkdir /home/greg/ansible/roles
    ```

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_45.png)

3.  现在，编辑配置文件 `/home/greg/ansible/ansible.cfg`，修改以下两个参数：
    *   `inventory`：指向我们创建的主机清单文件路径。
    *   `roles_path`：指向我们创建的角色目录路径。
    在配置文件中找到对应行并进行修改：
    ```ini
    inventory      = /home/greg/ansible/inventory
    roles_path    = /home/greg/ansible/roles
    ```

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_47.png)

---

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_49.png)

## 7-05：处理关键“暗坑”

除了题目明确的要求，考试环境说明中还隐藏着两个必须完成的关键配置，否则后续操作可能失败。

上一节我们完成了基础配置，本节中我们来看看必须注意的几个关键点。

需要在 `/home/greg/ansible/ansible.cfg` 文件中额外检查和修改以下部分：

1.  **设置远程执行用户**：题目要求所有Ansible命令必须由 `greg` 用户在被管理节点上执行。找到 `[defaults]` 部分，确保或添加以下行：
    ```ini
    remote_user = greg
    ```

2.  **配置特权升级**：为了允许 `greg` 用户执行需要root权限的任务，需要正确配置 `become` 相关参数。找到 `[privilege_escalation]` 部分，确保配置如下（通常只需取消注释）：
    ```ini
    become=True
    become_method=sudo
    become_user=root
    become_ask_pass=False
    ```

3.  **（可选）关闭主机密钥检查**：在实验环境中，为避免SSH连接提示，可以关闭主机密钥检查。找到并修改：
    ```ini
    host_key_checking = False
    ```

修改完成后，可以使用 `grep` 命令过滤掉注释行，检查核心配置：
```bash
cd /home/greg/ansible
grep -E -v ‘^(#|$)’ ansible.cfg
```

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_51.png)

---

## 7-06：验证Ansible配置

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_53.png)

所有配置完成后，必须进行验证，确保Ansible能够正确识别清单中的所有主机并与之通信。

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_55.png)

以下是验证步骤：

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_57.png)

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_59.png)

1.  使用 `ansible` 命令的 `ping` 模块测试所有主机的连通性。
    ```bash
    cd /home/greg/ansible
    ansible all -m ping
    ```
    如果配置正确，将对 `node1` 到 `node5` 所有主机返回 `SUCCESS` 和 `pong` 回复。

2.  可以进一步执行一个简单命令来确认控制能力，例如获取所有主机的主机名：
    ```bash
    ansible all -m command -a “hostname”
    ```

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_61.png)

---

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_63.png)

## 总结

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_65.png)

本节课中我们一起学习了RHCE考试中Ansible基础配置的全过程。我们完成了：
1.  **安装Ansible** 软件包。
2.  **创建并配置** 了专属的工作目录和主机清单文件，理解了主机与分组的定义方法。
3.  **复制并修改** 了Ansible主配置文件，设置了清单路径、角色路径等关键参数。
4.  **识别并处理** 了题目中的“暗坑”，正确配置了远程执行用户和特权升级参数，这是考试得分的关键。
5.  **使用 `ansible` 命令** 对配置结果进行了验证，确保环境可用。

![](img/3da6c4ddfb863c367f60ecbe90b60f3b_67.png)

通过本节的练习，你已经掌握了搭建一个可用Ansible控制环境的核心步骤，为后续学习编写Playbook和角色打下了坚实基础。