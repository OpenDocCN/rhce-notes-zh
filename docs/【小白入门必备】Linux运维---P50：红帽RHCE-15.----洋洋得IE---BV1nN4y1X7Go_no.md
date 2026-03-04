# Linux运维进阶：P50：cron计划任务与SELinux内核安全机制

![](img/1cb49d5924db1d52aad9193bd5b0c188_1.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_3.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_5.png)

在本节课中，我们将要学习Linux系统中两个重要的功能：**cron计划任务**和**SELinux内核安全机制**。计划任务能帮助我们自动化周期性工作，而SELinux则提供了内核级别的安全控制。掌握它们对于系统运维至关重要。

![](img/1cb49d5924db1d52aad9193bd5b0c188_7.png)

## cron计划任务

![](img/1cb49d5924db1d52aad9193bd5b0c188_9.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_11.png)

上一节我们结束了Shell脚本的学习。本节中我们来看看如何让脚本在指定时间自动运行，这就是计划任务的功能。

![](img/1cb49d5924db1d52aad9193bd5b0c188_13.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_15.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_17.png)

计划任务（cron）是一个应用程序，它可以帮我们实现**周期性**的计划任务，用来定期执行某些程序。目前最主要的用途是定期备份数据。

通俗地讲，计划任务就像计算机里的一个“闹钟”。你设定好时间，它就会在固定的时间点帮你执行预设的操作（命令或脚本）。例如，企业通常在服务器不忙的凌晨进行数据备份，这时就可以利用计划任务来自动执行备份脚本，实现工作自动化。

![](img/1cb49d5924db1d52aad9193bd5b0c188_19.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_21.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_23.png)

### 计划任务基础

![](img/1cb49d5924db1d52aad9193bd5b0c188_25.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_27.png)

计划任务的软件包名为 `cronie`，而 `crontabs` 包则提供了管理命令。这两个包在系统安装时通常已默认安装，无需额外操作。

![](img/1cb49d5924db1d52aad9193bd5b0c188_29.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_31.png)

以下是检查服务状态的命令：
```bash
systemctl status crond
```
该服务默认是启动（`running`）并随系统自启的。

计划任务的执行日志记录在 `/var/log/cron` 文件中，可以查看任务是否按时执行以及执行结果。

### 编写计划任务

管理计划任务的核心命令是 `crontab`。

![](img/1cb49d5924db1d52aad9193bd5b0c188_33.png)

*   `crontab -e`：编辑当前用户的计划任务。
*   `crontab -l`：列出当前用户的计划任务。
*   `crontab -r`：删除当前用户的所有计划任务。
*   `crontab -u username`：管理指定用户的计划任务（如 `-e`， `-l`）。

![](img/1cb49d5924db1d52aad9193bd5b0c188_35.png)

执行 `crontab -e` 会打开一个属于当前用户的专属任务文件进行编辑。

### 时间格式定义

![](img/1cb49d5924db1d52aad9193bd5b0c188_37.png)

计划任务配置行的核心是时间定义，格式如下：
```
分 时 日 月 周 要执行的命令
```
*   **分**：分钟 (0-59)
*   **时**：小时 (0-23)
*   **日**：一个月中的第几天 (1-31)
*   **月**：月份 (1-12)
*   **周**：星期几 (0-6，0代表星期日)

![](img/1cb49d5924db1d52aad9193bd5b0c188_39.png)

特殊符号说明：
*   `*`：代表“每”。例如分钟位置的 `*` 表示每分钟。
*   `,`：代表不连续的时间点。例如周位置的 `1,3,5` 表示每周一、三、五。
*   `-`：代表连续的时间范围。例如周位置的 `1-5` 表示周一到周五。
*   `/`：代表间隔频率。例如分钟位置的 `*/2` 表示每2分钟。

![](img/1cb49d5924db1d52aad9193bd5b0c188_41.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_43.png)

**重要提示**：
1.  “分钟”字段通常需要明确指定，如果设为 `*`，则表示该小时内的每一分钟都会执行，可能导致任务执行次数远超预期。整点执行应用 `0` 表示。
2.  “日”（日期）和“周”（星期几）字段最好不要同时指定具体值，容易产生逻辑冲突导致任务不执行。通常指定其中一个即可。

### 实践示例

![](img/1cb49d5924db1d52aad9193bd5b0c188_45.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_47.png)

假设我们有一个备份日志的脚本 `/root/scripts/log_backup.sh`，内容如下：
```bash
#!/bin/bash
tar -zcf /tmp/log_$(date +\%F_\%H\%M).tar.gz /var/log/*
```

![](img/1cb49d5924db1d52aad9193bd5b0c188_49.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_51.png)

我们希望**每周五凌晨3点整**执行此备份。

![](img/1cb49d5924db1d52aad9193bd5b0c188_53.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_55.png)

以下是计划任务的编写步骤：
1.  赋予脚本执行权限：`chmod +x /root/scripts/log_backup.sh`
2.  编辑计划任务：`crontab -e`
3.  在文件中添加一行：
    ```
    0 3 * * 5 /root/scripts/log_backup.sh
    ```
    （解析：分钟=0，小时=3，日期=*，月份=*，周几=5）
4.  保存并退出编辑器。

![](img/1cb49d5924db1d52aad9193bd5b0c188_57.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_59.png)

要验证任务是否添加成功，可以运行 `crontab -l` 查看。任务执行后，可以在 `/tmp` 目录下看到生成的备份文件，或在 `/var/log/cron` 日志中查看执行记录。

![](img/1cb49d5924db1d52aad9193bd5b0c188_61.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_63.png)

若要删除所有计划任务，使用 `crontab -r`。若要编辑或删除部分任务，建议使用 `crontab -e` 进入文件进行修改。

---

![](img/1cb49d5924db1d52aad9193bd5b0c188_65.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_67.png)

## SELinux内核安全机制

了解了如何自动化任务后，我们来看一个影响系统服务和安全的重要机制——SELinux。

![](img/1cb49d5924db1d52aad9193bd5b0c188_69.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_71.png)

SELinux（Security-Enhanced Linux）是美国国家安全局（NSA）集成到Linux内核（2.6及以上版本）的一套**强制访问控制体系**。你可以将其理解为“内核级别的防火墙”，它对系统上的进程、文件、目录等提供了预设的保护策略。

![](img/1cb49d5924db1d52aad9193bd5b0c188_73.png)

在企业环境中，SELinux常常被**禁用**。因为如果开启，它可能会严格限制某些服务或软件的运行，导致服务启动失败或访问异常，而配置其规则相对复杂。

![](img/1cb49d5924db1d52aad9193bd5b0c188_75.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_77.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_79.png)

### SELinux运行模式

![](img/1cb49d5924db1d52aad9193bd5b0c188_81.png)

SELinux有三种运行模式：
1.  **enforcing**：强制模式。启用并强制执行安全策略。
2.  **permissive**：宽松模式。启用安全策略，但仅记录违规行为而不阻止。相当于只警告不拦截。
3.  **disabled**：禁用模式。完全关闭SELinux。

### 管理SELinux模式

**查看当前模式**：
```bash
getenforce
```

![](img/1cb49d5924db1d52aad9193bd5b0c188_83.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_85.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_87.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_89.png)

**临时更改模式**（重启后失效）：
```bash
# 设置为强制模式
setenforce 1
# 或 setenforce enforcing

![](img/1cb49d5924db1d52aad9193bd5b0c188_91.png)

# 设置为宽松模式
setenforce 0
# 或 setenforce permissive
```
注意：`setenforce` 命令无法将模式直接改为 `disabled`。

![](img/1cb49d5924db1d52aad9193bd5b0c188_93.png)

**永久更改模式**（需修改配置文件）：
1.  编辑SELinux配置文件：`vim /etc/selinux/config`
2.  找到 `SELINUX=` 这一行，将其值修改为 `disabled`（或 `enforcing`， `permissive`）。
    ```
    SELINUX=disabled
    ```
3.  保存并退出。
4.  **修改配置文件后，必须重启系统才能生效**。

![](img/1cb49d5924db1d52aad9193bd5b0c188_95.png)

**常用操作组合**：
为了立即生效且永久关闭，可以：
1.  临时设为宽松模式：`setenforce 0`
2.  编辑配置文件永久禁用：将 `SELINUX` 改为 `disabled`
3.  安排重启系统。

---

![](img/1cb49d5924db1d52aad9193bd5b0c188_97.png)

## 总结

本节课中我们一起学习了两个重要的系统管理主题：
1.  **cron计划任务**：我们学习了其作用、`crontab`命令的使用方法，以及核心的**时间格式定义**。重点是掌握 `分 时 日 月 周` 的格式和 `*`， `,`， `-`， `/` 等特殊符号的用法，并能将脚本与计划任务结合实现自动化。
2.  **SELinux内核安全机制**：我们了解了SELinux的概念、三种运行模式（**enforcing**， **permissive**， **disabled**），以及通过 `getenforce`， `setenforce` 命令和 `/etc/selinux/config` 配置文件来查看和修改其状态的方法。在实际运维中，根据需求合理配置（通常是禁用）SELinux是一项常见操作。

![](img/1cb49d5924db1d52aad9193bd5b0c188_99.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_101.png)

![](img/1cb49d5924db1d52aad9193bd5b0c188_103.png)

通过掌握计划任务，你可以让系统更智能地工作；而理解SELinux，则能帮助你处理一些因权限限制导致的疑难问题。