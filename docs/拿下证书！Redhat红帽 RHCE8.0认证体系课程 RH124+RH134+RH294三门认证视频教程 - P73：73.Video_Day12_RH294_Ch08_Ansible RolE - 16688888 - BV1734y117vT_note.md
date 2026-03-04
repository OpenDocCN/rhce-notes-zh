# Ansible 课程：第8章：Ansible 角色

![](img/7e8ba747a8f9847724b3221e3f3b232c_0.png)

在本节课中，我们将要学习 Ansible 角色的概念、结构、创建和使用方法。角色是一种将 Playbook 内容模块化、结构化的强大方式，能够极大地提高配置管理的效率和可重用性。

## 概述

Ansible 角色是一种将任务、变量、文件、模板和处理程序等组件打包的标准化方法。它允许我们将复杂的配置管理任务分解为可重用的单元，从而简化 Playbook 的编写和维护。本章将详细介绍角色的结构、如何创建自定义角色、如何使用系统角色以及如何通过 Ansible Galaxy 获取社区共享的角色。

---

## 角色简介与结构

上一节我们介绍了 Ansible 的基本概念，本节中我们来看看如何通过角色来组织和复用代码。

角色可以理解为一种“打包工具”，它将多个任务文件通过既定的目录结构进行封装。在调用时，我们只需引用角色名称即可，无需编写冗长的任务列表。这包括自定义角色，以及通过 `ansible-galaxy` 工具来引用他人编写好的角色。

![](img/7e8ba747a8f9847724b3221e3f3b232c_2.png)

以下是角色的标准目录结构，我们可以使用 `tree` 命令来查看：

```
角色名称/
├── defaults
│   └── main.yml
├── files
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── tasks
│   └── main.yml
├── templates
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml
```

我们无需手动创建这些目录，在初始化角色时会自动生成。这种结构的好处在于，我们无需在 Playbook 中编写所有内容，只需将各部分代码分门别类地放入对应文件夹，并在使用时直接引用文件名，非常方便。

以下是各个目录的用途说明：

![](img/7e8ba747a8f9847724b3221e3f3b232c_4.png)

*   **defaults**：用于定义角色变量的默认值，优先级较低，通常可在 Playbook 中被覆盖。
*   **files**：用于存放角色任务中需要复制的静态文件。
*   **handlers**：用于存放角色的处理程序，当任务发生改变并发出 `notify` 时触发。
*   **meta**：用于存放角色的元数据信息，例如作者、许可证、支持的平台以及依赖的其他角色。
*   **tasks**：这是核心目录，用于存放角色所有任务的定义。如果有多个任务文件，可以在 `main.yml` 中编排执行顺序。
*   **templates**：用于存放 Jinja2 模板文件。
*   **tests**：用于存放测试该角色所需的清单和测试 Playbook。
*   **vars**：用于定义角色的变量，此处定义的变量优先级较高，通常用于内部用途。

此外，根目录下通常还有一个 `README.md` 文件，用于编写角色的说明、使用要求和示例。

![](img/7e8ba747a8f9847724b3221e3f3b232c_6.png)

![](img/7e8ba747a8f9847724b3221e3f3b232c_8.png)

简单来说，角色就像编程中的“打包”操作。当我们需要使用角色时，只需在 Playbook 中调用它即可，主 Playbook 的编写会变得非常简洁。

![](img/7e8ba747a8f9847724b3221e3f3b232c_10.png)

在 Playbook 中使用角色非常简单。角色中定义的任务、处理程序等会按顺序被导入。角色中的 `copy`、`script` 或 `template` 等任务可以引用相关文件，而无需指定相对或绝对路径，只需写文件名，Ansible 会按照默认路径去查找。

关于变量优先级：在角色中，通过 `vars` 目录定义的变量，其优先级高于在 `defaults` 目录中定义的默认值。

---

## 创建与使用自定义角色

了解了角色的结构后，本节我们来实际操作如何创建和使用一个自定义角色。

首先，我们需要定义角色的存放路径，这是使用角色的前提。可以通过设置 `roles_path` 来指定，这个文件夹需要手动创建。

![](img/7e8ba747a8f9847724b3221e3f3b232c_12.png)

定义好路径后，我们可以使用 `ansible-galaxy` 命令行工具来初始化角色的框架。这个工具也可用于从网络下载和管理角色。我们也可以使用标准的 Linux 命令来创建目录结构，但使用 `ansible-galaxy` 更为便捷。

![](img/7e8ba747a8f9847724b3221e3f3b232c_14.png)

![](img/7e8ba747a8f9847724b3221e3f3b232c_16.png)

进入我们为角色创建的目录（例如 `roles`），然后执行以下命令来初始化一个名为 `my_httpd_role` 的角色：

![](img/7e8ba747a8f9847724b3221e3f3b232c_18.png)

```bash
cd roles
ansible-galaxy init my_httpd_role
```

命令执行成功后，会生成完整的角色目录结构。接下来，我们开始编写角色的具体内容。

**编写任务**：所有任务定义在 `tasks/main.yml` 中。我们可以将一个大任务拆分成多个小任务文件，然后在 `main.yml` 中通过 `import_tasks` 或 `include_tasks` 按顺序导入。这就像组装家具，先把各个部件准备好，再拼装起来。

![](img/7e8ba747a8f9847724b3221e3f3b232c_20.png)

**定义变量**：角色所需的变量可以在 `vars/main.yml` 或 `defaults/main.yml` 中定义。`vars` 中的变量优先级更高。

**准备文件与模板**：静态文件放入 `files/` 目录，Jinja2 模板文件放入 `templates/` 目录。

![](img/7e8ba747a8f9847724b3221e3f3b232c_22.png)

**定义处理程序**：如果需要重启服务等操作，可以在 `handlers/main.yml` 中定义处理程序，并在任务中通过 `notify` 调用。

![](img/7e8ba747a8f9847724b3221e3f3b232c_24.png)

所有模块编写完成后，我们回到 Playbook 目录，编写一个非常简洁的 Playbook 来调用这个角色：

![](img/7e8ba747a8f9847724b3221e3f3b232c_26.png)

```yaml
---
- hosts: webservers
  roles:
    - my_httpd_role
```

![](img/7e8ba747a8f9847724b3221e3f3b232c_28.png)

运行这个 Playbook，Ansible 会自动查找并执行角色中定义的所有任务。这样，我们就实现了一条龙式的服务部署，所有逻辑都封装在角色内部，便于维护和复用。

![](img/7e8ba747a8f9847724b3221e3f3b232c_30.png)

角色创建完成后，可以打包并通过 `ansible-galaxy` 进行安装和分享，因为它支持压缩包格式。

![](img/7e8ba747a8f9847724b3221e3f3b232c_32.png)

![](img/7e8ba747a8f9847724b3221e3f3b232c_34.png)

![](img/7e8ba747a8f9847724b3221e3f3b232c_36.png)

---

![](img/7e8ba747a8f9847724b3221e3f3b232c_38.png)

## 使用系统角色与 Ansible Galaxy

除了自定义角色，我们还可以利用现成的角色来加速工作，这包括系统自带的角色和社区共享的角色。

![](img/7e8ba747a8f9847724b3221e3f3b232c_40.png)

![](img/7e8ba747a8f9847724b3221e3f3b232c_42.png)

从 RHEL 7.4 开始，系统自带了一些角色，包名为 `rhel-system-roles`。这些角色由红帽维护，包含了诸如时间同步（`timesync`）、内核参数调优（`tuned`）、SELinux 管理等功能。我们可以在控制节点上安装这个软件包来获取它们：

![](img/7e8ba747a8f9847724b3221e3f3b232c_43.png)

```bash
sudo dnf install rhel-system-roles
```

![](img/7e8ba747a8f9847724b3221e3f3b232c_45.png)

安装后，这些角色通常位于 `/usr/share/ansible/roles/` 目录下，可以直接在 Playbook 中引用。需要注意的是，部分系统角色可能对 Ansible 版本有特定要求。

另一个强大的资源是 **Ansible Galaxy**。这是一个由社区维护的公共角色库，网址是 `galaxy.ansible.com`。我们可以使用 `ansible-galaxy` 命令来搜索、下载和使用这些角色。

![](img/7e8ba747a8f9847724b3221e3f3b232c_47.png)

例如，搜索与 Redis 相关的、适用于企业 Linux 的角色：

```bash
ansible-galaxy search redis --platforms EL
```

查看某个特定角色（如 `geerlingguy.redis`）的详细信息：

```bash
ansible-galaxy info geerlingguy.redis
```

下载并安装一个角色到本地角色路径：

![](img/7e8ba747a8f9847724b3221e3f3b232c_49.png)

```bash
ansible-galaxy install geerlingguy.redis
```

下载后，就可以像使用自定义角色一样在 Playbook 中调用它。我们还可以编写一个 `requirements.yml` 文件来批量定义和管理需要安装的角色及其版本。

查看已安装的角色列表：

```bash
ansible-galaxy list
```

![](img/7e8ba747a8f9847724b3221e3f3b232c_51.png)

![](img/7e8ba747a8f9847724b3221e3f3b232c_53.png)

删除一个已安装的角色：

```bash
ansible-galaxy remove geerlingguy.redis
```

利用 Ansible Galaxy，我们可以践行“拿来主义”，对于常见的中间件（如 Nginx、MySQL、HAProxy 等），直接使用社区中经过验证的优秀角色，可以极大地减少重复劳动，提升部署效率和质量。

---

## 总结

本节课中我们一起学习了 Ansible 角色的核心知识。我们首先了解了角色的概念和标准目录结构，它通过将任务、变量、文件等组件模块化，使 Playbook 更清晰、更易维护。接着，我们一步步实践了如何创建、编写和使用一个自定义的 HTTPD 服务部署角色。最后，我们介绍了如何利用系统自带的 `rhel-system-roles` 和强大的社区资源 Ansible Galaxy 来获取和使用现成的角色，从而进一步提升自动化效率。掌握角色是编写高效、可复用 Ansible 代码的关键一步。