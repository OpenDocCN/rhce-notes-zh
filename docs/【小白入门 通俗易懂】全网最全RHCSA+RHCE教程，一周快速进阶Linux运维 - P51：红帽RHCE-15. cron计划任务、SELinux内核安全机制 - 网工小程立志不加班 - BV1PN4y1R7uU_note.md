# Linux运维教程：P51：cron计划任务与SELinux内核安全机制 🔧

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_1.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_3.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_5.png)

在本节课中，我们将学习Linux系统中两个重要的功能：**cron计划任务**和**SELinux内核安全机制**。计划任务能帮助我们自动化执行周期性任务，而SELinux则提供了内核级别的安全访问控制。我们将学习它们的基本概念、配置方法以及在实际工作中的简单应用。

---

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_7.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_9.png)

## 计划任务（cron）⏰

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_11.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_13.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_15.png)

上一节我们完成了Shell脚本的学习。本节中，我们来看看如何让脚本在指定时间自动运行，这就需要用到**cron计划任务**。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_17.png)

cron是一个应用程序，用于实现**周期性的计划任务**。它的主要用途是定期执行某些程序或命令，例如定期备份数据。你可以将它理解为一个“计算机闹钟”，在预设的时间点自动执行你设定的操作。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_19.png)

### cron基础组件

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_21.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_23.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_25.png)

cron由以下主要部分组成：
*   **软件包**：`cronie`（主程序包）和 `crontabs`（提供管理命令）。
*   **服务名**：`crond`。
*   **日志文件**：`/var/log/cron`，用于记录计划任务的执行情况。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_27.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_29.png)

这些组件在系统安装时通常已默认安装并随系统自动启动。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_31.png)

### 编写cron计划任务

管理cron计划任务的核心命令是 `crontab`。

*   `crontab -e`：编辑当前用户的计划任务。
*   `crontab -l`：列出当前用户的计划任务。
*   `crontab -r`：删除当前用户的所有计划任务（慎用）。
*   `crontab -u username`：管理指定用户的计划任务（需root权限）。

执行 `crontab -e` 会打开一个属于当前用户的专属任务文件进行编辑。

### 时间格式定义

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_33.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_35.png)

cron任务行的格式如下，**时间定义是其核心**：

```
分 时 日 月 周 要执行的命令
```

*   **分**：0-59
*   **时**：0-23
*   **日**：1-31
*   **月**：1-12
*   **周**：0-7 (0和7都代表周日，1=周一，6=周六)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_37.png)

特殊符号的含义：
*   `*`：代表“每”，例如在“分”字段为`*`表示每分钟。
*   `,`：指定多个不连续的时间点，例如 `1,3,5` 表示第1、3、5个时间单位。
*   `-`：指定一个连续的时间范围，例如 `1-5` 表示第1到第5个时间单位。
*   `/`：指定时间间隔，例如在“分”字段为 `*/2` 表示每2分钟。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_39.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_41.png)

**重要提示**：
1.  **“分”字段通常需要明确指定**。若设为`*`，则表示该小时内的每一分钟都会执行，这可能并非本意。通常整点执行应设为 `0`。
2.  **“日”和“周”字段最好不要同时指定具体值**，否则可能因逻辑冲突导致任务不执行。通常只使用其中一个字段来定义周期。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_43.png)

### 实践示例：自动备份日志

假设我们需要每周五凌晨3点整备份 `/var/log/` 目录下的日志文件。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_45.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_47.png)

首先，创建一个备份脚本 `/script/log_back.sh`：

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_49.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_51.png)

```bash
#!/bin/bash
# 使用当前日期时间作为备份文件名的一部分
tar -zcf /tmp/log_back_$(date +\%Y\%m\%d_\%H\%M).tar.gz /var/log/*
```

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_53.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_55.png)

给脚本添加执行权限：
```bash
chmod +x /script/log_back.sh
```

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_57.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_59.png)

然后，编辑cron任务：
```bash
crontab -e
```

在打开的文件中添加以下行：
```
0 3 * * 5 /script/log_back.sh
```
这表示：在每周五（5）的3点0分（0 3）执行备份脚本。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_61.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_63.png)

保存退出后，任务即生效。你可以通过 `crontab -l` 查看，或观察 `/tmp` 目录下是否按时生成备份文件，以及查看 `/var/log/cron` 日志来验证任务执行情况。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_65.png)

要修改或删除任务，重新使用 `crontab -e` 编辑文件即可。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_67.png)

---

## SELinux内核安全机制 🛡️

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_69.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_71.png)

了解了如何自动化任务后，我们来看一个可能影响服务运行的系统安全组件——SELinux。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_73.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_75.png)

SELinux（Security-Enhanced Linux）是美国国家安全局集成到Linux内核中的一套**强制访问控制体系**。你可以将其理解为“内核级别的防火墙”，它对系统上的进程、文件、目录等提供了更细粒度的安全策略控制。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_77.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_79.png)

在企业环境中，由于SELinux的严格策略可能导致正常服务无法访问，**通常会选择将其关闭**，除非有明确的安全合规要求。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_81.png)

### SELinux的运行模式

SELinux有三种运行模式：
1.  **enforcing**：强制模式。强制执行安全策略，阻止非法操作。
2.  **permissive**：宽松模式。仅记录违规行为而不阻止，用于审计和调试。
3.  **disabled**：禁用模式。完全关闭SELinux。

### 查看与设置SELinux模式

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_83.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_85.png)

*   **查看当前模式**：
    ```bash
    getenforce
    ```
    输出可能是 `Enforcing`、`Permissive` 或 `Disabled`。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_87.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_89.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_91.png)

*   **临时更改模式**（重启后失效）：
    ```bash
    # 设置为强制模式
    setenforce 1
    # 设置为宽松模式
    setenforce 0
    ```
    `setenforce` 命令只能用于在 `enforcing` 和 `permissive` 之间切换，**不能直接切换到 `disabled`**。

*   **永久更改模式**（需修改配置文件）：
    编辑SELinux配置文件 `/etc/selinux/config`：
    ```bash
    vim /etc/selinux/config
    ```
    找到 `SELINUX=` 这一行，将其值修改为 `enforcing`、`permissive` 或 `disabled` 之一。
    ```bash
    # 例如，永久禁用SELinux
    SELINUX=disabled
    ```
    **修改此文件后，必须重启系统才能生效**。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_93.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_95.png)

**常见操作**：如果希望立即生效且永久禁用，可以分两步：
1.  临时设为宽松模式，使当前策略不干扰操作：`setenforce 0`
2.  修改配置文件为 `disabled`，然后安排重启。

---

## 总结 📚

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_97.png)

本节课中我们一起学习了两个重要的系统管理主题：

1.  **cron计划任务**：我们掌握了如何使用 `crontab` 命令创建和管理周期性自动任务。关键在于理解 `分 时 日 月 周` 的时间格式定义，并结合脚本实现自动化运维，如定时备份。
2.  **SELinux内核安全机制**：我们了解了SELinux作为内核强制访问控制系统的三种运行模式（enforcing, permissive, disabled）。我们学习了使用 `getenforce`、`setenforce` 命令临时查看和切换模式，以及通过修改 `/etc/selinux/config` 文件来永久改变其状态。在实际运维中，根据需求选择合适的模式，通常为了减少复杂度会选择关闭。

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_99.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_101.png)

![](img/868e29cd15fff5049ebe92ef8b2bf4e1_103.png)

通过掌握计划任务，你可以让系统自动完成重复性工作；而了解SELinux，则能帮助你处理因安全策略导致的常见服务访问问题。两者都是Linux系统管理员必备的基础知识。