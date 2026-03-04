# Linux发展史与版本介绍：P7：从Unix到Linux的演变与主要发行版

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_1.png)

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_3.png)

在本节课中，我们将学习Linux操作系统的诞生背景、核心概念及其与Unix、GNU项目的关系，并了解以红帽（Red Hat）为代表的主要Linux发行版。

## Unix的商业化与开源精神的萌芽

Unix操作系统最初由贝尔实验室开发，后来逐渐演变为商业化的产品。商业化初期，Unix是免费且开源的，但后期情况发生了变化。

在Unix的发展过程中，理查德·斯托曼（Richard Stallman）是一位关键人物。

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_5.png)

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_7.png)

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_1.png)

他最初在一个开发小组中为Unix系统编写应用程序。随着Unix转向商业化并涉及版权与收费问题，斯托曼秉持开源精神，认为软件应该免费供人使用和学习。因此，他离开了原来的团队。

当时，Unix几乎是唯一可用的操作系统。斯托曼随后发起了一项名为GNU的运动（GNU‘s Not Unix），旨在倡导软件开源。

## Linux内核的诞生

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_9.png)

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_10.png)

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_11.png)

直到1991年，一个转折点出现了——Linux诞生了。Linux的创始人是林纳斯·托瓦兹（Linus Torvalds）。

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_5.png)

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_13.png)

当时他是一名在校研究生，拥有一台Unix机器。他参考了Unix的系统架构，独立编写了一套全新的操作系统内核，名为Linux。需要强调的是，Linux内核没有一行代码抄袭自Unix，它只是“类Unix（Unix-like）”系统，并非Unix的衍生版本。

最初开发出来的Linux内核（Kernel）本身并不能直接使用。内核是操作系统的心脏，负责与硬件交互，例如调度CPU、管理内存和读写磁盘。其核心功能可以概括为：

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_15.png)

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_17.png)

**内核（Kernel） = 硬件控制中心**

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_19.png)

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_20.png)

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_22.png)

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_24.png)

然而，只有内核，用户无法直接与之交互。需要一个外层的“操作系统”来提供人机交互的接口，例如图形界面或命令行。

## 操作系统、内核与GNU的融合

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_26.png)

为了更清晰地理解它们的关系，我们可以这样看：

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_28.png)

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_30.png)

1.  **硬件**：计算机的物理基础。
2.  **内核**：直接控制硬件的核心软件。
3.  **操作系统（外层）**：包含各种应用程序（如命令行工具、图形界面），为用户提供操作入口。

现代我们常说的“操作系统”，实际上是 **内核** 和 **外层操作系统（应用程序集）** 的组合体。

林纳斯将Linux内核开源后，吸引了全球开发者的贡献，内核得以迅速完善。1991年，Linux内核发布了1.0版本。如今，内核版本已迭代到5.x以上（例如，红帽RHEL 8采用4.18版本的内核）。任何人都可以访问 `https://www.kernel.org` 查看或下载内核源代码。

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_32.png)

与此同时，斯托曼的GNU项目已经为Unix系统开发了大量开源应用程序。由于Linux兼容Unix的接口，这些GNU应用程序可以轻松移植到Linux平台上运行。因此，一个完整的、可用的“Linux操作系统”实际上是 **Linux内核** 加上 **GNU应用程序** 的集合，这也是为什么有些Linux系统更准确地被称为“GNU/Linux”系统。

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_34.png)

## Linux的主要发行版

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_36.png)

基于开源的Linux内核和GNU等软件，不同的组织和公司进行整合、修改与包装，形成了各自的“发行版”。

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_38.png)

红帽（Red Hat）是Linux领域重要的公司之一，它基于Linux内核制作了自己的发行版。红帽旗下主要有三个知名的发行版：

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_39.png)

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_40.png)

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_42.png)

以下是红帽旗下的三个主要发行版介绍：

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_44.png)

*   **Red Hat Enterprise Linux (RHEL) - 红帽企业版Linux**
    *   **定位**：面向企业服务器，追求极致稳定与安全。
    *   **特点**：更新周期长（约3-4年一个大版本），提供付费的专业技术支持服务。我们课程中使用的就是此版本。

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_46.png)

*   **Fedora**
    *   **定位**：面向个人用户和桌面端，是新技术的前沿测试场。
    *   **特点**：更新频繁，拥有新颖炫酷的图形界面。许多新功能会先在Fedora上测试，稳定后再引入RHEL。

*   **CentOS (Community Enterprise Operating System)**
    *   **定位**：社区企业操作系统。
    *   **特点**：完全免费，其源代码源自RHEL，旨在提供一个与RHEL在功能上兼容的社区版本。适合那些需要企业级稳定性但无需红帽商业支持的用户。

## 总结

![](img/dc9797c42f36ec02bbb168ca8fb50bc0_48.png)

本节课我们一起学习了Linux的发展历程。我们了解到Linux内核由林纳斯·托瓦兹独立开发，并因开源理念而壮大。完整的Linux系统离不开GNU项目贡献的应用程序。最后，以红帽为例，我们认识了不同的Linux发行版及其定位：面向企业稳定的RHEL、面向桌面与新技术试验的Fedora，以及源自RHEL的免费社区版CentOS。理解这些背景知识，有助于我们更好地学习和运用Linux系统。