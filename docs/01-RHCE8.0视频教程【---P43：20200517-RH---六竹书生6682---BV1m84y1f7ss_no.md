# RHCE 8.0 课程：P43：变量优先级与流程控制

![](img/714a953da4241ef2497731445c70e0bd_0.png)

![](img/714a953da4241ef2497731445c70e0bd_2.png)

![](img/714a953da4241ef2497731445c70e0bd_4.png)

![](img/714a953da4241ef2497731445c70e0bd_6.png)

在本节课中，我们将要学习 Ansible 中变量的优先级顺序，以及如何使用循环（`loop`）和条件判断（`when`）来编写更高效、更灵活的 Playbook。

![](img/714a953da4241ef2497731445c70e0bd_8.png)

![](img/714a953da4241ef2497731445c70e0bd_10.png)

## 概述：变量优先级

![](img/714a953da4241ef2497731445c70e0bd_12.png)

上一节我们介绍了 Ansible 中变量的几种赋值模式。本节中我们来看看这些赋值模式的优先级顺序，即当同一个变量通过多种方式被赋值时，Ansible 最终会采用哪一个值。

![](img/714a953da4241ef2497731445c70e0bd_14.png)

![](img/714a953da4241ef2497731445c70e0bd_16.png)

变量赋值的优先级从高到低依次为：

![](img/714a953da4241ef2497731445c70e0bd_18.png)

![](img/714a953da4241ef2497731445c70e0bd_20.png)

![](img/714a953da4241ef2497731445c70e0bd_22.png)

1.  **命令行传递的变量**：通过 `-e` 或 `--extra-vars` 选项在命令行直接指定的变量拥有最高优先级。
2.  **Playbook 中定义的变量**：在 Playbook 的 `vars` 部分定义的变量。
3.  **变量文件中定义的变量**：通过 `vars_files` 引入的外部 YAML 文件中定义的变量。
4.  **Inventory 文件中定义的变量**：在主机清单文件中定义的变量。其中又分为：
    *   **主机变量**：针对单个主机定义的变量，优先级更高。
    *   **组变量**：针对主机组定义的变量。

![](img/714a953da4241ef2497731445c70e0bd_24.png)

## 实验验证优先级

![](img/714a953da4241ef2497731445c70e0bd_26.png)

![](img/714a953da4241ef2497731445c70e0bd_28.png)

为了验证上述优先级，我们将通过一个简单的 Playbook 进行测试。

![](img/714a953da4241ef2497731445c70e0bd_30.png)

![](img/714a953da4241ef2497731445c70e0bd_31.png)

![](img/714a953da4241ef2497731445c70e0bd_33.png)

### 准备测试 Playbook

![](img/714a953da4241ef2497731445c70e0bd_35.png)

![](img/714a953da4241ef2497731445c70e0bd_37.png)

![](img/714a953da4241ef2497731445c70e0bd_39.png)

首先，我们创建一个用于测试的 Playbook 文件 `test_priority.yml`。

```yaml
---
- hosts: web2012
  remote_user: root
  vars:
    test_var: "method_03"
  tasks:
    - name: Create a test file
      file:
        path: /tmp/{{ test_var }}
        state: touch
```

![](img/714a953da4241ef2497731445c70e0bd_41.png)

这个 Playbook 会在目标主机的 `/tmp` 目录下创建一个以 `test_var` 变量值为名称的文件。我们在 Playbook 的 `vars` 部分将 `test_var` 定义为 `"method_03"`。

![](img/714a953da4241ef2497731445c70e0bd_43.png)

![](img/714a953da4241ef2497731445c70e0bd_45.png)

### 测试 1：命令行变量 vs Playbook 变量

![](img/714a953da4241ef2497731445c70e0bd_47.png)

![](img/714a953da4241ef2497731445c70e0bd_49.png)

![](img/714a953da4241ef2497731445c70e0bd_50.png)

现在，我们通过命令行传递一个不同的值给 `test_var` 变量。

![](img/714a953da4241ef2497731445c70e0bd_52.png)

![](img/714a953da4241ef2497731445c70e0bd_54.png)

```bash
ansible-playbook test_priority.yml -e "test_var=method_01"
```

![](img/714a953da4241ef2497731445c70e0bd_56.png)

执行后，检查目标主机 `/tmp` 目录下的文件。你会发现创建的文件名为 `method_01`，而不是 Playbook 中定义的 `method_03`。这证明了**命令行传递的变量优先级高于 Playbook 中定义的变量**。

![](img/714a953da4241ef2497731445c70e0bd_58.png)

![](img/714a953da4241ef2497731445c70e0bd_60.png)

![](img/714a953da4241ef2497731445c70e0bd_62.png)

![](img/714a953da4241ef2497731445c70e0bd_64.png)

### 测试 2：变量文件变量 vs Playbook 变量

![](img/714a953da4241ef2497731445c70e0bd_66.png)

![](img/714a953da4241ef2497731445c70e0bd_68.png)

![](img/714a953da4241ef2497731445c70e0bd_70.png)

接下来，我们测试变量文件与 Playbook 变量的优先级。

![](img/714a953da4241ef2497731445c70e0bd_72.png)

![](img/714a953da4241ef2497731445c70e0bd_74.png)

![](img/714a953da4241ef2497731445c70e0bd_76.png)

首先，创建一个变量文件 `var_priority.yml`。

![](img/714a953da4241ef2497731445c70e0bd_78.png)

![](img/714a953da4241ef2497731445c70e0bd_80.png)

![](img/714a953da4241ef2497731445c70e0bd_82.png)

```yaml
---
test_var: "method_02"
```

![](img/714a953da4241ef2497731445c70e0bd_84.png)

![](img/714a953da4241ef2497731445c70e0bd_86.png)

然后，修改 Playbook，通过 `vars_files` 引入这个变量文件，并注释掉 Playbook 内部的 `vars` 定义。

![](img/714a953da4241ef2497731445c70e0bd_88.png)

![](img/714a953da4241ef2497731445c70e0bd_89.png)

![](img/714a953da4241ef2497731445c70e0bd_91.png)

![](img/714a953da4241ef2497731445c70e0bd_92.png)

```yaml
---
- hosts: web2012
  remote_user: root
  vars_files:
    - var_priority.yml
#  vars:
#    test_var: "method_03"
  tasks:
    - name: Create a test file
      file:
        path: /tmp/{{ test_var }}
        state: touch
```

![](img/714a953da4241ef2497731445c70e0bd_94.png)

![](img/714a953da4241ef2497731445c70e0bd_96.png)

再次运行 Playbook（不通过命令行传参）。

![](img/714a953da4241ef2497731445c70e0bd_98.png)

![](img/714a953da4241ef2497731445c70e0bd_100.png)

```bash
ansible-playbook test_priority.yml
```

![](img/714a953da4241ef2497731445c70e0bd_102.png)

![](img/714a953da4241ef2497731445c70e0bd_104.png)

![](img/714a953da4241ef2497731445c70e0bd_106.png)

执行后，创建的文件名为 `method_02`。这证明了**变量文件中定义的变量优先级高于 Playbook 中定义的变量**。

### 测试 3：Inventory 变量 vs Playbook 变量

![](img/714a953da4241ef2497731445c70e0bd_108.png)

![](img/714a953da4241ef2497731445c70e0bd_110.png)

![](img/714a953da4241ef2497731445c70e0bd_112.png)

最后，我们测试 Inventory 文件中的变量。

![](img/714a953da4241ef2497731445c70e0bd_114.png)

![](img/714a953da4241ef2497731445c70e0bd_116.png)

编辑主机清单文件 `/etc/ansible/hosts`，为主机 `web1` 定义主机变量，并为组 `web2012` 定义组变量。

```ini
[web2012]
web1 ansible_host=192.168.88.201 test_var=method_04_1
web2 ansible_host=192.168.88.202

![](img/714a953da4241ef2497731445c70e0bd_118.png)

![](img/714a953da4241ef2497731445c70e0bd_120.png)

![](img/714a953da4241ef2497731445c70e0bd_122.png)

[web2012:vars]
test_var=method_04_12
```

![](img/714a953da4241ef2497731445c70e0bd_124.png)

![](img/714a953da4241ef2497731445c70e0bd_126.png)

修改 Playbook，移除 `vars_files` 并取消注释 `vars`，将 `test_var` 重新设为 `"method_03"`。

![](img/714a953da4241ef2497731445c70e0bd_128.png)

![](img/714a953da4241ef2497731445c70e0bd_130.png)

```yaml
---
- hosts: web2012
  remote_user: root
  vars:
    test_var: "method_03"
  tasks:
    - name: Create a test file
      file:
        path: /tmp/{{ test_var }}
        state: touch
```

![](img/714a953da4241ef2497731445c70e0bd_132.png)

![](img/714a953da4241ef2497731445c70e0bd_134.png)

![](img/714a953da4241ef2497731445c70e0bd_136.png)

运行 Playbook。

```bash
ansible-playbook test_priority.yml
```

![](img/714a953da4241ef2497731445c70e0bd_138.png)

![](img/714a953da4241ef2497731445c70e0bd_140.png)

对于主机 `web1`，创建的文件名为 `method_03`，而不是 Inventory 中定义的 `method_04_1` 或 `method_04_12`。这证明了**Playbook 中定义的变量优先级高于 Inventory 文件中定义的变量**。

### 测试 4：主机变量 vs 组变量

![](img/714a953da4241ef2497731445c70e0bd_142.png)

![](img/714a953da4241ef2497731445c70e0bd_144.png)

![](img/714a953da4241ef2497731445c70e0bd_146.png)

为了测试主机变量和组变量的优先级，我们需要移除 Playbook 中对 `test_var` 的定义，让 Ansible 去读取 Inventory 中的值。

![](img/714a953da4241ef2497731445c70e0bd_148.png)

![](img/714a953da4241ef2497731445c70e0bd_150.png)

修改 Playbook，注释掉 `vars` 部分。

![](img/714a953da4241ef2497731445c70e0bd_152.png)

```yaml
---
- hosts: web2012
  remote_user: root
#  vars:
#    test_var: "method_03"
  tasks:
    - name: Create a test file
      file:
        path: /tmp/{{ test_var }}
        state: touch
```

再次运行 Playbook。

```bash
ansible-playbook test_priority.yml
```

检查主机 `web1` 创建的文件名。你会发现是 `method_04_1`，而不是 `method_04_12`。这证明了在 Inventory 内部，**主机变量的优先级高于组变量**。

## 总结：变量优先级

通过以上实验，我们可以清晰地总结出 Ansible 变量的优先级顺序（从高到低）：

![](img/714a953da4241ef2497731445c70e0bd_154.png)

1.  命令行传递的变量 (`-e`)
2.  Playbook 中定义的变量 (`vars`)
3.  变量文件中定义的变量 (`vars_files`)
4.  Inventory 文件中定义的变量
    *   主机变量
    *   组变量

![](img/714a953da4241ef2497731445c70e0bd_156.png)

![](img/714a953da4241ef2497731445c70e0bd_158.png)

理解这个顺序对于管理复杂的变量定义和避免冲突至关重要。

![](img/714a953da4241ef2497731445c70e0bd_160.png)

![](img/714a953da4241ef2497731445c70e0bd_162.png)

---

![](img/714a953da4241ef2497731445c70e0bd_164.png)

上一节我们明确了变量的优先级，这有助于我们管理配置。本节中我们来看看如何利用循环和条件判断，让 Playbook 的编写更加高效和智能。

![](img/714a953da4241ef2497731445c70e0bd_166.png)

![](img/714a953da4241ef2497731445c70e0bd_168.png)

## 使用循环（`loop`）

![](img/714a953da4241ef2497731445c70e0bd_170.png)

![](img/714a953da4241ef2497731445c70e0bd_172.png)

![](img/714a953da4241ef2497731445c70e0bd_174.png)

在编写 Playbook 时，我们经常需要对多个条目执行相同的操作。例如，启动多个服务。使用 `loop` 可以避免重复编写相似的任务模块。

![](img/714a953da4241ef2497731445c70e0bd_176.png)

![](img/714a953da4241ef2497731445c70e0bd_178.png)

### 基础循环示例

![](img/714a953da4241ef2497731445c70e0bd_180.png)

假设我们需要管理 `httpd` 和 `vsftpd` 两个服务。不使用循环的写法如下：

```yaml
tasks:
  - name: Manage httpd service
    service:
      name: httpd
      state: started
  - name: Manage vsftpd service
    service:
      name: vsftpd
      state: started
```

使用 `loop` 可以简化为：

```yaml
tasks:
  - name: Manage services
    service:
      name: "{{ item }}"
      state: started
    loop:
      - httpd
      - vsftpd
```

在这个例子中，`{{ item }}` 是一个特殊的变量，它会在每次循环迭代时被替换为 `loop` 列表中的当前值。第一次迭代 `item` 是 `httpd`，第二次是 `vsftpd`。

### 使用变量优化循环

![](img/714a953da4241ef2497731445c70e0bd_182.png)

![](img/714a953da4241ef2497731445c70e0bd_184.png)

我们可以将需要循环的列表定义为一个变量，使 Playbook 更易于维护。

![](img/714a953da4241ef2497731445c70e0bd_186.png)

![](img/714a953da4241ef2497731445c70e0bd_188.png)

![](img/714a953da4241ef2497731445c70e0bd_190.png)

```yaml
vars:
  services_to_manage:
    - httpd
    - vsftpd

![](img/714a953da4241ef2497731445c70e0bd_192.png)

![](img/714a953da4241ef2497731445c70e0bd_193.png)

tasks:
  - name: Manage services
    service:
      name: "{{ item }}"
      state: started
    loop: "{{ services_to_manage }}"
```

![](img/714a953da4241ef2497731445c70e0bd_195.png)

![](img/714a953da4241ef2497731445c70e0bd_197.png)

### 循环字典列表（高级用法）

![](img/714a953da4241ef2497731445c70e0bd_199.png)

![](img/714a953da4241ef2497731445c70e0bd_200.png)

有时，我们需要为每个循环项提供多个参数。例如，创建用户并指定其所属的组。这时可以使用字典列表。

![](img/714a953da4241ef2497731445c70e0bd_202.png)

```yaml
tasks:
  - name: Create users with specific groups
    user:
      name: "{{ item.name }}"
      groups: "{{ item.group }}"
      state: present
    loop:
      - { name: 'user10517', group: 'root' }
      - { name: 'user20517', group: 'sshd' }
```

![](img/714a953da4241ef2497731445c70e0bd_204.png)

![](img/714a953da4241ef2497731445c70e0bd_206.png)

![](img/714a953da4241ef2497731445c70e0bd_208.png)

![](img/714a953da4241ef2497731445c70e0bd_210.png)

在这个循环中，`item` 是一个字典。第一次迭代时，`item.name` 是 `user10517`，`item.group` 是 `root`。这使得在一个任务中处理多个关联变量变得非常方便。

![](img/714a953da4241ef2497731445c70e0bd_212.png)

![](img/714a953da4241ef2497731445c70e0bd_214.png)

## 使用条件判断（`when`）

![](img/714a953da4241ef2497731445c70e0bd_216.png)

![](img/714a953da4241ef2497731445c70e0bd_218.png)

![](img/714a953da4241ef2497731445c70e0bd_220.png)

`when` 语句用于条件性地执行任务。它类似于编程语言中的 `if` 判断。

![](img/714a953da4241ef2497731445c70e0bd_222.png)

![](img/714a953da4241ef2497731445c70e0bd_224.png)

![](img/714a953da4241ef2497731445c70e0bd_226.png)

![](img/714a953da4241ef2497731445c70e0bd_228.png)

### 基础条件判断

![](img/714a953da4241ef2497731445c70e0bd_230.png)

![](img/714a953da4241ef2497731445c70e0bd_232.png)

![](img/714a953da4241ef2497731445c70e0bd_234.png)

最基本的用法是判断一个变量是否被定义。

![](img/714a953da4241ef2497731445c70e0bd_236.png)

```yaml
vars:
  app_name: "httpd" # 如果注释掉这行，下面的任务将被跳过

tasks:
  - name: Install package only if variable is defined
    yum:
      name: "{{ app_name }}"
      state: present
    when: app_name is defined
```

如果 `app_name` 变量未被定义，则 `when` 条件为假，该任务将被跳过。

![](img/714a953da4241ef2497731445c70e0bd_238.png)

### 常用条件运算符

![](img/714a953da4241ef2497731445c70e0bd_240.png)

以下是 `when` 语句中常用的一些判断条件：

![](img/714a953da4241ef2497731445c70e0bd_242.png)

![](img/714a953da4241ef2497731445c70e0bd_244.png)

*   **等于（字符串）**：`when: variable == “value”`
*   **等于（数值）**：`when: variable == 123`
*   **不等于**：`when: variable != “value”`
*   **大于/小于**：`when: variable > 10` 或 `variable < 20`
*   **大于等于/小于等于**：`when: variable >= 10` 或 `variable <= 20`
*   **变量已定义**：`when: variable is defined`
*   **变量未定义**：`when: variable is not defined`
*   **值存在于列表中**：`when: variable in [“value1”, “value2”]`
*   **布尔值判断**：`when: variable` （变量为真时执行）或 `when: not variable` （变量为假时执行）

![](img/714a953da4241ef2497731445c70e0bd_246.png)

![](img/714a953da4241ef2497731445c70e0bd_248.png)

![](img/714a953da4241ef2497731445c70e0bd_250.png)

![](img/714a953da4241ef2497731445c70e0bd_252.png)

### 多条件组合

![](img/714a953da4241ef2497731445c70e0bd_254.png)

![](img/714a953da4241ef2497731445c70e0bd_256.png)

![](img/714a953da4241ef2497731445c70e0bd_258.png)

可以使用 `and` 和 `or` 来组合多个条件。

![](img/714a953da4241ef2497731445c70e0bd_260.png)

*   **`and`**：所有条件都必须满足。
    ```yaml
    when: ansible_distribution == “RedHat” and ansible_distribution_major_version == “8”
    ```
*   **`or`**：至少一个条件满足即可。
    ```yaml
    when: ansible_distribution == “RedHat” or ansible_distribution == “CentOS”
    ```

![](img/714a953da4241ef2497731445c70e0bd_262.png)

### 结合循环与条件判断

![](img/714a953da4241ef2497731445c70e0bd_264.png)

![](img/714a953da4241ef2497731445c70e0bd_266.png)

![](img/714a953da4241ef2497731445c70e0bd_268.png)

一个强大的模式是在循环任务中使用条件判断。例如，我们只想在根分区（`/`）的可用空间大于 300MB 时安装 `tree` 软件。

![](img/714a953da4241ef2497731445c70e0bd_270.png)

![](img/714a953da4241ef2497731445c70e0bd_272.png)

首先，我们需要获取挂载点信息。Ansible 的 `setup` 模块收集的 `ansible_mounts` 事实是一个列表，包含了所有挂载点的详细信息。

![](img/714a953da4241ef2497731445c70e0bd_274.png)

![](img/714a953da4241ef2497731445c70e0bd_276.png)

我们可以这样编写 Playbook：

![](img/714a953da4241ef2497731445c70e0bd_278.png)

![](img/714a953da4241ef2497731445c70e0bd_280.png)

```yaml
---
- hosts: web2012
  remote_user: root
  tasks:
    - name: Install tree if root partition has enough space
      yum:
        name: tree
        state: present
      loop: "{{ ansible_mounts }}"
      when: item.mount == “/” and item.size_available > 300000000
```

![](img/714a953da4241ef2497731445c70e0bd_282.png)

![](img/714a953da4241ef2497731445c70e0bd_284.png)

在这个任务中：
1.  `loop: “{{ ansible_mounts }}”` 会遍历所有挂载点信息，每次循环的 `item` 是一个包含 `mount`, `size_available` 等键的字典。
2.  `when: item.mount == “/” and item.size_available > 300000000` 会检查当前 `item` 是否是根分区（`/`）并且其可用空间是否大于 300MB（注意单位是字节）。
3.  只有同时满足这两个条件时，`yum` 任务才会针对当前的循环项（即根分区）执行，安装 `tree` 软件。

![](img/714a953da4241ef2497731445c70e0bd_286.png)

![](img/714a953da4241ef2497731445c70e0bd_288.png)

## 本节课总结

![](img/714a953da4241ef2497731445c70e0bd_290.png)

![](img/714a953da4241ef2497731445c70e0bd_292.png)

![](img/714a953da4241ef2497731445c70e0bd_294.png)

在本节课中，我们一起学习了：

![](img/714a953da4241ef2497731445c70e0bd_296.png)

1.  **变量优先级**：掌握了 Ansible 中不同变量来源的优先级顺序，这对于解决变量冲突和理解配置生效顺序非常重要。
2.  **循环（`loop`）**：学会了使用 `loop` 来简化对列表或字典列表的重复性操作，极大地提高了 Playbook 的编写效率和可读性。
3.  **条件判断（`when`）**：了解了如何使用 `when` 语句基于变量或事实来条件性地执行任务，使得 Playbook 能够根据目标主机的不同状态做出智能决策。
4.  **循环与条件结合**：探索了将 `loop` 和 `when` 结合使用的强大模式，能够实现更精细、更动态的自动化控制。

![](img/714a953da4241ef2497731445c70e0bd_298.png)

![](img/714a953da4241ef2497731445c70e0bd_300.png)

通过灵活运用这些流程控制功能，你可以编写出适应性强、逻辑清晰且易于维护的 Ansible Playbook。