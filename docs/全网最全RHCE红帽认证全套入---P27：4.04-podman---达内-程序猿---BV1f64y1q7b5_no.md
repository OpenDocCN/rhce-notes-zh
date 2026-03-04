# Podman容器管理：P27：4.04-podman容器操作 🐳

![](img/a3c8b5bf7edea781b262a3301aef1e3b_1.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_3.png)

在本节课中，我们将学习如何使用Podman进行容器的基本操作，包括创建、查看、访问、停止、启动、删除容器，以及关键的端口映射和目录映射技术。这些是管理容器化应用的核心技能。

![](img/a3c8b5bf7edea781b262a3301aef1e3b_5.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_7.png)

## 创建容器

上一节我们介绍了镜像管理，本节中我们来看看如何将镜像运行起来，即创建容器。创建容器的核心指令是 `podman run`。

以下是创建容器时常用的选项：
*   `podman run <镜像名>`：以前台方式运行容器。
*   `podman run -d <镜像名>`：以后台守护进程方式运行容器。
*   `podman run --name <容器名> <镜像名>`：为容器指定一个自定义名称。

## 查看容器

![](img/a3c8b5bf7edea781b262a3301aef1e3b_9.png)

创建容器后，我们需要知道如何查看它们的状态。这与查看系统进程类似。

以下是查看容器的命令：
*   `podman ps`：列出当前正在运行的容器。
*   `podman ps -a`：列出所有已创建的容器（包括已停止的）。

![](img/a3c8b5bf7edea781b262a3301aef1e3b_11.png)

## 访问与执行命令

![](img/a3c8b5bf7edea781b262a3301aef1e3b_13.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_15.png)

对于正在运行的容器，我们可能需要进入其内部环境或执行命令。

![](img/a3c8b5bf7edea781b262a3301aef1e3b_17.png)

以下是访问容器的命令：
*   `podman exec <容器ID或名称> <命令>`：在容器内执行一条命令。
*   `podman exec -it <容器ID或名称> /bin/bash`：以交互式终端方式进入容器内部。

![](img/a3c8b5bf7edea781b262a3301aef1e3b_19.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_21.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_23.png)

## 容器的生命周期管理

![](img/a3c8b5bf7edea781b262a3301aef1e3b_25.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_27.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_29.png)

容器像服务一样，有其生命周期，我们可以控制它的启动、停止和重启。

![](img/a3c8b5bf7edea781b262a3301aef1e3b_31.png)

以下是管理容器状态的命令：
*   `podman stop <容器ID或名称>`：停止一个正在运行的容器。
*   `podman start <容器ID或名称>`：启动一个已停止的容器。
*   `podman restart <容器ID或名称>`：重启一个容器。

## 删除容器

![](img/a3c8b5bf7edea781b262a3301aef1e3b_33.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_35.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_37.png)

当容器不再需要时，可以将其删除以释放资源。请注意，删除镜像使用 `podman rmi`，而删除容器使用 `podman rm`。

![](img/a3c8b5bf7edea781b262a3301aef1e3b_39.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_41.png)

以下是删除容器的命令：
*   `podman rm <容器ID>`：删除一个已停止的容器。
*   `podman rm -f <容器ID>`：强制删除一个容器（包括正在运行的）。
*   `podman rm -l`：删除最近创建的一个容器。

![](img/a3c8b5bf7edea781b262a3301aef1e3b_43.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_45.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_47.png)

## 端口映射

为了让外部网络能够访问容器内运行的服务（如Web服务器），需要进行端口映射。这通过 `-p` 选项实现。

![](img/a3c8b5bf7edea781b262a3301aef1e3b_49.png)

其公式为：
```
podman run -p <主机端口>:<容器端口> <镜像名>
```
例如，`podman run -p 8080:80 nginx` 表示将主机的8080端口映射到容器的80端口。这样，访问 `http://主机IP:8080` 就能访问到容器内的Nginx服务。

![](img/a3c8b5bf7edea781b262a3301aef1e3b_51.png)

## 目录映射（持久化存储）

![](img/a3c8b5bf7edea781b262a3301aef1e3b_53.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_55.png)

容器内的数据默认是临时的，容器删除后数据会丢失。为了持久化保存数据（如网站文件、数据库数据），需要将主机目录映射到容器内目录，这通过 `-v` 选项实现。

![](img/a3c8b5bf7edea781b262a3301aef1e3b_57.png)

其公式为：
```
podman run -v <主机目录路径>:<容器目录路径> <镜像名>
```
例如，`podman run -v /opt/webdata:/usr/share/nginx/html nginx` 表示将主机的 `/opt/webdata` 目录挂载到容器的网页目录。此后，容器对该目录的读写都会直接保存在主机上。

## 文件复制

除了目录映射，有时也需要临时向容器内复制单个文件，可以使用 `podman cp` 命令。

![](img/a3c8b5bf7edea781b262a3301aef1e3b_59.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_61.png)

其公式为：
```
podman cp <主机文件路径> <容器ID或名称>:<容器内目标路径>
```
例如，`podman cp index.html mycontainer:/tmp/` 将主机当前目录的 `index.html` 复制到容器 `mycontainer` 的 `/tmp/` 目录下。

## 检查容器信息

![](img/a3c8b5bf7edea781b262a3301aef1e3b_63.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_65.png)

要查看容器的详细配置信息，如网络设置、挂载点等，可以使用 `podman inspect` 命令。

![](img/a3c8b5bf7edea781b262a3301aef1e3b_67.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_69.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_71.png)

其命令为：
```
podman container inspect <容器ID或名称>
```

![](img/a3c8b5bf7edea781b262a3301aef1e3b_73.png)

---

![](img/a3c8b5bf7edea781b262a3301aef1e3b_75.png)

![](img/a3c8b5bf7edea781b262a3301aef1e3b_77.png)

本节课中我们一起学习了Podman容器的全套基本操作。从使用 `podman run` 创建容器，到用 `ps`、`exec`、`stop`、`start`、`rm` 进行管理，再到通过 `-p` 实现端口映射和 `-v` 实现数据持久化，这些是构建和管理容器化应用的基石。熟练掌握这些命令，你就能有效地让容器运行起来并为其配置网络与存储。