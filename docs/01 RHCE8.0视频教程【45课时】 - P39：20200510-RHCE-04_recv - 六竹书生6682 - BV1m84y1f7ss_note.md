# Ansible教程：04：Playbook基础与角色管理 📚

![](img/36d78ca2b16c158b79326c1cb3b99e84_1.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_3.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_5.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_7.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_9.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_11.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_13.png)

在本节课中，我们将学习Ansible的角色管理、交互式控制台的使用、YAML语法基础以及如何编写和运行一个基础的Playbook。我们将从Ansible Galaxy的角色管理开始，逐步深入到Playbook的核心元素和编写技巧。

![](img/36d78ca2b16c158b79326c1cb3b99e84_15.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_17.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_19.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_21.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_23.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_25.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_27.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_29.png)

---

![](img/36d78ca2b16c158b79326c1cb3b99e84_31.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_33.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_35.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_37.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_39.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_41.png)

## Ansible Galaxy角色管理 🔭

![](img/36d78ca2b16c158b79326c1cb3b99e84_43.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_45.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_47.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_49.png)

上一节我们介绍了Ansible的基本模块和命令。本节中，我们来看看如何利用Ansible Galaxy来管理和使用社区分享的预定义角色。

![](img/36d78ca2b16c158b79326c1cb3b99e84_51.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_53.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_55.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_57.png)

Ansible Galaxy是一个在线仓库，包含了大量由社区贡献的、针对常见任务的解决方案（即“角色”）。我们可以下载这些角色并直接使用，或者基于它们进行修改。

![](img/36d78ca2b16c158b79326c1cb3b99e84_59.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_61.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_63.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_65.png)

以下是管理角色的常用命令：

![](img/36d78ca2b16c158b79326c1cb3b99e84_67.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_69.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_71.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_73.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_75.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_77.png)

*   **列出所有已安装的角色**：`ansible-galaxy list`
*   **从Galaxy安装角色**：`ansible-galaxy install <作者名>.<角色名>`
*   **查看特定角色信息**：`ansible-galaxy list <角色名>`
*   **基于现有角色创建新角色**：`cp -r /root/.ansible/roles/<原角色名> /root/.ansible/roles/<新角色名>`
*   **删除角色**：
    *   方式一：直接删除角色目录 `rm -rf /root/.ansible/roles/<角色名>`
    *   方式二：使用命令 `ansible-galaxy remove <角色名>`

![](img/36d78ca2b16c158b79326c1cb3b99e84_79.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_81.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_83.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_85.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_87.png)

安装的角色通常位于 `/root/.ansible/roles/` 目录下，其内部结构（如 `tasks/`, `handlers/`, `templates/` 等）遵循标准的Ansible角色目录布局，方便我们查看和修改。

![](img/36d78ca2b16c158b79326c1cb3b99e84_89.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_91.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_93.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_95.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_97.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_99.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_101.png)

---

![](img/36d78ca2b16c158b79326c1cb3b99e84_103.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_105.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_107.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_109.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_111.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_113.png)

## 交互式控制台：ansible-console 🖥️

![](img/36d78ca2b16c158b79326c1cb3b99e84_115.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_117.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_119.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_121.png)

之前我们通过命令行直接执行ansible命令。本节中，我们来看看一个交互式的执行工具——`ansible-console`。

![](img/36d78ca2b16c158b79326c1cb3b99e84_123.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_125.png)

`ansible-console` 提供了一个交互式环境，允许我们以更便捷的方式执行ad-hoc命令，并支持Tab键补全。

![](img/36d78ca2b16c158b79326c1cb3b99e84_127.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_129.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_131.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_133.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_135.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_137.png)

启动后，提示符格式为：`执行用户@目标主机[并发数]>`
*   **执行用户**：表示在远程主机上执行命令的用户。
*   **目标主机**：表示从主机清单中选定的主机或组。
*   **并发数**：表示同时执行任务的主机数量。

![](img/36d78ca2b16c158b79326c1cb3b99e84_139.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_141.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_143.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_145.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_147.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_149.png)

在控制台内，我们可以执行各种模块命令，而无需重复指定主机和用户。

![](img/36d78ca2b16c158b79326c1cb3b99e84_151.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_153.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_155.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_157.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_159.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_161.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_163.png)

以下是控制台内的常用操作：

![](img/36d78ca2b16c158b79326c1cb3b99e84_165.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_167.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_169.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_171.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_173.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_175.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_177.png)

*   **切换目标主机**：输入主机或组名，如 `web1`
*   **设置并发数**：`forks 10`
*   **列出当前目标主机**：`list`
*   **执行命令**：直接使用模块，如 `command yum repolist` 或 `shell date`
*   **获取帮助**：输入 `?`
*   **退出控制台**：`exit`

![](img/36d78ca2b16c158b79326c1cb3b99e84_178.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_180.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_182.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_184.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_186.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_188.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_190.png)

---

![](img/36d78ca2b16c158b79326c1cb3b99e84_192.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_194.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_196.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_198.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_200.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_202.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_204.png)

## YAML语法与Playbook基础 📝

![](img/36d78ca2b16c158b79326c1cb3b99e84_206.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_208.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_210.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_212.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_214.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_216.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_218.png)

了解了交互式操作后，我们进入Ansible自动化的核心——Playbook。Playbook使用YAML格式编写，因此首先需要掌握基础的YAML语法。

![](img/36d78ca2b16c158b79326c1cb3b99e84_220.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_222.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_224.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_226.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_228.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_230.png)

YAML是一种可读性高、易于实现的数据序列化格式。在Ansible中，它用于描述配置和流程。

![](img/36d78ca2b16c158b79326c1cb3b99e84_232.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_234.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_236.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_238.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_240.png)

**YAML基本语法规则：**
1.  **结构**：使用空格缩进来表示层级关系。
2.  **序列（列表）**：使用短横线 `-` 加空格开头表示列表项。
3.  **映射（字典）**：使用冒号 `:` 加空格表示键值对。
4.  **大小写敏感**：对大小写严格区分。
5.  **文件扩展名**：建议使用 `.yml` 或 `.yaml`。

![](img/36d78ca2b16c158b79326c1cb3b99e84_242.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_244.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_246.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_248.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_250.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_252.png)

一个简单的Playbook结构示例如下：
```yaml
---
- hosts: web1          # 指定目标主机
  remote_user: root    # 指定远程执行用户
  tasks:               # 任务列表开始
    - name: Print hello # 第一个任务名称
      command: /usr/bin/echo hello # 使用的模块及参数
```
*   `---`：YAML文件可选的起始标记。
*   `hosts`：定义在哪些主机上执行任务，是必需项。
*   `tasks`：定义要执行的任务列表，是必需项。
*   每个 `task` 通常包含一个 `name`（描述）和一个模块调用。

![](img/36d78ca2b16c158b79326c1cb3b99e84_254.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_256.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_258.png)

---

![](img/36d78ca2b16c158b79326c1cb3b99e84_260.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_262.png)

## 编写与运行你的第一个Playbook 🚀

![](img/36d78ca2b16c158b79326c1cb3b99e84_264.png)

现在，让我们动手编写一个简单的Playbook，它将在远程主机上执行三个任务：创建文件、创建用户、删除另一个用户。

![](img/36d78ca2b16c158b79326c1cb3b99e84_266.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_268.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_270.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_272.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_274.png)

**Playbook示例 (`practice.yml`):**
```yaml
---
- hosts: web1
  remote_user: root
  tasks:
    - name: Create file
      file:
        name: /tmp/file0510
        state: touch
    - name: Create user
      user:
        name: testuser0510
        state: present
    - name: Remove user
      user:
        name: user0510
        state: absent
```

![](img/36d78ca2b16c158b79326c1cb3b99e84_276.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_278.png)

**运行Playbook：**
*   **语法检查（空运行）**：`ansible-playbook practice.yml --check`
*   **实际执行**：`ansible-playbook practice.yml`

![](img/36d78ca2b16c158b79326c1cb3b99e84_280.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_282.png)

Playbook中的任务会**从上到下顺序执行**。如果某个任务执行失败（返回非零状态码），默认情况下，后续任务将不再执行。

![](img/36d78ca2b16c158b79326c1cb3b99e84_284.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_286.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_288.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_290.png)

---

![](img/36d78ca2b16c158b79326c1cb3b99e84_292.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_294.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_296.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_298.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_300.png)

## 任务错误处理与触发器（Handlers）⚙️

![](img/36d78ca2b16c158b79326c1cb3b99e84_302.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_304.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_306.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_308.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_310.png)

在实际应用中，我们需要更灵活地控制任务流。本节中，我们来看看如何处理任务错误，以及如何使用触发器在文件变更后自动执行操作。

![](img/36d78ca2b16c158b79326c1cb3b99e84_312.png)

### 任务错误处理
当某个任务可能失败，但我们希望后续任务继续执行时，有两种处理方法：

![](img/36d78ca2b16c158b79326c1cb3b99e84_314.png)

1.  **强制返回成功状态**：使用 `shell` 或 `command` 模块时，添加 `|| /bin/true`。
    ```yaml
    - name: Install package (may fail)
      shell: yum install -y non-existent-pkg || /bin/true
    ```
2.  **忽略错误**：在任务级别设置 `ignore_errors: true`。
    ```yaml
    - name: Install package (may fail)
      yum:
        name: non-existent-pkg
        state: present
      ignore_errors: true
    ```

![](img/36d78ca2b16c158b79326c1cb3b99e84_316.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_318.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_320.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_322.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_324.png)

### 使用Handlers触发变更
Ansible具有**幂等性**，即如果系统已处于目标状态，则不会执行操作。这有时会带来问题，例如：配置文件已更新，但服务未重启。

![](img/36d78ca2b16c158b79326c1cb3b99e84_326.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_328.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_330.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_331.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_333.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_335.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_337.png)

**Handlers** 是一种特殊的任务，只在被其他任务“通知”（notify）时才会执行。通常用于在配置文件变更后重启服务。

![](img/36d78ca2b16c158b79326c1cb3b99e84_339.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_341.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_343.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_344.png)

**案例：配置HTTP服务**
1.  **编写Playbook (`httpd.yml`)**：
    ```yaml
    ---
    - hosts: web1
      remote_user: root
      tasks:
        - name: Install HTTPd
          yum:
            name: httpd
            state: present
        - name: Copy config file
          copy:
            src: /root/.ansible/httpd.conf
            dest: /etc/httpd/conf/httpd.conf
          notify: restart httpd # 如果copy任务改变了文件，则通知handler
      handlers:
        - name: restart httpd
          service:
            name: httpd
            state: restarted
    ```
2.  **流程**：
    *   首次运行Playbook，安装软件、拷贝配置文件（端口设为80）、启动服务。
    *   修改本地的 `httpd.conf` 文件，将端口改为8080。
    *   再次运行Playbook。`copy` 模块检测到文件内容变化，执行拷贝并**触发** `notify`。
    *   被通知的 `handler` (`restart httpd`) 执行，重启httpd服务，使新配置（8080端口）生效。

![](img/36d78ca2b16c158b79326c1cb3b99e84_346.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_348.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_350.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_352.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_354.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_356.png)

**Handlers要点：**
*   在 `handlers` 部分定义。
*   通过 `notify: <handler名称>` 在任务中调用。
*   多个任务可以通知同一个handler，但该handler只会在所有任务执行完后运行一次。
*   只有当通知它的任务状态为 **“changed”** 时，handler才会被触发。

![](img/36d78ca2b16c158b79326c1cb3b99e84_358.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_360.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_362.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_364.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_365.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_367.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_369.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_371.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_373.png)

---

![](img/36d78ca2b16c158b79326c1cb3b99e84_375.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_376.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_378.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_380.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_382.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_384.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_386.png)

## 总结 ✨

![](img/36d78ca2b16c158b79326c1cb3b99e84_388.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_390.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_392.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_394.png)

本节课中我们一起学习了：
1.  **Ansible Galaxy**：如何搜索、安装、管理和删除社区贡献的角色。
2.  **ansible-console**：交互式控制台的使用方法，便于快速执行ad-hoc命令。
3.  **YAML语法基础**：理解了Playbook编写的格式规范，包括缩进、列表和字典的表示方法。
4.  **Playbook编写与运行**：创建了第一个包含多个任务的Playbook，并学会了使用 `--check` 进行空运行测试。
5.  **任务流程控制**：掌握了通过 `ignore_errors` 和强制成功状态来处理任务失败，确保流程继续。
6.  **Handlers触发器**：学习了如何定义和使用handlers，在配置文件等发生变更后自动执行重启服务等操作，这是实现Ansible幂等性和状态管理的关键机制。

![](img/36d78ca2b16c158b79326c1cb3b99e84_396.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_398.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_400.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_402.png)

![](img/36d78ca2b16c158b79326c1cb3b99e84_404.png)

通过本节课，你已经掌握了编写基础Ansible Playbook的核心技能。下一节课，我们将深入探讨Playbook中的变量管理、循环和条件判断等高级功能。