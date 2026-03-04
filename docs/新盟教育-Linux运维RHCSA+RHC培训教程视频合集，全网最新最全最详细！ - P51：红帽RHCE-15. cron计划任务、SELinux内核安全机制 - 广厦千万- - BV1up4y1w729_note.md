# Linux运维RHCSA+RHCE培训教程：P51：cron计划任务与SELinux内核安全机制 🔧

![](img/7f1c1210e1c15a62c1785a92b1343dea_1.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_3.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_5.png)

在本节课中，我们将要学习Linux系统中两个重要的功能：`cron`计划任务和`SELinux`内核安全机制。计划任务可以帮助我们自动化执行周期性任务，而SELinux则提供了内核级别的安全访问控制。我们将从基本概念开始，逐步学习如何配置和使用它们。

![](img/7f1c1210e1c15a62c1785a92b1343dea_7.png)

---

![](img/7f1c1210e1c15a62c1785a92b1343dea_9.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_10.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_12.png)

## 计划任务（cron）⏰

![](img/7f1c1210e1c15a62c1785a92b1343dea_13.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_15.png)

上一节我们介绍了Shell脚本，它可以帮助我们完成一系列自动化操作。本节中我们来看看如何让这些脚本在指定的时间自动运行，这就需要用到`cron`计划任务。

![](img/7f1c1210e1c15a62c1785a92b1343dea_17.png)

`cron`是一个应用程序，用于实现周期性的计划任务。它的主要用途是定期执行某些程序或命令，例如定期备份数据。你可以将它理解为一个计算机里的“闹钟”，可以在你设定的固定时间执行预设的操作。

![](img/7f1c1210e1c15a62c1785a92b1343dea_19.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_20.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_22.png)

### 1. cron基础

![](img/7f1c1210e1c15a62c1785a92b1343dea_24.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_25.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_27.png)

`cron`软件包通常随系统默认安装，其服务名为`crond`，并且默认已启动。

**检查cron服务状态：**
```bash
systemctl status crond
```
**查看cron日志：**
cron的执行记录会保存在`/var/log/cron`文件中。

### 2. 编写cron计划任务

管理cron任务的命令是`crontab`。

![](img/7f1c1210e1c15a62c1785a92b1343dea_29.png)

**编辑当前用户的cron任务：**
```bash
crontab -e
```
**列出当前用户的cron任务：**
```bash
crontab -l
```
**删除当前用户的所有cron任务：**
```bash
crontab -r
```

![](img/7f1c1210e1c15a62c1785a92b1343dea_31.png)

### 3. 时间格式定义

cron任务配置的核心在于时间格式的定义。其基本格式如下：
```
* * * * * command_to_execute
```
这五个星号依次代表：
*   分钟 (0-59)
*   小时 (0-23)
*   日期 (1-31)
*   月份 (1-12)
*   星期几 (0-6，其中0代表星期日)

![](img/7f1c1210e1c15a62c1785a92b1343dea_33.png)

**时间格式示例：**
以下是几个常用的时间定义示例：
*   `0 3 * * 5 /path/to/script.sh`：每周五凌晨3点整执行。
*   `30 2 * * 1-5 /path/to/script.sh`：每周一到周五的凌晨2点30分执行。
*   `*/5 * * * * /path/to/script.sh`：每5分钟执行一次。
*   `0 */2 * * * /path/to/script.sh`：每2小时执行一次（在整点）。
*   `0 3 1 * * /path/to/script.sh`：每月1号凌晨3点执行。

![](img/7f1c1210e1c15a62c1785a92b1343dea_35.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_37.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_39.png)

**重要提示：**
*   “分钟”字段通常需要明确指定（如`0`或`30`），使用`*`会每分钟都执行。
*   “日期”和“星期几”字段最好不要同时指定，以免产生冲突导致任务不执行。

### 4. 实践：创建一个备份脚本的cron任务

![](img/7f1c1210e1c15a62c1785a92b1343dea_41.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_43.png)

接下来，我们通过一个实例来巩固所学内容。我们将创建一个备份日志的脚本，并设置cron任务定期执行它。

![](img/7f1c1210e1c15a62c1785a92b1343dea_45.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_47.png)

**第一步：创建备份脚本**
创建一个名为`log_backup.sh`的脚本：
```bash
#!/bin/bash
tar -zcf /tmp/log_backup_$(date +\%Y\%m\%d_\%H\%M).tar.gz /var/log/*
```
给脚本添加执行权限：
```bash
chmod +x /path/to/log_backup.sh
```

![](img/7f1c1210e1c15a62c1785a92b1343dea_49.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_51.png)

**第二步：设置cron任务**
编辑cron任务：
```bash
crontab -e
```
添加以下行，表示每周五凌晨3点执行备份脚本：
```
0 3 * * 5 /path/to/log_backup.sh
```
保存并退出后，任务即会生效。你可以通过查看`/tmp`目录下是否生成新的压缩包，或检查`/var/log/cron`日志来验证任务是否执行。

![](img/7f1c1210e1c15a62c1785a92b1343dea_53.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_55.png)

---

![](img/7f1c1210e1c15a62c1785a92b1343dea_57.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_59.png)

## SELinux内核安全机制 🛡️

![](img/7f1c1210e1c15a62c1785a92b1343dea_61.png)

在掌握了计划任务后，我们再来了解另一个系统层面的重要概念——SELinux。它是一种由美国国家安全局（NSA）主导开发的Linux内核安全模块，提供了强制访问控制（MAC）机制，可以将其理解为“内核防火墙”。

![](img/7f1c1210e1c15a62c1785a92b1343dea_63.png)

在企业环境中，SELinux的管理较为复杂，因此很多时候会选择将其关闭或设置为宽松模式。作为初学者，我们主要需要了解其运行模式及如何查看和修改。

![](img/7f1c1210e1c15a62c1785a92b1343dea_65.png)

### 1. SELinux的三种运行模式

![](img/7f1c1210e1c15a62c1785a92b1343dea_67.png)

SELinux有三种运行模式：
1.  **enforcing**：强制模式。SELinux强制执行安全策略，阻止未经授权的访问。
2.  **permissive**：宽松模式。SELinux仅记录违规行为而不阻止，用于监控和调试。
3.  **disabled**：禁用模式。SELinux完全关闭。

![](img/7f1c1210e1c15a62c1785a92b1343dea_69.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_71.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_73.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_75.png)

### 2. 查看与修改SELinux模式

![](img/7f1c1210e1c15a62c1785a92b1343dea_77.png)

**查看当前SELinux模式：**
```bash
getenforce
```

**临时修改SELinux模式（重启后失效）：**
```bash
# 设置为强制模式
setenforce 1
# 或 setenforce enforcing

# 设置为宽松模式
setenforce 0
# 或 setenforce permissive
```
*注意：`setenforce`命令无法将模式直接改为`disabled`。*

![](img/7f1c1210e1c15a62c1785a92b1343dea_79.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_81.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_83.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_85.png)

**永久修改SELinux模式：**
需要编辑配置文件`/etc/selinux/config`。
```bash
vim /etc/selinux/config
```
找到`SELINUX=`这一行，将其值修改为`enforcing`、`permissive`或`disabled`之一。
```
SELINUX=disabled
```
修改配置文件后，**必须重启系统**才能生效。在实际操作中，可以先用`setenforce 0`临时设为宽松模式，然后修改配置文件为`disabled`并安排重启，这样既能立即减少限制，又能永久关闭SELinux。

![](img/7f1c1210e1c15a62c1785a92b1343dea_87.png)

---

![](img/7f1c1210e1c15a62c1785a92b1343dea_89.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_91.png)

## 总结 📚

本节课中我们一起学习了Linux系统中两个关键的管理功能。

首先，我们深入探讨了**cron计划任务**。我们学习了cron的基本概念、如何编辑和管理任务，最重要的是掌握了其核心——**时间格式的定义**。通过创建备份脚本并设置定时任务的实践，我们掌握了如何利用cron实现工作的自动化。

![](img/7f1c1210e1c15a62c1785a92b1343dea_93.png)

其次，我们介绍了**SELinux内核安全机制**。我们了解了它的三种运行模式（enforcing, permissive, disabled），并学习了如何通过`getenforce`、`setenforce`命令以及修改`/etc/selinux/config`配置文件来查看和切换其模式。这对于处理某些因SELinux策略导致的权限问题非常有用。

![](img/7f1c1210e1c15a62c1785a92b1343dea_95.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_96.png)

![](img/7f1c1210e1c15a62c1785a92b1343dea_98.png)

将计划任务与之前学习的Shell脚本结合，可以构建强大的自动化运维体系。而对SELinux的基本了解，则有助于我们在遇到相关安全策略问题时，能够进行基本的排查和设置。