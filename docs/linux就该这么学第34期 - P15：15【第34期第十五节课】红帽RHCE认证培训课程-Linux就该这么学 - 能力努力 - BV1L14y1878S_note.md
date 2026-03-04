# Linux就该这么学：34：15【红帽RHCE认证培训课程 - Ansible自动化运维入门】🚀

![](img/f10852f89651a611742288a6d379d3a4_1.png)

![](img/f10852f89651a611742288a6d379d3a4_3.png)

![](img/f10852f89651a611742288a6d379d3a4_5.png)

在本节课中，我们将要学习红帽RHCE认证考试中关于Ansible自动化运维的核心内容。课程将从考试环境准备、考题结构解析开始，逐步深入到Ansible的安装、配置、模块使用、Playbook编写以及角色管理等核心概念。我们将通过详细的步骤和示例，确保初学者能够理解并掌握Ansible自动化运维的基础知识和考试要点。

![](img/f10852f89651a611742288a6d379d3a4_6.png)

![](img/f10852f89651a611742288a6d379d3a4_7.png)

![](img/f10852f89651a611742288a6d379d3a4_9.png)

![](img/f10852f89651a611742288a6d379d3a4_10.png)

![](img/f10852f89651a611742288a6d379d3a4_11.png)

---

![](img/f10852f89651a611742288a6d379d3a4_13.png)

![](img/f10852f89651a611742288a6d379d3a4_14.png)

![](img/f10852f89651a611742288a6d379d3a4_16.png)

![](img/f10852f89651a611742288a6d379d3a4_17.png)

## 课程概述与考试环境准备

![](img/f10852f89651a611742288a6d379d3a4_19.png)

![](img/f10852f89651a611742288a6d379d3a4_21.png)

![](img/f10852f89651a611742288a6d379d3a4_23.png)

![](img/f10852f89651a611742288a6d379d3a4_24.png)

红帽RHCE认证考试在2020年8月1日后进行了重大改革，从传统的服务管理与搭建考核，转变为以Ansible为核心的自动化运维考核。虽然考试难度有所增加，但得益于稳定的题库，备考所需的时间和精力并未显著增加。

![](img/f10852f89651a611742288a6d379d3a4_26.png)

![](img/f10852f89651a611742288a6d379d3a4_27.png)

![](img/f10852f89651a611742288a6d379d3a4_28.png)

![](img/f10852f89651a611742288a6d379d3a4_30.png)

![](img/f10852f89651a611742288a6d379d3a4_31.png)

![](img/f10852f89651a611742288a6d379d3a4_32.png)

由于全国疫情的影响，原定的考试时间需要推迟。预计在5月中下旬为大家预约考试，时间大约在6月中下旬，具体视政策放宽情况而定。但这并不影响我们今天开始学习备考内容。

![](img/f10852f89651a611742288a6d379d3a4_34.png)

![](img/f10852f89651a611742288a6d379d3a4_35.png)

![](img/f10852f89651a611742288a6d379d3a4_36.png)

### 实验环境下载与配置

![](img/f10852f89651a611742288a6d379d3a4_38.png)

首先，需要从课程网站下载实验环境。该环境是一个大小约为10GB的虚拟机软件包，包含了考试所需的所有服务。

![](img/f10852f89651a611742288a6d379d3a4_40.png)

下载完成后，打开虚拟机环境。由于打包环境的电脑配置可能与当前电脑不同，在还原虚拟机快照时，有一定概率会出现报错。如果遇到报错，请按以下步骤解决：

![](img/f10852f89651a611742288a6d379d3a4_42.png)

![](img/f10852f89651a611742288a6d379d3a4_43.png)

![](img/f10852f89651a611742288a6d379d3a4_45.png)

1.  点击“编辑虚拟机设置”。
2.  检查并调整内存（建议8GB以上）和处理器设置。处理器核心数量应根据您电脑的实际配置进行设置（例如，若为16核，则设置为16）。
3.  取消勾选“虚拟化Intel VT-x/EPT或AMD-V/RVI”和“虚拟化CPU性能计数器”选项。
4.  确认后再次尝试启动虚拟机。

![](img/f10852f89651a611742288a6d379d3a4_47.png)

![](img/f10852f89651a611742288a6d379d3a4_48.png)

![](img/f10852f89651a611742288a6d379d3a4_49.png)

![](img/f10852f89651a611742288a6d379d3a4_51.png)

如果上述方法无效，可能需要进入救援模式重置密码。具体操作已整理成详细文档《考试环境不能启动的解决方法》，可在学员群文件中下载。

![](img/f10852f89651a611742288a6d379d3a4_53.png)

![](img/f10852f89651a611742288a6d379d3a4_54.png)

![](img/f10852f89651a611742288a6d379d3a4_56.png)

**硬件配置要求**：为了流畅运行嵌套了6台虚拟机的实验环境，建议真机处理器为i5以上，内存为8GB以上。

### 进入考试模拟环境

成功启动实验环境后，使用root用户登录，密码在模拟环境中为 `redhat`（请注意，考试时以考题网页提示的密码为准）。

![](img/f10852f89651a611742288a6d379d3a4_58.png)

![](img/f10852f89651a611742288a6d379d3a4_59.png)

![](img/f10852f89651a611742288a6d379d3a4_61.png)

登录后，需要启动其余的5台受控虚拟机。使用以下命令查看并启动所有主机：

```bash
virsh list --all
virsh start <虚拟机名称>
```

依次启动所有虚拟机后，即可看到完整的红帽考题界面。考题也可以在学员页面的“RHCE考题”板块或教材第十六章节中找到。

**重要提示**：今天课程中关于环境启动和配置的操作，在正式考试中均由考官提前完成，考生无需操作。

---

![](img/f10852f89651a611742288a6d379d3a4_63.png)

![](img/f10852f89651a611742288a6d379d3a4_65.png)

![](img/f10852f89651a611742288a6d379d3a4_66.png)

## 考题结构与核心规则解析

![](img/f10852f89651a611742288a6d379d3a4_68.png)

![](img/f10852f89651a611742288a6d379d3a4_69.png)

![](img/f10852f89651a611742288a6d379d3a4_70.png)

![](img/f10852f89651a611742288a6d379d3a4_71.png)

![](img/f10852f89651a611742288a6d379d3a4_73.png)

![](img/f10852f89651a611742288a6d379d3a4_75.png)

在深入具体题目之前，我们必须充分理解考题说明，这直接关系到考试成绩。

![](img/f10852f89651a611742288a6d379d3a4_76.png)

![](img/f10852f89651a611742288a6d379d3a4_78.png)

![](img/f10852f89651a611742288a6d379d3a4_79.png)

![](img/f10852f89651a611742288a6d379d3a4_80.png)

![](img/f10852f89651a611742288a6d379d3a4_82.png)

![](img/f10852f89651a611742288a6d379d3a4_83.png)

### 考试环境与权限说明

*   **实验架构**：你面前只有一台物理机（控制节点），其中运行着6台虚拟机。你**没有**物理机的root权限，但对所有6台虚拟机拥有完整的root访问权限。
*   **网络与主机信息**：所有虚拟机的IP地址、主机名、静态网络配置和DNS解析均已预先设置好，**请勿修改**。建议将以下主机信息抄写在纸上，方便查阅：
    *   `control.example.com` (控制节点): `172.25.250.254`
    *   `dev.example.com`: `172.25.250.9`
    *   `test.example.com`: `172.25.250.10`
    *   `prod1.example.com`: `172.25.250.11`
    *   `prod2.example.com`: `172.25.250.12`
    *   `balancers.example.com`: `172.25.250.13`
*   **主机分组**：考题中会使用组名来指代多台主机，其对应关系为：
    *   `dev` 组: `dev.example.com`
    *   `test` 组: `test.example.com`
    *   `prod` 组: `prod1.example.com`, `prod2.example.com` (注意：`prod`组同时也是`webserver`组的成员)
    *   `balancers` 组: `balancers.example.com`
*   **SSH密钥**：所有主机之间已配置好SSH密钥认证，可以直接免密登录，**请勿修改**。

### 核心操作规则（必须遵守）

这是考试中最重要的部分，用黄色高亮标出，强调了两次：

![](img/f10852f89651a611742288a6d379d3a4_85.png)

![](img/f10852f89651a611742288a6d379d3a4_86.png)

1.  **执行用户**：除非特别说明，**所有**Ansible工作都必须在控制节点上，使用 `grader` 用户（考试中固定为此用户名）来执行。虽然你知道root密码，但不要使用root用户直接操作。
2.  **工作目录**：所有创建的Ansible配置文件、Playbook、角色等，都必须存放在 `/home/grader/ansible` 目录下。
3.  **判分机制**：考试采用脚本自动判分。脚本会到固定的目录寻找你的作业成果。如果文件没有放在指定位置，即使内容正确也无法得分。
4.  **其他服务状态**：考试中，防火墙（firewalld和iptables）默认是**禁用**的，SELinux处于强制模式，但通常不影响Ansible操作。软件仓库已由考官配置好，你只需要在需要时正确指向它们。

![](img/f10852f89651a611742288a6d379d3a4_88.png)

**考试时间与难度**：考试时长为4小时，时间非常充裕。目标是210分（满分300分）即可通过。考题平均每题约20分。

![](img/f10852f89651a611742288a6d379d3a4_90.png)

![](img/f10852f89651a611742288a6d379d3a4_91.png)

![](img/f10852f89651a611742288a6d379d3a4_92.png)

![](img/f10852f89651a611742288a6d379d3a4_94.png)

![](img/f10852f89651a611742288a6d379d3a4_95.png)

![](img/f10852f89651a611742288a6d379d3a4_97.png)

---

![](img/f10852f89651a611742288a6d379d3a4_98.png)

![](img/f10852f89651a611742288a6d379d3a4_99.png)

![](img/f10852f89651a611742288a6d379d3a4_101.png)

![](img/f10852f89651a611742288a6d379d3a4_102.png)

![](img/f10852f89651a611742288a6d379d3a4_104.png)

## 题目详解与实践操作

![](img/f10852f89651a611742288a6d379d3a4_105.png)

![](img/f10852f89651a611742288a6d379d3a4_106.png)

![](img/f10852f89651a611742288a6d379d3a4_107.png)

![](img/f10852f89651a611742288a6d379d3a4_109.png)

![](img/f10852f89651a611742288a6d379d3a4_111.png)

上一节我们解析了考试的整体规则和框架，本节中我们来看看具体的考题如何解答。我们将按照考题顺序，逐一讲解并演示操作。

![](img/f10852f89651a611742288a6d379d3a4_113.png)

![](img/f10852f89651a611742288a6d379d3a4_115.png)

![](img/f10852f89651a611742288a6d379d3a4_116.png)

![](img/f10852f89651a611742288a6d379d3a4_117.png)

![](img/f10852f89651a611742288a6d379d3a4_118.png)

### 题目1：安装和配置Ansible

![](img/f10852f89651a611742288a6d379d3a4_119.png)

![](img/f10852f89651a611742288a6d379d3a4_121.png)

**任务**：在控制节点上安装Ansible软件包。

![](img/f10852f89651a611742288a6d379d3a4_123.png)

**操作步骤**：
1.  首先，使用root用户SSH登录到控制节点（`172.25.250.254`），密码为 `redhat`。
2.  执行安装命令。由于软件仓库已配置好，直接安装即可。
    ```bash
    ssh root@172.25.250.254
    # 密码: redhat
    dnf install -y ansible
    ```
3.  安装完成后，退出root会话。**后续所有操作都必须切换回 `grader` 用户**。
    ```bash
    exit
    ssh grader@172.25.250.254
    # 初始密码可能需要重置，模拟环境中请根据实际情况操作
    ```

![](img/f10852f89651a611742288a6d379d3a4_125.png)

![](img/f10852f89651a611742288a6d379d3a4_126.png)

![](img/f10852f89651a611742288a6d379d3a4_127.png)

![](img/f10852f89651a611742288a6d379d3a4_129.png)

![](img/f10852f89651a611742288a6d379d3a4_130.png)

![](img/f10852f89651a611742288a6d379d3a4_131.png)

![](img/f10852f89651a611742288a6d379d3a4_132.png)

**任务**：创建符合要求的主机清单文件。

![](img/f10852f89651a611742288a6d379d3a4_134.png)

![](img/f10852f89651a611742288a6d379d3a4_135.png)

![](img/f10852f89651a611742288a6d379d3a4_136.png)

![](img/f10852f89651a611742288a6d379d3a4_137.png)

主机清单（inventory）文件用于定义Ansible管理哪些受控节点。

![](img/f10852f89651a611742288a6d379d3a4_139.png)

![](img/f10852f89651a611742288a6d379d3a4_140.png)

![](img/f10852f89651a611742288a6d379d3a4_141.png)

![](img/f10852f89651a611742288a6d379d3a4_142.png)

![](img/f10852f89651a611742288a6d379d3a4_143.png)

![](img/f10852f89651a611742288a6d379d3a4_145.png)

![](img/f10852f89651a611742288a6d379d3a4_146.png)

![](img/f10852f89651a611742288a6d379d3a4_147.png)

以下是需要完成的要求：
*   创建文件 `/home/grader/ansible/inventory`。
*   `dev.example.com` 是 `dev` 组的成员。
*   `test.example.com` 是 `test` 组的成员。
*   `prod1.example.com` 和 `prod2.example.com` 是 `prod` 组的成员。
*   `prod` 组是 `webserver` 组的成员。
*   `balancers.example.com` 是 `balancers` 组的成员。

![](img/f10852f89651a611742288a6d379d3a4_149.png)

![](img/f10852f89651a611742288a6d379d3a4_150.png)

**操作步骤**：
1.  确保工作目录存在。
    ```bash
    mkdir -p /home/grader/ansible
    cd /home/grader/ansible
    ```
2.  编辑清单文件。
    ```bash
    vim inventory
    ```
3.  写入以下内容，注意组和层级的语法：
    ```ini
    [dev]
    dev.example.com

    [test]
    test.example.com

    [prod]
    prod1.example.com
    prod2.example.com

    [webserver:children]
    prod

    [balancers]
    balancers.example.com

    [all:vars]
    ansible_user=root
    ansible_password=redhat
    ```
    *   `[group_name]` 定义主机组。
    *   `[groupA:children]` 表示 `groupA` 包含其他组（如 `prod`）。
    *   `[all:vars]` 为所有主机定义变量，这里设置了连接用的用户名和密码（模拟环境密码为`redhat`，考试时以考题为准）。

![](img/f10852f89651a611742288a6d379d3a4_151.png)

![](img/f10852f89651a611742288a6d379d3a4_153.png)

![](img/f10852f89651a611742288a6d379d3a4_154.png)

![](img/f10852f89651a611742288a6d379d3a4_155.png)

![](img/f10852f89651a611742288a6d379d3a4_156.png)

![](img/f10852f89651a611742288a6d379d3a4_157.png)

![](img/f10852f89651a611742288a6d379d3a4_158.png)

![](img/f10852f89651a611742288a6d379d3a4_159.png)

![](img/f10852f89651a611742288a6d379d3a4_161.png)

![](img/f10852f89651a611742288a6d379d3a4_162.png)

**任务**：创建Ansible配置文件。

![](img/f10852f89651a611742288a6d379d3a4_164.png)

![](img/f10852f89651a611742288a6d379d3a4_166.png)

![](img/f10852f89651a611742288a6d379d3a4_167.png)

需要创建配置文件 `/home/grader/ansible/ansible.cfg`，并满足：
*   指定主机清单文件为 `inventory`。
*   指定角色搜索路径包含 `/home/grader/ansible/roles`。

![](img/f10852f89651a611742288a6d379d3a4_169.png)

![](img/f10852f89651a611742288a6d379d3a4_170.png)

![](img/f10852f89651a611742288a6d379d3a4_171.png)

![](img/f10852f89651a611742288a6d379d3a4_172.png)

![](img/f10852f89651a611742288a6d379d3a4_174.png)

![](img/f10852f89651a611742288a6d379d3a4_175.png)

**操作步骤**：
1.  从系统默认配置复制一个模板并进行修改。
    ```bash
    cp /etc/ansible/ansible.cfg /home/grader/ansible/
    cd /home/grader/ansible
    vim ansible.cfg
    ```
2.  修改以下行（行号可能与实际略有出入，以查找关键字为准）：
    ```ini
    inventory      = ./inventory
    roles_path    = /home/grader/ansible/roles
    ```
3.  （加分项）建议额外修改以下参数，使连接更稳定：
    ```ini
    host_key_checking = False  # 首次连接不检查主机密钥
    remote_user = root         # 远程执行用户为root
    ```

![](img/f10852f89651a611742288a6d379d3a4_177.png)

![](img/f10852f89651a611742288a6d379d3a4_178.png)

![](img/f10852f89651a611742288a6d379d3a4_179.png)

![](img/f10852f89651a611742288a6d379d3a4_180.png)

![](img/f10852f89651a611742288a6d379d3a4_182.png)

**验证**：使用ad-hoc命令测试配置是否正确。
```bash
# 测试所有节点是否可达
ansible all -m ping

# 查看生效的Ansible配置（确认使用的是当前目录下的ansible.cfg）
ansible --version
```
如果ping模块返回绿色的`SUCCESS`，说明Ansible基础配置成功。

![](img/f10852f89651a611742288a6d379d3a4_184.png)

![](img/f10852f89651a611742288a6d379d3a4_186.png)

![](img/f10852f89651a611742288a6d379d3a4_187.png)

![](img/f10852f89651a611742288a6d379d3a4_188.png)

![](img/f10852f89651a611742288a6d379d3a4_189.png)

---

![](img/f10852f89651a611742288a6d379d3a4_191.png)

![](img/f10852f89651a611742288a6d379d3a4_192.png)

![](img/f10852f89651a611742288a6d379d3a4_193.png)

![](img/f10852f89651a611742288a6d379d3a4_194.png)

![](img/f10852f89651a611742288a6d379d3a4_195.png)

![](img/f10852f89651a611742288a6d379d3a4_197.png)

### 题目2：创建和运行临时命令

![](img/f10852f89651a611742288a6d379d3a4_199.png)

![](img/f10852f89651a611742288a6d379d3a4_200.png)

**任务**：在所有受控节点上配置两个YUM仓库。

![](img/f10852f89651a611742288a6d379d3a4_202.png)

![](img/f10852f89651a611742288a6d379d3a4_203.png)

![](img/f10852f89651a611742288a6d379d3a4_204.png)

![](img/f10852f89651a611742288a6d379d3a4_206.png)

我们需要编写一个Shell脚本，该脚本中包含Ansible的ad-hoc命令，用于在所有节点上创建仓库配置文件。

![](img/f10852f89651a611742288a6d379d3a4_208.png)

![](img/f10852f89651a611742288a6d379d3a4_209.png)

![](img/f10852f89651a611742288a6d379d3a4_210.png)

![](img/f10852f89651a611742288a6d379d3a4_211.png)

![](img/f10852f89651a611742288a6d379d3a4_213.png)

以下是仓库的具体要求：

![](img/f10852f89651a611742288a6d379d3a4_215.png)

![](img/f10852f89651a611742288a6d379d3a4_217.png)

![](img/f10852f89651a611742288a6d379d3a4_219.png)

**仓库1**：
*   名称：`EX294_BASE`
*   描述：`EX294 base software`
*   Base URL：`http://content.example.com/rhel8.0/x86_64/dvd/BaseOS`
*   GPG签名检查：启用
*   GPG密钥URL：`http://content.example.com/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release`
*   状态：启用

**仓库2**：
*   名称：`EX294_STREAM`
*   描述：`EX294 stream software`
*   Base URL：`http://content.example.com/rhel8.0/x86_64/dvd/AppStream`
*   GPG签名检查：启用
*   GPG密钥URL：`http://content.example.com/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release`
*   状态：启用

![](img/f10852f89651a611742288a6d379d3a4_221.png)

**操作步骤**：
1.  **查找模块**：首先确定使用哪个Ansible模块。我们需要管理YUM仓库，使用 `ansible-doc -l | grep yum` 过滤，发现 `yum_repository` 模块符合要求。
2.  **查看模块用法**：`ansible-doc yum_repository` 查看该模块的参数和示例。
3.  **编写脚本**：创建要求的脚本文件。
    ```bash
    cd /home/grader/ansible
    vim scripts/ansible-config.sh
    ```
4.  在脚本中写入以下内容。注意，脚本需要有执行权限，并且第一行是shebang声明。
    ```bash
    #!/bin/bash
    # 配置第一个仓库
    ansible all -m yum_repository -a \
    "name=EX294_BASE \
    description='EX294 base software' \
    baseurl=http://content.example.com/rhel8.0/x86_64/dvd/BaseOS \
    gpgcheck=yes \
    gpgkey=http://content.example.com/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release \
    enabled=yes"

    # 配置第二个仓库
    ansible all -m yum_repository -a \
    "name=EX294_STREAM \
    description='EX294 stream software' \
    baseurl=http://content.example.com/rhel8.0/x86_64/dvd/AppStream \
    gpgcheck=yes \
    gpgkey=http://content.example.com/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release \
    enabled=yes"
    ```
5.  给脚本添加执行权限并运行。
    ```bash
    chmod +x scripts/ansible-config.sh
    ./scripts/ansible-config.sh
    ```
6.  **验证**：可以登录任意一台受控节点（如`dev.example.com`），检查 `/etc/yum.repos.d/` 目录下是否生成了对应的repo文件，并且内容正确。
    ```bash
    ssh root@172.25.250.9
    cat /etc/yum.repos.d/EX294_BASE.repo
    ```

![](img/f10852f89651a611742288a6d379d3a4_223.png)

![](img/f10852f89651a611742288a6d379d3a4_225.png)

---

![](img/f10852f89651a611742288a6d379d3a4_227.png)

![](img/f10852f89651a611742288a6d379d3a4_228.png)

![](img/f10852f89651a611742288a6d379d3a4_229.png)

![](img/f10852f89651a611742288a6d379d3a4_230.png)

![](img/f10852f89651a611742288a6d379d3a4_231.png)

![](img/f10852f89651a611742288a6d379d3a4_233.png)

### 题目3：安装软件包

**任务**：创建一个Playbook，在指定主机组上安装和更新软件包。

![](img/f10852f89651a611742288a6d379d3a4_235.png)

![](img/f10852f89651a611742288a6d379d3a4_236.png)

![](img/f10852f89651a611742288a6d379d3a4_238.png)

![](img/f10852f89651a611742288a6d379d3a4_239.png)

Playbook是Ansible的脚本，使用YAML格式编写，定义了要在远程主机上执行的一系列任务。

![](img/f10852f89651a611742288a6d379d3a4_241.png)

![](img/f10852f89651a611742288a6d379d3a4_242.png)

![](img/f10852f89651a611742288a6d379d3a4_244.png)

![](img/f10852f89651a611742288a6d379d3a4_246.png)

![](img/f10852f89651a611742288a6d379d3a4_247.png)

![](img/f10852f89651a611742288a6d379d3a4_248.png)

具体要求：
1.  在 `dev`、`test` 和 `prod` 组的主机上，安装最新版的 `php` 和 `mariadb` 软件包。
2.  在 `dev` 组的主机上，安装软件包组 `RPM Development Tools`。
3.  在 `dev` 组的主机上，将所有软件包更新到最新版本。

![](img/f10852f89651a611742288a6d379d3a4_249.png)

![](img/f10852f89651a611742288a6d379d3a4_250.png)

![](img/f10852f89651a611742288a6d379d3a4_251.png)

![](img/f10852f89651a611742288a6d379d3a4_253.png)

![](img/f10852f89651a611742288a6d379d3a4_254.png)

**操作步骤**：
1.  创建Playbook文件。
    ```bash
    cd /home/grader/ansible
    vim playbooks/install-packages.yml
    ```
2.  编写Playbook内容。YAML格式对缩进（空格）非常敏感，必须精确对齐。
    ```yaml
    ---
    - name: Install and update packages
      hosts: dev,test,prod
      tasks:
        - name: Install latest PHP
          yum:
            name: php
            state: latest

        - name: Install latest MariaDB
          yum:
            name: mariadb
            state: latest

    - name: Handle dev group specific tasks
      hosts: dev
      tasks:
        - name: Install RPM Development Tools group
          yum:
            name: "@RPM Development Tools"
            state: present

        - name: Upgrade all packages to latest
          yum:
            name: "*"
            state: latest
    ```
    *   `---` 是YAML文件的开始标记。
    *   每个 `- name:` 开始一个“play”或“task”。
    *   `hosts:` 指定该play在哪些主机组上执行。
    *   `tasks:` 下列出要执行的任务列表。
    *   `yum:` 是调用yum模块。
    *   `name:` 是模块参数，指定软件包名。`@`开头表示软件包组。
    *   `state: latest` 确保安装最新版。`state: present` 确保安装。

![](img/f10852f89651a611742288a6d379d3a4_256.png)

![](img/f10852f89651a611742288a6d379d3a4_257.png)

![](img/f10852f89651a611742288a6d379d3a4_258.png)

![](img/f10852f89651a611742288a6d379d3a4_259.png)

![](img/f10852f89651a611742288a6d379d3a4_260.png)

3.  **运行Playbook进行验证**。
    ```bash
    ansible-playbook playbooks/install-packages.yml
    ```
    观察输出，绿色或黄色表示成功（黄色表示有变更），红色表示失败。

![](img/f10852f89651a611742288a6d379d3a4_262.png)

![](img/f10852f89651a611742288a6d379d3a4_263.png)

![](img/f10852f89651a611742288a6d379d3a4_264.png)

![](img/f10852f89651a611742288a6d379d3a4_266.png)

![](img/f10852f89651a611742288a6d379d3a4_267.png)

![](img/f10852f89651a611742288a6d379d3a4_268.png)

![](img/f10852f89651a611742288a6d379d3a4_269.png)

![](img/f10852f89651a611742288a6d379d3a4_270.png)

![](img/f10852f89651a611742288a6d379d3a4_271.png)

---

![](img/f10852f89651a611742288a6d379d3a4_273.png)

### 题目4：使用RHEL系统角色

![](img/f10852f89651a611742288a6d379d3a4_275.png)

![](img/f10852f89651a611742288a6d379d3a4_277.png)

![](img/f10852f89651a611742288a6d379d3a4_279.png)

**任务**：使用RHEL系统自带的角色，配置所有受控节点的时间同步。

![](img/f10852f89651a611742288a6d379d3a4_280.png)

![](img/f10852f89651a611742288a6d379d3a4_282.png)

![](img/f10852f89651a611742288a6d379d3a4_283.png)

Ansible角色（Role）是一种更高层次的抽象，它将相关的变量、任务、文件等组合在一起，便于复用。RHEL提供了一些预定义的系统角色。

![](img/f10852f89651a611742288a6d379d3a4_285.png)

**操作步骤**：
1.  **安装系统角色包**：首先需要在控制节点上安装包含系统角色的软件包。这需要root权限，操作后请切换回grader用户。
    ```bash
    # 切换到root用户安装
    sudo -i
    dnf install -y rhel-system-roles
    exit
    ```
2.  **寻找角色模板**：系统角色安装后，其示例Playbook通常位于 `/usr/share/doc/rhel-system-roles/` 目录下。我们需要时间同步角色 `timesync`。
    ```bash
    find /usr/share/doc -name "*timesync*" -type f
    # 通常可以找到一个示例playbook，如 /usr/share/doc/rhel-system-roles/timesync/example-timesync-playbook.yml
    ```
3.  **复制并修改Playbook**：将示例Playbook复制到我们的工作目录并进行修改。
    ```bash
    cd /home/grader/ansible
    cp /usr/share/doc/rhel-system-roles/timesync/example-timesync-playbook.yml playbooks/timesync.yml
    vim playbooks/timesync.yml
    ```
4.  修改Playbook，主要调整 `hosts` 和目标NTP服务器。参考考题要求，假设NTP服务器为 `172.25.250.254`。
    ```yaml
    ---
    - name: Configure time synchronization
      hosts: all  # 改为对所有节点生效
      vars:
        timesync_ntp_servers:
          - hostname: 172.25.250.254  # 修改为考题指定的NTP服务器
            iburst: yes
      roles:
        - role: rhel-system-roles.timesync
          become: true
    ```
5.  **配置角色路径**：为了让Ansible能找到系统角色，需要在 `ansible.cfg` 中正确设置 `roles_path`（在题目1中已完成）。
6.  **运行Playbook**：
    ```bash
    ansible-playbook playbooks/timesync.yml
    ```
7.  **验证**：登录受控节点，检查chronyd服务状态和配置。
    ```bash
    ssh root@172.25.250.9
    systemctl status chronyd
    cat /etc/chrony.conf
    ```

![](img/f10852f89651a611742288a6d379d3a4_287.png)

![](img/f10852f89651a611742288a6d379d3a4_289.png)

![](img/f10852f89651a611742288a6d379d3a4_290.png)

![](img/f10852f89651a611742288a6d379d3a4_291.png)

![](img/f10852f89651a611742288a6d379d3a4_292.png)

---

![](img/f10852f89651a611742288a6d379d3a4_294.png)

![](img/f10852f89651a611742288a6d379d3a4_295.png)

![](img/f10852f89651a611742288a6d379d3a4_296.png)

![](img/f10852f89651a611742288a6d379d3a4_298.png)

![](img/f10852f89651a611742288a6d379d3a4_299.png)

### 题目5：从Ansible Galaxy获取角色

![](img/f10852f89651a611742288a6d379d3a4_301.png)

![](img/f10852f89651a611742288a6d379d3a4_303.png)

![](img/f10852f89651a611742288a6d379d3a4_305.png)

**任务**：从Ansible Galaxy（官方角色社区）下载并安装指定的角色。

![](img/f10852f89651a611742288a6d379d3a4_307.png)

![](img/f10852f89651a611742288a6d379d3a4_309.png)

我们需要创建一个需求文件 `roles/requirements.yml`，定义要从Galaxy下载的角色。

![](img/f10852f89651a611742288a6d379d3a4_311.png)

![](img/f10852f89651a611742288a6d379d3a4_312.png)

![](img/f10852f89651a611742288a6d379d3a4_313.png)

![](img/f10852f89651a611742288a6d379d3a4_315.png)

![](img/f10852f89651a611742288a6d379d3a4_317.png)

**操作步骤**：
1.  创建需求文件。
    ```bash
    cd /home/grader/ansible
    vim roles/requirements.yml
    ```
2.  根据考题要求，填入角色来源。格式如下：
    ```yaml
    ---
    - src: https://github.com/username/role1.git
      name: my_role1

    - src: https://github.com/username/role2.git
      name: my_role2
    ```
    *   `src`：角色的Git仓库URL。
    *   `name`：安装到本地后的角色名称。
    *   **请将 `src` 和 `name` 替换为考题中给出的具体值**。
3.  **安装角色**：使用 `ansible-galaxy` 命令根据需求文件安装角色。
    ```bash
    ansible-galaxy install -r roles/requirements.yml -p roles/
    ```
    *   `-r`：指定需求文件。
    *   `-p`：指定角色安装路径。
4.  **验证**：查看角色是否已安装到指定目录。
    ```bash
    ls -la roles/
    ansible-galaxy list
    ```

![](img/f10852f89651a611742288a6d379d3a4_319.png)

![](img/f10852f89651a611742288a6d379d3a4_321.png)

![](img/f10852f89651a611742288a6d379d3a4_322.png)

![](img/f10852f89651a611742288a6d379d3a4_324.png)

![](img/f10852f89651a611742288a6d379d3a4_325.png)

![](img/f10852f89651a611742288a6d379d3a4_326.png)

---

![](img/f10852f89651a611742288a6d379d3a4_328.png)

![](img/f10852f89651a611742288a6d379d3a4_330.png)

![](img/f10852f89651a611742288a6d379d3a4_331.png)

![](img/f10852f89651a611742288a6d379d3a4_333.png)

![](img/f10852f89651a611742288a6d379d3a4_334.png)

### 题目6：创建和使用自定义角色

![](img/f10852f89651a611742288a6d379d3a4_336.png)

![](img/f10852f89651a611742288a6d379d3a4_337.png)

![](img/f10852f89651a611742288a6d379d3a4_338.png)

![](img/f10852f89651a611742288a6d379d3a4_339.png)

![](img/f10852f89651a611742288a6d379d3a4_341.png)

![](img/f10852f89651a611742288a6d379d3a4_342.png)

**任务**：手动创建一个名为 `apache` 的Ansible角色，实现以下功能：
1.  安装 `httpd` 软件包。
2.  启动并启用 `httpd` 服务。
3.  配置防火墙，放行HTTP服务。
4.  使用模板（Jinja2）部署一个首页文件，内容需显示主机的完全限定域名（FQDN）和IP地址。

![](img/f10852f89651a611742288a6d379d3a4_343.png)

![](img/f10852f89651a611742288a6d379d3a4_344.png)

![](img/f10852f89651a611742288a6d379d3a4_346.png)

**操作步骤**：
1.  **创建角色骨架**：使用 `ansible-galaxy init` 命令创建标准角色目录结构。
    ```bash
    cd /home/grader/ansible/roles
    ansible-galaxy init apache
    ```
2.  **编辑任务主文件**：角色任务定义在 `roles/apache/tasks/main.yml`。
    ```bash
    vim roles/apache/tasks/main.yml
    ```
    写入以下内容：
    ```yaml
    ---
    - name: Install Apache httpd
      yum:
        name: httpd
        state: present

    - name: Start and enable httpd service
      service:
        name: httpd
        state: started
        enabled: yes

    - name: Configure firewall to allow HTTP
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
3.  **创建模板文件**：Jinja2模板文件放在 `roles/apache/templates/` 目录下。
    ```bash
    vim roles/apache/templates/index.html.j2
    ```
    写入以下内容。`{{ }}` 内是Ansible变量，会在部署时被替换为具体主机的值。
    ```html
    Welcome to {{ ansible_fqdn }} on {{ ansible_default_ipv4.address }}
    ```
    **如何查找变量名？** 可以使用ad-hoc命令获取主机的所有事实（facts），并过滤：
    ```bash
    ansible all -m setup -a 'filter=*fqdn*'
    ansible all -m setup -a 'filter=*ipv4*'
    ```
    常用的变量有 `ansible_fqdn`（完全限定域名）和 `ansible_default_ipv4.address`（默认IPv4地址）。
4.  **使用角色**：创建一个Playbook来调用这个自定义角色。
    ```bash
    cd /home/grader/ansible
    vim playbooks/use-apache-role.yml
    ```
    写入：
    ```yaml
    ---
    - name: Apply Apache role to webserver group
      hosts: webserver
      roles:
        - apache
    ```
5.  **运行和验证**：
    ```bash
    ansible-playbook playbooks/use-apache-role.yml
    ```
    完成后，用浏览器或 `curl` 访问 `http://prod1.example.com`，应该能看到显示主机名和IP的欢迎页面。

![](img/f10852f89651a611742288a6d379d3a4_348.png)

![](img/f10852f89651a611742288a6d379d3a4_349.png)

---

![](img/f10852f89651a611742288a6d379d3a4_351.png)

![](img/f10852f89651a611742288a6d379d3a4_352.png)

![](img/f10852f89651a611742288a6d379d3a4_354.png)

![](img/f10852f89651a611742288a6d379d3a4_355.png)

![](img/f10852f89651a611742288a6d379d3a4_356.png)

### 题目7：使用角色部署Web集群

![](img/f10852f89651a611742288a6d379d3a4_358.png)

![](img/f10852f89651a611742288a6d379d3a4_359.png)

![](img/f10852f89651a611742288a6d379d3a4_360.png)

![](img/f10852f89651a611742288a6d379d3a4_361.png)

![](img/f10852f89651a611742288a6d379d3a4_363.png)

![](img/f10852f89651a611742288a6d379d3a4_364.png)

![](img/f10852f89651a611742288a6d379d3a4_365.png)

![](img/f10852f89651a611742288a6d379d3a4_366.png)

**任务**：创建一个Playbook，通过调用多个角色（包括之前下载或创建的角色），在 `balancers`、`prod` 组主机上部署一个负载均衡的Web服务，并在 `webserver` 组上部署Apache服务。

![](img/f10852f89651a611742288a6d379d3a4_368.png)

![](img/f10852f89651a611742288a6d379d3a4_370.png)

这道题是综合应用，通常题目描述较长，但实际是调用现成的角色。

![](img/f10852f89651a611742288a6d379d3a4_372.png)

**操作步骤**：
1.  **创建Playbook**：
    ```bash
    cd /home/grader/ansible
    vim playbooks/deploy-website.yml
    ```
2.  **编写Playbook**：根据考题描述，Playbook结构通常如下：
    ```yaml
    ---
    - name: Configure load balancer
      hosts: balancers
      roles:
        - role1_from_galaxy  # 替换为从Galaxy安装的负载均衡角色名

    - name: Configure backend web servers
      hosts: webserver
      roles:
        - role2_from_galaxy  # 替换为从Galaxy安装的PHP/Web角色名
        - apache             # 我们自定义的Apache角色
    ```
    **注意**：务必仔细阅读考题，将 `role1_from_galaxy` 和 `role2_from_galaxy` 替换为题目中给出的确切角色名称。
3.  **运行Playbook**：
    ```bash
    ansible-playbook playbooks/deploy-website.yml
    ```
4.  **验证**：按照考题提示，通过浏览器访问负载均衡器的地址（如 `http://balancers.example.com`），刷新页面，内容应在不同的后端服务器（`prod1`, `prod2`）之间切换，实现负载均衡效果。访问特定路径（如 `/hello`）应显示PHP信息。

![](img/f10852f89651a611742288a6d379d3a4_374.png)

![](img/f10852f89651a611742288a6d379d3a4_376.png)

![](img/f10852f89651a611742288a6d379d3a4_377.png)

---

![](img/f10852f89651a611742288a6d379d3a4_379.png)

![](img/f10852f89651a611742288a6d379d3a4_380.png)

![](img/f10852f89651a611742288a6d379d3a4_381.png)

### 题目8：创建和使用逻辑卷

![](img/f10852f89651a611742288a6d379d3a4_383.png)

![](img/f10852f89651a611742288a6d379d3a4_385.png)

![](img/f10852f89651a611742288a6d379d3a4_386.png)

![](img/f10852f89651a611742288a6d379d3a4_387.png)

**任务**：创建一个Playbook，在所有受控节点上，在名为 `research` 的卷组（VG）中创建逻辑卷（LV）。要求实现条件判断：
1.  如果 `research` VG有足够空间（>=1500MB），则创建大小为1500MB的LV `data`，并格式化为ext4。
2.  如果空间不足1500MB但>=800MB，则创建大小为800MB的LV `data`。
3.  如果空间不足800MB，或 `research` VG不存在，则输出相应的错误信息，并且不创建LV。

![](img/f10852f89651a611742288a6d379d3a4_389.png)

![](img/f10852f89651a611742288a6d379d3a4_390.png)

![](img/f10852f89651a611742288a6d379d3a4_391.png)

![](img/f10852f89651a611742288a6d379d3a4_393.png)

![](img/f10852f89651a611742288a6d379d3a4_394.png)

![](img/f10852f89651a611742288a6d379d3a4_395.png)

这道题考察Playbook中的错误处理（`block`, `rescue`）和条件判断（`when`）。

![](img/f10852f89651a611742288a6d379d3a4_397.png)

![](img/f10852f89651a611742288a6d379d3a4_399.png)

**操作步骤**：
1.  **创建Playbook**：
    ```bash
    cd /home/grader/ansible
    vim playbooks/create-lv.yml
    ```
2.  **编写Playbook**：这是一个复杂的Playbook，结构如下：
    ```yaml
    ---
    - name: Manage Logical Volume
      hosts: all
      tasks:
        - name: Attempt to create 1500MB LV and format
          block:
            - name: Create 1500MB LV
              lvol:
                vg: research
                lv: data
                size: 1500

            - name: Format LV as ext4
              filesystem:
                fstype: ext4
                dev: /dev/research/data

          rescue:
            - name: Check if research VG exists
              debug:
                msg: "Volume group research does not exist"
              when: "'research' not in ansible_lvm.vgs"

            - name: Attempt to create 800MB LV if VG exists
              block:
                - name: Create 800MB LV
                  lvol:
                    vg: research
                    lv: data
                    size: 800
              when: "'research' in ansible_lvm.vgs"

            - name: Notify insufficient space if VG exists but still fails
              debug:
                msg: "Volume group research exists but has insufficient space for an 800MB LV"
              when: "'research' in ansible_lvm.vgs"
    ```
    *   `block`：将多个任务组合成一个块。
    *   `rescue`：当`block`中的**任何**任务失败时，执行`rescue`中的任务。
    *   `when`：条件判断。`ansible_lvm.vgs` 是一个魔法变量（magic variable），包含了LVM卷组信息。
    *   `lvol`：管理LVM逻辑卷的模块。
    *   `filesystem`：格式化文件系统的模块。
3.  **运行与验证**：
    ```bash
    ansible-playbook playbooks/create-lv.yml
    ```
    由于实验环境中可能没有 `research` 卷组，运行后会触发`rescue`，并输出“Volume group research does not exist”的信息，这符合预期。在考试环境中，会根据实际的VG情况执行不同的分支。

---

![](img/f10852f89651a611742288a6d379d3a4_401.png)

## 课程总结

本节课中我们一起学习了红帽RHCE认证考试中Ansible自动化运维部分的核心内容。我们从考试环境搭建和规则解读入手，逐步完成了以下关键技能点的学习：

1.  **Ansible基础**：安装Ansible，配置主机清单（inventory）和主配置文件（ansible.cfg）。
2.  **Ad-hoc命令**：使用临时命令在多台主机上快速执行任务，如配置YUM仓库。
3.  **Playbook编写**：使用YAML语言编写自动化脚本，完成软件包安装、更新等复杂任务。
4.  **角色管理**：
    *   使用RHEL**系统自带角色**快速配置服务（如时间同步）。
    *   从**Ansible Galaxy**下载社区角色。
    *   **手动创建自定义角色**，将任务、变量、文件模板模块化，实现更清晰的代码组织和复用。
5.  **高级技巧**：在Playbook中实现**条件判断**（`when`）和**错误处理**（`block` / `rescue`），用于处理像逻辑卷创建这类需要根据不同情况执行不同操作的任务。

通过以上8道典型题目的详解与实操，你应该对RHCE考试中Ansible部分的题型、难度和解题思路有了清晰的认识。记住考试的核心原则：**使用指定用户、在指定目录工作、仔细阅读题目要求、善用`ansible-doc`和`ansible -m setup`命令查找帮助**。

![](img/f10852f89651a611742288a6d379d3a4_403.png)

接下来的课程将继续讲解剩余的考题，并最终使用判分脚本检验学习成果。请做好预习，巩固今天所学知识。