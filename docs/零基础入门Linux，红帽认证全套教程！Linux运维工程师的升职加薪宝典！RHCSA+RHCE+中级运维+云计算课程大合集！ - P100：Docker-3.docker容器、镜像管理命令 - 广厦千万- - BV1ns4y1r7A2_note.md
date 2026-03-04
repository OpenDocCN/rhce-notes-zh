# Docker入门教程：P100：Docker-3.docker容器、镜像管理命令 🐳

![](img/9e04839e85ebe572e93ecf9ecb04cb35_1.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_3.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_5.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_7.png)

在本节课中，我们将学习Docker的核心操作，包括如何管理镜像和容器。我们将从查看和下载镜像开始，逐步学习如何创建、运行、进入、停止、删除容器，以及如何进行端口映射和设置容器自启动。这些是使用Docker的基础，掌握它们将帮助你迈出容器化应用部署的第一步。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_8.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_10.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_11.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_13.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_14.png)

---

## 检查Docker状态与配置

上一节我们完成了Docker的安装。现在，让我们先确认Docker服务是否正常运行，并检查之前配置的镜像加速器是否生效。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_16.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_17.png)

首先，检查Docker服务的运行状态：
```bash
systemctl status docker
```
如果状态显示为 `running`，则说明Docker服务已成功启动。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_19.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_20.png)

接下来，验证镜像加速器的配置。加速器配置文件通常位于 `/etc/docker/daemon.json`。我们可以查看该文件内容来确认配置：
```bash
cat /etc/docker/daemon.json
```
如果文件中包含了类似国内镜像加速器的地址（如 `https://registry.docker-cn.com`），则说明加速器已配置成功。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_22.png)

---

![](img/9e04839e85ebe572e93ecf9ecb04cb35_23.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_24.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_26.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_27.png)

## Docker命令概览与分类

Docker提供了丰富的命令，初学者可能会感到困惑。我们可以通过输入 `docker` 命令来查看所有可用命令及其简要说明。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_29.png)

Docker官方对命令进行了分类，主要包含以下几类：
*   **环境管理命令**：如 `docker version`（查看版本）、`docker info`（查看系统信息）。
*   **容器生命周期管理命令**：如 `docker run`（创建并运行容器）、`docker start/stop/restart`（启动/停止/重启容器）。
*   **容器操作命令**：如 `docker exec`（进入容器）、`docker logs`（查看容器日志）。
*   **镜像管理命令**：这是本节课的重点，包括对镜像仓库的操作（如 `docker search`、`docker pull`）和对本地镜像的操作（如 `docker images`、`docker rmi`）。
*   **容器资源管理命令**：如管理数据卷、网络和日志等。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_31.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_32.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_34.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_36.png)

我们将从最常用的镜像管理命令开始学习。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_38.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_40.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_42.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_44.png)

---

![](img/9e04839e85ebe572e93ecf9ecb04cb35_45.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_47.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_48.png)

## 镜像管理命令 🖼️

![](img/9e04839e85ebe572e93ecf9ecb04cb35_50.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_52.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_53.png)

镜像是创建容器的基础。以下是管理镜像最常用的四个命令。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_55.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_57.png)

### 1. 查看本地镜像 (`docker images`)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_59.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_61.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_63.png)

这条命令用于列出当前主机上所有已下载的Docker镜像。
```bash
docker images
```
在刚安装完Docker时，执行此命令可能看不到任何镜像，因为本地还没有下载任何镜像。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_65.png)

### 2. 搜索镜像 (`docker search`)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_67.png)

如果我们想下载某个镜像，但不知道确切名称或想查看有哪些可用版本，可以先进行搜索。
```bash
docker search nginx
```
命令会返回一系列包含“nginx”的镜像。其中：
*   `NAME` 列是镜像在仓库中的名称。
*   `DESCRIPTION` 列是镜像的描述信息。
*   `OFFICIAL` 列标注是否为官方构建的镜像（显示 `[OK]`）。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_69.png)

**注意**：命令行搜索的结果较为简略，通常无法看到具体的镜像版本标签（如 `1.22.1`）。要查看详细的版本信息，建议直接访问 Docker 官方仓库网站 `https://hub.docker.com` 进行搜索。

### 3. 拉取（下载）镜像 (`docker pull`)

从镜像仓库将镜像下载到本地。**强烈建议下载时指定版本标签（Tag）**。
```bash
# 不指定版本，默认下载 latest（最新）标签的镜像
docker pull nginx

![](img/9e04839e85ebe572e93ecf9ecb04cb35_71.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_72.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_73.png)

# 指定下载特定版本的镜像（推荐）
docker pull nginx:1.22.1
```
如果不指定标签，Docker会默认拉取标记为 `latest` 的镜像，这可能不是最稳定的版本。在生产环境中，应指定具体的稳定版本号。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_75.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_77.png)

### 4. 删除本地镜像 (`docker rmi`)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_79.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_81.png)

当不再需要某个本地镜像时，可以使用此命令删除它。删除时需要指定镜像名和标签。
```bash
# 删除指定标签的镜像
docker rmi nginx:1.22.1

![](img/9e04839e85ebe572e93ecf9ecb04cb35_83.png)

# 如果镜像只有一个标签，也可以只使用镜像名
docker rmi centos:7
```
**注意**：如果一个镜像有多个标签（如 `nginx:1.22.1` 和 `nginx:latest` 可能指向同一个镜像层），删除一个标签不会立即删除镜像层，只有当所有标签都被移除后，镜像层才会被真正删除。

---

![](img/9e04839e85ebe572e93ecf9ecb04cb35_85.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_87.png)

## 容器管理命令 📦

![](img/9e04839e85ebe572e93ecf9ecb04cb35_89.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_91.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_93.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_95.png)

有了镜像之后，我们就可以创建和运行容器了。容器是基于镜像创建的运行实例。

### 1. 创建并运行容器 (`docker run`)

这是最核心的命令，用于从镜像创建一个新的容器并运行它。它包含许多选项，这里介绍几个最基本的。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_97.png)

*   **`-it`**：这是两个选项的组合。
    *   `-i` (`--interactive`)：保持标准输入（STDIN）打开，允许我们与容器交互。
    *   `-t` (`--tty`)：为容器分配一个伪终端（pseudo-TTY），这样它看起来就像一个标准的命令行界面。
    通常一起使用，以便我们能够进入容器的交互式命令行。
*   **`-d`** (`--detach`)：在后台运行容器并打印容器ID。这是运行守护进程类服务（如Nginx、MySQL）的常用方式。
*   **`--name`**：为容器指定一个自定义名称，便于后续管理。如果不指定，Docker会随机生成一个名字。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_99.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_101.png)

让我们通过两个例子来理解：

![](img/9e04839e85ebe572e93ecf9ecb04cb35_103.png)

**示例A：交互式运行容器（前台运行）**
```bash
docker run -it --name=mycentos centos:7 /bin/bash
```
执行后，你会直接进入容器的 `bash` 终端。当你输入 `exit` 退出时，这个容器也会随之停止运行。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_105.png)

**示例B：后台运行容器**
```bash
docker run -d --name=mynginx nginx:1.22.1
```
执行后，容器在后台启动，并返回一个长的容器ID。命令行不会进入容器内部。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_107.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_108.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_110.png)

### 2. 查看容器状态 (`docker ps`)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_112.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_114.png)

用于列出当前正在运行的容器。
```bash
docker ps
```
要查看所有容器（包括已停止的），需要加上 `-a` 选项：
```bash
docker ps -a
```
输出信息包括容器ID、所使用的镜像、状态、端口映射和名称等。

### 3. 进入运行中的容器 (`docker exec`)

对于在后台运行的容器（使用 `-d` 参数启动），我们可以使用此命令进入其内部进行操作。
```bash
docker exec -it mynginx /bin/bash
```
`-it` 参数的意义与 `docker run` 中相同，用于开启交互式终端。`/bin/bash` 指定了容器内使用的shell程序。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_116.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_118.png)

### 4. 容器的启动、停止与重启

*   **启动已停止的容器**：`docker start <容器名或ID>`
*   **停止运行中的容器**：`docker stop <容器名或ID>` （发送SIGTERM信号，允许容器进行优雅关闭）
*   **强制停止容器**：`docker kill <容器名或ID>` （发送SIGKILL信号，立即强制停止）
*   **重启容器**：`docker restart <容器名或ID>`

### 5. 删除容器 (`docker rm`)

删除已停止的容器。**无法直接删除正在运行的容器**。
```bash
# 删除已停止的容器
docker rm mycentos

# 强制删除一个容器（无论是否在运行）
docker rm -f mynginx
```
建议先使用 `docker stop` 停止容器，再使用 `docker rm` 删除，而不是直接使用 `-f` 参数强制删除。

---

![](img/9e04839e85ebe572e93ecf9ecb04cb35_120.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_122.png)

## 容器网络与端口映射 🔗

![](img/9e04839e85ebe572e93ecf9ecb04cb35_124.png)

默认情况下，容器内的网络服务（如Nginx的80端口）无法从宿主机外部直接访问。为了使外部能够访问容器内的服务，需要进行**端口映射**。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_126.png)

### 端口映射 (`-p`)

使用 `docker run` 命令的 `-p` 参数，可以将宿主机的端口映射到容器的内部端口。
```bash
docker run -d --name=nginx-web -p 8080:80 nginx:1.22.1
```
这个命令做了以下事情：
1.  从 `nginx:1.22.1` 镜像创建并在后台运行一个名为 `nginx-web` 的容器。
2.  将宿主机的 `8080` 端口映射到容器内部的 `80` 端口。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_128.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_130.png)

现在，你可以通过访问宿主机的IP地址和8080端口（例如 `http://宿主机IP:8080`）来访问容器内运行的Nginx服务了。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_132.png)

---

![](img/9e04839e85ebe572e93ecf9ecb04cb35_134.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_135.png)

## 容器高级管理

![](img/9e04839e85ebe572e93ecf9ecb04cb35_137.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_139.png)

### 设置容器自启动

![](img/9e04839e85ebe572e93ecf9ecb04cb35_141.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_143.png)

我们希望某些关键容器（如数据库、Web服务器）在宿主机重启后能自动启动。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_145.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_147.png)

*   **创建时设置自启动**：在 `docker run` 命令中加入 `--restart=always` 选项。
    ```bash
    docker run -d --name=nginx-auto --restart=always -p 80:80 nginx:1.22.1
    ```
*   **为已存在的容器更新自启动策略**：使用 `docker update` 命令。
    ```bash
    docker update --restart=always <容器名或ID>
    ```

![](img/9e04839e85ebe572e93ecf9ecb04cb35_149.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_151.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_153.png)

### 查看容器日志 (`docker logs`)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_155.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_157.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_158.png)

容器在运行时会将日志输出到标准输出（STDOUT）和标准错误（STDERR）。我们可以使用 `docker logs` 命令来查看这些日志，这对于调试和监控非常有用。
```bash
# 查看容器日志
docker logs nginx-web

![](img/9e04839e85ebe572e93ecf9ecb04cb35_160.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_162.png)

# 实时查看（跟踪）日志输出，类似于 `tail -f`
docker logs -f nginx-web
```

![](img/9e04839e85ebe572e93ecf9ecb04cb35_164.png)

### 在宿主机和容器间复制文件 (`docker cp`)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_166.png)

有时我们需要将文件从宿主机复制到容器内，或者从容器内复制文件到宿主机。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_168.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_169.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_171.png)

*   **从宿主机复制到容器**：
    ```bash
    docker cp /宿主机/路径/文件.txt nginx-web:/容器内/路径/
    ```
*   **从容器复制到宿主机**：
    ```bash
    docker cp nginx-web:/容器内/路径/文件.txt /宿主机/路径/
    ```

![](img/9e04839e85ebe572e93ecf9ecb04cb35_173.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_175.png)

---

![](img/9e04839e85ebe572e93ecf9ecb04cb35_177.png)

## 总结 📝

![](img/9e04839e85ebe572e93ecf9ecb04cb35_179.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_180.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_182.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_184.png)

本节课中我们一起学习了Docker镜像和容器管理的基础命令。我们首先学习了如何查看、搜索、拉取和删除镜像。然后，我们深入探讨了容器的核心操作：使用 `docker run` 创建容器（区分了前台交互 `-it` 和后台运行 `-d` 模式），使用 `docker ps` 查看状态，使用 `docker exec` 进入容器，以及如何启动、停止、重启和删除容器。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_186.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_188.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_190.png)

此外，我们还掌握了两个关键实践：通过 `-p` 参数进行端口映射，使外部能够访问容器内的服务；以及通过 `--restart` 策略设置容器随宿主机自启动。最后，我们了解了查看容器日志和与容器之间复制文件的方法。

![](img/9e04839e85ebe572e93ecf9ecb04cb35_191.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_193.png)

![](img/9e04839e85ebe572e93ecf9ecb04cb35_195.png)

这些命令是操作Docker的基石，熟练掌握它们将为后续学习更复杂的Docker网络、数据卷管理和Docker Compose编排打下坚实的基础。