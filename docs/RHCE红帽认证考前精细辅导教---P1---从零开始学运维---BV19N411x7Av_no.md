# RHCE红帽认证考前精细辅导教程：P1：从零开始学运维 🚀

![](img/7974b820ac00cfc94007b114361b2dcf_0.png)

![](img/7974b820ac00cfc94007b114361b2dcf_2.png)

![](img/7974b820ac00cfc94007b114361b2dcf_4.png)

在本节课中，我们将要学习红帽认证（RHCE）考试的核心内容，包括RHCSA和RHCE的考试环境搭建、典型题目解析以及备考策略。课程将涵盖从基础网络配置到Ansible自动化运维的关键知识点，旨在帮助初学者系统性地掌握考试要点。

## 概述 📋

红帽认证是Linux运维领域极具含金量的专业认证。本教程将引导您从零开始，熟悉考试环境，掌握必备的Linux命令和Ansible自动化技能。我们将通过实际操作演示，确保您理解每一道考题的解决思路。

## 考试环境与基础配置 🖥️

![](img/7974b820ac00cfc94007b114361b2dcf_6.png)

![](img/7974b820ac00cfc94007b114361b2dcf_8.png)

上一节我们介绍了课程的整体目标，本节中我们来看看如何搭建和进入考试环境。

首先，您需要获取红帽官方提供的考试虚拟机环境。导入虚拟机后，请勿修改其默认配置（如内存、处理器），以免影响快照功能。

![](img/7974b820ac00cfc94007b114361b2dcf_10.png)

![](img/7974b820ac00cfc94007b114361b2dcf_12.png)

![](img/7974b820ac00cfc94007b114361b2dcf_14.png)

启动虚拟机后，点击左上角的“Active”按钮，打开内置的火狐浏览器。这里将显示考试的所有要求和题目信息。

![](img/7974b820ac00cfc94007b114361b2dcf_16.png)

![](img/7974b820ac00cfc94007b114361b2dcf_18.png)

![](img/7974b820ac00cfc94007b114361b2dcf_20.png)

![](img/7974b820ac00cfc94007b114361b2dcf_22.png)

![](img/7974b820ac00cfc94007b114361b2dcf_24.png)

**重要提示**：在开始答题前，请务必花5-10分钟仔细阅读所有考试要求和注意事项。虽然操作命令本身简单，但理解题目要求是得分的关键。

![](img/7974b820ac00cfc94007b114361b2dcf_26.png)

![](img/7974b820ac00cfc94007b114361b2dcf_28.png)

![](img/7974b820ac00cfc94007b114361b2dcf_30.png)

![](img/7974b820ac00cfc94007b114361b2dcf_32.png)

![](img/7974b820ac00cfc94007b114361b2dcf_34.png)

![](img/7974b820ac00cfc94007b114361b2dcf_36.png)

以下是进入考试环境后的第一步操作流程：

![](img/7974b820ac00cfc94007b114361b2dcf_38.png)

1.  打开终端（Terminal）。
2.  使用 `cat /etc/hosts` 命令查看本地主机名与IP的映射关系。
3.  通过VM Control控制台登录到需要操作的节点（如node1）。

![](img/7974b820ac00cfc94007b114361b2dcf_40.png)

![](img/7974b820ac00cfc94007b114361b2dcf_42.png)

![](img/7974b820ac00cfc94007b114361b2dcf_44.png)

## RHCSA 典型题目解析：网络配置 🌐

上一节我们熟悉了考试界面，本节中我们来看看RHCSA的第一道典型题目——配置节点网络。

![](img/7974b820ac00cfc94007b114361b2dcf_46.png)

![](img/7974b820ac00cfc94007b114361b2dcf_48.png)

![](img/7974b820ac00cfc94007b114361b2dcf_50.png)

![](img/7974b820ac00cfc94007b114361b2dcf_52.png)

题目要求为node1节点配置静态IP地址、子网掩码、网关、DNS以及主机名。考试只看最终结果，不限制操作过程，但建议使用标准命令以确保无误。

**核心命令公式**：
*   修改主机名：`hostnamectl set-hostname <主机名>`
*   配置网络（推荐使用`nmcli`）：
    ```bash
    nmcli connection modify ‘Wired connection 1’ ipv4.addresses 172.25.250.100/24 ipv4.gateway 172.25.250.254 ipv4.dns 172.25.250.254 ipv4.method manual autoconnect yes
    nmcli connection up ‘Wired connection 1’
    ```

![](img/7974b820ac00cfc94007b114361b2dcf_54.png)

![](img/7974b820ac00cfc94007b114361b2dcf_56.png)

![](img/7974b820ac00cfc94007b114361b2dcf_58.png)

操作完成后，务必进行验证。您可以从控制机SSH到node1节点，使用 `hostname`、`ip a`、`cat /etc/resolv.conf` 等命令确认配置已生效。

![](img/7974b820ac00cfc94007b114361b2dcf_60.png)

![](img/7974b820ac00cfc94007b114361b2dcf_62.png)

![](img/7974b820ac00cfc94007b114361b2dcf_64.png)

## RHCSA 典型题目解析：软件仓库与SELinux 🔧

上一节我们完成了网络配置，本节中我们来看看如何配置系统软件仓库（Yum Repo）和管理SELinux。

![](img/7974b820ac00cfc94007b114361b2dcf_66.png)

![](img/7974b820ac00cfc94007b114361b2dcf_68.png)

![](img/7974b820ac00cfc94007b114361b2dcf_70.png)

配置软件仓库是后续安装软件的基础。题目会提供两个仓库地址，您需要创建对应的.repo文件。

![](img/7974b820ac00cfc94007b114361b2dcf_72.png)

![](img/7974b820ac00cfc94007b114361b2dcf_74.png)

![](img/7974b820ac00cfc94007b114361b2dcf_76.png)

以下是创建软件仓库的步骤：

1.  在 `/etc/yum.repos.d/` 目录下创建 `.repo` 文件。
2.  文件内容需包含 `[repository_id]`、`name`、`baseurl`、`enabled`、`gpgcheck` 等字段。
3.  使用 `yum repolist` 命令验证仓库是否可用。

**注意**：如果遇到仓库无法访问的情况，首先检查网络的DNS配置是否正确，这是基本的排错流程。

接下来是配置SELinux。题目可能要求允许某个服务（如Apache）在非标准端口（如82）运行。

**核心命令公式**：
```bash
# 添加端口到SELinux策略
semanage port -a -t http_port_t -p tcp 82
# 重启服务并设置开机自启
systemctl restart httpd
systemctl enable httpd
```
关键点在于使用 `semanage` 命令修改策略，并确保服务重启后生效且设置为开机自启。

## RHCSA 典型题目解析：用户与组管理 👥

上一节我们处理了系统服务配置，本节中我们来看看用户与组的管理。

![](img/7974b820ac00cfc94007b114361b2dcf_78.png)

![](img/7974b820ac00cfc94007b114361b2dcf_80.png)

![](img/7974b820ac00cfc94007b114361b2dcf_82.png)

这类题目要求创建用户、组，并设置相应的附属组、密码及Shell权限。

![](img/7974b820ac00cfc94007b114361b2dcf_84.png)

![](img/7974b820ac00cfc94007b114361b2dcf_86.png)

![](img/7974b820ac00cfc94007b114361b2dcf_88.png)

![](img/7974b820ac00cfc94007b114361b2dcf_90.png)

![](img/7974b820ac00cfc94007b114361b2dcf_92.png)

![](img/7974b820ac00cfc94007b114361b2dcf_94.png)

**核心命令公式**：
```bash
# 创建组
groupadd <组名>
# 创建用户并指定附属组
useradd -G <附属组名> <用户名>
# 设置用户密码（使用管道符避免交互）
echo “<密码>” | passwd --stdin <用户名>
# 修改用户Shell（禁止登录）
usermod -s /sbin/nologin <用户名>
```

操作完成后，使用 `id <用户名>` 和 `tail -3 /etc/passwd` 等命令进行验证，确保所有要求都已满足。

## 过渡到RHCE与Ansible基础 🤖

![](img/7974b820ac00cfc94007b114361b2dcf_96.png)

![](img/7974b820ac00cfc94007b114361b2dcf_98.png)

![](img/7974b820ac00cfc94007b114361b2dcf_100.png)

上一节我们完成了RHCSA部分的基础命令学习，本节中我们来看看RHCE考试的核心——Ansible自动化运维。

![](img/7974b820ac00cfc94007b114361b2dcf_102.png)

![](img/7974b820ac00cfc94007b114361b2dcf_104.png)

RHCE考试主要考察使用Ansible进行自动化配置的能力，涵盖ad-hoc临时命令、playbook剧本和roles角色三种模式。

首先，需要在控制节点（control）上安装并配置Ansible。

**核心操作流程**：
1.  安装Ansible软件包：`yum install ansible`
2.  在指定目录（如 `/home/greg/ansible`）下创建主机清单文件（inventory）。
3.  根据题目要求，在清单文件中定义主机组（如 `[dev]`、`[prod]`、`[webserver:children]`）。
4.  创建Ansible配置文件（`ansible.cfg`），并正确设置 `inventory`、`remote_user` 等参数。

配置完成后，使用 `ansible all -m ping` 命令测试到所有被管理节点的连通性。

## RHCE 典型题目解析：Ansible Ad-Hoc命令 📝

![](img/7974b820ac00cfc94007b114361b2dcf_106.png)

上一节我们配置好了Ansible环境，本节中我们来看看如何使用Ansible的ad-hoc命令模式完成任务。

题目要求创建一个Shell脚本，使用Ansible的ad-hoc命令在所有节点上配置统一的Yum仓库。

![](img/7974b820ac00cfc94007b114361b2dcf_108.png)

![](img/7974b820ac00cfc94007b114361b2dcf_110.png)

**核心步骤**：
1.  在指定目录创建脚本文件，如 `adhoc.sh`。
2.  在脚本中使用 `ansible` 命令配合 `yum_repository` 模块。
3.  模块参数需对应.repo文件中的字段（name, baseurl, enabled, gpgcheck）。
4.  **关键**：务必给脚本添加执行权限：`chmod +x adhoc.sh`。

**示例代码**：
```bash
#!/bin/bash
ansible all -m yum_repository -a “name=Base description=‘Base Repo’ baseurl=http://content.example.com/rhel8.2/x86_64/dvd/BaseOS gpgcheck=yes gpgkey=http://content.example.com/rhel8.2/x86_64/dvd/RPM-GPG-KEY-redhat-release enabled=yes”
```

执行脚本后，可到任意被管理节点使用 `yum repolist` 验证仓库是否已成功配置。

## 备考策略与考试细节 📚

在本节课中我们一起学习了RHCSA和RHCE的核心考题操作。最后，我们来总结一下备考策略和重要的考试细节。

**备考核心**：技术难度不高，但要求极度细心。务必在提供的模拟环境中反复练习每道题目，直至熟练。

![](img/7974b820ac00cfc94007b114361b2dcf_112.png)

![](img/7974b820ac00cfc94007b114361b2dcf_114.png)

![](img/7974b820ac00cfc94007b114361b2dcf_116.png)

**考试细节**：
*   **形式**：线下机房考试。RHCSA（上午）和RHCE（下午）通常在同一天完成。
*   **时长**：RHCSA约2.5小时（实际1小时内可完成），RHCE约4小时。
*   **语言**：考试界面支持中文。
*   **评分**：每科满分300分，210分即可通过。考试结束后1-2天内可查成绩并下载电子证书。
*   **有效期**：证书自获取日起3年内有效。对于大多数求职场景，证书的永久效力更被看重。
*   **注意事项**：考场允许合理休息和提问（仅限环境问题），但严禁任何形式的作弊。

**含金量与就业**：红帽认证在全球IT行业，尤其在运维领域认可度很高。许多企业的招聘要求中会注明“持有RHCE证书者优先”。它是应届生和转行人员证明自身Linux及自动化运维能力的有效凭证。

## 总结 🎯

本节课我们系统性地学习了红帽RHCE认证考试的入门知识。我们从搭建考试环境开始，逐步解析了RHCSA中的网络配置、软件仓库、SELinux和用户管理等重点题目。随后，我们过渡到RHCE部分，掌握了Ansible的基础配置和ad-hoc命令模式的使用。

![](img/7974b820ac00cfc94007b114361b2dcf_118.png)

记住，成功的关键在于：**理解题意、细心操作、反复练习**。早考早受益，抓紧在当前的RHCE 8版本下完成认证，以应对未来可能的版本升级。祝您考试顺利，一举拿下认证！