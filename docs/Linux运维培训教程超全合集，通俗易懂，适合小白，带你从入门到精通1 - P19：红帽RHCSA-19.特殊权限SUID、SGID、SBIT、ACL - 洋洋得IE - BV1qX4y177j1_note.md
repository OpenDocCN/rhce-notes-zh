# Linux运维培训教程：19：特殊权限SUID、SGID、SBIT、ACL 🛡️

![](img/166ab628561edae7fc876bc23ae0b672_1.png)

![](img/166ab628561edae7fc876bc23ae0b672_3.png)

![](img/166ab628561edae7fc876bc23ae0b672_5.png)

![](img/166ab628561edae7fc876bc23ae0b672_7.png)

在本节课中，我们将要学习Linux系统中的四种特殊权限：SUID、SGID、SBIT和ACL。这些权限扩展了基本权限（rwx）的功能，用于满足更精细和特殊的安全控制需求。我们将逐一了解它们的作用、应用场景以及如何设置。

## 概述 📋

上一节我们介绍了Linux文件的基本权限和归属关系。本节中，我们来看看几种特殊的权限机制。它们分别是：
*   **SUID** 和 **SGID**：主要针对可执行文件，用于临时提升用户或组的权限。
*   **SBIT**：主要针对目录，用于限制用户删除他人文件。
*   **ACL**：文件访问控制列表，可以为单个用户或组设置独立的权限。

![](img/166ab628561edae7fc876bc23ae0b672_9.png)

理解这些特殊权限，能帮助我们更好地管理系统安全和资源共享。

![](img/166ab628561edae7fc876bc23ae0b672_11.png)

![](img/166ab628561edae7fc876bc23ae0b672_13.png)

---

![](img/166ab628561edae7fc876bc23ae0b672_15.png)

![](img/166ab628561edae7fc876bc23ae0b672_17.png)

![](img/166ab628561edae7fc876bc23ae0b672_19.png)

## SUID（Set User ID）🔑

SUID权限主要应用于**可执行文件**。当一个可执行文件被设置了SUID权限后，任何普通用户在执行该文件时，都会**临时拥有文件所有者（通常是root）的身份**。该权限仅在程序执行过程中有效，程序执行完毕后用户恢复原有身份。

### 核心概念与示例

系统中有一些命令天然具有SUID权限，例如用于修改用户密码的 `passwd` 命令。

**为什么需要SUID？**
普通用户可以使用 `passwd` 命令修改自己的密码，但密码最终保存在 `/etc/shadow` 文件中。查看该文件的权限：
```bash
ls -l /etc/shadow
```
你会发现普通用户对这个文件**没有任何写权限**。那么用户是如何修改其中的密码字段的呢？原因就在于 `passwd` 命令本身。

查看 `passwd` 命令的权限：
```bash
ls -l /usr/bin/passwd
```
你会看到所有者的执行位是 `s`（而不是 `x`），并且文件有特殊的背景色。这表示它拥有SUID权限。

当普通用户执行 `passwd` 时，由于SUID的存在，该进程会临时获得文件所有者 `root` 的身份，从而拥有了修改 `/etc/shadow` 文件的权限。

### 如何设置与取消SUID

使用 `chmod` 命令设置SUID：
```bash
chmod u+s /path/to/file
```
使用 `chmod` 命令取消SUID：
```bash
chmod u-s /path/to/file
```

**警告**：SUID权限非常强大，如果错误地授予给普通命令（如 `vim` 或 `cat`），可能导致普通用户获得root权限，带来严重的安全风险。因此，**切勿随意设置SUID**。

---

## SGID（Set Group ID）👥

![](img/166ab628561edae7fc876bc23ae0b672_21.png)

SGID权限同样主要应用于**可执行文件**。其功能与SUID类似，但对象是组。当一个可执行文件被设置了SGID权限后，任何普通用户在执行该文件时，会**临时拥有文件所属组的身份**。

![](img/166ab628561edae7fc876bc23ae0b672_23.png)

### 核心概念与示例

系统命令 `locate` 是一个典型的例子。`locate` 命令需要读取 `/var/lib/mlocate/mlocate.db` 这个数据库文件来搜索文件位置。

查看数据库文件权限：
```bash
ls -l /var/lib/mlocate/mlocate.db
```
你会发现，其他用户（others）对这个文件**没有读权限**。

![](img/166ab628561edae7fc876bc23ae0b672_25.png)

查看 `locate` 命令的权限：
```bash
ls -l /usr/bin/locate
```
你会看到所属组的执行位是 `s`，并且文件有黄色背景。这表示它拥有SGID权限。

当普通用户执行 `locate` 时，由于SGID的存在，该进程会临时获得文件所属组（例如 `slocate`）的身份。而该组对数据库文件拥有读权限，因此用户能够成功执行搜索。

![](img/166ab628561edae7fc876bc23ae0b672_27.png)

### 如何设置与取消SGID

使用 `chmod` 命令设置SGID：
```bash
chmod g+s /path/to/file
```
使用 `chmod` 命令取消SGID：
```bash
chmod g-s /path/to/file
```
与SUID一样，SGID也需谨慎使用，日常管理中我们很少手动设置它。

![](img/166ab628561edae7fc876bc23ae0b672_29.png)

![](img/166ab628561edae7fc876bc23ae0b672_31.png)

---

![](img/166ab628561edae7fc876bc23ae0b672_33.png)

![](img/166ab628561edae7fc876bc23ae0b672_35.png)

![](img/166ab628561edae7fc876bc23ae0b672_37.png)

![](img/166ab628561edae7fc876bc23ae0b672_39.png)

## SBIT（Sticky Bit）📌

![](img/166ab628561edae7fc876bc23ae0b672_41.png)

SBIT权限**只对目录有效**。它对目录的写权限（w）进行了额外的限制。

### 核心概念与示例

![](img/166ab628561edae7fc876bc23ae0b672_43.png)

![](img/166ab628561edae7fc876bc23ae0b672_45.png)

当一个目录被赋予完全权限（如777）时，任何用户都可以删除该目录下的任何文件，包括他人创建的文件。这显然在某些共享目录（如 `/tmp`）中是不合理的。

SBIT权限就是为了解决这个问题。**设置了SBIT的目录，即使所有用户都有写权限，用户也只能删除自己创建的文件，而不能删除其他用户的文件**（root用户除外）。

![](img/166ab628561edae7fc876bc23ae0b672_47.png)

查看系统临时目录 `/tmp`：
```bash
ls -ld /tmp
```
你会看到其他用户（others）的执行位是 `t`，并且目录有绿色背景。这表示它拥有SBIT权限。

![](img/166ab628561edae7fc876bc23ae0b672_49.png)

**效果验证**：
1.  用户A在 `/tmp` 下创建文件 `fileA`。
2.  用户B在 `/tmp` 下创建文件 `fileB`。
3.  用户B**无法**删除用户A的 `fileA`，但可以删除自己的 `fileB`。

### 如何设置与取消SBIT

![](img/166ab628561edae7fc876bc23ae0b672_51.png)

![](img/166ab628561edae7fc876bc23ae0b672_53.png)

使用 `chmod` 命令设置SBIT：
```bash
chmod o+t /path/to/directory
```
使用 `chmod` 命令取消SBIT：
```bash
chmod o-t /path/to/directory
```
SBIT在管理共享目录时非常有用，是运维工作中常用的特殊权限之一。

---

![](img/166ab628561edae7fc876bc23ae0b672_55.png)

## ACL（Access Control List）访问控制列表 🗂️

![](img/166ab628561edae7fc876bc23ae0b672_57.png)

基本权限系统（所有者、所属组、其他人）有时不够灵活。例如，你想让某个特定用户（既不是所有者，也不在所属组内）拥有某个目录的特定权限，ACL就能完美解决这个问题。

![](img/166ab628561edae7fc876bc23ae0b672_59.png)

![](img/166ab628561edae7fc876bc23ae0b672_61.png)

ACL可以为**单个用户或用户组**设置独立的文件/目录访问权限，实现更精细的权限控制。

### 核心概念与示例

假设有一个目录 `/ops`，属于 `ops` 组，组内成员有读写执行权限，其他人无任何权限。
```bash
chmod 770 /ops
chown root:ops /ops
```
现在，有一个实习生用户 `intern`，你希望TA能进入和查看 `/ops` 目录，但不能修改或删除。使用基本权限无法实现，因为 `intern` 不属于 `ops` 组，且你不希望给“其他人”开放任何权限。

![](img/166ab628561edae7fc876bc23ae0b672_63.png)

![](img/166ab628561edae7fc876bc23ae0b672_65.png)

![](img/166ab628561edae7fc876bc23ae0b672_67.png)

这时就需要使用ACL。

![](img/166ab628561edae7fc876bc23ae0b672_69.png)

### 如何设置、查看与删除ACL

以下是ACL的常用命令：

**1. 设置ACL权限**
使用 `setfacl` 命令为特定用户添加权限：
```bash
setfacl -m u:intern:rx /ops
```
*   `-m` 表示修改ACL规则。
*   `u:intern:rx` 表示为用户 `intern` 添加读（r）和执行（x）权限。
*   同样，可以为组设置：`setfacl -m g:group_name:rx /ops`

**2. 查看ACL权限**
使用 `getfacl` 命令查看文件或目录的ACL信息：
```bash
getfacl /ops
```
输出中会显示额外的 `user:intern:r-x` 条目。

**3. 删除特定ACL条目**
使用 `setfacl` 命令删除某个用户或组的ACL规则：
```bash
setfacl -x u:intern /ops
```

![](img/166ab628561edae7fc876bc23ae0b672_71.png)

**4. 清除所有ACL条目**
使用 `setfacl` 命令清除文件或目录上的所有ACL规则：
```bash
setfacl -b /ops
```

![](img/166ab628561edae7fc876bc23ae0b672_73.png)

![](img/166ab628561edae7fc876bc23ae0b672_75.png)

设置了ACL的目录，在使用 `ls -l` 查看时，权限位末尾会显示一个 `+` 号，例如：
```bash
drwxrwx---+ 2 root ops 4096 Dec 1 10:00 ops
```

ACL是运维工作中实现复杂权限管理的利器，需要重点掌握。

---

## 总结 🎯

本节课中我们一起学习了Linux的四种特殊权限：

1.  **SUID**：作用于可执行文件，使执行者临时获得文件所有者的身份。**慎用**。
2.  **SGID**：作用于可执行文件，使执行者临时获得文件所属组的身份。**了解即可**。
3.  **SBIT**：作用于目录，限制用户只能删除自己的文件。**常用于共享目录如`/tmp`**。
4.  **ACL**：访问控制列表，可以为单个用户或组设置独立于基本权限体系之外的精细权限。**必须掌握**。

![](img/166ab628561edae7fc876bc23ae0b672_77.png)

![](img/166ab628561edae7fc876bc23ae0b672_79.png)

这些特殊权限与基本权限共同构成了Linux强大而灵活的文件安全体系。在实际工作中，SBIT和ACL的使用频率较高，需要多加练习和理解。下一节，我们将进入新的知识模块的学习。