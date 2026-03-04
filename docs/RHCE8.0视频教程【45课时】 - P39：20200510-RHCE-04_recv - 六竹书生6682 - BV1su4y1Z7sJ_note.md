# RHCE8.0视频教程：P39：Ansible Playbook基础与角色管理 📚

![](img/9802e8fc870ce5558fd70a002c2e026b_1.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_3.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_5.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_7.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_9.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_11.png)

在本节课中，我们将要学习Ansible中角色的管理、交互式控制台的使用、YAML文件的基础语法以及如何编写一个简单的Playbook。我们将通过具体的命令和示例，帮助初学者理解这些核心概念。

![](img/9802e8fc870ce5558fd70a002c2e026b_13.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_15.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_17.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_19.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_21.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_23.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_25.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_27.png)

---

![](img/9802e8fc870ce5558fd70a002c2e026b_29.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_31.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_33.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_35.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_37.png)

## Ansible Galaxy角色管理 🔍

![](img/9802e8fc870ce5558fd70a002c2e026b_39.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_41.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_43.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_45.png)

上一节我们介绍了Ansible的基本模块和命令，本节中我们来看看如何利用Ansible Galaxy来管理和使用预定义的角色。

![](img/9802e8fc870ce5558fd70a002c2e026b_47.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_49.png)

Ansible Galaxy是一个社区站点，上面提供了许多由他人创建并分享的、针对常见任务的解决方案（即角色）。我们可以下载这些角色直接使用，或在其基础上进行修改。

![](img/9802e8fc870ce5558fd70a002c2e026b_51.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_53.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_55.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_57.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_59.png)

以下是管理角色的常用命令：

![](img/9802e8fc870ce5558fd70a002c2e026b_61.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_63.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_65.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_67.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_69.png)

*   **列出所有已安装的角色**：`ansible-galaxy list`
*   **从Galaxy站点安装角色**：`ansible-galaxy install <作者名>.<角色名>`
*   **查看特定角色信息**：`ansible-galaxy list <角色名>`
*   **查看角色目录结构**：`tree /root/.ansible/roles/<角色名>/`
*   **通过复制目录创建新角色**：`cp -r /root/.ansible/roles/<源角色名> /root/.ansible/roles/<新角色名>`
*   **删除角色（方式一：删除目录）**：`rm -rf /root/.ansible/roles/<角色名>`
*   **删除角色（方式二：使用命令）**：`ansible-galaxy remove <角色名>`

![](img/9802e8fc870ce5558fd70a002c2e026b_71.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_73.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_75.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_77.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_79.png)

---

![](img/9802e8fc870ce5558fd70a002c2e026b_81.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_83.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_85.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_87.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_89.png)

## Ansible Console交互式控制台 🖥️

![](img/9802e8fc870ce5558fd70a002c2e026b_91.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_93.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_95.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_97.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_99.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_101.png)

之前我们执行Ansible命令都是单条执行，Ansible Console提供了一个交互式环境，可以更方便地进行测试和操作。

![](img/9802e8fc870ce5558fd70a002c2e026b_103.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_105.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_107.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_109.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_111.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_113.png)

启动交互式控制台：`ansible-console`

![](img/9802e8fc870ce5558fd70a002c2e026b_115.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_117.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_119.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_121.png)

进入控制台后，提示符格式为：`[执行用户@执行目标 (并发数)]$`
例如：`[root@all (5)]$` 表示以root用户在所有主机上执行，并发数为5。

![](img/9802e8fc870ce5558fd70a002c2e026b_123.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_125.png)

在控制台内，你可以：
*   使用Tab键补全命令。
*   切换执行目标主机组：`web1`
*   设置并发数：`forks 10`
*   列出当前主机：`list`
*   执行模块命令（无需写`-m`和主机参数）：
    *   查看yum源：`yum_repository list`
    *   显示日期：`shell date`
    *   停止服务：`service name=vsftpd state=stopped`
*   退出控制台：`exit`

![](img/9802e8fc870ce5558fd70a002c2e026b_127.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_129.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_131.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_133.png)

---

![](img/9802e8fc870ce5558fd70a002c2e026b_135.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_137.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_139.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_141.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_143.png)

## YAML语法与Playbook基础 📝

![](img/9802e8fc870ce5558fd70a002c2e026b_145.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_147.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_149.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_151.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_153.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_155.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_157.png)

Ansible Playbook使用YAML格式编写，它是一种可读性高、易于实现的数据序列化语言。

![](img/9802e8fc870ce5558fd70a002c2e026b_159.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_161.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_163.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_165.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_167.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_169.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_171.png)

### YAML核心语法规则

![](img/9802e8fc870ce5558fd70a002c2e026b_173.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_175.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_177.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_178.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_180.png)

1.  **结构使用空格缩进展示**，缩进级别表示层级关系。
2.  **列表（序列）** 使用短横线 `-` 加空格开头。
    ```yaml
    - 任务一
    - 任务二
    ```
3.  **字典（映射）** 使用冒号 `:` 加空格表示键值对。
    ```yaml
    name: 安装软件包
    yum:
      name: httpd
    ```
4.  **多个Play或文档** 之间可以使用三个横线 `---` 分隔（非必须）。
5.  **严格区分大小写**。
6.  **注释** 使用井号 `#`。
7.  建议Playbook文件以 `.yml` 或 `.yaml` 结尾。

![](img/9802e8fc870ce5558fd70a002c2e026b_182.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_184.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_186.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_188.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_190.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_192.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_194.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_196.png)

### 编写第一个Playbook

![](img/9802e8fc870ce5558fd70a002c2e026b_198.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_200.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_202.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_204.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_206.png)

一个最简单的Playbook包含以下核心元素：
*   **`hosts`**: 指定在哪些远程主机上执行。
*   **`tasks`**: 定义要执行的任务列表。

![](img/9802e8fc870ce5558fd70a002c2e026b_208.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_210.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_212.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_214.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_216.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_218.png)

创建一个名为 `hello.yml` 的文件：
```yaml
---
- hosts: web1          # 对web1主机组执行
  remote_user: root    # 使用root用户
  tasks:               # 任务列表开始
    - name: Print Hello # 第一个任务：打印Hello
      command: /usr/bin/echo hello
```

![](img/9802e8fc870ce5558fd70a002c2e026b_220.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_222.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_224.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_226.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_228.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_230.png)

**执行与检查Playbook：**
*   **语法检查（空运行）**：`ansible-playbook hello.yml --check`
*   **实际执行**：`ansible-playbook hello.yml`

![](img/9802e8fc870ce5558fd70a002c2e026b_232.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_234.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_236.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_238.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_240.png)

---

![](img/9802e8fc870ce5558fd70a002c2e026b_242.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_244.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_246.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_248.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_250.png)

## Playbook核心元素详解 🧩

![](img/9802e8fc870ce5558fd70a002c2e026b_252.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_254.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_256.png)

### 主机（hosts）定义

![](img/9802e8fc870ce5558fd70a002c2e026b_258.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_260.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_262.png)

`hosts` 字段支持多种模式来指定目标主机：
*   单个组：`web1`
*   单台主机：`192.168.127.201`
*   多个组（并集）：`web1:web2`
*   多个组（交集）：`web1:&web2`
*   排除：`web1:!web2` (在web1中但不在web2中的主机)

### 任务（tasks）定义

![](img/9802e8fc870ce5558fd70a002c2e026b_264.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_266.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_268.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_270.png)

`tasks` 是Playbook的主体，任务按顺序执行。每个任务通常包含一个 `name`（描述）和一个模块调用。

![](img/9802e8fc870ce5558fd70a002c2e026b_272.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_274.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_276.png)

**任务格式有两种：**
1.  **常用格式（模块: 参数）**：
    ```yaml
    - name: 创建文件
      file:
        path: /tmp/file1
        state: touch
    ```
2.  **传统格式（action: 模块 参数）**：
    ```yaml
    - name: 创建文件
      action: file path=/tmp/file1 state=touch
    ```

![](img/9802e8fc870ce5558fd70a002c2e026b_278.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_280.png)

### 处理任务错误

![](img/9802e8fc870ce5558fd70a002c2e026b_282.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_284.png)

默认情况下，一个任务失败会导致后续所有任务停止执行。有两种方法可以处理：

![](img/9802e8fc870ce5558fd70a002c2e026b_286.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_288.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_290.png)

1.  **让命令总是返回成功（状态码0）**：
    ```yaml
    - name: 尝试安装可能不存在的包
      shell: yum install -y notexist || /bin/true
    ```
2.  **忽略错误**：
    ```yaml
    - name: 尝试安装可能不存在的包
      yum:
        name: notexist
      ignore_errors: true
    ```

![](img/9802e8fc870ce5558fd70a002c2e026b_292.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_294.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_296.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_298.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_300.png)

---

![](img/9802e8fc870ce5558fd70a002c2e026b_302.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_304.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_306.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_308.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_310.png)

## Handlers：触发器 ⚡

![](img/9802e8fc870ce5558fd70a002c2e026b_312.png)

Ansible具有**幂等性**，即如果系统已处于目标状态，则不会执行操作。这有时会带来问题，例如配置文件更新后，服务不会自动重启。

![](img/9802e8fc870ce5558fd70a002c2e026b_314.png)

**Handlers** 是一种特殊的任务，只在被其他任务“通知”（notify）时才会执行，通常用于重启服务或触发其他变更后的操作。

![](img/9802e8fc870ce5558fd70a002c2e026b_316.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_318.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_320.png)

**案例：配置文件更新后重启服务**
```yaml
---
- hosts: web1
  remote_user: root
  tasks:
    - name: 安装httpd
      yum:
        name: httpd
    - name: 拷贝配置文件
      copy:
        src: /root/.ansible/httpd.conf
        dest: /etc/httpd/conf/httpd.conf
      notify: restart httpd # 如果copy模块改变了文件，则通知下面的handler
  handlers:
    - name: restart httpd
      service:
        name: httpd
        state: restarted
```
*   只有当 `copy` 任务**真正改变了**远程主机上的文件时，才会触发 `restart httpd` 这个handler。
*   `handlers` 部分定义可被触发的操作。
*   `notify` 在任务中声明，指定要触发的handler名称。

![](img/9802e8fc870ce5558fd70a002c2e026b_322.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_324.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_326.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_328.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_330.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_331.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_333.png)

---

![](img/9802e8fc870ce5558fd70a002c2e026b_335.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_337.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_339.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_341.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_343.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_344.png)

## Vault：加密敏感数据 🔒

![](img/9802e8fc870ce5558fd70a002c2e026b_346.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_348.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_350.png)

Playbook中可能包含密码等敏感信息。Ansible Vault可以加密整个Playbook文件。

![](img/9802e8fc870ce5558fd70a002c2e026b_352.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_354.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_356.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_358.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_360.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_362.png)

以下是Vault常用操作：
*   **加密现有文件**：`ansible-vault encrypt hello.yml` (会提示输入密码)
*   **查看加密文件**：`ansible-vault view hello.yml`
*   **编辑加密文件**：`ansible-vault edit hello.yml`
*   **解密文件**：`ansible-vault decrypt hello.yml`
*   **创建时就加密新文件**：`ansible-vault create secret.yml`
*   **更改加密密码**：`ansible-vault rekey hello.yml`

![](img/9802e8fc870ce5558fd70a002c2e026b_364.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_365.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_367.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_369.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_371.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_373.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_375.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_376.png)

---

![](img/9802e8fc870ce5558fd70a002c2e026b_378.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_380.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_382.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_384.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_386.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_388.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_390.png)

## 总结 📋

![](img/9802e8fc870ce5558fd70a002c2e026b_392.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_394.png)

本节课中我们一起学习了：
1.  **Ansible Galaxy**：用于搜索、安装、管理和分享社区角色。
2.  **Ansible Console**：交互式命令行工具，便于测试和操作。
3.  **YAML语法基础**：理解了Playbook编写的缩进、列表、字典等基本规则。
4.  **Playbook核心结构**：掌握了 `hosts`、`tasks` 等基本元素的写法，并编写了第一个Playbook。
5.  **错误处理**：学会了使用 `ignore_errors` 或shell技巧来防止任务失败导致剧本中止。
6.  **Handlers**：理解了如何通过触发器在文件变更等事件后自动执行重启服务等操作。
7.  **Ansible Vault**：了解了如何加密Playbook以保护其中的敏感数据。

![](img/9802e8fc870ce5558fd70a002c2e026b_396.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_398.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_400.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_402.png)

![](img/9802e8fc870ce5558fd70a002c2e026b_404.png)

这些是编写自动化任务脚本的基础，下一节课我们将深入探讨Playbook中的变量管理、循环、条件判断等更高级的功能。