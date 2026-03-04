# Linux认证课程：P18：3.04-autofs自动文件系统 🗂️

![](img/29f1ce75e0c1a80210239b3f84f569c4_0.png)

![](img/29f1ce75e0c1a80210239b3f84f569c4_2.png)

![](img/29f1ce75e0c1a80210239b3f84f569c4_4.png)

在本节课中，我们将学习如何配置 **autofs** 自动文件系统，以实现按需自动挂载远程NFS共享目录。这是RHCE/RHCSA认证考试中的一个重要考点。

![](img/29f1ce75e0c1a80210239b3f84f569c4_6.png)

![](img/29f1ce75e0c1a80210239b3f84f569c4_8.png)

![](img/29f1ce75e0c1a80210239b3f84f569c4_10.png)

## 概述与环境说明

![](img/29f1ce75e0c1a80210239b3f84f569c4_12.png)

上一节我们介绍了文件系统的基本概念。本节中，我们来看看如何实现自动挂载。

![](img/29f1ce75e0c1a80210239b3f84f569c4_14.png)

考试题目要求配置 **autofs**，以便为远程LDAP用户自动挂载其主目录。在考试环境中，会有一台服务器（例如 `server1`）提供LDAP用户认证，并通过NFS共享用户的主目录。我们的任务是在客户端（例如 `rhce` 机器）上配置autofs，使得当用户登录时，其主目录能自动出现。

![](img/29f1ce75e0c1a80210239b3f84f569c4_16.png)

> **注意**：我们的练习环境没有配置LDAP服务器和用户，但配置autofs的核心步骤是相同的。我们将通过访问一个特定的挂载点来验证配置是否成功。

![](img/29f1ce75e0c1a80210239b3f84f569c4_18.png)

## 核心概念：本地与网络文件系统

在深入配置之前，需要理解两种文件系统：
*   **本地文件系统**：存储在本机磁盘上，例如 `/dev/vda1` 上的 `xfs` 或 `ext4` 分区。
*   **网络文件系统 (NFS)**：资源存储在网络中的其他服务器上，通过共享方式提供给客户端使用。这类似于Windows的网络共享（SMB/CIFS）。

考试中，用户主目录就是通过NFS从 `server1` 共享出来的。

## 手动挂载NFS共享

![](img/29f1ce75e0c1a80210239b3f84f569c4_20.png)

![](img/29f1ce75e0c1a80210239b3f84f569c4_22.png)

在配置自动挂载前，我们先了解如何手动访问NFS资源。

![](img/29f1ce75e0c1a80210239b3f84f569c4_24.png)

首先，需要安装NFS客户端工具包：
```bash
sudo yum install nfs-utils -y
```
然后，可以查看服务器 `server1` 共享了哪些目录：
```bash
showmount -e server1
```
命令输出会显示类似 `server1:/home` 的信息，表示 `server1` 共享了 `/home` 目录。

手动挂载该共享到本地目录（例如 `/mnt`）的命令是：
```bash
sudo mount server1:/home /mnt
```
或者，如果明确知道共享目录下的子路径（如用户主目录），可以：
```bash
sudo mount server1:/home/ldapuser0 /opt
```
挂载成功后，即可访问远程目录。但这种方式需要手动操作，且挂载会一直存在。

## 配置autofs实现自动挂载

![](img/29f1ce75e0c1a80210239b3f84f569c4_26.png)

autofs 服务可以实现“触发挂载”或“按需挂载”：当访问特定目录时自动挂载远程资源，并在闲置一段时间（默认5分钟）后自动卸载。

![](img/29f1ce75e0c1a80210239b3f84f569c4_28.png)

![](img/29f1ce75e0c1a80210239b3f84f569c4_30.png)

以下是配置autofs的完整步骤。

![](img/29f1ce75e0c1a80210239b3f84f569c4_32.png)

![](img/29f1ce75e0c1a80210239b3f84f569c4_34.png)

### 第一步：安装autofs软件包
```bash
sudo yum install autofs -y
```

![](img/29f1ce75e0c1a80210239b3f84f569c4_36.png)

### 第二步：编辑主配置文件 `/etc/auto.master`
这个文件定义了“挂载点父目录”和对应的“映射文件”。
我们需要在 `/etc/auto.master` 文件中添加一行，指明在 `/home` 目录下设置自动挂载，并指定一个映射文件（例如 `/etc/auto.home`）来定义详细规则。
```bash
/home /etc/auto.home
```
这行配置的意思是：监控 `/home` 目录。当有人访问 `/home` 下的特定子目录时，参照 `/etc/auto.home` 文件中的规则执行操作。

### 第三步：创建并编辑映射文件
现在创建上一步中指定的映射文件 `/etc/auto.home`。
在这个文件中，我们定义具体的触发规则和挂载参数。格式为：
```
[子目录名] [挂载选项] [服务器:共享路径]
```
根据题目要求，我们需要为 `ldapuser0` 用户配置。因此，在 `/etc/auto.home` 文件中写入：
```bash
ldapuser0 -fstype=nfs,rw server1:/home/ldapuser0
```
*   `ldapuser0`：这是“触发器”或“引爆点”。当访问 `/home/ldapuser0` 时，规则生效。
*   `-fstype=nfs,rw`：指定文件系统类型为NFS，挂载为可读写（`rw`）。`rw`参数通常可省略，因为服务器共享时已设置。
*   `server1:/home/ldapuser0`：指定要挂载的远程NFS资源。

![](img/29f1ce75e0c1a80210239b3f84f569c4_38.png)

### 第四步：启动并启用autofs服务
```bash
sudo systemctl enable --now autofs
sudo systemctl restart autofs  # 确保配置生效
```

![](img/29f1ce75e0c1a80210239b3f84f569c4_40.png)

![](img/29f1ce75e0c1a80210239b3f84f569c4_41.png)

## 验证配置

![](img/29f1ce75e0c1a80210239b3f84f569c4_43.png)

配置完成后，即可进行验证。

![](img/29f1ce75e0c1a80210239b3f84f569c4_45.png)

![](img/29f1ce75e0c1a80210239b3f84f569c4_47.png)

![](img/29f1ce75e0c1a80210239b3f84f569c4_49.png)

1.  首先，查看 `/home` 目录，此时 `ldapuser0` 子目录应该不存在。
    ```bash
    ls /home
    ```

2.  然后，尝试访问触发目录 `/home/ldapuser0`。autofs服务会检测到这次访问，并自动创建该目录，同时将远程NFS共享挂载过来。
    ```bash
    ls /home/ldapuser0
    ```
    如果输出显示 **`Permission denied`**，即表示成功！因为这证明目录已存在（由autofs创建并挂载），只是当前用户（root）没有权限访问属于 `ldapuser0` 的目录。

3.  可以对比访问一个不存在的用户目录，例如 `ldapuser1`，会得到“没有那个文件或目录”的提示，这与 `ldapuser0` 的“权限被拒绝”提示不同，从而证明autofs仅为 `ldapuser0` 生效。
    ```bash
    ls /home/ldapuser1
    ```

4.  等待约5分钟不访问 `/home/ldapuser0` 后，autofs会自动卸载该共享，目录会消失。也可以手动重启autofs服务来立即卸载。
    ```bash
    sudo systemctl restart autofs
    ls /home  # 再次查看，ldapuser0目录应已消失
    ```

![](img/29f1ce75e0c1a80210239b3f84f569c4_51.png)

## 总结

![](img/29f1ce75e0c1a80210239b3f84f569c4_53.png)

![](img/29f1ce75e0c1a80210239b3f84f569c4_55.png)

![](img/29f1ce75e0c1a80210239b3f84f569c4_57.png)

本节课中我们一起学习了 **autofs自动文件系统** 的配置。

![](img/29f1ce75e0c1a80210239b3f84f569c4_59.png)

我们首先区分了本地文件系统与网络文件系统（NFS）。然后，通过手动挂载NFS共享理解了基本操作。最后，我们重点掌握了配置autofs的核心步骤：
1.  安装 `autofs` 软件包。
2.  在主配置文件 `/etc/auto.master` 中指定监控目录和映射文件。
3.  在映射文件（如 `/etc/auto.home`）中定义具体的触发挂载规则。
4.  启动 `autofs` 服务。

![](img/29f1ce75e0c1a80210239b3f84f569c4_61.png)

配置成功后，实现了当访问特定目录（如 `/home/ldapuser0`）时，自动挂载远程NFS共享，并在闲置后自动卸载的功能。在考试中，此配置确保了远程LDAP用户登录时能自动获得其主目录。