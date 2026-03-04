# Redhat红帽 RHCE8.0认证体系课程：P40：SELINUX 安全上下文配置与管理 🛡️

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_1.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_3.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_5.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_7.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_9.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_10.png)

在本节课中，我们将要学习 SELinux 的核心概念、工作模式以及如何通过配置安全上下文来解决进程访问文件时的权限问题。我们将通过一个具体的 HTTPD 服务示例，演示当安全上下文不匹配时如何导致访问失败，并学习修复方法。

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_12.png)

---

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_14.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_16.png)

## 概述：什么是 SELinux？

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_18.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_20.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_22.png)

上一节我们介绍了常规的文件权限管理。本节中我们来看看 SELinux，它是一种增强的 Linux 安全机制。

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_24.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_26.png)

SELinux 的全称是 **Security-Enhanced Linux**。与传统的用户-文件权限模型不同，SELinux 引入了**安全上下文**的概念。其核心思想是：进程（主体）能否访问一个文件或目录（客体），不仅取决于传统的读/写/执行权限，还必须满足双方安全上下文的匹配规则。

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_28.png)

简单来说，即使一个文件对“其他用户”有读权限，如果运行中的进程（如 Apache）的安全上下文类型与文件的安全上下文类型不匹配，访问也会被 SELinux 阻止。

---

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_30.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_32.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_34.png)

## SELinux 工作模式

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_36.png)

SELinux 有三种运行模式，了解并切换这些模式是管理 SELinux 的基础。

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_38.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_40.png)

以下是 SELinux 的三种模式：
*   **Enforcing（强制模式）**：SELinux 策略被强制执行，违反策略的行为将被阻止并记录到日志。
*   **Permissive（宽容模式）**：SELinux 策略不被强制执行，但违反策略的行为会被记录到日志。此模式常用于故障排查。
*   **Disabled（禁用模式）**：SELinux 被完全关闭。

### 查看与切换 SELinux 模式

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_42.png)

要查看当前 SELinux 的模式，使用命令：
```bash
getenforce
```

要在 **Enforcing** 和 **Permissive** 模式之间**临时切换**，使用 `setenforce` 命令（重启后失效）：
```bash
# 切换到 Enforcing 模式
setenforce 1
# 切换到 Permissive 模式
setenforce 0
```
**注意**：`setenforce` 命令无法切换到 `Disabled` 模式。

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_44.png)

要**永久修改** SELinux 模式（包括切换到 Disabled），需要编辑配置文件 `/etc/selinux/config`（或它的软链接 `/etc/sysconfig/selinux`）。
```bash
vim /etc/selinux/config
```
找到 `SELINUX=` 这一行，将其值修改为 `enforcing`、`permissive` 或 `disabled`。

**重要规则**：
*   任何涉及 `disabled` 的模式切换（如 `enforcing` -> `disabled` 或 `disabled` -> `enforcing`），都必须在修改配置文件后**重启系统**才能生效。
*   仅在 `enforcing` 和 `permissive` 之间切换时，可以临时使用 `setenforce`，或修改配置文件后**无需重启**（对新启动的进程生效）。

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_46.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_48.png)

---

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_50.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_51.png)

## 实验：安全上下文不匹配导致的问题

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_53.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_55.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_57.png)

上一节我们了解了 SELinux 的模式，本节中我们通过一个实际案例来看看安全上下文不匹配如何影响服务。

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_59.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_61.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_63.png)

我们以 Apache HTTPD 服务为例。假设我们想将网站的默认根目录从 `/var/www/html/` 更改为 `/usr/local/www/`。

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_65.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_67.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_68.png)

1.  **准备环境**：确保 HTTPD 服务已安装并启动。
2.  **创建测试页面**：在新目录 `/usr/local/www/` 下创建首页文件 `index.html`。
3.  **修改配置**：将 HTTPD 配置文件中的 `DocumentRoot` 指向新目录 `/usr/local/www/`，并重启服务。
4.  **测试访问**：此时通过浏览器或 `curl` 命令访问服务器，可能会收到 **403 Forbidden** 错误。

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_70.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_72.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_74.png)

**问题分析**：
检查 HTTPD 进程和文件的安全上下文：
```bash
# 查看 HTTPD 进程的安全上下文类型
ps -eZ | grep httpd
# 输出示例：system_u:system_r:httpd_t:s0
# 关键类型是 `httpd_t`

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_76.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_78.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_79.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_81.png)

# 查看文件的安全上下文
ls -lZ /usr/local/www/index.html
# 输出示例：unconfined_u:object_r:usr_t:s0
# 关键类型是 `usr_t`
```
发现进程的安全上下文类型 (`httpd_t`) 与文件的安全上下文类型 (`usr_t`) 不一致。在 SELinux 处于 `enforcing` 模式时，即使文件有传统的读权限，访问也会被拒绝。

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_83.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_84.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_86.png)

---

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_88.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_90.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_91.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_92.png)

## 解决方案：管理安全上下文

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_94.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_95.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_96.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_97.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_98.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_99.png)

当安全上下文不匹配时，我们需要修改文件或目录的安全上下文类型，使其与进程所期望的类型一致。

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_101.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_103.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_105.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_107.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_109.png)

### 方法一：使用 `semanage fcontext` 修改默认规则并还原

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_111.png)

这是推荐的方法，因为它会修改 SELinux 的策略规则，使其持久化，并且使用 `restorecon` 命令可以安全地应用更改。

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_113.png)

以下是操作步骤：
1.  **添加新的默认上下文规则**：告诉 SELinux，某个路径下的文件默认应该是何种类型。
    ```bash
    semanage fcontext -a -t httpd_sys_content_t “/usr/local/www(/.*)?”
    ```
    *   `-a`: 添加规则。
    *   `-t`: 指定安全上下文类型，`httpd_sys_content_t` 是 Web 内容文件的常用类型。
    *   `“/usr/local/www(/.*)?”`: 路径规则，`(/.*)?` 表示匹配该目录及其下所有子目录和文件。

2.  **应用新的规则**：使用 `restorecon` 命令将新规则递归地应用到目标路径。
    ```bash
    restorecon -Rv /usr/local/www/
    ```
    *   `-R`: 递归操作。
    *   `-v`: 显示详细过程。

执行后，`/usr/local/www/` 目录及其内容的安全上下文类型将变为 `httpd_sys_content_t`。

### 方法二：使用 `chcon` 临时修改

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_115.png)

`chcon` 命令可以直接更改文件对象的安全上下文，但它是临时性的。如果系统执行了 `restorecon` 或文件系统被重新打标（relabel），更改可能会被覆盖。

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_117.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_119.png)

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_120.png)

以下是 `chcon` 的几种用法：
*   **直接指定类型**：
    ```bash
    chcon -t httpd_sys_content_t /usr/local/www/index.html
    ```
*   **参考另一个文件的安全上下文**：
    ```bash
    chcon --reference=/var/www/html/index.html /usr/local/www/index.html
    ```
    `--reference` 参数会参照第一个文件（源）的安全上下文来修改第二个文件（目标）。

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_122.png)

**注意**：`chcon` 适用于快速测试和临时修复。对于生产环境，建议使用方法一 (`semanage fcontext` + `restorecon`) 以确保配置持久化。

---

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_124.png)

## 总结

![](img/bc64bb66c6c4aa1fb48f8da7ce355e84_126.png)

本节课中我们一起学习了 SELinux 的核心知识。
1.  **SELinux 概念**：它是一种基于安全上下文的强制访问控制机制，用于增强系统安全。
2.  **三种模式**：`Enforcing`（强制）、`Permissive`（宽容）、`Disabled`（禁用），并掌握了使用 `getenforce`/`setenforce` 和修改 `/etc/selinux/config` 文件来查看和切换模式的方法。
3.  **安全上下文**：理解了进程与文件的安全上下文类型必须匹配，访问才能被允许。
4.  **解决问题**：当因上下文不匹配导致服务异常（如 HTTPD 403 错误）时，我们学会了使用 `semanage fcontext` 和 `restorecon` 命令持久地修正文件安全上下文，也了解了使用 `chcon` 进行临时修改。

通过正确配置 SELinux 安全上下文，可以在不降低安全性的前提下，确保各种服务和应用正常运行。