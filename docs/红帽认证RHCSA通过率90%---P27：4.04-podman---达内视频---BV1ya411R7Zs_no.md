# Podman容器管理：P27：4.04-podman容器操作

![](img/8b50b54f2df568c0d81d201e1340a3fa_1.png)

在本节课中，我们将学习如何使用Podman进行容器的全生命周期管理，包括创建、查看、访问、停止、启动和删除容器。我们还将重点介绍两个核心概念：端口映射和目录映射，它们对于容器与外部世界的交互和数据持久化至关重要。

![](img/8b50b54f2df568c0d81d201e1340a3fa_3.png)

## 容器基本操作

![](img/8b50b54f2df568c0d81d201e1340a3fa_5.png)

![](img/8b50b54f2df568c0d81d201e1340a3fa_7.png)

上一节我们介绍了镜像的管理，本节中我们来看看如何将镜像运行起来，即容器的操作。将镜像运行起来的过程称为创建容器，对应的Podman指令是 `run`。

查看当前已创建的容器，使用 `ps` 指令，这与管理进程类似。`podman ps` 列出正在运行的容器。要列出所有已创建的容器（包括已停止的），需要使用 `podman ps -a`。

以下是访问和管理容器的核心指令列表：
*   **`podman exec`**：访问并执行正在运行的容器内部的命令。
*   **`podman stop`**：停止一个正在运行的容器。
*   **`podman start`**：启动一个已停止的容器。
*   **`podman restart`**：重启一个容器。
*   **`podman rm`**：删除一个容器。请注意，`podman rmi` 用于删除镜像，而 `podman rm` 用于删除容器。

`run` 指令将镜像转变为内存中可控制的运行状态，即容器。`rm` 指令则从系统中移除该容器及其状态数据。停止容器类似于暂停，其状态数据被保留；删除容器则会清除这些状态数据。

## 端口映射与目录映射

![](img/8b50b54f2df568c0d81d201e1340a3fa_9.png)

接下来，我们探讨 `run` 指令的两个重要用法：端口映射和目录映射。

**端口映射** 允许将宿主机的端口映射到容器内部的端口，使得外部客户端可以通过访问宿主机的端口来访问容器内运行的服务。这通过 `-p` 选项实现。

![](img/8b50b54f2df568c0d81d201e1340a3fa_11.png)

**公式：`-p <宿主机端口>:<容器端口>`**

![](img/8b50b54f2df568c0d81d201e1340a3fa_13.png)

例如，运行一个Nginx容器，并将宿主机的8000端口映射到容器的80端口：
```bash
podman run -d -p 8000:80 nginx
```
此后，外部客户端访问宿主机的 `8000` 端口，即可访问到容器内Nginx提供的网页服务。

![](img/8b50b54f2df568c0d81d201e1340a3fa_15.png)

**目录映射（持久化存储）** 解决了容器数据易失性的问题。它将宿主机上的一个目录与容器内的目录关联起来，使得容器内对该目录的操作直接保存在宿主机上。即使容器被删除，数据也不会丢失。这通过 `-v` 选项实现。

![](img/8b50b54f2df568c0d81d201e1340a3fa_17.png)

**公式：`-v <宿主机目录路径>:<容器内目录路径>`**

![](img/8b50b54f2df568c0d81d201e1340a3fa_19.png)

![](img/8b50b54f2df568c0d81d201e1340a3fa_21.png)

例如，将宿主机的 `/opt/webroot` 目录映射到Nginx容器默认的网页目录 `/usr/share/nginx/html`：
```bash
podman run -d -v /opt/webroot:/usr/share/nginx/html nginx
```
这样，容器内网站的数据将持久化存储在宿主机的 `/opt/webroot` 目录中。

![](img/8b50b54f2df568c0d81d201e1340a3fa_23.png)

![](img/8b50b54f2df568c0d81d201e1340a3fa_25.png)

![](img/8b50b54f2df568c0d81d201e1340a3fa_27.png)

> **注意**：如果宿主机启用了SELinux，可能需要在 `-v` 选项后添加 `:Z` 或 `:z` 后缀来调整安全上下文，或者选择关闭SELinux。

![](img/8b50b54f2df568c0d81d201e1340a3fa_29.png)

## 容器操作详解与示例

![](img/8b50b54f2df568c0d81d201e1340a3fa_31.png)

了解了核心概念后，我们通过具体示例来掌握这些操作。

### 创建与查看容器

![](img/8b50b54f2df568c0d81d201e1340a3fa_33.png)

![](img/8b50b54f2df568c0d81d201e1340a3fa_35.png)

使用 `podman run` 运行一个Nginx镜像。默认情况下，容器会在前台运行并占用终端。为了让容器在后台运行，需要添加 `-d` 选项。

![](img/8b50b54f2df568c0d81d201e1340a3fa_37.png)

![](img/8b50b54f2df568c0d81d201e1340a3fa_39.png)

**代码：`podman run -d nginx`**

![](img/8b50b54f2df568c0d81d201e1340a3fa_41.png)

运行后，使用 `podman ps` 可以查看正在运行的容器。每个容器都有一个唯一的ID和名称。可以通过 `--name` 选项在创建时指定容器名称。

![](img/8b50b54f2df568c0d81d201e1340a3fa_43.png)

![](img/8b50b54f2df568c0d81d201e1340a3fa_44.png)

![](img/8b50b54f2df568c0d81d201e1340a3fa_46.png)

**代码：`podman run -d --name myweb1 nginx`**

### 访问容器内部

![](img/8b50b54f2df568c0d81d201e1340a3fa_48.png)

要访问运行中的容器并执行命令，使用 `podman exec`。

执行单条命令，例如查看容器系统信息：
```bash
podman exec -l cat /etc/os-release
```
（`-l` 代表最近创建的容器）

![](img/8b50b54f2df568c0d81d201e1340a3fa_50.png)

若要进入容器的交互式命令行环境，需要结合 `-it` 选项并执行 `bash`（如果容器内包含）：
```bash
podman exec -it -l bash
```
在交互环境中，可以像在普通Linux系统中一样执行命令（但命令可能不完整）。

![](img/8b50b54f2df568c0d81d201e1340a3fa_52.png)

![](img/8b50b54f2df568c0d81d201e1340a3fa_54.png)

### 停止、启动与删除容器

管理容器状态：
*   **停止容器**：`podman stop <容器ID或名称>`
*   **启动容器**：`podman start <容器ID或名称>`
*   **删除容器**：`podman rm <容器ID或名称>`

![](img/8b50b54f2df568c0d81d201e1340a3fa_56.png)

> **注意**：删除正在运行的容器需要添加 `-f` 选项强制删除。

### 文件复制与目录映射实践

**1. 复制文件到容器**
可以使用 `podman cp` 在宿主机和容器间复制文件。

![](img/8b50b54f2df568c0d81d201e1340a3fa_58.png)

![](img/8b50b54f2df568c0d81d201e1340a3fa_60.png)

**代码：`podman cp /opt/index.html <容器ID>:/usr/share/nginx/html/`**

**2. 综合示例：运行一个带映射的容器**
下面是一个结合了后台运行、端口映射、目录映射和自定义名称的完整示例：
```bash
# 在宿主机准备目录和网页文件
mkdir -p /opt/webroot
echo “Podman Test” > /opt/webroot/index.html

# 运行容器
podman run -d \
  --name myweb2 \
  -p 8080:80 \
  -v /opt/webroot:/usr/share/nginx/html \
  nginx

![](img/8b50b54f2df568c0d81d201e1340a3fa_62.png)

![](img/8b50b54f2df568c0d81d201e1340a3fa_64.png)

# 测试访问
curl http://localhost:8080
```
此命令会创建一个名为 `myweb2` 的容器，使用宿主机的 `/opt/webroot` 作为网页根目录，并通过宿主机的8080端口对外提供服务。

![](img/8b50b54f2df568c0d81d201e1340a3fa_66.png)

![](img/8b50b54f2df568c0d81d201e1340a3fa_68.png)

### 检查容器信息

![](img/8b50b54f2df568c0d81d201e1340a3fa_70.png)

![](img/8b50b54f2df568c0d81d201e1340a3fa_72.png)

使用 `podman inspect` 可以查看容器或镜像的详细配置信息。

**代码：`podman inspect <容器ID>`**

![](img/8b50b54f2df568c0d81d201e1340a3fa_74.png)

![](img/8b50b54f2df568c0d81d201e1340a3fa_76.png)

本节课中我们一起学习了Podman管理容器的核心操作。我们从创建容器（`run`）开始，学习了如何查看（`ps`）、访问（`exec`）、控制状态（`stop`/`start`/`restart`）以及删除（`rm`）容器。重点掌握了**端口映射（`-p`）** 和**目录映射（`-v`）** 这两个关键功能，它们分别解决了容器服务的网络访问和数据持久化问题。通过大量的实践示例，你应该已经能够熟练地使用Podman来部署和管理容器化应用了。记住，`-d` 用于后台运行，`--name` 用于指定名称，`-it` 用于交互式访问，这些选项的组合使用能满足大部分日常需求。