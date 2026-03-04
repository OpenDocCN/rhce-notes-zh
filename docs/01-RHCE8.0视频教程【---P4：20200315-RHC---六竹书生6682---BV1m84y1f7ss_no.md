# RHCE8.0课程：01：用户与权限管理进阶

![](img/66a0f91e2f8068fcd88fa953b246740e_0.png)

在本节课中，我们将深入学习用户管理、密码管理、文件权限管理以及进程管理的基础知识。课程内容从用户和组的修改操作开始，逐步深入到权限的查看与修改、进程的查看与控制，旨在帮助初学者构建清晰的系统管理概念。

## 用户与组管理进阶

上一节我们介绍了用户的基本添加、修改和删除操作。本节中我们来看看如何修改用户的属性以及如何将用户加入到其他组中。

`useradd` 命令用于添加用户，而 `usermod` 命令则用于修改用户的属性。可以修改的属性包括登录名（`-l`）、家目录（`-d`）、登录解释器（`-s`）等。例如，`-s /sbin/nologin` 表示禁止该用户登录系统。

`userdel` 命令用于删除用户。直接使用 `userdel` 命令删除用户时，其家目录等信息可能仍然存在。若加上 `-r` 选项，则会删除用户的家目录、邮箱等所有相关文件。

**注意**：删除用户后，如果再次创建同名用户，系统可能会重用之前被回收的用户ID（UID）。这可能导致新用户继承旧用户文件的所有权。为了避免信息泄露，需要清理无主的文件。

以下是查找无主文件的方法：
*   使用 `find` 命令从根目录开始查找。
*   `-nouser` 选项查找没有所有者的文件。
*   `-nogroup` 选项查找没有所属组的文件。
*   使用 `-o` 表示“或”的关系，查找满足任一条件的文件。
*   将查找结果（包括正确和错误信息）重定向到 `/dev/null`。

例如：
```bash
find / -nouser -o -nogroup 2>/dev/null
```
后续课程会详细讲解如何删除这些找到的文件。

---

## 组管理

用户信息存储在 `/etc/passwd` 文件中，而组信息则存储在 `/etc/group` 文件中。该文件的结构为：`组名:密码占位符:组ID(GID):组成员列表`。

要查看用户属于哪些组，可以使用 `groups` 命令：
```bash
groups username
```

若要将用户加入到其他组（附属组），有以下几种方法：

1.  **使用 `usermod` 命令**：
    *   `usermod -G groupname username`：将用户加入到指定组，但此方式会**替换**用户现有的所有附属组（主组不变）。用户同时只能属于一个主组和一个附属组。
    *   `usermod -aG groupname username`：`-a` (append) 选项表示追加，使用户可以属于多个附属组。

2.  **使用 `gpasswd` 命令**：
    *   `gpasswd -a username groupname`：将指定用户加入到指定组。此方法没有组数量限制，不会替换现有附属组。
    *   `gpasswd -d username groupname`：将指定用户从指定组中移除。

创建组的命令是 `groupadd`。常用选项 `-g` 可以指定组ID (GID)。删除组的命令是 `groupdel`。

**重要**：如果一个组是某个用户的**主组**，则无法直接删除该组。必须先将该用户的主组更改为其他组，或删除该用户后，才能删除此组。

![](img/66a0f91e2f8068fcd88fa953b246740e_2.png)

---

## 密码管理

密码管理涉及三个方面：root用户修改密码、普通用户修改密码以及密码策略（时间）管理。

*   **root用户**：
    *   修改自身密码：`passwd`
    *   修改其他用户密码：`passwd username`
*   **普通用户**：
    *   只能修改自身密码：`passwd` （需要先输入旧密码）

![](img/66a0f91e2f8068fcd88fa953b246740e_4.png)

在考试或批量操作中，可以使用一条命令非交互式地修改密码，避免盲打错误：
```bash
echo "NewPassword" | passwd --stdin username
```
`echo` 命令输出新密码，通过管道 `|` 传递给 `passwd --stdin` 命令作为输入。

![](img/66a0f91e2f8068fcd88fa953b246740e_6.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_8.png)

密码策略通过 `/etc/shadow` 文件管理，可使用 `chage` 命令进行修改。常用选项包括：
*   `-d`：设置密码最后修改日期（时间戳或YYYY-MM-DD格式）。
*   `-E`：设置账户过期日期。
*   `-I`：设置密码过期后，账户被锁定前的宽限天数。
*   `-m`：设置密码最短使用天数。
*   `-M`：设置密码最长使用天数（有效期）。
*   `-W`：设置密码到期前的警告天数。
*   `-l`：列出用户的密码策略信息。

![](img/66a0f91e2f8068fcd88fa953b246740e_10.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_12.png)

例如，强制用户下次登录时必须修改密码：
```bash
chage -d 0 username
```

账户还可以被锁定和解锁：
*   锁定用户：`usermod -L username` 或 `passwd -l username`
*   解锁用户：`usermod -U username` 或 `passwd -u username`
*   查看状态：`passwd -S username`

用户被锁定后，即使输入正确密码也会显示认证失败。只有root用户可以登录被锁定的账户。

---

## 文件权限管理

如何查看文件权限？使用 `ls -l` 命令（可简写为 `ll`）。输出信息包含：文件类型与权限、链接数、所有者、所属组、大小、修改时间和文件名。

![](img/66a0f91e2f8068fcd88fa953b246740e_14.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_16.png)

权限分为三段：`所有者权限|所属组权限|其他用户权限`。每段三个字符，依次表示：
*   `r` (read)：读权限。对文件可查看内容，对目录可列出内容。
*   `w` (write)：写权限。对文件可修改内容，对目录可创建/删除文件。
*   `x` (execute)：执行权限。对文件可执行（如脚本），对目录可进入 (`cd`)。

**修改文件所有者和所属组**：
*   命令：`chown`
*   修改所有者：`chown newowner filename`
*   修改所属组：`chown :newgroup filename` 或 `chgrp newgroup filename`
*   同时修改：`chown newowner:newgroup filename`

![](img/66a0f91e2f8068fcd88fa953b246740e_18.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_20.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_22.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_24.png)

**修改文件权限**：
命令：`chmod`。有两种表示方法：

![](img/66a0f91e2f8068fcd88fa953b246740e_26.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_28.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_30.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_32.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_34.png)

1.  **字母法**：`chmod [ugoa][+-=][rwx] filename`
    *   `u`：所有者，`g`：所属组，`o`：其他用户，`a`：所有用户。
    *   `+`：添加权限，`-`：移除权限，`=`：设置精确权限。
    *   `r`， `w`， `x`：对应权限。
    *   示例：`chmod u+x file` （给所有者添加执行权限）

![](img/66a0f91e2f8068fcd88fa953b246740e_36.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_38.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_40.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_42.png)

2.  **数字法**：`chmod abc filename`
    *   `a`， `b`， `c` 各是一个数字，分别代表所有者、所属组、其他用户的权限。
    *   计算规则：`r=4`， `w=2`， `x=1`。将所需权限的数值相加即可。
    *   示例：`rwxr-x---` 表示为 `750` (7=4+2+1， 5=4+0+1， 0=0+0+0)。

![](img/66a0f91e2f8068fcd88fa953b246740e_44.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_46.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_48.png)

**默认权限与掩码 (umask)**：
*   系统默认：
    *   文件默认权限：`666` (rw-rw-rw-)
    *   目录默认权限：`777` (rwxrwxrwx)
*   用户创建文件或目录时的最终权限 = 默认权限 - umask值（注意：这里是“过滤”，非直接减运算）。
*   查看当前umask：`umask`
*   设置umask：`umask 022` （会话内临时生效）
*   在脚本或命令中临时设置umask：`(umask 022; touch file)`

![](img/66a0f91e2f8068fcd88fa953b246740e_50.png)

例如，umask为022时，创建的文件权限为 `644` (rw-r--r--)，目录权限为 `755` (rwxr-xr-x)。

![](img/66a0f91e2f8068fcd88fa953b246740e_52.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_54.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_56.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_58.png)

---

![](img/66a0f91e2f8068fcd88fa953b246740e_60.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_62.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_64.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_66.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_68.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_70.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_72.png)

## 进程管理基础

![](img/66a0f91e2f8068fcd88fa953b246740e_74.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_76.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_78.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_80.png)

**查看进程**：
*   `ps`：查看当前终端会话的进程。
*   `ps aux`：查看系统所有进程的详细信息。
*   `top`：动态实时查看进程信息（默认3秒刷新，按 `q` 退出）。使用 `top -d 秒数` 可调整刷新频率。
*   `uptime`：查看系统运行时间与平均负载。

![](img/66a0f91e2f8068fcd88fa953b246740e_82.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_84.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_86.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_88.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_90.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_92.png)

**查找进程号 (PID)**：
*   `ps aux | grep process_name`
*   `pgrep process_name`
*   `pidof process_name`

![](img/66a0f91e2f8068fcd88fa953b246740e_94.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_96.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_98.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_100.png)

**控制进程**：
*   启动后台进程：在命令末尾加 `&`，如 `firefox &`。
*   查看后台作业：`jobs`。
*   将前台进程挂起并放入后台：`Ctrl + Z`。
*   激活后台作业使其运行：`bg %作业号`。
*   将后台作业调至前台：`fg %作业号`。

![](img/66a0f91e2f8068fcd88fa953b246740e_102.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_104.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_106.png)

**终止进程**：
命令：`kill`
*   `kill -15 PID`：默认信号，友好终止（可被拦截处理）。
*   `kill -9 PID`：强制终止，不可拦截。
*   `kill -20 PID`：相当于 `Ctrl+Z`，停止进程。
*   `kill -2 PID`：相当于 `Ctrl+C`，中断进程。
*   终止后台作业：`kill -9 %作业号`

![](img/66a0f91e2f8068fcd88fa953b246740e_108.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_110.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_112.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_114.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_116.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_118.png)

---

![](img/66a0f91e2f8068fcd88fa953b246740e_120.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_122.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_124.png)

![](img/66a0f91e2f8068fcd88fa953b246740e_126.png)

本节课中我们一起学习了用户与组的进阶管理操作，掌握了密码策略的设置方法，理解了文件权限的查看、修改以及umask的作用，并初步了解了进程的查看、控制和终止方法。这些是Linux系统管理中非常核心的基础技能，需要多加练习以熟练掌握。