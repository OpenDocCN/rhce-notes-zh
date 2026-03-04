# Redhat红帽 RHCE8.0认证体系课程：P65：Day11_Day09Day10回顾02 📚

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_0.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_2.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_4.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_6.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_8.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_9.png)

在本节课中，我们将回顾Ansible的临时命令、常用管理模块、Playbook剧本编写基础、变量与事实等核心概念。这些内容是RHCE认证考试和日常自动化运维工作的基础。

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_11.png)

## 第一部分：Ansible临时命令 ⚙️

上一节我们介绍了Ansible的安装与初始化配置，本节中我们来看看Ansible的临时命令。

Ansible临时命令主要用于快速执行单个任务，无需编写完整的Playbook。其基本语法结构如下：

```bash
ansible <host-pattern> -m <module_name> -a "<module_arguments>"
```

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_13.png)

以下是临时命令的核心组成部分：
*   **`<host-pattern>`**：指定在资产清单中定义的主机或主机组。
*   **`-m <module_name>`**：指定要使用的Ansible模块。
*   **`-a "<module_arguments>"`**：指定模块对应的参数。

如果需要使用不同的资产清单文件，可以通过 `-i` 参数指定，但通常无需使用，因为配置文件 `ansible.cfg` 中已定义。

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_15.png)

**幂等性** 是Ansible的一个重要特性。它意味着无论任务执行多少次，只要目标主机的状态与期望的最终状态一致，Ansible就不会重复执行操作，只会返回 `ok` 状态而非 `changed` 状态。

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_17.png)

关于模块文档参考，推荐使用 `ansible-doc` 命令。在考试环境中，由于网络隔离，无法访问在线文档，因此必须掌握使用 `ansible-doc -l` 查看模块列表，以及 `ansible-doc <module_name>` 查看特定模块的详细用法和示例。

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_19.png)

## 第二部分：常用管理模块回顾 🧩

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_21.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_23.png)

接下来，我们回顾几个在Day09和Day10中学习过的核心管理模块。这些模块是构建自动化任务的基础。

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_24.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_26.png)

### 文件管理模块

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_27.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_29.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_31.png)

以下是常用的文件操作模块及其功能：

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_32.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_34.png)

*   **`copy` 模块**：用于复制文件。它有两个主要作用：
    1.  将文件从控制节点复制到受管主机。
    2.  直接在受管主机上生成文件内容（使用 `content` 参数）。
    关键参数包括 `src`（源）、`dest`（目标，必需）、`owner`（所有者）、`group`（属组）、`mode`（权限）和 `backup`（是否备份）。
*   **`file` 模块**：用于设置文件属性，如创建文件、目录或链接。
    关键参数包括 `path`（路径）和 `state`（状态，如 `directory` 创建目录，`touch` 创建文件，`link` 创建软链接，`absent` 删除）。
*   **`fetch` 模块**：用于从受管主机拉取文件到控制节点。
    关键参数包括 `src`（受管主机上的源路径）和 `dest`（控制节点上的目标路径）。
*   **`lineinfile` 模块**：用于确保文件中存在特定行，以行为单位进行流式处理。
    关键参数包括 `path`（目标文件路径）、`regexp`（用于定位行的正则表达式）、`line`（要插入/替换的整行内容）。可以使用 `insertafter` 或 `insertbefore` 在指定行之后或之前插入。`state=absent` 用于删除匹配的行。
*   **`synchronize` 模块**：用于目录同步，功能类似于 `rsync` 命令。

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_36.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_37.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_38.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_39.png)

### 软件包管理模块

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_41.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_43.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_45.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_47.png)

软件包管理是系统运维中的常见任务。以下是相关模块：

*   **`yum_repository` 模块**：用于配置YUM软件仓库。在综合练习中，通常需要先配置软件仓库，然后才能安装软件。对于RHEL 8，通常需要配置两个仓库：`BaseOS`（基础系统包）和 `AppStream`（应用流包）。其参数与编写 `.repo` 文件类似，需要指定 `baseurl`（仓库地址）、`name`（仓库描述）、`enabled`（是否启用）、`gpgcheck`（是否进行GPG验证）以及 `gpgkey`（GPG密钥地址）。
*   **`yum` 模块**：用于管理软件包。
    关键参数包括 `name`（指定软件包或软件包组名，组名前加 `@`）和 `state`（状态，如 `present` 安装、`absent` 卸载、`latest` 升级）。

### 系统管理模块

以下是用于管理系统用户、组和服务的模块：

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_49.png)

*   **`user` 模块**：用于管理用户，相当于 `useradd`、`userdel`、`usermod` 命令的集合。
    可以指定参数如 `name`（用户名）、`comment`（注释）、`group`（主组）、`groups`（附加组）、`state`（状态）、`shell`、`uid` 等。
*   **`group` 模块**：用于管理用户组。
*   **`service` 模块**：用于管理服务状态。
    关键参数包括 `name`（服务名）、`state`（状态，如 `started`、`stopped`、`restarted`）和 `enabled`（是否开机自启）。
*   **`firewalld` 模块**：用于管理 `firewalld` 防火墙规则，类似于 `firewall-cmd` 命令。
    在添加规则时，除了 `permanent`（永久生效）参数外，**务必记得加上 `immediate=yes`**，这相当于执行 `firewall-cmd --reload` 使规则立即生效。
*   **`get_url` 模块**：用于从网络下载文件。（注：`network` 模块的网络管理功能较弱，因此常用此模块处理下载任务）。

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_51.png)

## 第三部分：Playbook剧本基础 📜

在掌握了临时命令后，我们开始学习更强大的自动化工具——Playbook剧本。如果说临时命令是单条指令，那么Playbook就是完整的自动化脚本。

Playbook使用YAML格式编写。一个基本的Playbook结构如下：

```yaml
---
- name: Playbook的名称，用于注释说明
  hosts: target_hosts # 指定作用的目标主机组
  tasks: # 任务列表
    - name: 第一个任务的描述
      module_name: # 使用的模块
        parameter1: value1 # 模块参数
        parameter2: value2
    - name: 第二个任务的描述
      module_name:
        parameter: value
```

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_53.png)

关于YAML语法和Playbook编写的注意事项：

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_55.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_57.png)

1.  文件以 `---` 开头，表示一个YAML文档的开始。
2.  **缩进至关重要**：使用两个空格进行缩进，不要使用Tab键。任务列表（`tasks`）下的每个任务都以 `- `（横杠加空格）开头。
3.  **键值对格式**：键（如 `name`）后面跟着冒号和空格，然后是值。
4.  任务之间可以用空行隔开，提高可读性，便于后续调试。
5.  一个YAML文件可以包含多个Play（剧本），每个Play都以 `---` 分隔。

Playbook的执行与检查：
*   语法检查：`ansible-playbook playbook.yml --syntax-check`
*   空运行（模拟执行）：`ansible-playbook playbook.yml -C`
*   实际执行：`ansible-playbook playbook.yml`
*   可以添加 `-v`、`-vv`、`-vvv` 等参数来增加输出信息的详细程度，通常 `-v` 足以查看执行详情。

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_59.png)

## 第四部分：变量与事实 🔑

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_61.png)

在Playbook中，变量用于存储和引用值，使剧本更加灵活和可重用。事实（Facts）则是Ansible自动从受管主机收集的系统信息。

### 变量定义与引用

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_63.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_65.png)

**变量命名规则**：可以包含字母、数字和下划线，但不能以数字或下划线开头。避免使用点号（`.`），因为它在变量引用中有特殊含义。

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_67.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_69.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_71.png)

**变量定义的位置**：
1.  Playbook内部：使用 `vars:` 关键字。
2.  独立变量文件：使用 `vars_files:` 关键字引入。
3.  主机或组变量：在 `inventory` 目录下的 `host_vars/` 或 `group_vars/` 子目录中定义。
4.  在资产清单（Inventory）文件中定义：格式为 `hostname variable_name=value`（注意这里用等号）。
5.  命令行传递：`ansible-playbook playbook.yml -e "var_name=value"`

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_73.png)

**变量引用**：使用双花括号包裹变量名，如 `{{ variable_name }}`。

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_75.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_76.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_78.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_80.png)

### 变量类型

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_82.png)

*   **普通变量**：简单的键值对。
*   **列表/数组变量**：包含多个同类型值的变量。
    ```yaml
    package_list:
      - httpd
      - mariadb-server
      - php
    ```
*   **字典变量**：包含多个键值对，用于描述一个对象的多个属性。
    ```yaml
    user_info:
      name: alice
      uid: 2001
      shell: /bin/bash
    ```
*   **注册变量（Register Variables）**：使用 `register` 关键字将一个任务的输出结果保存到变量中，供后续任务使用。这在条件判断和循环中非常有用。

### 事实（Facts）

事实是Ansible自动收集的受管主机信息。要收集事实，需要在Play中设置 `gather_facts: yes`（默认即为 `yes`）。

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_84.png)

**引用事实**：
*   方式一：`{{ ansible_facts[‘hostname’] }}`
*   方式二：`{{ ansible_facts.hostname }}`
    对于包含点号的主机名等，建议使用第一种方式，或将整个引用用括号括起 `{{ (ansible_facts).hostname }}`。

事实包含丰富的信息，如主机名、IP地址、磁盘信息、网络接口等。可以使用 `ansible hostname -m setup` 命令查看所有收集到的事实。

**自定义事实**：可以在受管主机的 `/etc/ansible/facts.d/` 目录下创建 `.fact` 文件来定义自定义事实。

**魔法变量（Magic Variables）**：这些不是通过 `setup` 模块收集的，而是Ansible内部提供的变量，用于获取清单信息，例如：
*   `{{ groups }}`：所有组的字典。
*   `{{ group_names }}`：当前主机所属的组列表。
*   `{{ inventory_hostname }}`：当前主机的清单主机名。

---

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_86.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_88.png)

![](img/d1aad48273a9cfdfb63cc3c4e4352bdf_90.png)

本节课中我们一起学习了Ansible临时命令的用法，回顾了文件、软件包、系统管理等核心模块，掌握了Playbook剧本的基本结构和YAML语法，并深入理解了变量与事实的定义、类型及使用方法。这些知识是构建复杂自动化任务的基础，请务必熟练掌握。