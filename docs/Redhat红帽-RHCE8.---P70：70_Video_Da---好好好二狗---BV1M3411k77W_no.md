# Redhat红帽 RHCE8.0认证体系课程：P70：第911天综合练习 🧩

在本节课中，我们将通过三个综合练习题来巩固Ansible剧本的编写技巧。这些练习涵盖了任务块、错误处理、变量加密、条件判断和文件操作等核心概念。

---

## 练习一：使用任务块处理异常与状态变化

上一节我们介绍了Ansible的基础模块，本节中我们来看看如何使用任务块（`block`）来组织任务，并处理异常和状态变化。

![](img/ef8f97979e06894a68b2e372cdec2894_1.png)

![](img/ef8f97979e06894a68b2e372cdec2894_2.png)

![](img/ef8f97979e06894a68b2e372cdec2894_3.png)

**任务要求**：
1.  列出 `/tmp/testdir` 目录中的内容。
2.  如果任务运行异常（例如目录不存在），则自动创建该目录。
3.  任务状态发生改变时，创建一个名为 `text_five` 的文件。
4.  无论任务执行是否正常，都在 `/tmp/tfile` 文件中输出一段话。

![](img/ef8f97979e06894a68b2e372cdec2894_5.png)

![](img/ef8f97979e06894a68b2e372cdec2894_6.png)

![](img/ef8f97979e06894a68b2e372cdec2894_7.png)

以下是实现该要求的剧本示例：

![](img/ef8f97979e06894a68b2e372cdec2894_9.png)

![](img/ef8f97979e06894a68b2e372cdec2894_11.png)

```yaml
---
- name: 练习01
  hosts: all
  ignore_errors: yes
  tasks:
    - block:
        - name: 列出目录内容
          command: ls /tmp/testdir
          register: dir_list
          changed_when: true
      rescue:
        - name: 创建目录
          file:
            path: /tmp/testdir
            state: directory
      always:
        - name: 无论成功与否都写入文件
          copy:
            content: "任务执行完成。"
            dest: /tmp/tfile
```

**关键点解析**：
*   `block`：将多个任务组合成一个逻辑块。
*   `rescue`：当 `block` 中的任务执行失败时，运行此部分的任务。
*   `always`：无论 `block` 成功还是失败，最后都会运行此部分的任务。
*   `changed_when: true`：强制让任务状态被视为“已更改”，从而触发后续的 `handler`（本例中未使用，但可用于触发通知）。

---

![](img/ef8f97979e06894a68b2e372cdec2894_13.png)

![](img/ef8f97979e06894a68b2e372cdec2894_14.png)

## 练习二：使用加密变量创建用户

![](img/ef8f97979e06894a68b2e372cdec2894_16.png)

在上一练习中，我们处理了任务流程。本节我们将学习如何使用加密的变量文件（Vault）来安全地管理敏感信息，并基于主机组动态创建用户。

![](img/ef8f97979e06894a68b2e372cdec2894_18.png)

![](img/ef8f97979e06894a68b2e372cdec2894_20.png)

**任务要求**：
1.  创建一个加密的变量文件（Vault），用于存储用户密码。
2.  编写剧本，根据不同的主机组（`dev` 和 `mgr`），使用加密的密码创建对应的用户。

以下是实现步骤：

**第一步：准备变量文件**
创建明文变量文件 `secret.txt`，内容如下：
```
pw_dev: dev_password123
pw_mgr: mgr_password456
```

![](img/ef8f97979e06894a68b2e372cdec2894_22.png)

![](img/ef8f97979e06894a68b2e372cdec2894_23.png)

**第二步：创建加密的Vault文件**
使用 `ansible-vault` 命令加密变量，生成 `locker.yml`：
```bash
ansible-vault create locker.yml --vault-password-file=secret.txt
```
在编辑器中输入以下内容并保存：
```yaml
pw_develop: "{{ pw_dev }}"
pw_manager: "{{ pw_mgr }}"
```

![](img/ef8f97979e06894a68b2e372cdec2894_25.png)

![](img/ef8f97979e06894a68b2e372cdec2894_27.png)

**第三步：创建用户列表文件**
创建 `user_list.yml`，定义用户信息：
```yaml
users:
  dev:
    - name: alice
      job: developer
    - name: bob
      job: developer
  mgr:
    - name: charlie
      job: manager
```

![](img/ef8f97979e06894a68b2e372cdec2894_29.png)

**第四步：编写主剧本**
创建 `create_users.yml` 剧本：

![](img/ef8f97979e06894a68b2e372cdec2894_31.png)

```yaml
---
- name: 通过Vault创建用户
  hosts: dev, mgr
  vars_files:
    - user_list.yml
    - locker.yml
  tasks:
    - name: 为dev组创建用户
      user:
        name: "{{ item.name }}"
        password: "{{ pw_develop | password_hash('sha512') }}"
        comment: "{{ item.job }}"
      loop: "{{ users.dev }}"
      when: inventory_hostname in groups['dev']

    - name: 为mgr组创建用户
      user:
        name: "{{ item.name }}"
        password: "{{ pw_manager | password_hash('sha512') }}"
        comment: "{{ item.job }}"
      loop: "{{ users.mgr }}"
      when: inventory_hostname in groups['mgr']
```

![](img/ef8f97979e06894a68b2e372cdec2894_33.png)

![](img/ef8f97979e06894a68b2e372cdec2894_34.png)

![](img/ef8f97979e06894a68b2e372cdec2894_36.png)

**第五步：运行剧本**
使用 `--vault-password-file` 参数提供密码文件来运行剧本：
```bash
ansible-playbook create_users.yml --vault-password-file=secret.txt
```

![](img/ef8f97979e06894a68b2e372cdec2894_37.png)

![](img/ef8f97979e06894a68b2e372cdec2894_38.png)

**核心概念**：
*   **Vault加密**：`ansible-vault` 用于加密敏感数据。
*   **变量引用**：通过 `vars_files` 引入变量文件，包括加密文件。
*   **条件循环**：使用 `loop` 遍历列表，结合 `when` 条件判断主机所属组。
*   **密码哈希**：`password_hash(‘sha512’)` 过滤器将明文密码转换为Linux系统可识别的加密格式。

![](img/ef8f97979e06894a68b2e372cdec2894_40.png)

![](img/ef8f97979e06894a68b2e372cdec2894_41.png)

---

![](img/ef8f97979e06894a68b2e372cdec2894_43.png)

![](img/ef8f97979e06894a68b2e372cdec2894_45.png)

## 练习三：基于主机组替换文件内容

最后，我们来看一个文件操作练习。本节将学习如何根据目标主机所在的不同组，动态地替换其文件内容。

![](img/ef8f97979e06894a68b2e372cdec2894_47.png)

![](img/ef8f97979e06894a68b2e372cdec2894_49.png)

**任务要求**：
在 `/etc/issue` 文件中替换文本内容。如果主机在 `dev` 组，则内容为 `development`；如果在 `mgr` 组，则内容为 `manager`。

![](img/ef8f97979e06894a68b2e372cdec2894_50.png)

![](img/ef8f97979e06894a68b2e372cdec2894_51.png)

![](img/ef8f97979e06894a68b2e372cdec2894_52.png)

![](img/ef8f97979e06894a68b2e372cdec2894_54.png)

以下是实现该要求的剧本示例：

![](img/ef8f97979e06894a68b2e372cdec2894_56.png)

![](img/ef8f97979e06894a68b2e372cdec2894_57.png)

```yaml
---
- name: 替换文件内容
  hosts: all
  tasks:
    - name: 为dev组设置内容
      copy:
        content: "development\n"
        dest: /etc/issue
      when: inventory_hostname in groups['dev']

    - name: 为mgr组设置内容
      copy:
        content: "manager\n"
        dest: /etc/issue
      when: inventory_hostname in groups['mgr']
```

![](img/ef8f97979e06894a68b2e372cdec2894_59.png)

**方法说明**：
本例使用 `copy` 模块的 `content` 参数直接生成文件内容并覆盖目标文件。这是一种简单直接的方法。你也可以使用 `lineinfile` 或 `replace` 模块来修改文件的特定部分。

![](img/ef8f97979e06894a68b2e372cdec2894_61.png)

**关键点**：
*   `inventory_hostname in groups[‘组名’]`：这是一个常用的条件判断句式，用于检查当前执行任务的主机是否属于指定的主机组。

![](img/ef8f97979e06894a68b2e372cdec2894_63.png)

---

![](img/ef8f97979e06894a68b2e372cdec2894_65.png)

## 总结 📝

![](img/ef8f97979e06894a68b2e372cdec2894_67.png)

![](img/ef8f97979e06894a68b2e372cdec2894_69.png)

本节课中我们一起学习了三个Ansible综合练习：
1.  **任务块与错误处理**：使用 `block`、`rescue`、`always` 结构化管理任务流程，实现异常处理和事后清理。
2.  **加密变量与动态用户创建**：使用 `ansible-vault` 保护敏感数据，结合变量文件、循环和条件判断，实现基于主机组的自动化用户管理。
3.  **条件化文件操作**：根据主机所属组，使用 `copy` 模块和 `when` 条件判断，动态修改远程主机上的文件内容。

![](img/ef8f97979e06894a68b2e372cdec2894_71.png)

![](img/ef8f97979e06894a68b2e372cdec2894_73.png)

这些练习涵盖了RHCE认证考试中常见的任务类型，理解并掌握这些技能对于通过考试和实际工作都至关重要。请务必在实验环境中亲自操作，加深理解。