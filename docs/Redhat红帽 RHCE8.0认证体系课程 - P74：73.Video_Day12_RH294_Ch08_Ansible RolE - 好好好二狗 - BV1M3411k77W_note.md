# Redhat红帽 RHCE8.0认证体系课程：P74：Ansible角色（Role）详解 🧩

![](img/87de34e158fbdeb6530257dbc14f6d20_1.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_3.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_5.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_7.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_9.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_11.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_12.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_14.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_16.png)

在本节课中，我们将要学习Ansible中一个非常重要的概念——角色（Role）。角色可以帮助我们将复杂的任务、变量、文件等打包成可重用的模块，从而极大地简化Playbook的编写和维护。我们将从角色的结构、创建方法、使用方式以及如何利用社区角色等多个方面进行详细讲解。

![](img/87de34e158fbdeb6530257dbc14f6d20_18.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_19.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_21.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_23.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_24.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_26.png)

---

![](img/87de34e158fbdeb6530257dbc14f6d20_28.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_30.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_32.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_34.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_36.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_38.png)

## 角色的概念与结构

![](img/87de34e158fbdeb6530257dbc14f6d20_40.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_41.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_42.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_44.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_46.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_47.png)

上一节我们介绍了Ansible任务控制，本节中我们来看看如何通过角色来组织和复用这些任务。

![](img/87de34e158fbdeb6530257dbc14f6d20_49.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_50.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_52.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_54.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_56.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_57.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_59.png)

角色可以简单理解为“打包工具箱”。它把多个任务文件、变量、处理程序等，通过一个既定的目录结构进行打包封装。在使用时，我们只需调用角色名称即可，无需在Playbook中重复编写所有细节。

![](img/87de34e158fbdeb6530257dbc14f6d20_61.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_62.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_64.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_66.png)

一个标准的Ansible角色包含以下目录结构，这些目录在初始化角色时会自动创建：

![](img/87de34e158fbdeb6530257dbc14f6d20_68.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_70.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_72.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_74.png)

```
角色名称/
├── defaults/
│   └── main.yml      # 角色变量的默认值（优先级最低）
├── files/            # 角色任务中需要复制的静态文件存放处
├── handlers/
│   └── main.yml      # 角色的处理程序（handlers）
├── meta/
│   └── main.yml      # 角色的元数据，如作者、许可证、依赖关系
├── tasks/
│   └── main.yml      # 角色的主要任务列表
├── templates/        # Jinja2模板文件存放处
├── tests/            # 用于测试角色的清单和Playbook
├── vars/
│   └── main.yml      # 角色的变量定义（优先级较高）
└── README.md         # 角色的说明文档
```

![](img/87de34e158fbdeb6530257dbc14f6d20_76.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_78.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_79.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_80.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_81.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_83.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_85.png)

以下是各个目录的核心用途：
*   **defaults/main.yml**：定义变量的默认值。这些值可以被Playbook或其他地方定义的变量轻松覆盖。
*   **vars/main.yml**：定义角色的变量，优先级高于`defaults`。
*   **tasks/main.yml**：这是角色的核心，所有任务都在此文件中定义或通过`import_tasks`引入。
*   **handlers/main.yml**：存放`notify`触发的处理程序。
*   **files/**：存放需要通过`copy`模块同步到受管主机的静态文件。
*   **templates/**：存放Jinja2模板文件，供`template`模块使用。
*   **meta/main.yml**：包含角色的描述、作者、平台要求、依赖的其他角色等信息。
*   **tests/**：存放用于测试该角色的清单和Playbook示例。

![](img/87de34e158fbdeb6530257dbc14f6d20_87.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_88.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_90.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_92.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_94.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_96.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_98.png)

这种结构化的方式使得Playbook变得非常简洁，我们只需要引用角色，而无需关心其内部复杂的文件路径和任务编排。

![](img/87de34e158fbdeb6530257dbc14f6d20_100.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_102.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_104.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_105.png)

---

![](img/87de34e158fbdeb6530257dbc14f6d20_107.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_109.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_110.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_112.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_113.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_114.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_116.png)

## 创建与使用自定义角色

![](img/87de34e158fbdeb6530257dbc14f6d20_118.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_120.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_122.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_123.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_125.png)

了解了角色的结构后，本节中我们来看看如何创建并使用一个自定义角色。

![](img/87de34e158fbdeb6530257dbc14f6d20_127.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_129.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_131.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_133.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_134.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_136.png)

### 1. 设置角色路径
首先，需要告诉Ansible去哪里寻找角色。这通过设置`roles_path`来实现。通常我们在Ansible配置文件`ansible.cfg`中定义，或在环境变量中设置。

![](img/87de34e158fbdeb6530257dbc14f6d20_138.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_140.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_142.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_144.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_146.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_148.png)

例如，在`ansible.cfg`中添加：
```ini
[defaults]
roles_path = /home/student/ansible/roles
```
你需要确保这个目录存在（`mkdir -p /home/student/ansible/roles`）。

![](img/87de34e158fbdeb6530257dbc14f6d20_150.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_151.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_153.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_155.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_157.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_159.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_160.png)

### 2. 初始化角色框架
进入角色目录，使用`ansible-galaxy`命令可以快速生成一个角色框架。
```bash
cd /home/student/ansible/roles
ansible-galaxy init my_httpd_role
```
命令执行后，会在当前目录下创建名为`my_httpd_role`的目录，并包含上述所有标准子目录和文件。

![](img/87de34e158fbdeb6530257dbc14f6d20_162.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_164.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_165.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_166.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_168.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_170.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_171.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_172.png)

### 3. 编写角色内容
现在，我们可以像拆分乐高积木一样，将部署一个服务（如Apache HTTPD）的完整过程分解到各个目录中。

![](img/87de34e158fbdeb6530257dbc14f6d20_173.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_174.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_176.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_178.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_180.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_181.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_182.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_184.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_186.png)

**a. 分解任务 (tasks/)**
我们将总任务分解为多个子任务文件，例如：
*   `install.yml` - 安装软件包
*   `configure.yml` - 复制配置模板
*   `firewall.yml` - 配置防火墙
*   `service.yml` - 启动并启用服务

![](img/87de34e158fbdeb6530257dbc14f6d20_188.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_190.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_192.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_194.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_196.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_198.png)

每个子任务文件内容示例如下 (`tasks/install.yml`)：
```yaml
- name: Install HTTPD package
  yum:
    name: "{{ package_name }}"
    state: present
```

![](img/87de34e158fbdeb6530257dbc14f6d20_200.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_202.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_204.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_206.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_207.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_209.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_210.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_211.png)

**b. 编排主任务 (tasks/main.yml)**
在`tasks/main.yml`中，通过`import_tasks`按顺序引入所有子任务。
```yaml
- import_tasks: install.yml
- import_tasks: configure.yml
- import_tasks: firewall.yml
- import_tasks: service.yml
```

![](img/87de34e158fbdeb6530257dbc14f6d20_213.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_214.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_216.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_218.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_219.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_220.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_222.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_224.png)

**c. 定义变量 (vars/main.yml 或 defaults/main.yml)**
在`vars/main.yml`中定义角色内部使用的变量。
```yaml
package_name: httpd
http_port: 80
```

![](img/87de34e158fbdeb6530257dbc14f6d20_226.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_227.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_229.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_231.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_233.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_235.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_237.png)

**d. 添加处理程序 (handlers/main.yml)**
定义重启服务的handler。
```yaml
- name: restart httpd
  service:
    name: httpd
    state: restarted
```

![](img/87de34e158fbdeb6530257dbc14f6d20_239.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_241.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_243.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_244.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_245.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_247.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_249.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_250.png)

**e. 准备模板和文件**
将Jinja2配置文件模板放入`templates/`目录，将静态文件（如首页HTML）放入`files/`目录。

![](img/87de34e158fbdeb6530257dbc14f6d20_252.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_254.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_256.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_258.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_260.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_262.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_264.png)

### 4. 在Playbook中使用角色
编写一个极其简洁的Playbook来调用我们创建的角色。
```yaml
- name: Deploy HTTPD using role
  hosts: web_servers
  roles:
    - my_httpd_role
```
运行此Playbook，Ansible会自动到`roles_path`下寻找`my_httpd_role`角色，并执行其中定义的所有任务。

![](img/87de34e158fbdeb6530257dbc14f6d20_265.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_267.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_269.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_271.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_273.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_275.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_276.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_278.png)

---

![](img/87de34e158fbdeb6530257dbc14f6d20_279.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_281.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_282.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_284.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_285.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_287.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_288.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_289.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_291.png)

## 使用Ansible Galaxy社区角色

![](img/87de34e158fbdeb6530257dbc14f6d20_293.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_295.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_297.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_299.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_301.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_303.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_305.png)

除了自定义角色，我们还可以直接使用由社区维护的现成角色，这能极大提升工作效率。

![](img/87de34e158fbdeb6530257dbc14f6d20_307.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_309.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_311.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_312.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_314.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_316.png)

Ansible Galaxy是一个公共的角色共享平台（galaxy.ansible.com）。我们可以使用`ansible-galaxy`命令行工具来搜索、安装和管理这些角色。

![](img/87de34e158fbdeb6530257dbc14f6d20_318.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_319.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_321.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_322.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_324.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_325.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_327.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_329.png)

以下是常用命令：
*   **搜索角色**：`ansible-galaxy search ‘redis’ --platforms EL`
*   **查看角色信息**：`ansible-galaxy info geerlingguy.redis`
*   **安装角色到本地**：`ansible-galaxy install geerlingguy.redis -p /path/to/roles`
*   **列出已安装角色**：`ansible-galaxy list`
*   **删除角色**：`ansible-galaxy remove geerlingguy.redis`

![](img/87de34e158fbdeb6530257dbc14f6d20_331.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_333.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_335.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_337.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_338.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_340.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_341.png)

安装后，就可以像使用自定义角色一样，在Playbook的`roles`关键字下引用它们。

![](img/87de34e158fbdeb6530257dbc14f6d20_343.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_344.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_346.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_348.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_350.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_351.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_353.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_355.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_356.png)

---

![](img/87de34e158fbdeb6530257dbc14f6d20_358.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_360.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_362.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_364.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_366.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_368.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_370.png)

## 系统角色与角色变量优先级

![](img/87de34e158fbdeb6530257dbc14f6d20_371.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_373.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_375.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_377.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_379.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_381.png)

### 系统角色
从RHEL 7.4开始，系统提供了一些预包装的“系统角色”，用于执行常见的系统管理任务，如时间同步、内核参数调优、SELinux管理等。这些角色以软件包形式提供（如`rhel-system-roles`），安装后位于`/usr/share/ansible/roles/`目录下，可以直接调用。

![](img/87de34e158fbdeb6530257dbc14f6d20_383.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_384.png)

### 变量优先级
在角色中使用变量时，了解其优先级非常重要，这决定了最终生效的值。优先级从低到高大致如下：
1.  `defaults/main.yml`中定义的变量（最低，最易被覆盖）。
2.  清单（Inventory）中定义的变量。
3.  Playbook中`vars`关键字定义的变量。
4.  `vars/main.yml`中定义的变量（较高）。
5.  在Playbook中通过`roles`关键字直接传入的变量（最高）。

![](img/87de34e158fbdeb6530257dbc14f6d20_386.png)

**注意**：Playbook中`vars`定义的变量与角色内部`vars/main.yml`定义的变量是独立的命名空间，通常不会互相覆盖，而是用于不同的目的。

![](img/87de34e158fbdeb6530257dbc14f6d20_388.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_390.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_391.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_393.png)

![](img/87de34e158fbdeb6530257dbc14f6d20_395.png)

---

## 总结

本节课中我们一起学习了Ansible角色的核心知识。我们了解到角色是一种强大的代码组织和复用机制，它通过标准化的目录结构，将任务、变量、文件、模板等分门别类地管理。我们掌握了如何从零开始创建自定义角色，如何利用`ansible-galaxy`工具初始化框架和引用社区共享角色，以及角色中变量的优先级规则。

通过使用角色，我们可以将复杂的自动化逻辑模块化、标准化，使得Playbook更加简洁清晰，也极大地提升了代码的可维护性和团队协作效率。这是迈向Ansible高级应用的关键一步。