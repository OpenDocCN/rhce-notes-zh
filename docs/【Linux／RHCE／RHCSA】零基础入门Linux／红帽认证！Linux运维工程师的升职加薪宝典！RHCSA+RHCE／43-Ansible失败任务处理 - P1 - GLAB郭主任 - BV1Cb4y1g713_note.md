# Linux运维与红帽认证：43：Ansible失败任务处理 🛠️

在本节课中，我们将学习Ansible流程控制中的核心概念——如何处理任务执行中的失败。我们将通过分析几个具体的Playbook示例，来理解如何忽略错误、使用条件判断以及通过`block`、`rescue`、`always`等结构来控制任务流程。掌握这些技巧，能让你编写的自动化脚本更加健壮和灵活。

## 概述：错误处理的重要性

在自动化运维中，任务执行难免会遇到错误。默认情况下，Ansible遇到任务失败会停止执行后续任务。但通过特定的错误处理机制，我们可以控制脚本的行为，例如忽略非关键错误、在失败时执行补救措施或无论成功失败都执行清理任务。

![](img/980a17cf01ec4fbfe63ce5c629574795_1.png)

上一节我们介绍了Ansible的基础任务编写，本节中我们来看看如何优雅地处理这些任务可能出现的失败情况。

## 实验一：基础任务失败与停止

![](img/980a17cf01ec4fbfe63ce5c629574795_3.png)

我们首先来看一个最简单的Playbook示例，它演示了默认的失败处理行为。

![](img/980a17cf01ec4fbfe63ce5c629574795_5.png)

![](img/980a17cf01ec4fbfe63ce5c629574795_7.png)

**Playbook 1 内容：**
```yaml
---
- name: Install packages
  hosts: all
  vars:
    web_package: http
    db_package: mariadb-server
  tasks:
    - name: Install web package
      yum:
        name: "{{ web_package }}"
        state: present
    - name: Install db package
      yum:
        name: "{{ db_package }}"
        state: present
```

![](img/980a17cf01ec4fbfe63ce5c629574795_9.png)

执行这个Playbook时，第一个任务会失败，因为Red Hat系统中Apache软件包的正确名称是`httpd`，而非`http`。由于第一个任务失败，第二个安装`mariadb-server`的任务将不会执行。

这个例子说明了默认行为：**一个任务失败会导致整个Play后续任务终止**。

![](img/980a17cf01ec4fbfe63ce5c629574795_11.png)

![](img/980a17cf01ec4fbfe63ce5c629574795_13.png)

## 实验二：使用 `ignore_errors` 忽略错误

![](img/980a17cf01ec4fbfe63ce5c629574795_15.png)

为了让Play在遇到非致命错误时能继续执行，我们可以使用 `ignore_errors` 参数。

![](img/980a17cf01ec4fbfe63ce5c629574795_17.png)

以下是第二个Playbook的内容，它在上一个例子的基础上增加了 `ignore_errors`。

![](img/980a17cf01ec4fbfe63ce5c629574795_19.png)

![](img/980a17cf01ec4fbfe63ce5c629574795_21.png)

**Playbook 2 内容：**
```yaml
---
- name: Install packages with ignore
  hosts: all
  vars:
    web_package: http
    db_package: mariadb-server
  tasks:
    - name: Install web package
      yum:
        name: "{{ web_package }}"
        state: present
      ignore_errors: yes
    - name: Install db package
      yum:
        name: "{{ db_package }}"
        state: present
```

![](img/980a17cf01ec4fbfe63ce5c629574795_23.png)

![](img/980a17cf01ec4fbfe63ce5c629574795_25.png)

执行这个Playbook，第一个任务虽然失败，但被标记为忽略。因此，Play会继续执行第二个任务并成功安装`mariadb-server`。

`ignore_errors: yes` 的作用是**忽略当前任务的错误，让Play可以继续向下执行**。这适用于那些失败不影响整体流程的次要任务。

![](img/980a17cf01ec4fbfe63ce5c629574795_27.png)

![](img/980a17cf01ec4fbfe63ce5c629574795_29.png)

## 实验三：使用 `block`， `rescue` 和 `always`

![](img/980a17cf01ec4fbfe63ce5c629574795_31.png)

对于更复杂的错误处理逻辑，Ansible提供了 `block`、`rescue` 和 `always` 语句块。

![](img/980a17cf01ec4fbfe63ce5c629574795_33.png)

![](img/980a17cf01ec4fbfe63ce5c629574795_35.png)

以下是第三个Playbook的内容，它展示了这三个关键字的用法。

**Playbook 3 内容：**
```yaml
---
- name: Demonstrate block, rescue, always
  hosts: all
  vars:
    web_package: http
    db_package: mariadb-server
  tasks:
    - block:
        - name: Install web package
          yum:
            name: "{{ web_package }}"
            state: present
      rescue:
        - name: Install db package (fallback)
          yum:
            name: "{{ db_package }}"
            state: present
      always:
        - name: Always start httpd service
          service:
            name: httpd
            state: started
```

![](img/980a17cf01ec4fbfe63ce5c629574795_37.png)

这三个部分的逻辑关系如下：
*   **`block`**: 定义要执行的主要任务。
*   **`rescue`**: 只有当 `block` 中的**任一任务失败**时才会执行。
*   **`always`**: **无论 `block` 成功还是失败，都会执行**。

在这个例子中，因为 `web_package` 变量值是错误的 `http`，所以 `block` 失败。执行流程将是：执行 `block`（失败）→ 跳过剩余 `block` 任务 → 执行 `rescue` → 执行 `always`。

如果我们将变量改为正确的 `httpd`，那么执行流程将是：执行 `block`（成功）→ 跳过 `rescue` → 执行 `always`。

![](img/980a17cf01ec4fbfe63ce5c629574795_39.png)

![](img/980a17cf01ec4fbfe63ce5c629574795_41.png)

## 实验四：使用 `changed_when` 和 `failed_when`

![](img/980a17cf01ec4fbfe63ce5c629574795_43.png)

![](img/980a17cf01ec4fbfe63ce5c629574795_45.png)

有时，Ansible对任务是否“已更改”或“已失败”的默认判断不符合我们的需求。这时可以使用 `changed_when` 和 `failed_when` 进行手动控制。

![](img/980a17cf01ec4fbfe63ce5c629574795_47.png)

![](img/980a17cf01ec4fbfe63ce5c629574795_49.png)

![](img/980a17cf01ec4fbfe63ce5c629574795_51.png)

以下是第五个Playbook的内容，它演示了这两个参数的用法。

![](img/980a17cf01ec4fbfe63ce5c629574795_53.png)

**Playbook 5 内容（节选）：**
```yaml
---
- name: Demonstrate changed_when and failed_when
  hosts: all
  vars:
    web_package: httpd
  tasks:
    - name: Get current date
      command: date
      register: date_result
      changed_when: false
    - name: Debug date
      debug:
        var: date_result.stdout
    - name: Install web package
      yum:
        name: "{{ web_package }}"
        state: present
      failed_when: web_package == \"httpd\"
```

*   **`changed_when: false`**: `command` 模块默认总是报告“已更改”。通过此设置，我们强制将其结果标记为“未更改”，在输出中显示为绿色。这只是一个**状态标记**，不影响命令的实际执行。
*   **`failed_when: web_package == \"httpd\"`**: 这个条件判断变量值，并**标记**任务为失败。但请注意，它**仅标记失败状态**，`yum`模块安装`httpd`的操作实际上会成功执行。这个标记可以用于后续的条件判断或通知处理。

![](img/980a17cf01ec4fbfe63ce5c629574795_55.png)

**核心结论**：`changed_when` 和 `failed_when` 主要用于**影响Ansible对任务结果的判断和报告**，而不是直接决定任务能否执行成功。

## 综合练习解析

![](img/980a17cf01ec4fbfe63ce5c629574795_57.png)

最后，我们通过一个综合练习来巩固以上概念。这个练习要求实现一个Web服务器部署脚本，包含条件检查、软件安装、文件配置、服务启动和错误处理。

练习的关键点包括：
1.  **条件检查 (`fail`模块)**: 使用 `fail` 模块检查受控节点是否满足最低要求（如内存大小、操作系统版本）。
2.  **变量文件**: 将软件包列表、文件路径等变量定义在独立的YAML文件中，使Playbook更清晰。
3.  **循环 (`with_items`)**: 使用循环来遍历变量列表，批量安装软件包、复制配置文件，避免代码重复。
4.  **处理程序 (`notify` & `handlers`)**: 当配置文件被修改后，通过 `notify` 触发 `handler` 来重启服务。
5.  **错误处理 (`block` & `rescue`)**: 将关键配置任务放在 `block` 中。如果其中任何任务失败，则执行 `rescue` 块中的任务（例如发送告警信息）。`always` 块可用于执行最终的防火墙规则配置等收尾工作。

这个练习几乎用到了本节所有知识点，是编写生产级Ansible Playbook的很好范例。

![](img/980a17cf01ec4fbfe63ce5c629574795_59.png)

![](img/980a17cf01ec4fbfe63ce5c629574795_61.png)

## 总结

本节课中我们一起学习了Ansible中处理任务失败和流程控制的核心方法。

我们首先看到了默认的失败行为，然后学习了如何用 `ignore_errors` 忽略非关键错误。接着，我们深入探讨了 `block`、`rescue`、`always` 这一强大的错误处理结构，它可以实现“尝试-捕获-最终”的逻辑。最后，我们了解了 `changed_when` 和 `failed_when` 这两个用于精细控制任务状态报告的参数。

![](img/980a17cf01ec4fbfe63ce5c629574795_63.png)

此外，我们还回顾了与之紧密相关的**循环**、**变量**（包括事实变量和自定义变量）以及**条件判断 (`when`)** 的用法。将这些元素与错误处理机制结合，你就能编写出健壮、灵活、可维护的自动化运维脚本，从容应对各种复杂的部署场景。