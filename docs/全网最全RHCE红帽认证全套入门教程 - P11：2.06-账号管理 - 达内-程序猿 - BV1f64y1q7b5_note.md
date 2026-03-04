# RHCE红帽认证全套入门教程：P11：2.06-账号管理 👤

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_0.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_2.png)

在本节课中，我们将要学习Linux系统中的用户账号和组账号管理。这是控制文件访问权限和系统登录的基础，也是RHCE考试中的常见考点。我们将从基本概念入手，学习如何创建、修改、删除用户和组，并理解它们之间的关系。

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_4.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_6.png)

## 用户账号基础

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_8.png)

Linux系统中的用户账号主要用于两个目的：**登录系统**和**控制文件访问权限**。每个用户都有一个唯一的用户名和数字标识（UID）。用户账号的信息存储在系统的特定配置文件中。

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_10.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_12.png)

以下是存储用户账号信息的两个核心文件：
*   `/etc/passwd`：存储用户的基本信息，如用户名、UID、基本组ID（GID）、全名、主目录和登录Shell。
*   `/etc/shadow`：存储用户的加密密码及密码策略信息（如有效期）。出于安全考虑，普通用户无法查看此文件。

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_14.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_16.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_18.png)

例如，查看`/etc/passwd`文件中`root`用户的信息：
```bash
cat /etc/passwd | grep root
```
输出可能类似于：`root:x:0:0:root:/root:/bin/bash`。其中，`x`表示密码已移至`/etc/shadow`文件。

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_20.png)

## 组账号基础

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_22.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_24.png)

组账号用于将具有相同权限需求的用户归类，方便进行批量权限管理。每个用户至少属于一个组，称为**基本组**（或主要组）。用户还可以属于多个**附加组**，以获得额外的权限。

组账号信息同样存储在配置文件中：
*   `/etc/group`：存储组的基本信息。
*   `/etc/gshadow`：存储组的管理信息（如组密码）。

## 用户账号管理命令

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_26.png)

上一节我们介绍了用户和组的基本概念，本节中我们来看看管理它们的具体命令。管理用户账号主要通过以下几个命令实现。

### 添加用户 (`useradd`)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_28.png)

最基础的添加用户命令是 `useradd [用户名]`。例如，添加一个名为`zhangsan`的用户：
```bash
useradd zhangsan
```
这样创建的用户会使用系统默认属性（如UID从1000开始，主目录为`/home/zhangsan`，登录Shell为`/bin/bash`）。

我们可以在创建时指定用户属性。以下是`useradd`命令的常用选项：

*   `-u [UID]`：指定用户的UID。
    ```bash
    useradd -u 666 lisi
    ```
*   `-g [组名]`：指定用户的基本组（不常用）。
*   `-G [组名]`：指定用户的附加组。
    ```bash
    useradd -G root stu02
    ```
*   `-d [目录路径]`：指定用户的主目录。
*   `-s [Shell路径]`：指定用户的登录Shell。若要禁止用户登录，可指定为`/sbin/nologin`。
    ```bash
    useradd -s /sbin/nologin wangwu
    ```

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_30.png)

### 修改用户 (`usermod`)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_32.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_34.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_36.png)

如果创建用户后需要修改其属性，可以使用`usermod`命令。其选项与`useradd`类似。

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_38.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_40.png)

例如，将用户`lisi`的UID修改为888：
```bash
usermod -u 888 lisi
```
将用户`stu01`添加到`tech`组（作为附加组），且不影响其原有的其他附加组，需要使用`-aG`选项（`-a`表示追加）：
```bash
usermod -aG tech stu01
```
如果仅使用`-G tech`，则用户`stu01`将**只属于**`tech`组，原有的其他附加组关系会被清除。

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_42.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_44.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_46.png)

### 设置用户密码 (`passwd`)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_48.png)

为用户设置密码使用`passwd`命令。管理员可以为任何用户设置密码。
```bash
passwd zhangsan
```
根据提示输入两次密码即可。

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_50.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_52.png)

在考试或需要批量操作时，可以使用管道`|`快速设置密码，避免交互式输入：
```bash
echo "iLoveLinux" | passwd --stdin timmy
```
这条命令将密码`iLoveLinux`通过管道传递给`passwd`命令，并设置为用户`timmy`的密码。`--stdin`选项表示从标准输入读取密码。

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_54.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_56.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_58.png)

### 删除用户 (`userdel`)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_60.png)

删除用户使用`userdel`命令。`-r`选项表示同时删除用户的主目录和邮件池。
```bash
userdel -r zhangsan
```
**注意**：在实际工作中，删除用户前务必确认其数据已备份。

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_62.png)

### 查看用户信息 (`id`)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_64.png)

要查看用户的UID、GID以及所属组的信息，使用`id`命令。
```bash
id lisi
```

## 组账号管理命令

管理组账号的命令与用户账号管理类似，但更简单。

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_66.png)

### 添加组 (`groupadd`)
```bash
groupadd tech
```

### 删除组 (`groupdel`)
```bash
groupdel tech
```
注意：如果组是某个用户的基本组，则无法直接删除，需要先修改该用户的基本组或删除该用户。

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_68.png)

### 管理组成员 (`gpasswd`)

除了使用`usermod -aG`，还可以用`gpasswd`命令管理组的成员。

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_70.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_72.png)

将用户`stu02`添加到`tech`组：
```bash
gpasswd -a stu02 tech
```
将用户`stu02`从`tech`组中移除：
```bash
gpasswd -d stu02 tech
```

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_74.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_76.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_78.png)

## 实战练习与总结

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_80.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_82.png)

现在，让我们运用所学知识来完成两个典型的练习题。

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_84.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_86.png)

**题目一：创建指定属性的用户**
创建一个用户`timmy`，其UID为`2020`，密码为`iLoveLinux`。
```bash
useradd -u 2020 timmy
echo "iLoveLinux" | passwd --stdin timmy
```

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_88.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_90.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_92.png)

**题目二：创建组和多个用户**
1.  创建一个名为`admins`的组。
    ```bash
    groupadd admins
    ```
2.  创建用户`zhangsan`和`lisi`，并将他们加入`admins`附加组。
    ```bash
    useradd -G admins zhangsan
    useradd -G admins lisi
    ```
3.  创建用户`wangwu`，并禁止其登录系统（使用`/sbin/nologin` Shell）。
    ```bash
    useradd -s /sbin/nologin wangwu
    ```
4.  为所有用户设置密码（假设密码均为`redhat`）。
    ```bash
    echo "redhat" | passwd --stdin zhangsan
    echo "redhat" | passwd --stdin lisi
    echo "redhat" | passwd --stdin wangwu
    ```

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_94.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_96.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_98.png)

![](img/0c9bbbda9f9a5b1a58afa7dcb4087519_100.png)

本节课中我们一起学习了Linux账号管理的核心知识。我们了解了用户和组的作用，掌握了使用`useradd`、`usermod`、`passwd`、`userdel`管理用户，以及使用`groupadd`、`groupdel`、`gpasswd`管理组的方法。关键是要理解**基本组**和**附加组**的区别，并熟练运用命令选项来精确控制账号属性。这些是进行系统管理和权限配置的基础，务必熟练掌握。