# Linux最全RHCSA+RHCE培训教程合集，小白入门必备！ - P19：红帽RHCSA-19.特殊权限SUID、SGID、SBIT、ACL

![](img/6b57f34d390c1dd08040262ded986cec_1.png)

![](img/6b57f34d390c1dd08040262ded986cec_3.png)

![](img/6b57f34d390c1dd08040262ded986cec_5.png)

![](img/6b57f34d390c1dd08040262ded986cec_7.png)

![](img/6b57f34d390c1dd08040262ded986cec_9.png)

在本节课中，我们将要学习Linux系统中的四种特殊权限：SUID、SGID、SBIT和ACL。这些权限扩展了基本权限（rwx）的功能，用于满足更精细和特殊的安全管理需求。我们将逐一了解它们的作用、应用场景和设置方法。

## 概述：特殊权限简介

上一节我们介绍了Linux的基本权限管理。本节中，我们来看看几种特殊的权限机制。基本权限（rwx）虽然常用，但在某些特定场景下功能有限。特殊权限（SUID、SGID、SBIT、ACL）提供了更强大的控制能力，例如允许程序临时提权、限制目录内文件删除、为特定用户定制权限等。理解这些权限对于系统安全和管理至关重要。

![](img/6b57f34d390c1dd08040262ded986cec_11.png)

---

![](img/6b57f34d390c1dd08040262ded986cec_13.png)

![](img/6b57f34d390c1dd08040262ded986cec_15.png)

## SUID（Set User ID）权限

![](img/6b57f34d390c1dd08040262ded986cec_17.png)

![](img/6b57f34d390c1dd08040262ded986cec_19.png)

![](img/6b57f34d390c1dd08040262ded986cec_21.png)

![](img/6b57f34d390c1dd08040262ded986cec_23.png)

SUID权限主要针对可执行文件。当一个可执行文件被设置了SUID权限后，任何普通用户在执行该文件时，会临时拥有该文件**所有者**的身份（通常是root）。该权限仅在程序执行过程中有效，程序执行完毕后用户恢复原有身份。

### SUID的作用与示例

为什么需要SUID？我们以修改密码的命令`/usr/bin/passwd`为例。普通用户可以使用`passwd`命令修改自己的密码，但最终密码是保存在`/etc/shadow`文件中的。查看`/etc/shadow`的权限：

```bash
ls -l /etc/shadow
```
输出类似：`-r--------. 1 root root ...`，表示普通用户对此文件**没有任何写权限**。

那么普通用户如何修改其中的密码字段呢？奥秘就在于`/usr/bin/passwd`这个命令文件本身。查看它的权限：

```bash
ls -l /usr/bin/passwd
```
你会发现输出中所有者的执行位是 `s`（例如 `-r**s**r-xr-x. 1 root root ...`），而不是普通的 `x`。这个 `s` 就是SUID权限的标志。

当普通用户执行`passwd`时，由于SUID的存在，该进程会临时获得文件所有者（root）的身份，从而拥有了修改`/etc/shadow`文件的权限。执行结束后，用户身份恢复原状。

### 设置与取消SUID

设置SUID的命令是 `chmod`，使用 `u+s` 参数：
```bash
chmod u+s /path/to/file
```
取消SUID权限使用 `u-s` 参数：
```bash
chmod u-s /path/to/file
```

**注意**：SUID权限应谨慎设置。如果给像`vim`这样的编辑器设置SUID，意味着任何用户使用`vim`时都拥有root权限，可以随意修改系统文件，这将带来极大的安全风险。

---

## SGID（Set Group ID）权限

![](img/6b57f34d390c1dd08040262ded986cec_25.png)

上一节我们介绍了SUID是针对文件所有者的。本节中我们来看看针对组的特殊权限——SGID。SGID同样主要针对可执行文件。设置了SGID的文件，任何用户在执行时，会临时拥有该文件**所属组**的身份。

![](img/6b57f34d390c1dd08040262ded986cec_27.png)

### SGID的作用与示例

SGID的典型例子是`locate`命令（对应的文件通常是`/usr/bin/locate`）。`locate`命令需要读取数据库文件`/var/lib/mlocate/mlocate.db`来快速搜索文件。查看该数据库文件的权限：

```bash
ls -l /var/lib/mlocate/mlocate.db
```
输出可能为 `-rw-r-----. 1 root slocate ...`，表示只有`root`用户和`slocate`组的成员有读权限。

![](img/6b57f34d390c1dd08040262ded986cec_29.png)

普通用户不属于`slocate`组，按理无法读取此文件。但为什么能使用`locate`命令呢？查看`/usr/bin/locate`的权限：

```bash
ls -l /usr/bin/locate
```
你会发现所属组的执行位是 `s`（例如 `-rwxr-**s**r-x. 1 root slocate ...`）。这个 `s` 就是SGID权限的标志。

![](img/6b57f34d390c1dd08040262ded986cec_31.png)

当普通用户执行`locate`时，由于SGID的存在，该进程会临时获得文件所属组（slocate）的身份，从而继承了该组对数据库文件的读权限。

### 设置与取消SGID

![](img/6b57f34d390c1dd08040262ded986cec_33.png)

![](img/6b57f34d390c1dd08040262ded986cec_35.png)

![](img/6b57f34d390c1dd08040262ded986cec_37.png)

设置SGID的命令是 `chmod`，使用 `g+s` 参数：
```bash
chmod g+s /path/to/file
```
取消SGID权限使用 `g-s` 参数：
```bash
chmod g-s /path/to/file
```
与SUID类似，SGID权限也需谨慎使用，系统自身设置好的通常无需改动。

![](img/6b57f34d390c1dd08040262ded986cec_39.png)

![](img/6b57f34d390c1dd08040262ded986cec_41.png)

![](img/6b57f34d390c1dd08040262ded986cec_43.png)

![](img/6b57f34d390c1dd08040262ded986cec_45.png)

---

## SBIT（Sticky Bit）权限

前面两种权限主要针对可执行文件。本节中我们来看看一种针对目录的特殊权限——SBIT（粘滞位）。SBIT权限用于解决一个共享目录下的安全问题。

![](img/6b57f34d390c1dd08040262ded986cec_47.png)

![](img/6b57f34d390c1dd08040262ded986cec_49.png)

### SBIT的作用与示例

当一个目录权限为777（即所有用户都有完整的rwx权限）时，任何用户都可以删除该目录下的任何文件，包括其他用户创建的文件。这显然在共享目录（如`/tmp`）中是不合理的。

![](img/6b57f34d390c1dd08040262ded986cec_51.png)

SBIT权限就是为了解决这个问题。**对一个目录设置SBIT权限后，该目录下的文件，只有文件的所有者、目录的所有者或root用户才能删除或重命名，其他用户即使有写权限也不行。**

![](img/6b57f34d390c1dd08040262ded986cec_53.png)

系统临时目录`/tmp`就是一个典型的例子。查看其权限：
```bash
ls -ld /tmp
```
输出为 `drwxrwxrwt. ...`，注意其他用户的执行位是 `t`。这个 `t` 就是SBIT权限的标志。

![](img/6b57f34d390c1dd08040262ded986cec_55.png)

在`/tmp`目录中，用户A可以创建和删除自己的文件，但无法删除用户B创建的文件。

![](img/6b57f34d390c1dd08040262ded986cec_57.png)

### 设置与取消SBIT

设置SBIT权限的命令是 `chmod`，使用 `o+t` 参数：
```bash
chmod o+t /path/to/directory
```
取消SBIT权限使用 `o-t` 参数：
```bash
chmod o-t /path/to/directory
```
SBIT权限在需要创建共享、但又要防止用户相互干扰的目录时非常有用。

![](img/6b57f34d390c1dd08040262ded986cec_59.png)

![](img/6b57f34d390c1dd08040262ded986cec_61.png)

---

![](img/6b57f34d390c1dd08040262ded986cec_63.png)

## ACL（Access Control List）访问控制列表

![](img/6b57f34d390c1dd08040262ded986cec_65.png)

基本权限和上述特殊权限提供了“所有者-所属组-其他人”的三元组控制模型。但有时我们需要更精细的控制，例如为某个特定用户单独赋予某个目录的读权限，而不影响其他用户。这时就需要用到ACL。

### ACL的作用与概念

![](img/6b57f34d390c1dd08040262ded986cec_67.png)

![](img/6b57f34d390c1dd08040262ded986cec_69.png)

![](img/6b57f34d390c1dd08040262ded986cec_71.png)

ACL（文件系统访问控制列表）可以突破传统三元组权限的限制，为任意用户或用户组单独设置对文件/目录的访问权限。

![](img/6b57f34d390c1dd08040262ded986cec_73.png)

假设有一个目录`/ops`，属于`ops`组。你希望：
1.  `ops`组成员拥有读写执行权限（rwx）。
2.  其他用户没有任何权限。
3.  但实习生`xiaowei`需要能进入目录并查看文件（r-x），但不能修改或删除。

使用基本权限无法满足第3点要求。因为`xiaowei`属于“其他人”，一旦赋予权限，所有其他用户都会获得相同权限。这时就可以使用ACL为`xiaowei`单独授权。

### ACL的设置与查看

首先，确保文件系统已挂载支持ACL（现代Linux发行版默认支持）。

**设置ACL权限**：使用 `setfacl` 命令。
*   `-m` 选项表示修改ACL。
*   `u:` 表示用户，`g:` 表示组。
*   格式：`setfacl -m u:用户名:权限 文件/目录`

为实习生`xiaowei`对`/ops`目录添加读和执行权限：
```bash
setfacl -m u:xiaowei:rx /ops
```

![](img/6b57f34d390c1dd08040262ded986cec_75.png)

![](img/6b57f34d390c1dd08040262ded986cec_77.png)

![](img/6b57f34d390c1dd08040262ded986cec_79.png)

**查看ACL权限**：使用 `getfacl` 命令。
```bash
getfacl /ops
```
输出中，除了传统的权限信息，你还会看到一行 `user:xiaowei:r-x`，这就是单独为`xiaowei`设置的ACL条目。

**删除ACL权限**：
*   删除特定用户的ACL条目：`setfacl -x u:用户名 文件/目录`
*   删除所有ACL条目（恢复标准权限）：`setfacl -b 文件/目录`

ACL功能强大，可以实现非常复杂的权限管理模型，是系统管理员必须掌握的工具之一。

---

## 总结

本节课中我们一起学习了Linux的四种特殊权限：
1.  **SUID**：针对可执行文件，使执行者临时拥有文件所有者的身份。
2.  **SGID**：针对可执行文件或目录（对目录时意义不同，本课未展开），使执行者临时拥有文件所属组的身份，或在目录中新建的文件继承目录的所属组。
3.  **SBIT**：针对目录，保护目录内的文件只能被其所有者、目录所有者或root删除。
4.  **ACL**：访问控制列表，突破了传统三元组权限模型，可以为特定用户或组单独设置精细的权限。

![](img/6b57f34d390c1dd08040262ded986cec_81.png)

![](img/6b57f34d390c1dd08040262ded986cec_83.png)

这些特殊权限与基本权限（rwx）共同构成了Linux强大而灵活的文件系统安全体系。理解并正确使用它们，是进行高效、安全系统管理的基础。在实际工作中，SUID/SGID需格外谨慎，SBIT常用于共享目录，而ACL则用于解决复杂的权限分配需求。