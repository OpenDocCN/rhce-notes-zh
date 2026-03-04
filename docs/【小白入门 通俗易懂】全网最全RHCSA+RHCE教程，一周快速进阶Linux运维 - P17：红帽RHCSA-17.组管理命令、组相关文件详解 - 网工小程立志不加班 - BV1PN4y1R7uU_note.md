# Linux用户与组管理：P17：组管理命令、组相关文件详解 🧑‍💻

![](img/5be0f470baac647c43cf69340239d97c_1.png)

![](img/5be0f470baac647c43cf69340239d97c_3.png)

![](img/5be0f470baac647c43cf69340239d97c_5.png)

在本节课中，我们将学习如何修改已存在用户的属性，以及如何进行组的创建、管理和删除。理解这些操作是系统管理的基础。

![](img/5be0f470baac647c43cf69340239d97c_7.png)

## 修改用户属性

上一节我们介绍了如何创建用户，本节中我们来看看如何修改已存在的用户信息。当用户创建后，可能需要修改其UID、家目录、描述信息或登录解释器等属性。

修改用户属性的命令是 `usermod`，其选项与创建用户时使用的 `useradd` 命令基本一致。

以下是 `usermod` 命令的常用操作示例：

*   **修改用户UID**：将用户 `user1` 的UID改为 `6666`。
    ```bash
    usermod -u 6666 user1
    ```
*   **修改用户描述信息**：为用户 `user1` 添加描述信息。
    ```bash
    usermod -c “xxx@163.com” user1
    ```
*   **修改用户附加组**：将用户 `user1` 加入到 `yunwei`（运维）、`kaifa`（开发）、`ceshi`（测试）组中。
    ```bash
    usermod -G yunwei,kaifa,ceshi user1
    ```
*   **修改用户登录解释器**：将用户 `user1` 的登录解释器改为 `/bin/sh`。
    ```bash
    usermod -s /bin/sh user1
    ```

## 删除用户

![](img/5be0f470baac647c43cf69340239d97c_9.png)

删除用户的操作相对简单。使用 `userdel` 命令可以删除指定的用户。

![](img/5be0f470baac647c43cf69340239d97c_11.png)

以下是删除用户的相关说明：

![](img/5be0f470baac647c43cf69340239d97c_13.png)

![](img/5be0f470baac647c43cf69340239d97c_15.png)

*   **仅删除用户账号**：使用 `userdel` 命令不加选项，仅删除用户账号，但保留其家目录。
    ```bash
    userdel user1
    ```
*   **删除用户及其家目录**：使用 `-r` 选项，可以在删除用户的同时，一并删除其家目录。
    ```bash
    userdel -r user100
    ```
    需要注意的是，此命令只会删除用户的家目录，用户在其他路径创建的文件不会被自动清除。

## 组管理

组是用户的集合，其主要作用是在设置文件或目录权限时，可以针对一个组内的所有用户进行统一授权。接下来我们学习组的管理。

![](img/5be0f470baac647c43cf69340239d97c_17.png)

### 创建组

![](img/5be0f470baac647c43cf69340239d97c_19.png)

![](img/5be0f470baac647c43cf69340239d97c_21.png)

创建新组的命令是 `groupadd`。组的信息会被保存在 `/etc/group` 文件中。

![](img/5be0f470baac647c43cf69340239d97c_23.png)

![](img/5be0f470baac647c43cf69340239d97c_25.png)

例如，创建一个名为 `student` 的组：
```bash
groupadd student
```

### 组信息文件详解

要查看系统中的所有组信息，需要查看 `/etc/group` 文件。该文件中每一行代表一个组，各字段以冒号 `:` 分隔。

以下是每个字段的含义：

*   **组名**：组的名称。
*   **组密码占位符**：通常为 `x`，真实的组密码加密后存放在 `/etc/gshadow` 文件中。
*   **组ID (GID)**：组的唯一数字标识。
*   **组内用户列表**：属于该附加组的用户成员列表，多个用户名之间用逗号分隔。

![](img/5be0f470baac647c43cf69340239d97c_27.png)

![](img/5be0f470baac647c43cf69340239d97c_29.png)

### 组密码文件

![](img/5be0f470baac647c43cf69340239d97c_31.png)

![](img/5be0f470baac647c43cf69340239d97c_33.png)

组的密码信息存放在 `/etc/gshadow` 文件中。在现代Linux系统中，直接为组设置密码并分配组管理员的场景已不常见，通常由root用户直接管理组成员。

### 修改组属性

![](img/5be0f470baac647c43cf69340239d97c_35.png)

对于已存在的组，可以使用 `groupmod` 命令修改其属性。

![](img/5be0f470baac647c43cf69340239d97c_37.png)

以下是修改组属性的示例：

*   **修改组ID**：将 `student` 组的GID改为 `5678`。
    ```bash
    groupmod -g 5678 student
    ```
*   **修改组名**：将 `student` 组改名为 `stugrp`。
    ```bash
    groupmod -n stugrp student
    ```

![](img/5be0f470baac647c43cf69340239d97c_39.png)

### 管理组成员

![](img/5be0f470baac647c43cf69340239d97c_41.png)

`gpasswd` 命令是管理组成员的主要工具，可用于设置组密码、添加或删除组内用户。

![](img/5be0f470baac647c43cf69340239d97c_43.png)

![](img/5be0f470baac647c43cf69340239d97c_44.png)

以下是管理组成员的操作：

*   **添加用户到组**：将用户 `user6` 添加到 `stugrp` 组中。
    ```bash
    gpasswd -a user6 stugrp
    ```
*   **从组中移除用户**：将用户 `user6` 从 `stugrp` 组中移除。
    ```bash
    gpasswd -d user6 stugrp
    ```

![](img/5be0f470baac647c43cf69340239d97c_46.png)

### 删除组

![](img/5be0f470baac647c43cf69340239d97c_48.png)

![](img/5be0f470baac647c43cf69340239d97c_50.png)

删除组的命令是 `groupdel`。删除操作很简单，但需要注意，如果一个组是某个用户的基本组（初始组），则不允许被删除。

例如，删除 `stugrp` 组：
```bash
groupdel stugrp
```

## 总结

![](img/5be0f470baac647c43cf69340239d97c_52.png)

![](img/5be0f470baac647c43cf69340239d97c_54.png)

本节课中我们一起学习了Linux用户和组管理的重要操作。我们掌握了如何使用 `usermod` 修改用户属性，使用 `userdel` 删除用户。在组管理方面，我们学习了使用 `groupadd`、`groupmod`、`gpasswd` 和 `groupdel` 命令进行组的创建、属性修改、成员管理及删除。理解并熟练运用这些命令，是进行有效的系统用户权限管理的基础。下一节，我们将进入更核心的Linux文件权限管理学习。