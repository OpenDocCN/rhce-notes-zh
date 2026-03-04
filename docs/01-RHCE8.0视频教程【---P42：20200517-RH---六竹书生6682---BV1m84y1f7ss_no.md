# Ansible 变量管理：P42：变量定义与使用详解

![](img/e43e72f5bc2d944378c9256406f6d26a_0.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_2.png)

在本节课中，我们将学习 Ansible 中变量的概念、定义方式以及如何灵活地使用它们。变量可以让我们的 Playbook 更加通用和易于维护。

## 概述

变量是 Ansible 中的一个核心概念，它允许我们使用一个代号来代表一个值。这样，在编写 Playbook 时，我们无需将具体的值（如主机名、端口号）写死，而是通过变量来引用。当需要改变这些值时，只需修改变量的赋值即可，无需修改 Playbook 本身，这大大提高了脚本的灵活性和可重用性。

## 变量的命名规则

在定义变量之前，我们需要了解 Ansible 变量的命名规则。以下是变量命名的基本要求：

*   **组成元素**：变量名只能由**数字**、**字母**和**下划线（_）** 组成。
*   **开头字符**：变量名**只能以字母开头**，不能以数字或下划线开头。

一个简单的变量赋值格式如下：
```yaml
变量名=值
```
例如，我们可以定义一个变量来代表端口号：`port=80` 或 `port=8080`。

## 变量的定义方式

Ansible 提供了多种定义变量的方式，以适应不同的场景。以下是几种常见的方法：

### 1. 在 Playbook 中直接定义

第一种方式是在 Playbook 文件内部直接定义变量和它的值。

![](img/e43e72f5bc2d944378c9256406f6d26a_4.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_6.png)

**定义格式**：
在 Playbook 的 `vars:` 部分直接列出变量名和值。
```yaml
vars:
  service_name: httpd
  port_number: 80
```

![](img/e43e72f5bc2d944378c9256406f6d26a_8.png)

**调用方式**：
在 Playbook 的任务中，使用双层花括号 `{{ }}` 来引用变量。
```yaml
tasks:
  - name: 启动服务
    service:
      name: "{{ service_name }}"
      state: started
```

![](img/e43e72f5bc2d944378c9256406f6d26a_10.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_11.png)

### 2. 通过命令行赋值

![](img/e43e72f5bc2d944378c9256406f6d26a_13.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_15.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_16.png)

第二种方式是在执行 `ansible-playbook` 命令时，通过 `-e` 或 `--extra-vars` 参数来传递变量。

![](img/e43e72f5bc2d944378c9256406f6d26a_18.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_19.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_21.png)

**命令格式**：
```bash
ansible-playbook playbook.yml -e "变量名=值"
```
如果需要传递多个变量，用空格分隔：
```bash
ansible-playbook playbook.yml -e "pkg_name=tree service_state=stopped"
```
**优先级说明**：通过命令行传递的变量拥有最高的优先级，因为它代表了执行时人为指定的明确值。

![](img/e43e72f5bc2d944378c9256406f6d26a_23.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_25.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_27.png)

### 3. 在主机清单文件中定义

![](img/e43e72f5bc2d944378c9256406f6d26a_29.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_31.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_33.png)

第三种方式是在 Ansible 的主机清单文件（通常是 `/etc/ansible/hosts`）中定义变量。这又分为针对单台主机和针对主机组两种情况。

![](img/e43e72f5bc2d944378c9256406f6d26a_35.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_37.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_39.png)

*   **针对单台主机**：在主机IP或别名后直接设置变量。
    ```ini
    [webservers]
    web1 ansible_host=192.168.1.101 http_port=8080 max_requests=1000
    web2 ansible_host=192.168.1.102 http_port=8081
    ```
    在上面的例子中，变量 `http_port` 和 `max_requests` 只对 `web1` 主机生效。

![](img/e43e72f5bc2d944378c9256406f6d26a_41.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_42.png)

*   **针对主机组**：为主机组设置统一的变量，组内所有主机都会继承这些变量。
    ```ini
    [webservers]
    web1
    web2

    [webservers:vars]
    ntp_server=ntp.aliyun.com
    proxy_env=http://proxy.example.com:8080
    ```
    这里定义的 `ntp_server` 和 `proxy_env` 变量将对 `webservers` 组下的所有主机（web1 和 web2）生效。

![](img/e43e72f5bc2d944378c9256406f6d26a_44.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_46.png)

**优先级对比**：当单台主机定义的变量与它所在主机组定义的变量同名时，**单台主机的变量值优先级更高**。

### 4. 使用独立的变量文件

![](img/e43e72f5bc2d944378c9256406f6d26a_48.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_50.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_52.png)

第四种方式是将变量定义在一个独立的 YAML 文件中，然后在 Playbook 中导入。这种方式便于集中管理大量变量。

![](img/e43e72f5bc2d944378c9256406f6d26a_54.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_56.png)

**创建变量文件** (例如 `vars_file.yml`)：
```yaml
---
# 这是一个独立的变量文件
app_version: 2.0
deploy_path: /opt/myapp
admin_email: admin@example.com
```
**在 Playbook 中导入**：
使用 `vars_files` 关键字来包含外部变量文件。
```yaml
---
- hosts: webservers
  vars_files:
    - vars_file.yml  # 导入同目录下的变量文件
    - /path/to/other_vars.yml # 也可以使用绝对路径

  tasks:
    - name: 显示变量
      debug:
        msg: "应用版本是 {{ app_version }}，部署路径是 {{ deploy_path }}"
```

![](img/e43e72f5bc2d944378c9256406f6d26a_58.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_59.png)

## 使用系统变量（Facts）

除了自定义变量，Ansible 还能自动收集被管理节点的系统信息，这些信息被称为 “Facts”，它们本身就是一组强大的系统变量。

![](img/e43e72f5bc2d944378c9256406f6d26a_61.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_63.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_65.png)

**收集 Facts**：
使用 `setup` 模块可以获取远程主机的所有系统信息。
```bash
ansible web1 -m setup
```
这条命令会输出 `web1` 主机的大量信息，如 IP 地址、主机名、磁盘分区等。

![](img/e43e72f5bc2d944378c9256406f6d26a_67.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_69.png)

**在 Playbook 中使用 Facts**：
Facts 变量可以直接在 Playbook 中引用，它们通常以 `ansible_` 前缀开头。
```yaml
tasks:
  - name: 创建以主机名命名的文件
    file:
      path: /tmp/{{ ansible_hostname }}.txt  # 使用短主机名
      state: touch
  - name: 显示完全限定域名
    debug:
      msg: "这台主机的FQDN是 {{ ansible_fqdn }}"
```
常用的 Facts 变量包括：
*   `{{ ansible_hostname }}`: 系统短主机名。
*   `{{ ansible_fqdn }}`: 完全限定域名。
*   `{{ ansible_default_ipv4.address }}`: 默认 IPv4 地址。
*   `{{ ansible_distribution }}`: 系统发行版（如 CentOS， Ubuntu）。

![](img/e43e72f5bc2d944378c9256406f6d26a_71.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_73.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_75.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_77.png)

## 实战示例：变量定义与使用

![](img/e43e72f5bc2d944378c9256406f6d26a_79.png)

上一节我们介绍了变量的各种定义方式，本节我们通过一个实战示例来综合运用它们。我们将编写一个 Playbook，用于安装并管理一个服务（如 VSFTPD），并展示不同变量定义方式的效果。

**实验准备**：首先，确保目标节点上没有安装 VSFTPD 服务。
```bash
# 在目标节点上执行
yum remove -y vsftpd
```

![](img/e43e72f5bc2d944378c9256406f6d26a_81.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_83.png)

**编写 Playbook** (`manage_service.yml`)：
```yaml
---
- hosts: webservers  # 目标主机组
  remote_user: root
  vars:  # 方式1：在Playbook中定义变量
    pkg_name: "vsftpd"
    service_name: "vsftpd"

  tasks:
    - name: 安装软件包
      yum:
        name: "{{ pkg_name }}"
        state: present

    - name: 管理服务状态
      service:
        name: "{{ service_name }}"
        state: "{{ service_state | default('started') }}"  # 使用默认值
        enabled: "{{ service_enabled | default('yes') }}"
```
在这个 Playbook 中，我们定义了 `pkg_name` 和 `service_name` 变量。对于 `service_state` 和 `service_enabled`，我们使用了 Jinja2 过滤器的 `default()` 功能，这意味着如果变量未定义，将使用我们提供的默认值（‘started’ 和 ‘yes’）。

![](img/e43e72f5bc2d944378c9256406f6d26a_85.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_87.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_89.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_91.png)

**执行 Playbook**：
1.  **使用 Playbook 内定义的变量**：直接运行，服务将被安装并启动。
    ```bash
    ansible-playbook manage_service.yml
    ```
2.  **通过命令行覆盖变量**：在命令行中为 `service_state` 和 `service_enabled` 赋值，优先级最高。
    ```bash
    ansible-playbook manage_service.yml -e "service_state=stopped service_enabled=no"
    ```
    执行后，VSFTPD 服务将被停止并禁用开机自启。
3.  **结合主机清单变量**：假设我们在 `/etc/ansible/hosts` 中为某台主机定义了特定的变量。
    ```ini
    [webservers]
    web1
    web2 service_port=2121  # 为web2单独定义一个变量
    ```
    我们可以在 Playbook 中引用这个主机级别的变量来配置不同的端口（需扩展 Playbook 任务）。

![](img/e43e72f5bc2d944378c9256406f6d26a_93.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_95.png)

## 总结

![](img/e43e72f5bc2d944378c9256406f6d26a_97.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_99.png)

![](img/e43e72f5bc2d944378c9256406f6d26a_101.png)

本节课我们一起深入学习了 Ansible 中的变量管理。我们首先了解了变量的基本命名规则，然后系统地学习了四种主要的变量定义方式：在 Playbook 中定义、通过命令行传递、在主机清单中定义以及使用独立的变量文件，并比较了它们的优先级。我们还探索了 Ansible 自动收集的系统变量（Facts）及其用法。最后，通过一个实战示例，我们综合运用了这些知识。合理使用变量是编写高效、可维护 Ansible Playbook 的关键技能，它能让你的自动化脚本适应多变的环境和需求。