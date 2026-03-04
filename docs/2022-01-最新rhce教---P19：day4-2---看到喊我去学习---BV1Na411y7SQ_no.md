# Linux系统管理：P19：日志轮转配置与管理 📁

![](img/8b4be98f2c5b0967df19fa4891d6ee44_0.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_2.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_4.png)

在本节课中，我们将要学习Linux系统中的日志轮转（Log Rotation）。日志轮转是系统管理中的一项重要任务，它负责自动管理日志文件，防止单个日志文件过大，并通过压缩归档旧日志来节省磁盘空间。我们将了解其工作原理、核心配置文件以及如何手动执行和测试日志轮转。

## 日志轮转概述 🔄

![](img/8b4be98f2c5b0967df19fa4891d6ee44_6.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_8.png)

日志文件管理工具用于删除旧的日志文件并创建新的日志文件，这个过程称为日志转存或滚动，通常我们称之为日志轮转。轮转可以根据日志文件的大小或天数来触发。

![](img/8b4be98f2c5b0967df19fa4891d6ee44_10.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_12.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_14.png)

这个过程一般通过一个定时执行的程序来完成。它通过定时任务调用 `logrotate` 命令来执行日志轮转。例如，在每天凌晨，`cron` 定时任务会触发 `logrotate`，它会创建新的日志文件，并将原来的日志文件压缩保存。

![](img/8b4be98f2c5b0967df19fa4891d6ee44_16.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_18.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_20.png)

## 轮转周期与计划任务 ⏰

![](img/8b4be98f2c5b0967df19fa4891d6ee44_22.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_24.png)

`logrotate` 的触发周期配置非常灵活。以下是常见的轮转周期选项：

*   **daily**: 按天轮转。
*   **weekly**: 按周轮转。
*   **monthly**: 按月轮转。
*   **hourly**: 按小时轮转（需要额外配置）。

这些计划任务通常定义在 `/etc/cron.daily/logrotate` 等文件中。我们可以查看这些文件，会发现它们最终都是调用 `/usr/sbin/logrotate` 命令，并读取其主配置文件 `/etc/logrotate.conf` 以及 `/etc/logrotate.d/` 目录下的自定义配置。

## 核心配置文件详解 ⚙️

![](img/8b4be98f2c5b0967df19fa4891d6ee44_26.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_28.png)

上一节我们介绍了日志轮转的基本概念和触发方式，本节中我们来看看其核心配置文件。主配置文件是 `/etc/logrotate.conf`，而针对特定服务或应用的配置通常放在 `/etc/logrotate.d/` 目录下。

![](img/8b4be98f2c5b0967df19fa4891d6ee44_30.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_32.png)

首先，可以使用 `man logrotate` 命令查看其详细的使用手册。配置文件中包含了许多参数，以下是一些关键配置项的说明：

*   **`compress`** 与 **`nocompress`**: 用于控制是否压缩轮转后的旧日志文件。默认是 `nocompress`。
*   **`copytruncate`** 与 **`nocopytruncate`**: `copytruncate` 会复制当前日志文件内容后进行截断，这可能导致日志在轮转瞬间丢失。`nocopytruncate` 是默认值，它先重命名旧文件，再创建新文件。
*   **`create mode owner group`** 与 **`nocreate`**: 指定新创建的日志文件的权限、所有者和所属组。例如 `create 644 root root`。`nocreate` 表示不创建新的日志文件。
*   **`delaycompress`**: 与 `compress` 一起使用。轮转后的日志文件会等到下一次轮转时再进行压缩。
*   **`mail address`**: 将轮转出的旧日志文件通过邮件发送到指定地址。
*   **`ifempty`** 与 **`notifempty`**: `ifempty` 即使日志文件为空也进行轮转。`notifempty` 是默认值，如果日志文件为空，则不进行轮转。
*   **`olddir directory`** 与 **`noolddir`**: `olddir` 指定轮转后的日志文件放入的目录，该目录必须与当前日志文件在同一文件系统。`noolddir` 是默认值，表示放在同一目录下。
*   **`prerotate/endscript`** 与 **`postrotate/endscript`**: 这两个指令块用于定义在轮转**之前**和**之后**需要执行的命令。它们必须单独成行。
*   **`daily`/`weekly`/`monthly`**: 指定轮转的周期。
*   **`rotate count`**: 指定保留的备份文件数量。例如 `rotate 5` 表示保留5次轮转的备份，第6次轮转时，最旧的备份会被删除。
*   **`size size`**: 指定日志文件达到多大尺寸时立即触发轮转，例如 `size 1M`。
*   **`missingok`**: 如果日志文件不存在，不报错，继续处理下一个配置。
*   **`sharedscripts`**: 对于通配符匹配的多个日志文件，`prerotate` 和 `postrotate` 脚本只运行一次，而不是每个文件运行一次。

## 配置案例与实践 🛠️

理解了核心参数后，我们来看一个具体的配置案例。以下是一个自定义日志轮转配置的示例，它定义了 `/var/log/test.log` 文件的轮转行为：

```bash
/var/log/test.log {
    daily
    rotate 5
    compress
    delaycompress
    missingok
    notifempty
    size 1M
    create 644 root root
    postrotate
        /bin/echo “$(date) - Rotation completed for test.log” >> /var/log/rotation.log
    endscript
}
```

这个配置表示：
*   按天轮转。
*   保留5个备份。
*   启用压缩，但延迟到下一次轮转时再压缩本次轮转出的文件。
*   如果文件不存在也不报错。
*   空文件不轮转。
*   文件大小超过1MB时立即轮转。
*   新创建的文件权限为644，所有者为root。
*   轮转后，将一条完成信息追加到 `/var/log/rotation.log` 文件中。

![](img/8b4be98f2c5b0967df19fa4891d6ee44_34.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_35.png)

`postrotate` 脚本在实际生产中非常有用，例如可以用于触发日志分析脚本，或将轮转事件通知到监控系统。

![](img/8b4be98f2c5b0967df19fa4891d6ee44_37.png)

## 手动测试与问题排查 🧪

![](img/8b4be98f2c5b0967df19fa4891d6ee44_39.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_41.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_42.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_43.png)

配置完成后，我们可以手动执行 `logrotate` 命令来测试配置是否正确，而不必等待定时任务触发。

![](img/8b4be98f2c5b0967df19fa4891d6ee44_45.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_47.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_48.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_50.png)

手动测试的命令是：
```bash
sudo logrotate -vf /etc/logrotate.d/your-config-file
```
*   `-v`: 显示详细过程。
*   `-f`: 强制轮转。

![](img/8b4be98f2c5b0967df19fa4891d6ee44_52.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_54.png)

在测试过程中，可能会遇到轮转未按预期执行的情况。常见原因包括：
1.  **配置文件语法错误**：例如括号不匹配、指令拼写错误。
2.  **路径或权限问题**：`logrotate` 进程权限不足以读取日志文件或写入目标目录。
3.  **条件未满足**：例如配置了 `size 1M`，但文件大小未达到；或配置了 `notifempty`，但文件为空。
4.  **`sharedscripts` 误解**：当通配符匹配多个文件时，脚本执行时机可能和预期不符。

排查时，可以结合手动执行的 `-v` 输出信息，以及查看 `/var/lib/logrotate/logrotate.status` 状态文件或系统日志（如 `/var/log/messages`）来获取线索。

![](img/8b4be98f2c5b0967df19fa4891d6ee44_56.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_58.png)

## 总结 📝

![](img/8b4be98f2c5b0967df19fa4891d6ee44_60.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_61.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_63.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_64.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_66.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_67.png)

本节课中我们一起学习了Linux下的日志轮转工具 `logrotate`。我们从日志轮转的概念和必要性讲起，了解了其通过系统定时任务触发的机制。然后，我们深入分析了 `/etc/logrotate.conf` 配置文件中的各项核心参数，例如轮转周期(`daily`)、备份数量(`rotate`)、压缩选项(`compress`, `delaycompress`)、文件大小触发(`size`)以及前后执行脚本(`prerotate`/`postrotate`)等。

![](img/8b4be98f2c5b0967df19fa4891d6ee44_68.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_70.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_71.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_72.png)

![](img/8b4be98f2c5b0967df19fa4891d6ee44_73.png)

通过一个具体的配置案例，我们掌握了如何为自定义日志文件编写轮转策略。最后，我们学习了如何使用 `logrotate -vf` 命令手动测试配置，并探讨了常见的配置问题与排查思路。正确配置和管理日志轮转，是保持系统整洁、高效运行及满足审计要求的重要技能。