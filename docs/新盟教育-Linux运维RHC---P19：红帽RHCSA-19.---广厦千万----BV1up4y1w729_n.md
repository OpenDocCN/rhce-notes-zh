# Linux运维RHCSA+RHC培训教程：P19：特殊权限SUID、SGID、SBIT、ACL 🛡️

![](img/c8ef997feaa8cf286c3498d96b489528_1.png)

![](img/c8ef997feaa8cf286c3498d96b489528_3.png)

![](img/c8ef997feaa8cf286c3498d96b489528_4.png)

![](img/c8ef997feaa8cf286c3498d96b489528_6.png)

在本节课中，我们将要学习Linux系统中的四种特殊权限：SUID、SGID、SBIT和ACL。这些权限扩展了基本权限（rwx）的功能，用于满足更精细和特殊的安全控制需求。我们将逐一了解它们的作用、应用场景及设置方法。

![](img/c8ef997feaa8cf286c3498d96b489528_8.png)

## 概述 📋

上一节我们介绍了Linux文件的基本权限和归属关系。本节中，我们来看看几种特殊的权限机制。它们分别是针对可执行文件的SUID和SGID，针对目录的SBIT（粘滞位），以及更为灵活的访问控制列表ACL。理解这些权限对于系统安全和精细化管理至关重要。

![](img/c8ef997feaa8cf286c3498d96b489528_10.png)

## SUID（Set User ID）权限 🔑

![](img/c8ef997feaa8cf286c3498d96b489528_12.png)

SUID权限主要针对**可执行文件**。当一个可执行文件被设置了SUID权限后，任何普通用户在执行该文件时，会**临时拥有文件所有者（通常是root）的身份和权限**。该权限仅在程序执行过程中有效，执行完毕后用户恢复原有身份。

![](img/c8ef997feaa8cf286c3498d96b489528_14.png)

![](img/c8ef997feaa8cf286c3498d96b489528_16.png)

![](img/c8ef997feaa8cf286c3498d96b489528_17.png)

### 功能与原理

![](img/c8ef997feaa8cf286c3498d96b489528_18.png)

为什么需要SUID？以系统命令`/usr/bin/passwd`为例。普通用户可以使用`passwd`命令修改自己的密码，但密码最终存储在`/etc/shadow`文件中。查看该文件的权限：

```bash
ls -l /etc/shadow
```
输出类似：`-r--------. 1 root root ...`，表示只有root用户可读。

普通用户对`/etc/shadow`文件没有写入权限，理论上无法修改密码。但`passwd`命令设置了SUID权限：

```bash
ls -l /usr/bin/passwd
```
输出中，所有者的执行位是 `s`（例如 `-rwsr-xr-x`），而不是普通的 `x`。

当普通用户执行`passwd`时，由于SUID的存在，该进程会临时获得**root身份**，从而能够修改`/etc/shadow`文件中的密码字段。

### 设置与取消SUID

使用 `chmod` 命令设置和取消SUID权限。

*   **设置SUID**：`chmod u+s 文件名`
*   **取消SUID**：`chmod u-s 文件名`

**注意**：SUID权限非常强大，不当设置（例如给文本编辑器vim设置SUID）可能导致普通用户获得root权限，带来严重安全风险，因此**切勿随意设置**。

### 权限标识

*   如果文件**原有执行权限（x）**，设置SUID后显示为小写 `s`。
*   如果文件**没有执行权限**，设置SUID后显示为大写 `S`。这仅表示设置了SUID位，但文件本身不可执行。

---

## SGID（Set Group ID）权限 👥

SGID权限同样主要针对**可执行文件**。设置了SGID的文件，任何用户在执行时，会**临时拥有文件所属组的身份和权限**。

![](img/c8ef997feaa8cf286c3498d96b489528_20.png)

![](img/c8ef997feaa8cf286c3498d96b489528_21.png)

### 功能与原理

以`locate`命令为例。`locate`通过查询`/var/lib/mlocate/mlocate.db`数据库文件来快速定位文件。查看该数据库文件的权限：

```bash
ls -l /var/lib/mlocate/mlocate.db
```
通常，其组权限为 `r--`，且属于 `slocate` 组。

![](img/c8ef997feaa8cf286c3498d96b489528_23.png)

普通用户（如tom）不属于`slocate`组，按理无法读取此数据库。但`locate`命令本身设置了SGID权限：

```bash
ls -l /usr/bin/locate
```
输出中，所属组的执行位是 `s`（例如 `-rwx--s--x`）。

![](img/c8ef997feaa8cf286c3498d96b489528_25.png)

当普通用户执行`locate`时，进程临时获得`slocate`组的身份，从而继承了该组对数据库文件的读权限，得以完成搜索。

### 设置与取消SGID

*   **设置SGID**：`chmod g+s 文件名`
*   **取消SGID**：`chmod g-s 文件名`

![](img/c8ef997feaa8cf286c3498d96b489528_27.png)

![](img/c8ef997feaa8cf286c3498d96b489528_29.png)

![](img/c8ef997feaa8cf286c3498d96b489528_31.png)

![](img/c8ef997feaa8cf286c3498d96b489528_33.png)

**注意**：与SUID类似，SGID也需谨慎使用。对于普通用户而言，了解其存在意义即可，通常无需手动设置。

![](img/c8ef997feaa8cf286c3498d96b489528_35.png)

![](img/c8ef997feaa8cf286c3498d96b489528_37.png)

![](img/c8ef997feaa8cf286c3498d96b489528_39.png)

---

## SBIT（Sticky Bit）粘滞位权限 📌

SBIT权限**只对目录有效**。它对一个权限开放的目录（如`/tmp`）提供了额外的安全限制。

![](img/c8ef997feaa8cf286c3498d96b489528_41.png)

![](img/c8ef997feaa8cf286c3498d96b489528_42.png)

### 功能与原理

如果一个目录权限为777（即所有用户可读、写、执行），那么任何用户都可以删除该目录下的任何文件，包括其他用户创建的文件。这显然不够安全。

![](img/c8ef997feaa8cf286c3498d96b489528_44.png)

为目录设置SBIT（粘滞位）后，情况发生变化：**在该目录下，用户只能删除或重命名自己创建的文件或目录，而不能操作其他用户的文件**（root用户除外）。

系统临时目录`/tmp`就是一个典型例子：
```bash
ls -ld /tmp
```
输出中，其他人的执行位是 `t`（例如 `drwxrwxrwt`）。

![](img/c8ef997feaa8cf286c3498d96b489528_46.png)

这意味着，用户A可以在`/tmp`中创建和删除自己的文件，但无法删除用户B创建的文件。

![](img/c8ef997feaa8cf286c3498d96b489528_48.png)

![](img/c8ef997feaa8cf286c3498d96b489528_50.png)

### 设置与取消SBIT

*   **设置SBIT**：`chmod o+t 目录名`
*   **取消SBIT**：`chmod o-t 目录名`

这个权限在需要多用户共享一个可写目录时非常有用，能有效防止误删或恶意删除。

![](img/c8ef997feaa8cf286c3498d96b489528_52.png)

![](img/c8ef997feaa8cf286c3498d96b489528_54.png)

---

## ACL（Access Control List）访问控制列表 📝

![](img/c8ef997feaa8cf286c3498d96b489528_56.png)

![](img/c8ef997feaa8cf286c3498d96b489528_58.png)

上述的基本权限和特殊权限（SUID/SGID/SBIT）有时仍不够灵活。例如，我们想给一个特定的用户（既不是所有者，也不在所属组内）分配某个目录的特定权限，用传统方式很难实现。

这时就需要ACL（文件系统访问控制列表）。ACL可以为**单个用户或用户组**设置独立的、细粒度的文件/目录访问权限。

### 设置ACL权限

![](img/c8ef997feaa8cf286c3498d96b489528_60.png)

![](img/c8ef997feaa8cf286c3498d96b489528_62.png)

![](img/c8ef997feaa8cf286c3498d96b489528_63.png)

使用 `setfacl` 命令管理ACL。

![](img/c8ef997feaa8cf286c3498d96b489528_65.png)

*   **为用户设置ACL权限**：
    ```bash
    setfacl -m u:用户名:权限 文件/目录名
    ```
    例如，允许用户`xiaowei`对目录`/yunwei`有读和执行权限：
    ```bash
    setfacl -m u:xiaowei:rx /yunwei
    ```

*   **为用户组设置ACL权限**：
    ```bash
    setfacl -m g:组名:权限 文件/目录名
    ```
    例如，允许组`stugrp`对目录`/yunwei`有读和执行权限：
    ```bash
    setfacl -m g:stugrp:rx /yunwei
    ```

### 查看ACL权限

使用 `getfacl` 命令查看文件或目录的ACL信息。
```bash
getfacl /yunwei
```
输出会显示所有者、所属组的基本权限，以及下方列出的额外ACL条目。

设置了ACL权限的文件/目录，在使用 `ls -l` 查看时，权限位末尾会有一个 `+` 号。

### 删除ACL权限

![](img/c8ef997feaa8cf286c3498d96b489528_67.png)

![](img/c8ef997feaa8cf286c3498d96b489528_69.png)

![](img/c8ef997feaa8cf286c3498d96b489528_71.png)

*   **删除特定用户/组的ACL条目**：
    ```bash
    setfacl -x u:用户名 文件/目录名  # 删除用户条目
    setfacl -x g:组名 文件/目录名    # 删除组条目
    ```

*   **删除所有ACL条目（恢复默认）**：
    ```bash
    setfacl -b 文件/目录名
    ```

ACL提供了远超传统Unix权限模型的灵活性，是实现复杂权限管理的利器。

---

## 总结 🎯

本节课中我们一起学习了Linux系统中的四种特殊权限：

1.  **SUID**：针对可执行文件，使执行者临时获得文件所有者的身份。**慎用**。
2.  **SGID**：针对可执行文件，使执行者临时获得文件所属组的身份。**了解即可**。
3.  **SBIT**：针对目录，确保用户只能管理自己在目录中的文件。**常用于共享目录（如`/tmp`）**。
4.  **ACL**：访问控制列表，可为特定用户或组设置独立的文件/目录权限。**功能强大且常用**。

![](img/c8ef997feaa8cf286c3498d96b489528_73.png)

![](img/c8ef997feaa8cf286c3498d96b489528_75.png)

这些特殊权限与基本权限共同构成了Linux系统完整而坚固的安全体系。掌握它们，你将能更从容地应对各种系统管理和安全配置场景。