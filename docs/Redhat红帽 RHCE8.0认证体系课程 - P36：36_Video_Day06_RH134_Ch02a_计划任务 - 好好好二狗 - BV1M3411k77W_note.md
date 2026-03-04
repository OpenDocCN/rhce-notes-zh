# Redhat红帽 RHCE8.0认证体系课程：P36：计划任务与临时文件管理（上）📅

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_1.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_3.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_5.png)

在本节课中，我们将要学习Linux系统中的计划任务与临时文件管理。计划任务是系统管理中的核心功能，它允许我们在指定的时间点自动执行预定义的操作，从而解放管理员，实现自动化运维。本节课我们将重点介绍一次性计划任务和周期性计划任务的原理与配置方法。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_6.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_8.png)

## 什么是计划任务？🤔

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_10.png)

计划任务是指让系统在某个特定的时间点，执行一次或多次用户预先定义好的操作。这就像是给系统设定一个闹钟，到时间就自动完成某项工作。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_12.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_14.png)

根据执行频率，计划任务主要分为两类：
*   **一次性计划任务**：系统在指定的某个时间点执行一次操作，执行完毕后该任务即结束。
*   **周期性计划任务**：系统根据设定的时间条件（如每天、每周），循环往复地执行任务。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_16.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_17.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_18.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_20.png)

上一节我们介绍了计划任务的基本概念，本节中我们来看看如何实现一次性计划任务。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_21.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_23.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_25.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_26.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_27.png)

## 一次性计划任务 ⏰

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_28.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_30.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_32.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_34.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_35.png)

一次性计划任务让系统在未来的某一个具体时间点执行一次操作。任务执行完成后，该计划就会被清除。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_37.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_38.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_40.png)

它的实现依赖于一个名为 **`atd`** 的后台守护进程。这个服务负责监控和管理所有通过 `at` 命令创建的一次性任务。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_42.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_44.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_45.png)

### 如何定义一次性任务？

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_47.png)

定义一次性任务主要使用 `at` 命令。以下是其基本用法和示例。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_49.png)

**基本命令格式：**
```bash
at [时间定义]
```
输入此命令后，会进入 `at>` 提示符，此时可以输入要执行的命令。输入完毕后，按 `Ctrl + D` 组合键结束并提交任务。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_51.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_53.png)

**时间定义有多种格式：**
*   **具体时间点**：`at 17:25 2020-07-12`
*   **相对时间**：
    *   `at now + 1 day` （一天后）
    *   `at now + 3 min` （三分钟后）
    *   `at now + 2 hours` （两小时后）

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_55.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_57.png)

**任务管理命令：**
*   **查看等待中的任务列表**：`at -l` 或 `atq`
*   **查看任务具体内容**：`at -c [任务编号]`
*   **删除任务**：`at -r [任务编号]` 或 `atrm [任务编号]`

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_58.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_60.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_62.png)

**示例演示：**
1.  创建一个在17:25执行的任务，用于创建文件 `/tmp/test_at_file`。
    ```bash
    # 假设当前时间是17:21
    at 17:25
    at> /usr/bin/touch /tmp/test_at_file
    at> <EOT> # 此处按 Ctrl + D
    job 3 at Mon Jul 12 17:25:00 2020
    ```
    任务创建后，相关信息会存储在 `/var/spool/at/` 目录下。任务执行后，该文件会被自动删除。
2.  创建一个3分钟后执行的任务。
    ```bash
    at now + 3 min
    at> /usr/bin/touch /tmp/test_at_file2
    at> <EOT> # 按 Ctrl + D
    ```

理解了如何创建和管理一次性任务后，我们接下来看看更常用的周期性计划任务。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_64.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_66.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_68.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_70.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_71.png)

## 周期性计划任务 🔄

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_73.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_74.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_75.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_77.png)

周期性计划任务允许系统循环执行任务，例如每天备份、每周清理日志等。这是RHCE考试的重点内容。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_79.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_81.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_83.png)

周期性任务由 **`crond`** 服务监管。用户可以使用 `crontab` 命令来管理自己的周期性任务。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_85.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_87.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_88.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_90.png)

### 用户级周期性任务

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_92.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_94.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_96.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_98.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_99.png)

普通用户可以管理自己的周期性任务。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_101.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_103.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_105.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_106.png)

以下是 `crontab` 命令的常用选项：
*   `crontab -l`：列出当前用户的周期性任务。
*   `crontab -e`：编辑当前用户的周期性任务（调用默认文本编辑器，如vim）。
*   `crontab -r`：删除当前用户的所有周期性任务（慎用！）。
*   `crontab -u [用户名] -e`：**仅限root用户使用**，用于编辑指定用户的周期性任务。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_108.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_109.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_111.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_113.png)

**周期性任务格式：**
一个完整的周期性任务由6个字段组成，格式如下：
```
分 时 日 月 周 命令
```
*   **分**：分钟 (0-59)
*   **时**：小时 (0-23)
*   **日**：一个月中的第几天 (1-31)
*   **月**：月份 (1-12)
*   **周**：星期几 (0-7，0和7都代表周日)
*   **命令**：要执行的具体命令或脚本路径

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_115.png)

**特殊符号说明：**
*   `*`：代表任意值。例如在“分”字段为 `*`，表示每分钟。
*   `,`：代表多个不连续的时间点。例如在“时”字段写 `8,14,20`，表示8点、14点和20点。
*   `-`：代表一个连续范围。例如“日”字段写 `1-15`，表示每月1到15号。
*   `/n`：代表时间间隔。例如在“时”字段写 `*/2`，表示每2小时。

**示例：**
*   `0 1 * * * /usr/bin/systemctl restart httpd`：每天凌晨1点重启httpd服务。
*   `*/30 8-18 * * 1-5 /home/user/backup.sh`：每周一到周五的8点到18点之间，每30分钟执行一次备份脚本。
*   `0 8 10,20 * * /usr/bin/wall “记得提交周报！”`：每月10号和20号的早上8点，向所有用户发送提醒消息。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_117.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_119.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_120.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_121.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_123.png)

用户编辑并保存的周期性任务，实际会存储在 `/var/spool/cron/` 目录下，以用户名命名的文件中。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_125.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_127.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_129.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_131.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_133.png)

### 系统级周期性任务

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_135.png)

系统级别的周期性任务通常用于执行系统维护工作，如日志轮转、更新数据库等。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_137.png)

系统任务主要有两种定义方式：

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_139.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_141.png)

**方法一：在预定义目录中放置脚本**
系统预定义了多个执行频率的目录，将可执行脚本或命令放入对应目录即可。
*   `/etc/cron.hourly/`：每小时执行
*   `/etc/cron.daily/`：每天执行
*   `/etc/cron.weekly/`：每周执行
*   `/etc/cron.monthly/`：每月执行
例如，系统的日志轮转任务 `logrotate` 就是通过放置在 `/etc/cron.daily/` 目录下的脚本来实现的。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_143.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_145.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_146.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_148.png)

**方法二：在 `/etc/cron.d/` 目录中创建配置文件**
此方法更为灵活，可以自定义精确的执行时间。文件格式与用户级任务类似，但需要**额外指定执行任务的用户**。
```
# 文件 /etc/cron.d/myjob
# 分 时 日 月 周 用户 命令
0 4 * * 0 root /usr/local/bin/weekly_report.sh
```
以上配置表示：每周日（0）的凌晨4点，以root用户身份执行 `/usr/local/bin/weekly_report.sh` 脚本。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_150.png)

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_152.png)

**系统任务执行机制**
系统周期性任务的具体执行时间（包括随机延迟）在 `/etc/anacrontab` 文件中定义。例如，`daily` 任务最多会随机延迟5分钟执行，这是为了错开用户任务的高峰期，避免系统负载瞬间过高。通常我们不需要修改此文件。

![](img/fb4f5f97c0d2606812a8bf46e56ea7b2_154.png)

---
本节课中我们一起学习了Linux计划任务的核心知识。我们掌握了如何使用 `at` 命令创建和管理**一次性计划任务**，以及如何使用 `crontab` 命令和系统目录来配置**用户级**和**系统级**的**周期性计划任务**。理解并熟练运用这些工具，是实现系统自动化管理的重要基础。下节课我们将继续学习本章的另一个主题：临时文件的管理。