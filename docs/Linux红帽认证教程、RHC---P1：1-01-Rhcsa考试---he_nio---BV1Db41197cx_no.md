# Linux红帽认证教程：1-01：RHCSA考试环境讲解 🖥️

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_1.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_3.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_5.png)

在本节课中，我们将学习如何搭建和熟悉RHCSA（红帽认证系统管理员）的模拟考试环境。这是开始练习考试题目的第一步。

## 概述

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_7.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_9.png)

RHCSA考试在特定的虚拟化环境中进行。为了帮助大家练习，我们将使用一个预先配置好的虚拟机系统来模拟真实的考试环境。本节将指导你完成环境的准备和基本操作。

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_11.png)

## 环境准备步骤

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_13.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_15.png)

上一节我们介绍了课程目标，本节中我们来看看具体的环境搭建步骤。

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_17.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_19.png)

你需要完成以下三个核心步骤：

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_21.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_23.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_25.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_27.png)

1.  **安装虚拟化软件**：你需要先安装VMware Workstation（或其他兼容的虚拟化软件）。
2.  **导入虚拟机文件**：将我们提供的红帽考试系统虚拟机文件导入到VMware中。
3.  **恢复考试快照**：在VMware中，将虚拟机状态恢复到专为RHCSA练习准备的快照点。

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_29.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_31.png)

完成上述步骤后，你将看到一个运行着红帽操作系统的虚拟机窗口，这就是你的模拟考试“物理机”。

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_33.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_35.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_37.png)

## 考试环境界面导航

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_39.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_41.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_43.png)

现在，你的虚拟机已经启动。屏幕上会出现几个图标，以下是关键功能说明：

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_45.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_47.png)

*   **红帽图标 (View Exam)**：点击此图标会打开一个内置浏览器，显示完整的考试题目和要求。这是你查看考题的地方。
*   **VM Control图标**：这是管理考试中需要用到的**虚拟系统**（即考题实际操作的机器）的控制面板。
*   **终端图标 (Terminal)**：点击此图标会打开一个命令行终端。这个终端模拟的是你所在的“物理机”的控制台，你可以在这里执行命令来管理考试虚拟机。

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_49.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_51.png)

## 管理考试虚拟机

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_53.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_55.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_57.png)

在真实的RHCSA考试中，你需要在提供的虚拟系统（如 `node1` 和 `node2`）上完成任务。以下是管理它们的方法。

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_59.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_61.png)

**方法一：通过VM Control面板**
点击 **VM Control** 图标，会出现节点列表（例如 `node1`， `node2`）。选择任一节点，你可以进行**启动(Start)**、**关闭(Shutdown)**或**打开控制台(Console)**等操作。打开控制台后，你就可以像操作一台独立机器一样登录并答题了。

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_63.png)

**方法二：通过终端命令**
点击 **终端** 图标打开命令行。你可以输入以下命令来启动一个图形化的虚拟机管理工具：

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_65.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_67.png)

```bash
virt-manager
```

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_69.png)

运行此命令后，会打开“Virtual Machine Manager”窗口。在这里，你可以清晰地看到所有考试虚拟机（如 `node1`， `node2`）的状态（是否在运行），并可以直接对其进行管理。

## 查看考题与考试信息

点击桌面上的 **红帽图标 (View Exam)**，仔细阅读打开的考试说明页面。这个页面包含以下关键信息：

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_71.png)

*   **系统信息**：列出了考试中使用的虚拟机名称（如 `node1`， `node2`）。
*   **网络信息**：初始的网络配置，你通常需要根据题目要求修改这些设置。
*   **账户信息**：提供了默认的登录用户名和密码。例如，root用户的密码可能是 `flectrag`。
*   **题目列表**：明确指出了哪些题目需要在 `node1` 上完成，哪些需要在 `node2` 上完成。
*   **重要评测说明**：强调考试系统会在重启后自动评测你的配置，因此所有修改必须保证在重启后依然生效，否则不得分。

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_73.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_75.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_77.png)

## 总结

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_79.png)

![](img/50ea8a3b9ffad2ff2e50aac239bf3550_81.png)

本节课中我们一起学习了RHCSA模拟考试环境的搭建与基本操作。我们掌握了三个关键点：1) 如何准备和启动练习环境；2) 如何通过 **VM Control** 和 **virt-manager** 管理考试用的虚拟机；3) 如何查看考试题目和要求。现在，你的练习环境已经就绪，在接下来的课程中，我们将基于这个环境，逐一讲解和练习RHCSA的考试题目。