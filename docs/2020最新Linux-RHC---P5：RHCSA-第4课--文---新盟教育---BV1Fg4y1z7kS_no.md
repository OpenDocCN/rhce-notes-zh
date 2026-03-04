# Linux-RHCSA入门实战课：P5：RHCSA-第4课 - 文件类型与权限管理 🗂️🔐

![](img/ad96fee1421f38e1d05770da8ca83b08_1.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_3.png)

在本节课中，我们将要学习Linux系统中至关重要的文件类型与权限管理知识。理解这些概念是安全、高效地使用Linux系统的基础。

![](img/ad96fee1421f38e1d05770da8ca83b08_5.png)

## 文件类型 📄

![](img/ad96fee1421f38e1d05770da8ca83b08_7.png)

上一节我们介绍了Linux用户管理，但用户管理需要结合权限才能发挥作用。本节中我们来看看如何设置和管理权限，首先从识别文件类型开始。

![](img/ad96fee1421f38e1d05770da8ca83b08_9.png)

Linux系统遵循“一切皆文件”的理念。所有内容主要分为两种类型：普通文件和特殊文件。

*   **普通文件**：所有文本文件都属于普通文件。例如，`.txt`、`.doc`、`.c`等文件。在`ls -l`命令的输出中，第一列以短横线 `-` 开头。
*   **特殊文件**：包括目录、链接文件、设备文件等，它们不以短横线开头。

![](img/ad96fee1421f38e1d05770da8ca83b08_11.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_13.png)

以下是Linux中常见的特殊文件类型：

*   **目录 (d)**：表示一个目录，可以包含其他文件。
*   **链接文件 (l)**：特指软链接（符号链接），类似于快捷方式。
*   **块设备文件 (b)**：代表存储类设备，如硬盘、U盘。数据以“块”为单位进行读写。
*   **字符设备文件 (c)**：代表字符类设备，如终端。数据以字符流形式进行传输。可以使用 `tty` 命令查看当前终端设备文件。
*   **管道文件 (p)**：用于进程间通信，与管道符相关。
*   **套接字文件 (s)**：安全套接字文件，用于网络通信。服务监听端口时会在文件系统层面创建此类文件，例如MySQL的 `mysql.sock`。

## 权限基础 🔑

理解了文件类型后，我们来看权限管理。Linux的普通权限主要有三种：

![](img/ad96fee1421f38e1d05770da8ca83b08_15.png)

*   **r (read)**：读取权限。
*   **w (write)**：写入权限。
*   **x (execute)**：执行权限。

![](img/ad96fee1421f38e1d05770da8ca83b08_17.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_19.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_20.png)

此外，短横线 `-` 表示无相应权限。

![](img/ad96fee1421f38e1d05770da8ca83b08_22.png)

**权限对于文件和目录的含义不同：**

![](img/ad96fee1421f38e1d05770da8ca83b08_24.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_26.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_28.png)

*   **对文件**：
    *   `r`：可以读取文件内容。
    *   `w`：可以修改文件内容。
    *   `x`：可以执行该文件（通常是命令或脚本）。
*   **对目录**：
    *   `r`：可以列出目录内的文件列表。
    *   `w`：可以在目录内创建、删除、重命名文件。
    *   `x`：可以进入 (`cd`) 该目录。

![](img/ad96fee1421f38e1d05770da8ca83b08_30.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_32.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_34.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_36.png)

**一个重要概念**：能否删除一个文件，**不取决于文件自身的权限**，而是取决于**文件所在目录**是否对用户有 `w` 权限。

![](img/ad96fee1421f38e1d05770da8ca83b08_38.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_39.png)

## 权限的归属与表示 👥

![](img/ad96fee1421f38e1d05770da8ca83b08_41.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_42.png)

在 `ls -l` 命令的输出中，权限位共有10位。第一位表示文件类型，后9位每3位一组，分别代表：

![](img/ad96fee1421f38e1d05770da8ca83b08_44.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_46.png)

*   **前3位**：文件**属主 (user)** 的权限。
*   **中间3位**：文件**属组 (group)** 的权限。
*   **后3位**：**其他用户 (other)** 的权限。

这9位权限可以用数字（八进制）简洁表示：
*   `r` = 4
*   `w` = 2
*   `x` = 1
*   `-` = 0

每组权限的数字是其包含权限值的**和**。例如：
*   `rwx` = 4+2+1 = 7
*   `r-x` = 4+0+1 = 5
*   `rw-` = 4+2+0 = 6

![](img/ad96fee1421f38e1d05770da8ca83b08_48.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_50.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_52.png)

因此，权限 `rwxr-xr--` 可以表示为数字 `754`。

![](img/ad96fee1421f38e1d05770da8ca83b08_54.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_56.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_57.png)

## 默认权限与umask 🎭

![](img/ad96fee1421f38e1d05770da8ca83b08_59.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_60.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_62.png)

新创建的文件和目录有默认权限：
*   文件默认权限：`666` (rw-rw-rw-)
*   目录默认权限：`777` (rwxrwxrwx)

![](img/ad96fee1421f38e1d05770da8ca83b08_64.png)

但系统会用一个叫 **umask（权限掩码）** 的值来“过滤”掉一些权限，得到最终默认权限。计算公式为：
**最终默认权限 = 满位权限 - umask**

![](img/ad96fee1421f38e1d05770da8ca83b08_66.png)

查看当前umask：
```bash
umask
```
通常输出为 `0022`。第一位是特殊权限位，后三位 `022` 是普通权限掩码。

![](img/ad96fee1421f38e1d05770da8ca83b08_68.png)

例如，创建文件：`666 - 022 = 644` (rw-r--r--)
创建目录：`777 - 022 = 755` (rwxr-xr-x)

![](img/ad96fee1421f38e1d05770da8ca83b08_70.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_72.png)

可以修改umask来改变默认权限，但不建议在生产环境中随意修改。

## 修改权限与归属 🛠️

![](img/ad96fee1421f38e1d05770da8ca83b08_74.png)

### 1. 修改权限 (chmod)

![](img/ad96fee1421f38e1d05770da8ca83b08_76.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_78.png)

命令 `chmod` 用于修改文件或目录的权限。常用参数 `-R` 表示递归操作，影响目录及其内部所有内容。

![](img/ad96fee1421f38e1d05770da8ca83b08_80.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_82.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_84.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_86.png)

**数字方式（推荐）**：
```bash
chmod 755 filename  # 将文件权限设置为 rwxr-xr-x
chmod -R 644 dirname # 递归将目录内所有文件权限设为 rw-r--r--
```

![](img/ad96fee1421f38e1d05770da8ca83b08_88.png)

**符号方式**：
使用 `u`(user), `g`(group), `o`(other), `a`(all) 结合 `+`(添加), `-`(撤销), `=`(覆盖) 来操作。
```bash
chmod u+x file      # 为属主添加执行权限
chmod g-w,o-r file  # 撤销属组的写权限和其他用户的读权限
chmod a=rw file     # 将所有权限覆盖为读写（无执行）
```

### 2. 修改归属 (chown & chgrp)

![](img/ad96fee1421f38e1d05770da8ca83b08_90.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_92.png)

命令 `chown` 用于修改文件的属主和属组。
```bash
chown user1 file          # 将文件属主改为 user1
chown user1:group1 file   # 同时修改属主为user1，属组为group1 (冒号或点分隔)
chown :group1 file        # 仅修改属组为group1
chown -R user1 dir        # 递归修改目录归属
```

![](img/ad96fee1421f38e1d05770da8ca83b08_94.png)

命令 `chgrp` 专门用于修改文件的属组。
```bash
chgrp group1 file
```

![](img/ad96fee1421f38e1d05770da8ca83b08_96.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_98.png)

## 特殊权限 ⚡

![](img/ad96fee1421f38e1d05770da8ca83b08_99.png)

除了rwx，还有三种特殊权限，占用权限位的第一位（八进制数字的千位）。

*   **SUID (Set UID, 4)**：当具有SUID权限的**可执行文件**被运行时，进程将以**文件属主**的身份运行，而不是执行者。例如 `/usr/bin/passwd`。
    *   设置：`chmod 4755 file` 或 `chmod u+s file`
    *   显示：属主执行位变为 `s` (如果原有x权限) 或 `S` (如果原无x权限)。

![](img/ad96fee1421f38e1d05770da8ca83b08_101.png)

*   **SGID (Set GID, 2)**：
    *   对**可执行文件**：运行时以**文件属组**身份运行。
    *   对**目录**：在该目录下新建的文件，其属组会自动继承目录的属组。
    *   设置：`chmod 2755 file` 或 `chmod g+s file`

![](img/ad96fee1421f38e1d05770da8ca83b08_103.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_105.png)

*   **Sticky Bit (粘滞位, 1)**：仅对目录有效。具有写权限的用户**仅能删除自己拥有的文件**，无法删除其他用户的文件。常用于共享目录如 `/tmp`。
    *   设置：`chmod 1777 dir` 或 `chmod o+t dir`
    *   显示：其他用户执行位变为 `t` 或 `T`。

![](img/ad96fee1421f38e1d05770da8ca83b08_107.png)

## 文件隐藏属性 🕵️♂️

![](img/ad96fee1421f38e1d05770da8ca83b08_109.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_111.png)

除了上述权限，文件还有隐藏属性，使用 `ls -l` 无法查看，需要使用 `lsattr` 和 `chattr` 命令。

![](img/ad96fee1421f38e1d05770da8ca83b08_112.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_114.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_116.png)

*   `lsattr file`：查看文件的隐藏属性。
*   `chattr +i file`：设置文件为**不可修改**（不能删除、重命名、修改内容，不能添加链接）。
*   `chattr +a file`：设置文件为**仅追加**（只能向文件末尾添加内容，不能覆盖或删除）。
*   `chattr -i file`：取消不可修改属性。

这些属性常用于保护重要的系统文件或日志文件。

![](img/ad96fee1421f38e1d05770da8ca83b08_118.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_120.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_122.png)

## 访问控制列表 (ACL) 📋

![](img/ad96fee1421f38e1d05770da8ca83b08_124.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_126.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_128.png)

标准的UGO（用户-组-其他）权限模型有时不够灵活。ACL允许为**单个用户或组**设置更精细的权限。

![](img/ad96fee1421f38e1d05770da8ca83b08_130.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_132.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_134.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_136.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_138.png)

*   **查看ACL**：`getfacl file`
*   **设置ACL**：`setfacl`
    ```bash
    setfacl -m u:username:rwx file   # 为用户username添加rwx权限
    setfacl -m g:groupname:rx file   # 为组groupname添加rx权限
    setfacl -x u:username file       # 删除用户username的ACL条目
    setfacl -b file                  # 删除文件所有ACL条目
    ```
*   **递归设置**：`setfacl -R -m u:username:rwx dir/`

![](img/ad96fee1421f38e1d05770da8ca83b08_140.png)

设置了ACL的文件，在 `ls -l` 显示的权限位后会出现一个加号 `+`。

![](img/ad96fee1421f38e1d05770da8ca83b08_142.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_144.png)

## 用户切换与提权 👑

### 1. su (Switch User)

![](img/ad96fee1421f38e1d05770da8ca83b08_146.png)

`su` 命令用于切换用户身份。
```bash
su username          # 切换到username，环境变量可能不变
su - username        # 完全切换到username，并加载其环境变量
su -                 # 切换到root用户
```

![](img/ad96fee1421f38e1d05770da8ca83b08_148.png)

### 2. sudo

![](img/ad96fee1421f38e1d05770da8ca83b08_150.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_152.png)

`sudo` 命令允许普通用户以**其他用户（通常是root）** 的身份执行命令，执行前需要验证当前用户密码（非root密码）。

![](img/ad96fee1421f38e1d05770da8ca83b08_154.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_156.png)

**配置sudo**：编辑 `/etc/sudoers` 文件，**强烈建议使用 `visudo` 命令编辑**，因为它会检查语法。
配置格式：
```
用户    主机=(可切换到的身份)    可执行的命令
```
示例：
```
cisco    ALL=(ALL)       ALL                     # cisco用户可在任何主机以任何身份执行任何命令
cisco    ALL=(root)      /usr/bin/systemctl      # cisco用户只能以root身份执行systemctl命令
%wheel   ALL=(ALL)       NOPASSWD: ALL           # wheel组用户可无密码执行任何命令
```

![](img/ad96fee1421f38e1d05770da8ca83b08_158.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_160.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_162.png)

**使用sudo**：
```bash
sudo command          # 以root身份执行command
sudo -u username command # 以username身份执行command
```

![](img/ad96fee1421f38e1d05770da8ca83b08_164.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_166.png)

![](img/ad96fee1421f38e1d05770da8ca83b08_168.png)

## 总结 📚

本节课中我们一起学习了Linux系统管理的核心——文件类型与权限体系。

我们首先区分了普通文件与各种特殊文件（目录、链接、设备等）。然后深入讲解了基础的读(r)、写(w)、执行(x)权限对文件和目录的不同含义，并理解了权限的归属（属主、属组、其他用户）及其数字表示法。

我们探讨了umask如何影响默认权限，并掌握了使用 `chmod` 和 `chown`/`chgrp` 修改权限与归属的方法。接着，我们学习了功能强大的特殊权限（SUID、SGID、Sticky Bit）和用于额外保护的文件隐藏属性。

为了突破标准权限模型的限制，我们引入了访问控制列表（ACL），它可以为特定用户或组设置独立权限。最后，我们了解了如何在用户之间切换 (`su`) 以及如何安全地授予普通用户管理命令的执行权 (`sudo`)。

![](img/ad96fee1421f38e1d05770da8ca83b08_170.png)

熟练掌握这些知识，是成为一名合格的Linux系统管理员的重要一步。请务必通过实践练习来巩固理解。