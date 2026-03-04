# RHCE8.0视频教程：P46：Jinja2模板进阶与系统信息展示

![](img/1aaa17b1d03ccbae61b69d9e6495341a_1.png)

## 概述
在本节课中，我们将深入学习Jinja2模板在Ansible中的高级应用。我们将通过一个具体案例——在受管主机上配置登录欢迎信息，来掌握如何在配置文件中动态使用系统变量。课程将涵盖从查询系统变量、创建模板文件到编写和执行Playbook的完整流程。

![](img/1aaa17b1d03ccbae61b69d9e6495341a_3.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_5.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_7.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_9.png)

---

![](img/1aaa17b1d03ccbae61b69d9e6495341a_11.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_13.png)

## 回顾上节课内容
上一节我们介绍了Jinja2模板的基本概念，并尝试在Nginx角色中使用变量来动态设置工作进程数。但在操作过程中，由于变量位置不当，导致了一些异常。本节我们将详细讲解如何正确地在配置文件中使用变量。

![](img/1aaa17b1d03ccbae61b69d9e6495341a_15.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_17.png)

## 变量使用的优势
在配置文件中使用变量的核心优势在于其灵活性。例如，如果我们写死一个值（如 `worker_processes 3;`），所有主机都会应用相同的配置。但如果使用变量（如 `worker_processes {{ ansible_processor_vcpus + 2 }};`），Ansible会根据每台主机的实际CPU核心数动态计算并设置，从而实现个性化配置。

![](img/1aaa17b1d03ccbae61b69d9e6495341a_19.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_21.png)

## 实践案例：配置登录欢迎信息
我们的目标是：当用户登录到受管主机时，系统能显示包含该主机CPU核心数和总内存信息的欢迎语。

![](img/1aaa17b1d03ccbae61b69d9e6495341a_23.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_25.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_26.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_27.png)

以下是实现此目标的步骤：

![](img/1aaa17b1d03ccbae61b69d9e6495341a_29.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_31.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_33.png)

### 第一步：确认受管主机清单
我们继续使用之前定义的主机组 `web2012`。可以通过以下命令查看：
```bash
cat /etc/ansible/hosts
```

### 第二步：查询受管主机的系统信息
我们需要获取目标主机的CPU核心数和内存总量。Ansible的 `setup` 模块可以收集这些信息（称为 `facts`）。
```bash
ansible web2012 -m setup
```
输出信息较多，我们可以使用过滤器查找特定变量：
*   CPU核心数变量名：`ansible_processor_vcpus`
*   内存总量变量名：`ansible_memtotal_mb`

![](img/1aaa17b1d03ccbae61b69d9e6495341a_35.png)

### 第三步：创建Jinja2模板文件
模板文件定义了最终配置文件的格式和动态内容。我们在Ansible控制节点上创建它。
1.  创建专用目录并进入：
    ```bash
    mkdir information
    cd information
    ```
2.  创建模板文件 `motd.j2`：
    ```bash
    vim motd.j2
    ```
3.  在文件中写入以下内容：
    ```
    System total memory: {{ ansible_memtotal_mb }} MB
    Processor count: {{ ansible_processor_vcpus }}
    ```
    **说明**：`{{ }}` 是Jinja2的变量插值语法，Ansible执行时会用对应的 `fact` 值替换其中的变量名。

![](img/1aaa17b1d03ccbae61b69d9e6495341a_37.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_39.png)

### 第四步：编写Playbook
Playbook定义了要执行的任务。我们创建一个YAML文件来调用模板模块。
1.  创建Playbook文件 `motd.yml`：
    ```bash
    vim motd.yml
    ```
2.  写入以下内容：
    ```yaml
    ---
    - hosts: web2012
      remote_user: root
      tasks:
        - name: Copy Jinja2 template file
          template:
            src: motd.j2
            dest: /etc/motd
    ```
    **说明**：
    *   `hosts`: 指定在 `web2012` 主机组上执行。
    *   `remote_user`: 以root用户身份执行。
    *   `tasks`: 定义任务列表。
    *   `name`: 任务描述。
    *   `template`: 使用模板模块。
    *   `src`: 源模板文件路径（相对于Playbook位置）。
    *   `dest`: 目标文件路径（受管主机上的 `/etc/motd` 文件用于显示登录后的欢迎信息）。

### 第五步：执行Playbook并验证
1.  首先，检查Playbook语法：
    ```bash
    ansible-playbook motd.yml --syntax-check
    ```
2.  执行Playbook：
    ```bash
    ansible-playbook motd.yml
    ```
3.  验证结果。通过SSH登录到任意一台 `web2012` 受管主机：
    ```bash
    ssh 192.168.127.201
    ```
    登录后，您应该能看到类似如下的欢迎信息：
    ```
    System total memory: 4096 MB
    Processor count: 2
    ```

![](img/1aaa17b1d03ccbae61b69d9e6495341a_41.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_42.png)

### 扩展练习
您还可以使用同样的方法，通过模板修改 `/etc/issue` 或 `/etc/issue.net` 文件，来定制登录前显示的提示信息。

---

![](img/1aaa17b1d03ccbae61b69d9e6495341a_44.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_46.png)

![](img/1aaa17b1d03ccbae61b69d9e6495341a_48.png)

## 总结
本节课我们一起学习了Jinja2模板的进阶应用。我们通过一个配置登录欢迎信息的完整案例，实践了如何查询系统变量、创建包含动态变量的Jinja2模板文件，并编写和执行相应的Playbook。关键在于掌握 `{{ variable_name }}` 的变量引用语法，并确保在模板中使用的变量名与Ansible `facts` 收集的信息名称一致。这种方法极大地增强了配置管理的灵活性和自动化能力。