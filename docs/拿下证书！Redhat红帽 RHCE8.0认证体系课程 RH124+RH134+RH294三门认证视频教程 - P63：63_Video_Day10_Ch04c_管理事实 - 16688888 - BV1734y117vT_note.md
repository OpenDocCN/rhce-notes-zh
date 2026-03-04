# Ansible 认证课程：第四章：管理事实

![](img/dbc2c41cf467994133835bcefc4294d7_0.png)

在本节课程中，我们将学习 Ansible 中的“事实”概念。事实是 Ansible 在执行剧本时自动收集的受管主机信息，这些信息以变量的形式存在，可用于后续的任务决策和配置。

![](img/dbc2c41cf467994133835bcefc4294d7_2.png)

## 什么是事实？

![](img/dbc2c41cf467994133835bcefc4294d7_4.png)

上一节我们介绍了变量的使用，本节中我们来看看 Ansible 自动收集的一种特殊变量——事实。

事实是 Ansible 在执行剧本过程中，自动收集的关于受管主机的通用信息。这些信息是只读的，反映了主机在收集时刻的状态，因此被称为“事实”。它包含了诸如主机名、内核版本、网络接口、IP地址、操作系统版本、环境变量、CPU数量、可用内存和磁盘空间等信息。

![](img/dbc2c41cf467994133835bcefc4294d7_6.png)

![](img/dbc2c41cf467994133835bcefc4294d7_8.png)

收集到的事实可以用于多种场景，例如：
*   监测资产状态。
*   根据内核版本决定是否执行系统更新。
*   根据内存使用情况调整应用程序的内存分配策略。
*   基于现有网络配置设置新的IP地址。

## 如何收集与查看事实？

![](img/dbc2c41cf467994133835bcefc4294d7_10.png)

默认情况下，Ansible 会在每个剧本执行第一个任务之前，自动运行一个名为 `gathering facts` 的步骤来收集事实。在早期版本中，这个步骤对应 `setup` 模块。

![](img/dbc2c41cf467994133835bcefc4294d7_12.png)

![](img/dbc2c41cf467994133835bcefc4294d7_14.png)

若要获取详细的事实信息，需要在剧本中明确启用事实收集。以下是查看事实信息的示例剧本：

![](img/dbc2c41cf467994133835bcefc4294d7_16.png)

![](img/dbc2c41cf467994133835bcefc4294d7_18.png)

```yaml
---
- name: 显示事实信息
  hosts: node1
  gather_facts: yes
  tasks:
    - name: 使用 debug 模块显示事实
      debug:
        var: ansible_facts
```

![](img/dbc2c41cf467994133835bcefc4294d7_20.png)

运行此剧本后，Ansible 会输出一长串关于 `node1` 主机的详细信息，所有事实都存储在名为 **`ansible_facts`** 的变量中。

## 如何引用事实？

![](img/dbc2c41cf467994133835bcefc4294d7_22.png)

收集到的事实存储在 `ansible_facts` 变量中，我们可以通过两种语法来引用其中的具体值。

![](img/dbc2c41cf467994133835bcefc4294d7_24.png)

![](img/dbc2c41cf467994133835bcefc4294d7_26.png)

以下是引用事实的几种常见示例：

![](img/dbc2c41cf467994133835bcefc4294d7_28.png)

![](img/dbc2c41cf467994133835bcefc4294d7_30.png)

![](img/dbc2c41cf467994133835bcefc4294d7_32.png)

*   **获取主机名**：`ansible_facts.hostname` 或 `ansible_facts[‘hostname’]`
*   **获取完全限定域名**：`ansible_facts.fqdn` 或 `ansible_facts[‘fqdn’]`
*   **获取默认 IPv4 地址**：`ansible_facts.default_ipv4.address`
*   **获取所有网络接口列表**：`ansible_facts.interfaces`
*   **获取特定磁盘分区大小**：`ansible_facts.devices.vda1.partitions.vda1.size`

![](img/dbc2c41cf467994133835bcefc4294d7_34.png)

**注意**：当事实的键名中包含点号（如 `fqdn`）时，推荐使用方括号 `[‘key’]` 的语法来引用，以避免歧义。

## 魔法变量

![](img/dbc2c41cf467994133835bcefc4294d7_36.png)

除了 `ansible_facts`，Ansible 还提供了一些特殊的“魔法变量”，它们无需收集事实即可直接使用，用于获取清单（inventory）相关信息。

以下是几个常用的魔法变量：

![](img/dbc2c41cf467994133835bcefc4294d7_38.png)

*   **`groups`**：列出清单中所有组及其包含的主机。
*   **`group_names`**：列出当前受管主机所属的所有组名。
*   **`hostvars`**：这是一个字典，可以获取其他受管主机的变量（包括事实）。例如，在 `node2` 上获取 `node1` 的 FQDN：`hostvars[‘node1’].ansible_facts.fqdn`
*   **`inventory_hostname`**：当前受管主机在清单中定义的主机名。

![](img/dbc2c41cf467994133835bcefc4294d7_40.png)

## 自定义事实

除了自动收集的事实，用户还可以在受管主机上创建自定义事实。通常，自定义事实脚本或文件放置在 `/etc/ansible/facts.d/` 目录下，且文件需以 `.fact` 结尾。Ansible 在执行时会自动加载这些自定义事实，并可通过 `ansible_facts.local` 进行访问。

## 本章总结

本节课中我们一起学习了 Ansible 事实的管理。
*   **事实**是 Ansible 自动收集的受管主机状态信息，存储在 `ansible_facts` 变量中。
*   通过 `gather_facts: yes` 启用详细事实收集。
*   可以使用点号或方括号语法引用具体的事实值。
*   **魔法变量**如 `groups`、`hostvars` 等，提供了访问清单信息和其他主机变量的便捷方式。
*   可以通过在受管主机上创建特定文件来定义**自定义事实**。

![](img/dbc2c41cf467994133835bcefc4294d7_42.png)

掌握事实的收集与使用，是编写智能化、自适应 Ansible 剧本的关键，例如生成主机资产报告、根据系统条件执行不同任务等，在后续的运维自动化实践中将经常用到。