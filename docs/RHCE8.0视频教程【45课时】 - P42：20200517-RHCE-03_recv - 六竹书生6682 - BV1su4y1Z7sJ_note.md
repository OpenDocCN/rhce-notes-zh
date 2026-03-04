# Ansible 变量管理：P42：变量定义与使用详解

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_0.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_2.png)

在本节课中，我们将要学习 Ansible 中变量的概念、定义方式以及如何在实际的 Playbook 中使用它们。变量可以让我们的自动化脚本更加灵活和可重用。

## 什么是变量？🤔

变量是一个代号，用于存储可以在后续操作中动态改变的值。在编写 Playbook 时，如果某些值（如主机名、端口号）是固定的，修改起来会很麻烦。使用变量后，我们可以根据实际情况灵活地设置这些值，而无需反复编辑 Playbook 文件。

## 变量的命名规则 📝

变量在命名时需要遵循特定的规则，其组成只能包含以下三种元素：
*   数字
*   字母
*   下划线 (`_`)

并且，**变量名只能以字母开头**，不能以数字或下划线开头。

变量的基本赋值格式如下：
```yaml
变量名=变量值
```
例如，我们可以定义一个变量来设置 HTTP 服务的端口：`port=8080`。

## 变量的定义方式 🗂️

Ansible 提供了多种定义变量的方式，我们将重点介绍其中四种。

### 方式一：在 Playbook 中直接定义

第一种方式是在 Playbook 文件内部直接定义变量和它的值。

在 Playbook 中定义变量的格式如下：
```yaml
vars:
  变量名1: 值1
  变量名2: 值2
```
定义好变量后，在 Playbook 中调用的方式是使用两级花括号 `{{ }}`，例如：`{{ 变量名1 }}`。

### 方式二：通过命令行传递变量

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_4.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_6.png)

第二种方式是在执行 `ansible-playbook` 命令时，通过 `-e` 参数在命令行中为变量赋值。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_8.png)

命令行赋值的格式如下：
```bash
ansible-playbook playbook.yml -e “变量名=值”
```
如果有多个变量，可以用空格分隔：`-e “变量名1=值1 变量名2=值2”`。**命令行赋值的优先级是最高的**，因为它代表了人为的明确指定。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_10.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_11.png)

### 方式三：在主机清单文件中定义变量

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_13.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_15.png)

第三种方式是在 `/etc/ansible/hosts` 主机清单文件中定义变量。这又分为针对单台主机和针对主机组两种情况。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_16.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_18.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_19.png)

以下是针对单台主机定义变量的示例：
```ini
192.168.127.201 number_port=1001 zone=上海
192.168.127.202 number_port=1002 zone=杭州
```
这表示变量 `number_port` 对于主机 `201` 的值是 `1001`，对于主机 `202` 的值是 `1002`。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_21.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_23.png)

以下是针对主机组定义变量的示例：
```ini
[web_2012]
192.168.127.201
192.168.127.202

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_25.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_27.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_29.png)

[web_2012:vars]
country=china
```
这表示变量 `country` 会对 `web_2012` 这个组中的所有主机生效。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_31.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_33.png)

**优先级说明**：当单台主机定义的变量与主机组定义的变量冲突时（例如主机 `202` 自身定义了 `zone=杭州`，而其所属的组定义了 `zone=上海`），**单台主机定义的变量优先级更高**。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_35.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_37.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_39.png)

### 方式四：使用独立的变量文件

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_41.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_43.png)

第四种方式是创建一个专门的文件来存放变量，然后在 Playbook 中引入这个文件。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_45.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_47.png)

首先，创建一个变量文件（例如 `vars_file.yml`），其内容格式如下：
```yaml
变量名1: 值1
变量名2: 值2
```
然后，在 Playbook 中通过 `vars_files` 指令引入这个文件：
```yaml
vars_files:
  - vars_file.yml
```
引入后，就可以在 Playbook 中像使用普通变量一样使用文件里定义的变量了。这种方式便于集中管理多个 Playbook 共用的变量。

## 使用系统变量（Facts）🔧

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_49.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_51.png)

除了自定义变量，Ansible 还能自动收集被管理节点的系统信息，这些信息被称为 “Facts”，它们本身就是一系列可用的系统变量。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_53.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_55.png)

我们可以使用 `setup` 模块来查看所有可用的系统变量：
```bash
ansible web_2012 -m setup
```
由于输出信息很多，我们通常需要过滤。例如，获取主机名：
```bash
ansible web_2012 -m setup -a “filter=ansible_hostname”
```
一些常用的系统变量包括：
*   `ansible_hostname`：短主机名。
*   `ansible_nodename`：完全限定域名（FQDN）。
*   `ansible_fqdn`：同 `ansible_nodename`。
*   `ansible_interfaces`：网络接口列表。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_57.png)

在 Playbook 中，我们可以直接调用这些变量。例如，在远端主机的 `/tmp` 目录下创建一个以主机名命名的文件：
```yaml
- name: 使用系统变量创建文件
  file:
    path: /tmp/{{ ansible_hostname }}.txt
    state: touch
```

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_59.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_61.png)

## 实战演练：测试多种变量赋值方式 🧪

上一节我们介绍了变量的各种定义方式，本节中我们通过一个实际例子来测试它们。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_63.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_65.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_67.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_69.png)

我们将编写一个 Playbook 来安装并启动一个服务（如 `vsftpd`），但服务名通过变量指定。

**实验准备**：首先，确保被管理节点上移除了 `vsftpd` 服务。
```bash
yum remove vsftpd -y
```

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_71.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_73.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_75.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_77.png)

**编写 Playbook**：创建一个 Playbook 文件 `app_test.yml`，内容如下：
```yaml
- hosts: web_2012
  remote_user: root
  tasks:
    - name: 安装软件包
      yum:
        name: “{{ pkg_name }}”
        state: present

    - name: 启动服务
      service:
        name: “{{ pkg_name }}”
        state: started
        enabled: yes
```
在这个 Playbook 中，我们使用了变量 `pkg_name` 来代表要操作的软件包名。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_79.png)

**测试命令行赋值**：执行 Playbook 时，通过 `-e` 参数指定变量值。
```bash
ansible-playbook app_test.yml -e “pkg_name=vsftpd”
```
执行后，检查服务是否已安装并启动。

**测试在 Playbook 中定义变量**：修改 Playbook，在顶部添加 `vars` 部分。
```yaml
- hosts: web_2012
  remote_user: root
  vars:
    pkg_name: vsftpd
  tasks:
    … # 任务部分不变
```
然后直接运行 Playbook，无需 `-e` 参数，效果相同。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_81.png)

**测试主机清单变量**：在 `/etc/ansible/hosts` 中为主机组定义 `pkg_name` 变量，然后运行一个调用该变量的简单 Playbook（如创建文件），观察效果。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_83.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_85.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_87.png)

**测试独立变量文件**：创建一个变量文件 `my_vars.yml`，定义 `pkg_name: tree`。在 Playbook 中使用 `vars_files` 引入该文件，并运行 Playbook 安装 `tree` 软件。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_89.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_91.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_93.png)

通过以上练习，你可以直观地感受不同变量定义方式的使用场景和效果。

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_95.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_97.png)

## 总结 📚

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_99.png)

![](img/0786549b3b1d7cd0fe8dbe099d0a202f_101.png)

本节课中我们一起学习了 Ansible 变量的核心知识。我们首先了解了变量的作用和命名规则，然后详细探讨了四种主要的变量定义方式：在 Playbook 内定义、通过命令行传递、在主机清单中定义以及使用独立的变量文件。我们还学习了如何获取和使用系统自带的 Facts 变量。最后，通过一个安装服务的实战例子，我们综合练习了多种变量赋值方法。掌握变量的灵活运用，是编写高效、可复用 Ansible 自动化脚本的关键一步。