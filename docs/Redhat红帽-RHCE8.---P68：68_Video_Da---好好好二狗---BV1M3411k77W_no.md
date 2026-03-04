# Redhat红帽 RHCE8.0认证体系课程：P68：循环任务控制 🔄

在本节课中，我们将要学习 Ansible 剧本中的循环任务控制。通过循环，我们可以高效地批量执行重复性任务，例如同时创建多个用户或安装多个软件包。

---

## 循环的基本概念

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_1.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_2.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_3.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_5.png)

上一节我们介绍了变量，本节中我们来看看如何使用循环来控制任务。循环允许我们在一个任务中迭代处理一个项目列表，从而避免为每个项目编写重复的任务代码。

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_7.png)

在 Ansible 中，我们使用 `loop` 关键字来定义循环。循环体内的每个项目都可以通过特殊的变量 `{{ item }}` 来引用。

以下是循环的基本语法结构：
```yaml
- name: 任务名称
  module_name:
    parameter: "{{ item }}"
  loop:
    - 项目1
    - 项目2
    - 项目3
```

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_9.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_11.png)

---

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_13.png)

## 创建循环剧本示例

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_15.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_17.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_18.png)

接下来，我们通过一个具体的例子来学习如何编写循环剧本。我们将创建一个剧本，用于批量创建用户。

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_20.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_21.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_22.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_23.png)

首先，我们创建一个名为 `loop1.yml` 的剧本文件。

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_24.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_25.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_27.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_29.png)

```yaml
---
- name: 创建多个用户
  hosts: all
  tasks:
    - name: 创建用户
      user:
        name: "{{ item }}"
        state: present
      loop:
        - user130
        - user131
```

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_31.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_33.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_35.png)

在这个剧本中：
*   `hosts: all` 表示在所有目标主机上执行。
*   任务使用 `user` 模块。
*   `name: "{{ item }}"` 表示每次循环时，`item` 的值会被替换为列表中的当前项目（例如 `user130`）。
*   `loop` 关键字后面是一个 YAML 列表，包含了我们要创建的所有用户名。

---

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_37.png)

## 在单个剧本中执行多个循环任务

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_39.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_40.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_42.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_44.png)

一个剧本可以包含多个任务，每个任务都可以有自己的循环。这允许我们在一个剧本中完成多种批量操作。

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_46.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_48.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_49.png)

以下是包含两个循环任务的剧本示例：
```yaml
---
- name: 批量操作示例
  hosts: all
  tasks:
    - name: 创建多个用户
      user:
        name: "{{ item }}"
        state: present
      loop:
        - user130
        - user131

    - name: 安装多个软件包
      yum:
        name: "{{ item }}"
        state: latest
      loop:
        - httpd
        - samba
        - nfs-utils
```

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_50.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_52.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_53.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_54.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_55.png)

在这个剧本中，第一个任务循环创建用户，第二个任务循环安装软件包。两个任务的循环列表是独立的。

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_56.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_57.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_59.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_60.png)

---

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_62.png)

## 使用变量定义循环列表

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_64.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_66.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_67.png)

为了提高剧本的可维护性和灵活性，我们可以将循环列表定义为变量。这样，列表内容可以在剧本开头集中管理，方便修改。

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_69.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_71.png)

以下是使用变量定义循环列表的示例：
```yaml
---
- name: 使用变量进行循环
  hosts: all
  vars:
    user_list:
      - user140
      - user141
    package_list:
      - httpd
      - samba
  tasks:
    - name: 通过变量列表创建用户
      user:
        name: "{{ item }}"
        state: present
      loop: "{{ user_list }}"

    - name: 通过变量列表安装软件包
      yum:
        name: "{{ item }}"
        state: present
      loop: "{{ package_list }}"
```

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_73.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_74.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_75.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_77.png)

在这个剧本中：
*   `vars` 部分定义了两个变量：`user_list` 和 `package_list`。
*   在任务的 `loop` 参数中，我们通过 `"{{ variable_name }}"` 的形式引用了这些变量列表。

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_79.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_81.png)

---

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_83.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_84.png)

## 循环中的字典列表（复杂数据结构）

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_86.png)

当任务需要多个参数时，我们可以循环一个字典列表。字典中的每个键值对可以对应任务模块的一个参数。

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_88.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_89.png)

以下是循环字典列表的示例：
```yaml
---
- name: 使用字典列表进行循环
  hosts: all
  tasks:
    - name: 创建用户并设置Shell
      user:
        name: "{{ item.name }}"
        shell: "{{ item.shell }}"
        state: present
      loop:
        - { name: 'user150', shell: '/bin/bash' }
        - { name: 'user151', shell: '/sbin/nologin' }
```

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_90.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_91.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_93.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_94.png)

在这个剧本中：
*   循环列表中的每个项目都是一个字典。
*   在任务中，我们使用 `{{ item.key }}` 的形式来引用字典中的值，例如 `{{ item.name }}` 和 `{{ item.shell }}`。

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_96.png)

**注意**：当使用预定义的变量字典进行循环时，可能需要使用 `dict2items` 过滤器将字典转换为适合循环的列表格式。这是一个更高级的用法，其基本形式如下：
```yaml
loop: "{{ my_dict | dict2items }}"
```
在循环体内，可以通过 `{{ item.key }}` 和 `{{ item.value }}` 来访问原字典的键和值。

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_98.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_100.png)

---

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_102.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_103.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_105.png)

## 内容总结

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_107.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_109.png)

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_111.png)

本节课中我们一起学习了 Ansible 剧本中循环任务控制的核心知识：
1.  **循环的基本语法**：使用 `loop` 关键字和 `{{ item }}` 变量。
2.  **编写循环剧本**：如何创建任务来批量处理列表中的项目。
3.  **多个循环任务**：在一个剧本中组织多个独立的循环。
4.  **使用变量定义循环列表**：将列表定义为变量，提高剧本的可读性和可维护性。
5.  **循环复杂数据结构**：如何循环字典列表来为任务提供多个参数。

![](img/52c5ef9a0d8d2507a571eddee4ec59c6_113.png)

掌握循环是编写高效、简洁 Ansible 剧本的关键技能，它能极大地简化批量配置和部署工作。