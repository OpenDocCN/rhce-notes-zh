# RHCSA认证精讲：P11：2.06-账号管理 👤

![](img/ca804b9ac6d35246c197010cfa9c4cde_0.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_2.png)

在本节课中，我们将学习Linux系统中用户和组账号管理的基础知识。这是RHCSA考试中的常见考点，掌握这些操作对于系统管理至关重要。

![](img/ca804b9ac6d35246c197010cfa9c4cde_4.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_6.png)

## 用户账号概述

上一节我们介绍了系统的基本操作，本节中我们来看看如何管理用户账号。用户账号在Linux系统中主要有两个作用：**登录系统**和**控制文件访问权限**。系统通过判断文件的所有者来决定用户能对该文件执行何种操作。

![](img/ca804b9ac6d35246c197010cfa9c4cde_8.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_10.png)

用户账号的信息存储在系统的特定配置文件中。

![](img/ca804b9ac6d35246c197010cfa9c4cde_12.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_13.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_15.png)

## 用户账号存储文件

![](img/ca804b9ac6d35246c197010cfa9c4cde_17.png)

Linux系统的用户账号信息主要存储在以下两个文件中：

![](img/ca804b9ac6d35246c197010cfa9c4cde_19.png)

*   `/etc/passwd`：存储用户的基本信息，如用户名、用户ID(UID)、基本组ID(GID)、全名、主目录和登录Shell。
*   `/etc/shadow`：存储用户的加密密码及密码策略信息（如有效期）。此文件普通用户无法读取，增强了安全性。

![](img/ca804b9ac6d35246c197010cfa9c4cde_21.png)

`/etc/passwd`文件中每一行的格式如下：
```
username:password:UID:GID:comment:home_directory:shell
```
例如，root用户的记录通常为：
```
root:x:0:0:root:/root:/bin/bash
```
其中，`x`表示密码已移至`/etc/shadow`文件。

## 组账号简介

除了用户账号，Linux还有组账号。组的主要作用是将具有相同权限需求的用户归类，方便进行批量权限分配。例如，可以将所有开发人员加入“developers”组，然后一次性赋予该组对某个目录的访问权限。

![](img/ca804b9ac6d35246c197010cfa9c4cde_23.png)

组账号的信息存储在以下文件中：
*   `/etc/group`：存储组的基本信息。
*   `/etc/gshadow`：存储组的加密密码（较少使用）。

## 用户账号管理命令

![](img/ca804b9ac6d35246c197010cfa9c4cde_25.png)

以下是管理用户账号的核心命令，我们将学习如何使用它们进行增、删、改、查。

### 添加用户 (`useradd`)

`useradd`命令用于创建新用户。最简单的用法是直接指定用户名：
```bash
useradd zhangsan
```
此命令会使用系统默认设置创建用户“zhangsan”，包括：
*   从1000开始分配UID（红帽8及以上系统）。
*   创建一个与用户同名的私有组作为其**基本组**。
*   在`/home`目录下创建同名文件夹作为其**主目录**。
*   设置登录Shell为`/bin/bash`。

![](img/ca804b9ac6d35246c197010cfa9c4cde_27.png)

### 设置用户密码 (`passwd`)

![](img/ca804b9ac6d35246c197010cfa9c4cde_29.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_31.png)

新创建的用户没有密码，需要使用`passwd`命令设置：
```bash
passwd zhangsan
```
执行后，系统会提示输入两次新密码。管理员可以为任何用户设置密码，而普通用户只能修改自己的密码。

![](img/ca804b9ac6d35246c197010cfa9c4cde_33.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_35.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_36.png)

**快速设置密码技巧**：考试中可能需要为多个用户设置密码，使用管道命令可以提高效率：
```bash
echo "password123" | passwd --stdin zhangsan
```
这条命令将“password123”作为密码直接设置给用户“zhangsan”，无需交互式输入。

![](img/ca804b9ac6d35246c197010cfa9c4cde_38.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_40.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_42.png)

### 修改用户属性 (`usermod`)

如果创建用户时未指定某些属性，或需要修改，可以使用`usermod`命令。

![](img/ca804b9ac6d35246c197010cfa9c4cde_44.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_46.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_48.png)

以下是`useradd`和`usermod`命令的常用选项：

![](img/ca804b9ac6d35246c197010cfa9c4cde_50.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_52.png)

| 选项 | 说明 | 示例 |
| :--- | :--- | :--- |
| `-u UID` | 指定用户ID。 | `useradd -u 2020 lisi` |
| `-g GID/组名` | 指定用户的基本组。 | `usermod -g developers zhangsan` |
| `-G 组名` | 指定用户的**附加组**。 | `useradd -G admins wangwu` |
| `-d 目录` | 指定用户的主目录。 | `useradd -d /data/zhangsan zhangsan` |
| `-s Shell路径` | 指定用户的登录Shell。 | `useradd -s /sbin/nologin serviceuser` |
| `-aG 组名` | (与`usermod`合用) 将用户追加到附加组，不影响原有附加组。 | `usermod -aG sudo zhangsan` |

![](img/ca804b9ac6d35246c197010cfa9c4cde_54.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_56.png)

**重要概念：基本组与附加组**
*   **基本组**：用户创建时自动生成或指定的主要组，每个用户必须有且只有一个基本组。
*   **附加组**：用户额外所属的组，一个用户可以属于零个或多个附加组。用于灵活分配权限。

### 删除用户 (`userdel`)

![](img/ca804b9ac6d35246c197010cfa9c4cde_58.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_60.png)

要删除用户，使用`userdel`命令。`-r`选项可以同时删除用户的主目录和邮件池。
```bash
userdel -r zhangsan
```
**注意**：生产环境中执行删除前务必确认数据已备份。

### 查看用户信息 (`id`)

`id`命令可以查看用户的UID、GID以及所属的组列表。
```bash
id zhangsan
```

## 组账号管理命令

![](img/ca804b9ac6d35246c197010cfa9c4cde_62.png)

组的管理相对简单，常用命令如下：

*   **创建组**：`groupadd groupname`
    ```bash
    groupadd admins
    ```
*   **删除组**：`groupdel groupname`
*   **将用户加入附加组**：除了在`useradd`或`usermod`中使用`-G`选项，还可以用`gpasswd`。
    ```bash
    gpasswd -a zhangsan admins  # 将zhangsan加入admins组
    ```
*   **查看组成员**：`getent group groupname`

![](img/ca804b9ac6d35246c197010cfa9c4cde_64.png)

## 实战练习与考试要点

现在，让我们运用所学知识来解决RHCSA考试中可能出现的题目。

![](img/ca804b9ac6d35246c197010cfa9c4cde_66.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_68.png)

**题目1：创建特定UID的用户**
> 创建一个用户名为`teddy`，用户ID为`2020`，密码为`i love linux`的用户。

![](img/ca804b9ac6d35246c197010cfa9c4cde_70.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_72.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_74.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_76.png)

**解答步骤：**
1.  创建用户并指定UID：
    ```bash
    useradd -u 2020 teddy
    ```
2.  使用快速方法设置密码：
    ```bash
    echo "i love linux" | passwd --stdin teddy
    ```

![](img/ca804b9ac6d35246c197010cfa9c4cde_78.png)

**题目2：综合管理用户和组**
> 1. 创建一个名为`admins`的组。
> 2. 创建用户`zhangsan`和`lisi`，他们的附加组均为`admins`。
> 3. 创建用户`wangwu`，其登录Shell为`/sbin/nologin`（不可交互登录），且不属于`admins`组。
> 4. 为所有用户设置密码`redhat`。

![](img/ca804b9ac6d35246c197010cfa9c4cde_80.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_82.png)

**解答步骤：**
1.  创建组：
    ```bash
    groupadd admins
    ```
2.  创建用户并指定附加组：
    ```bash
    useradd -G admins zhangsan
    useradd -G admins lisi
    ```
3.  创建不可登录用户：
    ```bash
    useradd -s /sbin/nologin wangwu
    ```
4.  批量设置密码：
    ```bash
    echo "redhat" | passwd --stdin zhangsan
    echo "redhat" | passwd --stdin lisi
    echo "redhat" | passwd --stdin wangwu
    ```
    *(在终端中，可使用上方向键调出历史命令快速修改用户名，提高效率)*

![](img/ca804b9ac6d35246c197010cfa9c4cde_84.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_85.png)

## 总结

![](img/ca804b9ac6d35246c197010cfa9c4cde_87.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_88.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_90.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_91.png)

![](img/ca804b9ac6d35246c197010cfa9c4cde_93.png)

本节课中我们一起学习了Linux账号管理的核心内容。我们了解了用户和组账号的作用，掌握了用户账号的存储文件(`/etc/passwd`, `/etc/shadow`)。重点练习了使用`useradd`、`usermod`、`passwd`、`userdel`和`id`命令对用户进行增、删、改、查、设密等操作，以及使用`groupadd`和`gpasswd`管理组账号。特别需要理解**基本组**与**附加组**的区别。通过两道经典考题的演练，希望大家能够熟练掌握这些在RHCSA考试和实际工作中都非常重要的技能。