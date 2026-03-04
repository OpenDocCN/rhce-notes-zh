# Ansible 角色管理：P45：自定义角色详解 🎭

![](img/50e1fde6bd44518bea22bf919d035d2b_0.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_2.png)

在本节课中，我们将要学习 Ansible 角色的核心概念、结构以及如何从零开始创建一个自定义角色。我们将通过一个管理 Nginx 服务的完整示例，来理解角色的各个组成部分及其协作方式。

![](img/50e1fde6bd44518bea22bf919d035d2b_4.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_6.png)

## 概述

![](img/50e1fde6bd44518bea22bf919d035d2b_8.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_10.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_12.png)

Ansible 角色是一种将 Playbook 内容模块化、结构化的方法。它通过预定义的目录结构来组织变量、任务、模板等，使得自动化脚本更易于复用和维护。本节我们将深入探讨角色的内部结构，并动手创建一个自定义角色。

![](img/50e1fde6bd44518bea22bf919d035d2b_14.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_16.png)

---

![](img/50e1fde6bd44518bea22bf919d035d2b_18.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_20.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_22.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_24.png)

## 角色结构解析

![](img/50e1fde6bd44518bea22bf919d035d2b_26.png)

上一节我们介绍了如何使用现有的角色，本节中我们来看看角色在系统内部的具体结构是怎样的。

![](img/50e1fde6bd44518bea22bf919d035d2b_28.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_30.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_32.png)

在 Ansible 中，一个角色由一系列特定目录和文件组成。我们可以通过查看已安装的角色来了解其结构。

![](img/50e1fde6bd44518bea22bf919d035d2b_34.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_36.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_38.png)

```bash
ls /usr/share/ansible/roles/
```

![](img/50e1fde6bd44518bea22bf919d035d2b_40.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_42.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_44.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_46.png)

每个角色目录下通常包含以下核心目录：

![](img/50e1fde6bd44518bea22bf919d035d2b_48.png)

*   **`tasks/`**: 存放角色执行的主要任务列表。
*   **`templates/`**: 存放使用 Jinja2 模板语言的配置文件模板。
*   **`vars/`**: 存放角色使用的变量定义，优先级较高。
*   **`defaults/`**: 存放角色的默认变量，优先级最低。
*   **`meta/`**: 存放角色的元数据，如作者、许可证、依赖平台等信息。
*   **`handlers/`**: 存放由任务通知触发的处理器。
*   **`files/`**: 存放任务中需要引用的静态文件。

![](img/50e1fde6bd44518bea22bf919d035d2b_50.png)

这些目录通过一个名为 `main.yml` 的协调文件来组织调用顺序，其思想类似于 C 语言中的 `main` 函数调用其他模块。

![](img/50e1fde6bd44518bea22bf919d035d2b_52.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_54.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_56.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_58.png)

---

![](img/50e1fde6bd44518bea22bf919d035d2b_60.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_62.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_64.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_66.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_68.png)

## 变量优先级

![](img/50e1fde6bd44518bea22bf919d035d2b_70.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_71.png)

在角色中定义变量时，需要注意其优先级，这决定了最终生效的值。

![](img/50e1fde6bd44518bea22bf919d035d2b_73.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_75.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_77.png)

以下是变量定义的优先级顺序（从低到高）：

![](img/50e1fde6bd44518bea22bf919d035d2b_79.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_81.png)

1.  **`defaults/main.yml`**： 这里定义的变量拥有**最低**的优先级。如果在 Playbook 中定义了同名变量，将会覆盖这里的值。
2.  **`vars/main.yml`**： 这里定义的变量拥有**较高**的优先级。即使在 Playbook 中定义了同名变量，也会优先使用这里定义的值。

![](img/50e1fde6bd44518bea22bf919d035d2b_83.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_85.png)

简单来说，`vars/` 目录下的变量会覆盖 `defaults/` 目录下的变量，而 Playbook 中定义的变量可以覆盖 `defaults/` 的变量，但通常无法覆盖 `vars/` 的变量。

![](img/50e1fde6bd44518bea22bf919d035d2b_87.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_89.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_91.png)

---

![](img/50e1fde6bd44518bea22bf919d035d2b_93.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_95.png)

## 系统预置角色

![](img/50e1fde6bd44518bea22bf919d035d2b_97.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_99.png)

Ansible 系统本身和红帽发行版提供了一些预置的系统角色，可以直接使用，无需从网络下载。

![](img/50e1fde6bd44518bea22bf919d035d2b_101.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_103.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_105.png)

首先，需要挂载系统镜像并安装包含这些角色的软件包：

![](img/50e1fde6bd44518bea22bf919d035d2b_107.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_109.png)

```bash
mount /dev/cdrom /mnt
yum install rhel-system-roles
```

![](img/50e1fde6bd44518bea22bf919d035d2b_111.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_113.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_115.png)

安装完成后，可以在 `/usr/share/ansible/roles/` 目录下找到这些角色。

![](img/50e1fde6bd44518bea22bf919d035d2b_117.png)

以下是部分系统角色的简介：

![](img/50e1fde6bd44518bea22bf919d035d2b_119.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_121.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_123.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_125.png)

*   **`kdump`**: **全面支持**。用于配置系统崩溃恢复服务。
*   **`network`**: **全面支持**。用于配置网络接口。
*   **`postfix`**: **技术预览**。用于配置邮件传输代理（MTA）。
*   **`selinux`**: **全面支持**。用于管理 SELinux 端口、布尔值和上下文。
*   **`timesync`**: **全面支持**。用于配置系统时间同步（NTP/Chrony）。

![](img/50e1fde6bd44518bea22bf919d035d2b_127.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_129.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_131.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_133.png)

每个角色目录下通常有一个 `README.md` 文件，详细说明了该角色的使用方法和可配置变量。例如，查看 `timesync` 角色的帮助信息：

![](img/50e1fde6bd44518bea22bf919d035d2b_135.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_137.png)

```bash
less /usr/share/ansible/roles/rhel-system-roles.timesync/README.md
```

![](img/50e1fde6bd44518bea22bf919d035d2b_139.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_141.png)

---

![](img/50e1fde6bd44518bea22bf919d035d2b_143.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_145.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_147.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_149.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_151.png)

## 实战：创建自定义 Nginx 角色

![](img/50e1fde6bd44518bea22bf919d035d2b_153.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_155.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_157.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_159.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_161.png)

了解了角色的结构后，我们动手创建一个自定义角色，用于自动化部署和配置 Nginx 服务。该角色将完成以下任务：
1.  创建运行 Nginx 所需的用户和组。
2.  安装 Nginx 软件包。
3.  下发自定义的 Nginx 配置文件模板。
4.  启动并启用 Nginx 服务。

![](img/50e1fde6bd44518bea22bf919d035d2b_163.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_165.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_167.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_169.png)

### 步骤 1：创建角色目录结构

![](img/50e1fde6bd44518bea22bf919d035d2b_171.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_173.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_175.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_177.png)

建议在 `/etc/ansible/roles/` 目录下创建角色。首先，创建角色目录及其子目录。

![](img/50e1fde6bd44518bea22bf919d035d2b_179.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_181.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_183.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_185.png)

```bash
mkdir -p /etc/ansible/roles/nginx_new/{tasks,templates}
```

![](img/50e1fde6bd44518bea22bf919d035d2b_187.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_188.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_190.png)

### 步骤 2：编写任务文件

![](img/50e1fde6bd44518bea22bf919d035d2b_192.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_194.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_196.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_198.png)

进入 `tasks/` 目录，创建各个子任务文件。

![](img/50e1fde6bd44518bea22bf919d035d2b_200.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_202.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_203.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_205.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_207.png)

**1. 创建组 (`group.yml`)**：
```yaml
- name: Create nginx group
  group:
    name: nginx
    gid: 80
    system: yes
```

![](img/50e1fde6bd44518bea22bf919d035d2b_209.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_210.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_212.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_214.png)

**2. 创建用户 (`user.yml`)**：
```yaml
- name: Create nginx user
  user:
    name: nginx
    uid: 80
    group: nginx
    system: yes
    shell: /sbin/nologin
```

![](img/50e1fde6bd44518bea22bf919d035d2b_216.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_218.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_219.png)

**3. 安装软件包 (`install.yml`)**：
```yaml
- name: Install nginx software package
  yum:
    name: nginx
    state: present
```

![](img/50e1fde6bd44518bea22bf919d035d2b_221.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_223.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_225.png)

**4. 启动服务 (`start.yml`)**：
```yaml
- name: Start nginx service
  service:
    name: nginx
    state: started
    enabled: yes
```

![](img/50e1fde6bd44518bea22bf919d035d2b_227.png)

**5. 配置模板文件 (`template.yml`)**：
首先，需要准备模板文件。从已安装的 Nginx 中复制默认配置文件到 `templates/` 目录。
```bash
cp /etc/nginx/nginx.conf /etc/ansible/roles/nginx_new/templates/nginx.conf.j2
```
然后编辑此模板文件，例如，根据 CPU 核心数动态设置工作进程数：
```jinja2
worker_processes {{ ansible_processor_vcpus + 2 }};
```
接着，编写任务来下发此模板：
```yaml
- name: Copy config file
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
```

![](img/50e1fde6bd44518bea22bf919d035d2b_229.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_230.png)

### 步骤 3：编写主任务协调文件

![](img/50e1fde6bd44518bea22bf919d035d2b_232.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_234.png)

在 `tasks/` 目录下创建 `main.yml` 文件，定义任务执行的顺序。
```yaml
- include_tasks: group.yml
- include_tasks: user.yml
- include_tasks: install.yml
- include_tasks: template.yml
- include_tasks: start.yml
```

![](img/50e1fde6bd44518bea22bf919d035d2b_235.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_237.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_239.png)

### 步骤 4：编写角色调用 Playbook

![](img/50e1fde6bd44518bea22bf919d035d2b_241.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_243.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_245.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_247.png)

在角色同级目录 (`/etc/ansible/roles/`) 下，创建调用该角色的 Playbook 文件，例如 `nginx_role.yml`。
```yaml
- hosts: web2012
  remote_user: root
  roles:
    - nginx_new
```

![](img/50e1fde6bd44518bea22bf919d035d2b_249.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_251.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_253.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_255.png)

### 步骤 5：执行与测试

![](img/50e1fde6bd44518bea22bf919d035d2b_257.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_259.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_261.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_263.png)

运行 Playbook 来应用角色：
```bash
ansible-playbook /etc/ansible/roles/nginx_role.yml
```
执行完成后，可以在目标主机上验证 Nginx 用户、组、服务状态以及配置文件是否已按模板生成。

**注意**：在模板中使用变量运算（如 `{{ ansible_processor_vcpus + 2 }}`）时，需确保语法正确，否则可能导致服务启动失败。这是一个常见的调试点。

![](img/50e1fde6bd44518bea22bf919d035d2b_265.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_267.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_269.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_271.png)

---

![](img/50e1fde6bd44518bea22bf919d035d2b_273.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_275.png)

## 总结

![](img/50e1fde6bd44518bea22bf919d035d2b_277.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_279.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_281.png)

![](img/50e1fde6bd44518bea22bf919d035d2b_283.png)

本节课中我们一起学习了 Ansible 角色的核心知识。我们首先解析了角色的标准目录结构及其作用，明确了变量定义的优先级规则。然后，我们介绍了系统预置的角色及其用途。最后，通过一个完整的“创建自定义 Nginx 角色”的实战练习，我们掌握了从规划、编写任务、定义执行顺序到最终调用的全流程。角色化是编写可维护、可复用 Ansible 代码的关键，熟练掌握它将极大提升自动化工程效率。