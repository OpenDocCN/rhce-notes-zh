# 华为云PaaS微服务治理技术 - P36：16.微服务部署-1 🚀

![](img/cdb29f33aa28c015fec58057a28ade33_1.png)

![](img/cdb29f33aa28c015fec58057a28ade33_3.png)

在本节课中，我们将学习如何为微服务项目搭建一个基础的部署环境。核心内容包括搭建私有Docker镜像仓库、配置Docker以支持远程访问，以及构建并推送第一个微服务镜像。这是将微服务应用容器化并准备运行的关键第一步。

![](img/cdb29f33aa28c015fec58057a28ade33_5.png)

![](img/cdb29f33aa28c015fec58057a28ade33_7.png)

![](img/cdb29f33aa28c015fec58057a28ade33_9.png)

## 搭建私有Docker镜像仓库 🏗️

![](img/cdb29f33aa28c015fec58057a28ade33_11.png)

![](img/cdb29f33aa28c015fec58057a28ade33_13.png)

首先，我们需要搭建一个私有Docker镜像仓库，用于存储我们自己构建的微服务镜像。由于我们使用的是新准备的虚拟机，因此需要从头创建这个仓库。

![](img/cdb29f33aa28c015fec58057a28ade33_15.png)

![](img/cdb29f33aa28c015fec58057a28ade33_17.png)

以下是搭建私有仓库的步骤：

![](img/cdb29f33aa28c015fec58057a28ade33_19.png)

![](img/cdb29f33aa28c015fec58057a28ade33_21.png)

1.  **启动私有仓库容器**：使用Docker命令启动一个Registry服务，该服务将运行在5000端口。
    ```bash
    docker run -d -p 5000:5000 --name registry registry:2
    ```
2.  **验证仓库**：在浏览器中访问 `http://<你的虚拟机IP>:5000/v2/_catalog`，如果看到 `{"repositories":[]}`，则表示私有仓库已成功启动且当前为空。
3.  **配置Docker信任私有仓库**：默认情况下，Docker不信任非HTTPS的私有仓库地址，我们需要将其添加到信任列表中。
    *   编辑Docker守护进程配置文件 `/etc/docker/daemon.json`（如果不存在则创建）。
    *   在文件中添加以下配置，将 `<你的虚拟机IP>` 替换为实际IP地址。
        ```json
        {
          "insecure-registries": ["<你的虚拟机IP>:5000"]
        }
        ```

![](img/cdb29f33aa28c015fec58057a28ade33_23.png)

![](img/cdb29f33aa28c015fec58057a28ade33_25.png)

![](img/cdb29f33aa28c015fec58057a28ade33_26.png)

## 配置Docker远程访问 🔧

![](img/cdb29f33aa28c015fec58057a28ade33_28.png)

![](img/cdb29f33aa28c015fec58057a28ade33_30.png)

上一节我们搭建了私有仓库，本节中我们来看看如何配置Docker以允许远程管理。这对于后续从开发机器操作服务器上的Docker非常有用。

![](img/cdb29f33aa28c015fec58057a28ade33_32.png)

以下是修改Docker配置的步骤：

1.  **修改服务配置文件**：编辑Docker的systemd服务配置文件 `/lib/systemd/system/docker.service`。
2.  **添加远程访问参数**：找到以 `ExecStart=` 开头的行，在其后添加 `-H tcp://0.0.0.0:2375` 参数。`0.0.0.0` 表示允许所有IP访问，如需限制可替换为特定IP。
    ```
    ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2375
    ```
3.  **使配置生效**：依次执行以下命令，重新加载配置并重启Docker服务。
    ```bash
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    ```
4.  **重启私有仓库**：由于Docker服务重启，私有仓库容器也需要重新启动。
    ```bash
    docker start registry
    ```

![](img/cdb29f33aa28c015fec58057a28ade33_34.png)

## 构建并推送微服务镜像 📦

环境准备就绪后，现在我们可以开始构建第一个微服务镜像并将其推送到私有仓库。

![](img/cdb29f33aa28c015fec58057a28ade33_36.png)

以下是构建和推送镜像的步骤：

1.  **准备微服务项目**：我们使用一个基础的微服务工程（例如 `base` 工程）。确保项目中已集成 `docker-maven-plugin` 插件，并正确配置了镜像名称和私有仓库地址。
    *   在项目的 `pom.xml` 中，配置插件将镜像推送到我们刚搭建的私有仓库（例如 `192.168.144.150:5000/base-service:1.0`）。
2.  **处理基础镜像依赖**：在构建过程中，如果Dockerfile中指定的基础镜像（如 `FROM jdk:1.8`）不存在，构建会失败。
    *   我们需要先在服务器上构建一个名为 `jdk:1.8` 的基础镜像。
    *   创建一个 `Dockerfile` 文件，内容包含从本地安装包安装JDK 8的指令。
    *   使用 `docker build -t jdk:1.8 .` 命令构建该基础镜像。
3.  **执行Maven构建**：在微服务项目根目录下，执行Maven命令来清理、打包项目，并触发Docker镜像的构建和推送。
    ```bash
    mvn clean package docker:build docker:push
    ```
4.  **验证推送结果**：命令执行成功后，可以再次访问私有仓库的目录接口（`http://<你的虚拟机IP>:5000/v2/_catalog`），查看是否包含了新推送的微服务镜像。同时，在服务器上执行 `docker images` 命令也能看到该镜像。

![](img/cdb29f33aa28c015fec58057a28ade33_38.png)

![](img/cdb29f33aa28c015fec58057a28ade33_40.png)

## 总结 📝

![](img/cdb29f33aa28c015fec58057a28ade33_42.png)

![](img/cdb29f33aa28c015fec58057a28ade33_43.png)

本节课中我们一起学习了微服务部署的初始准备工作。我们首先搭建了私有的Docker镜像仓库，然后配置了Docker守护进程以支持远程访问，最后通过一个示例微服务项目，完成了从代码到Docker镜像的构建，并将其成功推送到了私有仓库中。这些步骤为后续在服务器上编排和运行多个微服务容器奠定了坚实的基础。