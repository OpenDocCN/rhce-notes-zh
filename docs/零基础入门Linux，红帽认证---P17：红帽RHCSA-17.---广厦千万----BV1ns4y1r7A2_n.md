# Linux用户与组管理：P17：组管理命令、组相关文件详解 🔧

![](img/bce6be11900dc43cb0ea44dd8ab18e12_1.png)

在本节课中，我们将学习如何修改已存在用户的属性，以及如何进行组的管理，包括创建、修改、删除组，以及如何将用户添加到组中或从组中移除。理解这些操作是进行系统用户权限管理的基础。

![](img/bce6be11900dc43cb0ea44dd8ab18e12_3.png)

![](img/bce6be11900dc43cb0ea44dd8ab18e12_5.png)

![](img/bce6be11900dc43cb0ea44dd8ab18e12_6.png)

---

## 修改用户属性

上一节我们介绍了如何创建用户，本节中我们来看看如何修改已存在的用户信息。当用户账户已经创建，但需要更改其用户ID（UID）、家目录、描述信息或登录解释器时，可以使用 `usermod` 命令。

`usermod` 命令用于修改已存在用户的基本信息。其命令格式与 `useradd` 类似，通过不同的选项来指定要修改的属性。

以下是 `usermod` 命令的一些常用选项示例：

*   **修改用户UID**：将用户 `user1` 的UID改为 `6666`。
    ```bash
    usermod -u 6666 user1
    ```
*   **添加用户描述信息**：为用户 `user1` 添加描述信息（如邮箱）。
    ```bash
    usermod -c "xxx@163.com" user1
    ```
*   **将用户加入附加组**：将用户 `user1` 加入到 `yunwei`（运维）、`kaifa`（开发）、`ceshi`（测试）组中。
    ```bash
    usermod -G yunwei,kaifa,ceshi user1
    ```
*   **修改用户登录解释器**：将用户 `user1` 的默认解释器改为 `/bin/sh`。
    ```bash
    usermod -s /bin/sh user1
    ```

执行上述命令后，可以使用 `id user1` 或查看 `/etc/passwd` 文件来确认修改是否生效。

---

## 删除用户

![](img/bce6be11900dc43cb0ea44dd8ab18e12_8.png)

删除操作相对简单。`userdel` 命令用于删除给定的用户。需要注意的是，该命令有不同的选项来控制是否同时删除用户的家目录。

![](img/bce6be11900dc43cb0ea44dd8ab18e12_10.png)

以下是删除用户的相关操作：

![](img/bce6be11900dc43cb0ea44dd8ab18e12_12.png)

![](img/bce6be11900dc43cb0ea44dd8ab18e12_14.png)

*   **仅删除用户账户**：此命令仅删除 `user1` 这个账户，但保留其在 `/home/user1` 下的家目录。
    ```bash
    userdel user1
    ```
*   **删除用户及其家目录**：使用 `-r` 选项可以删除用户 `user100` 的同时，一并删除其家目录。
    ```bash
    userdel -r user100
    ```
*   **手动删除遗留家目录**：如果仅用 `userdel` 删除了账户，可以手动删除其遗留的家目录。
    ```bash
    rm -rf /home/user1
    ```

**注意**：`userdel -r` 命令只会删除用户的家目录，而不会删除该用户在其他路径（如 `/tmp`）下创建的文件。

---

## 组管理基础

![](img/bce6be11900dc43cb0ea44dd8ab18e12_16.png)

组是用户的集合，主要用于简化权限管理。下面我们来看看如何创建和管理组。

![](img/bce6be11900dc43cb0ea44dd8ab18e12_18.png)

![](img/bce6be11900dc43cb0ea44dd8ab18e12_19.png)

### 创建组

![](img/bce6be11900dc43cb0ea44dd8ab18e12_21.png)

`groupadd` 命令用于创建一个新的用户组。组信息会被保存在 `/etc/group` 文件中。

![](img/bce6be11900dc43cb0ea44dd8ab18e12_23.png)

例如，创建一个名为 `student` 的组：
```bash
groupadd student
```

### 组信息文件详解

组的主要信息存储在 `/etc/group` 文件中。我们可以通过查看此文件来了解系统中的所有组。

每一行代表一个组的信息，由冒号 `:` 分隔为四个字段：
1.  **组名**：组的名称。
2.  **组密码占位符**：通常为 `x`，真实的组密码（已很少使用）存储在 `/etc/gshadow` 文件。
3.  **组ID (GID)**：组的唯一数字标识。
4.  **组成员列表**：属于该组的用户列表，用户名之间用逗号分隔。

例如，一行 `yunwei:x:1238:user5` 表示：存在一个名为 `yunwei` 的组，其GID是1238，组内有一个成员 `user5`。

![](img/bce6be11900dc43cb0ea44dd8ab18e12_25.png)

### 修改组属性

![](img/bce6be11900dc43cb0ea44dd8ab18e12_27.png)

![](img/bce6be11900dc43cb0ea44dd8ab18e12_29.png)

`groupmod` 命令用于修改已存在组的属性，例如组ID或组名。

![](img/bce6be11900dc43cb0ea44dd8ab18e12_30.png)

以下是修改组属性的操作示例：

*   **修改组ID**：将 `student` 组的GID改为 `5678`。
    ```bash
    groupmod -g 5678 student
    ```
*   **修改组名**：将 `student` 组改名为 `stugrp`。
    ```bash
    groupmod -n stugrp student
    ```

### 管理组成员

![](img/bce6be11900dc43cb0ea44dd8ab18e12_32.png)

`gpasswd` 命令是管理组成员的主要工具，可用于向组内添加或删除用户。

![](img/bce6be11900dc43cb0ea44dd8ab18e12_34.png)

以下是管理组成员的操作：

*   **将用户添加到组**：将用户 `user6` 添加到 `stugrp` 组中。
    ```bash
    gpasswd -a user6 stugrp
    ```
*   **将用户从组中移除**：将用户 `user6` 从 `stugrp` 组中移除。
    ```bash
    gpasswd -d user6 stugrp
    ```

![](img/bce6be11900dc43cb0ea44dd8ab18e12_36.png)

执行后，可以查看 `/etc/group` 文件来确认组成员的变化。

### 删除组

![](img/bce6be11900dc43cb0ea44dd8ab18e12_38.png)

![](img/bce6be11900dc43cb0ea44dd8ab18e12_39.png)

![](img/bce6be11900dc43cb0ea44dd8ab18e12_40.png)

`groupdel` 命令用于删除一个组。

例如，删除 `stugrp` 组：
```bash
groupdel stugrp
```

**重要提示**：如果一个组是某个用户的主组（初始组），则该组不允许被删除。尝试删除时会收到错误提示。

![](img/bce6be11900dc43cb0ea44dd8ab18e12_42.png)

---

![](img/bce6be11900dc43cb0ea44dd8ab18e12_44.png)

![](img/bce6be11900dc43cb0ea44dd8ab18e12_46.png)

## 课程总结

本节课中我们一起学习了Linux中用户和组管理的进阶操作。

我们掌握了如何使用 `usermod` 命令修改用户的UID、描述信息、附加组和登录解释器。也学会了使用 `userdel` 命令删除用户，并了解了是否保留家目录的区别。

在组管理部分，我们学习了使用 `groupadd`、`groupmod` 和 `groupdel` 命令来创建、修改和删除组。重点理解了 `/etc/group` 文件的结构和含义。最后，我们通过 `gpasswd` 命令实践了如何向组中添加或移除用户，这是实现用户权限分类管理的关键步骤。

![](img/bce6be11900dc43cb0ea44dd8ab18e12_48.png)

![](img/bce6be11900dc43cb0ea44dd8ab18e12_50.png)

理解并熟练运用这些命令，将为后续学习文件权限管理和多用户环境配置打下坚实的基础。