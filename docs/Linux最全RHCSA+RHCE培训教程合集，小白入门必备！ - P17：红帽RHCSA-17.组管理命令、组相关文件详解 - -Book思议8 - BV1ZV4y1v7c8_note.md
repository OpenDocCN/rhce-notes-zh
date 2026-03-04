# Linux最全RHCSA+RHCE培训教程合集，小白入门必备！ - P17：红帽RHCSA-17.组管理命令、组相关文件详解

![](img/99b7eec74ed270a2a887097f5b5b05e1_1.png)

![](img/99b7eec74ed270a2a887097f5b5b05e1_3.png)

![](img/99b7eec74ed270a2a887097f5b5b05e1_5.png)

在本节课中，我们将要学习如何修改已存在用户的属性，以及如何进行组的管理，包括创建、修改、删除组，以及如何将用户添加到组中或从组中移除。这些操作是Linux系统管理的基础。

![](img/99b7eec74ed270a2a887097f5b5b05e1_7.png)

## 修改用户属性

上一节我们介绍了如何创建用户，本节中我们来看看如何修改已存在的用户信息。当用户创建后，可能需要修改其UID、家目录、描述信息或登录解释器等属性。

修改用户属性的命令是 `usermod`，其选项与创建用户时使用的 `useradd` 命令基本一致。

以下是 `usermod` 命令的常用操作示例：

*   **修改用户UID**：`usermod -u 6666 uc1`。此命令将用户 `uc1` 的UID修改为 `6666`。
*   **修改用户描述信息**：`usermod -c “xxx@163.com” uc1`。此命令为用户 `uc1` 添加描述信息。
*   **将用户加入附加组**：`usermod -G yunwei,kaifa,ceshi uc1`。此命令将用户 `uc1` 同时加入到 `yunwei`、`kaifa`、`ceshi` 三个组中。
*   **修改用户登录解释器**：`usermod -s /bin/sh uc1`。此命令将用户 `uc1` 的默认登录解释器修改为 `/bin/sh`。

## 删除用户

![](img/99b7eec74ed270a2a887097f5b5b05e1_9.png)

删除用户的操作相对简单。使用 `userdel` 命令可以删除指定的用户。

![](img/99b7eec74ed270a2a887097f5b5b05e1_11.png)

以下是删除用户的两种方式：

![](img/99b7eec74ed270a2a887097f5b5b05e1_13.png)

![](img/99b7eec74ed270a2a887097f5b5b05e1_15.png)

*   **仅删除用户账号**：`userdel uc1`。此命令仅删除 `uc1` 用户账号，但保留其家目录。
*   **删除用户账号及家目录**：`userdel -r uc100`。此命令在删除 `uc100` 用户账号的同时，也删除其家目录。

需要注意的是，`userdel` 命令只会删除用户的家目录，用户在其他路径创建的文件不会被自动删除。

## 组管理

![](img/99b7eec74ed270a2a887097f5b5b05e1_17.png)

组是用户的集合，其主要作用是方便进行权限管理。接下来我们学习如何创建、查看和修改组。

![](img/99b7eec74ed270a2a887097f5b5b05e1_19.png)

![](img/99b7eec74ed270a2a887097f5b5b05e1_21.png)

### 创建组

![](img/99b7eec74ed270a2a887097f5b5b05e1_23.png)

![](img/99b7eec74ed270a2a887097f5b5b05e1_25.png)

创建新组的命令是 `groupadd`。

例如，创建一个名为 `student` 的组：`groupadd student`。组的信息会被记录在 `/etc/group` 文件中。

### 组信息文件详解

组的基本信息存储在 `/etc/group` 文件中。我们可以通过查看此文件来了解系统中的所有组。

以下是 `/etc/group` 文件内容的格式说明，每一行代表一个组，字段以冒号 `:` 分隔：

![](img/99b7eec74ed270a2a887097f5b5b05e1_27.png)

![](img/99b7eec74ed270a2a887097f5b5b05e1_29.png)

```
组名:组密码占位符:组ID(GID):组内用户列表
```

![](img/99b7eec74ed270a2a887097f5b5b05e1_31.png)

![](img/99b7eec74ed270a2a887097f5b5b05e1_33.png)

*   **组名**：组的名称。
*   **组密码占位符**：通常为 `x`，真实的组密码（现已很少使用）存储在 `/etc/gshadow` 文件中。
*   **组ID(GID)**：组的唯一数字标识。
*   **组内用户列表**：属于该组的用户成员列表，多个用户名用逗号分隔。

### 修改组属性

![](img/99b7eec74ed270a2a887097f5b5b05e1_35.png)

对于已存在的组，可以使用 `groupmod` 命令修改其属性。

![](img/99b7eec74ed270a2a887097f5b5b05e1_37.png)

以下是修改组属性的示例：

*   **修改组ID**：`groupmod -g 5678 student`。此命令将 `student` 组的GID修改为 `5678`。
*   **修改组名**：`groupmod -n stugrp student`。此命令将 `student` 组重命名为 `stugrp`。

![](img/99b7eec74ed270a2a887097f5b5b05e1_39.png)

### 管理组成员

![](img/99b7eec74ed270a2a887097f5b5b05e1_41.png)

`gpasswd` 命令是管理组成员的主要工具，可用于设置组密码（现已少用）以及添加或删除组内用户。

![](img/99b7eec74ed270a2a887097f5b5b05e1_43.png)

![](img/99b7eec74ed270a2a887097f5b5b05e1_44.png)

以下是使用 `gpasswd` 管理组成员的示例：

*   **将用户添加到组**：`gpasswd -a uc6 stugrp`。此命令将用户 `uc6` 添加到 `stugrp` 组中。
*   **将用户从组中移除**：`gpasswd -d uc6 stugrp`。此命令将用户 `uc6` 从 `stugrp` 组中移除。

![](img/99b7eec74ed270a2a887097f5b5b05e1_46.png)

### 删除组

![](img/99b7eec74ed270a2a887097f5b5b05e1_48.png)

![](img/99b7eec74ed270a2a887097f5b5b05e1_50.png)

删除组的命令是 `groupdel`。

例如，删除 `stugrp` 组：`groupdel stugrp`。需要注意的是，如果一个组是某个用户的基本组（主组），则该组不允许被删除。

## 总结

![](img/99b7eec74ed270a2a887097f5b5b05e1_52.png)

![](img/99b7eec74ed270a2a887097f5b5b05e1_54.png)

本节课中我们一起学习了Linux用户和组管理的重要操作。我们掌握了如何使用 `usermod` 修改用户属性，使用 `userdel` 删除用户。在组管理方面，我们学习了使用 `groupadd`、`groupmod`、`groupdel` 命令对组进行创建、修改和删除，并重点学习了使用 `gpasswd` 命令来管理组的成员。理解并熟练运用这些命令，是进行系统用户权限管理的基础。下一节，我们将进入权限管理的核心内容。