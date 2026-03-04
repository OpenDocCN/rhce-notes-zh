# Linux运维工程师升职加薪宝典：P94：DevOps-2.GitLab快速入门 🚀

![](img/c1e564a52f9d2a29158c82a72adac977_1.png)

## 概述
在本节课中，我们将学习GitLab的快速入门。我们将从Git工具的基本使用开始，了解其核心概念和工作流程，然后完成GitLab私有代码仓库的部署与配置，并最终将本地代码推送到远程仓库。通过本课，你将掌握DevOps流程中代码托管环节的基础操作。

![](img/c1e564a52f9d2a29158c82a72adac977_3.png)

![](img/c1e564a52f9d2a29158c82a72adac977_5.png)

---

![](img/c1e564a52f9d2a29158c82a72adac977_7.png)

![](img/c1e564a52f9d2a29158c82a72adac977_9.png)

![](img/c1e564a52f9d2a29158c82a72adac977_11.png)

![](img/c1e564a52f9d2a29158c82a72adac977_13.png)

## Git工具简介与配置 🔧

上一节我们介绍了DevOps的基本概念和GitHub的使用。本节中，我们来看看在企业内部更常用的私有代码仓库工具——GitLab。但在使用GitLab之前，我们需要先熟悉其客户端工具：Git。

Git是一款开源的分布式版本控制系统，由Linus Torvalds为管理Linux内核开发而创建。在企业内部，开发人员使用Git将代码提交到私有仓库（如GitLab），后续的集成服务器（如Jenkins）也会使用Git拉取代码进行构建和发布。

使用Git前，必须配置用户信息，以便在提交代码时标识作者身份。

以下是配置Git用户信息的命令：
```bash
git config --global user.name "Your_Name"
git config --global user.email "your_email@example.com"
```
为了获得更好的命令行体验，可以启用颜色输出：
```bash
git config --global color.ui auto
```

---

![](img/c1e564a52f9d2a29158c82a72adac977_15.png)

## Git核心工作流程 📁

![](img/c1e564a52f9d2a29158c82a72adac977_17.png)

![](img/c1e564a52f9d2a29158c82a72adac977_19.png)

![](img/c1e564a52f9d2a29158c82a72adac977_20.png)

![](img/c1e564a52f9d2a29158c82a72adac977_21.png)

理解了Git的基本配置后，我们来深入了解一下Git的核心工作流程。Git涉及几个关键区域：工作目录、暂存区、本地仓库和远程仓库。

![](img/c1e564a52f9d2a29158c82a72adac977_23.png)

![](img/c1e564a52f9d2a29158c82a72adac977_25.png)

![](img/c1e564a52f9d2a29158c82a72adac977_27.png)

1.  **工作目录 (Working Directory)**：你实际存放项目文件的地方。
2.  **暂存区 (Staging Area)**：一个中间区域，用于临时存放你打算提交的更改。
3.  **本地仓库 (Local Repository)**：存储项目所有版本数据的数据库，位于工作目录的 `.git` 隐藏文件夹中。
4.  **远程仓库 (Remote Repository)**：通常位于服务器上（如GitLab），用于团队协作和备份。

![](img/c1e564a52f9d2a29158c82a72adac977_29.png)

其基本工作流程可以概括为：**工作目录 -> 暂存区 -> 本地仓库 -> 远程仓库**。

![](img/c1e564a52f9d2a29158c82a72adac977_31.png)

![](img/c1e564a52f9d2a29158c82a72adac977_33.png)

---

![](img/c1e564a52f9d2a29158c82a72adac977_35.png)

![](img/c1e564a52f9d2a29158c82a72adac977_37.png)

## 本地Git操作实践 💻

![](img/c1e564a52f9d2a29158c82a72adac977_39.png)

现在，让我们通过一个具体的例子来实践Git的本地操作。我们将初始化一个仓库，并将项目文件提交到本地仓库。

![](img/c1e564a52f9d2a29158c82a72adac977_41.png)

首先，创建一个项目目录并进入：
```bash
mkdir /data && cd /data
```
假设你已将项目文件（例如一个前端抽奖页面）放置于此目录。

![](img/c1e564a52f9d2a29158c82a72adac977_43.png)

![](img/c1e564a52f9d2a29158c82a72adac977_45.png)

![](img/c1e564a52f9d2a29158c82a72adac977_47.png)

接下来，初始化Git仓库，这将把当前目录变为一个Git工作目录：
```bash
git init
```
初始化后，使用 `git add` 命令将工作目录中的所有文件添加到暂存区：
```bash
git add .
```
使用 `git status` 命令可以查看暂存区的状态：
```bash
git status
```
最后，使用 `git commit` 命令将暂存区的内容提交到本地仓库，并附上提交信息：
```bash
git commit -m “Initial commit: frontend lottery page”
```
至此，你的代码已经安全地保存在本地Git仓库中。

![](img/c1e564a52f9d2a29158c82a72adac977_49.png)

![](img/c1e564a52f9d2a29158c82a72adac977_51.png)

![](img/c1e564a52f9d2a29158c82a72adac977_53.png)

---

![](img/c1e564a52f9d2a29158c82a72adac977_55.png)

## 部署GitLab私有仓库 🦊

本地操作完成后，我们需要一个远程仓库来托管代码。本节我们将部署企业级的私有代码仓库——GitLab。

GitLab分为社区版(CE)和企业版(EE)，我们使用社区版。可以从清华大学开源软件镜像站下载安装包。

![](img/c1e564a52f9d2a29158c82a72adac977_57.png)

![](img/c1e564a52f9d2a29158c82a72adac977_59.png)

安装完成后，需要配置GitLab的访问地址。编辑其主配置文件：
```bash
vim /etc/gitlab/gitlab.rb
```
找到 `external_url` 配置项，将其值修改为你的服务器地址或域名，例如：
```ruby
external_url ‘http://web.gitlab.com’
```
为了让域名生效，需要在你的客户端机器（如Windows）的 `hosts` 文件（`C:\Windows\System32\drivers\etc\hosts`）中添加解析：
```
192.168.0.14 web.gitlab.com
```
保存配置后，重新配置并启动GitLab服务：
```bash
gitlab-ctl reconfigure
```
此过程可能需要几分钟。启动后，可以通过 `gitlab-ctl status` 命令检查所有服务是否正常运行。

![](img/c1e564a52f9d2a29158c82a72adac977_61.png)

![](img/c1e564a52f9d2a29158c82a72adac977_63.png)

---

![](img/c1e564a52f9d2a29158c82a72adac977_65.png)

![](img/c1e564a52f9d2a29158c82a72adac977_67.png)

## 访问与配置GitLab Web界面 🌐

![](img/c1e564a52f9d2a29158c82a72adac977_69.png)

![](img/c1e564a52f9d2a29158c82a72adac977_71.png)

服务启动后，我们就可以通过Web界面来管理GitLab了。

![](img/c1e564a52f9d2a29158c82a72adac977_73.png)

![](img/c1e564a52f9d2a29158c82a72adac977_75.png)

在浏览器中访问你配置的地址（如 `http://web.gitlab.com`）。首次登录需要使用默认管理员账户：
*   **用户名**：`root`
*   **密码**：存储在服务器上的 `/etc/gitlab/initial_root_password` 文件中，使用 `cat` 命令查看。

![](img/c1e564a52f9d2a29158c82a72adac977_77.png)

![](img/c1e564a52f9d2a29158c82a72adac977_79.png)

登录成功后，为了便于使用，建议将界面语言改为中文。点击右上角用户头像 -> **Settings (偏好设置)** -> **Preferences (偏好)**，在 **Language (语言)** 下拉菜单中选择 **简体中文** 并保存。

![](img/c1e564a52f9d2a29158c82a72adac977_81.png)

![](img/c1e564a52f9d2a29158c82a72adac977_83.png)

![](img/c1e564a52f9d2a29158c82a72adac977_85.png)

---

![](img/c1e564a52f9d2a29158c82a72adac977_87.png)

## 推送本地代码到GitLab 📤

GitLab已经就绪，现在我们将之前提交到本地仓库的代码推送到这个远程仓库。

首先，需要在GitLab上创建一个新项目来接收我们的代码。在GitLab仪表盘点击 **新建项目**，输入项目名称（如 `my-lottery`），然后点击 **创建项目**。

项目创建后，GitLab会提供远程仓库的地址。我们需要将这个远程仓库地址添加到本地Git配置中。在本地项目目录 (`/data`) 中执行：
```bash
git remote add origin http://web.gitlab.com/root/my-lottery.git
```
这里的 `origin` 是远程仓库的别名，`http://...` 是创建项目后GitLab提供的地址。

最后，使用 `git push` 命令将本地仓库的代码推送到远程仓库：
```bash
git push -u origin main
```
`-u` 参数会将本地 `main` 分支与远程 `origin/main` 分支关联起来。首次推送需要输入你在GitLab的账号密码。

![](img/c1e564a52f9d2a29158c82a72adac977_89.png)

![](img/c1e564a52f9d2a29158c82a72adac977_91.png)

![](img/c1e564a52f9d2a29158c82a72adac977_92.png)

操作完成后，刷新GitLab项目页面，即可看到刚刚推送的代码文件。

![](img/c1e564a52f9d2a29158c82a72adac977_94.png)

![](img/c1e564a52f9d2a29158c82a72adac977_96.png)

---

![](img/c1e564a52f9d2a29158c82a72adac977_98.png)

![](img/c1e564a52f9d2a29158c82a72adac977_100.png)

## 总结
本节课中我们一起学习了DevOps中代码管理的基础环节。我们从Git工具的配置和基本工作流程入手，完成了GitLab私有仓库的部署与访问配置，并实践了将本地代码通过Git命令推送到远程GitLab仓库的完整过程。这为后续学习持续集成与持续部署(CI/CD)打下了坚实的基础。