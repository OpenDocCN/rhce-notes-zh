# Linux基础入门：第一章：Linux发展史、版本与核心原则 🐧

在本节课中，我们将要学习Linux操作系统的起源、主要发行版本及其特点，并了解Linux系统设计的核心哲学。这些知识是理解和使用Linux的基础。

## Linux的发行版本

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_1.png)

上一节我们介绍了Linux的起源，本节中我们来看看它的主要发行版本。Linux有许多不同的发行版，它们基于相同的内核，但在软件包管理、系统工具和默认配置上有所不同。

### 红帽（Red Hat）家族

红帽公司是开源领域的领导者，旗下拥有多个重要的Linux发行版。

**红帽企业版（RHEL）**
这是红帽公司面向企业市场推出的商业发行版。它以其**极高的稳定性**和**长期的技术支持**而闻名。企业需要为其支付订阅费用以获得官方支持、安全更新和补丁。

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_3.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_5.png)

**Fedora**
Fedora是一个由社区支持、红帽赞助的发行版。它充当了RHEL的**技术试验场**。新功能和软件包会先在Fedora上进行测试和验证，待稳定后才会引入到RHEL中。因此，Fedora的特点是**功能新、更新快**，但可能不如RHEL稳定。它主要面向个人用户和开发者。

**CentOS**
CentOS（Community Enterprise Operating System）最初是一个社区项目，其目标是提供一个与RHEL**完全二进制兼容**的免费版本。它通过重新编译RHEL的源代码来实现这一点。红帽后来收购了CentOS项目，但其免费、开源的模式得以保留。CentOS与RHEL的版本几乎同步，区别主要在于品牌标识和**不提供商业支持**。许多预算有限或拥有自有运维团队的企业会选择CentOS。

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_7.png)

以下是红帽家族三个版本的关系总结：
*   **RHEL**：商业版，稳定，有付费支持。
*   **Fedora**：社区版，技术前沿，用于测试新功能。
*   **CentOS**：社区版，RHEL的免费克隆版，无官方支持。

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_8.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_10.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_12.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_14.png)

### 其他主流发行版

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_16.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_18.png)

除了红帽家族，Linux世界还有其他几个重要的发行版家族。

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_20.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_21.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_23.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_25.png)

**Debian/Ubuntu家族**
*   **Debian**：以**稳定性**著称的纯社区发行版，拥有庞大的软件仓库和严格的软件包管理策略。
*   **Ubuntu**：基于Debian，更注重**桌面用户体验**和易用性。它在个人电脑、云计算和人工智能/机器学习领域应用广泛，对多媒体和硬件（特别是显卡）的支持较好。

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_27.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_29.png)

**SUSE家族**
*   **openSUSE**：社区发行版，以其强大的系统配置工具（YaST）和稳定性闻名。
*   **SUSE Linux Enterprise**：对应的商业企业版，提供专业支持。

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_31.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_32.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_33.png)

**其他特色发行版**
*   **Kali Linux**：基于Debian，专为**网络安全审计和渗透测试**设计，预装了数百种安全工具。

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_35.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_37.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_38.png)

### 国内发行版

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_40.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_42.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_44.png)

国内也有一些基于开源Linux开发的发行版，例如：
*   **麒麟（Kylin）**：中标麒麟、银河麒麟等，多用于政府和企事业单位。
*   **深度（Deepin）**：注重美观和易用的桌面操作系统。
*   **欧拉（openEuler）**：华为推出的面向企业级的操作系统，属于红帽家族生态。

学习其中一个主流发行版（如RHEL/CentOS）后，其知识和技能在很大程度上可以迁移到其他发行版。

## Linux的核心设计原则 🧠

了解了Linux的版本后，我们来看看其背后统一的设计哲学。这些原则是理解Linux系统行为的关键。

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_46.png)

**一切皆文件**
这是Linux系统最重要的抽象理念。在Linux中，**几乎所有的资源和操作对象都被抽象为文件**。这包括：
*   **普通文件**：文档、图片、程序。
*   **目录**：文件夹。
*   **硬件设备**：磁盘（`/dev/sda`）、打印机、终端。
*   **系统进程和内核信息**：在 `/proc` 和 `/sys` 目录下。

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_48.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_49.png)

这种设计使得用户可以使用统一的文件操作命令（如 `cat`, `echo`, `>`）来与各种资源进行交互。例如，向设备文件写入数据就能控制硬件。

**小型、单一目的的应用程序**
Linux推崇“一个程序只做好一件事”。系统由大量功能单一、小巧精悍的工具组成。例如：
*   `ls` 命令只负责**列出目录内容**。
*   `grep` 命令只负责**文本搜索**。
*   `cat` 命令只负责**连接文件并打印到标准输出**。

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_51.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_52.png)

这与Windows上一些“大而全”的软件（如旧版QQ集聊天、游戏、邮箱于一身）形成对比。单一目的的工具更**专注、高效且稳定**。

**组合小程序完成复杂任务**
通过管道（`|`）和重定向（`>`，`<`）等机制，可以将多个单一功能的小程序像积木一样组合起来，完成复杂的任务。例如，以下命令组合用于查找包含“error”的日志行并计数：
```bash
grep "error" /var/log/syslog | wc -l
```

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_54.png)

**避免迷惑的用户界面**
Linux的核心设计**不依赖于特定的图形用户界面（GUI）**。绝大多数系统管理和配置工作可以通过**命令行界面（CLI）** 完成。CLI功能更强大、更高效，且在不同发行版和系统环境中保持一致。图形界面只是运行在系统之上的一个可选应用程序。

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_56.png)

**以文本文件保存配置**
Linux系统的配置通常存储在**纯文本文件**中。例如，网络配置可能在 `/etc/sysconfig/network-scripts/` 目录下的文件中。修改配置意味着编辑这些文本文件。这种方式的好处是：
*   **透明且易于管理**：配置一目了然。
*   **易于备份和版本控制**：可以用 `git` 等工具管理配置变更。
*   **便于批量部署和自动化**：可以通过脚本快速修改大量服务器的配置。

## 总结

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_58.png)

![](img/cadfdb800e4bffa915a1e3c672b4f1b5_60.png)

本节课中我们一起学习了Linux的基础知识。我们首先回顾了Linux的起源，然后详细介绍了主要的发行版本，特别是红帽（Red Hat）家族的RHEL、Fedora和CentOS，以及它们之间的关系和适用场景。我们还探讨了其他如Debian/Ubuntu、SUSE等家族。最后，我们深入理解了Linux系统的核心设计原则：“一切皆文件”、使用小型单一目的的程序、通过组合完成复杂任务、偏爱命令行界面以及用文本文件存储配置。这些原则是高效使用和管理Linux系统的基石。