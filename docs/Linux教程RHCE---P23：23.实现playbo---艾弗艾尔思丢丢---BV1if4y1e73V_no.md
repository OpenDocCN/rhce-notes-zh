# Linux教程RHCE：P23：实现playbook高级应用和企业级实战

![](img/dfac1091b77567fbf6683ffd1cd1e624_1.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_3.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_5.png)

在本节课中，我们将学习Ansible playbook的高级应用，包括使用handlers实现任务触发、使用tags进行任务选择，以及灵活运用变量来编写更通用、更强大的自动化脚本。

![](img/dfac1091b77567fbf6683ffd1cd1e624_7.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_9.png)

---

![](img/dfac1091b77567fbf6683ffd1cd1e624_11.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_12.png)

## 概述：Playbook的进阶功能

上一节我们介绍了playbook的基本结构和任务执行。本节中，我们来看看如何让playbook变得更智能、更灵活。我们将重点学习三个核心概念：**handlers（触发器）**、**tags（标签）** 和 **variables（变量）**。

---

![](img/dfac1091b77567fbf6683ffd1cd1e624_14.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_16.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_18.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_20.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_21.png)

## Handlers：任务触发器 🎯

![](img/dfac1091b77567fbf6683ffd1cd1e624_23.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_24.png)

在默认情况下，playbook中的任务（tasks）会按照定义的顺序依次执行。但这有时会带来问题。例如，当我们更新了服务的配置文件并复制到目标主机后，如果服务之前已经启动，那么新的配置并不会自动生效，因为服务没有重启。

![](img/dfac1091b77567fbf6683ffd1cd1e624_26.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_28.png)

Handlers就是用来解决这类问题的。它是一个**触发器**，与`tasks`是并列的元素。Handlers可以监控某个任务（task）中的动作（action）。当该动作成功执行并发生变化时，会触发handlers中定义的其他命令。

Handlers需要配合`notify`关键字使用。`notify`写在某个action的后面，表示当这个action的状态发生变化时，就通知（notify）对应的handler去执行。

![](img/dfac1091b77567fbf6683ffd1cd1e624_30.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_32.png)

以下是handler和notify配合使用的核心结构：

![](img/dfac1091b77567fbf6683ffd1cd1e624_34.png)

```yaml
tasks:
  - name: 复制配置文件
    copy:
      src: /path/to/file
      dest: /destination/path
    notify: 重启服务  # 当复制动作成功时，通知名为“重启服务”的handler

handlers:
  - name: 重启服务
    service:
      name: httpd
      state: restarted
```

### 实践示例：配置文件更新后重启服务

![](img/dfac1091b77567fbf6683ffd1cd1e624_36.png)

假设我们有一个安装并配置HTTPD服务的playbook。我们希望当配置文件被更新并复制后，能自动重启HTTPD服务使其生效。

![](img/dfac1091b77567fbf6683ffd1cd1e624_38.png)

1.  **编写基础playbook**：包含安装包、复制配置、启动服务三个任务。
2.  **发现问题**：首次执行后，修改配置文件模板，再次执行playbook。复制配置的任务会执行，但启动服务的任务因为服务已运行而不会执行，导致新配置未生效。
3.  **使用Handler解决**：
    *   在`tasks`中，为“复制配置文件”的action添加`notify`，指向一个handler。
    *   在`handlers`中，定义名为“重启服务”的任务，使用`service`模块重启服务。

![](img/dfac1091b77567fbf6683ffd1cd1e624_40.png)

这样，当配置文件发生变化并被成功复制后，就会触发handler，自动重启服务，使新配置生效。

一个handler可以被多个任务通知，一个任务也可以通知多个handler（使用列表形式）。

---

## Tags：任务标签 🏷️

![](img/dfac1091b77567fbf6683ffd1cd1e624_42.png)

Tags（标签）允许我们为playbook中的单个或多个任务打上标记。它的作用是：**在运行playbook时，可以只执行带有特定标签的任务，而不是运行全部任务**。

![](img/dfac1091b77567fbf6683ffd1cd1e624_44.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_46.png)

这在进行调试、只更新部分服务或分阶段执行时非常有用。

![](img/dfac1091b77567fbf6683ffd1cd1e624_48.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_50.png)

### 如何定义和使用Tags

在任务中，使用`tags`关键字为其添加标签。

```yaml
tasks:
  - name: 安装HTTPD软件包
    yum:
      name: httpd
      state: present
    tags: install_httpd  # 为此任务打上标签

  - name: 启动HTTPD服务
    service:
      name: httpd
      state: started
    tags: start_httpd  # 为此任务打上另一个标签
```

### 如何运行特定标签的任务

![](img/dfac1091b77567fbf6683ffd1cd1e624_52.png)

使用`ansible-playbook`命令的`-t`或`--tags`选项来指定要运行的标签。

![](img/dfac1091b77567fbf6683ffd1cd1e624_54.png)

```bash
# 只运行标签为“start_httpd”的任务
ansible-playbook playbook.yml -t start_httpd

# 运行多个标签的任务
ansible-playbook playbook.yml -t install_httpd,start_httpd
```

### 标签的灵活应用

*   **多个任务共享同一标签**：可以为多个任务定义相同的标签，运行该标签时会执行所有这些任务。
*   **查看标签**：使用`ansible-playbook playbook.yml --list-tags`可以列出playbook中所有定义的标签。

---

## Variables：让Playbook更灵活 🔧

![](img/dfac1091b77567fbf6683ffd1cd1e624_56.png)

在playbook中硬编码值（如软件包名、文件路径、端口号）会降低脚本的灵活性。Variables（变量）允许我们将这些值参数化，从而编写出更通用、可复用的playbook。

![](img/dfac1091b77567fbf6683ffd1cd1e624_58.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_60.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_62.png)

### 变量的定义位置与优先级

变量可以在多个地方定义，Ansible会按照优先级顺序使用它们（优先级高的覆盖优先级低的）。

![](img/dfac1091b77567fbf6683ffd1cd1e624_64.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_66.png)

1.  **命令行传递 (`-e`)**：优先级最高，用于临时覆盖。
    ```bash
    ansible-playbook playbook.yml -e “package_name=vsftpd”
    ```
2.  **在Playbook中定义 (`vars`)**：在playbook文件内部直接声明变量。
    ```yaml
    vars:
      package_name: httpd
      service_port: 80
    tasks:
      - name: 安装软件包
        yum:
          name: “{{ package_name }}”  # 引用变量
    ```
3.  **在主机清单文件中定义**：
    *   **主机变量**：针对单个主机定义，优先级高于组变量。
    *   **组变量**：针对一个主机组中的所有主机定义。
    ```ini
    # 主机变量示例 (在 /etc/ansible/hosts 或独立文件中)
    [webservers]
    192.168.1.101 http_port=81
    192.168.1.102 http_port=82

    # 组变量示例
    [webservers:vars]
    domain_name=mageedu.com
    ```
4.  **内置变量 (`setup`模块)**：Ansible通过`setup`模块自动收集的目标主机信息（如IP地址、主机名、系统架构等），可以直接作为变量使用。
    ```bash
    # 查看目标主机的所有facts信息
    ansible all -m setup
    # 过滤查看IP地址
    ansible all -m setup -a “filter=*ipv4*”
    ```
    在playbook中，可以通过`{{ ansible_facts[‘default_ipv4’][‘address’] }}`等方式引用这些内置变量。

### 变量使用示例：动态设置主机名

假设我们想为`webservers`组中的主机设置主机名，格式为`www[端口号].mageedu.com`，且每台主机的端口号不同。

![](img/dfac1091b77567fbf6683ffd1cd1e624_68.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_70.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_72.png)

1.  **在主机清单中定义变量**：
    ```ini
    [webservers]
    192.168.1.101 http_port=81
    192.168.1.102 http_port=82
    [webservers:vars]
    domain_name=mageedu.com
    ```
2.  **编写Playbook引用变量**：
    ```yaml
    - hosts: webservers
      tasks:
        - name: 设置主机名
          hostname:
            name: “www{{ http_port }}.{{ domain_name }}”  # 组合变量
    ```
3.  **执行Playbook**：每台主机会根据其自身的`http_port`变量值，被设置成不同的主机名（如`www81.mageedu.com`）。

![](img/dfac1091b77567fbf6683ffd1cd1e624_74.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_76.png)

---

## 总结

![](img/dfac1091b77567fbf6683ffd1cd1e624_78.png)

![](img/dfac1091b77567fbf6683ffd1cd1e624_80.png)

本节课中我们一起学习了Ansible playbook的三个高级特性：

1.  **Handlers**：作为任务触发器，与`notify`配合，能在某个任务状态改变后自动执行后续操作（如重启服务），解决了配置更新后需手动干预的问题。
2.  **Tags**：为任务打上标签，允许我们灵活地选择执行playbook中的特定部分，提高了脚本的可维护性和执行效率。
3.  **Variables**：通过变量将playbook参数化，我们可以在命令行、playbook内部或主机清单中定义和覆盖变量，并利用内置的facts信息，编写出适应不同主机和环境的通用型自动化脚本。

![](img/dfac1091b77567fbf6683ffd1cd1e624_82.png)

掌握这些高级应用，是编写企业级、可维护的Ansible自动化脚本的关键。请务必多加练习，尝试将这些功能组合运用到你自己的场景中。