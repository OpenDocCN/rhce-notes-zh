# RHCE8认证课程：02-1：桌面环境介绍 🖥️

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_1.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_3.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_5.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_7.png)

在本节课中，我们将学习红帽企业Linux 8（RHEL 8）的图形化桌面环境。我们将重点介绍GNOME桌面环境，包括其标准版和经典版主题，并学习如何通过图形界面执行基本操作。

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_9.png)

## 概述

Linux是一个模块化的操作系统。其核心是内核，而桌面环境（如GNOME）是运行在用户空间的一个可选组件。红帽RHEL 8默认使用GNOME 3桌面环境，它提供了直观的图形界面，方便用户与系统交互。

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_11.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_13.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_14.png)

## GNOME桌面环境介绍

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_16.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_18.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_20.png)

上一节我们概述了Linux的模块化特性，本节中我们来看看RHEL 8使用的具体桌面环境——GNOME。

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_22.png)

GNOME是红帽采用的桌面环境，当前版本为3。它集成了多种工具和开发平台，为初学者访问操作系统提供了便利。在RHEL 8中，GNOME主要有两个主题：标准版和经典版。RHEL 8默认使用标准版，而RHEL 7则使用经典版。

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_24.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_26.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_28.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_29.png)

### 虚拟化环境下的显示服务器选择

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_31.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_33.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_35.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_37.png)

在虚拟机环境中运行图形界面时，需要注意显示服务器的选择，以确保兼容性和流畅性。

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_39.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_41.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_43.png)

以下是可选的显示服务器：
*   **Wayland**：在虚拟化环境中推荐选择，兼容性更好。
*   **X11**：在虚拟化环境中可能导致图形界面卡顿或出现问题。

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_45.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_47.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_49.png)

建议在虚拟机启动时，选择 **`GNOME`** 或 **`GNOME Classic`** 下的 **`Wayland`** 选项。

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_51.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_53.png)

### 调整显示设置

登录系统后，为了获得更好的视觉体验，可能需要调整显示设置。

1.  **调整分辨率**：进入“设置” -> “设备” -> “显示器”，根据你的屏幕选择合适的分辨率（例如1920x1080）。
2.  **调整显示模式**：在虚拟机窗口，确保选择“自由拉伸”模式，以避免屏幕两侧出现黑边。

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_55.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_57.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_59.png)

### 桌面布局与基本操作

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_61.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_62.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_64.png)

成功登录并调整好显示后，我们来熟悉一下GNOME标准版桌面的主要区域和基本操作。

*   **活动概述 (Activities Overview)**：点击屏幕左上角的“活动”按钮或按键盘上的 **`Super`** 键（通常是Windows键）可以进入。这里相当于Windows的“开始”菜单，集中了应用程序、搜索和虚拟桌面管理功能。
*   **应用程序与搜索**：在“活动概述”左侧是应用程序快捷方式栏。顶部的搜索框可以快速查找并启动应用程序。
*   **窗口与任务切换**：打开的应用程序会以窗口形式显示。可以使用 **`Alt + Tab`** 快捷键在不同应用程序窗口间切换。
*   **虚拟桌面 (Workspaces)**：在“活动概述”右侧，可以管理多个虚拟桌面。你可以将不同的应用程序分组到不同的桌面，例如在一个桌面专门用于系统管理终端，在另一个桌面用于文档编辑。拖动应用程序窗口到屏幕右侧可以创建新的虚拟桌面。
*   **顶部状态栏**：屏幕右上角是系统状态栏，包含以下功能：
    *   声音控制
    *   网络连接状态（在虚拟机中可能显示不全）
    *   当前用户账户菜单（可进行锁定、注销、账户设置等操作）
    *   系统菜单（包含“设置”控制面板、锁定屏幕、关机/重启等选项）

### 系统设置与控制面板

通过系统菜单，我们可以访问系统的各种设置，这类似于Windows的控制面板。

点击右上角的系统菜单，选择“设置”即可进入系统设置面板。在这里，你可以配置网络、蓝牙、背景、通知、电源管理（如自动锁屏和挂起）等各项参数。

## 实践练习：使用桌面环境

理论介绍完毕，现在让我们通过一个指导练习来巩固所学知识。这个练习将引导你完成登录、修改密码、注销、锁定屏幕和关机等基本操作。

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_66.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_68.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_70.png)

**练习目标**：能够使用GNOME桌面环境登录RHEL 8系统，并在终端中运行命令。

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_72.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_74.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_76.png)

**前提条件**：确保你的`workstation`虚拟机正在运行。你可以在终端中使用以下命令检查并启动虚拟机：
```bash
rht-vmctl status workstation
rht-vmctl start workstation
```

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_78.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_80.png)

以下是具体的练习步骤：

1.  **登录系统**：在登录界面，选择用户 `student`，输入密码 `student` 进行登录。
2.  **修改用户密码**：
    *   按 **`Super`** 键，搜索并打开“终端”(Terminal)。
    *   在终端中输入命令 `passwd`。
    *   根据提示，先输入当前密码 `student`（输入时无显示是正常的）。
    *   然后输入新密码 `55TURNK3Y`（区分大小写），并再次确认。
    *   看到“密码更新成功”的提示即表示修改完成。
3.  **注销并重新登录**：点击右上角系统菜单 -> 用户账户 -> 选择“注销”(Log Out)。然后使用新密码 `55TURNK3Y` 重新登录 `student` 用户。
4.  **锁定屏幕**：点击右上角系统菜单 -> 点击“锁定”(Lock)按钮。屏幕锁定后，按任意键或点击鼠标，输入新密码 `55TURNK3Y` 即可解锁。
5.  **关机操作（练习中取消）**：点击右上角系统菜单 -> 点击底部的电源按钮。在弹出的对话框中，我们选择“取消”(Cancel)来中断关机操作（如果选择“关机”Power Off，虚拟机将会关闭）。
6.  **完成练习与清理**：练习结束后，需要在终端中运行清理脚本，该脚本会重置实验环境并将`student`用户的密码恢复为默认值。
    *   打开终端。
    *   输入命令：`lab cli-desktop finish`
    *   看到脚本执行成功的提示后，整个练习就完成了。

## 经典版GNOME主题简介

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_82.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_84.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_86.png)

之前我们详细介绍了标准版主题，本节中我们快速了解一下经典版主题，它提供了更接近传统桌面布局的体验。

在登录界面，选择 **`GNOME Classic (Wayland)`** 会话即可进入经典版主题。其界面特点包括：
*   屏幕底部有一个类似Windows“开始”菜单的“应用程序”菜单。
*   顶部面板集成了应用程序菜单、快捷图标和系统状态区。
*   文件管理器、终端等常用工具可以更方便地从“应用程序”菜单中找到。
*   红帽还提供了图形化的虚拟机管理工具（如`virt-manager`），可以在“应用程序” -> “系统工具”中找到。

无论是标准版还是经典版，最终高效的系统管理通常依赖于命令行终端。图形界面主要用于初步熟悉和日常便捷操作。

## 总结

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_88.png)

![](img/75fd2a0c2cbad7a67f23a31bafd3bfc2_90.png)

本节课中我们一起学习了RHEL 8的GNOME桌面环境。我们了解了Linux模块化的概念，认识了GNOME桌面及其两种主题（标准版和经典版），掌握了在虚拟化环境中选择正确显示服务器的方法。我们详细探索了桌面布局，包括活动概述、应用程序启动、窗口切换、虚拟桌面管理和系统设置。最后，通过一个完整的实践练习，我们演练了从登录、修改密码到系统基本操作的全过程，为后续的命令行学习打下了基础。