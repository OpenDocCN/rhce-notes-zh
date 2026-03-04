# RHCSA 红帽系统管理员培训：P6：管理用户、组与文件权限

![](img/8cb93bafcaec96b0241c0f999ebbce2f_1.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_3.png)

在本节课中，我们将要学习Linux系统中用户、组以及文件权限的核心管理知识。这是系统管理员必须掌握的基础技能，关系到系统的安全性和资源管理。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_5.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_7.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_8.png)

## 第五章：管理本地用户和组

![](img/8cb93bafcaec96b0241c0f999ebbce2f_10.png)

### 5.1 用户与组的基本概念

![](img/8cb93bafcaec96b0241c0f999ebbce2f_12.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_14.png)

在Linux系统中，用户和组是权限管理的基础。一个文件可以被一个用户所拥有，一个进程也可以被一个用户所执行。理解这两者的关系至关重要。

#### 查看进程与文件所有者

![](img/8cb93bafcaec96b0241c0f999ebbce2f_16.png)

要查看系统中正在运行的进程，可以使用 `ps` 命令。添加 `aux` 选项可以查看所有用户的所有进程的详细信息。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_18.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_20.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_22.png)

```bash
ps aux
```

![](img/8cb93bafcaec96b0241c0f999ebbce2f_24.png)

要查看文件的所有者，可以使用 `ls -l` 命令。输出结果中会显示每个文件的拥有者。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_26.png)

```bash
ls -l
```

#### 用户信息的存储

![](img/8cb93bafcaec96b0241c0f999ebbce2f_28.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_30.png)

在Linux中，一切皆文件。所有用户的信息存储在 `/etc/passwd` 文件中。我们可以使用 `cat` 命令查看，但通常不建议直接手动修改此文件。

```bash
cat /etc/passwd
```

![](img/8cb93bafcaec96b0241c0f999ebbce2f_32.png)

更直观地查看用户信息（如用户ID、组ID等）可以使用 `id` 命令。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_34.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_36.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_38.png)

```bash
id username
```

![](img/8cb93bafcaec96b0241c0f999ebbce2f_40.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_42.png)

#### 组信息的存储

组信息存储在 `/etc/group` 文件中。组分为两种：主组和附属组。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_44.png)

*   **主组**：用户创建时自动生成的同名组。
*   **附属组**：用户可以被添加到其他组中，这些组称为附属组。附属组可以赋予用户额外的权限，例如，`wheel` 组的成员可以使用 `sudo` 命令临时获得root权限。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_46.png)

查看组信息：
```bash
cat /etc/group
```

### 5.2 切换用户与超级用户权限

![](img/8cb93bafcaec96b0241c0f999ebbce2f_48.png)

root用户拥有系统的最高权限。为了安全起见，我们通常使用普通用户登录，在需要时再切换权限。

#### 使用 `su` 命令切换用户

`su` 命令用于切换用户身份。直接输入 `su` 会切换到root用户，`su -` 则会同时切换用户和环境变量，这是一个更完整的切换方式。

```bash
su - # 切换到root用户并加载其环境变量
su - username # 切换到指定用户并加载其环境变量
```

![](img/8cb93bafcaec96b0241c0f999ebbce2f_50.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_52.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_54.png)

#### 使用 `sudo` 命令临时提权

`sudo` 允许被授权的普通用户以root权限执行特定命令，并且所有操作会被记录，便于审计。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_56.png)

配置 `sudo` 权限需要编辑 `/etc/sudoers` 文件，**强烈建议使用 `visudo` 命令编辑**，因为它会进行语法检查。

1.  将用户添加到 `wheel` 组（该组默认拥有 `sudo` 权限）：
    ```bash
    usermod -aG wheel username
    ```
2.  之后，该用户就可以在命令前加 `sudo` 来临时获得root权限：
    ```bash
    sudo command
    ```

![](img/8cb93bafcaec96b0241c0f999ebbce2f_58.png)

`/etc/sudoers` 文件中的配置行示例：
```
%wheel ALL=(ALL) ALL # wheel组成员可以在任何主机上执行任何命令
username ALL=(ALL) NOPASSWD: ALL # 指定用户执行sudo时无需密码
```

![](img/8cb93bafcaec96b0241c0f999ebbce2f_60.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_62.png)

### 5.3 用户的增删改查与管理技巧

上一节我们介绍了用户和权限的概念，本节中我们来看看如何具体管理用户账户。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_64.png)

以下是用户管理的基本命令：

*   **添加用户**：`useradd`
*   **修改用户**：`usermod`
*   **设置密码**：`passwd`
*   **删除用户**：`userdel`

常用操作示例：
```bash
useradd user01 # 创建用户user01
passwd user01 # 为user01设置密码
usermod -aG wheel user01 # 将user01添加到wheel组
usermod -L user01 # 锁定用户user01账户
usermod -U user01 # 解锁用户user01账户
userdel -r user01 # 删除用户user01及其主目录
```

![](img/8cb93bafcaec96b0241c0f999ebbce2f_66.png)

**用户标识符UID**：
*   `0`： root用户。
*   `1-999`： 系统用户，由系统或安装的软件创建。
*   `1000+`： 普通用户，由管理员创建。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_68.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_70.png)

**安全提示**： 定期检查系统中是否存在UID为0的非root用户，这可能是系统被入侵的迹象。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_72.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_74.png)

### 5.4 管理用户密码与密码策略

![](img/8cb93bafcaec96b0241c0f999ebbce2f_76.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_78.png)

用户密码经过加密后存储在 `/etc/shadow` 文件中，该文件普通用户无法读取，增强了安全性。

查看密码文件：
```bash
sudo cat /etc/shadow
```

`/etc/shadow` 文件每一列的含义包括：用户名、加密的密码、上次修改密码的日期、密码最短有效期、密码最长有效期、密码过期前警告天数、密码过期后宽限天数、账户失效日期等。

可以使用 `chage` 命令来修改这些密码策略。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_80.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_82.png)

```bash
chage -l username # 列出用户的密码策略详情
chage -M 90 username # 设置密码最长有效期为90天
```

![](img/8cb93bafcaec96b0241c0f999ebbce2f_84.png)

系统的默认密码策略定义在 `/etc/login.defs` 文件中，可以在此修改密码的最小长度、最大天数等默认值。

### 5.5 管理组

组的管理相对简单，主要命令如下：

![](img/8cb93bafcaec96b0241c0f999ebbce2f_86.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_88.png)

```bash
groupadd group_name # 创建新组
groupdel group_name # 删除组
usermod -aG group_name username # 将用户添加到附属组
gpasswd -d username group_name # 将用户从组中移除
```

## 第六章：管理系统文件权限

![](img/8cb93bafcaec96b0241c0f999ebbce2f_90.png)

文件权限是Linux安全模型的基石。它控制着用户对文件和目录的访问能力。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_92.png)

### 6.1 理解文件权限

![](img/8cb93bafcaec96b0241c0f999ebbce2f_94.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_96.png)

使用 `ls -l` 命令查看文件详细信息时，第一列就是权限标识，例如 `-rwxr-xr--`。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_98.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_100.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_102.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_103.png)

*   **第一个字符**：表示文件类型（`-` 普通文件，`d` 目录，`l` 链接等）。
*   **后续9个字符**：每3个一组，分别代表**文件所有者（u）**、**所属组（g）** 和**其他用户（o）** 的权限。
*   **权限字符**：`r` (读)， `w` (写)， `x` (执行)。`-` 表示无此权限。

**权限对文件和目录的影响不同**：

![](img/8cb93bafcaec96b0241c0f999ebbce2f_105.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_107.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_109.png)

| 权限 | 对文件的影响 | 对目录的影响 |
| :--- | :--- | :--- |
| **r (读)** | 可读取文件内容 | 可列出目录中的文件列表 |
| **w (写)** | 可修改文件内容 | 可在目录内创建、删除、重命名文件 |
| **x (执行)** | 可将文件作为程序执行 | 可进入（`cd`）该目录 |

![](img/8cb93bafcaec96b0241c0f999ebbce2f_111.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_113.png)

### 6.2 修改文件权限与所有权

![](img/8cb93bafcaec96b0241c0f999ebbce2f_115.png)

#### 使用 `chmod` 修改权限

有两种模式：
1.  **符号模式**：使用 `u/g/o/a` 和 `+/-/=` 进行操作。
    ```bash
    chmod u+x file # 给所有者增加执行权限
    chmod g-w file # 移除所属组的写权限
    chmod o=r file # 设置其他用户权限为只读
    chmod a+x file # 给所有用户增加执行权限
    ```
2.  **数字（八进制）模式**：每位数字代表一组权限（u/g/o）。`r=4`, `w=2`, `x=1`，数字相加即得权限值。
    ```bash
    chmod 755 file # u=rwx (7), g=rx (5), o=rx (5)
    chmod 644 file # u=rw (6), g=r (4), o=r (4)
    ```
    **递归修改**目录及其内部所有文件的权限：
    ```bash
    chmod -R 755 directory/
    ```

![](img/8cb93bafcaec96b0241c0f999ebbce2f_117.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_119.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_121.png)

#### 使用 `chown` 和 `chgrp` 修改所有者和所属组

```bash
chown new_owner file # 修改文件所有者
chown new_owner:new_group file # 同时修改所有者和所属组
chown :new_group file # 仅修改所属组
chgrp new_group file # 修改文件所属组（功能同上行）
```
递归修改选项同样适用 `-R`。

### 6.3 特殊权限：SUID， SGID， Sticky Bit

![](img/8cb93bafcaec96b0241c0f999ebbce2f_123.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_125.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_127.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_129.png)

除了基本的rwx，还有三个特殊权限位。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_131.png)

1.  **SUID (Set User ID)**：当设置在**可执行文件**上时，任何用户执行此文件期间，都会临时获得文件**所有者**的权限。通常用于需要普通用户临时提权的程序，如 `passwd`。
    ```bash
    chmod u+s /usr/bin/passwd # 设置SUID位
    ls -l /usr/bin/passwd # 查看，所有者执行位会显示为 ‘s’
    ```

2.  **SGID (Set Group ID)**：
    *   设置在**可执行文件**上时，类似SUID，执行时获得文件**所属组**的权限。
    *   设置在**目录**上时，在该目录下创建的任何新文件或子目录，其所属组都会自动继承该目录的所属组，便于协作。
    ```bash
    chmod g+s /shared_directory # 在目录上设置SGID位
    ```

3.  **Sticky Bit (粘滞位)**：仅对**目录**有效。设置在目录上时，即使目录权限为777，用户也只能删除或重命名**自己创建**的文件，不能删除他人的文件。常用于临时文件夹如 `/tmp`。
    ```bash
    chmod o+t /tmp # 设置Sticky Bit位
    ls -ld /tmp # 查看，其他用户执行位会显示为 ‘t’
    ```

**使用数字模式设置特殊权限**：特殊权限位位于普通三位权限数字之前，构成四位数字。SUID=4，SGID=2，Sticky Bit=1。
```bash
chmod 4755 file # 设置SUID，普通权限755
chmod 2770 directory # 设置SGID，普通权限770
chmod 1777 /tmp # 设置Sticky Bit，普通权限777
```

### 6.4 默认权限与umask

新创建的文件和目录都有一个默认权限。这个默认权限由系统的 `umask`（权限掩码）值决定。

**umask** 是一个反向掩码，它指定了需要从完全权限中“扣除”的权限。
*   文件的完全权限是 `666` (rw-rw-rw-)。
*   目录的完全权限是 `777` (rwxrwxrwx)。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_133.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_135.png)

![](img/8cb93bafcaec96b0241c0f999ebbce2f_136.png)

查看当前umask值：
```bash
umask
```
通常普通用户的umask为 `0002`，root用户的umask为 `0022`。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_138.png)

计算默认权限：
*   用户创建文件：`666 - 022 = 644` (rw-r--r--)
*   用户创建目录：`777 - 022 = 755` (rwxr-xr-x)

可以临时修改umask：
```bash
umask 027
```
永久修改需要将umask设置写入shell的配置文件中，如 `~/.bashrc` 或 `/etc/profile`。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_140.png)

---

![](img/8cb93bafcaec96b0241c0f999ebbce2f_142.png)

**本节课中我们一起学习了**：
1.  Linux中用户和组的概念、存储位置及管理命令（`useradd`, `usermod`, `passwd`, `groupadd`等）。
2.  超级用户root的权限管理，以及如何使用 `su` 和 `sudo` 安全地进行权限切换。
3.  文件权限的核心机制，包括如何解读 `ls -l` 的输出，如何使用 `chmod`、`chown` 修改权限和所有权。
4.  三个特殊权限位（SUID， SGID， Sticky Bit）的作用与设置方法。
5.  系统默认权限是如何通过 `umask` 值来控制的。

![](img/8cb93bafcaec96b0241c0f999ebbce2f_144.png)

掌握这些内容是成为一名合格系统管理员的必经之路，请务必通过实践加深理解。