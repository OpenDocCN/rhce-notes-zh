# Docker 运维教程：P100：Dockerfile 定制镜像 🐳

![](img/86dbd27af88633fe18d42c3634f5216b_1.png)

![](img/86dbd27af88633fe18d42c3634f5216b_3.png)

![](img/86dbd27af88633fe18d42c3634f5216b_5.png)

在本节课中，我们将学习如何使用 Dockerfile 来定制自己的 Docker 镜像。当从公共仓库下载的镜像无法满足特定需求时，我们可以通过编写 Dockerfile 来构建一个包含所需软件和配置的新镜像。

![](img/86dbd27af88633fe18d42c3634f5216b_7.png)

## 概述

Docker 镜像是容器运行的基础。通常我们从 Docker Hub 等仓库下载镜像，但有时这些镜像缺少我们需要的特定工具或配置。这时，我们可以通过两种方式定制镜像：
1.  使用 **Dockerfile** 从头构建新镜像。
2.  将已修改的容器提交为新的镜像。

本节课我们将重点学习第一种方法，即使用 Dockerfile 进行构建。

![](img/86dbd27af88633fe18d42c3634f5216b_9.png)

![](img/86dbd27af88633fe18d42c3634f5216b_11.png)

![](img/86dbd27af88633fe18d42c3634f5216b_13.png)

## Dockerfile 简介

Dockerfile 是一个文本文件，其中包含了一系列用于构建镜像的指令。每一条指令都会在镜像中创建一层，最终层层叠加，形成我们需要的镜像。

上一节我们介绍了镜像的基本概念，本节中我们来看看如何编写一个 Dockerfile。

![](img/86dbd27af88633fe18d42c3634f5216b_15.png)

以下是 Dockerfile 中一些常用指令的说明：

![](img/86dbd27af88633fe18d42c3634f5216b_17.png)

*   **`FROM`**：指定构建新镜像所基于的基础镜像。所有 Dockerfile 都必须以 `FROM` 指令开始。
    ```dockerfile
    FROM centos:7.9.2009
    ```
*   **`LABEL`**：为镜像添加元数据标签，例如维护者信息、版本描述等。此指令非必需。
    ```dockerfile
    LABEL maintainer="your-email@example.com"
    ```
*   **`RUN`**：在构建镜像时执行的命令，常用于安装软件包、创建目录等。
    ```dockerfile
    RUN yum install -y vim wget
    ```
*   **`CMD`**：指定容器启动时默认执行的命令。一个 Dockerfile 中只能有一条 `CMD` 指令。
    ```dockerfile
    CMD ["/bin/bash"]
    ```
*   **`WORKDIR`**：设置容器内部的工作目录。如果目录不存在，`WORKDIR` 会帮你创建。后续的 `RUN`、`CMD` 等指令会在此目录下执行。
    ```dockerfile
    WORKDIR /root
    ```
*   **`COPY`**：将构建上下文（执行 `docker build` 命令的目录）中的文件或目录复制到镜像内。
    ```dockerfile
    COPY index.html /usr/share/nginx/html/
    ```
*   **`ADD`**：与 `COPY` 功能类似，但 `ADD` 的源文件可以是 URL，并且如果源文件是压缩包，`ADD` 会自动解压。
    ```dockerfile
    ADD https://example.com/package.tar.gz /usr/src/
    ```
*   **`ENV`**：设置容器内的环境变量。
    ```dockerfile
    ENV NGINX_VERSION 1.22.1
    ```

![](img/86dbd27af88633fe18d42c3634f5216b_19.png)

> **注意**：`COPY` 和 `ADD` 的主要区别在于，`ADD` 支持从远程 URL 获取文件并自动解压压缩包，而 `COPY` 仅能从本地构建上下文复制文件。

![](img/86dbd27af88633fe18d42c3634f5216b_21.png)

![](img/86dbd27af88633fe18d42c3634f5216b_23.png)

## 实践：定制 CentOS 镜像

![](img/86dbd27af88633fe18d42c3634f5216b_25.png)

![](img/86dbd27af88633fe18d42c3634f5216b_27.png)

![](img/86dbd27af88633fe18d42c3634f5216b_29.png)

![](img/86dbd27af88633fe18d42c3634f5216b_31.png)

现在，我们通过一个实例来学习如何定制一个 CentOS 镜像。我们希望这个新镜像具备以下特性：
1.  基于官方的 CentOS 7.9 镜像。
2.  预装 `vim` 和 `wget` 工具。
3.  使用阿里云的 YUM 仓库。
4.  容器启动后自动进入 `/root` 目录并启动 Bash 环境。

![](img/86dbd27af88633fe18d42c3634f5216b_33.png)

![](img/86dbd27af88633fe18d42c3634f5216b_35.png)

以下是具体步骤：

![](img/86dbd27af88633fe18d42c3634f5216b_37.png)

![](img/86dbd27af88633fe18d42c3634f5216b_39.png)

![](img/86dbd27af88633fe18d42c3634f5216b_41.png)

1.  **创建构建目录和 Dockerfile 文件**
    首先，在宿主机上创建一个目录，并进入该目录。
    ```bash
    mkdir dockerfile-centos && cd dockerfile-centos
    ```
    然后，创建一个名为 `Dockerfile` 的文件（注意大小写）。
    ```bash
    vim Dockerfile
    ```

![](img/86dbd27af88633fe18d42c3634f5216b_43.png)

![](img/86dbd27af88633fe18d42c3634f5216b_45.png)

2.  **编写 Dockerfile 内容**
    将以下指令写入 `Dockerfile` 文件中。
    ```dockerfile
    # 指定基础镜像
    FROM centos:7.9.2009

    # 作者信息（可选）
    MAINTAINER your-email@example.com

    # 安装常用工具
    RUN yum install -y vim wget

    # 设置工作目录为 /root
    WORKDIR /root

    # 容器启动时执行 /bin/bash
    CMD ["/bin/bash"]
    ```
    保存并退出编辑器。

3.  **构建镜像**
    在 `Dockerfile` 所在目录执行构建命令。`-t` 参数用于指定新镜像的名称和标签，最后的 `.` 表示构建上下文为当前目录。
    ```bash
    docker build -t mycentos:7.9 .
    ```
    命令执行后，Docker 会逐条运行 `Dockerfile` 中的指令。看到 `Successfully built` 提示即表示构建成功。

![](img/86dbd27af88633fe18d42c3634f5216b_47.png)

![](img/86dbd27af88633fe18d42c3634f5216b_49.png)

![](img/86dbd27af88633fe18d42c3634f5216b_51.png)

![](img/86dbd27af88633fe18d42c3634f5216b_53.png)

4.  **验证新镜像**
    构建完成后，使用新镜像创建并运行一个容器。
    ```bash
    docker run -it --name mycentos-test mycentos:7.9
    ```
    进入容器后，可以验证 `vim` 和 `wget` 命令是否可用，并且当前目录是否为 `/root`。

![](img/86dbd27af88633fe18d42c3634f5216b_55.png)

![](img/86dbd27af88633fe18d42c3634f5216b_57.png)

## 实践：定制 Nginx 镜像

![](img/86dbd27af88633fe18d42c3634f5216b_59.png)

![](img/86dbd27af88633fe18d42c3634f5216b_61.png)

接下来，我们定制一个包含自定义网页的 Nginx 镜像。这在实际开发中非常常见，开发人员将应用程序打包进镜像，运维人员直接使用。

![](img/86dbd27af88633fe18d42c3634f5216b_63.png)

![](img/86dbd27af88633fe18d42c3634f5216b_65.png)

![](img/86dbd27af88633fe18d42c3634f5216b_67.png)

![](img/86dbd27af88633fe18d42c3634f5216b_69.png)

1.  **准备构建上下文**
    创建一个新目录，并准备你的网页文件（例如 `index.html`）。
    ```bash
    mkdir dockerfile-nginx && cd dockerfile-nginx
    echo “Hello, Custom Nginx!” > index.html
    ```

![](img/86dbd27af88633fe18d42c3634f5216b_71.png)

![](img/86dbd27af88633fe18d42c3634f5216b_73.png)

![](img/86dbd27af88633fe18d42c3634f5216b_75.png)

2.  **编写 Dockerfile**
    创建 `Dockerfile` 文件，内容如下：
    ```dockerfile
    # 基于官方 Nginx 镜像
    FROM nginx:1.22.1

    # 将宿主机当前目录下的 index.html 复制到镜像中 Nginx 的默认网页目录
    COPY index.html /usr/share/nginx/html/
    ```
    这个 Dockerfile 非常简单，它只是替换了 Nginx 默认的欢迎页面。

![](img/86dbd27af88633fe18d42c3634f5216b_77.png)

![](img/86dbd27af88633fe18d42c3634f5216b_79.png)

![](img/86dbd27af88633fe18d42c3634f5216b_81.png)

3.  **构建并运行**
    执行构建命令，并给镜像打上标签。
    ```bash
    docker build -t mynginx:v1 .
    ```
    使用新镜像运行一个容器，并将容器的 80 端口映射到宿主机的 81 端口。
    ```bash
    docker run -d --name mynginx-test -p 81:80 mynginx:v1
    ```
    现在，在浏览器中访问 `http://<你的宿主机IP>:81`，就能看到自定义的 “Hello, Custom Nginx!” 页面了。

## 总结

本节课我们一起学习了 Dockerfile 的基本语法和核心指令，并通过两个实践案例掌握了如何定制 CentOS 基础镜像和 Nginx 应用镜像。

![](img/86dbd27af88633fe18d42c3634f5216b_83.png)

关键点总结：
1.  **Dockerfile** 是用于自动化构建 Docker 镜像的脚本文件。
2.  核心指令包括 `FROM`（指定基础镜像）、`RUN`（执行命令）、`COPY`/`ADD`（复制文件）、`WORKDIR`（设置工作目录）和 `CMD`（设置启动命令）。
3.  构建命令为 `docker build -t <镜像名:标签> <构建上下文路径>`。
4.  在实际工作中，通常由开发人员编写 Dockerfile 来构建应用镜像，运维人员则更多地负责镜像的存储、分发和运行。

![](img/86dbd27af88633fe18d42c3634f5216b_85.png)

通过定制镜像，我们可以确保环境的一致性，并简化应用的部署流程。虽然运维人员不常编写复杂的 Dockerfile，但理解其原理对于管理和维护容器化环境至关重要。