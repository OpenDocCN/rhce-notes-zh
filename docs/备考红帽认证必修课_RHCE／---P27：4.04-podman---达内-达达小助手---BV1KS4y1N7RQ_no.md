# 备考红帽认证必修课：P27：4.04-podman容器操作 🐳

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_1.png)

在本节课中，我们将学习如何使用Podman进行容器的基本操作，包括创建、查看、访问、停止、启动和删除容器。我们还将重点学习两个核心概念：端口映射和目录映射，它们对于容器化应用的网络访问和数据持久化至关重要。

---

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_3.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_5.png)

## 创建容器

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_7.png)

上一节我们介绍了镜像的管理，本节中我们来看看如何将镜像运行起来，即创建容器。创建容器的核心命令是 `podman run`。

以下是创建容器时常用的几种方式：

*   **基本运行**：`podman run <镜像名>`。这种方式会占用当前终端前台运行。
*   **后台运行**：`podman run -d <镜像名>`。使用 `-d` 选项让容器在后台运行，不占用当前终端。
*   **指定容器名称**：`podman run -d --name <自定义名称> <镜像名>`。使用 `--name` 选项可以为容器指定一个易于识别的名称，这在管理多个容器时非常有用。

## 查看容器

创建容器后，我们需要能够查看它们的状态。Podman提供了 `podman ps` 命令来管理容器列表。

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_9.png)

以下是查看容器的相关命令：

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_11.png)

*   `podman ps`：列出当前正在运行的容器。
*   `podman ps -a`：列出所有已创建的容器，包括已停止的。

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_13.png)

## 访问容器

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_15.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_17.png)

对于正在运行的容器，我们可能需要进入其内部执行命令或进行交互式操作。

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_19.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_21.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_23.png)

以下是访问容器的两种主要方式：

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_25.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_27.png)

*   **执行单条命令**：`podman exec <容器ID或名称> <命令>`。例如，`podman exec myweb cat /etc/os-release`。
*   **进入交互式终端**：`podman exec -it <容器ID或名称> /bin/bash`。使用 `-i`（交互式）和 `-t`（分配伪终端）选项可以进入容器的命令行环境。

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_29.png)

## 容器的生命周期管理

与系统服务类似，容器也有其生命周期，我们可以对其进行停止、启动、重启和删除操作。

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_31.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_33.png)

以下是管理容器生命周期的命令：

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_35.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_37.png)

*   `podman stop <容器ID或名称>`：停止一个正在运行的容器。
*   `podman start <容器ID或名称>`：启动一个已停止的容器。
*   `podman restart <容器ID或名称>`：重启一个容器。
*   `podman rm <容器ID或名称>`：删除一个已停止的容器。若要删除正在运行的容器，需使用 `podman rm -f` 强制删除。

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_39.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_41.png)

## 端口映射

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_42.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_44.png)

容器默认运行在隔离的网络环境中。为了让外部能够访问容器内运行的服务（如Web服务器），需要进行端口映射。

**核心概念**：将宿主机的端口映射到容器内的服务端口。
**公式**：`podman run -d -p <宿主机端口>:<容器端口> <镜像名>`

例如，运行一个Nginx容器，并将宿主机的8000端口映射到容器的80端口：
```bash
podman run -d -p 8000:80 nginx
```
之后，即可通过访问 `http://宿主机IP:8000` 来访问容器内的Nginx服务。

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_46.png)

## 目录映射（数据持久化）

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_48.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_50.png)

容器内的数据是临时的，当容器被删除时，其中的数据也会丢失。为了实现数据持久化，可以将宿主机的目录映射到容器内的目录。

**核心概念**：将宿主机的目录挂载到容器内，使容器对该目录的读写操作直接保存在宿主机上。
**公式**：`podman run -d -v <宿主机目录路径>:<容器目录路径> <镜像名>`

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_52.png)

例如，运行一个Nginx容器，并将宿主机的 `/opt/webroot` 目录映射到容器内的网页目录 `/usr/share/nginx/html`：
```bash
podman run -d -v /opt/webroot:/usr/share/nginx/html -p 8001:80 nginx
```
这样，容器服务的网页文件实际存储在宿主机 `/opt/webroot` 下，即使容器重启或删除，数据也不会丢失。

## 文件复制

除了目录映射，有时我们只需要临时向容器内传递单个文件，可以使用复制命令。

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_54.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_56.png)

**命令**：`podman cp <宿主机文件路径> <容器ID或名称>:<容器内目标路径>`

例如，将宿主机上的 `index.html` 复制到容器内：
```bash
podman cp /opt/index.html myweb:/usr/share/nginx/html/
```

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_58.png)

## 检查容器信息

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_60.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_62.png)

我们可以查看容器的详细配置和运行时信息。

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_64.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_66.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_68.png)

**命令**：`podman container inspect <容器ID或名称>`
此命令会以JSON格式输出容器的详细配置，包括网络设置、挂载卷、环境变量等。

---

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_70.png)

![](img/b818c8a4fe5e1de9dbdfe2a19cf6616e_72.png)

本节课中我们一起学习了Podman容器的核心操作。我们掌握了如何使用 `run` 命令创建并运行容器，使用 `ps` 查看容器状态，使用 `exec` 进入容器执行命令。更重要的是，我们理解了**端口映射**（`-p`）是实现容器服务外部访问的关键，而**目录映射**（`-v`）则是实现容器数据持久化存储的基础。最后，我们还学习了容器的生命周期管理命令（`stop`， `start`， `rm`）以及文件复制（`cp`）和信息检查（`inspect`）等实用技巧。这些是管理和使用容器化应用的基本功。