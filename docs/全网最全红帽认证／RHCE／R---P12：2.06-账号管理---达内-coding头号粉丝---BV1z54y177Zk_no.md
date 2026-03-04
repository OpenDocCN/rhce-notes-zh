# Linux用户与组管理：2.06：账号管理

![](img/d2dccc4c778f5274f06486d59ab343e5_0.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_2.png)

在本节课中，我们将要学习Linux系统中用户账号和组账号的基本管理操作。这是Linux系统管理的基础，也是RHCSA/RHCE认证考试中的重要考点。我们将从账号的基本概念讲起，逐步学习如何创建、修改、删除用户和组，并理解它们之间的关系。

![](img/d2dccc4c778f5274f06486d59ab343e5_4.png)

## 用户账号概述

![](img/d2dccc4c778f5274f06486d59ab343e5_6.png)

上一节我们介绍了Linux系统的基本操作，本节中我们来看看如何管理系统的使用者——用户账号。

![](img/d2dccc4c778f5274f06486d59ab343e5_8.png)

Linux系统中的用户账号主要用于两个核心功能：**登录系统**和**控制文件访问权限**。每个用户都拥有唯一的用户名和密码，用于身份验证。用户对文件能执行的操作（读、写、执行）取决于文件的归属和权限设置。

![](img/d2dccc4c778f5274f06486d59ab343e5_10.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_11.png)

用户账号的信息主要存储在系统的两个关键文件中：
*   `/etc/passwd`：存储用户的基本信息，如用户名、用户ID(UID)、主组ID(GID)、全名、主目录和登录Shell。
*   `/etc/shadow`：存储用户的加密密码及密码策略信息（如有效期）。出于安全考虑，普通用户无法查看此文件。

![](img/d2dccc4c778f5274f06486d59ab343e5_13.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_14.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_16.png)

## 用户账号管理

![](img/d2dccc4c778f5274f06486d59ab343e5_18.png)

了解了用户账号的基础后，本节中我们来看看如何对用户账号进行增、删、改、查等操作。

![](img/d2dccc4c778f5274f06486d59ab343e5_20.png)

### 添加用户账号

![](img/d2dccc4c778f5274f06486d59ab343e5_22.png)

最基本的添加用户命令是 `useradd`，后跟用户名。

```bash
useradd zhangsan
```

此命令会使用系统默认值创建用户 `zhangsan`，例如：
*   UID从1000开始自动分配（1000以下通常为系统账号）。
*   创建一个与用户名同名的**基本组（私有组）**。
*   主目录默认为 `/home/zhangsan`。
*   登录Shell默认为 `/bin/bash`。

创建用户后，需要使用 `passwd` 命令为其设置密码，用户才能登录。

![](img/d2dccc4c778f5274f06486d59ab343e5_24.png)

```bash
passwd zhangsan
```

### 自定义用户属性

![](img/d2dccc4c778f5274f06486d59ab343e5_26.png)

在创建用户时，我们可以通过选项来自定义其属性，一步到位。

以下是创建用户时可用的常用选项：
*   `-u UID`：指定用户的数字ID。
    ```bash
    useradd -u 666 lisi
    ```
*   `-g GID或组名`：指定用户的基本组（主要组）。
*   `-G 组名1,组名2,...`：指定用户的**附加组**。
    ```bash
    useradd -G admins,developers wangwu
    ```
*   `-d /path/to/home`：指定用户的主目录路径。
*   `-s /path/to/shell`：指定用户的登录Shell。例如，使用 `/sbin/nologin` 可以禁止用户登录。
    ```bash
    useradd -s /sbin/nologin serviceuser
    ```

### 修改用户账号

![](img/d2dccc4c778f5274f06486d59ab343e5_28.png)

如果创建用户时未指定某些属性，或后续需要修改，可以使用 `usermod` 命令。

![](img/d2dccc4c778f5274f06486d59ab343e5_30.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_32.png)

`usermod` 的选项与 `useradd` 类似，用于修改现有用户的属性。
*   修改用户UID：
    ```bash
    usermod -u 888 lisi
    ```
*   **将用户追加到附加组（不影响原有附加组）**：
    ```bash
    usermod -aG newgroup zhangsan
    ```
    **注意**：使用 `-G` 而不加 `-a` 选项，会使用指定的组**替换**用户当前所有的附加组。

![](img/d2dccc4c778f5274f06486d59ab343e5_34.png)

### 设置用户密码（快速方法）

![](img/d2dccc4c778f5274f06486d59ab343e5_36.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_37.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_39.png)

考试或批量操作时，使用交互式 `passwd` 命令效率较低。我们可以使用管道和 `echo` 命令快速设置密码。

![](img/d2dccc4c778f5274f06486d59ab343e5_41.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_43.png)

```bash
echo "mypassword" | passwd --stdin username
```
例如，为用户 `zhangsan` 设置密码 `123456`：
```bash
echo "123456" | passwd --stdin zhangsan
```

![](img/d2dccc4c778f5274f06486d59ab343e5_45.png)

### 查看与删除用户账号

![](img/d2dccc4c778f5274f06486d59ab343e5_47.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_49.png)

*   **查看用户属性**：使用 `id` 命令可以查看用户的UID、GID以及所属的组。
    ```bash
    id zhangsan
    ```
*   **删除用户账号**：使用 `userdel` 命令。`-r` 选项会同时删除用户的主目录和邮件池。
    ```bash
    userdel -r zhangsan
    ```
    **警告**：生产环境中删除用户前，请务必确认其数据已备份。

![](img/d2dccc4c778f5274f06486d59ab343e5_51.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_53.png)

## 组账号管理

![](img/d2dccc4c778f5274f06486d59ab343e5_55.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_57.png)

用户可以被分组以便于权限管理。本节中我们来看看组的管理。

组是用户的集合，主要用于简化权限分配。当需要对多个用户授予相同的文件访问权限时，将这些用户加入同一个组，然后对组授权即可。

![](img/d2dccc4c778f5274f06486d59ab343e5_59.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_61.png)

### 管理组账号

组的管理命令与用户管理类似，但更简单。
*   **创建组**：
    ```bash
    groupadd developers
    ```
*   **删除组**：
    ```bash
    groupdel developers
    ```
*   **管理组成员**：
    *   除了在创建/修改用户时使用 `-G` 选项，还可以使用 `gpasswd` 命令专门管理组成员。
    *   将用户添加到组：
        ```bash
        gpasswd -a zhangsan developers
        ```
    *   将用户从组中移除：
        ```bash
        gpasswd -d zhangsan developers
        ```
*   **查看用户所属组**：
    ```bash
    groups zhangsan
    ```

## 核心概念：基本组与附加组

理解基本组和附加组的区别对于权限管理至关重要。

![](img/d2dccc4c778f5274f06486d59ab343e5_63.png)

*   **基本组（Primary Group）**：
    *   每个用户必须且只能属于一个基本组。
    *   创建用户时，若未指定 (`-g`)，系统会创建一个与用户名同名的新组作为其基本组。
    *   用户新建的文件，其所属组默认就是该用户的基本组。
*   **附加组（Supplementary Groups）**：
    *   用户除了基本组外，还可以属于零个或多个附加组。
    *   附加组主要用于让用户获取额外的文件访问权限。
    *   通过 `-G` 选项或 `usermod -aG`、`gpasswd -a` 命令管理。

使用 `id` 命令查看用户 `zhangsan` 的信息：
```bash
uid=1001(zhangsan) gid=1001(zhangsan) groups=1001(zhangsan),1002(admins)
```
其中，`gid=1001(zhangsan)` 是其基本组，`groups=...1002(admins)` 是其附加组。

## 实战演练与考试要点

![](img/d2dccc4c778f5274f06486d59ab343e5_65.png)

现在，让我们运用所学知识，解决类似RHCSA考试中的典型题目。

**题目1：创建指定属性的用户**
> 创建一个用户名为 `teddy`，UID为 `2020`，密码为 `i love linux` 的用户。

![](img/d2dccc4c778f5274f06486d59ab343e5_67.png)

**操作步骤：**
1.  创建用户并指定UID：
    ```bash
    useradd -u 2020 teddy
    ```
2.  使用快速方法设置密码：
    ```bash
    echo "i love linux" | passwd --stdin teddy
    ```

![](img/d2dccc4c778f5274f06486d59ab343e5_69.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_71.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_73.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_75.png)

**题目2：综合管理用户和组**
> 1. 创建一个名为 `admins` 的组。
> 2. 创建用户 `zhangsan` 和 `lisi`，他们的附加组均为 `admins`。
> 3. 创建用户 `wangwu`，其登录Shell为 `/sbin/nologin`，且不属于 `admins` 组。
> 4. 为所有用户设置密码 `redhat`。

![](img/d2dccc4c778f5274f06486d59ab343e5_77.png)

**操作步骤：**
1.  创建组：
    ```bash
    groupadd admins
    ```
2.  创建用户并指定附加组：
    ```bash
    useradd -G admins zhangsan
    useradd -G admins lisi
    ```
3.  创建无法登录的用户：
    ```bash
    useradd -s /sbin/nologin wangwu
    ```
4.  批量设置密码：
    ```bash
    echo "redhat" | passwd --stdin zhangsan
    echo "redhat" | passwd --stdin lisi
    echo "redhat" | passwd --stdin wangwu
    ```

![](img/d2dccc4c778f5274f06486d59ab343e5_79.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_81.png)

## 总结

![](img/d2dccc4c778f5274f06486d59ab343e5_83.png)

本节课中我们一起学习了Linux系统账号管理的核心内容。

![](img/d2dccc4c778f5274f06486d59ab343e5_85.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_86.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_88.png)

我们首先了解了用户账号的作用及其信息存储文件（`/etc/passwd` 和 `/etc/shadow`）。然后，系统学习了用户账号的**增** (`useradd`)、**删** (`userdel -r`)、**改** (`usermod`)、**查** (`id`) 以及**密码设置** (`passwd`, `echo ... | passwd --stdin`) 操作。接着，我们学习了更简单的组账号管理（`groupadd`, `groupdel`, `gpasswd`）。最后，我们厘清了**基本组**与**附加组**这一关键概念，并通过实战题目巩固了所有知识点。

![](img/d2dccc4c778f5274f06486d59ab343e5_89.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_91.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_92.png)

![](img/d2dccc4c778f5274f06486d59ab343e5_94.png)

掌握这些命令和概念，是进行系统用户权限配置的基础，请大家务必熟练操作。