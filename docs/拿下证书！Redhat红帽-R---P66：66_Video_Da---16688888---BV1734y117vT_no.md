# 红帽 RHCE 认证课程：RH294：第5章 - 处理任务失败 🛠️

![](img/15108be02c73bbca5de8473ede88adba_0.png)

在本节课中，我们将学习如何在 Ansible Playbook 中处理任务执行失败的情况。我们将探讨 `notify` 和 `handlers` 的联动机制，以及如何使用 `ignore_errors`、`force_handlers` 和 `failed_when` 等关键字来控制任务流程。

## 概述

Ansible Playbook 默认在某个任务失败时会停止执行。但在实际运维中，我们可能需要更灵活的错误处理方式，例如忽略某些错误、在配置改变后自动重启服务，或者根据自定义条件判断任务是否失败。本节将通过一个部署 HTTPD 服务的完整示例，讲解这些核心概念。

## 使用 `notify` 和 `handlers` 响应变更

上一节我们介绍了任务的基本结构，本节中我们来看看如何让任务在状态改变时触发特定操作。`notify` 和 `handlers` 通常配合使用，用于在任务做出实际改变（`changed` 状态）后，执行后续操作，例如重启服务。

以下是部署 HTTPD 服务的 Playbook 示例，它演示了 `notify` 如何触发 `handler`：

```yaml
---
- name: Deploy HTTPD Service
  hosts: all
  tasks:
    # 1. 安装 HTTPD 服务
    - name: Install HTTPD
      yum:
        name: httpd
        state: present

    # 2. 创建测试页面
    - name: Create test page
      copy:
        content: |
          This is a test page.
        dest: /var/www/html/index.html
        owner: root
        group: root
        mode: '0644'

    # 3. 修改监听端口配置
    - name: Modify configuration to listen on port 80
      lineinfile:
        path: /etc/httpd/conf/httpd.conf
        regexp: '^Listen '
        line: 'Listen 80'
      notify: restart httpd service  # 如果此行配置被修改，则通知 handler

    # 4. 启动 HTTPD 服务
    - name: Start HTTPD service
      service:
        name: httpd
        state: started
        enabled: yes

    # 5. 配置防火墙放行 80 端口
    - name: Configure firewall
      firewalld:
        port: 80/tcp
        permanent: yes
        immediate: yes
        state: enabled

  handlers:
    # 定义被 notify 调用的操作
    - name: restart httpd service
      service:
        name: httpd
        state: restarted
```

![](img/15108be02c73bbca5de8473ede88adba_2.png)

**关键点解析**：
*   `notify: restart httpd service` 位于修改配置的任务中。只有当 `lineinfile` 模块实际改变了配置文件（即从 `ok` 状态变为 `changed` 状态）时，才会发送通知。
*   `handlers` 部分定义了名为 `restart httpd service` 的操作。它只会在收到同名通知时执行，且在所有普通任务执行完毕后**仅运行一次**。
*   如果 Playbook 再次运行且配置未改变，则 `notify` 不会被触发，`handler` 也不会运行。

![](img/15108be02c73bbca5de8473ede88adba_4.png)

![](img/15108be02c73bbca5de8473ede88adba_5.png)

![](img/15108be02c73bbca5de8473ede88adba_6.png)

![](img/15108be02c73bbca5de8473ede88adba_7.png)

## 处理任务失败的其他方法

![](img/15108be02c73bbca5de8473ede88adba_8.png)

![](img/15108be02c73bbca5de8473ede88adba_10.png)

除了响应变更，我们还需要处理任务执行失败的情况。Ansible 提供了多种机制。

![](img/15108be02c73bbca5de8473ede88adba_12.png)

![](img/15108be02c73bbca5de8473ede88adba_13.png)

![](img/15108be02c73bbca5de8473ede88adba_14.png)

![](img/15108be02c73bbca5de8473ede88adba_15.png)

### 忽略错误 (`ignore_errors`)

![](img/15108be02c73bbca5de8473ede88adba_17.png)

![](img/15108be02c73bbca5de8473ede88adba_19.png)

使用 `ignore_errors: yes` 可以让某个任务失败时，Playbook 继续执行后续任务。

![](img/15108be02c73bbca5de8473ede88adba_21.png)

```yaml
- name: This task might fail
  command: /some/nonexistent/command
  ignore_errors: yes
```

![](img/15108be02c73bbca5de8473ede88adba_23.png)

![](img/15108be02c73bbca5de8473ede88adba_24.png)

![](img/15108be02c73bbca5de8473ede88adba_26.png)

### 强制处理程序 (`force_handlers`)

![](img/15108be02c73bbca5de8473ede88adba_28.png)

默认情况下，如果 Playbook 在 `notify` 之后发生错误而中止，已通知的 `handler` 将不会运行。使用 `force_handlers: yes` 可以强制运行已被通知的 `handler`。

![](img/15108be02c73bbca5de8473ede88adba_30.png)

![](img/15108be02c73bbca5de8473ede88adba_31.png)

![](img/15108be02c73bbca5de8473ede88adba_33.png)

![](img/15108be02c73bbca5de8473ede88adba_35.png)

```yaml
---
- hosts: all
  force_handlers: yes
  tasks:
    - name: A task that changes and notifies
      # ... 此任务会触发 notify ...
      notify: my handler

    - name: A subsequent task that fails
      command: /bin/false  # 此任务会失败

  handlers:
    - name: my handler
      # ... handler 定义 ...
```
即使第二个任务失败，第一个任务触发的 `my handler` 仍然会执行。

![](img/15108be02c73bbca5de8473ede88adba_37.png)

![](img/15108be02c73bbca5de8473ede88adba_39.png)

### 自定义失败条件 (`failed_when`)

![](img/15108be02c73bbca5de8473ede88adba_41.png)

`failed_when` 允许你根据任务执行结果（例如命令返回值或输出内容）来定义失败条件。

![](img/15108be02c73bbca5de8473ede88adba_43.png)

以下是使用 `failed_when` 的示例：

```yaml
- name: Check if a file exists
  command: ls /tmp/myfile
  register: file_check
  failed_when: file_check.rc == 0  # 传统上 rc=0 表示成功，这里我们反其道而行之
  ignore_errors: yes  # 通常与 failed_when 配合使用，避免 Playbook 停止

![](img/15108be02c73bbca5de8473ede88adba_45.png)

- name: Create the file if it was “failed“ (i.e., didn‘t exist)
  file:
    path: /tmp/myfile
    state: touch
  when: file_check is failed  # 利用上面定义的“失败”状态作为条件
```

**关键点解析**：
*   第一个任务执行 `ls` 命令，并将结果注册到变量 `file_check`。
*   `failed_when: file_check.rc == 0` 意味着如果文件存在（命令成功，返回码 `rc` 为 0），Ansible 会认为这个任务“失败”了。
*   第二个任务根据第一个任务是否“失败”（即文件不存在）来决定是否创建文件。

### 任务块 (`block`)、救援 (`rescue`) 和总是 (`always`)

`block` 可以将多个任务组合成一个逻辑单元，并与 `rescue` 和 `always` 结合，实现类似编程语言中 `try-catch-finally` 的结构。

以下是任务块的示例：

![](img/15108be02c73bbca5de8473ede88adba_47.png)

```yaml
- name: Handle operation with error recovery
  block:
    - name: This is the main task that might fail
      debug:
        msg: “Doing something critical...“

    - name: Another task in the block
      command: /bin/false  # 这个任务会失败

  rescue:
    - name: This runs only if a task in the block fails
      debug:
        msg: “Caught an error! Performing recovery steps.“

  always:
    - name: This always runs, regardless of success or failure
      debug:
        msg: “This cleanup step always executes.“
```

![](img/15108be02c73bbca5de8473ede88adba_49.png)

**关键点解析**：
*   `block` 中的任务按顺序执行。
*   如果 `block` 中**任何**任务失败，执行将跳转到 `rescue` 部分。
*   无论 `block` 成功还是失败，`always` 部分都会执行。

![](img/15108be02c73bbca5de8473ede88adba_51.png)

## 总结

本节课中我们一起学习了 Ansible 中处理任务失败和响应变更的高级技巧。
1.  我们掌握了 **`notify` 和 `handlers`** 的用法，用于在配置发生变更后自动执行重启等操作。
2.  我们学习了通过 **`ignore_errors`** 忽略非关键错误，保证 Playbook 继续执行。
3.  我们了解了 **`force_handlers`** 选项，确保即使后续任务失败，已通知的处理程序也能执行。
4.  我们探索了 **`failed_when`** 的威力，能够根据自定义条件（如命令返回值）灵活定义任务的成功与失败。
5.  最后，我们认识了 **`block`、`rescue` 和 `always`** 结构，它为复杂的错误处理和资源清理提供了强大的逻辑控制能力。

![](img/15108be02c73bbca5de8473ede88adba_53.png)

这些功能使得 Ansible Playbook 更加健壮和智能，能够适应复杂的自动化运维场景。