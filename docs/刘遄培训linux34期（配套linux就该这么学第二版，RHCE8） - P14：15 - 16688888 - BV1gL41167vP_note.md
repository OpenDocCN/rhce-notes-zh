# RHCE 8 备考培训：第14章：Ansible 基础与实战

![](img/8d32135ad6ac5bf93a8319104b95b5a4_1.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_3.png)

在本节课中，我们将学习红帽认证工程师（RHCE）考试中关于 Ansible 自动化运维的核心内容。课程将涵盖 Ansible 的安装、配置、主机清单管理、临时命令执行、Playbook 编写以及角色的创建与使用。我们将通过详细的步骤和示例，确保初学者能够理解并掌握这些关键概念。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_5.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_7.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_9.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_11.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_13.png)

---

![](img/8d32135ad6ac5bf93a8319104b95b5a4_15.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_17.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_19.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_21.png)

## 实验环境准备

![](img/8d32135ad6ac5bf93a8319104b95b5a4_23.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_25.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_27.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_29.png)

上一节我们介绍了课程概述，本节中我们来看看如何准备实验环境。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_31.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_33.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_35.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_37.png)

首先，您需要从指定的网站下载实验环境。该环境是一个虚拟机镜像，大小约为 10 GB。下载完成后，您将获得一个包含多个虚拟机的环境，用于模拟真实的考试场景。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_39.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_41.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_43.png)

以下是下载和启动实验环境的步骤：

![](img/8d32135ad6ac5bf93a8319104b95b5a4_45.png)

1.  访问学员页面，下载名为 `rhce-exam-environment` 的虚拟机文件。
2.  使用虚拟机软件（如 VMware 或 VirtualBox）导入该文件。
3.  启动虚拟机，并使用 root 用户和密码 `redhat` 登录。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_47.png)

**注意**：在考试中，所有环境均由考官预先配置好，考生无需进行此步骤。此操作仅用于模拟练习。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_49.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_51.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_53.png)

如果启动虚拟机时遇到错误（例如 CPU 兼容性问题），可以尝试以下解决方法：

![](img/8d32135ad6ac5bf93a8319104b95b5a4_55.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_57.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_59.png)

1.  编辑虚拟机设置，调整处理器核心数量以匹配您的主机配置。
2.  禁用“虚拟化 CPU 性能计数器”选项。
3.  重新启动虚拟机。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_61.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_63.png)

如果问题仍然存在，可以参考提供的文档《考场环境不能启动的解决方法》进行操作。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_65.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_67.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_69.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_71.png)

---

![](img/8d32135ad6ac5bf93a8319104b95b5a4_73.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_75.png)

## 考试环境与考题概览

成功启动实验环境后，您将看到一个包含六台虚拟机的控制界面。这些虚拟机包括一个控制节点（`bastion`）和五个受控节点。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_77.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_79.png)

以下是考试环境的关键信息：

*   **控制节点**：`bastion` (IP: `172.25.250.254`)
*   **受控节点**：五台虚拟机，IP 地址分别为 `172.25.250.9` 至 `172.25.250.13`。
*   **用户与权限**：所有操作必须使用 `greg` 用户身份在控制节点上执行，并将相关文件保存在 `/home/greg/ansible` 目录中。root 密码为 `redhat`（模拟环境）或考题指定的密码（实际考试）。
*   **网络与安全**：所有主机的网络配置（IP、DNS）已预先设置，请勿修改。SSH 密钥认证已配置，主机间可免密登录。防火墙默认未启用，SELinux 处于强制模式。
*   **软件仓库**：考题中会提供软件仓库的 URL，用于配置 YUM 源。

在考试中，您将通过点击屏幕上的红帽图标来查看考题。考题内容与官方考试完全一致，是备考的绝佳材料。

---

![](img/8d32135ad6ac5bf93a8319104b95b5a4_81.png)

## 题目一：安装与配置 Ansible

![](img/8d32135ad6ac5bf93a8319104b95b5a4_83.png)

上一节我们熟悉了环境，本节中我们来看看第一道考题：安装与配置 Ansible。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_85.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_87.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_89.png)

**任务概述**：在控制节点上安装 Ansible 软件，并创建符合要求的主机清单和配置文件。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_91.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_93.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_95.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_97.png)

以下是具体步骤：

![](img/8d32135ad6ac5bf93a8319104b95b5a4_99.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_101.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_103.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_105.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_107.png)

1.  **安装 Ansible**：
    使用 root 用户登录控制节点 (`172.25.250.254`)，执行以下命令安装 Ansible：
    ```bash
    dnf install ansible
    ```
    安装完成后，退出 root 用户。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_109.png)

2.  **使用 `greg` 用户操作**：
    切换到 `greg` 用户，并进入其家目录下的 `ansible` 目录：
    ```bash
    su - greg
    cd /home/greg/ansible
    ```

3.  **创建主机清单文件**：
    创建文件 `/home/greg/ansible/inventory`，并添加以下内容，定义主机组：
    ```ini
    172.25.250.9
    172.25.250.10
    172.25.250.11
    172.25.250.12
    172.25.250.13

    [dev]
    172.25.250.9

    [test]
    172.25.250.10

    [prod]
    172.25.250.11
    172.25.250.12

    [balancer]
    172.25.250.13

    [webservers:children]
    prod
    ```
    此清单将主机进行了分组，并定义了 `webservers` 组包含 `prod` 组。

4.  **创建 Ansible 配置文件**：
    将系统默认的 Ansible 配置文件复制到当前目录，并命名为 `ansible.cfg`：
    ```bash
    cp /etc/ansible/ansible.cfg /home/greg/ansible/
    ```
    编辑此文件，确保以下关键参数设置正确：
    ```ini
    inventory      = /home/greg/ansible/inventory
    roles_path    = /home/greg/ansible/roles
    host_key_checking = False
    remote_user   = root
    ```
    其中：
    *   `inventory`：指向刚创建的主机清单文件。
    *   `roles_path`：指定角色查找路径。
    *   `host_key_checking = False`：禁用 SSH 主机密钥检查，避免首次连接时手动确认。
    *   `remote_user = root`：指定连接受控节点时使用的用户。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_111.png)

5.  **验证配置**：
    执行以下命令测试与所有主机的连通性以及配置是否正确：
    ```bash
    ansible all -m ping
    ansible --version | grep "config file"
    ```
    如果 `ping` 模块返回成功，且配置文件路径显示为 `/home/greg/ansible/ansible.cfg`，则配置正确。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_113.png)

---

![](img/8d32135ad6ac5bf93a8319104b95b5a4_115.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_117.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_119.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_121.png)

## 题目二：使用临时命令配置软件仓库

![](img/8d32135ad6ac5bf93a8319104b95b5a4_123.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_125.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_127.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_129.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_131.png)

上一节我们完成了 Ansible 的基础配置，本节中我们来看看如何使用 Ansible 临时命令执行任务。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_133.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_135.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_137.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_139.png)

**任务概述**：创建一个 Shell 脚本，该脚本使用 Ansible 临时命令在所有受控节点上配置两个 YUM 软件仓库。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_141.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_143.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_145.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_147.png)

以下是具体步骤：

![](img/8d32135ad6ac5bf93a8319104b95b5a4_149.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_151.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_153.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_155.png)

1.  **查找所需模块**：
    首先，我们需要找到用于管理 YUM 仓库的模块。使用以下命令搜索：
    ```bash
    ansible-doc -l | grep yum_repository
    ```
    确认模块名为 `yum_repository`。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_157.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_159.png)

2.  **查看模块用法**：
    获取该模块的详细用法和参数示例：
    ```bash
    ansible-doc yum_repository
    ```
    在输出的 `EXAMPLES` 部分，可以找到配置仓库的示例。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_161.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_163.png)

3.  **编写临时命令**：
    根据考题要求，需要配置两个仓库。命令结构如下：
    ```bash
    ansible all -m yum_repository -a "name=EX294 description='EX294 repository' baseurl=http://content.example.com/rhel8.0/x86_64/dvd/AppStream gpgcheck=yes gpgkey=http://content.example.com/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release enabled=yes"
    ```
    请将 `baseurl` 和 `gpgkey` 的 URL 替换为考题中提供的实际地址。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_165.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_167.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_169.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_171.png)

4.  **创建 Shell 脚本**：
    在 `/home/greg/ansible` 目录下，创建考题要求的脚本文件（例如 `setup_repos.sh`）。
    ```bash
    #!/bin/bash
    # 配置第一个软件仓库
    ansible all -m yum_repository -a "name=Repo1 description='Repository 1' baseurl=URL1 gpgcheck=yes gpgkey=KEY1 enabled=yes"
    # 配置第二个软件仓库
    ansible all -m yum_repository -a "name=Repo2 description='Repository 2' baseurl=URL2 gpgcheck=yes gpgkey=KEY2 enabled=yes"
    ```
    将 `URL1`、`KEY1`、`URL2`、`KEY2` 替换为考题中的实际值。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_173.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_175.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_177.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_179.png)

5.  **赋予执行权限并运行**：
    ```bash
    chmod +x setup_repos.sh
    ./setup_repos.sh
    ```

![](img/8d32135ad6ac5bf93a8319104b95b5a4_181.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_183.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_185.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_187.png)

6.  **验证**：
    登录任意受控节点，检查 `/etc/yum.repos.d/` 目录下是否生成了对应的 `.repo` 文件，并确认内容正确。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_189.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_191.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_193.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_195.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_197.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_199.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_201.png)

---

![](img/8d32135ad6ac5bf93a8319104b95b5a4_203.png)

## 题目三：编写 Playbook 安装软件包

![](img/8d32135ad6ac5bf93a8319104b95b5a4_205.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_207.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_209.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_211.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_213.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_215.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_217.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_219.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_221.png)

上一节我们使用了临时命令，本节中我们来看看如何编写更结构化的任务——Playbook。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_223.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_225.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_227.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_229.png)

**任务概述**：创建一个 Playbook，在指定的主机组上安装软件包和软件包组。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_231.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_233.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_235.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_237.png)

以下是具体步骤：

![](img/8d32135ad6ac5bf93a8319104b95b5a4_239.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_241.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_243.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_245.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_247.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_249.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_251.png)

1.  **创建 Playbook 文件**：
    在 `/home/greg/ansible` 目录下，创建 YAML 文件（例如 `install_packages.yml`）。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_253.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_255.png)

2.  **编写 Playbook 内容**：
    Playbook 使用 YAML 格式，必须注意严格的缩进（通常为两个空格）。内容如下：
    ```yaml
    ---
    - name: Install packages on dev, test, and prod
      hosts: dev,test,prod
      tasks:
        - name: Install PHP
          yum:
            name: php
            state: latest
        - name: Install MariaDB
          yum:
            name: mariadb
            state: latest

    - name: Install package group and update on dev
      hosts: dev
      tasks:
        - name: Install development tools package group
          yum:
            name: "@Development Tools"
            state: latest
        - name: Upgrade all packages to latest
          yum:
            name: "*"
            state: latest
    ```
    **代码解释**：
    *   `---`：YAML 文件开始标记。
    *   `- name`：定义一个 Play 或 Task 的名称。
    *   `hosts`：指定该 Play 在哪些主机组上执行。
    *   `tasks`：定义要执行的任务列表。
    *   `yum`：调用 yum 模块来管理软件包。
        *   `name`：指定软件包或软件包组名称（组名前加 `@`）。
        *   `state: latest`：确保安装最新版本。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_257.png)

3.  **运行 Playbook**：
    ```bash
    ansible-playbook install_packages.yml
    ```

![](img/8d32135ad6ac5bf93a8319104b95b5a4_259.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_261.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_263.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_265.png)

4.  **验证**：
    在 `dev` 组的主机上运行 `yum list installed | grep -E ‘php|mariadb|gcc’` 等命令，确认软件包已安装。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_267.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_269.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_271.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_273.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_275.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_277.png)

---

![](img/8d32135ad6ac5bf93a8319104b95b5a4_279.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_281.png)

## 题目四：使用系统角色（System Roles）

![](img/8d32135ad6ac5bf93a8319104b95b5a4_283.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_285.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_287.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_289.png)

上一节我们编写了基础的 Playbook，本节中我们来看看如何利用红帽系统预定义的角色来简化工作。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_291.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_293.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_295.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_297.png)

**任务概述**：使用红帽系统角色 `timesync` 来配置所有受控节点的时间同步。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_299.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_301.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_303.png)

以下是具体步骤：

![](img/8d32135ad6ac5bf93a8319104b95b5a4_305.png)

1.  **安装系统角色包**：
    在控制节点上，使用 root 用户安装包含系统角色的软件包：
    ```bash
    dnf install rhel-system-roles
    ```

2.  **查找角色模板**：
    系统角色安装后，其示例 Playbook 通常位于 `/usr/share/doc/rhel-system-roles/` 目录下。找到 `timesync` 角色的示例：
    ```bash
    find /usr/share/doc/rhel-system-roles -name “*timesync*” -type f
    ```
    通常会找到一个示例 Playbook 文件。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_307.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_309.png)

3.  **复制并修改 Playbook**：
    将找到的示例文件复制到 `/home/greg/ansible` 目录，并命名为考题要求的名称（如 `timesync.yml`）。
    ```bash
    cp /usr/share/doc/rhel-system-roles/timesync/example-timesync-playbook.yml /home/greg/ansible/timesync.yml
    ```
    编辑此文件，主要修改 `hosts` 为 `all`，并根据考题设置正确的时间服务器（NTP server）地址：
    ```yaml
    ---
    - hosts: all
      vars:
        timesync_ntp_servers:
          - hostname: time-server.example.com # 改为考题提供的地址
            iburst: yes
      roles:
        - rhel-system-roles.timesync
    ```

![](img/8d32135ad6ac5bf93a8319104b95b5a4_311.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_313.png)

4.  **配置角色路径**：
    确保 `ansible.cfg` 文件中的 `roles_path` 包含了系统角色的安装路径，例如：
    ```ini
    roles_path = /home/greg/ansible/roles:/usr/share/ansible/roles:/usr/share/ansible/roles
    ```
    然后运行以下命令，确认角色可被找到：
    ```bash
    ansible-galaxy list
    ```

![](img/8d32135ad6ac5bf93a8319104b95b5a4_315.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_317.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_319.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_321.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_323.png)

5.  **运行 Playbook**：
    ```bash
    ansible-playbook timesync.yml
    ```

6.  **验证**：
    在受控节点上检查 `/etc/chrony.conf` 文件，确认已配置了指定的时间服务器。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_325.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_327.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_329.png)

---

![](img/8d32135ad6ac5bf93a8319104b95b5a4_331.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_333.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_335.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_337.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_339.png)

## 题目五：从 Ansible Galaxy 获取角色

![](img/8d32135ad6ac5bf93a8319104b95b5a4_341.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_343.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_345.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_347.png)

上一节我们使用了系统自带的角色，本节中我们来看看如何从外部社区（Ansible Galaxy）获取并使用角色。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_349.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_351.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_353.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_355.png)

**任务概述**：创建一个需求文件（requirements.yml），从指定的 URL 下载并安装两个社区角色。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_357.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_359.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_361.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_363.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_365.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_367.png)

以下是具体步骤：

![](img/8d32135ad6ac5bf93a8319104b95b5a4_369.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_371.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_373.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_375.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_377.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_379.png)

1.  **创建需求文件**：
    在 `/home/greg/ansible` 目录下，创建文件 `requirements.yml`。
    ```yaml
    ---
    - src: http://content.example.com/roles/role1.tar.gz
      name: my_apache_role
    - src: http://content.example.com/roles/role2.tar.gz
      name: my_phpinfo_role
    ```
    将 `src` 的 URL 替换为考题中提供的实际地址。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_381.png)

2.  **安装角色**：
    使用 `ansible-galaxy` 命令根据需求文件安装角色到默认角色目录（由 `ansible.cfg` 中的 `roles_path` 指定）：
    ```bash
    ansible-galaxy install -r requirements.yml
    ```

![](img/8d32135ad6ac5bf93a8319104b95b5a4_383.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_385.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_387.png)

3.  **验证安装**：
    再次列出已安装的角色，确认新角色出现在列表中：
    ```bash
    ansible-galaxy list
    ```

![](img/8d32135ad6ac5bf93a8319104b95b5a4_389.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_391.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_393.png)

---

![](img/8d32135ad6ac5bf93a8319104b95b5a4_395.png)

## 题目六：创建与使用自定义角色

![](img/8d32135ad6ac5bf93a8319104b95b5a4_397.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_399.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_401.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_403.png)

上一节我们从外部获取了角色，本节中我们来看看如何从头创建自己的 Ansible 角色。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_405.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_407.png)

**任务概述**：创建一个名为 `apache` 的自定义角色，实现安装 Apache、配置防火墙、部署网站文件等功能。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_409.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_411.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_413.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_415.png)

以下是具体步骤：

![](img/8d32135ad6ac5bf93a8319104b95b5a4_417.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_419.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_421.png)

1.  **创建角色骨架**：
    使用 `ansible-galaxy` 命令初始化一个角色结构：
    ```bash
    ansible-galaxy init /home/greg/ansible/roles/apache
    ```
    这会在 `roles/apache` 目录下创建标准的角色子目录（如 `tasks`, `handlers`, `files`, `templates`, `vars`, `defaults`, `meta`）。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_423.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_425.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_427.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_429.png)

2.  **编辑主任务文件**：
    编辑 `roles/apache/tasks/main.yml`，定义角色的核心任务：
    ```yaml
    ---
    - name: Install Apache web server
      yum:
        name: httpd
        state: present

    - name: Start and enable Apache service
      service:
        name: httpd
        state: started
        enabled: yes

    - name: Allow HTTP traffic in firewall
      firewalld:
        service: http
        permanent: yes
        immediate: yes
        state: enabled

    - name: Deploy website index page
      template:
        src: index.html.j2
        dest: /var/www/html/index.html
    ```

![](img/8d32135ad6ac5bf93a8319104b95b5a4_431.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_433.png)

3.  **创建 Jinja2 模板**：
    在 `roles/apache/templates/` 目录下创建 `index.html.j2` 文件。这是一个 Jinja2 模板文件，其中可以包含变量。
    ```html
    <!DOCTYPE html>
    <html>
    <head><title>Welcome</title></head>
    <body>
    <h1>Welcome to {{ ansible_fqdn }}</h1>
    <p>This server’s IP address is {{ ansible_default_ipv4.address }}</p>
    </body>
    </html>
    ```
    **公式解释**：
    *   `{{ ansible_fqdn }}`：Ansible 事实变量，代表主机的完全限定域名。
    *   `{{ ansible_default_ipv4.address }}`：Ansible 事实变量，代表主机的默认 IPv4 地址。
    *   这些变量会在 Playbook 执行时，自动从各受控节点收集并替换。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_435.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_437.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_439.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_441.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_443.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_445.png)

4.  **如何使用变量**：
    在 Playbook 或角色中，可以通过 `setup` 模块收集所有事实变量，并用 `filter` 过滤查找特定变量名：
    ```bash
    ansible localhost -m setup -a ‘filter=ansible_fqdn’
    ```

![](img/8d32135ad6ac5bf93a8319104b95b5a4_447.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_449.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_451.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_453.png)

---

![](img/8d32135ad6ac5bf93a8319104b95b5a4_455.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_457.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_459.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_461.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_463.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_465.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_467.png)

## 题目七：在 Playbook 中调用角色

![](img/8d32135ad6ac5bf93a8319104b95b5a4_469.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_471.png)

上一节我们创建了一个自定义角色，本节中我们来看看如何在 Playbook 中组合调用多个角色来完成复杂任务。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_473.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_475.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_477.png)

**任务概述**：编写一个 Playbook，在 `balancer` 主机组上调用负载均衡角色，在 `webservers` 主机组上调用 PHP 信息角色，并在所有主机上调用我们自定义的 Apache 角色。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_479.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_481.png)

以下是具体步骤：

![](img/8d32135ad6ac5bf93a8319104b95b5a4_483.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_485.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_487.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_489.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_491.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_493.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_495.png)

1.  **创建 Playbook 文件**：
    在 `/home/greg/ansible` 目录下，创建文件 `site.yml`。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_497.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_499.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_501.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_503.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_505.png)

2.  **编写 Playbook 内容**：
    ```yaml
    ---
    - name: Configure load balancer
      hosts: balancer
      roles:
        - role: load_balancer_role # 使用之前从Galaxy安装或系统存在的角色名

    - name: Configure PHP info on web servers
      hosts: webservers
      roles:
        - role: my_phpinfo_role # 使用题目五安装的角色名

    - name: Deploy Apache web server on all nodes
      hosts: all
      roles:
        - role: apache # 使用题目六创建的自定义角色名
    ```

![](img/8d32135ad6ac5bf93a8319104b95b5a4_507.png)

3.  **运行 Playbook**：
    ```bash
    ansible-playbook site.yml
    ```

![](img/8d32135ad6ac5bf93a8319104b95b5a4_509.png)

4.  **验证**：
    *   使用浏览器访问 `balancer` 主机的 IP 地址，应能看到负载均衡效果（轮流显示不同后端服务器的内容）。
    *   访问 `webservers` 组主机的 `/phpinfo.php` 页面（如果角色部署了此文件），应能看到 PHP 信息页。
    *   访问任何主机的根目录，应能看到由 Apache 角色部署的、包含主机名和 IP 的欢迎页面。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_511.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_513.png)

---

![](img/8d32135ad6ac5bf93a8319104b95b5a4_515.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_517.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_519.png)

## 题目八：使用逻辑卷（LVM）模块与错误处理

![](img/8d32135ad6ac5bf93a8319104b95b5a4_521.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_523.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_525.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_527.png)

上一节我们组合使用了多个角色，本节中我们来看一道综合题，涉及 LVM 管理和 Playbook 的错误处理控制。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_529.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_531.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_533.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_535.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_537.png)

**任务概述**：编写一个 Playbook，尝试在所有节点上创建一个 1500MB 的逻辑卷。如果卷组空间不足，则改为创建 800MB 的逻辑卷。如果卷组不存在，则输出错误信息而不创建。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_539.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_541.png)

以下是具体步骤：

![](img/8d32135ad6ac5bf93a8319104b95b5a4_543.png)

1.  **创建 Playbook 文件**：
    在 `/home/greg/ansible` 目录下，创建文件 `lvm.yml`。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_545.png)

2.  **编写 Playbook 内容**：
    这道题需要用到 `block`, `rescue`, 和 `when` 等高级语法来进行错误处理。
    ```yaml
    ---
    - name: Manage Logical Volume
      hosts: all
      tasks:
        - block:
            - name: Create 1500MB Logical Volume
              lvol:
                vg: research
                lv: data
                size: 1500

            - name: Format the Logical Volume with ext4
              filesystem:
                fstype: ext4
                dev: /dev/research/data

          rescue:
            - name: Debug message for size fallback
              debug:
                msg: “Volume group research exists but does not have enough space. Creating 800MB volume.”

            - name: Create 800MB Logical Volume as fallback
              lvol:
                vg: research
                lv: data
                size: 800
              when: “‘research’ in ansible_lvm.vgs”

            - name: Debug message for missing VG
              debug:
                msg: “Volume group research does not exist.”
              when: “‘research’ not in ansible_lvm.vgs”
    ```
    **代码解释**：
    *   `block`：将多个任务定义为一个块。
    *   `rescue`：如果 `block` 中的任何任务失败，则执行 `rescue` 块中的任务。
    *   `when`：条件判断。`ansible_lvm.vgs` 是一个魔法变量（magic variable），包含了主机的 LVM 卷组信息。我们通过判断卷组名是否在其中，来决定执行哪条救援路径。
    *   `lvol`：用于管理 LVM 逻辑卷的模块。
    *   `filesystem`：用于在设备上创建文件系统的模块。

3.  **运行 Playbook**：
    ```bash
    ansible-playbook lvm.yml
    ```
    根据各受控节点上 `research` 卷组的实际情况，Playbook 会输出不同的结果和信息。

---

## 总结

在本节课中，我们一起学习了 RHCE 8 考试中 Ansible 部分的核心内容。我们从实验环境准备开始，逐步完成了 Ansible 的安装配置、主机清单管理、临时命令的使用、Playbook 的编写、以及三种角色（系统角色、Galaxy 角色、自定义角色）的获取、创建和调用。最后，我们还探讨了如何使用高级控制结构处理任务中的错误。

![](img/8d32135ad6ac5bf93a8319104b95b5a4_547.png)

![](img/8d32135ad6ac5bf93a8319104b95b5a4_549.png)

通过以上八个题目的实战演练，您应该对 Ansible 自动化运维有了扎实的理解，并掌握了应对 RHCE 考试相关考题的方法和技巧。请务必在实验环境中反复练习，确保熟练操作。下节课我们将继续讲解剩余的考题。