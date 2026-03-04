# RHCE备考课程：P15：Ansible进阶实战 🚀

![](img/20e5ba47bb84401ba422c1b630291411_1.png)

![](img/20e5ba47bb84401ba422c1b630291411_3.png)

![](img/20e5ba47bb84401ba422c1b630291411_5.png)

![](img/20e5ba47bb84401ba422c1b630291411_7.png)

![](img/20e5ba47bb84401ba422c1b630291411_9.png)

![](img/20e5ba47bb84401ba422c1b630291411_11.png)

![](img/20e5ba47bb84401ba422c1b630291411_13.png)

![](img/20e5ba47bb84401ba422c1b630291411_15.png)

![](img/20e5ba47bb84401ba422c1b630291411_17.png)

![](img/20e5ba47bb84401ba422c1b630291411_19.png)

在本节课中，我们将深入学习Ansible的进阶应用，包括模板技术、变量管理、条件判断、循环控制以及加密库的使用。课程内容基于RHCE8考试要求，通过一系列实战题目，帮助大家掌握Ansible的核心技能。

![](img/20e5ba47bb84401ba422c1b630291411_21.png)

![](img/20e5ba47bb84401ba422c1b630291411_23.png)

![](img/20e5ba47bb84401ba422c1b630291411_25.png)

![](img/20e5ba47bb84401ba422c1b630291411_27.png)

![](img/20e5ba47bb84401ba422c1b630291411_29.png)

![](img/20e5ba47bb84401ba422c1b630291411_31.png)

---

![](img/20e5ba47bb84401ba422c1b630291411_33.png)

![](img/20e5ba47bb84401ba422c1b630291411_35.png)

![](img/20e5ba47bb84401ba422c1b630291411_37.png)

![](img/20e5ba47bb84401ba422c1b630291411_39.png)

## 课程安排与准备 📋

![](img/20e5ba47bb84401ba422c1b630291411_41.png)

![](img/20e5ba47bb84401ba422c1b630291411_43.png)

我们今天将继续讲解RHCE的Ansible部分，预计课程时间较长，请大家做好准备。

![](img/20e5ba47bb84401ba422c1b630291411_45.png)

![](img/20e5ba47bb84401ba422c1b630291411_47.png)

![](img/20e5ba47bb84401ba422c1b630291411_49.png)

![](img/20e5ba47bb84401ba422c1b630291411_51.png)

![](img/20e5ba47bb84401ba422c1b630291411_53.png)

**以下是课程开始前的准备工作：**

![](img/20e5ba47bb84401ba422c1b630291411_55.png)

![](img/20e5ba47bb84401ba422c1b630291411_57.png)

![](img/20e5ba47bb84401ba422c1b630291411_59.png)

![](img/20e5ba47bb84401ba422c1b630291411_61.png)

![](img/20e5ba47bb84401ba422c1b630291411_63.png)

*   请准备好记录有IP地址和对应组名称的纸张，以便快速查阅。
*   今天的课程将重点讲解网页上提供的“解题方法二”，该方法更稳定可靠。
*   我们将从第9道题目开始，涵盖第28、29、30题，总计160分。

![](img/20e5ba47bb84401ba422c1b630291411_65.png)

![](img/20e5ba47bb84401ba422c1b630291411_67.png)

![](img/20e5ba47bb84401ba422c1b630291411_69.png)

![](img/20e5ba47bb84401ba422c1b630291411_71.png)

---

![](img/20e5ba47bb84401ba422c1b630291411_73.png)

![](img/20e5ba47bb84401ba422c1b630291411_75.png)

![](img/20e5ba47bb84401ba422c1b630291411_77.png)

![](img/20e5ba47bb84401ba422c1b630291411_79.png)

## 题目九：生成主机文件 📄

![](img/20e5ba47bb84401ba422c1b630291411_81.png)

![](img/20e5ba47bb84401ba422c1b630291411_83.png)

![](img/20e5ba47bb84401ba422c1b630291411_85.png)

![](img/20e5ba47bb84401ba422c1b630291411_87.png)

![](img/20e5ba47bb84401ba422c1b630291411_89.png)

![](img/20e5ba47bb84401ba422c1b630291411_91.png)

上一节我们介绍了Ansible的基础操作，本节中我们来看看如何利用模板技术动态生成文件。

![](img/20e5ba47bb84401ba422c1b630291411_93.png)

![](img/20e5ba47bb84401ba422c1b630291411_95.png)

![](img/20e5ba47bb84401ba422c1b630291411_97.png)

**题目要求分析：**
1.  从一个远程模板文件下载初始模板到本地。
2.  编辑该模板文件，使用Ansible变量填充内容，使其输出特定格式的主机信息。
3.  创建一个Playbook，仅在`dv`主机组上运行，将编辑好的模板文件复制到目标主机，并生成最终的主机文件。

![](img/20e5ba47bb84401ba422c1b630291411_99.png)

![](img/20e5ba47bb84401ba422c1b630291411_101.png)

![](img/20e5ba47bb84401ba422c1b630291411_103.png)

![](img/20e5ba47bb84401ba422c1b630291411_105.png)

**核心步骤与操作：**

![](img/20e5ba47bb84401ba422c1b630291411_107.png)

![](img/20e5ba47bb84401ba422c1b630291411_109.png)

![](img/20e5ba47bb84401ba422c1b630291411_111.png)

![](img/20e5ba47bb84401ba422c1b630291411_113.png)

![](img/20e5ba47bb84401ba422c1b630291411_115.png)

![](img/20e5ba47bb84401ba422c1b630291411_117.png)

![](img/20e5ba47bb84401ba422c1b630291411_119.png)

**1. 下载模板文件**
首先，我们需要以`greg`用户身份登录到控制节点，并下载远程模板文件。
```bash
wget http://172.25.250.254/hosts.j2
```

![](img/20e5ba47bb84401ba422c1b630291411_121.png)

![](img/20e5ba47bb84401ba422c1b630291411_123.png)

![](img/20e5ba47bb84401ba422c1b630291411_125.png)

![](img/20e5ba47bb84401ba422c1b630291411_127.png)

**2. 编辑模板文件（使用Jinja2技术）**
下载的模板文件内容不完整，我们需要使用Jinja2循环语句和魔法变量来动态填充每个主机的信息。编辑`hosts.j2`文件，添加以下内容：
```jinja2
{% for host in groups['all'] %}
{{ hostvars[host]['ansible_default_ipv4']['address'] }}   {{ hostvars[host]['ansible_fqdn'] }}   {{ host }}
{% endfor %}
```
**关键点说明：**
*   `{% for ... in ... %}` 和 `{% endfor %}` 构成了一个循环结构。
*   `groups['all']` 获取所有受管主机。
*   `hostvars[host]` 用于访问特定主机的变量。
*   `ansible_default_ipv4.address` 和 `ansible_fqdn` 是Ansible收集的事实变量，分别代表IP地址和完全限定域名。
*   **重要**：字段之间的间隔是一个**制表符（Tab）**，而非空格。最终文件的第一部分（静态内容）和循环生成部分之间**没有空行**。

![](img/20e5ba47bb84401ba422c1b630291411_129.png)

![](img/20e5ba47bb84401ba422c1b630291411_131.png)

**3. 创建并运行Playbook**
创建一个名为`hosts.yml`的Playbook，使用`template`模块将模板文件渲染并复制到目标主机。
```yaml
---
- name: Generate hosts file
  hosts: dv
  tasks:
    - name: Copy template to manage node
      template:
        src: /home/greg/ansible/hosts.j2
        dest: /home/greg/ansible/hosts
```
**模块解析：**
*   `template`模块：与`copy`模块类似，但会在复制前解析文件中的Jinja2模板变量。

![](img/20e5ba47bb84401ba422c1b630291411_133.png)

![](img/20e5ba47bb84401ba422c1b630291411_135.png)

![](img/20e5ba47bb84401ba422c1b630291411_137.png)

![](img/20e5ba47bb84401ba422c1b630291411_139.png)

![](img/20e5ba47bb84401ba422c1b630291411_141.png)

---

![](img/20e5ba47bb84401ba422c1b630291411_143.png)

![](img/20e5ba47bb84401ba422c1b630291411_145.png)

![](img/20e5ba47bb84401ba422c1b630291411_147.png)

![](img/20e5ba47bb84401ba422c1b630291411_149.png)

## 题目十：修改文件内容 ✏️

![](img/20e5ba47bb84401ba422c1b630291411_151.png)

![](img/20e5ba47bb84401ba422c1b630291411_153.png)

![](img/20e5ba47bb84401ba422c1b630291411_155.png)

本节我们将学习如何在Playbook中使用条件判断，根据不同主机组修改文件内容。

![](img/20e5ba47bb84401ba422c1b630291411_157.png)

![](img/20e5ba47bb84401ba422c1b630291411_159.png)

![](img/20e5ba47bb84401ba422c1b630291411_161.png)

![](img/20e5ba47bb84401ba422c1b630291411_163.png)

![](img/20e5ba47bb84401ba422c1b630291411_165.png)

![](img/20e5ba47bb84401ba422c1b630291411_167.png)

**题目要求分析：**
创建一个Playbook，在所有受管主机上运行，但根据主机所属组（`dv`, `test`, `prod`）的不同，将指定文件的内容修改为不同的字符串。

![](img/20e5ba47bb84401ba422c1b630291411_169.png)

![](img/20e5ba47bb84401ba422c1b630291411_171.png)

**核心步骤与操作：**

![](img/20e5ba47bb84401ba422c1b630291411_173.png)

![](img/20e5ba47bb84401ba422c1b630291411_175.png)

**1. 创建Playbook**
创建名为`filecontent.yml`的Playbook。

![](img/20e5ba47bb84401ba422c1b630291411_177.png)

![](img/20e5ba47bb84401ba422c1b630291411_179.png)

![](img/20e5ba47bb84401ba422c1b630291411_181.png)

![](img/20e5ba47bb84401ba422c1b630291411_183.png)

**2. 使用条件判断（when）**
在Playbook中，我们使用`copy`模块的`content`参数直接修改文件内容，并结合`when`条件语句进行判断。
```yaml
---
- name: Modify file content based on group
  hosts: all
  tasks:
    - name: For dv group
      copy:
        content: 'development'
        dest: /home/greg/ansible/file.txt
      when: '"dv" in group_names'

    - name: For test group
      copy:
        content: 'test'
        dest: /home/greg/ansible/file.txt
      when: '"test" in group_names'

    - name: For prod group
      copy:
        content: 'production'
        dest: /home/greg/ansible/file.txt
      when: '"prod" in group_names'
```
**关键点说明：**
*   `group_names` 是一个列表变量，包含当前主机所属的所有组名。
*   `when` 语句用于条件判断，其缩进层级与它所属的`task`模块对齐。
*   `content`参数的值需要用单引号括起来。

---

![](img/20e5ba47bb84401ba422c1b630291411_185.png)

![](img/20e5ba47bb84401ba422c1b630291411_187.png)

## 题目十一：创建Web内容目录 🌐

![](img/20e5ba47bb84401ba422c1b630291411_189.png)

![](img/20e5ba47bb84401ba422c1b630291411_191.png)

![](img/20e5ba47bb84401ba422c1b630291411_193.png)

![](img/20e5ba47bb84401ba422c1b630291411_195.png)

现在，我们来看一个综合性的题目，它涉及服务管理、用户/组管理、文件权限、链接文件以及SELinux上下文设置。

![](img/20e5ba47bb84401ba422c1b630291411_197.png)

![](img/20e5ba47bb84401ba422c1b630291411_199.png)

![](img/20e5ba47bb84401ba422c1b630291411_201.png)

![](img/20e5ba47bb84401ba422c1b630291411_203.png)

**题目要求分析：**
在`dv`主机组上创建一个Web内容目录结构，具体要求包括：启动httpd服务、放行防火墙、创建目录并设置权限和归属、创建符号链接、创建网页文件，并设置正确的SELinux安全上下文。

![](img/20e5ba47bb84401ba422c1b630291411_205.png)

![](img/20e5ba47bb84401ba422c1b630291411_207.png)

![](img/20e5ba47bb84401ba422c1b630291411_209.png)

**核心步骤与操作：**

**1. 创建Playbook**
创建名为`webcontent.yml`的Playbook，目标主机为`dv`组。

![](img/20e5ba47bb84401ba422c1b630291411_211.png)

![](img/20e5ba47bb84401ba422c1b630291411_213.png)

![](img/20e5ba47bb84401ba422c1b630291411_215.png)

![](img/20e5ba47bb84401ba422c1b630291411_217.png)

**2. 前置任务（考试中可能需要的隐藏步骤）**
根据经验，目标主机可能未安装或启动Web服务，防火墙也可能未放行。因此，Playbook需要包含这些前置任务。
```yaml
---
- name: Configure web content directory
  hosts: dv
  tasks:
    # 前置任务 1: 确保httpd服务运行并启用
    - name: Start and enable httpd service
      service:
        name: httpd
        state: started
        enabled: yes

    # 前置任务 2: 放行防火墙
    - name: Configure firewall for httpd
      firewalld:
        service: http
        permanent: yes
        immediate: yes
        state: enabled

    # 前置任务 3: 创建所需的用户组（考题中提及但可能不存在）
    - name: Create webdev group
      group:
        name: webdev
        state: present

    # 考题任务 1: 创建目录并设置权限
    - name: Create web directory
      file:
        path: /home/greg/ansible/webdev
        state: directory
        owner: greg
        group: webdev
        mode: '2775'

    # 考题任务 2: 创建符号链接（注意源和目标的顺序）
    - name: Create symbolic link
      file:
        src: /home/greg/ansible/webdev
        dest: /var/www/html/webdev
        state: link

    # 考题任务 3: 创建网页文件
    - name: Create index.html
      copy:
        content: 'Hello from greg'
        dest: /home/greg/ansible/webdev/index.html

    # 考题任务 4: 设置SELinux上下文（确保Web服务可访问）
    - name: Set SELinux context for web directory
      sefcontext:
        target: '/home/greg/ansible/webdev(/.*)?'
        setype: httpd_sys_content_t
        state: present
    - name: Apply SELinux context
      shell: restorecon -Rv /home/greg/ansible/webdev/
```
**关键点说明：**
*   `mode: '2775'`：`2`表示设置SGID特殊权限，`775`是一般权限（所有者7，所属组7，其他人5）。
*   符号链接的创建：根据考题反馈，有时需要反方向创建链接，即从Ansible目录链接到Web根目录，以确保内容同步。
*   SELinux上下文：为了让Apache进程能够读取非标准路径下的Web文件，必须将其安全上下文设置为`httpd_sys_content_t`。

![](img/20e5ba47bb84401ba422c1b630291411_219.png)

![](img/20e5ba47bb84401ba422c1b630291411_221.png)

---

![](img/20e5ba47bb84401ba422c1b630291411_223.png)

![](img/20e5ba47bb84401ba422c1b630291411_225.png)

![](img/20e5ba47bb84401ba422c1b630291411_227.png)

![](img/20e5ba47bb84401ba422c1b630291411_229.png)

![](img/20e5ba47bb84401ba422c1b630291411_231.png)

![](img/20e5ba47bb84401ba422c1b630291411_233.png)

## 题目十二：生成硬件报告 🖥️

![](img/20e5ba47bb84401ba422c1b630291411_235.png)

![](img/20e5ba47bb84401ba422c1b630291411_237.png)

这是一道难度较高的题目，考察了复杂变量处理、循环、文件下载和内容替换的综合能力。

![](img/20e5ba47bb84401ba422c1b630291411_239.png)

![](img/20e5ba47bb84401ba422c1b630291411_241.png)

![](img/20e5ba47bb84401ba422c1b630291411_243.png)

**题目要求分析：**
1.  创建一个Playbook，在所有受管节点上运行。
2.  从远程下载一个包含硬件信息键值对模板的文件。
3.  将模板文件中的值（value）替换为从各个主机上获取的实际硬件信息变量（如主机名、内存总量、BIOS版本、磁盘大小等）。
4.  如果某个硬件信息不存在，则将其值设置为`None`。

![](img/20e5ba47bb84401ba422c1b630291411_245.png)

![](img/20e5ba47bb84401ba422c1b630291411_247.png)

**核心思路与操作：**

![](img/20e5ba47bb84401ba422c1b630291411_249.png)

![](img/20e5ba47bb84401ba422c1b630291411_251.png)

![](img/20e5ba47bb84401ba422c1b630291411_253.png)

**1. 定义变量字典**
首先，在Playbook中定义一个包含所有所需硬件信息变量的字典（或列表的字典）。
```yaml
---
- name: Generate hardware report
  hosts: all
  vars:
    hardware_all:
      - name: inventory_hostname
        value: "{{ inventory_hostname }}"
      - name: Memory
        value: "{{ ansible_memtotal_mb | default('None') }}"
      - name: BIOS
        value: "{{ ansible_bios_version | default('None') }}"
      - name: vda_size
        value: "{{ ansible_devices.vda.size | default('None') }}"
      - name: vdb_size
        value: "{{ ansible_devices.vdb.size | default('None') }}"
```
**关键点说明：**
*   `inventory_hostname` 是魔法变量，代表主机在清单中的名称。
*   `ansible_memtotal_mb` 等是事实变量，通过`gathering_facts`阶段收集。
*   `| default('None')` 是Jinja2过滤器，如果变量未定义，则使用默认值`'None'`。

![](img/20e5ba47bb84401ba422c1b630291411_255.png)

![](img/20e5ba47bb84401ba422c1b630291411_257.png)

![](img/20e5ba47bb84401ba422c1b630291411_259.png)

![](img/20e5ba47bb84401ba422c1b630291411_261.png)

![](img/20e5ba47bb84401ba422c1b630291411_263.png)

![](img/20e5ba47bb84401ba422c1b630291411_265.png)

**2. 下载模板文件**
使用`get_url`模块下载远程模板文件到每个受管主机。
```yaml
  tasks:
    - name: Download template file
      get_url:
        url: http://172.25.250.254/hardware.empty
        dest: /home/greg/ansible/hardware.txt
```

![](img/20e5ba47bb84401ba422c1b630291411_267.png)

**3. 使用循环和行替换**
使用`lineinfile`模块，循环遍历`hardware_all`变量字典，在目标文件中查找匹配的键（`name`），并将其对应的行替换为`键=值`的格式。
```yaml
    - name: Replace values in hardware file
      lineinfile:
        path: /home/greg/ansible/hardware.txt
        regexp: "^{{ item.name }}="
        line: "{{ item.name }}={{ item.value }}"
      loop: "{{ hardware_all }}"
```
**关键点说明：**
*   `lineinfile`模块：用于确保某一行文本存在于文件中。
*   `regexp`: 使用正则表达式匹配以`item.name`开头、后接等号的行。
*   `loop`: 对`hardware_all`列表中的每个元素（`item`）执行此任务。

![](img/20e5ba47bb84401ba422c1b630291411_269.png)

![](img/20e5ba47bb84401ba422c1b630291411_271.png)

![](img/20e5ba47bb84401ba422c1b630291411_273.png)

![](img/20e5ba47bb84401ba422c1b630291411_275.png)

---

![](img/20e5ba47bb84401ba422c1b630291411_277.png)

![](img/20e5ba47bb84401ba422c1b630291411_279.png)

![](img/20e5ba47bb84401ba422c1b630291411_281.png)

![](img/20e5ba47bb84401ba422c1b630291411_283.png)

![](img/20e5ba47bb84401ba422c1b630291411_285.png)

## 题目十三：创建Ansible密码库 🔐

![](img/20e5ba47bb84401ba422c1b630291411_287.png)

![](img/20e5ba47bb84401ba422c1b630291411_289.png)

![](img/20e5ba47bb84401ba422c1b630291411_291.png)

![](img/20e5ba47bb84401ba422c1b630291411_293.png)

![](img/20e5ba47bb84401ba422c1b630291411_295.png)

本节我们学习如何管理Ansible中的敏感数据，例如密码。

![](img/20e5ba47bb84401ba422c1b630291411_297.png)

![](img/20e5ba47bb84401ba422c1b630291411_299.png)

**题目要求分析：**
1.  创建一个用于存储Ansible Vault密码的文件。
2.  配置Ansible，使其使用该密码文件。
3.  创建一个包含加密变量的YAML文件。
4.  使用Vault加密该文件。

![](img/20e5ba47bb84401ba422c1b630291411_301.png)

![](img/20e5ba47bb84401ba422c1b630291411_303.png)

**核心步骤与操作：**

![](img/20e5ba47bb84401ba422c1b630291411_305.png)

![](img/20e5ba47bb84401ba422c1b630291411_307.png)

![](img/20e5ba47bb84401ba422c1b630291411_309.png)

![](img/20e5ba47bb84401ba422c1b630291411_311.png)

![](img/20e5ba47bb84401ba422c1b630291411_313.png)

![](img/20e5ba47bb84401ba422c1b630291411_315.png)

**1. 创建密码文件**
在控制节点上创建密码文件，并写入密码。
```bash
echo 'when you wish upon a star' > /home/greg/ansible/secret.txt
```

![](img/20e5ba47bb84401ba422c1b630291411_317.png)

![](img/20e5ba47bb84401ba422c1b630291411_319.png)

![](img/20e5ba47bb84401ba422c1b630291411_321.png)

![](img/20e5ba47bb84401ba422c1b630291411_323.png)

![](img/20e5ba47bb84401ba422c1b630291411_325.png)

![](img/20e5ba47bb84401ba422c1b630291411_327.png)

![](img/20e5ba47bb84401ba422c1b630291411_329.png)

![](img/20e5ba47bb84401ba422c1b630291411_331.png)

**2. 配置Ansible使用密码文件**
编辑Ansible配置文件`/etc/ansible/ansible.cfg`，在`[defaults]`部分添加以下行：
```ini
vault_password_file = /home/greg/ansible/secret.txt
```

![](img/20e5ba47bb84401ba422c1b630291411_333.png)

![](img/20e5ba47bb84401ba422c1b630291411_335.png)

![](img/20e5ba47bb84401ba422c1b630291411_337.png)

![](img/20e5ba47bb84401ba422c1b630291411_339.png)

![](img/20e5ba47bb84401ba422c1b630291411_341.png)

![](img/20e5ba47bb84401ba422c1b630291411_343.png)

**3. 创建变量文件并加密**
创建一个包含敏感变量的YAML文件，然后使用`ansible-vault`命令加密它。
```bash
# 创建变量文件
cat > /home/greg/ansible/locker.yml << EOF
---
pw_developer: redhat
pw_manager: redhat
EOF

# 加密文件
ansible-vault encrypt /home/greg/ansible/locker.yml
```
加密后，文件内容将变为密文。在Playbook中，可以通过`vars_files`加载此文件，并像使用普通变量一样使用`pw_developer`和`pw_manager`，Ansible会自动解密。

![](img/20e5ba47bb84401ba422c1b630291411_345.png)

---

![](img/20e5ba47bb84401ba422c1b630291411_347.png)

![](img/20e5ba47bb84401ba422c1b630291411_349.png)

![](img/20e5ba47bb84401ba422c1b630291411_351.png)

## 题目十四：使用密码库创建用户 👥

![](img/20e5ba47bb84401ba422c1b630291411_353.png)

![](img/20e5ba47bb84401ba422c1b630291411_355.png)

![](img/20e5ba47bb84401ba422c1b630291411_357.png)

![](img/20e5ba47bb84401ba422c1b630291411_359.png)

![](img/20e5ba47bb84401ba422c1b630291411_361.png)

这道题是拔高题，综合运用了变量加载、循环、条件判断、用户管理以及从Vault加密文件中读取密码等高级功能。

![](img/20e5ba47bb84401ba422c1b630291411_363.png)

![](img/20e5ba47bb84401ba422c1b630291411_365.png)

![](img/20e5ba47bb84401ba422c1b630291411_367.png)

**题目要求分析：**
1.  根据一个YAML文件（`users_list.yml`）中定义的用户列表（包含用户名和职位）。
2.  根据另一个Vault加密文件（`locker.yml`）中存储的密码。
3.  创建一个Playbook，在特定的主机组上，为特定职位的用户创建系统账户，并分配对应的密码和附加组。

![](img/20e5ba47bb84401ba422c1b630291411_369.png)

![](img/20e5ba47bb84401ba422c1b630291411_371.png)

![](img/20e5ba47bb84401ba422c1b630291411_373.png)

**简化版核心思路：**
1.  在Playbook中加载两个变量文件：`users_list.yml` 和 `locker.yml`。
2.  使用循环（`loop`）遍历用户列表。
3.  使用条件判断（`when`）组合判断：**当前主机的组**和**当前用户的职位**，两者都满足特定条件时才执行创建用户的任务。
4.  创建用户时，从`locker.yml`中读取对应职位的密码，并使用`password`参数配合哈希过滤器进行设置。

![](img/20e5ba47bb84401ba422c1b630291411_375.png)

![](img/20e5ba47bb84401ba422c1b630291411_377.png)

**示例代码片段（概念示意）：**
```yaml
---
- name: Create users based on role and host group
  hosts: dv,test,prod  # 在多个组上运行
  vars_files:
    - /home/greg/ansible/users_list.yml
    - /home/greg/ansible/locker.yml
  tasks:
    - name: Create developer users on dv/test hosts
      user:
        name: "{{ item.name }}"
        password: "{{ pw_developer | password_hash('sha512') }}"
        groups: "webdev"
        append: yes
      loop: "{{ users }}"
      when: >
        item.job == "developer" and
        (inventory_hostname in groups['dv'] or inventory_hostname in groups['test'])

    - name: Create manager users on prod hosts
      user:
        name: "{{ item.name }}"
        password: "{{ pw_manager | password_hash('sha512') }}"
        groups: "webdev"
        append: yes
      loop: "{{ users }}"
      when: >
        item.job == "manager" and
        inventory_hostname in groups['prod']
```
**关键点说明：**
*   `vars_files`：用于加载外部变量文件。
*   复杂的`when`条件：使用`and`和`or`逻辑运算符组合多个条件。
*   `password_hash`过滤器：用于生成Linux系统可识别的密码哈希值。

![](img/20e5ba47bb84401ba422c1b630291411_379.png)

![](img/20e5ba47bb84401ba422c1b630291411_381.png)

![](img/20e5ba47bb84401ba422c1b630291411_383.png)

![](img/20e5ba47bb84401ba422c1b630291411_385.png)

![](img/20e5ba47bb84401ba422c1b630291411_387.png)

![](img/20e5ba47bb84401ba422c1b630291411_389.png)

![](img/20e5ba47bb84401ba422c1b630291411_391.png)

---

![](img/20e5ba47bb84401ba422c1b630291411_393.png)

![](img/20e5ba47bb84401ba422c1b630291411_395.png)

![](img/20e5ba47bb84401ba422c1b630291411_397.png)

![](img/20e5ba47bb84401ba422c1b630291411_399.png)

![](img/20e5ba47bb84401ba422c1b630291411_401.png)

![](img/20e5ba47bb84401ba422c1b630291411_403.png)

## 题目十五：更新Ansible Vault密钥 🔑

![](img/20e5ba47bb84401ba422c1b630291411_405.png)

![](img/20e5ba47bb84401ba422c1b630291411_407.png)

这是一道送分题，考察`ansible-vault`命令的基本使用。

![](img/20e5ba47bb84401ba422c1b630291411_409.png)

![](img/20e5ba47bb84401ba422c1b630291411_411.png)

![](img/20e5ba47bb84401ba422c1b630291411_413.png)

![](img/20e5ba47bb84401ba422c1b630291411_415.png)

**题目要求分析：**
1.  从远程下载一个已加密的Vault文件。
2.  使用`ansible-vault rekey`命令更改该文件的加密密码。

![](img/20e5ba47bb84401ba422c1b630291411_417.png)

![](img/20e5ba47bb84401ba422c1b630291411_419.png)

![](img/20e5ba47bb84401ba422c1b630291411_421.png)

![](img/20e5ba47bb84401ba422c1b630291411_423.png)

![](img/20e5ba47bb84401ba422c1b630291411_425.png)

**核心步骤与操作：**
```bash
# 1. 下载加密文件
wget -O /home/greg/ansible/vault.txt http://172.25.250.254/vault.txt

![](img/20e5ba47bb84401ba422c1b630291411_427.png)

![](img/20e5ba47bb84401ba422c1b630291411_429.png)

# 2. 重新设置密钥（会提示输入原密码和新密码）
ansible-vault rekey --ask-vault-pass /home/greg/ansible/vault.txt
# 原密码：redhat
# 新密码：when you wish upon a star
```

---

## 隐藏题目与考试说明 ℹ️

![](img/20e5ba47bb84401ba422c1b630291411_431.png)

![](img/20e5ba47bb84401ba422c1b630291411_433.png)

![](img/20e5ba47bb84401ba422c1b630291411_435.png)

根据学员反馈，考试中可能会出现以下题目，用于替换现有题库中的部分题目：

1.  **配置计划任务**：使用`cron`模块，为特定用户创建每两分钟执行一次的计划任务。比文件操作题更简单。
2.  **创建分区（替换LVM题目）**：使用`parted`或`filesystem`模块直接在磁盘上创建分区、格式化和挂载，逻辑比LVM更直接。
3.  **配置SELinux角色（替换时间同步角色）**：使用RHEL系统自带的SELinux管理角色，将受管主机的SELinux模式设置为`enforcing`。通常只需安装角色并运行即可，无需额外配置。

![](img/20e5ba47bb84401ba422c1b630291411_437.png)

![](img/20e5ba47bb84401ba422c1b630291411_439.png)

**考试注意事项：**
*   RHCE考试满分300分，210分通过。
*   考试环境为中文，但翻译可能生硬，需仔细审题。
*   考试时间充裕（4小时），通常足以完成所有题目。
*   考前务必充分练习，尤其是复杂题目，建议动手操作3-5遍以加深理解。
*   当前由于疫情原因，约考时间可能延迟，请关注后续通知。

![](img/20e5ba47bb84401ba422c1b630291411_441.png)

![](img/20e5ba47bb84401ba422c1b630291411_443.png)

![](img/20e5ba47bb84401ba422c1b630291411_445.png)

![](img/20e5ba47bb84401ba422c1b630291411_447.png)

![](img/20e5ba47bb84401ba422c1b630291411_449.png)

![](img/20e5ba47bb84401ba422c1b630291411_451.png)

![](img/20e5ba47bb84401ba422c1b630291411_453.png)

![](img/20e5ba47bb84401ba422c1b630291411_455.png)

---

![](img/20e5ba47bb84401ba422c1b630291411_457.png)

![](img/20e5ba47bb84401ba422c1b630291411_459.png)

![](img/20e5ba47bb84401ba422c1b630291411_461.png)

![](img/20e5ba47bb84401ba422c1b630291411_463.png)

## 课程总结 🎯

本节课中我们一起学习了Ansible的多个高级主题和实战应用。我们从动态生成文件的模板技术开始，学习了变量替换和循环控制。接着，我们掌握了如何使用条件判断`when`来编写更具逻辑性的Playbook。通过创建Web目录的综合实验，我们复习了服务、防火墙、文件权限、SELinux等系统管理知识在Ansible中的实现。在难度较高的硬件报告题目中，我们深入探讨了复杂数据结构处理、循环与文件内容修改的结合。最后，我们学习了如何使用Ansible Vault来加密敏感数据，并在此基础上完成了用户管理的综合题目。

![](img/20e5ba47bb84401ba422c1b630291411_465.png)

![](img/20e5ba47bb84401ba422c1b630291411_466.png)

这些内容涵盖了RHCE考试中Ansible部分的核心与难点，理解并掌握它们对于通过考试至关重要。请大家课后结合实验环境反复练习，巩固所学知识。