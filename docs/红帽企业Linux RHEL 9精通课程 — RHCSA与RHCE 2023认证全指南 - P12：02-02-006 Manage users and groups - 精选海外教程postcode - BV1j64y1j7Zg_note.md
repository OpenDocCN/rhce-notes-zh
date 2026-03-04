# 红帽企业Linux RHEL 9精通课程：02-02-006：管理用户和组

![](img/aceada4eaa93968834901c4c73752391_1.png)

在本节课程中，我们将学习如何在RHEL 9系统中管理本地用户和组。这是RHCSA认证考试中的一项核心技能，也是日常系统管理的基础。我们将涵盖如何查看、创建、修改和删除用户与组，以及如何配置超级用户访问权限。

![](img/aceada4eaa93968834901c4c73752391_3.png)

## 查看用户和组信息

上一节我们介绍了课程概述，本节中我们来看看如何获取系统中已有的用户和组信息。这是管理工作的第一步。

有多种命令和文件可以帮助我们查看用户和组的信息。

![](img/aceada4eaa93968834901c4c73752391_5.png)

以下是查看用户信息的主要方法：

*   **`id` 命令**：显示指定用户的用户ID（UID）、主组ID（GID）以及所属的补充组。
    ```bash
    id cloud_user
    ```
*   **`groups` 命令**：仅显示指定用户所属的组列表。
    ```bash
    groups cloud_user
    ```
*   **`/etc/passwd` 文件**：存储所有用户的基本信息，如用户名、UID、GID、描述、主目录和登录Shell。
    ```bash
    cat /etc/passwd
    ```
*   **`/etc/shadow` 文件**：存储用户的安全信息，主要是加密后的密码哈希。
    ```bash
    cat /etc/shadow
    ```

![](img/aceada4eaa93968834901c4c73752391_7.png)

以下是查看组信息的主要方法：

![](img/aceada4eaa93968834901c4c73752391_9.png)

*   **`/etc/group` 文件**：存储系统中所有组的信息，包括组名、组ID（GID）和组成员列表。
    ```bash
    cat /etc/group
    ```

![](img/aceada4eaa93968834901c4c73752391_11.png)

![](img/aceada4eaa93968834901c4c73752391_13.png)

记住这些命令和文件，对于诊断和获取用户、组的详细信息非常有帮助。

![](img/aceada4eaa93968834901c4c73752391_15.png)

## 管理本地用户

了解了如何查看信息后，接下来我们学习如何创建、修改和删除本地用户账户。

### 创建用户

使用 `useradd` 命令创建新用户。创建时可以指定各种选项，例如主目录和登录Shell。

以下是一个创建用户的基本示例，其中使用 `-d` 选项指定了自定义的主目录：

```bash
useradd -d /home/maddie matt
```

### 修改用户

创建用户后，可以使用 `usermod` 命令修改其属性。

以下是 `usermod` 命令的一些常用选项示例：

![](img/aceada4eaa93968834901c4c73752391_17.png)

*   `-d /home/matt -m`：将用户的主目录改为 `/home/matt`，并使用 `-m` 选项将旧目录内容移动到新位置。
*   `-aG wheel`：将用户添加到 `wheel` 补充组（`-a` 表示追加，`-G` 指定组）。
*   `-g test1`：更改用户的主登录组为 `test1`。
*   `-L`：锁定用户账户，禁止登录。
*   `-U`：解锁用户账户。

例如，要修改用户 `matt` 的主目录并添加补充组，可以执行：

```bash
usermod -d /home/matt -aG wheel matt
```

### 设置与修改用户密码

使用 `passwd` 命令为用户设置或更改密码。

![](img/aceada4eaa93968834901c4c73752391_19.png)

```bash
passwd matt
```

![](img/aceada4eaa93968834901c4c73752391_21.png)

执行后，系统会提示你输入并确认新密码。设置成功后，可以查看 `/etc/shadow` 文件，会发现原本表示未设置密码的 `!!` 已被加密哈希值取代。

![](img/aceada4eaa93968834901c4c73752391_23.png)

### 管理密码和账户过期

![](img/aceada4eaa93968834901c4c73752391_25.png)

使用 `chage` 命令管理密码和账户的有效期。

![](img/aceada4eaa93968834901c4c73752391_27.png)

以下是 `chage` 命令的常用选项示例：

![](img/aceada4eaa93968834901c4c73752391_29.png)

![](img/aceada4eaa93968834901c4c73752391_30.png)

*   `chage -l matt`：列出用户 `matt` 的密码和账户过期信息。
*   `chage -M 30 matt`：设置密码在30天后过期。
*   `chage -E 2019-11-15 matt`：设置账户在2019年11月15日过期。

## 管理本地组

![](img/aceada4eaa93968834901c4c73752391_32.png)

用户管理离不开组。现在，我们来看看如何管理组。

![](img/aceada4eaa93968834901c4c73752391_34.png)

### 创建组

![](img/aceada4eaa93968834901c4c73752391_36.png)

使用 `groupadd` 命令创建新组。

```bash
groupadd test1
groupadd test2
groupadd test3
```

### 修改组成员

创建组后，可以像之前一样，使用 `usermod` 命令修改用户的主组和补充组。

![](img/aceada4eaa93968834901c4c73752391_38.png)

```bash
usermod -g test1 -aG test2,test3 matt
```

执行 `id matt` 或 `groups matt` 可以验证修改是否成功。

![](img/aceada4eaa93968834901c4c73752391_40.png)

### 修改组属性

使用 `groupmod` 命令可以修改组的属性，例如组名或GID。

![](img/aceada4eaa93968834901c4c73752391_42.png)

*   `-n` 选项用于更改组名。
    ```bash
    groupmod -n test4 test3
    ```
    执行后，所有原属于 `test3` 组的用户将自动更新为属于 `test4` 组。

![](img/aceada4eaa93968834901c4c73752391_44.png)

## 配置超级用户访问权限 ⚙️

![](img/aceada4eaa93968834901c4c73752391_46.png)

为了安全地执行需要特权的操作，我们需要为普通用户配置 `sudo` 权限。这在自动化运维（如Ansible）中尤其重要。

`sudo` 的配置通过 `/etc/sudoers` 文件管理。**强烈建议使用 `visudo` 命令来编辑此文件**，因为它会进行语法检查，防止配置错误导致无法使用 `sudo`。

查看当前 `sudoers` 配置：

```bash
visudo -r # 以只读模式打开
# 或
less /etc/sudoers
```

文件中的示例条目展示了配置语法：

*   `root ALL=(ALL) ALL`：允许 `root` 用户在任何主机上以任何用户身份执行任何命令。
*   `%wheel ALL=(ALL) ALL`：允许 `wheel` 组中的所有成员在任何主机上以任何用户身份执行任何命令。
*   `%wheel ALL=(ALL) NOPASSWD: ALL`：允许 `wheel` 组成员无需输入密码即可执行所有命令。

要为特定用户（如 `matt`）授予无密码的 `sudo` 权限，可以添加如下行：

```
matt ALL=(ALL) NOPASSWD: ALL
```

![](img/aceada4eaa93968834901c4c73752391_48.png)

保存退出后，用户 `matt` 就可以使用 `sudo` 执行特权命令了。

![](img/aceada4eaa93968834901c4c73752391_50.png)

![](img/aceada4eaa93968834901c4c73752391_52.png)

```bash
# 切换到 matt 用户
su - matt
# 执行需要特权的命令，无需输入密码
sudo systemctl status httpd
```

同样，也可以为整个组配置 `sudo` 权限。

![](img/aceada4eaa93968834901c4c73752391_54.png)

## 总结

本节课中我们一起学习了RHEL 9中管理用户和组的核心操作。我们掌握了如何使用 `id`、`groups` 等命令以及 `/etc/passwd`、`/etc/shadow`、`/etc/group` 等文件查看信息；学会了使用 `useradd`、`usermod`、`passwd`、`chage` 命令来创建、修改和管理用户账户；也了解了如何使用 `groupadd` 和 `groupmod` 命令管理组。最后，我们重点学习了如何通过安全的方式，使用 `visudo` 编辑 `/etc/sudoers` 文件来配置用户的超级用户访问权限。这些是成为一名合格系统管理员必须掌握的基础技能。