# Linux运维全套培训课程：P98：Docker-3.docker容器、镜像管理命令 🐳

![](img/ee2dc99e5cd497b4818fe4c63f08797e_1.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_3.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_5.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_7.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_8.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_10.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_11.png)

在本节课中，我们将要学习Docker的核心操作命令，包括如何管理镜像和容器。通过具体的命令演示，你将学会如何搜索、下载镜像，以及如何创建、启动、停止、进入和删除容器，为后续的Docker应用部署打下坚实基础。

![](img/ee2dc99e5cd497b4818fe4c63f08797e_13.png)

---

## 镜像管理命令

![](img/ee2dc99e5cd497b4818fe4c63f08797e_15.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_17.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_18.png)

上一节我们介绍了Docker的安装与配置，本节中我们来看看如何管理Docker镜像。镜像是创建容器的基础，因此学会管理镜像是使用Docker的第一步。

### 查看本地镜像

![](img/ee2dc99e5cd497b4818fe4c63f08797e_20.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_21.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_22.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_24.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_25.png)

要查看当前系统中已下载的Docker镜像，可以使用 `docker images` 命令。

```bash
docker images
```
执行此命令后，如果本地没有镜像，将只显示表头信息。

![](img/ee2dc99e5cd497b4818fe4c63f08797e_27.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_29.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_30.png)

### 搜索镜像

![](img/ee2dc99e5cd497b4818fe4c63f08797e_32.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_34.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_36.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_38.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_40.png)

在下载镜像前，可以先在Docker官方仓库中搜索。虽然可以在命令行搜索，但更推荐在浏览器中访问[Docker Hub](https://hub.docker.com)查看详细的版本信息。

![](img/ee2dc99e5cd497b4818fe4c63f08797e_42.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_44.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_45.png)

命令行搜索镜像的命令格式如下：
```bash
docker search [镜像名称]
```
例如，搜索Nginx镜像：
```bash
docker search nginx
```
搜索结果会显示镜像名称、描述、是否为官方构建（`OK`标记）以及点赞数。

![](img/ee2dc99e5cd497b4818fe4c63f08797e_47.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_49.png)

### 下载镜像

![](img/ee2dc99e5cd497b4818fe4c63f08797e_51.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_53.png)

从仓库下载镜像到本地使用 `docker pull` 命令。**强烈建议指定镜像版本**，否则默认下载 `latest`（最新）标签的镜像。

![](img/ee2dc99e5cd497b4818fe4c63f08797e_55.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_57.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_59.png)

下载指定版本Nginx镜像的命令如下：
```bash
docker pull nginx:1.22.1
```

### 删除镜像

![](img/ee2dc99e5cd497b4818fe4c63f08797e_61.png)

当不再需要某个镜像时，可以使用 `docker rmi` 命令将其删除。同样需要指定镜像名称和版本。

删除指定版本Nginx镜像的命令如下：
```bash
docker rmi nginx:1.22.1
```

![](img/ee2dc99e5cd497b4818fe4c63f08797e_63.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_64.png)

---

![](img/ee2dc99e5cd497b4818fe4c63f08797e_66.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_68.png)

## 容器管理命令

![](img/ee2dc99e5cd497b4818fe4c63f08797e_70.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_72.png)

掌握了镜像管理后，我们就可以基于镜像来创建和管理容器了。容器是镜像的运行实例，是实际承载应用的环境。

### 创建并运行容器

![](img/ee2dc99e5cd497b4818fe4c63f08797e_74.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_76.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_78.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_80.png)

创建容器最常用的命令是 `docker run`。该命令包含许多选项，用于控制容器的行为。

以下是创建容器时几个关键的选项：
*   `-d`: 让容器在**后台运行**。
*   `-it`: 分配一个交互式终端，通常用于需要进入容器执行命令的场景。`-i` 表示保持标准输入打开，`-t` 表示分配一个伪终端。
*   `--name`: 为容器指定一个自定义名称，便于后续管理。

例如，创建一个名为 `centos-test` 并在后台运行的CentOS容器：
```bash
docker run -d --name=centos-test centos:7 /bin/bash
```

![](img/ee2dc99e5cd497b4818fe4c63f08797e_82.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_84.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_86.png)

### 查看容器状态

![](img/ee2dc99e5cd497b4818fe4c63f08797e_88.png)

使用 `docker ps` 命令可以查看**正在运行**的容器。
```bash
docker ps
```
若要查看所有容器（包括已停止的），需要加上 `-a` 选项。
```bash
docker ps -a
```

![](img/ee2dc99e5cd497b4818fe4c63f08797e_90.png)

### 进入运行中的容器

![](img/ee2dc99e5cd497b4818fe4c63f08797e_92.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_94.png)

对于在后台运行的容器，可以使用 `docker exec` 命令进入其内部执行命令。

![](img/ee2dc99e5cd497b4818fe4c63f08797e_96.png)

进入名为 `centos-test` 的容器，并使用bash终端：
```bash
docker exec -it centos-test /bin/bash
```
执行此命令后，命令行提示符会发生变化，表示已进入容器内部环境。

### 容器的启动、停止与重启

容器的生命周期管理与系统服务类似，有对应的启动、停止和重启命令。

以下是相关命令：
*   启动已停止的容器：`docker start [容器名/ID]`
*   停止运行中的容器：`docker stop [容器名/ID]`
*   重启容器：`docker restart [容器名/ID]`

### 删除容器

删除容器使用 `docker rm` 命令。**无法直接删除运行中的容器**，需要先停止容器，或使用 `-f` 选项强制删除。

![](img/ee2dc99e5cd497b4818fe4c63f08797e_98.png)

以下是删除容器的步骤：
1.  先停止容器：`docker stop centos-test`
2.  再删除容器：`docker rm centos-test`
或者使用强制删除（不推荐常规使用）：
```bash
docker rm -f centos-test
```

![](img/ee2dc99e5cd497b4818fe4c63f08797e_100.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_102.png)

### 端口映射

![](img/ee2dc99e5cd497b4818fe4c63f08797e_104.png)

对于像Nginx、Tomcat这类需要对外提供网络服务的容器，创建时必须进行**端口映射**，将容器内部的端口暴露给宿主机。

使用 `-p` 选项进行端口映射，格式为 `-p [宿主机端口]:[容器内部端口]`。

![](img/ee2dc99e5cd497b4818fe4c63f08797e_106.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_108.png)

例如，创建一个Nginx容器，将宿主机的 `8080` 端口映射到容器的 `80` 端口：
```bash
docker run -d --name=nginx-web -p 8080:80 nginx:1.22.1
```
创建后，即可通过访问 `http://宿主机IP:8080` 来访问容器内的Nginx服务。

### 容器随宿主机自启

![](img/ee2dc99e5cd497b4818fe4c63f08797e_110.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_112.png)

如果希望容器在宿主机重启后能自动启动，可以在创建时加入 `--restart=always` 策略。

![](img/ee2dc99e5cd497b4818fe4c63f08797e_114.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_116.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_118.png)

创建随宿主机自启的Nginx容器：
```bash
docker run -d --name=nginx-auto --restart=always -p 8080:80 nginx:1.22.1
```
对于已存在的容器，可以使用 `docker update` 命令来修改重启策略：
```bash
docker update --restart=always [容器名]
```

![](img/ee2dc99e5cd497b4818fe4c63f08797e_120.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_122.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_124.png)

### 查看容器日志

![](img/ee2dc99e5cd497b4818fe4c63f08797e_126.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_128.png)

使用 `docker logs` 命令可以查看容器的运行日志，这对于调试和监控应用非常有用。

![](img/ee2dc99e5cd497b4818fe4c63f08797e_130.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_132.png)

查看名为 `nginx-web` 的容器日志：
```bash
docker logs nginx-web
```

![](img/ee2dc99e5cd497b4818fe4c63f08797e_134.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_136.png)

---

![](img/ee2dc99e5cd497b4818fe4c63f08797e_138.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_139.png)

## 数据拷贝

![](img/ee2dc99e5cd497b4818fe4c63f08797e_141.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_143.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_145.png)

Docker提供了 `docker cp` 命令，用于在宿主机和容器之间相互拷贝文件。

![](img/ee2dc99e5cd497b4818fe4c63f08797e_147.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_149.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_150.png)

以下是两种常用的拷贝场景：
*   **从宿主机拷贝文件到容器**：
    ```bash
    docker cp /宿主机/路径/文件.txt 容器名:/容器内/路径/
    ```
*   **从容器拷贝文件到宿主机**：
    ```bash
    docker cp 容器名:/容器内/路径/文件.txt /宿主机/路径/
    ```

![](img/ee2dc99e5cd497b4818fe4c63f08797e_152.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_154.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_156.png)

---

![](img/ee2dc99e5cd497b4818fe4c63f08797e_158.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_160.png)

![](img/ee2dc99e5cd497b4818fe4c63f08797e_162.png)

本节课中我们一起学习了Docker镜像和容器管理的核心命令。从镜像的搜索、拉取、删除，到容器的创建、运行、状态管理、端口映射以及日志查看，这些是日常使用Docker的基础操作。理解并熟练运用这些命令，是迈向容器化运维的重要一步。下一节，我们将探讨更深入的Docker网络与数据卷管理。