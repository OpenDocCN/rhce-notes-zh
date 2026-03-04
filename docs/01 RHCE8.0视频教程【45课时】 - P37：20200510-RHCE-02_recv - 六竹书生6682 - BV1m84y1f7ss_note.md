# Ansible 基础：02：文件、计划任务与主机名管理

在本节课中，我们将学习如何使用 Ansible 管理远程主机上的文件、计划任务和主机名。我们将重点介绍 `copy`、`file`、`cron` 和 `hostname` 模块的用法，并通过具体示例演示如何执行脚本、传输文件、创建目录、管理定时任务以及修改主机名。

---

## 远程执行脚本的两种方式

![](img/83d82d172e7e1378cc5a5822d98b2e2a_1.png)

有时我们希望将本地脚本传输到远程主机上执行。通常有两种实现方式。

以下是两种执行远程脚本的方法：

1.  **先传输，后执行**：首先在本地编写脚本文件，然后使用 `copy` 模块将脚本发送到远程主机，最后使用 `shell` 或 `command` 模块在远程主机上执行该脚本。
2.  **直接执行**：在本地使用 `script` 模块直接执行脚本文件，Ansible 会自动将脚本传输到目标主机运行，省去了手动推送的步骤。

上一节我们介绍了远程执行命令，本节中我们来看看如何执行脚本。

### 方式一：使用 `script` 模块直接执行

首先，我们在本地 `/tmp` 目录下创建一个简单的脚本 `date.sh`，内容为显示当前时间。

```bash
#!/bin/bash
date
```

然后，使用 `ansible` 命令配合 `script` 模块让其在 `web1` 主机上运行。

```bash
ansible web1 -m script -a “/tmp/date.sh”
```

执行后，`stdout` 会输出命令结果。如果目标主机有多台，结果会分别显示。

### 方式二：先传输文件，再执行

我们也可以先将脚本文件发送到远程主机，然后再执行。发送文件使用 `copy` 模块。

我们先查看 `copy` 模块的帮助信息。

![](img/83d82d172e7e1378cc5a5822d98b2e2a_3.png)

![](img/83d82d172e7e1378cc5a5822d98b2e2a_5.png)

```bash
ansible-doc copy
```

![](img/83d82d172e7e1378cc5a5822d98b2e2a_7.png)

帮助信息中，重要的参数有：
*   `src`: 本地源文件路径。
*   `dest`: 远程主机目标路径。
*   `backup`: 是否在覆盖前备份远程原文件。

![](img/83d82d172e7e1378cc5a5822d98b2e2a_9.png)

现在，我们将本地的 `/tmp/date.sh` 脚本发送到远程主机的 `/tmp` 目录。

```bash
ansible web1 -m copy -a “src=/tmp/date.sh dest=/tmp”
```

文件发送成功后，我们需要为其添加执行权限，然后才能在远程执行。

![](img/83d82d172e7e1378cc5a5822d98b2e2a_11.png)

```bash
ansible web1 -m shell -a “chmod 755 /tmp/date.sh”
ansible web1 -m shell -a “/tmp/date.sh”
```

至此，我们介绍了让远程主机执行脚本的两种方式。接下来，我们深入了解一下 `copy` 模块的更多功能。

---

## `copy` 模块详解

![](img/83d82d172e7e1378cc5a5822d98b2e2a_13.png)

`copy` 模块不仅用于传输文件，还可以在传输时修改文件属性，或直接将内容写入远程文件。

![](img/83d82d172e7e1378cc5a5822d98b2e2a_15.png)

### 传输时修改文件属性

![](img/83d82d172e7e1378cc5a5822d98b2e2a_17.png)

在拷贝文件时，可以同时指定其所有者、所属组和权限。

```bash
ansible web1 -m copy -a “src=/tmp/aa.txt dest=/tmp/bb.txt owner=root group=root mode=‘0777’”
```

这条命令将本地文件 `aa.txt` 拷贝到远程主机的 `/tmp/bb.txt`，并设置其所有者为 `root`，所属组为 `root`，权限为 `777`。

### 使用 `content` 参数直接写入内容

`copy` 模块的 `content` 参数可以直接将字符串内容写入远程文件，这会覆盖目标文件的原有内容。

```bash
ansible web1 -m copy -a “content=‘bye\n20200510’ dest=/tmp/bb.txt”
```

### 从远程主机提取文件

`fetch` 模块用于将远程主机上的文件拉取到本地控制机。如果目标是目录，则会打包归档。

![](img/83d82d172e7e1378cc5a5822d98b2e2a_19.png)

```bash
ansible web1 -m fetch -a “src=/tmp/bb.txt dest=/tmp/”
```

![](img/83d82d172e7e1378cc5a5822d98b2e2a_21.png)

此命令将远程 `web1` 主机上的 `/tmp/bb.txt` 文件拉取到本地的 `/tmp` 目录下。如果有多台主机，会为每台主机创建一个以主机名命名的目录来存放对应的文件。

![](img/83d82d172e7e1378cc5a5822d98b2e2a_23.png)

---

## `file` 模块：管理文件与目录属性

![](img/83d82d172e7e1378cc5a5822d98b2e2a_25.png)

![](img/83d82d172e7e1378cc5a5822d98b2e2a_26.png)

![](img/83d82d172e7e1378cc5a5822d98b2e2a_28.png)

除了在 `copy` 时设置属性，我们还可以使用专门的 `file` 模块来管理文件和目录的状态，如创建、删除、修改权限等。

查看 `file` 模块的帮助信息。

```bash
ansible-doc file
```

常用参数包括：
*   `path`: 文件或目录的路径。
*   `state`: 状态。`directory` 为创建目录，`touch` 为创建文件，`absent` 为删除，`file` 仅检查文件是否存在。
*   `owner`: 设置所有者。
*   `group`: 设置所属组。
*   `mode`: 设置权限。

### 创建目录与文件

使用 `file` 模块创建目录。

```bash
ansible web1 -m file -a “path=/tmp/dir2 state=directory”
```

创建文件。

```bash
ansible web1 -m file -a “path=/tmp/file1 state=touch”
```

### 检查文件是否存在

![](img/83d82d172e7e1378cc5a5822d98b2e2a_30.png)

将 `state` 设置为 `file` 可以检查普通文件是否存在，但不会创建它。如果目标是目录，则会返回提示信息。

```bash
ansible web1 -m file -a “path=/tmp/file1 state=file”
```

### 删除文件或目录

将 `state` 设置为 `absent` 可以删除文件或目录。删除目录时会递归删除其内容。

![](img/83d82d172e7e1378cc5a5822d98b2e2a_32.png)

```bash
# 删除文件
ansible web1 -m file -a “path=/tmp/file1 state=absent”
# 递归删除目录
ansible web1 -m file -a “path=/tmp/dir2 state=absent”
```

---

![](img/83d82d172e7e1378cc5a5822d98b2e2a_34.png)

## `hostname` 模块：管理主机名

![](img/83d82d172e7e1378cc5a5822d98b2e2a_36.png)

有时我们需要修改远程主机的主机名，可以使用 `hostname` 模块。

![](img/83d82d172e7e1378cc5a5822d98b2e2a_38.png)

```bash
ansible web1 -m hostname -a “name=www”
```

这条命令会将 `web1` 主机的主机名改为 `www`。**注意**：如果在多台主机的组上执行此命令，所有主机都会被改为相同的名字。在实际应用中，通常需要配合变量来为不同主机设置不同的名字。

修改后，可以改回原来的主机名。

![](img/83d82d172e7e1378cc5a5822d98b2e2a_40.png)

![](img/83d82d172e7e1378cc5a5822d98b2e2a_41.png)

```bash
ansible web1 -m hostname -a “name=web1.example.com”
```

![](img/83d82d172e7e1378cc5a5822d98b2e2a_43.png)

---

## `cron` 模块：管理计划任务

`cron` 模块用于管理远程主机的计划任务（crontab）。

查看 `cron` 模块的帮助信息。

```bash
ansible-doc cron
```

关键参数：
*   `name`: 计划任务的名称（标识符）。
*   `job`: 要执行的命令。
*   `minute`, `hour`, `day`, `month`, `weekday`: 时间设定字段（默认为 `*`）。
*   `user`: 以哪个用户身份创建任务（默认为 `root`）。
*   `state`: `present` 为添加（默认），`absent` 为删除。
*   `disabled`: `yes` 禁用任务，`no` 启用任务。

![](img/83d82d172e7e1378cc5a5822d98b2e2a_45.png)

![](img/83d82d172e7e1378cc5a5822d98b2e2a_47.png)

### 创建计划任务

创建一个每分钟执行一次 `date` 命令的任务，并命名为 `show_time`。

```bash
ansible web1 -m cron -a “name=‘show_time’ minute=‘*’ job=‘/usr/bin/date’”
```

### 修改计划任务

将上面任务改为每5分钟执行一次。

```bash
ansible web1 -m cron -a “name=‘show_time’ minute=‘*/5’ job=‘/usr/bin/date’”
```

![](img/83d82d172e7e1378cc5a5822d98b2e2a_49.png)

### 禁用与启用计划任务

禁用名为 `show_time` 的任务。

![](img/83d82d172e7e1378cc5a5822d98b2e2a_51.png)

```bash
ansible web1 -m cron -a “name=‘show_time’ disabled=yes”
```

启用该任务。

```bash
ansible web1 -m cron -a “name=‘show_time’ disabled=no”
```

### 删除计划任务

![](img/83d82d172e7e1378cc5a5822d98b2e2a_53.png)

删除名为 `show_time` 的计划任务。

![](img/83d82d172e7e1378cc5a5822d98b2e2a_55.png)

![](img/83d82d172e7e1378cc5a5822d98b2e2a_56.png)

```bash
ansible web1 -m cron -a “name=‘show_time’ state=absent”
```

**注意**：删除操作必须指定 `name` 参数来精确匹配要删除的任务。

---

## 总结

本节课中我们一起学习了 Ansible 的几个核心模块：
1.  使用 `script` 和 `copy` + `shell` 两种方式在远程主机执行脚本。
2.  使用 `copy` 模块传输文件、直接写入内容，以及使用 `fetch` 模块拉取文件。
3.  使用 `file` 模块创建、删除文件和目录，并管理其属性。
4.  使用 `hostname` 模块修改远程主机的主机名。
5.  使用 `cron` 模块对远程主机的计划任务进行增、删、改、查、启用和禁用操作。

![](img/83d82d172e7e1378cc5a5822d98b2e2a_58.png)

这些模块是进行自动化配置管理的基础，熟练掌握它们将为编写更复杂的 Ansible Playbook 打下坚实的基础。