# 尚观Linux视频教程RHCE精品课程：P21：RH033-ULE112-12-1-进程控制

![](img/b542f4c9fcf1dbd374233a5c45fb5032_1.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_2.png)

在本节课中，我们将要学习Linux系统状态检测及进程控制。这包括如何查询系统信息、检查系统状态、理解`/proc`文件系统、查看系统日志以及管理和控制进程。这些技能对于诊断系统问题和维护系统健康至关重要。

![](img/b542f4c9fcf1dbd374233a5c45fb5032_4.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_5.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_7.png)

## 系统信息收集

![](img/b542f4c9fcf1dbd374233a5c45fb5032_9.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_11.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_13.png)

上一节我们介绍了本章的学习目标，本节中我们来看看如何收集系统的基本信息。

![](img/b542f4c9fcf1dbd374233a5c45fb5032_15.png)

`hostname`命令用于查看当前主机名。在Linux系统中，主机名非常重要。

![](img/b542f4c9fcf1dbd374233a5c45fb5032_17.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_18.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_20.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_21.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_23.png)

```bash
hostname
```

![](img/b542f4c9fcf1dbd374233a5c45fb5032_25.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_26.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_27.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_29.png)

如果系统是默认安装且未作修改，主机名通常是`localhost`或`localhost.localdomain`。

主机名需要与IP地址正确对应，这通过`/etc/hosts`文件实现。一个标准的`hosts`文件应包含以下解析关系：
*   `localhost` 解析为 `127.0.0.1`
*   `localhost.localdomain` 解析为 `127.0.0.1`
*   您设定的主机名（如`uplook.com`）解析为您服务器的真实IP地址（如`192.168.1.231`）

![](img/b542f4c9fcf1dbd374233a5c45fb5032_31.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_32.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_33.png)

遵循这个原则可以避免服务启动缓慢等莫名问题。Windows系统同样存在`hosts`文件，路径通常为`C:\Windows\System32\drivers\etc\hosts`。

![](img/b542f4c9fcf1dbd374233a5c45fb5032_35.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_36.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_38.png)

若要永久更改主机名，需要编辑`/etc/sysconfig/network`文件，修改其中的`HOSTNAME`值，并同步更新`/etc/hosts`文件。

![](img/b542f4c9fcf1dbd374233a5c45fb5032_40.png)

`uname -a`命令可以查看当前系统平台、内核版本等详细信息。

![](img/b542f4c9fcf1dbd374233a5c45fb5032_42.png)

`last`命令用于查看系统重启记录以及用户登录历史。
`lastlog`命令用于查看每个用户最近一次登录的时间。

## 系统状态查询

![](img/b542f4c9fcf1dbd374233a5c45fb5032_44.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_45.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_47.png)

了解了如何收集静态信息后，我们来看看如何检查系统的实时状态。

`df`命令用于查看磁盘空间使用情况。
`du -sh <目录名>`命令用于查看指定目录的大小。

`free`命令用于查看内存使用情况。

`/proc`文件系统是内核提供的一个信息窗口，它并非真实的文件系统，其中的文件和目录是内核数据的接口。例如，`free`命令的数据就来源于`/proc/meminfo`。

`ls /proc`可以查看`/proc`下的内容。`/proc/sys`目录下的文件是可写的内核参数。例如，控制是否响应ping请求的参数路径为`/proc/sys/net/ipv4/icmp_echo_ignore_all`。将其值改为`1`可禁止ping通。

![](img/b542f4c9fcf1dbd374233a5c45fb5032_49.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_50.png)

```bash
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all
```

![](img/b542f4c9fcf1dbd374233a5c45fb5032_52.png)

许多系统命令（如`ifconfig`, `hostname`, `mount`）都依赖于`/proc`文件系统。

![](img/b542f4c9fcf1dbd374233a5c45fb5032_54.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_56.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_58.png)

## 系统日志查看

![](img/b542f4c9fcf1dbd374233a5c45fb5032_59.png)

系统日志是诊断问题的关键。常见的系统日志存放在`/var/log`目录下。

![](img/b542f4c9fcf1dbd374233a5c45fb5032_61.png)

以下是主要日志文件及其用途：
*   **`/var/log/messages`**：最重要的综合日志文件，绝大多数系统和程序日志都会记录于此。查看最新日志常用`tail -f /var/log/messages`命令。
*   **`/var/log/secure`**：安全相关日志，如用户登录、密码修改等。
*   **`/var/log/wtmp`**：二进制格式的登录记录，无法直接编辑，需使用`last`命令查看，安全性更高。
*   **`/var/log/maillog`**：邮件服务相关日志。
*   **`/var/log/cron`**：计划任务`cron`的日志。
*   **`/var/log/boot.log`**：系统启动相关日志。
*   **`/var/log/xferlog`**：FTP服务日志。

![](img/b542f4c9fcf1dbd374233a5c45fb5032_63.png)

`dmesg`命令或查看`/var/log/dmesg`文件，可以获取内核启动和运行时的日志。

当日志文件过大时，系统会进行轮替（如`messages.1`, `messages.2.gz`），并清理旧日志。

## 进程概念与管理

![](img/b542f4c9fcf1dbd374233a5c45fb5032_65.png)

进程是系统中正在运行的程序实例。每个进程都有独立的运行空间，进程间通信（IPC）需要通过特定机制。

`top`命令是一个动态的进程监控工具，类似于Windows的任务管理器。启动后，它会显示系统概况（运行时间、负载、CPU/内存使用率）和进程列表。

![](img/b542f4c9fcf1dbd374233a5c45fb5032_67.png)

在`top`界面中，可以进行以下操作：
*   按 **M** 键：根据内存使用率排序。
*   按 **P** 键：根据CPU使用率排序（默认）。
*   按 **k** 键：终止指定PID的进程，默认发送15号信号（SIGTERM）。
*   按 **q** 键：退出`top`。
*   按 **?** 键：查看帮助。

进程的重要属性包括：
*   **PID**：进程ID。
*   **USER**：进程所有者。
*   **%CPU**：CPU使用百分比。
*   **%MEM**：内存使用百分比。
*   **STAT**：进程状态（S-睡眠, R-运行, T-停止, Z-僵尸, D-不可中断睡眠）。
*   **COMMAND**：启动进程的命令。

`ps`命令用于静态查看进程快照。常用组合有：
*   `ps aux`：显示所有用户的详细进程信息，包括后台守护进程。
*   `ps -ef`：以另一种格式显示所有进程信息，在Unix系统中常用。
*   `ps axf`：以树状结构显示进程，清晰展示父子关系。

所有进程的父进程是`init`（或`systemd`），其PID永远是1。

## 进程信号与控制

进程间可以通过信号进行通信。在32位系统上，通常有32个标准信号。

`kill`命令用于向进程发送信号。
*   `kill <PID>`：默认向指定PID的进程发送15号信号（SIGTERM），请求其正常终止。
*   `kill -9 <PID>`：向指定PID的进程发送9号信号（SIGKILL），强制终止进程。应谨慎使用，可能产生僵尸进程或导致数据丢失。
*   `kill -19 <PID>`：发送19号信号（SIGSTOP），暂停进程。
*   `kill -18 <PID>`：发送18号信号（SIGCONT），继续运行被暂停的进程。

`killall`命令可以根据进程名发送信号。
*   `killall <进程名>`：终止所有指定名称的进程。
*   `killall -9 <进程名>`：强制终止所有指定名称的进程。

`pkill`命令可以根据更丰富的条件（如用户名、终端号）发送信号，功能强大。
*   `pkill -9 -t pts/2`：强制终止在`pts/2`终端上运行的所有进程。

`kill`, `killall`, `pkill`构成了进程控制的工具家族。

## 进程前后台运行

![](img/b542f4c9fcf1dbd374233a5c45fb5032_69.png)

在命令行中，在命令末尾加上`&`符号，可以让该命令在后台运行。

![](img/b542f4c9fcf1dbd374233a5c45fb5032_71.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_72.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_73.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_75.png)

```bash
sleep 100 &
```

![](img/b542f4c9fcf1dbd374233a5c45fb5032_77.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_79.png)

![](img/b542f4c9fcf1dbd374233a5c45fb5032_81.png)

使用`jobs`命令可以查看当前会话的后台任务。使用`fg %<任务编号>`可以将后台任务切换到前台。使用`bg %<任务编号>`可以将暂停的任务在后台继续运行。

## 总结

本节课中我们一起学习了Linux系统状态检测与进程控制的核心知识。我们掌握了如何通过`hostname`, `uname`, `last`等命令收集系统信息；使用`df`, `free`, `top`监控系统状态；理解了`/proc`文件系统的作用；学会了查看`/var/log`下各类日志文件。更重要的是，我们深入学习了进程的概念，熟练运用`ps`, `top`查看进程，并使用`kill`, `killall`, `pkill`家族命令以及信号机制来管理进程，包括前后台任务切换。这些技能是进行系统运维和故障排查的基础。