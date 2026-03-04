# Linux运维进阶：P17：组管理命令、组相关文件详解 🔧

![](img/73cf084defea771374643b523cea5725_1.png)

![](img/73cf084defea771374643b523cea5725_3.png)

在本节课中，我们将要学习如何修改已存在用户的属性，以及如何进行组的管理，包括创建、修改、删除组，以及如何将用户添加到组中或从组中移除。这些操作是Linux系统管理的基础。

![](img/73cf084defea771374643b523cea5725_5.png)

![](img/73cf084defea771374643b523cea5725_7.png)

## 修改用户属性

上一节我们介绍了如何创建用户，本节中我们来看看如何修改已存在的用户信息。当用户创建后，可能需要修改其UID、家目录、描述信息或登录解释器等属性。

修改用户属性的命令是 `usermod`，其选项与创建用户时使用的 `useradd` 命令基本一致。

**命令格式**：
```bash
usermod [选项] 用户名
```

以下是 `usermod` 命令的常用操作示例：

*   **修改用户UID**：将用户 `user1` 的UID修改为 `6666`。
    ```bash
    usermod -u 6666 user1
    ```
*   **修改用户描述信息**：为用户 `user1` 添加描述信息，例如邮箱。
    ```bash
    usermod -c "xxx@163.com" user1
    ```
*   **修改用户附加组**：将用户 `user1` 加入到 `yunwei`（运维）、`kaifa`（开发）、`ceshi`（测试）组中。
    ```bash
    usermod -G yunwei,kaifa,ceshi user1
    ```
*   **修改用户登录解释器**：将用户 `user1` 的登录解释器修改为 `/bin/sh`。
    ```bash
    usermod -s /bin/sh user1
    ```

## 删除用户

删除用户的操作相对简单。使用 `userdel` 命令可以删除指定的用户。

![](img/73cf084defea771374643b523cea5725_9.png)

![](img/73cf084defea771374643b523cea5725_11.png)

**命令格式**：
```bash
userdel [选项] 用户名
```

![](img/73cf084defea771374643b523cea5725_13.png)

以下是删除用户时的两种常见情况：

![](img/73cf084defea771374643b523cea5725_15.png)

*   **仅删除用户账号**：删除用户 `user1`，但保留其家目录。
    ```bash
    userdel user1
    ```
*   **删除用户账号及其家目录**：删除用户 `user100` 的同时，一并删除其家目录。
    ```bash
    userdel -r user100
    ```
> 注意：`userdel -r` 命令只会删除用户的家目录。如果该用户在其他路径创建了文件，需要手动清理。

## 组管理

组是用户的集合，其主要作用是方便进行权限管理。接下来，我们学习如何管理组。

![](img/73cf084defea771374643b523cea5725_17.png)

### 创建组

![](img/73cf084defea771374643b523cea5725_19.png)

![](img/73cf084defea771374643b523cea5725_21.png)

创建新组的命令是 `groupadd`。

![](img/73cf084defea771374643b523cea5725_23.png)

**命令格式**：
```bash
groupadd [选项] 组名
```

![](img/73cf084defea771374643b523cea5725_25.png)

例如，创建一个名为 `student` 的组：
```bash
groupadd student
```
组信息会被保存在 `/etc/group` 文件中。

### 组信息文件详解

要查看系统中的所有组信息，可以查看 `/etc/group` 文件。

**文件格式**：
该文件每行代表一个组，各字段以冒号 `:` 分隔。
```
组名:组密码占位符:组ID(GID):组内成员列表
```

*   **组名**：组的名称。
*   **组密码占位符**：通常为 `x`，真实密码存储在 `/etc/gshadow` 文件中。
*   **组ID (GID)**：组的唯一数字标识。
*   **组内成员列表**：属于该组的用户列表，用户名之间用逗号分隔。

![](img/73cf084defea771374643b523cea5725_27.png)

![](img/73cf084defea771374643b523cea5725_29.png)

例如，一行 `yunwei:x:1238:user5` 表示存在一个名为 `yunwei`、GID为 `1238` 的组，其中包含用户 `user5`。

![](img/73cf084defea771374643b523cea5725_31.png)

![](img/73cf084defea771374643b523cea5725_33.png)

### 修改组属性

对于已存在的组，可以使用 `groupmod` 命令修改其属性。

![](img/73cf084defea771374643b523cea5725_35.png)

**命令格式**：
```bash
groupmod [选项] 组名
```

以下是修改组属性的操作示例：

![](img/73cf084defea771374643b523cea5725_37.png)

*   **修改组ID**：将 `student` 组的GID修改为 `5678`。
    ```bash
    groupmod -g 5678 student
    ```
*   **修改组名**：将 `student` 组重命名为 `stugrp`。
    ```bash
    groupmod -n stugrp student
    ```

### 管理组成员

![](img/73cf084defea771374643b523cea5725_39.png)

`gpasswd` 命令是管理组成员的主要工具，可用于设置组密码、添加或删除组内用户。

![](img/73cf084defea771374643b523cea5725_41.png)

**命令格式**：
```bash
gpasswd [选项] 组名
```

![](img/73cf084defea771374643b523cea5725_43.png)

![](img/73cf084defea771374643b523cea5725_44.png)

以下是管理组成员的操作示例：

*   **添加用户到组**：将用户 `user6` 和 `user7` 添加到 `stugrp` 组。
    ```bash
    gpasswd -a user6 stugrp
    gpasswd -a user7 stugrp
    ```
*   **从组中移除用户**：将用户 `user6` 从 `stugrp` 组中移除。
    ```bash
    gpasswd -d user6 stugrp
    ```

### 删除组

![](img/73cf084defea771374643b523cea5725_46.png)

![](img/73cf084defea771374643b523cea5725_48.png)

删除组的命令是 `groupdel`。

![](img/73cf084defea771374643b523cea5725_50.png)

**命令格式**：
```bash
groupdel 组名
```

例如，删除 `stugrp` 组：
```bash
groupdel stugrp
```
> 注意：如果一个组是某个用户的主组（基本组），则该组不允许被删除。

---

![](img/73cf084defea771374643b523cea5725_52.png)

![](img/73cf084defea771374643b523cea5725_54.png)

本节课中我们一起学习了用户和组的管理操作。我们掌握了如何使用 `usermod` 修改用户属性，使用 `userdel` 删除用户。在组管理方面，我们学会了使用 `groupadd`、`groupmod`、`gpasswd` 和 `groupdel` 命令来创建、修改、管理成员以及删除组，并了解了组信息文件 `/etc/group` 的结构。这些知识是后续学习Linux文件权限管理的重要基础。