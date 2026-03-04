# 全网最全RHCE红帽认证全套入门教程：P13：2.08-附加权限

在本节课中，我们将要学习Linux系统中的附加权限。这些权限扩展了基本的读写执行（rwx）权限，提供了更精细的控制能力，例如让程序以所有者权限运行，或让目录中的新文件自动继承目录的属组。

![](img/c49706bae75252cf6c3fce2bfb66a685_1.png)

上一节我们介绍了访问控制列表（ACL），它解决了基本归属分类（用户、组、其他人）过于笼统的问题。本节中我们来看看权限本身是否也存在类似的扩展需求。答案是肯定的，Linux系统提供了一些特殊的附加权限。

![](img/c49706bae75252cf6c3fce2bfb66a685_3.png)

## 附加权限概述

![](img/c49706bae75252cf6c3fce2bfb66a685_5.png)

![](img/c49706bae75252cf6c3fce2bfb66a685_7.png)

Linux的基本权限标记（rwx）只有九位，分别对应所有者、所属组和其他人的读写执行权限。当需要实现超出这些基本控制的功能时，就需要使用附加权限。附加权限通过修改执行位（x）的特殊标记来实现。

主要有三种附加权限：
*   **Set User ID (SUID)**：附加在所有者执行位上。
*   **Set Group ID (SGID)**：附加在所属组执行位上。
*   **Sticky Bit**：附加在其他人执行位上。

这些标记会将原本的 `x` 变为 `s`（对于SUID和SGID）或 `t`（对于Sticky Bit）。如果文件本身没有执行权限，则会显示为大写的 `S` 或 `T`。

![](img/c49706bae75252cf6c3fce2bfb66a685_9.png)

![](img/c49706bae75252cf6c3fce2bfb66a685_11.png)

## Set User ID (SUID) 🛡️

![](img/c49706bae75252cf6c3fce2bfb66a685_13.png)

SUID权限让执行该程序的用户，能够临时获得程序所有者的权限，而非执行者自身的权限。这类似于“尚方宝剑”，持有者可以行使赋予者的部分权力。

一个典型的例子是 `/usr/bin/passwd` 命令。普通用户可以使用它修改自己的密码，但密码最终存储在 `/etc/shadow` 文件中，而该文件对普通用户是不可读写的。

**原理如下：**
1.  `passwd` 命令的所有者是 `root`。
2.  `passwd` 命令被设置了SUID权限。
3.  当普通用户执行 `passwd` 时，该进程会临时拥有 `root` 用户的权限，从而能够修改 `/etc/shadow` 文件。

**查看与设置SUID：**
我们可以使用 `ls -l` 查看文件的SUID权限（所有者执行位为 `s`）。

```bash
ls -l /usr/bin/passwd
```
输出类似：`-rwsr-xr-x. 1 root root ...`

![](img/c49706bae75252cf6c3fce2bfb66a685_15.png)

使用 `chmod` 命令设置或取消SUID：
*   **设置SUID**：`chmod u+s 文件名`
*   **取消SUID**：`chmod u-s 文件名`

![](img/c49706bae75252cf6c3fce2bfb66a685_17.png)

> **注意**：SUID权限需谨慎使用。如果给普通文本编辑器（如vim）设置SUID，任何用户都可能用它修改系统关键文件，带来安全风险。

![](img/c49706bae75252cf6c3fce2bfb66a685_19.png)

![](img/c49706bae75252cf6c3fce2bfb66a685_21.png)

## Set Group ID (SGID) 📁

SGID权限有两种应用场景，考试中重点考察其对目录的作用。

![](img/c49706bae75252cf6c3fce2bfb66a685_23.png)

### SGID对目录的作用

![](img/c49706bae75252cf6c3fce2bfb66a685_25.png)

当对一个目录设置SGID权限后，任何用户在此目录下创建的新文件或子目录，其所属组都会自动继承该目录的所属组，而不是创建者本人的主要组。

![](img/c49706bae75252cf6c3fce2bfb66a685_27.png)

**这解决了什么问题？**
在协作目录中，我们希望所有成员创建的文件都归属于同一个特定的工作组，便于统一管理权限，而不受个人主要组的影响。

![](img/c49706bae75252cf6c3fce2bfb66a685_29.png)

![](img/c49706bae75252cf6c3fce2bfb66a685_31.png)

![](img/c49706bae75252cf6c3fce2bfb66a685_33.png)

### 实战：创建公用目录 🛠️

以下是本节相关的练习题要求：
1.  创建目录 `/home/tools`。
2.  目录的所属组应为 `admins`。
3.  目录应能被 `admins` 组的成员读取、写入和访问。
4.  在此目录下创建的文件，其所属组应自动继承为 `admins` 组。

![](img/c49706bae75252cf6c3fce2bfb66a685_35.png)

**以下是解题步骤与命令：**

首先，创建目录并修改其所属组。
```bash
mkdir /home/tools
chgrp admins /home/tools
```

接着，设置目录的基本权限，确保 `admins` 组成员有读写执行权限，而其他人无任何权限。
```bash
chmod g+rwx,o-rwx /home/tools
# 或使用数字表示法：chmod 770 /home/tools
```

此时，如果管理员在目录下创建文件，其所属组仍是 `root`，不符合第4条要求。
```bash
touch /home/tools/root1.txt
ls -l /home/tools/root1.txt
```

为了实现“自动继承所属组”的功能，需要给目录设置SGID权限。
```bash
chmod g+s /home/tools
```
设置后，目录的权限标记中，所属组的执行位会变为 `s`。

![](img/c49706bae75252cf6c3fce2bfb66a685_37.png)

![](img/c49706bae75252cf6c3fce2bfb66a685_39.png)

现在验证效果，新建的文件所属组会自动变为 `admins`。
```bash
touch /home/tools/root2.txt
ls -l /home/tools/root2.txt
```

![](img/c49706bae75252cf6c3fce2bfb66a685_41.png)

![](img/c49706bae75252cf6c3fce2bfb66a685_43.png)

**命令总结：**
完成此题需要四条命令：
1.  `mkdir /home/tools`
2.  `chgrp admins /home/tools`
3.  `chmod 770 /home/tools` (或 `chmod g+rwx,o-rwx /home/tools`)
4.  `chmod g+s /home/tools`

![](img/c49706bae75252cf6c3fce2bfb66a685_45.png)

![](img/c49706bae75252cf6c3fce2bfb66a685_47.png)

## 权限的数字表示法 🔢

除了 `rwx` 这种符号表示法，权限还可以用八进制数字表示，便于快速设置。这种方法将权限位视为二进制开关，并转换为十进制数。

**对应关系：**
*   `r` (读) = 4
*   `w` (写) = 2
*   `x` (执行) = 1
*   `-` (无) = 0

**计算方法：**
将用户、组、其他的权限分别相加，得到三个数字。
例如：权限 `rwxr-xr--`
*   所有者：`rwx` = 4+2+1 = **7**
*   所属组：`r-x` = 4+0+1 = **5**
*   其他人：`r--` = 4+0+0 = **4**
因此，数字表示为 **754**。

![](img/c49706bae75252cf6c3fce2bfb66a685_49.png)

**设置附加权限的数字表示法：**
附加权限需要在三位基本权限数字前再加一位数字。
*   **SUID** 用 **4** 表示
*   **SGID** 用 **2** 表示
*   **Sticky Bit** 用 **1** 表示

例如，要设置权限为 `rwxrws---`（即770权限加上SGID），其数字表示为：
`2` (SGID) `7` (所有者) `7` (所属组) `0` (其他人) = **2770**
```bash
chmod 2770 /home/tools
```

## Sticky Bit (粘滞位) 🍬

![](img/c49706bae75252cf6c3fce2bfb66a685_51.png)

Sticky Bit（粘滞位）通常设置在公共可写目录（如 `/tmp`）上。它允许所有用户在其中创建文件，但每个用户只能删除或重命名自己的文件，而不能删除其他人的文件。

**作用：**
没有粘滞位时，对目录有写权限的用户可以删除目录内的任何文件。设置了粘滞位后，即使有目录的写权限，用户也只能操作自己创建的文件。

**查看与设置：**
查看 `/tmp` 目录权限，其他人执行位为 `t`。
```bash
ls -ld /tmp
```
设置粘滞位：
```bash
chmod o+t /目录名
# 或使用数字表示法，在首位加1，例如 chmod 1777 /tmp
```

## 总结

![](img/c49706bae75252cf6c3fce2bfb66a685_53.png)

![](img/c49706bae75252cf6c3fce2bfb66a685_55.png)

本节课中我们一起学习了Linux的三种附加权限：
1.  **SUID**：使程序执行者临时获得文件所有者的权限，常用于系统管理命令（如 `passwd`）。
2.  **SGID（对目录）**：使目录下新建的文件自动继承目录的所属组，用于创建协作共享的公用目录，这是考试的重点。
3.  **Sticky Bit**：用于公共目录（如 `/tmp`），确保用户只能管理自己的文件，不能删除他人的文件。

![](img/c49706bae75252cf6c3fce2bfb66a685_57.png)

![](img/c49706bae75252cf6c3fce2bfb66a685_59.png)

![](img/c49706bae75252cf6c3fce2bfb66a685_61.png)

理解并掌握这些附加权限，特别是SGID在目录上的应用，能够帮助你更有效地管理Linux系统中的文件访问和协作环境。