# RHCE 8 考前辅导：1：课程概述与考试介绍

在本节课中，我们将要学习红帽认证工程师（RHCE）8版本的考试内容、方法及技巧。RHCE认证是红帽认证体系中的重要组成部分，与RHCSA认证共同构成了红帽认证系统管理员（RHCA）的基础。

![](img/c04863d07827b97fe321287a5dbce5ab_1.png)

## 课程概述

![](img/c04863d07827b97fe321287a5dbce5ab_3.png)

RHCE认证由两部分组成：上午的RHCSA考试（2.5小时）和下午的RHCE考试（3.5小时）。考生必须同时通过这两门考试，才能获得最终的RHCE认证。与思科等认证不同，红帽认证不能跳级考试，必须按顺序先通过RHCSA，再通过RHCE。

![](img/c04863d07827b97fe321287a5dbce5ab_5.png)

如果上午的RHCSA考试未通过，需要先补考通过RHCSA，才能获得RHCE认证。同样，如果下午的RHCE考试未通过，也需要进行补考。这类似于教育体系，拥有高中毕业证意味着已经拥有初中毕业证。因此，获得RHCE认证，就意味着已经通过了RHCSA认证，其含金量更高。

## 考试内容变化

与红帽5、6、7版本相比，RHCE 8版本有显著变化。RHCSA考试内容基本保持不变，依然考核系统管理能力，如开关机、硬盘分区格式化挂载、服务启停、用户增删改查等。

而传统的RHCE 5/6/7版本主要考核对各种服务的管理能力，如Web服务、文件共享服务（Samba/NFS）、数据库服务等，考核的是一个“面”，要求考生掌握多种常用服务。

RHCE 8版本则主要考核一个“点”——即Ansible自动化运维。它实质上是将原先红帽的DO407（Ansible自动化）课程内容纳入RHCE考试。这意味着考试深度增加，难度完全超越了之前的版本。好消息是，考核点更集中；坏消息是，考生需要投入更多时间和精力进行备考。

## 考试难度与技巧

毫无疑问，RHCE 8的考试难度增加了。但考试形式也发生了变化：之前的版本需要考生具备较强的理解和排错能力；而RHCE 8版本更适合通过练习和记忆来备考，配置文件的内容较多。

本课程作为考前辅导，不会完全照搬红帽的标准答案，而是会分享一些考试技巧，帮助大家以最低成本、最轻松的方式通过认证。这些方法可能被视为“技巧”或“取巧”，但红帽考试只看结果不看过程，没有唯一的标准答案，只有无限接近最优解的参考答案。

在接下来的章节中，我们将逐一讲解考题，并进行实际操作，帮助大家熟悉考试流程，找到考试感觉。

![](img/c04863d07827b97fe321287a5dbce5ab_7.png)

![](img/c04863d07827b97fe321287a5dbce5ab_8.png)

---

![](img/c04863d07827b97fe321287a5dbce5ab_10.png)

# RHCE 8 考前辅导：2：考试环境与重要须知

![](img/c04863d07827b97fe321287a5dbce5ab_12.png)

![](img/c04863d07827b97fe321287a5dbce5ab_14.png)

上一节我们介绍了RHCE 8考试的总体情况，本节中我们来看看具体的考试环境设置和一些必须牢记的重要事项，这些是考试成功的基础。

![](img/c04863d07827b97fe321287a5dbce5ab_16.png)

## 考试环境架构

![](img/c04863d07827b97fe321287a5dbce5ab_18.png)

![](img/c04863d07827b97fe321287a5dbce5ab_19.png)

考试期间，您将使用一台物理台式机和多个虚拟机。
*   您**不具备**物理台式机系统的root访问权限。
*   您**具有**对所有虚拟机的完全root访问权限。

![](img/c04863d07827b97fe321287a5dbce5ab_21.png)

具体来说，考场内您面前只有一台电脑，这台电脑上运行着6台虚拟机。这与RHCSA考试不同（RHCSA通常有两台实体机）。其中一台虚拟机作为**控制节点（control node）**，您需要在这台机器上完成所有答题操作。

![](img/c04863d07827b97fe321287a5dbce5ab_23.png)

![](img/c04863d07827b97fe321287a5dbce5ab_24.png)

以下是主机列表和关键说明：

![](img/c04863d07827b97fe321287a5dbce5ab_26.png)

*   **control.example.com**：这是您的控制节点（Control Node）。**所有答题操作必须在此节点上进行**，切勿在其他虚拟机上直接操作，否则可能导致零分。
*   **servera.example.com** 等其余5台虚拟机：这些都是**受管节点（Managed Node）**，它们之间没有功能差异，只是名称不同。

![](img/c04863d07827b97fe321287a5dbce5ab_28.png)

![](img/c04863d07827b97fe321287a5dbce5ab_29.png)

![](img/c04863d07827b97fe321287a5dbce5ab_30.png)

![](img/c04863d07827b97fe321287a5dbce5ab_32.png)

![](img/c04863d07827b97fe321287a5dbce5ab_33.png)

![](img/c04863d07827b97fe321287a5dbce5ab_34.png)

## 关键配置与默认设置

![](img/c04863d07827b97fe321287a5dbce5ab_36.png)

考试环境已预先配置好以下内容，**请勿更改**：
*   所有虚拟机的IP地址、DNS和主机名。
*   所有系统的root密码均为 `redhat`。考试中只要涉及密码，均使用此密码。
*   所有系统间已预设SSH密钥，可实现免密登录。
*   所有受管节点上已创建一个名为 `grader` 的用户，并配置了SSH密钥。在模拟环境中，我们需要自行创建此用户。

![](img/c04863d07827b97fe321287a5dbce5ab_38.png)

![](img/c04863d07827b97fe321287a5dbce5ab_40.png)

## **重中之重：用户与目录**

![](img/c04863d07827b97fe321287a5dbce5ab_42.png)

![](img/c04863d07827b97fe321287a5dbce5ab_44.png)

![](img/c04863d07827b97fe321287a5dbce5ab_45.png)

![](img/c04863d07827b97fe321287a5dbce5ab_47.png)

这是考试中最容易丢分的地方，请务必牢记：

![](img/c04863d07827b97fe321287a5dbce5ab_49.png)

![](img/c04863d07827b97fe321287a5dbce5ab_51.png)

![](img/c04863d07827b97fe321287a5dbce5ab_53.png)

![](img/c04863d07827b97fe321287a5dbce5ab_55.png)

![](img/c04863d07827b97fe321287a5dbce5ab_56.png)

**所有Ansible相关操作（配置、编写Playbook）都必须使用 `grader` 用户执行，并且所有相关的文件、工具和YAML文件都必须存放在 `/home/grader/ansible` 目录下。**

![](img/c04863d07827b97fe321287a5dbce5ab_58.png)

![](img/c04863d07827b97fe321287a5dbce5ab_60.png)

![](img/c04863d07827b97fe321287a5dbce5ab_62.png)

![](img/c04863d07827b97fe321287a5dbce5ab_64.png)

判分脚本会在这个特定目录下检查您的成果。即使您的Playbook编写正确，但若存放目录错误，也将无法得分。**Root用户仅用于安装软件包等需要特权的操作。**

![](img/c04863d07827b97fe321287a5dbce5ab_65.png)

![](img/c04863d07827b97fe321287a5dbce5ab_66.png)

![](img/c04863d07827b97fe321287a5dbce5ab_67.png)

## 其他注意事项

![](img/c04863d07827b97fe321287a5dbce5ab_69.png)

*   **清单文件**：系统中已存在一个名为 `/home/grader/ansible/inventory` 的主机清单文件。您可以修改或添加内容，但**不要删除**已有的内容。
*   **防火墙与SELinux**：在RHCE 8的Ansible上下文中，不考核防火墙和SELinux的配置。
*   **软件仓库**：考试系统已配置好软件仓库，您只需正确指向即可。
*   **帮助信息**：系统提供在线帮助，但考试时间宝贵，建议考前熟悉，考试时无需查看。
*   **节点重置**：评分前，受管节点会被重置，只有控制节点上的操作是有效的。

确保遵循以上所有要求，是顺利通过考试的第一步。

---

![](img/c04863d07827b97fe321287a5dbce5ab_71.png)

![](img/c04863d07827b97fe321287a5dbce5ab_72.png)

![](img/c04863d07827b97fe321287a5dbce5ab_73.png)

![](img/c04863d07827b97fe321287a5dbce5ab_74.png)

![](img/c04863d07827b97fe321287a5dbce5ab_76.png)

![](img/c04863d07827b97fe321287a5dbce5ab_78.png)

![](img/c04863d07827b97fe321287a5dbce5ab_79.png)

# RHCE 8 考前辅导：3：实战题目串讲与操作（上）

![](img/c04863d07827b97fe321287a5dbce5ab_81.png)

![](img/c04863d07827b97fe321287a5dbce5ab_83.png)

在熟悉了考试环境后，我们从本节开始进入实战题目串讲。我们将按照考试顺序，逐一讲解核心题目，并提供高效的操作方法。

![](img/c04863d07827b97fe321287a5dbce5ab_85.png)

## 题目1：安装和配置Ansible

![](img/c04863d07827b97fe321287a5dbce5ab_87.png)

![](img/c04863d07827b97fe321287a5dbce5ab_89.png)

![](img/c04863d07827b97fe321287a5dbce5ab_90.png)

![](img/c04863d07827b97fe321287a5dbce5ab_91.png)

![](img/c04863d07827b97fe321287a5dbce5ab_92.png)

**任务概述**：在控制节点上安装Ansible，并创建配置文件。

![](img/c04863d07827b97fe321287a5dbce5ab_94.png)

![](img/c04863d07827b97fe321287a5dbce5ab_96.png)

![](img/c04863d07827b97fe321287a5dbce5ab_97.png)

![](img/c04863d07827b97fe321287a5dbce5ab_98.png)

以下是操作步骤：

![](img/c04863d07827b97fe321287a5dbce5ab_100.png)

![](img/c04863d07827b97fe321287a5dbce5ab_102.png)

1.  **创建用户和目录**（模拟环境需要，考试环境已存在）：
    ```bash
    sudo useradd grader
    echo “redhat” | sudo passwd --stdin grader
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_104.png)

![](img/c04863d07827b97fe321287a5dbce5ab_105.png)

![](img/c04863d07827b97fe321287a5dbce5ab_107.png)

![](img/c04863d07827b97fe321287a5dbce5ab_109.png)

2.  **切换到grader用户并创建关键目录**：
    ```bash
    su - grader
    mkdir ~/ansible
    cd ~/ansible
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_110.png)

![](img/c04863d07827b97fe321287a5dbce5ab_112.png)

![](img/c04863d07827b97fe321287a5dbce5ab_113.png)

![](img/c04863d07827b97fe321287a5dbce5ab_115.png)

![](img/c04863d07827b97fe321287a5dbce5ab_117.png)

![](img/c04863d07827b97fe321287a5dbce5ab_118.png)

3.  **创建主机清单文件** `inventory`，并按要求分组。例如，`dev` 组包含主机 `172.25.250.9`：
    ```ini
    [dev]
    172.25.250.9
    [test]
    172.25.250.10
    [prod]
    172.25.250.11
    [balancers]
    172.25.250.12
    [webservers]
    172.25.250.13
    [webservers:children]
    prod
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_120.png)

![](img/c04863d07827b97fe321287a5dbce5ab_122.png)

![](img/c04863d07827b97fe321287a5dbce5ab_124.png)

![](img/c04863d07827b97fe321287a5dbce5ab_126.png)

4.  **创建Ansible配置文件** `ansible.cfg`。最简单的方法是从系统配置复制并修改：
    ```bash
    cp /etc/ansible/ansible.cfg ~/ansible/
    cd ~/ansible
    ```
    编辑 `ansible.cfg`，修改以下几处关键行：
    ```ini
    inventory      = /home/grader/ansible/inventory
    roles_path    = /home/grader/ansible/roles
    host_key_checking = False
    remote_user = root
    ```
    在 `[all:vars]` 部分添加：
    ```ini
    ansible_user=root
    ansible_password=redhat
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_128.png)

![](img/c04863d07827b97fe321287a5dbce5ab_130.png)

5.  **测试配置**：执行以下命令，测试到所有主机的连接。
    ```bash
    ansible all -m ping
    ```

**本节总结**：第一题主要考察环境搭建和基础配置。核心是牢记使用 `grader` 用户和在 `/home/grader/ansible` 目录下工作。

![](img/c04863d07827b97fe321287a5dbce5ab_132.png)

![](img/c04863d07827b97fe321287a5dbce5ab_133.png)

---

![](img/c04863d07827b97fe321287a5dbce5ab_135.png)

![](img/c04863d07827b97fe321287a5dbce5ab_137.png)

![](img/c04863d07827b97fe321287a5dbce5ab_139.png)

![](img/c04863d07827b97fe321287a5dbce5ab_141.png)

## 题目2：使用临时命令创建YUM仓库

![](img/c04863d07827b97fe321287a5dbce5ab_143.png)

![](img/c04863d07827b97fe321287a5dbce5ab_144.png)

![](img/c04863d07827b97fe321287a5dbce5ab_146.png)

![](img/c04863d07827b97fe321287a5dbce5ab_147.png)

**任务概述**：编写一个脚本，使用Ansible临时命令在所有受管节点上创建两个特定的YUM仓库。

![](img/c04863d07827b97fe321287a5dbce5ab_149.png)

![](img/c04863d07827b97fe321287a5dbce5ab_150.png)

以下是操作思路：

![](img/c04863d07827b97fe321287a5dbce5ab_152.png)

![](img/c04863d07827b97fe321287a5dbce5ab_153.png)

1.  **首先创建脚本文件** `create_repos.sh`：
    ```bash
    cd ~/ansible
    vim create_repos.sh
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_155.png)

![](img/c04863d07827b97fe321287a5dbce5ab_157.png)

![](img/c04863d07827b97fe321287a5dbce5ab_159.png)

![](img/c04863d07827b97fe321287a5dbce5ab_160.png)

2.  **编写临时命令**。使用 `ansible-doc yum_repository` 命令查看模块用法和示例。创建仓库的临时命令格式如下：
    ```bash
    ansible all -m yum_repository -a “name=EXAMPLENAME description=’EXAMPLE DESC’ baseurl=EXAMPLEURL gpgcheck=yes gpgkey=EXAMPLEKEY enabled=yes”
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_162.png)

3.  **根据题目要求，替换参数**。将创建两个仓库的命令写入脚本。例如：
    ```bash
    #!/bin/bash
    ansible all -m yum_repository -a “name=EXAM_REPO1 description=’Example Repo 1’ baseurl=http://content.example.com/rhel8.0/x86_64/dvd/BaseOS gpgcheck=yes gpgkey=http://content.example.com/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release enabled=yes”
    ansible all -m yum_repository -a “name=EXAM_REPO2 description=’Example Repo 2’ baseurl=http://content.example.com/rhel8.0/x86_64/dvd/AppStream gpgcheck=yes gpgkey=http://content.example.com/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release enabled=yes”
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_164.png)

![](img/c04863d07827b97fe321287a5dbce5ab_166.png)

4.  **给脚本加执行权限并运行**：
    ```bash
    chmod +x create_repos.sh
    ./create_repos.sh
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_168.png)

![](img/c04863d07827b97fe321287a5dbce5ab_169.png)

![](img/c04863d07827b97fe321287a5dbce5ab_170.png)

![](img/c04863d07827b97fe321287a5dbce5ab_172.png)

![](img/c04863d07827b97fe321287a5dbce5ab_174.png)

![](img/c04863d07827b97fe321287a5dbce5ab_175.png)

5.  **验证**：可以登录任意受管节点，检查 `/etc/yum.repos.d/` 目录下是否生成了对应的repo文件。

![](img/c04863d07827b97fe321287a5dbce5ab_177.png)

![](img/c04863d07827b97fe321287a5dbce5ab_179.png)

![](img/c04863d07827b97fe321287a5dbce5ab_180.png)

**本节总结**：此题考察Ansible临时命令的使用。关键在于使用 `ansible-doc` 查找模块语法，并正确拼接参数。将所有命令保存到指定脚本文件即可得分。

---

![](img/c04863d07827b97fe321287a5dbce5ab_182.png)

## 题目3：使用Playbook安装软件包

![](img/c04863d07827b97fe321287a5dbce5ab_184.png)

![](img/c04863d07827b97fe321287a5dbce5ab_185.png)

![](img/c04863d07827b97fe321287a5dbce5ab_186.png)

![](img/c04863d07827b97fe321287a5dbce5ab_188.png)

![](img/c04863d07827b97fe321287a5dbce5ab_190.png)

**任务概述**：创建Playbook，为不同的主机组安装指定的软件包或软件包组。

![](img/c04863d07827b97fe321287a5dbce5ab_192.png)

![](img/c04863d07827b97fe321287a5dbce5ab_193.png)

![](img/c04863d07827b97fe321287a5dbce5ab_195.png)

![](img/c04863d07827b97fe321287a5dbce5ab_197.png)

以下是操作步骤：

![](img/c04863d07827b97fe321287a5dbce5ab_199.png)

1.  **创建Playbook文件** `install_packages.yml`。
2.  **编写Playbook内容**。题目通常要求：
    *   为 `dev`, `test`, `prod` 组安装 `php` 和 `mariadb` 软件包。
    *   为 `dev` 组安装 `RPM Development Tools` 软件包组。
    *   将 `dev` 主机上的所有软件包更新到最新版本。

    示例Playbook结构如下：
    ```yaml
    ---
    - name: Install packages for dev, test, prod
      hosts: dev,test,prod
      tasks:
        - name: Install php
          yum:
            name: php
            state: latest
        - name: Install mariadb
          yum:
            name: mariadb
            state: latest

    - name: Install package group for dev
      hosts: dev
      tasks:
        - name: Install RPM Development Tools
          yum:
            name: “@RPM Development Tools”
            state: present

    - name: Update all packages on dev
      hosts: dev
      tasks:
        - name: Update all packages
          yum:
            name: “*”
            state: latest
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_201.png)

3.  **运行Playbook并验证**：
    ```bash
    cd ~/ansible
    ansible-playbook install_packages.yml
    ```
    之后可以到受管节点上使用 `yum list installed | grep php` 等命令验证。

![](img/c04863d07827b97fe321287a5dbce5ab_203.png)

![](img/c04863d07827b97fe321287a5dbce5ab_205.png)

![](img/c04863d07827b97fe321287a5dbce5ab_207.png)

**本节总结**：此题考察使用Playbook进行软件包管理。注意区分 `hosts` 的目标组，以及安装单个包、包组和全部更新的不同写法。使用 `@` 符号表示软件包组。

![](img/c04863d07827b97fe321287a5dbce5ab_209.png)

---

![](img/c04863d07827b97fe321287a5dbce5ab_211.png)

# RHCE 8 考前辅导：4：实战题目串讲与操作（下）

![](img/c04863d07827b97fe321287a5dbce5ab_213.png)

![](img/c04863d07827b97fe321287a5dbce5ab_215.png)

![](img/c04863d07827b97fe321287a5dbce5ab_217.png)

上一节我们完成了前三道基础题的操作，本节我们继续深入，讲解涉及角色、变量、文件管理等更复杂的题目。

![](img/c04863d07827b97fe321287a5dbce5ab_219.png)

![](img/c04863d07827b97fe321287a5dbce5ab_220.png)

![](img/c04863d07827b97fe321287a5dbce5ab_221.png)

![](img/c04863d07827b97fe321287a5dbce5ab_222.png)

![](img/c04863d07827b97fe321287a5dbce5ab_224.png)

![](img/c04863d07827b97fe321287a5dbce5ab_225.png)

![](img/c04863d07827b97fe321287a5dbce5ab_226.png)

## 题目4：使用RHEL系统角色

![](img/c04863d07827b97fe321287a5dbce5ab_227.png)

![](img/c04863d07827b97fe321287a5dbce5ab_229.png)

![](img/c04863d07827b97fe321287a5dbce5ab_231.png)

![](img/c04863d07827b97fe321287a5dbce5ab_233.png)

**任务概述**：安装RHEL系统角色软件包，并创建Playbook使用 `timesync` 角色来配置所有受管节点的NTP。

![](img/c04863d07827b97fe321287a5dbce5ab_235.png)

![](img/c04863d07827b97fe321287a5dbce5ab_236.png)

![](img/c04863d07827b97fe321287a5dbce5ab_238.png)

以下是操作步骤：

![](img/c04863d07827b97fe321287a5dbce5ab_240.png)

1.  **安装系统角色包**（需root权限）：
    ```bash
    sudo yum install rhel-system-roles -y
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_242.png)

![](img/c04863d07827b97fe321287a5dbce5ab_243.png)

![](img/c04863d07827b97fe321287a5dbce5ab_245.png)

![](img/c04863d07827b97fe321287a5dbce5ab_247.png)

![](img/c04863d07827b97fe321287a5dbce5ab_249.png)

2.  **配置Ansible角色路径**。编辑 `/home/grader/ansible/ansible.cfg`，确保 `roles_path` 包含系统角色路径，例如：
    ```ini
    roles_path = /home/grader/ansible/roles:/usr/share/ansible/roles:/usr/share/ansible/roles/rhel-system-roles
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_250.png)

![](img/c04863d07827b97fe321287a5dbce5ab_252.png)

![](img/c04863d07827b97fe321287a5dbce5ab_254.png)

![](img/c04863d07827b97fe321287a5dbce5ab_256.png)

3.  **获取角色模板**。系统角色提供了示例Playbook，可以直接复制使用：
    ```bash
    cp /usr/share/doc/rhel-system-roles/timesync/example-timesync-playbook.yml ~/ansible/timesync.yml
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_257.png)

![](img/c04863d07827b97fe321287a5dbce5ab_259.png)

![](img/c04863d07827b97fe321287a5dbce5ab_261.png)

![](img/c04863d07827b97fe321287a5dbce5ab_262.png)

![](img/c04863d07827b97fe321287a5dbce5ab_264.png)

![](img/c04863d07827b97fe321287a5dbce5ab_266.png)

4.  **修改模板Playbook**。编辑 `timesync.yml`，主要修改两部分：
    *   将 `hosts` 改为 `all`，针对所有主机。
    *   在 `vars` 部分，配置 `timesync_ntp_servers`，指定NTP服务器，例如：
        ```yaml
        vars:
          timesync_ntp_servers:
            - hostname: classroom.example.com
              iburst: yes
        ```
    *   删除其他不必要的示例配置。

![](img/c04863d07827b97fe321287a5dbce5ab_268.png)

![](img/c04863d07827b97fe321287a5dbce5ab_270.png)

![](img/c04863d07827b97fe321287a5dbce5ab_272.png)

![](img/c04863d07827b97fe321287a5dbce5ab_274.png)

5.  **运行Playbook并验证**：
    ```bash
    cd ~/ansible
    ansible-playbook timesync.yml
    ```
    登录受管节点，使用 `timedatectl` 或检查 `/etc/chrony.conf` 验证NTP服务器是否已配置。

![](img/c04863d07827b97fe321287a5dbce5ab_276.png)

**本节总结**：此题考察使用预定义的系统角色。关键在于找到并利用现有的示例模板，根据题目要求进行最小化修改，可以极大提高效率和准确性。

![](img/c04863d07827b97fe321287a5dbce5ab_278.png)

![](img/c04863d07827b97fe321287a5dbce5ab_280.png)

---

![](img/c04863d07827b97fe321287a5dbce5ab_282.png)

![](img/c04863d07827b97fe321287a5dbce5ab_284.png)

![](img/c04863d07827b97fe321287a5dbce5ab_286.png)

![](img/c04863d07827b97fe321287a5dbce5ab_288.png)

## 题目5：创建和使用自定义角色

![](img/c04863d07827b97fe321287a5dbce5ab_290.png)

![](img/c04863d07827b97fe321287a5dbce5ab_292.png)

**任务概述**：创建一个名为 `apache` 的自定义角色，实现安装、启动、配置防火墙和部署网页等功能。

![](img/c04863d07827b97fe321287a5dbce5ab_294.png)

![](img/c04863d07827b97fe321287a5dbce5ab_295.png)

![](img/c04863d07827b97fe321287a5dbce5ab_296.png)

![](img/c04863d07827b97fe321287a5dbce5ab_297.png)

![](img/c04863d07827b97fe321287a5dbce5ab_299.png)

![](img/c04863d07827b97fe321287a5dbce5ab_300.png)

![](img/c04863d07827b97fe321287a5dbce5ab_301.png)

以下是操作思路：

![](img/c04863d07827b97fe321287a5dbce5ab_303.png)

![](img/c04863d07827b97fe321287a5dbce5ab_304.png)

![](img/c04863d07827b97fe321287a5dbce5ab_306.png)

![](img/c04863d07827b97fe321287a5dbce5ab_308.png)

1.  **初始化角色结构**：
    ```bash
    cd ~/ansible/roles
    ansible-galaxy init apache
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_310.png)

![](img/c04863d07827b97fe321287a5dbce5ab_312.png)

![](img/c04863d07827b97fe321287a5dbce5ab_314.png)

![](img/c04863d07827b97fe321287a5dbce5ab_316.png)

2.  **编辑角色的主任务文件** `roles/apache/tasks/main.yml`。任务通常包括：
    *   安装 `httpd` 软件包。
    *   启动并启用 `httpd` 服务。
    *   配置防火墙放行 `http` 服务。
    *   使用模板部署首页文件。

    示例 `main.yml` 内容：
    ```yaml
    ---
    - name: Install httpd package
      yum:
        name: httpd
        state: present

    - name: Start and enable httpd service
      service:
        name: httpd
        state: started
        enabled: yes

    - name: Configure firewall to allow http
      firewalld:
        service: http
        permanent: yes
        immediate: yes
        state: enabled

    - name: Deploy index.html using template
      template:
        src: index.html.j2
        dest: /var/www/html/index.html
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_318.png)

![](img/c04863d07827b97fe321287a5dbce5ab_320.png)

![](img/c04863d07827b97fe321287a5dbce5ab_322.png)

3.  **创建模板文件** `roles/apache/templates/index.html.j2`。内容需根据题目要求，使用变量动态生成，例如显示主机名和IP：
    ```html
    <html>
    <body>
    <p>Hostname: {{ ansible_fqdn }}</p>
    <p>IP Address: {{ ansible_default_ipv4.address }}</p>
    </body>
    </html>
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_323.png)

![](img/c04863d07827b97fe321287a5dbce5ab_325.png)

![](img/c04863d07827b97fe321287a5dbce5ab_326.png)

4.  **创建Playbook调用该角色**，例如 `use_apache.yml`：
    ```yaml
    ---
    - name: Use apache role
      hosts: webservers
      roles:
        - apache
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_328.png)

![](img/c04863d07827b97fe321287a5dbce5ab_329.png)

![](img/c04863d07827b97fe321287a5dbce5ab_331.png)

![](img/c04863d07827b97fe321287a5dbce5ab_333.png)

5.  **运行测试**。

**本节总结**：创建自定义角色是RHCE 8的核心。重点是理解角色目录结构，在 `tasks/main.yml` 中组织任务序列，在 `templates/` 中使用Jinja2模板和变量（如 `{{ ansible_fqdn }}`）生成动态内容。

![](img/c04863d07827b97fe321287a5dbce5ab_335.png)

![](img/c04863d07827b97fe321287a5dbce5ab_336.png)

![](img/c04863d07827b97fe321287a5dbce5ab_337.png)

![](img/c04863d07827b97fe321287a5dbce5ab_338.png)

---

![](img/c04863d07827b97fe321287a5dbce5ab_340.png)

![](img/c04863d07827b97fe321287a5dbce5ab_341.png)

## 题目6：使用Ansible Galaxy安装并使用角色

![](img/c04863d07827b97fe321287a5dbce5ab_343.png)

![](img/c04863d07827b97fe321287a5dbce5ab_344.png)

![](img/c04863d07827b97fe321287a5dbce5ab_346.png)

**任务概述**：从Ansible Galaxy安装指定的社区角色，并在Playbook中调用它们。

以下是操作步骤：

1.  **创建角色需求文件** `roles/requirements.yml`，指定要安装的角色和名称：
    ```yaml
    ---
    - src: https://github.com/geerlingguy/ansible-role-haproxy
      name: haproxy
    - src: https://github.com/geerlingguy/ansible-role-php-info
      name: phpinfo
    ```

2.  **安装角色**。**关键技巧**：为确保判分脚本识别，建议在 `roles` 目录和项目根目录各执行一次安装：
    ```bash
    cd ~/ansible/roles
    ansible-galaxy install -r requirements.yml
    cd ~/ansible
    ansible-galaxy install -r roles/requirements.yml
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_348.png)

![](img/c04863d07827b97fe321287a5dbce5ab_350.png)

3.  **创建Playbook使用这些角色**，例如 `galaxy_roles.yml`：
    ```yaml
    ---
    - name: Use HAProxy role
      hosts: balancers
      roles:
        - haproxy

    - name: Use PHP info role
      hosts: webservers
      roles:
        - phpinfo
    # 注意：通常还需要确保httpd服务已安装运行，可能需要包含之前创建的apache角色或单独的任务。
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_352.png)

![](img/c04863d07827b97fe321287a5dbce5ab_353.png)

![](img/c04863d07827b97fe321287a5dbce5ab_355.png)

![](img/c04863d07827b97fe321287a5dbce5ab_356.png)

![](img/c04863d07827b97fe321287a5dbce5ab_358.png)

**本节总结**：此题考察从外部源获取角色并集成到Playbook中。安装角色时执行两遍是一个实用的考试技巧，用以确保角色被正确识别。调用方式与自定义角色相同。

![](img/c04863d07827b97fe321287a5dbce5ab_360.png)

![](img/c04863d07827b97fe321287a5dbce5ab_362.png)

---

![](img/c04863d07827b97fe321287a5dbce5ab_364.png)

## 题目7：生成主机文件（Hosts File）

![](img/c04863d07827b97fe321287a5dbce5ab_366.png)

![](img/c04863d07827b97fe321287a5dbce5ab_368.png)

**任务概述**：创建一个Playbook，在指定主机组的 `/etc/hosts` 文件中生成包含组内所有主机记录。

![](img/c04863d07827b97fe321287a5dbce5ab_370.png)

![](img/c04863d07827b97fe321287a5dbce5ab_372.png)

![](img/c04863d07827b97fe321287a5dbce5ab_373.png)

这是一个可以巧妙简化的题目。标准方法是使用Jinja2模板和循环变量动态生成。但有一个更直接的技巧：

![](img/c04863d07827b97fe321287a5dbce5ab_374.png)

![](img/c04863d07827b97fe321287a5dbce5ab_376.png)

![](img/c04863d07827b97fe321287a5dbce5ab_378.png)

![](img/c04863d07827b97fe321287a5dbce5ab_380.png)

![](img/c04863d07827b97fe321287a5dbce5ab_382.png)

![](img/c04863d07827b97fe321287a5dbce5ab_384.png)

1.  **获取模板文件**（如果题目提供链接）。
2.  **直接创建Playbook** `hosts.yml`，使用 `copy` 模块而非 `template` 模块。
3.  **手动构造最终的 `/etc/hosts` 文件内容**。因为考试时组内主机和IP是固定的，我们可以直接将完整的、静态的内容写入Playbook。

    示例 `hosts.yml`：
    ```yaml
    ---
    - name: Generate hosts file for dev group
      hosts: dev
      tasks:
        - name: Copy static hosts file
          copy:
            content: |
              172.25.250.9 servera.example.com servera
              172.25.250.10 serverb.example.com serverb
              172.25.250.11 serverc.example.com serverc
              # ... 根据实际清单添加所有主机
            dest: /etc/hosts
    ```
    这种方法避免了复杂的变量循环，只要内容与最终要求一致，就能得分，因为红帽“只看结果”。

![](img/c04863d07827b97fe321287a5dbce5ab_385.png)

![](img/c04863d07827b97fe321287a5dbce5ab_387.png)

![](img/c04863d07827b97fe321287a5dbce5ab_388.png)

![](img/c04863d07827b97fe321287a5dbce5ab_390.png)

![](img/c04863d07827b97fe321287a5dbce5ab_392.png)

![](img/c04863d07827b97fe321287a5dbce5ab_394.png)

**本节总结**：此题考察文件内容管理。在理解变量循环方法的同时，掌握这种基于静态内容的“结果导向”技巧，可以在考试中节省大量时间。这是考前辅导提供的实用策略之一。

![](img/c04863d07827b97fe321287a5dbce5ab_396.png)

![](img/c04863d07827b97fe321287a5dbce5ab_398.png)

![](img/c04863d07827b97fe321287a5dbce5ab_400.png)

![](img/c04863d07827b97fe321287a5dbce5ab_402.png)

---

![](img/c04863d07827b97fe321287a5dbce5ab_404.png)

![](img/c04863d07827b97fe321287a5dbce5ab_406.png)

![](img/c04863d07827b97fe321287a5dbce5ab_408.png)

# RHCE 8 考前辅导：5：高级任务与总结

![](img/c04863d07827b97fe321287a5dbce5ab_410.png)

![](img/c04863d07827b97fe321287a5dbce5ab_411.png)

![](img/c04863d07827b97fe321287a5dbce5ab_413.png)

本节我们将完成最后几道涉及逻辑卷、加密库、用户创建等高级任务的题目，并对整个RHCE 8考前辅导进行总结。

## 题目8：创建和使用逻辑卷

**任务概述**：编写Playbook，在存在 `research` 卷组的受管节点上创建指定大小的逻辑卷，并格式化。要求包含错误处理（如卷组不存在或空间不足）。

![](img/c04863d07827b97fe321287a5dbce5ab_415.png)

以下是操作要点：

1.  **使用 `lvol` 模块**创建逻辑卷。需要指定 `vg`（卷组名）和 `lv`（逻辑卷名）和 `size`。
2.  **使用 `filesystem` 模块**格式化逻辑卷为 `ext4`。
3.  **实现错误处理**：使用 `block`, `rescue` 结构。
    *   `block` 中包含创建1500MB逻辑卷和格式化的任务。
    *   `rescue` 中处理失败情况：先输出错误信息，然后尝试创建800MB的逻辑卷并格式化。
4.  **进一步判断卷组是否存在**：可以使用 `ansible_facts` 中的 `ansible_lvm` 变量来检查 `research` 卷组是否存在，如果不存在，则在 `rescue` 中输出相应信息。

![](img/c04863d07827b97fe321287a5dbce5ab_417.png)

示例Playbook结构片段：
```yaml
- name: Create LV
  hosts: all
  tasks:
    - block:
        - name: Create 1500M LV
          lvol:
            vg: research
            lv: data
            size: 1500
        - name: Format LV as ext4
          filesystem:
            fstype: ext4
            dev: /dev/research/data
      rescue:
        - name: Output warning for size fallback
          debug:
            msg: “Could not create 1500M LV, falling back to 800M.”
        - name: Create 800M LV as fallback
          lvol:
            vg: research
            lv: data
            size: 800
          when: “‘research’ in ansible_lvm.vgs”
        - name: Format fallback LV
          filesystem:
            fstype: ext4
            dev: /dev/research/data
          when: “‘research’ in ansible_lvm.vgs”
        - debug:
            msg: “Volume group ‘research’ does not exist.”
          when: “‘research’ not in ansible_lvm.vgs”
```

![](img/c04863d07827b97fe321287a5dbce5ab_419.png)

![](img/c04863d07827b97fe321287a5dbce5ab_421.png)

**本节总结**：此题综合考察存储管理和Playbook的错误处理能力。重点是 `block/rescue` 的使用和基于 `ansible_facts` 的条件判断。

![](img/c04863d07827b97fe321287a5dbce5ab_423.png)

![](img/c04863d07827b97fe321287a5dbce5ab_425.png)

---

![](img/c04863d07827b97fe321287a5dbce5ab_427.png)

![](img/c04863d07827b97fe321287a5dbce5ab_428.png)

![](img/c04863d07827b97fe321287a5dbce5ab_429.png)

![](img/c04863d07827b97fe321287a5dbce5ab_431.png)

![](img/c04863d07827b97fe321287a5dbce5ab_433.png)

## 题目9：使用Ansible Vault管理加密变量

![](img/c04863d07827b97fe321287a5dbce5ab_435.png)

![](img/c04863d07827b97fe321287a5dbce5ab_436.png)

![](img/c04863d07827b97fe321287a5dbce5ab_438.png)

![](img/c04863d07827b97fe321287a5dbce5ab_440.png)

![](img/c04863d07827b97fe321287a5dbce5ab_441.png)

**任务概述**：创建加密的变量文件（密码库），并在Playbook中使用它来设置用户密码。

![](img/c04863d07827b97fe321287a5dbce5ab_443.png)

![](img/c04863d07827b97fe321287a5dbce5ab_445.png)

![](img/c04863d07827b97fe321287a5dbce5ab_447.png)

![](img/c04863d07827b97fe321287a5dbce5ab_449.png)

以下是操作步骤：

![](img/c04863d07827b97fe321287a5dbce5ab_450.png)

![](img/c04863d07827b97fe321287a5dbce5ab_451.png)

![](img/c04863d07827b97fe321287a5dbce5ab_452.png)

![](img/c04863d07827b97fe321287a5dbce5ab_453.png)

![](img/c04863d07827b97fe321287a5dbce5ab_454.png)

![](img/c04863d07827b97fe321287a5dbce5ab_455.png)

![](img/c04863d07827b97fe321287a5dbce5ab_456.png)

![](img/c04863d07827b97fe321287a5dbce5ab_457.png)

![](img/c04863d07827b97fe321287a5dbce5ab_458.png)

![](img/c04863d07827b97fe321287a5dbce5ab_459.png)

![](img/c04863d07827b97fe321287a5dbce5ab_460.png)

1.  **创建存储变量的YAML文件**，例如 `locker.yml`：
    ```yaml
    ---
    pw_developer: redhat123
    pw_manager: redhat456
    ```

![](img/c04863d07827b97fe321287a5dbce5ab_461.png)

![](img/c04863d07827b97fe321287a5dbce5ab_463.png)

![](img/c04863d07827b97fe321287a5dbce5ab_465.png)

![](img/c04863d07827b97fe321287a5dbce5ab_467.png)

![](img/c04863d07827b97fe321287a5dbce5ab_469.png)

![](img/c04863d07827b97fe321287a5dbce5ab_471.png)

![](img/c04863d07827b97fe321287a5dbce5ab_473.png)

2.  **配置Ansible Vault密码文件**。编辑 `ansible.cfg`，指定 `vault_password_file` 路径，例如：
    ```ini
    vault_password_file = /home/grader/ansible/vault_pass.txt
    ```
    并创建该密码文件，内容就是加密密码（如 `redhat`）。

![](img/c04863d07827b97fe321287a5dbce5ab_475.png)

![](img/c04863d07827b97fe321287a5dbce5ab_477.png)

![](img/c04863d07827b97fe321287a5dbce5ab_479.png)

![](img/c04863d07827b97fe321287a5dbce5ab_480.png)

![](img/c04863d07827b97fe321287a5dbce5ab_481.png)

![](img/c04863d07827b97fe321287a5dbce5ab_482.png)

![](img/c04863d07827b97fe321287a5dbce5ab_483.png)

3.  **加密变量文件**：
    ```bash
    cd ~/ansible
    ansible-vault encrypt locker.yml
    ```
    输入加密密码（与密码文件一致，即 `redhat`）。

4.  **在Playbook中使用加密变量**。例如，创建用户时：
    ```yaml
    - name: Create developer users
      hosts: dev,test
      vars_files:
        - locker.yml # 直接引用，Ansible会自动用配置的密码解密
      tasks:
        - name: Create user bob with hashed password
          user:
            name: bob
            password: “{{ pw_developer | password_hash(‘sha512’, ‘mysecretsalt’) }}”
    ```
    **注意**：`sha512` 必须小写，大写会导致错误。

![](img/c04863d07827b97fe321287a5dbce5ab_485.png)

![](img/c04863d07827b97fe321287a5dbce5ab_487.png)

![](img/c04863d07827b97fe321287a5dbce5ab_489.png)

5.  **更改Vault密码**（如果题目要求）：
    ```bash
    ansible-vault rekey locker.yml
    ```
    依次输入旧密码和新密码。

![](img/c04863d07827b97fe321287a5dbce5ab_491.png)

**本节总结**：此题考察敏感数据管理。核心是配置 `vault_password_file` 实现自动解密，以及在Playbook中通过 `vars_files` 引用加密文件，并使用 `password_hash` 过滤器生成加密密码。

![](img/c04863d07827b97fe321287a5dbce5ab_493.png)

![](img/c04863d07827b97fe321287a5dbce5ab_495.png)

---

![](img/c04863d07827b97fe321287a5dbce5ab_497.png)

![](img/c04863d07827b97fe321287a5dbce5ab_498.png)

## 课程总结与考试建议

在本课程中，我们一起学习了RHCE 8版本考试的全套内容。从环境配置、基础操作，到角色管理、变量加密等高级主题，我们不仅讲解了技术点，更分享了许多提高效率和通过率的实战技巧。

![](img/c04863d07827b97fe321287a5dbce5ab_500.png)

![](img/c04863d07827b97fe321287a5dbce5ab_501.png)

![](img/c04863d07827b97fe321287a5dbce5ab_502.png)

![](img/c04863d07827b97fe321287a5dbce5ab_503.png)

![](img/c04863d07827b97fe321287a5dbce5ab_504.png)

![](img/c04863d07827b97fe321287a5dbce5ab_505.png)

![](img/c04863d07827b97fe321287a5dbce5ab_507.png)

![](img/c04863d07827b97fe321287a5dbce5ab_509.png)

![](img/c04863d07827b97fe321287a5dbce5ab_511.png)

**RHCE 8考试特点总结**：
1.  **核心聚焦**：深度考核Ansible自动化，而非广度的服务管理。
2.  **结果导向**：红帽判分主要看最终系统状态是否符合要求，对实现过程限制较少。
3.  **记忆与熟练度**：相较于早期版本对排错和理解的高要求，RHCE 8更侧重于对Ansible模块、语法和题目要求的熟悉程度，通过反复练习可以显著提升通过率。

![](img/c04863d07827b97fe321287a5dbce5ab_513.png)

**最终考试建议**：
*   **严格遵循目录和用户要求**：这是最重要的失分点。
*   **善用复制粘贴**：减少手动输入错误，提高速度。
*   **合理分配时间**：对于极少数复杂题目（如需要复杂变量循环和错误处理的用户创建题），如果时间紧张，可以确保基础部分满分后，适当取舍。
*   **考前充分练习**：确保将提供的题目练习到3小时内完成，留出充足的检查和应急时间。

![](img/c04863d07827b97fe321287a5dbce5ab_515.png)

![](img/c04863d07827b97fe321287a5dbce5ab_517.png)

![](img/c04863d07827b97fe321287a5dbce5ab_519.png)

![](img/c04863d07827b97fe321287a5dbce5ab_521.png)

希望本课程能帮助您顺利通过RHCE 8认证考试。祝您考试成功！