# Ansible教程：02：Playbook进阶与变量基础

在本节课中，我们将深入学习Ansible Playbook的进阶功能，包括标签（Tag）的使用、剧本的检查与调试命令，并初步了解变量的概念。这些知识将帮助你更精细地控制任务执行，并提升剧本的灵活性和可维护性。

## 标签（Tag）的作用与使用

![](img/bcdd175fea50364a75220552cb5aafe8_1.png)

上一节我们介绍了Playbook的基本结构，本节中我们来看看如何使用标签（Tag）来选择性执行任务。标签允许你为Playbook中的任务打上标记，以便在运行时只执行带有特定标签的任务，而不是运行整个剧本。

标签的核心作用是**选择性地运行Playbook中的部分代码**。你可以将其理解为给代码块起一个名字，执行时只调用这个名字对应的部分。

以下是定义标签的示例代码：

![](img/bcdd175fea50364a75220552cb5aafe8_3.png)

![](img/bcdd175fea50364a75220552cb5aafe8_5.png)

```yaml
---
- hosts: web2012
  remote_user: root
  tasks:
    - name: Ping test
      ping:
      tags: host_ping

    - name: Restart httpd service
      service:
        name: httpd
        state: restarted
      tags: restart_httpd
```

![](img/bcdd175fea50364a75220552cb5aafe8_7.png)

![](img/bcdd175fea50364a75220552cb5aafe8_9.png)

在这个剧本中，我们定义了两个任务，并分别给它们打上了 `host_ping` 和 `restart_httpd` 标签。

![](img/bcdd175fea50364a75220552cb5aafe8_11.png)

## 如何执行特定标签的任务

定义了标签后，你可以使用 `ansible-playbook` 命令的 `-t` 或 `--tags` 参数来指定要运行的标签。

以下是执行特定标签任务的命令：

```bash
ansible-playbook -t restart_httpd your_playbook.yml
```

![](img/bcdd175fea50364a75220552cb5aafe8_13.png)

![](img/bcdd175fea50364a75220552cb5aafe8_15.png)

![](img/bcdd175fea50364a75220552cb5aafe8_16.png)

这条命令只会执行标签为 `restart_httpd` 的任务（即重启HTTP服务），而不会执行 `host_ping` 任务。

![](img/bcdd175fea50364a75220552cb5aafe8_18.png)

![](img/bcdd175fea50364a75220552cb5aafe8_20.png)

如果你想一次性执行多个标签的任务，可以用逗号分隔标签名：

![](img/bcdd175fea50364a75220552cb5aafe8_21.png)

```bash
ansible-playbook -t host_ping,restart_httpd your_playbook.yml
```

## 标签的两种设置方式

![](img/bcdd175fea50364a75220552cb5aafe8_23.png)

标签的设置主要有两种方式，理解它们对灵活控制任务至关重要。

![](img/bcdd175fea50364a75220552cb5aafe8_25.png)

1.  **每个任务使用独立标签**：如上例所示，每个任务有自己独特的标签，便于精确调用。
2.  **多个任务共享同一标签**：你可以为多个任务设置相同的标签名。当调用这个标签时，所有匹配到的任务都会被执行。

![](img/bcdd175fea50364a75220552cb5aafe8_27.png)

![](img/bcdd175fea50364a75220552cb5aafe8_29.png)

以下是共享标签的示例：

```yaml
tasks:
  - name: Ping test
    ping:
    tags: common_tag

  - name: Restart httpd service
    service:
      name: httpd
      state: restarted
    tags: common_tag
```

![](img/bcdd175fea50364a75220552cb5aafe8_31.png)

执行 `ansible-playbook -t common_tag your_playbook.yml` 时，上述两个任务都会运行。

![](img/bcdd175fea50364a75220552cb5aafe8_33.png)

## 查看剧本中的标签

![](img/bcdd175fea50364a75220552cb5aafe8_35.png)

当剧本文件变得复杂时，手动查找所有标签可能很麻烦。Ansible提供了快速查看剧本中所有标签的命令。

![](img/bcdd175fea50364a75220552cb5aafe8_37.png)

![](img/bcdd175fea50364a75220552cb5aafe8_39.png)

以下是查看剧本标签的命令：

![](img/bcdd175fea50364a75220552cb5aafe8_41.png)

```bash
ansible-playbook --list-tags your_playbook.yml
```

![](img/bcdd175fea50364a75220552cb5aafe8_43.png)

这条命令会列出剧本中定义的所有唯一标签，方便你管理和调用。

## Playbook的检查与调试命令

![](img/bcdd175fea50364a75220552cb5aafe8_45.png)

在运行Playbook之前，进行检查和调试是很好的习惯，可以避免错误影响到实际环境。Ansible提供了一系列有用的命令。

![](img/bcdd175fea50364a75220552cb5aafe8_47.png)

以下是几个常用的Playbook检查与信息查询命令：

![](img/bcdd175fea50364a75220552cb5aafe8_49.png)

![](img/bcdd175fea50364a75220552cb5aafe8_51.png)

*   **语法检查与空运行**：使用 `-C` 或 `--check` 参数可以进行“空运行”（Dry Run）。Ansible会模拟执行任务并检查语法，但不会在远程主机上实际执行任何更改。
    ```bash
    ansible-playbook -C your_playbook.yml
    ```
*   **列出剧本针对的主机**：使用 `--list-hosts` 参数可以查看剧本将会在哪些主机上执行。
    ```bash
    ansible-playbook --list-hosts your_playbook.yml
    ```
*   **列出剧本中的所有任务**：使用 `--list-tasks` 参数可以清晰地看到剧本中包含哪些任务，这有助于理解剧本结构。
    ```bash
    ansible-playbook --list-tasks your_playbook.yml
    ```
*   **限制执行的主机**：使用 `--limit` 参数可以限制剧本只在特定的主机或主机组上运行，这在调试或针对部分主机进行操作时非常有用。
    ```bash
    ansible-playbook your_playbook.yml --limit 192.168.127.202
    ```
*   **显示详细执行过程**：使用 `-v`、`-vv`、`-vvv` 或 `-vvvv` 参数可以输出更详细的执行日志，`v` 越多，信息越详细，便于排查问题。
    ```bash
    ansible-playbook -vv your_playbook.yml
    ```

![](img/bcdd175fea50364a75220552cb5aafe8_53.png)

## 变量（Variable）简介

![](img/bcdd175fea50364a75220552cb5aafe8_55.png)

![](img/bcdd175fea50364a75220552cb5aafe8_57.png)

在掌握了Playbook的进阶控制后，我们将目光转向另一个核心概念——**变量**。变量让Playbook变得动态和可复用。例如，你可以将服务名、端口号、文件路径等信息定义为变量，从而轻松适配不同的环境或主机。

![](img/bcdd175fea50364a75220552cb5aafe8_59.png)

变量的定义和使用有多种方式，我们将在接下来的课程中详细探讨。简单来说，你可以：
1.  在Playbook中直接定义变量。
2.  在独立的变量文件中定义。
3.  使用Ansible收集的“事实”（Facts）作为变量。
4.  通过命令行传递变量。

![](img/bcdd175fea50364a75220552cb5aafe8_61.png)

本节课中我们一起学习了Ansible Playbook的标签功能，它让我们能够灵活选择执行部分任务；同时也掌握了检查、调试Playbook以及获取其信息的各种命令。最后，我们引出了变量的概念，它是构建动态、可配置自动化剧本的基石。在下一节，我们将深入探讨变量的定义和使用方法。