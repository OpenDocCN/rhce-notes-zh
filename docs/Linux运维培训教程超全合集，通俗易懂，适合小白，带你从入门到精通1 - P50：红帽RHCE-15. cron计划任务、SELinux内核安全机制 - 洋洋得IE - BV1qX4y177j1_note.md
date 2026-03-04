# Linux运维培训教程：P50：cron计划任务与SELinux内核安全机制 🔧

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_1.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_3.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_5.png)

在本节课中，我们将要学习Linux系统中两个重要的功能：**cron计划任务**和**SELinux内核安全机制**。计划任务能帮助我们自动化周期性工作，而SELinux则是系统内核层面的安全卫士。掌握它们对于系统运维至关重要。

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_7.png)

## 计划任务（cron）⏰

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_9.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_11.png)

上一节我们介绍了Shell脚本，本节中我们来看看如何让脚本在指定时间自动运行。计划任务（cron）就像一个计算机里的闹钟，它可以在你设定的时间自动执行命令或脚本，常用于定期备份、系统维护等，是实现工作自动化的利器。

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_13.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_15.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_17.png)

### cron基础

cron由两个主要软件包提供：`cronie`（主程序）和`crontabs`（提供管理命令）。它们通常在系统安装时就已经存在，并且服务（`crond`）默认是启动并随系统自启的。

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_19.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_21.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_23.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_25.png)

*   **检查服务状态**：
    ```bash
    systemctl status crond
    ```
*   **查看执行日志**：
    ```bash
    cat /var/log/cron
    ```

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_27.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_29.png)

### 编写计划任务

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_31.png)

管理计划任务的核心命令是 `crontab`。

*   **编辑当前用户的任务**：`crontab -e`
*   **列出当前用户的任务**：`crontab -l`
*   **删除当前用户的所有任务**：`crontab -r`

**注意**：每个用户都可以管理自己的计划任务。以root用户执行，则编辑的是root的计划任务；以普通用户执行，则编辑该用户自己的计划任务。

### 时间格式详解 ⏳

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_33.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_35.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_37.png)

计划任务最关键的部分是时间定义。其格式由5个字段组成，分别代表：**分钟、小时、日期、月份、星期几**。

格式如下：
```
* * * * * 要执行的命令
分 时 日 月 周
```

以下是各字段的取值范围和说明：

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_39.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_41.png)

*   **分钟**：0-59
*   **小时**：0-23
*   **日期**：1-31
*   **月份**：1-12
*   **星期几**：0-7 (0和7都代表星期日)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_43.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_45.png)

特殊符号的含义：

*   `*`：代表所有可能的值（例如，在分钟字段表示每分钟）。
*   `,`：指定多个不连续的时间点（例如 `1,3,5` 在星期字段表示周一、周三、周五）。
*   `-`：指定一个连续的时间范围（例如 `1-5` 在星期字段表示周一到周五）。
*   `/`：指定时间间隔频率（例如 `*/2` 在小时字段表示每2小时）。

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_47.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_49.png)

**重要提示**：
1.  **分钟字段通常需要明确指定**。如果设为 `*`，则表示每分钟都会执行，这可能不是你想要的效果。通常整点执行我们用 `0` 表示。
2.  **“日期”和“星期几”字段最好不要同时指定具体值**，否则容易产生逻辑冲突，可能导致任务不执行。通常两者只指定一个，另一个用 `*` 表示。

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_51.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_53.png)

### 实战：创建一个备份任务 📦

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_55.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_57.png)

假设我们有一个备份日志的脚本 `/script/log_back.sh`，内容如下：
```bash
#!/bin/bash
tar -zcf /tmp/log_back_$(date +%F_%H%M).tar.gz /var/log/*
```

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_59.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_61.png)

我们希望**每周五凌晨3点整**执行这个备份。

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_63.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_65.png)

1.  给脚本添加执行权限：
    ```bash
    chmod +x /script/log_back.sh
    ```
2.  编辑计划任务：
    ```bash
    crontab -e
    ```
3.  在打开的文件中添加一行：
    ```
    0 3 * * 5 /script/log_back.sh
    ```
    *   `0`：第0分钟（整点）。
    *   `3`：凌晨3点。
    *   `*`：每一天（由于指定了星期几，此字段忽略）。
    *   `*`：每一月。
    *   `5`：星期五。

保存退出后，计划任务即生效。系统会在每周五03:00自动执行备份脚本，你可以在 `/tmp` 目录下看到生成的备份文件，并在 `/var/log/cron` 日志中查看执行记录。

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_67.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_69.png)

---

## SELinux内核安全机制 🛡️

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_71.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_72.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_74.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_76.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_78.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_80.png)

上一节我们学习了如何自动化任务，本节中我们来看看Linux系统一个内核级别的安全组件——SELinux。它是美国国家安全局（NSA）开发的一套强制访问控制（MAC）系统，用于增强Linux的安全性。你可以把它理解为**内核自带的防火墙**，对进程、文件、目录等提供更细粒度的控制。

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_82.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_84.png)

### SELinux的三种运行模式

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_86.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_88.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_90.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_92.png)

在企业环境中，由于SELinux规则配置复杂，有时会干扰正常服务运行，因此常将其关闭或设为宽松模式。

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_94.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_96.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_98.png)

1.  **enforcing**：强制模式。违反SELinux策略的行为将被阻止并记录。
2.  **permissive**：宽松模式。违反策略的行为只会被记录，而不会被阻止。常用于调试。
3.  **disabled**：禁用模式。SELinux完全关闭。

### 管理SELinux模式

以下是管理SELinux运行模式的命令和配置文件。

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_100.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_102.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_104.png)

*   **查看当前模式**：
    ```bash
    getenforce
    ```
*   **临时修改模式**（重启后失效）：
    ```bash
    setenforce 1  # 设置为强制模式 (enforcing)
    setenforce 0  # 设置为宽松模式 (permissive)
    ```
    **注意**：`setenforce` 命令无法将模式直接改为 `disabled`。

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_106.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_108.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_110.png)

*   **永久修改模式**（需修改配置文件并重启）：
    配置文件位于 `/etc/selinux/config`。
    ```bash
    vim /etc/selinux/config
    ```
    找到 `SELINUX=` 这一行，将其值修改为 `enforcing`、`permissive` 或 `disabled` 之一。
    例如，要永久禁用SELinux，则修改为：
    ```
    SELINUX=disabled
    ```
    **修改配置文件后，必须重启系统才能生效。**

**常见操作**：如果不想立即重启，可以先执行 `setenforce 0` 临时设为宽松模式，然后修改配置文件为 `disabled`，这样下次重启后就会永久禁用。

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_112.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_114.png)

---

## 总结 📝

本节课中我们一起学习了两个重要的系统管理工具：

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_116.png)

1.  **cron计划任务**：我们掌握了如何使用 `crontab` 命令创建、管理和删除计划任务，重点理解了其复杂但强大的时间定义格式（分、时、日、月、周），并通过一个备份脚本实例进行了演练。这是实现运维自动化的基础技能。
2.  **SELinux内核安全机制**：我们了解了SELinux作为内核级安全模块的作用，学习了它的三种运行模式（enforcing, permissive, disabled），并掌握了通过 `getenforce`/`setenforce` 命令临时查看、修改模式，以及通过编辑 `/etc/selinux/config` 文件永久修改模式的方法。在实际工作中，根据需求合理配置SELinux模式是保证系统安全或服务正常运行的关键一步。

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_118.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_120.png)

![](img/b4c0b7fc2d606d1a1e84dd9fe775124e_122.png)

通过结合脚本与计划任务，你可以让系统在无人值守的情况下完成许多重复性工作；而理解SELinux则能帮助你在更安全或更兼容的环境下部署服务。