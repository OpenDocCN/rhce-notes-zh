# Red Hat RHCE 8.0 认证体系课程：第五章：SELinux 安全上下文管理 🛡️

![](img/bc24fae8c4876b45126794d609354b61_0.png)

![](img/bc24fae8c4876b45126794d609354b61_1.png)

![](img/bc24fae8c4876b45126794d609354b61_3.png)

![](img/bc24fae8c4876b45126794d609354b61_5.png)

![](img/bc24fae8c4876b45126794d609354b61_7.png)

![](img/bc24fae8c4876b45126794d609354b61_9.png)

![](img/bc24fae8c4876b45126794d609354b61_10.png)

在本节课中，我们将要学习 SELinux 的基本概念、工作模式以及如何管理文件的安全上下文，以解决因安全策略导致的访问问题。

## 概述

![](img/bc24fae8c4876b45126794d609354b61_12.png)

![](img/bc24fae8c4876b45126794d609354b61_14.png)

SELinux 是 Linux 内核的一个安全模块，它通过强制访问控制来增强系统的安全性。与传统的用户-文件权限模型不同，SELinux 的主体是进程，客体是文件，访问能否成功取决于进程的安全上下文是否与文件的安全上下文匹配。

![](img/bc24fae8c4876b45126794d609354b61_16.png)

![](img/bc24fae8c4876b45126794d609354b61_18.png)

## SELinux 的基本概念

![](img/bc24fae8c4876b45126794d609354b61_20.png)

上一节我们介绍了传统权限模型，本节中我们来看看 SELinux 如何增强安全性。

在传统权限模型中，主体是用户，客体是文件。例如，用户 `apache` 对文件 `/var/www/html/index.html` 有读权限即可访问。

![](img/bc24fae8c4876b45126794d609354b61_22.png)

![](img/bc24fae8c4876b45126794d609354b61_24.png)

在 SELinux 模型中，主体变成了进程（例如 `httpd` 进程），客体仍然是文件。只有当客体的安全上下文符合主体的安全上下文设定时，访问才被允许。这提供了更细粒度的安全控制。

![](img/bc24fae8c4876b45126794d609354b61_26.png)

## SELinux 的工作模式

![](img/bc24fae8c4876b45126794d609354b61_28.png)

SELinux 有三种主要的工作模式，了解这些模式是管理 SELinux 的基础。

以下是 SELinux 的三种工作模式：

*   **Enforcing（强制模式）**：SELinux 策略被强制执行，违反策略的行为将被阻止并记录。
*   **Permissive（宽容模式）**：SELinux 策略不被强制执行，但违反策略的行为会被记录到日志中。此模式常用于故障排查。
*   **Disabled（禁用模式）**：SELinux 被完全关闭。

**模式切换规则**：
*   在 **Enforcing** 和 **Permissive** 模式之间切换是临时的，使用 `setenforce` 命令即可，无需重启。
*   切换到或从 **Disabled** 模式切换出来，必须修改配置文件并**重启系统**才能生效。

### 查看与修改 SELinux 模式

以下是管理 SELinux 模式的核心命令：

![](img/bc24fae8c4876b45126794d609354b61_30.png)

![](img/bc24fae8c4876b45126794d609354b61_31.png)

![](img/bc24fae8c4876b45126794d609354b61_33.png)

![](img/bc24fae8c4876b45126794d609354b61_35.png)

**查看当前模式**：
```bash
getenforce
```

![](img/bc24fae8c4876b45126794d609354b61_37.png)

**临时切换模式**：
```bash
# 切换到 Enforcing 模式
setenforce 1
# 切换到 Permissive 模式
setenforce 0
```

![](img/bc24fae8c4876b45126794d609354b61_39.png)

![](img/bc24fae8c4876b45126794d609354b61_41.png)

**永久修改模式**：
需要编辑配置文件 `/etc/selinux/config`（或它的软链接 `/etc/sysconfig/selinux`）。
```bash
vim /etc/selinux/config
```
找到 `SELINUX=` 这一行，将其值修改为 `enforcing`、`permissive` 或 `disabled`。修改为 `disabled` 后必须重启系统。

![](img/bc24fae8c4876b45126794d609354b61_43.png)

![](img/bc24fae8c4876b45126794d609354b61_45.png)

## 安全上下文与问题模拟

![](img/bc24fae8c4876b45126794d609354b61_47.png)

![](img/bc24fae8c4876b45126794d609354b61_49.png)

理解了 SELinux 的模式后，我们通过一个实验来模拟因安全上下文不匹配导致的问题。

![](img/bc24fae8c4876b45126794d609354b61_51.png)

![](img/bc24fae8c4876b45126794d609354b61_53.png)

![](img/bc24fae8c4876b45126794d609354b61_55.png)

![](img/bc24fae8c4876b45126794d609354b61_57.png)

我们以 Apache HTTP 服务器 (`httpd`) 为例。当我们将网页文件从默认目录 `/var/www/html/` 移动到一个新建的目录（如 `/usr/local/www/`）后，即使文件权限正确，访问也可能返回 **403 Forbidden** 错误。

![](img/bc24fae8c4876b45126794d609354b61_58.png)

![](img/bc24fae8c4876b45126794d609354b61_60.png)

![](img/bc24fae8c4876b45126794d609354b61_61.png)

![](img/bc24fae8c4876b45126794d609354b61_63.png)

![](img/bc24fae8c4876b45126794d609354b61_64.png)

**原因分析**：
1.  使用 `ps -Z` 查看 `httpd` 进程的安全上下文，其类型为 `httpd_t`。
    ```bash
    ps -Z -C httpd
    ```
2.  使用 `ls -Z` 查看新建目录下文件的安全上下文，其类型可能为 `usr_t` 或 `default_t`。
    ```bash
    ls -Z -d /usr/local/www/
    ls -Z /usr/local/www/index.html
    ```
3.  进程的安全上下文类型 (`httpd_t`) 与文件的安全上下文类型 (`usr_t`) 不匹配，因此 SELinux 拒绝了 `httpd` 进程对该文件的访问。

![](img/bc24fae8c4876b45126794d609354b61_66.png)

![](img/bc24fae8c4876b45126794d609354b61_67.png)

![](img/bc24fae8c4876b45126794d609354b61_68.png)

![](img/bc24fae8c4876b45126794d609354b61_69.png)

![](img/bc24fae8c4876b45126794d609354b61_71.png)

## 管理文件安全上下文

![](img/bc24fae8c4876b45126794d609354b61_73.png)

![](img/bc24fae8c4876b45126794d609354b61_75.png)

![](img/bc24fae8c4876b45126794d609354b61_77.png)

当出现因安全上下文不匹配导致的访问问题时，我们需要修改文件或目录的安全上下文类型。

以下是修改安全上下文的几种方法：

### 方法一：使用 `chcon` 命令（临时修改）

`chcon` 命令可以临时更改文件的安全上下文，但新建文件不会继承此设置。

*   **修改类型**：使用 `-t` 选项指定新的类型。
    ```bash
    chcon -t httpd_sys_content_t /usr/local/www/index.html
    ```
*   **参照修改**：使用 `--reference` 选项，参照另一个文件的安全上下文进行设置。
    ```bash
    chcon --reference=/var/www/html/index.html /usr/local/www/index.html
    ```

![](img/bc24fae8c4876b45126794d609354b61_79.png)

### 方法二：使用 `semanage` 与 `restorecon`（永久修改）

这是推荐的方法，可以修改默认规则，并使更改在系统重启后依然有效，新创建的文件也会自动继承正确的上下文。

1.  **添加默认上下文规则**：使用 `semanage fcontext` 为指定路径添加默认的上下文类型。
    ```bash
    # 为目录及其下所有内容添加规则
    semanage fcontext -a -t httpd_sys_content_t “/usr/local/www(/.*)?”
    ```
    命令解释：
    *   `-a`: 添加规则。
    *   `-t httpd_sys_content_t`: 设置类型为 `httpd_sys_content_t`（Web 内容的标准类型）。
    *   `“/usr/local/www(/.*)?”`: 匹配 `/usr/local/www/` 目录及其所有子文件和子目录。

2.  **应用新的默认规则**：使用 `restorecon` 命令将新规则递归地应用到目标路径。
    ```bash
    restorecon -Rv /usr/local/www/
    ```
    命令解释：
    *   `-R`: 递归处理子目录。
    *   `-v`: 显示详细过程。
    *   执行后，该目录下文件的安全上下文类型将变为 `httpd_sys_content_t`。

## 总结

本节课中我们一起学习了 SELinux 的核心知识。我们首先了解了 SELinux 通过进程和文件的安全上下文进行强制访问控制的基本原理。然后，我们掌握了 SELinux 的三种工作模式（Enforcing, Permissive, Disabled）及其查看与切换方法。最后，我们通过模拟实验，深入探讨了因安全上下文不匹配导致的常见问题，并学习了使用 `chcon` 命令进行临时修改，以及使用 `semanage fcontext` 和 `restorecon` 命令进行永久性修正的两种解决方案。正确管理 SELinux 安全上下文对于确保系统服务在严格安全策略下正常运行至关重要。