# Ansible教程：01：Playbook核心元素详解

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_1.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_2.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_4.png)

在本节课中，我们将深入学习Ansible Playbook的核心构成元素。上一节我们初步接触了Playbook的概念，本节我们将详细解析Playbook文件中的关键组成部分，包括主机清单的指定、任务的定义与执行顺序等。

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_6.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_8.png)

## 主机清单（hosts）

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_10.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_12.png)

Playbook中的`hosts`元素用于指定任务将在哪些远程主机上执行。它需要与Ansible的主机清单文件（`/etc/ansible/hosts`）中定义的主机或组名相匹配。如果主机未在清单中定义，则Ansible无法管理该主机。

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_14.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_16.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_18.png)

以下是主机清单的几种常见定义形式：

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_20.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_22.png)

1.  **单台主机名**：直接使用主机名，例如 `www.xtt.com`。
2.  **多台主机**：分行列出多台主机。
3.  **IP地址**：直接使用IP地址，例如 `192.168.127.200`。
4.  **IP地址段**：使用通配符指定一个网段，例如 `192.168.127.*`。
5.  **主机组并集**：使用 `:` 连接两个组名，表示这两个组的所有主机。例如 `group1:group2` 表示属于group1或group2的主机。
6.  **主机组交集**：使用 `:&` 连接两个组名，表示同时属于这两个组的主机。例如 `group1:&group2` 表示既在group1中又在group2中的主机。
7.  **主机组差集**：使用 `:!` 连接两个组名，表示在第一个组但不在第二个组中的主机。例如 `group1:!group2` 表示在group1中但不在group2中的主机。

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_24.png)

## 任务列表（tasks）

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_26.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_28.png)

`tasks`是Playbook的主体，它定义了一系列需要按顺序执行的任务。每个任务都是一个独立的操作单元。

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_30.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_32.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_34.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_35.png)

以下是关于任务执行顺序的重要规则：

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_37.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_39.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_41.png)

*   任务默认**从上到下依次执行**。
*   如果某个任务执行失败，Ansible会尝试**回滚**该任务已做的操作。
*   任务执行失败后，**后续的任务将不会被执行**。
*   当修正Playbook中的错误后，重新运行Playbook，它会**再次从上到下完整执行**。

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_43.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_45.png)

为了演示任务的执行流程，我们可以创建一个简单的Playbook文件。例如，创建一个名为 `test_demo.yml` 的文件：

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_47.png)

```yaml
---
- hosts: web2012  # 指定在web2012主机组上执行
  remote_user: root  # 以root用户身份执行
  tasks:
    - name: 任务一：创建文件 /tmp/file1
      command: touch /tmp/file1

    - name: 任务二：创建一个会失败的命令（例如，向不存在的目录写文件）
      command: echo "test" > /nonexistent_dir/test.txt
      ignore_errors: yes  # 即使此任务失败，也继续执行下一个任务

    - name: 任务三：创建文件 /tmp/file3
      command: touch /tmp/file3
```

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_49.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_51.png)

在这个例子中，我们使用了 `ignore_errors: yes` 来让Playbook在任务二失败后继续执行任务三，这有助于我们理解任务间的依赖和控制流。

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_53.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_54.png)

![](img/d582bfc7aa1120a6ecea8e7c5e93263a_56.png)

本节课中我们一起学习了Playbook的两个核心元素：`hosts`和`tasks`。我们了解了如何精确指定任务执行的目标主机，以及任务列表的执行逻辑和错误处理机制。掌握这些是编写有效、可靠自动化脚本的基础。