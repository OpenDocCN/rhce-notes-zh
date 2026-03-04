# Docker容器虚拟化平台：P4：Docker镜像制作与发布方法 🐳

在本节课中，我们将学习制作和发布Docker镜像的两种核心方法：`docker commit`和`docker build`。掌握这些技能，你将能够创建自定义的、包含特定服务的镜像，并实现快速部署。

---

![](img/86a5ef471dec8fbbef33fed788bf4ce4_1.png)

## Docker Commit：基于容器创建镜像 🔄

![](img/86a5ef471dec8fbbef33fed788bf4ce4_3.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_4.png)

上一节我们介绍了Docker的基本操作，本节中我们来看看如何通过`docker commit`命令快速制作镜像。这种方法基于一个正在运行或已停止的容器，将其当前状态保存为一个新的镜像。

**核心命令公式**：
```bash
docker commit [容器ID] [新镜像名称]
```

操作步骤如下：
1.  首先，基于一个基础镜像（如`centos`）启动一个容器并进入交互模式。
    ```bash
    docker run -it centos
    ```
2.  在容器内部安装你所需的服务，例如Apache HTTP服务器。
    ```bash
    yum install -y httpd
    ```
3.  退出容器后，使用`docker commit`命令，根据该容器的ID创建一个新的镜像。
    ```bash
    docker commit [你的容器ID] my_apache_image
    ```
4.  现在，你可以使用`docker images`查看新生成的镜像，并基于它启动新的容器实例。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_6.png)

虽然`docker commit`方法简单直接，但它存在局限性，例如无法自动化构建过程，且创建的服务通常不会自动启动。因此，我们更推荐使用下面介绍的`docker build`方法。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_8.png)

---

## Docker Build：使用Dockerfile自动化构建镜像 ⚙️

![](img/86a5ef471dec8fbbef33fed788bf4ce4_10.png)

上一节我们介绍了手动创建镜像的方法，本节中我们来看看如何通过编写`Dockerfile`文件，实现镜像构建的自动化。这类似于软件开发中的`Makefile`。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_12.png)

**为什么要使用Dockerfile？**
想象一个场景：需要在两分钟内创建200个新的Web服务器。传统方式安装系统和软件需要20分钟，这显然无法满足需求。如果提前制作好一个包含Web服务的Docker镜像，那么启动一个新实例只需几秒钟。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_14.png)

以下是创建一个包含Apache服务并设置开机启动的镜像的完整步骤。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_16.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_18.png)

### 第一步：准备Dockerfile及相关文件

首先，创建一个工作目录并编写`Dockerfile`文件。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_20.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_22.png)

**Dockerfile 内容示例**：
```dockerfile
# 基于centos镜像构建
FROM centos
# 维护者信息
MAINTAINER Xueshen Tech
# 在镜像中执行命令：安装Apache
RUN yum install -y httpd
# 将本地的启动脚本添加到镜像的指定路径
ADD start.sh /usr/local/bin/start.sh
# 将本地的网页文件添加到Apache默认目录
ADD index.html /var/www/html/
# 设置启动容器时自动执行的命令
CMD ["/usr/local/bin/start.sh"]
```

**文件说明**：
*   `FROM`：指定基础镜像。
*   `RUN`：在构建过程中执行的命令，例如安装软件。
*   `ADD`：将本地文件复制到镜像内的指定路径。
*   `CMD`：指定容器启动后默认执行的命令，一个Dockerfile中应只有一条有效CMD指令。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_24.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_26.png)

### 第二步：创建辅助脚本和网页

以下是需要创建的两个辅助文件内容。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_28.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_30.png)

**启动脚本 (`start.sh`)**：
```bash
#!/bin/bash
# 启动Apache服务
/usr/sbin/httpd -D FOREGROUND
```
创建后赋予执行权限：`chmod +x start.sh`

![](img/86a5ef471dec8fbbef33fed788bf4ce4_32.png)

**首页文件 (`index.html`)**：
```html
<h1>Hello from Docker Apache Container!</h1>
```

![](img/86a5ef471dec8fbbef33fed788bf4ce4_34.png)

### 第三步：执行构建命令

![](img/86a5ef471dec8fbbef33fed788bf4ce4_36.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_38.png)

当`Dockerfile`、`start.sh`和`index.html`三个文件在同一目录下准备就绪后，即可执行构建。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_40.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_41.png)

**构建命令公式**：
```bash
docker build -t [镜像名称:标签] [Dockerfile所在路径]
```

![](img/86a5ef471dec8fbbef33fed788bf4ce4_43.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_45.png)

例如，在当前目录构建一个名为`centos_httpd`的镜像：
```bash
docker build -t centos_httpd .
```
命令执行后，Docker会逐步执行`Dockerfile`中的指令，最终生成一个新的镜像。你可以使用`docker images`命令查看它。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_47.png)

---

![](img/86a5ef471dec8fbbef33fed788bf4ce4_49.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_50.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_52.png)

## 镜像的导出、导入与发布 📤

![](img/86a5ef471dec8fbbef33fed788bf4ce4_54.png)

镜像构建完成后，你可能需要将其保存到本地或分享给他人。以下是几种常见的方法。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_56.png)

### 方法一：本地保存与加载（Save/Load）

![](img/86a5ef471dec8fbbef33fed788bf4ce4_58.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_60.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_62.png)

这种方法适用于离线环境或快速迁移。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_64.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_66.png)

**导出镜像到本地文件**：
```bash
docker save -o centos_httpd.tar centos_httpd
```

![](img/86a5ef471dec8fbbef33fed788bf4ce4_68.png)

**从本地文件加载镜像**：
```bash
docker load -i centos_httpd.tar
```

![](img/86a5ef471dec8fbbef33fed788bf4ce4_70.png)

### 方法二：推送到镜像仓库（Push/Pull）

这是团队协作和持续集成的标准方式，通常使用Docker Hub或阿里云等容器镜像服务。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_72.png)

操作流程概述：
1.  注册一个镜像仓库账号（如Docker Hub或阿里云容器镜像服务）。
2.  在本地登录到该仓库。
    ```bash
    docker login -u [用户名] -p [密码] [仓库地址]
    ```
3.  为本地镜像打上符合仓库规范的标签。
    ```bash
    docker tag centos_httpd [仓库地址]/[命名空间]/centos_httpd
    ```
4.  将镜像推送到远程仓库。
    ```bash
    docker push [仓库地址]/[命名空间]/centos_httpd
    ```
5.  其他人或服务器可以通过`docker pull`命令获取该镜像。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_74.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_76.png)

对于国内用户，使用阿里云等国内仓库通常能获得更快的速度。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_78.png)

---

![](img/86a5ef471dec8fbbef33fed788bf4ce4_80.png)

## 总结 🎯

![](img/86a5ef471dec8fbbef33fed788bf4ce4_82.png)

![](img/86a5ef471dec8fbbef33fed788bf4ce4_84.png)

本节课中我们一起学习了Docker镜像制作与发布的核心方法：
1.  **`docker commit`**：快速将容器的当前状态保存为镜像，适合简单场景和临时测试。
2.  **`docker build` + `Dockerfile`**：通过编写声明式文件自动化构建镜像，可重复、可维护，是生产环境的最佳实践。我们详细演练了创建一个带Apache服务的镜像的完整步骤。
3.  **镜像分发**：掌握了通过`save/load`进行本地导出导入，以及通过`push/pull`利用远程仓库进行分享和部署的方法。

![](img/86a5ef471dec8fbbef33fed788bf4ce4_86.png)

掌握这些技能，你就能封装任意应用环境，实现服务的快速、一致化部署，这是容器化技术的核心优势之一。下一节，我们将学习如何通过端口映射，让容器内的服务能够被外部网络访问。