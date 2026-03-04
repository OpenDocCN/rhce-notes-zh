# Redhat红帽 RHCE8.0认证体系课程：P14：文件及目录权限练习 📝

![](img/5fa026b6083ec07b5885e443c49019cd_1.png)

![](img/5fa026b6083ec07b5885e443c49019cd_3.png)

在本节课中，我们将通过解决四道练习题，来复习和巩固上午学习的关于用户、组及文件权限的知识。我们将重点学习如何创建用户和组、设置文件权限，并引入一个重要的补充知识点——访问控制列表（ACL）。

![](img/5fa026b6083ec07b5885e443c49019cd_5.png)

---

## 第一题：创建用户与组

上一节我们介绍了用户和组的基本概念，本节中我们来看看如何根据具体要求创建它们。

![](img/5fa026b6083ec07b5885e443c49019cd_7.png)

![](img/5fa026b6083ec07b5885e443c49019cd_9.png)

题目要求如下：
1.  创建一个名为 `examuser` 的组，组ID为 `1802`。
2.  创建用户 `andy`，并将其附属组设置为 `examuser`。
3.  创建用户 `bella`，并将其附属组设置为 `examuser`。
4.  创建用户 `cherry`，其不属于 `examuser` 组，且其登录shell设置为不可登录。
5.  为以上三个用户设置密码，密码均为 `redhat`。

![](img/5fa026b6083ec07b5885e443c49019cd_11.png)

![](img/5fa026b6083ec07b5885e443c49019cd_13.png)

![](img/5fa026b6083ec07b5885e443c49019cd_15.png)

以下是解题步骤：

![](img/5fa026b6083ec07b5885e443c49019cd_17.png)

```bash
# 1. 创建指定GID的组
groupadd -g 1802 examuser

![](img/5fa026b6083ec07b5885e443c49019cd_19.png)

![](img/5fa026b6083ec07b5885e443c49019cd_21.png)

# 2. 创建用户andy，并指定附属组
useradd -G examuser andy

![](img/5fa026b6083ec07b5885e443c49019cd_23.png)

# 3. 创建用户bella，并指定附属组
useradd -G examuser bella

# 4. 创建用户cherry，指定不可登录的shell（如/sbin/nologin）
useradd -s /sbin/nologin cherry

![](img/5fa026b6083ec07b5885e443c49019cd_25.png)

![](img/5fa026b6083ec07b5885e443c49019cd_27.png)

# 5. 为三个用户设置密码
echo “redhat” | passwd --stdin andy
echo “redhat” | passwd --stdin bella
echo “redhat” | passwd --stdin cherry
```

![](img/5fa026b6083ec07b5885e443c49019cd_29.png)

![](img/5fa026b6083ec07b5885e443c49019cd_31.png)

这道题主要考察了使用 `groupadd` 创建组、使用 `useradd` 的 `-G` 选项指定附属组、使用 `-s` 选项指定shell类型，以及使用 `passwd` 命令设置密码。

![](img/5fa026b6083ec07b5885e443c49019cd_33.png)

---

![](img/5fa026b6083ec07b5885e443c49019cd_35.png)

![](img/5fa026b6083ec07b5885e443c49019cd_37.png)

![](img/5fa026b6083ec07b5885e443c49019cd_39.png)

## 第二题：设置文件权限与ACL

![](img/5fa026b6083ec07b5885e443c49019cd_41.png)

![](img/5fa026b6083ec07b5885e443c49019cd_43.png)

完成了用户和组的创建，现在我们来处理文件权限。本题将引入一个核心知识点——访问控制列表（ACL），它允许我们为特定用户或组设置超出传统 `user/group/other` 模型的精细权限。

![](img/5fa026b6083ec07b5885e443c49019cd_45.png)

![](img/5fa026b6083ec07b5885e443c49019cd_47.png)

题目要求如下：
1.  将文件 `/var/log/message` 复制到 `/tmp` 目录下，新文件名为 `message`。
2.  设置 `/tmp/message` 文件的所有者和所属组均为 `root`。
3.  该文件对任何人（所有者、所属组、其他用户）均**没有**执行权限。
4.  用户 `andy` 对该文件拥有读和写的权限。
5.  用户 `bella` 对该文件**既不能读也不能写**。
6.  其他用户（包括未来创建的用户）对该文件拥有读和写的权限。

![](img/5fa026b6083ec07b5885e443c49019cd_49.png)

![](img/5fa026b6083ec07b5885e443c49019cd_51.png)

![](img/5fa026b6083ec07b5885e443c49019cd_52.png)

以下是解题步骤：

![](img/5fa026b6083ec07b5885e443c49019cd_54.png)

![](img/5fa026b6083ec07b5885e443c49019cd_56.png)

![](img/5fa026b6083ec07b5885e443c49019cd_58.png)

```bash
# 1. 复制文件
cp /var/log/message /tmp/message

![](img/5fa026b6083ec07b5885e443c49019cd_60.png)

![](img/5fa026b6083ec07b5885e443c49019cd_62.png)

![](img/5fa026b6083ec07b5885e443c49019cd_64.png)

# 2. 设置所有者和所属组为root
chown root:root /tmp/message

![](img/5fa026b6083ec07b5885e443c49019cd_66.png)

![](img/5fa026b6083ec07b5885e443c49019cd_68.png)

# 3. 移除所有人的执行权限
chmod a-x /tmp/message

![](img/5fa026b6083ec07b5885e443c49019cd_70.png)

![](img/5fa026b6083ec07b5885e443c49019cd_72.png)

# 4. 使用ACL为特定用户andy设置读写权限
setfacl -m u:andy:rw /tmp/message

# 5. 使用ACL为特定用户bella设置无任何权限（用‘-’表示）
setfacl -m u:bella:- /tmp/message

![](img/5fa026b6083ec07b5885e443c49019cd_74.png)

![](img/5fa026b6083ec07b5885e443c49019cd_76.png)

# 6. 为其他用户（other）设置读写权限
chmod o+rw /tmp/message
```

![](img/5fa026b6083ec07b5885e443c49019cd_78.png)

为了验证设置结果，可以使用 `getfacl` 命令查看文件的ACL信息：

![](img/5fa026b6083ec07b5885e443c49019cd_80.png)

![](img/5fa026b6083ec07b5885e443c49019cd_82.png)

```bash
getfacl /tmp/message
```

![](img/5fa026b6083ec07b5885e443c49019cd_84.png)

![](img/5fa026b6083ec07b5885e443c49019cd_86.png)

### 补充知识点：访问控制列表（ACL）命令详解

![](img/5fa026b6083ec07b5885e443c49019cd_88.png)

以下是关于 `setfacl` 命令的常用操作：

![](img/5fa026b6083ec07b5885e443c49019cd_90.png)

*   **查看ACL**：`getfacl <文件名>`
*   **设置/修改ACL（针对文件）**：`setfacl -m u:<用户名>:<权限> <文件名>` 或 `setfacl -m g:<组名>:<权限> <文件名>`
*   **设置/修改ACL（递归针对目录）**：`setfacl -Rm u:<用户名>:<权限> <目录名>`
*   **删除单条ACL记录**：`setfacl -x u:<用户名> <文件名>`
*   **删除所有ACL记录**：`setfacl -b <文件名>`
*   **设置默认ACL（仅对目录有效，影响其后新建的文件）**：`setfacl -dm u:<用户名>:<权限> <目录名>`

![](img/5fa026b6083ec07b5885e443c49019cd_92.png)

---

## 第三题：设置目录的SGID权限

接下来，我们学习如何设置目录的特殊权限。本题重点考察SGID权限，它可以使在目录下创建的新文件自动继承目录的所属组。

![](img/5fa026b6083ec07b5885e443c49019cd_94.png)

题目要求如下：
1.  在 `/tmp` 目录下创建一个名为 `examdir` 的子目录。
2.  设置该目录的所属组为 `examuser`。
3.  该目录对 `examuser` 组的成员可读、可写、可访问（即拥有 `rwx` 权限）。
4.  在目录上设置SGID权限，使得任何用户在此目录中创建的文件，其所属组自动为 `examuser`。

![](img/5fa026b6083ec07b5885e443c49019cd_96.png)

以下是解题步骤：

![](img/5fa026b6083ec07b5885e443c49019cd_98.png)

```bash
# 1. 创建目录
mkdir /tmp/examdir

![](img/5fa026b6083ec07b5885e443c49019cd_100.png)

# 2. 设置目录的所属组为examuser
chown :examuser /tmp/examdir
# 或使用 chgrp examuser /tmp/examdir

![](img/5fa026b6083ec07b5885e443c49019cd_102.png)

![](img/5fa026b6083ec07b5885e443c49019cd_104.png)

# 3. 为所属组（examuser）设置读写执行权限
chmod g+rwx /tmp/examdir
# 或直接指定权限 chmod g=rwx /tmp/examdir

# 4. 设置SGID权限（2000）
chmod g+s /tmp/examdir
```

![](img/5fa026b6083ec07b5885e443c49019cd_106.png)

设置完成后，可以使用 `ls -ld /tmp/examdir` 命令查看目录详情，如果所属组的执行权限位置显示为 `s` 而非 `x`，则表明SGID权限设置成功。

---

![](img/5fa026b6083ec07b5885e443c49019cd_108.png)

![](img/5fa026b6083ec07b5885e443c49019cd_110.png)

## 第四题：创建指定UID的用户

![](img/5fa026b6083ec07b5885e443c49019cd_112.png)

![](img/5fa026b6083ec07b5885e443c49019cd_114.png)

最后，我们回顾如何创建具有特定用户ID（UID）的用户。

题目要求：创建一个用户 `mona`，其用户ID（UID）为 `21802`，并为其设置密码 `redhat`。

![](img/5fa026b6083ec07b5885e443c49019cd_116.png)

解题命令非常简单：

![](img/5fa026b6083ec07b5885e443c49019cd_118.png)

```bash
# 创建指定UID的用户
useradd -u 21802 mona

![](img/5fa026b6083ec07b5885e443c49019cd_120.png)

![](img/5fa026b6083ec07b5885e443c49019cd_122.png)

# 设置密码
echo “redhat” | passwd --stdin mona
```

![](img/5fa026b6083ec07b5885e443c49019cd_124.png)

![](img/5fa026b6083ec07b5885e443c49019cd_126.png)

---

![](img/5fa026b6083ec07b5885e443c49019cd_128.png)

## 总结

![](img/5fa026b6083ec07b5885e443c49019cd_130.png)

![](img/5fa026b6083ec07b5885e443c49019cd_132.png)

本节课中我们一起学习了四道综合练习题，回顾并实践了以下核心技能：
1.  **用户与组管理**：使用 `groupadd` 和 `useradd` 创建指定属性的组和用户，并使用 `passwd` 设置密码。
2.  **基本文件权限**：使用 `chown` 改变所有者和所属组，使用 `chmod` 为 `user/group/other` 设置读、写、执行权限。
3.  **高级文件权限控制（ACL）**：使用 `setfacl` 和 `getfacl` 命令，为特定的用户或组设置更精细的文件访问权限，这是RHCE考试中的重要考点。
4.  **特殊目录权限（SGID）**：使用 `chmod g+s` 为目录设置SGID位，使目录下新建的文件自动继承目录的所属组。

![](img/5fa026b6083ec07b5885e443c49019cd_134.png)

![](img/5fa026b6083ec07b5885e443c49019cd_136.png)

通过解决这些实际问题，相信大家对Linux系统中的权限管理有了更深入的理解。熟练掌握这些命令和概念，是成为一名合格系统管理员的基础。