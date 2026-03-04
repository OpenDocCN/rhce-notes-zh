# RHCE8.0视频教程：P40：Playbook核心元素详解

在本节课中，我们将深入学习Ansible Playbook的核心元素。上一节我们介绍了Playbook的基本概念和结构，本节中我们将详细解析构成Playbook的关键组成部分，包括主机清单、任务定义及其执行逻辑。

![](img/ea222c4f723541f6704fedbd14446dfa_1.png)

![](img/ea222c4f723541f6704fedbd14446dfa_2.png)

## 主机清单（hosts）详解

![](img/ea222c4f723541f6704fedbd14446dfa_4.png)

Playbook中的`hosts`元素用于指定任务将在哪些远程主机上执行。它必须与Ansible的主机清单文件（`/etc/ansible/hosts`）中定义的主机或组相匹配。如果主机未在清单中定义，则Ansible无法管理它。

![](img/ea222c4f723541f6704fedbd14446dfa_6.png)

![](img/ea222c4f723541f6704fedbd14446dfa_8.png)

以下是主机清单的几种常见定义形式：

![](img/ea222c4f723541f6704fedbd14446dfa_10.png)

![](img/ea222c4f723541f6704fedbd14446dfa_12.png)

1.  **单台主机名**：直接使用主机名。
    ```
    www.xtt.com
    ```

![](img/ea222c4f723541f6704fedbd14446dfa_14.png)

![](img/ea222c4f723541f6704fedbd14446dfa_16.png)

2.  **多台主机**：逐行列出。
    ```
    www1.xtt.com
    www2.xtt.com
    ```

![](img/ea222c4f723541f6704fedbd14446dfa_18.png)

3.  **IP地址**：直接使用IP地址。
    ```
    192.168.127.200
    ```

![](img/ea222c4f723541f6704fedbd14446dfa_20.png)

![](img/ea222c4f723541f6704fedbd14446dfa_22.png)

4.  **IP地址段**：使用通配符匹配一个网段。
    ```
    192.168.127.*
    ```

![](img/ea222c4f723541f6704fedbd14446dfa_24.png)

5.  **主机组**：使用在清单文件中定义的组名，如 `web_servers`。

6.  **组的并集**：使用冒号连接多个组，表示这些组中所有主机的集合。
    ```
    group1:group2
    ```
    *示例*：若group1包含主机1,2,3，group2包含主机3,4,5，则 `group1:group2` 表示主机1,2,3,4,5。

7.  **组的交集**：使用 `&` 符号连接多个组，表示同时属于这些组的主机。
    ```
    group1:&group2
    ```
    *示例*：沿用上例，`group1:&group2` 表示主机3。

![](img/ea222c4f723541f6704fedbd14446dfa_26.png)

![](img/ea222c4f723541f6704fedbd14446dfa_28.png)

![](img/ea222c4f723541f6704fedbd14446dfa_30.png)

8.  **组的差集**：使用 `!` 符号，表示在第一个组中但不在后续组中的主机。
    ```
    group1:!group2
    ```
    *示例*：沿用上例，`group1:!group2` 表示主机1和2。

![](img/ea222c4f723541f6704fedbd14446dfa_32.png)

![](img/ea222c4f723541f6704fedbd14446dfa_34.png)

![](img/ea222c4f723541f6704fedbd14446dfa_35.png)

## 任务（tasks）详解

![](img/ea222c4f723541f6704fedbd14446dfa_37.png)

![](img/ea222c4f723541f6704fedbd14446dfa_39.png)

`tasks` 是Playbook的主体，它定义了一系列需要按顺序执行的任务。每个任务都是一个独立的操作单元。

![](img/ea222c4f723541f6704fedbd14446dfa_41.png)

![](img/ea222c4f723541f6704fedbd14446dfa_43.png)

任务执行遵循以下核心规则：

![](img/ea222c4f723541f6704fedbd14446dfa_45.png)

*   **顺序执行**：任务默认从上到下依次执行。只有前一个任务成功完成后，才会执行下一个任务。
*   **错误处理**：如果某个任务执行失败，Ansible会尝试回滚该任务已做的操作（如果模块支持），并且后续任务将不会被执行。
*   **错误修正**：当您修正了导致Playbook运行错误的代码后，重新运行Playbook，它会再次从头开始执行所有任务。

为了更直观地理解，我们可以创建一个简单的Playbook进行演示。

![](img/ea222c4f723541f6704fedbd14446dfa_47.png)

以下是一个示例Playbook文件 `test_demo.yml` 的内容，它定义了三个连续的任务：

![](img/ea222c4f723541f6704fedbd14446dfa_49.png)

![](img/ea222c4f723541f6704fedbd14446dfa_51.png)

```yaml
---
- hosts: web_2012          # 对 web_2012 主机组执行任务
  remote_user: root        # 以 root 用户身份执行

  tasks:
    - name: Task 1 - Create a test file
      command: touch /tmp/test_file_1
      # 任务1：在远端创建一个测试文件

    - name: Task 2 - Create another test file (This will fail)
      command: some_nonexistent_command
      # 任务2：执行一个不存在的命令，此任务会失败

    - name: Task 3 - Create a third test file
      command: touch /tmp/test_file_3
      # 任务3：创建第三个测试文件
```

**执行逻辑说明**：
1.  Playbook首先在 `web_2012` 组的所有主机上运行。
2.  **Task 1** 会成功执行，在目标主机上创建 `/tmp/test_file_1`。
3.  **Task 2** 由于命令不存在，执行会失败。Ansible会停止执行，**Task 3** 将不会运行。
4.  在您修正了Task 2的错误（例如，将命令改为有效的命令）后，重新运行此Playbook，Ansible会再次从Task 1开始执行所有三个任务。

![](img/ea222c4f723541f6704fedbd14446dfa_53.png)

![](img/ea222c4f723541f6704fedbd14446dfa_54.png)

![](img/ea222c4f723541f6704fedbd14446dfa_56.png)

本节课中我们一起学习了Playbook的两个核心元素：`hosts` 和 `tasks`。我们详细探讨了主机清单的多种灵活定义方式，并深入理解了任务列表的顺序执行、错误处理与回滚机制。掌握这些是编写有效、可靠自动化剧本的基础。在接下来的课程中，我们将学习更多模块和高级Playbook编写技巧。