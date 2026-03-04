# 红帽企业Linux RHEL 9精通课程：P19：03-03-006 Ansible模块 - 系统管理任务

![](img/db670d2e3c938680adf1ef5cf2b970da_1.png)

## 概述
在本节课中，我们将学习如何使用Ansible模块执行常见的系统管理任务。我们将涵盖软件包管理、服务控制、防火墙配置、存储管理、文件操作、任务调度、SELinux管理以及用户和组管理。每个模块都配有清晰的示例，帮助你快速上手。

![](img/db670d2e3c938680adf1ef5cf2b970da_3.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_5.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_7.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_9.png)

---

## 软件包与存储库管理 🗃️

![](img/db670d2e3c938680adf1ef5cf2b970da_11.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_13.png)

上一节我们介绍了Ansible的基础知识，本节中我们来看看如何使用Ansible管理软件包和存储库。

![](img/db670d2e3c938680adf1ef5cf2b970da_15.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_17.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_19.png)

### Yum模块
`yum`模块使用Yum包管理器在RHEL系统上安装、升级、降级、删除和列出软件包。

![](img/db670d2e3c938680adf1ef5cf2b970da_21.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_22.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_24.png)

以下是使用该模块的几种方式：

![](img/db670d2e3c938680adf1ef5cf2b970da_26.png)

*   **安装单个软件包**：使用`state: latest`确保安装最新版本，或使用`state: present`仅确保软件包已安装。
    ```yaml
    - name: 安装一个软件包
      yum:
        name: httpd
        state: latest
    ```
*   **安装软件包列表**：直接在`name`下列出多个软件包比使用循环更高效。
    ```yaml
    - name: 安装软件包列表
      yum:
        name:
          - httpd
          - vsftpd
          - tmux
          - firewalld
        state: latest
    ```
*   **从远程URL安装RPM**：指定RPM文件的URL。
    ```yaml
    - name: 从远程仓库安装RPM
      yum:
        name: http://example.com/package.rpm
        state: present
    ```
*   **从本地文件安装RPM**：提供本地RPM文件的路径。
    ```yaml
    - name: 从本地文件安装RPM
      yum:
        name: /path/to/package.rpm
        state: present
    ```
*   **删除软件包**：使用`state: absent`。
    ```yaml
    - name: 删除软件包
      yum:
        name: httpd
        state: absent
    ```

![](img/db670d2e3c938680adf1ef5cf2b970da_28.png)

**注意**：`yum`模块也支持安装软件包组，需要在组名前加上`@`符号。虽然RHEL 8及以上版本使用DNF作为后端，但`yum`模块对于基本操作仍然有效。

![](img/db670d2e3c938680adf1ef5cf2b970da_30.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_31.png)

### Yum存储库模块
`yum_repository`模块用于添加或删除Yum存储库。

![](img/db670d2e3c938680adf1ef5cf2b970da_33.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_35.png)

以下是该模块的使用示例：

![](img/db670d2e3c938680adf1ef5cf2b970da_37.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_39.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_41.png)

*   **添加存储库**：指定名称、描述、基础URL和GPG检查。
    ```yaml
    - name: 添加存储库
      yum_repository:
        name: app_repo
        description: 'Apple Repository'
        baseurl: 'http://example.com/repo'
        gpgcheck: no
        state: present
    ```
*   **删除存储库**：指定存储库名称和状态。
    ```yaml
    - name: 删除存储库
      yum_repository:
        name: app_repo
        state: absent
    ```

**重要提示**：当使用存储库模块时，建议将`description`和`baseurl`等参数的值用单引号括起来，以避免因空格或特殊字符导致的问题。

---

![](img/db670d2e3c938680adf1ef5cf2b970da_43.png)

## 服务管理 ⚙️

![](img/db670d2e3c938680adf1ef5cf2b970da_45.png)

上一节我们学习了软件包管理，本节中我们来看看如何控制系统服务。

![](img/db670d2e3c938680adf1ef5cf2b970da_47.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_49.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_51.png)

### 服务模块
`service`模块用于控制远程主机上的服务，支持多种初始化系统（如systemd、SysV init等）。

![](img/db670d2e3c938680adf1ef5cf2b970da_53.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_55.png)

以下是使用`service`模块的示例：
```yaml
- name: 启动并启用httpd服务
  service:
    name: httpd
    state: started
    enabled: yes
```

### Systemd模块
`systemd`模块是专门为控制systemd服务而设计的，提供了一些特定于systemd的选项。

以下是使用`systemd`模块的示例：
```yaml
- name: 重启并启用firewalld服务
  systemd:
    name: firewalld
    state: restarted
    enabled: yes
    daemon_reload: yes
```

![](img/db670d2e3c938680adf1ef5cf2b970da_57.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_59.png)

**选择建议**：如果只需要基本的启动、停止、启用功能，使用`service`模块即可。如果需要利用`daemon_reload`、`scope`等systemd特定参数，则使用`systemd`模块。

![](img/db670d2e3c938680adf1ef5cf2b970da_61.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_63.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_65.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_67.png)

---

## 防火墙规则管理 🔥

上一节我们介绍了服务控制，本节中我们来看看如何管理防火墙规则。

![](img/db670d2e3c938680adf1ef5cf2b970da_69.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_71.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_72.png)

### Firewalld模块
`firewalld`模块用于与Firewalld交互，可以添加或删除运行中或永久的防火墙规则、端口。

![](img/db670d2e3c938680adf1ef5cf2b970da_74.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_76.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_78.png)

以下是该模块的使用示例：

![](img/db670d2e3c938680adf1ef5cf2b970da_80.png)

*   **按服务添加规则**：
    ```yaml
    - name: 为HTTP和HTTPS添加防火墙规则
      firewalld:
        zone: public
        service: "{{ item }}"
        permanent: yes
        immediate: yes
        state: enabled
      loop:
        - http
        - https
    ```
*   **按端口添加规则**：
    ```yaml
    - name: 为端口8080-8084添加防火墙规则
      firewalld:
        port: 8080-8084/tcp
        permanent: yes
        immediate: yes
        state: enabled
    ```
*   **使用富规则**：
    ```yaml
    - name: 添加端口转发富规则
      firewalld:
        rich_rule: 'rule family="ipv4" forward-port port="443" protocol="tcp" to-port="8443"'
        permanent: yes
        immediate: yes
        state: enabled
    ```

**重要提示**：要删除规则，需将`state`参数设置为`disabled`，而不是`absent`。`absent`状态仅用于区域级别的操作。

![](img/db670d2e3c938680adf1ef5cf2b970da_82.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_83.png)

---

![](img/db670d2e3c938680adf1ef5cf2b970da_85.png)

## 存储设备管理 💾

![](img/db670d2e3c938680adf1ef5cf2b970da_87.png)

上一节我们配置了防火墙，本节中我们来看看如何管理存储设备，包括分区和逻辑卷。

![](img/db670d2e3c938680adf1ef5cf2b970da_89.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_90.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_92.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_94.png)

### Parted模块
`parted`模块使用`parted`命令行工具配置块设备分区。

![](img/db670d2e3c938680adf1ef5cf2b970da_96.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_98.png)

以下是使用`parted`模块创建分区的示例：
```yaml
- name: 在设备上创建LVM分区
  parted:
    device: "{{ item }}"
    number: 1
    state: present
    part_end: 1GiB
    label: msdos
    flags: [ lvm ]
  loop:
    - /dev/nvme1n1
    - /dev/nvme2n1
```

![](img/db670d2e3c938680adf1ef5cf2b970da_100.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_102.png)

### LVG模块
`lvg`模块用于创建、删除卷组和调整其大小。如果指定的物理卷不存在，模块会自动创建。

![](img/db670d2e3c938680adf1ef5cf2b970da_104.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_106.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_108.png)

以下是使用`lvg`模块的示例：
```yaml
- name: 创建卷组
  lvg:
    pvs: /dev/nvme1n1p1,/dev/nvme2n1p1
    vg: vg_ansible
    state: present
```

![](img/db670d2e3c938680adf1ef5cf2b970da_110.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_112.png)

### LVOL模块
`lvol`模块用于创建、删除逻辑卷和调整其大小。

![](img/db670d2e3c938680adf1ef5cf2b970da_114.png)

以下是使用`lvol`模块的示例：
```yaml
- name: 创建逻辑卷
  lvol:
    vg: vg_ansible
    lv: lv_ansible
    size: 512m
    state: present
```

![](img/db670d2e3c938680adf1ef5cf2b970da_116.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_118.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_120.png)

---

![](img/db670d2e3c938680adf1ef5cf2b970da_122.png)

## 文件内容管理 📄

![](img/db670d2e3c938680adf1ef5cf2b970da_124.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_126.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_128.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_130.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_132.png)

上一节我们处理了存储设备，本节中我们来看看如何管理文件及其内容。

![](img/db670d2e3c938680adf1ef5cf2b970da_134.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_136.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_138.png)

### 文件模块
`file`模块用于管理文件和文件属性。以下示例展示如何创建一个空文件：
```yaml
- name: 创建一个新文件
  file:
    path: /tmp/testfile1
    state: touch
```

![](img/db670d2e3c938680adf1ef5cf2b970da_140.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_142.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_144.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_146.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_148.png)

### 复制模块
`copy`模块将文件复制到远程位置。可以使用`content`参数直接设置文件内容。
```yaml
- name: 创建文件并添加一行内容
  copy:
    content: '通过copy模块添加'
    dest: /tmp/testfile2
```

![](img/db670d2e3c938680adf1ef5cf2b970da_150.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_152.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_154.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_156.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_158.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_160.png)

### 行文件模块
`lineinfile`模块用于管理文本文件中的行。默认会查找并替换由正则表达式匹配的行，如果未找到则添加到文件末尾。
```yaml
- name: 添加一行并创建文件
  lineinfile:
    path: /tmp/testfile3
    line: '通过lineinfile模块添加'
    create: yes
```

![](img/db670d2e3c938680adf1ef5cf2b970da_162.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_164.png)

### 替换模块
`replace`模块会替换文件中所有匹配特定字符串的实例。
```yaml
- name: 替换文件中的一行
  replace:
    path: /tmp/testfile3
    regexp: '^.*module$'
    replace: '通过replace模块替换'
```

![](img/db670d2e3c938680adf1ef5cf2b970da_166.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_168.png)

### 模板模块
`template`模块是处理复杂文件内容的最佳方式，它使用Jinja2模板引擎。
```yaml
- name: 推送信息模板
  template:
    src: /home/cloud-user/ansible/templates/info.j2
    dest: /tmp/info.txt
```
模板文件（如`info.j2`）可以使用Ansible收集的事实变量：
```jinja2
主机名: {{ ansible_hostname }}
操作系统: {{ ansible_distribution }} {{ ansible_distribution_version }}
默认IPv4地址: {{ ansible_default_ipv4.address }}
接口: {{ ansible_interfaces | join(', ') }}
```

![](img/db670d2e3c938680adf1ef5cf2b970da_170.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_172.png)

---

![](img/db670d2e3c938680adf1ef5cf2b970da_174.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_176.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_178.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_180.png)

## 文件系统管理 📂

![](img/db670d2e3c938680adf1ef5cf2b970da_182.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_184.png)

上一节我们操作了文件内容，本节中我们来看看如何创建文件系统并挂载它们。

![](img/db670d2e3c938680adf1ef5cf2b970da_186.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_188.png)

### 文件系统模块
`filesystem`模块用于在设备上创建文件系统。
```yaml
- name: 在逻辑卷上创建XFS文件系统
  filesystem:
    fstype: xfs
    dev: /dev/mapper/vg_ansible-lv_ansible
```

![](img/db670d2e3c938680adf1ef5cf2b970da_190.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_192.png)

### 挂载模块
`mount`模块用于控制和配置挂载点。
```yaml
- name: 挂载文件系统
  mount:
    path: /mnt/test_mount
    src: /dev/mapper/vg_ansible-lv_ansible
    fstype: xfs
    state: mounted
    backup: yes
```
**状态参数说明**：
*   `mounted`：主动挂载设备并在`/etc/fstab`中添加条目。
*   `present`：仅在`/etc/fstab`中配置条目，不挂载设备。
*   `absent`：从`/etc/fstab`中删除条目，卸载设备并删除挂载点。

![](img/db670d2e3c938680adf1ef5cf2b970da_194.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_196.png)

---

![](img/db670d2e3c938680adf1ef5cf2b970da_198.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_200.png)

## 归档管理 📦

上一节我们处理了文件系统，本节中我们来看看如何创建和解压归档文件。

![](img/db670d2e3c938680adf1ef5cf2b970da_202.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_204.png)

### 归档模块
`archive`模块在一个或多个文件或目录上创建压缩归档。
```yaml
- name: 归档多个文件
  archive:
    path:
      - /home/cloud-user/archive/tester/testfile2
      - /home/cloud-user/archive/tester/testfile4
      - /home/cloud-user/archive/tester/testfile6
    dest: /home/cloud-user/archive/multi/multi-files.tar.gz
    format: gz
```

![](img/db670d2e3c938680adf1ef5cf2b970da_206.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_208.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_210.png)

### 解归档模块
`unarchive`模块用于解压归档文件。如果归档在远程主机上，需要设置`remote_src: yes`。
```yaml
- name: 解压远程归档
  unarchive:
    src: /home/cloud-user/archive/multi/multi-files.tar.gz
    dest: /home/cloud-user/archive/multi/
    remote_src: yes
```

---

![](img/db670d2e3c938680adf1ef5cf2b970da_212.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_213.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_215.png)

## 任务调度 ⏰

![](img/db670d2e3c938680adf1ef5cf2b970da_217.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_219.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_220.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_222.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_224.png)

上一节我们管理了归档文件，本节中我们来看看如何使用cron和at安排任务。

### Cron模块
`cron`模块允许您管理cron作业。
```yaml
- name: 安排每周yum更新
  cron:
    name: 'weekly yum update'
    minute: '0'
    hour: '2'
    weekday: '0'
    user: root
    state: present
    job: 'yum -y update'
```

![](img/db670d2e3c938680adf1ef5cf2b970da_226.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_228.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_229.png)

### At模块
`at`模块使用`at`命令安排命令或脚本的一次性执行。需要确保`at`软件包已安装。
```yaml
- name: 安装at软件包
  yum:
    name: at
    state: latest

![](img/db670d2e3c938680adf1ef5cf2b970da_231.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_233.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_235.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_237.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_239.png)

- name: 安排在2分钟后复制日志
  at:
    command: 'cp /var/log/httpd/error_log /home/cloud-user/'
    count: 2
    units: minutes
    state: present
```

![](img/db670d2e3c938680adf1ef5cf2b970da_241.png)

---

![](img/db670d2e3c938680adf1ef5cf2b970da_243.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_245.png)

## SELinux管理 🛡️

![](img/db670d2e3c938680adf1ef5cf2b970da_247.png)

上一节我们安排了系统任务，本节中我们来看看如何管理SELinux策略、布尔值和上下文。

### SELinux模块
`selinux`模块用于更改SELinux的策略和状态。
```yaml
- name: 将SELinux模式设置为宽容
  selinux:
    policy: targeted
    state: permissive
```

![](img/db670d2e3c938680adf1ef5cf2b970da_249.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_251.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_253.png)

### SELinux布尔值模块
`seboolean`模块用于切换SELinux布尔值。
```yaml
- name: 禁用httpd CGI布尔值
  seboolean:
    name: httpd_enable_cgi
    state: no
    persistent: yes
```

![](img/db670d2e3c938680adf1ef5cf2b970da_255.png)

### SELinux上下文模块
`sefcontext`模块用于管理SELinux文件上下文映射定义。
```yaml
- name: 为Web目录设置上下文
  sefcontext:
    target: '/webcontent(/.*)?'
    setype: httpd_sys_content_t
    state: present
```
设置上下文后，通常需要运行`restorecon`命令将其应用到现有文件：
```yaml
- name: 在目录上运行restorecon
  command: restorecon -irv /webcontent
```

---

![](img/db670d2e3c938680adf1ef5cf2b970da_257.png)

## 用户和组管理 👥

![](img/db670d2e3c938680adf1ef5cf2b970da_259.png)

上一节我们加强了系统安全，本节中我们来看看本部分的最后一个主题：管理用户和组。

![](img/db670d2e3c938680adf1ef5cf2b970da_261.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_263.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_265.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_267.png)

### 用户模块
`user`模块用于管理用户帐户和属性。
```yaml
- name: 创建用户Zach
  user:
    name: zach
    comment: 'Zach Morris'
    shell: /bin/bash
    groups: 'students,bayside'
    append: yes
    state: present
```
**关键参数**：
*   `append: yes`：将用户添加到指定组，同时保留其原有组。
*   `append: no`：用户将仅属于`groups`参数中指定的组。
*   `remove: yes`：删除用户时，同时删除其主目录和邮件假脱机。

![](img/db670d2e3c938680adf1ef5cf2b970da_269.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_271.png)

### 组模块
`group`模块用于添加或删除组。
```yaml
- name: 创建组
  group:
    name: "{{ item }}"
    state: present
  loop:
    - students
    - bayside
```

![](img/db670d2e3c938680adf1ef5cf2b970da_273.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_275.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_277.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_279.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_281.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_283.png)

---

![](img/db670d2e3c938680adf1ef5cf2b970da_285.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_287.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_289.png)

![](img/db670d2e3c938680adf1ef5cf2b970da_291.png)

## 总结
在本节课中，我们一起学习了如何使用Ansible模块执行广泛的系统管理任务。我们从软件包和存储库管理开始，逐步深入到服务控制、防火墙配置、存储设备管理、文件操作、任务调度、SELinux策略管理，最后是用户和组管理。每个模块都配有实践示例，帮助你理解如何将这些工具应用到实际的自动化工作流中。掌握这些模块是成为一名高效系统管理员和通过RHCE认证的关键步骤。