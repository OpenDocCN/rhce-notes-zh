# Redhat红帽 RHCE8.0认证体系课程：P48：autofs实例 🚀

在本节课中，我们将学习如何配置autofs（自动挂载器）来实现一个特定的应用场景：为远程用户自动挂载其家目录。这类似于Windows AD域环境中的漫游配置文件功能，用户在任何一台域成员机器上登录，都能访问到位于中央服务器上的个人文件。我们将通过一个完整的实例来演示这一过程。

## 概述与问题情境

上一节我们介绍了autofs的基本概念和配置方法。本节中，我们来看看一个具体的应用实例。

![](img/a9d58b1110d7019fdfa423b656bde411_1.png)

![](img/a9d58b1110d7019fdfa423b656bde411_2.png)

![](img/a9d58b1110d7019fdfa423b656bde411_3.png)

我们的目标是：当指定用户（例如 `remoteuser`）在客户端登录时，系统能自动将其家目录挂载为服务器端共享的对应目录。这样，用户文件就集中存储在服务器上，实现了跨客户端的文件共享和一致性访问。

![](img/a9d58b1110d7019fdfa423b656bde411_4.png)

![](img/a9d58b1110d7019fdfa423b656bde411_6.png)

首先，我们模拟一个常见问题：在客户端，如果删除了本地用户的家目录，用户将无法正常切换或登录。

```
# 在客户端创建用户
useradd -u 2000 remoteuser
# 查看家目录
ls -ld /home/remoteuser
# 删除家目录
rm -rf /home/remoteuser
# 尝试切换用户失败
su - remoteuser
```

![](img/a9d58b1110d7019fdfa423b656bde411_8.png)

![](img/a9d58b1110d7019fdfa423b656bde411_10.png)

![](img/a9d58b1110d7019fdfa423b656bde411_12.png)

此时切换用户会失败，因为家目录已不存在。我们的解决方案是使用autofs，在用户登录时自动挂载服务器上的家目录。

![](img/a9d58b1110d7019fdfa423b656bde411_13.png)

![](img/a9d58b1110d7019fdfa423b656bde411_14.png)

## 实验架构与目标

![](img/a9d58b1110d7019fdfa423b656bde411_16.png)

以下是本次实验需要完成的步骤：
1.  **服务端（Server A）**：发布 `/home/remoteuser` 目录为NFS共享。
2.  **客户端（Server B）**：配置autofs，监控 `/home` 目录。
3.  当用户 `remoteuser` 在客户端登录时，自动将服务端的 `/home/remoteuser` 目录挂载到客户端的 `/home/remoteuser`。

![](img/a9d58b1110d7019fdfa423b656bde411_18.png)

![](img/a9d58b1110d7019fdfa423b656bde411_19.png)

![](img/a9d58b1110d7019fdfa423b656bde411_21.png)

## 服务端配置

![](img/a9d58b1110d7019fdfa423b656bde411_23.png)

![](img/a9d58b1110d7019fdfa423b656bde411_25.png)

首先，我们在服务端进行NFS共享配置。

![](img/a9d58b1110d7019fdfa423b656bde411_26.png)

![](img/a9d58b1110d7019fdfa423b656bde411_28.png)

![](img/a9d58b1110d7019fdfa423b656bde411_30.png)

1.  编辑NFS导出配置文件 `/etc/exports`，添加共享目录。

    ```
    /home/remoteuser 192.168.145.0/24(rw,no_root_squash)
    ```

    这行配置表示将 `/home/remoteuser` 目录共享给 `192.168.145.0/24` 网段，权限为读写（`rw`），并且保留root权限（`no_root_squash`）。

![](img/a9d58b1110d7019fdfa423b656bde411_32.png)

![](img/a9d58b1110d7019fdfa423b656bde411_34.png)

2.  重新加载NFS配置，使更改生效。

    ```
    exportfs -rv
    ```

![](img/a9d58b1110d7019fdfa423b656bde411_36.png)

![](img/a9d58b1110d7019fdfa423b656bde411_38.png)

![](img/a9d58b1110d7019fdfa423b656bde411_39.png)

## 客户端配置

![](img/a9d58b1110d7019fdfa423b656bde411_41.png)

接下来，我们在客户端配置autofs。

![](img/a9d58b1110d7019fdfa423b656bde411_43.png)

1.  首先，可以测试一下是否能查看到服务端发布的共享。

    ```
    showmount -e 192.168.145.128
    ```

![](img/a9d58b1110d7019fdfa423b656bde411_44.png)

![](img/a9d58b1110d7019fdfa423b656bde411_45.png)

![](img/a9d58b1110d7019fdfa423b656bde411_46.png)

2.  编辑autofs的主配置文件 `/etc/auto.master`，指定监控点和对应的映射文件。

    ```
    /home /etc/auto.remote
    ```

    这表示autofs将监控 `/home` 目录，其挂载规则定义在 `/etc/auto.remote` 文件中。

![](img/a9d58b1110d7019fdfa423b656bde411_48.png)

![](img/a9d58b1110d7019fdfa423b656bde411_49.png)

![](img/a9d58b1110d7019fdfa423b656bde411_50.png)

3.  创建并编辑映射文件 `/etc/auto.remote`。

    ```
    remoteuser -fstype=nfs,rw 192.168.145.128:/home/remoteuser
    ```

    这行规则的含义是：当访问 `/home/remoteuser` 时，自动以NFS协议、读写方式挂载服务端 `192.168.145.128` 的 `/home/remoteuser` 目录。

![](img/a9d58b1110d7019fdfa423b656bde411_51.png)

![](img/a9d58b1110d7019fdfa423b656bde411_53.png)

![](img/a9d58b1110d7019fdfa423b656bde411_54.png)

4.  重启autofs服务，加载新配置。

    ```
    systemctl restart autofs
    ```

![](img/a9d58b1110d7019fdfa423b656bde411_56.png)

![](img/a9d58b1110d7019fdfa423b656bde411_57.png)

## 验证测试

![](img/a9d58b1110d7019fdfa423b656bde411_59.png)

![](img/a9d58b1110d7019fdfa423b656bde411_61.png)

![](img/a9d58b1110d7019fdfa423b656bde411_63.png)

![](img/a9d58b1110d7019fdfa423b656bde411_65.png)

配置完成后，我们进行验证。

![](img/a9d58b1110d7019fdfa423b656bde411_67.png)

![](img/a9d58b1110d7019fdfa423b656bde411_69.png)

1.  在客户端切换为 `remoteuser` 用户。

    ```
    su - remoteuser
    ```

![](img/a9d58b1110d7019fdfa423b656bde411_70.png)

![](img/a9d58b1110d7019fdfa423b656bde411_72.png)

2.  如果配置成功，系统将自动挂载远程目录，并正常进入家目录。你可以使用 `pwd` 命令查看当前路径，并使用 `touch` 命令创建测试文件。

    ```
    pwd
    touch testfile.txt
    ls -l
    ```

    创建的文件实际存储在服务端的 `/home/remoteuser` 目录下。在其他配置了相同autofs规则的客户端上，`remoteuser` 用户登录后也能看到这个文件。

![](img/a9d58b1110d7019fdfa423b656bde411_74.png)

![](img/a9d58b1110d7019fdfa423b656bde411_76.png)

![](img/a9d58b1110d7019fdfa423b656bde411_77.png)

## 总结与场景应用

![](img/a9d58b1110d7019fdfa423b656bde411_79.png)

本节课中我们一起学习了如何配置autofs来实现用户家目录的自动挂载。通过这个实例，我们模拟了类似Windows AD域的环境，确保了用户无论在哪个客户端登录，都能访问到统一、集中的个人文件存储空间。

![](img/a9d58b1110d7019fdfa423b656bde411_80.png)

![](img/a9d58b1110d7019fdfa423b656bde411_82.png)

![](img/a9d58b1110d7019fdfa423b656bde411_84.png)

这种配置的典型应用场景包括：
*   企业AD域环境下的Linux客户端集成。
*   需要为用户提供漫游配置文件的实验室或公用计算机环境。
*   任何要求用户数据集中存储和管理的场景。

![](img/a9d58b1110d7019fdfa423b656bde411_86.png)

![](img/a9d58b1110d7019fdfa423b656bde411_88.png)

通过掌握autofs的配置，你可以构建更灵活、更易于管理的网络文件系统架构。