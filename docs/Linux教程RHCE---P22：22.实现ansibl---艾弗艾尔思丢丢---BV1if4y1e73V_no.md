# Linux教程RHCE - P22：22.实现ansible企业级用法playbook

![](img/f8397c946799ced8cedde1754cc2466d_0.png)

![](img/f8397c946799ced8cedde1754cc2466d_2.png)

![](img/f8397c946799ced8cedde1754cc2466d_4.png)

![](img/f8397c946799ced8cedde1754cc2466d_6.png)

## 概述
在本节课中，我们将学习Ansible的核心组件之一——Playbook。我们将了解如何编写和使用Playbook来自动化复杂的任务序列，并介绍Ansible Galaxy、Vault等辅助工具，帮助你掌握企业级的Ansible用法。

![](img/f8397c946799ced8cedde1754cc2466d_8.png)

![](img/f8397c946799ced8cedde1754cc2466d_10.png)

![](img/f8397c946799ced8cedde1754cc2466d_12.png)

---

![](img/f8397c946799ced8cedde1754cc2466d_14.png)

![](img/f8397c946799ced8cedde1754cc2466d_15.png)

![](img/f8397c946799ced8cedde1754cc2466d_17.png)

## Ansible Galaxy：下载与使用社区角色

![](img/f8397c946799ced8cedde1754cc2466d_19.png)

![](img/f8397c946799ced8cedde1754cc2466d_21.png)

![](img/f8397c946799ced8cedde1754cc2466d_22.png)

上一节我们介绍了Ansible的基础模块，本节中我们来看看如何利用社区资源来加速我们的工作。

![](img/f8397c946799ced8cedde1754cc2466d_24.png)

![](img/f8397c946799ced8cedde1754cc2466d_26.png)

![](img/f8397c946799ced8cedde1754cc2466d_28.png)

Ansible Galaxy是一个连接社区的网站和工具，你可以从该网站下载由其他用户编写并分享的“角色”。角色是将多个Playbook和相关文件组合在一起的复杂项目，用于解决特定的实际问题。

![](img/f8397c946799ced8cedde1754cc2466d_30.png)

以下是使用`ansible-galaxy`命令的基本操作：

*   **安装角色**：`ansible-galaxy install <角色名>`。此命令会从Galaxy网站下载指定的角色到本地。
*   **列出已安装角色**：`ansible-galaxy list`。此命令显示当前用户目录下已安装的所有角色。
*   **删除角色**：`ansible-galaxy remove <角色名>`。此命令会删除指定的已安装角色。

![](img/f8397c946799ced8cedde1754cc2466d_32.png)

![](img/f8397c946799ced8cedde1754cc2466d_34.png)

下载的角色通常位于用户家目录下的隐藏文件夹`.ansible/roles/`中。你可以直接使用这些角色，或者复制并修改它们以满足自己的需求。

![](img/f8397c946799ced8cedde1754cc2466d_36.png)

![](img/f8397c946799ced8cedde1754cc2466d_38.png)

---

![](img/f8397c946799ced8cedde1754cc2466d_40.png)

## Playbook简介：自动化任务脚本

了解了如何获取现成的角色后，我们来看看如何从基础开始，编写自己的自动化脚本——Playbook。

如果说单个Ansible模块相当于Linux的一条命令，那么Playbook就相当于一个Shell脚本。它允许你将多个任务（即模块调用）按顺序组织在一个YAML格式的文件中，形成一个完整的自动化流程。

一个简单的Playbook结构如下：
```yaml
---
- hosts: webservers
  remote_user: root
  tasks:
    - name: Ensure a file exists
      file:
        path: /data/newfile
        state: touch
```
*   `---`：YAML文件的起始标记（可选，但为惯例）。
*   `hosts`：指定要在哪些主机或主机组上执行任务。
*   `remote_user`：指定在远程主机上执行任务时使用的身份。
*   `tasks`：定义要执行的任务列表。
*   `name`：任务的描述性名称。
*   `file:`：调用的模块名，其后的缩进内容是传递给该模块的参数。

![](img/f8397c946799ced8cedde1754cc2466d_42.png)

![](img/f8397c946799ced8cedde1754cc2466d_44.png)

执行Playbook的命令是：`ansible-playbook <playbook文件名>.yml`。在正式运行前，可以使用`ansible-playbook --check <playbook文件名>.yml`进行“干跑”，检查语法和潜在问题。

![](img/f8397c946799ced8cedde1754cc2466d_46.png)

![](img/f8397c946799ced8cedde1754cc2466d_48.png)

---

![](img/f8397c946799ced8cedde1754cc2466d_50.png)

![](img/f8397c946799ced8cedde1754cc2466d_52.png)

![](img/f8397c946799ced8cedde1754cc2466d_53.png)

![](img/f8397c946799ced8cedde1754cc2466d_55.png)

## YAML语言基础：Playbook的语法

![](img/f8397c946799ced8cedde1754cc2466d_57.png)

![](img/f8397c946799ced8cedde1754cc2466d_59.png)

要熟练编写Playbook，必须理解其底层使用的YAML语言。YAML是一种对人类友好的数据序列化语言，强调可读性和简洁性。

YAML有两个核心概念：

![](img/f8397c946799ced8cedde1754cc2466d_61.png)

![](img/f8397c946799ced8cedde1754cc2466d_63.png)

![](img/f8397c946799ced8cedde1754cc2466d_64.png)

1.  **列表（List）**：使用短横线`-`开头表示一个列表项，所有项属于同一集合。
    ```yaml
    - Apple
    - Orange
    - Strawberry
    ```
2.  **字典（Dictionary）**：使用`键: 值`的形式表示，键值对共同描述一个对象的属性。
    ```yaml
    name: John Doe
    age: 30
    job: Developer
    ```

在Playbook中，`tasks`本身是一个列表，其中的每个任务项（`- name: ...`）是一个字典。模块参数（如`path: /data/newfile`）也是字典形式。缩进在YAML中至关重要，它定义了数据的层级和从属关系。

---

## 编写一个综合性的Playbook

掌握了基础语法后，让我们动手编写一个更实用的Playbook，它将在目标主机上完成一系列操作。

![](img/f8397c946799ced8cedde1754cc2466d_66.png)

![](img/f8397c946799ced8cedde1754cc2466d_68.png)

![](img/f8397c946799ced8cedde1754cc2466d_70.png)

![](img/f8397c946799ced8cedde1754cc2466d_72.png)

以下Playbook示例演示了如何组合多个任务：
```yaml
---
- hosts: webservers
  remote_user: root
  tasks:
    - name: Create a new empty file
      file:
        path: /data/newfile
        state: touch

    - name: Create a system user
      user:
        name: test2
        system: yes
        shell: /sbin/nologin

    - name: Install the httpd package
      yum:
        name: httpd
        state: present

    - name: Copy website index page
      copy:
        src: files/index.html
        dest: /var/www/html/

    - name: Ensure httpd service is running and enabled
      service:
        name: httpd
        state: started
        enabled: yes
```
这个Playbook依次执行了：创建文件、创建用户、安装软件包、复制网站文件、启动并设置服务开机自启。执行后，一个简单的Web服务就部署完成了。

![](img/f8397c946799ced8cedde1754cc2466d_74.png)

![](img/f8397c946799ced8cedde1754cc2466d_76.png)

![](img/f8397c946799ced8cedde1754cc2466d_78.png)

![](img/f8397c946799ced8cedde1754cc2466d_80.png)

**注意**：`copy`模块中的`src: files/index.html`使用了相对路径。Ansible会在Playbook文件所在目录下寻找名为`files`的子目录，并从其中复制文件。这是一种组织项目文件的良好实践。

![](img/f8397c946799ced8cedde1754cc2466d_82.png)

---

![](img/f8397c946799ced8cedde1754cc2466d_84.png)

![](img/f8397c946799ced8cedde1754cc2466d_86.png)

![](img/f8397c946799ced8cedde1754cc2466d_87.png)

![](img/f8397c946799ced8cedde1754cc2466d_89.png)

## Playbook高级技巧与选项

![](img/f8397c946799ced8cedde1754cc2466d_91.png)

![](img/f8397c946799ced8cedde1754cc2466d_93.png)

![](img/f8397c946799ced8cedde1754cc2466d_95.png)

在实际使用中，你可能会遇到任务失败或需要更精细控制的情况。本节介绍一些有用的高级技巧和命令选项。

![](img/f8397c946799ced8cedde1754cc2466d_97.png)

![](img/f8397c946799ced8cedde1754cc2466d_99.png)

### 错误处理
默认情况下，Playbook中某个任务失败会导致整个Playbook停止。如果你希望即使某个任务失败也继续执行后续任务，可以使用`ignore_errors`。
```yaml
tasks:
  - name: This command might fail
    command: /bin/false
    ignore_errors: yes
```

### 常用命令选项
执行`ansible-playbook`时，可以搭配以下常用选项：
*   `--limit`：限制只在特定主机上执行Playbook。例如，`ansible-playbook site.yml --limit host1`。
*   `--list-hosts`：列出Playbook将会在哪些主机上运行。
*   `--list-tasks`：列出Playbook中定义的所有任务。
*   `-v`, `-vv`, `-vvv`：提供更详细（verbose）的输出信息，用于调试。

![](img/f8397c946799ced8cedde1754cc2466d_101.png)

![](img/f8397c946799ced8cedde1754cc2466d_103.png)

### 配置文件更新与服务重启
一个常见的场景是：更新配置文件后需要重启服务才能生效。简单的复制任务后紧跟启动任务，在配置文件未变更时会导致服务被无意义地重复重启。更优雅的解决方案是使用`handlers`，我们将在后续课程中详细介绍。

![](img/f8397c946799ced8cedde1754cc2466d_105.png)

![](img/f8397c946799ced8cedde1754cc2466d_107.png)

![](img/f8397c946799ced8cedde1754cc2466d_109.png)

---

![](img/f8397c946799ced8cedde1754cc2466d_111.png)

![](img/f8397c946799ced8cedde1754cc2466d_113.png)

![](img/f8397c946799ced8cedde1754cc2466d_115.png)

## 总结
本节课中我们一起学习了Ansible Playbook的企业级用法。我们从使用Ansible Galaxy获取社区角色开始，然后深入学习了Playbook的编写，包括其基于YAML的语法结构。我们动手编写了一个包含多个任务的综合性Playbook，并了解了错误处理、执行限制等高级选项。Playbook是Ansible自动化的核心，通过将任务流程脚本化，可以极大地提升运维工作的效率和一致性。