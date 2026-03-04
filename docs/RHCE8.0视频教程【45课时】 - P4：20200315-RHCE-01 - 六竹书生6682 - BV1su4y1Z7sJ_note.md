# RHCE8.0视频教程：P4：用户、组、权限与进程管理

![](img/92fce80411bf8dc6572ad123620669c0_0.png)

在本节课中，我们将要学习Linux系统管理中的几个核心概念：用户与组的管理、文件权限的设置以及进程的基本操作。这些是系统管理员日常工作中必须掌握的技能。

## 用户与组管理进阶

上一节我们介绍了用户的基本添加、修改和删除操作。本节中我们来看看如何修改用户的附属组以及组的管理。

### 修改用户属性

`useradd`命令用于添加用户，而`usermod`命令用于修改用户的属性。以下是`usermod`的一些常用选项：
*   `-c`：修改用户的注释信息（如全名）。
*   `-d`：修改用户的家目录路径。
*   `-s`：修改用户的默认登录Shell。例如，`/sbin/nologin`表示禁止该用户登录系统。
*   `-g`：修改用户的主组（GID）。
*   `-G`：修改用户的附属组列表。**注意**：单独使用`-G`会使用新的组列表完全替换旧的附属组列表。

### 将用户加入其他组

有时需要将用户加入到另一个组中，以赋予其额外的权限。

以下是两种主要方法：

**方法一：使用`usermod`命令**
此命令从用户角度出发，为其添加附属组。
```bash
usermod -aG wheel tk_edu
```
*   `-a`：表示追加（Append），必须与`-G`一起使用，否则会覆盖原有的附属组列表。
*   `-G`：指定要加入的组名。
*   执行后，用户`tk_edu`将被添加到`wheel`组中。

**方法二：使用`gpasswd`命令**
此命令从组的角度出发，为组添加成员。
```bash
gpasswd -a tk_edu sshd
```
*   `-a`：添加（Add）用户到组。
*   执行后，用户`tk_edu`被添加到`sshd`组中。

要从组中移除用户，使用`gpasswd -d`命令：
```bash
gpasswd -d tk_edu sshd
```

### 组的管理

*   **创建组**：使用`groupadd`命令。`-g`选项可以指定组的GID。
    ```bash
    groupadd hangzhou_tk_edu
    ```
*   **删除组**：使用`groupdel`命令。
    ```bash
    groupdel hangzhou_tk_edu
    ```
    **注意**：如果一个组是某个用户的主组，则无法直接删除。必须先修改该用户的主组。

### 用户删除的潜在问题

删除用户时（`userdel`），如果未使用`-r`选项（删除家目录和邮件池），该用户创建的文件会保留在系统中。如果之后创建了同名的新用户，系统可能会回收利用旧的UID，导致新用户“继承”了旧用户文件的所有权，存在信息泄露风险。

**解决方案**：在删除用户后，查找并清理无属主的文件。
```bash
find / -nouser -o -nogroup 2>/dev/null
```
*   `find`：查找命令。
*   `/`：从根目录开始查找。
*   `-nouser`：查找没有属主的文件。
*   `-o`：逻辑“或”。
*   `-nogroup`：查找没有属组的文件。
*   `2>/dev/null`：将错误信息重定向到“黑洞”，只显示正常结果。

---

## 密码管理

密码管理涉及密码的修改、策略设置以及账户的锁定与解锁。

### 修改密码

*   **root用户**：可以修改任何用户的密码，且无需验证旧密码。
    ```bash
    passwd redhat # 修改redhat用户的密码
    passwd        # 修改root自身的密码
    ```
*   **普通用户**：只能修改自己的密码，且必须输入旧密码。
    ```bash
    passwd
    ```

**技巧：使用一条命令设置密码**
在脚本或考试中，为避免交互式输入错误，可以使用以下命令：
```bash
echo "NewPassword123" | passwd --stdin redhat
```
这条命令将`NewPassword123`作为新密码设置给`redhat`用户。

### 密码策略与账户过期

![](img/92fce80411bf8dc6572ad123620669c0_2.png)

`chage`命令用于查看和修改用户的密码策略及账户过期信息。这些信息存储在`/etc/shadow`文件中。

常用选项：
*   `chage -l username`：列出用户的密码策略详情。
*   `chage -d 0 username`：强制用户在下一次登录时必须更改密码。
*   `chage -E YYYY-MM-DD username`：设置账户的绝对过期日期。
*   `chage -M 90 username`：设置密码的有效期（最大天数）为90天。
*   `chage -W 7 username`：在密码过期前7天开始警告用户。
*   `chage -I 5 username`：密码过期后，账户仍可使用的宽限天数（不活跃天数）。

### 锁定与解锁用户

*   **锁定用户**：禁止用户登录。
    ```bash
    usermod -L username
    # 或
    passwd -l username
    ```
    锁定后，即使用户输入正确密码，也会显示认证失败。
*   **解锁用户**：
    ```bash
    usermod -U username
    # 或
    passwd -u username
    ```
*   **查看用户状态**：
    ```bash
    passwd -S username
    ```

---

![](img/92fce80411bf8dc6572ad123620669c0_4.png)

## 文件权限管理

![](img/92fce80411bf8dc6572ad123620669c0_6.png)

![](img/92fce80411bf8dc6572ad123620669c0_8.png)

文件权限决定了谁可以对文件进行读、写、执行操作。

### 查看权限

使用`ls -l`（可简写为`ll`）命令查看文件详细信息，其中包含权限信息。
```
-rwxrw-r-- 1 tk_edu root 0 Mar 15 10:00 aa
```
*   第1位：文件类型（`-`普通文件，`d`目录）。
*   第2-10位：权限位，每3位一组。
    *   第2-4位：**所有者（u）**权限。
    *   第5-7位：**所属组（g）**权限。
    *   第8-10位：**其他用户（o）**权限。
*   权限字符：
    *   `r`：读权限。对文件是查看内容，对目录是列出内容。
    *   `w`：写权限。对文件是修改内容，对目录是创建/删除文件。
    *   `x`：执行权限。对文件是运行脚本/程序，对目录是进入（cd）该目录。

![](img/92fce80411bf8dc6572ad123620669c0_10.png)

![](img/92fce80411bf8dc6572ad123620669c0_12.png)

### 修改文件所有者和所属组

*   `chown`：改变文件所有者（Owner）和/或所属组（Group）。
    ```bash
    chown redhat aa          # 将aa文件的所有者改为redhat
    chown :tk_edu aa         # 将aa文件的所属组改为tk_edu
    chown redhat:tk_edu aa   # 同时修改所有者和所属组
    ```
*   `chgrp`：专门改变文件所属组。
    ```bash
    chgrp wheel aa
    ```

### 修改文件权限

使用`chmod`命令修改权限，有两种表示方法：

**1. 字母表示法**
格式：`chmod [ugoa][+-=][rwx] file`
*   `u`：所有者，`g`：所属组，`o`：其他用户，`a`：所有用户（默认）。
*   `+`：添加权限，`-`：移除权限，`=`：设置精确权限。
```bash
chmod u+x aa      # 给所有者增加执行权限
chmod g-w aa      # 移除所属组的写权限
chmod o=rx aa     # 设置其他用户的权限为读和执行
chmod a+r aa      # 给所有用户增加读权限
```

**2. 数字表示法（推荐）**
用三位八进制数表示权限，每位数字是`r(4)`、`w(2)`、`x(1)`权限值的和。
*   所有者权限 = 第一位数字
*   所属组权限 = 第二位数字
*   其他用户权限 = 第三位数字

```bash
chmod 755 aa  # 权限：rwxr-xr-x
chmod 644 aa  # 权限：rw-r--r--
chmod 760 aa  # 权限：rwxrw----
```

![](img/92fce80411bf8dc6572ad123620669c0_14.png)

![](img/92fce80411bf8dc6572ad123620669c0_16.png)

### 默认权限与umask

新创建的文件和目录有一个默认权限：
*   文件默认权限：`666` (rw-rw-rw-)
*   目录默认权限：`777` (rwxrwxrwx)

实际看到的权限（如文件644，目录755）是默认权限减去`umask`（权限掩码）的结果。`umask`定义了要“屏蔽”掉的权限位。

![](img/92fce80411bf8dc6572ad123620669c0_18.png)

![](img/92fce80411bf8dc6572ad123620669c0_20.png)

![](img/92fce80411bf8dc6572ad123620669c0_22.png)

![](img/92fce80411bf8dc6572ad123620669c0_24.png)

*   查看当前umask：`umask`
*   设置umask：`umask 022`
*   **计算**：最终权限 = 默认权限 & (~umask)。对于文件`666`，umask`022`，结果是`644`。

![](img/92fce80411bf8dc6572ad123620669c0_26.png)

![](img/92fce80411bf8dc6572ad123620669c0_28.png)

![](img/92fce80411bf8dc6572ad123620669c0_30.png)

**临时修改umask**（仅对当前Shell有效）：
```bash
(umask 077; touch secret.txt)
```

![](img/92fce80411bf8dc6572ad123620669c0_32.png)

![](img/92fce80411bf8dc6572ad123620669c0_34.png)

---

![](img/92fce80411bf8dc6572ad123620669c0_36.png)

![](img/92fce80411bf8dc6572ad123620669c0_38.png)

![](img/92fce80411bf8dc6572ad123620669c0_40.png)

![](img/92fce80411bf8dc6572ad123620669c0_42.png)

## 进程管理

![](img/92fce80411bf8dc6572ad123620669c0_44.png)

![](img/92fce80411bf8dc6572ad123620669c0_46.png)

![](img/92fce80411bf8dc6572ad123620669c0_48.png)

进程是正在运行的程序的实例。

![](img/92fce80411bf8dc6572ad123620669c0_50.png)

### 查看进程

![](img/92fce80411bf8dc6572ad123620669c0_52.png)

![](img/92fce80411bf8dc6572ad123620669c0_54.png)

*   `ps`：查看进程快照。
    ```bash
    ps                     # 查看当前终端下的进程
    ps aux                 # 查看系统所有进程的详细信息
    ```
*   `top` / `htop`：动态、交互式查看进程状态和系统资源使用情况。按`q`退出。
    ```bash
    top -d 1               # 设置刷新间隔为1秒
    ```
*   `uptime`：查看系统运行时间、用户数和平均负载。

![](img/92fce80411bf8dc6572ad123620669c0_56.png)

![](img/92fce80411bf8dc6572ad123620669c0_58.png)

![](img/92fce80411bf8dc6572ad123620669c0_60.png)

### 查找进程号（PID）

![](img/92fce80411bf8dc6572ad123620669c0_62.png)

![](img/92fce80411bf8dc6572ad123620669c0_64.png)

![](img/92fce80411bf8dc6572ad123620669c0_66.png)

![](img/92fce80411bf8dc6572ad123620669c0_68.png)

![](img/92fce80411bf8dc6572ad123620669c0_70.png)

![](img/92fce80411bf8dc6572ad123620669c0_72.png)

关闭进程需要知道其进程号（PID）。
```bash
ps aux | grep firefox     # 查找包含firefox的进程
pgrep firefox             # 直接查找firefox进程的PID
pidof firefox             # 查找firefox进程的PID（可能返回多个）
```

![](img/92fce80411bf8dc6572ad123620669c0_74.png)

![](img/92fce80411bf8dc6572ad123620669c0_76.png)

![](img/92fce80411bf8dc6572ad123620669c0_78.png)

### 控制进程

![](img/92fce80411bf8dc6572ad123620669c0_80.png)

![](img/92fce80411bf8dc6572ad123620669c0_82.png)

![](img/92fce80411bf8dc6572ad123620669c0_84.png)

![](img/92fce80411bf8dc6572ad123620669c0_86.png)

*   **发送信号**：使用`kill`命令向进程发送信号。
    ```bash
    kill -15 PID   # 发送SIGTERM信号（默认），请求进程正常终止
    kill -9 PID    # 发送SIGKILL信号，强制立即终止进程（无法拦截）
    ```
    *   `-2` (SIGINT)：相当于在终端按`Ctrl+C`，中断进程。
    *   `-20` (SIGTSTP)：相当于在终端按`Ctrl+Z`，暂停（挂起）进程。

![](img/92fce80411bf8dc6572ad123620669c0_88.png)

![](img/92fce80411bf8dc6572ad123620669c0_90.png)

![](img/92fce80411bf8dc6572ad123620669c0_92.png)

![](img/92fce80411bf8dc6572ad123620669c0_94.png)

![](img/92fce80411bf8dc6572ad123620669c0_96.png)

*   **前后台作业控制**：
    *   `&`：在命令后加`&`，使其在后台运行。
        ```bash
        firefox &
        ```
    *   `jobs`：列出当前Shell的后台作业。
    *   `fg %n`：将后台作业`n`切换到前台运行。
    *   `bg %n`：将暂停的后台作业`n`继续在后台运行。
    *   `kill %n`：终止后台作业`n`。

![](img/92fce80411bf8dc6572ad123620669c0_98.png)

![](img/92fce80411bf8dc6572ad123620669c0_100.png)

**操作示例**：
1.  启动`gedit`，它会占用前台。
2.  按`Ctrl+Z`，暂停`gedit`并将其放入后台。
3.  输入`jobs`，查看后台作业列表及其编号（如`[1]`）。
4.  输入`bg %1`，让`gedit`在后台继续运行。
5.  输入`fg %1`，将`gedit`调回前台。
6.  输入`kill %1`或`kill -9 %1`，终止该后台作业。

![](img/92fce80411bf8dc6572ad123620669c0_102.png)

![](img/92fce80411bf8dc6572ad123620669c0_104.png)

![](img/92fce80411bf8dc6572ad123620669c0_106.png)

---

![](img/92fce80411bf8dc6572ad123620669c0_108.png)

![](img/92fce80411bf8dc6572ad123620669c0_110.png)

![](img/92fce80411bf8dc6572ad123620669c0_112.png)

![](img/92fce80411bf8dc6572ad123620669c0_114.png)

## 总结

![](img/92fce80411bf8dc6572ad123620669c0_116.png)

![](img/92fce80411bf8dc6572ad123620669c0_118.png)

![](img/92fce80411bf8dc6572ad123620669c0_120.png)

![](img/92fce80411bf8dc6572ad123620669c0_122.png)

本节课中我们一起学习了Linux系统管理的核心内容：
1.  **用户与组管理**：使用`usermod`和`gpasswd`管理用户附属组，使用`groupadd`和`groupdel`管理组，并了解了用户删除后的文件清理问题。
2.  **密码管理**：使用`passwd`修改密码，使用`chage`设置密码策略和账户过期，使用`usermod -L/-U`锁定和解锁用户。
3.  **文件权限管理**：理解了`r/w/x`权限的含义，使用`chown`和`chgrp`修改所有者和所属组，使用`chmod`的字母法和数字法修改权限，并了解了`umask`对默认权限的影响。
4.  **进程管理**：使用`ps`、`top`查看进程，使用`kill`终止进程，以及使用`jobs`、`fg`、`bg`、`&`和`Ctrl+Z`进行作业的前后台控制。

![](img/92fce80411bf8dc6572ad123620669c0_124.png)

![](img/92fce80411bf8dc6572ad123620669c0_126.png)

掌握这些命令和概念，是成为一名合格的Linux系统管理员的基础。请务必通过实践练习来巩固理解。