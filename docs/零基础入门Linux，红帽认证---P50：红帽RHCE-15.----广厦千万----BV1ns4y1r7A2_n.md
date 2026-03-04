# Linux运维工程师升职加薪宝典：P50：cron计划任务与SELinux内核安全机制 🔧

![](img/baba960d4eed4b79c296c4c42d987581_1.png)

![](img/baba960d4eed4b79c296c4c42d987581_3.png)

![](img/baba960d4eed4b79c296c4c42d987581_5.png)

在本节课中，我们将要学习Linux系统中两个非常重要的概念：**cron计划任务**和**SELinux内核安全机制**。计划任务能帮助我们自动化周期性工作，而SELinux则是系统底层的安全卫士。掌握它们对于日常运维工作至关重要。

![](img/baba960d4eed4b79c296c4c42d987581_7.png)

---

![](img/baba960d4eed4b79c296c4c42d987581_9.png)

![](img/baba960d4eed4b79c296c4c42d987581_10.png)

![](img/baba960d4eed4b79c296c4c42d987581_12.png)

![](img/baba960d4eed4b79c296c4c42d987581_13.png)

## 计划任务（cron）⏰

![](img/baba960d4eed4b79c296c4c42d987581_15.png)

上一节我们介绍了Shell脚本，它可以帮助我们完成一系列自动化操作。本节中我们来看看如何让这些脚本在指定的时间自动运行，这就是计划任务的功能。

![](img/baba960d4eed4b79c296c4c42d987581_17.png)

![](img/baba960d4eed4b79c296c4c42d987581_19.png)

计划任务软件`cron`就像一个计算机里的“闹钟”。你可以设定好时间，让它定期执行某些命令或脚本，例如在服务器不忙的凌晨进行数据备份，从而实现工作的自动化。

![](img/baba960d4eed4b79c296c4c42d987581_20.png)

![](img/baba960d4eed4b79c296c4c42d987581_22.png)

![](img/baba960d4eed4b79c296c4c42d987581_24.png)

### 1. cron基础与状态检查

![](img/baba960d4eed4b79c296c4c42d987581_25.png)

![](img/baba960d4eed4b79c296c4c42d987581_27.png)

`cron`相关的软件包通常是系统自带的，其服务名为`crond`，并且默认已启动。

**检查cron服务状态：**
```bash
systemctl status crond
```
**查看cron日志（记录任务执行情况）：**
```bash
cat /var/log/cron
```

### 2. 管理计划任务

管理计划任务的核心命令是`crontab`。

![](img/baba960d4eed4b79c296c4c42d987581_29.png)

![](img/baba960d4eed4b79c296c4c42d987581_31.png)

以下是`crontab`的常用命令：
*   **`crontab -e`**：编辑当前用户的计划任务。
*   **`crontab -l`**：列出当前用户的计划任务。
*   **`crontab -r`**：删除当前用户的所有计划任务（慎用）。
*   **`crontab -u username -e`**：编辑指定用户的计划任务。

每个用户的计划任务实际上是一个文件，位于`/var/spool/cron/`目录下，以用户名命名。你也可以直接编辑这个文件来管理任务。

### 3. 时间格式详解

![](img/baba960d4eed4b79c296c4c42d987581_33.png)

![](img/baba960d4eed4b79c296c4c42d987581_35.png)

编写计划任务时，最重要的是理解其时间格式。格式由5个时间字段组成，依次为：**分、时、日、月、周**。

![](img/baba960d4eed4b79c296c4c42d987581_37.png)

![](img/baba960d4eed4b79c296c4c42d987581_39.png)

基本格式如下：
```
* * * * * 要执行的命令
```
*   **星号（`*`）**：代表该字段的所有有效值。例如在“分钟”字段使用`*`，表示每分钟。
*   **数字**：指定具体的时间点。
*   **逗号（`,`）**：指定多个不连续的时间点。例如 `1,3,5` 表示第1、3、5个时间单位。
*   **横杠（`-`）**：指定一个连续的时间范围。例如 `1-5` 表示第1到第5个时间单位。
*   **斜杠（`/`）**：指定时间间隔频率。例如在“分钟”字段使用 `*/2`，表示每2分钟。

**时间字段取值范围：**
*   分钟：0 - 59
*   小时：0 - 23
*   日期：1 - 31
*   月份：1 - 12
*   星期：0 - 7 (0和7都代表星期日)

![](img/baba960d4eed4b79c296c4c42d987581_41.png)

![](img/baba960d4eed4b79c296c4c42d987581_43.png)

**重要提示：**
“日期”（月中的某天）和“星期”（周中的某天）字段通常**不要同时指定**，因为两者逻辑可能冲突，导致任务不按预期执行。一般根据需求选择其中一个字段进行定义。

![](img/baba960d4eed4b79c296c4c42d987581_45.png)

![](img/baba960d4eed4b79c296c4c42d987581_47.png)

### 4. 实战：创建备份计划任务

![](img/baba960d4eed4b79c296c4c42d987581_49.png)

![](img/baba960d4eed4b79c296c4c42d987581_51.png)

![](img/baba960d4eed4b79c296c4c42d987581_53.png)

下面我们通过一个实例，创建一个每周五凌晨3点整执行日志备份的脚本任务。

![](img/baba960d4eed4b79c296c4c42d987581_55.png)

![](img/baba960d4eed4b79c296c4c42d987581_57.png)

**第一步：创建备份脚本**
创建一个简单的脚本，将`/var/log/`目录打包压缩，并在文件名中加入时间戳。
```bash
vim /root/log_backup.sh
```
脚本内容：
```bash
#!/bin/bash
tar -zcf /tmp/log_backup_$(date +%F_%H%M).tar.gz /var/log/*
```
给脚本添加执行权限：
```bash
chmod +x /root/log_backup.sh
```

![](img/baba960d4eed4b79c296c4c42d987581_59.png)

**第二步：编写计划任务**
编辑root用户的计划任务：
```bash
crontab -e
```
在文件中添加以下行：
```
0 3 * * 5 /root/log_backup.sh
```
这行配置的含义是：在每周五（5）的凌晨3点（3）整（0分），执行指定的备份脚本。

![](img/baba960d4eed4b79c296c4c42d987581_61.png)

![](img/baba960d4eed4b79c296c4c42d987581_63.png)

保存退出后，任务即生效。你可以通过`crontab -l`查看，或观察`/tmp`目录下是否按时生成备份文件来验证任务是否成功执行。

---

![](img/baba960d4eed4b79c296c4c42d987581_65.png)

![](img/baba960d4eed4b79c296c4c42d987581_67.png)

## SELinux内核安全机制 🛡️

![](img/baba960d4eed4b79c296c4c42d987581_69.png)

![](img/baba960d4eed4b79c296c4c42d987581_71.png)

![](img/baba960d4eed4b79c296c4c42d987581_73.png)

了解了如何自动化任务后，我们来看一个可能影响服务运行的系统安全组件——SELinux。它是集成在Linux内核中的强制访问控制安全机制，可以理解为“内核级别的防火墙”。

![](img/baba960d4eed4b79c296c4c42d987581_75.png)

![](img/baba960d4eed4b79c296c4c42d987581_77.png)

在企业环境中，由于SELinux规则复杂，常常会直接将其关闭以避免不必要的访问阻拦。我们主要需要了解其运行模式及如何设置。

### 1. SELinux的三种运行模式

SELinux有三种运行模式：
1.  **`enforcing`**：强制模式。严格执行安全策略，阻止未经授权的访问。
2.  **`permissive`**：宽容模式。仅记录违规行为而不阻止，用于调试。
3.  **`disabled`**：禁用模式。完全关闭SELinux。

![](img/baba960d4eed4b79c296c4c42d987581_79.png)

![](img/baba960d4eed4b79c296c4c42d987581_81.png)

![](img/baba960d4eed4b79c296c4c42d987581_83.png)

![](img/baba960d4eed4b79c296c4c42d987581_85.png)

### 2. 查看与设置SELinux模式

![](img/baba960d4eed4b79c296c4c42d987581_87.png)

**查看当前SELinux模式：**
```bash
getenforce
```

![](img/baba960d4eed4b79c296c4c42d987581_89.png)

![](img/baba960d4eed4b79c296c4c42d987581_91.png)

**临时更改SELinux模式（重启后失效）：**
可以使用`setenforce`命令临时在 `enforcing` (1) 和 `permissive` (0) 之间切换。
```bash
setenforce 0 # 设置为宽容模式
setenforce 1 # 设置为强制模式
```
**注意**：`setenforce`命令无法将模式直接设为`disabled`。

**永久更改SELinux模式：**
要永久修改（例如禁用SELinux），需要编辑其配置文件。
```bash
vim /etc/selinux/config
```
找到 `SELINUX=` 这一行，将其值修改为 `disabled`：
```
SELINUX=disabled
```
保存退出。**此修改需要重启系统才能生效**。

在实际操作中，可以先用`setenforce 0`临时设为宽容模式，保证当前操作不受阻，然后修改配置文件为`disabled`并安排重启，以实现永久禁用。

![](img/baba960d4eed4b79c296c4c42d987581_93.png)

---

![](img/baba960d4eed4b79c296c4c42d987581_95.png)

![](img/baba960d4eed4b79c296c4c42d987581_96.png)

![](img/baba960d4eed4b79c296c4c42d987581_98.png)

本节课中我们一起学习了`cron`计划任务和`SELinux`安全机制。`cron`是运维自动化的利器，重点在于掌握灵活的时间格式定义。而`SELinux`作为内核安全组件，了解其基本模式和管理方法（尤其是如何关闭）对于保障服务顺利运行非常重要。将脚本与计划任务结合，就能构建起基础的系统自动化维护体系。