# Linux就该这么学第34期 - P16：16【第34期第十六节课】红帽RHCE认证培训课程-Linux就该这么学

![](img/f95c0e5a85990870276174bd6b48998e_1.png)

![](img/f95c0e5a85990870276174bd6b48998e_3.png)

![](img/f95c0e5a85990870276174bd6b48998e_4.png)

在本节课中，我们将要学习RHCE下午考试部分的核心内容，包括使用Ansible进行自动化运维的多个高级实验。我们将从生成主机文件开始，逐步深入到复杂的变量管理、条件判断、循环语句以及加密库的使用。课程内容紧密围绕考试要求，旨在帮助初学者掌握关键技能。

![](img/f95c0e5a85990870276174bd6b48998e_6.png)

![](img/f95c0e5a85990870276174bd6b48998e_7.png)

![](img/f95c0e5a85990870276174bd6b48998e_8.png)

![](img/f95c0e5a85990870276174bd6b48998e_9.png)

![](img/f95c0e5a85990870276174bd6b48998e_10.png)

![](img/f95c0e5a85990870276174bd6b48998e_11.png)

![](img/f95c0e5a85990870276174bd6b48998e_12.png)

![](img/f95c0e5a85990870276174bd6b48998e_14.png)

![](img/f95c0e5a85990870276174bd6b48998e_15.png)

![](img/f95c0e5a85990870276174bd6b48998e_16.png)

## 概述

![](img/f95c0e5a85990870276174bd6b48998e_18.png)

![](img/f95c0e5a85990870276174bd6b48998e_19.png)

![](img/f95c0e5a85990870276174bd6b48998e_20.png)

![](img/f95c0e5a85990870276174bd6b48998e_22.png)

![](img/f95c0e5a85990870276174bd6b48998e_23.png)

![](img/f95c0e5a85990870276174bd6b48998e_24.png)

本节课我们将重点讲解RHCE考试中的多个实验题目，涵盖Ansible的模板技术、变量管理、条件判断、循环语句、文件操作、用户管理以及加密库的使用。通过本课程的学习，您将能够掌握Ansible的高级应用，为通过RHCE考试打下坚实基础。

![](img/f95c0e5a85990870276174bd6b48998e_26.png)

## 生成主机文件

![](img/f95c0e5a85990870276174bd6b48998e_28.png)

![](img/f95c0e5a85990870276174bd6b48998e_29.png)

上一节我们介绍了Ansible的基础知识，本节中我们来看看如何使用Ansible生成主机文件。这个实验要求我们从远程服务器下载一个模板文件，并使用Jinja2技术填充变量，最终在目标主机上生成完整的主机文件。

![](img/f95c0e5a85990870276174bd6b48998e_31.png)

![](img/f95c0e5a85990870276174bd6b48998e_32.png)

![](img/f95c0e5a85990870276174bd6b48998e_33.png)

![](img/f95c0e5a85990870276174bd6b48998e_34.png)

![](img/f95c0e5a85990870276174bd6b48998e_35.png)

### 实验步骤

![](img/f95c0e5a85990870276174bd6b48998e_37.png)

![](img/f95c0e5a85990870276174bd6b48998e_38.png)

![](img/f95c0e5a85990870276174bd6b48998e_40.png)

![](img/f95c0e5a85990870276174bd6b48998e_41.png)

![](img/f95c0e5a85990870276174bd6b48998e_43.png)

以下是生成主机文件的具体步骤：

![](img/f95c0e5a85990870276174bd6b48998e_45.png)

![](img/f95c0e5a85990870276174bd6b48998e_47.png)

1. **下载模板文件**：首先，我们需要从远程服务器下载一个初始化的模板文件到本地。
   ```bash
   wget http://172.25.250.254/hosts.j2
   ```

![](img/f95c0e5a85990870276174bd6b48998e_49.png)

![](img/f95c0e5a85990870276174bd6b48998e_50.png)

![](img/f95c0e5a85990870276174bd6b48998e_51.png)

![](img/f95c0e5a85990870276174bd6b48998e_52.png)

2. **编辑模板文件**：使用Jinja2技术编辑下载的模板文件，填充变量信息。模板文件需要包含主机IP地址、主机名和主机清单中的名称。
   ```jinja2
   {% for host in groups['all'] %}
   {{ hostvars[host]['ansible_default_ipv4']['address'] }} {{ hostvars[host]['ansible_fqdn'] }} {{ host }}
   {% endfor %}
   ```

![](img/f95c0e5a85990870276174bd6b48998e_54.png)

![](img/f95c0e5a85990870276174bd6b48998e_55.png)

![](img/f95c0e5a85990870276174bd6b48998e_57.png)

![](img/f95c0e5a85990870276174bd6b48998e_58.png)

![](img/f95c0e5a85990870276174bd6b48998e_59.png)

![](img/f95c0e5a85990870276174bd6b48998e_60.png)

3. **创建Playbook**：创建一个名为`generate_hosts.yml`的Playbook，仅在`dev`组上运行，将编辑后的模板文件复制到目标主机。
   ```yaml
   ---
   - name: Generate hosts file
     hosts: dev
     tasks:
       - name: Copy template to target
         template:
           src: /home/ansible/hosts.j2
           dest: /etc/hosts
   ```

![](img/f95c0e5a85990870276174bd6b48998e_62.png)

![](img/f95c0e5a85990870276174bd6b48998e_63.png)

![](img/f95c0e5a85990870276174bd6b48998e_65.png)

![](img/f95c0e5a85990870276174bd6b48998e_66.png)

### 注意事项

![](img/f95c0e5a85990870276174bd6b48998e_68.png)

在编辑模板文件时，需要注意以下两点：
- 变量之间的分隔符必须使用Tab键，而不是空格。
- 文件内容中不能有多余的空行，否则可能导致判分失败。

![](img/f95c0e5a85990870276174bd6b48998e_70.png)

![](img/f95c0e5a85990870276174bd6b48998e_71.png)

![](img/f95c0e5a85990870276174bd6b48998e_72.png)

![](img/f95c0e5a85990870276174bd6b48998e_73.png)

![](img/f95c0e5a85990870276174bd6b48998e_75.png)

## 修改文件内容

![](img/f95c0e5a85990870276174bd6b48998e_77.png)

![](img/f95c0e5a85990870276174bd6b48998e_78.png)

![](img/f95c0e5a85990870276174bd6b48998e_80.png)

![](img/f95c0e5a85990870276174bd6b48998e_81.png)

![](img/f95c0e5a85990870276174bd6b48998e_82.png)

![](img/f95c0e5a85990870276174bd6b48998e_83.png)

上一节我们介绍了如何生成主机文件，本节中我们来看看如何使用Ansible修改文件内容。这个实验要求我们根据主机组的不同，修改文件中的特定内容。

![](img/f95c0e5a85990870276174bd6b48998e_85.png)

![](img/f95c0e5a85990870276174bd6b48998e_86.png)

![](img/f95c0e5a85990870276174bd6b48998e_88.png)

![](img/f95c0e5a85990870276174bd6b48998e_89.png)

![](img/f95c0e5a85990870276174bd6b48998e_90.png)

![](img/f95c0e5a85990870276174bd6b48998e_91.png)

### 实验步骤

![](img/f95c0e5a85990870276174bd6b48998e_93.png)

以下是修改文件内容的具体步骤：

![](img/f95c0e5a85990870276174bd6b48998e_95.png)

![](img/f95c0e5a85990870276174bd6b48998e_97.png)

![](img/f95c0e5a85990870276174bd6b48998e_98.png)

![](img/f95c0e5a85990870276174bd6b48998e_100.png)

![](img/f95c0e5a85990870276174bd6b48998e_101.png)

1. **创建Playbook**：创建一个名为`modify_content.yml`的Playbook，在所有主机上运行。
   ```yaml
   ---
   - name: Modify file content
     hosts: all
     tasks:
       - name: Modify content for dev group
         copy:
           content: 'development'
           dest: /tmp/file.txt
         when: "'dev' in group_names"
   ```

![](img/f95c0e5a85990870276174bd6b48998e_103.png)

![](img/f95c0e5a85990870276174bd6b48998e_104.png)

2. **使用条件判断**：通过`when`语句判断主机组，并根据组名修改文件内容。
   ```yaml
   - name: Modify content for test group
     copy:
       content: 'test'
       dest: /tmp/file.txt
     when: "'test' in group_names"
   ```

![](img/f95c0e5a85990870276174bd6b48998e_106.png)

![](img/f95c0e5a85990870276174bd6b48998e_107.png)

![](img/f95c0e5a85990870276174bd6b48998e_108.png)

![](img/f95c0e5a85990870276174bd6b48998e_110.png)

![](img/f95c0e5a85990870276174bd6b48998e_112.png)

### 核心概念

![](img/f95c0e5a85990870276174bd6b48998e_114.png)

在这个实验中，我们使用了`group_names`变量来获取主机所属的组，并通过`when`语句进行条件判断。这样可以确保每个主机组修改的内容不同。

![](img/f95c0e5a85990870276174bd6b48998e_116.png)

![](img/f95c0e5a85990870276174bd6b48998e_117.png)

![](img/f95c0e5a85990870276174bd6b48998e_119.png)

![](img/f95c0e5a85990870276174bd6b48998e_120.png)

![](img/f95c0e5a85990870276174bd6b48998e_121.png)

![](img/f95c0e5a85990870276174bd6b48998e_122.png)

## 创建Web网站目录

![](img/f95c0e5a85990870276174bd6b48998e_124.png)

![](img/f95c0e5a85990870276174bd6b48998e_125.png)

上一节我们介绍了如何修改文件内容，本节中我们来看看如何使用Ansible创建Web网站目录。这个实验要求我们在`dev`组上创建Web目录，并设置相应的权限和链接。

![](img/f95c0e5a85990870276174bd6b48998e_127.png)

![](img/f95c0e5a85990870276174bd6b48998e_128.png)

### 实验步骤

![](img/f95c0e5a85990870276174bd6b48998e_130.png)

![](img/f95c0e5a85990870276174bd6b48998e_132.png)

以下是创建Web网站目录的具体步骤：

![](img/f95c0e5a85990870276174bd6b48998e_134.png)

![](img/f95c0e5a85990870276174bd6b48998e_135.png)

1. **启动Web服务**：首先，确保Web服务已经安装并启动。
   ```yaml
   - name: Start web service
     service:
       name: httpd
       state: started
       enabled: yes
   ```

![](img/f95c0e5a85990870276174bd6b48998e_137.png)

![](img/f95c0e5a85990870276174bd6b48998e_139.png)

![](img/f95c0e5a85990870276174bd6b48998e_141.png)

![](img/f95c0e5a85990870276174bd6b48998e_142.png)

2. **放行防火墙**：放行Web服务所需的防火墙端口。
   ```yaml
   - name: Allow web service in firewall
     firewalld:
       service: http
       permanent: yes
       state: enabled
       immediate: yes
   ```

![](img/f95c0e5a85990870276174bd6b48998e_144.png)

![](img/f95c0e5a85990870276174bd6b48998e_145.png)

![](img/f95c0e5a85990870276174bd6b48998e_146.png)

![](img/f95c0e5a85990870276174bd6b48998e_148.png)

3. **创建用户组**：创建一个名为`webdev`的用户组。
   ```yaml
   - name: Create webdev group
     group:
       name: webdev
       state: present
   ```

![](img/f95c0e5a85990870276174bd6b48998e_150.png)

![](img/f95c0e5a85990870276174bd6b48998e_152.png)

![](img/f95c0e5a85990870276174bd6b48998e_154.png)

4. **创建Web目录**：创建Web目录，并设置权限和所有者。
   ```yaml
   - name: Create web directory
     file:
       path: /var/www/html/webdev
       state: directory
       mode: '2775'
       group: webdev
   ```

![](img/f95c0e5a85990870276174bd6b48998e_155.png)

![](img/f95c0e5a85990870276174bd6b48998e_157.png)

![](img/f95c0e5a85990870276174bd6b48998e_158.png)

![](img/f95c0e5a85990870276174bd6b48998e_159.png)

![](img/f95c0e5a85990870276174bd6b48998e_160.png)

5. **创建链接文件**：创建一个链接文件，指向Web目录。
   ```yaml
   - name: Create symbolic link
     file:
       src: /var/www/html/webdev
       dest: /webdev
       state: link
   ```

![](img/f95c0e5a85990870276174bd6b48998e_162.png)

![](img/f95c0e5a85990870276174bd6b48998e_163.png)

![](img/f95c0e5a85990870276174bd6b48998e_164.png)

![](img/f95c0e5a85990870276174bd6b48998e_165.png)

![](img/f95c0e5a85990870276174bd6b48998e_167.png)

![](img/f95c0e5a85990870276174bd6b48998e_168.png)

![](img/f95c0e5a85990870276174bd6b48998e_170.png)

6. **创建网页文件**：在Web目录中创建一个网页文件，并设置SELinux上下文。
   ```yaml
   - name: Create web page
     copy:
       content: 'Welcome to webdev'
       dest: /var/www/html/webdev/index.html
     secontext:
       type: httpd_sys_content_t
   ```

![](img/f95c0e5a85990870276174bd6b48998e_172.png)

### 注意事项

![](img/f95c0e5a85990870276174bd6b48998e_174.png)

![](img/f95c0e5a85990870276174bd6b48998e_175.png)

在创建Web目录时，需要注意权限设置和SELinux上下文的配置，否则可能导致Web服务无法正常访问。

![](img/f95c0e5a85990870276174bd6b48998e_177.png)

![](img/f95c0e5a85990870276174bd6b48998e_179.png)

![](img/f95c0e5a85990870276174bd6b48998e_181.png)

![](img/f95c0e5a85990870276174bd6b48998e_182.png)

![](img/f95c0e5a85990870276174bd6b48998e_183.png)

## 生成硬件报告

![](img/f95c0e5a85990870276174bd6b48998e_185.png)

上一节我们介绍了如何创建Web网站目录，本节中我们来看看如何使用Ansible生成硬件报告。这个实验要求我们从远程服务器下载一个模板文件，并根据主机的硬件信息填充变量，最终生成硬件报告文件。

![](img/f95c0e5a85990870276174bd6b48998e_187.png)

![](img/f95c0e5a85990870276174bd6b48998e_188.png)

![](img/f95c0e5a85990870276174bd6b48998e_189.png)

![](img/f95c0e5a85990870276174bd6b48998e_191.png)

![](img/f95c0e5a85990870276174bd6b48998e_192.png)

![](img/f95c0e5a85990870276174bd6b48998e_193.png)

![](img/f95c0e5a85990870276174bd6b48998e_194.png)

### 实验步骤

![](img/f95c0e5a85990870276174bd6b48998e_195.png)

以下是生成硬件报告的具体步骤：

![](img/f95c0e5a85990870276174bd6b48998e_197.png)

![](img/f95c0e5a85990870276174bd6b48998e_199.png)

![](img/f95c0e5a85990870276174bd6b48998e_201.png)

![](img/f95c0e5a85990870276174bd6b48998e_202.png)

1. **下载模板文件**：从远程服务器下载模板文件到本地。
   ```bash
   wget http://172.25.250.254/hardware_report.j2
   ```

![](img/f95c0e5a85990870276174bd6b48998e_204.png)

![](img/f95c0e5a85990870276174bd6b48998e_205.png)

![](img/f95c0e5a85990870276174bd6b48998e_206.png)

![](img/f95c0e5a85990870276174bd6b48998e_208.png)

2. **定义变量组**：在Playbook中定义一个变量组，包含硬件信息的变量。
   ```yaml
   vars:
     hardware:
       - name: inventory_hostname
         value: "{{ inventory_hostname }}"
       - name: memory_mb
         value: "{{ ansible_memory_mb['real']['total'] }}"
   ```

![](img/f95c0e5a85990870276174bd6b48998e_210.png)

![](img/f95c0e5a85990870276174bd6b48998e_212.png)

![](img/f95c0e5a85990870276174bd6b48998e_213.png)

3. **使用循环和条件判断**：通过循环和条件判断，将变量填充到模板文件中。
   ```yaml
   - name: Generate hardware report
     lineinfile:
       path: /tmp/hardware_report.txt
       regexp: "{{ item.name }}"
       line: "{{ item.name }} = {{ item.value | default('N/A') }}"
     loop: "{{ hardware }}"
   ```

![](img/f95c0e5a85990870276174bd6b48998e_215.png)

![](img/f95c0e5a85990870276174bd6b48998e_216.png)

![](img/f95c0e5a85990870276174bd6b48998e_217.png)

![](img/f95c0e5a85990870276174bd6b48998e_218.png)

### 核心概念

![](img/f95c0e5a85990870276174bd6b48998e_220.png)

![](img/f95c0e5a85990870276174bd6b48998e_221.png)

![](img/f95c0e5a85990870276174bd6b48998e_223.png)

![](img/f95c0e5a85990870276174bd6b48998e_224.png)

![](img/f95c0e5a85990870276174bd6b48998e_225.png)

![](img/f95c0e5a85990870276174bd6b48998e_227.png)

在这个实验中，我们使用了变量组和循环语句来动态生成硬件报告。通过`default`过滤器，可以处理变量不存在的情况。

![](img/f95c0e5a85990870276174bd6b48998e_228.png)

![](img/f95c0e5a85990870276174bd6b48998e_230.png)

![](img/f95c0e5a85990870276174bd6b48998e_231.png)

![](img/f95c0e5a85990870276174bd6b48998e_232.png)

![](img/f95c0e5a85990870276174bd6b48998e_233.png)

## 创建密码库

![](img/f95c0e5a85990870276174bd6b48998e_235.png)

![](img/f95c0e5a85990870276174bd6b48998e_236.png)

上一节我们介绍了如何生成硬件报告，本节中我们来看看如何使用Ansible创建密码库。这个实验要求我们创建一个加密的密码库，用于安全存储敏感信息。

![](img/f95c0e5a85990870276174bd6b48998e_238.png)

![](img/f95c0e5a85990870276174bd6b48998e_240.png)

![](img/f95c0e5a85990870276174bd6b48998e_241.png)

![](img/f95c0e5a85990870276174bd6b48998e_242.png)

### 实验步骤

![](img/f95c0e5a85990870276174bd6b48998e_244.png)

![](img/f95c0e5a85990870276174bd6b48998e_245.png)

![](img/f95c0e5a85990870276174bd6b48998e_247.png)

![](img/f95c0e5a85990870276174bd6b48998e_248.png)

![](img/f95c0e5a85990870276174bd6b48998e_249.png)

以下是创建密码库的具体步骤：

![](img/f95c0e5a85990870276174bd6b48998e_251.png)

![](img/f95c0e5a85990870276174bd6b48998e_252.png)

![](img/f95c0e5a85990870276174bd6b48998e_253.png)

![](img/f95c0e5a85990870276174bd6b48998e_254.png)

![](img/f95c0e5a85990870276174bd6b48998e_256.png)

1. **创建密码文件**：创建一个密码文件，用于存储加密密钥。
   ```bash
   echo "your_password" > /home/ansible/vault_password.txt
   ```

![](img/f95c0e5a85990870276174bd6b48998e_258.png)

![](img/f95c0e5a85990870276174bd6b48998e_259.png)

2. **配置Ansible**：在Ansible配置文件中指定密码文件的路径。
   ```ini
   vault_password_file = /home/ansible/vault_password.txt
   ```

![](img/f95c0e5a85990870276174bd6b48998e_261.png)

![](img/f95c0e5a85990870276174bd6b48998e_262.png)

3. **创建加密变量文件**：创建一个变量文件，并使用Ansible Vault进行加密。
   ```bash
   ansible-vault create vars.yml
   ```

![](img/f95c0e5a85990870276174bd6b48998e_264.png)

![](img/f95c0e5a85990870276174bd6b48998e_266.png)

4. **在Playbook中使用加密变量**：在Playbook中加载加密的变量文件，并使用其中的变量。
   ```yaml
   ---
   - name: Use encrypted variables
     hosts: all
     vars_files:
       - vars.yml
     tasks:
       - name: Display secret variable
         debug:
           msg: "{{ secret_variable }}"
   ```

![](img/f95c0e5a85990870276174bd6b48998e_268.png)

![](img/f95c0e5a85990870276174bd6b48998e_269.png)

![](img/f95c0e5a85990870276174bd6b48998e_271.png)

![](img/f95c0e5a85990870276174bd6b48998e_272.png)

### 注意事项

![](img/f95c0e5a85990870276174bd6b48998e_274.png)

![](img/f95c0e5a85990870276174bd6b48998e_275.png)

![](img/f95c0e5a85990870276174bd6b48998e_276.png)

![](img/f95c0e5a85990870276174bd6b48998e_278.png)

![](img/f95c0e5a85990870276174bd6b48998e_279.png)

![](img/f95c0e5a85990870276174bd6b48998e_280.png)

![](img/f95c0e5a85990870276174bd6b48998e_282.png)

![](img/f95c0e5a85990870276174bd6b48998e_283.png)

![](img/f95c0e5a85990870276174bd6b48998e_284.png)

在创建密码库时，需要确保密码文件的安全，避免泄露敏感信息。

![](img/f95c0e5a85990870276174bd6b48998e_285.png)

![](img/f95c0e5a85990870276174bd6b48998e_287.png)

![](img/f95c0e5a85990870276174bd6b48998e_289.png)

![](img/f95c0e5a85990870276174bd6b48998e_291.png)

![](img/f95c0e5a85990870276174bd6b48998e_292.png)

![](img/f95c0e5a85990870276174bd6b48998e_294.png)

## 创建用户账户

![](img/f95c0e5a85990870276174bd6b48998e_296.png)

![](img/f95c0e5a85990870276174bd6b48998e_298.png)

上一节我们介绍了如何创建密码库，本节中我们来看看如何使用Ansible创建用户账户。这个实验要求我们根据用户信息文件，在指定的主机组中创建用户账户，并设置密码和组信息。

![](img/f95c0e5a85990870276174bd6b48998e_300.png)

![](img/f95c0e5a85990870276174bd6b48998e_301.png)

### 实验步骤

![](img/f95c0e5a85990870276174bd6b48998e_303.png)

![](img/f95c0e5a85990870276174bd6b48998e_305.png)

![](img/f95c0e5a85990870276174bd6b48998e_307.png)

以下是创建用户账户的具体步骤：

![](img/f95c0e5a85990870276174bd6b48998e_309.png)

![](img/f95c0e5a85990870276174bd6b48998e_310.png)

![](img/f95c0e5a85990870276174bd6b48998e_311.png)

![](img/f95c0e5a85990870276174bd6b48998e_313.png)

![](img/f95c0e5a85990870276174bd6b48998e_314.png)

![](img/f95c0e5a85990870276174bd6b48998e_316.png)

![](img/f95c0e5a85990870276174bd6b48998e_317.png)

1. **加载用户信息文件**：在Playbook中加载用户信息文件和密码文件。
   ```yaml
   vars_files:
     - /home/ansible/user_list.yml
     - /home/ansible/vault.yml
   ```

![](img/f95c0e5a85990870276174bd6b48998e_319.png)

![](img/f95c0e5a85990870276174bd6b48998e_320.png)

![](img/f95c0e5a85990870276174bd6b48998e_321.png)

![](img/f95c0e5a85990870276174bd6b48998e_322.png)

![](img/f95c0e5a85990870276174bd6b48998e_324.png)

![](img/f95c0e5a85990870276174bd6b48998e_325.png)

![](img/f95c0e5a85990870276174bd6b48998e_327.png)

2. **使用循环和条件判断**：通过循环和条件判断，根据用户信息创建用户账户。
   ```yaml
   - name: Create users for dev group
     user:
       name: "{{ item.name }}"
       password: "{{ item.password | password_hash('sha512') }}"
       groups: "{{ item.groups }}"
     loop: "{{ users }}"
     when: "'dev' in group_names and item.role == 'developer'"
   ```

![](img/f95c0e5a85990870276174bd6b48998e_329.png)

![](img/f95c0e5a85990870276174bd6b48998e_331.png)

### 核心概念

![](img/f95c0e5a85990870276174bd6b48998e_333.png)

![](img/f95c0e5a85990870276174bd6b48998e_334.png)

在这个实验中，我们使用了`password_hash`过滤器对密码进行加密，确保密码的安全性。同时，通过`when`语句和`group_names`变量，实现了条件判断和组过滤。

![](img/f95c0e5a85990870276174bd6b48998e_336.png)

![](img/f95c0e5a85990870276174bd6b48998e_337.png)

![](img/f95c0e5a85990870276174bd6b48998e_339.png)

![](img/f95c0e5a85990870276174bd6b48998e_340.png)

## 更新密码库密钥

![](img/f95c0e5a85990870276174bd6b48998e_342.png)

![](img/f95c0e5a85990870276174bd6b48998e_343.png)

上一节我们介绍了如何创建用户账户，本节中我们来看看如何使用Ansible更新密码库的密钥。这个实验要求我们下载一个加密文件，并更新其中的密码。

![](img/f95c0e5a85990870276174bd6b48998e_345.png)

![](img/f95c0e5a85990870276174bd6b48998e_346.png)

![](img/f95c0e5a85990870276174bd6b48998e_347.png)

![](img/f95c0e5a85990870276174bd6b48998e_348.png)

![](img/f95c0e5a85990870276174bd6b48998e_350.png)

![](img/f95c0e5a85990870276174bd6b48998e_351.png)

![](img/f95c0e5a85990870276174bd6b48998e_353.png)

![](img/f95c0e5a85990870276174bd6b48998e_354.png)

### 实验步骤

![](img/f95c0e5a85990870276174bd6b48998e_356.png)

![](img/f95c0e5a85990870276174bd6b48998e_357.png)

![](img/f95c0e5a85990870276174bd6b48998e_358.png)

![](img/f95c0e5a85990870276174bd6b48998e_359.png)

以下是更新密码库密钥的具体步骤：

![](img/f95c0e5a85990870276174bd6b48998e_361.png)

![](img/f95c0e5a85990870276174bd6b48998e_362.png)

![](img/f95c0e5a85990870276174bd6b48998e_363.png)

![](img/f95c0e5a85990870276174bd6b48998e_364.png)

![](img/f95c0e5a85990870276174bd6b48998e_366.png)

![](img/f95c0e5a85990870276174bd6b48998e_367.png)

1. **下载加密文件**：从远程服务器下载加密文件到本地。
   ```bash
   wget http://172.25.250.254/encrypted.yml
   ```

![](img/f95c0e5a85990870276174bd6b48998e_369.png)

2. **更新密码**：使用Ansible Vault更新加密文件中的密码。
   ```bash
   ansible-vault rekey encrypted.yml
   ```

![](img/f95c0e5a85990870276174bd6b48998e_371.png)

![](img/f95c0e5a85990870276174bd6b48998e_372.png)

![](img/f95c0e5a85990870276174bd6b48998e_373.png)

![](img/f95c0e5a85990870276174bd6b48998e_374.png)

![](img/f95c0e5a85990870276174bd6b48998e_376.png)

![](img/f95c0e5a85990870276174bd6b48998e_377.png)

### 注意事项

![](img/f95c0e5a85990870276174bd6b48998e_379.png)

![](img/f95c0e5a85990870276174bd6b48998e_380.png)

![](img/f95c0e5a85990870276174bd6b48998e_382.png)

![](img/f95c0e5a85990870276174bd6b48998e_383.png)

在更新密码时，需要确保新密码的安全性，并妥善保管密码文件。

![](img/f95c0e5a85990870276174bd6b48998e_384.png)

![](img/f95c0e5a85990870276174bd6b48998e_386.png)

![](img/f95c0e5a85990870276174bd6b48998e_387.png)

## 隐藏题目

在RHCE考试中，可能会有一些隐藏题目，用于替换原有的题目。这些题目通常涉及计划任务、分区管理或系统角色的使用。以下是隐藏题目的简要介绍：

1. **计划任务**：配置一个计划任务，每两分钟执行一次指定的命令。
2. **分区管理**：创建一个分区，并根据分区大小进行格式化或挂载操作。
3. **系统角色**：使用系统自带的角色，如时间同步或SELinux管理角色。

![](img/f95c0e5a85990870276174bd6b48998e_389.png)

### 实验步骤

![](img/f95c0e5a85990870276174bd6b48998e_391.png)

![](img/f95c0e5a85990870276174bd6b48998e_392.png)

![](img/f95c0e5a85990870276174bd6b48998e_394.png)

以下是隐藏题目的具体步骤：

1. **计划任务**：
   ```yaml
   - name: Schedule a cron job
     cron:
       name: "Test cron job"
       minute: "*/2"
       user: ansible
       job: "echo 'Hello World'"
   ```

![](img/f95c0e5a85990870276174bd6b48998e_396.png)

![](img/f95c0e5a85990870276174bd6b48998e_397.png)

2. **分区管理**：
   ```yaml
   - name: Create partition
     parted:
       device: /dev/vdb
       number: 1
       state: present
       part_end: "1500MB"
   ```

![](img/f95c0e5a85990870276174bd6b48998e_399.png)

![](img/f95c0e5a85990870276174bd6b48998e_401.png)

![](img/f95c0e5a85990870276174bd6b48998e_402.png)

![](img/f95c0e5a85990870276174bd6b48998e_403.png)

![](img/f95c0e5a85990870276174bd6b48998e_404.png)

![](img/f95c0e5a85990870276174bd6b48998e_406.png)

3. **系统角色**：
   ```yaml
   - name: Use system role
     include_role:
       name: rhel-system-roles.selinux
   ```

![](img/f95c0e5a85990870276174bd6b48998e_407.png)

![](img/f95c0e5a85990870276174bd6b48998e_409.png)

![](img/f95c0e5a85990870276174bd6b48998e_410.png)

![](img/f95c0e5a85990870276174bd6b48998e_411.png)

![](img/f95c0e5a85990870276174bd6b48998e_412.png)

![](img/f95c0e5a85990870276174bd6b48998e_414.png)

![](img/f95c0e5a85990870276174bd6b48998e_415.png)

![](img/f95c0e5a85990870276174bd6b48998e_416.png)

![](img/f95c0e5a85990870276174bd6b48998e_417.png)

### 注意事项

![](img/f95c0e5a85990870276174bd6b48998e_419.png)

![](img/f95c0e5a85990870276174bd6b48998e_421.png)

![](img/f95c0e5a85990870276174bd6b48998e_422.png)

在隐藏题目中，需要仔细阅读题目要求，确保操作符合考试标准。

![](img/f95c0e5a85990870276174bd6b48998e_424.png)

![](img/f95c0e5a85990870276174bd6b48998e_425.png)

## 总结

![](img/f95c0e5a85990870276174bd6b48998e_427.png)

本节课中我们一起学习了RHCE考试中的多个高级实验，包括生成主机文件、修改文件内容、创建Web网站目录、生成硬件报告、创建密码库、创建用户账户以及更新密码库密钥。通过这些实验，我们掌握了Ansible的模板技术、变量管理、条件判断、循环语句、文件操作、用户管理和加密库的使用。希望本课程能够帮助您顺利通过RHCE考试，并在实际工作中应用所学知识。