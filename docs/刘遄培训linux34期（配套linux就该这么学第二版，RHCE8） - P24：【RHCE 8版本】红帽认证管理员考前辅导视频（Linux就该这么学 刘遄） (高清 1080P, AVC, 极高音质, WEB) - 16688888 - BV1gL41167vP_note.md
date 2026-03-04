# RHCE 8 考前辅导：RHCE 8 考试内容、方法与技巧详解 🎯

## 概述

![](img/c0a6275e0abb35205ac48b494124d511_1.png)

在本节课中，我们将系统性地学习红帽认证工程师（RHCE）8版本的考试内容、考试方法以及应试技巧。课程将涵盖考试结构、环境配置、核心知识点串讲以及实战操作演示，旨在帮助初学者以最有效的方式理解和掌握考试要点，顺利通过认证。

![](img/c0a6275e0abb35205ac48b494124d511_3.png)

---

![](img/c0a6275e0abb35205ac48b494124d511_5.png)

![](img/c0a6275e0abb35205ac48b494124d511_7.png)

## RHCE 8 认证结构解析

RHCE认证由两部分组成：
*   **上午考试（RHCSA）**：时长2.5小时，考核系统基础管理能力。
*   **下午考试（RHCE）**：时长3.5小时，考核自动化运维与服务管理能力。

考生必须同时通过这两门考试，才能获得最终的RHCE认证。这类似于教育体系，拥有高中毕业证意味着你已具备初中毕业的资格。因此，RHCE认证的含金量更高，它建立在扎实的RHCSA基础之上。

---

## RHCE 8 与旧版本的核心差异

上一节我们介绍了RHCE认证的整体结构，本节中我们来看看RHCE 8版本与之前版本（如RHEL 5/6/7）的主要区别。

**传统RHCE（RHEL 5/6/7）**：
*   **考核广度**：考核对多个常用服务的管理能力，例如：
    *   网站服务（Apache/Nginx）
    *   文件共享服务（NFS/Samba）
    *   域名服务（DNS）
    *   动态主机配置服务（DHCP）
    *   邮件服务（Postfix）
*   **核心要求**：需要对每个服务的配置、管理和排错有全面的理解和实践能力。

**RHCE 8**：
*   **考核深度**：**核心考核点聚焦于Ansible自动化工具**。
*   **考核方式**：使用Ansible将RHCSA考核的系统管理任务（如用户管理、软件包安装、服务配置等）重新自动化执行一遍。
*   **优缺点分析**：
    *   **优点（含金量提升）**：考核难度显著提高，达到了RHCA（红帽认证架构师）部分内容的水平。
    *   **挑战**：考生需要在Ansible这一“点”上进行深入学习和备考。

简单来说，RHCE 8是将原先RHCA认证中的`DO407`（Ansible自动化）课程内容下放到了RHCE级别进行考核。

![](img/c0a6275e0abb35205ac48b494124d511_9.png)

![](img/c0a6275e0abb35205ac48b494124d511_11.png)

对于考生而言，RHCE 8的备考策略发生了变化：
*   **传统版本**：更侧重于理解服务原理和排错能力。
*   **RHCE 8**：更侧重于记忆配置文件、编写Playbook和掌握Ansible模块的使用，非常适合通过“刷题”和记忆来备考。

![](img/c0a6275e0abb35205ac48b494124d511_13.png)

![](img/c0a6275e0abb35205ac48b494124d511_15.png)

![](img/c0a6275e0abb35205ac48b494124d511_17.png)

![](img/c0a6275e0abb35205ac48b494124d511_19.png)

---

![](img/c0a6275e0abb35205ac48b494124d511_21.png)

![](img/c0a6275e0abb35205ac48b494124d511_23.png)

## 考试环境与重要规则详解

![](img/c0a6275e0abb35205ac48b494124d511_25.png)

![](img/c0a6275e0abb35205ac48b494124d511_27.png)

在深入Ansible技术细节之前，我们必须彻底理解考试环境设置和那些至关重要的考试规则，这是成功的第一步。

### 考试环境拓扑

![](img/c0a6275e0abb35205ac48b494124d511_29.png)

![](img/c0a6275e0abb35205ac48b494124d511_31.png)

![](img/c0a6275e0abb35205ac48b494124d511_33.png)

考试时，您面前只有一台物理机，其中运行着**6台虚拟机**：
1.  **控制节点 (Bastion)**：`bastion.lab.example.com` (IP: `172.25.250.254`)
    *   **重要**：**所有Ansible操作都必须在此节点上进行**，切勿在其他虚拟机上进行答题操作。
2.  **受管节点 (5台)**：
    *   `workstation.lab.example.com` (IP: `172.25.250.9`)
    *   `servera.lab.example.com` (IP: `172.25.250.10`)
    *   `serverb.lab.example.com` (IP: `172.25.250.11`)
    *   `serverc.lab.example.com` (IP: `172.25.250.12`)
    *   `serverd.lab.example.com` (IP: `172.25.250.13`)

![](img/c0a6275e0abb35205ac48b494124d511_35.png)

**关键理解**：`workstation`与其他`servera/b/c/d`在考试中**没有任何区别**，都是普通的受管节点，不要被名称迷惑。

![](img/c0a6275e0abb35205ac48b494124d511_37.png)

### 必须遵守的核心规则

![](img/c0a6275e0abb35205ac48b494124d511_39.png)

以下是考试中必须严格遵守的规则，任何违反都可能导致丢分：

![](img/c0a6275e0abb35205ac48b494124d511_41.png)

![](img/c0a6275e0abb35205ac48b494124d511_43.png)

![](img/c0a6275e0abb35205ac48b494124d511_45.png)

1.  **使用指定用户和目录**：
    *   所有Ansible相关操作（编写Playbook、执行命令）**必须**使用`greg`用户。
    *   所有创建的Playbook、脚本、配置文件**必须**存放在`/home/greg/ansible`目录下。
    *   **判分脚本只会检查该目录**，文件放在其他位置即使正确也无分。

![](img/c0a6275e0abb35205ac48b494124d511_47.png)

![](img/c0a6275e0abb35205ac48b494124d511_49.png)

![](img/c0a6275e0abb35205ac48b494124d511_51.png)

![](img/c0a6275e0abb35205ac48b494124d511_53.png)

![](img/c0a6275e0abb35205ac48b494124d511_55.png)

![](img/c0a6275e0abb35205ac48b494124d511_57.png)

2.  **不要修改预设配置**：
    *   所有虚拟机的IP地址、主机名、DNS、YUM仓库均已配置好，**请勿更改**。
    *   所有系统的root密码均为`redhat`（考试时为`flectrag`），**请勿更改**。题目中需要密码时均使用此密码。
    *   主机之间已配置SSH密钥免密登录，**请勿修改**。

![](img/c0a6275e0abb35205ac48b494124d511_59.png)

![](img/c0a6275e0abb35205ac48b494124d511_61.png)

![](img/c0a6275e0abb35205ac48b494124d511_63.png)

![](img/c0a6275e0abb35205ac48b494124d511_65.png)

![](img/c0a6275e0abb35205ac48b494124d511_67.png)

3.  **保留现有内容**：
    *   系统可能已存在一些配置文件（如Ansible清单），修改时请**增量添加**，不要删除原有内容。

![](img/c0a6275e0abb35205ac48b494124d511_69.png)

![](img/c0a6275e0abb35205ac48b494124d511_71.png)

![](img/c0a6275e0abb35205ac48b494124d511_73.png)

![](img/c0a6275e0abb35205ac48b494124d511_75.png)

4.  **服务简化**：
    *   考试中**不考核防火墙**和**SELinux**的配置，两者默认已禁用或处于宽容模式，简化了操作。

![](img/c0a6275e0abb35205ac48b494124d511_77.png)

---

## 实验一：安装与配置Ansible

![](img/c0a6275e0abb35205ac48b494124d511_79.png)

![](img/c0a6275e0abb35205ac48b494124d511_81.png)

掌握了考试环境规则后，我们开始进行第一个实验操作，安装和配置Ansible控制端。

![](img/c0a6275e0abb35205ac48b494124d511_83.png)

![](img/c0a6275e0abb35205ac48b494124d511_85.png)

![](img/c0a6275e0abb35205ac48b494124d511_87.png)

![](img/c0a6275e0abb35205ac48b494124d511_89.png)

![](img/c0a6275e0abb35205ac48b494124d511_91.png)

**实验目标**：在控制节点（Bastion）上安装Ansible，并创建符合要求的主机清单和配置文件。

![](img/c0a6275e0abb35205ac48b494124d511_93.png)

![](img/c0a6275e0abb35205ac48b494124d511_95.png)

以下是操作步骤：

![](img/c0a6275e0abb35205ac48b494124d511_97.png)

![](img/c0a6275e0abb35205ac48b494124d511_99.png)

1.  **安装Ansible软件包**：
    ```bash
    # 切换到root用户安装软件
    sudo yum install -y ansible
    ```

![](img/c0a6275e0abb35205ac48b494124d511_101.png)

![](img/c0a6275e0abb35205ac48b494124d511_103.png)

![](img/c0a6275e0abb35205ac48b494124d511_105.png)

![](img/c0a6275e0abb35205ac48b494124d511_107.png)

![](img/c0a6275e0abb35205ac48b494124d511_109.png)

![](img/c0a6275e0abb35205ac48b494124d511_111.png)

![](img/c0a6275e0abb35205ac48b494124d511_113.png)

![](img/c0a6275e0abb35205ac48b494124d511_115.png)

2.  **创建`greg`用户并设置密码**（模拟环境需要，考试时已存在）：
    ```bash
    sudo useradd greg
    echo "redhat" | sudo passwd --stdin greg
    ```

![](img/c0a6275e0abb35205ac48b494124d511_117.png)

3.  **切换到`greg`用户并进入工作目录**：
    ```bash
    su - greg
    cd /home/greg/ansible
    ```

![](img/c0a6275e0abb35205ac48b494124d511_119.png)

![](img/c0a6275e0abb35205ac48b494124d511_121.png)

![](img/c0a6275e0abb35205ac48b494124d511_123.png)

![](img/c0a6275e0abb35205ac48b494124d511_125.png)

4.  **创建并配置主机清单文件 (`inventory`)**：
    ```bash
    vim /home/greg/ansible/inventory
    ```
    *文件内容如下，定义了不同的主机组：*
    ```ini
    [dev]
    172.25.250.9

    [test]
    172.25.250.10

    [prod]
    172.25.250.11
    172.25.250.12

    [balancers]
    172.25.250.13

    [webservers:children]
    prod
    balancers
    ```

![](img/c0a6275e0abb35205ac48b494124d511_127.png)

![](img/c0a6275e0abb35205ac48b494124d511_129.png)

![](img/c0a6275e0abb35205ac48b494124d511_131.png)

![](img/c0a6275e0abb35205ac48b494124d511_133.png)

![](img/c0a6275e0abb35205ac48b494124d511_135.png)

![](img/c0a6275e0abb35205ac48b494124d511_137.png)

![](img/c0a6275e0abb35205ac48b494124d511_139.png)

5.  **创建并配置Ansible主配置文件 (`ansible.cfg`)**：
    ```bash
    cp /etc/ansible/ansible.cfg /home/greg/ansible/
    vim /home/greg/ansible/ansible.cfg
    ```
    *修改以下关键参数：*
    ```ini
    inventory      = /home/greg/ansible/inventory  # 指定清单文件路径
    roles_path    = /home/greg/ansible/roles       # 指定角色存放路径
    host_key_checking = False                      # 禁用SSH主机密钥检查
    remote_user = root                             # 设置默认远程用户
    [all:vars]
    ansible_user=root
    ansible_password=redhat                        # 设置默认密码（考试时为flectrag）
    ```

![](img/c0a6275e0abb35205ac48b494124d511_141.png)

![](img/c0a6275e0abb35205ac48b494124d511_143.png)

![](img/c0a6275e0abb35205ac48b494124d511_145.png)

![](img/c0a6275e0abb35205ac48b494124d511_147.png)

6.  **验证配置**：
    ```bash
    cd /home/greg/ansible
    ansible all -m ping
    ```
    如果所有主机返回`pong`，则表示Ansible配置成功，控制节点可以管理所有受管节点。

![](img/c0a6275e0abb35205ac48b494124d511_149.png)

![](img/c0a6275e0abb35205ac48b494124d511_151.png)

---

![](img/c0a6275e0abb35205ac48b494124d511_153.png)

![](img/c0a6275e0abb35205ac48b494124d511_155.png)

## 实验二：使用临时命令创建YUM仓库

![](img/c0a6275e0abb35205ac48b494124d511_157.png)

![](img/c0a6275e0abb35205ac48b494124d511_159.png)

上一节我们完成了Ansible的基础配置，本节中我们来看看如何使用Ansible的临时命令执行简单任务。

![](img/c0a6275e0abb35205ac48b494124d511_161.png)

![](img/c0a6275e0abb35205ac48b494124d511_163.png)

![](img/c0a6275e0abb35205ac48b494124d511_165.png)

![](img/c0a6275e0abb35205ac48b494124d511_167.png)

**实验目标**：编写一个脚本，使用Ansible临时命令在所有受管节点上创建两个特定的YUM仓库。

![](img/c0a6275e0abb35205ac48b494124d511_169.png)

![](img/c0a6275e0abb35205ac48b494124d511_171.png)

![](img/c0a6275e0abb35205ac48b494124d511_173.png)

以下是操作步骤：

![](img/c0a6275e0abb35205ac48b494124d511_175.png)

![](img/c0a6275e0abb35205ac48b494124d511_177.png)

![](img/c0a6275e0abb35205ac48b494124d511_179.png)

1.  **创建脚本文件**：
    ```bash
    vim /home/greg/ansible/adhoc.sh
    ```

![](img/c0a6275e0abb35205ac48b494124d511_181.png)

![](img/c0a6275e0abb35205ac48b494124d511_183.png)

![](img/c0a6275e0abb35205ac48b494124d511_185.png)

![](img/c0a6275e0abb35205ac48b494124d511_187.png)

2.  **编写脚本内容**：
    ```bash
    #!/bin/bash
    # 创建第一个YUM仓库
    ansible all -m yum_repository \
    -a "name=EX294_BASE description='EX294 base software' baseurl=http://content.example.com/rhel8.0/x86_64/dvd/BaseOS gpgcheck=yes gpgkey=http://content.example.com/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release enabled=yes"

    # 创建第二个YUM仓库
    ansible all -m yum_repository \
    -a "name=EX294_STREAM description='EX294 stream software' baseurl=http://content.example.com/rhel8.0/x86_64/dvd/AppStream gpgcheck=yes gpgkey=http://content.example.com/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release enabled=yes"
    ```

![](img/c0a6275e0abb35205ac48b494124d511_189.png)

![](img/c0a6275e0abb35205ac48b494124d511_191.png)

3.  **给脚本添加执行权限并运行**：
    ```bash
    chmod +x /home/greg/ansible/adhoc.sh
    ./adhoc.sh
    ```

![](img/c0a6275e0abb35205ac48b494124d511_193.png)

![](img/c0a6275e0abb35205ac48b494124d511_195.png)

![](img/c0a6275e0abb35205ac48b494124d511_197.png)

4.  **验证仓库是否创建成功**：
    ```bash
    # 可以任选一台受管节点验证，例如servera
    ansible servera -a "ls /etc/yum.repos.d/"
    ansible servera -a "cat /etc/yum.repos.d/EX294_BASE.repo"
    ```

![](img/c0a6275e0abb35205ac48b494124d511_199.png)

![](img/c0a6275e0abb35205ac48b494124d511_201.png)

![](img/c0a6275e0abb35205ac48b494124d511_203.png)

![](img/c0a6275e0abb35205ac48b494124d511_205.png)

![](img/c0a6275e0abb35205ac48b494124d511_207.png)

**技巧提示**：如果记不住`yum_repository`模块的参数，可以使用`ansible-doc yum_repository`命令查看详细帮助和示例。

![](img/c0a6275e0abb35205ac48b494124d511_209.png)

![](img/c0a6275e0abb35205ac48b494124d511_211.png)

---

![](img/c0a6275e0abb35205ac48b494124d511_213.png)

## 实验三：使用Playbook安装软件包

![](img/c0a6275e0abb35205ac48b494124d511_215.png)

![](img/c0a6275e0abb35205ac48b494124d511_217.png)

![](img/c0a6275e0abb35205ac48b494124d511_219.png)

接下来，我们将学习编写Ansible Playbook，这是实现自动化任务的核心。

![](img/c0a6275e0abb35205ac48b494124d511_221.png)

![](img/c0a6275e0abb35205ac48b494124d511_223.png)

![](img/c0a6275e0abb35205ac48b494124d511_225.png)

![](img/c0a6275e0abb35205ac48b494124d511_227.png)

![](img/c0a6275e0abb35205ac48b494124d511_229.png)

**实验目标**：创建一个Playbook，为指定的主机组安装软件包和软件包组，并更新所有软件包。

![](img/c0a6275e0abb35205ac48b494124d511_231.png)

以下是操作步骤：

![](img/c0a6275e0abb35205ac48b494124d511_233.png)

![](img/c0a6275e0abb35205ac48b494124d511_235.png)

1.  **创建Playbook文件**：
    ```bash
    vim /home/greg/ansible/packages.yml
    ```

![](img/c0a6275e0abb35205ac48b494124d511_237.png)

![](img/c0a6275e0abb35205ac48b494124d511_239.png)

![](img/c0a6275e0abb35205ac48b494124d511_241.png)

![](img/c0a6275e0abb35205ac48b494124d511_243.png)

2.  **编写Playbook内容**：
    ```yaml
    ---
    - name: Install packages on dev, test, prod groups
      hosts: dev,test,prod
      tasks:
        - name: Install PHP package
          yum:
            name: php
            state: latest

        - name: Install MariaDB package
          yum:
            name: mariadb
            state: latest

    - name: Install package group on dev group
      hosts: dev
      tasks:
        - name: Install RPM development tools group
          yum:
            name: "@RPM Development Tools"
            state: latest

    - name: Update all packages on all hosts
      hosts: all
      tasks:
        - name: Update all packages to the latest version
          yum:
            name: "*"
            state: latest
    ```

3.  **执行Playbook并验证**：
    ```bash
    cd /home/greg/ansible
    ansible-playbook packages.yml
    # 验证软件包是否安装，例如在test组主机上检查php
    ansible test -a "rpm -q php"
    ```

![](img/c0a6275e0abb35205ac48b494124d511_245.png)

![](img/c0a6275e0abb35205ac48b494124d511_247.png)

---

![](img/c0a6275e0abb35205ac48b494124d511_249.png)

![](img/c0a6275e0abb35205ac48b494124d511_251.png)

![](img/c0a6275e0abb35205ac48b494124d511_253.png)

![](img/c0a6275e0abb35205ac48b494124d511_255.png)

![](img/c0a6275e0abb35205ac48b494124d511_257.png)

## 实验四：使用RHEL系统角色

![](img/c0a6275e0abb35205ac48b494124d511_259.png)

![](img/c0a6275e0abb35205ac48b494124d511_261.png)

![](img/c0a6275e0abb35205ac48b494124d511_263.png)

![](img/c0a6275e0abb35205ac48b494124d511_265.png)

Ansible的强大之处在于其可复用的角色。本节我们将学习如何使用红帽官方提供的系统角色。

![](img/c0a6275e0abb35205ac48b494124d511_267.png)

![](img/c0a6275e0abb35205ac48b494124d511_269.png)

![](img/c0a6275e0abb35205ac48b494124d511_271.png)

**实验目标**：安装RHEL系统角色软件包，并创建一个Playbook，使用`timesync`角色为所有受管节点配置NTP时间同步。

![](img/c0a6275e0abb35205ac48b494124d511_273.png)

![](img/c0a6275e0abb35205ac48b494124d511_275.png)

![](img/c0a6275e0abb35205ac48b494124d511_277.png)

以下是操作步骤：

![](img/c0a6275e0abb35205ac48b494124d511_279.png)

![](img/c0a6275e0abb35205ac48b494124d511_281.png)

![](img/c0a6275e0abb35205ac48b494124d511_283.png)

![](img/c0a6275e0abb35205ac48b494124d511_285.png)

![](img/c0a6275e0abb35205ac48b494124d511_287.png)

1.  **安装RHEL系统角色软件包（需root权限）**：
    ```bash
    # 在bastion节点上操作
    sudo yum install -y rhel-system-roles
    ```

![](img/c0a6275e0abb35205ac48b494124d511_289.png)

![](img/c0a6275e0abb35205ac48b494124d511_291.png)

![](img/c0a6275e0abb35205ac48b494124d511_293.png)

![](img/c0a6275e0abb35205ac48b494124d511_295.png)

![](img/c0a6275e0abb35205ac48b494124d511_297.png)

![](img/c0a6275e0abb35205ac48b494124d511_299.png)

2.  **配置Ansible角色路径**：
    ```bash
    # 编辑ansible.cfg，确保roles_path包含系统角色路径
    vim /home/greg/ansible/ansible.cfg
    ```
    *在`roles_path`行添加系统角色路径，用冒号分隔：*
    ```ini
    roles_path = /home/greg/ansible/roles:/usr/share/ansible/roles
    ```

![](img/c0a6275e0abb35205ac48b494124d511_301.png)

![](img/c0a6275e0abb35205ac48b494124d511_303.png)

![](img/c0a6275e0abb35205ac48b494124d511_305.png)

![](img/c0a6275e0abb35205ac48b494124d511_307.png)

3.  **创建Playbook文件**：
    ```bash
    vim /home/greg/ansible/timesync.yml
    ```

![](img/c0a6275e0abb35205ac48b494124d511_309.png)

![](img/c0a6275e0abb35205ac48b494124d511_311.png)

![](img/c0a6275e0abb35205ac48b494124d511_313.png)

4.  **编写Playbook内容**：
    ```yaml
    ---
    - name: Configure NTP using timesync role
      hosts: all
      vars:
        timesync_ntp_servers:
          - hostname: classroom.example.com
            iburst: yes
      roles:
        - role: rhel-system-roles.timesync
    ```

![](img/c0a6275e0abb35205ac48b494124d511_315.png)

![](img/c0a6275e0abb35205ac48b494124d511_317.png)

5.  **执行Playbook并验证**：
    ```bash
    ansible-playbook timesync.yml
    # 验证NTP配置，例如在balancers主机上
    ansible balancers -a "cat /etc/chrony.conf | grep server"
    ```

![](img/c0a6275e0abb35205ac48b494124d511_319.png)

![](img/c0a6275e0abb35205ac48b494124d511_321.png)

![](img/c0a6275e0abb35205ac48b494124d511_323.png)

![](img/c0a6275e0abb35205ac48b494124d511_325.png)

---

![](img/c0a6275e0abb35205ac48b494124d511_327.png)

![](img/c0a6275e0abb35205ac48b494124d511_329.png)

![](img/c0a6275e0abb35205ac48b494124d511_331.png)

![](img/c0a6275e0abb35205ac48b494124d511_333.png)

## 实验五：从Ansible Galaxy安装角色

![](img/c0a6275e0abb35205ac48b494124d511_335.png)

![](img/c0a6275e0abb35205ac48b494124d511_337.png)

![](img/c0a6275e0abb35205ac48b494124d511_339.png)

![](img/c0a6275e0abb35205ac48b494124d511_341.png)

![](img/c0a6275e0abb35205ac48b494124d511_343.png)

![](img/c0a6275e0abb35205ac48b494124d511_345.png)

![](img/c0a6275e0abb35205ac48b494124d511_347.png)

除了系统角色，我们还可以从Ansible Galaxy社区仓库安装第三方角色。

![](img/c0a6275e0abb35205ac48b494124d511_349.png)

![](img/c0a6275e0abb35205ac48b494124d511_351.png)

![](img/c0a6275e0abb35205ac48b494124d511_353.png)

![](img/c0a6275e0abb35205ac48b494124d511_355.png)

**实验目标**：从指定的URL安装两个角色，并确保它们能用于后续的Playbook。

![](img/c0a6275e0abb35205ac48b494124d511_357.png)

![](img/c0a6275e0abb35205ac48b494124d511_359.png)

![](img/c0a6275e0abb35205ac48b494124d511_361.png)

![](img/c0a6275e0abb35205ac48b494124d511_363.png)

以下是操作步骤：

![](img/c0a6275e0abb35205ac48b494124d511_365.png)

![](img/c0a6275e0abb35205ac48b494124d511_367.png)

1.  **创建角色需求文件**：
    ```bash
    mkdir -p /home/greg/ansible/roles
    vim /home/greg/ansible/roles/requirements.yml
    ```

![](img/c0a6275e0abb35205ac48b494124d511_369.png)

![](img/c0a6275e0abb35205ac48b494124d511_371.png)

![](img/c0a6275e0abb35205ac48b494124d511_373.png)

![](img/c0a6275e0abb35205ac48b494124d511_375.png)

2.  **编写需求文件内容**：
    ```yaml
    ---
    - src: http://content.example.com/roles/haproxy.tar
      name: haproxy

    - src: http://content.example.com/roles/phpinfo.tar
      name: phpinfo
    ```

![](img/c0a6275e0abb35205ac48b494124d511_377.png)

![](img/c0a6275e0abb35205ac48b494124d511_379.png)

![](img/c0a6275e0abb35205ac48b494124d511_381.png)

3.  **安装角色**：
    ```bash
    cd /home/greg/ansible
    ansible-galaxy role install -r roles/requirements.yml
    # 为确保判分脚本识别，建议在ansible目录下再执行一次
    ansible-galaxy role install -r roles/requirements.yml -p roles/
    ```

![](img/c0a6275e0abb35205ac48b494124d511_383.png)

4.  **验证角色是否安装成功**：
    ```bash
    ansible-galaxy role list
    ```
    应该能看到`haproxy`和`phpinfo`两个角色。

![](img/c0a6275e0abb35205ac48b494124d511_385.png)

![](img/c0a6275e0abb35205ac48b494124d511_387.png)

![](img/c0a6275e0abb35205ac48b494124d511_389.png)

![](img/c0a6275e0abb35205ac48b494124d511_391.png)

---

## 实验六：创建和使用自定义角色

现在，我们将综合运用所学知识，创建一个完整的自定义角色。

**实验目标**：创建一个名为`apache`的角色，实现以下功能：安装并启动httpd服务、配置防火墙规则、部署自定义首页。

![](img/c0a6275e0abb35205ac48b494124d511_393.png)

![](img/c0a6275e0abb35205ac48b494124d511_395.png)

以下是操作步骤：

![](img/c0a6275e0abb35205ac48b494124d511_397.png)

![](img/c0a6275e0abb35205ac48b494124d511_399.png)

![](img/c0a6275e0abb35205ac48b494124d511_401.png)

![](img/c0a6275e0abb35205ac48b494124d511_403.png)

![](img/c0a6275e0abb35205ac48b494124d511_405.png)

1.  **初始化角色结构**：
    ```bash
    cd /home/greg/ansible/roles
    ansible-galaxy init apache
    ```

![](img/c0a6275e0abb35205ac48b494124d511_407.png)

![](img/c0a6275e0abb35205ac48b494124d511_409.png)

2.  **编辑角色的主任务文件**：
    ```bash
    vim /home/greg/ansible/roles/apache/tasks/main.yml
    ```
    *文件内容如下：*
    ```yaml
    ---
    - name: Install Apache httpd package
      yum:
        name: httpd
        state: latest

    - name: Start and enable httpd service
      service:
        name: httpd
        state: started
        enabled: yes

    - name: Allow http traffic in firewalld
      firewalld:
        service: http
        permanent: yes
        state: enabled
        immediate: yes

    - name: Deploy index.html from template
      template:
        src: index.html.j2
        dest: /var/www/html/index.html
    ```

![](img/c0a6275e0abb35205ac48b494124d511_411.png)

![](img/c0a6275e0abb35205ac48b494124d511_413.png)

3.  **创建首页模板文件**：
    ```bash
    vim /home/greg/ansible/roles/apache/templates/index.html.j2
    ```
    *文件内容如下，使用了Ansible事实变量：*
    ```html
    <!DOCTYPE html>
    <html>
    <body>
    <h1>Hostname: {{ ansible_fqdn }}</h1>
    <h1>IP Address: {{ ansible_default_ipv4.address }}</h1>
    </body>
    </html>
    ```

![](img/c0a6275e0abb35205ac48b494124d511_415.png)

![](img/c0a6275e0abb35205ac48b494124d511_417.png)

4.  **创建一个Playbook来调用此角色**：
    ```bash
    vim /home/greg/ansible/test-apache.yml
    ```
    ```yaml
    ---
    - name: Test Apache role
      hosts: all
      roles:
        - apache
    ```

![](img/c0a6275e0abb35205ac48b494124d511_419.png)

![](img/c0a6275e0abb35205ac48b494124d511_421.png)

![](img/c0a6275e0abb35205ac48b494124d511_423.png)

![](img/c0a6275e0abb35205ac48b494124d511_425.png)

![](img/c0a6275e0abb35205ac48b494124d511_427.png)

![](img/c0a6275e0abb35205ac48b494124d511_429.png)

5.  **执行测试并验证**：
    ```bash
    ansible-playbook test-apache.yml
    # 使用curl访问任意受管节点的网站，例如servera
    ansible servera -a "curl http://localhost"
    ```

![](img/c0a6275e0abb35205ac48b494124d511_431.png)

![](img/c0a6275e0abb35205ac48b494124d511_433.png)

![](img/c0a6275e0abb35205ac48b494124d511_434.png)

![](img/c0a6275e0abb35205ac48b494124d511_436.png)

![](img/c0a6275e0abb35205ac48b494124d511_438.png)

![](img/c0a6275e0abb35205ac48b494124d511_440.png)

![](img/c0a6275e0abb35205ac48b494124d511_442.png)

---

![](img/c0a6275e0abb35205ac48b494124d511_444.png)

![](img/c0a6275e0abb35205ac48b494124d511_446.png)

![](img/c0a6275e0abb35205ac48b494124d511_448.png)

![](img/c0a6275e0abb35205ac48b494124d511_450.png)

## 实验七：使用变量、循环和条件判断

![](img/c0a6275e0abb35205ac48b494124d511_452.png)

![](img/c0a6275e0abb35205ac48b494124d511_454.png)

![](img/c0a6275e0abb35205ac48b494124d511_456.png)

![](img/c0a6275e0abb35205ac48b494124d511_458.png)

在复杂的自动化任务中，我们经常需要处理变量、循环和条件判断。本实验将演示这些高级功能。

![](img/c0a6275e0abb35205ac48b494124d511_460.png)

![](img/c0a6275e0abb35205ac48b494124d511_462.png)

![](img/c0a6275e0abb35205ac48b494124d511_464.png)

**实验目标**：创建一个Playbook，根据主机所属的不同组，修改其`/etc/issue`文件的内容。

以下是操作步骤：

![](img/c0a6275e0abb35205ac48b494124d511_466.png)

![](img/c0a6275e0abb35205ac48b494124d511_468.png)

![](img/c0a6275e0abb35205ac48b494124d511_470.png)

1.  **创建Playbook文件**：
    ```bash
    vim /home/greg/ansible/issue.yml
    ```

2.  **编写Playbook内容**：
    ```yaml
    ---
    - name: Modify /etc/issue based on host group
      hosts: all
      tasks:
        - name: Set content for dev group
          copy:
            content: "Development"
            dest: /etc/issue
          when: inventory_hostname in groups['dev']

        - name: Set content for test group
          copy:
            content: "Test"
            dest: /etc/issue
          when: inventory_hostname in groups['test']

        - name: Set content for prod group
          copy:
            content: "Production"
            dest: /etc/issue
          when: inventory_hostname in groups['prod']
    ```

![](img/c0a6275e0abb35205ac48b494124d511_472.png)

3.  **执行Playbook并验证**：
    ```bash
    ansible-playbook issue.yml
    # 分别检查不同组主机的文件内容
    ansible dev -a "cat /etc/issue"
    ansible test -a "cat /etc/issue"
    ansible prod -a "cat /etc/issue"
    ```

![](img/c0a6275e0abb35205ac48b494124d511_474.png)

---

![](img/c0a6275e0abb35205ac48b494124d511_476.png)

![](img/c0a6275e0abb35205ac48b494124d511_478.png)

![](img/c0a6275e0abb35205ac48b494124d511_480.png)

## 实验八：使用Ansible Vault加密敏感数据

![](img/c0a6275e0abb35205ac48b494124d511_482.png)

![](img/c0a6275e0abb35205ac48b494124d511_484.png)

![](img/c0a6275e0abb35205ac48b494124d511_486.png)

![](img/c0a6275e0abb35205ac48b494124d511_488.png)

![](img/c0a6275e0abb35205ac48b494124d511_490.png)

最后，我们将学习如何使用Ansible Vault来保护Playbook中的敏感信息，如密码。

![](img/c0a6275e0abb35205ac48b494124d511_492.png)

![](img/c0a6275e0abb35205ac48b494124d511_494.png)

![](img/c0a6275e0abb35205ac48b494124d511_496.png)

![](img/c0a6275e0abb35205ac48b494124d511_498.png)

![](img/c0a6275e0abb35205ac48b494124d511_500.png)

**实验目标**：创建一个加密的变量文件（密码库），并在Playbook中调用它。

![](img/c0a6275e0abb35205ac48b494124d511_502.png)

![](img/c0a6275e0abb35205ac48b494124d511_504.png)

![](img/c0a6275e0abb35205ac48b494124d511_506.png)

![](img/c0a6275e0abb35205ac48b494124d511_507.png)

![](img/c0a6275e0abb35205ac48b494124d511_508.png)

![](img/c0a6275e0abb35205ac48b494124d511_509.png)

以下是操作步骤：

![](img/c0a6275e0abb35205ac48b494124d511_510.png)

![](img/c0a6275e0abb35205ac48b494124d511_511.png)

![](img/c0a6275e0abb35205ac48b494124d511_512.png)

![](img/c0a6275e0abb35205ac48b494124d511_513.png)

![](img/c0a6275e0abb35205ac48b494124d511_514.png)

![](img/c0a6275e0abb35205ac48b494124d511_515.png)

![](img/c0a6275e0abb35205ac48b494124d511_516.png)

![](img/c0a6275e0abb35205ac48b494124d511_517.png)

![](img/c0a6275e0abb35205ac48b494124d511_518.png)

![](img/c0a6275e0abb35205ac48b494124d511_520.png)

![](img/c0a6275e0abb35205ac48b494124d511_522.png)

1.  **创建包含敏感变量的YAML文件**：
    ```bash
    vim /home/greg/ansible/locker.yml
    ```
    *内容如下：*
    ```yaml
    ---
    pw_developer: redhat_dev
    pw_manager: redhat_mgr
    ```

![](img/c0a6275e0abb35205ac48b494124d511_524.png)

![](img/c0a6275e0abb35205ac48b494124d511_526.png)

![](img/c0a6275e0abb35205ac48b494124d511_528.png)

![](img/c0a6275e0abb35205ac48b494124d511_530.png)

![](img/c0a6275e0abb35205ac48b494124d511_532.png)

![](img/c0a6275e0abb35205ac48b494124d511_534.png)

![](img/c0a6275e0abb35205ac48b494124d511_535.png)

![](img/c0a6275e0abb35205ac48b494124d511_536.png)

![](img/c0a6275e0abb35205ac48b494124d511_537.png)

![](img/c0a6275e0abb35205ac48b494124d511_538.png)

2.  **配置Ansible.cfg以指定Vault密码文件**：
    ```bash
    vim /home/greg/ansible/ansible.cfg
    ```
    *添加或修改以下行：*
    ```ini
    vault_password_file = /home/greg/ansible/vault_pass.txt
    ```

![](img/c0a6275e0abb35205ac48b494124d511_540.png)

3.  **创建Vault密码文件**：
    ```bash
    echo 'redhat' > /home/greg/ansible/vault_pass.txt
    chmod 600 /home/greg/ansible/vault_pass.txt
    ```

![](img/c0a6275e0abb35205ac48b494124d511_542.png)

4.  **加密变量文件**：
    ```bash
    cd /home/greg/ansible
    ansible-vault encrypt locker.yml
    ```
    执行后，`locker.yml`文件内容将变为加密格式。

![](img/c0a6275e0abb35205ac48b494124d511_544.png)

![](img/c0a6275e0abb35205ac48b494124d511_546.png)

5.  **在Playbook中调用加密变量**：
    *创建一个使用这些变量的Playbook示例（例如创建用户）：*
    ```bash
    vim /home/greg/ansible/create-users.yml
    ```
    ```yaml
    ---
    - name: Include encrypted variables
      hosts: localhost
      tasks:
        - name: Load variables
          include_vars: locker.yml

        - name: Debug variables (just for testing, don't do this in production!)
          debug:
            msg: "Developer password is {{ pw_developer }}"
    ```
    *运行此Playbook时，Ansible会自动使用vault密码文件解密。*

![](img/c0a6275e0abb35205ac48b494124d511_548.png)

![](img/c0a6275e0abb35205ac48b494124d511_550.png)

![](img/c0a6275e0abb35205ac48b494124d511_552.png)

![](img/c0a6275e0abb35205ac48b494124d511_553.png)

---

## 总结

![](img/c0a6275e0abb35205ac48b494124d511_555.png)

![](img/c0a6275e0abb35205ac48b494124d511_557.png)

![](img/c0a6275e0abb35205ac48b494124d511_559.png)

![](img/c0a6275e0abb35205ac48b494124d511_561.png)

在本节课中，我们一起学习了RHCE 8认证考试的全貌。从考试结构、环境规则，到Ansible的核心应用，我们涵盖了：

![](img/c0a6275e0abb35205ac48b494124d511_563.png)

![](img/c0a6275e0abb35205ac48b494124d511_565.png)

![](img/c0a6275e0abb35205ac48b494124d511_566.png)

![](img/c0a6275e0abb35205ac48b494124d511_568.png)

1.  **考试基础**：理解了RHCE 8的考核重点从多服务转向Ansible深度考核。
2.  **环境配置**：掌握了考试环境的特殊要求和必须遵守的规则。
3.  **Ansible核心技能**：
    *   安装配置与临时命令使用。
    *   Playbook的编写与执行。
    *   角色的使用（系统角色、Galaxy角色、自定义角色）。
    *   变量、循环、条件判断等高级功能。
    *   使用Ansible Vault加密敏感数据。

![](img/c0a6275e0abb35205ac48b494124d511_570.png)

![](img/c0a6275e0abb35205ac48b494124d511_572.png)

![](img/c0a6275e0abb35205ac48b494124d511_574.png)

![](img/c0a6275e0abb35205ac48b494124d511_576.png)

通过以上步骤化的学习和实践，您已经掌握了通过RHCE 8考试所需的关键知识和实战技能。记住，在考试中，仔细阅读题目要求、严格遵守目录和用户规则、善用`ansible-doc`查询帮助，是成功的关键。祝您考试顺利！