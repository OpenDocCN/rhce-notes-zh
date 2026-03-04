# Redhat红帽 RHCE8.0认证体系课程：P63：管理事实 📚

在本节课中，我们将要学习Ansible中一个非常重要的概念——**事实**。事实是Ansible在执行剧本时自动收集的关于受管主机的信息，这些信息以变量的形式存在，可以被剧本中的任务调用，用于实现条件判断、配置生成等多种功能。

![](img/dd91d67692a12c783e52e19054efbe0d_1.png)

![](img/dd91d67692a12c783e52e19054efbe0d_3.png)

![](img/dd91d67692a12c783e52e19054efbe0d_5.png)

---

![](img/dd91d67692a12c783e52e19054efbe0d_7.png)

## 什么是事实？🔍

![](img/dd91d67692a12c783e52e19054efbe0d_9.png)

上一节我们介绍了变量的多种定义方式，本节中我们来看看由Ansible自动收集的变量——事实。

![](img/dd91d67692a12c783e52e19054efbe0d_11.png)

![](img/dd91d67692a12c783e52e19054efbe0d_12.png)

![](img/dd91d67692a12c783e52e19054efbe0d_14.png)

![](img/dd91d67692a12c783e52e19054efbe0d_16.png)

![](img/dd91d67692a12c783e52e19054efbe0d_18.png)

事实是Ansible在执行剧本时，自动收集的关于受管主机的通用信息。这些信息是只读的，反映了主机在收集时刻的状态，因此被称为“事实”。事实包含的内容非常广泛。

![](img/dd91d67692a12c783e52e19054efbe0d_20.png)

以下是事实可能包含的一些信息类别：
*   **系统信息**：如主机名称、内核版本、操作系统版本。
*   **网络信息**：如网络接口、IP地址。
*   **硬件信息**：如CPU数量、可用内存、磁盘空间。
*   **环境信息**：如各种环境变量。

![](img/dd91d67692a12c783e52e19054efbe0d_22.png)

这些信息可以用于多种场景，例如：
*   根据内核版本来决定是否执行内核更新任务。
*   根据内存使用情况调整应用程序的内存分配策略。
*   基于获取的IP地址信息来配置网络服务。

![](img/dd91d67692a12c783e52e19054efbe0d_24.png)

![](img/dd91d67692a12c783e52e19054efbe0d_26.png)

![](img/dd91d67692a12c783e52e19054efbe0d_28.png)

![](img/dd91d67692a12c783e52e19054efbe0d_29.png)

## 如何获取事实？🛠️

![](img/dd91d67692a12c783e52e19054efbe0d_31.png)

![](img/dd91d67692a12c783e52e19054efbe0d_32.png)

![](img/dd91d67692a12c783e52e19054efbe0d_34.png)

![](img/dd91d67692a12c783e52e19054efbe0d_35.png)

![](img/dd91d67692a12c783e52e19054efbe0d_36.png)

在Ansible 2.3版本之后，收集事实的任务默认会自动运行，无需手动添加。这个自动过程被称为 `gathering facts`。

![](img/dd91d67692a12c783e52e19054efbe0d_38.png)

![](img/dd91d67692a12c783e52e19054efbe0d_40.png)

![](img/dd91d67692a12c783e52e19054efbe0d_42.png)

![](img/dd91d67692a12c783e52e19054efbe0d_43.png)

![](img/dd91d67692a12c783e52e19054efbe0d_44.png)

如果需要在剧本执行过程中查看详细的事实信息，可以在剧本的 `play` 级别设置 `gather_facts: yes`。

![](img/dd91d67692a12c783e52e19054efbe0d_45.png)

![](img/dd91d67692a12c783e52e19054efbe0d_47.png)

![](img/dd91d67692a12c783e52e19054efbe0d_49.png)

![](img/dd91d67692a12c783e52e19054efbe0d_51.png)

![](img/dd91d67692a12c783e52e19054efbe0d_53.png)

让我们通过一个剧本来演示如何获取并显示详细的事实信息。

![](img/dd91d67692a12c783e52e19054efbe0d_55.png)

![](img/dd91d67692a12c783e52e19054efbe0d_57.png)

**剧本示例：`manage_facts.yml`**
```yaml
---
- name: 显示事实的详细信息
  hosts: node1
  gather_facts: yes
  tasks:
    - name: 调试输出事实
      debug:
        var: ansible_facts
```

![](img/dd91d67692a12c783e52e19054efbe0d_59.png)

运行此剧本后，Ansible会输出一长串关于 `node1` 主机的详细信息，包括IP地址、发行版、分区、时间、磁盘等。所有收集到的事实都存储在一个名为 **`ansible_facts`** 的变量中。

## 如何使用事实？💡

![](img/dd91d67692a12c783e52e19054efbe0d_61.png)

了解了如何获取事实后，我们来看看如何在任务中调用这些具体的值。

![](img/dd91d67692a12c783e52e19054efbe0d_63.png)

![](img/dd91d67692a12c783e52e19054efbe0d_64.png)

![](img/dd91d67692a12c783e52e19054efbe0d_66.png)

![](img/dd91d67692a12c783e52e19054efbe0d_67.png)

![](img/dd91d67692a12c783e52e19054efbe0d_68.png)

![](img/dd91d67692a12c783e52e19054efbe0d_70.png)

我们可以通过 `ansible_facts` 变量来引用具体的事实值。引用方式有两种，取决于事实的结构。

![](img/dd91d67692a12c783e52e19054efbe0d_72.png)

![](img/dd91d67692a12c783e52e19054efbe0d_74.png)

![](img/dd91d67692a12c783e52e19054efbe0d_75.png)

**核心概念：引用事实**
*   对于字典（散列）类型的事实，可以使用**点号`.`**或**方括号`[]`**来检索值。
*   公式：`ansible_facts.<key>` 或 `ansible_facts['<key>']`
*   注意：如果键名中包含点号（如`ansible_facts.fqdn`），则**必须**使用方括号语法，例如 `ansible_facts['fqdn']`。

![](img/dd91d67692a12c783e52e19054efbe0d_77.png)

![](img/dd91d67692a12c783e52e19054efbe0d_79.png)

![](img/dd91d67692a12c783e52e19054efbe0d_81.png)

以下是几个常见事实的调用示例：

![](img/dd91d67692a12c783e52e19054efbe0d_83.png)

![](img/dd91d67692a12c783e52e19054efbe0d_84.png)

![](img/dd91d67692a12c783e52e19054efbe0d_86.png)

**剧本示例：`use_facts.yml`**
```yaml
---
- name: 使用事实信息
  hosts: node1
  gather_facts: yes
  tasks:
    - name: 获取完全限定域名 (FQDN)
      debug:
        msg: "主机FQDN是 {{ ansible_facts['fqdn'] }}"
    - name: 获取短主机名
      debug:
        msg: "短主机名是 {{ ansible_facts.nodename }}"
    - name: 获取默认IPv4地址
      debug:
        msg: "默认IPv4地址是 {{ ansible_facts.default_ipv4.address }}"
    - name: 获取网络接口列表
      debug:
        msg: "网络接口有 {{ ansible_facts.interfaces }}"
```

![](img/dd91d67692a12c783e52e19054efbe0d_88.png)

![](img/dd91d67692a12c783e52e19054efbe0d_90.png)

![](img/dd91d67692a12c783e52e19054efbe0d_92.png)

## 魔法变量 🧙

![](img/dd91d67692a12c783e52e19054efbe0d_93.png)

![](img/dd91d67692a12c783e52e19054efbe0d_95.png)

![](img/dd91d67692a12c783e52e19054efbe0d_97.png)

![](img/dd91d67692a12c783e52e19054efbe0d_99.png)

![](img/dd91d67692a12c783e52e19054efbe0d_100.png)

除了 `ansible_facts`，Ansible还提供了一些特殊的“魔法变量”。这些变量不是通过`gather_facts`收集的，而是由Ansible自动设置，用于获取与清单（Inventory）相关的信息。

![](img/dd91d67692a12c783e52e19054efbe0d_102.png)

![](img/dd91d67692a12c783e52e19054efbe0d_103.png)

![](img/dd91d67692a12c783e52e19054efbe0d_105.png)

![](img/dd91d67692a12c783e52e19054efbe0d_107.png)

以下是几个常用的魔法变量：

![](img/dd91d67692a12c783e52e19054efbe0d_109.png)

![](img/dd91d67692a12c783e52e19054efbe0d_111.png)

![](img/dd91d67692a12c783e52e19054efbe0d_112.png)

![](img/dd91d67692a12c783e52e19054efbe0d_114.png)

**魔法变量列表：**
*   **`hostvars`**：包含所有受管主机变量的字典。允许在一台主机上获取另一台主机的变量（包括事实）。
    *   示例：`hostvars[‘node2’].ansible_facts[‘fqdn’]` 可以在`node1`上获取`node2`的域名。
*   **`group_names`**：列出当前受管主机所属的所有组名。
*   **`groups`**：列出控制节点上资产清单中所有组和主机的映射关系。
*   **`inventory_hostname`**：当前受管主机在清单中定义的主机名。

![](img/dd91d67692a12c783e52e19054efbe0d_116.png)

![](img/dd91d67692a12c783e52e19054efbe0d_118.png)

![](img/dd91d67692a12c783e52e19054efbe0d_119.png)

让我们通过一个剧本来理解 `hostvars` 的用法。

![](img/dd91d67692a12c783e52e19054efbe0d_121.png)

![](img/dd91d67692a12c783e52e19054efbe0d_123.png)

**剧本示例：`magic_vars.yml`**
```yaml
---
- name: 演示魔法变量 hostvars
  hosts: all
  tasks:
    - name: 在 node2 上输出 node1 的 FQDN
      debug:
        msg: "Node1 的 FQDN 是 {{ hostvars['node1.lab.example.com'].ansible_facts['fqdn'] }}"
      when: inventory_hostname == "node2.lab.example.com"
```
这个剧本会在 `node2` 上执行一个任务，该任务通过 `hostvars` 获取并打印出 `node1` 的完全限定域名。

![](img/dd91d67692a12c783e52e19054efbe0d_125.png)

![](img/dd91d67692a12c783e52e19054efbe0d_127.png)

![](img/dd91d67692a12c783e52e19054efbe0d_128.png)

![](img/dd91d67692a12c783e52e19054efbe0d_130.png)

## 创建自定义事实 ✏️

![](img/dd91d67692a12c783e52e19054efbe0d_132.png)

![](img/dd91d67692a12c783e52e19054efbe0d_134.png)

![](img/dd91d67692a12c783e52e19054efbe0d_136.png)

![](img/dd91d67692a12c783e52e19054efbe0d_138.png)

除了使用系统事实，我们还可以在受管主机上创建自定义事实。这通常用于提供Ansible无法自动收集的应用程序或业务特定信息。

![](img/dd91d67692a12c783e52e19054efbe0d_139.png)

![](img/dd91d67692a12c783e52e19054efbe0d_141.png)

![](img/dd91d67692a12c783e52e19054efbe0d_143.png)

自定义事实可以通过在受管主机的 `/etc/ansible/facts.d/` 目录下放置脚本或JSON、INI格式的文件来创建。文件必须以 `.fact` 结尾。Ansible在执行`gather_facts`时会自动加载这些文件中的信息，并将其并入 `ansible_facts` 中，通常位于 `ansible_facts.local` 键下。

![](img/dd91d67692a12c783e52e19054efbe0d_145.png)

![](img/dd91d67692a12c783e52e19054efbe0d_147.png)

![](img/dd91d67692a12c783e52e19054efbe0d_149.png)

![](img/dd91d67692a12c783e52e19054efbe0d_151.png)

这部分内容在基础课程中不做深入要求，但需要知道其存在和基本机制。

---

## 总结 📝

本节课中我们一起学习了Ansible事实的管理。
*   **事实**是Ansible自动收集的受管主机信息，存储在 `ansible_facts` 变量中。
*   通过设置 `gather_facts: yes` 可以启用详细事实收集。
*   可以使用**点号**或**方括号**语法来引用具体的事实值，例如 `ansible_facts[‘fqdn’]`。
*   **魔法变量**如 `hostvars`、`groups` 等，提供了访问清单和其他运行时信息的能力。
*   了解可以通过 `/etc/ansible/facts.d/` 目录创建**自定义事实**。

![](img/dd91d67692a12c783e52e19054efbe0d_153.png)

![](img/dd91d67692a12c783e52e19054efbe0d_155.png)

![](img/dd91d67692a12c783e52e19054efbe0d_157.png)

掌握事实的获取和使用，是编写灵活、智能的Ansible剧本的关键，它使得剧本能够根据目标主机的实际状态做出动态决策。