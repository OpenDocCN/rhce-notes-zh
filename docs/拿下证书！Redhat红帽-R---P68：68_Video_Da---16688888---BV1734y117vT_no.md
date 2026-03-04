# Ansible 认证课程：第五章：循环任务控制

![](img/ce9e070ddba92efd3dca013aee3f9598_0.png)

在本节课中，我们将要学习如何在 Ansible Playbook 中使用循环来控制任务。循环允许我们重复执行一个任务，但每次使用不同的值，例如批量创建用户或安装多个软件包。这极大地提高了 Playbook 的效率和灵活性。

上一节我们介绍了变量的使用，本节中我们来看看如何利用循环来批量处理任务。

## 循环的基本语法

在 Ansible 中，使用 `loop` 关键字来实现循环。`loop` 后面跟着一个列表，列表中的每个元素都会在任务中作为 `{{ item }}` 被引用一次。

![](img/ce9e070ddba92efd3dca013aee3f9598_2.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_3.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_4.png)

以下是一个简单的示例，展示了如何在单个任务中使用循环：

![](img/ce9e070ddba92efd3dca013aee3f9598_6.png)

```yaml
- name: 创建多个用户
  user:
    name: "{{ item }}"
    state: present
  loop:
    - user30
    - user31
```

在这个例子中，`user` 模块会执行两次，分别创建名为 `user30` 和 `user31` 的用户。`{{ item }}` 在每次循环中会被替换为列表中的当前元素。

![](img/ce9e070ddba92efd3dca013aee3f9598_8.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_10.png)

## 在 Playbook 中定义循环

我们可以将循环直接写在 Playbook 的任务中。以下是一个完整的 Playbook 示例，它包含两个使用循环的任务。

![](img/ce9e070ddba92efd3dca013aee3f9598_12.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_14.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_16.png)

```yaml
---
- name: 循环任务示例
  hosts: all
  tasks:
    - name: 创建10个用户
      user:
        name: "{{ item }}"
        state: present
      loop:
        - user30
        - user31
        - user32
        # ... 此处省略其他7个用户

    - name: 安装多个软件包
      yum:
        name: "{{ item }}"
        state: latest
      loop:
        - samba
        - crontabs
        - nfs-utils
```

![](img/ce9e070ddba92efd3dca013aee3f9598_17.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_19.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_20.png)

在这个 Playbook 中：
*   第一个任务循环一个用户列表，创建10个用户。
*   第二个任务循环一个软件包列表，安装三个软件包。
*   每个任务内的 `loop` 关键字是独立的，互不影响。

![](img/ce9e070ddba92efd3dca013aee3f9598_21.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_22.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_24.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_25.png)

## 使用变量定义循环列表

![](img/ce9e070ddba92efd3dca013aee3f9598_27.png)

为了提高代码的可读性和可维护性，我们可以将循环列表定义为变量。变量通常在 Playbook 的 `vars` 部分定义。

以下是使用变量定义循环列表的示例：

![](img/ce9e070ddba92efd3dca013aee3f9598_29.png)

```yaml
---
- name: 使用变量循环
  hosts: all
  vars:
    user_names:
      - user40
      - user41
      - user42
      # ... 其他用户
    package_list:
      - samba
      - crontabs
      - nfs-utils

  tasks:
    - name: 使用变量创建用户
      user:
        name: "{{ item }}"
        state: present
      loop: "{{ user_names }}"

    - name: 使用变量安装软件包
      yum:
        name: "{{ item }}"
        state: present
      loop: "{{ package_list }}"
```

![](img/ce9e070ddba92efd3dca013aee3f9598_30.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_31.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_33.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_35.png)

这种方法将数据（用户列表、软件包列表）与任务逻辑分离，使得 Playbook 结构更清晰，也更容易修改。

![](img/ce9e070ddba92efd3dca013aee3f9598_37.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_39.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_40.png)

## 处理字典列表的循环

![](img/ce9e070ddba92efd3dca013aee3f9598_42.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_43.png)

有时，我们的循环项可能不是简单的字符串，而是包含多个键值对的字典。例如，创建用户时可能需要同时指定用户名和登录 Shell。

![](img/ce9e070ddba92efd3dca013aee3f9598_45.png)

以下是处理字典列表循环的两种方法。

![](img/ce9e070ddba92efd3dca013aee3f9598_47.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_49.png)

**方法一：在 `loop` 中直接定义字典列表**

![](img/ce9e070ddba92efd3dca013aee3f9598_51.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_53.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_55.png)

```yaml
- name: 创建带属性的用户
  user:
    name: "{{ item.name }}"
    shell: "{{ item.shell }}"
    state: present
  loop:
    - name: user50
      shell: /bin/bash
    - name: user51
      shell: /sbin/nologin
```

![](img/ce9e070ddba92efd3dca013aee3f9598_57.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_59.png)

在这个任务中，`{{ item.name }}` 和 `{{ item.shell }}` 分别引用了每个字典元素中的 `name` 和 `shell` 键对应的值。

![](img/ce9e070ddba92efd3dca013aee3f9598_61.png)

**方法二：使用变量和 `dict2items` 过滤器**

如果字典是作为变量定义的，并且我们需要遍历它，可以使用 `dict2items` 过滤器将字典转换为适合 `loop` 的列表项。

![](img/ce9e070ddba92efd3dca013aee3f9598_63.png)

```yaml
---
- name: 使用字典变量循环
  hosts: all
  vars:
    user_info:
      user52: /bin/bash
      user53: /sbin/nologin

  tasks:
    - name: 创建用户 (使用 dict2items)
      user:
        name: "{{ item.key }}"
        shell: "{{ item.value }}"
        state: present
      loop: "{{ user_info | dict2items }}"
```

![](img/ce9e070ddba92efd3dca013aee3f9598_65.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_66.png)

![](img/ce9e070ddba92efd3dca013aee3f9598_68.png)

这里，`user_info` 变量是一个字典。`dict2items` 过滤器将其转换为一个列表，列表中的每个元素都是一个包含 `key` 和 `value` 的小字典。然后在任务中，我们就可以通过 `{{ item.key }}` 和 `{{ item.value }}` 来引用它们。

![](img/ce9e070ddba92efd3dca013aee3f9598_70.png)

## 总结

![](img/ce9e070ddba92efd3dca013aee3f9598_72.png)

本节课中我们一起学习了 Ansible 中循环任务控制的核心概念。

*   **基本循环**：使用 `loop` 关键字和 `{{ item }}` 变量来重复执行任务。
*   **变量化列表**：将循环列表定义为 `vars`，使 Playbook 更清晰。
*   **字典循环**：学习了如何循环遍历字典列表，以及如何使用 `dict2items` 过滤器来处理字典变量。

![](img/ce9e070ddba92efd3dca013aee3f9598_74.png)

循环是编写高效、简洁 Playbook 的强大工具，熟练掌握它对于自动化批量操作至关重要。