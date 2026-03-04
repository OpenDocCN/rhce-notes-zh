# Docker入门教程：P103：Docker-6.dockerfile定制镜像 🐳

![](img/378738c3729bcd170cd87bba6158cb1b_1.png)

在本节课中，我们将学习如何使用 Dockerfile 来定制自己的 Docker 镜像。我们将了解 Dockerfile 的基本语法、常用指令，并通过实际案例演示如何构建一个包含自定义内容的镜像。

![](img/378738c3729bcd170cd87bba6158cb1b_3.png)

## 概述

默认情况下，我们从 Docker 镜像仓库下载的镜像可能无法完全满足特定需求。此时，我们可以通过两种方式修改镜像：
1.  使用 **Dockerfile** 重新构建一个新镜像。
2.  将已创建的容器转为镜像。

本节我们将重点学习第一种方法：使用 Dockerfile 定制镜像。

![](img/378738c3729bcd170cd87bba6158cb1b_5.png)

![](img/378738c3729bcd170cd87bba6158cb1b_7.png)

## Dockerfile 是什么？

Dockerfile 是一个文本文件，其中包含了一系列用于构建镜像的指令。每一条指令都会构建镜像的一层，最终所有层叠加起来形成完整的镜像。

例如，我们希望基于 Nginx 官方镜像，构建一个自带特定网页的新镜像，或者基于 CentOS 镜像，构建一个预装了常用工具的新镜像，都可以通过编写 Dockerfile 来实现。

## Dockerfile 常用指令

![](img/378738c3729bcd170cd87bba6158cb1b_9.png)

以下是构建镜像时常用的一些 Dockerfile 指令及其说明：

![](img/378738c3729bcd170cd87bba6158cb1b_11.png)

*   **`FROM`**: 指定基础镜像。所有镜像的构建都必须从一个基础镜像开始。
    *   **格式**: `FROM <image>[:<tag>]`
*   **`LABEL`**: 为镜像添加元数据标签，例如维护者信息。此指令非必需。
    *   **格式**: `LABEL <key>=<value> <key>=<value> ...`
*   **`RUN`**: 在构建镜像时执行的命令，常用于安装软件包、创建目录等。
    *   **格式**: `RUN <command>` 或 `RUN ["executable", "param1", "param2"]`
*   **`COPY`**: 将构建上下文（主机）中的文件或目录复制到镜像内。
    *   **格式**: `COPY <源路径> <目标路径>`
*   **`ADD`**: 与 `COPY` 功能类似，但 `ADD` 的源路径可以是 URL，并且会自动解压 tar 压缩包。
    *   **格式**: `ADD <源路径> <目标路径>`
*   **`WORKDIR`**: 指定容器启动后的默认工作目录。如果目录不存在，则会自动创建。
    *   **格式**: `WORKDIR <工作目录路径>`
*   **`CMD`**: 指定容器启动时默认执行的命令。一个 Dockerfile 中只能有一条 `CMD` 指令。
    *   **格式**: `CMD ["executable","param1","param2"]` 或 `CMD command param1 param2`
*   **`ENV`**: 设置容器内的环境变量。
    *   **格式**: `ENV <key> <value>` 或 `ENV <key>=<value> ...`

![](img/378738c3729bcd170cd87bba6158cb1b_13.png)

![](img/378738c3729bcd170cd87bba6158cb1b_15.png)

> **`COPY` 与 `ADD` 的区别**：`COPY` 只能从构建上下文的主机复制文件，而 `ADD` 可以从远程 URL 获取文件并添加到镜像中。这是面试中可能被问到的细节。

![](img/378738c3729bcd170cd87bba6158cb1b_17.png)

## 实践：定制 CentOS 镜像

![](img/378738c3729bcd170cd87bba6158cb1b_19.png)

![](img/378738c3729bcd170cd87bba6158cb1b_21.png)

![](img/378738c3729bcd170cd87bba6158cb1b_23.png)

上一节我们介绍了 Dockerfile 的基本指令，本节我们通过一个实际案例来学习如何构建镜像。

![](img/378738c3729bcd170cd87bba6158cb1b_25.png)

![](img/378738c3729bcd170cd87bba6158cb1b_27.png)

默认的 CentOS 镜像不包含 `vim` 等常用工具，且登录后默认在根目录 `/`。我们希望构建一个新镜像，使其：
1.  预装 `vim` 和 `wget` 工具。
2.  使用阿里云的 yum 仓库。
3.  用户首次进入容器时，自动位于 `/root` 目录。
4.  容器启动时自动进入 `bash` 环境。

![](img/378738c3729bcd170cd87bba6158cb1b_29.png)

![](img/378738c3729bcd170cd87bba6158cb1b_31.png)

以下是构建步骤：

![](img/378738c3729bcd170cd87bba6158cb1b_33.png)

1.  **创建构建目录和 Dockerfile 文件**
    ```bash
    mkdir dockerfile-centos
    cd dockerfile-centos
    vim Dockerfile
    ```

2.  **编写 Dockerfile 内容**
    ```dockerfile
    # 指定基础镜像
    FROM centos:7.9.2009
    
    # 作者信息（可选）
    LABEL maintainer="yourname@example.com"
    
    # 安装软件包并更换yum源为阿里云（示例，实际需替换为正确的repo文件URL）
    RUN yum install -y vim wget
    RUN wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
    RUN wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
    
    # 指定工作目录
    WORKDIR /root
    
    # 容器启动时执行的命令
    CMD ["/bin/bash"]
    ```

![](img/378738c3729bcd170cd87bba6158cb1b_35.png)

![](img/378738c3729bcd170cd87bba6158cb1b_37.png)

3.  **构建镜像**
    在 `Dockerfile` 所在目录执行构建命令：
    ```bash
    docker build -f ./Dockerfile -t mycentos:7.9 .
    ```
    *   `-f`: 指定 Dockerfile 文件路径。
    *   `-t`: 为构建的镜像指定名称和标签。
    *   `.`: 表示当前目录为构建上下文，Docker 会将此目录下的文件发送给 Docker 引擎。

![](img/378738c3729bcd170cd87bba6158cb1b_39.png)

![](img/378738c3729bcd170cd87bba6158cb1b_41.png)

![](img/378738c3729bcd170cd87bba6158cb1b_43.png)

4.  **验证新镜像**
    构建成功后，运行新镜像创建的容器：
    ```bash
    docker run -it --name mycentos-test mycentos:7.9
    ```
    进入容器后，可以检查 `vim` 命令是否可用，以及当前目录是否为 `/root`。

![](img/378738c3729bcd170cd87bba6158cb1b_45.png)

## 实践：定制 Nginx 镜像

![](img/378738c3729bcd170cd87bba6158cb1b_47.png)

接下来，我们学习如何构建一个自带静态网页的 Nginx 镜像。

![](img/378738c3729bcd170cd87bba6158cb1b_49.png)

![](img/378738c3729bcd170cd87bba6158cb1b_51.png)

![](img/378738c3729bcd170cd87bba6158cb1b_53.png)

![](img/378738c3729bcd170cd87bba6158cb1b_55.png)

1.  **准备网页文件**
    在主机上创建一个目录，并放入你的网页文件（例如 `index.html`）。
    ```bash
    mkdir dockerfile-nginx
    cd dockerfile-nginx
    # 将你的网页文件放在此目录下
    echo "<h1>Hello from Custom Nginx!</h1>" > index.html
    ```

![](img/378738c3729bcd170cd87bba6158cb1b_57.png)

![](img/378738c3729bcd170cd87bba6158cb1b_59.png)

2.  **编写 Dockerfile**
    ```dockerfile
    # 基于官方Nginx镜像
    FROM nginx:1.22.1
    
    # 将主机当前目录下的所有文件复制到镜像的Nginx默认网页目录
    ADD . /usr/share/nginx/html
    ```

![](img/378738c3729bcd170cd87bba6158cb1b_61.png)

![](img/378738c3729bcd170cd87bba6158cb1b_62.png)

3.  **构建并运行镜像**
    ```bash
    docker build -f ./Dockerfile -t mynginx:v1 .
    docker run -d --name nginx-test -p 81:80 mynginx:v1
    ```
    访问 `http://<你的主机IP>:81`，即可看到自定义的网页内容。

## 镜像与容器的转换

除了使用 Dockerfile，还可以通过 `docker commit` 命令将正在运行的容器保存为一个新镜像。但这通常用于临时保存状态，**不推荐**作为构建镜像的标准方式，因为过程不透明、难以重复。
```bash
docker commit [容器ID] [新镜像名:标签]
```

![](img/378738c3729bcd170cd87bba6158cb1b_64.png)

## 总结

![](img/378738c3729bcd170cd87bba6158cb1b_66.png)

本节课我们一起学习了 Docker 镜像定制的核心方法。我们首先了解了 Dockerfile 的作用和基本语法，然后通过定制 CentOS 镜像和 Nginx 镜像两个案例，实践了 `FROM`、`RUN`、`ADD`、`WORKDIR`、`CMD` 等关键指令的使用。最后，我们简要了解了容器转为镜像的命令。在实际工作中，镜像通常由开发人员构建，运维人员更侧重于镜像的部署和运行管理，但理解镜像的构建原理对于排查问题和管理容器化应用至关重要。