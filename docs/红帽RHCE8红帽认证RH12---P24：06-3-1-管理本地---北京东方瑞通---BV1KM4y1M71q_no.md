# 红帽RHCE8认证课程：06-3-1：管理本地用户2-修改和删除用户 🛠️

![](img/f1e39499f0bec8e756feb8c2967d5fe3_1.png)

![](img/f1e39499f0bec8e756feb8c2967d5fe3_3.png)

在本节课中，我们将学习如何修改已存在的本地用户账户信息，以及如何安全地删除不再需要的用户。这是系统管理员日常维护工作中的重要部分。

上一节我们介绍了如何使用 `useradd` 命令添加用户。本节中我们来看看如何修改用户属性以及删除用户。

## 修改用户信息

![](img/f1e39499f0bec8e756feb8c2967d5fe3_5.png)

![](img/f1e39499f0bec8e756feb8c2967d5fe3_7.png)

用户创建后，如果信息不符合需求，可以使用 `usermod` 命令进行修改。

以下是 `usermod` 命令的一些常用选项和示例：

![](img/f1e39499f0bec8e756feb8c2967d5fe3_9.png)

![](img/f1e39499f0bec8e756feb8c2967d5fe3_10.png)

*   **修改用户描述信息**：使用 `-c` 选项。
    ```bash
    usermod -c "hello world" laoma2
    ```
*   **修改用户主组**：使用 `-g` 选项。
    ```bash
    usermod -g 1002 laoma2
    ```
*   **为用户添加附属组**：使用 `-aG` 选项（`-a` 表示追加，`-G` 指定组）。
    ```bash
    usermod -aG wheel laoma2
    ```
*   **移动用户家目录**：使用 `-md` 选项（`-m` 移动内容，`-d` 指定新路径）。
    ```bash
    usermod -md /home/new_laoma1 laoma1
    ```
*   **修改用户默认Shell**：使用 `-s` 选项。
    ```bash
    usermod -s /bin/sh laoma1
    ```
*   **锁定用户账户**：使用 `-L` 选项。这会在 `/etc/shadow` 文件中用户密码前添加 `!`，使其失效。
    ```bash
    usermod -L laoma7
    ```
*   **解锁用户账户**：使用 `-U` 选项。
    ```bash
    usermod -U laoma7
    ```

![](img/f1e39499f0bec8e756feb8c2967d5fe3_12.png)

![](img/f1e39499f0bec8e756feb8c2967d5fe3_13.png)

**重要提示**：所有 `usermod` 命令的操作，本质上都是修改 `/etc/passwd`、`/etc/shadow` 和 `/etc/group` 等系统配置文件。高级管理员应理解命令背后对文件的具体修改。

![](img/f1e39499f0bec8e756feb8c2967d5fe3_15.png)

![](img/f1e39499f0bec8e756feb8c2967d5fe3_17.png)

## 删除用户

![](img/f1e39499f0bec8e756feb8c2967d5fe3_19.png)

当用户不再需要时，应使用 `userdel` 命令将其删除。

![](img/f1e39499f0bec8e756feb8c2967d5fe3_21.png)

以下是删除用户时的注意事项和命令：

![](img/f1e39499f0bec8e756feb8c2967d5fe3_23.png)

![](img/f1e39499f0bec8e756feb8c2967d5fe3_25.png)

*   **删除用户并同时删除其家目录和邮件**：使用 `-r` 选项。**这是推荐的做法**，可以避免残留文件被新创建的同UID用户意外继承，造成安全隐患。
    ```bash
    userdel -r laoma6
    ```
*   **仅删除用户（保留家目录等文件）**：不使用 `-r` 选项。不推荐，因为会留下属于“无主用户”（仅显示UID数字）的文件。
    ```bash
    userdel laoma1
    ```
*   **强制删除用户**：使用 `-f` 选项。此选项应**谨慎使用**，例如当用户仍处于登录状态时强制删除可能导致问题。

## 清理无主文件

如果删除用户时未使用 `-r` 选项，或者操作不当，系统中可能会留下一些不属于任何用户的文件（其所有者显示为数字UID）。这些文件占用磁盘空间且可能带来安全风险。

我们可以使用 `find` 命令查找并清理这些文件：

![](img/f1e39499f0bec8e756feb8c2967d5fe3_27.png)

![](img/f1e39499f0bec8e756feb8c2967d5fe3_29.png)

1.  **查找无主文件**：
    ```bash
    find / -nouser 2>/dev/null
    ```
    （`2>/dev/null` 用于屏蔽错误信息）

![](img/f1e39499f0bec8e756feb8c2967d5fe3_31.png)

![](img/f1e39499f0bec8e756feb8c2967d5fe3_33.png)

2.  **手动删除找到的无主文件**。例如，删除属于UID 1001、1007、1009的文件：
    ```bash
    rm -rf /home/laoma{1,7,9}
    rm -rf /var/spool/mail/laoma{1,7,9}
    ```

![](img/f1e39499f0bec8e756feb8c2967d5fe3_35.png)

![](img/f1e39499f0bec8e756feb8c2967d5fe3_36.png)

![](img/f1e39499f0bec8e756feb8c2967d5fe3_38.png)

定期清理这些文件是良好的系统管理习惯。

## 管理本地组（预览）

![](img/f1e39499f0bec8e756feb8c2967d5fe3_40.png)

本地组的管理相对简单，主要涉及以下命令，我们将在下节课详细讲解：
*   `groupadd`：添加组。
*   `groupdel`：删除组。
*   `groupmod`：修改组属性。
*   `gpasswd`：管理组成员。

![](img/f1e39499f0bec8e756feb8c2967d5fe3_42.png)

![](img/f1e39499f0bec8e756feb8c2967d5fe3_44.png)

---

![](img/f1e39499f0bec8e756feb8c2967d5fe3_46.png)

![](img/f1e39499f0bec8e756feb8c2967d5fe3_48.png)

本节课中我们一起学习了如何修改用户账户的各项属性，以及安全删除用户的正确方法。关键点在于：修改用户使用 `usermod` 命令，删除用户时务必使用 `userdel -r` 以清除相关文件，并学会查找和清理系统中遗留的无主文件，以维护系统的整洁与安全。