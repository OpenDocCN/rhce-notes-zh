# Redhat红帽 RHCE8.0认证体系课程：P71：第12天知识回顾与练习 🧠

## 概述
在本节课中，我们将回顾前五章的核心知识，并通过综合练习来巩固所学内容。我们将涵盖Ansible的基础架构、安装配置、临时命令、剧本编写、变量与事实以及任务控制等关键概念。

---

## 第一章：Ansible基础架构回顾 🏗️

上一节我们介绍了课程的整体安排，本节中我们来看看Ansible的基础架构模型。

Ansible的核心架构基于控制节点与受管主机的关系。控制节点作为“指挥部”，通过建立好的连接向受管主机“发号施令”，实现批量管理和部署。

以下是几个核心概念：
*   **控制节点**：运行Ansible命令、存放剧本和配置的机器，即指挥部。
*   **受管主机**：被控制节点管理的目标机器。
*   **资产清单**：受管主机的列表，定义了主机和分组。
*   **剧本**：定义了要在受管主机上执行的任务和步骤，如同演员的台本。
*   **角色**：一种更高级的剧本组织方式，将任务、变量、文件等模块化。

**核心关系公式**：
`控制节点` + `资产清单` + `剧本/角色` -> `批量管理受管主机`

---

## 第二章：安装、初始化与配置 ⚙️

上一节我们回顾了基础概念，本节中我们来看看如何安装和初始化Ansible环境。

![](img/fbf0aabbdc55e114eaf774216babc93e_1.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_2.png)

### 安装Ansible
安装过程相对简单，主要前提是配置好EPEL（Extra Packages for Enterprise Linux）源。
```bash
# 示例：在RHEL/CentOS 8上安装Ansible
sudo dnf install epel-release
sudo dnf install ansible
```

### 配置资产清单
资产清单文件（默认`/etc/ansible/hosts`）定义了受管主机。其配置非常灵活。

![](img/fbf0aabbdc55e114eaf774216babc93e_4.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_6.png)

以下是几种常见的定义方式：
1.  **单台主机**：直接使用IP地址或主机名。
    ```
    192.168.1.100
    ```
2.  **主机分组**：将多台主机归入一个组。
    ```
    [webservers]
    node1.example.com
    node2.example.com
    ```
3.  **嵌套分组**：使用 `:children` 将多个组组合成一个大组。
    ```
    [datacenter:children]
    webservers
    databaseservers
    ```
4.  **主机范围**：使用模式简化连续主机的定义。
    ```
    node[1:3].example.com
    ```

![](img/fbf0aabbdc55e114eaf774216babc93e_8.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_10.png)

**注意**：在实验环境中，通常需要在控制节点的`/etc/hosts`文件中为受管主机添加主机名解析，但在实际考试中可能不需要。

![](img/fbf0aabbdc55e114eaf774216babc93e_12.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_14.png)

### 配置免密认证
为了实现无密码连接，需要在控制节点和受管主机之间建立SSH密钥认证。
1.  在控制节点生成SSH密钥对：`ssh-keygen`
2.  将公钥复制到受管主机：`ssh-copy-id user@remote_host`

![](img/fbf0aabbdc55e114eaf774216babc93e_16.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_18.png)

### 验证连接
使用以下命令验证资产清单中的主机是否可连接：
```bash
ansible all --list-hosts
ansible all -m ping
```

### 配置文件与优先级
Ansible配置文件的优先级从高到低如下：
1.  **环境变量**：`ANSIBLE_CONFIG`
2.  **当前工作目录**：`./ansible.cfg`
3.  **用户家目录**：`~/.ansible.cfg`
4.  **系统默认**：`/etc/ansible/ansible.cfg`

一个常见的配置文件示例如下，它配置了使用普通用户连接并提权：
```ini
[defaults]
inventory = ./inventory
remote_user = student
host_key_checking = False

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
```
**注意**：需要在受管主机上为相应用户配置`sudo`权限。

---

## 第三章：临时命令与常用模块 🛠️

上一节我们完成了环境配置，本节中我们来看看如何通过临时命令和模块快速执行任务。

### 临时命令格式
临时命令用于快速执行单个任务，格式如下：
```bash
ansible <pattern> -m <module_name> -a “<module_arguments>” -i <inventory_file>
```
*   `pattern`：目标主机或组，如 `all`, `webservers`。
*   `-m`：指定使用的模块。
*   `-a`：传递给模块的参数。
*   `-i`：指定资产清单文件（如果未在配置文件中指定）。

### 核心概念：幂等性
Ansible模块设计是**幂等**的。这意味着无论执行多少次，只要目标状态一致，结果都是相同的，不会造成重复操作或错误。

### 常用模块简介
以下是前五章涉及的部分核心模块：

**文档与帮助**
*   `ansible-doc <module_name>`：查看模块详细文档。
*   `ansible-doc -s <module_name>`：查看模块参数简例。
*   官方文档网站：`docs.ansible.com`

![](img/fbf0aabbdc55e114eaf774216babc93e_20.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_22.png)

**文件操作模块**
*   `copy`：将文件从控制节点复制到受管主机，或直接在受管主机上生成文件内容。
*   `file`：设置文件属性（权限、所有权），或创建目录、链接。
*   `fetch`：将文件从受管主机拉取到控制节点。
*   `lineinfile`：确保文件中存在某一行内容（增、改）。
*   `synchronize`：使用`rsync`协议同步目录内容。

**软件包模块**
*   `yum_repository`：配置YUM/DNF软件仓库。
*   `yum` 或 `dnf`：管理软件包（安装、移除、更新）。

![](img/fbf0aabbdc55e114eaf774216babc93e_24.png)

**系统模块**
*   `user`/`group`：管理用户和组。
*   `service`：管理系统服务（启动、停止、启用）。
*   `firewalld`：管理防火墙规则和区域。
*   `command`/`shell`：在受管主机上执行命令。
    *   `command`：直接执行命令，不通过shell处理，因此无法使用变量、管道等shell特性。
    *   `shell`：通过shell执行命令，可以使用shell特性，但需注意潜在的安全风险。

---

## 第四章：剧本编写基础 📜

上一节我们学习了即时的临时命令，本节中我们来看看如何编写可重复使用的剧本。

### 剧本结构
剧本使用YAML格式编写，以三个连字符 `---` 开头。基本结构包含主机列表、变量和任务序列。
```yaml
---
- name: 我的第一个剧本
  hosts: webservers
  vars:
    http_port: 80
  tasks:
    - name: 确保nginx已安装
      yum:
        name: nginx
        state: present
    - name: 启动nginx服务
      service:
        name: nginx
        state: started
        enabled: yes
```

### 关键语法点
*   **缩进**：YAML严格依赖缩进（通常为两个空格）来定义结构。错误的缩进会导致剧本执行失败。
*   **列表项**：使用短横线 `-` 加一个空格开头。
*   **键值对**：使用 `key: value` 格式，冒号后通常有一个空格。

### 剧本执行与验证
建议按以下流程执行剧本：
1.  **语法检查**：`ansible-playbook --syntax-check myplaybook.yml`
2.  **试运行（空跑）**：`ansible-playbook -C myplaybook.yml`
3.  **实际执行**：`ansible-playbook myplaybook.yml`
4.  **增加输出详情**：使用 `-v`、`-vv`、`-vvv` 参数，`-v` 通常足以查看任务执行状态。

---

![](img/fbf0aabbdc55e114eaf774216babc93e_26.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_28.png)

## 第五章：变量、事实与任务控制 🎛️

![](img/fbf0aabbdc55e114eaf774216babc93e_30.png)

上一节我们掌握了剧本的基本写法，本节中我们深入探讨剧本的灵活性，包括变量、事实收集和流程控制。

![](img/fbf0aabbdc55e114eaf774216babc93e_32.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_34.png)

### 变量
变量用于使剧本动态化、参数化。

![](img/fbf0aabbdc55e114eaf774216babc93e_35.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_37.png)

**变量定义位置与优先级（从高到低）**
1.  命令行传递：`ansible-playbook -e “var_name=value”`
2.  剧本内 `vars` 或 `vars_files` 定义。
3.  资产清单中定义（主机变量 `host_vars/`，组变量 `group_vars/`）。
4.  剧本内置变量或事实。

![](img/fbf0aabbdc55e114eaf774216babc93e_39.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_41.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_43.png)

**变量引用与类型**
*   **引用**：`{{ variable_name }}`
*   **类型**：
    *   字符串/数字：`my_var: “some value”`
    *   列表：`my_list: [‘item1’, ‘item2’]`
    *   字典：`my_dict: {‘key1’: ‘value1’, ‘key2’: ‘value2’}`

**特殊变量**
*   **注册变量**：使用 `register` 关键字将一个任务的结果保存到变量中，供后续任务使用。
    ```yaml
    - name: 检查某个命令的输出
      command: ls /tmp
      register: command_result
    - name: 打印输出
      debug:
        msg: “{{ command_result.stdout }}”
    ```
*   **加密变量**：使用 `ansible-vault` 加密敏感数据（如密码）。
    *   创建：`ansible-vault create secret.yml`
    *   编辑：`ansible-vault edit secret.yml`
    *   查看：`ansible-vault view secret.yml`
    *   剧本中使用：`ansible-playbook --ask-vault-pass playbook.yml`

### 事实
事实是Ansible从受管主机自动收集的系统信息。

*   **收集事实**：默认在剧本开始时收集。可通过 `gather_facts: yes/no` 控制。
*   **使用事实**：所有事实都存储在 `ansible_facts` 字典中，或直接通过 `{{ ansible_facts[‘os_family’] }}` 或 `{{ ansible_facts.os_family }}` 访问。
*   **常用事实**：`ansible_facts.hostname`, `ansible_facts.default_ipv4.address`, `ansible_facts.memtotal_mb`。
*   **魔法变量**：如 `hostvars`（所有主机事实）、`group_names`（当前主机所属组）、`inventory_hostname`（资产清单中的主机名）。

### 任务控制
任务控制结构让剧本逻辑更强大。

**循环**
使用 `loop` 对列表项进行迭代。
```yaml
- name: 创建多个用户
  user:
    name: “{{ item }}”
    state: present
  loop:
    - alice
    - bob
    - charlie
```
对于字典列表，可以使用 `dict2items` 过滤器转换后循环。

![](img/fbf0aabbdc55e114eaf774216babc93e_45.png)

**条件判断**
使用 `when` 语句根据条件执行任务。
```yaml
- name: 仅在CentOS上安装软件包
  yum:
    name: some_package
    state: present
  when: ansible_facts[‘os_family’] == “RedHat”
```

![](img/fbf0aabbdc55e114eaf774216babc93e_47.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_48.png)

**触发器**
使用 `notify` 和 `handlers`。当任务状态发生**改变**时，`notify` 会触发对应的 `handler` 执行。
```yaml
tasks:
  - name: 修改配置文件
    template:
      src: template.j2
      dest: /etc/app/config.conf
    notify: restart app service

![](img/fbf0aabbdc55e114eaf774216babc93e_50.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_52.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_53.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_54.png)

handlers:
  - name: restart app service
    service:
      name: app
      state: restarted
```

![](img/fbf0aabbdc55e114eaf774216babc93e_55.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_56.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_58.png)

**错误处理**
控制任务失败时的行为是编写健壮剧本的关键。
*   `ignore_errors: yes`：忽略当前任务的错误，继续执行后续任务。
*   `failed_when`：自定义任务失败的条件。
*   `changed_when`：自定义任务状态“已改变”的条件。
*   **区块错误处理**：使用 `block`, `rescue`, `always`。
    ```yaml
    - block:
        - name: 尝试执行可能失败的操作
          command: /bin/false
      rescue:
        - name: 当block失败时执行救援
          debug:
            msg: “任务失败，执行救援步骤”
      always:
        - name: 无论成功失败都执行
          debug:
            msg: “清理步骤”
    ```

![](img/fbf0aabbdc55e114eaf774216babc93e_60.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_62.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_63.png)

以下流程图清晰地概括了剧本执行流程与错误处理逻辑：
```
[开始执行Playbook]
        |
        v
[顺序执行任务] ---成功---> [继续下一个任务]
        |
        v (失败)
[任务失败？] ---是---> [默认：Playbook终止]
        |               |
        |          (使用 ignore_errors: yes)
        |               |
        v               v
[否，继续]        [忽略错误，继续执行]
        |
        v
[任务状态改变？] ---是---> [触发 notify -> 执行 handler]
        |               |        |
        |               |   (handler执行失败)
        |               |        v
        |               |   [默认：Playbook终止]
        |               |   (使用 force_handlers: yes)
        |               |        |
        |               |        v
        |               |   [强制执行handler，然后终止]
        |               |
        v               v
[继续执行]        [流程结束或继续]
```

---

![](img/fbf0aabbdc55e114eaf774216babc93e_65.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_66.png)

![](img/fbf0aabbdc55e114eaf774216babc93e_68.png)

## 总结
本节课中我们一起回顾了Ansible前五章的核心知识体系。我们从基础架构和安装配置出发，学习了通过临时命令和模块进行快速操作，进而掌握了编写可复用、结构化的剧本。最后，我们深入探讨了使剧本动态化的变量与事实，以及实现复杂逻辑的任务控制机制（循环、条件、触发器、错误处理）。通过接下来的综合练习，希望大家能巩固这些概念，为后续更高级的主题（如角色、模板等）打下坚实基础。