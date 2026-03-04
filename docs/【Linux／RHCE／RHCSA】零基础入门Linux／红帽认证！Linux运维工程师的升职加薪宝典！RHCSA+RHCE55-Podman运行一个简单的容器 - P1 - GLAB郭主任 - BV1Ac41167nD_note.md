# Linux容器技术入门：1：Podman运行一个简单的容器 🐳

![](img/31c66949d1e1d064d4fac8860bc2c510_0.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_2.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_4.png)

在本节课中，我们将要学习如何使用Podman来运行一个简单的容器。Podman是一个用于管理容器和镜像的工具，它无需守护进程即可运行，是学习容器技术的理想起点。我们将从安装配置开始，逐步学习如何拉取镜像、运行容器并进行基本管理。

![](img/31c66949d1e1d064d4fac8860bc2c510_6.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_8.png)

---

## 概述

本节教程将引导你完成使用Podman运行容器的完整流程。主要内容包括：安装Podman、配置镜像仓库、拉取镜像、运行容器以及进行基本的容器生命周期管理。我们还会探讨容器实现隔离的基本原理。

---

![](img/31c66949d1e1d064d4fac8860bc2c510_10.png)

## 安装Podman

首先，我们需要在系统上安装Podman及其相关软件包。使用`yum module install`命令可以安装容器所需的所有包。

```bash
yum module install container-tools
```

安装完成后，Podman就可以使用了。

---

![](img/31c66949d1e1d064d4fac8860bc2c510_12.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_14.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_16.png)

## 配置镜像仓库

![](img/31c66949d1e1d064d4fac8860bc2c510_18.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_20.png)

上一节我们安装了Podman，本节中我们来看看如何配置镜像仓库。Podman的全局配置文件位于`/etc/containers/registries.conf`。我们需要修改此文件以指定使用的镜像仓库地址。

![](img/31c66949d1e1d064d4fac8860bc2c510_22.png)

默认配置指向公网镜像仓库，例如红帽官方的`registry.redhat.io`。公网仓库提供了丰富的镜像，如Apache HTTPD。选择镜像时，建议使用最新的（`latest`）标签，因为旧版本可能包含更多已知的安全漏洞。

在我们的实验环境中，需要将仓库地址改为内部私有仓库。修改`registries.conf`文件，添加或修改以下内容：

```ini
[registries.search]
registries = ['registry.lab.example.com']
```

这是需要修改的第一个关键文件。

---

## 登录镜像仓库

配置文件修改好后，我们需要登录到配置的镜像仓库才能拉取镜像。使用`podman login`命令进行登录。

```bash
podman login registry.lab.example.com
```

系统会提示输入用户名和密码（例如，用户名`admin`，密码`redhat321`）。初次登录可能会因为自签名证书而报错。

为了解决证书问题，我们需要在`registries.conf`文件的`[registries.insecure]`部分添加我们的仓库地址，表示信任该站点的证书。

```ini
[registries.insecure]
registries = ['registry.lab.example.com']
```

添加后，再次执行登录命令即可成功。

登录成功后，可以使用`podman search`命令搜索仓库中的镜像，例如搜索`ubi`相关的镜像：

```bash
podman search ubi
```

你也可以通过浏览器访问仓库的Web界面（如果有），以图形化方式查看可用镜像，这与访问Docker Hub等公网仓库类似。

![](img/31c66949d1e1d064d4fac8860bc2c510_24.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_26.png)

---

![](img/31c66949d1e1d064d4fac8860bc2c510_28.png)

## 拉取与运行容器

![](img/31c66949d1e1d064d4fac8860bc2c510_30.png)

现在我们已经可以连接到镜像仓库，接下来学习如何拉取并运行一个容器。

![](img/31c66949d1e1d064d4fac8860bc2c510_32.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_34.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_36.png)

### 拉取镜像

![](img/31c66949d1e1d064d4fac8860bc2c510_38.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_40.png)

使用`podman pull`命令从仓库下载镜像到本地。

```bash
podman pull registry.lab.example.com/ubi8:latest
```

这条命令会从`registry.lab.example.com`仓库拉取标签为`latest`的`ubi8`镜像。

![](img/31c66949d1e1d064d4fac8860bc2c510_42.png)

### 查看本地镜像

下载的镜像默认存储在`/var/lib/containers/storage/`目录下。要查看本地已有的镜像，使用：

![](img/31c66949d1e1d064d4fac8860bc2c510_44.png)

```bash
podman images
```

### 运行容器

![](img/31c66949d1e1d064d4fac8860bc2c510_46.png)

镜像拉取后，需要通过`podman run`命令来运行容器。以下是几个常用参数：
*   `-it`: 以交互模式运行容器并分配一个伪终端。
*   `--name`: 为容器指定一个名称。
*   `-d`: 在后台运行容器（守护进程模式）。

以下是运行容器的几种方式：

1.  **交互式运行**：运行并立即进入容器内部。
    ```bash
    podman run -it --name=rhel8 ubi8:latest
    ```
    执行后，命令行提示符会发生变化，表示你已进入容器内部。在另一个终端使用`podman ps`可以看到容器正在运行（`Up`状态）。退出容器（输入`exit`）后，容器会自动停止。

2.  **后台运行**：让容器在后台持续运行。
    ```bash
    podman run -d --name=rhel8 ubi8:latest
    ```
    使用`-d`参数后，不会进入容器，但容器已在后台运行。使用`podman ps`可以验证。

---

![](img/31c66949d1e1d064d4fac8860bc2c510_48.png)

## 容器基本管理

![](img/31c66949d1e1d064d4fac8860bc2c510_50.png)

容器运行起来后，我们需要掌握一些基本的管理操作。以下是常用的容器管理命令列表：

![](img/31c66949d1e1d064d4fac8860bc2c510_52.png)

*   **查看容器状态**：
    *   `podman ps`: 查看正在运行的容器。
    *   `podman ps -a`: 查看所有容器（包括已停止的）。

![](img/31c66949d1e1d064d4fac8860bc2c510_54.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_56.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_58.png)

*   **控制容器生命周期**：
    *   `podman start <容器名或ID>`: 启动一个已停止的容器。
    *   `podman stop <容器名或ID>`: 停止一个正在运行的容器。
    *   `podman restart <容器名或ID>`: 重启容器。

![](img/31c66949d1e1d064d4fac8860bc2c510_60.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_62.png)

*   **删除容器与镜像**：
    *   `podman rm <容器名或ID>`: 删除一个已停止的容器。使用`-f`参数可以强制删除运行中的容器。
    *   `podman rm -a`: 删除所有已停止的容器。
    *   `podman rmi <镜像名或ID>`: 删除一个本地镜像。**注意**：如果该镜像有对应的容器存在（即使已停止），则无法直接删除。
    *   `podman rmi -a`: 删除所有本地镜像。

![](img/31c66949d1e1d064d4fac8860bc2c510_64.png)

*   **在容器内执行命令**：
    *   可以在运行容器时，在命令末尾指定要执行的命令。
        ```bash
        podman run --name=rhel8-cmd ubi8:latest /bin/bash -c "ls /"
        ```
        这会在容器启动后立即执行`ls /`命令，然后退出。

**重要提示**：容器是镜像的运行实例。删除正在被容器使用的镜像是不可行的，必须先停止并删除依赖它的容器。

![](img/31c66949d1e1d064d4fac8860bc2c510_66.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_68.png)

---

## 容器隔离原理初探

![](img/31c66949d1e1d064d4fac8860bc2c510_70.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_71.png)

我们一直在使用容器，那么容器是如何实现与宿主机及其他容器隔离的呢？这主要依赖于Linux内核的三项技术：**Namespaces**、**Cgroups** 和 **SELinux**。

### 通过Namespaces实现隔离

Namespaces为进程提供了独立的系统资源视图，如独立的网络、进程ID、挂载点等。每个容器都拥有自己的一组Namespaces。

1.  首先，查找容器的进程ID（PID）。容器在宿主机上本质上是一个进程。
    ```bash
    podman inspect rhel8 --format ‘{{.State.Pid}}‘
    ```
2.  查看该进程拥有的独立Namespaces。
    ```bash
    lsns -p <容器PID>
    ```
    输出会显示该进程独立的Cgroup、User、Network、Mount等Namespace，这确保了容器环境的隔离性。
3.  可以进一步查看某个特定Namespace的详情，例如网络：
    ```bash
    nsenter -t <容器PID> -n ip addr
    ```
    这条命令让我们看到了该容器内部的独立网络接口。

### 通过SELinux实现强制访问控制

![](img/31c66949d1e1d064d4fac8860bc2c510_73.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_75.png)

SELinux通过为进程和文件对象打上特定的安全标签（`context`）来实现强制访问控制。不同容器的进程和文件通常具有不同的SELinux标签，从而阻止了跨容器的未授权访问。

1.  查看容器进程的SELinux上下文：
    ```bash
    ps auxZ | grep <容器PID>
    ```
    注意输出中`context`字段，容器的进程通常带有类似`container_t`的标签以及唯一标识符（如`c418,c419`）。
2.  查看容器内部文件系统的SELinux上下文。容器的可写层通常位于`/var/lib/containers/storage/overlay/`下的子目录中。进入对应容器的目录查看：
    ```bash
    ls -lZ /var/lib/containers/storage/overlay/<容器层ID>/
    ```
    你会发现其中文件的SELinux标签与容器进程的标签是匹配或相关联的。

由于标签不同，一个容器的进程默认无法访问另一个容器的文件，这就是SELinux在容器安全中起到的关键作用。

![](img/31c66949d1e1d064d4fac8860bc2c510_77.png)

![](img/31c66949d1e1d064d4fac8860bc2c510_79.png)

Cgroups则主要用于限制和记录进程组所使用的物理资源（如CPU、内存），是资源隔离的保障。

![](img/31c66949d1e1d064d4fac8860bc2c510_81.png)

---

## 总结

![](img/31c66949d1e1d064d4fac8860bc2c510_83.png)

本节课中我们一起学习了Podman的基础操作。我们从安装和配置镜像仓库开始，逐步掌握了如何使用`podman pull`拉取镜像，以及使用`podman run`配合不同参数运行容器。我们还学习了管理容器状态（`start`/`stop`/`restart`）和清理资源（`rm`/`rmi`）的常用命令。最后，我们初步探讨了容器实现隔离的底层原理，即依靠Linux内核的Namespaces、Cgroups和SELinux技术，这有助于我们理解容器轻量级和安全性背后的机制。