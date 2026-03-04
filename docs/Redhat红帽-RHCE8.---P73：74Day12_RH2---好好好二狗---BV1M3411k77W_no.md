# Redhat红帽 RHCE8.0认证体系课程：P73：第九章：Ansible故障排除 🐛

![](img/ce499298aed3492554c2070fdab2b312_0.png)

在本节课中，我们将要学习Ansible故障排除的核心方法与工具。掌握这些技巧能帮助你在剧本执行出错时，快速定位并解决问题。

![](img/ce499298aed3492554c2070fdab2b312_2.png)

上一节我们介绍了Ansible的高级模块，本节中我们来看看如何系统地排查Ansible执行过程中的故障。

![](img/ce499298aed3492554c2070fdab2b312_3.png)

![](img/ce499298aed3492554c2070fdab2b312_4.png)

## 查看日志文件 📄

![](img/ce499298aed3492554c2070fdab2b312_6.png)

Ansible可以配置日志路径来记录执行详情，便于排查问题。

![](img/ce499298aed3492554c2070fdab2b312_8.png)

*   在Ansible配置文件中，可以定义一个日志路径，例如 `log_path = /var/log/ansible.log`。
*   执行剧本后，可以通过查看该日志文件（如 `cat /var/log/ansible.log`）来获取详细的执行记录和错误信息。

![](img/ce499298aed3492554c2070fdab2b312_9.png)

## 使用Debug模块

![](img/ce499298aed3492554c2070fdab2b312_11.png)

Debug模块是常用的调试工具，用于在屏幕上输出信息或变量值。

*   **输出指定信息**：使用 `msg` 参数直接在屏幕上显示内容。
*   **输出变量值**：通过 `var` 参数可以输出指定变量的值。

## 语法检查与分布调试

在运行剧本前进行语法检查，以及分布执行剧本，是预防和定位错误的有效方法。

![](img/ce499298aed3492554c2070fdab2b312_13.png)

以下是语法检查与调试的相关命令：
*   **语法检查**：使用 `ansible-playbook --syntax-check playbook.yml` 命令检查剧本语法是否正确。
*   **分布调试**：使用 `ansible-playbook --step playbook.yml` 命令。执行时，Ansible会为每个任务暂停并询问是否执行。输入 `y` 执行当前任务，输入 `n` 跳过，输入 `c` 则继续执行剩余所有任务。

## 从指定任务开始运行

![](img/ce499298aed3492554c2070fdab2b312_15.png)

当剧本较长时，可以从某个特定任务开始执行，避免重复运行已成功的部分。

![](img/ce499298aed3492554c2070fdab2b312_17.png)

*   使用 `--start-at-task` 参数，后面跟上任务的 `name`。例如：`ansible-playbook --start-at-task="安装软件包" playbook.yml`。

![](img/ce499298aed3492554c2070fdab2b312_19.png)

## 显示详细执行过程

![](img/ce499298aed3492554c2070fdab2b312_21.png)

增加输出信息的详细程度，有助于观察Ansible的每一步操作。

*   使用 `-v`、`-vv`、`-vvv` 或 `-vvvv` 参数，可以获取不同级别的详细信息。例如：`ansible-playbook -v playbook.yml`。

![](img/ce499298aed3492554c2070fdab2b312_23.png)

## 冒烟测试（空运行）

![](img/ce499298aed3492554c2070fdab2b312_25.png)

在不实际改变目标主机状态的情况下，模拟运行剧本以检查潜在问题。

![](img/ce499298aed3492554c2070fdab2b312_27.png)

*   使用 `--check` 参数进行空运行。例如：`ansible-playbook --check playbook.yml`。

![](img/ce499298aed3492554c2070fdab2b312_29.png)

![](img/ce499298aed3492554c2070fdab2b312_31.png)

## 使用URI模块检测服务

URI模块可用于检测Web服务等URL地址的可访问性。

这是一个使用URI模块检测节点Web服务状态的剧本示例：
```yaml
- name: 测试节点Web服务
  hosts: node2
  tasks:
    - name: 检查HTTP服务
      uri:
        url: http://node2
      register: http_result  # 将结果注册到变量

    - name: 输出结果
      debug:
        msg: "服务状态正常"
      when: http_result.status == 200  # 当状态码为200时输出信息
```
该剧本的作用是访问node2的HTTP服务，并将返回结果保存到变量 `http_result` 中。只有当返回的状态码为200时，才会输出“服务状态正常”的信息。

![](img/ce499298aed3492554c2070fdab2b312_33.png)

![](img/ce499298aed3492554c2070fdab2b312_34.png)

## 使用Script模块执行脚本

![](img/ce499298aed3492554c2070fdab2b312_36.png)

Script模块用于在受管主机上运行控制节点上的脚本，并根据脚本退出状态判断任务成败。

![](img/ce499298aed3492554c2070fdab2b312_38.png)

*   例如，在控制节点有一个脚本 `test.sh`，内容为 `echo "Hello Ansible"`。
*   在剧本中使用 `script` 模块调用它。如果脚本执行后返回的状态码非零，则任务标记为失败。

![](img/ce499298aed3492554c2070fdab2b312_40.png)

## 使用Fail和Assert模块

![](img/ce499298aed3492554c2070fdab2b312_41.png)

Fail和Assert模块用于主动控制任务流程，在特定条件下使任务失败。

![](img/ce499298aed3492554c2070fdab2b312_43.png)

![](img/ce499298aed3492554c2070fdab2b312_45.png)

*   **Fail模块**：通常与条件判断 `when` 一起使用，在满足条件时主动使任务失败。
*   **Assert模块**：是Fail模块的另一种替代选择，用于声明某个条件必须为真。

![](img/ce499298aed3492554c2070fdab2b312_47.png)

## 连接故障排查要点 🔌

![](img/ce499298aed3492554c2070fdab2b312_49.png)

当Ansible无法连接受管主机时，需要检查以下几个常见方面。

![](img/ce499298aed3492554c2070fdab2b312_51.png)

以下是连接故障的主要检查点：
*   **SSH连接与用户**：检查是否指定了正确的远程连接用户（如 `ansible_user`）。
*   **SSH密钥认证**：如果使用密钥认证，请确认是否已将控制节点的公钥分发到受管主机的相应用户下。
*   **受管主机SSH配置**：检查受管主机的SSH服务配置（如 `sshd_config`），确保允许相应用户的连接。

![](img/ce499298aed3492554c2070fdab2b312_53.png)

![](img/ce499298aed3492554c2070fdab2b312_55.png)

## 使用Ping和Command模块

![](img/ce499298aed3492554c2070fdab2b312_57.png)

Ping和Command模块可用于基础的连通性和命令测试。

*   **Ping模块**：测试与受管主机的Ansible连接是否正常。命令：`ansible all -m ping`。
*   **Command模块**：在受管主机上执行简单的Shell命令，用于验证环境。

本节课中我们一起学习了Ansible故障排除的核心方法。我们了解了如何查看日志、使用Debug模块、进行语法检查和分布调试。还掌握了从指定任务运行、显示详细过程、进行冒烟测试等技巧。最后，我们学习了使用URI、Script、Fail等模块进行特定测试，并总结了连接故障的排查要点。掌握这些技能将使你能够更高效地定位和解决Ansible自动化部署中的问题。