# Redhat红帽 RHCE8.0认证体系课程 - P60：60_Video_Day10_Ch03b_Ansible Playbook练习

![](img/366eac0799926033b205d6446ace2dd7_1.png)

![](img/366eac0799926033b205d6446ace2dd7_3.png)

## 概述
在本节课中，我们将学习如何编写一个完整的Ansible Playbook，并通过一个具体的练习来巩固对Playbook结构、模块使用和密等性等核心概念的理解。我们还将回顾和解答一些关于Ansible执行特性的常见问题。

---

![](img/366eac0799926033b205d6446ace2dd7_5.png)

![](img/366eac0799926033b205d6446ace2dd7_6.png)

## 解答问题与概念回顾

![](img/366eac0799926033b205d6446ace2dd7_8.png)

![](img/366eac0799926033b205d6446ace2dd7_10.png)

![](img/366eac0799926033b205d6446ace2dd7_12.png)

![](img/366eac0799926033b205d6446ace2dd7_13.png)

上一节我们介绍了Ansible Playbook的基础结构。在开始新的练习之前，我们先来解答两个关于Ansible核心特性的问题。

**第一个问题：关于密等性**
Ansible具有**密等性**特点。这意味着在执行剧本或命令时，即使多次执行，Ansible也会判断目标主机的当前状态。只有当目标状态与剧本描述的状态不一致时，Ansible才会执行操作。这保证了操作仅执行必要的一次，而不会产生重复或覆盖问题。

**第二个问题：关于错误处理**
如果剧本执行过程中出现问题并报错，Ansible不会自动回退已成功完成的操作。已成功完成的任务会保持其状态，而报错的任务会停止执行。我们可以通过语法检查（如 `ansible-playbook --syntax-check`）来提前发现语法错误。对于逻辑错误或运行时错误，我们可以在后续学习“任务控制”时，通过定义错误处理、忽略错误或设置回退方案来管理。

![](img/366eac0799926033b205d6446ace2dd7_15.png)

---

## Playbook练习：配置Web服务器

现在，我们来看一个具体的Playbook练习。这个练习的目标是在受控主机上部署一个简单的网页并配置防火墙。

![](img/366eac0799926033b205d6446ace2dd7_17.png)

![](img/366eac0799926033b205d6446ace2dd7_19.png)

**练习场景**：
我们需要在目标主机上完成以下任务：
1.  在Web服务器的默认目录创建并写入一个HTML文件。
2.  在防火墙中永久放行HTTP服务（端口80）。

![](img/366eac0799926033b205d6446ace2dd7_21.png)

![](img/366eac0799926033b205d6446ace2dd7_23.png)

**注意**：由于安装`httpd`软件包时会自动创建其默认目录，因此我们无需在Playbook中额外创建目录。

![](img/366eac0799926033b205d6446ace2dd7_25.png)

![](img/366eac0799926033b205d6446ace2dd7_27.png)

![](img/366eac0799926033b205d6446ace2dd7_29.png)

![](img/366eac0799926033b205d6446ace2dd7_30.png)

以下是完整的Playbook实现：

![](img/366eac0799926033b205d6446ace2dd7_32.png)

![](img/366eac0799926033b205d6446ace2dd7_34.png)

![](img/366eac0799926033b205d6446ace2dd7_36.png)

```yaml
---
- name: 配置Web服务器内容与防火墙
  hosts: all
  tasks:
    - name: 创建并写入index.html文件
      copy:
        content: |
          <h1>Hello Ansible Playbook</h1>
        dest: /var/www/html/index.html
        owner: apache
        group: apache
        mode: '0644'

    - name: 在防火墙中永久启用http服务
      firewalld:
        service: http
        permanent: yes
        immediate: yes
        state: enabled

    - name: 在防火墙中永久放行80端口
      firewalld:
        port: 80/tcp
        permanent: yes
        immediate: yes
        state: enabled
```

![](img/366eac0799926033b205d6446ace2dd7_38.png)

**代码解析**：
*   **`copy` 模块**：使用 `content` 参数直接定义文件内容，`dest` 参数指定目标路径。`owner`、`group` 和 `mode` 参数用于设置文件属性和权限。
*   **`firewalld` 模块**：
    *   第一个任务使用 `service: http` 来放行预定义的HTTP服务。
    *   第二个任务使用 `port: 80/tcp` 来明确放行TCP协议的80端口。端口规则需要指定协议（如`tcp`或`udp`）。
    *   `permanent: yes` 表示规则永久生效，`immediate: yes` 表示立即应用规则，`state: enabled` 表示启用该规则。

![](img/366eac0799926033b205d6446ace2dd7_40.png)

**执行与验证**：
在运行Playbook前，建议先进行语法检查：
```bash
ansible-playbook web_setup.yml --syntax-check
```
然后执行Playbook（使用 `-v` 参数查看详细输出）：
```bash
ansible-playbook web_setup.yml -v
```
执行成功后，可以使用 `curl` 命令测试两台目标主机，验证网页是否可以正常访问。

![](img/366eac0799926033b205d6446ace2dd7_42.png)

---

## 总结
本节课我们一起学习了如何编写一个实用的Ansible Playbook。我们通过一个配置Web服务器的练习，实践了`copy`和`firewalld`模块的使用，并再次理解了Ansible密等性的重要性。我们还明确了Playbook执行出错时的行为，为后续学习任务控制打下了基础。记住，将任务模块化并清晰定义其参数，是编写高效、可靠Playbook的关键。