# Redhat红帽 RHCE8.0认证体系课程：P10：管理用户和组 👥

在本节课中，我们将要学习Linux系统中用户和组的管理。Linux是一个多用户、多任务的操作系统，因此理解如何创建、管理和配置用户与组是系统管理员的核心技能。我们将从基本概念入手，逐步学习相关的配置文件、命令和实用技巧。

## 用户分类 🧑‍💻

Linux系统中的用户主要分为三类。

*   **超级管理员 (root)**：这是系统中权限最高的用户，其**UID (User ID)** 为 **0**。root用户可以执行任何操作，包括系统级别的修改。
*   **系统用户**：这类用户通常用于运行系统服务或应用程序（例如 `apache`, `mysql`）。他们的UID范围通常是 **1-999**，并且其登录Shell通常设置为 `/sbin/nologin`，意味着他们不能直接登录系统进行交互。
*   **普通用户**：这是日常使用系统的常规用户。在RHEL 8中，他们的UID从 **1000** 开始分配。在实际生产环境中，系统管理员通常使用普通用户进行日常管理，仅在需要时使用root权限，这有助于提高系统安全性。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_1.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_3.png)

## 用户与组标识符 🔢

Linux系统不是通过用户名，而是通过数字ID来识别用户和组。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_5.png)

*   **UID (User ID)**：用户的唯一数字标识符。例如，root的UID是0。
*   **GID (Group ID)**：组的唯一数字标识符。

一个用户可以属于多个组，但其中一个是**初始组（主要组）**。当用户创建文件时，该文件的属组默认就是用户的初始组。用户还可以加入多个**附加组**，以获得这些组的权限。

## 核心配置文件 📄

用户和组的信息存储在几个特定的系统文件中。了解这些文件的结构至关重要。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_7.png)

### 1. 用户账户信息 (`/etc/passwd`)

此文件存储用户的基本信息，每行代表一个用户，由冒号(`:`)分隔为7个字段。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_9.png)

**文件格式示例：**
```
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
```

![](img/6f8500b76a4c7a8f37f48f871df8a79c_10.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_12.png)

**各字段含义：**
1.  **用户名** (如 `root`)
2.  **密码占位符** (通常为 `x`，真实密码存储在 `/etc/shadow`)
3.  **UID**
4.  **初始组GID**
5.  **描述信息 (GECOS)**
6.  **家目录路径**
7.  **登录Shell** (如 `/bin/bash` 或 `/sbin/nologin`)

**注意**：通常不直接手动编辑此文件，而是使用专门的命令。

### 2. 用户密码信息 (`/etc/shadow`)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_14.png)

此文件存储加密后的用户密码及密码策略信息，每行对应一个用户，由冒号(`:`)分隔为9个字段。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_16.png)

**文件格式示例：**
```
root:$6$...:18659:0:99999:7:::
bin:*:17784:0:99999:7:::
```

![](img/6f8500b76a4c7a8f37f48f871df8a79c_18.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_20.png)

**各字段含义：**
1.  **用户名**
2.  **加密后的密码** (如果为 `*` 或 `!!` 则表示密码被锁定)
3.  **最近一次更改密码的日期** (从1970年1月1日算起的天数)
4.  **密码不可更改的天数** (0表示随时可改)
5.  **密码必须更改的天数** (99999通常表示永不过期)
6.  **密码过期前警告天数**
7.  **密码过期后宽限天数**
8.  **账号失效日期**
9.  **保留字段**

![](img/6f8500b76a4c7a8f37f48f871df8a79c_22.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_23.png)

### 3. 组账户信息 (`/etc/group`)

此文件存储组的基本信息，每行代表一个组，由冒号(`:`)分隔为4个字段。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_25.png)

**文件格式示例：**
```
root:x:0:
student:x:1000:user1,user2
```

![](img/6f8500b76a4c7a8f37f48f871df8a79c_27.png)

**各字段含义：**
1.  **组名**
2.  **组密码占位符** (通常为 `x`，真实密码存储在 `/etc/gshadow`)
3.  **GID**
4.  **组成员列表** (用户名之间用逗号分隔)

### 4. 组密码信息 (`/etc/gshadow`)

此文件存储组的管理员和加密后的组密码，格式与 `/etc/group` 类似，但字段含义不同，日常使用频率较低。

## 用户管理命令 ⚙️

![](img/6f8500b76a4c7a8f37f48f871df8a79c_29.png)

上一节我们介绍了核心的配置文件，本节中我们来看看用于管理用户和组的具体命令。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_31.png)

### 查看用户信息

![](img/6f8500b76a4c7a8f37f48f871df8a79c_33.png)

使用 `id` 命令可以查看当前用户或指定用户的UID、GID及所属组。

```bash
id
id username
```

![](img/6f8500b76a4c7a8f37f48f871df8a79c_35.png)

### 切换用户

![](img/6f8500b76a4c7a8f37f48f871df8a79c_37.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_38.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_40.png)

使用 `su` (switch user) 命令可以切换用户身份。

*   `su - username`：完全切换到目标用户，并加载其环境变量和家目录。
*   `su username`：切换到目标用户，但不改变当前工作目录和环境变量。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_42.png)

**注意**：从root切换到普通用户不需要密码，反之则需要。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_44.png)

### 创建用户

![](img/6f8500b76a4c7a8f37f48f871df8a79c_46.png)

使用 `useradd` 命令创建新用户。默认会创建一个与用户名同名的组作为其初始组，并分配一个大于999的UID。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_48.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_49.png)

```bash
# 创建用户test1
useradd test1

# 创建用户时指定UID、初始组、附加组、Shell和家目录
useradd -u 2000 -g student -G wheel,develop -s /bin/bash -d /home/newhome -c "Test User" student2
```

**常用选项：**
*   `-u UID`：指定用户UID。
*   `-g GID/组名`：指定初始组。
*   `-G 组名1,组名2`：指定附加组。
*   `-s Shell路径`：指定登录Shell。
*   `-d 家目录路径`：指定家目录。
*   `-c "注释"`：添加用户描述。
*   `-r`：创建系统用户 (UID在1-999之间)。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_51.png)

### 修改用户属性

![](img/6f8500b76a4c7a8f37f48f871df8a79c_53.png)

使用 `usermod` 命令修改已存在用户的属性，其选项与 `useradd` 类似。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_55.png)

```bash
# 修改用户test2的UID为2020
usermod -u 2020 test2

# 将用户test2添加到develop组（作为附加组）
usermod -aG develop test2
```
**注意**：使用 `-G` 选项时，配合 `-a` (append) 可以添加附加组而不覆盖原有的附加组列表。

### 删除用户

使用 `userdel` 命令删除用户。

```bash
# 仅删除用户记录，保留其家目录和邮件
userdel test3

![](img/6f8500b76a4c7a8f37f48f871df8a79c_57.png)

# 彻底删除用户，包括其家目录和邮件
userdel -r test4
```

![](img/6f8500b76a4c7a8f37f48f871df8a79c_59.png)

## 组管理命令 👥

### 创建与删除组

![](img/6f8500b76a4c7a8f37f48f871df8a79c_61.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_63.png)

使用 `groupadd` 和 `groupdel` 命令管理组。

```bash
# 创建组
groupadd developers

# 删除组
groupdel developers
```

### 管理组成员

使用 `gpasswd` 命令管理组的成员。

```bash
# 将用户test2添加到student组
gpasswd -a test2 student

# 从student组中移除用户test2
gpasswd -d test2 student
```

## 密码管理 🔐

### 设置与修改密码

使用 `passwd` 命令管理密码。只有root用户可以修改任意用户的密码，普通用户只能修改自己的密码，且需要提供原密码。

```bash
# root用户修改其他用户密码
passwd test2

# 普通用户修改自己的密码
passwd

# 使用管道非交互式设置密码 (仅root可用)
echo "NewPassword123" | passwd --stdin test2
```

### 锁定与解锁账户

![](img/6f8500b76a4c7a8f37f48f871df8a79c_65.png)

通过锁定密码可以禁用用户登录。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_66.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_68.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_69.png)

```bash
# 锁定用户test2的密码
passwd -l test2

![](img/6f8500b76a4c7a8f37f48f871df8a79c_71.png)

# 解锁用户test2的密码
passwd -u test2
```

![](img/6f8500b76a4c7a8f37f48f871df8a79c_73.png)

### 查看与修改密码策略

使用 `chage` 命令可以查看和修改用户的密码过期策略。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_75.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_77.png)

```bash
# 查看用户test2的密码策略
chage -l test2

![](img/6f8500b76a4c7a8f37f48f871df8a79c_79.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_81.png)

# 设置用户test2密码30天后过期，过期前7天发出警告
chage -M 30 -W 7 test2
```

![](img/6f8500b76a4c7a8f37f48f871df8a79c_83.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_85.png)

## 总结 📝

![](img/6f8500b76a4c7a8f37f48f871df8a79c_86.png)

本节课中我们一起学习了Linux用户和组的管理。我们首先了解了三类用户：超级管理员、系统用户和普通用户，以及通过UID/GID进行识别的机制。然后，我们深入分析了四个核心配置文件：`/etc/passwd`、`/etc/shadow`、`/etc/group` 和 `/etc/gshadow` 的结构与含义。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_88.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_89.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_90.png)

![](img/6f8500b76a4c7a8f37f48f871df8a79c_91.png)

接着，我们掌握了管理用户和组的一系列命令：
*   使用 `useradd`、`usermod`、`userdel` 进行用户的增、改、删。
*   使用 `groupadd`、`groupdel`、`gpasswd` 进行组的增、删及成员管理。
*   使用 `passwd` 和 `chage` 进行密码设置和策略管理。
*   使用 `id` 查看身份信息，使用 `su` 切换用户身份。

![](img/6f8500b76a4c7a8f37f48f871df8a79c_93.png)

理解并熟练运用这些知识和命令，是进行系统用户权限管理和维护系统安全的基础。