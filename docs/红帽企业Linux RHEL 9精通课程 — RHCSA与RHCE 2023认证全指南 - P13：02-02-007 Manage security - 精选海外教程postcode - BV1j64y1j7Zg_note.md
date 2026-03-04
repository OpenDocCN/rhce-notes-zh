# 红帽企业Linux RHEL 9精通课程：P13：管理安全 🔐

![](img/3848de90e34ddc828ee24cb0802c31fa_1.png)

在本节课中，我们将学习如何在RHEL 9系统中管理安全。主要内容包括配置防火墙、设置基于SSH密钥的身份验证以及管理SELinux。这些是RHCSA和RHCE认证考试中的核心技能，也是系统管理员日常工作的基础。

![](img/3848de90e34ddc828ee24cb0802c31fa_3.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_5.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_7.png)

## 配置防火墙设置

![](img/3848de90e34ddc828ee24cb0802c31fa_9.png)

上一节我们介绍了课程概述，本节中我们来看看如何配置防火墙。在RHEL 9中，管理防火墙的主要工具是`firewalld`，它取代了旧版本中的`iptables`。`firewalld`提供了两种配置方式：命令行工具`firewall-cmd`和图形界面工具。本教程将重点介绍命令行实用程序。

![](img/3848de90e34ddc828ee24cb0802c31fa_11.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_13.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_15.png)

首先，我们需要安装并启动`firewalld`服务。

![](img/3848de90e34ddc828ee24cb0802c31fa_17.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_19.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_20.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_22.png)

以下是安装和启动`firewalld`的步骤：
1.  安装`firewalld`软件包：
    ```bash
    yum install -y firewalld
    ```
2.  启动并启用`firewalld`服务，使其在系统启动时自动运行：
    ```bash
    systemctl start firewalld
    systemctl enable firewalld
    ```
3.  检查服务状态，确认其已启动并启用：
    ```bash
    systemctl status firewalld
    ```

## 理解防火墙区域

![](img/3848de90e34ddc828ee24cb0802c31fa_24.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_26.png)

`firewalld`的核心概念是**区域**。区域是一组预定义的规则，用于管理特定网络连接或接口的信任级别。每个区域都包含一组允许的服务和端口。

![](img/3848de90e34ddc828ee24cb0802c31fa_28.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_30.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_32.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_34.png)

以下是查看和管理区域的命令：
1.  查看所有可用区域：
    ```bash
    firewall-cmd --get-zones
    ```
2.  查看当前的默认区域：
    ```bash
    firewall-cmd --get-default-zone
    ```
3.  查看指定区域（如`public`）的详细配置：
    ```bash
    firewall-cmd --zone=public --list-all
    ```

![](img/3848de90e34ddc828ee24cb0802c31fa_36.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_38.png)

## 管理防火墙规则

![](img/3848de90e34ddc828ee24cb0802c31fa_40.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_42.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_44.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_46.png)

了解了区域概念后，我们可以开始添加或修改防火墙规则。规则可以针对整个服务（如`http`）或特定端口进行设置。

![](img/3848de90e34ddc828ee24cb0802c31fa_48.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_50.png)

以下是管理防火墙规则的步骤：
1.  向默认区域永久添加一项服务（例如`http`）：
    ```bash
    firewall-cmd --add-service=http --permanent
    ```
2.  向默认区域永久添加一个特定端口（例如TCP协议的80端口）：
    ```bash
    firewall-cmd --add-port=80/tcp --permanent
    ```
    > **注意**：使用`--permanent`标志可使规则在重启后依然生效。添加规则后，需要重新加载防火墙配置才能立即应用。
3.  重新加载防火墙配置，使新的永久规则生效：
    ```bash
    firewall-cmd --reload
    ```
4.  再次列出区域配置，确认新规则已激活：
    ```bash
    firewall-cmd --zone=public --list-all
    ```

![](img/3848de90e34ddc828ee24cb0802c31fa_52.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_54.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_56.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_58.png)

## 配置基于SSH密钥的身份验证

![](img/3848de90e34ddc828ee24cb0802c31fa_60.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_62.png)

上一节我们配置了防火墙，本节中我们来看看如何配置更安全的SSH登录方式——基于密钥的身份验证。这种方法比仅使用密码更安全。

![](img/3848de90e34ddc828ee24cb0802c31fa_64.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_66.png)

以下是配置SSH密钥认证的步骤：
1.  在客户端生成SSH密钥对（公钥和私钥）。使用`ssh-keygen`命令，过程中可以设置密钥密码（为方便演示，此处留空）：
    ```bash
    ssh-keygen
    ```
2.  将生成的公钥复制到远程服务器。使用`ssh-copy-id`命令，这会将公钥自动添加到服务器对应用户的`~/.ssh/authorized_keys`文件中：
    ```bash
    ssh-copy-id username@server_ip_address
    ```
3.  测试使用密钥登录，此时应无需输入密码即可连接：
    ```bash
    ssh username@server_ip_address
    ```

## 管理SELinux

![](img/3848de90e34ddc828ee24cb0802c31fa_68.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_69.png)

SELinux（安全增强型Linux）为系统提供了额外的强制访问控制安全层。理解其基本操作对于系统安全至关重要。

![](img/3848de90e34ddc828ee24cb0802c31fa_71.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_72.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_74.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_76.png)

首先，我们需要了解SELinux的当前运行模式。

![](img/3848de90e34ddc828ee24cb0802c31fa_78.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_80.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_82.png)

以下是查看和更改SELinux模式的命令：
1.  查看当前SELinux模式：
    ```bash
    getenforce
    ```
    模式通常为**Enforcing**（强制模式，执行策略）或**Permissive**（宽容模式，仅记录警告）。
2.  临时将模式更改为宽容模式（重启后失效）：
    ```bash
    setenforce 0
    ```
3.  临时将模式改回强制模式：
    ```bash
    setenforce 1
    ```

![](img/3848de90e34ddc828ee24cb0802c31fa_84.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_86.png)

## 管理SELinux布尔值

![](img/3848de90e34ddc828ee24cb0802c31fa_88.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_90.png)

SELinux布尔值是预定义的策略开关，可以动态调整，无需重新编译整个策略。

![](img/3848de90e34ddc828ee24cb0802c31fa_92.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_94.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_96.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_98.png)

以下是管理SELinux布尔值的命令：
1.  列出所有布尔值，并筛选出与`http`相关的：
    ```bash
    getsebool -a | grep http
    ```
2.  永久开启或关闭一个布尔值（例如`httpd_enable_cgi`）：
    ```bash
    setsebool -P httpd_enable_cgi on
    # 或
    setsebool -P httpd_enable_cgi off
    ```

![](img/3848de90e34ddc828ee24cb0802c31fa_100.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_102.png)

## 管理SELinux文件上下文

![](img/3848de90e34ddc828ee24cb0802c31fa_104.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_106.png)

SELinux为文件和进程分配安全上下文（标签）。当服务（如Apache）无法访问非默认位置的文件时，通常是上下文不正确导致的。

![](img/3848de90e34ddc828ee24cb0802c31fa_108.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_110.png)

以下示例演示了如何为Apache配置一个非默认的网站根目录，并修正其SELinux上下文。
1.  创建新目录并更改Apache配置，将`DocumentRoot`指向新目录（如`/apache`）。
2.  在新目录中创建测试网页`index.html`。
3.  尝试访问网页时，如果SELinux处于强制模式，可能会收到“403 Forbidden”错误。切换到宽容模式后访问成功，则确认是SELinux策略阻止。
4.  修正文件上下文：
    *   查看默认网站目录的正确上下文：
        ```bash
        ls -Z /var/www/html
        ```
    *   将正确的上下文类型（如`httpd_sys_content_t`）应用到新目录：
        ```bash
        semanage fcontext -a -t httpd_sys_content_t “/apache(/.*)?”
        ```
    *   使用`restorecon`命令递归地应用新的上下文规则：
        ```bash
        restorecon -Rv /apache
        ```
5.  将SELinux模式改回**Enforcing**，再次测试网页访问，此时应能成功访问。

## 查看SELinux审计日志

![](img/3848de90e34ddc828ee24cb0802c31fa_112.png)

![](img/3848de90e34ddc828ee24cb0802c31fa_114.png)

当遇到权限问题时，SELinux审计日志是首要的排查工具。

![](img/3848de90e34ddc828ee24cb0802c31fa_116.png)

使用以下命令查看与SELinux相关的警报，日志通常会给出解决问题的建议：
```bash
sealert -a /var/log/audit/audit.log
```

本节课中我们一起学习了RHEL 9中的核心安全管理技能。我们掌握了使用`firewalld`配置防火墙规则、设置基于密钥的SSH认证以提高安全性，以及管理SELinux的运行模式、布尔值和文件上下文来确保系统服务的正常访问。这些技能对于维护Linux系统的安全至关重要。