# Red Hat RHCE 8.0 认证体系课程：RH134：Ch09b_autofs 实例 🚀

![](img/862006b843028bff871c8e68c16d98cb_0.png)

在本节课中，我们将学习如何配置 `autofs` 服务，以实现一个特定的应用场景：当指定用户登录时，自动将其家目录挂载为远程服务器上的共享目录。这模拟了类似 Windows AD 域环境中的用户配置文件漫游功能。

## 概述与问题背景

上一节我们介绍了 `autofs` 的基本概念和配置方法。本节中，我们来看一个具体的应用实例。

在类似 Windows AD 域的环境中，用户登录域账户后，其个人文件（如桌面、文档）通常存储在域控制器上的网络共享目录中。这样，用户在任何一台加入域的计算机上登录，都能访问到相同的个人文件。Linux 系统也可以通过 `autofs` 和 NFS 实现类似的功能。

![](img/862006b843028bff871c8e68c16d98cb_2.png)

我们的目标是：在客户端（Server B）上，当特定用户（`remoteuser`）登录时，自动将其家目录挂载为服务端（Server A）上发布的 NFS 共享目录。

![](img/862006b843028bff871c8e68c16d98cb_4.png)

![](img/862006b843028bff871c8e68c16d98cb_5.png)

## 实验环境与前提

![](img/862006b843028bff871c8e68c16d98cb_7.png)

![](img/862006b843028bff871c8e68c16d98cb_9.png)

以下是本次实验的假定情境：
*   **服务端 (Server A)**: IP 地址为 `192.168.145.128`，已创建用户 `remoteuser`，并准备共享其家目录 `/home/remoteuser`。
*   **客户端 (Server B)**: 已创建同名的本地用户 `remoteuser`，但其家目录 `/home/remoteuser` 已被删除（模拟初始状态）。我们将在此配置 `autofs`。

**核心思路**：
1.  服务端通过 NFS 发布 `/home/remoteuser` 目录。
2.  客户端配置 `autofs`，监听 `/home` 目录。
3.  当用户 `remoteuser` 登录客户端时，`autofs` 自动将服务端的共享目录挂载到客户端的 `/home/remoteuser`。

![](img/862006b843028bff871c8e68c16d98cb_11.png)

![](img/862006b843028bff871c8e68c16d98cb_12.png)

## 配置步骤详解

### 第一步：在服务端 (Server A) 配置 NFS 共享

首先，我们需要在服务端配置并发布 NFS 共享。

![](img/862006b843028bff871c8e68c16d98cb_14.png)

![](img/862006b843028bff871c8e68c16d98cb_16.png)

![](img/862006b843028bff871c8e68c16d98cb_17.png)

1.  编辑 NFS 配置文件 `/etc/exports`，添加共享目录和权限。
    ```bash
    /home/remoteuser 192.168.145.0/24(rw,no_root_squash)
    ```
    *   `/home/remoteuser`: 要共享的目录。
    *   `192.168.145.0/24`: 允许访问的客户端网段。
    *   `rw`: 读写权限。
    *   `no_root_squash`: 允许 root 用户保持权限（在实验环境中简化配置，生产环境需谨慎）。

![](img/862006b843028bff871c8e68c16d98cb_19.png)

![](img/862006b843028bff871c8e68c16d98cb_21.png)

2.  使 NFS 共享配置生效。
    ```bash
    exportfs -rv
    ```

![](img/862006b843028bff871c8e68c16d98cb_23.png)

![](img/862006b843028bff871c8e68c16d98cb_25.png)

### 第二步：在客户端 (Server B) 验证 NFS 共享

![](img/862006b843028bff871c8e68c16d98cb_27.png)

![](img/862006b843028bff871c8e68c16d98cb_29.png)

在客户端，我们可以先验证服务端的 NFS 共享是否可见。
```bash
showmount -e 192.168.145.128
```
此命令应列出服务端发布的共享，包括 `/home/remoteuser`。

![](img/862006b843028bff871c8e68c16d98cb_31.png)

### 第三步：在客户端 (Server B) 配置 autofs

这是实现自动挂载的核心步骤。

![](img/862006b843028bff871c8e68c16d98cb_33.png)

![](img/862006b843028bff871c8e68c16d98cb_35.png)

1.  **编辑主映射文件 `/etc/auto.master`**。
    我们需要指定一个挂载点目录和对应的映射文件。
    ```bash
    /home /etc/auto.remote
    ```
    *   `/home`: 监听目录。当访问此目录下的子目录时，`autofs` 会触发挂载。
    *   `/etc/auto.remote`: 自定义的间接映射文件，用于定义具体的挂载规则。

![](img/862006b843028bff871c8e68c16d98cb_37.png)

![](img/862006b843028bff871c8e68c16d98cb_38.png)

2.  **创建并编辑间接映射文件 `/etc/auto.remote`**。
    首先复制一个模板文件，然后进行编辑。
    ```bash
    cp /etc/auto.misc /etc/auto.remote
    vi /etc/auto.remote
    ```
    在文件中添加以下内容：
    ```bash
    remoteuser -fstype=nfs,rw 192.168.145.128:/home/remoteuser
    ```
    *   `remoteuser`: 映射键。当访问 `/home/remoteuser` 时触发此规则。
    *   `-fstype=nfs,rw`: 挂载选项，指定文件系统类型为 NFS，并具有读写权限。
    *   `192.168.145.128:/home/remoteuser`: 远程 NFS 共享的位置。

![](img/862006b843028bff871c8e68c16d98cb_40.png)

![](img/862006b843028bff871c8e68c16d98cb_41.png)

![](img/862006b843028bff871c8e68c16d98cb_42.png)

3.  **重启 autofs 服务**。
    为了使配置生效，需要重启 `autofs` 服务。
    ```bash
    systemctl restart autofs
    ```

![](img/862006b843028bff871c8e68c16d98cb_43.png)

![](img/862006b843028bff871c8e68c16d98cb_44.png)

### 第四步：测试自动挂载

![](img/862006b843028bff871c8e68c16d98cb_46.png)

配置完成后，我们可以进行测试。

![](img/862006b843028bff871c8e68c16d98cb_48.png)

1.  在客户端切换到 `remoteuser` 用户。
    ```bash
    su - remoteuser
    ```
2.  如果配置成功，系统将不会报错（而之前会因为家目录不存在而报错），并且会自动将服务端的 `/home/remoteuser` 挂载到客户端的 `/home/remoteuser`。
3.  验证挂载和读写权限。
    ```bash
    # 查看当前目录，确认已挂载
    pwd
    # 尝试创建文件
    touch testfile.txt
    ls -l testfile.txt
    ```
    如果文件创建成功，说明自动挂载和读写权限均配置正确。

![](img/862006b843028bff871c8e68c16d98cb_50.png)

## 总结与场景延伸

![](img/862006b843028bff871c8e68c16d98cb_52.png)

本节课中，我们一起学习了如何配置 `autofs` 来实现用户家目录的自动挂载。通过这个实例，我们模拟了企业环境中常见的“用户配置文件漫游”场景，使得用户无论从哪台 Linux 客户端登录，都能访问到位于中央服务器上的个人家目录。

![](img/862006b843028bff871c8e68c16d98cb_54.png)

这个技术的典型应用场景包括：
*   **Linux 域环境**：在配置了 LDAP 或 FreeIPA 等集中认证的服务中，配合 `autofs` 实现用户家目录的统一管理。
*   **简化桌面管理**：在实验室、教室或公用计算机机房，用户数据不保存在本地，便于维护和备份。

![](img/862006b843028bff871c8e68c16d98cb_56.png)

![](img/862006b843028bff871c8e68c16d98cb_58.png)

通过掌握 `autofs` 的配置，你可以让 Linux 系统的存储访问更加灵活和自动化。