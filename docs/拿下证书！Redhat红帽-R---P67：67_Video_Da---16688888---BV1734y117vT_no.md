# Ansible自动化运维：RH294：第5章 判断任务控制

![](img/825d92da9ff257e7f3eb4daf218c305a_0.png)

在本节课中，我们将学习Ansible剧本中的条件判断。通过`when`语句，我们可以根据特定条件来决定是否执行某个任务，从而实现更灵活、更智能的自动化流程。

上一节我们介绍了循环控制，本节中我们来看看如何通过条件判断来控制任务的执行。

## 条件判断基础

![](img/825d92da9ff257e7f3eb4daf218c305a_2.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_3.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_4.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_6.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_8.png)

在Ansible剧本中，条件判断使用`when`关键字实现。`when`语句通常放在任务定义的末尾，用于指定该任务执行的前提条件。

![](img/825d92da9ff257e7f3eb4daf218c305a_10.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_12.png)

以下是一个基本示例，展示了如何根据主机名来决定是否执行任务：

![](img/825d92da9ff257e7f3eb4daf218c305a_14.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_16.png)

```yaml
---
- name: 条件判断示例
  hosts: all
  gather_facts: yes
  tasks:
    - name: 生成测试文件
      copy:
        content: "这是一个条件测试文件\n"
        dest: /tmp/test.txt
      when: inventory_hostname == "node1.lab.example.com"
```

![](img/825d92da9ff257e7f3eb4daf218c305a_18.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_20.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_22.png)

在这个剧本中，`copy`任务**仅当**`inventory_hostname`（一个Ansible魔法变量，代表当前主机的清单名称）等于`"node1.lab.example.com"`时才会执行。对于清单中的其他主机（如`node2`），该任务将被跳过。

![](img/825d92da9ff257e7f3eb4daf218c305a_24.png)

## 判断条件的多种写法

![](img/825d92da9ff257e7f3eb4daf218c305a_26.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_28.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_29.png)

`when`语句支持多种条件表达式，使其功能非常强大。

![](img/825d92da9ff257e7f3eb4daf218c305a_31.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_33.png)

以下是几种常见的条件判断写法：

![](img/825d92da9ff257e7f3eb4daf218c305a_35.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_37.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_39.png)

1.  **检查变量是否存在或为真**：这是最直接的判断方式。
    ```yaml
    when: some_variable is defined
    when: some_variable
    ```

![](img/825d92da9ff257e7f3eb4daf218c305a_41.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_43.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_45.png)

2.  **检查变量是否在列表中**：使用`in`关键字可以判断一个值是否存在于某个列表变量中。
    ```yaml
    vars:
      package_list: [‘httpd‘, ‘nginx‘, ‘ansible‘]
    tasks:
      - name: 安装特定软件包
        yum:
          name: "{{ item }}"
        loop: "{{ package_list }}"
        when: item in [‘httpd‘, ‘ansible‘]
    ```
    这个任务只会安装`package_list`中属于`[‘httpd‘, ‘ansible‘]`列表的软件包。

![](img/825d92da9ff257e7f3eb4daf218c305a_46.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_48.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_50.png)

3.  **结合事实变量进行判断**：可以基于从受管主机收集到的事实（Facts）来做决策。
    ```yaml
    when: ansible_distribution == "RedHat" and ansible_distribution_major_version == "8"
    ```
    这个条件确保任务只在Red Hat Enterprise Linux 8系统上执行。

![](img/825d92da9ff257e7f3eb4daf218c305a_52.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_54.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_55.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_57.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_58.png)

## 条件判断与变量类型

在编写条件时，需要注意变量的类型，因为不同类型的变量引用方式不同。

*   **列表类型变量**：在循环中，使用`item`来引用列表中的当前元素。
*   **字典类型变量**：在循环字典时，需要使用`dict2items`过滤器将字典转换为列表项，然后通过`item.key`和`item.value`来访问键值对。

![](img/825d92da9ff257e7f3eb4daf218c305a_60.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_62.png)

## 条件判断与注册变量结合

![](img/825d92da9ff257e7f3eb4daf218c305a_64.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_66.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_68.png)

`register`关键字用于将任务执行的结果保存到一个变量中。这个结果变量可以用于后续任务的条件判断，实现任务间的逻辑关联。

![](img/825d92da9ff257e7f3eb4daf218c305a_70.png)

以下是一个结合`register`和`when`的典型示例：

```yaml
---
- name: 检查服务并重启
  hosts: web_servers
  tasks:
    - name: 检查HTTPD服务状态
      command: systemctl is-active httpd
      register: httpd_status
      ignore_errors: yes  # 忽略错误，使剧本继续执行

    - name: 如果服务未运行则启动它
      systemd:
        name: httpd
        state: started
      when: httpd_status.rc != 0  # 返回码非0表示服务未运行
```

![](img/825d92da9ff257e7f3eb4daf218c305a_72.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_74.png)

在这个例子中：
1.  第一个任务检查`httpd`服务的状态，并将结果（包括命令的返回码`rc`）注册到变量`httpd_status`中。
2.  第二个任务的条件是`httpd_status.rc != 0`，即只有当第一个任务检查发现服务未运行时，才会执行启动服务的操作。

![](img/825d92da9ff257e7f3eb4daf218c305a_76.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_77.png)

## 实践练习：安装并测试HTTP服务

![](img/825d92da9ff257e7f3eb4daf218c305a_79.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_81.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_83.png)

现在，让我们运用所学的知识来编写一个综合性的剧本。

**任务目标**：在目标主机上安装`httpd`软件包，启动服务，并添加防火墙规则，但**仅当**主机是Red Hat系统时才执行。

以下是实现此目标的剧本步骤：

![](img/825d92da9ff257e7f3eb4daf218c305a_85.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_87.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_89.png)

![](img/825d92da9ff257e7f3eb4daf218c305a_91.png)

```yaml
---
- name: 在RHEL系统上部署HTTP服务
  hosts: all
  become: yes  # 使用特权执行
  gather_facts: yes  # 必须收集事实以判断系统类型

  tasks:
    - name: 安装HTTPD软件包 (仅限RHEL)
      yum:
        name: httpd
        state: present
      when: ansible_os_family == "RedHat"  # 条件判断

    - name: 启动并启用HTTPD服务 (仅限RHEL)
      systemd:
        name: httpd
        state: started
        enabled: yes
      when: ansible_os_family == "RedHat"  # 同样的条件

    - name: 配置防火墙允许HTTP流量 (仅限RHEL)
      firewalld:
        service: http
        permanent: yes
        state: enabled
        immediate: yes
      when: ansible_os_family == "RedHat"  # 同样的条件
```

![](img/825d92da9ff257e7f3eb4daf218c305a_93.png)

**剧本解析**：
*   `gather_facts: yes` 是必须的，因为它会收集`ansible_os_family`等事实变量。
*   三个任务都使用了相同的`when`条件：`ansible_os_family == "RedHat"`。
*   这意味着整个剧本块只会在操作系统家族为“RedHat”（包括RHEL、CentOS、Fedora等）的主机上执行。对于Debian或SUSE系列的主机，所有任务都会被跳过。

![](img/825d92da9ff257e7f3eb4daf218c305a_95.png)

本节课中我们一起学习了Ansible中条件判断`when`的用法。我们掌握了如何根据变量值、主机事实或之前任务的结果来决定任务的执行路径，这是编写智能、健壮自动化剧本的关键技能。通过将条件判断与循环、变量注册等功能结合，你可以构建出能够适应复杂环境需求的强大自动化流程。