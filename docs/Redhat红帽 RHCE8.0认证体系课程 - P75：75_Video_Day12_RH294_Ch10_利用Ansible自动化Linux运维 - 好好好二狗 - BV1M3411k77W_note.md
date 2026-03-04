# Redhat红帽 RHCE8.0认证体系课程：P75：利用Ansible自动化Linux运维 🚀

在本章中，我们将学习如何使用Ansible来自动化Linux系统的日常运维任务。我们将回顾并深入讲解几个关键模块，特别是存储管理模块，以帮助初学者掌握自动化运维的核心技能。

![](img/b7ef440c768295c4e10e602a8cda5567_1.png)

---

![](img/b7ef440c768295c4e10e602a8cda5567_3.png)

![](img/b7ef440c768295c4e10e602a8cda5567_4.png)

## 软件包管理 📦

![](img/b7ef440c768295c4e10e602a8cda5567_6.png)

上一节我们介绍了Ansible的基础，本节中我们来看看如何使用Ansible管理软件包。

![](img/b7ef440c768295c4e10e602a8cda5567_8.png)

![](img/b7ef440c768295c4e10e602a8cda5567_10.png)

![](img/b7ef440c768295c4e10e602a8cda5567_12.png)

Ansible提供了多种模块来管理软件包，包括 `yum`、`dnf` 和 `package`。`yum` 和 `dnf` 模块功能相似，都用于安装、更新和删除软件包。

以下是使用 `yum` 模块安装软件包的基本语法：
```yaml
- name: 安装软件包
  yum:
    name: httpd
    state: present
```
将 `yum` 替换为 `dnf` 用法完全相同。

![](img/b7ef440c768295c4e10e602a8cda5567_14.png)

`package_facts` 模块用于收集受管主机上已安装软件包的信息。收集到的信息可以在 `ansible_facts.packages` 中查看。

![](img/b7ef440c768295c4e10e602a8cda5567_15.png)

![](img/b7ef440c768295c4e10e602a8cda5567_16.png)

![](img/b7ef440c768295c4e10e602a8cda5567_18.png)

我们可以通过添加判断条件来筛选特定软件的版本信息。例如，以下任务会列出所有已安装的软件包，但通过 `when` 条件只显示 `httpd` 的版本：
```yaml
- name: 收集包信息并显示httpd版本
  package_facts:
    manager: auto
  debug:
    var: ansible_facts.packages['httpd']
```

![](img/b7ef440c768295c4e10e602a8cda5567_19.png)

![](img/b7ef440c768295c4e10e602a8cda5567_21.png)

![](img/b7ef440c768295c4e10e602a8cda5567_23.png)

`package` 模块是一个通用模块，也可以用来安装软件包，其参数类似于 `rpm` 命令。目前，安装软件包主要有 `yum`、`dnf` 和 `package` 三种方式。

![](img/b7ef440c768295c4e10e602a8cda5567_25.png)

![](img/b7ef440c768295c4e10e602a8cda5567_27.png)

`rpm_key` 模块用于导入GPG密钥。通常在部署软件仓库时已经完成此步骤。以下是一个导入密钥的例子：
```yaml
- name: 导入GPG密钥
  rpm_key:
    key: "http://mirror.example.com/RPM-GPG-KEY-example"
    state: present
```

![](img/b7ef440c768295c4e10e602a8cda5567_29.png)

![](img/b7ef440c768295c4e10e602a8cda5567_31.png)

---

![](img/b7ef440c768295c4e10e602a8cda5567_33.png)

## 用户与组管理 👥

软件包管理之后，我们来探讨如何自动化管理用户和组。

![](img/b7ef440c768295c4e10e602a8cda5567_35.png)

`user` 模块用于管理用户账户，功能非常全面。它不仅可以创建用户，还可以生成SSH密钥。

![](img/b7ef440c768295c4e10e602a8cda5567_37.png)

![](img/b7ef440c768295c4e10e602a8cda5567_39.png)

![](img/b7ef440c768295c4e10e602a8cda5567_41.png)

以下是使用 `user` 模块创建一个用户的综合示例：
```yaml
- name: 创建用户示例
  hosts: examplehost
  tasks:
    - name: 创建用户
      user:
        name: user1
        uid: 2000
        shell: /sbin/nologin
        password: "{{ 'redhat' | password_hash('sha512') }}"
```
其中，密码使用了SHA-512哈希编码，而非明文。

![](img/b7ef440c768295c4e10e602a8cda5567_42.png)

![](img/b7ef440c768295c4e10e602a8cda5567_44.png)

![](img/b7ef440c768295c4e10e602a8cda5567_46.png)

`group` 模块用于管理组。以下是一个创建组的简单示例：
```yaml
- name: 创建组
  group:
    name: developers
    state: present
```

![](img/b7ef440c768295c4e10e602a8cda5567_47.png)

![](img/b7ef440c768295c4e10e602a8cda5567_48.png)

`known_hosts` 模块用于在受管主机的 `~/.ssh/known_hosts` 文件中添加或删除主机密钥。它可以通过 `delegate_to` 插件调用外部来源的数据。

![](img/b7ef440c768295c4e10e602a8cda5567_50.png)

`authorized_key` 模块用于为用户添加或删除SSH授权密钥。它可以直接添加公钥，也可以从URL提取密钥。以下是一个示例：
```yaml
- name: 添加授权密钥
  authorized_key:
    user: user1
    key: "https://github.com/user1.keys"
    state: present
```

![](img/b7ef440c768295c4e10e602a8cda5567_52.png)

![](img/b7ef440c768295c4e10e602a8cda5567_54.png)

---

## 进程与任务管理 ⏰

![](img/b7ef440c768295c4e10e602a8cda5567_56.png)

![](img/b7ef440c768295c4e10e602a8cda5567_58.png)

完成了用户管理，我们接下来看看如何管理系统的进程和计划任务。

![](img/b7ef440c768295c4e10e602a8cda5567_60.png)

![](img/b7ef440c768295c4e10e602a8cda5567_62.png)

![](img/b7ef440c768295c4e10e602a8cda5567_64.png)

`at` 模块用于安排一次性任务。其参数包括要执行的命令 (`command`)、运行时间 (`units`) 等。

![](img/b7ef440c768295c4e10e602a8cda5567_66.png)

以下是一个在2分钟后创建用户的 `at` 任务示例：
```yaml
- name: 安排一次性任务
  at:
    command: "useradd user201"
    count: 2
    units: minutes
    unique: yes
```
`unique: yes` 参数确保该任务不会重复执行。

![](img/b7ef440c768295c4e10e602a8cda5567_68.png)

![](img/b7ef440c768295c4e10e602a8cda5567_69.png)

`cron` 模块用于管理周期性计划任务，可以将任务写入指定用户的crontab中。

![](img/b7ef440c768295c4e10e602a8cda5567_70.png)

![](img/b7ef440c768295c4e10e602a8cda5567_71.png)

![](img/b7ef440c768295c4e10e602a8cda5567_73.png)

![](img/b7ef440c768295c4e10e602a8cda5567_74.png)

以下是一个创建周期性备份任务的示例：
```yaml
- name: 添加计划任务
  cron:
    name: "Daily backup"
    user: root
    hour: 1
    minute: 0
    job: "tar -czf /backup/backup.tar.gz /data"
```
执行后，该任务会被写入root用户的crontab。

![](img/b7ef440c768295c4e10e602a8cda5567_75.png)

![](img/b7ef440c768295c4e10e602a8cda5567_76.png)

![](img/b7ef440c768295c4e10e602a8cda5567_78.png)

![](img/b7ef440c768295c4e10e602a8cda5567_80.png)

`service` 和 `systemd` 模块用于管理服务。`systemd` 模块功能更全，例如它允许守护进程重新加载配置，而 `service` 模块则不行。

![](img/b7ef440c768295c4e10e602a8cda5567_81.png)

![](img/b7ef440c768295c4e10e602a8cda5567_82.png)

![](img/b7ef440c768295c4e10e602a8cda5567_83.png)

`reboot` 模块用于安全地重启受管主机。它比使用 `shell` 模块执行关机命令更安全，并且可以在剧本运行期间操作。`reboot_delay` 参数可以设置重启前的等待时间。
```yaml
- name: 重启主机
  reboot:
    reboot_timeout: 600
```

![](img/b7ef440c768295c4e10e602a8cda5567_85.png)

`shell` 和 `command` 模块用于执行命令。`command` 模块更安全，但它不支持管道符 `|` 等shell操作符。如果需要使用管道符，则应使用 `shell` 模块。

![](img/b7ef440c768295c4e10e602a8cda5567_87.png)

![](img/b7ef440c768295c4e10e602a8cda5567_88.png)

`ansible_env` 是一个特殊的变量，它包含了连接Ansible受管主机时收集到的所有环境变量。

![](img/b7ef440c768295c4e10e602a8cda5567_90.png)

![](img/b7ef440c768295c4e10e602a8cda5567_92.png)

![](img/b7ef440c768295c4e10e602a8cda5567_94.png)

我们可以通过 `lookup` 插件来访问这些变量。以下任务会打印出所有的环境变量：
```yaml
- name: 打印环境变量
  debug:
    msg: "{{ lookup('env', 'HOME') }}"
```
要查看当前Ansible的所有环境变量信息，可以使用命令：`ansible localhost -m debug -a "msg={{ ansible_env }}"`

![](img/b7ef440c768295c4e10e602a8cda5567_95.png)

![](img/b7ef440c768295c4e10e602a8cda5567_97.png)

![](img/b7ef440c768295c4e10e602a8cda5567_98.png)

![](img/b7ef440c768295c4e10e602a8cda5567_100.png)

---

![](img/b7ef440c768295c4e10e602a8cda5567_102.png)

## 系统信息收集与存储管理 💾

![](img/b7ef440c768295c4e10e602a8cda5567_104.png)

![](img/b7ef440c768295c4e10e602a8cda5567_105.png)

![](img/b7ef440c768295c4e10e602a8cda5567_107.png)

了解了进程管理，我们进入系统管理的另一个核心领域：信息收集和存储。

![](img/b7ef440c768295c4e10e602a8cda5567_109.png)

![](img/b7ef440c768295c4e10e602a8cda5567_111.png)

`setup` 模块用于收集受管主机的事实信息。默认情况下，运行剧本时会自动收集。我们也可以手动执行并过滤信息。

例如，以下命令只显示设备相关的信息：
```bash
ansible all -m setup -a "filter=ansible_devices"
```

![](img/b7ef440c768295c4e10e602a8cda5567_113.png)

![](img/b7ef440c768295c4e10e602a8cda5567_114.png)

存储管理是自动化运维的重点。`parted` 模块用于磁盘分区，它是 `fdisk` 命令的现代替代品。

![](img/b7ef440c768295c4e10e602a8cda5567_116.png)

以下示例演示了如何创建一个10GB的分区：
```yaml
- name: 创建分区
  parted:
    device: /dev/vdb
    number: 1
    state: present
    part_end: 10GB
```

![](img/b7ef440c768295c4e10e602a8cda5567_117.png)

![](img/b7ef440c768295c4e10e602a8cda5567_119.png)

`lvg` 模块用于管理卷组（VG），`lvol` 模块用于管理逻辑卷（LV）。

![](img/b7ef440c768295c4e10e602a8cda5567_121.png)

![](img/b7ef440c768295c4e10e602a8cda5567_123.png)

以下是创建卷组和逻辑卷的示例：
```yaml
- name: 创建卷组
  lvg:
    vg: myvg
    pvs: /dev/vdb1
    pesize: 32

![](img/b7ef440c768295c4e10e602a8cda5567_125.png)

![](img/b7ef440c768295c4e10e602a8cda5567_127.png)

- name: 创建逻辑卷
  lvol:
    vg: myvg
    lv: mylv
    size: 2G
```

![](img/b7ef440c768295c4e10e602a8cda5567_128.png)

`filesystem` 模块用于创建和调整文件系统，相当于 `mkfs` 命令。

![](img/b7ef440c768295c4e10e602a8cda5567_130.png)

![](img/b7ef440c768295c4e10e602a8cda5567_132.png)

以下示例在 `/dev/vdb1` 上创建XFS文件系统：
```yaml
- name: 创建文件系统
  filesystem:
    fstype: xfs
    dev: /dev/vdb1
```

![](img/b7ef440c768295c4e10e602a8cda5567_134.png)

![](img/b7ef440c768295c4e10e602a8cda5567_136.png)

`mount` 模块用于挂载文件系统，并可在 `/etc/fstab` 中配置永久挂载。

![](img/b7ef440c768295c4e10e602a8cda5567_138.png)

以下示例将设备永久挂载到 `/data` 目录：
```yaml
- name: 挂载文件系统
  mount:
    path: /data
    src: UUID=xxxx-xxxx-xxxx
    fstype: xfs
    state: present
```

![](img/b7ef440c768295c4e10e602a8cda5567_140.png)

目前，Ansible没有专门的交换分区管理模块。一种可行的实现思路是：先使用 `lvol` 模块创建逻辑卷，然后通过 `command` 或 `shell` 模块执行 `mkswap` 和 `swapon` 命令。

![](img/b7ef440c768295c4e10e602a8cda5567_142.png)

![](img/b7ef440c768295c4e10e602a8cda5567_143.png)

---

![](img/b7ef440c768295c4e10e602a8cda5567_145.png)

![](img/b7ef440c768295c4e10e602a8cda5567_146.png)

![](img/b7ef440c768295c4e10e602a8cda5567_148.png)

## 网络与防火墙管理 🌐

![](img/b7ef440c768295c4e10e602a8cda5567_150.png)

![](img/b7ef440c768295c4e10e602a8cda5567_152.png)

最后，我们来看看如何使用Ansible进行网络和防火墙配置的管理。

![](img/b7ef440c768295c4e10e602a8cda5567_154.png)

网络配置在当前的Ansible版本中功能尚在完善中，主要可以通过系统角色 `rhel-system-roles.network` 来实现，但使用并不广泛。

![](img/b7ef440c768295c4e10e602a8cda5567_156.png)

![](img/b7ef440c768295c4e10e602a8cda5567_158.png)

`nmcli` 模块相当于 `nmcli` 命令，用于管理NetworkManager连接。它可以配置IP地址、DNS、网关等。

![](img/b7ef440c768295c4e10e602a8cda5567_160.png)

![](img/b7ef440c768295c4e10e602a8cda5567_161.png)

以下是一个配置网络连接的示例：
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

![](img/b7ef440c768295c4e10e602a8cda5567_163.png)

![](img/b7ef440c768295c4e10e602a8cda5567_165.png)

![](img/b7ef440c768295c4e10e602a8cda5567_167.png)

`hostname` 模块用于修改受管主机的主机名。
```yaml
- name: 修改主机名
  hostname:
    name: newhostname.example.com
```

![](img/b7ef440c768295c4e10e602a8cda5567_169.png)

![](img/b7ef440c768295c4e10e602a8cda5567_171.png)

`firewalld` 模块用于配置防火墙规则，我们之前已经接触过。它可以在指定区域添加或删除规则。

![](img/b7ef440c768295c4e10e602a8cda5567_172.png)

![](img/b7ef440c768295c4e10e602a8cda5567_174.png)

![](img/b7ef440c768295c4e10e602a8cda5567_176.png)

以下是在 `public` 区域永久放行HTTP服务的示例：
```yaml
- name: 配置防火墙规则
  firewalld:
    service: http
    zone: public
    permanent: yes
    state: enabled
```

---

## 总结 📝

本节课中我们一起学习了利用Ansible自动化Linux运维的多个关键领域。

我们回顾了软件包管理（`yum`， `dnf`， `package`）、用户与组管理（`user`， `group`）、进程与计划任务管理（`at`， `cron`， `service`）。接着，我们深入探讨了系统信息收集（`setup`）和复杂的存储管理，包括磁盘分区（`parted`）、逻辑卷管理（`lvg`， `lvol`）、文件系统创建（`filesystem`）和挂载（`mount`）。最后，我们简要介绍了网络（`nmcli`， `hostname`）和防火墙（`firewalld`）的自动化管理。

![](img/b7ef440c768295c4e10e602a8cda5567_178.png)

通过本章的学习，你应该能够使用Ansible编写Playbook来自动化完成Linux系统中许多常见的运维任务，为大规模系统管理打下坚实基础。