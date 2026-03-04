# Ansible 课程：第38章：Ansible Playbook 介绍

![](img/70a3e97fa25e258d4f22883745b45a39_1.png)

![](img/70a3e97fa25e258d4f22883745b45a39_3.png)

在本节课中，我们将要学习 Ansible Playbook 的核心概念、基本语法和编写方法。Playbook 是 Ansible 用于描述复杂自动化任务的脚本语言，是 Ansible 自动化运维的核心工具。

![](img/70a3e97fa25e258d4f22883745b45a39_5.png)

## Playbook 与临时命令的区别

![](img/70a3e97fa25e258d4f22883745b45a39_7.png)

![](img/70a3e97fa25e258d4f22883745b45a39_9.png)

上一节我们介绍了 Ansible 的临时命令。本节中我们来看看 Playbook 与它的区别。

![](img/70a3e97fa25e258d4f22883745b45a39_11.png)

![](img/70a3e97fa25e258d4f22883745b45a39_13.png)

临时命令（Ad-hoc）适用于执行简单的、一次性的任务。它一次只能调用一个模块，不适合复杂的环境编排和场景管理。

![](img/70a3e97fa25e258d4f22883745b45a39_15.png)

Playbook 则用于配置复杂的程序，控制复杂的应用场景需求，提供灵活的解决方案。

![](img/70a3e97fa25e258d4f22883745b45a39_17.png)

![](img/70a3e97fa25e258d4f22883745b45a39_19.png)

![](img/70a3e97fa25e258d4f22883745b45a39_21.png)

![](img/70a3e97fa25e258d4f22883745b45a39_23.png)

## Playbook 的层次结构

![](img/70a3e97fa25e258d4f22883745b45a39_25.png)

![](img/70a3e97fa25e258d4f22883745b45a39_27.png)

接下来，我们来了解 Playbook 的层次结构。一个 Playbook 由多个层级组成，理解这个结构是编写剧本的基础。

![](img/70a3e97fa25e258d4f22883745b45a39_29.png)

![](img/70a3e97fa25e258d4f22883745b45a39_31.png)

以下是 Playbook 的层次关系：
*   **Playbook**： 一个完整的自动化剧本文件。
*   **Play**： Playbook 中包含的多个“场景”或“段落”。
*   **Task**： 每个 Play 中包含的多个具体“任务”。
*   **Module**： 每个 Task 中调用的 Ansible “模块”，用于执行具体操作。

![](img/70a3e97fa25e258d4f22883745b45a39_33.png)

![](img/70a3e97fa25e258d4f22883745b45a39_35.png)

其结构可以用以下伪代码表示：
```
Playbook
├── Play 1
│   ├── Task 1
│   │   ├── Module 1
│   │   └── Module 2
│   └── Task 2
│       └── Module 3
└── Play 2
    └── Task 3
        └── Module 4
```

![](img/70a3e97fa25e258d4f22883745b45a39_37.png)

![](img/70a3e97fa25e258d4f22883745b45a39_39.png)

![](img/70a3e97fa25e258d4f22883745b45a39_41.png)

## Playbook 的执行特性

![](img/70a3e97fa25e258d4f22883745b45a39_43.png)

了解了结构后，我们来看看 Playbook 的执行规则。

![](img/70a3e97fa25e258d4f22883745b45a39_45.png)

Playbook 以任务为导向，具有“幂等性”。这意味着只要运行一次，它就不会再对目标主机产生重复的变更效果。所有的 Play、Task 和 Module 都按照定义的顺序依次执行。因此，在编排剧本时，必须考虑任务执行的先后顺序。

## YAML 格式介绍

Playbook 使用 YAML 格式编写。它是一种人类可读的数据序列化语言，用于组织 Ansible 的脚本。

YAML 文件是文本文件，扩展名通常为 `.yml` 或 `.yaml`。编写时必须遵循严格的格式规范。

以下是 YAML 格式的核心规则：
*   **缩进**： 使用空格进行缩进，不能使用 Tab 键。相同层级的元素必须有相同的缩进量，子项必须比父项缩进更多。
*   **可读性**： 可以增加空行来分隔不同部分，提高可读性。
*   **文档标记**： 通常以三个破折号 `---` 作为文档开始，以三个点 `...` 作为文档结束（结束标记常可省略）。
*   **列表项**： 列表项以破折号加空格 `- ` 开头。
*   **键值对**： 使用冒号加空格 `: ` 表示键值对（Key-Value）。

![](img/70a3e97fa25e258d4f22883745b45a39_47.png)

![](img/70a3e97fa25e258d4f22883745b45a39_49.png)

![](img/70a3e97fa25e258d4f22883745b45a39_51.png)

一个简单的 YAML 示例如下：
```yaml
---
- red
- green
- blue
...
```
这等价于一个列表：`[‘red’， ‘green’， ‘blue’]`。

![](img/70a3e97fa25e258d4f22883745b45a39_53.png)

![](img/70a3e97fa25e258d4f22883745b45a39_55.png)

![](img/70a3e97fa25e258d4f22883745b45a39_57.png)

## YAML 支持的数据结构

![](img/70a3e97fa25e258d4f22883745b45a39_59.png)

YAML 脚本语言支持特定的数据结构，这是在 Playbook 中组织数据的基础。

YAML 主要支持三种数据结构：字符串、列表和字典。在实际的 Playbook 编写中，通常会混合使用这些结构。

以下是三种数据结构的示例：
1.  **字符串**：
    ```yaml
    ---
    This is a string.
    ...
    ```
2.  **列表**：
    ```yaml
    ---
    - red
    - green
    - blue
    ...
    ```
    （等价于 Python 列表 `[‘red’， ‘green’， ‘blue’]`）
3.  **字典**：
    ```yaml
    ---
    name: zhangsan
    code: d1234
    ...
    ```
    （等价于 Python 字典 `{‘name’: ‘zhangsan’， ‘code’: ‘d1234’}`）

混合使用的复杂示例如下：
```yaml
---
class:
  - name: stu1
    no: 001
  - name: stu2
    no: 002
...
```
这等价于一个 Python 字典中包含一个列表，列表中的每个元素又是一个字典：`{‘class’: [{‘name’: ‘stu1’， ‘no’: ‘001’}， {‘name’: ‘stu2’， ‘no’: ‘002’}]}`。Ansible 引擎会自动将其转换为 Python 数据结构并执行。

## 编写第一个 Playbook

![](img/70a3e97fa25e258d4f22883745b45a39_61.png)

![](img/70a3e97fa25e258d4f22883745b45a39_63.png)

![](img/70a3e97fa25e258d4f22883745b45a39_65.png)

理论介绍完毕，现在我们来动手编写一个完整的 Playbook 脚本。

![](img/70a3e97fa25e258d4f22883745b45a39_67.png)

![](img/70a3e97fa25e258d4f22883745b45a39_69.png)

![](img/70a3e97fa25e258d4f22883745b45a39_71.png)

我们将创建一个简单的 Playbook，它包含一个 Play，该 Play 中有三个 Task，分别用于安装软件包、复制文件和启动服务。

![](img/70a3e97fa25e258d4f22883745b45a39_73.png)

![](img/70a3e97fa25e258d4f22883745b45a39_75.png)

```yaml
---
- name: The first playbook
  hosts: servera
  tasks:
    - name: Install HTTPD server service
      yum:
        name: httpd
        state: present

    - name: Copy a.sh to b
      copy:
        src: /home/devops/a.sh
        dest: /tmp/test12

    - name: Start HTTPD service
      service:
        name: httpd
        enabled: true
        state: started
```

**脚本解析**：
*   `name`： 定义 Play 的名称。
*   `hosts`： 指定目标主机或主机组（如 `servera`， `all`， 或具体 IP）。
*   `tasks`： 任务列表。
    *   每个 Task 都有自己的 `name`（描述）和要调用的`模块`（如 `yum`， `copy`， `service`）。
    *   模块内部是具体的参数（键值对），如 `name: httpd`， `state: present`。

## Playbook 的检查与运行

编写好 Playbook 后，我们需要学习如何检查语法并运行它。

![](img/70a3e97fa25e258d4f22883745b45a39_77.png)

以下是操作 Playbook 的三个常用命令：

![](img/70a3e97fa25e258d4f22883745b45a39_79.png)

1.  **语法检查**： 使用 `--syntax-check` 选项检查 YAML 语法格式是否正确。
    ```bash
    ansible-playbook playbook1.yml --syntax-check
    ```
    （此命令仅检查语法，不检查模块参数等内容是否正确。）

![](img/70a3e97fa25e258d4f22883745b45a39_81.png)

![](img/70a3e97fa25e258d4f22883745b45a39_83.png)

![](img/70a3e97fa25e258d4f22883745b45a39_85.png)

2.  **模拟运行（干跑）**： 使用 `-C` 或 `--check` 选项模拟执行，不会在目标主机上实际做出更改。
    ```bash
    ansible-playbook playbook1.yml -C
    ```

3.  **实际运行**： 直接运行 Playbook 以执行自动化任务。
    ```bash
    ansible-playbook playbook1.yml
    ```
    运行后，输出中**黄色**表示目标主机状态发生了改变，**绿色**表示未改变（符合幂等性）。

## 多 Play 的 Playbook 示例

最后，我们来看一个包含多个 Play 的更复杂示例，这在实际场景中非常常见。

![](img/70a3e97fa25e258d4f22883745b45a39_87.png)

```yaml
---
- name: Multi playbook - Play One
  hosts: servera
  tasks:
    - name: Remove httpd
      yum:
        name: httpd
        state: absent

    - name: Delete a.sh
      file:
        path: /tmp/test12
        state: absent

![](img/70a3e97fa25e258d4f22883745b45a39_89.png)

![](img/70a3e97fa25e258d4f22883745b45a39_91.png)

![](img/70a3e97fa25e258d4f22883745b45a39_93.png)

- name: Multi playbook - Play Two
  hosts: serverb
  tasks:
    - name: Debug message
      debug:
        msg: “This is playbook two”
```

![](img/70a3e97fa25e258d4f22883745b45a39_94.png)

![](img/70a3e97fa25e258d4f22883745b45a39_96.png)

![](img/70a3e97fa25e258d4f22883745b45a39_98.png)

**脚本解析**：
*   这个 Playbook 包含两个独立的 Play。
*   每个 Play 都有自己的 `name`， `hosts` 和 `tasks`。
*   Play 和 Task 都是按顺序从上到下执行的。
*   第二个 Play 使用了 `debug` 模块来打印信息。

![](img/70a3e97fa25e258d4f22883745b45a39_100.png)

![](img/70a3e97fa25e258d4f22883745b45a39_102.png)

## Playbook 的属性说明

![](img/70a3e97fa25e258d4f22883745b45a39_104.png)

在结束之前，我们简要总结一下 Play 级别的常见属性。

![](img/70a3e97fa25e258d4f22883745b45a39_106.png)

*   `name`： Play 的描述性名称。
*   `hosts`： 定义本 Play 要管理的主机或主机组。
*   `tasks`： 定义要执行的任务列表。
*   `become`： 是否提权执行（例如 `become: yes`）。如果已在 Ansible 主配置文件中全局设置，则此处可省略。Playbook 中的设置优先级高于主配置文件。

![](img/70a3e97fa25e258d4f22883745b45a39_108.png)

![](img/70a3e97fa25e258d4f22883745b45a39_110.png)

## 总结

![](img/70a3e97fa25e258d4f22883745b45a39_112.png)

本节课中我们一起学习了 Ansible Playbook 的核心知识。我们了解了 Playbook 的层次结构、YAML 格式的编写规则、支持的数据结构，并动手编写了单 Play 和多 Play 的剧本。同时，我们也掌握了使用 `ansible-playbook` 命令进行语法检查、模拟运行和实际执行的方法。记住，Playbook 编写的难点主要在于对 YAML 格式和缩进的准确把握，而模块的使用可以通过查阅官方文档快速掌握。