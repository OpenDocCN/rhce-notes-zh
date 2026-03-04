# Podman容器管理教程：P28：4.04-podman容器操作

![](img/9e12feb850abb3edac378aae7786b1f6_1.png)

![](img/9e12feb850abb3edac378aae7786b1f6_3.png)

在本节课中，我们将要学习如何使用Podman进行容器的基本操作，包括创建、查看、访问、停止、启动、删除容器，以及端口映射和目录映射等核心功能。

![](img/9e12feb850abb3edac378aae7786b1f6_5.png)

![](img/9e12feb850abb3edac378aae7786b1f6_7.png)

## 创建容器

上一节我们介绍了镜像的管理，本节中我们来看看如何将镜像运行起来，即创建容器。创建容器的核心指令是 `podman run`。

以下是创建容器时常用的选项：
*   `podman run`：运行镜像以创建并启动容器。
*   `-d`：以后台（守护进程）模式运行容器。
*   `--name <容器名称>`：为容器指定一个自定义名称。
*   `-p <主机端口>:<容器端口>`：将主机端口映射到容器内部端口。
*   `-v <主机目录>:<容器目录>`：将主机目录挂载（映射）到容器内部目录。

![](img/9e12feb850abb3edac378aae7786b1f6_9.png)

## 查看容器

创建容器后，我们需要能够查看容器的状态。Podman提供了 `podman ps` 命令来管理容器列表。

![](img/9e12feb850abb3edac378aae7786b1f6_11.png)

以下是查看容器的相关命令：
*   `podman ps`：列出当前正在运行的容器。
*   `podman ps -a`：列出所有已创建的容器（包括已停止的）。

![](img/9e12feb850abb3edac378aae7786b1f6_13.png)

![](img/9e12feb850abb3edac378aae7786b1f6_15.png)

## 访问与执行命令

![](img/9e12feb850abb3edac378aae7786b1f6_17.png)

有时我们需要进入容器内部或在其内部执行命令，这可以通过 `podman exec` 指令实现。

![](img/9e12feb850abb3edac378aae7786b1f6_19.png)

![](img/9e12feb850abb3edac378aae7786b1f6_21.png)

![](img/9e12feb850abb3edac378aae7786b1f6_23.png)

![](img/9e12feb850abb3edac378aae7786b1f6_25.png)

以下是访问容器的常用方法：
*   `podman exec <容器ID或名称> <命令>`：在指定容器内执行单条命令。
*   `podman exec -it <容器ID或名称> /bin/bash`：以交互模式进入容器的命令行环境。

![](img/9e12feb850abb3edac378aae7786b1f6_27.png)

![](img/9e12feb850abb3edac378aae7786b1f6_29.png)

## 容器的生命周期管理

![](img/9e12feb850abb3edac378aae7786b1f6_31.png)

与系统服务类似，容器也有启动、停止、重启和删除的生命周期。

![](img/9e12feb850abb3edac378aae7786b1f6_33.png)

![](img/9e12feb850abb3edac378aae7786b1f6_35.png)

以下是管理容器生命周期的命令：
*   `podman stop <容器ID或名称>`：停止一个正在运行的容器。
*   `podman start <容器ID或名称>`：启动一个已停止的容器。
*   `podman restart <容器ID或名称>`：重启一个容器。
*   `podman rm <容器ID或名称>`：删除一个已停止的容器。
*   `podman rm -f <容器ID或名称>`：强制删除一个容器（包括正在运行的）。

![](img/9e12feb850abb3edac378aae7786b1f6_37.png)

![](img/9e12feb850abb3edac378aae7786b1f6_39.png)

![](img/9e12feb850abb3edac378aae7786b1f6_41.png)

## 端口映射详解

![](img/9e12feb850abb3edac378aae7786b1f6_43.png)

![](img/9e12feb850abb3edac378aae7786b1f6_44.png)

![](img/9e12feb850abb3edac378aae7786b1f6_46.png)

端口映射允许外部客户端通过访问主机端口来访问容器内运行的服务。

其核心公式为：
```
-p <主机端口>:<容器端口>
```
例如，`-p 8000:80` 表示将主机的8000端口映射到容器的80端口。外部用户访问 `主机IP:8000` 即可访问容器内的Web服务。

![](img/9e12feb850abb3edac378aae7786b1f6_48.png)

## 目录映射（持久化存储）

![](img/9e12feb850abb3edac378aae7786b1f6_50.png)

![](img/9e12feb850abb3edac378aae7786b1f6_52.png)

目录映射（或称卷挂载）用于将主机上的目录与容器内的目录关联起来，从而实现容器数据的持久化存储。

![](img/9e12feb850abb3edac378aae7786b1f6_54.png)

其核心公式为：
```
-v <主机目录路径>:<容器目录路径>
```
例如，`-v /opt/webroot:/usr/share/nginx/html` 会将主机 `/opt/webroot` 目录挂载到容器的网页目录。这样，即使容器被删除，网页数据仍保留在主机的 `/opt/webroot` 目录中。

![](img/9e12feb850abb3edac378aae7786b1f6_56.png)

## 文件复制

除了目录映射，还可以在容器和主机之间直接复制单个文件。

![](img/9e12feb850abb3edac378aae7786b1f6_58.png)

![](img/9e12feb850abb3edac378aae7786b1f6_60.png)

相关命令为：
*   `podman cp <主机文件路径> <容器ID或名称>:<容器内路径>`：将文件从主机复制到容器内。

## 容器信息检查

![](img/9e12feb850abb3edac378aae7786b1f6_62.png)

我们可以查看容器的详细配置和状态信息。

![](img/9e12feb850abb3edac378aae7786b1f6_64.png)

![](img/9e12feb850abb3edac378aae7786b1f6_66.png)

![](img/9e12feb850abb3edac378aae7786b1f6_68.png)

相关命令为：
*   `podman inspect <容器ID或名称>`：查看指定容器的详细信息（如IP地址、挂载卷、端口映射等）。

![](img/9e12feb850abb3edac378aae7786b1f6_70.png)

![](img/9e12feb850abb3edac378aae7786b1f6_72.png)

---

![](img/9e12feb850abb3edac378aae7786b1f6_74.png)

![](img/9e12feb850abb3edac378aae7786b1f6_76.png)

本节课中我们一起学习了Podman管理容器的全套基本操作。我们掌握了如何使用 `run` 创建并运行容器，使用 `ps` 查看容器状态，使用 `exec` 进入容器执行命令，以及使用 `stop`、`start`、`rm` 管理容器的生命周期。此外，我们还深入理解了端口映射 (`-p`) 和目录映射 (`-v`) 这两个关键概念，它们分别是实现容器网络服务和数据持久化的基石。通过结合这些命令和选项，你已经可以有效地使用Podman来部署和管理容器化应用了。