# DevOps入门：P92：GitLab快速入门 🚀

![](img/e13654724f63a453667df0627bc0c18e_1.png)

![](img/e13654724f63a453667df0627bc0c18e_3.png)

## 概述
在本节课中，我们将学习如何快速部署和使用GitLab，这是一个在企业内部广泛使用的代码托管平台。我们将从Git工具的基础操作开始，逐步完成GitLab的安装、配置，并最终通过Web界面管理我们的代码仓库。

![](img/e13654724f63a453667df0627bc0c18e_5.png)

![](img/e13654724f63a453667df0627bc0c18e_7.png)

---

![](img/e13654724f63a453667df0627bc0c18e_9.png)

![](img/e13654724f63a453667df0627bc0c18e_11.png)

![](img/e13654724f63a453667df0627bc0c18e_13.png)

## Git工具基础

上一节我们了解了DevOps的基本概念，本节中我们来看看实现代码托管的第一个核心工具——Git。

Git是一款开源的分布式版本控制系统，由Linus Torvalds为管理Linux内核开发而创建。在企业开发流程中，开发人员使用Git将代码提交到仓库，而后续的集成服务器（如Jenkins）也会使用Git从仓库拉取代码进行构建和发布。

### 安装与初始配置
首先，我们需要在Linux系统上安装Git。

```bash
yum install -y git
```

![](img/e13654724f63a453667df0627bc0c18e_15.png)

安装完成后，使用Git前必须配置用户信息，这用于标识代码的提交者。

![](img/e13654724f63a453667df0627bc0c18e_17.png)

![](img/e13654724f63a453667df0627bc0c18e_19.png)

以下是配置用户和邮箱的命令：
```bash
git config --global user.name "your_username"
git config --global user.email "your_email@example.com"
```

![](img/e13654724f63a453667df0627bc0c18e_21.png)

![](img/e13654724f63a453667df0627bc0c18e_23.png)

![](img/e13654724f63a453667df0627bc0c18e_25.png)

为了获得更好的命令行体验，可以启用颜色输出：
```bash
git config --global color.ui auto
```

![](img/e13654724f63a453667df0627bc0c18e_27.png)

![](img/e13654724f63a453667df0627bc0c18e_29.png)

![](img/e13654724f63a453667df0627bc0c18e_31.png)

### Git工作流程
Git的工作流程涉及几个核心概念：**工作目录**、**暂存区**、**本地仓库**和**远程仓库**。

![](img/e13654724f63a453667df0627bc0c18e_33.png)

1.  **初始化仓库**：将一个普通目录初始化为Git仓库。
    ```bash
    git init
    ```
    此命令会在当前目录下创建一个隐藏的 `.git` 目录，这就是本地仓库。

![](img/e13654724f63a453667df0627bc0c18e_35.png)

![](img/e13654724f63a453667df0627bc0c18e_37.png)

2.  **添加文件到暂存区**：将工作目录中的文件变更添加到暂存区。
    ```bash
    git add .
    ```
    这里的 `.` 代表添加当前目录下的所有文件。

![](img/e13654724f63a453667df0627bc0c18e_39.png)

![](img/e13654724f63a453667df0627bc0c18e_41.png)

3.  **查看仓库状态**：可以随时查看哪些文件已被暂存或修改。
    ```bash
    git status
    ```

![](img/e13654724f63a453667df0627bc0c18e_43.png)

4.  **提交到本地仓库**：将暂存区的内容正式保存到本地仓库，并附上提交说明。
    ```bash
    git commit -m "提交信息说明"
    ```

![](img/e13654724f63a453667df0627bc0c18e_45.png)

![](img/e13654724f63a453667df0627bc0c18e_47.png)

---

![](img/e13654724f63a453667df0627bc0c18e_49.png)

![](img/e13654724f63a453667df0627bc0c18e_51.png)

![](img/e13654724f63a453667df0627bc0c18e_53.png)

## 部署GitLab代码托管平台

![](img/e13654724f63a453667df0627bc0c18e_55.png)

![](img/e13654724f63a453667df0627bc0c18e_57.png)

了解了Git的基本操作后，我们需要一个像GitHub一样的平台来集中存储和管理代码，这就是GitLab。

### 环境准备与安装
GitLab适合在团队内部使用。我们将在一台CentOS虚拟机上部署它，建议配置为：2核CPU、4G内存、50G磁盘。

从清华大学开源软件镜像站下载GitLab社区版（CE）的RPM安装包。安装过程会自动解决依赖。

![](img/e13654724f63a453667df0627bc0c18e_59.png)

![](img/e13654724f63a453667df0627bc0c18e_61.png)

```bash
yum install -y /path/to/gitlab-ce-xxx.rpm
```

![](img/e13654724f63a453667df0627bc0c18e_63.png)

![](img/e13654724f63a453667df0627bc0c18e_65.png)

### 配置与启动
安装完成后，需要配置GitLab实例的访问地址。

![](img/e13654724f63a453667df0627bc0c18e_67.png)

![](img/e13654724f63a453667df0627bc0c18e_69.png)

1.  编辑GitLab的主配置文件：
    ```bash
    vim /etc/gitlab/gitlab.rb
    ```
2.  找到并修改 `external_url` 配置项，将其设置为你的域名或IP地址，例如：
    ```
    external_url 'http://web.gitlab.com'
    ```
3.  如果使用了域名，需要在客户端（如你的Windows电脑）的 `hosts` 文件（`C:\Windows\System32\drivers\etc\hosts`）中添加解析：
    ```
    192.168.0.14 web.gitlab.com
    ```
4.  重新配置并启动GitLab服务（此过程较慢）：
    ```bash
    gitlab-ctl reconfigure
    ```

![](img/e13654724f63a453667df0627bc0c18e_71.png)

![](img/e13654724f63a453667df0627bc0c18e_73.png)

### GitLab组件与服务管理
GitLab由多个组件构成，主要包括：
*   **Web服务**：由Nginx提供。
*   **应用核心**：Ruby on Rails应用。
*   **数据库**：默认使用PostgreSQL存储数据。
*   **缓存**：使用Redis。

![](img/e13654724f63a453667df0627bc0c18e_75.png)

以下是GitLab服务的常用管理命令：
*   查看所有服务状态：`gitlab-ctl status`
*   启动所有服务：`gitlab-ctl start`
*   停止所有服务：`gitlab-ctl stop`
*   重启所有服务：`gitlab-ctl restart`

![](img/e13654724f63a453667df0627bc0c18e_77.png)

![](img/e13654724f63a453667df0627bc0c18e_79.png)

---

![](img/e13654724f63a453667df0627bc0c18e_81.png)

![](img/e13654724f63a453667df0627bc0c18e_83.png)

![](img/e13654724f63a453667df0627bc0c18e_85.png)

## 访问与使用GitLab

服务启动后，我们就可以通过浏览器访问GitLab的Web界面了。

### 首次登录
在浏览器中访问你配置的地址（如 `http://web.gitlab.com`）。

*   **用户名**：`root`
*   **初始密码**：存放在服务器上的 `/etc/gitlab/initial_root_password` 文件中，使用以下命令查看：
    ```bash
    cat /etc/gitlab/initial_root_password
    ```
    登录后请立即修改密码。

### 设置中文界面
GitLab支持多语言。登录后，点击右上角用户头像 -> **Preferences**（偏好设置），在页面中找到 **Localization**（本地化）区域，将 **Language**（语言）改为“简体中文”并保存即可。

![](img/e13654724f63a453667df0627bc0c18e_87.png)

![](img/e13654724f63a453667df0627bc0c18e_89.png)

![](img/e13654724f63a453667df0627bc0c18e_91.png)

---

![](img/e13654724f63a453667df0627bc0c18e_93.png)

![](img/e13654724f63a453667df0627bc0c18e_95.png)

## 总结
本节课我们一起学习了代码托管的基础知识。我们首先掌握了Git工具的核心命令，包括初始化仓库、添加文件、提交更改等。然后，我们成功部署并配置了GitLab私有代码托管平台，了解了其基本组件，并完成了首次登录和界面中文化设置。

![](img/e13654724f63a453667df0627bc0c18e_97.png)

![](img/e13654724f63a453667df0627bc0c18e_99.png)

至此，我们已经拥有了一个功能完整的内部代码仓库，为后续集成Jenkins实现自动化CI/CD流程打下了坚实的基础。