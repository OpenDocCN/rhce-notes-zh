# Redhat红帽 RHCE8.0认证体系课程：P59：Ansible Playbook 入门与实践 🚀

![](img/b2f275caa9ad35897322c44e6534620d_0.png)

在本节课中，我们将学习如何从使用 Ansible 临时命令过渡到编写和运行 Ansible Playbook。Playbook 是 Ansible 实现自动化配置、部署和编排的核心，它以 YAML 格式编写，能够将复杂的任务序列化、结构化。我们将重点掌握 Playbook 的编写、语法验证和运行方法。

## 从临时命令到 Playbook 📝

上一节我们介绍了 Ansible 的临时命令。本节中我们来看看如何将这些命令组织成更强大、可重复使用的 Playbook。

Playbook 本质上是将一系列临时命令按照层级和区块进行组织，并使用缩进来识别结构。我们需要掌握三个核心问题：如何编写、如何校验语法、如何运行。其中，最需要注意的就是 **YAML** 格式的书写规范。

## 编写第一个 Playbook ✍️

为了方便管理，我们首先在 Ansible 项目目录下创建一个用于存放 Playbook 的文件夹，例如 `playbooks`。但请注意，为了确保 Ansible 配置文件生效，我们通常直接在控制节点的项目根目录下操作。

下面，我们将通过一个实例，一步步编写一个用于安装和配置 HTTPD 服务的完整 Playbook。这个 Playbook 将完成以下任务：
1.  创建挂载 ISO 所需的目录。
2.  挂载系统安装光盘。
3.  配置 YUM 软件仓库。
4.  安装 HTTPD 软件包。
5.  启动并启用 HTTPD 服务。

以下是创建 Playbook 文件的步骤和核心概念：

![](img/b2f275caa9ad35897322c44e6534620d_2.png)

![](img/b2f275caa9ad35897322c44e6534620d_3.png)

我们创建一个名为 `httpd.yml` 的 Playbook 文件（扩展名 `.yml` 或 `.yaml` 均可）。

```yaml
---
- name: Install and enable HTTPD service
  hosts: node2.lab.example.com
  tasks:
```

*   `---`：YAML 文件的开头标记。
*   `- name: ...`：定义一个 Play。`name` 是对该 Play 用途的描述。
*   `hosts: ...`：指定该 Play 在哪些受管主机上运行。可以是单个主机、主机组或 `all`。
*   `tasks:`：下面将列出要执行的任务列表。

接下来，我们在 `tasks:` 下添加具体的任务。每个任务都是一个列表项，以 `- name:` 开头。

### 任务一：创建目录

![](img/b2f275caa9ad35897322c44e6534620d_5.png)

![](img/b2f275caa9ad35897322c44e6534620d_7.png)

第一个任务是创建用于挂载光盘的目录。

![](img/b2f275caa9ad35897322c44e6534620d_9.png)

![](img/b2f275caa9ad35897322c44e6534620d_11.png)

```yaml
    - name: Create directory for ISO mount
      file:
        path: /mnt/iso
        state: directory
```

![](img/b2f275caa9ad35897322c44e6534620d_13.png)

*   `file:`：调用的 Ansible 模块名称，用于管理文件和目录。
*   `path:` 和 `state:`：模块的参数。这里表示确保 `/mnt/iso` 目录存在。

![](img/b2f275caa9ad35897322c44e6534620d_15.png)

### 任务二：挂载光盘

![](img/b2f275caa9ad35897322c44e6534620d_17.png)

![](img/b2f275caa9ad35897322c44e6534620d_19.png)

第二个任务是将系统安装 ISO 文件挂载到刚创建的目录。

```yaml
    - name: Mount RHEL8 ISO
      mount:
        src: /dev/sr0
        path: /mnt/iso
        fstype: iso9660
        state: mounted
```

![](img/b2f275caa9ad35897322c44e6534620d_21.png)

*   `mount:`：用于管理文件系统挂载的模块。
*   `state: mounted`：确保设备被挂载，并在 `/etc/fstab` 中创建相应条目。

![](img/b2f275caa9ad35897322c44e6534620d_23.png)

![](img/b2f275caa9ad35897322c44e6534620d_25.png)

### 任务三：配置 YUM 仓库

![](img/b2f275caa9ad35897322c44e6534620d_27.png)

![](img/b2f275caa9ad35897322c44e6534620d_29.png)

![](img/b2f275caa9ad35897322c44e6534620d_31.png)

第三个任务是配置两个 YUM 软件仓库，指向我们挂载的光盘。

![](img/b2f275caa9ad35897322c44e6534620d_33.png)

```yaml
    - name: Configure BaseOS YUM repository
      yum_repository:
        name: BaseOS
        file: rhel8
        description: RHEL8 BaseOS
        baseurl: file:///mnt/iso/BaseOS
        enabled: yes
        gpgcheck: no

    - name: Configure AppStream YUM repository
      yum_repository:
        name: AppStream
        file: rhel8
        description: RHEL8 AppStream
        baseurl: file:///mnt/iso/AppStream
        enabled: yes
        gpgcheck: no
```

![](img/b2f275caa9ad35897322c44e6534620d_35.png)

![](img/b2f275caa9ad35897322c44e6534620d_37.png)

*   `yum_repository:`：管理 YUM 仓库的模块。
*   每个仓库需要独立定义 `name`、`baseurl` 等参数。

![](img/b2f275caa9ad35897322c44e6534620d_38.png)

![](img/b2f275caa9ad35897322c44e6534620d_40.png)

### 任务四：安装软件包

![](img/b2f275caa9ad35897322c44e6534620d_42.png)

![](img/b2f275caa9ad35897322c44e6534620d_44.png)

第四个任务是安装 HTTPD 软件包。

![](img/b2f275caa9ad35897322c44e6534620d_46.png)

```yaml
    - name: Install HTTPD package
      yum:
        name: httpd
        state: present
```

![](img/b2f275caa9ad35897322c44e6534620d_48.png)

*   `yum:`：用于管理 RPM 软件包的模块。
*   `state: present`：确保软件包已安装。

![](img/b2f275caa9ad35897322c44e6534620d_50.png)

![](img/b2f275caa9ad35897322c44e6534620d_52.png)

![](img/b2f275caa9ad35897322c44e6534620d_53.png)

### 任务五：启动并启用服务

![](img/b2f275caa9ad35897322c44e6534620d_54.png)

![](img/b2f275caa9ad35897322c44e6534620d_56.png)

![](img/b2f275caa9ad35897322c44e6534620d_57.png)

最后一个任务是启动 HTTPD 服务，并设置其开机自启。

![](img/b2f275caa9ad35897322c44e6534620d_59.png)

![](img/b2f275caa9ad35897322c44e6534620d_61.png)

![](img/b2f275caa9ad35897322c44e6534620d_63.png)

```yaml
    - name: Start and enable HTTPD service
      service:
        name: httpd
        state: started
        enabled: yes
```

![](img/b2f275caa9ad35897322c44e6534620d_64.png)

![](img/b2f275caa9ad35897322c44e6534620d_66.png)

*   `service:`：管理系统服务的模块。
*   `state: started`：确保服务处于运行状态。
*   `enabled: yes`：确保服务开机自动启动。

![](img/b2f275caa9ad35897322c44e6534620d_68.png)

![](img/b2f275caa9ad35897322c44e6534620d_69.png)

![](img/b2f275caa9ad35897322c44e6534620d_70.png)

**YAML 格式要点总结：**
*   缩进使用**两个空格**，不要使用 Tab 键。
*   列表项以 `-`（横杠加空格）开头。
*   键值对使用 `key: value` 形式，冒号后必须有一个空格。
*   为保持可读性，建议在不同任务之间使用空行分隔。

![](img/b2f275caa9ad35897322c44e6534620d_72.png)

![](img/b2f275caa9ad35897322c44e6534620d_74.png)

![](img/b2f275caa9ad35897322c44e6534620d_75.png)

![](img/b2f275caa9ad35897322c44e6534620d_76.png)

## 校验 Playbook 语法 ✅

![](img/b2f275caa9ad35897322c44e6534620d_78.png)

![](img/b2f275caa9ad35897322c44e6534620d_79.png)

![](img/b2f275caa9ad35897322c44e6534620d_81.png)

编写完 Playbook 后，在运行之前必须进行语法校验，这可以避免因格式错误导致的运行失败。

![](img/b2f275caa9ad35897322c44e6534620d_83.png)

我们使用 `ansible-playbook` 命令的 `--syntax-check` 选项来进行语法检查。

![](img/b2f275caa9ad35897322c44e6534620d_85.png)

![](img/b2f275caa9ad35897322c44e6534620d_86.png)

![](img/b2f275caa9ad35897322c44e6534620d_88.png)

```bash
ansible-playbook --syntax-check playbooks/httpd.yml
```

![](img/b2f275caa9ad35897322c44e6534620d_90.png)

![](img/b2f275caa9ad35897322c44e6534620d_92.png)

![](img/b2f275caa9ad35897322c44e6534620d_94.png)

如果语法正确，命令将不会有错误输出，可能只有一些警告（如重复的参数名）。如果语法有误，命令会明确指出错误发生在文件的第几行，方便我们定位和修改。**请注意，语法检查只验证 YAML 格式是否正确，不验证任务逻辑（例如软件包名是否存在）**。

![](img/b2f275caa9ad35897322c44e6534620d_95.png)

![](img/b2f275caa9ad35897322c44e6534620d_97.png)

![](img/b2f275caa9ad35897322c44e6534620d_99.png)

## 运行 Playbook ▶️

![](img/b2f275caa9ad35897322c44e6534620d_101.png)

![](img/b2f275caa9ad35897322c44e6534620d_102.png)

语法校验通过后，就可以运行 Playbook 了。使用以下命令：

![](img/b2f275caa9ad35897322c44e6534620d_104.png)

![](img/b2f275caa9ad35897322c44e6534620d_106.png)

![](img/b2f275caa9ad35897322c44e6534620d_108.png)

```bash
ansible-playbook playbooks/httpd.yml
```

![](img/b2f275caa9ad35897322c44e6534620d_109.png)

![](img/b2f275caa9ad35897322c44e6534620d_111.png)

运行后，Ansible 会输出执行摘要，显示每个任务的执行结果：
*   **OK**：任务成功执行，且系统状态未发生改变（幂等性体现）。
*   **CHANGED**：任务成功执行，并且改变了系统状态。
*   **FAILED**：任务执行失败。

![](img/b2f275caa9ad35897322c44e6534620d_112.png)

### 提高输出详细程度

![](img/b2f275caa9ad35897322c44e6534620d_114.png)

默认输出比较简洁。我们可以使用 `-v` 参数来获取更详细的执行信息，便于调试。

![](img/b2f275caa9ad35897322c44e6534620d_116.png)

```bash
ansible-playbook -v playbooks/httpd.yml
```
*   `-v`：显示详细结果。
*   `-vv`：显示更详细的结果，包括连接细节。
*   `-vvv`：显示调试信息。
*   `--check`：空运行模式，模拟执行并显示预期结果，但不会在实际主机上做任何更改。

## 总结 📚

![](img/b2f275caa9ad35897322c44e6534620d_118.png)

本节课中我们一起学习了 Ansible Playbook 的基础知识。我们了解了 Playbook 相对于临时命令的优势，并动手编写了一个完整的 Playbook，实现了从环境准备到服务部署的自动化流程。关键步骤包括：
1.  **编写**：遵循 YAML 语法，结构化地定义 Play、主机和任务。
2.  **校验**：使用 `ansible-playbook --syntax-check` 确保格式正确。
3.  **运行**：使用 `ansible-playbook` 命令执行，并可利用 `-v`、`--check` 等参数控制输出和行为。

![](img/b2f275caa9ad35897322c44e6534620d_120.png)

通过将离散的操作步骤整合到一个 Playbook 文件中，我们实现了任务的标准化和可重复执行，这是 Ansible 自动化的核心价值所在。