# Linux系统管理：P25：系统日志管理 📝

![](img/9d0a51c1e5bf2c1b22e224b662e579df_1.png)

在本节课中，我们将要学习Linux系统中的日志管理。日志是系统运行过程中记录各种事件的重要工具，对于系统排错、安全分析和性能监控至关重要。我们将了解日志的基本概念、系统日志服务（systemd-journald和rsyslog）的运作方式，以及如何查看和管理日志文件。

## 日志基础概念

上一节我们介绍了课程概述，本节中我们来看看日志的基础概念。

日志用于将系统或应用程序发生的事件记录下来，以助于排错和分析使用。日志记录的内容包括历史事件、时间、地点、人物和事件日志级别。

![](img/9d0a51c1e5bf2c1b22e224b662e579df_3.png)

日志级别根据事件的关键性程度进行划分，例如：
*   **debug**：调试信息。
*   **info**：一般信息。
*   **warning**：警告信息。
*   **error**：错误信息。

![](img/9d0a51c1e5bf2c1b22e224b662e579df_5.png)

这些不同级别的信息都会记录在日志中。

![](img/9d0a51c1e5bf2c1b22e224b662e579df_7.png)

## 系统日志服务

![](img/9d0a51c1e5bf2c1b22e224b662e579df_9.png)

![](img/9d0a51c1e5bf2c1b22e224b662e579df_11.png)

在红帽（RHEL）系统中，日志主要通过 `systemd-journald` 和 `rsyslog` 服务来记录。

![](img/9d0a51c1e5bf2c1b22e224b662e579df_13.png)

![](img/9d0a51c1e5bf2c1b22e224b662e579df_15.png)

`systemd-journald` 服务负责收集来自内核、启动过程、标准输出/错误以及系统服务的日志信息。它以二进制形式将日志存储在内存和持久化存储中，提供了高性能的日志记录。

![](img/9d0a51c1e5bf2c1b22e224b662e579df_17.png)

![](img/9d0a51c1e5bf2c1b22e224b662e579df_19.png)

`rsyslog` 服务则是一个更传统的、功能强大的系统日志守护进程。它从 `systemd-journald` 或其他来源获取日志消息，并根据配置规则将其分类并写入到 `/var/log/` 目录下的各个文本文件中（如 `/var/log/messages`， `/var/log/secure` 等），便于管理员直接阅读。`rsyslog` 支持多种协议，并具备强大的过滤和转发功能。

以下是常见的系统日志文件及其作用：
*   `/var/log/messages`：记录大多数系统日志消息，如系统服务、认证、电子邮件处理等。
*   `/var/log/secure`：记录与安全认证相关的日志，如用户登录（SSH）、权限变更等。
*   `/var/log/boot.log`：记录系统启动过程的信息。
*   `/var/log/maillog`：记录邮件服务（如Postfix）的相关信息。
*   `/var/log/cron`：记录定时任务（cron）的执行信息。

## 日志优先级与配置

系统日志根据程序功能进行分类，例如邮件、安全、用户认证等。同时，每条日志都有一个优先级（Priority），用于标识其严重程度。

优先级由设施（Facility）和级别（Level）组合而成，格式为 `facility.level`。级别从0到7，数字越小越紧急：
*   **0 (emerg)**：系统不可用。
*   **1 (alert)**：必须立即采取行动。
*   **2 (crit)**：临界状况。
*   **3 (err)**：错误状况。
*   **4 (warning)**：警告状况。
*   **5 (notice)**：正常但重要的事件。
*   **6 (info)**：一般信息性事件。
*   **7 (debug)**：调试级别信息。

`rsyslog` 的配置文件为 `/etc/rsyslog.conf`，它由模块（Modules）、全局指令（Global Directives）和规则（Rules）三部分组成。规则定义了哪些日志（根据设施和级别）应该被记录到哪个文件。

一条配置规则的格式通常如下：
```
facility.priority    action
```
例如，将所有内核消息记录到 `/var/log/kern.log`：
```
kern.*    /var/log/kern.log
```

## 使用 `journalctl` 查看日志

![](img/9d0a51c1e5bf2c1b22e224b662e579df_21.png)

从RHEL 7/CentOS 7开始，`systemd-journald` 成为默认的日志系统，并提供了 `journalctl` 命令来查看和管理日志。它整合了所有系统单元（服务）的日志，查询功能非常强大。

![](img/9d0a51c1e5bf2c1b22e224b662e579df_23.png)

![](img/9d0a51c1e5bf2c1b22e224b662e579df_24.png)

![](img/9d0a51c1e5bf2c1b22e224b662e579df_25.png)

以下是 `journalctl` 的常用命令示例：

![](img/9d0a51c1e5bf2c1b22e224b662e579df_27.png)

![](img/9d0a51c1e5bf2c1b22e224b662e579df_29.png)

![](img/9d0a51c1e5bf2c1b22e224b662e579df_31.png)

查看所有日志（最新）：
```bash
journalctl
```

查看指定服务的日志（例如 `httpd`）：
```bash
journalctl -u httpd.service
```

查看本次启动后的日志：
```bash
journalctl -b
```

查看从某个时间开始的日志（例如查看最近30分钟的日志）：
```bash
journalctl --since “30 min ago”
```

![](img/9d0a51c1e5bf2c1b22e224b662e579df_33.png)

实时跟踪日志输出（类似 `tail -f`）：
```bash
journalctl -f
```

![](img/9d0a51c1e5bf2c1b22e224b662e579df_35.png)

![](img/9d0a51c1e5bf2c1b22e224b662e579df_37.png)

![](img/9d0a51c1e5bf2c1b22e224b662e579df_39.png)

以JSON格式输出（便于其他工具处理）：
```bash
journalctl -o json
```

![](img/9d0a51c1e5bf2c1b22e224b662e579df_41.png)

![](img/9d0a51c1e5bf2c1b22e224b662e579df_43.png)

![](img/9d0a51c1e5bf2c1b22e224b662e579df_45.png)

![](img/9d0a51c1e5bf2c1b22e224b662e579df_47.png)

## 实战：自定义日志路径

![](img/9d0a51c1e5bf2c1b22e224b662e579df_49.png)

![](img/9d0a51c1e5bf2c1b22e224b662e579df_51.png)

我们可以配置 `rsyslog`，将特定服务的日志记录到自定义的文件中。

例如，将 SSH 服务（`authpriv` 设施）的日志记录到 `/var/log/ssh.log` 文件中：

1.  编辑 `/etc/rsyslog.conf` 文件，在规则部分添加一行：
    ```
    authpriv.*    /var/log/ssh.log
    ```
2.  重启 `rsyslog` 服务使配置生效：
    ```bash
    systemctl restart rsyslog
    ```
3.  使用 `logger` 命令测试日志是否能够写入新文件。`logger` 命令可以向系统日志发送消息。
    ```bash
    logger -p authpriv.info “This is a test message for SSH log”
    ```
4.  查看自定义日志文件内容：
    ```bash
    tail -f /var/log/ssh.log
    ```
    你应该能看到刚才输入的测试消息。

![](img/9d0a51c1e5bf2c1b22e224b662e579df_53.png)

## 集中式日志管理简介

在企业环境中，通常会有多台服务器。为了方便统一管理和分析日志，会搭建集中式日志服务器。常见的方案是 ELK Stack（Elasticsearch, Logstash, Kibana）或 EFK Stack（Elasticsearch, Fluentd, Kibana）。

*   **Elasticsearch**：一个分布式的搜索和分析引擎，负责存储和索引日志数据。
*   **Logstash/Fluentd**：日志收集和处理管道，负责从各个服务器收集日志，进行过滤、转换，然后发送给 Elasticsearch。
*   **Kibana**：一个数据可视化平台，为 Elasticsearch 提供图形化界面，用于搜索、查看和交互式地分析日志数据。

通过配置 `rsyslog` 或 `systemd-journald` 将日志转发到中央的 Logstash 或 Fluentd 服务器，即可实现日志的集中管理。

![](img/9d0a51c1e5bf2c1b22e224b662e579df_55.png)

## 总结

![](img/9d0a51c1e5bf2c1b22e224b662e579df_57.png)

本节课中我们一起学习了 Linux 系统日志管理的核心知识。我们了解了日志的基本概念和级别，认识了 `systemd-journald` 和 `rsyslog` 两大日志服务的作用与关系。我们掌握了通过 `journalctl` 命令高效查询日志的技巧，并实践了如何通过配置 `rsyslog` 将日志记录到自定义路径。最后，我们还简要了解了企业级集中式日志管理架构（如ELK）。熟练管理日志是系统管理员进行故障排查、安全审计和性能优化的一项基本且重要的技能。