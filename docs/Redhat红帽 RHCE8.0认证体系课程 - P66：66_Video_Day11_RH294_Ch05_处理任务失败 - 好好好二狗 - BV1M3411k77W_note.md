# Redhat红帽 RHCE8.0认证体系课程：P66：处理任务失败 🛠️

在本节课中，我们将学习Ansible剧本中处理任务失败的方法，特别是`notify`和`handler`的配合使用，以及如何控制任务在失败时的行为。我们将通过一个部署HTTPD服务的完整例子来理解这些概念。

---

## 概述

上一节我们介绍了Ansible任务的基本结构。本节中，我们来看看当任务执行过程中出现错误或状态改变时，如何通过`notify`和`handler`等机制进行响应和处理。

## 使用 Notify 和 Handler

`notify`和`handler`用于在任务状态发生改变时触发特定的后续操作。其核心思想是：当一个任务（例如修改配置文件）的执行结果发生了**改变**（`changed`状态为`true`），它会`notify`（通知）一个对应的`handler`（处理程序）来执行特定操作（例如重启服务）。

![](img/a46e3e98ad5189863cdace95427f6692_1.png)

以下是编写一个部署HTTPD服务的剧本，并集成`notify`和`handler`的步骤。

![](img/a46e3e98ad5189863cdace95427f6692_3.png)

![](img/a46e3e98ad5189863cdace95427f6692_4.png)

![](img/a46e3e98ad5189863cdace95427f6692_5.png)

### 第一步：安装HTTPD服务

![](img/a46e3e98ad5189863cdace95427f6692_6.png)

![](img/a46e3e98ad5189863cdace95427f6692_7.png)

![](img/a46e3e98ad5189863cdace95427f6692_8.png)

![](img/a46e3e98ad5189863cdace95427f6692_9.png)

![](img/a46e3e98ad5189863cdace95427f6692_11.png)

第一个任务是安装HTTPD软件包。这里使用`yum`模块，`state: present`确保软件包被安装。此任务本身不需要额外判断，因为`yum`模块会处理状态检查。

![](img/a46e3e98ad5189863cdace95427f6692_13.png)

![](img/a46e3e98ad5189863cdace95427f6692_15.png)

```yaml
- name: Install HTTPD service
  yum:
    name: httpd
    state: present
```

![](img/a46e3e98ad5189863cdace95427f6692_16.png)

![](img/a46e3e98ad5189863cdace95427f6692_17.png)

![](img/a46e3e98ad5189863cdace95427f6692_18.png)

![](img/a46e3e98ad5189863cdace95427f6692_19.png)

### 第二步：创建测试页面

![](img/a46e3e98ad5189863cdace95427f6692_21.png)

![](img/a46e3e98ad5189863cdace95427f6692_23.png)

![](img/a46e3e98ad5189863cdace95427f6692_24.png)

接下来，我们创建一个简单的测试页面。使用`copy`模块将内容写入HTTPD的默认文档目录。

![](img/a46e3e98ad5189863cdace95427f6692_26.png)

![](img/a46e3e98ad5189863cdace95427f6692_28.png)

![](img/a46e3e98ad5189863cdace95427f6692_29.png)

![](img/a46e3e98ad5189863cdace95427f6692_30.png)

```yaml
- name: Create test page
  copy:
    content: |
      This is a test page.
    dest: /var/www/html/index.html
    owner: apache
    group: apache
    mode: '0644'
    seuser: system_u
    setype: httpd_sys_content_t
```

![](img/a46e3e98ad5189863cdace95427f6692_31.png)

![](img/a46e3e98ad5189863cdace95427f6692_32.png)

![](img/a46e3e98ad5189863cdace95427f6692_34.png)

### 第三步：修改服务配置

![](img/a46e3e98ad5189863cdace95427f6692_35.png)

![](img/a46e3e98ad5189863cdace95427f6692_36.png)

![](img/a46e3e98ad5189863cdace95427f6692_37.png)

![](img/a46e3e98ad5189863cdace95427f6692_38.png)

然后，我们修改HTTPD的监听端口。使用`lineinfile`模块将配置文件中`Listen`一行的端口改为80。此任务可能改变系统状态，因此我们为其添加了`notify`。

![](img/a46e3e98ad5189863cdace95427f6692_40.png)

![](img/a46e3e98ad5189863cdace95427f6692_42.png)

![](img/a46e3e98ad5189863cdace95427f6692_44.png)

![](img/a46e3e98ad5189863cdace95427f6692_46.png)

```yaml
- name: Modify HTTPD configuration (Listen port)
  lineinfile:
    path: /etc/httpd/conf/httpd.conf
    regexp: '^Listen '
    line: 'Listen 80'
    state: present
  notify: restart httpd service
```

![](img/a46e3e98ad5189863cdace95427f6692_48.png)

![](img/a46e3e98ad5189863cdace95427f6692_49.png)

![](img/a46e3e98ad5189863cdace95427f6692_51.png)

![](img/a46e3e98ad5189863cdace95427f6692_53.png)

**关键点**：`notify`后面的名称必须与后续`handler`中定义的`name`完全一致。

![](img/a46e3e98ad5189863cdace95427f6692_55.png)

### 第四步：启动服务并配置防火墙

![](img/a46e3e98ad5189863cdace95427f6692_57.png)

![](img/a46e3e98ad5189863cdace95427f6692_58.png)

![](img/a46e3e98ad5189863cdace95427f6692_60.png)

启动HTTPD服务，并配置防火墙允许80端口的流量。

![](img/a46e3e98ad5189863cdace95427f6692_62.png)

![](img/a46e3e98ad5189863cdace95427f6692_64.png)

![](img/a46e3e98ad5189863cdace95427f6692_66.png)

```yaml
- name: Start HTTPD service
  service:
    name: httpd
    state: started
    enabled: yes

![](img/a46e3e98ad5189863cdace95427f6692_68.png)

![](img/a46e3e98ad5189863cdace95427f6692_69.png)

![](img/a46e3e98ad5189863cdace95427f6692_71.png)

- name: Configure firewall for HTTPD
  firewalld:
    port: 80/tcp
    permanent: yes
    immediate: yes
    state: enabled
```

![](img/a46e3e98ad5189863cdace95427f6692_73.png)

![](img/a46e3e98ad5189863cdace95427f6692_75.png)

![](img/a46e3e98ad5189863cdace95427f6692_77.png)

![](img/a46e3e98ad5189863cdace95427f6692_78.png)

### 第五步：定义 Handler

![](img/a46e3e98ad5189863cdace95427f6692_80.png)

![](img/a46e3e98ad5189863cdace95427f6692_82.png)

最后，我们定义`handler`。它会在被`notify`通知时执行，用于重启HTTPD服务。`handler`是一个独立的模块，通常写在剧本的末尾。

![](img/a46e3e98ad5189863cdace95427f6692_84.png)

![](img/a46e3e98ad5189863cdace95427f6692_85.png)

![](img/a46e3e98ad5189863cdace95427f6692_87.png)

```yaml
handlers:
  - name: restart httpd service
    service:
      name: httpd
      state: restarted
```

![](img/a46e3e98ad5189863cdace95427f6692_89.png)

**执行逻辑**：只有当`notify`所在的任务（如修改端口）的执行状态为`changed`（即发生了实际改变）时，对应的`handler`才会被触发。如果状态是`ok`（未改变），则`handler`会被跳过。

---

![](img/a46e3e98ad5189863cdace95427f6692_91.png)

## 处理任务失败的其他方法

![](img/a46e3e98ad5189863cdace95427f6692_93.png)

除了`notify`和`handler`，Ansible还提供了其他几种处理任务失败或控制执行流程的方式。

以下是几种常用的任务控制方法：

![](img/a46e3e98ad5189863cdace95427f6692_95.png)

1.  **忽略错误 (`ignore_errors`)**
    在任务中添加`ignore_errors: yes`，即使该任务失败，Ansible也会继续执行后续任务，而不会中止整个剧本。

![](img/a46e3e98ad5189863cdace95427f6692_96.png)

![](img/a46e3e98ad5189863cdace95427f6692_98.png)

![](img/a46e3e98ad5189863cdace95427f6692_100.png)

2.  **强制处理 (`force_handlers`)**
    使用`force_handlers`关键字，可以强制在剧本运行结束时执行所有被通知的`handler`，即使后续任务有失败。但前提是`notify`所在的任务本身必须成功执行并发生了改变。

3.  **定义失败条件 (`failed_when`)**
    通过`failed_when`可以自定义任务失败的条件。默认情况下，一个任务的返回码（rc）非0即视为失败。但我们可以反转或修改这个逻辑。
    ```yaml
    - name: A task that fails only under specific condition
      command: /usr/bin/somecommand
      register: command_result
      failed_when: “‘ERROR‘ in command_result.stdout”
    ```
    上面的例子中，只有当命令输出中包含“ERROR”字样时，任务才被视为失败。

![](img/a46e3e98ad5189863cdace95427f6692_102.png)

![](img/a46e3e98ad5189863cdace95427f6692_103.png)

![](img/a46e3e98ad5189863cdace95427f6692_105.png)

4.  **使用块 (`block`) 进行错误处理**
    `block`允许你将多个任务组合成一个逻辑单元，并与`rescue`和`always`结合，实现类似编程语言中的`try-catch-finally`结构。
    ```yaml
    - block:
        - name: Attempt a task
          debug:
            msg: ‘This task might fail.‘
      rescue:
        - name: Run if the block fails
          debug:
            msg: ‘Rescue task executed.‘
      always:
        - name: Always run this
          debug:
            msg: ‘This always executes.‘
    ```
    *   `block`中的任务会顺序执行。
    *   如果`block`中任何任务失败，则跳转到`rescue`部分执行。
    *   无论`block`成功还是失败，`always`部分都会执行。

![](img/a46e3e98ad5189863cdace95427f6692_107.png)

![](img/a46e3e98ad5189863cdace95427f6692_109.png)

---

![](img/a46e3e98ad5189863cdace95427f6692_111.png)

![](img/a46e3e98ad5189863cdace95427f6692_113.png)

## 总结

本节课中我们一起学习了在Ansible中处理任务失败和状态改变的几种核心机制：
1.  我们通过一个完整的HTTPD部署例子，掌握了如何使用`notify`和`handler`在配置改变后自动重启服务。
2.  我们了解了通过`ignore_errors`忽略非关键错误，保证剧本继续执行。
3.  我们学习了使用`failed_when`来自定义任务的成功与失败条件，提供了更灵活的控制。
4.  最后，我们介绍了使用`block`、`rescue`和`always`来对一组任务进行整体的错误捕获和处理，使剧本更加健壮。

![](img/a46e3e98ad5189863cdace95427f6692_115.png)

![](img/a46e3e98ad5189863cdace95427f6692_117.png)

掌握这些任务控制技巧，能够帮助你编写出更可靠、更智能的自动化运维剧本。