# RHCSA认证精讲教程：P18：3.04-autofs自动文件系统 🗂️

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_0.png)

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_2.png)

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_4.png)

在本节课中，我们将学习如何配置 `autofs`（自动文件系统），以实现按需自动挂载远程NFS共享目录。这是RHCSA考试中的一个重要考点，核心目标是让远程用户的主目录能够在登录时自动可用。

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_6.png)

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_8.png)

## 环境与需求概述

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_10.png)

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_12.png)

在考试环境中，会有一台服务器（例如 `server1`）通过LDAP提供用户账号（如 `ldapuser0`），并通过NFS共享该用户的主目录。我们的客户端机器（`rhcsa`）需要配置 `autofs`，使得当 `ldapuser0` 用户登录时，能自动将其远程主目录挂载到本地的 `/home/ldapuser0` 路径下。

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_14.png)

**核心任务**：配置 `autofs`，实现访问 `/home/ldapuser0` 时自动挂载 `server1:/home/ldapuser0`。

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_16.png)

## 理解核心概念：本地与网络文件系统

在深入配置之前，我们先区分两种文件系统。

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_18.png)

上一节我们介绍了考试需求，本节中我们来看看文件系统的基础概念。

*   **本地文件系统**：存储在本机磁盘上，例如 `/dev/vda1` 上的 `xfs` 或 `ext4` 分区。系统通过 `/etc/fstab` 文件管理其挂载。
*   **网络文件系统**：资源存储在网络中的其他服务器上。最常用的是 **NFS**。要使用NFS共享，需要先确认服务器提供了哪些共享资源。

以下是检查NFS共享的命令：
```bash
showmount -e server1
```
此命令会列出 `server1` 服务器上所有导出的共享目录。

## 手动挂载NFS共享

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_20.png)

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_22.png)

在配置自动挂载前，我们先了解如何手动挂载NFS共享，这有助于理解后续的自动挂载配置。

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_24.png)

如果 `showmount` 命令不可用，通常是因为缺少 `nfs-utils` 软件包，需要先安装：
```bash
yum install -y nfs-utils
```
安装后，即可使用 `showmount` 命令查看共享。假设查看到 `server1` 共享了 `/home` 目录。

手动挂载该共享到本地 `/mnt` 目录的命令如下：
```bash
mount server1:/home /mnt
```
执行后，访问 `/mnt` 就能看到服务器上的内容。这是一种持久化的挂载方式，直到手动卸载或重启。

然而，考试要求的是更智能的“按需自动挂载”，这就需要用到 `autofs` 服务。

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_26.png)

## 配置autofs实现自动挂载 🚀

`autofs` 服务就像一个智能管家。我们告诉它“监视哪个目录”，以及“当有人访问该目录下的特定子目录时，应该自动挂载哪个远程资源”。配置完成后，只有在真正访问时资源才会出现，闲置一段时间（默认5分钟）后会自动卸载，既方便又安全。

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_28.png)

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_30.png)

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_32.png)

以下是配置 `autofs` 的详细步骤：

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_34.png)

1.  **安装autofs软件包**
    ```bash
    yum install -y autofs
    ```

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_36.png)

2.  **配置主映射文件 `/etc/auto.master`**
    这个文件告诉 `autofs` 需要监视的顶层目录以及对应的映射文件。
    我们需要在 `/etc/auto.master` 文件中添加一行：
    ```
    /home   /etc/auto.home
    ```
    **含义**：请监视 `/home` 目录。当有人访问 `/home` 下的子目录时，具体操作规则请查看 `/etc/auto.home` 这个文件。

3.  **创建并配置映射文件 `/etc/auto.home`**
    这个文件定义了具体的触发挂载规则。
    创建并编辑该文件，添加以下内容：
    ```
    ldapuser0  -fstype=nfs,rw  server1:/home/ldapuser0
    ```
    **含义**：当有人访问 `/home/ldapuser0` 时，自动以 `nfs` 文件系统类型、读写 (`rw`) 权限，将 `server1:/home/ldapuser0` 挂载过来。

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_38.png)

4.  **启动并启用autofs服务**
    配置完成后，需要启动服务使其生效。
    ```bash
    systemctl enable --now autofs
    ```

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_40.png)

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_41.png)

## 验证自动挂载效果 ✅

配置完成后，我们可以验证 `autofs` 是否工作正常。

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_43.png)

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_45.png)

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_47.png)

首先，查看 `/home` 目录，此时 `ldapuser0` 子目录还不存在：
```bash
ls /home
```

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_49.png)

然后，尝试访问或列出 `/home/ldapuser0` 目录：
```bash
ls /home/ldapuser0
```
此时会发生两件事：
1.  `autofs` 服务被触发，自动创建 `/home/ldapuser0` 目录。
2.  将 `server1:/home/ldapuser0` 挂载到该目录。

由于该目录权限属于 `ldapuser0` 用户，`root` 用户访问时会得到 `Permission denied` 的错误。**这恰恰说明挂载成功了**，因为系统能识别到该目录的存在及其权限属性，而不是提示“没有那个文件或目录”。

你可以对比访问一个不存在的用户目录，例如 `ls /home/ldapuser1`，系统会直接提示目录不存在。

等待约5分钟不访问 `/home/ldapuser0` 后，再次执行 `ls /home`，会发现该目录自动消失了，这证明了自动卸载功能。

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_51.png)

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_53.png)

## 课程总结

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_55.png)

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_57.png)

本节课中我们一起学习了 `autofs` 自动文件系统的配置。

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_59.png)

*   **核心原理**：`autofs` 实现按需自动挂载和闲置自动卸载，提升安全性与资源管理效率。
*   **配置流程**：
    1.  安装 `autofs` 软件包。
    2.  在 `/etc/auto.master` 中指定监视目录和映射文件。
    3.  在映射文件（如 `/etc/auto.home`）中定义子目录与远程资源的对应关系。
    4.  启动 `autofs` 服务。
*   **验证方法**：通过 `ls` 命令访问目标目录，若出现权限错误而非“目录不存在”，即表示自动挂载成功。

![](img/f7cbb396d96a3f41d46d18ecdd9500ab_61.png)

这个考点在RHCSA考试中要求精确配置，理解其“触发挂载”的机制是关键。只要按照上述步骤操作，即可轻松完成题目要求。