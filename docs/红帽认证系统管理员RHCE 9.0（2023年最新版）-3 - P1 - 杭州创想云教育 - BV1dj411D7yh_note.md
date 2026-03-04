# RHCE 9.0 认证课程：第3章：用户、组与权限管理

![](img/a01a2f7efbfe7e3c00895942f6a38641_1.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_3.png)

在本节课中，我们将要学习Linux系统中用户、组以及文件权限的核心管理知识。这是系统管理员日常工作中最基础且重要的部分，理解并掌握这些概念是迈向RHCE认证的关键一步。

![](img/a01a2f7efbfe7e3c00895942f6a38641_5.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_7.png)

## 回顾与概述

![](img/a01a2f7efbfe7e3c00895942f6a38641_9.png)

上一节我们介绍了用户管理的基础命令，本节中我们来看看组的管理以及文件权限的详细机制。

![](img/a01a2f7efbfe7e3c00895942f6a38641_11.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_13.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_15.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_17.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_19.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_21.png)

## 第六章：组的管理

![](img/a01a2f7efbfe7e3c00895942f6a38641_23.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_25.png)

组是用户的集合，用于简化权限分配。管理组的命令比用户管理命令更为简单。

![](img/a01a2f7efbfe7e3c00895942f6a38641_27.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_29.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_31.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_32.png)

### 创建组

![](img/a01a2f7efbfe7e3c00895942f6a38641_33.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_35.png)

创建组的命令是 `groupadd`。其基本用法是直接指定组名，也可以通过 `-g` 选项指定组的GID。

![](img/a01a2f7efbfe7e3c00895942f6a38641_37.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_39.png)

以下是创建组的命令示例：
```bash
groupadd -g 2000 webadmins
```
此命令创建了一个GID为2000，名为 `webadmins` 的组。组信息存储在 `/etc/group` 文件中，可以使用 `getent group [组名]` 命令查看。

![](img/a01a2f7efbfe7e3c00895942f6a38641_41.png)

### 修改组信息

修改组信息的命令是 `groupmod`。常用选项包括 `-g`（修改GID）和 `-n`（修改组名）。

![](img/a01a2f7efbfe7e3c00895942f6a38641_43.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_44.png)

以下是修改组信息的命令示例：
```bash
groupmod -g 3000 -n webadmin webadmins
```
此命令将 `webadmins` 组的GID改为3000，同时将组名改为 `webadmin`。

![](img/a01a2f7efbfe7e3c00895942f6a38641_46.png)

### 删除组

删除不再需要的组使用 `groupdel` 命令。删除前需确保组内没有成员。

以下是删除组的命令示例：
```bash
groupdel webadmin
```

### 用户与组的关联

![](img/a01a2f7efbfe7e3c00895942f6a38641_48.png)

除了直接管理组，也可以通过用户管理命令将用户加入或移出组。

![](img/a01a2f7efbfe7e3c00895942f6a38641_50.png)

*   **修改用户的主要组**：使用 `usermod -g [组名] [用户名]`。
*   **为用户添加附属组**：使用 `usermod -aG [组名] [用户名]`（`-a` 表示追加，非常重要）。
*   **临时切换有效组**：用户可以使用 `newgrp [组名]` 命令临时切换到另一个组（该组需有密码）。

![](img/a01a2f7efbfe7e3c00895942f6a38641_52.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_54.png)

## 用户密码与账户策略

![](img/a01a2f7efbfe7e3c00895942f6a38641_56.png)

用户密码信息及其策略是系统安全的重要组成部分。

![](img/a01a2f7efbfe7e3c00895942f6a38641_58.png)

### 密码存储文件

![](img/a01a2f7efbfe7e3c00895942f6a38641_60.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_61.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_63.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_65.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_67.png)

早期用户密码明文存储在 `/etc/passwd` 文件中，出于安全考虑，现已将加密后的密码移至 `/etc/shadow` 文件。

![](img/a01a2f7efbfe7e3c00895942f6a38641_69.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_70.png)

*   **`/etc/passwd` 文件**：存储用户基本信息，每行格式为 `用户名:密码占位符(x):UID:GID:描述信息:家目录:登录Shell`。
*   **`/etc/shadow` 文件**：存储加密密码和账户策略，普通用户无读取权限。每行包含多个由冒号分隔的字段，例如加密算法标识符、加密后的密码、上次修改密码的天数等。

![](img/a01a2f7efbfe7e3c00895942f6a38641_72.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_74.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_76.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_77.png)

密码加密使用“盐值”（salt）来增加破解难度。可以使用 `openssl passwd` 命令验证密码。

![](img/a01a2f7efbfe7e3c00895942f6a38641_79.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_81.png)

### 密码策略管理

![](img/a01a2f7efbfe7e3c00895942f6a38641_83.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_85.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_87.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_89.png)

密码策略管理有两种方式：系统全局配置和针对特定用户配置。

![](img/a01a2f7efbfe7e3c00895942f6a38641_91.png)

**1. 系统全局配置**
修改 `/etc/login.defs` 文件中的以下参数，对新创建的用户生效：
*   `PASS_MAX_DAYS`：密码最长使用天数。
*   `PASS_MIN_DAYS`：密码最短使用天数。
*   `PASS_WARN_AGE`：密码过期前警告天数。

![](img/a01a2f7efbfe7e3c00895942f6a38641_93.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_95.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_96.png)

**2. 针对用户配置**
使用 `chage` 命令管理特定用户的密码策略。

![](img/a01a2f7efbfe7e3c00895942f6a38641_98.png)

以下是 `chage` 命令的常用选项：
```bash
chage -m 1 -M 30 -W 3 -I 2 jack
```
*   `-m 1`：密码最短1天后可更改。
*   `-M 30`：密码最长有效30天。
*   `-W 3`：密码过期前3天开始警告。
*   `-I 2`：密码过期后，账户还有2天“宽限期”可以登录修改密码。
*   `-d 0`：强制用户下次登录时必须更改密码。

可以使用 `chage -l [用户名]` 查看用户的密码策略详情。

![](img/a01a2f7efbfe7e3c00895942f6a38641_100.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_102.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_104.png)

### 账户锁定与解锁

![](img/a01a2f7efbfe7e3c00895942f6a38641_106.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_108.png)

*   **锁定账户/密码**：`usermod -L [用户名]` 或 `passwd -l [用户名]`。
*   **解锁账户/密码**：`usermod -U [用户名]` 或 `passwd -u [用户名]`。

![](img/a01a2f7efbfe7e3c00895942f6a38641_110.png)

**重要提示**：在RHCSA课程中，学到的权限管理命令对 `root` 用户不生效。

![](img/a01a2f7efbfe7e3c00895942f6a38641_112.png)

## 第七章：文件权限管理

权限决定了用户或组对系统资源（文件和目录）的访问能力。

![](img/a01a2f7efbfe7e3c00895942f6a38641_114.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_116.png)

### 理解文件权限

![](img/a01a2f7efbfe7e3c00895942f6a38641_118.png)

使用 `ls -l` 命令可以查看文件的详细权限信息。输出结果类似 `-rwxr-xr--`，其含义如下：

![](img/a01a2f7efbfe7e3c00895942f6a38641_120.png)

1.  第一个字符表示文件类型（`-` 普通文件，`d` 目录，`l` 链接等）。
2.  后续9个字符分为三组，分别代表**文件所有者（u）**、**所属组（g）** 和**其他用户（o）** 的权限。
3.  每组三个字符依次表示**读（r）**、**写（w）**、**执行（x）** 权限，`-` 表示无此权限。

![](img/a01a2f7efbfe7e3c00895942f6a38641_122.png)

**权限对文件和目录的影响不同：**

![](img/a01a2f7efbfe7e3c00895942f6a38641_123.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_125.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_126.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_127.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_129.png)

| 权限 | 对文件的影响 | 对目录的影响 |
| :--- | :--- | :--- |
| **r (读)** | 可查看文件内容 | 可列出目录内容（需配合x权限） |
| **w (写)** | 可修改文件内容 | 可在目录内创建、删除、重命名文件（需配合x权限） |
| **x (执行)** | 可将文件作为程序运行 | 可进入（`cd`）该目录 |

![](img/a01a2f7efbfe7e3c00895942f6a38641_131.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_133.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_135.png)

**核心规则**：对于目录而言，`x`（执行）权限是基础。没有`x`权限，即使有`r`或`w`权限也无效。

![](img/a01a2f7efbfe7e3c00895942f6a38641_137.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_139.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_141.png)

### 更改文件权限

![](img/a01a2f7efbfe7e3c00895942f6a38641_143.png)

更改权限的命令是 `chmod`，有两种表示方法：符号法和数字法。

![](img/a01a2f7efbfe7e3c00895942f6a38641_145.png)

**1. 符号法**
语法：`chmod [who][operator][permissions] file...`
*   **who**: `u`（所有者），`g`（所属组），`o`（其他人），`a`（所有人，可省略）。
*   **operator**: `+`（添加），`-`（移除），`=`（精确设置）。
*   **permissions**: `r`， `w`， `x`。

![](img/a01a2f7efbfe7e3c00895942f6a38641_147.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_149.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_151.png)

以下是符号法示例：
```bash
chmod g+w file.txt        # 为所属组添加写权限
chmod o-r file.txt        # 移除其他人的读权限
chmod u=rwx,g=rx,o=r file.txt # 精确设置权限
chmod -R o+X directory/   # 递归地为目录添加执行权限，但仅对目录生效（大X）
```

**2. 数字法（八进制）**
将每组权限 `rwx` 视为一个二进制数（有权限为1，无则为0），再转换为八进制数。
*   `r` = 4 (2^2)
*   `w` = 2 (2^1)
*   `x` = 1 (2^0)

![](img/a01a2f7efbfe7e3c00895942f6a38641_153.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_155.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_157.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_158.png)

计算每组权限值后，按 `所有者|所属组|其他人` 的顺序组合。

![](img/a01a2f7efbfe7e3c00895942f6a38641_160.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_162.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_164.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_166.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_168.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_170.png)

以下是数字法示例：
```bash
chmod 755 script.sh  # rwxr-xr-x
chmod 644 document.txt # rw-r--r--
chmod 600 secret.key # rw-------
```

**权限管理原则**：遵循最小权限原则，只授予完成工作所必需的最低权限。

![](img/a01a2f7efbfe7e3c00895942f6a38641_172.png)

### 更改文件所有者和所属组

*   **更改所有者**：`chown [新所有者] 文件`
*   **更改所属组**：`chown :[新所属组] 文件` 或 `chgrp [新所属组] 文件`
*   **同时更改所有者和所属组**：`chown [新所有者]:[新所属组] 文件`
*   **递归更改**：对目录及其内容递归操作，使用 `-R` 选项。

### 特殊权限

除了基本的rwx权限，还有三种特殊权限位。

![](img/a01a2f7efbfe7e3c00895942f6a38641_174.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_176.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_178.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_180.png)

**1. SUID (Set User ID)**
*   **作用对象**：可执行的二进制程序。
*   **作用**：当用户执行此程序时，进程的有效用户ID（EUID）将被设置为程序文件的所有者，而非执行者本人。
*   **典型例子**：`/usr/bin/passwd`。普通用户借此可以修改自己的密码（实质是以root身份修改`/etc/shadow`文件）。
*   **设置方法**：
    *   符号法：`chmod u+s file`
    *   数字法：在普通权限前加 `4`，如 `4755` (`rwsr-xr-x`)。

![](img/a01a2f7efbfe7e3c00895942f6a38641_182.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_184.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_185.png)

**2. SGID (Set Group ID)**
*   **作用对象**：可执行程序 **或** 目录。
*   **作用**：
    *   对程序：执行时进程的有效组ID（EGID）设置为程序文件的所属组。
    *   对目录：在该目录下新建的文件或子目录，会自动继承该目录的所属组。
*   **典型例子**：`/var/log/journal` 目录，用于集中管理日志文件。
*   **设置方法**：
    *   符号法：`chmod g+s directory`
    *   数字法：在普通权限前加 `2`，如 `2770` (`rwxrws---`)。

![](img/a01a2f7efbfe7e3c00895942f6a38641_187.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_189.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_191.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_193.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_195.png)

**3. Sticky Bit (粘滞位)**
*   **作用对象**：目录。
*   **作用**：在具有写权限的目录中，用户只能删除或重命名自己创建的文件，不能删除其他用户的文件。
*   **典型例子**：系统的临时目录 `/tmp`。
*   **设置方法**：
    *   符号法：`chmod o+t directory`
    *   数字法：在普通权限前加 `1`，如 `1777` (`rwxrwxrwt`)。

## 总结

![](img/a01a2f7efbfe7e3c00895942f6a38641_197.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_198.png)

![](img/a01a2f7efbfe7e3c00895942f6a38641_200.png)

本节课中我们一起学习了Linux系统管理中关于组、用户密码策略以及文件权限的核心知识。我们掌握了如何创建、修改和删除组；理解了密码存储机制和如何通过 `chage` 命令管理账户策略；深入学习了文件普通权限（rwx）的含义、查看和修改方法（`chmod`， `chown`， `chgrp`），并了解了三种特殊权限（SUID， SGID， Sticky Bit）的作用和应用场景。牢记**最小权限原则**和**root用户不受普通权限约束**这两个关键点，是进行安全、有效系统管理的基础。