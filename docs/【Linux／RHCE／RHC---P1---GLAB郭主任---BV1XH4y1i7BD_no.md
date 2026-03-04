# Linux运维与红帽认证：50：使用Ansible管理用户与身份验证 🔑

在本节课中，我们将学习如何使用Ansible Playbook来管理Linux系统中的用户、组以及配置SSH身份验证。我们将通过一个综合性的任务，涵盖创建用户组、创建用户、部署SSH公钥、配置sudo权限以及优化SSH安全设置。

## 概述

我们将编写一个Ansible Playbook，实现以下核心功能：
1.  创建一个用户组。
2.  创建多个用户，并将他们加入该组。
3.  为每个用户部署其SSH公钥，以实现密钥认证登录。
4.  配置sudo权限，允许该组用户无需密码即可执行特权命令。
5.  修改SSH配置，禁止root用户远程登录以增强安全性。
6.  在修改SSH配置后，自动重启SSH服务。

上一节我们介绍了Ansible的基础模块，本节中我们来看看如何组合这些模块来完成一个实际的系统管理任务。

## 任务分解与实现

![](img/59459e0749f7458e2007f74fbb3ae102_1.png)

以下是实现上述需求的详细步骤，我们将逐一编写Playbook中的每个任务。

![](img/59459e0749f7458e2007f74fbb3ae102_3.png)

### 1. 创建用户组

![](img/59459e0749f7458e2007f74fbb3ae102_5.png)

![](img/59459e0749f7458e2007f74fbb3ae102_7.png)

首先，我们需要创建一个用户组。这里我们使用 `group` 模块。

```yaml
- name: Add group
  group:
    name: webadmin
    state: present
```

![](img/59459e0749f7458e2007f74fbb3ae102_9.png)

### 2. 创建用户并加入组

![](img/59459e0749f7458e2007f74fbb3ae102_11.png)

接下来，我们需要创建用户。用户信息存储在一个变量文件中（例如 `vars/users.yml`），我们将使用循环来批量创建用户，并将他们加入 `webadmin` 组。

```yaml
- name: Create users
  user:
    name: "{{ item.username }}"
    groups: webadmin
  loop: "{{ users }}"
```

### 3. 部署SSH公钥

![](img/59459e0749f7458e2007f74fbb3ae102_13.png)

![](img/59459e0749f7458e2007f74fbb3ae102_15.png)

创建用户后，我们需要为每个用户部署其SSH公钥，以便他们可以通过密钥认证登录。我们使用 `authorized_key` 模块，并同样使用循环来处理多个用户。

![](img/59459e0749f7458e2007f74fbb3ae102_17.png)

```yaml
- name: Add authentication key
  authorized_key:
    user: "{{ item.username }}"
    key: "{{ lookup('file', 'files/{{ item.username }}.key.pub') }}"
  loop: "{{ users }}"
```

![](img/59459e0749f7458e2007f74fbb3ae102_19.png)

### 4. 配置Sudo权限

![](img/59459e0749f7458e2007f74fbb3ae102_21.png)

![](img/59459e0749f7458e2007f74fbb3ae102_23.png)

我们需要配置sudo，使得 `webadmin` 组的成员可以无需密码执行特权命令。这里我们采用在 `/etc/sudoers.d/` 目录下创建独立配置文件的方式，使用 `copy` 模块。

![](img/59459e0749f7458e2007f74fbb3ae102_25.png)

![](img/59459e0749f7458e2007f74fbb3ae102_27.png)

```yaml
- name: Modify sudoers file
  copy:
    content: "%webadmin ALL=(ALL) NOPASSWD: ALL"
    dest: /etc/sudoers.d/webadmin
    mode: '0440'
```

![](img/59459e0749f7458e2007f74fbb3ae102_29.png)

![](img/59459e0749f7458e2007f74fbb3ae102_31.png)

### 5. 禁用Root远程SSH登录

![](img/59459e0749f7458e2007f74fbb3ae102_33.png)

为了系统安全，我们修改SSH服务的配置文件，禁止root用户直接通过SSH远程登录。我们使用 `lineinfile` 模块来精确修改配置文件中的特定行。

![](img/59459e0749f7458e2007f74fbb3ae102_35.png)

```yaml
- name: Disable root remote login
  lineinfile:
    path: /etc/ssh/sshd_config
    regexp: '^PermitRootLogin'
    line: 'PermitRootLogin no'
  notify: Restart SSH service
```

![](img/59459e0749f7458e2007f74fbb3ae102_37.png)

### 6. 重启SSH服务（处理器）

![](img/59459e0749f7458e2007f74fbb3ae102_39.png)

![](img/59459e0749f7458e2007f74fbb3ae102_41.png)

当SSH配置文件被修改后（即上一步任务状态为 `changed` 时），我们需要重启SSH服务以使更改生效。这通过定义一个处理器（handler）来实现。

![](img/59459e0749f7458e2007f74fbb3ae102_43.png)

![](img/59459e0749f7458e2007f74fbb3ae102_45.png)

```yaml
handlers:
  - name: Restart SSH service
    service:
      name: sshd
      state: restarted
```

![](img/59459e0749f7458e2007f74fbb3ae102_47.png)

![](img/59459e0749f7458e2007f74fbb3ae102_49.png)

![](img/59459e0749f7458e2007f74fbb3ae102_51.png)

## 完整的Playbook示例

![](img/59459e0749f7458e2007f74fbb3ae102_53.png)

![](img/59459e0749f7458e2007f74fbb3ae102_55.png)

将以上所有任务整合，并添加必要的Playbook头部信息（如引入变量文件），就构成了完整的解决方案。

```yaml
---
- name: Manage users and authentication
  hosts: webservers
  vars_files:
    - vars/users.yml
  tasks:
    - name: Add group
      group:
        name: webadmin
        state: present

    - name: Create users
      user:
        name: "{{ item.username }}"
        groups: webadmin
      loop: "{{ users }}"

    - name: Add authentication key
      authorized_key:
        user: "{{ item.username }}"
        key: "{{ lookup('file', 'files/{{ item.username }}.key.pub') }}"
      loop: "{{ users }}"

    - name: Modify sudoers file
      copy:
        content: "%webadmin ALL=(ALL) NOPASSWD: ALL"
        dest: /etc/sudoers.d/webadmin
        mode: '0440'

    - name: Disable root remote login
      lineinfile:
        path: /etc/ssh/sshd_config
        regexp: '^PermitRootLogin'
        line: 'PermitRootLogin no'
      notify: Restart SSH service

  handlers:
    - name: Restart SSH service
      service:
        name: sshd
        state: restarted
```

## 验证与执行

![](img/59459e0749f7458e2007f74fbb3ae102_57.png)

![](img/59459e0749f7458e2007f74fbb3ae102_58.png)

![](img/59459e0749f7458e2007f74fbb3ae102_60.png)

编写完成后，可以使用以下命令检查Playbook语法并执行：

![](img/59459e0749f7458e2007f74fbb3ae102_62.png)

```bash
# 检查语法
ansible-playbook users.yml --syntax-check

![](img/59459e0749f7458e2007f74fbb3ae102_64.png)

![](img/59459e0749f7458e2007f74fbb3ae102_66.png)

![](img/59459e0749f7458e2007f74fbb3ae102_68.png)

# 执行Playbook
ansible-playbook users.yml
```

![](img/59459e0749f7458e2007f74fbb3ae102_70.png)

执行后，可以通过Ansible命令验证结果，例如检查用户是否创建成功，或查看SSH配置是否已更改。

```bash
# 检查远程主机上的用户
ansible webservers -a "id user1"

# 检查SSH配置中PermitRootLogin的设置
ansible webservers -a "grep ^PermitRootLogin /etc/ssh/sshd_config"
```

## 总结

![](img/59459e0749f7458e2007f74fbb3ae102_72.png)

![](img/59459e0749f7458e2007f74fbb3ae102_74.png)

本节课中我们一起学习了如何使用Ansible Playbook自动化完成Linux用户与身份验证管理。我们实践了创建组和用户、部署SSH密钥、配置sudo权限以及加强SSH安全策略等一系列操作。这个综合案例演示了如何将多个Ansible模块和功能（如变量、循环、处理器）组合起来，解决实际的系统运维问题，是RHCE认证考核中需要掌握的核心技能之一。