# Linux最全RHCSA+RHCE培训教程合集，小白入门必备！ - P51：红帽RHCE-15. cron计划任务、SELinux内核安全机制

![](img/2ddfd18f6125966befca15f04f6dfd9e_1.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_3.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_5.png)

在本节课中，我们将要学习Linux系统中两个重要的管理工具：**cron计划任务**和**SELinux内核安全机制**。计划任务能帮助我们自动化周期性工作，而SELinux则是系统内核层面的安全防护机制。掌握它们对于系统管理员至关重要。

![](img/2ddfd18f6125966befca15f04f6dfd9e_7.png)

## cron计划任务

![](img/2ddfd18f6125966befca15f04f6dfd9e_9.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_11.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_13.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_15.png)

上一节我们介绍了Shell脚本，本节中我们来看看如何让脚本在指定时间自动运行，这就是计划任务的功能。

![](img/2ddfd18f6125966befca15f04f6dfd9e_17.png)

计划任务软件`cron`就像一个计算机里的“闹钟”。它可以在你设定的固定时间，自动执行预设的命令或脚本。例如，企业通常在凌晨服务器空闲时进行数据备份，这时就可以利用`cron`来自动执行备份脚本，实现运维工作的自动化。

![](img/2ddfd18f6125966befca15f04f6dfd9e_19.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_21.png)

### 软件包与服务

![](img/2ddfd18f6125966befca15f04f6dfd9e_23.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_25.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_27.png)

`cron`相关的软件包通常是系统自带的，无需额外安装。

![](img/2ddfd18f6125966befca15f04f6dfd9e_29.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_31.png)

*   **软件包**：`cronie`（主程序包）、`crontabs`（提供管理命令）
*   **服务名**：`crond`
*   **服务状态**：默认已启动并随系统自启。

你可以使用以下命令检查服务状态：
```bash
systemctl status crond
```
*   **日志文件**：`/var/log/cron`，该文件记录了所有计划任务的执行情况。

### 编写计划任务

管理计划任务的核心命令是`crontab`。

![](img/2ddfd18f6125966befca15f04f6dfd9e_33.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_35.png)

*   `crontab -e`：编辑当前用户的计划任务。
*   `crontab -l`：列出当前用户的计划任务。
*   `crontab -r`：删除当前用户的所有计划任务（慎用）。
*   `crontab -u username -e`：编辑指定用户的计划任务（需管理员权限）。

执行`crontab -e`会打开一个属于当前用户的专属任务文件进行编辑。

![](img/2ddfd18f6125966befca15f04f6dfd9e_37.png)

### 时间格式详解

![](img/2ddfd18f6125966befca15f04f6dfd9e_39.png)

计划任务配置行的核心是时间定义，格式如下：
```
分 时 日 月 周 要执行的命令
```
*   **分**：分钟 (0-59)
*   **时**：小时 (0-23)
*   **日**：一个月中的第几天 (1-31)
*   **月**：月份 (1-12)
*   **周**：星期几 (0-7，0和7都代表周日)

![](img/2ddfd18f6125966befca15f04f6dfd9e_41.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_43.png)

特殊符号说明：
*   `*`：代表“每”。例如分钟位置的`*`表示每分钟。
*   `,`：代表“不连续的时间”。例如周位置的`1,3,5`表示每周一、三、五。
*   `-`：代表“连续的时间范围”。例如周位置的`1-5`表示周一到周五。
*   `/`：代表“间隔频率”。例如分钟位置的`*/5`表示每5分钟。

**重要注意事项**：
1.  **分钟必须指定**：即使你想在整点执行，分钟位也应写为`0`，而不是`*`。`*`会导致该小时内每分钟都执行。
2.  **“日”和“周”避免同时指定**：两者同时定义可能产生冲突，导致任务不执行。通常指定“周”更为直观。

![](img/2ddfd18f6125966befca15f04f6dfd9e_45.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_47.png)

### 实践示例：创建备份计划任务

![](img/2ddfd18f6125966befca15f04f6dfd9e_49.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_51.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_53.png)

假设我们有一个备份日志的脚本`/scripts/log_backup.sh`，内容如下：
```bash
#!/bin/bash
tar -zcf /tmp/log_backup_$(date +\%F_\%H\%M).tar.gz /var/log/*
```
请确保脚本有执行权限：`chmod +x /scripts/log_backup.sh`。

![](img/2ddfd18f6125966befca15f04f6dfd9e_55.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_57.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_59.png)

以下是几个计划任务配置示例：

![](img/2ddfd18f6125966befca15f04f6dfd9e_61.png)

*   **每周五凌晨3点整执行备份**：
    ```
    0 3 * * 5 /scripts/log_backup.sh
    ```
*   **每周一、三、五的凌晨2点30分执行备份**：
    ```
    30 2 * * 1,3,5 /scripts/log_backup.sh
    ```
*   **每天凌晨2点30分执行备份**：
    ```
    30 2 * * * /scripts/log_backup.sh
    ```
*   **每周一到周五，每隔2小时整点执行一次备份**：
    ```
    0 */2 * * 1-5 /scripts/log_backup.sh
    ```
*   **每分钟执行一次（用于测试）**：
    ```
    * * * * * /scripts/log_backup.sh
    ```

![](img/2ddfd18f6125966befca15f04f6dfd9e_63.png)

编写完成后保存退出，`cron`会自动加载新任务。你可以通过查看`/tmp/`目录下是否生成新的备份文件，或检查`/var/log/cron`日志来验证任务是否执行。

![](img/2ddfd18f6125966befca15f04f6dfd9e_65.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_67.png)

若要修改或删除某条任务，重新使用`crontab -e`进入编辑模式进行操作即可。

## SELinux内核安全机制

![](img/2ddfd18f6125966befca15f04f6dfd9e_69.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_71.png)

接下来，我们了解一个Linux内核层面的安全增强工具——SELinux。

![](img/2ddfd18f6125966befca15f04f6dfd9e_73.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_75.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_77.png)

SELinux（Security-Enhanced Linux）是一个由美国国家安全局（NSA）开发的**强制访问控制（MAC）系统**。你可以将其理解为内核内置的、更精细的防火墙。它对系统上的进程、文件、目录等资源都设有预设的保护策略。

![](img/2ddfd18f6125966befca15f04f6dfd9e_79.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_81.png)

在企业环境中，由于SELinux策略复杂，管理难度大，常常会选择将其**关闭**，以避免其对正常服务运行造成不必要的阻碍。作为初学者，我们的重点是学会查看和设置它的运行模式。

### SELinux的三种运行模式

1.  **enforcing**：强制模式。违反SELinux策略的行为将被阻止并记录。
2.  **permissive**：宽松模式。违反策略的行为仅被记录，而不被阻止。常用于调试。
3.  **disabled**：禁用模式。SELinux完全关闭。

![](img/2ddfd18f6125966befca15f04f6dfd9e_83.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_85.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_87.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_89.png)

### 管理SELinux模式

![](img/2ddfd18f6125966befca15f04f6dfd9e_91.png)

*   **查看当前模式**：
    ```bash
    getenforce
    ```

![](img/2ddfd18f6125966befca15f04f6dfd9e_93.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_95.png)

*   **临时更改模式**（重启后失效）：
    ```bash
    setenforce 1  # 设置为 enforcing 模式
    setenforce 0  # 设置为 permissive 模式
    ```
    **注意**：`setenforce`命令无法将模式直接改为`disabled`。

*   **永久更改模式**（需修改配置文件）：
    1.  编辑SELinux配置文件：`vim /etc/selinux/config`
    2.  找到 `SELINUX=` 这一行，将其值修改为 `enforcing`、`permissive` 或 `disabled`。
        ```
        SELINUX=disabled
        ```
    3.  保存文件。**修改为`disabled`后，必须重启系统才能生效**。

**常用操作流程**：如果希望永久关闭SELinux，可以先通过`setenforce 0`将其临时设为宽松模式，然后编辑配置文件设为`disabled`，最后安排重启。这样既能立即避免干扰，又能保证重启后彻底关闭。

![](img/2ddfd18f6125966befca15f04f6dfd9e_97.png)

---

![](img/2ddfd18f6125966befca15f04f6dfd9e_99.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_101.png)

![](img/2ddfd18f6125966befca15f04f6dfd9e_103.png)

本节课中我们一起学习了`cron`计划任务和SELinux安全机制。`cron`是自动化运维的利器，重点在于掌握灵活的时间格式定义。而SELinux作为高级安全模块，你需要知道它的三种运行模式以及如何查看和设置它们。在实际工作中，根据需求合理使用计划任务，并根据公司策略决定SELinux的开启与否，是系统管理员的基本技能。