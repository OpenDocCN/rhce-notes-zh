# RHCE8考前辅导（1）：课程概述与环境准备

![](img/43e75b1e86ef3823bafe2045586484f2_0.png)

![](img/43e75b1e86ef3823bafe2045586484f2_2.png)

![](img/43e75b1e86ef3823bafe2045586484f2_4.png)

![](img/43e75b1e86ef3823bafe2045586484f2_6.png)

在本节课中，我们将学习RHCE8认证考试的第一部分考前辅导内容。课程将涵盖考试的基本规则、环境搭建以及前几道考题的详细解析，帮助初学者理解考试流程和解题思路。

![](img/43e75b1e86ef3823bafe2045586484f2_8.png)

## 考试规则与环境说明

上一节我们介绍了课程概述，本节中我们来看看RHCE8考试的具体规则和操作环境。

![](img/43e75b1e86ef3823bafe2045586484f2_10.png)

*   RHCE8（EX294）考试为全中文考试。
*  考试时长为4小时，满分300分，210分通过。
*  考试期间严禁作弊，必须携带身份证。
*  所有节点的SSH免密登录已由考试系统预先配置完成，考生无需操作。
*  考试在**一台Linux物理机**中进行，考生通过桌面图标启动虚拟机控制台进行操作。
*  考试题目以图形界面形式提供，类似一个PDF或网页文档。
*  考试提供草稿纸，但考试结束后不得带走。

![](img/43e75b1e86ef3823bafe2045586484f2_12.png)

![](img/43e75b1e86ef3823bafe2045586484f2_14.png)

以下是考试拓扑的简要说明：
```
+-----------------------+
|   Linux物理考试机     |
|  +-----------------+  |
|  | 虚拟机1 (控制节点) |  |
|  +-----------------+  |
|  | 虚拟机2 (被控节点) |  |
|  +-----------------+  |
|  | 虚拟机3 (被控节点) |  |
|  +-----------------+  |
|  |        ...       |  |
|  +-----------------+  |
|  | 虚拟机6 (被控节点) |  |
|  +-----------------+  |
+-----------------------+
```

![](img/43e75b1e86ef3823bafe2045586484f2_16.png)

## 模拟环境与考前准备

![](img/43e75b1e86ef3823bafe2045586484f2_18.png)

![](img/43e75b1e86ef3823bafe2045586484f2_20.png)

了解了考试规则后，我们需要在模拟环境中进行相应的准备。本节将指导你完成考试环境的初始化配置。

![](img/43e75b1e86ef3823bafe2045586484f2_22.png)

**重要注意事项**：
1.  所有操作必须使用指定的**非管理员用户**（如 `student`）进行，使用管理员（root）答题将不得分。
2.  所有的Ansible Playbook都必须存放在 `/home/student/ansible` 目录下。
3.  必要时可以关闭防火墙和SELinux。

![](img/43e75b1e86ef3823bafe2045586484f2_24.png)

由于模拟环境与真实考试环境存在细微差异，我们需要先在所有节点上配置`sudo`免密码提权。**请注意，此步骤在真实考试中不需要操作。**

![](img/43e75b1e86ef3823bafe2045586484f2_26.png)

![](img/43e75b1e86ef3823bafe2045586484f2_28.png)

以下是配置`sudo`免密码的具体步骤：
1.  在`workstation`节点上执行 `sudo visudo`。
2.  在打开的文件中，找到 `%wheel` 组相关的配置行。
3.  注释掉要求输入密码的行（`# %wheel ALL=(ALL) ALL`）。
4.  取消注释免密码的行（`%wheel ALL=(ALL) NOPASSWD: ALL`）。
5.  保存并退出编辑器。
6.  在`servera`、`serverb`、`serverc`、`serverd`及`bastion`节点上重复步骤1-5。

![](img/43e75b1e86ef3823bafe2045586484f2_30.png)

配置完成后，`student`用户在执行`sudo`命令时就不再需要输入密码。

![](img/43e75b1e86ef3823bafe2045586484f2_32.png)

## 考题解析：第1题 - 安装Ansible与清单配置

![](img/43e75b1e86ef3823bafe2045586484f2_34.png)

![](img/43e75b1e86ef3823bafe2045586484f2_36.png)

![](img/43e75b1e86ef3823bafe2045586484f2_38.png)

![](img/43e75b1e86ef3823bafe2045586484f2_40.png)

环境准备就绪后，我们开始解析具体的考题。第一题通常涉及Ansible的安装和基础配置。

![](img/43e75b1e86ef3823bafe2045586484f2_42.png)

![](img/43e75b1e86ef3823bafe2045586484f2_44.png)

**1a. 安装Ansible**
在控制节点（`workstation`）上安装Ansible软件包。
```bash
sudo yum install -y ansible
```

![](img/43e75b1e86ef3823bafe2045586484f2_46.png)

![](img/43e75b1e86ef3823bafe2045586484f2_48.png)

**1b. 创建静态清单文件**
在`/home/student/ansible`目录下创建静态清单文件`inventory`，并按要求定义主机组。
```ini
[dev]
servera

![](img/43e75b1e86ef3823bafe2045586484f2_50.png)

![](img/43e75b1e86ef3823bafe2045586484f2_52.png)

![](img/43e75b1e86ef3823bafe2045586484f2_54.png)

[test]
serverb

![](img/43e75b1e86ef3823bafe2045586484f2_56.png)

![](img/43e75b1e86ef3823bafe2045586484f2_58.png)

[prod]
serverc
serverd

![](img/43e75b1e86ef3823bafe2045586484f2_60.png)

[webservers:children]
prod

![](img/43e75b1e86ef3823bafe2045586484f2_62.png)

[balancer]
bastion
```

![](img/43e75b1e86ef3823bafe2045586484f2_64.png)

![](img/43e75b1e86ef3823bafe2045586484f2_66.png)

![](img/43e75b1e86ef3823bafe2045586484f2_68.png)

**1c. 配置Ansible**
在`/home/student/ansible`目录下创建Ansible配置文件`ansible.cfg`。
```ini
[defaults]
inventory = /home/student/ansible/inventory
remote_user = student

![](img/43e75b1e86ef3823bafe2045586484f2_70.png)

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
```

![](img/43e75b1e86ef3823bafe2045586484f2_72.png)

![](img/43e75b1e86ef3823bafe2045586484f2_74.png)

![](img/43e75b1e86ef3823bafe2045586484f2_76.png)

**验证配置**：
使用 `ansible all --list-hosts` 命令可以列出所有配置的主机，验证清单文件是否正确。

![](img/43e75b1e86ef3823bafe2045586484f2_78.png)

## 考题解析：第2题 - 使用Ad-Hoc命令配置Yum仓库

![](img/43e75b1e86ef3823bafe2045586484f2_80.png)

完成了Ansible的基础配置，接下来我们看一道使用Ad-Hoc命令的题目。本节要求通过脚本执行Ad-Hoc任务来配置Yum仓库。

![](img/43e75b1e86ef3823bafe2045586484f2_82.png)

![](img/43e75b1e86ef3823bafe2045586484f2_84.png)

创建一个脚本文件 `/home/student/ansible/adhoc.sh`，其内容为使用`ansible`命令为所有节点配置两个Yum仓库。
```bash
#!/bin/bash
ansible all -m yum_repository -a "name=294-base description='Base 294' baseurl=http://content.example.com/rhel8.0/x86_64/dvd/BaseOS gpgcheck=yes gpgkey=http://content.example.com/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release enabled=yes"
ansible all -m yum_repository -a "name=294-stream description='Stream 294' baseurl=http://content.example.com/rhel8.0/x86_64/dvd/AppStream gpgcheck=yes gpgkey=http://content.example.com/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release enabled=yes"
```
给脚本添加执行权限并运行：`chmod +x adhoc.sh && ./adhoc.sh`

![](img/43e75b1e86ef3823bafe2045586484f2_86.png)

![](img/43e75b1e86ef3823bafe2045586484f2_88.png)

## 考题解析：第3题 - 使用Playbook管理软件包

![](img/43e75b1e86ef3823bafe2045586484f2_90.png)

上一题我们使用了Ad-Hoc命令，本节我们来看看如何使用Playbook完成更复杂的任务：在多组主机上安装、组安装和升级软件包。

![](img/43e75b1e86ef3823bafe2045586484f2_92.png)

创建Playbook文件 `/home/student/ansible/install_packages.yml`。
```yaml
---
- name: Install php and mariadb on dev, test, prod
  hosts: dev,test,prod
  tasks:
    - name: Install packages
      yum:
        name: "{{ item }}"
        state: present
      loop:
        - php
        - mariadb

![](img/43e75b1e86ef3823bafe2045586484f2_94.png)

![](img/43e75b1e86ef3823bafe2045586484f2_96.png)

![](img/43e75b1e86ef3823bafe2045586484f2_98.png)

- name: Install package group on dev
  hosts: dev
  tasks:
    - name: Install 'Development Tools' package group
      yum:
        name: "@Development Tools"
        state: present

![](img/43e75b1e86ef3823bafe2045586484f2_100.png)

![](img/43e75b1e86ef3823bafe2045586484f2_102.png)

- name: Upgrade all packages on dev
  hosts: dev
  tasks:
    - name: Upgrade all packages
      yum:
        name: "*"
        state: latest
```
运行Playbook：`ansible-playbook install_packages.yml`

![](img/43e75b1e86ef3823bafe2045586484f2_104.png)

![](img/43e75b1e86ef3823bafe2045586484f2_106.png)

## 考题解析：第4题 - 使用红帽官方角色

![](img/43e75b1e86ef3823bafe2045586484f2_108.png)

![](img/43e75b1e86ef3823bafe2045586484f2_110.png)

从第4题开始，我们将接触Ansible的核心概念——角色。本节学习如何使用红帽官方提供的角色来配置系统服务。

![](img/43e75b1e86ef3823bafe2045586484f2_112.png)

**4a. 安装角色包并配置路径**
```bash
sudo yum install -y rhel-system-roles
mkdir -p /home/student/ansible/roles
```
编辑`/home/student/ansible/ansible.cfg`，添加角色搜索路径：
```ini
[defaults]
roles_path = /home/student/ansible/roles
```
将系统自带的角色复制到自定义路径：
```bash
sudo cp -r /usr/share/ansible/roles/rhel-system-roles.timesync /home/student/ansible/roles/
```

![](img/43e75b1e86ef3823bafe2045586484f2_114.png)

**4b. 创建Playbook使用角色**
创建Playbook文件 `/home/student/ansible/configure_timesync.yml`。
```yaml
---
- name: Configure time synchronization
  hosts: all
  vars:
    timesync_ntp_servers:
      - hostname: 172.25.254.254
  roles:
    - rhel-system-roles.timesync
```
运行Playbook：`ansible-playbook configure_timesync.yml`

![](img/43e75b1e86ef3823bafe2045586484f2_116.png)

![](img/43e75b1e86ef3823bafe2045586484f2_118.png)

![](img/43e75b1e86ef3823bafe2045586484f2_120.png)

![](img/43e75b1e86ef3823bafe2045586484f2_122.png)

## 考题解析：第5题 - 通过需求文件安装角色

![](img/43e75b1e86ef3823bafe2045586484f2_124.png)

![](img/43e75b1e86ef3823bafe2045586484f2_126.png)

![](img/43e75b1e86ef3823bafe2045586484f2_128.png)

![](img/43e75b1e86ef3823bafe2045586484f2_130.png)

学会了使用现有角色，本节我们来看看如何从指定来源获取并安装角色。这是管理角色依赖的常用方法。

![](img/43e75b1e86ef3823bafe2045586484f2_132.png)

![](img/43e75b1e86ef3823bafe2045586484f2_134.png)

在`/home/student/ansible/roles`目录下创建需求文件`requirements.yml`。
```yaml
---
- src: http://materials.example.com/haproxy
  name: balancer
- src: http://materials.example.com/phpinfo
  name: phpinfo
```
根据需求文件安装角色：
```bash
cd /home/student/ansible
ansible-galaxy install -r roles/requirements.yml -p roles/
```
使用 `ansible-galaxy list` 命令可以验证角色是否安装成功。

![](img/43e75b1e86ef3823bafe2045586484f2_136.png)

## 考题解析：第6题 - 创建自定义角色

![](img/43e75b1e86ef3823bafe2045586484f2_138.png)

![](img/43e75b1e86ef3823bafe2045586484f2_140.png)

前面我们使用了官方和下载的角色，本节难度升级，我们将学习如何从头创建一个自定义的Apache Web服务器角色，并在Playbook中调用它。

![](img/43e75b1e86ef3823bafe2045586484f2_142.png)

**6a. 创建角色骨架**
```bash
cd /home/student/ansible/roles
ansible-galaxy init apache
```

![](img/43e75b1e86ef3823bafe2045586484f2_144.png)

![](img/43e75b1e86ef3823bafe2045586484f2_146.png)

![](img/43e75b1e86ef3823bafe2045586484f2_148.png)

![](img/43e75b1e86ef3823bafe2045586484f2_150.png)

![](img/43e75b1e86ef3823bafe2045586484f2_152.png)

**6b. 配置角色任务**
编辑 `roles/apache/tasks/main.yml`，定义安装、启动Apache和配置防火墙的任务。
```yaml
---
- name: Install httpd package
  yum:
    name: httpd
    state: present

- name: Start and enable httpd service
  service:
    name: httpd
    state: started
    enabled: yes

![](img/43e75b1e86ef3823bafe2045586484f2_154.png)

![](img/43e75b1e86ef3823bafe2045586484f2_156.png)

![](img/43e75b1e86ef3823bafe2045586484f2_158.png)

- name: Open firewall port for http
  firewalld:
    port: 80/tcp
    permanent: yes
    immediate: yes
    state: enabled

![](img/43e75b1e86ef3823bafe2045586484f2_160.png)

![](img/43e75b1e86ef3823bafe2045586484f2_162.png)

![](img/43e75b1e86ef3823bafe2045586484f2_164.png)

![](img/43e75b1e86ef3823bafe2045586484f2_166.png)

![](img/43e75b1e86ef3823bafe2045586484f2_168.png)

- name: Deploy index.html template
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
```

![](img/43e75b1e86ef3823bafe2045586484f2_170.png)

![](img/43e75b1e86ef3823bafe2045586484f2_172.png)

![](img/43e75b1e86ef3823bafe2045586484f2_174.png)

![](img/43e75b1e86ef3823bafe2045586484f2_176.png)

**6c. 创建Jinja2模板**
创建模板文件 `roles/apache/templates/index.html.j2`，内容如下：
```jinja2
Welcome to {{ ansible_fqdn }} on {{ ansible_default_ipv4.address }}
```

![](img/43e75b1e86ef3823bafe2045586484f2_178.png)

![](img/43e75b1e86ef3823bafe2045586484f2_180.png)

![](img/43e75b1e86ef3823bafe2045586484f2_182.png)

**6d. 创建Playbook调用角色**
创建Playbook文件 `/home/student/ansible/new_roles.yml`。
```yaml
---
- name: Deploy Apache role to webservers
  hosts: webservers
  roles:
    - apache
```
运行Playbook：`ansible-playbook new_roles.yml`

![](img/43e75b1e86ef3823bafe2045586484f2_184.png)

![](img/43e75b1e86ef3823bafe2045586484f2_186.png)

![](img/43e75b1e86ef3823bafe2045586484f2_188.png)

## 考题解析：第7题 - 实现负载均衡配置

![](img/43e75b1e86ef3823bafe2045586484f2_190.png)

![](img/43e75b1e86ef3823bafe2045586484f2_192.png)

![](img/43e75b1e86ef3823bafe2045586484f2_194.png)

角色创建完成后，我们进入一个综合应用场景。本节将利用之前安装的负载均衡角色，实现一个经典的Web负载均衡拓扑。

创建Playbook文件 `/home/student/ansible/roles.yml`。
```yaml
---
- name: Configure load balancer
  hosts: balancer
  roles:
    - balancer
```
**重要提示**：在模拟环境中，`bastion`主机可能预装了Apache并占用了80端口，需要先停止它，负载均衡服务才能成功启动。**此步骤仅针对本模拟环境，考试中不需要。**
```bash
# 在bastion节点上执行
sudo systemctl stop httpd
sudo systemctl disable httpd
```
返回控制节点运行Playbook：`ansible-playbook roles.yml`
配置完成后，通过浏览器访问 `http://bastion.lab.example.com`，刷新页面可以看到请求被轮流分发到`serverc`和`serverd`。

![](img/43e75b1e86ef3823bafe2045586484f2_196.png)

![](img/43e75b1e86ef3823bafe2045586484f2_198.png)

![](img/43e75b1e86ef3823bafe2045586484f2_200.png)

## 考题解析：第9题 - 使用模板生成Hosts文件

![](img/43e75b1e86ef3823bafe2045586484f2_202.png)

![](img/43e75b1e86ef3823bafe2045586484f2_204.png)

![](img/43e75b1e86ef3823bafe2045586484f2_206.png)

![](img/43e75b1e86ef3823bafe2045586484f2_208.png)

最后，我们来看一道关于Jinja2模板和循环的题目。本节要求使用模板为特定主机组动态生成一个hosts文件。

![](img/43e75b1e86ef3823bafe2045586484f2_210.png)

![](img/43e75b1e86ef3823bafe2045586484f2_212.png)

![](img/43e75b1e86ef3823bafe2045586484f2_214.png)

**9a. 下载并修改模板文件**
```bash
cd /home/student/ansible
wget http://materials.example.com/hosts.j2
```
编辑下载的`hosts.j2`文件，使用循环生成所有主机的记录。
```jinja2
# 开始模板内容
{% for host in groups.all %}
{{ hostvars[host].ansible_default_ipv4.address }} {{ hostvars[host].ansible_fqdn }} {{ hostvars[host].ansible_hostname }}
{% endfor %}
```

![](img/43e75b1e86ef3823bafe2045586484f2_216.png)

![](img/43e75b1e86ef3823bafe2045586484f2_218.png)

![](img/43e75b1e86ef3823bafe2045586484f2_220.png)

![](img/43e75b1e86ef3823bafe2045586484f2_222.png)

**9b. 创建Playbook使用模板**
创建Playbook文件 `/home/student/ansible/hosts.yml`。
```yaml
---
- name: Generate hosts file for dev group
  hosts: all
  tasks:
    - name: Create myhosts file from template
      template:
        src: hosts.j2
        dest: /home/student/myhosts
      when: inventory_hostname in groups['dev']
```
运行Playbook：`ansible-playbook hosts.yml`
此Playbook会为`dev`组的主机（如`servera`）在指定路径生成`myhosts`文件，内容包含所有主机的IP、全限定域名和短主机名。

![](img/43e75b1e86ef3823bafe2045586484f2_224.png)

![](img/43e75b1e86ef3823bafe2045586484f2_226.png)

## 课程总结

![](img/43e75b1e86ef3823bafe2045586484f2_228.png)

![](img/43e75b1e86ef3823bafe2045586484f2_229.png)

![](img/43e75b1e86ef3823bafe2045586484f2_230.png)

![](img/43e75b1e86ef3823bafe2045586484f2_232.png)

![](img/43e75b1e86ef3823bafe2045586484f2_234.png)

![](img/43e75b1e86ef3823bafe2045586484f2_236.png)

本节课中我们一起学习了RHCE8考前辅导的第一部分内容。我们从考试规则和环境准备入手，逐步解析了前9道考题，涵盖了Ansible安装、清单配置、Ad-Hoc命令、Playbook编写、软件包管理、以及角色的使用（包括官方角色、通过需求文件安装角色和创建自定义角色）。最后，我们还实践了利用角色实现负载均衡和使用Jinja2模板生成文件等综合技能。这些题目环环相扣，很好地模拟了真实考试的难度和连贯性。请务必理解每道题目的意图和解决方法，而不仅仅是记住命令。