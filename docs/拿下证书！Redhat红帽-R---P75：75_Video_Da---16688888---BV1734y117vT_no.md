# 红帽 RHCE 8.0 认证课程：第10章：利用 Ansible 自动化 Linux 运维 🚀

![](img/893ee3f8bd51924c8b66730cc0847e35_0.png)

在本章中，我们将学习如何使用 Ansible 来自动化执行常见的 Linux 系统管理任务。我们将回顾并深入讲解一些核心模块，特别是存储管理模块，帮助你构建高效、可重复的自动化运维流程。

![](img/893ee3f8bd51924c8b66730cc0847e35_2.png)

---

![](img/893ee3f8bd51924c8b66730cc0847e35_4.png)

## 软件包管理 📦

![](img/893ee3f8bd51924c8b66730cc0847e35_6.png)

上一节我们介绍了 Ansible 的基础架构，本节中我们来看看如何使用 Ansible 管理软件包。

![](img/893ee3f8bd51924c8b66730cc0847e35_8.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_9.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_10.png)

### Yum 与 DNF 模块
`yum` 和 `dnf` 模块都用于安装、升级或删除软件包。它们的用法几乎完全相同。

![](img/893ee3f8bd51924c8b66730cc0847e35_12.png)

**示例代码：**
```yaml
- name: 安装软件包
  yum:
    name: httpd
    state: present
```
将上述代码中的 `yum` 替换为 `dnf`，效果完全一致。

### Package Facts 模块
`package_facts` 模块用于收集受管主机上已安装软件包的信息。

![](img/893ee3f8bd51924c8b66730cc0847e35_14.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_16.png)

以下是使用 `package_facts` 的步骤：
1.  使用 `ansible_facts` 收集信息。
2.  通过 `ansible_facts.packages` 列出所有软件包。
3.  可以添加条件来筛选特定软件的版本。

![](img/893ee3f8bd51924c8b66730cc0847e35_18.png)

**示例代码：**
```yaml
- name: 收集软件包信息
  package_facts:
    manager: auto

- name: 显示特定软件版本
  debug:
    msg: "{{ ansible_facts.packages['httpd'][0].version }}"
```
此示例会先列出所有已安装的软件包，然后仅筛选并显示 `httpd` 的版本信息。

![](img/893ee3f8bd51924c8b66730cc0847e35_20.png)

### Package 模块
`package` 模块是一个通用的包管理模块，可以调用系统默认的包管理器（如 `yum` 或 `dnf`）来安装软件。

![](img/893ee3f8bd51924c8b66730cc0847e35_21.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_23.png)

**示例代码：**
```yaml
- name: 使用 package 模块安装软件
  package:
    name: vim
    state: present
```

![](img/893ee3f8bd51924c8b66730cc0847e35_25.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_27.png)

### RPM Key 模块
`rpm_key` 模块用于导入 RPM 包的 GPG 密钥。这通常在部署软件仓库时完成。

![](img/893ee3f8bd51924c8b66730cc0847e35_29.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_31.png)

**示例代码：**
```yaml
- name: 导入 GPG 密钥
  rpm_key:
    key: "http://example.com/RPM-GPG-KEY-example"
    state: present
```

![](img/893ee3f8bd51924c8b66730cc0847e35_33.png)

---

![](img/893ee3f8bd51924c8b66730cc0847e35_35.png)

## 用户与组管理 👥

![](img/893ee3f8bd51924c8b66730cc0847e35_37.png)

在掌握了软件包管理后，我们来看看如何自动化用户和组的管理。

![](img/893ee3f8bd51924c8b66730cc0847e35_39.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_41.png)

### User 模块
`user` 模块用于创建、修改或删除用户账户，功能非常全面。

**示例代码：**
```yaml
- name: 创建用户
  user:
    name: user200
    uid: 2000
    shell: /sbin/nologin
    password: "{{ 'redhat' | password_hash('sha512') }}"
```
此任务会创建一个名为 `user200`、UID 为 2000、使用不可登录 shell 的用户，并为其设置加密密码。

### 生成 SSH 密钥
`user` 模块也可以为用户生成 SSH 密钥对。

![](img/893ee3f8bd51924c8b66730cc0847e35_43.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_45.png)

**示例代码：**
```yaml
- name: 为用户生成 SSH 密钥
  user:
    name: alice
    generate_ssh_key: yes
    ssh_key_bits: 2048
    ssh_key_file: .ssh/id_rsa
```

![](img/893ee3f8bd51924c8b66730cc0847e35_47.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_49.png)

### Group 模块
`group` 模块用于管理用户组。

![](img/893ee3f8bd51924c8b66730cc0847e35_50.png)

**示例代码：**
```yaml
- name: 创建用户组
  group:
    name: developers
    state: present
```

### Known Hosts 模块
`known_hosts` 模块用于在受管主机的 `~/.ssh/known_hosts` 文件中添加或删除主机密钥。

![](img/893ee3f8bd51924c8b66730cc0847e35_52.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_54.png)

**示例代码：**
```yaml
- name: 添加主机密钥到 known_hosts
  known_hosts:
    name: server.example.com
    key: "{{ lookup('file', '/path/to/server_key.pub') }}"
```

### Authorized Key 模块
`authorized_key` 模块用于管理用户的 SSH 授权密钥。

**示例代码：**
```yaml
- name: 添加授权密钥
  authorized_key:
    user: alice
    key: "{{ lookup('url', 'https://github.com/alice.keys') }}"
    state: present
```

![](img/893ee3f8bd51924c8b66730cc0847e35_56.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_58.png)

---

## 进程与计划任务管理 ⏰

![](img/893ee3f8bd51924c8b66730cc0847e35_60.png)

用户管理完成后，我们进入进程和计划任务的管理，这是系统自动化的重要部分。

![](img/893ee3f8bd51924c8b66730cc0847e35_62.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_63.png)

### At 模块
`at` 模块用于安排一次性任务。

![](img/893ee3f8bd51924c8b66730cc0847e35_65.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_66.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_68.png)

以下是 `at` 模块的关键参数：
*   `command`: 要执行的命令。
*   `count`: 延迟时间（单位由 `units` 指定）。
*   `script_file`: 要执行的脚本文件。
*   `state`: 任务状态。
*   `unique`: 是否防止重复执行。
*   `units`: 时间单位（如 minutes）。
*   `name`: 任务名称。

![](img/893ee3f8bd51924c8b66730cc0847e35_70.png)

**示例代码：**
```yaml
- name: 安排一次性任务
  at:
    name: adduser201
    command: "useradd user201"
    count: 2
    units: minutes
    unique: yes
```
执行后，可以使用 `atq` 命令查看已安排的任务。

![](img/893ee3f8bd51924c8b66730cc0847e35_72.png)

### Cron 模块
`cron` 模块用于管理周期性计划任务（cron jobs）。

![](img/893ee3f8bd51924c8b66730cc0847e35_74.png)

**示例代码：**
```yaml
- name: 添加计划任务
  cron:
    name: "Backup web directory"
    user: root
    minute: "0"
    hour: "1"
    job: "tar -czf /backup/web.tar.gz /var/www/html"
```
此任务会在每天凌晨 1 点以 root 用户身份执行备份。

![](img/893ee3f8bd51924c8b66730cc0847e35_76.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_78.png)

### Service 与 Systemd 模块
`service` 和 `systemd` 模块用于管理系统服务。`systemd` 模块功能更全，例如允许重新加载守护进程。

![](img/893ee3f8bd51924c8b66730cc0847e35_80.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_82.png)

**示例代码：**
```yaml
- name: 启动并启用服务
  systemd:
    name: httpd
    state: started
    enabled: yes
```

![](img/893ee3f8bd51924c8b66730cc0847e35_84.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_85.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_86.png)

### Reboot 模块
`reboot` 模块用于安全地重启受管主机，并可在重启后继续执行 Playbook。

![](img/893ee3f8bd51924c8b66730cc0847e35_88.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_89.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_91.png)

**示例代码：**
```yaml
- name: 重启主机
  reboot:
    reboot_timeout: 600
```

![](img/893ee3f8bd51924c8b66730cc0847e35_92.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_94.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_95.png)

### Command 与 Shell 模块
`command` 和 `shell` 模块用于执行命令。`command` 模块更安全，不支持管道符等 Shell 特性；`shell` 模块则支持完整的 Shell 功能。

**示例代码：**
```yaml
- name: 使用 command 执行命令
  command: ls -la /tmp

![](img/893ee3f8bd51924c8b66730cc0847e35_97.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_99.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_100.png)

- name: 使用 shell 执行复杂命令
  shell: cat /var/log/messages | grep error
```

![](img/893ee3f8bd51924c8b66730cc0847e35_102.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_103.png)

### Ansible 环境变量
可以通过 `ansible_env` 事实来访问受管主机上的环境变量。

**示例代码：**
```yaml
- name: 打印所有环境变量
  debug:
    var: ansible_env
```
也可以使用 `lookup` 插件来获取变量。

![](img/893ee3f8bd51924c8b66730cc0847e35_105.png)

**示例代码：**
```yaml
- name: 使用 lookup 获取环境变量
  debug:
    msg: "{{ lookup('env', 'HOME') }}"
```

![](img/893ee3f8bd51924c8b66730cc0847e35_106.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_108.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_110.png)

---

## 存储管理 💾

![](img/893ee3f8bd51924c8b66730cc0847e35_112.png)

系统管理离不开存储。接下来，我们将探讨如何使用 Ansible 自动化磁盘分区、逻辑卷和文件系统的管理。

![](img/893ee3f8bd51924c8b66730cc0847e35_114.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_116.png)

### Parted 模块
`parted` 模块用于磁盘分区，是 `fdisk` 或 `parted` 命令的 Ansible 替代品。

![](img/893ee3f8bd51924c8b66730cc0847e35_118.png)

**示例代码：**
```yaml
- name: 创建分区
  parted:
    device: /dev/vdb
    number: 1
    state: present
    part_end: 10GiB
```
此任务在 `/dev/vdb` 上创建一个大小为 10GB 的分区。

![](img/893ee3f8bd51924c8b66730cc0847e35_120.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_122.png)

### Lvg 与 Lvol 模块
`lvg` 模块用于管理卷组（VG），`lvol` 模块用于管理逻辑卷（LV）。

![](img/893ee3f8bd51924c8b66730cc0847e35_124.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_126.png)

以下是创建物理卷、卷组和逻辑卷的步骤：
1.  使用 `lvg` 创建卷组。
2.  使用 `lvol` 在卷组上创建逻辑卷。

![](img/893ee3f8bd51924c8b66730cc0847e35_128.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_130.png)

**示例代码：**
```yaml
- name: 创建卷组
  lvg:
    vg: myvg
    pvs: /dev/vdb1
    state: present

- name: 创建逻辑卷
  lvol:
    vg: myvg
    lv: mylv
    size: 2g
    state: present
```

![](img/893ee3f8bd51924c8b66730cc0847e35_132.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_134.png)

### Filesystem 模块
`filesystem` 模块用于创建或调整文件系统，相当于 `mkfs` 命令。

**示例代码：**
```yaml
- name: 创建 XFS 文件系统
  filesystem:
    fstype: xfs
    dev: /dev/myvg/mylv
```

### Mount 模块
`mount` 模块用于在 `/etc/fstab` 中配置持久化挂载。

![](img/893ee3f8bd51924c8b66730cc0847e35_136.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_138.png)

**示例代码：**
```yaml
- name: 永久挂载文件系统
  mount:
    path: /data
    src: UUID=xxxx-xxxx-xxxx
    fstype: xfs
    state: present
```

### 交换分区管理
目前 Ansible 没有专门的交换分区模块。一个可行的实现思路是：先使用 `lvol` 创建逻辑卷，然后使用 `command` 或 `shell` 模块执行 `mkswap` 和 `swapon` 命令。

![](img/893ee3f8bd51924c8b66730cc0847e35_140.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_142.png)

---

![](img/893ee3f8bd51924c8b66730cc0847e35_144.png)

![](img/893ee3f8bd51924c8b66730cc0847e35_145.png)

## 网络与防火墙管理 🌐

最后，我们来看网络配置和防火墙管理。虽然 Ansible 的网络管理模块仍在发展中，但已有一些可用的工具。

![](img/893ee3f8bd51924c8b66730cc0847e35_147.png)

### 网络角色（RHEL System Roles）
可以使用 `rhel-system-roles.network` 角色来配置网络，但其功能尚在完善中。

![](img/893ee3f8bd51924c8b66730cc0847e35_149.png)

### Nmcli 模块
`nmcli` 模块是对 `nmcli` 命令行工具的封装，用于配置网络连接。

**示例代码：**
```yaml
- name: 配置网络连接
  nmcli:
    conn_name: eth0
    ifname: eth0
    type: ethernet
    ip4: 192.168.1.100/24
    gw4: 192.168.1.1
    state: present
```

![](img/893ee3f8bd51924c8b66730cc0847e35_151.png)

### Hostname 模块
`hostname` 模块用于修改受管主机的主机名。

![](img/893ee3f8bd51924c8b66730cc0847e35_153.png)

**示例代码：**
```yaml
- name: 修改主机名
  hostname:
    name: new-server-name
```

### Firewalld 模块
`firewalld` 模块用于配置防火墙规则，功能强大。

**示例代码：**
```yaml
- name: 在 public 区域开放 HTTP 服务
  firewalld:
    service: http
    zone: public
    permanent: yes
    state: enabled
    immediate: yes
```

---

## 总结 📝

在本节课中，我们一起学习了如何使用 Ansible 自动化执行广泛的 Linux 系统管理任务。我们从软件包管理开始，逐步深入到用户、进程、存储以及网络和防火墙的自动化配置。我们重点讲解了 `parted`、`lvg`、`lvol`、`filesystem` 等存储管理模块，并了解了如何通过 Playbook 将复杂的运维操作标准化、自动化。

![](img/893ee3f8bd51924c8b66730cc0847e35_155.png)

通过本章的学习，你应该能够运用 Ansible 来高效管理多台 Linux 服务器，为应对大规模运维场景打下坚实基础。建议结合课后练习巩固所学知识，将理论转化为实践能力。