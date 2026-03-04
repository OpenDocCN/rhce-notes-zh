# Linux运维全套培训课程：P97：Docker-2.docker容器部署 🐳

![](img/2f20444ae254672c9c0580ca12d1bc5d_1.png)

![](img/2f20444ae254672c9c0580ca12d1bc5d_3.png)

![](img/2f20444ae254672c9c0580ca12d1bc5d_4.png)

在本节课中，我们将学习Docker的基本概念、安装方法以及如何配置镜像加速器，为后续深入学习Docker命令和容器部署打下基础。

## 概述

Docker是一种容器化技术，它允许开发者将应用程序及其依赖打包到一个可移植的容器中。本节课程将介绍Docker的核心概念、在企业中的应用场景，并指导您完成Docker的安装与基础配置。

---

## 容器的应用场景

上一节我们介绍了Docker的基本概念，本节中我们来看看Docker在企业中的具体应用场景。容器技术非常适合持续集成和持续交付的工作流程。

以下是容器在CI/CD流程中的应用步骤：

![](img/2f20444ae254672c9c0580ca12d1bc5d_6.png)

1.  **开发人员编写代码**：开发人员在本地编写代码。
2.  **封装为镜像**：开发人员使用Docker将自己的代码封装成镜像。
3.  **推送至测试环境**：将封装好的镜像推送到测试环境。
4.  **执行测试**：在测试环境中执行自动或手动测试。
5.  **问题修复与重新部署**：如果测试发现问题，则修复代码并重新构建、测试镜像。
6.  **部署至生产环境**：测试通过后，将最终镜像部署到生产环境。

这个过程可以概括为：从推送代码到构建代码属于**持续集成**，将代码发布到测试或生产环境属于**持续交付**。

---

## Docker版本与资源

Docker主要分为两个版本：

*   **Docker CE**：社区版，免费使用，但无官方技术支持。
*   **Docker EE**：企业版，收费，提供更多功能及官方技术支持。

对于学习和个人使用，社区版已足够。以下是一些有用的Docker资源：

*   **官方文档**：获取安装和使用指南。
*   **Docker Hub**：官方的镜像仓库，用于查找和分享镜像。
*   **常见问题**：遇到问题时可以参考。

---

![](img/2f20444ae254672c9c0580ca12d1bc5d_8.png)

## Docker安装指南

![](img/2f20444ae254672c9c0580ca12d1bc5d_9.png)

接下来，我们将在CentOS系统上安装Docker社区版。安装过程主要分为设置仓库和安装软件包两步。

以下是安装步骤：

![](img/2f20444ae254672c9c0580ca12d1bc5d_11.png)

1.  **安装依赖包**：首先安装 `yum-utils` 包，它提供了 `yum-config-manager` 工具。
    ```bash
    yum install -y yum-utils
    ```
2.  **设置Docker仓库**：使用 `yum-config-manager` 添加Docker的官方仓库。
    ```bash
    yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    ```
3.  **安装Docker引擎**：安装Docker社区版及其相关组件。
    ```bash
    yum install -y docker-ce
    ```
    执行此命令后，`docker-ce-cli`、`containerd.io` 等依赖包会自动安装。

![](img/2f20444ae254672c9c0580ca12d1bc5d_13.png)

安装完成后，可以启动Docker服务并设置为开机自启：
```bash
systemctl start docker
systemctl enable docker
```

![](img/2f20444ae254672c9c0580ca12d1bc5d_15.png)

若要卸载Docker，可以使用以下命令：
```bash
yum remove -y docker-ce
rm -rf /var/lib/docker
```

![](img/2f20444ae254672c9c0580ca12d1bc5d_17.png)

![](img/2f20444ae254672c9c0580ca12d1bc5d_19.png)

---

![](img/2f20444ae254672c9c0580ca12d1bc5d_21.png)

![](img/2f20444ae254672c9c0580ca12d1bc5d_22.png)

![](img/2f20444ae254672c9c0580ca12d1bc5d_24.png)

## 配置镜像加速器

![](img/2f20444ae254672c9c0580ca12d1bc5d_26.png)

默认情况下，Docker从国外官方仓库拉取镜像，速度可能较慢。配置国内镜像加速器可以显著提升下载速度。此步骤非必需，但推荐进行。

![](img/2f20444ae254672c9c0580ca12d1bc5d_28.png)

![](img/2f20444ae254672c9c0580ca12d1bc5d_30.png)

![](img/2f20444ae254672c9c0580ca12d1bc5d_32.png)

我们将以阿里云镜像加速器为例进行配置。

![](img/2f20444ae254672c9c0580ca12d1bc5d_34.png)

![](img/2f20444ae254672c9c0580ca12d1bc5d_35.png)

![](img/2f20444ae254672c9c0580ca12d1bc5d_37.png)

以下是配置步骤：

![](img/2f20444ae254672c9c0580ca12d1bc5d_39.png)

![](img/2f20444ae254672c9c0580ca12d1bc5d_40.png)

1.  登录阿里云控制台，搜索“容器镜像服务”。
2.  在“镜像工具”下找到“镜像加速器”。
3.  选择您的操作系统（如CentOS），页面上会显示专属的加速器地址和配置命令。
4.  复制提供的配置命令，在服务器上执行。

该命令会创建 `/etc/docker/daemon.json` 文件，并写入您的加速器地址，然后重启Docker服务使配置生效。

![](img/2f20444ae254672c9c0580ca12d1bc5d_42.png)

配置完成后，您可以检查Docker服务状态，确认其运行正常。

---

![](img/2f20444ae254672c9c0580ca12d1bc5d_44.png)

## 总结

![](img/2f20444ae254672c9c0580ca12d1bc5d_46.png)

![](img/2f20444ae254672c9c0580ca12d1bc5d_48.png)

本节课中我们一起学习了Docker的核心概念及其在CI/CD流程中的应用，成功在CentOS系统上完成了Docker社区版的安装，并配置了阿里云镜像加速器以优化镜像拉取速度。这些是使用Docker的基础准备工作，下节课我们将开始学习具体的Docker命令。