# RHCE RH124 课程：11：Linux日志与时钟同步(2) - P1

![](img/ea8d288753193f224d50efd0d009ac9d_1.png)

![](img/ea8d288753193f224d50efd0d009ac9d_3.png)

![](img/ea8d288753193f224d50efd0d009ac9d_4.png)

在本节课中，我们将要学习Linux系统中日志配置文件 `rsyslog.conf` 的详细结构与配置规则。我们将了解如何通过定义“设施”和“优先级”来精确控制不同来源和级别的系统日志的存储位置与处理方式。

## 日志配置文件详解

上一节我们介绍了系统日志的基本概念，本节中我们来看看其核心配置文件 `rsyslog.conf` 的具体内容与配置语法。

### 配置文件结构说明

`rsyslog.conf` 文件定义了系统日志的收集、过滤和存储规则。其核心配置项主要由“设施”和“优先级”两个参数构成。

![](img/ea8d288753193f224d50efd0d009ac9d_6.png)

*   **设施**：指生成日志的子系统或来源。例如，内核、邮件服务、身份验证系统等。
*   **优先级**：指日志事件的严重程度级别，从低到高排列。

![](img/ea8d288753193f224d50efd0d009ac9d_8.png)

![](img/ea8d288753193f224d50efd0d009ac9d_10.png)

以下是优先级的关键字及其对应级别（数字越小，优先级越高/越严重）：

![](img/ea8d288753193f224d50efd0d009ac9d_12.png)

![](img/ea8d288753193f224d50efd0d009ac9d_14.png)

```
debug (7) -> info (6) -> notice (5) -> warning (4) -> err (3) -> crit (2) -> alert (1) -> emerg (0)
```
注意：`warning` 和 `warn` 是同一级别（4级）。

![](img/ea8d288753193f224d50efd0d009ac9d_16.png)

![](img/ea8d288753193f224d50efd0d009ac9d_18.png)

### 配置文件规则解析

![](img/ea8d288753193f224d50efd0d009ac9d_20.png)

![](img/ea8d288753193f224d50efd0d009ac9d_21.png)

理解了设施和优先级后，我们来看配置文件中具体的规则行。这些规则决定了特定来源和级别的日志将被记录到何处。

以下是配置文件中一些典型规则的解析：

![](img/ea8d288753193f224d50efd0d009ac9d_23.png)

1.  **内核日志输出到控制台**（通常被注释）：
    ```
    #kern.*                                                 /dev/console
    ```
    *   `kern`：设施，表示内核。
    *   `.*`：优先级，`*` 代表所有级别（从 `debug` 到 `emerg`）。
    *   `/dev/console`：动作，表示输出到系统控制台（屏幕）。

2.  **记录所有info及以上级别的日志（排除特定设施）**：
    ```
    *.info;mail.none;authpriv.none;cron.none                /var/log/messages
    ```
    *   `*.info`：所有设施的 `info` 级别及更高级别（`info`, `notice`, `warning`, `err`, `crit`, `alert`, `emerg`）的日志。
    *   `mail.none`：排除邮件设施（`mail`）的所有日志。
    *   `authpriv.none`：排除特权身份验证设施的所有日志。
    *   `cron.none`：排除计划任务设施的所有日志。
    *   `/var/log/messages`：动作，将匹配的日志记录到 `/var/log/messages` 文件中。

3.  **单独记录特权身份验证日志**：
    ```
    authpriv.*                                              /var/log/secure
    ```
    *   `authpriv.*`：特权身份验证设施的所有级别日志。
    *   `/var/log/secure`：动作，专门记录到 `/var/log/secure` 文件。

4.  **记录邮件日志（异步）**：
    ```
    mail.*                                                  -/var/log/maillog
    ```
    *   `mail.*`：邮件设施的所有级别日志。
    *   `-`（横杠）：表示异步写入，系统不会为每一条日志立即更新文件，有助于提升性能。
    *   `/var/log/maillog`：动作，记录到 `/var/log/maillog` 文件。

![](img/ea8d288753193f224d50efd0d009ac9d_25.png)

![](img/ea8d288753193f224d50efd0d009ac9d_26.png)

![](img/ea8d288753193f224d50efd0d009ac9d_28.png)

5.  **紧急警报发送给所有用户**：
    ```
    *.emerg                                                 :omusrmsg:*
    ```
    *   `*.emerg`：所有设施的 `emerg`（紧急）级别日志。
    *   `:omusrmsg:*`：动作，将消息发送给所有已登录的用户。

6.  **引导日志记录**：
    ```
    local7.*                                                /var/log/boot.log
    ```
    *   `local7.*`：自定义设施 `local7` 的所有级别日志，通常被系统用于记录启动过程信息。
    *   `/var/log/boot.log`：动作，记录到 `/var/log/boot.log` 文件。

![](img/ea8d288753193f224d50efd0d009ac9d_30.png)

### 自定义日志规则

![](img/ea8d288753193f224d50efd0d009ac9d_32.png)

![](img/ea8d288753193f224d50efd0d009ac9d_34.png)

通过修改 `rsyslog.conf` 文件，我们可以创建自定义规则。例如，将某个特定应用程序（使用 `local0` 设施）的错误及以上日志记录到单独文件：
```
local0.err                                                 /var/log/myapp-error.log
```

![](img/ea8d288753193f224d50efd0d009ac9d_36.png)

## 总结

![](img/ea8d288753193f224d50efd0d009ac9d_37.png)

![](img/ea8d288753193f224d50efd0d009ac9d_38.png)

本节课中我们一起学习了 `rsyslog.conf` 配置文件的核心机制。关键在于理解 **设施**（`facility`，日志来源）和 **优先级**（`priority`，日志严重程度）这两个参数，并通过 `设施.优先级` 的格式来定义过滤规则，最后指定日志的存储位置或处理动作（如写入文件、发送消息）。掌握这些规则后，你就能根据需求灵活地管理和分类系统日志。