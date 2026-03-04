# Linux教程RHCE：P21：Ansible常见企业级应用模块实战

![](img/b081c5c0b6ce030d7e16464a55700dfe_1.png)

在本节课中，我们将深入学习Ansible的几个核心企业级应用模块。上午我们已经学习了Ansible的基本使用和部分模块，下午我们将继续探索更多实用的模块，包括文件抓取、文件管理、主机名修改、计划任务、软件包管理、服务管理以及用户和组管理。这些模块是自动化运维中的基石，掌握它们能极大地提升工作效率。

## 🗂️ Fetch模块：抓取远程文件

上一节我们介绍了`copy`模块用于分发文件，本节中我们来看看功能相反的`fetch`模块。`fetch`模块用于将远程被控节点上的单个文件抓取到Ansible控制端。

它的核心功能是抓取文件，但有两个重要限制：
*   只能抓取**单个文件**，不能抓取目录。
*   抓取到控制端后，会自动按`目标目录/主机名/文件原始路径`的结构存放。

![](img/b081c5c0b6ce030d7e16464a55700dfe_3.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_5.png)

以下是`fetch`模块的基本语法示例：
```bash
ansible all -m fetch -a "src=/var/log/messages dest=/data/"
```
此命令会将所有主机上的`/var/log/messages`文件抓取到控制端的`/data/`目录下。

如果需要抓取多个文件（例如所有日志文件），可以先在远程主机上打包，再用`fetch`抓取压缩包，或者使用专门的`archive`打包模块。

## 📁 File模块：管理文件属性

`file`模块专门用于设置文件属性，功能非常强大。它可以创建文件或目录、设置权限、所有者、创建链接，或者删除文件。

![](img/b081c5c0b6ce030d7e16464a55700dfe_7.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_9.png)

该模块通过`state`参数来定义要执行的操作：
*   `state=directory`：创建目录。
*   `state=touch`：创建空文件。
*   `state=link`：创建软链接。
*   `state=absent`：删除文件或目录。

![](img/b081c5c0b6ce030d7e16464a55700dfe_11.png)

以下是`file`模块的一些常见用法示例：
```bash
# 创建一个空文件
ansible all -m file -a "path=/tmp/f3 state=touch"
# 创建一个目录
ansible all -m file -a "name=/data/dir1 state=directory"
# 创建一个软链接
ansible all -m file -a "src=/etc/fstab dest=/root/fstab.link state=link"
# 删除文件或目录
ansible all -m file -a "path=/data/dir1 state=absent"
```
此外，还可以用`owner`、`group`、`mode`等参数来设置文件的所有者、所属组和权限。

![](img/b081c5c0b6ce030d7e16464a55700dfe_13.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_15.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_17.png)

## 🏷️ Hostname模块：修改主机名

`hostname`模块用于修改远程主机的主机名。使用它修改主机名会立即生效，并且会自动更新相关的配置文件（如`/etc/hostname`）。

![](img/b081c5c0b6ce030d7e16464a55700dfe_19.png)

基本用法如下：
```bash
ansible 192.168.30.101 -m hostname -a "name=node1.magedu.com"
```
需要注意的是，如果批量执行且主机名相同会造成冲突，后续可以结合变量为每台主机设置唯一的主机名。

![](img/b081c5c0b6ce030d7e16464a55700dfe_21.png)

## ⏰ Cron模块：管理计划任务

`cron`模块用于管理计划任务，可以方便地添加、禁用、启用或删除cron作业。

其参数与crontab的格式对应：
*   `minute`、`hour`、`day`、`month`、`weekday`：定义任务执行时间。
*   `job`：指定要执行的命令。
*   `name`：为cron作业命名（注释），便于管理。
*   `state`：`present`（添加）、`absent`（删除）。
*   `disabled`：`yes`/`no`，用于禁用或启用某个作业。

以下是使用`cron`模块的示例：
```bash
# 添加一个计划任务：每周一、三、五的每分钟执行一次广播
ansible all -m cron -a 'minute="*" weekday="1,3,5" job="/usr/bin/wall FBI warning" name="warning"'
# 禁用名为“warning”的计划任务
ansible all -m cron -a 'name="warning" disabled=yes'
# 启用名为“warning”的计划任务
ansible all -m cron -a 'name="warning" disabled=no'
# 删除名为“warning”的计划任务
ansible all -m cron -a 'name="warning" state=absent'
```

## 📦 Yum模块：管理软件包

![](img/b081c5c0b6ce030d7e16464a55700dfe_23.png)

`yum`模块用于通过Yum包管理器来管理软件包，支持安装、升级、降级、删除、列表查看等操作。

![](img/b081c5c0b6ce030d7e16464a55700dfe_25.png)

关键参数：
*   `name`：指定包名，可以是一个包，也可以是逗号分隔的多个包。
*   `state`：`present`或`installed`（安装），`latest`（安装最新版），`absent`或`removed`（移除）。
*   `disable_gpg_check`：禁用GPG检查。

![](img/b081c5c0b6ce030d7e16464a55700dfe_27.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_29.png)

以下是`yum`模块的用法示例：
```bash
# 安装一个软件包（如vsftpd）
ansible all -m yum -a "name=vsftpd"
# 安装多个软件包
ansible all -m yum -a "name=memcached,httpd"
# 移除软件包
ansible all -m yum -a "name=vsftpd state=absent"
# 列出所有已安装的包
ansible all -m yum -a "list=installed"
# 安装本地的rpm包（需先使用copy模块将包分发到目标主机）
ansible all -m yum -a "name=/root/vsftpd-3.0.2-25.el7.x86_64.rpm"
```

![](img/b081c5c0b6ce030d7e16464a55700dfe_31.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_33.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_35.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_36.png)

## 🔧 Service模块：管理服务状态

![](img/b081c5c0b6ce030d7e16464a55700dfe_38.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_40.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_42.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_44.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_46.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_48.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_50.png)

`service`模块用于管理服务的状态，如启动、停止、重启、重载以及设置开机自启。

![](img/b081c5c0b6ce030d7e16464a55700dfe_51.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_53.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_55.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_57.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_58.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_60.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_62.png)

关键参数：
*   `name`：服务名称。
*   `state`：`started`（启动）、`stopped`（停止）、`restarted`（重启）、`reloaded`（重载配置）。
*   `enabled`：`yes`/`no`，设置是否开机自启。

![](img/b081c5c0b6ce030d7e16464a55700dfe_64.png)

以下是`service`模块的用法示例：
```bash
# 启动vsftpd服务并设为开机自启
ansible webservers -m service -a "name=vsftpd state=started enabled=yes"
# 重启服务
ansible webservers -m service -a "name=vsftpd state=restarted"
# 停止服务并禁用开机自启
ansible webservers -m service -a "name=vsftpd state=stopped enabled=no"
```

## 👤 User模块：管理用户账户

`user`模块用于管理用户账户，可以创建、修改、删除用户，并设置各种属性。

![](img/b081c5c0b6ce030d7e16464a55700dfe_66.png)

关键参数：
*   `name`：用户名。
*   `state`：`present`（创建）、`absent`（删除）。
*   `system`：`yes`/`no`，是否为系统用户。
*   `shell`：指定用户的登录shell。
*   `home`：指定家目录路径。
*   `groups`：指定附加组。
*   `uid`：指定用户UID。
*   `comment`：用户描述信息。
*   `remove`：删除用户时是否同时删除家目录。

![](img/b081c5c0b6ce030d7e16464a55700dfe_68.png)

以下是`user`模块的用法示例：
```bash
# 创建一个系统用户nginx，指定shell、家目录、UID和描述
ansible webservers -m user -a "name=nginx shell=/sbin/nologin system=yes home=/app/nginx uid=80 comment='nginx service account'"
# 删除用户及其家目录
ansible webservers -m user -a "name=nginx state=absent remove=yes"
```

## 👥 Group模块：管理用户组

![](img/b081c5c0b6ce030d7e16464a55700dfe_70.png)

![](img/b081c5c0b6ce030d7e16464a55700dfe_72.png)

`group`模块用于管理用户组，可以创建或删除组。

关键参数：
*   `name`：组名。
*   `state`：`present`（创建）、`absent`（删除）。
*   `gid`：指定GID。
*   `system`：`yes`/`no`，是否为系统组。

以下是`group`模块的用法示例：
```bash
# 创建一个系统组nginx，并指定GID
ansible webservers -m group -a "name=nginx system=yes gid=80"
# 删除组
ansible webservers -m group -a "name=nginx state=absent"
```

## 📚 总结

![](img/b081c5c0b6ce030d7e16464a55700dfe_74.png)

本节课中我们一起学习了Ansible多个核心的企业级应用模块。我们掌握了如何使用`fetch`抓取文件，用`file`管理文件和属性，用`hostname`修改主机名，用`cron`管理计划任务。接着，我们学习了如何使用`yum`安装软件包，用`service`管理服务状态，最后用`user`和`group`模块来管理用户和组账户。

![](img/b081c5c0b6ce030d7e16464a55700dfe_76.png)

这些模块是构成Ansible自动化任务的基础单元。虽然Ansible有上千个模块，但熟练掌握这些常用模块足以应对大部分日常运维工作。记住，如果对模块用法不熟悉，随时可以使用`ansible-doc <模块名>`命令查看详细帮助文档。