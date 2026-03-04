# Linux权限管理：2.08：附加权限 🔐

在本节课中，我们将要学习Linux系统中的附加权限。之前我们介绍了基本的文件权限和归属，以及用于解决归属分类过于笼统的访问控制列表（ACL）。本节中我们来看看，除了基本的读写执行（RWX）权限外，Linux还提供了一些特殊的附加权限，它们可以赋予文件或目录额外的能力。

![](img/40214fc064b20592df65aab66c15d29e_1.png)

## 附加权限概述

![](img/40214fc064b20592df65aab66c15d29e_3.png)

![](img/40214fc064b20592df65aab66c15d29e_4.png)

Linux的基本权限位只有9个（所有者、所属组、其他人各占3位RWX），有时这不足以满足复杂的管理需求。附加权限就是通过修改执行位（X）的表示方式来实现特殊功能。主要有三种类型：Set UID、Set GID 和 粘滞位。

![](img/40214fc064b20592df65aab66c15d29e_6.png)

## Set UID 权限

Set UID（SUID）是一种附加在**可执行文件**所有者执行位（X）上的特殊权限。当普通用户执行一个具有SUID权限的程序时，该程序会临时获得其**所有者**的权限，而非执行者的权限。

![](img/40214fc064b20592df65aab66c15d29e_8.png)

**核心概念公式/代码：**
*   权限标记：所有者执行位 `x` 变为 `s`（如果所有者原本有x权限）或 `S`（如果所有者原本无x权限）。
*   设置命令：`chmod u+s 文件名`
*   移除命令：`chmod u-s 文件名`

![](img/40214fc064b20592df65aab66c15d29e_10.png)

![](img/40214fc064b20592df65aab66c15d29e_12.png)

一个典型的例子是 `/usr/bin/passwd` 命令。普通用户可以使用它修改自己的密码，但密码实际存储在 `/etc/shadow` 文件中，而该文件普通用户并无写入权限。这是因为 `passwd` 命令被设置了SUID权限，属于root用户。当普通用户执行它时，便临时获得了root的权限，从而能够更新 `/etc/shadow` 文件。

**演示：**
```bash
# 查看passwd命令的权限，注意所有者执行位是 ‘s’
ls -l /usr/bin/passwd

# 移除SUID权限
sudo chmod u-s /usr/bin/passwd

# 再次查看，执行位变回 ‘x’ 或 ‘-’
ls -l /usr/bin/passwd

# 此时普通用户将无法修改密码
su - lisi
passwd
# 操作会失败
```

![](img/40214fc064b20592df65aab66c15d29e_14.png)

> **注意**：SUID权限需谨慎使用，如果错误地授予像 `vim` 这样的编辑器，可能导致安全风险。RHCE考试不直接考查SUID。

![](img/40214fc064b20592df65aab66c15d29e_16.png)

![](img/40214fc064b20592df65aab66c15d29e_18.png)

## Set GID 权限

![](img/40214fc064b20592df65aab66c15d29e_20.png)

Set GID（SGID）有两种应用场景：
1.  **应用于可执行文件**：类似于SUID，但程序执行时会临时获得其**所属组**的权限。
2.  **应用于目录**（**考试重点**）：这是更常见的用法。当一个目录被设置SGID权限后，任何用户在此目录下创建的新文件或子目录，其所属组都会自动**继承**该目录的所属组，而不是创建者本人的主要组。

![](img/40214fc064b20592df65aab66c15d29e_22.png)

**核心概念公式/代码：**
*   权限标记：所属组执行位 `x` 变为 `s`。
*   设置命令：`chmod g+s 目录名`
*   移除命令：`chmod g-s 目录名`

![](img/40214fc064b20592df65aab66c15d29e_24.png)

### 实战：创建共用目录 🗂️

![](img/40214fc064b20592df65aab66c15d29e_26.png)

![](img/40214fc064b20592df65aab66c15d29e_28.png)

上一节我们介绍了SUID权限，本节中我们来看看SGID权限的一个典型应用场景。以下是创建共用目录的题目要求：
1.  创建目录 `/home/tools`。
2.  目录的所属组应为 `admins`。
3.  目录应能被 `admins` 组的成员读取、写入和访问。
4.  在此目录下创建的任何文件，其组所有权应自动属于 `admins` 组。

![](img/40214fc064b20592df65aab66c15d29e_30.png)

![](img/40214fc064b20592df65aab66c15d29e_32.png)

**解题步骤与命令：**

![](img/40214fc064b20592df65aab66c15d29e_34.png)

首先，创建目录并修改其所属组。
```bash
mkdir /home/tools
chown :admins /home/tools
```

接着，设置目录的基本权限，确保 `admins` 组成员有全部权限，而其他人无任何权限。
```bash
chmod g=rwx,o= /home/tools
# 或使用数字法：chmod 770 /home/tools
```

此时，如果管理员在目录下创建文件，其所属组仍是 `root`，不符合第4条要求。
```bash
touch /home/tools/root1.txt
ls -l /home/tools/root1.txt # 查看所属组
```

最后，为目录设置SGID权限，实现组权限继承。
```bash
chmod g+s /home/tools
# 此时查看目录权限，所属组执行位变为 ‘s’
ls -ld /home/tools
```

现在，任何人在此目录下创建的文件，其所属组都会自动变为 `admins`。
```bash
touch /home/tools/root2.txt
ls -l /home/tools/root2.txt # 所属组已变为 admins
```

![](img/40214fc064b20592df65aab66c15d29e_36.png)

![](img/40214fc064b20592df65aab66c15d29e_38.png)

![](img/40214fc064b20592df65aab66c15d29e_40.png)

**完整命令序列总结：**
```bash
mkdir /home/tools
chown :admins /home/tools
chmod 770 /home/tools
chmod g+s /home/tools
```

![](img/40214fc064b20592df65aab66c15d29e_42.png)

![](img/40214fc064b20592df65aab66c15d29e_44.png)

## 粘滞位权限

![](img/40214fc064b20592df65aab66c15d29e_46.png)

粘滞位（Sticky Bit）通常附加在**目录**的其他人执行位（X）上。它用于限制目录内的删除操作。对于一个设置了粘滞位的目录，即使用户对该目录有写入权限，也只能删除自己创建的文件或目录，而不能删除其他用户的文件。

**核心概念公式/代码：**
*   权限标记：其他人执行位 `x` 变为 `t`。
*   设置命令：`chmod o+t 目录名`
*   移除命令：`chmod o-t 目录名`

系统目录 `/tmp` 和 `/var/tmp` 是粘滞位的典型应用。它们权限为 `drwxrwxrwt`，允许所有用户读写，但防止用户随意删除他人的临时文件。
```bash
ls -ld /tmp # 查看权限，注意最后的 ‘t’
```

![](img/40214fc064b20592df65aab66c15d29e_48.png)

## 权限的数字表示法（扩展）

除了 `u/g/o +/- r/w/x/s/t` 这种符号法，权限还可以用八进制数字表示，便于快速设置。

**对应关系：**
*   `r` = 4
*   `w` = 2
*   `x` = 1
*   无权限 = 0

![](img/40214fc064b20592df65aab66c15d29e_50.png)

所有者、所属组、其他人的权限分别由三个数字之和表示，例如 `rwxr-xr--` 即为 `4+2+1=7`, `4+0+1=5`, `4+0+0=4`，所以是 `754`。

**附加权限的数字表示：**
在三位基本权限数字前，可增加第四位来表示附加权限：
*   Set UID = 4
*   Set GID = 2
*   粘滞位 = 1

例如，设置权限为 `rwxrws---`（即770加SGID）的命令是：
```bash
chmod 2770 /home/tools
```

![](img/40214fc064b20592df65aab66c15d29e_52.png)

![](img/40214fc064b20592df65aab66c15d29e_54.png)

## 总结

![](img/40214fc064b20592df65aab66c15d29e_56.png)

![](img/40214fc064b20592df65aab66c15d29e_58.png)

![](img/40214fc064b20592df65aab66c15d29e_60.png)

本节课中我们一起学习了Linux的三种附加权限：Set UID、Set GID 和 粘滞位。
*   **Set UID** 使程序执行时获得文件所有者的权限。
*   **Set GID** 应用于目录时，能实现组权限的自动继承，这是RHCE考试中“创建共用目录”题目的关键。
*   **粘滞位** 用于保护目录内容，防止用户删除他人的文件。
理解并掌握这些特殊权限，尤其是SGID在目录上的应用，对于进行有效的系统文件管理和完成认证考试都至关重要。