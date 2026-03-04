# Docker入门教程：P97：Docker容器与镜像管理命令

![](img/b50f2ad637bd4255b973917f295390fe_1.png)

![](img/b50f2ad637bd4255b973917f295390fe_3.png)

![](img/b50f2ad637bd4255b973917f295390fe_5.png)

![](img/b50f2ad637bd4255b973917f295390fe_7.png)

![](img/b50f2ad637bd4255b973917f295390fe_8.png)

在本节课中，我们将学习Docker的核心操作，包括如何管理镜像和容器。我们将从查看和下载镜像开始，然后学习如何创建、运行、进入、停止和删除容器，最后会涉及端口映射、容器自启和日志查看等实用操作。

![](img/b50f2ad637bd4255b973917f295390fe_10.png)

![](img/b50f2ad637bd4255b973917f295390fe_11.png)

![](img/b50f2ad637bd4255b973917f295390fe_13.png)

---

## 镜像管理命令

上一节我们安装了Docker，本节中我们来看看如何使用它。首先，我们需要学习如何管理Docker镜像，镜像是创建容器的基础。

![](img/b50f2ad637bd4255b973917f295390fe_15.png)

![](img/b50f2ad637bd4255b973917f295390fe_17.png)

![](img/b50f2ad637bd4255b973917f295390fe_18.png)

### 查看本地镜像

要查看本地已下载的镜像，可以使用 `docker images` 命令。

![](img/b50f2ad637bd4255b973917f295390fe_20.png)

![](img/b50f2ad637bd4255b973917f295390fe_21.png)

![](img/b50f2ad637bd4255b973917f295390fe_22.png)

```bash
docker images
```

![](img/b50f2ad637bd4255b973917f295390fe_24.png)

![](img/b50f2ad637bd4255b973917f295390fe_25.png)

执行此命令后，如果本地没有镜像，列表将为空。

![](img/b50f2ad637bd4255b973917f295390fe_27.png)

### 搜索镜像

![](img/b50f2ad637bd4255b973917f295390fe_29.png)

![](img/b50f2ad637bd4255b973917f295390fe_30.png)

![](img/b50f2ad637bd4255b973917f295390fe_32.png)

在下载镜像前，可以先搜索可用的镜像。使用 `docker search` 命令。

![](img/b50f2ad637bd4255b973917f295390fe_34.png)

![](img/b50f2ad637bd4255b973917f295390fe_36.png)

![](img/b50f2ad637bd4255b973917f295390fe_38.png)

![](img/b50f2ad637bd4255b973917f295390fe_40.png)

```bash
docker search nginx
```

![](img/b50f2ad637bd4255b973917f295390fe_42.png)

![](img/b50f2ad637bd4255b973917f295390fe_44.png)

命令会列出名称中包含“nginx”的镜像。其中，`OFFICIAL` 列标记为 `[OK]` 的表示是官方构建的镜像。

![](img/b50f2ad637bd4255b973917f295390fe_45.png)

![](img/b50f2ad637bd4255b973917f295390fe_47.png)

**注意**：命令行搜索通常不显示镜像的具体版本号。要查看所有可用版本，建议访问 Docker 官方仓库网站 `hub.docker.com`。

![](img/b50f2ad637bd4255b973917f295390fe_49.png)

![](img/b50f2ad637bd4255b973917f295390fe_51.png)

### 下载镜像

![](img/b50f2ad637bd4255b973917f295390fe_53.png)

![](img/b50f2ad637bd4255b973917f295390fe_55.png)

下载镜像使用 `docker pull` 命令。如果不指定版本，默认下载 `latest`（最新）标签的镜像。

![](img/b50f2ad637bd4255b973917f295390fe_57.png)

![](img/b50f2ad637bd4255b973917f295390fe_59.png)

```bash
# 下载最新版 nginx 镜像
docker pull nginx

# 下载指定版本的 nginx 镜像（例如 1.22.1）
docker pull nginx:1.22.1
```

![](img/b50f2ad637bd4255b973917f295390fe_61.png)

下载完成后，再次使用 `docker images` 命令即可看到已下载的镜像。

### 删除镜像

要删除本地镜像，使用 `docker rmi` 命令。必须指定要删除的镜像名称及标签（版本）。

![](img/b50f2ad637bd4255b973917f295390fe_63.png)

![](img/b50f2ad637bd4255b973917f295390fe_64.png)

```bash
# 删除指定版本的 nginx 镜像
docker rmi nginx:1.22.1
```

![](img/b50f2ad637bd4255b973917f295390fe_66.png)

![](img/b50f2ad637bd4255b973917f295390fe_68.png)

**注意**：如果镜像正在被某个容器使用，则需要先删除该容器才能删除镜像。

![](img/b50f2ad637bd4255b973917f295390fe_70.png)

![](img/b50f2ad637bd4255b973917f295390fe_72.png)

---

## 容器管理命令

![](img/b50f2ad637bd4255b973917f295390fe_74.png)

有了镜像之后，我们就可以创建并管理容器了。容器是基于镜像运行的一个实例。

![](img/b50f2ad637bd4255b973917f295390fe_76.png)

![](img/b50f2ad637bd4255b973917f295390fe_78.png)

![](img/b50f2ad637bd4255b973917f295390fe_80.png)

### 创建并运行容器

创建容器的主要命令是 `docker run`。它包含许多选项来控制容器的行为。

以下是创建容器时常用的选项组合：

![](img/b50f2ad637bd4255b973917f295390fe_82.png)

*   **`-it`**：以交互模式运行容器并分配一个伪终端（TTY）。通常用于需要进入容器执行命令的场景，但退出后容器会停止。
*   **`-d`**：让容器在后台运行。
*   **`--name`**：为容器指定一个自定义名称。
*   **`-p`**：映射宿主机端口到容器端口，格式为 `-p <宿主机端口>:<容器端口>`。

![](img/b50f2ad637bd4255b973917f295390fe_84.png)

![](img/b50f2ad637bd4255b973917f295390fe_86.png)

以下是几个创建容器的示例：

![](img/b50f2ad637bd4255b973917f295390fe_88.png)

```bash
# 示例1：创建并立即进入一个 centos 容器（退出后容器停止）
docker run -it --name=centos7.9 centos:7.9.2009 /bin/bash

![](img/b50f2ad637bd4255b973917f295390fe_90.png)

# 示例2：在后台运行一个名为 nginx01 的 nginx 容器
docker run -d --name=nginx01 nginx:1.22.1

![](img/b50f2ad637bd4255b973917f295390fe_92.png)

![](img/b50f2ad637bd4255b973917f295390fe_94.png)

# 示例3：在后台运行 nginx 容器，并将宿主机的 81 端口映射到容器的 80 端口
docker run -d --name=nginx02 -p 81:80 nginx:1.22.1
```

![](img/b50f2ad637bd4255b973917f295390fe_96.png)

### 查看容器状态

使用 `docker ps` 命令可以查看当前正在运行的容器。

```bash
# 查看正在运行的容器
docker ps

# 查看所有容器（包括已停止的）
docker ps -a
```

### 进入运行中的容器

对于在后台运行的容器（使用 `-d` 参数创建），可以使用 `docker exec` 命令进入其内部。

```bash
# 进入名为 nginx01 的容器，并使用 /bin/bash 作为shell
docker exec -it nginx01 /bin/bash
```

进入容器后，可以像操作一个独立的Linux系统一样执行命令。输入 `exit` 可以退出容器，但容器本身会继续在后台运行。

![](img/b50f2ad637bd4255b973917f295390fe_98.png)

![](img/b50f2ad637bd4255b973917f295390fe_100.png)

### 容器的启动、停止、重启与删除

![](img/b50f2ad637bd4255b973917f295390fe_102.png)

以下是对容器生命周期进行管理的常用命令：

![](img/b50f2ad637bd4255b973917f295390fe_104.png)

```bash
# 停止一个运行中的容器
docker stop nginx01

# 启动一个已停止的容器
docker start nginx01

![](img/b50f2ad637bd4255b973917f295390fe_106.png)

![](img/b50f2ad637bd4255b973917f295390fe_108.png)

# 重启一个容器
docker restart nginx01

# 删除一个已停止的容器
docker rm nginx01

![](img/b50f2ad637bd4255b973917f295390fe_110.png)

# 强制删除一个容器（无论是否在运行）
docker rm -f nginx01
```

![](img/b50f2ad637bd4255b973917f295390fe_112.png)

![](img/b50f2ad637bd4255b973917f295390fe_114.png)

### 设置容器随机自启

![](img/b50f2ad637bd4255b973917f295390fe_116.png)

![](img/b50f2ad637bd4255b973917f295390fe_118.png)

如果希望容器在宿主机重启后自动启动，可以在创建时或之后进行设置。

![](img/b50f2ad637bd4255b973917f295390fe_120.png)

![](img/b50f2ad637bd4255b973917f295390fe_122.png)

```bash
# 创建时设置随机自启
docker run -d --name=nginx03 --restart=always -p 82:80 nginx:1.22.1

![](img/b50f2ad637bd4255b973917f295390fe_124.png)

![](img/b50f2ad637bd4255b973917f295390fe_126.png)

# 为已存在的容器更新为随机自启
docker update --restart=always nginx02
```

![](img/b50f2ad637bd4255b973917f295390fe_128.png)

![](img/b50f2ad637bd4255b973917f295390fe_130.png)

![](img/b50f2ad637bd4255b973917f295390fe_132.png)

### 查看容器日志

容器内应用输出的日志可以通过 `docker logs` 命令查看，这对于调试和监控非常有用。

![](img/b50f2ad637bd4255b973917f295390fe_134.png)

![](img/b50f2ad637bd4255b973917f295390fe_136.png)

```bash
# 查看容器 nginx02 的日志
docker logs nginx02

![](img/b50f2ad637bd4255b973917f295390fe_138.png)

![](img/b50f2ad637bd4255b973917f295390fe_139.png)

# 实时查看日志（类似 tail -f）
docker logs -f nginx02
```

![](img/b50f2ad637bd4255b973917f295390fe_141.png)

![](img/b50f2ad637bd4255b973917f295390fe_143.png)

---

![](img/b50f2ad637bd4255b973917f295390fe_145.png)

![](img/b50f2ad637bd4255b973917f295390fe_147.png)

## 总结

![](img/b50f2ad637bd4255b973917f295390fe_149.png)

![](img/b50f2ad637bd4255b973917f295390fe_150.png)

![](img/b50f2ad637bd4255b973917f295390fe_152.png)

![](img/b50f2ad637bd4255b973917f295390fe_154.png)

本节课中我们一起学习了Docker镜像和容器的基础管理命令。

![](img/b50f2ad637bd4255b973917f295390fe_156.png)

*   **镜像管理**：我们学会了使用 `docker images` 查看镜像、`docker search` 搜索镜像、`docker pull` 下载镜像以及 `docker rmi` 删除镜像。
*   **容器管理**：我们掌握了使用 `docker run` 及其各种选项（`-d`, `-it`, `-p`, `--name`）来创建容器。学习了如何通过 `docker ps` 查看状态、`docker exec` 进入容器、以及使用 `start`/`stop`/`restart`/`rm` 来管理容器的生命周期。
*   **高级功能**：我们还了解了如何设置容器随机自启（`--restart=always`）以及如何查看容器日志（`docker logs`）。

![](img/b50f2ad637bd4255b973917f295390fe_158.png)

![](img/b50f2ad637bd4255b973917f295390fe_160.png)

![](img/b50f2ad637bd4255b973917f295390fe_162.png)

这些命令是使用Docker的基础，熟练掌握后，你就能轻松地运行和管理各种容器化应用了。