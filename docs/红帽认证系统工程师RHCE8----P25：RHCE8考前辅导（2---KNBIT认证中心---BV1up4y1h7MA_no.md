# RHCE8考前辅导：P25：RHCE8考前辅导（2）📚

![](img/18e09d00e6f80c6d378885dce62a3399_0.png)

![](img/18e09d00e6f80c6d378885dce62a3399_2.png)

在本节课中，我们将继续学习RHCE8考试中Ansible部分的剩余题目。我们将通过具体的Playbook编写任务，深入理解如何根据不同的主机组、条件判断和变量来管理系统配置。课程内容涵盖文件修改、目录创建、服务部署、硬件报告生成以及用户管理等核心技能。

---

![](img/18e09d00e6f80c6d378885dce62a3399_4.png)

![](img/18e09d00e6f80c6d378885dce62a3399_6.png)

![](img/18e09d00e6f80c6d378885dce62a3399_8.png)

## 第十题：修改 `/etc/issue` 文件内容

上一节我们介绍了条件判断在任务中的应用，本节中我们来看看如何根据不同的主机组来修改系统文件。

创建一个名为 `issue.yml` 的Playbook，其目的是修改所有受管主机上 `/etc/issue` 文件的内容。具体要求如下：
*   在 `dev` 主机组上，文件内容应为单行的 `DEVELOPMENT`。
*   在 `test` 主机组上，文件内容应为单行的 `TEST`。

**核心思路**：使用 `copy` 或 `lineinfile` 模块修改文件内容，并通过 `when` 条件语句针对不同主机组执行不同任务。

以下是实现该功能的Playbook示例：

![](img/18e09d00e6f80c6d378885dce62a3399_10.png)

```yaml
---
- name: Modify /etc/issue
  hosts: all
  tasks:
    - name: Set content for dev group
      copy:
        content: "DEVELOPMENT\n"
        dest: /etc/issue
      when: inventory_hostname in groups['dev']

    - name: Set content for test group
      copy:
        content: "TEST\n"
        dest: /etc/issue
      when: inventory_hostname in groups['test']
```

**代码解释**：
*   `inventory_hostname in groups[‘dev’]`：这是一个条件判断，检查当前执行任务的主机是否属于清单中定义的 `dev` 组。
*   `content: “DEVELOPMENT\n”`：`\n` 代表换行符，确保内容是单行。

![](img/18e09d00e6f80c6d378885dce62a3399_12.png)

**验证方法**：运行Playbook后，登录到 `dev` 组的主机（例如 `servera`），执行 `cat /etc/issue` 命令，应显示 `DEVELOPMENT`。

![](img/18e09d00e6f80c6d378885dce62a3399_14.png)

![](img/18e09d00e6f80c6d378885dce62a3399_16.png)

---

## 第十一题：部署Web内容

![](img/18e09d00e6f80c6d378885dce62a3399_18.png)

![](img/18e09d00e6f80c6d378885dce62a3399_20.png)

完成了基础文件操作后，我们来看一个更综合的任务：部署一个简单的Web服务。

![](img/18e09d00e6f80c6d378885dce62a3399_22.png)

![](img/18e09d00e6f80c6d378885dce62a3399_24.png)

创建一个名为 `web_content.yml` 的Playbook，在 `dev` 主机组上运行，并完成以下任务：
1.  创建一个名为 `webdev` 的组。
2.  创建目录 `/webdev`，其所属组为 `webdev`，权限为 `2775`，SELinux上下文类型为 `httpd_sys_content_t`。
3.  在 `/var/www/html` 目录下创建一个指向 `/webdev` 的符号链接，链接名为 `webdev`。
4.  在 `/webdev` 目录中创建文件 `index.html`，内容为 `Development`。
5.  确保 `httpd` 服务已启动并启用，且防火墙允许 `80/tcp` 端口的流量。

![](img/18e09d00e6f80c6d378885dce62a3399_26.png)

![](img/18e09d00e6f80c6d378885dce62a3399_28.png)

**核心思路**：按顺序使用 `group`、`file`、`copy`、`service` 和 `firewalld` 模块完成系统配置。

![](img/18e09d00e6f80c6d378885dce62a3399_30.png)

![](img/18e09d00e6f80c6d378885dce62a3399_32.png)

以下是实现该功能的Playbook示例：

![](img/18e09d00e6f80c6d378885dce62a3399_34.png)

![](img/18e09d00e6f80c6d378885dce62a3399_36.png)

![](img/18e09d00e6f80c6d378885dce62a3399_38.png)

![](img/18e09d00e6f80c6d378885dce62a3399_40.png)

```yaml
---
- name: Deploy Web Content
  hosts: dev
  tasks:
    - name: Create webdev group
      group:
        name: webdev
        state: present

    - name: Create /webdev directory
      file:
        path: /webdev
        state: directory
        group: webdev
        mode: ‘2775’
        setype: httpd_sys_content_t

    - name: Create symbolic link
      file:
        src: /webdev
        dest: /var/www/html/webdev
        state: link

    - name: Create index.html
      copy:
        content: “Development\n”
        dest: /webdev/index.html

    - name: Start and enable httpd service
      service:
        name: httpd
        state: started
        enabled: yes

    - name: Allow http traffic in firewall
      firewalld:
        service: http
        state: enabled
        permanent: yes
        immediate: yes
```

![](img/18e09d00e6f80c6d378885dce62a3399_42.png)

![](img/18e09d00e6f80c6d378885dce62a3399_44.png)

![](img/18e09d00e6f80c6d378885dce62a3399_45.png)

**验证方法**：在控制节点上使用 `curl http://servera.lab.example.com/webdev/` 命令，应能成功获取到 `Development` 页面内容。

![](img/18e09d00e6f80c6d378885dce62a3399_47.png)

![](img/18e09d00e6f80c6d378885dce62a3399_49.png)

---

![](img/18e09d00e6f80c6d378885dce62a3399_51.png)

## 第十二题：生成硬件报告

![](img/18e09d00e6f80c6d378885dce62a3399_53.png)

接下来，我们处理一个需要动态获取系统信息并生成报告的任务。这涉及到变量、条件判断和文件内容替换。

![](img/18e09d00e6f80c6d378885dce62a3399_55.png)

![](img/18e09d00e6f80c6d378885dce62a3399_57.png)

创建一个名为 `hw_report.yml` 的Playbook，首先从指定URL下载一个硬件报告模板，然后根据每台主机的实际情况填充该报告。

**任务要求**：
1.  从 `http://materials.example.com/hw_report.empty` 下载文件，并保存为 `/root/hw_report.txt`。
2.  修改报告文件，将模板中的占位符替换为实际值：
    *   `HOST=` 后应为主机名。
    *   `MEMORY=` 后应为内存大小。
    *   `BIOS=` 后应为BIOS版本。
    *   `VDA=` 后应为 `/dev/vda` 磁盘的大小。
    *   `VDB=` 后应为 `/dev/vdb` 磁盘的大小，如果该磁盘不存在，则值应为 `NONE`。
3.  每一项占位符替换为一行键值对。

![](img/18e09d00e6f80c6d378885dce62a3399_59.png)

![](img/18e09d00e6f80c6d378885dce62a3399_61.png)

**核心思路**：使用 `get_url` 模块下载，使用 `lineinfile` 模块配合 `ansible_facts` 变量和 `when` 条件判断进行内容替换。

![](img/18e09d00e6f80c6d378885dce62a3399_63.png)

以下是实现该功能的关键任务示例：

```yaml
---
- name: Generate Hardware Report
  hosts: all
  tasks:
    - name: Download report template
      get_url:
        url: http://materials.example.com/hw_report.empty
        dest: /root/hw_report.txt

    - name: Set hostname in report
      lineinfile:
        path: /root/hw_report.txt
        regexp: ‘^HOST=’
        line: “HOST={{ ansible_hostname }}”

    - name: Set memory size in report
      lineinfile:
        path: /root/hw_report.txt
        regexp: ‘^MEMORY=’
        line: “MEMORY={{ ansible_memtotal_mb }}”

    - name: Set VDA size if exists
      lineinfile:
        path: /root/hw_report.txt
        regexp: ‘^VDA=’
        line: “VDA={{ ansible_devices.vda.size }}”
      when: ansible_devices.vda is defined

    - name: Set VDB size or NONE
      lineinfile:
        path: /root/hw_report.txt
        regexp: ‘^VDB=’
        line: “VDB={{ ansible_devices.vdb.size if ansible_devices.vdb is defined else ‘NONE’ }}”
```

![](img/18e09d00e6f80c6d378885dce62a3399_65.png)

![](img/18e09d00e6f80c6d378885dce62a3399_67.png)

**重要提示**：运行Playbook后，务必检查生成的文件内容是否正确。例如，登录 `serverc` 执行 `cat /root/hw_report.txt`，确认各项信息已正确更新。

---

![](img/18e09d00e6f80c6d378885dce62a3399_69.png)

![](img/18e09d00e6f80c6d378885dce62a3399_71.png)

## 第十三题与第十四题：使用Vault加密与创建用户

最后两题是关联题，综合考察了Ansible Vault加密、变量文件引用和循环创建用户等高级功能。

![](img/18e09d00e6f80c6d378885dce62a3399_73.png)

### 第十三题：创建加密的变量文件

首先，创建一个使用 `ansible-vault` 加密的变量文件。

![](img/18e09d00e6f80c6d378885dce62a3399_75.png)

![](img/18e09d00e6f80c6d378885dce62a3399_77.png)

**任务要求**：
1.  创建加密文件 `locker.yml`，密码为 `redhat`。
2.  在文件中定义两个变量：
    *   `pw_developer: iamadev`
    *   `pw_manager: iamamanager`
3.  将密码 `redhat` 单独保存到文件 `/home/student/ansible/secret.txt` 中（用于后续解密）。

![](img/18e09d00e6f80c6d378885dce62a3399_79.png)

![](img/18e09d00e6f80c6d378885dce62a3399_81.png)

![](img/18e09d00e6f80c6d378885dce62a3399_83.png)

**操作步骤**：
在控制节点上执行以下命令：
```bash
cd /home/student/ansible
echo “redhat” > secret.txt
ansible-vault create locker.yml
```
输入密码 `redhat` 后，在编辑器中输入变量内容：
```yaml
pw_developer: iamadev
pw_manager: iamamanager
```

![](img/18e09d00e6f80c6d378885dce62a3399_85.png)

### 第十四题：引用加密变量创建用户

![](img/18e09d00e6f80c6d378885dce62a3399_87.png)

这是最复杂的一题，需要引用外部变量文件（包括加密文件）和循环来创建用户。

![](img/18e09d00e6f80c6d378885dce62a3399_89.png)

![](img/18e09d00e6f80c6d378885dce62a3399_91.png)

**任务要求**：
1.  从指定URL下载用户列表文件 `user_list.yml` 到本地。
2.  为 `dev` 和 `test` 主机组中，工作描述为 `developer` 的用户创建账户。密码使用 `pw_developer` 变量（来自加密文件），并使用 `sha512` 算法加密。用户主组为 `devops`。
3.  为 `prod` 主机组中，工作描述为 `manager` 的用户创建账户。密码使用 `pw_manager` 变量，同样使用 `sha512` 加密。用户主组为 `opsmgr`。

**核心思路**：
1.  使用 `get_url` 下载用户列表。
2.  使用 `vars_files` 导入外部变量文件（包括加密的 `locker.yml`）。
3.  使用 `group` 模块预先创建所需的组。
4.  使用 `user` 模块配合 `loop` 循环和 `when` 条件判断来创建用户，并通过 `vars` 引用加密的密码变量。

![](img/18e09d00e6f80c6d378885dce62a3399_93.png)

以下是Playbook的结构示例：

```yaml
---
- name: Download user list
  hosts: localhost
  tasks:
    - name: Get user list file
      get_url:
        url: http://materials.example.com/user_list.yml
        dest: /home/student/ansible/user_list.yml

- name: Create groups
  hosts: all
  tasks:
    - name: Create devops group
      group:
        name: devops
        state: present
      when: inventory_hostname in groups[‘dev’] or inventory_hostname in groups[‘test’]

    - name: Create opsmgr group
      group:
        name: opsmgr
        state: present
      when: inventory_hostname in groups[‘prod’]

![](img/18e09d00e6f80c6d378885dce62a3399_95.png)

- name: Create users for dev and test
  hosts: dev,test
  vars_files:
    - /home/student/ansible/user_list.yml
    - /home/student/ansible/locker.yml
  tasks:
    - name: Create developer users
      user:
        name: “{{ item.name }}”
        comment: “{{ item.description }}”
        group: devops
        password: “{{ pw_developer | password_hash(‘sha512’) }}”
        state: present
      loop: “{{ users }}”
      when: item.description == “developer”

- name: Create users for prod
  hosts: prod
  vars_files:
    - /home/student/ansible/user_list.yml
    - /home/student/ansible/locker.yml
  tasks:
    - name: Create manager users
      user:
        name: “{{ item.name }}”
        comment: “{{ item.description }}”
        group: opsmgr
        password: “{{ pw_manager | password_hash(‘sha512’) }}”
        state: present
      loop: “{{ users }}”
      when: item.description == “manager”
```

**运行与验证**：由于Playbook引用了Vault加密文件，运行时必须提供密码。可以使用之前保存的密码文件：
```bash
ansible-playbook create_users.yml --vault-password-file secret.txt
```
运行后，可以登录到对应主机，使用 `id <用户名>` 命令检查用户和组信息是否正确创建。

![](img/18e09d00e6f80c6d378885dce62a3399_97.png)

![](img/18e09d00e6f80c6d378885dce62a3399_99.png)

---

![](img/18e09d00e6f80c6d378885dce62a3399_101.png)

![](img/18e09d00e6f80c6d378885dce62a3399_102.png)

## 第十五题：重置用户密码（附加题）

![](img/18e09d00e6f80c6d378885dce62a3399_104.png)

![](img/18e09d00e6f80c6d378885dce62a3399_106.png)

作为附加任务，有时需要重置用户的密码。

![](img/18e09d00e6f80c6d378885dce62a3399_108.png)

![](img/18e09d00e6f80c6d378885dce62a3399_110.png)

**任务要求**：从指定URL下载一个脚本，该脚本能将用户 `bob` 的密码重置为 `redhat`。

**操作步骤**：
1.  下载脚本。
2.  直接运行该脚本即可。

![](img/18e09d00e6f80c6d378885dce62a3399_112.png)

![](img/18e09d00e6f80c6d378885dce62a3399_114.png)

```bash
wget http://materials.example.com/scripts/change_password.sh
bash change_password.sh
```

![](img/18e09d00e6f80c6d378885dce62a3399_116.png)

![](img/18e09d00e6f80c6d378885dce62a3399_118.png)

**提示**：在实际考试中，密码可能不同，但操作逻辑一致。

![](img/18e09d00e6f80c6d378885dce62a3399_120.png)

![](img/18e09d00e6f80c6d378885dce62a3399_122.png)

![](img/18e09d00e6f80c6d378885dce62a3399_123.png)

---

![](img/18e09d00e6f80c6d378885dce62a3399_125.png)

![](img/18e09d00e6f80c6d378885dce62a3399_126.png)

![](img/18e09d00e6f80c6d378885dce62a3399_128.png)

![](img/18e09d00e6f80c6d378885dce62a3399_130.png)

## 课程总结 🎯

![](img/18e09d00e6f80c6d378885dce62a3399_131.png)

![](img/18e09d00e6f80c6d378885dce62a3399_133.png)

本节课中我们一起学习了RHCE8考试Ansible部分的后半部分核心题目。我们实践了如何：
*   使用 `when` 条件语句针对不同主机组执行任务。
*   综合运用多个模块（`file`， `service`， `firewalld`）来部署和配置Web服务。
*   利用 `ansible_facts` 系统变量和 `lineinfile` 模块动态生成系统硬件报告。
*   使用 `ansible-vault` 加密敏感数据（如密码），并在Playbook中安全地引用。
*   通过 `vars_files` 导入变量、结合 `loop` 循环和条件判断来批量创建和管理用户账户。

![](img/18e09d00e6f80c6d378885dce62a3399_135.png)

![](img/18e09d00e6f80c6d378885dce62a3399_136.png)

![](img/18e09d00e6f80c6d378885dce62a3399_137.png)

这些题目涵盖了RHCE认证所需的Ansible高级自动化技能。请务必在实验环境中反复练习，理解每一行代码的作用，并注意避免拼写错误和语法错误，这样才能在考试中从容应对。