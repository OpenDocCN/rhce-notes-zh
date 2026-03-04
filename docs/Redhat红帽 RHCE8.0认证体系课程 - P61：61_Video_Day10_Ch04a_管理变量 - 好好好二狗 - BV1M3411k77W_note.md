# Redhat红帽 RHCE8.0认证体系课程：P61：管理变量 📚

![](img/5c9dc3105b7451e8bcca3f53c06545fd_1.png)

在本节课中，我们将要学习Ansible中的变量管理。变量是Ansible剧本中存储和引用数据的关键，理解不同类型的变量及其用法，对于编写高效、灵活的自动化剧本至关重要。我们将介绍常规变量、数组、字典以及用于存储任务结果的`register`变量。

## 常规变量 🔧

![](img/5c9dc3105b7451e8bcca3f53c06545fd_3.png)

上一节我们介绍了变量的基本概念，本节中我们来看看最基础的变量类型——常规变量。

![](img/5c9dc3105b7451e8bcca3f53c06545fd_4.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_6.png)

常规变量用于存储单个值，其定义和取值方式有两种：
*   `key=value`
*   `key: value`

![](img/5c9dc3105b7451e8bcca3f53c06545fd_8.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_10.png)

**公式/代码示例**：
```yaml
# 方式一：使用等号
my_variable = "some_value"

# 方式二：使用冒号（在Playbook中更常见）
my_variable: "some_value"
```

需要注意的是，定义方式需要根据使用场景选择。例如，在资产清单文件中，使用冒号定义变量会导致错误，必须使用等号。

## 数组变量 📋

当我们需要定义一系列同类型的变量时，可以使用数组。例如，一批用户名或软件包名。

![](img/5c9dc3105b7451e8bcca3f53c06545fd_12.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_14.png)

以下是数组变量的定义和使用方法。

![](img/5c9dc3105b7451e8bcca3f53c06545fd_15.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_16.png)

**代码示例**：
```yaml
---
- name: 演示数组变量
  hosts: servers
  vars:
    # 定义一个名为user_names的数组
    user_names:
      - test_user01
      - test_user02
      - test_user03
      - test_user04

  tasks:
    - name: 创建用户（调用数组中的单个值）
      user:
        name: "{{ user_names[0] }}"  # 数组索引从0开始，此处引用'test_user01'
        state: present
```

![](img/5c9dc3105b7451e8bcca3f53c06545fd_18.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_20.png)

数组的索引从0开始。调用时，只能引用数组中的单个元素值，不能直接使用整个数组名作为某些模块（如`user`）的参数，否则会报错。

![](img/5c9dc3105b7451e8bcca3f53c06545fd_22.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_24.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_26.png)

## 字典变量 📖

![](img/5c9dc3105b7451e8bcca3f53c06545fd_28.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_29.png)

当变量的信息包含多种不同类型、不同含义的元素时，适合使用字典。例如，一个用户的信息可能包含用户名、Shell类型、UID和注释等。

![](img/5c9dc3105b7451e8bcca3f53c06545fd_31.png)

以下是字典变量的定义和两种引用方式。

![](img/5c9dc3105b7451e8bcca3f53c06545fd_32.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_34.png)

**代码示例**：
```yaml
---
- name: 演示字典变量
  hosts: servers
  vars:
    # 定义一个名为user_info的字典变量
    user_info:
      test_user10:  # 第一个字典项
        name: test_user10
        shell: /sbin/nologin
        comment: test user10
      test_user11:  # 第二个字典项
        name: test_user11
        shell: /bin/bash
        comment: test user11

  tasks:
    - name: 使用字典创建用户 - 方式一（括号引用）
      user:
        name: "{{ user_info['test_user10']['name'] }}"
        shell: "{{ user_info['test_user10']['shell'] }}"
        state: present

    - name: 使用字典创建用户 - 方式二（点号引用）
      user:
        name: "{{ user_info.test_user11.name }}"
        shell: "{{ user_info.test_user11.shell }}"
        state: present
```

![](img/5c9dc3105b7451e8bcca3f53c06545fd_36.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_38.png)

字典提供了层级化的数据存储。引用时有两种主要方式：
1.  使用方括号和引号：`{{ 字典名['键名']['子键名'] }}`
2.  使用点号：`{{ 字典名.键名.子键名 }}`（注意：如果键名包含点号或特殊字符，则必须使用第一种方式）。

![](img/5c9dc3105b7451e8bcca3f53c06545fd_40.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_41.png)

## Register变量 💾

![](img/5c9dc3105b7451e8bcca3f53c06545fd_43.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_45.png)

`register`变量用于将一个任务的执行结果保存下来，供后续任务引用。这对于需要根据前一个任务的结果来决定后续操作，或者需要提取任务输出中的特定信息时非常有用。

![](img/5c9dc3105b7451e8bcca3f53c06545fd_47.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_49.png)

以下是`register`变量的使用方法。

![](img/5c9dc3105b7451e8bcca3f53c06545fd_51.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_53.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_55.png)

**代码示例**：
```yaml
---
- name: 演示register变量
  hosts: servers
  tasks:
    - name: 创建用户并注册结果
      user:
        name: test_user12
        state: present
      register: user_result  # 将user模块的执行结果存入user_result变量

    - name: 调试输出注册的变量
      debug:
        msg: "{{ user_result }}"  # 输出整个结果字典

    - name: 输出创建用户的UID
      debug:
        msg: "新用户的UID是：{{ user_result.uid }}"

    - name: 检查用户创建是否导致了变更
      debug:
        msg: "任务是否改变了系统状态：{{ user_result.changed }}"
```

![](img/5c9dc3105b7451e8bcca3f53c06545fd_57.png)

`register`捕获的结果是一个字典，包含了任务执行的详细信息，如是否成功(`changed`)、返回码(`rc`)、标准输出(`stdout`)等。你可以通过点号或方括号语法引用其中的特定字段。

![](img/5c9dc3105b7451e8bcca3f53c06545fd_59.png)

## 总结 🎯

![](img/5c9dc3105b7451e8bcca3f53c06545fd_61.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_63.png)

本节课中我们一起学习了Ansible中四种主要的变量类型：
1.  **常规变量**：用于存储单个值，定义方式为`key=value`或`key: value`。
2.  **数组变量**：用于存储一系列同类型的值，通过索引（从0开始）引用单个元素。
3.  **字典变量**：用于存储具有层级结构的、多种类型的数据，通过键名来引用值。
4.  **Register变量**：用于捕获一个任务的执行结果，并将结果字典存入变量，便于后续任务分析和引用。

![](img/5c9dc3105b7451e8bcca3f53c06545fd_65.png)

![](img/5c9dc3105b7451e8bcca3f53c06545fd_67.png)

熟练掌握这些变量的定义和使用方法，是编写复杂、动态Ansible剧本的基础。