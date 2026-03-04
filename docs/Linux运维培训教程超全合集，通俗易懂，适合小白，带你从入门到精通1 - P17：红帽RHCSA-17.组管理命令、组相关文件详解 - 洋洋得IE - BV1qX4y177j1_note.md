# Linux运维培训教程：1.17：组管理命令与组相关文件详解 🔧

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_1.png)

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_3.png)

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_5.png)

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_6.png)

在本节课中，我们将要学习Linux系统中的组管理。组是用户管理的重要组成部分，它允许我们将多个用户组织在一起，以便于统一分配权限和管理。我们将详细介绍如何创建、修改、删除组，以及如何将用户添加到组中或从组中移除。同时，我们也会了解与组相关的系统文件。

## 修改用户属性

上一节我们介绍了如何创建用户，本节中我们来看看如何修改已存在用户的属性。当用户创建后，我们可能需要修改其用户ID（UID）、家目录、描述信息或登录解释器等。这时，我们可以使用 `usermod` 命令。

`usermod` 命令用于修改已存在用户的基本信息。其命令格式与 `useradd` 类似，通过不同的选项来指定要修改的属性。

以下是 `usermod` 命令的一些常用选项示例：

*   **修改用户UID**：`usermod -u 6666 user1` 将用户 `user1` 的UID修改为6666。
*   **修改用户描述信息**：`usermod -c “邮箱 xxx@163.com” user1` 为用户 `user1` 添加描述信息。
*   **将用户加入附加组**：`usermod -G 运维组,开发组,测试组 user1` 将用户 `user1` 同时加入到“运维组”、“开发组”和“测试组”中。
*   **修改用户登录解释器**：`usermod -s /bin/sh user1` 将用户 `user1` 的默认解释器修改为 `/bin/sh`。

## 删除用户

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_8.png)

删除操作相对简单。`userdel` 命令用于删除给定的用户。

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_10.png)

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_12.png)

*   **仅删除用户账号**：`userdel user1` 仅删除 `user1` 这个账号，但保留其家目录。
*   **删除用户及其家目录**：`userdel -r user100` 删除 `user100` 账号，并同时删除其家目录。

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_14.png)

需要注意的是，`userdel -r` 命令只会删除用户的家目录，而不会删除该用户在其他路径下创建的文件。

## 组管理基础

组的主要作用是将用户组织起来，方便进行权限管理。接下来，我们学习如何管理组。

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_16.png)

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_18.png)

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_19.png)

### 创建组

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_21.png)

使用 `groupadd` 命令可以创建一个新的组。组的信息会存储在 `/etc/group` 文件中。

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_23.png)

例如，创建一个名为 `student` 的组：
```bash
groupadd student
```

### 组信息文件 `/etc/group`

系统所有组的信息都存储在 `/etc/group` 文件中。我们可以通过查看这个文件来了解系统中的组。

使用 `cat /etc/group` 命令查看文件内容。文件中的每一行代表一个组，格式如下：
```
组名:组密码占位符:组ID(GID):组内成员列表
```

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_25.png)

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_27.png)

例如，一行内容 `yunjizu:x:1238:user5` 表示：
*   **组名**：`yunjizu`
*   **组密码占位符**：`x`（密码实际存放在 `/etc/gshadow` 文件）
*   **组ID**：`1238`
*   **组内成员**：`user5`

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_29.png)

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_30.png)

### 组密码文件 `/etc/gshadow`

组的加密密码信息存储在 `/etc/gshadow` 文件中。在现代Linux系统中，直接给组设置密码并分配组管理员的场景已非常少见，通常由root用户直接管理组成员。因此，这个文件我们了解即可，一般无需修改。

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_32.png)

## 修改组属性

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_34.png)

对于已存在的组，我们可以使用 `groupmod` 命令来修改其属性。

*   **修改组ID**：`groupmod -g 5678 student` 将 `student` 组的GID修改为5678。
*   **修改组名**：`groupmod -n STUGRP student` 将 `student` 组重命名为 `STUGRP`。

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_36.png)

## 管理组成员

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_38.png)

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_39.png)

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_40.png)

将用户添加到组中或从组中移除，是组管理的核心操作。我们可以使用 `gpasswd` 命令来完成。

*   **将用户添加到组**：`gpasswd -a user6 STUGRP` 将用户 `user6` 添加到 `STUGRP` 组中。
*   **将用户从组中移除**：`gpasswd -d user6 STUGRP` 将用户 `user6` 从 `STUGRP` 组中移除。

## 删除组

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_42.png)

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_44.png)

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_46.png)

删除组的命令是 `groupdel`。删除操作很简单，但需要注意，如果一个组是某个用户的主组（初始组），则不允许被删除。

例如，删除 `student` 组：
```bash
groupdel student
```
如果尝试删除一个用户的主组，系统会提示“不能移除用户的主组”。

## 总结

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_48.png)

![](img/6b59bfacf2f9487e7c7ce206ea26ed55_50.png)

本节课中我们一起学习了Linux系统中的组管理。我们掌握了如何使用 `groupadd`、`groupmod`、`groupdel` 命令来创建、修改和删除组。重点学习了如何使用 `gpasswd` 命令来管理组的成员，这是实现用户权限分组管理的基础。我们还了解了存储组信息的系统文件 `/etc/group` 和 `/etc/gshadow`。理解组的概念和管理方法，对于后续学习文件权限控制至关重要。