# Docker入门与实践：P20：课程总结 📚

在本节课中，我们将对之前讲解的Docker核心知识进行系统性的回顾与总结。我们将梳理从基础概念到实际操作的完整知识脉络，帮助初学者巩固所学内容。

---

![](img/95d98b5ff5b02ba76ad6254c49f130ec_1.png)

## 核心概念回顾 🧠

![](img/95d98b5ff5b02ba76ad6254c49f130ec_3.png)

上一节我们介绍了Docker的多种应用部署，本节我们来回顾整个课程的核心概念。

![](img/95d98b5ff5b02ba76ad6254c49f130ec_5.png)

![](img/95d98b5ff5b02ba76ad6254c49f130ec_7.png)

Docker的核心是**镜像**与**容器**。镜像可以理解为一个**模板**，而容器则是根据这个模板创建并运行的一个**独立环境**。它们的关系可以用一个简单的公式表示：

**容器 = 镜像的运行时实例**

![](img/95d98b5ff5b02ba76ad6254c49f130ec_9.png)

我们可以在本地下载或构建多种镜像，例如MySQL、Tomcat、Nginx、Redis，甚至是自己编写的应用程序镜像。要让镜像运行起来，就需要根据它来创建容器。每个容器都可以视为一个独立的、环境隔离的服务器，这种隔离性使得使用容器搭建生产环境具有较高的安全性。

![](img/95d98b5ff5b02ba76ad6254c49f130ec_11.png)

![](img/95d98b5ff5b02ba76ad6254c49f130ec_13.png)

---

![](img/95d98b5ff5b02ba76ad6254c49f130ec_15.png)

## Docker的安装与配置 ⚙️

![](img/95d98b5ff5b02ba76ad6254c49f130ec_17.png)

![](img/95d98b5ff5b02ba76ad6254c49f130ec_19.png)

我们学习了如何在系统中安装Docker，并配置国内镜像加速器以提升下载速度。

![](img/95d98b5ff5b02ba76ad6254c49f130ec_21.png)

Docker本身是一个服务，我们可以使用 `systemctl` 命令来管理它。以下是常用的服务管理命令：

![](img/95d98b5ff5b02ba76ad6254c49f130ec_23.png)

*   **启动Docker服务**：`systemctl start docker`
*   **停止Docker服务**：`systemctl stop docker`
*   **重启Docker服务**：`systemctl restart docker`
*   **查看Docker服务状态**：`systemctl status docker`
*   **设置Docker开机自启**：`systemctl enable docker`

![](img/95d98b5ff5b02ba76ad6254c49f130ec_25.png)

![](img/95d98b5ff5b02ba76ad6254c49f130ec_27.png)

---

![](img/95d98b5ff5b02ba76ad6254c49f130ec_29.png)

## Docker核心命令 📋

命令操作是Docker学习的重点，主要分为镜像相关和容器相关两大类。

![](img/95d98b5ff5b02ba76ad6254c49f130ec_31.png)

### 镜像相关命令

![](img/95d98b5ff5b02ba76ad6254c49f130ec_33.png)

![](img/95d98b5ff5b02ba76ad6254c49f130ec_35.png)

以下是管理Docker镜像的常用命令：

![](img/95d98b5ff5b02ba76ad6254c49f130ec_37.png)

*   **查看本地镜像**：`docker images`
*   **搜索远程镜像**：`docker search [镜像名]`
*   **拉取远程镜像**：`docker pull [镜像名]:[标签]`
*   **删除本地镜像**：`docker rmi [镜像ID]`

### 容器相关命令

![](img/95d98b5ff5b02ba76ad6254c49f130ec_39.png)

以下是管理Docker容器的常用命令，这些命令需要重点掌握：

![](img/95d98b5ff5b02ba76ad6254c49f130ec_41.png)

![](img/95d98b5ff5b02ba76ad6254c49f130ec_43.png)

*   **查看容器**：
    *   `docker ps`：查看正在运行的容器。
    *   `docker ps -a`：查看所有容器（包括已停止的）。
    *   `docker ps -l`：查看最后一次创建的容器。
    *   `docker ps -f status=exited`：查看所有已停止的容器。
*   **创建与启动容器**：主要有两种方式。
    *   **交互式运行**：创建后会直接进入容器内部。如果使用 `exit` 命令退出，容器会随之停止。常用参数 `-it`。
    *   **守护式运行**：创建后后台运行，不直接进入。之后可以使用 `docker exec` 命令进入容器，退出时容器仍保持运行。常用参数 `-d`。
*   **其他常用参数与命令**：
    *   `--name`：为容器指定一个名称。
    *   `-v`：进行目录挂载（映射宿主机目录到容器内）。
    *   `-p`：进行端口映射（映射宿主机端口到容器端口）。
    *   `docker cp`：在宿主机和容器之间拷贝文件。
    *   `docker start/stop [容器名]`：启动/停止一个已存在的容器。
    *   `docker inspect [容器名]`：查看容器的详细信息（如IP地址）。
    *   `docker rm [容器名]`：删除一个容器。

![](img/95d98b5ff5b02ba76ad6254c49f130ec_45.png)

---

![](img/95d98b5ff5b02ba76ad6254c49f130ec_47.png)

## 应用部署实践 🚀

![](img/95d98b5ff5b02ba76ad6254c49f130ec_49.png)

![](img/95d98b5ff5b02ba76ad6254c49f130ec_51.png)

我们利用所学命令，实践部署了四种常用应用。

![](img/95d98b5ff5b02ba76ad6254c49f130ec_53.png)

以下是部署这四种应用的核心要点：

![](img/95d98b5ff5b02ba76ad6254c49f130ec_55.png)

![](img/95d98b5ff5b02ba76ad6254c49f130ec_57.png)

1.  **MySQL**：部署时需要**通过 `-e` 参数设置环境变量**，来指定root用户的初始密码。
2.  **Tomcat**：部署时通常结合 `-v` 参数，将web应用目录挂载到容器中。
3.  **Nginx**：部署时主要使用 `-p` 参数，将容器的80端口映射到宿主机的特定端口。
4.  **Redis**：部署同样使用 `-p` 参数，映射其默认的6379端口。

![](img/95d98b5ff5b02ba76ad6254c49f130ec_59.png)

---

![](img/95d98b5ff5b02ba76ad6254c49f130ec_61.png)

## 镜像的迁移与备份 💾

![](img/95d98b5ff5b02ba76ad6254c49f130ec_63.png)

为了便于镜像的传输和恢复，我们学习了三个关键命令。

![](img/95d98b5ff5b02ba76ad6254c49f130ec_65.png)

![](img/95d98b5ff5b02ba76ad6254c49f130ec_67.png)

以下是实现镜像迁移与备份的命令：

![](img/95d98b5ff5b02ba76ad6254c49f130ec_69.png)

*   **`docker commit`**：将一个**容器**的当前状态提交为一个新的**镜像**。
*   **`docker save`**：将一个**镜像**导出为 `.tar` 格式的压缩文件。
*   **`docker load`**：将一个 `.tar` 格式的压缩文件**导入并恢复为镜像**。

![](img/95d98b5ff5b02ba76ad6254c49f130ec_71.png)

---

![](img/95d98b5ff5b02ba76ad6254c49f130ec_73.png)

![](img/95d98b5ff5b02ba76ad6254c49f130ec_75.png)

## Dockerfile与自定义镜像 🛠️

![](img/95d98b5ff5b02ba76ad6254c49f130ec_77.png)

Dockerfile是一个文本文件，里面包含了一系列的指令，用于自动化地构建Docker镜像。

![](img/95d98b5ff5b02ba76ad6254c49f130ec_79.png)

我们学习了Dockerfile的常用指令，并用它构建了一个JDK 1.8的基础镜像。这个镜像将作为后续构建微服务应用的基础。常用指令包括：
*   `FROM`：指定基础镜像。
*   `MAINTAINER`：指定镜像维护者信息。
*   `RUN`：在构建过程中执行命令。
*   `ADD`/`COPY`：将文件从构建上下文复制到镜像中。
*   `WORKDIR`：设置容器内部的工作目录。

![](img/95d98b5ff5b02ba76ad6254c49f130ec_81.png)

---

![](img/95d98b5ff5b02ba76ad6254c49f130ec_83.png)

## Docker私有仓库搭建 🏠

![](img/95d98b5ff5b02ba76ad6254c49f130ec_85.png)

最后，我们学习了如何搭建企业内部的Docker私有仓库。

![](img/95d98b5ff5b02ba76ad6254c49f130ec_87.png)

私有仓库本身也是一个Docker镜像（如 `registry`）。我们拉取该镜像并运行成容器，就得到了一个私有的镜像仓库。搭建步骤的关键点如下：
1.  修改Docker守护进程配置文件 `/etc/docker/daemon.json`，添加 `insecure-registries` 配置项，让Docker信任我们的私有仓库地址。
2.  **修改配置后，必须重启Docker服务**才能使配置生效：`systemctl restart docker`。
3.  要向私有仓库上传镜像，需要先用 `docker tag` 命令为本地镜像打上符合私有仓库地址的标签，然后再使用 `docker push` 命令推送。

---

![](img/95d98b5ff5b02ba76ad6254c49f130ec_89.png)

![](img/95d98b5ff5b02ba76ad6254c49f130ec_91.png)

## 课程总结 📝

本节课中，我们一起系统回顾了Docker的核心知识体系。我们从**镜像与容器**的基本概念出发，学习了Docker的**安装配置**、丰富的**操作命令**，并实践了**应用部署**、**镜像管理**、**使用Dockerfile构建自定义镜像**以及**搭建私有仓库**。

![](img/95d98b5ff5b02ba76ad6254c49f130ec_93.png)

掌握这些内容，您已经具备了使用Docker进行基础应用容器化和环境管理的能力，为后续学习更复杂的容器编排技术打下了坚实的基础。