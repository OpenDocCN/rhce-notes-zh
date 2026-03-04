# Ansible 课程：第三章：Ansible Playbook 编写与运行 🚀

![](img/c952a2ca494014381af1e586605e96c0_0.png)

在本节课中，我们将学习如何将 Ansible 的临时命令转换为结构化的 Playbook。我们将重点掌握 Playbook 的编写、语法验证和运行方法，核心在于理解 YAML 格式的规范。

## 概述

Playbook 是 Ansible 的配置、部署和编排语言，它将临时命令组织成结构化的任务列表。掌握 Playbook 需要解决三个核心问题：如何编写、如何校验语法以及如何运行。其中，正确使用 **YAML** 格式是编写 Playbook 的基础。

## 创建 Playbook 工作目录

为了方便管理，我们首先在 Ansible 项目目录下创建一个用于存放 Playbook 的目录。为了确保 Ansible 命令能正确执行，我们保持在当前工作目录下操作。

以下是创建目录的命令：
```bash
mkdir playbooks
```

## 编写第一个 Playbook

我们将通过一个安装并启用 HTTPD 服务的完整示例来学习 Playbook 的编写。这个 Playbook 将包含创建目录、挂载 ISO、配置 YUM 仓库、安装软件包和启动服务等一系列任务。

首先，我们创建一个名为 `httpd.yml` 的 Playbook 文件。YAML 文件可以使用 `.yml` 或 `.yaml` 扩展名。

一个 Playbook 的基本结构以三个横杠 `---` 开头，这表示一个 YAML 文档的开始。

### Playbook 结构详解

Playbook 由“剧本”组成，每个剧本包含以下几个关键部分：

*   **`name`**: 描述此 Playbook 的用途。
*   **`hosts`**: 指定任务将在哪些受管主机上执行。
*   **`tasks`**: 定义要执行的任务列表。

YAML 格式严格要求缩进（通常为两个空格）和冒号后的空格。以下是编写 Playbook 时需要注意的格式要点：

*   使用两个空格进行缩进，不要使用 Tab 键。
*   列表项以 `- `（横杠加空格）开头。
*   键值对使用 `key: value` 格式，冒号后面必须有一个空格。

![](img/c952a2ca494014381af1e586605e96c0_2.png)

现在，让我们开始编写具体的任务。每个任务都有自己的 `name` 和调用的模块及其参数。

以下是第一个任务的示例，用于创建一个目录：
```yaml
- name: Create directory for ISO mount
  file:
    path: /mnt/iso
    state: directory
```

![](img/c952a2ca494014381af1e586605e96c0_4.png)

接下来，我们添加挂载 ISO 光盘的任务：
```yaml
- name: Mount RHEL8 ISO
  mount:
    src: /dev/sr0
    path: /mnt/iso
    fstype: iso9660
    state: mounted
```

然后，配置 YUM 软件仓库。这里我们配置两个仓库：`BaseOS` 和 `AppStream`。
```yaml
- name: Configure YUM repository - BaseOS
  yum_repository:
    file: rhel8
    name: BaseOS
    description: RHEL8 BaseOS
    baseurl: file:///mnt/iso/BaseOS
    enabled: yes
    gpgcheck: no

- name: Configure YUM repository - AppStream
  yum_repository:
    file: rhel8
    name: AppStream
    description: RHEL8 AppStream
    baseurl: file:///mnt/iso/AppStream
    enabled: yes
    gpgcheck: no
```

现在，安装 HTTPD 软件包：
```yaml
- name: Install HTTPD package
  yum:
    name: httpd
    state: present
```

![](img/c952a2ca494014381af1e586605e96c0_6.png)

最后，启动并设置 HTTPD 服务开机自启：
```yaml
- name: Start and enable HTTPD service
  service:
    name: httpd
    state: started
    enabled: yes
```

![](img/c952a2ca494014381af1e586605e96c0_8.png)

![](img/c952a2ca494014381af1e586605e96c0_10.png)

为了保持代码清晰易读，建议在每个任务之间使用空行进行分隔。

## 验证 Playbook 语法

![](img/c952a2ca494014381af1e586605e96c0_12.png)

编写完成后，在运行 Playbook 之前，必须验证其 YAML 语法是否正确。我们可以使用 `ansible-playbook` 命令的 `--syntax-check` 选项。

执行以下命令进行语法检查：
```bash
ansible-playbook playbooks/httpd.yml --syntax-check
```

如果语法正确，命令将不会有错误输出。如果存在语法错误，例如缩进错误或拼写错误的属性，命令会明确指出错误的位置和原因。

![](img/c952a2ca494014381af1e586605e96c0_14.png)

**请注意**：语法检查只验证 YAML 格式是否正确，**不验证**任务逻辑（例如，路径是否存在、软件包名称是否正确）。逻辑错误需要在运行时才能发现。

![](img/c952a2ca494014381af1e586605e96c0_15.png)

![](img/c952a2ca494014381af1e586605e96c0_17.png)

## 运行 Playbook

![](img/c952a2ca494014381af1e586605e96c0_19.png)

语法验证通过后，就可以运行 Playbook 了。使用 `ansible-playbook` 命令后跟 Playbook 文件的路径。

![](img/c952a2ca494014381af1e586605e96c0_21.png)

![](img/c952a2ca494014381af1e586605e96c0_22.png)

基本运行命令如下：
```bash
ansible-playbook playbooks/httpd.yml
```

![](img/c952a2ca494014381af1e586605e96c0_24.png)

运行后，Ansible 会输出执行摘要，显示每个任务的执行状态：
*   **`ok`**: 任务成功执行，且系统状态未发生改变（幂等性体现）。
*   **`changed`**: 任务成功执行，并且改变了系统状态。
*   **`failed`**: 任务执行失败。
*   **`unreachable`**: 无法连接到受管主机。

![](img/c952a2ca494014381af1e586605e96c0_25.png)

![](img/c952a2ca494014381af1e586605e96c0_26.png)

### 输出详细程度控制

![](img/c952a2ca494014381af1e586605e96c0_28.png)

![](img/c952a2ca494014381af1e586605e96c0_30.png)

你可以通过 `-v` 参数来控制输出信息的详细程度，这在调试时非常有用。

![](img/c952a2ca494014381af1e586605e96c0_31.png)

![](img/c952a2ca494014381af1e586605e96c0_33.png)

以下是不同详细级别的参数：
*   `-v`: 显示任务结果。
*   `-vv`: 显示任务结果和配置信息。
*   `-vvv`: 增加连接详细信息。
*   `-vvvv`: 显示所有详细信息，包括插件内部信息。

![](img/c952a2ca494014381af1e586605e96c0_35.png)

![](img/c952a2ca494014381af1e586605e96c0_37.png)

对于日常使用，`-v` 或 `-vv` 通常就足够了。

### 空运行（Dry Run）

有时，你可能想预览 Playbook 会做什么更改，而不实际执行。这可以通过 `-C` 或 `--check` 参数实现空运行模式。

![](img/c952a2ca494014381af1e586605e96c0_39.png)

执行以下命令进行空运行：
```bash
ansible-playbook playbooks/httpd.yml --check
```

在空运行模式下，Ansible 会模拟执行并报告每个任务**预期**会发生的变化（`changed`），但不会对受管主机进行任何实际修改。

## 总结

本节课中，我们一起学习了 Ansible Playbook 的核心知识。我们从临时命令过渡，学习了如何编写结构化的 YAML 格式 Playbook，其关键在于正确的缩进和键值对格式。我们掌握了使用 `--syntax-check` 验证语法，以及使用 `ansible-playbook` 命令运行和调试 Playbook 的方法。通过控制输出详细程度（`-v`）和进行空运行（`--check`），我们可以更安全、更高效地管理和测试我们的自动化任务。