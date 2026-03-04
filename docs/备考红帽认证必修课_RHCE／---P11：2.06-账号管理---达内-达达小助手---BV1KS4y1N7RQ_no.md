# 备考红帽认证必修课：P11：2.06-账号管理 👤

![](img/6cef9a3aef2ba50cdadab5e539a755dc_0.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_2.png)

在本节课中，我们将要学习Linux系统中的用户和组账号管理。这是控制文件访问权限和系统登录的基础，也是RHCE/RHCSA认证考试中的常见考点。我们将从基本概念入手，逐步学习如何创建、修改、删除用户和组，并理解它们之间的关系。

---

![](img/6cef9a3aef2ba50cdadab5e539a755dc_4.png)

## 用户账号概述 🔑

![](img/6cef9a3aef2ba50cdadab5e539a755dc_6.png)

Linux系统中的用户账号主要用于两个目的：**登录系统**和**控制文件访问权限**。每个用户都有一个唯一的用户名和数字标识（UID）。系统通过检查文件的归属和权限，来决定用户能对其进行何种操作。

![](img/6cef9a3aef2ba50cdadab5e539a755dc_8.png)

用户账号的信息主要存储在以下两个系统文件中：
*   **`/etc/passwd`**：存储用户的基本信息，如用户名、UID、基本组ID（GID）、主目录和登录Shell。
*   **`/etc/shadow`**：存储用户的加密密码及其相关属性（如有效期），此文件普通用户无法读取，增强了安全性。

组账号用于对用户进行分组管理，方便批量分配权限。组信息存储在 `/etc/group` 和 `/etc/gshadow` 文件中。

![](img/6cef9a3aef2ba50cdadab5e539a755dc_10.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_11.png)

上一节我们介绍了账号的基本概念，本节中我们来看看如何具体管理用户账号。

![](img/6cef9a3aef2ba50cdadab5e539a755dc_13.png)

---

![](img/6cef9a3aef2ba50cdadab5e539a755dc_15.png)

## 用户账号管理 🛠️

管理用户账号的核心操作包括增、删、改、查。以下是常用的命令和选项。

![](img/6cef9a3aef2ba50cdadab5e539a755dc_17.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_19.png)

### 添加用户 (`useradd`)

最基础的命令是 `useradd`，后接用户名即可创建一个具有默认属性的用户。

```bash
useradd zhangsan
```

默认创建的用户，其主目录在 `/home/用户名` 下，UID从1000开始分配，并使用 `/bin/bash` 作为登录Shell。

但通常我们需要定制用户属性，这时可以使用以下选项：

![](img/6cef9a3aef2ba50cdadab5e539a755dc_21.png)

*   **`-u`**：指定用户的UID。
    ```bash
    useradd -u 666 lisi
    ```
*   **`-d`**：指定用户的主目录路径。
*   **`-s`**：指定用户的登录Shell。若要禁止用户登录，可指定为 `/sbin/nologin`。
    ```bash
    useradd -s /sbin/nologin wangwu
    ```
*   **`-G`**：指定用户的**附加组**。关于基本组与附加组的区别，下文会详细说明。
    ```bash
    useradd -G admins zhangsan
    ```

### 修改用户 (`usermod`)

如果创建用户时未指定某些属性，或需要修改，可以使用 `usermod` 命令。其选项与 `useradd` 类似。

![](img/6cef9a3aef2ba50cdadab5e539a755dc_23.png)

例如，修改用户 `lisi` 的UID为888：
```bash
usermod -u 888 lisi
```

将一个已存在的用户 `stu01` **追加**到 `admins` 组（不影响其原有所属组）：
```bash
usermod -aG admins stu01
```
**注意**：使用 `usermod -G` 时若不添加 `-a` 选项，则会用指定的组**替换**掉用户当前的附加组列表。

### 设置用户密码 (`passwd`)

新创建的用户没有密码，需要使用 `passwd` 命令设置。

![](img/6cef9a3aef2ba50cdadab5e539a755dc_25.png)

```bash
passwd zhangsan
```
执行后，根据提示输入两次密码即可。

在考试或需要批量操作时，可以使用管道符 `|` 进行快速密码设置：
```bash
echo "password123" | passwd --stdin zhangsan
```
这条命令将 `"password123"` 设置为用户 `zhangsan` 的密码，无需交互确认。

![](img/6cef9a3aef2ba50cdadab5e539a755dc_27.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_29.png)

### 查看用户属性 (`id`)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_31.png)

使用 `id` 命令可以查看用户的UID、GID以及所属的组列表。

![](img/6cef9a3aef2ba50cdadab5e539a755dc_33.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_34.png)

```bash
id zhangsan
```

![](img/6cef9a3aef2ba50cdadab5e539a755dc_36.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_38.png)

### 删除用户 (`userdel`)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_40.png)

要删除一个用户，使用 `userdel` 命令。添加 `-r` 选项可以同时删除该用户的主目录和邮件池。

![](img/6cef9a3aef2ba50cdadab5e539a755dc_42.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_44.png)

```bash
userdel -r zhangsan
```
**警告**：在实际工作中，执行删除操作前务必确认用户数据已备份。

![](img/6cef9a3aef2ba50cdadab5e539a755dc_46.png)

---

![](img/6cef9a3aef2ba50cdadab5e539a755dc_48.png)

## 组账号管理 👥

![](img/6cef9a3aef2ba50cdadab5e539a755dc_50.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_52.png)

组的管理相对简单，核心目的是为了方便对用户进行归类。

![](img/6cef9a3aef2ba50cdadab5e539a755dc_54.png)

### 添加组 (`groupadd`)

```bash
groupadd admins
```

![](img/6cef9a3aef2ba50cdadab5e539a755dc_56.png)

### 删除组 (`groupdel`)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_58.png)

```bash
groupdel admins
```
注意，如果组是某个用户的基本组，则无法直接删除。

### 管理组成员

除了在创建用户时用 `-G` 指定，或之后用 `usermod -aG` 修改，还有一个专用命令 `gpasswd` 来管理组成员。

*   将用户 `zhangsan` 添加到组 `admins`：
    ```bash
    gpasswd -a zhangsan admins
    ```
*   将用户 `zhangsan` 从组 `admins` 中移除：
    ```bash
    gpasswd -d zhangsan admins
    ```

### 基本组与附加组

![](img/6cef9a3aef2ba50cdadab5e539a755dc_60.png)

理解这两个概念对权限管理至关重要：
*   **基本组 (Primary Group)**：每个用户必须属于且只能属于一个基本组。创建用户时，系统默认会创建一个与用户名同名的组作为其基本组。在 `id` 命令输出中，第一个显示的GID对应的就是基本组。
*   **附加组 (Supplementary Group)**：用户除了基本组外，还可以属于零个或多个附加组。赋予用户对某些文件的权限，通常是通过将其加入对应的附加组来实现的。

例如，用户 `zhangsan` 的基本组是 `zhangsan`，附加组是 `admins` 和 `developers`。当他访问一个属于 `developers` 组的文件时，系统会检查他的附加组身份。

---

![](img/6cef9a3aef2ba50cdadab5e539a755dc_62.png)

## 实战演练：解题示例 📝

现在，我们运用所学知识来解决典型的考试题目。

**题目1**：创建一个名为 `timed` 的用户，其UID为 `2020`，密码设置为 `i love linux`。

![](img/6cef9a3aef2ba50cdadab5e539a755dc_64.png)

**解答**：
```bash
# 创建用户并指定UID
useradd -u 2020 timed
# 使用非交互方式设置密码
echo "i love linux" | passwd --stdin timed
```

![](img/6cef9a3aef2ba50cdadab5e539a755dc_66.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_68.png)

**题目2**：
1.  创建名为 `admins` 的组。
2.  创建用户 `zhangsan` 和 `lisi`，他们的附加组均为 `admins`。
3.  创建用户 `wangwu`，其登录Shell为 `/sbin/nologin`（不可登录），且不属于 `admins` 组。
4.  为所有用户设置密码 `redhat`。

![](img/6cef9a3aef2ba50cdadab5e539a755dc_70.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_72.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_74.png)

**解答**：
```bash
# 1. 创建组
groupadd admins

# 2. 创建用户并指定附加组
useradd -G admins zhangsan
useradd -G admins lisi

![](img/6cef9a3aef2ba50cdadab5e539a755dc_76.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_78.png)

# 3. 创建不可登录的用户
useradd -s /sbin/nologin wangwu

![](img/6cef9a3aef2ba50cdadab5e539a755dc_80.png)

# 4. 批量设置密码
echo "redhat" | passwd --stdin zhangsan
echo "redhat" | passwd --stdin lisi
echo "redhat" | passwd --stdin wangwu
```

![](img/6cef9a3aef2ba50cdadab5e539a755dc_82.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_83.png)

---

![](img/6cef9a3aef2ba50cdadab5e539a755dc_85.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_86.png)

## 总结 📚

![](img/6cef9a3aef2ba50cdadab5e539a755dc_88.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_89.png)

![](img/6cef9a3aef2ba50cdadab5e539a755dc_91.png)

本节课中我们一起学习了Linux账号管理的核心内容。我们首先了解了用户和组账号的作用及其存储文件（`/etc/passwd`, `/etc/shadow`, `/etc/group`）。然后，我们系统地学习了用户账号的**增 (`useradd`)**、**删 (`userdel`)**、**改 (`usermod`)**、**查 (`id`)** 以及**密码设置 (`passwd`)** 操作，并理解了通过管道快速设置密码的技巧。接着，我们探讨了组的管理命令 (`groupadd`, `groupdel`, `gpasswd`) 以及**基本组**与**附加组**的关键区别。最后，通过两道实战题目巩固了所有知识点。掌握这些命令和概念，是进行系统管理和通过相关认证考试的重要基础。