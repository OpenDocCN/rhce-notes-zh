# Docker容器部署：P96：Docker安装与配置 🐳

![](img/b9eccc32284ddd62858f5d9addb842b4_1.png)

![](img/b9eccc32284ddd62858f5d9addb842b4_3.png)

![](img/b9eccc32284ddd62858f5d9addb842b4_4.png)

在本节课中，我们将学习Docker的基本概念、安装方法以及如何配置镜像加速器，为后续的容器操作打下基础。

---

## 概述

Docker是一种容器化技术，它允许开发者将应用及其依赖打包到一个可移植的容器中。本节课我们将完成Docker的安装和基本环境配置。

---

## Docker的应用场景

![](img/b9eccc32284ddd62858f5d9addb842b4_6.png)

上一节我们介绍了Docker的基本概念，本节中我们来看看它的一个核心应用场景：持续集成与持续交付（CI/CD）。

在CI/CD工作流程中，Docker扮演了关键角色。开发人员在本地编写代码后，可以使用Docker将代码封装成镜像。这个镜像可以被推送到测试环境进行自动化或手动测试。如果测试通过，相同的镜像可以无缝部署到生产环境。这种方式确保了环境的一致性，简化了部署流程。

以下是CI/CD流程中Docker的典型应用步骤：
1.  开发人员推送源代码到Git仓库（如GitLab）。
2.  Jenkins服务器拉取源代码。
3.  Jenkins使用Docker将源代码构建成镜像。
4.  将镜像部署到测试环境进行验证。
5.  测试通过后，将同一个镜像部署到生产环境。

这个过程体现了Docker在实现快速、可靠的应用交付方面的价值。

---

## Docker版本选择

Docker主要提供两个版本：
*   **Docker CE (Community Edition)**：社区版，免费使用，但无官方技术支持。
*   **Docker EE (Enterprise Edition)**：企业版，提供额外功能和技术支持，需要付费。

对于学习和测试，我们选择Docker CE版本即可。

![](img/b9eccc32284ddd62858f5d9addb842b4_8.png)

![](img/b9eccc32284ddd62858f5d9addb842b4_9.png)

---

## Docker安装步骤

![](img/b9eccc32284ddd62858f5d9addb842b4_11.png)

我们将根据官方文档，在CentOS系统上安装Docker CE。

![](img/b9eccc32284ddd62858f5d9addb842b4_13.png)

**1. 设置Docker仓库**
首先，我们需要安装一个工具来管理仓库。
```bash
yum install -y yum-utils
```
接着，添加Docker的官方仓库。
```bash
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

![](img/b9eccc32284ddd62858f5d9addb842b4_15.png)

![](img/b9eccc32284ddd62858f5d9addb842b4_17.png)

**2. 安装Docker引擎**
使用yum命令安装Docker CE及其相关组件。
```bash
yum install -y docker-ce
```
安装命令会自动处理所有依赖，包括`docker-ce-cli`、`containerd.io`等。

![](img/b9eccc32284ddd62858f5d9addb842b4_19.png)

![](img/b9eccc32284ddd62858f5d9addb842b4_21.png)

![](img/b9eccc32284ddd62858f5d9addb842b4_22.png)

**3. 启动并设置开机自启**
安装完成后，启动Docker服务并设置为开机自动启动。
```bash
systemctl start docker
systemctl enable docker
```

![](img/b9eccc32284ddd62858f5d9addb842b4_24.png)

![](img/b9eccc32284ddd62858f5d9addb842b4_26.png)

---

![](img/b9eccc32284ddd62858f5d9addb842b4_28.png)

![](img/b9eccc32284ddd62858f5d9addb842b4_30.png)

![](img/b9eccc32284ddd62858f5d9addb842b4_32.png)

## 配置镜像加速器

![](img/b9eccc32284ddd62858f5d9addb842b4_34.png)

![](img/b9eccc32284ddd62858f5d9addb842b4_35.png)

![](img/b9eccc32284ddd62858f5d9addb842b4_37.png)

![](img/b9eccc32284ddd62858f5d9addb842b4_39.png)

默认情况下，Docker从国外官方仓库拉取镜像，速度可能较慢。我们可以配置国内镜像加速器来提升下载速度。这里以阿里云加速器为例。

![](img/b9eccc32284ddd62858f5d9addb842b4_40.png)

**操作步骤如下：**
1.  登录阿里云控制台，搜索“容器镜像服务”。
2.  在“镜像工具”中找到“镜像加速器”。
3.  选择您的操作系统（如CentOS），页面上会显示专属的加速器地址和配置命令。
4.  在服务器上执行提供的配置命令。该命令会创建`/etc/docker/daemon.json`文件并写入加速器地址，然后重启Docker服务。

配置完成后，可以通过`docker info`命令检查Registry Mirrors是否已包含您配置的加速地址。

![](img/b9eccc32284ddd62858f5d9addb842b4_42.png)

---

![](img/b9eccc32284ddd62858f5d9addb842b4_44.png)

## 总结

![](img/b9eccc32284ddd62858f5d9addb842b4_46.png)

![](img/b9eccc32284ddd62858f5d9addb842b4_48.png)

本节课我们一起学习了Docker的核心应用场景、版本区别，并完成了在CentOS系统上安装Docker CE以及配置国内镜像加速器的全过程。这些是使用Docker的基础准备工作。下节课我们将开始学习具体的Docker命令，来操作镜像和容器。