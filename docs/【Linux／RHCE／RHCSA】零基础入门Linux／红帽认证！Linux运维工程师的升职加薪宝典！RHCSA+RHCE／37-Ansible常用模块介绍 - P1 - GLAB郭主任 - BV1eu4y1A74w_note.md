# Ansible 常用模块介绍：P1：Ad-Hoc 命令与核心模块

![](img/e29c629c6dcedc1e1825527bb40ff657_1.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_3.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_4.png)

在本节课中，我们将学习 Ansible 中 Ad-Hoc 命令的使用方法，并详细介绍一系列最常用的核心模块。这些模块是构建自动化任务的基础，理解它们对于编写 Playbook 至关重要。

![](img/e29c629c6dcedc1e1825527bb40ff657_6.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_8.png)

上一节我们介绍了 Ansible 的基本配置，包括清单文件和配置文件。本节中，我们来看看如何使用 Ad-Hoc 命令进行快速测试和任务执行。

![](img/e29c629c6dcedc1e1825527bb40ff657_10.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_12.png)

## Ad-Hoc 命令简介

Ad-Hoc 命令用于在远程主机上执行单个、独立的命令。它适合执行快速、一次性的任务，例如测试连通性或执行简单的系统命令。对于复杂的、流程化的自动化任务，则需要使用 Playbook。

以下是一个简单的 Ad-Hoc 命令测试连通性的例子：
```bash
ansible all -m ping
```
如果所有主机都返回绿色的 `SUCCESS` 状态，则说明 Ansible 控制节点与被管理节点之间的基础通信配置正确。

![](img/e29c629c6dcedc1e1825527bb40ff657_14.png)

## 常用模块详解

![](img/e29c629c6dcedc1e1825527bb40ff657_16.png)

接下来，我们将逐一介绍 Ansible 中一些最常用的模块及其应用场景。

![](img/e29c629c6dcedc1e1825527bb40ff657_18.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_20.png)

### 1. Command 与 Shell 模块

![](img/e29c629c6dcedc1e1825527bb40ff657_22.png)

这两个模块的作用都是在远程服务器上执行指定的命令。

*   **command 模块**：是 Ansible 的默认模块。它直接在远程主机上执行命令，但**不支持** Shell 的特性，如管道符 `|`、重定向 `>`、变量 `$HOME` 等。
*   **shell 模块**：通过远程主机的 Shell（如 `/bin/bash`）来执行命令，因此**支持**所有 Shell 特性。

![](img/e29c629c6dcedc1e1825527bb40ff657_24.png)

以下是它们的区别示例：
```bash
# 使用 command 模块执行简单命令（成功）
ansible server_a -a "echo hello world"

![](img/e29c629c6dcedc1e1825527bb40ff657_26.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_28.png)

# 使用 command 模块执行带管道的命令（失败）
ansible server_a -a "echo hello world | grep e"

# 使用 shell 模块执行带管道的命令（成功）
ansible server_a -m shell -a "echo hello world | grep e"
```
**核心区别**：`shell` 模块可以执行需要 Shell 环境特性的命令，而 `command` 模块不能。`shell` 模块能完成的任务，`command` 模块不一定能完成。

### 2. Script 模块

![](img/e29c629c6dcedc1e1825527bb40ff657_30.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_31.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_33.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_35.png)

`script` 模块用于将**管理节点**上的脚本文件传输到**被管理节点**上去执行。

例如，管理节点上有一个脚本 `/home/devops/a.sh`，其内容为 `touch /tmp/test1`。我们可以将其送到远程主机执行：
```bash
# 将本地脚本在远程主机上执行
ansible server_a -m script -a "/home/devops/a.sh"

# 使用 command 模块验证远程主机上是否创建了文件
ansible server_a -m command -a "ls /tmp/test1"
```

![](img/e29c629c6dcedc1e1825527bb40ff657_37.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_39.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_41.png)

### 3. Copy 模块

![](img/e29c629c6dcedc1e1825527bb40ff657_43.png)

`copy` 模块用于在管理节点和被管理节点之间拷贝文件。

![](img/e29c629c6dcedc1e1825527bb40ff657_45.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_47.png)

这是一个功能丰富的模块，包含许多参数。当你不确定如何使用一个模块时，可以使用 `ansible-doc <模块名>` 命令查看官方文档和示例。

![](img/e29c629c6dcedc1e1825527bb40ff657_49.png)

以下是 `copy` 模块的一个复杂示例，展示了其核心参数：
```bash
ansible server_a -m copy -a "src=/path/to/local/file dest=/etc/motd backup=yes owner=root group=root mode=0755"
```
以下是该模块的常用参数说明：
*   `src`：指定源文件路径（在控制节点上）。
*   `dest`：指定目标文件路径（在被管理节点上）。
*   `backup`：在覆盖前备份原文件（`yes`/`no`）。
*   `owner`：设置文件所属用户。
*   `group`：设置文件所属组。
*   `mode`：设置文件权限（如 `0755`）。
*   `content`：直接用内容创建文件，而不是拷贝源文件。

![](img/e29c629c6dcedc1e1825527bb40ff657_51.png)

### 4. Yum Repository 模块

![](img/e29c629c6dcedc1e1825527bb40ff657_53.png)

`yum_repository` 模块用于在被管理节点上添加或删除 YUM 软件仓库源。

这在考试和实际工作中都很常见。其参数与手动编写 `.repo` 文件时的字段对应：
```bash
ansible server_a -m yum_repository -a "name=myrepo description='My Repo' baseurl=http://mirror.example.com/repo gpgcheck=no enabled=yes"
```
**主要参数**：
*   `name`：仓库名称（对应 `.repo` 文件名中的节名）。
*   `description`：仓库描述。
*   `baseurl`：仓库的 URL 地址。
*   `gpgcheck`：是否进行 GPG 校验。
*   `enabled`：是否启用该仓库。
*   `state`：状态，`present` 为添加（默认），`absent` 为删除。

### 5. Yum 模块

`yum` 模块用于通过 YUM 包管理器安装、升级或删除软件包。

![](img/e29c629c6dcedc1e1825527bb40ff657_55.png)

这是最常用的模块之一。其核心是 `state` 参数，它决定了操作行为：
```bash
# 确保 httpd 软件包已安装（如果已安装则不升级，如果未安装则安装）
ansible server_a -m yum -a "name=httpd state=present"

![](img/e29c629c6dcedc1e1825527bb40ff657_57.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_59.png)

# 确保 httpd 软件包安装且为最新版本（如果已安装但非最新，则会升级）
ansible server_a -m yum -a "name=httpd state=latest"

![](img/e29c629c6dcedc1e1825527bb40ff657_61.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_63.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_65.png)

# 卸载 httpd 软件包
ansible server_a -m yum -a "name=httpd state=absent"
```
**`state` 参数详解**：
*   `present` / `installed`：确保软件包已安装。如果已安装，即使有更新也不会升级。两者细微差别在于 `installed` 仅是确认安装状态。
*   `latest`：确保软件包安装且为最新版本。如果已安装但不是最新，则会执行升级。
*   `absent` / `removed`：确保软件包已被卸载。

![](img/e29c629c6dcedc1e1825527bb40ff657_67.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_69.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_71.png)

### 6. Systemd 模块

![](img/e29c629c6dcedc1e1825527bb40ff657_73.png)

`systemd` 模块用于管理远程节点的系统服务（如 systemd 服务）。

它的功能类似于在单台机器上执行 `systemctl` 命令：
```bash
# 启动 httpd 服务
ansible server_a -m systemd -a "name=httpd state=started"

![](img/e29c629c6dcedc1e1825527bb40ff657_75.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_77.png)

# 停止 httpd 服务
ansible server_a -m systemd -a "name=httpd state=stopped"

![](img/e29c629c6dcedc1e1825527bb40ff657_79.png)

# 重启 httpd 服务
ansible server_a -m systemd -a "name=httpd state=restarted"

![](img/e29c629c6dcedc1e1825527bb40ff657_81.png)

# 设置 httpd 服务开机自启
ansible server_a -m systemd -a "name=httpd enabled=yes"
```
**主要参数**：
*   `name`：要管理的服务名称。
*   `state`：服务状态，如 `started`, `stopped`, `restarted`, `reloaded`。
*   `enabled`：是否开机自启（`yes`/`no`）。

### 7. Group 与 User 模块

这两个模块用于管理被管理节点上的组和用户。

**Group 模块示例**：
```bash
# 创建一个名为 dba 的组（非系统组）
ansible server_a -m group -a "name=dba state=present"
```
**User 模块示例**：
```bash
# 创建一个名为 foo 的用户，并设置密码和家目录
ansible server_a -m user -a "name=foo password='{{ 'mypass' | password_hash('sha512') }}' home=/home/foo shell=/bin/bash state=present"
```
`user` 模块参数非常丰富，几乎涵盖了 `useradd` 命令的所有选项，如 `uid`, `group`, `groups`（附加组）, `comment`（注释）, `create_home`, `expires`（过期时间）等。`state=absent` 结合 `remove=yes` 可以删除用户及其家目录。

### 8. File 模块

`file` 模块用于设置文件的属性，或创建文件、目录、链接等。

![](img/e29c629c6dcedc1e1825527bb40ff657_83.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_85.png)

其功能非常灵活，主要通过 `state` 参数来定义操作类型：
```bash
# 创建一个空文件（类似 touch）
ansible server_a -m file -a "path=/tmp/test.txt state=touch"

# 修改文件的属主和权限
ansible server_a -m file -a "path=/tmp/test.txt owner=devops group=devops mode=0644"

# 创建一个目录
ansible server_a -m file -a "path=/opt/mydir state=directory"

# 创建一个软链接
ansible server_a -m file -a "src=/etc/hosts dest=/tmp/hosts_link state=link"

# 删除一个文件或链接
ansible server_a -m file -a "path=/tmp/hosts_link state=absent"
```
**`state` 参数说明**：
*   `file`：修改属性（默认），如果文件不存在则创建。
*   `touch`：创建空文件或更新文件时间戳。
*   `directory`：创建目录。
*   `link`：创建软链接。
*   `hard`：创建硬链接。
*   `absent`：删除文件、目录或链接。

### 9. Cron 模块

`cron` 模块用于在被管理节点上管理计划任务（cron job）。

其参数与 crontab 的字段对应：
```bash
# 创建一个计划任务，每小时整点执行 ls 命令，并将输出追加到 /dev/null
ansible server_a -m cron -a "name='list files' minute=0 job='ls -al >> /dev/null'"
```
**主要参数**：
*   `name`：计划任务的名称（注释），用于标识。
*   `minute`, `hour`, `day`, `month`, `weekday`：对应 cron 的时间字段。
*   `job`：要执行的命令。
*   `state`：状态，`present` 为添加（默认），`absent` 为删除。

### 10. Debug 模块

`debug` 模块用于在 Playbook 或 Ad-Hoc 执行过程中打印调试信息，类似于编程语言中的 `print` 函数。

它常用于输出变量的值或自定义消息：
```bash
# 定义变量并打印
ansible localhost -m debug -a "msg='The role is {{ role_name }}'" -e "role_name=web"
```
**主要参数**：
*   `msg`：要打印的字符串信息。
*   `var`：直接打印指定变量的值。

### 11. Template 模块

`template` 模块是一个高级文件处理模块。它使用 **Jinja2** 模板引擎，可以将一个包含变量和逻辑的模板文件，渲染成针对不同被管理节点的定制化配置文件，然后复制到目标主机。

这是实现配置差异化的关键模块，在考试和实践中非常重要。
```bash
# 假设有一个模板文件 nginx.conf.j2，其中包含变量 {{ server_name }}
ansible web_servers -m template -a "src=nginx.conf.j2 dest=/etc/nginx/nginx.conf" -e "server_name=example.com"
```
模板文件（如 `.j2` 后缀）中可以使用变量、条件判断、循环等 Jinja2 语法，使得最终生成的文件内容能根据目标主机的不同而动态变化。

### 12. Lineinfile 模块

`lineinfile` 模块用于确保某一行文本存在于（或不存在于）目标文件中。它非常适合对配置文件进行精确的单行修改。

```bash
# 确保 /etc/selinux/config 文件中包含 SELINUX=disabled 这一行
ansible server_a -m lineinfile -a "path=/etc/selinux/config regexp='^SELINUX=' line='SELINUX=disabled'"

# 删除 /etc/fstab 中包含 “olddisk” 的行
ansible server_a -m lineinfile -a "path=/etc/fstab state=absent regexp='olddisk'"
```
**主要参数**：
*   `path`：要修改的目标文件路径。
*   `regexp`：用于匹配已有行的正则表达式。
*   `line`：要确保存在的那一行文本。
*   `state`：`present`（确保存在，默认）或 `absent`（确保不存在）。
*   `create`：如果文件不存在，是否创建它。

### 13. Blockinfile 模块

`blockinfile` 模块用于在文件中插入、更新或删除一个由标记线包围的文本块。适合进行多行配置的插入或替换。

```bash
# 在 /etc/ssh/sshd_config 文件末尾插入一个配置块
ansible server_a -m blockinfile -a "path=/etc/ssh/sshd_config block='
AllowUsers devops
DenyUsers root
'"
```
**主要参数**：
*   `path`：目标文件路径。
*   `block`：要插入的文本块内容。
*   `marker`：自定义标记线的格式（默认使用 `# {mark} ANSIBLE MANAGED BLOCK` 和 `# {mark} ANSIBLE MANAGED BLOCK`）。
*   `state`：`present`（插入/更新块）或 `absent`（删除块）。

**`lineinfile` 与 `blockinfile` 的选择**：需要对文件进行精细的单行操作时，使用 `lineinfile`；需要插入或管理一整段（多行）配置时，使用 `blockinfile`。

## 总结

本节课中我们一起学习了 Ansible Ad-Hoc 命令的基本概念，并深入介绍了十三个最常用、最核心的模块。从执行命令（`command`, `shell`）、操作文件（`copy`, `file`, `template`）、管理软件包和服务（`yum`, `systemd`），到配置系统资源（`user`, `group`, `cron`）和修改文件内容（`lineinfile`, `blockinfile`），这些模块构成了 Ansible 自动化能力的基石。

![](img/e29c629c6dcedc1e1825527bb40ff657_87.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_89.png)

![](img/e29c629c6dcedc1e1825527bb40ff657_91.png)

理解每个模块的用途、参数以及它们之间的区别，是编写高效、可靠 Playbook 的前提。建议你结合 `ansible-doc` 命令和实际操作来巩固对这些模块的掌握。下一节，我们将开始学习如何将这些模块组织成更强大的自动化剧本——Playbook。