# Linux运维全套培训课程：P17：组管理命令、组相关文件详解 📚

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_1.png)

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_3.png)

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_5.png)

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_7.png)

在本节课中，我们将要学习Linux系统中的组管理。上一节我们介绍了用户管理命令，本节中我们来看看如何创建、修改、删除用户组，以及如何管理组内的成员。理解组的概念对于后续学习文件权限至关重要。

## 修改用户属性 🔧

用户创建后，其属性（如UID、家目录、描述信息等）可能需要调整。`usermod`命令用于修改已存在用户的基本信息。

其命令格式与`useradd`类似，通过指定选项来修改不同属性。例如，修改用户`user1`的UID为6666：
```bash
usermod -u 6666 user1
```
修改用户的描述信息（如添加邮箱）：
```bash
usermod -c "xxx@163.com" user1
```
将用户添加到多个附加组（如运维组、开发组、测试组）：
```bash
usermod -G yunwei,kaifa,ceshi user1
```
修改用户的默认解释器（Shell）：
```bash
usermod -s /bin/sh user1
```

## 删除用户 🗑️

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_9.png)

删除用户使用`userdel`命令。该命令可以仅删除用户账户，也可以同时删除用户的家目录及相关文件。

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_11.png)

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_13.png)

以下是删除用户的不同方式：
*   仅删除用户账户`user1`，保留其家目录：
    ```bash
    userdel user1
    ```
*   删除用户账户`user100`的同时，删除其家目录：
    ```bash
    userdel -r user100
    ```
*   如果仅删除了用户，可以手动删除其遗留的家目录：
    ```bash
    rm -rf /home/user1
    ```
**注意**：`userdel -r`命令只会删除用户的家目录，用户在其他路径创建的文件不会被自动清除。

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_15.png)

## 创建用户组 🏗️

组用于对用户进行逻辑分类，便于批量管理权限。创建新组使用`groupadd`命令。

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_17.png)

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_19.png)

创建一个名为`student`的组（系统自动分配GID）：
```bash
groupadd student
```
创建组时指定GID（例如1005）：
```bash
groupadd -g 1005 student
```
组信息被保存在`/etc/group`文件中。

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_21.png)

## 组信息文件详解 📄

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_23.png)

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_25.png)

`/etc/group`文件存储了系统中所有组的信息。每一行代表一个组，字段由冒号`:`分隔。

查看文件内容：
```bash
cat /etc/group
```
一个典型的行如下所示：
```
student:x:1005:user6,user7
```
以下是每个字段的含义：
*   **组名**：组的名称，如`student`。
*   **组密码占位符**：`x`表示密码信息存储在`/etc/gshadow`文件中。
*   **组ID (GID)**：组的唯一数字标识，如`1005`。
*   **组成员列表**：属于该组的用户列表，用户名之间用逗号分隔，如`user6,user7`。

## 组密码文件

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_27.png)

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_29.png)

组的加密密码信息存储在`/etc/gshadow`文件中。在现代Linux系统中，为组设置密码和管理员的需求已不常见，通常无需手动管理此文件。

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_31.png)

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_33.png)

## 修改组属性 ✏️

使用`groupmod`命令可以修改已存在组的属性，如组ID或组名。

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_35.png)

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_37.png)

修改`student`组的GID为5678：
```bash
groupmod -g 5678 student
```
将`student`组改名为`stugrp`：
```bash
groupmod -n stugrp student
```

## 管理组成员 👥

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_39.png)

`gpasswd`命令是管理组成员的主要工具，可用于添加或删除组内用户。

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_41.png)

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_43.png)

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_44.png)

以下是管理组成员的操作：
*   将用户`user6`添加到`stugrp`组中：
    ```bash
    gpasswd -a user6 stugrp
    ```
*   将用户`user6`从`stugrp`组中移除：
    ```bash
    gpasswd -d user6 stugrp
    ```

## 删除用户组 🗑️

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_46.png)

删除组使用`groupdel`命令。

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_48.png)

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_50.png)

删除`stugrp`组：
```bash
groupdel stugrp
```
**重要提示**：如果一个组是某个用户的基本组（主组），则该组不能被删除。删除命令会提示“不能移除用户的主组”。

---

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_52.png)

![](img/3a47ce50760bb4c8e66c4ad40d6212f6_54.png)

本节课中我们一起学习了Linux下的组管理。我们掌握了如何使用`groupadd`、`groupmod`、`groupdel`和`gpasswd`命令来创建、修改、删除组以及管理组成员。同时，我们也了解了`/etc/group`和`/etc/gshadow`这两个重要的组信息配置文件。组是Linux权限管理体系的基础，理解它能为后续学习文件权限控制打下坚实的基础。