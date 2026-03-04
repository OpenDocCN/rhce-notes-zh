# Linux运维课程：P99：Docker-2.docker容器部署

![](img/bd4a2fd74de6ce489c02560949b1d920_1.png)

![](img/bd4a2fd74de6ce489c02560949b1d920_3.png)

![](img/bd4a2fd74de6ce489c02560949b1d920_4.png)

在本节课中，我们将学习Docker容器的基本概念、应用场景，并完成Docker CE（社区版）的安装与镜像加速器的配置，为后续的容器操作打下基础。

## 概述

Docker是一种容器化技术，它允许开发者将应用及其依赖打包到一个可移植的容器中。本节课程将介绍Docker在企业持续集成/持续交付（CI/CD）工作流中的应用，并指导你完成Docker的安装与基础环境配置。

## Docker的应用场景

上一节我们介绍了Docker的基本概念，本节中我们来看看它在企业中的典型应用场景。Docker非常适合持续集成和持续交付的工作流程。

以下是Docker在CI/CD流程中的具体应用步骤：

![](img/bd4a2fd74de6ce489c02560949b1d920_6.png)

1.  **代码提交与镜像构建**：开发人员在本地编写代码，并使用Docker将代码封装成镜像。随后，将此镜像推送到代码仓库或镜像仓库。
2.  **自动化测试**：CI/CD工具（如Jenkins）从仓库拉取镜像，并将其部署到测试环境中执行自动化或手动测试。
3.  **问题反馈与修复**：如果测试发现镜像中的程序存在问题，则将问题反馈给开发人员。开发人员修复代码后，重新提交并构建新的镜像。
4.  **生产环境部署**：测试通过后，将最终确认的镜像部署到生产环境。由于应用及其环境已打包在镜像中，因此部署过程简单且一致。

这个流程的核心在于，从代码到测试再到生产，始终以**Docker镜像**作为交付物，确保了环境的一致性和部署的可靠性。

## Docker资源与版本选择

在开始安装前，了解可用的资源与版本区别很重要。

以下是Docker相关的主要资源：

*   **官方文档**：获取安装、配置和使用指南。
*   **Docker Hub**：官方的公共镜像仓库，可以下载和分享镜像。
*   **常见问题（FAQ）**：用于排查使用中遇到的问题。

Docker主要提供两个版本：

*   **Docker CE (Community Edition)**：社区版，免费使用，功能齐全，适合个人学习和大多数场景。
*   **Docker EE (Enterprise Edition)**：企业版，提供付费的技术支持、额外的安全和管理功能，主要面向企业客户。

对于学习和一般使用，我们选择安装 **Docker CE** 版本。

![](img/bd4a2fd74de6ce489c02560949b1d920_8.png)

![](img/bd4a2fd74de6ce489c02560949b1d920_9.png)

## Docker CE 安装步骤

现在，我们开始进行Docker CE在CentOS系统上的安装。我们将采用官方推荐的通过设置Yum仓库的方式进行安装，这是最简便的方法。

以下是安装Docker CE的具体步骤：

![](img/bd4a2fd74de6ce489c02560949b1d920_11.png)

![](img/bd4a2fd74de6ce489c02560949b1d920_13.png)

1.  **安装必要工具**：首先安装 `yum-utils` 包，它提供了 `yum-config-manager` 工具，用于管理Yum仓库。
    ```bash
    yum install -y yum-utils
    ```
2.  **添加Docker官方仓库**：使用上一步的工具添加Docker的官方Yum仓库。
    ```bash
    yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    ```
3.  **安装Docker引擎**：安装Docker CE包及其所有依赖。
    ```bash
    yum install -y docker-ce
    ```

![](img/bd4a2fd74de6ce489c02560949b1d920_15.png)

执行上述命令后，系统会自动安装 `docker-ce`（Docker社区版引擎）、`docker-ce-cli`（Docker命令行工具）、`containerd.io`（容器运行时）等必要的软件包。

![](img/bd4a2fd74de6ce489c02560949b1d920_17.png)

![](img/bd4a2fd74de6ce489c02560949b1d920_19.png)

安装完成后，需要启动Docker服务并设置开机自启：

![](img/bd4a2fd74de6ce489c02560949b1d920_21.png)

![](img/bd4a2fd74de6ce489c02560949b1d920_22.png)

![](img/bd4a2fd74de6ce489c02560949b1d920_24.png)

```bash
systemctl start docker
systemctl enable docker
```

![](img/bd4a2fd74de6ce489c02560949b1d920_26.png)

![](img/bd4a2fd74de6ce489c02560949b1d920_28.png)

可以通过 `systemctl status docker` 命令来验证Docker服务是否已正常运行。

![](img/bd4a2fd74de6ce489c02560949b1d920_30.png)

![](img/bd4a2fd74de6ce489c02560949b1d920_32.png)

![](img/bd4a2fd74de6ce489c02560949b1d920_34.png)

## 配置镜像加速器

![](img/bd4a2fd74de6ce489c02560949b1d920_35.png)

![](img/bd4a2fd74de6ce489c02560949b1d920_37.png)

![](img/bd4a2fd74de6ce489c02560949b1d920_39.png)

![](img/bd4a2fd74de6ce489c02560949b1d920_40.png)

默认情况下，Docker会从Docker Hub拉取镜像，但其服务器在国外，下载速度可能较慢。为了提高镜像下载速度，我们可以配置国内的镜像加速器。

以下是配置阿里云镜像加速器的步骤：

1.  **获取加速器地址**：登录阿里云控制台，进入“容器镜像服务”，在“镜像工具”下找到“镜像加速器”，获取为你账号分配的专属加速器地址。
2.  **配置Docker**：执行阿里云提供的配置命令。该命令会创建 `/etc/docker/daemon.json` 配置文件，并将你的加速器地址写入其中。
    ```bash
    # 请将以下命令中的 <your-accelerator-url> 替换为你从阿里云获取的实际地址
    sudo mkdir -p /etc/docker
    sudo tee /etc/docker/daemon.json <<-'EOF'
    {
      "registry-mirrors": ["<your-accelerator-url>"]
    }
    EOF
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    ```
3.  **验证配置**：配置完成后，可以检查 `/etc/docker/daemon.json` 文件内容，确认加速器地址已正确写入。

![](img/bd4a2fd74de6ce489c02560949b1d920_42.png)

配置镜像加速器后，后续拉取镜像的速度将得到显著提升。

![](img/bd4a2fd74de6ce489c02560949b1d920_44.png)

## 总结

![](img/bd4a2fd74de6ce489c02560949b1d920_46.png)

![](img/bd4a2fd74de6ce489c02560949b1d920_48.png)

本节课中我们一起学习了Docker的核心应用场景、资源与版本选择，并完成了Docker CE的安装与镜像加速器的配置。我们了解到Docker在现代化CI/CD流程中扮演着关键角色，能够实现从开发到生产环境的一致性。通过安装Docker CE和配置国内镜像加速器，我们已经搭建好了基础的容器运行环境。下节课我们将开始学习具体的Docker命令，来操作镜像和容器。