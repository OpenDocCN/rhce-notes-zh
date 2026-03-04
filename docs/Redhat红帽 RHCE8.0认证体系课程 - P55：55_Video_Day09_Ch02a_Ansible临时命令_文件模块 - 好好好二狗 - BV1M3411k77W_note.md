# Redhat红帽 RHCE8.0认证体系课程：P55：Ansible临时命令与文件模块

在本节课中，我们将要学习Ansible临时命令的基本语法，并重点掌握文件管理相关的核心模块，包括`copy`、`file`、`fetch`、`lineinfile`和`synchronize`。这些模块是进行自动化文件操作的基础。

![](img/fd4b365afd7b511822165d2e1b6c168e_1.png)

## 临时命令语法概述

![](img/fd4b365afd7b511822165d2e1b6c168e_3.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_5.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_7.png)

上一节我们介绍了Ansible的基本配置，本节中我们来看看如何执行临时命令。临时命令是直接在命令行中执行单条Ansible任务的方式。

![](img/fd4b365afd7b511822165d2e1b6c168e_8.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_9.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_11.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_12.png)

其基本语法结构如下：
```bash
ansible <主机模式> -m <模块名> -a "<模块参数>"
```

![](img/fd4b365afd7b511822165d2e1b6c168e_14.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_16.png)

以下是关键组成部分的说明：
*   **`<主机模式>`**：指资产清单中定义的主机组名、具体主机名或IP地址。
*   **`-m <模块名>`**：指定要使用的功能模块。
*   **`-a "<模块参数>"`**：传递给模块的具体参数和值。
*   **`-i <清单文件>`**：指定使用的资产清单文件（当有多个时使用，通常可省略）。

![](img/fd4b365afd7b511822165d2e1b6c168e_18.png)

## 获取模块帮助

![](img/fd4b365afd7b511822165d2e1b6c168e_20.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_22.png)

在执行命令前，了解模块的用法至关重要。Ansible提供了两种主要的帮助途径。

![](img/fd4b365afd7b511822165d2e1b6c168e_24.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_26.png)

以下是两种查询帮助的方法：
1.  **`ansible-doc` 命令**：在控制节点本地查询模块的详细说明。例如，`ansible-doc user` 可以查看`user`模块的所有参数。参数前标注 **`=`** 表示是必要参数，标注 **`-`** 表示是可选参数（通常有默认值）。
2.  **官方文档网站**：访问 `docs.ansible.com` 可以查阅最全面、最新的模块、角色等信息。

## 理解幂等性

![](img/fd4b365afd7b511822165d2e1b6c168e_28.png)

Ansible有一个非常重要的核心概念叫**幂等性**。它指的是：当使用Ansible模块执行任务时，会先检查受管主机的当前状态。如果已经处于目标状态，则不会进行任何更改；如果未达到目标状态，则执行操作使其达到目标状态。执行后，Ansible会返回 `"changed": true` 或 `"changed": false` 来表明状态是否发生了改变。

## 文件管理模块详解

![](img/fd4b365afd7b511822165d2e1b6c168e_30.png)

接下来，我们将深入讲解几个最常用的文件操作模块。

![](img/fd4b365afd7b511822165d2e1b6c168e_32.png)

### 1. copy模块：复制文件

![](img/fd4b365afd7b511822165d2e1b6c168e_34.png)

`copy`模块用于将文件从控制节点复制到受管主机上。

![](img/fd4b365afd7b511822165d2e1b6c168e_36.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_38.png)

以下是`copy`模块的常用参数：
*   **`src`**：控制节点上的源文件路径（绝对或相对路径）。如果是目录，需在路径末尾加`/`才会复制目录内容，否则会复制目录本身。此参数与`content`二选一。
*   **`content`**：直接在受管主机上创建文件内容，无需源文件。
*   **`dest`**：（**必要参数**）文件在受管主机上的目标路径。
*   **`owner`**：设置文件所有者。
*   **`group`**：设置文件所属组。
*   **`mode`**：设置文件权限（如 `0644`）。
*   **`seuser` / `serole` / `setype`**：设置SELinux上下文。
*   **`backup`**：在覆盖前备份原文件，值为 `yes` 或 `no`。

![](img/fd4b365afd7b511822165d2e1b6c168e_39.png)

**应用示例：**
```bash
# 示例1：复制本地文件到远程
ansible node1 -m copy -a "src=./copy_test dest=/tmp/copy_test"

# 示例2：直接在远程创建文件内容
ansible node1 -m copy -a "content='This is a test file\n' dest=/tmp/f1"

# 示例3：复制并设置权限、属主、SELinux上下文
ansible node1 -m copy -a "src=./copy_test dest=/tmp/f3 owner=student group=student mode=0755 setype=httpd_sys_content_t"

# 示例4：覆盖文件前进行备份
ansible node1 -m copy -a "src=./copy_test dest=/tmp/f1 backup=yes"
```

![](img/fd4b365afd7b511822165d2e1b6c168e_41.png)

### 2. file模块：管理文件属性

![](img/fd4b365afd7b511822165d2e1b6c168e_43.png)

`file`模块用于在受管主机上创建、删除文件或目录，以及设置属性、创建链接等。

以下是`file`模块的常用参数：
*   **`path`**：（**必要参数**）文件或目录的路径。
*   **`state`**：目标状态。常用值：
    *   `directory`：创建目录。
    *   `touch`：创建空文件。
    *   `link`：创建软链接（需配合`src`参数）。
    *   `hard`：创建硬链接（需配合`src`参数）。
    *   `absent`：删除。
*   **`owner`** / **`group`** / **`mode`**：同`copy`模块。
*   **`src`**：当`state=link`或`hard`时，指定链接的源目标。

**应用示例：**
```bash
# 示例1：创建目录并设置属性
ansible node1 -m file -a "path=/tmp/dir1 state=directory owner=student group=student mode=0755 setype=httpd_sys_content_t"

# 示例2：创建空文件（必须用touch，不能用file）
ansible node1 -m file -a "path=/tmp/f1 state=touch"

# 示例3：创建软链接和硬链接
ansible node1 -m file -a "src=/tmp/f1 dest=/tmp/f2 state=link"
ansible node1 -m file -a "src=/tmp/f1 dest=/tmp/f3 state=hard"
```

### 3. fetch模块：拉取文件

![](img/fd4b365afd7b511822165d2e1b6c168e_45.png)

`fetch`模块用于将文件从受管主机拉取（下载）到控制节点。

![](img/fd4b365afd7b511822165d2e1b6c168e_47.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_49.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_51.png)

以下是`fetch`模块的常用参数：
*   **`src`**：（**必要参数**）受管主机上的源文件路径。
*   **`dest`**：（**必要参数**）控制节点上保存文件的目录。
*   **`flat`**：默认为`no`，拉取的文件会保存在以主机名命名的子目录下。设为`yes`时，则直接保存在`dest`指定的路径，且`dest`需包含文件名。

![](img/fd4b365afd7b511822165d2e1b6c168e_53.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_54.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_56.png)

**应用示例：**
```bash
# 示例1：拉取文件，保存到以主机名命名的目录下
ansible node1 -m fetch -a "src=/tmp/f1 dest=./"

![](img/fd4b365afd7b511822165d2e1b6c168e_58.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_60.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_62.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_63.png)

![](img/fd4b365afd7b511822165d2e1b6c168e_64.png)

# 示例2：拉取文件，直接保存到指定路径（flat=yes）
ansible node1 -m fetch -a "src=/tmp/f1 dest=./f1_local flat=yes"
```

### 4. lineinfile模块：行内容管理

`lineinfile`模块以“行”为单位，对文件内容进行增、删、改的流式处理。

以下是`lineinfile`模块的常用参数：
*   **`path`**：（**必要参数**）要修改的文件路径。
*   **`regexp`**：用于定位行的正则表达式。
*   **`line`**：替换或插入的新行内容。
*   **`state`**：行状态。`present`（默认，确保行存在）或 `absent`（确保行被删除）。
*   **`insertafter`** / **`insertbefore`**：在匹配行之后或之前插入新行。
*   **`backup`**：修改前备份原文件。

**应用示例：**
```bash
# 前提：复制一个测试文件到远程
ansible node1 -m copy -a "src=/etc/selinux/config dest=/tmp/selinux_test"

# 示例1：替换以 SELINUX= 开头的行
ansible node1 -m lineinfile -a "path=/tmp/selinux_test regexp='^SELINUX=' line='SELINUX=disabled'"

# 示例2：在 SELINUX= 开头的行之后插入注释行
ansible node1 -m lineinfile -a "path=/tmp/selinux_test regexp='^SELINUX=' insertafter=yes line='# SELINUX is disabled now'"

# 示例3：删除以 ## DISA 开头的行
ansible node1 -m lineinfile -a "path=/tmp/selinux_test regexp='^## DISA' state=absent"

# 示例4：在指定行前插入内容并备份原文件
ansible node1 -m lineinfile -a "path=/tmp/selinux_test regexp='^SELINUX=' insertbefore=yes line='# Before SELINUX line' backup=yes"
```

### 5. synchronize模块：目录同步

`synchronize`模块是对`rsync`命令的封装，用于在控制节点和受管主机之间同步目录。

以下是`synchronize`模块的常用参数：
*   **`src`**：（**必要参数**）控制节点上的源路径。
*   **`dest`**：（**必要参数**）受管主机上的目标路径。
*   **`delete`**：同步后，删除目标端存在而源端不存在的文件（谨慎使用）。值为 `yes` 或 `no`。
*   **`recursive`**：递归同步子目录，默认为 `yes`。

**应用示例：**
```bash
# 同步本地目录到远程主机
ansible node1 -m synchronize -a "src=./test_dir/ dest=/home/student/test_dir/"
```

## 随堂练习

请使用Ansible临时命令在`node1`主机上完成以下任务：
1.  创建一个名为 `/opt/demo` 的目录。
2.  将该目录的所有者设为 `student`，所属组设为 `devops`（需先确保存在`devops`组）。
3.  设置目录权限为 `2770`（即设置SGID位）。
4.  在 `/opt/demo` 目录下创建一个指向 `/etc/hosts` 的符号链接，名为 `hosts_link`。
5.  在 `/opt/demo` 目录下创建一个文件 `info.txt`，内容为 `“This is a managed directory.”`。

## 课程总结

本节课中我们一起学习了Ansible临时命令的核心语法和强大的文件管理模块。我们了解了如何通过`ansible-doc`获取帮助，理解了**幂等性**这一重要概念，并详细演练了`copy`、`file`、`fetch`、`lineinfile`和`synchronize`模块的使用方法。掌握这些模块是编写复杂Ansible剧本的基础，请务必通过练习加以巩固。