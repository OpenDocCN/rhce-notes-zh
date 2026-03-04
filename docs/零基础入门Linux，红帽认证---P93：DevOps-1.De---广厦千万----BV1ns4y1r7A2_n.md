# Linux运维工程师的升职加薪宝典：P93：DevOps-1.DevOps与CICD概念、GitHub基本应用 🚀

![](img/1e6f0d1fb4aa59255fbccf7187547723_1.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_3.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_5.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_7.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_8.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_10.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_12.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_14.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_16.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_18.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_19.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_21.png)

在本节课中，我们将要学习DevOps的基本思想、CI/CD的核心工作流程，并初步了解如何使用GitHub这个流行的代码托管平台。

## 概述 📖

随着互联网的普及，软件已经成为我们生活中不可或缺的一部分。从社交娱乐到衣食住行，都离不开各种应用程序。一款软件从构思、开发到最终交付给用户使用，会经历多个阶段，涉及开发、测试、运维等多个团队的协作。本节课，我们将探讨如何通过DevOps思想和CI/CD流程来优化这一过程，实现更高效、自动化的软件交付。

## DevOps：一种协作思想 🤝

上一节我们提到了软件从开发到上线的整体流程。本节中，我们来看看如何让这个流程更顺畅。

DevOps是Development（开发）和Operations（运维）两个词的组合。它不是一个具体的工具或软件，而是一种旨在促进开发、运维及质量保障（QA）部门之间更好沟通、协作与整合的文化、运动或实践。

在传统的软件公司中，开发和运维部门常常因为代码在测试环境正常、在生产环境却出现问题而相互“甩锅”，运维人员甚至被称为“背锅侠”。DevOps的目标就是打破这种壁垒，让运维人员在项目开发初期就介入，了解开发所使用的技术架构，从而制定更合适的运维方案；同时，开发人员也需要参与到后续的部署环节，提供部署策略和优化建议。通过这种深度协作，共同提升工作效率和软件交付质量。

## CI/CD：自动化的工作流程 ⚙️

理解了DevOps促进协作的目标后，我们来看看在公司内部具体如何实现高效交付，这就是持续集成与持续交付/部署（CI/CD）。

*   **持续集成（CI - Continuous Integration）**：指开发人员频繁地（例如每天多次）将代码集成到共享仓库（如GitHub）中。每次集成都通过自动化构建（编译、打包等）来验证，从而尽快发现集成错误。
*   **持续交付（CD - Continuous Delivery）**：在CI的基础上，将集成后的代码自动部署到“类生产环境”（如测试环境）进行测试。如果测试通过，可以**手动**批准后将代码部署到生产环境。
*   **持续部署（CD - Continuous Deployment）**：持续交付的更高阶段。在CI和自动化测试通过后，代码**自动**部署到生产环境，无需人工干预。

简单来说，CI/CD是一套自动化的流程，它使得软件从代码提交到最终上线给用户使用的过程变得快速、可靠且可重复。

以下是CI/CD流程的一个典型示意图：
```
开发人员提交代码 -> 代码仓库（GitHub/GitLab） -> 集成服务器（如Jenkins）拉取代码并构建 -> 部署到测试环境并测试 -> 部署到生产环境
```
这个流程持续、循环地进行，以适应软件快速迭代和用户需求不断变化的市场环境。

## 代码仓库与GitHub入门 🗃️

在CI/CD流程中，代码仓库是起点和中心。目前最主流的代码托管平台是GitHub和GitLab，它们都使用Git作为版本控制系统。本节我们以GitHub为例，了解其基本应用。

GitHub是一个面向开源及私有软件项目的托管平台，上面托管了海量的开源项目源代码。对于运维工程师而言，虽然我们不需要深入理解这些代码，但需要知道如何与仓库交互，例如拉取代码进行部署。

![](img/1e6f0d1fb4aa59255fbccf7187547723_23.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_24.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_26.png)

以下是注册GitHub账号并创建第一个仓库的简要步骤：

![](img/1e6f0d1fb4aa59255fbccf7187547723_28.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_30.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_31.png)

1.  **访问与注册**：打开 [GitHub官网](https://github.com)，点击“Sign up”进行注册。你需要提供一个有效的邮箱地址，并完成邮件验证。
2.  **创建仓库**：登录后，点击页面右上角的“+”号，选择“New repository”。为你的仓库命名（如`hello-world`），添加描述，选择公开（Public）或私有（Private），然后点击“Create repository”。
3.  **获取仓库地址**：仓库创建成功后，你会看到仓库的访问地址，通常有HTTPS和SSH两种格式。

![](img/1e6f0d1fb4aa59255fbccf7187547723_33.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_34.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_36.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_38.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_40.png)

## 向GitHub推送代码示例 💻

![](img/1e6f0d1fb4aa59255fbccf7187547723_42.png)

了解基本概念后，我们通过一个简单的例子，演示如何将本地代码推送到GitHub仓库。这个过程主要涉及Git命令的使用。

![](img/1e6f0d1fb4aa59255fbccf7187547723_44.png)

**环境准备**：你需要一台安装了Git的Linux服务器或虚拟机。

![](img/1e6f0d1fb4aa59255fbccf7187547723_46.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_48.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_49.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_51.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_53.png)

以下是核心操作步骤：

![](img/1e6f0d1fb4aa59255fbccf7187547723_55.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_57.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_59.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_61.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_63.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_65.png)

1.  **安装Git工具**：
    ```bash
    yum install -y git
    ```

![](img/1e6f0d1fb4aa59255fbccf7187547723_67.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_69.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_71.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_73.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_75.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_77.png)

2.  **配置Git用户信息**（用于标识提交者）：
    ```bash
    git config --global user.name "YourName"
    git config --global user.email "your_email@example.com"
    ```

![](img/1e6f0d1fb4aa59255fbccf7187547723_79.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_81.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_83.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_85.png)

3.  **初始化本地仓库并提交代码**：进入你的项目目录。
    ```bash
    cd /your/project/path
    git init                      # 初始化本地Git仓库
    git add .                     # 将当前目录所有文件添加到暂存区
    git commit -m "Initial commit" # 将暂存区内容提交到本地仓库，并添加提交说明
    ```

![](img/1e6f0d1fb4aa59255fbccf7187547723_87.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_89.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_91.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_93.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_95.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_97.png)

4.  **关联远程仓库并推送**：将你在GitHub上创建的仓库地址关联到本地。
    ```bash
    git remote add origin https://github.com/YourName/hello-world.git
    # 或者使用SSH地址: git remote add origin git@github.com:YourName/hello-world.git
    git push -u origin master    # 将本地仓库的master分支推送到远程仓库（origin）
    ```
    *注意：使用HTTPS方式首次推送需要输入GitHub用户名和密码；使用SSH方式需提前在GitHub账户设置中配置SSH公钥以实现免密推送。*

![](img/1e6f0d1fb4aa59255fbccf7187547723_99.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_101.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_103.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_105.png)

操作成功后，刷新你的GitHub仓库页面，即可看到刚刚上传的代码文件。

![](img/1e6f0d1fb4aa59255fbccf7187547723_107.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_109.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_111.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_113.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_114.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_116.png)

## 总结 🎯

![](img/1e6f0d1fb4aa59255fbccf7187547723_118.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_120.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_122.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_124.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_126.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_128.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_129.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_131.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_133.png)

本节课我们一起学习了DevOps与CI/CD的核心概念以及GitHub的基本应用。
*   **DevOps** 是一种强调开发与运维团队紧密协作的文化和思想，旨在提升软件交付效率和质量。
*   **CI/CD** 是实现DevOps目标的具体技术实践，通过自动化流程实现代码的持续集成、测试和部署。
*   **GitHub** 是一个强大的代码托管平台，是CI/CD流程中的重要一环。我们初步掌握了创建仓库和推送代码的基本方法。

![](img/1e6f0d1fb4aa59255fbccf7187547723_135.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_136.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_138.png)

![](img/1e6f0d1fb4aa59255fbccf7187547723_140.png)

作为运维工程师，理解这些概念和流程，并能够与开发团队在代码仓库层面进行协作，是迈向自动化运维和提升自身价值的关键一步。在接下来的课程中，我们将深入探讨功能更强大的GitLab及其在企业中的实践。