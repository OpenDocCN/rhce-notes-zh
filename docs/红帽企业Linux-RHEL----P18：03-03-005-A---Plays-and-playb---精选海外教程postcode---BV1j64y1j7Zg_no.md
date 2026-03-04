# 红帽企业Linux RHEL 9精通课程：第七部分：创建Ansible剧本和剧本

![](img/fab3645f83a03793eabfb9734dba447d_1.png)

## 概述
在本节课程中，我们将学习Ansible的核心概念，包括常用模块、变量、条件控制、错误处理，并最终将这些知识整合起来，创建一个完整的、用于将系统配置到指定状态的剧本。

---

![](img/fab3645f83a03793eabfb9734dba447d_3.png)

![](img/fab3645f83a03793eabfb9734dba447d_5.png)

![](img/fab3645f83a03793eabfb9734dba447d_7.png)

![](img/fab3645f83a03793eabfb9734dba447d_9.png)

![](img/fab3645f83a03793eabfb9734dba447d_11.png)

![](img/fab3645f83a03793eabfb9734dba447d_13.png)

## 第七部分：03-03-005：常用Ansible模块 🛠️

![](img/fab3645f83a03793eabfb9734dba447d_15.png)

![](img/fab3645f83a03793eabfb9734dba447d_17.png)

上一节我们介绍了Ansible的基础知识，本节中我们来看看一些最常用和重要的Ansible模块。熟练掌握这些模块对于高效使用Ansible至关重要，尤其是在考试环境中，可以节省大量查阅文档的时间。

![](img/fab3645f83a03793eabfb9734dba447d_19.png)

以下是值得了解的常见模块列表：

![](img/fab3645f83a03793eabfb9734dba447d_21.png)

![](img/fab3645f83a03793eabfb9734dba447d_23.png)

![](img/fab3645f83a03793eabfb9734dba447d_25.png)

![](img/fab3645f83a03793eabfb9734dba447d_26.png)

*   **ping模块**：用于验证托管节点是否可访问。它没有必需参数，是运行Ansible即席命令的绝佳用例。
    *   **代码示例**：`ansible all -m ping`
*   **setup模块**：用于收集被管理主机的“事实”（系统信息）。它也没有必需参数。
    *   **代码示例**：`ansible all -m setup -a “filter=ansible_eth0”`
*   **yum模块**：使用Yum包管理器管理软件包。常用参数是`name`（包名）和`state`（状态，如`present`安装或`absent`删除）。
    *   **代码示例**：`ansible ms-pearson -m yum -a “name=httpd state=latest”`
*   **service模块**：控制系统服务。常用参数是`name`（服务名）、`state`（如`started`, `stopped`）和`enabled`（是否开机自启）。
    *   **代码示例**：`ansible ms-pearson -m service -a “name=httpd state=started enabled=yes”`
*   **user模块**：管理用户账户。常用参数包括`name`（用户名）、`state`、`group`（主组）和`shell`。
    *   **代码示例**：`ansible ms-pearson -m user -a “name=pinhead”`
*   **copy模块**：将文件复制到远程主机。常用参数是`src`（源）、`dest`（目标）、`owner`、`group`和`mode`（权限）。
    *   **代码示例**：`ansible ms-pearson -m copy -a “src=/home/cloud_user/secret_file dest=/home/pinhead/ owner=pinhead group=pinhead mode=0644”`
*   **file模块**：管理文件和目录。常用参数是`path`（路径）、`state`（如`file`, `directory`, `touch`, `absent`）、`owner`、`group`和`mode`。
    *   **代码示例**：`ansible ms-pearson -m file -a “path=/home/pinhead/test state=directory owner=pinhead group=pinhead”`
*   **git模块**：与Git仓库交互。常用参数是`repo`（仓库地址）和`dest`（目标目录）。
    *   **代码示例**：`ansible ms-pearson -m git -a “repo=https://github.com/example/repo.git dest=/home/pinhead/ansible”`

![](img/fab3645f83a03793eabfb9734dba447d_28.png)

![](img/fab3645f83a03793eabfb9734dba447d_30.png)

![](img/fab3645f83a03793eabfb9734dba447d_32.png)

除了上述模块，`lineinfile`模块用于管理文件中的行，`command`和`shell`模块用于执行命令。建议多查阅Ansible官方文档的模块索引，以找到最适合任务的模块。

![](img/fab3645f83a03793eabfb9734dba447d_34.png)

---

![](img/fab3645f83a03793eabfb9734dba447d_36.png)

## 第七部分：03-03-006：使用变量检索命令结果 📥

![](img/fab3645f83a03793eabfb9734dba447d_38.png)

了解了常用模块后，我们来看看如何获取并利用任务执行的结果。这通过`register`关键字实现，它可以将任务输出存储为变量。

**核心概念**：在任务中使用 `register: <variable_name>` 来捕获输出。

注册的变量可以在同一剧本的后续任务中被引用。这样做的好处不仅是获取信息，还能基于返回值做出决策。需要注意的是，注册变量仅在当前剧本运行期间对特定主机有效，且不同模块的返回值结构不同。

![](img/fab3645f83a03793eabfb9734dba447d_40.png)

让我们通过一个例子来理解。以下剧本创建一个文件，并将创建过程的详细信息注册到变量`var`中，然后在后续任务中使用这个变量。

![](img/fab3645f83a03793eabfb9734dba447d_42.png)

```yaml
---
- hosts: ms-pearson
  tasks:
    - name: Create a file
      file:
        path: /tmp/testfile
        state: touch
      register: var  # 将文件创建的结果注册到变量‘var’

    - name: Display debug message
      debug:
        msg: “Register output is {{ var }}”  # 使用变量

    - name: Edit file
      lineinfile:
        path: /tmp/testfile
        line: “UID is {{ var.uid }} GID is {{ var.gid }}”  # 使用变量中的特定值
```

![](img/fab3645f83a03793eabfb9734dba447d_44.png)

![](img/fab3645f83a03793eabfb9734dba447d_46.png)

运行此剧本后，`/tmp/testfile`文件中将被写入类似“UID is 1001 GID is 1001”的内容，展示了如何提取和使用注册变量中的具体值。

---

![](img/fab3645f83a03793eabfb9734dba447d_48.png)

## 第七部分：03-03-007：使用条件控制执行流程 ⚙️

![](img/fab3645f83a03793eabfb9734dba447d_50.png)

![](img/fab3645f83a03793eabfb9734dba447d_52.png)

现在我们已经能获取任务结果，接下来学习如何根据条件控制任务的执行。这包括**处理程序**、**when语句**和**循环**。

![](img/fab3645f83a03793eabfb9734dba447d_54.png)

### 处理程序
处理程序是一种特殊的任务，只在被通知时执行。它们通常用于服务重启等操作。使用`notify`关键字来通知处理程序。

**核心概念**：处理程序在任务发生“变更”时被触发，且无论被通知多少次，在剧本的一次运行中只执行一次。

![](img/fab3645f83a03793eabfb9734dba447d_56.png)

![](img/fab3645f83a03793eabfb9734dba447d_58.png)

```yaml
---
- hosts: ms-pearson
  become: yes
  tasks:
    - name: Update httpd.conf
      replace:
        path: /etc/httpd/conf/httpd.conf
        regexp: ‘^ServerAdmin .*$’
        replace: ‘ServerAdmin cloud_user@localhost’
        backup: yes
      notify: restart webserver  # 如果文件被更改，则通知处理程序

  handlers:
    - name: restart webserver  # 定义处理程序
      service:
        name: httpd
        state: restarted
```

### when语句
`when`语句用于在满足特定条件时才执行任务。它支持逻辑运算符（`and`, `or`）、分组和数学比较。

```yaml
---
- hosts: web_servers
  become: yes
  tasks:
    - name: Copy file to specific host
      copy:
        src: /home/cloud_user/index.html
        dest: /var/www/html/index.html
      when: ansible_hostname == “ms-pearson-3c”  # 仅当主机名匹配时执行
```

![](img/fab3645f83a03793eabfb9734dba447d_60.png)

![](img/fab3645f83a03793eabfb9734dba447d_61.png)

![](img/fab3645f83a03793eabfb9734dba447d_63.png)

### 循环
循环用于对列表中的每个项目重复执行任务。推荐使用`loop`关键字。

![](img/fab3645f83a03793eabfb9734dba447d_65.png)

**核心概念**：使用 `loop: [‘item1‘， ’item2‘]` 和 `{{ item }}` 变量进行迭代。

![](img/fab3645f83a03793eabfb9734dba447d_67.png)

```yaml
---
- hosts: web_servers
  become: yes
  tasks:
    - name: Create a list of users
      user:
        name: “{{ item }}”  # ‘item‘ 是循环变量
        state: present
        groups: wheel
      loop:  # 定义要循环的列表
        - violet
        - graham
        - bethany
```

---

![](img/fab3645f83a03793eabfb9734dba447d_69.png)

![](img/fab3645f83a03793eabfb9734dba447d_71.png)

![](img/fab3645f83a03793eabfb9734dba447d_73.png)

## 第七部分：03-03-008：配置错误处理 🚨

在自动化过程中，优雅地处理错误至关重要。Ansible提供了多种错误处理机制。

*   **ignore_errors**：忽略任务失败，继续执行后续任务。
    ```yaml
    tasks:
      - name: Attempt to fetch a file
        fetch:
          src: /tmp/error_file
          dest: /tmp/
        ignore_errors: yes  # 即使文件不存在导致失败，也继续运行
    ```
*   **block, rescue, always**：对任务进行逻辑分组和错误处理。
    *   `block`：定义要执行的主要任务块。
    *   `rescue`：当`block`中的任务失败时执行。
    *   `always`：无论`block`成功还是失败，最后都会执行。
    ```yaml
    tasks:
      - name: Handle errors with block
        block:
          - name: Fetch a file
            fetch:
              src: /tmp/block_file
              dest: /tmp/
        rescue:
          - name: Debug if file missing
            debug:
              msg: “File does not exist on {{ ansible_hostname }}”
        always:
          - name: Always run this
            debug:
              msg: “Playbook execution completed.”
    ```

![](img/fab3645f83a03793eabfb9734dba447d_75.png)

![](img/fab3645f83a03793eabfb9734dba447d_77.png)

---

## 第七部分：03-03-009：创建配置系统的剧本 🚀

最后，我们将综合运用所学知识，创建一个完整的剧本来配置系统。这个剧本包含两个“play”，分别针对Web服务器组和数据库服务器组。

![](img/fab3645f83a03793eabfb9734dba447d_79.png)

![](img/fab3645f83a03793eabfb9734dba447d_81.png)

![](img/fab3645f83a03793eabfb9734dba447d_83.png)

![](img/fab3645f83a03793eabfb9734dba447d_85.png)

![](img/fab3645f83a03793eabfb9734dba447d_87.png)

```yaml
---
# Play 1: 配置Web服务器
- hosts: web_servers
  become: yes
  tasks:
    - name: Install Apache
      yum:
        name: httpd
        state: latest

    - name: Create users and add to apache group
      user:
        name: “{{ item }}”
        group: apache
      loop:
        - will
        - miles

    - name: Create index.html from template
      template:
        src: /home/cloud_user/templates/index.j2
        dest: /var/www/html/index.html
        owner: apache
        group: apache
        mode: ‘0644’

    - name: Start and enable httpd service
      service:
        name: httpd
        state: started
        enabled: yes

![](img/fab3645f83a03793eabfb9734dba447d_89.png)

![](img/fab3645f83a03793eabfb9734dba447d_90.png)

![](img/fab3645f83a03793eabfb9734dba447d_92.png)

![](img/fab3645f83a03793eabfb9734dba447d_94.png)

![](img/fab3645f83a03793eabfb9734dba447d_96.png)

![](img/fab3645f83a03793eabfb9734dba447d_98.png)

![](img/fab3645f83a03793eabfb9734dba447d_100.png)

# Play 2: 配置数据库服务器
- hosts: database_servers
  become: yes
  tasks:
    - name: Install PostgreSQL
      yum:
        name: postgresql-server
        state: latest

    - name: Initialize DB cluster
      command: postgresql-setup --initdb
      become_user: postgres

    - name: Create database users
      user:
        name: “{{ item }}”
        groups: postgres
      loop:
        - cory
        - aaron

    - name: Start and enable postgresql
      service:
        name: postgresql
        state: started
        enabled: yes
```

![](img/fab3645f83a03793eabfb9734dba447d_102.png)

![](img/fab3645f83a03793eabfb9734dba447d_104.png)

![](img/fab3645f83a03793eabfb9734dba447d_106.png)

![](img/fab3645f83a03793eabfb9734dba447d_108.png)

![](img/fab3645f83a03793eabfb9734dba447d_110.png)

这个剧本演示了如何结构化一个复杂的配置任务，涵盖了软件安装、用户管理、文件部署和服务管理。

![](img/fab3645f83a03793eabfb9734dba447d_112.png)

![](img/fab3645f83a03793eabfb9734dba447d_114.png)

## 总结
本节课中我们一起学习了Ansible剧本编写的核心技能。我们从熟悉常用模块开始，掌握了如何使用变量捕获任务结果，进而学习了通过条件语句（处理程序、when、循环）来控制执行流程，并了解了如何配置健壮的错误处理。最后，我们综合这些知识点，创建了一个能够将多台服务器配置到指定状态的完整剧本。这些是使用Ansible进行自动化配置的基石。