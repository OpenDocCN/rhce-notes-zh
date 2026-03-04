# Linux账号管理：2.06：用户与组管理基础

![](img/24ce9fa6bbef83332f387078fefe602c_0.png)

![](img/24ce9fa6bbef83332f387078fefe602c_2.png)

在本节课中，我们将要学习Linux系统中用户账号和组账号管理的基础知识。理解如何创建、修改、删除用户和组，以及如何设置密码，是进行系统管理和权限控制的第一步。

![](img/24ce9fa6bbef83332f387078fefe602c_4.png)

## 用户账号概述

![](img/24ce9fa6bbef83332f387078fefe602c_6.png)

上一节我们介绍了Linux的基本操作，本节中我们来看看账号管理。用户账号是Linux系统用于识别用户身份、控制登录和文件访问权限的核心机制。每个用户都有一个唯一的用户名和数字标识符（UID）。

![](img/24ce9fa6bbef83332f387078fefe602c_8.png)

用户账号信息主要存储在以下两个系统文件中：
*   `/etc/passwd`：存储用户的基本信息，如用户名、UID、主目录和登录Shell。
*   `/etc/shadow`：存储用户的加密密码及其他安全相关信息（如密码有效期），普通用户无法查看。

![](img/24ce9fa6bbef83332f387078fefe602c_10.png)

![](img/24ce9fa6bbef83332f387078fefe602c_12.png)

![](img/24ce9fa6bbef83332f387078fefe602c_13.png)

## 用户账号管理操作

![](img/24ce9fa6bbef83332f387078fefe602c_15.png)

![](img/24ce9fa6bbef83332f387078fefe602c_16.png)

以下是用户账号的增、删、改、查等基本操作命令。

![](img/24ce9fa6bbef83332f387078fefe602c_18.png)

![](img/24ce9fa6bbef83332f387078fefe602c_20.png)

### 添加用户 (`useradd`)

使用 `useradd` 命令可以创建新用户。最简单的用法是直接跟上用户名。

![](img/24ce9fa6bbef83332f387078fefe602c_22.png)

![](img/24ce9fa6bbef83332f387078fefe602c_24.png)

```bash
useradd zhangsan
```

此命令会使用系统默认设置创建用户 `zhangsan`，包括：
*   在 `/home` 目录下创建同名主目录。
*   分配一个从1000开始的UID（普通用户）。
*   创建一个与用户同名的私有组作为其基本组。
*   设置登录Shell为 `/bin/bash`。

### 设置用户密码 (`passwd`)

新创建的用户没有密码，无法登录。使用 `passwd` 命令为用户设置密码。

![](img/24ce9fa6bbef83332f387078fefe602c_26.png)

```bash
passwd zhangsan
```

执行后，系统会提示输入两次新密码。如果是管理员为其他用户改密码，则无需验证旧密码。

![](img/24ce9fa6bbef83332f387078fefe602c_28.png)

为了在脚本或考试中快速设置密码，可以使用管道配合 `echo` 命令：

```bash
echo "123456" | passwd --stdin zhangsan
```

这条命令将密码“123456”通过管道传递给 `passwd` 命令，并立即为 `zhangsan` 用户设置。

![](img/24ce9fa6bbef83332f387078fefe602c_30.png)

### 修改用户属性 (`usermod`)

如果创建用户时未指定某些属性，或后续需要修改，可以使用 `usermod` 命令。

![](img/24ce9fa6bbef83332f387078fefe602c_32.png)

![](img/24ce9fa6bbef83332f387078fefe602c_34.png)

![](img/24ce9fa6bbef83332f387078fefe602c_36.png)

例如，修改用户 `lisi` 的UID为888：

![](img/24ce9fa6bbef83332f387078fefe602c_38.png)

![](img/24ce9fa6bbef83332f387078fefe602c_39.png)

```bash
usermod -u 888 lisi
```

![](img/24ce9fa6bbef83332f387078fefe602c_41.png)

![](img/24ce9fa6bbef83332f387078fefe602c_43.png)

### 删除用户 (`userdel`)

![](img/24ce9fa6bbef83332f387078fefe602c_45.png)

使用 `userdel` 命令可以删除用户账号。添加 `-r` 选项可以同时删除该用户的主目录和邮件池。

![](img/24ce9fa6bbef83332f387078fefe602c_47.png)

![](img/24ce9fa6bbef83332f387078fefe602c_49.png)

```bash
userdel -r zhangsan
```

![](img/24ce9fa6bbef83332f387078fefe602c_51.png)

![](img/24ce9fa6bbef83332f387078fefe602c_53.png)

**注意**：在实际工作中，删除用户前请确认其数据已备份。

![](img/24ce9fa6bbef83332f387078fefe602c_55.png)

![](img/24ce9fa6bbef83332f387078fefe602c_57.png)

### 查看用户信息 (`id`)

![](img/24ce9fa6bbef83332f387078fefe602c_59.png)

使用 `id` 命令可以查看用户的UID、GID以及所属的组。

```bash
id zhangsan
```

![](img/24ce9fa6bbef83332f387078fefe602c_61.png)

![](img/24ce9fa6bbef83332f387078fefe602c_63.png)

## 用户账号的进阶属性

在创建用户时，可以通过选项指定其属性，避免使用默认值。

以下是 `useradd` 命令的一些常用选项：
*   `-u UID`：指定用户的UID。
    ```bash
    useradd -u 666 lisi
    ```
*   `-d 目录`：指定用户的主目录。
*   `-g 组名`：指定用户的基本组（初始登录组）。
*   `-G 组名`：指定用户的附加组（可以有多个，用逗号分隔）。
*   `-s Shell路径`：指定用户的登录Shell。例如，设置为 `/sbin/nologin` 可以禁止该用户登录系统。
    ```bash
    useradd -s /sbin/nologin wangwu
    ```

## 组账号管理

![](img/24ce9fa6bbef83332f387078fefe602c_65.png)

组账号用于将多个用户归类，方便进行统一的权限分配。每个组也有一个唯一的组名和数字标识符（GID）。

### 组的基本概念

*   **基本组（私有组）**：创建用户时自动生成的、与用户同名的组。每个用户必须有且仅有一个基本组。
*   **附加组**：用户除了基本组外，还可以属于其他组，这些组称为附加组。一个用户可以属于多个附加组。

![](img/24ce9fa6bbef83332f387078fefe602c_67.png)

### 组管理操作

以下是组账号的增、删、改命令。

![](img/24ce9fa6bbef83332f387078fefe602c_69.png)

![](img/24ce9fa6bbef83332f387078fefe602c_71.png)

*   **创建组**：使用 `groupadd` 命令。
    ```bash
    groupadd admins
    ```
*   **删除组**：使用 `groupdel` 命令。
    ```bash
    groupdel admins
    ```
*   **管理组成员**：
    *   在创建用户时直接指定附加组：
        ```bash
        useradd -G admins zhangsan
        ```
    *   使用 `usermod` 为已存在用户添加附加组（`-aG` 选项表示追加，不影响原有附加组）：
        ```bash
        usermod -aG admins lisi
        ```
    *   使用 `gpasswd` 命令管理组成员：
        ```bash
        gpasswd -a zhangsan admins  # 添加用户到组
        gpasswd -d zhangsan admins  # 从组中移除用户
        ```

![](img/24ce9fa6bbef83332f387078fefe602c_73.png)

![](img/24ce9fa6bbef83332f387078fefe602c_75.png)

![](img/24ce9fa6bbef83332f387078fefe602c_77.png)

## 实战练习

![](img/24ce9fa6bbef83332f387078fefe602c_79.png)

现在，让我们应用所学知识来完成两个典型的账号管理任务。

![](img/24ce9fa6bbef83332f387078fefe602c_81.png)

![](img/24ce9fa6bbef83332f387078fefe602c_83.png)

**任务一：创建特定UID的用户并设置密码**
1.  创建用户 `teddy`，并指定其UID为2020。
    ```bash
    useradd -u 2020 teddy
    ```
2.  为 `teddy` 用户设置密码为 `i love linux`。
    ```bash
    echo "i love linux" | passwd --stdin teddy
    ```

![](img/24ce9fa6bbef83332f387078fefe602c_85.png)

**任务二：创建组和多个用户，并分配属性**
1.  创建一个名为 `admins` 的组。
    ```bash
    groupadd admins
    ```
2.  创建用户 `zhangsan` 和 `lisi`，并将他们加入 `admins` 附加组。
    ```bash
    useradd -G admins zhangsan
    useradd -G admins lisi
    ```
3.  创建用户 `wangwu`，禁止其登录系统，且不属于 `admins` 组。
    ```bash
    useradd -s /sbin/nologin wangwu
    ```
4.  为这三个用户设置密码（假设密码均为 `redhat`）。
    ```bash
    echo "redhat" | passwd --stdin zhangsan
    echo "redhat" | passwd --stdin lisi
    echo "redhat" | passwd --stdin wangwu
    ```

![](img/24ce9fa6bbef83332f387078fefe602c_87.png)

![](img/24ce9fa6bbef83332f387078fefe602c_88.png)

![](img/24ce9fa6bbef83332f387078fefe602c_90.png)

## 总结

![](img/24ce9fa6bbef83332f387078fefe602c_91.png)

![](img/24ce9fa6bbef83332f387078fefe602c_93.png)

![](img/24ce9fa6bbef83332f387078fefe602c_94.png)

![](img/24ce9fa6bbef83332f387078fefe602c_96.png)

本节课中我们一起学习了Linux账号管理的基础。我们了解了用户和组的概念，掌握了使用 `useradd`、`usermod`、`userdel`、`passwd`、`groupadd` 等命令进行账号的创建、修改、删除和密码设置。关键点在于理解基本组与附加组的区别，以及如何通过UID、GID、登录Shell等属性精细控制用户账号。这些技能是后续学习文件权限管理和系统安全配置的重要基石。