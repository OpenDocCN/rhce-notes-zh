# Ansible 变量与流程控制：P43：变量优先级、循环与条件判断

![](img/0080b606b764efc04328e70fcc5fe588_0.png)

![](img/0080b606b764efc04328e70fcc5fe588_2.png)

![](img/0080b606b764efc04328e70fcc5fe588_4.png)

![](img/0080b606b764efc04328e70fcc5fe588_6.png)

![](img/0080b606b764efc04328e70fcc5fe588_8.png)

![](img/0080b606b764efc04328e70fcc5fe588_10.png)

在本节课中，我们将深入学习 Ansible 中变量的优先级规则，以及如何使用循环和条件判断来编写更高效、更灵活的 Playbook。

![](img/0080b606b764efc04328e70fcc5fe588_12.png)

![](img/0080b606b764efc04328e70fcc5fe588_14.png)

![](img/0080b606b764efc04328e70fcc5fe588_16.png)

![](img/0080b606b764efc04328e70fcc5fe588_18.png)

## 变量优先级详解

![](img/0080b606b764efc04328e70fcc5fe588_20.png)

![](img/0080b606b764efc04328e70fcc5fe588_22.png)

![](img/0080b606b764efc04328e70fcc5fe588_24.png)

![](img/0080b606b764efc04328e70fcc5fe588_26.png)

上一节我们介绍了 Ansible 中几种常见的变量赋值方式。本节中我们来看看这些赋值方式的优先级顺序，即当同一个变量通过多种方式被赋值时，Ansible 最终会采用哪一个值。

![](img/0080b606b764efc04328e70fcc5fe588_28.png)

![](img/0080b606b764efc04328e70fcc5fe588_30.png)

![](img/0080b606b764efc04328e70fcc5fe588_31.png)

![](img/0080b606b764efc04328e70fcc5fe588_33.png)

![](img/0080b606b764efc04328e70fcc5fe588_35.png)

以下是 Ansible 变量赋值的优先级顺序，从高到低排列：

![](img/0080b606b764efc04328e70fcc5fe588_37.png)

![](img/0080b606b764efc04328e70fcc5fe588_39.png)

1.  **命令行传递的变量**：通过 `-e` 或 `--extra-vars` 选项在命令行直接指定的变量拥有最高优先级。
2.  **Playbook 中定义的变量**：在 Playbook 的 `vars` 部分定义的变量。
3.  **变量文件中定义的变量**：通过 `vars_files` 引入的外部变量文件中的变量。
4.  **Inventory 文件中定义的变量**：
    *   **主机变量**：针对单个主机定义的变量。
    *   **组变量**：针对主机组定义的变量。

![](img/0080b606b764efc04328e70fcc5fe588_41.png)

![](img/0080b606b764efc04328e70fcc5fe588_43.png)

**注意**：主机变量的优先级高于组变量。

![](img/0080b606b764efc04328e70fcc5fe588_45.png)

![](img/0080b606b764efc04328e70fcc5fe588_47.png)

![](img/0080b606b764efc04328e70fcc5fe588_49.png)

![](img/0080b606b764efc04328e70fcc5fe588_50.png)

### 优先级验证实验

![](img/0080b606b764efc04328e70fcc5fe588_52.png)

![](img/0080b606b764efc04328e70fcc5fe588_54.png)

![](img/0080b606b764efc04328e70fcc5fe588_56.png)

为了验证上述优先级，我们可以通过一个简单的实验来观察。

![](img/0080b606b764efc04328e70fcc5fe588_58.png)

![](img/0080b606b764efc04328e70fcc5fe588_60.png)

![](img/0080b606b764efc04328e70fcc5fe588_62.png)

![](img/0080b606b764efc04328e70fcc5fe588_64.png)

![](img/0080b606b764efc04328e70fcc5fe588_66.png)

![](img/0080b606b764efc04328e70fcc5fe588_68.png)

**步骤一：创建测试 Playbook**
首先，我们创建一个用于测试的 Playbook 文件 `test_priority.yml`。

![](img/0080b606b764efc04328e70fcc5fe588_70.png)

![](img/0080b606b764efc04328e70fcc5fe588_72.png)

![](img/0080b606b764efc04328e70fcc5fe588_74.png)

![](img/0080b606b764efc04328e70fcc5fe588_76.png)

![](img/0080b606b764efc04328e70fcc5fe588_78.png)

![](img/0080b606b764efc04328e70fcc5fe588_80.png)

```yaml
---
- hosts: web
  remote_user: root
  vars:
    test_var: "method_03"
  tasks:
    - name: Create a test file
      file:
        path: /tmp/{{ test_var }}
        state: touch
```

![](img/0080b606b764efc04328e70fcc5fe588_82.png)

![](img/0080b606b764efc04328e70fcc5fe588_84.png)

![](img/0080b606b764efc04328e70fcc5fe588_85.png)

![](img/0080b606b764efc04328e70fcc5fe588_87.png)

![](img/0080b606b764efc04328e70fcc5fe588_88.png)

**步骤二：验证命令行优先级最高**
我们通过命令行传递变量 `test_var` 的值为 `method_01`，然后运行 Playbook。

![](img/0080b606b764efc04328e70fcc5fe588_90.png)

![](img/0080b606b764efc04328e70fcc5fe588_92.png)

![](img/0080b606b764efc04328e70fcc5fe588_94.png)

```bash
ansible-playbook test_priority.yml -e "test_var=method_01"
```

![](img/0080b606b764efc04328e70fcc5fe588_96.png)

![](img/0080b606b764efc04328e70fcc5fe588_98.png)

![](img/0080b606b764efc04328e70fcc5fe588_100.png)

![](img/0080b606b764efc04328e70fcc5fe588_102.png)

执行后，检查目标主机 `/tmp` 目录下创建的文件名。如果文件名为 `method_01`，则证明命令行赋值的优先级高于 Playbook 中 `vars` 定义的 `method_03`。

![](img/0080b606b764efc04328e70fcc5fe588_104.png)

![](img/0080b606b764efc04328e70fcc5fe588_106.png)

![](img/0080b606b764efc04328e70fcc5fe588_108.png)

![](img/0080b606b764efc04328e70fcc5fe588_110.png)

**步骤三：验证变量文件优先级高于 Playbook**
1.  创建变量文件 `var_priority.yml`，内容如下：
    ```yaml
    test_var: "method_02"
    ```
2.  修改 Playbook，引入变量文件并移除命令行参数：
    ```yaml
    ---
    - hosts: web
      remote_user: root
      vars_files:
        - var_priority.yml
      vars:
        test_var: "method_03"
      tasks:
        - name: Create a test file
          file:
            path: /tmp/{{ test_var }}
            state: touch
    ```
3.  运行 Playbook（不通过命令行传参）：
    ```bash
    ansible-playbook test_priority.yml
    ```
执行后，若创建的文件名为 `method_02`，则证明变量文件 (`var_priority.yml`) 中定义的变量优先级高于 Playbook 中 `vars` 定义的变量。

![](img/0080b606b764efc04328e70fcc5fe588_112.png)

**步骤四：验证 Playbook 变量优先级高于 Inventory 变量**
1.  在 Inventory 文件（如 `/etc/ansible/hosts`）中定义主机变量和组变量：
    ```ini
    [web]
    192.168.1.201 test_var="method_04_12"

    [web:vars]
    test_var="method_04_12"
    ```
2.  修改 Playbook，注释掉 `vars_files` 和 `vars` 部分，使其不定义 `test_var`。
3.  运行 Playbook。观察创建的文件名。如果文件名为 `method_04_1`，则证明主机变量优先级高于组变量。如果之前 Playbook 中定义的 `method_03` 生效，则证明 Playbook 变量优先级高于 Inventory 变量。

![](img/0080b606b764efc04328e70fcc5fe588_114.png)

![](img/0080b606b764efc04328e70fcc5fe588_116.png)

![](img/0080b606b764efc04328e70fcc5fe588_118.png)

![](img/0080b606b764efc04328e70fcc5fe588_120.png)

![](img/0080b606b764efc04328e70fcc5fe588_122.png)

通过以上实验，可以清晰地理解并记住 Ansible 变量赋值的优先级顺序。

![](img/0080b606b764efc04328e70fcc5fe588_124.png)

![](img/0080b606b764efc04328e70fcc5fe588_126.png)

![](img/0080b606b764efc04328e70fcc5fe588_128.png)

![](img/0080b606b764efc04328e70fcc5fe588_130.png)

![](img/0080b606b764efc04328e70fcc5fe588_132.png)

## 使用循环简化任务

![](img/0080b606b764efc04328e70fcc5fe588_134.png)

在编写 Playbook 时，我们经常需要对多个条目执行相同的操作。例如，启动多个服务、创建多个用户或安装多个软件包。Ansible 提供了 `loop` 关键字来实现循环，避免代码重复。

![](img/0080b606b764efc04328e70fcc5fe588_136.png)

![](img/0080b606b764efc04328e70fcc5fe588_138.png)

![](img/0080b606b764efc04328e70fcc5fe588_140.png)

### 基础循环

![](img/0080b606b764efc04328e70fcc5fe588_142.png)

![](img/0080b606b764efc04328e70fcc5fe588_144.png)

![](img/0080b606b764efc04328e70fcc5fe588_146.png)

![](img/0080b606b764efc04328e70fcc5fe588_148.png)

基础循环遍历一个简单的列表，每次循环时，当前项的值存储在默认变量 `{{ item }}` 中。

**示例：使用循环启动多个服务**
以下 Playbook 使用循环来启动 `httpd` 和 `vsftpd` 服务。

```yaml
---
- hosts: web
  remote_user: root
  tasks:
    - name: Manage services with loop
      service:
        name: "{{ item }}"
        state: started
      loop:
        - httpd
        - vsftpd
```
在这个例子中，`loop` 列表包含两个字符串。任务第一次执行时，`{{ item }}` 的值为 `httpd`；第二次执行时，`{{ item }}` 的值为 `vsftpd`。

### 使用变量优化循环

![](img/0080b606b764efc04328e70fcc5fe588_150.png)

![](img/0080b606b764efc04328e70fcc5fe588_152.png)

我们可以将列表定义为一个变量，使 Playbook 更易于维护和复用。

![](img/0080b606b764efc04328e70fcc5fe588_154.png)

![](img/0080b606b764efc04328e70fcc5fe588_156.png)

![](img/0080b606b764efc04328e70fcc5fe588_158.png)

![](img/0080b606b764efc04328e70fcc5fe588_160.png)

```yaml
---
- hosts: web
  remote_user: root
  vars:
    services_to_manage:
      - httpd
      - vsftpd
  tasks:
    - name: Manage services with variable loop
      service:
        name: "{{ item }}"
        state: started
      loop: "{{ services_to_manage }}"
```

![](img/0080b606b764efc04328e70fcc5fe588_162.png)

![](img/0080b606b764efc04328e70fcc5fe588_164.png)

![](img/0080b606b764efc04328e70fcc5fe588_166.png)

### 字典列表循环（高级循环）

![](img/0080b606b764efc04328e70fcc5fe588_168.png)

![](img/0080b606b764efc04328e70fcc5fe588_170.png)

![](img/0080b606b764efc04328e70fcc5fe588_172.png)

![](img/0080b606b764efc04328e70fcc5fe588_174.png)

当需要对每个条目设置多个属性时，可以使用字典列表。在循环中，可以通过 `{{ item.key }}` 的形式访问字典的各个值。

![](img/0080b606b764efc04328e70fcc5fe588_176.png)

**示例：使用循环创建多个用户并指定组**
以下 Playbook 创建两个用户，并分别指定他们的主要组。

```yaml
---
- hosts: web
  remote_user: root
  tasks:
    - name: Create users with loop
      user:
        name: "{{ item.name }}"
        group: "{{ item.group }}"
        state: present
      loop:
        - { name: 'user1_0517', group: 'root' }
        - { name: 'user2_0517', group: 'sshd' }
```
在这个例子中，`loop` 列表包含两个字典。每个字典有 `name` 和 `group` 两个键。在任务中，我们使用 `{{ item.name }}` 和 `{{ item.group }}` 来引用它们。

## 使用条件判断控制任务执行

![](img/0080b606b764efc04328e70fcc5fe588_178.png)

![](img/0080b606b764efc04328e70fcc5fe588_180.png)

Ansible 使用 `when` 关键字来实现条件判断，只有满足条件的任务才会被执行。这类似于编程语言中的 `if` 语句。

![](img/0080b606b764efc04328e70fcc5fe588_182.png)

![](img/0080b606b764efc04328e70fcc5fe588_184.png)

![](img/0080b606b764efc04328e70fcc5fe588_186.png)

![](img/0080b606b764efc04328e70fcc5fe588_188.png)

![](img/0080b606b764efc04328e70fcc5fe588_189.png)

### 基础条件判断

![](img/0080b606b764efc04328e70fcc5fe588_191.png)

![](img/0080b606b764efc04328e70fcc5fe588_193.png)

![](img/0080b606b764efc04328e70fcc5fe588_195.png)

![](img/0080b606b764efc04328e70fcc5fe588_196.png)

`when` 后面跟的是一个 Jinja2 表达式，其结果为真时任务执行。

![](img/0080b606b764efc04328e70fcc5fe588_198.png)

![](img/0080b606b764efc04328e70fcc5fe588_200.png)

![](img/0080b606b764efc04328e70fcc5fe588_202.png)

**示例：变量已定义时才执行任务**
```yaml
---
- hosts: web
  remote_user: root
  vars:
    app_name: "httpd" # 注释掉这行，任务将跳过
  tasks:
    - name: Start the app service
      service:
        name: "{{ app_name }}"
        state: started
      when: app_name is defined
```

![](img/0080b606b764efc04328e70fcc5fe588_204.png)

![](img/0080b606b764efc04328e70fcc5fe588_206.png)

![](img/0080b606b764efc04328e70fcc5fe588_208.png)

![](img/0080b606b764efc04328e70fcc5fe588_210.png)

![](img/0080b606b764efc04328e70fcc5fe588_212.png)

### 常用条件判断运算符

![](img/0080b606b764efc04328e70fcc5fe588_214.png)

![](img/0080b606b764efc04328e70fcc5fe588_216.png)

![](img/0080b606b764efc04328e70fcc5fe588_218.png)

![](img/0080b606b764efc04328e70fcc5fe588_220.png)

![](img/0080b606b764efc04328e70fcc5fe588_222.png)

![](img/0080b606b764efc04328e70fcc5fe588_224.png)

以下是一些在 `when` 语句中常用的判断方式：

![](img/0080b606b764efc04328e70fcc5fe588_226.png)

![](img/0080b606b764efc04328e70fcc5fe588_228.png)

![](img/0080b606b764efc04328e70fcc5fe588_230.png)

![](img/0080b606b764efc04328e70fcc5fe588_232.png)

*   **等于（字符串）**：`when: variable == “value”`
*   **等于（数字）**：`when: variable == 123`
*   **不等于**：`when: variable != “value”`
*   **大于/小于**：`when: variable > 10` 或 `variable < 10`
*   **大于等于/小于等于**：`when: variable >= 10` 或 `variable <= 10`
*   **变量已定义**：`when: variable is defined`
*   **变量未定义**：`when: variable is not defined`
*   **值存在于列表中**：`when: variable in [“value1”, “value2”]`
*   **布尔值判断**：`when: variable` （变量为真时执行）或 `when: not variable` （变量为假时执行）

### 多条件组合判断

![](img/0080b606b764efc04328e70fcc5fe588_234.png)

![](img/0080b606b764efc04328e70fcc5fe588_236.png)

可以使用 `and` 和 `or` 来组合多个条件。

![](img/0080b606b764efc04328e70fcc5fe588_238.png)

![](img/0080b606b764efc04328e70fcc5fe588_240.png)

![](img/0080b606b764efc04328e70fcc5fe588_242.png)

*   **`and`**：所有条件都必须满足。
    ```yaml
    when: ansible_distribution == “RedHat” and ansible_distribution_major_version == “8”
    ```
*   **`or`**：至少一个条件满足即可。
    ```yaml
    when: ansible_distribution == “RedHat” or ansible_distribution == “CentOS”
    ```

![](img/0080b606b764efc04328e70fcc5fe588_244.png)

![](img/0080b606b764efc04328e70fcc5fe588_246.png)

![](img/0080b606b764efc04328e70fcc5fe588_248.png)

![](img/0080b606b764efc04328e70fcc5fe588_250.png)

![](img/0080b606b764efc04328e70fcc5fe588_252.png)

![](img/0080b606b764efc04328e70fcc5fe588_254.png)

### 结合循环与条件判断

![](img/0080b606b764efc04328e70fcc5fe588_256.png)

![](img/0080b606b764efc04328e70fcc5fe588_258.png)

`when` 条件判断可以很好地与 `loop` 循环结合使用。在循环中，`when` 条件会对每一个 `item` 进行判断。

![](img/0080b606b764efc04328e70fcc5fe588_260.png)

![](img/0080b606b764efc04328e70fcc5fe588_262.png)

![](img/0080b606b764efc04328e70fcc5fe588_264.png)

![](img/0080b606b764efc04328e70fcc5fe588_266.png)

![](img/0080b606b764efc04328e70fcc5fe588_268.png)

**综合示例：根据磁盘信息决定是否安装软件**
以下 Playbook 演示了一个综合案例：它使用 `ansible_mounts` 事实变量获取磁盘挂载信息，循环判断每个挂载点。只有当挂载点为根目录 `/` 且其可用空间大于 300MB 时，才在该主机上安装 `tree` 软件。

![](img/0080b606b764efc04328e70fcc5fe588_270.png)

![](img/0080b606b764efc04328e70fcc5fe588_272.png)

![](img/0080b606b764efc04328e70fcc5fe588_274.png)

```yaml
---
- hosts: web
  remote_user: root
  tasks:
    - name: Install tree if root partition has enough space
      yum:
        name: tree
        state: present
      loop: "{{ ansible_mounts }}"
      when: item.mount == “/” and item.size_available > 300000000
```
在这个例子中：
*   `loop: “{{ ansible_mounts }}”` 遍历所有挂载点信息。
*   `item.mount` 获取当前循环项的挂载点路径。
*   `item.size_available` 获取当前循环项的可用空间大小（单位：字节）。
*   `when` 条件确保只对根目录且空间充足的挂载点执行安装操作。

![](img/0080b606b764efc04328e70fcc5fe588_276.png)

![](img/0080b606b764efc04328e70fcc5fe588_278.png)

![](img/0080b606b764efc04328e70fcc5fe588_280.png)

## 总结

![](img/0080b606b764efc04328e70fcc5fe588_282.png)

![](img/0080b606b764efc04328e70fcc5fe588_284.png)

![](img/0080b606b764efc04328e70fcc5fe588_286.png)

![](img/0080b606b764efc04328e70fcc5fe588_288.png)

![](img/0080b606b764efc04328e70fcc5fe588_290.png)

本节课中我们一起学习了 Ansible 中三个核心的流程控制概念：
1.  **变量优先级**：明确了从命令行、Playbook、变量文件到 Inventory 的变量覆盖顺序，这是编写可预测 Playbook 的基础。
2.  **循环**：使用 `loop` 关键字可以高效地处理重复性任务，无论是简单的列表循环还是复杂的字典列表循环，都能极大简化 Playbook 的编写。
3.  **条件判断**：使用 `when` 关键字可以根据变量、事实或其它条件动态决定是否执行任务，使得 Playbook 能够智能地适应不同的目标主机和环境。

![](img/0080b606b764efc04328e70fcc5fe588_292.png)

![](img/0080b606b764efc04328e70fcc5fe588_294.png)

![](img/0080b606b764efc04328e70fcc5fe588_296.png)

掌握这些知识后，你将能够编写出更简洁、更强大、更易于维护的 Ansible Playbook。