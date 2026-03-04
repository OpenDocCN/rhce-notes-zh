# Linux运维进阶：P90：DevOps与CICD概念、GitHub基本应用 🚀

![](img/b16a75e5c920877a0e37e898c1db9f17_1.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_3.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_5.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_7.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_8.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_10.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_12.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_14.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_16.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_18.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_19.png)

在本节课中，我们将要学习DevOps的基本思想、CI/CD的核心工作流程，并初步了解如何使用GitHub这个流行的代码托管平台。

![](img/b16a75e5c920877a0e37e898c1db9f17_21.png)

---

## 软件开发生命周期概述

一款软件从零开始到最终交付给用户使用，会经历多个阶段。这些阶段通常涉及不同的团队和角色。

以下是软件从开发到上线的主要阶段：

1.  **前期规划与市场调研**：公司市场部进行调研，确定软件面向的人群和核心功能。开发团队根据需求制定开发计划，包括技术选型、架构设计等。
2.  **编写代码**：开发工程师根据规划，使用编程语言编写软件的功能代码。
3.  **构建**：构建是指让代码能够在系统中运行起来的过程，为后续测试做准备。
4.  **测试**：测试工程师对软件进行各类测试，确保其功能正常、性能达标且没有漏洞。测试可分为功能性测试和性能测试等。
5.  **环境部署与运维**：运维工程师将测试通过的代码部署到生产环境的服务器上，并负责后续服务器的维护、数据备份、软件升级等工作。
6.  **发布与使用**：公司将软件的应用程序发布到各大应用商店，用户下载后即可使用。

---

## 什么是DevOps？ 🤝

上一节我们介绍了传统的软件交付流程，本节中我们来看看DevOps这一热门概念。

DevOps是Development（开发）和Operations（运维）两个词的组合。它并非一个特定的软件或工具，而是一种**思想**或**目标**。

其核心目标是**促进开发、运维、质量保障等部门之间更好的沟通、协作与整合**，打破部门墙，从而提升软件交付的效率和质量。

在传统模式中，开发和运维部门容易因环境问题、代码问题相互推诿。DevOps倡导运维人员提前介入开发过程，了解技术架构；开发人员也参与部署，提供优化建议。通过这种深度协作，实现更高效、更自动化的软件交付流程。

许多现代运维工具（如Ansible、Jenkins）都体现了DevOps的思想。

---

## 持续集成与持续交付

理解了DevOps的思想后，我们来看一个在企业中具体落地的工作流程：持续集成与持续交付。

*   **持续集成**：英文为 **Continuous Integration**，简称 **CI**。
    *   指的是开发人员频繁地（例如每天多次）将代码提交到共享的代码仓库中。每次提交后，系统会自动进行构建和测试，以便快速发现集成错误。
    *   公式化描述：`频繁提交代码 -> 自动构建 -> 自动化测试`。

*   **持续交付**：英文为 **Continuous Delivery**，简称 **CD**。
    *   在CI的基础上，将集成后的代码自动部署到“类生产环境”中进行更严格的测试。如果测试通过，可以**手动**批准将其部署到生产环境。
    *   核心是确保代码随时处于可发布状态。

*   **持续部署**：英文为 **Continuous Deployment**，是CD的更高级阶段。
    *   在持续交付的基础上，省略手动批准环节，只要通过测试的代码就会**自动**部署到生产环境。

整个CI/CD流程可以概括为：**开发提交代码 -> 自动构建与测试 -> 自动部署到测试环境 -> (手动/自动) 部署到生产环境**。这个过程周而复始，实现了软件交付的自动化与敏捷化。

---

## 代码仓库与GitHub初探

![](img/b16a75e5c920877a0e37e898c1db9f17_23.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_24.png)

在CI/CD流程中，代码仓库是核心枢纽。开发将代码提交至此，集成服务器（如Jenkins）从此处拉取代码进行后续操作。

目前主流的代码托管平台是 **GitHub** 和 **GitLab**。它们使用 **Git** 作为版本控制系统。Git是一个开源的分布式版本控制工具，开发人员用它来管理代码版本。

![](img/b16a75e5c920877a0e37e898c1db9f17_26.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_28.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_30.png)

本节中，我们以GitHub为例，演示其基本使用。

以下是注册GitHub账号并创建仓库的简要步骤：

![](img/b16a75e5c920877a0e37e898c1db9f17_32.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_34.png)

1.  访问 [GitHub官网](https://github.com)，点击“Sign up”进行注册。需要使用有效的电子邮箱接收验证码。
2.  登录后，点击页面右上角的“+”号，选择“New repository”创建新仓库。
3.  填写仓库名称、描述，并选择公开（Public）或私有（Private）可见性，然后点击“Create repository”。

---

![](img/b16a75e5c920877a0e37e898c1db9f17_36.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_38.png)

## 向GitHub仓库推送代码

仓库创建好后，我们可以从本地Linux系统向其中推送代码。这需要先在Linux上安装和配置Git工具。

![](img/b16a75e5c920877a0e37e898c1db9f17_40.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_42.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_43.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_45.png)

以下是关键操作命令：

![](img/b16a75e5c920877a0e37e898c1db9f17_47.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_49.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_51.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_53.png)

1.  **安装Git工具**：
    ```bash
    yum install -y git
    ```

![](img/b16a75e5c920877a0e37e898c1db9f17_55.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_57.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_59.png)

2.  **配置Git用户信息**（用于标识提交者）：
    ```bash
    git config --global user.name "YourUsername"
    git config --global user.email "your-email@example.com"
    ```

![](img/b16a75e5c920877a0e37e898c1db9f17_61.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_63.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_65.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_67.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_69.png)

3.  **初始化本地项目目录为Git仓库**：
    ```bash
    git init
    ```

![](img/b16a75e5c920877a0e37e898c1db9f17_71.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_73.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_75.png)

4.  **将文件添加到暂存区**：
    ```bash
    git add .
    ```

![](img/b16a75e5c920877a0e37e898c1db9f17_77.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_79.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_81.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_83.png)

5.  **提交到本地仓库**：
    ```bash
    git commit -m "提交说明信息"
    ```

![](img/b16a75e5c920877a0e37e898c1db9f17_85.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_87.png)

6.  **关联远程仓库**（使用SSH地址，需提前在GitHub账户设置中添加SSH公钥）：
    ```bash
    git remote add origin git@github.com:YourUsername/YourRepoName.git
    ```

![](img/b16a75e5c920877a0e37e898c1db9f17_89.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_91.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_93.png)

7.  **推送代码到远程仓库**：
    ```bash
    git push -u origin master
    ```

![](img/b16a75e5c920877a0e37e898c1db9f17_95.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_97.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_99.png)

完成以上步骤后，刷新GitHub仓库页面，即可看到推送上去的代码文件。

![](img/b16a75e5c920877a0e37e898c1db9f17_101.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_103.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_105.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_106.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_108.png)

---

![](img/b16a75e5c920877a0e37e898c1db9f17_110.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_112.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_114.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_116.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_118.png)

## 总结

![](img/b16a75e5c920877a0e37e898c1db9f17_120.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_121.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_123.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_125.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_126.png)

![](img/b16a75e5c920877a0e37e898c1db9f17_128.png)

本节课中我们一起学习了：
1.  **软件开发生命周期**的基本阶段。
2.  **DevOps**的核心思想是促进开发与运维的协作。
3.  **CI/CD**是实现自动化软件交付的具体流程，包括持续集成、持续交付和持续部署。
4.  **GitHub**作为代码托管平台的基本用法，包括注册、创建仓库以及从Linux命令行推送代码的完整流程。

![](img/b16a75e5c920877a0e37e898c1db9f17_130.png)

理解这些概念是后续学习基于GitLab和Jenkins构建自动化流水线的基础。