# Redhat红帽 RHCE8.0认证体系课程：P54：管理Ansible配置文件 🛠️

在本节课中，我们将要学习如何管理Ansible的配置文件。我们将了解配置文件的优先级、结构，并通过实践配置一个工作目录下的配置文件，以实现对受管主机的自动化管理。

---

![](img/d5181e19d7c86b555b31a4f13a8e80e7_1.png)

![](img/d5181e19d7c86b555b31a4f13a8e80e7_3.png)

## 配置文件优先级 📊

上一节我们介绍了Ansible的资产清单和免密认证。本节中我们来看看如何通过配置文件来定制Ansible的行为。Ansible控制节点会从多个可能的位置之一读取其配置文件，这些位置有不同的优先级。数字越大，代表优先级越低。

![](img/d5181e19d7c86b555b31a4f13a8e80e7_5.png)

以下是Ansible配置文件查找的优先级顺序：

1.  **当前工作目录下的 `ansible.cfg` 文件**：如果执行Ansible命令的目录中存在此文件，则使用它。这是优先级最高的配置，生效范围仅限于当前工作目录。
2.  **用户家目录下的 `.ansible.cfg` 文件**：这是一个隐藏文件。如果当前工作目录没有配置文件，且用户家目录存在此文件，则使用它。生效范围是当前用户。
3.  **`/etc/ansible/ansible.cfg` 文件**：这是系统级的默认配置文件。如果以上两个位置都没有配置文件，则使用此文件。生效范围是全局。

![](img/d5181e19d7c86b555b31a4f13a8e80e7_7.png)

在考试和实际项目中，通常使用优先级最高的第一种方式，即为不同的项目或环境创建独立的配置文件。

---

## 配置文件结构解析 📝

![](img/d5181e19d7c86b555b31a4f13a8e80e7_9.png)

![](img/d5181e19d7c86b555b31a4f13a8e80e7_10.png)

了解了优先级后，我们来看看配置文件的具体结构。配置文件主要由不同的配置节（section）组成，其中 `[defaults]` 是最核心的配置节，定义了Ansible的基本行为。

![](img/d5181e19d7c86b555b31a4f13a8e80e7_12.png)

以下是 `[defaults]` 配置节中一些关键选项的说明：

![](img/d5181e19d7c86b555b31a4f13a8e80e7_14.png)

![](img/d5181e19d7c86b555b31a4f13a8e80e7_16.png)

![](img/d5181e19d7c86b555b31a4f13a8e80e7_18.png)

*   **`inventory`**：此选项定义了资产清单文件的路径。这是必要的配置项。
    *   **公式/代码**：`inventory = /path/to/your/inventory_file`
*   **`remote_user`**：此选项指定了连接受管主机时使用的默认远程用户。
    *   **公式/代码**：`remote_user = student`
*   **`remote_port`**：此选项指定了连接受管主机时使用的SSH端口，默认为22。
    *   **公式/代码**：`remote_port = 22`
*   **`ask_pass`**：此选项决定在执行任务时是否需要手动输入SSH密码。为了实现自动化，通常将其设为 `false`，并配合SSH密钥认证使用。
    *   **公式/代码**：`ask_pass = false`
*   **`[privilege_escalation]`**：此配置节用于配置权限提升（例如，以普通用户身份执行需要root权限的任务）。
    *   **`become`**：是否启用权限提升，设为 `true`。
        *   **公式/代码**：`become = true`
    *   **`become_method`**：权限提升的方法，常用 `sudo`。
        *   **公式/代码**：`become_method = sudo`
    *   **`become_user`**：要提升到的目标用户，通常为 `root`。
        *   **公式/代码**：`become_user = root`
    *   **`become_ask_pass`**：权限提升时是否需要输入密码。如果设为 `false`，则需要在受管主机上配置 `sudo` 免密码。
        *   **公式/代码**：`become_ask_pass = false`

![](img/d5181e19d7c86b555b31a4f13a8e80e7_20.png)

---

![](img/d5181e19d7c86b555b31a4f13a8e80e7_22.png)

![](img/d5181e19d7c86b555b31a4f13a8e80e7_24.png)

## 实践：配置工作目录环境 🧪

![](img/d5181e19d7c86b555b31a4f13a8e80e7_26.png)

![](img/d5181e19d7c86b555b31a4f13a8e80e7_28.png)

现在，让我们动手配置一个符合考试要求的工作环境。我们将使用普通用户 `student` 在其家目录下的一个特定文件夹中工作。

以下是配置步骤：

![](img/d5181e19d7c86b555b31a4f13a8e80e7_30.png)

1.  **切换到 `student` 用户并创建工作目录**：
    ```bash
    su - student
    mkdir ~/ansible
    cd ~/ansible
    ```

![](img/d5181e19d7c86b555b31a4f13a8e80e7_32.png)

2.  **创建资产清单文件 `inventory`**：
    ```bash
    vi inventory
    ```
    文件内容示例（根据你的实际主机名修改）：
    ```
    [servers]
    node1.lab.example.com
    node2.lab.example.com
    ```

![](img/d5181e19d7c86b555b31a4f13a8e80e7_34.png)

3.  **创建并编辑 Ansible 配置文件 `ansible.cfg`**：
    ```bash
    vi ansible.cfg
    ```
    文件内容示例：
    ```ini
    [defaults]
    inventory = /home/student/ansible/inventory
    remote_user = student
    remote_port = 22
    ask_pass = false

    [privilege_escalation]
    become = true
    become_method = sudo
    become_user = root
    become_ask_pass = false
    ```
    *注意：确保 `student` 用户已与受管主机建立SSH免密认证，并且在受管主机上已配置 `student` 用户可以通过 `sudo` 执行所有命令且无需密码（例如，在 `/etc/sudoers.d/` 中添加 `student ALL=(ALL) NOPASSWD:ALL`）。*

![](img/d5181e19d7c86b555b31a4f13a8e80e7_36.png)

![](img/d5181e19d7c86b555b31a4f13a8e80e7_38.png)

![](img/d5181e19d7c86b555b31a4f13a8e80e7_40.png)

4.  **验证配置**：
    *   检查生效的配置文件路径：
        ```bash
        ansible --version | grep “config file”
        ```
        应显示当前目录下的 `ansible.cfg`。
    *   测试列出所有受管主机：
        ```bash
        ansible all --list-hosts
        ```
    *   测试执行一个需要权限的命令（如查看磁盘分区）：
        ```bash
        ansible node1 -m shell -a “fdisk -l /dev/sda”
        ```
        如果配置正确，此命令应能成功执行并返回结果，无需手动输入任何密码。

---

![](img/d5181e19d7c86b555b31a4f13a8e80e7_42.png)

## 总结 📚

![](img/d5181e19d7c86b555b31a4f13a8e80e7_44.png)

![](img/d5181e19d7c86b555b31a4f13a8e80e7_46.png)

本节课中我们一起学习了Ansible配置文件的管理。我们首先明确了配置文件的三种优先级，其中当前工作目录下的 `ansible.cfg` 优先级最高，也最常用。接着，我们解析了配置文件的核心结构，特别是 `[defaults]` 和 `[privilege_escalation]` 配置节中的关键参数。最后，我们通过一个完整的实践，配置了一个基于普通用户 `student` 的工作环境，实现了资产清单管理、免密连接以及通过 `sudo` 进行权限提升的自动化流程。掌握这些是使用Ansible进行高效自动化运维的基础。