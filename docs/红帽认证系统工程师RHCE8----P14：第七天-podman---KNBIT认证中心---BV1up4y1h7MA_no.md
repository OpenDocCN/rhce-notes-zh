# 红帽认证系统工程师RHCE8：P14：第七天 podman

![](img/e47f3cf6fadeef8369e60f9e22014dde_1.png)

![](img/e47f3cf6fadeef8369e60f9e22014dde_3.png)

## 概述

在本节课中，我们将学习两个核心主题：NFS与自动挂载的综合实验，以及容器技术的基础知识与管理。课程将分为两个主要部分：首先，我们将通过一个综合实验，掌握NFS服务器的配置与三种客户端挂载方式（临时、持久、自动挂载）；其次，我们将系统性地学习容器技术，特别是红帽8.2中引入的`podman`工具，包括容器的基本操作、镜像管理、持久化存储以及使用`systemd`管理容器服务。

---

## NFS与自动挂载综合实验

上一节我们回顾了文件系统的基本操作，本节中我们来看看如何实现网络文件共享。NFS（Network File System）是一种用于在Linux系统之间共享目录和文件的网络协议。

### 实验环境分析

![](img/e47f3cf6fadeef8369e60f9e22014dde_5.png)

![](img/e47f3cf6fadeef8369e60f9e22014dde_7.png)

![](img/e47f3cf6fadeef8369e60f9e22014dde_9.png)

![](img/e47f3cf6fadeef8369e60f9e22014dde_11.png)

假设我们有两台Linux主机：
*   **服务器端 (server a)**：IP地址为 `172.25.250.10`，负责共享目录。
*   **客户端 (server b)**：IP地址为 `172.25.250.11`，负责挂载服务器共享的目录。

实验目标是在server a上配置NFS共享，并在server b上分别通过三种方式挂载该共享目录。

### 服务器端 (server a) 配置

以下是配置NFS服务器的具体步骤：

![](img/e47f3cf6fadeef8369e60f9e22014dde_13.png)

1.  **安装NFS软件包**：首先需要在服务器端安装NFS服务所需的软件包。
    ```bash
    yum install nfs-utils -y
    ```

2.  **启用并启动NFS服务**：设置NFS服务开机自启，并立即启动它。同时，为了实验顺利进行，暂时关闭防火墙。
    ```bash
    systemctl enable --now nfs-server
    systemctl stop firewalld
    ```

3.  **创建共享目录并准备测试文件**：创建一个用于共享的目录，并在其中放置一个文件，以便客户端验证。
    ```bash
    mkdir /redhat
    echo "This is a test file from server a" > /redhat/testfile.txt
    ```

![](img/e47f3cf6fadeef8369e60f9e22014dde_15.png)

4.  **配置NFS共享**：编辑NFS的主配置文件 `/etc/exports`，定义共享规则。
    ```bash
    vim /etc/exports
    ```
    在文件中添加以下内容：
    ```
    /redhat 172.25.250.0/24(ro)
    ```
    *   `/redhat`：要共享的目录路径。
    *   `172.25.250.0/24`：允许访问此共享的客户端网段。
    *   `(ro)`：客户端对此共享目录只有**只读**权限。

![](img/e47f3cf6fadeef8369e60f9e22014dde_17.png)

5.  **使配置生效**：重新加载NFS服务的配置。
    ```bash
    systemctl reload nfs-server
    ```
    至此，NFS服务器端的配置完成。

![](img/e47f3cf6fadeef8369e60f9e22014dde_19.png)

### 客户端 (server b) 挂载配置

![](img/e47f3cf6fadeef8369e60f9e22014dde_21.png)

现在，我们切换到客户端server b，尝试用三种方式挂载server a共享的目录。

#### 方式一：临时挂载 (mount命令)

这种方式在系统重启后失效。

1.  **创建本地挂载点**：
    ```bash
    mkdir /mnt/nfs_temp
    ```

![](img/e47f3cf6fadeef8369e60f9e22014dde_23.png)

2.  **执行挂载**：使用`mount`命令将远程目录挂载到本地。
    ```bash
    mount 172.25.250.10:/redhat /mnt/nfs_temp
    ```

3.  **验证挂载**：查看挂载结果和测试文件内容。
    ```bash
    df -h | grep nfs_temp
    cat /mnt/nfs_temp/testfile.txt
    ```

![](img/e47f3cf6fadeef8369e60f9e22014dde_25.png)

#### 方式二：持久化挂载 (/etc/fstab)

这种方式将挂载信息写入配置文件，实现开机自动挂载。

![](img/e47f3cf6fadeef8369e60f9e22014dde_27.png)

![](img/e47f3cf6fadeef8369e60f9e22014dde_29.png)

![](img/e47f3cf6fadeef8369e60f9e22014dde_31.png)

1.  **编辑 `/etc/fstab` 文件**：
    ```bash
    vim /etc/fstab
    ```
    在文件末尾添加一行：
    ```
    172.25.250.10:/redhat /mnt/nfs_perm nfs defaults 0 0
    ```
    *   `172.25.250.10:/redhat`：NFS服务器和共享目录。
    *   `/mnt/nfs_perm`：本地挂载点（需提前创建 `mkdir /mnt/nfs_perm`）。
    *   `nfs`：文件系统类型。
    *   `defaults`：挂载选项。
    *   `0 0`：dump和fsck选项。

2.  **测试并应用配置**：
    ```bash
    mount -a # 挂载fstab中所有未挂载的设备
    df -h | grep nfs_perm
    ```

![](img/e47f3cf6fadeef8369e60f9e22014dde_33.png)

#### 方式三：自动挂载 (autofs)

![](img/e47f3cf6fadeef8369e60f9e22014dde_35.png)

这种方式仅在访问挂载点时才自动挂载，闲置一段时间后自动卸载，更加灵活节能。

![](img/e47f3cf6fadeef8369e60f9e22014dde_37.png)

![](img/e47f3cf6fadeef8369e60f9e22014dde_39.png)

1.  **安装autofs软件包**：
    ```bash
    yum install autofs -y
    ```

![](img/e47f3cf6fadeef8369e60f9e22014dde_41.png)

![](img/e47f3cf6fadeef8369e60f9e22014dde_43.png)

![](img/e47f3cf6fadeef8369e60f9e22014dde_45.png)

2.  **启用并启动autofs服务**：
    ```bash
    systemctl enable --now autofs
    ```

![](img/e47f3cf6fadeef8369e60f9e22014dde_47.png)

![](img/e47f3cf6fadeef8369e60f9e22014dde_49.png)

3.  **配置autofs**：
    *   编辑主配置文件 `/etc/auto.master`，定义挂载点的父目录和子配置文件。
        ```bash
        vim /etc/auto.master
        ```
        添加一行：
        ```
        /mnt/nfs_auto /etc/auto.nfsdemo
        ```
        *   `/mnt/nfs_auto`：这是**一级目录**，autofs会监控这个目录下的子目录。
        *   `/etc/auto.nfsdemo`：这是**子配置文件**，用于定义具体的挂载规则。

    *   创建并编辑子配置文件 `/etc/auto.nfsdemo`。
        ```bash
        vim /etc/auto.nfsdemo
        ```
        添加一行：
        ```
        data -ro 172.25.250.10:/redhat
        ```
        *   `data`：这是**二级目录**，即实际的访问点。**注意：不要手动创建这个目录**。
        *   `-ro`：挂载选项，表示只读。
        *   `172.25.250.10:/redhat`：要挂载的远程NFS路径。

![](img/e47f3cf6fadeef8369e60f9e22014dde_51.png)

4.  **重启autofs服务并测试**：
    ```bash
    systemctl restart autofs
    ```
    *   **测试自动挂载**：首先查看 `/mnt/nfs_auto` 目录，此时`data`子目录不存在。
        ```bash
        ls -la /mnt/nfs_auto/
        ```
    *   访问自动挂载点：`cd /mnt/nfs_auto/data`。此时autofs会自动触发挂载。
    *   查看挂载状态：`df -h | grep nfs_auto`，可以看到挂载已建立。
    *   退出目录并等待一段时间（默认5分钟）后，再次执行 `df -h | grep nfs_auto`，会发现挂载已自动解除。

### 实验总结

![](img/e47f3cf6fadeef8369e60f9e22014dde_53.png)

在本节NFS实验中，我们系统性地实践了：
1.  在NFS服务器端安装服务、配置共享目录和访问控制。
2.  在客户端使用`mount`命令进行临时挂载。
3.  通过编辑`/etc/fstab`文件实现持久化挂载。
4.  利用`autofs`服务实现按需自动挂载与卸载，提升资源利用效率。

---

## 容器技术基础与管理 (Podman)

上一节我们完成了网络文件系统的学习，本节中我们来看看现代应用部署中的重要技术——容器。红帽企业Linux 8.2开始，容器管理工具从Docker转向了`podman`。

### 容器概述：什么是容器？

容器是一种轻量级、可移植的软件打包技术，它将应用程序及其所有依赖项（库、配置文件、环境变量等）打包在一起，确保应用在任何环境中都能以相同的方式运行。

**容器与虚拟机的核心区别**：
*   **虚拟机**：每个虚拟机包含完整的客户操作系统(Guest OS)，运行在虚拟机监控器(Hypervisor)之上，资源占用大，启动慢。
    *   公式表示：`物理硬件 -> Hypervisor -> Guest OS -> App`
*   **容器**：容器共享主机的操作系统内核，将应用与依赖打包在独立的用户空间中，更加轻量，启动迅速。
    *   公式表示：`物理硬件 -> Host OS Kernel -> Container (Namespace/Cgroups) -> App`

![](img/e47f3cf6fadeef8369e60f9e22014dde_55.png)

容器技术依赖于三大核心概念：
1.  **Control Groups (cgroups)**：**资源控制组**，用于限制和隔离进程组所使用的物理资源（如CPU、内存）。
2.  **Namespaces**：**命名空间**，为进程提供独立的系统视图（如网络、进程ID、文件系统），实现隔离。
3.  **SELinux**：提供额外的安全层，为容器和主机资源定义强制访问控制策略。

![](img/e47f3cf6fadeef8369e60f9e22014dde_57.png)

### 核心概念：镜像 (Image)

镜像是容器的基础模板，是一个只读的静态文件。容器是镜像的运行实例。

**镜像的关键特性**：
*   **分层结构**：镜像由多个只读层叠加而成。每次修改都会创建一个新的层放在最上面。
*   **写时复制**：运行容器时，会在最顶层添加一个可写层。所有修改都发生在此层，下层镜像保持不变。
*   **镜像仓库**：用于存储和分发镜像。分为公共仓库（如`quay.io`）和私有仓库。
    *   镜像地址格式：`[registry_hostname]/[user_name]/[image_name]:[tag]`
    *   例如：`quay.io/bitnami/apache:latest`

### Podman 基础操作

以下是使用`podman`管理容器和镜像的基本命令。

#### 1. 安装与登录
```bash
# 安装podman
yum install podman -y

# 登录到镜像仓库（考试环境示例）
podman login -u admin -p redhat321 registry.lab.example.com
```

#### 2. 镜像管理
```bash
# 从仓库拉取镜像
podman pull registry.lab.example.com/httpd:latest

# 列出本地镜像
podman images

# 删除本地镜像
podman rmi <image_id>
```

![](img/e47f3cf6fadeef8369e60f9e22014dde_59.png)

![](img/e47f3cf6fadeef8369e60f9e22014dde_61.png)

#### 3. 容器生命周期管理
```bash
# 运行一个新容器
# -it: 交互式终端
# --name: 为容器命名
# /bin/bash: 容器内启动的命令
podman run -it --name mycontainer registry.lab.example.com/rhel8 /bin/bash

# 列出运行中的容器
podman ps

# 列出所有容器（包括已停止的）
podman ps -a

# 停止容器
podman stop mycontainer

# 启动已停止的容器
podman start mycontainer

![](img/e47f3cf6fadeef8369e60f9e22014dde_63.png)

# 删除容器
podman rm mycontainer

# 进入正在运行的容器执行命令
podman exec -it mycontainer /bin/bash
```

![](img/e47f3cf6fadeef8369e60f9e22014dde_65.png)

#### 4. 运行容器示例：映射端口与持久化存储
```bash
# 运行一个Apache容器，将主机8080端口映射到容器80端口，并在后台运行(-d)
podman run -d --name webapp -p 8080:80 registry.lab.example.com/httpd

# 运行一个MariaDB容器，设置环境变量，并映射主机目录到容器以实现数据持久化
# -v: 卷挂载 (主机目录:容器目录)
# -e: 设置环境变量
podman run -d --name mysql_db \
  -e MYSQL_USER=user1 \
  -e MYSQL_PASSWORD=redhat \
  -e MYSQL_DATABASE=items \
  -p 3306:3306 \
  -v /home/mysql_data:/var/lib/mysql:Z \
  registry.lab.example.com/mariadb:latest
```
**注意**：`-v`参数中的`:Z`选项用于在SELinux环境下正确标记主机目录的上下文，这对红帽系统至关重要。

### 使用 Systemd 管理容器服务

为了让容器像系统服务一样被管理（开机自启、状态监控），我们可以为其创建`systemd`单元文件。

#### 为用户级容器创建Systemd服务

1.  **为普通用户创建systemd配置目录**：
    ```bash
    mkdir -p ~/.config/systemd/user/
    ```

2.  **生成容器服务的systemd单元文件**：
    ```bash
    cd ~/.config/systemd/user/
    podman generate systemd --name webapp --files --new
    ```
    此命令会生成一个类似 `container-webapp.service` 的文件。

3.  **管理用户级服务**：
    ```bash
    # 重新加载systemd配置
    systemctl --user daemon-reload

    # 启用服务（开机自启）
    systemctl --user enable container-webapp.service

    # 启动服务
    systemctl --user start container-webapp.service

    # 查看服务状态
    systemctl --user status container-webapp.service
    ```

![](img/e47f3cf6fadeef8369e60f9e22014dde_67.png)

![](img/e47f3cf6fadeef8369e60f9e22014dde_69.png)

### SELinux 端口上下文管理

有时服务无法绑定端口，可能是因为SELinux端口标签不正确。

1.  **查看端口标签**：
    ```bash
    semanage port -l | grep http_port_t
    ```
    输出会显示默认允许的端口，如 `80, 81, 443, 488, 8008, 8009, 8443, 9000`。

2.  **添加自定义端口标签**（例如，将82端口加入http允许列表）：
    ```bash
    semanage port -a -t http_port_t -p tcp 82
    ```

3.  **如果操作失误，可以删除**：
    ```bash
    semanage port -d -t http_port_t -p tcp 82
    ```

## 课程总结

本节课中我们一起学习了两个重要部分：
1.  **NFS与自动挂载**：我们掌握了如何配置NFS服务器，以及在客户端通过`mount`命令、`/etc/fstab`文件和`autofs`服务三种方式挂载网络共享目录，理解了它们各自的应用场景。
2.  **容器技术与管理**：我们学习了容器的基本概念、与虚拟机的区别，以及红帽8.2中`podman`工具的使用。核心内容包括：
    *   镜像的拉取与管理。
    *   容器的创建、运行、查看和删除。
    *   实现容器的端口映射与数据持久化存储。
    *   使用`systemd`将容器管理为系统服务，实现更规范的生命周期管理。
    *   排查并修复因SELinux端口标签导致的服务启动故障。

![](img/e47f3cf6fadeef8369e60f9e22014dde_71.png)

这些技能是成为一名合格的系统工程师，特别是应对RHCE认证考试所必备的。