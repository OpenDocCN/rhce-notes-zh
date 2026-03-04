# 邮件服务器配置教程：P39：9. 配置 main.cf 文件 🔧

![](img/cf1668a694192f5fb736b37b11f82d20_1.png)

![](img/cf1668a694192f5fb736b37b11f82d20_3.png)

![](img/cf1668a694192f5fb736b37b11f82d20_5.png)

![](img/cf1668a694192f5fb736b37b11f82d20_7.png)

在本节课中，我们将学习如何配置 Postfix 邮件服务器的主配置文件 `main.cf`。我们将逐步修改关键参数，以设置服务器的主机名、域名、网络接口、安全选项等，确保邮件服务能够正确运行。

---

![](img/cf1668a694192f5fb736b37b11f82d20_9.png)

![](img/cf1668a694192f5fb736b37b11f82d20_11.png)

上一节我们介绍了 Postfix 的基本概念，本节中我们来看看如何具体配置其主配置文件。

![](img/cf1668a694192f5fb736b37b11f82d20_13.png)

![](img/cf1668a694192f5fb736b37b11f82d20_15.png)

首先，我们需要打开并编辑 Postfix 的主配置文件 `main.cf`。

![](img/cf1668a694192f5fb736b37b11f82d20_17.png)

![](img/cf1668a694192f5fb736b37b11f82d20_19.png)

![](img/cf1668a694192f5fb736b37b11f82d20_21.png)

```
vim /etc/postfix/main.cf
```

![](img/cf1668a694192f5fb736b37b11f82d20_23.png)

![](img/cf1668a694192f5fb736b37b11f82d20_25.png)

![](img/cf1668a694192f5fb736b37b11f82d20_27.png)

在文件中，我们需要搜索并修改几个关键的配置项。

![](img/cf1668a694192f5fb736b37b11f82d20_29.png)

![](img/cf1668a694192f5fb736b37b11f82d20_31.png)

以下是需要查找和修改的配置项列表：

![](img/cf1668a694192f5fb736b37b11f82d20_33.png)

![](img/cf1668a694192f5fb736b37b11f82d20_35.png)

1.  **`inet_interfaces`**
    找到 `inet_interfaces` 这一行。它可能被注释掉，需要取消注释并将其值设置为 `localhost`。
    ```
    inet_interfaces = localhost
    ```

![](img/cf1668a694192f5fb736b37b11f82d20_37.png)

![](img/cf1668a694192f5fb736b37b11f82d20_39.png)

![](img/cf1668a694192f5fb736b37b11f82d20_41.png)

2.  **`mydestination`**
    找到 `mydestination` 这一行。同样，取消注释并将其值设置为 `$myhostname`, `localhost.$mydomain`, `localhost`。
    ```
    mydestination = $myhostname, localhost.$mydomain, localhost
    ```

![](img/cf1668a694192f5fb736b37b11f82d20_43.png)

![](img/cf1668a694192f5fb736b37b11f82d20_45.png)

接下来，我们需要在文件末尾添加一系列新的配置行。

![](img/cf1668a694192f5fb736b37b11f82d20_47.png)

![](img/cf1668a694192f5fb736b37b11f82d20_49.png)

以下是需要添加的新配置参数列表：

![](img/cf1668a694192f5fb736b37b11f82d20_51.png)

*   **`myhostname`**: 设置服务器的主机名。
    ```
    myhostname = mail.linuxgig.com
    ```
*   **`mydomain`**: 设置服务器的域名。
    ```
    mydomain = linuxgig.com
    ```
*   **`myorigin`**: 设置外发邮件的域名。
    ```
    myorigin = $mydomain
    ```
*   **`home_mailbox`**: 设置用户邮箱的存储路径。
    ```
    home_mailbox = Mail/
    ```
*   **`mynetworks`**: 定义信任的网络，允许中继邮件。
    ```
    mynetworks = 127.0.0.0/8
    ```
*   **`inet_interfaces`**: 再次确认监听的网络接口。
    ```
    inet_interfaces = all
    ```
*   **`mydestination`**: 再次确认本机接收邮件的域名。
    ```
    mydestination = $myhostname, localhost.$mydomain, localhost
    ```
*   **`smtpd_sasl_type`**: 指定 SASL 认证类型。
    ```
    smtpd_sasl_type = dovecot
    ```
*   **`smtpd_sasl_path`**: 指定 SASL 认证套接字路径。
    ```
    smtpd_sasl_path = private/auth
    ```
*   **`smtpd_sasl_local_domain`**: 留空即可。
    ```
    smtpd_sasl_local_domain =
    ```
*   **`smtpd_sasl_security_options`**: 设置 SASL 安全选项，禁止匿名登录。
    ```
    smtpd_sasl_security_options = noanonymous
    ```
*   **`broken_sasl_auth_clients`**: 兼容旧版 SASL 客户端。
    ```
    broken_sasl_auth_clients = yes
    ```
*   **`smtpd_sasl_auth_enable`**: 启用 SASL 认证。
    ```
    smtpd_sasl_auth_enable = yes
    ```
*   **`smtpd_recipient_restrictions`**: 设置收件人限制规则。
    ```
    smtpd_recipient_restrictions = permit_sasl_authenticated, permit_mynetworks
    ```
*   **`smtp_tls_security_level`**: 设置 SMTP 客户端 TLS 安全级别。
    ```
    smtp_tls_security_level = may
    ```
*   **`smtpd_tls_security_level`**: 设置 SMTP 服务器端 TLS 安全级别。
    ```
    smtpd_tls_security_level = may
    ```
*   **`smtp_tls_note_starttls_offer`**: 记录 STARTTLS 支持情况。
    ```
    smtp_tls_note_starttls_offer = yes
    ```
*   **`smtpd_tls_note_starttls_offer`**: 记录 STARTTLS 支持情况。
    ```
    smtpd_tls_note_starttls_offer = yes
    ```
*   **`smtpd_tls_loglevel`**: 设置 TLS 日志级别。
    ```
    smtpd_tls_loglevel = 1
    ```
*   **`smtpd_tls_key_file`**: 指定 TLS 私钥文件路径。
    ```
    smtpd_tls_key_file = /etc/postfix/ssl/server.key
    ```
*   **`smtpd_tls_cert_file`**: 指定 TLS 证书文件路径。
    ```
    smtpd_tls_cert_file = /etc/postfix/ssl/server.crt
    ```
*   **`smtpd_tls_received_header`**: 在邮件头中添加 TLS 信息。
    ```
    smtpd_tls_received_header = yes
    ```
*   **`smtpd_tls_session_cache_timeout`**: 设置 TLS 会话缓存超时时间。
    ```
    smtpd_tls_session_cache_timeout = 3600s
    ```
*   **`tls_random_source`**: 指定 TLS 随机数源。
    ```
    tls_random_source = dev:/dev/urandom
    ```

![](img/cf1668a694192f5fb736b37b11f82d20_53.png)

![](img/cf1668a694192f5fb736b37b11f82d20_55.png)

![](img/cf1668a694192f5fb736b37b11f82d20_57.png)

---

![](img/cf1668a694192f5fb736b37b11f82d20_59.png)

![](img/cf1668a694192f5fb736b37b11f82d20_61.png)

本节课中我们一起学习了如何配置 Postfix 邮件服务器的 `main.cf` 文件。我们修改了核心的网络、域名和安全设置，并添加了支持 SASL 认证和 TLS 加密的配置项，为搭建一个功能完整的邮件服务器奠定了基础。请仔细检查所有配置项，确保没有拼写错误。