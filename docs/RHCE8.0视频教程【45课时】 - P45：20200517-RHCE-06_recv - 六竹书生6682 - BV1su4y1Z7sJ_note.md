# Ansible 角色教程：P45：Ansible 角色详解与自定义

![](img/6cc2d806a58b0994874a7264a74b2a97_0.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_2.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_4.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_6.png)

在本节课中，我们将要学习 Ansible 角色的核心概念、结构以及如何创建和使用自定义角色。我们将从了解系统自带角色开始，逐步深入到手动创建一个完整的 Nginx 管理角色。

![](img/6cc2d806a58b0994874a7264a74b2a97_8.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_10.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_12.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_14.png)

## 角色结构与核心概念

![](img/6cc2d806a58b0994874a7264a74b2a97_16.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_18.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_20.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_22.png)

上一节我们介绍了 Ansible 的基本任务编写，本节中我们来看看如何将任务模块化、组织化，这就是 Ansible 角色的作用。

![](img/6cc2d806a58b0994874a7264a74b2a97_24.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_26.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_28.png)

一个 Ansible 角色是一系列按特定结构组织的目录和文件。其核心思想类似于编程中的模块化，通过一个主文件（`main.yml`）来协调调用各个子任务，使代码更清晰、更易复用。

![](img/6cc2d806a58b0994874a7264a74b2a97_30.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_32.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_34.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_36.png)

以下是 Ansible 角色的标准目录结构及其作用：

![](img/6cc2d806a58b0994874a7264a74b2a97_38.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_40.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_42.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_44.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_46.png)

*   **`defaults/`**: 此目录用于定义角色的**默认变量**。这里定义的变量**优先级最低**，当在其他地方（如 `vars/` 或 Playbook）定义了同名变量时，会被覆盖。
*   **`vars/`**: 此目录用于定义角色的**变量**。这里定义的变量**优先级较高**，会覆盖 `defaults/` 中的变量，但通常仍可被 Playbook 中定义的变量覆盖。
*   **`tasks/`**: 这是角色的核心目录，用于存放定义具体任务的 YAML 文件。通常包含一个 `main.yml` 文件来定义任务的执行顺序。
    ```yaml
    # tasks/main.yml 示例
    - include: group.yml
    - include: user.yml
    - include: install.yml
    ```
*   **`templates/`**: 此目录存放 **Jinja2 模板文件**。这些文件包含变量，在任务执行时会被渲染成具体的配置文件并下发到目标主机。
*   **`files/`**: 此目录存放任务中需要引用的**静态文件**。这些文件在任务执行时会被直接拷贝到目标主机，内容不会改变。
*   **`handlers/`**: 此目录用于定义**触发器**。当某些任务（如配置文件变更）执行后，可以触发这里定义的操作（如重启服务）。
*   **`meta/`**: 此目录包含角色的**元信息**，例如作者、许可证、支持的平台以及角色依赖关系。

![](img/6cc2d806a58b0994874a7264a74b2a97_48.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_50.png)

## 使用系统预置角色

![](img/6cc2d806a58b0994874a7264a74b2a97_52.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_54.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_56.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_58.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_60.png)

在开始创建自定义角色前，我们先了解一下如何获取和使用系统预置的角色。在 RHEL/CentOS 系统中，安装光盘提供了多个官方维护的角色。

![](img/6cc2d806a58b0994874a7264a74b2a97_62.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_64.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_66.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_68.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_70.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_71.png)

首先，需要挂载安装介质并安装包含系统角色的软件包：
```bash
mount /dev/cdrom /mnt
yum install rhel-system-roles
```
安装完成后，角色位于 `/usr/share/ansible/roles/` 目录下。

![](img/6cc2d806a58b0994874a7264a74b2a97_73.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_75.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_77.png)

以下是部分系统角色的简介：

![](img/6cc2d806a58b0994874a7264a74b2a97_79.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_81.png)

*   **`rhel-system-roles.kdump`**: **全面支持**。用于配置系统崩溃时的内核转储服务。
*   **`rhel-system-roles.network`**: **全面支持**。用于配置网络接口。
*   **`rhel-system-roles.postfix`**: **技术预览**。用于配置邮件传输代理（MTA），目前尚不可用于生产。
*   **`rhel-system-roles.selinux`**: **全面支持**。用于管理 SELinux 策略，如端口、布尔值和上下文。
*   **`rhel-system-roles.timesync`**: **全面支持**。用于配置系统时间同步（NTP/Chrony）。
*   **`rhel-system-roles.firewall`** 和 **`rhel-system-roles.tuned`**: 这些角色正在开发中，分别用于配置防火墙和系统性能调优。

![](img/6cc2d806a58b0994874a7264a74b2a97_83.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_85.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_87.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_89.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_91.png)

每个角色目录下通常有一个 `README.md` 文件，详细说明了该角色的可用变量和用法。例如，查看 `timesync` 角色的说明：
```bash
less /usr/share/ansible/roles/rhel-system-roles.timesync/README.md
```

![](img/6cc2d806a58b0994874a7264a74b2a97_93.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_95.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_97.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_99.png)

## 创建自定义 Nginx 管理角色

![](img/6cc2d806a58b0994874a7264a74b2a97_101.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_103.png)

了解了角色的结构后，本节我们将动手创建一个自定义角色，用于自动化部署和管理 Nginx 服务。我们的角色将完成以下任务：
1.  创建专用的 Nginx 用户和组。
2.  安装 Nginx 软件包。
3.  下发自定义的 Nginx 配置文件（使用模板）。
4.  启动并启用 Nginx 服务。

![](img/6cc2d806a58b0994874a7264a74b2a97_105.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_107.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_109.png)

### 第一步：创建角色目录结构

![](img/6cc2d806a58b0994874a7264a74b2a97_111.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_113.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_115.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_117.png)

建议将自定义角色创建在 `/etc/ansible/roles/` 目录下。首先创建角色目录及其子目录：
```bash
mkdir -p /etc/ansible/roles/nginx_new/{tasks,templates}
```
这将创建名为 `nginx_new` 的角色，并包含 `tasks` 和 `templates` 两个必要的子目录。

![](img/6cc2d806a58b0994874a7264a74b2a97_119.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_121.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_123.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_125.png)

### 第二步：编写任务文件 (Tasks)

![](img/6cc2d806a58b0994874a7264a74b2a97_127.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_129.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_131.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_133.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_135.png)

我们进入 `tasks` 目录，为每个子任务创建独立的 YAML 文件。

![](img/6cc2d806a58b0994874a7264a74b2a97_137.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_139.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_141.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_143.png)

1.  **创建用户组 (`group.yml`)**:
    ```yaml
    - name: Create nginx group
      group:
        name: nginx
        gid: 80
        system: yes
    ```

![](img/6cc2d806a58b0994874a7264a74b2a97_145.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_147.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_149.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_151.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_153.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_155.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_157.png)

2.  **创建用户 (`user.yml`)**:
    ```yaml
    - name: Create nginx user
      user:
        name: nginx
        uid: 80
        group: nginx
        system: yes
        shell: /sbin/nologin
    ```

![](img/6cc2d806a58b0994874a7264a74b2a97_159.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_161.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_163.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_165.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_167.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_169.png)

3.  **安装软件包 (`install.yml`)**:
    ```yaml
    - name: Install nginx software package
      yum:
        name: nginx
        state: present
    ```

![](img/6cc2d806a58b0994874a7264a74b2a97_171.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_173.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_175.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_177.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_179.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_181.png)

4.  **启动服务 (`start.yml`)**:
    ```yaml
    - name: Start nginx service
      service:
        name: nginx
        state: started
        enabled: yes
    ```

![](img/6cc2d806a58b0994874a7264a74b2a97_183.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_185.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_187.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_188.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_190.png)

5.  **配置模板下发 (`template.yml`)**:
    ```yaml
    - name: Copy config file via template
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
    ```

![](img/6cc2d806a58b0994874a7264a74b2a97_192.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_194.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_196.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_198.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_200.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_202.png)

### 第三步：创建模板文件 (Template)

![](img/6cc2d806a58b0994874a7264a74b2a97_203.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_205.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_207.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_209.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_210.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_212.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_214.png)

首先，从已安装的 Nginx 中复制默认配置文件作为模板基础：
```bash
cp /etc/nginx/nginx.conf /etc/ansible/roles/nginx_new/templates/nginx.conf.j2
```
然后，编辑这个 Jinja2 模板文件 (`nginx.conf.j2`)，在其中使用变量。例如，我们修改 worker 进程数，使其等于 CPU 核心数加 2：
```jinja2
user nginx;
worker_processes {{ ansible_processor_cores + 2 }};
# ... 文件其他部分保持不变
```
这里 `{{ ansible_processor_cores + 2 }}` 是一个 Jinja2 表达式，它会在角色执行时被替换为实际计算出的值。

![](img/6cc2d806a58b0994874a7264a74b2a97_216.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_218.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_219.png)

### 第四步：编写主任务文件 (tasks/main.yml)

![](img/6cc2d806a58b0994874a7264a74b2a97_221.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_223.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_225.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_227.png)

在 `tasks` 目录下创建 `main.yml` 文件，用于定义所有子任务的执行顺序：
```yaml
- include: group.yml
- include: user.yml
- include: install.yml
- include: template.yml
- include: start.yml
```

![](img/6cc2d806a58b0994874a7264a74b2a97_229.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_230.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_232.png)

### 第五步：编写角色调用 Playbook

![](img/6cc2d806a58b0994874a7264a74b2a97_234.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_235.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_237.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_239.png)

最后，在角色同级目录 (`/etc/ansible/roles/`) 下创建一个 Playbook 来调用我们创建的角色：
```yaml
# nginx_role.yml
- hosts: web_servers
  remote_user: root
  roles:
    - nginx_new
```

![](img/6cc2d806a58b0994874a7264a74b2a97_241.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_243.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_245.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_247.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_249.png)

### 第六步：测试与运行

![](img/6cc2d806a58b0994874a7264a74b2a97_251.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_253.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_255.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_257.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_259.png)

使用 `ansible-playbook` 命令来执行这个角色 Playbook：
```bash
ansible-playbook /etc/ansible/roles/nginx_role.yml
```
执行后，可以登录目标主机验证 Nginx 用户、组、服务状态以及配置文件是否按预期生成。

![](img/6cc2d806a58b0994874a7264a74b2a97_261.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_263.png)

**注意**：在模板中使用数学运算（如 `+2`）时，需要确保 Ansible 能正确获取到变量值并进行计算。如果遇到问题，可能是变量未定义或格式问题，需要检查 `ansible_processor_cores` 事实（fact）的收集情况。

![](img/6cc2d806a58b0994874a7264a74b2a97_265.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_267.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_269.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_271.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_273.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_275.png)

## 总结

![](img/6cc2d806a58b0994874a7264a74b2a97_277.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_279.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_281.png)

![](img/6cc2d806a58b0994874a7264a74b2a97_283.png)

本节课中我们一起学习了 Ansible 角色的核心概念与实战应用。我们首先剖析了角色的标准目录结构，了解了 `defaults`、`vars`、`tasks`、`templates` 等关键目录的作用。接着，我们探索了如何获取和使用系统预置的角色来简化常见系统任务的配置。最后，我们通过一个完整的示例，从零开始创建了一个用于部署和管理 Nginx 服务的自定义角色，涵盖了从规划、编写任务、创建模板到最终整合调用的全过程。掌握角色的创建和使用，是编写高效、可维护 Ansible 自动化代码的关键一步。