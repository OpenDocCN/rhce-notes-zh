# Ansible 变量管理：P61：管理变量 🔧

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_0.png)

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_2.png)

在本节课中，我们将学习 Ansible 中四种核心的变量类型：常规变量、数组变量、字典变量和注册变量。理解并掌握这些变量的定义和使用方法，是编写高效、灵活 Ansible Playbook 的基础。

## 常规变量 📝

上一节我们介绍了变量的基本概念，本节中我们来看看最基础的变量类型——常规变量。常规变量采用 `key=value` 或 `key: value` 的格式进行定义。

**核心格式**：
```
变量名=值
```
或
```
变量名: 值
```

需要注意的是，在资产清单（inventory）文件中，必须使用等号 `=` 来定义变量，使用冒号 `:` 会导致错误。

## 数组变量 📊

当需要定义一系列同类型的值时，例如一批用户名或软件包列表，使用数组变量是更高效的方式。

以下是定义和使用数组变量的示例：

**示例 Playbook**:
```yaml
- hosts: servers
  vars:
    user_name: ['testuser0', 'testuser1', 'testuser2', 'testuser3']
  tasks:
    - name: 创建用户
      user:
        name: "{{ user_name[0] }}"
        state: present
```

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_4.png)

**关键点**：
*   数组使用列表形式定义：`[‘值1‘， ‘值2‘]`。
*   数组索引从 `0` 开始。例如，`user_name[0]` 引用的是第一个值 `testuser0`。
*   在任务中，通常一次只能调用数组中的一个特定值，不能直接使用整个数组名作为参数（如 `name: “{{ user_name }}”`），这会导致错误。

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_6.png)

## 字典变量 📖

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_8.png)

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_10.png)

当变量的信息包含多种不同类型、具有层级关系的元素时（例如一个用户的姓名、UID、Shell类型），适合使用字典变量。

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_12.png)

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_14.png)

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_15.png)

以下是定义和使用字典变量的两种写法：

**示例 Playbook (写法一：括号引用)**:
```yaml
- hosts: servers
  vars:
    user_info:
      testuser10:
        name: ‘testuser10‘
        shell: ‘/sbin/nologin‘
        comment: ‘test user10‘
      testuser11:
        name: ‘testuser11‘
        shell: ‘/bin/bash‘
        comment: ‘test user11‘
  tasks:
    - name: 通过字典创建用户
      user:
        name: "{{ user_info[‘testuser10‘][‘name‘] }}"
        shell: "{{ user_info[‘testuser10‘][‘shell‘] }}"
        state: present
```

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_17.png)

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_19.png)

**示例 Playbook (写法二：点号引用)**:
```yaml
    - name: 通过字典创建用户 (点号写法)
      user:
        name: "{{ user_info.testuser11.name }}"
        shell: "{{ user_info.testuser11.shell }}"
        state: present
```

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_21.png)

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_22.png)

**关键点**：
*   字典变量可以清晰地表达数据的层级结构。
*   引用字典值时，如果键名不包含点号 `.`，可以使用更简洁的点号表示法（如 `user_info.testuser11.name`）。如果键名可能包含点号，则应使用括号表示法（如 `user_info[‘testuser10‘][‘name‘]`）以确保正确解析。

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_24.png)

## 注册变量 💾

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_26.png)

注册变量 (`register`) 用于捕获一个任务的执行结果，并将其存储到一个变量中，供后续任务使用。这对于基于前一个任务的结果来动态决定后续操作非常有用。

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_28.png)

以下是使用注册变量的示例：

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_30.png)

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_32.png)

**示例 Playbook**:
```yaml
- hosts: servers
  tasks:
    - name: 创建用户并注册结果
      user:
        name: testuser12
        state: present
      register: user_result  # 将创建用户任务的结果注册到变量 user_result

    - name: 调试输出注册结果
      debug:
        msg: “创建用户的任务结果是：{{ user_result }}”

    - name: 输出用户的 UID
      debug:
        msg: “新用户的 UID 是：{{ user_result.uid }}”

    - name: 检查用户是否被更改
      debug:
        msg: “用户状态是否被更改？ {{ user_result.changed }}”
```

**关键点**：
*   `register` 关键字与任务模块（如 `user`, `shell`）位于同一层级。
*   注册后，可以通过 `{{ 变量名 }}` 引用整个结果对象，也可以通过 `{{ 变量名.属性名 }}`（如 `user_result.uid`）引用结果的特定属性。
*   `debug` 模块常用于输出变量值进行调试，它不会对系统配置做出任何实际更改。
*   注册变量的值可以传递给后续任务，实现信息的流转和基于结果的条件判断。

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_34.png)

## 总结 🎯

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_36.png)

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_38.png)

本节课中我们一起学习了 Ansible 的四种核心变量类型：
1.  **常规变量**：用于存储简单的键值对，格式为 `key=value`。
2.  **数组变量**：用于存储一系列同类型的值，通过索引 `[0]` 引用。
3.  **字典变量**：用于存储具有层级结构的复杂数据，可通过点号或括号引用内部值。
4.  **注册变量**：用于捕获任务执行结果，是实现任务间数据传递和条件逻辑的关键。

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_40.png)

![](img/6a059c2b31f8ec84e2e0ec4866283aa4_42.png)

熟练掌握这些变量的定义和引用方法，将使你能够编写出更加强大和灵活的自动化剧本。