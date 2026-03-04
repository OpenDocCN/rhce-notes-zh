# Linux用户和组管理：07：用户与组管理详解

## 概述

在本节课中，我们将深入学习Linux系统中的用户和组管理。我们将探讨用户和组的核心概念、相关配置文件、管理命令，并学习如何在不依赖标准命令的情况下手动创建用户。通过本节课的学习，你将能够理解Linux权限管理的基础，并掌握处理用户和组相关问题的核心技能。

![](img/4c9dc32bfb845919663daee8b6321b2a_1.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_3.png)

## 用户配置文件回顾

![](img/4c9dc32bfb845919663daee8b6321b2a_5.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_7.png)

上一节我们介绍了`/etc/passwd`文件的基本结构。本节中，我们来看看用户管理中的一些实践细节和常见误区。

![](img/4c9dc32bfb845919663daee8b6321b2a_9.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_11.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_13.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_15.png)

关于`/etc/passwd`文件中的密码占位符`x`，其行为在不同版本的Linux中有所差异。在红帽8中，对于root用户，移除`x`后登录不再需要密码验证；而对于普通用户，可能仍需要密码。在红帽7及以下版本中，移除`x`对所有用户都意味着需要密码验证。通常，我们只对root用户进行此类操作，因为如果root用户无需密码即可登录，管理员就能为所有用户重置密码。

![](img/4c9dc32bfb845919663daee8b6321b2a_17.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_19.png)

**核心概念**：密码占位符`x`被移除，并不意味着用户“没有密码”，而是系统“不再验证密码”。重新添加`x`后，密码验证即恢复。

![](img/4c9dc32bfb845919663daee8b6321b2a_21.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_23.png)

关于用户登录shell（`/etc/passwd`文件的最后一个字段），它代表用户登录后运行的第一个程序。如果设置为`/bin/bash`，用户可以登录操作系统；如果设置为`/sbin/nologin`，则用户无法登录系统，但这不代表用户不能使用系统服务（例如FTP服务）。

![](img/4c9dc32bfb845919663daee8b6321b2a_25.png)

## 组管理详解

![](img/4c9dc32bfb845919663daee8b6321b2a_27.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_29.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_31.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_33.png)

有用户就会有组，这是两个紧密关联的概念。在Linux系统中，每个组的创建都与用户有着强关联性。

![](img/4c9dc32bfb845919663daee8b6321b2a_35.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_37.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_39.png)

### 组与用户的关系

![](img/4c9dc32bfb845919663daee8b6321b2a_41.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_43.png)

在Linux中，创建一个用户的同时，系统会默认创建一个与用户名同名的组，作为该用户的“私有组”（或称为“属组”）。这与Windows系统不同。

![](img/4c9dc32bfb845919663daee8b6321b2a_45.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_47.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_49.png)

每个用户必须属于一个主组，每个组都有一个独一无二的组ID（GID）。所有组的信息都保存在`/etc/group`文件中。

![](img/4c9dc32bfb845919663daee8b6321b2a_51.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_53.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_55.png)

我们可以将用户添加到其他组，这些组就成为该用户的“附加组”（或称为“公共组”）。

![](img/4c9dc32bfb845919663daee8b6321b2a_57.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_59.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_61.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_63.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_65.png)

### `/etc/group`文件解析

![](img/4c9dc32bfb845919663daee8b6321b2a_67.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_69.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_71.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_73.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_75.png)

以下是`/etc/group`文件的结构解析：

![](img/4c9dc32bfb845919663daee8b6321b2a_77.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_79.png)

```
root:x:0:
user1:x:1000:
it:x:1002:user1
```

![](img/4c9dc32bfb845919663daee8b6321b2a_81.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_83.png)

*   **第一字段**：组名。
*   **第二字段**：组的密码占位符（`x`）。组可以设置密码，目的是为了临时授权。例如，用户A需要临时获得开发组的权限来修改某个文件，管理员可以将开发组的密码告知用户A，用户A通过`newgrp`命令临时切换到该组，从而获得权限。这一切都是临时的。
*   **第三字段**：组ID（GID）。
*   **第四字段**：属于该附加组的用户列表，多个用户名用逗号分隔。

![](img/4c9dc32bfb845919663daee8b6321b2a_85.png)

**重要说明**：作为用户“私有组”的组（即与用户名同名的组），其用户名不会出现在第四字段的列表中。只有作为“附加组”时，用户名才会在此列出。

![](img/4c9dc32bfb845919663daee8b6321b2a_87.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_89.png)

### 组权限的共享与例外

![](img/4c9dc32bfb845919663daee8b6321b2a_91.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_92.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_94.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_96.png)

同一个组内的所有用户可以共享该组的文件权限。但有一个例外情况：即使用户被加入到`root`组，他也不会获得root用户的超级管理员权限。因为Linux系统的超级管理员权限是由用户ID（UID）是否为0决定的，而不是由所属的组决定。`root`组在系统中的权限很小。

![](img/4c9dc32bfb845919663daee8b6321b2a_98.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_100.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_102.png)

**核心概念**：在Linux中，系统通过**UID**来标识和决定权限，而非用户名。`root`用户之所以是超级管理员，是因为其`UID=0`。

![](img/4c9dc32bfb845919663daee8b6321b2a_104.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_106.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_108.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_109.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_111.png)

## 用户管理命令

![](img/4c9dc32bfb845919663daee8b6321b2a_113.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_115.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_117.png)

以下是管理用户和组的常用命令。

![](img/4c9dc32bfb845919663daee8b6321b2a_119.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_121.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_123.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_125.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_127.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_129.png)

### 创建用户 (`useradd`)

![](img/4c9dc32bfb845919663daee8b6321b2a_131.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_133.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_135.png)

最基本的命令是`useradd`。如果不加任何选项，必须指定用户名。

![](img/4c9dc32bfb845919663daee8b6321b2a_137.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_139.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_141.png)

**命令示例**：
```bash
useradd redhat
```

![](img/4c9dc32bfb845919663daee8b6321b2a_143.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_145.png)

`useradd`命令支持许多选项来定制用户属性：

![](img/4c9dc32bfb845919663daee8b6321b2a_147.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_149.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_151.png)

*   `-g`：指定用户的私有组（主组）。
    ```bash
    useradd -g it user6
    ```
*   `-c`：指定用户的描述信息。
    ```bash
    useradd -c “hello” user7
    ```
*   `-G`：指定用户的附加组。
    ```bash
    useradd -G it user8
    ```
*   `-s`：指定用户的登录shell。
    ```bash
    useradd -s /sbin/nologin user9
    ```
*   `-u`：指定用户的UID。
    ```bash
    useradd -u 2000 user10
    ```
*   `-o`：允许创建重复UID的用户（与`-u`一起使用）。这类似于用户的“别名”，系统实际通过UID识别用户。
    ```bash
    useradd -u 1006 -o user13
    ```

![](img/4c9dc32bfb845919663daee8b6321b2a_153.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_155.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_157.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_159.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_161.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_163.png)

**重要提示**：UID 0-999通常为系统保留，不建议分配给普通用户，以免与系统服务用户冲突。

![](img/4c9dc32bfb845919663daee8b6321b2a_165.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_167.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_169.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_171.png)

### 修改用户 (`usermod`)

![](img/4c9dc32bfb845919663daee8b6321b2a_173.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_175.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_177.png)

`usermod`命令用于修改现有用户的属性。

![](img/4c9dc32bfb845919663daee8b6321b2a_179.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_181.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_183.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_185.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_187.png)

*   `-aG`：为用户追加一个附加组。
    ```bash
    usermod -aG user12 user10
    ```
*   `-L`：锁定用户。锁定机制是在用户加密密码前添加`!`，使密码失效。但root用户切换到被锁用户不受此限制。
    ```bash
    usermod -L user3
    ```
*   `-U`：解锁用户。
    ```bash
    usermod -U user3
    ```
*   `-m -d`：移动用户的家目录到新路径。目标目录最好不存在，由命令自动创建。
    ```bash
    usermod -m -d /data4 user10
    ```

![](img/4c9dc32bfb845919663daee8b6321b2a_189.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_191.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_193.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_195.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_197.png)

### 删除用户 (`userdel`)

![](img/4c9dc32bfb845919663daee8b6321b2a_199.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_201.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_203.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_205.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_207.png)

*   直接使用`userdel`只会删除`/etc/passwd`、`/etc/group`、`/etc/shadow`中的记录，但会保留用户的家目录和邮箱文件。
*   使用`-r`选项会同时删除用户的家目录和邮箱文件。
    ```bash
    userdel -r user20
    ```

![](img/4c9dc32bfb845919663daee8b6321b2a_209.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_210.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_211.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_213.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_215.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_216.png)

### 组管理命令

![](img/4c9dc32bfb845919663daee8b6321b2a_218.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_219.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_221.png)

*   `groupadd`：创建新组。
    ```bash
    groupadd -g 3000 it5
    ```
*   `groupmod`：修改组属性。`-n`修改组名，`-g`修改GID。
    ```bash
    groupmod -n it6 it5
    groupmod -g 4000 it6
    ```
*   `groupdel`：删除组。
    ```bash
    groupdel it6
    ```
*   `gpasswd`：为组设置密码。用户可以使用`newgrp <组名>`并输入密码来临时切换到该组，获得其权限。
    ```bash
    gpasswd it8
    # 用户操作
    newgrp it8
    ```

![](img/4c9dc32bfb845919663daee8b6321b2a_223.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_225.png)

## 密码管理

![](img/4c9dc32bfb845919663daee8b6321b2a_227.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_229.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_231.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_233.png)

所有用户的密码信息都存储在`/etc/shadow`文件中。

![](img/4c9dc32bfb845919663daee8b6321b2a_235.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_237.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_239.png)

### `/etc/shadow`文件解析

![](img/4c9dc32bfb845919663daee8b6321b2a_241.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_243.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_245.png)

该文件包含9个字段，由冒号分隔：

![](img/4c9dc32bfb845919663daee8b6321b2a_247.png)

1.  **用户名**。
2.  **加密的密码**：如果为`!`或`!!`或`*`，表示用户被锁定。`!!`也可能表示用户从未设置密码。加密算法标识（如`$6$`）表示使用SHA-512加密。
3.  **最近一次密码修改时间**：表示从1970年1月1日（Unix纪元）到上次修改密码所经过的天数。
4.  **密码最短有效期**：0表示无限制。
5.  **密码最长有效期**：99999通常表示永久有效。
6.  **密码警告期**：在密码到期前多少天开始警告用户。
7.  **密码不活动期**：密码过期后，账号仍可保持活跃（但无法登录）的天数。
8.  **账号失效时间**：从1970年1月1日起，账号失效的具体天数。
9.  **保留字段**。

![](img/4c9dc32bfb845919663daee8b6321b2a_249.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_251.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_253.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_255.png)

### 密码加密与生成

![](img/4c9dc32bfb845919663daee8b6321b2a_257.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_259.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_261.png)

可以使用`openssl`或`passwd`命令生成加密密码。

![](img/4c9dc32bfb845919663daee8b6321b2a_263.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_265.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_267.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_269.png)

**使用`passwd`生成SHA-512加密密码**：
```bash
passwd -6 redhat
```

![](img/4c9dc32bfb845919663daee8b6321b2a_271.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_273.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_275.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_277.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_279.png)

**使用`openssl`进行加密解密示例**：
```bash
# 加密文本
openssl enc -base64 -in 1.txt -out 2.txt
# 解密文本
openssl enc -base64 -d -in 2.txt -out 3.txt
# 使用随机盐值加密密码（不可逆，更安全）
openssl passwd -salt “123abc” redhat
```

![](img/4c9dc32bfb845919663daee8b6321b2a_281.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_283.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_285.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_287.png)

生成加密密码后，可以手动编辑`/etc/shadow`文件，将密码字符串填入相应用户的第二字段。

![](img/4c9dc32bfb845919663daee8b6321b2a_289.png)

### 密码策略管理 (`chage`)

![](img/4c9dc32bfb845919663daee8b6321b2a_291.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_293.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_295.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_297.png)

`chage`命令用于查看和修改用户的密码策略。

![](img/4c9dc32bfb845919663daee8b6321b2a_299.png)

*   查看用户密码策略：
    ```bash
    chage -l user1
    ```
*   修改密码最短有效期：
    ```bash
    chage -m 30 user1
    ```
*   修改密码最长有效期：
    ```bash
    chage -M 100 user1
    ```

## 实战：手动创建用户

![](img/4c9dc32bfb845919663daee8b6321b2a_301.png)

假设在一个所有用户管理命令都不可用的环境中，需要手工创建用户`user100`。

![](img/4c9dc32bfb845919663daee8b6321b2a_303.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_305.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_307.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_309.png)

**需求**：
*   用户名：`user100`
*   私有组：`user100`
*   家目录：`/data100`
*   登录shell：`/bin/bash`
*   描述信息：`MySQL`
*   密码：`mysql`

**操作步骤**：

1.  **编辑`/etc/passwd`，添加用户信息**：
    ```bash
    vim /etc/passwd
    # 在文件末尾添加一行
    user100:x:5000:5000:MySQL:/data100:/bin/bash
    ```

![](img/4c9dc32bfb845919663daee8b6321b2a_311.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_313.png)

2.  **编辑`/etc/group`，创建用户私有组**：
    ```bash
    vim /etc/group
    # 在文件末尾添加一行
    user100:x:5000:
    ```

![](img/4c9dc32bfb845919663daee8b6321b2a_315.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_317.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_319.png)

3.  **创建用户家目录**：
    ```bash
    mkdir /data100
    ```

![](img/4c9dc32bfb845919663daee8b6321b2a_321.png)

4.  **生成加密密码并编辑`/etc/shadow`**：
    ```bash
    openssl passwd -6 mysql
    # 复制生成的加密字符串
    vim /etc/shadow
    # 在文件末尾添加一行
    user100:生成的加密字符串:18460:0:99999:7:::
    ```

![](img/4c9dc32bfb845919663daee8b6321b2a_323.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_325.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_327.png)

5.  **创建用户邮箱文件并设置权限**（可选，但完整用户需要）：
    ```bash
    cp /var/spool/mail/user1 /var/spool/mail/user100
    chown user100:mail /var/spool/mail/user100
    chmod 660 /var/spool/mail/user100
    ```

![](img/4c9dc32bfb845919663daee8b6321b2a_329.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_331.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_333.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_335.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_337.png)

完成以上步骤后，用户`user100`即可使用密码`mysql`登录系统。

![](img/4c9dc32bfb845919663daee8b6321b2a_339.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_341.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_343.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_345.png)

## 总结

![](img/4c9dc32bfb845919663daee8b6321b2a_347.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_349.png)

![](img/4c9dc32bfb845919663daee8b6321b2a_351.png)

本节课我们一起深入学习了Linux用户和组的管理。我们从`/etc/passwd`、`/etc/group`、`/etc/shadow`等核心配置文件入手，理解了用户、组、密码的存储原理。我们学习了`useradd`、`usermod`、`userdel`、`groupadd`、`gpasswd`、`chage`等关键管理命令的用法，并探讨了密码加密机制。最后，通过手动创建用户的实战练习，我们巩固了对用户管理底层原理的理解，掌握了在极端情况下解决问题的能力。这些知识是进行系统管理、权限控制和安全管理的重要基础。