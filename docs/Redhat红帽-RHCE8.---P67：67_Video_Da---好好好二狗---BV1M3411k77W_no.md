# Redhat红帽 RHCE8.0认证体系课程：P67：任务控制中的判断

![](img/2ebb5020ed0f2b548bbd79934f462bc2_1.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_2.png)

在本节课中，我们将要学习 Ansible 剧本中一个非常重要的概念：**条件判断**。通过使用 `when` 语句，我们可以根据特定条件来决定是否执行某个任务，从而实现更灵活、更智能的自动化流程。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_4.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_6.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_7.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_8.png)

上一节我们介绍了如何使用循环来批量执行任务，本节中我们来看看如何通过判断来控制任务的执行。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_10.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_12.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_14.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_16.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_18.png)

## 判断语句的基本语法

![](img/2ebb5020ed0f2b548bbd79934f462bc2_20.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_22.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_24.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_26.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_28.png)

在 Ansible 剧本中，判断语句写在任务（task）的末尾，使用 `when` 关键字来引出判断条件。其基本结构如下：

![](img/2ebb5020ed0f2b548bbd79934f462bc2_30.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_32.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_34.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_36.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_38.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_39.png)

```yaml
- name: 任务名称
  module_name:
    module_parameter: value
  when: condition
```

![](img/2ebb5020ed0f2b548bbd79934f462bc2_41.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_43.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_44.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_46.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_48.png)

只有当 `condition`（条件）为真时，该任务才会被执行。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_49.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_51.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_52.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_54.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_56.png)

## 判断条件示例

![](img/2ebb5020ed0f2b548bbd79934f462bc2_58.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_60.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_62.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_64.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_66.png)

以下是几种常见的判断条件写法。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_67.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_69.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_71.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_73.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_75.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_76.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_77.png)

### 示例一：基于事实变量判断

![](img/2ebb5020ed0f2b548bbd79934f462bc2_79.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_81.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_83.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_85.png)

我们可以使用从受管主机收集来的“事实”（facts）作为判断依据。例如，根据主机名来决定是否执行任务。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_86.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_87.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_89.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_91.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_92.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_93.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_95.png)

```yaml
- hosts: all
  tasks:
    - name: 收集事实
      setup:

    - name: 为特定主机创建文件
      copy:
        content: "这是一个测试文件\n"
        dest: "/tmp/test_{{ inventory_hostname }}"
      when: inventory_hostname == "node1.lab.example.com"
```

![](img/2ebb5020ed0f2b548bbd79934f462bc2_97.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_98.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_99.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_101.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_103.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_105.png)

在这个例子中，`inventory_hostname` 是一个内置的“魔法变量”，代表当前主机的清单名称。只有当主机名是 `node1.lab.example.com` 时，`copy` 任务才会执行，在其他主机上该任务会被跳过。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_107.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_109.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_111.png)

**注意**：在 `when` 语句中，变量名（如 `inventory_hostname`）不需要加引号，但如果其值是字符串（如 `"node1.lab.example.com"`），则需要用引号括起来。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_113.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_115.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_117.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_118.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_120.png)

### 示例二：判断变量是否在列表中

![](img/2ebb5020ed0f2b548bbd79934f462bc2_121.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_123.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_125.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_127.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_128.png)

我们可以检查一个变量的值是否存在于一个预先定义的列表中。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_130.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_132.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_133.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_134.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_135.png)

```yaml
- hosts: all
  vars:
    my_list:
      - redhat
      - ansible
      - test
  tasks:
    - name: 收集事实
      setup:

    - name: 如果变量值在列表中，则创建文件
      copy:
        content: "条件成立时创建的文件\n"
        dest: "/tmp/test2"
      when: ansible_os_family in my_list
```

![](img/2ebb5020ed0f2b548bbd79934f462bc2_137.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_139.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_140.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_142.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_144.png)

这里，我们定义了一个列表变量 `my_list`。`when` 语句检查从事实中收集的 `ansible_os_family`（操作系统家族）是否在 `my_list` 中。如果在，则执行任务。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_145.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_147.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_149.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_151.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_153.png)

**重要提示**：`when` 语句必须与模块参数（如 `copy`）处于同一缩进级别，这是一个常见的错误位置。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_154.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_155.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_156.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_158.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_160.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_162.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_164.png)

## 变量类型与循环、判断的结合使用

![](img/2ebb5020ed0f2b548bbd79934f462bc2_166.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_168.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_169.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_170.png)

在编写复杂剧本时，我们经常需要结合变量、循环和判断。理解不同变量类型的引用方式至关重要。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_172.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_174.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_176.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_178.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_179.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_181.png)

### 列表类型变量

![](img/2ebb5020ed0f2b548bbd79934f462bc2_183.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_185.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_186.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_188.png)

列表类型变量在循环中非常常用。在任务中，我们使用 `loop` 关键字进行循环，并通过 `{{ item }}` 来引用列表中的每一个元素。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_190.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_191.png)

```yaml
- hosts: all
  vars:
    package_list:
      - httpd
      - mariadb-server
      - php
  tasks:
    - name: 安装软件包列表
      yum:
        name: "{{ item }}"
        state: present
      loop: "{{ package_list }}"
```

![](img/2ebb5020ed0f2b548bbd79934f462bc2_193.png)

### 字典类型变量

![](img/2ebb5020ed0f2b548bbd79934f462bc2_195.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_197.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_198.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_200.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_202.png)

当变量是字典（包含键值对）时，循环的写法会有所不同。我们需要使用 `dict2items` 过滤器将字典转换为列表项，然后通过 `item.key` 和 `item.value` 来引用。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_203.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_204.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_206.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_207.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_208.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_209.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_210.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_212.png)

```yaml
- hosts: all
  vars:
    users_info:
      alice:
        shell: /bin/bash
      bob:
        shell: /sbin/nologin
  tasks:
    - name: 创建用户并指定shell
      user:
        name: "{{ item.key }}"
        shell: "{{ item.value.shell }}"
      loop: "{{ users_info | dict2items }}"
```

![](img/2ebb5020ed0f2b548bbd79934f462bc2_213.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_214.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_215.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_216.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_218.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_220.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_222.png)

### 注册变量与循环结合

![](img/2ebb5020ed0f2b548bbd79934f462bc2_223.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_225.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_227.png)

我们可以将循环中每个任务的执行结果注册（register）到一个变量中，然后在后续任务中根据这些结果进行判断和操作。

```yaml
- hosts: all
  tasks:
    - name: 执行一系列命令并注册结果
      shell: "{{ item }}"
      loop:
        - "ls -ld /tmp"
        - "ls -ld /tmp/nonexist"
      ignore_errors: yes
      register: command_results

    - name: 仅输出执行成功的命令结果
      debug:
        var: item.stdout
      loop: "{{ command_results.results }}"
      when: item.rc == 0
```

![](img/2ebb5020ed0f2b548bbd79934f462bc2_229.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_231.png)

在这个例子中：
1.  第一个任务循环执行两条 `shell` 命令，并使用 `register: command_results` 将每次循环的执行结果保存起来。`ignore_errors: yes` 确保即使某条命令失败，剧本也会继续执行。
2.  第二个任务使用 `debug` 模块输出结果。它循环遍历 `command_results.results`（这是一个列表，包含了每次循环的执行详情）。
3.  `when: item.rc == 0` 是一个判断条件。`item.rc` 引用了每次循环执行结果的返回码（Return Code）。只有返回码为 0（表示成功）的项，才会被输出。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_233.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_235.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_236.png)

**关于 `item` 的理解**：在 Ansible 循环中，`item` 是一个特殊变量，代表当前循环迭代中的元素。它的具体内容取决于循环的上下文：
*   在第一个任务的 `loop` 中，`item` 是命令字符串（如 `"ls -ld /tmp"`）。
*   在第二个任务的 `loop` 中，`item` 是第一个任务注册结果 `command_results.results` 列表中的一个字典元素，其中包含了 `rc`（返回码）、`stdout`（标准输出）等信息。
*   在字典循环中，`item` 有 `key` 和 `value` 属性。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_238.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_240.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_242.png)

## 实践练习：安装并测试 HTTP 服务

![](img/2ebb5020ed0f2b548bbd79934f462bc2_244.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_246.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_247.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_248.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_250.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_251.png)

现在，让我们运用所学的循环和判断知识，完成一个综合练习。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_253.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_255.png)

**任务目标**：编写一个剧本，在受管主机上安装 `httpd` 服务，启动并启用它，然后通过一个判断任务来验证服务是否成功启动（例如，检查端口是否监听）。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_257.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_259.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_261.png)

**思路提示**：
1.  使用 `yum` 模块安装 `httpd` 软件包。
2.  使用 `systemd` 模块启动并启用 `httpd` 服务。
3.  使用 `wait_for` 模块或 `shell` 模块执行 `curl` 命令来测试服务，并根据返回结果（返回码或输出内容）使用 `when` 判断是否成功。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_263.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_264.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_266.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_267.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_269.png)

请大家尝试动手编写这个剧本。

![](img/2ebb5020ed0f2b548bbd79934f462bc2_271.png)

![](img/2ebb5020ed0f2b548bbd79934f462bc2_273.png)

---

![](img/2ebb5020ed0f2b548bbd79934f462bc2_275.png)

本节课中我们一起学习了 Ansible 中条件判断 `when` 的用法。我们掌握了如何基于事实变量、自定义变量列表进行判断，以及如何将判断与循环、注册变量结合使用，从而构建出能够智能决策的自动化任务流。理解并熟练运用判断是编写高效、健壮 Ansible 剧本的关键。