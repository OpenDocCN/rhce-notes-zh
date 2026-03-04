# Linux运维全套培训课程：P51：cron计划任务与SELinux内核安全机制 🔧

![](img/6d3fd6ce5860eb799f730a7aae2d9881_1.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_3.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_5.png)

在本节课中，我们将学习Linux系统中两个重要的功能：**cron计划任务**和**SELinux内核安全机制**。计划任务能帮助我们自动化执行周期性任务，而SELinux则是系统内核层面的安全防护工具。掌握它们对于日常运维工作至关重要。

![](img/6d3fd6ce5860eb799f730a7aae2d9881_7.png)

---

![](img/6d3fd6ce5860eb799f730a7aae2d9881_9.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_11.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_13.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_15.png)

## 计划任务 cron ⏰

![](img/6d3fd6ce5860eb799f730a7aae2d9881_17.png)

上一节我们完成了Shell脚本的学习。本节中，我们来看看如何让脚本在指定时间自动运行，这就是计划任务的功能。

![](img/6d3fd6ce5860eb799f730a7aae2d9881_19.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_21.png)

计划任务软件`cron`就像一个计算机里的“闹钟”。你可以设定一个时间，到了这个时间，系统就会自动执行你指定的命令或脚本。这对于定期备份、系统维护等工作非常有用。

![](img/6d3fd6ce5860eb799f730a7aae2d9881_23.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_25.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_27.png)

### 软件包与服务

![](img/6d3fd6ce5860eb799f730a7aae2d9881_29.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_31.png)

`cron`相关的软件包通常是系统自带的，无需额外安装。
*   **软件包**：`cronie`（主包）、`cronie-anacron`（提供管理命令）
*   **服务名**：`crond`
*   **服务状态**：默认已启动并随系统自启。

你可以使用以下命令检查服务状态：
```bash
systemctl status crond
```
计划任务的执行日志记录在 `/var/log/cron` 文件中，可以查看任务是否成功执行。

### 编写计划任务

管理计划任务的核心命令是 `crontab`。
*   `crontab -e`：编辑当前用户的计划任务。
*   `crontab -l`：列出当前用户的计划任务。
*   `crontab -r`：删除当前用户的所有计划任务（慎用）。
*   `crontab -u username`：管理指定用户的计划任务（如`root`用户操作其他用户）。

![](img/6d3fd6ce5860eb799f730a7aae2d9881_33.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_35.png)

执行 `crontab -e` 会打开一个编辑器，每一行代表一条计划任务，格式如下：

```
* * * * * 要执行的命令或脚本路径
```

这五个`*`分别代表：**分钟、小时、日期、月份、星期几**。它们共同定义了任务执行的时间。

![](img/6d3fd6ce5860eb799f730a7aae2d9881_37.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_39.png)

### 时间格式详解

![](img/6d3fd6ce5860eb799f730a7aae2d9881_41.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_43.png)

时间格式的定义是计划任务的核心。以下是各字段的取值范围和含义：

| 字段 | 取值范围 | 说明 |
| :--- | :--- | :--- |
| 分钟 | 0-59 | 一小时中的第几分钟 |
| 小时 | 0-23 | 一天中的第几小时 |
| 日期 | 1-31 | 一月中的第几天 |
| 月份 | 1-12 | 一年中的第几月 |
| 星期 | 0-7 (0和7都代表周日) | 一周中的星期几 |

![](img/6d3fd6ce5860eb799f730a7aae2d9881_45.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_47.png)

**特殊符号说明：**
*   `*`：代表所有可能的值。例如在“小时”字段为`*`，表示每小时。
*   `,`：指定多个不连续的时间点。例如 `1,3,5` 在“星期”字段，表示周一、周三、周五。
*   `-`：指定一个连续的时间范围。例如 `1-5` 在“星期”字段，表示周一到周五。
*   `/`：指定时间间隔频率。例如 `*/2` 在“小时”字段，表示每两小时。

![](img/6d3fd6ce5860eb799f730a7aae2d9881_49.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_51.png)

**重要注意事项：**
1.  **“分钟”字段通常需要明确指定**。如果设为`*`，则表示每分钟都会执行，这可能不是你想要的效果。例如，希望每天3点整执行，应写为 `0 3 * * *`。
2.  **“日期”和“星期”字段最好不要同时指定**，因为它们的逻辑是“或”关系，容易产生混淆或冲突。通常使用“星期”字段来定义更直观。

![](img/6d3fd6ce5860eb799f730a7aae2d9881_53.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_55.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_57.png)

### 实战：创建日志备份计划任务

![](img/6d3fd6ce5860eb799f730a7aae2d9881_59.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_61.png)

下面我们通过一个例子，创建一个每周五凌晨3点整执行日志备份的脚本任务。

![](img/6d3fd6ce5860eb799f730a7aae2d9881_63.png)

首先，创建一个简单的备份脚本 `/script/log_backup.sh`：
```bash
#!/bin/bash
# 将/var/log目录打包，并以当前日期命名
tar -zcf /tmp/log_backup_$(date +\%Y\%m\%d_\%H\%M).tar.gz /var/log/*
```
给脚本添加执行权限：
```bash
chmod +x /script/log_backup.sh
```
然后，编辑计划任务：
```bash
crontab -e
```
在打开的文件末尾添加以下行：
```
0 3 * * 5 /script/log_backup.sh
```
这行配置的含义是：在每周五（5）的3点0分（0 3），执行指定的备份脚本。

![](img/6d3fd6ce5860eb799f730a7aae2d9881_65.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_67.png)

保存退出后，计划任务即生效。你可以通过 `crontab -l` 查看，或观察 `/tmp` 目录下是否在指定时间生成备份文件，以及查看 `/var/log/cron` 日志来验证。

---

![](img/6d3fd6ce5860eb799f730a7aae2d9881_69.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_70.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_71.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_73.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_75.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_77.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_79.png)

## SELinux 内核安全机制 🛡️

![](img/6d3fd6ce5860eb799f730a7aae2d9881_81.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_83.png)

了解了如何自动化任务后，我们来看一个可能影响服务运行的系统安全组件——SELinux。

![](img/6d3fd6ce5860eb799f730a7aae2d9881_85.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_87.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_89.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_91.png)

SELinux（Security-Enhanced Linux）是美国国家安全局集成到Linux内核中的一个**强制访问控制**安全子系统。你可以将它理解为**内核级别的防火墙**，它对进程、文件、用户等提供了更细粒度的安全策略。

![](img/6d3fd6ce5860eb799f730a7aae2d9881_93.png)

在企业环境中，由于SELinux的严格策略可能会阻止正常的应用程序运行（例如Web服务无法访问特定端口或文件），管理员常常选择将其关闭或设为宽松模式，以避免复杂的策略调整。

### SELinux 的三种运行模式

1.  **enforcing**：强制模式。违反策略的行为将被阻止并记录。
2.  **permissive**：宽松模式。违反策略的行为只会被记录，而不会被阻止。常用于调试。
3.  **disabled**：禁用模式。SELinux完全关闭。

![](img/6d3fd6ce5860eb799f730a7aae2d9881_95.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_97.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_99.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_101.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_103.png)

### 查看与修改SELinux模式

![](img/6d3fd6ce5860eb799f730a7aae2d9881_105.png)

**查看当前模式：**
```bash
getenforce
```
**临时修改模式（重启后失效）：**
```bash
# 设置为强制模式
setenforce 1
# 设置为宽松模式
setenforce 0
# 注意：无法通过此命令直接设置为 disabled 模式
```
**永久修改模式：**
需要编辑配置文件 `/etc/selinux/config`。
```bash
vim /etc/selinux/config
```
找到 `SELINUX=` 这一行，将其值修改为 `enforcing`、`permissive` 或 `disabled`。
例如，要永久禁用SELinux：
```
SELINUX=disabled
```
**修改配置文件后，必须重启系统才能生效。**

![](img/6d3fd6ce5860eb799f730a7aae2d9881_107.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_109.png)

**常用操作组合：**
1.  临时设为宽松模式，以便测试服务：`setenforce 0`
2.  永久关闭SELinux：修改配置文件为 `SELINUX=disabled`，然后重启系统。

---

## 总结 📚

![](img/6d3fd6ce5860eb799f730a7aae2d9881_111.png)

本节课中我们一起学习了两个重要的系统工具：
1.  **cron计划任务**：我们掌握了如何使用 `crontab` 命令创建、管理和删除计划任务，重点理解了其时间格式（分、时、日、月、周）的定义方法以及特殊符号（`*`， `,`， `-`， `/`）的使用。通过创建自动备份脚本的任务，我们实践了自动化运维的基本步骤。
2.  **SELinux内核安全机制**：我们了解了SELinux作为内核级安全模块的作用，学习了其三种运行模式（enforcing， permissive， disabled），并掌握了通过 `getenforce`/`setenforce` 命令临时查看、修改模式，以及通过编辑 `/etc/selinux/config` 配置文件来永久修改模式的方法。

![](img/6d3fd6ce5860eb799f730a7aae2d9881_113.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_115.png)

![](img/6d3fd6ce5860eb799f730a7aae2d9881_117.png)

合理使用计划任务可以极大提升运维效率，而正确理解SELinux的状态则有助于排除因权限问题导致的服务故障。