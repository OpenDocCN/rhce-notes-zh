# Ansible 教程：01：Jinja2 模板与变量使用 🧩

在本节课中，我们将学习如何在 Ansible 的配置文件中使用 Jinja2 模板和变量。通过模板，我们可以根据受控主机的不同信息动态生成配置文件，从而实现更灵活的自动化管理。

![](img/005c83309106011bccb56b01db5082e7_1.png)

![](img/005c83309106011bccb56b01db5082e7_3.png)

![](img/005c83309106011bccb56b01db5082e7_4.png)

上一节我们介绍了 Ansible 角色和模块的基本使用，本节中我们来看看如何利用 Jinja2 模板来动态生成配置文件。

![](img/005c83309106011bccb56b01db5082e7_6.png)

![](img/005c83309106011bccb56b01db5082e7_8.png)

![](img/005c83309106011bccb56b01db5082e7_10.png)

## 回顾与问题引入

![](img/005c83309106011bccb56b01db5082e7_12.png)

在上节课中，我们编写了一个用于部署 Nginx 的角色。在定义其配置文件模板时，我们尝试修改一个参数，但遇到了错误。核心问题在于我们希望在模板中使用变量。

![](img/005c83309106011bccb56b01db5082e7_14.png)

例如，在 Nginx 配置中，我们希望根据主机的 CPU 核心数来动态设置 `worker_processes` 参数。我们使用了以下 Jinja2 表达式：
```jinja2
worker_processes {{ ansible_processor_vcpus + 2 }};
```
这个表达式会获取主机的虚拟 CPU 数量（`ansible_processor_vcpus`），然后加上 2，从而动态决定 Nginx 启动的工作进程数。

![](img/005c83309106011bccb56b01db5082e7_16.png)

![](img/005c83309106011bccb56b01db5082e7_18.png)

**注意**：在配置 Nginx 时，请确保主机上的 80 端口未被其他服务（如 `httpd`）占用，否则会导致服务启动冲突。

![](img/005c83309106011bccb56b01db5082e7_20.png)

![](img/005c83309106011bccb56b01db5082e7_22.png)

## Jinja2 模板实践：配置登录欢迎信息

![](img/005c83309106011bccb56b01db5082e7_24.png)

![](img/005c83309106011bccb56b01db5082e7_25.png)

![](img/005c83309106011bccb56b01db5082e7_26.png)

现在，我们通过一个具体任务来学习 Jinja2 模板的编写和使用。我们的目标是：在用户登录受控主机时，显示包含该系统硬件信息的自定义欢迎语。

![](img/005c83309106011bccb56b01db5082e7_28.png)

![](img/005c83309106011bccb56b01db5082e7_30.png)

![](img/005c83309106011bccb56b01db5082e7_32.png)

以下是实现此目标的步骤：

### 第一步：配置受控主机清单
我们已经在 `/etc/ansible/hosts` 文件中定义好了受控主机组，例如 `web2012` 组。

### 第二步：查询受控主机信息
我们需要获取受控主机的系统信息（如 CPU 核心数、总内存），以便在模板中引用。这可以通过 Ansible 的 `setup` 模块完成。
```bash
ansible web2012 -m setup
```
从输出中，我们可以找到相关的变量名，例如：
*   CPU 核心数：`ansible_processor_count`
*   总内存：`ansible_memtotal_mb`

![](img/005c83309106011bccb56b01db5082e7_34.png)

### 第三步：创建 Jinja2 模板配置文件
我们需要创建一个模板文件，其中包含我们想要显示的欢迎信息，并使用变量动态填充部分内容。
1.  创建一个工作目录：`mkdir show_information && cd show_information`
2.  创建模板文件 `motd.j2`：
```jinja2
System Total Memory: {{ ansible_memtotal_mb }} MB
System Processor Count: {{ ansible_processor_count }}
```
在这个模板中，我们直接使用双大括号 `{{ }}` 来引用 Ansible 收集到的 `facts` 变量。

![](img/005c83309106011bccb56b01db5082e7_36.png)

![](img/005c83309106011bccb56b01db5082e7_38.png)

### 第四步：创建 Playbook 文件
接下来，我们需要编写一个 Playbook 来调用这个模板，并将其部署到受控主机上。
创建 `motd.yml` 文件，内容如下：
```yaml
---
- hosts: web2012
  remote_user: root
  tasks:
    - name: Copy Jinja2 file
      template:
        src: motd.j2
        dest: /etc/motd
```
这个 Playbook 定义了一个任务：使用 `template` 模块将本地的 `motd.j2` 模板文件渲染后，复制到远程主机的 `/etc/motd` 路径下。

### 第五步：执行 Playbook
运行 Playbook 将配置推送到受控主机：
```bash
ansible-playbook motd.yml
```

![](img/005c83309106011bccb56b01db5082e7_40.png)

### 第六步：测试效果
通过 SSH 登录到任意一台受控主机，你将看到类似以下的欢迎信息：
```
System Total Memory: 3950 MB
System Processor Count: 1
```
这表明模板已成功执行，并动态显示了该主机的实际内存和 CPU 信息。

![](img/005c83309106011bccb56b01db5082e7_42.png)

**提示**：你还可以用同样的方法修改 `/etc/issue` 或 `/etc/issue.net` 文件，来配置其他登录场景的显示信息。

## 总结

![](img/005c83309106011bccb56b01db5082e7_44.png)

![](img/005c83309106011bccb56b01db5082e7_46.png)

![](img/005c83309106011bccb56b01db5082e7_48.png)

本节课中我们一起学习了 Jinja2 模板的核心用法。关键在于理解如何在模板文件中使用 `{{ 变量名 }}` 的语法来嵌入动态内容，这些变量主要来自 Ansible 自动收集的系统 `facts`。通过将模板与 `template` 模块结合在 Playbook 中，我们能够轻松实现“一份模板，多处适配”的配置管理，极大地提升了自动化脚本的灵活性和可维护性。