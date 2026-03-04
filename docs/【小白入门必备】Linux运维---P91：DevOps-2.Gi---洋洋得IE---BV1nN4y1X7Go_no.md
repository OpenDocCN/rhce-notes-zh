# Linux运维进阶：P91：DevOps-2.GitLab快速入门 🚀

在本节课中，我们将学习如何搭建和使用GitLab，这是一个在企业内部广泛使用的私有代码托管平台。我们将从Git工具的基础操作开始，逐步完成GitLab的安装、配置，并最终将本地代码推送到GitLab仓库中。

![](img/1d352eed17b1516f98e715fcb34e2422_1.png)

---

![](img/1d352eed17b1516f98e715fcb34e2422_3.png)

## Git工具基础 📚

![](img/1d352eed17b1516f98e715fcb34e2422_5.png)

上一节我们了解了DevOps的基本概念。本节中，我们来看看实现代码版本控制的核心工具——Git。

![](img/1d352eed17b1516f98e715fcb34e2422_7.png)

![](img/1d352eed17b1516f98e715fcb34e2422_9.png)

Git是一款开源的分布式版本控制系统，由Linus Torvalds为管理Linux内核开发而创建。在企业内部，开发人员使用Git将代码提交到私有仓库，而后续的集成服务器（如Jenkins）也会使用Git从仓库拉取代码进行构建和发布。

![](img/1d352eed17b1516f98e715fcb34e2422_11.png)

![](img/1d352eed17b1516f98e715fcb34e2422_13.png)

### 配置Git用户

使用Git前，必须配置提交者的身份信息，以便在代码仓库中追踪是谁提交了代码。

以下是配置全局用户和邮箱的命令：

```bash
git config --global user.name "your_username"
git config --global user.email "your_email@example.com"
```

为了获得更好的命令行体验，可以启用颜色输出：

```bash
git config --global color.ui auto
```

### Git工作流程

Git的工作流程涉及几个核心区域：工作目录、暂存区、本地仓库和远程仓库。

1.  **工作目录**：存放项目文件的本地目录。
2.  **暂存区**：一个临时区域，用于存放准备提交的文件变更。
3.  **本地仓库**：存储项目所有版本历史记录的地方。
4.  **远程仓库**：位于服务器上的项目仓库，用于团队协作。

以下是Git的基本操作命令示例：

![](img/1d352eed17b1516f98e715fcb34e2422_15.png)

```bash
# 1. 初始化一个目录为Git仓库
git init

![](img/1d352eed17b1516f98e715fcb34e2422_17.png)

# 2. 将工作目录中的所有文件添加到暂存区
git add .

![](img/1d352eed17b1516f98e715fcb34e2422_19.png)

![](img/1d352eed17b1516f98e715fcb34e2422_20.png)

![](img/1d352eed17b1516f98e715fcb34e2422_21.png)

# 3. 查看当前仓库状态（哪些文件在暂存区）
git status

![](img/1d352eed17b1516f98e715fcb34e2422_23.png)

![](img/1d352eed17b1516f98e715fcb34e2422_25.png)

# 4. 将暂存区的文件提交到本地仓库，并附上提交信息
git commit -m “Initial commit: added frontend lottery page”
```

![](img/1d352eed17b1516f98e715fcb34e2422_27.png)

完成以上步骤后，代码就保存在了本地Git仓库中。接下来，我们需要一个远程仓库来托管代码。

![](img/1d352eed17b1516f98e715fcb34e2422_29.png)

---

## 安装与配置GitLab 🛠️

![](img/1d352eed17b1516f98e715fcb34e2422_31.png)

上一节我们介绍了Git的基本操作。本节中，我们来搭建一个私有的远程代码仓库——GitLab。

![](img/1d352eed17b1516f98e715fcb34e2422_33.png)

GitLab分为社区版（CE）和企业版（EE）。我们使用免费的社区版。可以从清华大学开源软件镜像站下载安装包。

![](img/1d352eed17b1516f98e715fcb34e2422_35.png)

### 安装GitLab

![](img/1d352eed17b1516f98e715fcb34e2422_37.png)

通过YUM包管理器安装下载好的GitLab软件包：

![](img/1d352eed17b1516f98e715fcb34e2422_39.png)

```bash
yum install -y /path/to/gitlab-ce-xxx.rpm
```

![](img/1d352eed17b1516f98e715fcb34e2422_41.png)

安装过程可能需要一些时间。

![](img/1d352eed17b1516f98e715fcb34e2422_43.png)

### 配置GitLab访问地址

![](img/1d352eed17b1516f98e715fcb34e2422_45.png)

安装完成后，需要配置GitLab实例的外部访问URL。

![](img/1d352eed17b1516f98e715fcb34e2422_47.png)

![](img/1d352eed17b1516f98e715fcb34e2422_49.png)

编辑GitLab的主配置文件：

![](img/1d352eed17b1516f98e715fcb34e2422_51.png)

![](img/1d352eed17b1516f98e715fcb34e2422_53.png)

```bash
vim /etc/gitlab/gitlab.rb
```

![](img/1d352eed17b1516f98e715fcb34e2422_55.png)

找到 `external_url` 配置项，将其修改为你的服务器IP或域名，例如：

```ruby
external_url ‘http://web.gitlab.com’
```

为了让客户端能够通过域名访问，需要在客户端的 hosts 文件（如 Windows 的 `C:\Windows\System32\drivers\etc\hosts`）中添加解析：

```
192.168.0.14 web.gitlab.com
```

### 启动GitLab服务

![](img/1d352eed17b1516f98e715fcb34e2422_57.png)

应用配置并启动所有GitLab服务：

![](img/1d352eed17b1516f98e715fcb34e2422_59.png)

```bash
gitlab-ctl reconfigure
gitlab-ctl start
```

可以使用以下命令检查服务状态：

![](img/1d352eed17b1516f98e715fcb34e2422_61.png)

![](img/1d352eed17b1516f98e715fcb34e2422_63.png)

```bash
gitlab-ctl status
```

![](img/1d352eed17b1516f98e715fcb34e2422_65.png)

![](img/1d352eed17b1516f98e715fcb34e2422_67.png)

### GitLab组件与路径

了解GitLab的关键组件和文件路径有助于后续管理和排错。

![](img/1d352eed17b1516f98e715fcb34e2422_69.png)

以下是GitLab的核心信息：

![](img/1d352eed17b1516f98e715fcb34e2422_71.png)

![](img/1d352eed17b1516f98e715fcb34e2422_73.png)

*   **Web服务器**：使用Nginx提供Web界面。
*   **数据库**：使用PostgreSQL存储项目、用户等数据。
*   **缓存**：使用Redis缓存会话和数据。
*   **应用目录**：`/opt/gitlab`
*   **配置文件目录**：`/etc/gitlab`，主配置文件为 `gitlab.rb`
*   **日志目录**：`/var/log/gitlab`
*   **仓库数据目录**：`/var/opt/gitlab/git-data/repositories`
*   **常用服务命令**：
    *   `gitlab-ctl start`：启动所有服务
    *   `gitlab-ctl stop`：停止所有服务
    *   `gitlab-ctl restart`：重启所有服务

---

## 使用GitLab Web界面 🌐

![](img/1d352eed17b1516f98e715fcb34e2422_75.png)

![](img/1d352eed17b1516f98e715fcb34e2422_77.png)

上一节我们完成了GitLab的安装和启动。本节中，我们通过Web界面登录并初步使用GitLab。

![](img/1d352eed17b1516f98e715fcb34e2422_79.png)

### 首次登录

![](img/1d352eed17b1516f98e715fcb34e2422_81.png)

![](img/1d352eed17b1516f98e715fcb34e2422_83.png)

服务启动后，在浏览器中访问配置的URL（如 `http://web.gitlab.com`）。

首次登录需要使用默认管理员账户：
*   **用户名**：`root`
*   **密码**：存放在服务器上的 `/etc/gitlab/initial_root_password` 文件中，使用 `cat` 命令查看。

### 设置语言

登录后，界面默认为英文。可以将其切换为中文以方便使用。

以下是切换语言的步骤：
1.  点击右上角用户头像，进入 **“Settings” (设置)**。
2.  在左侧菜单中找到 **“Preferences” (偏好设置)**。
3.  在 **“Localization” (本地化)** 区域，将 **“Language” (语言)** 下拉菜单选择为 **“简体中文”**。
4.  点击页面底部的 **“Save changes” (保存更改)**。

### 创建项目与推送代码

现在，我们可以将之前在本地的代码推送到GitLab仓库。

首先，在GitLab网页上创建一个新项目（New project），获取项目的远程仓库地址（HTTP或SSH格式）。

![](img/1d352eed17b1516f98e715fcb34e2422_85.png)

然后，在本地仓库中，添加远程仓库地址并推送代码：

![](img/1d352eed17b1516f98e715fcb34e2422_87.png)

![](img/1d352eed17b1516f98e715fcb34e2422_88.png)

```bash
# 1. 添加远程仓库地址（将 <your_repo_url> 替换为GitLab提供的地址）
git remote add origin <your_repo_url>

![](img/1d352eed17b1516f98e715fcb34e2422_90.png)

# 2. 将本地仓库的代码推送到远程仓库的 master 分支
git push -u origin master
```

![](img/1d352eed17b1516f98e715fcb34e2422_92.png)

执行 `git push` 命令时，可能会要求输入GitLab的用户名和密码进行身份验证。

![](img/1d352eed17b1516f98e715fcb34e2422_94.png)

---

![](img/1d352eed17b1516f98e715fcb34e2422_96.png)

本节课中我们一起学习了Git的基础操作、GitLab私有仓库的搭建与配置，以及如何将本地代码推送到远程GitLab仓库。这是实现自动化CI/CD流水线的第一步，为后续集成Jenkins等工具打下了基础。