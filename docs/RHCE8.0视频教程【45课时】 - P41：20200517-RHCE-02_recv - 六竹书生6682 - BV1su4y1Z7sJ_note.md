# Ansible 教程：P41：Playbook 进阶与标签管理

在本节课中，我们将深入学习 Ansible Playbook 的进阶功能，特别是标签（Tags）的使用方法。标签允许我们有选择地执行 Playbook 中的特定任务，这对于调试和运行复杂剧本中的部分代码非常有用。我们还将学习如何检查剧本、列出标签和主机，以及如何限制任务在特定主机上运行。

![](img/198820121462110f123485e51eaf080d_1.png)

上一节我们介绍了 Playbook 的基本结构和 Handlers 等概念，本节中我们来看看如何更精细地控制任务的执行。

## 标签（Tags）的作用与定义

![](img/198820121462110f123485e51eaf080d_3.png)

标签（Tags）是 Ansible Playbook 中的一个核心元素，它允许我们为任务（Task）打上标记。其主要作用是**选择性地运行 Playbook 中的部分代码**。例如，一个 Playbook 包含多个任务，但你可能只想执行其中标记为“配置服务”的任务，而不执行“安装软件包”的任务。

![](img/198820121462110f123485e51eaf080d_5.png)

![](img/198820121462110f123485e51eaf080d_7.png)

![](img/198820121462110f123485e51eaf080d_9.png)

标签的定义语法是在任务中使用 `tags` 关键字。

![](img/198820121462110f123485e51eaf080d_11.png)

**代码示例：**
```yaml
- name: Ping test
  ping:
  tags: ping_test

- name: Restart httpd service
  service:
    name: httpd
    state: restarted
  tags: restart_httpd
```

## 如何使用标签执行任务

![](img/198820121462110f123485e51eaf080d_13.png)

![](img/198820121462110f123485e51eaf080d_15.png)

![](img/198820121462110f123485e51eaf080d_16.png)

![](img/198820121462110f123485e51eaf080d_18.png)

定义了标签后，我们可以在运行 `ansible-playbook` 命令时，通过 `-t` 或 `--tags` 参数来指定要运行的标签。

![](img/198820121462110f123485e51eaf080d_20.png)

![](img/198820121462110f123485e51eaf080d_21.png)

以下是使用标签的几种常见场景：

**1. 执行单个标签的任务**
```bash
ansible-playbook -t restart_httpd my_playbook.yml
```
这条命令只会执行标签为 `restart_httpd` 的任务。

![](img/198820121462110f123485e51eaf080d_23.png)

**2. 执行多个标签的任务**
多个标签之间用逗号分隔。
```bash
ansible-playbook -t ping_test,restart_httpd my_playbook.yml
```

![](img/198820121462110f123485e51eaf080d_25.png)

![](img/198820121462110f123485e51eaf080d_27.png)

**3. 多个任务共享同一个标签**
你可以为多个不同的任务设置相同的标签名。当调用这个标签时，所有匹配的任务都会被执行。
```yaml
- name: Task one
  debug:
    msg: "This is task one"
  tags: common_tag

![](img/198820121462110f123485e51eaf080d_29.png)

- name: Task two
  debug:
    msg: "This is task two"
  tags: common_tag
```
运行 `ansible-playbook -t common_tag my_playbook.yml` 将会执行 `Task one` 和 `Task two`。

![](img/198820121462110f123485e51eaf080d_31.png)

## 查看与管理标签

![](img/198820121462110f123485e51eaf080d_33.png)

在编写复杂的 Playbook 时，可能记不清里面定义了多少标签。Ansible 提供了命令来列出剧本中的所有标签。

![](img/198820121462110f123485e51eaf080d_35.png)

![](img/198820121462110f123485e51eaf080d_37.png)

![](img/198820121462110f123485e51eaf080d_39.png)

**查看剧本中的标签列表：**
```bash
ansible-playbook --list-tags my_playbook.yml
```
这个命令会输出剧本中定义的所有唯一标签名称，帮助你快速了解剧本结构。

![](img/198820121462110f123485e51eaf080d_41.png)

## Playbook 的检查与调试命令

![](img/198820121462110f123485e51eaf080d_43.png)

在正式运行 Playbook 之前，进行语法检查和模拟运行是很好的习惯。Ansible 提供了一系列命令来辅助我们。

![](img/198820121462110f123485e51eaf080d_45.png)

**1. 语法检查与空运行（Dry Run）**
使用 `-C` 或 `--check` 参数可以进行空运行。Ansible 会检查剧本语法并模拟执行过程，但不会在远程主机上实际做出任何更改。
```bash
ansible-playbook -C my_playbook.yml
```

![](img/198820121462110f123485e51eaf080d_47.png)

**2. 列出剧本针对的主机**
使用 `--list-hosts` 参数可以查看剧本将会在哪些主机上执行。
```bash
ansible-playbook --list-hosts my_playbook.yml
```

![](img/198820121462110f123485e51eaf080d_49.png)

![](img/198820121462110f123485e51eaf080d_51.png)

**3. 列出剧本中的所有任务**
使用 `--list-tasks` 参数可以清晰地看到剧本中包含哪些任务，这有助于理解剧本的执行流程。
```bash
ansible-playbook --list-tasks my_playbook.yml
```

![](img/198820121462110f123485e51eaf080d_53.png)

![](img/198820121462110f123485e51eaf080d_55.png)

**4. 限制任务在特定主机上执行**
有时，你可能只想让剧本在某个主机子集上运行。可以使用 `--limit` 参数。
```bash
ansible-playbook my_playbook.yml --limit 192.168.1.100
```
这条命令只会对 IP 为 `192.168.1.100` 的主机执行剧本。

![](img/198820121462110f123485e51eaf080d_57.png)

**5. 显示详细执行过程**
使用 `-v` 参数可以增加输出信息的详细程度，`-vvv` 或 `-vvvv` 会显示更详细的调试信息，这对于排查问题非常有帮助。
```bash
ansible-playbook -vvv my_playbook.yml
```

![](img/198820121462110f123485e51eaf080d_59.png)

![](img/198820121462110f123485e51eaf080d_61.png)

本节课中我们一起学习了 Ansible Playbook 的标签系统及其管理方法。通过使用标签，我们可以灵活、精确地控制任务的执行范围。同时，我们还掌握了一系列用于检查、调试和限制 Playbook 运行的实用命令，如 `--check`、`--list-tags`、`--limit` 等。这些工具能极大地提升我们编写和维护 Ansible 自动化脚本的效率与可靠性。下一节，我们将开始学习 Ansible 中另一个重要概念：变量（Variables）的定义和使用。