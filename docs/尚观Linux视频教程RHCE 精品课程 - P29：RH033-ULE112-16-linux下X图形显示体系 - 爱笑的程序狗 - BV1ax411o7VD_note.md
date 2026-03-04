# 尚观Linux视频教程RHCE精品课程：P29：RH033-ULE112-16：Linux下X图形显示体系 🖥️

![](img/392ba6bc576a0e2d7595e22269e5c5e6_1.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_3.png)

在本节课中，我们将要学习Linux系统中的图形显示体系，特别是X Window系统。我们将了解其与Windows图形界面的根本区别，剖析其客户端/服务器架构，并学习如何在不同机器间进行图形界面的远程控制。

![](img/392ba6bc576a0e2d7595e22269e5c5e6_5.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_6.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_8.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_9.png)

## 概述：Linux图形界面与Windows的区别

上一节我们介绍了Linux的命令行操作。本节中我们来看看Linux的图形界面。Linux的图形界面与Windows不同，它秉承了Unix的文本操作习惯，但也提供了贴近常规使用模式的图形方式，甚至支持3D桌面效果。然而，其响应速度有时较慢，这与其独特的架构有关。

## X Window系统的基本架构

X Window系统，通常简称为X，是Unix/Linux系统上标准的图形显示系统。其核心架构与Windows完全不同，采用了客户端/服务器模型。

**核心概念**：X系统由`X Server`（服务器端）和`X Client`（客户端）组成，它们之间通过`X协议`（通常是X11）进行通信。

*   **X Server**：负责底层的图形显示、驱动显示器、键盘和鼠标。它接收来自客户端的图形指令并将其渲染到屏幕上，同时将输入设备的事件发送给客户端。
*   **X Client**：是实际的应用程序，如图形界面程序、终端模拟器等。它负责执行程序逻辑，并将需要显示的图形信息发送给X Server。

![](img/392ba6bc576a0e2d7595e22269e5c5e6_11.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_12.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_13.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_14.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_15.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_17.png)

一个典型的应用场景是：一台昂贵的大型机（运行X Client程序，如卫星云图运算软件）将图形输出到另一台普通的桌面电脑（运行X Server），用户通过桌面电脑的键盘鼠标控制大型机上的程序。电影《功夫熊猫》、《泰坦尼克号》的渲染集群也采用了类似的工作模式。

![](img/392ba6bc576a0e2d7595e22269e5c5e6_19.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_21.png)

## 本地启动X Window的步骤

![](img/392ba6bc576a0e2d7595e22269e5c5e6_23.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_24.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_26.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_27.png)

当我们在一台机器上使用`startx`命令启动图形界面时，实际上系统执行了以下两步：

![](img/392ba6bc576a0e2d7595e22269e5c5e6_29.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_30.png)

1.  首先启动本机的`X Server`（例如`Xorg`进程）。
2.  然后启动一个默认的`X Client`程序（如桌面环境）。

![](img/392ba6bc576a0e2d7595e22269e5c5e6_32.png)

我们可以手动分步演示这个过程：

1.  **仅启动X Server**：在命令行输入大写的`X`命令。此时屏幕会变为黑色，只有一个“X”形光标，这表明X Server已启动，但没有客户端程序输出任何图形。
    ```bash
    X
    ```
    使用 `Ctrl + Alt + Backspace` 可以强制关闭X Server。

2.  **启动X Client**：更常见的是使用`xinit`命令，它会先启动X Server，再启动一个默认的客户端（通常是`xterm`，一个简单的终端窗口）。
    ```bash
    xinit
    ```

`startx`脚本则做了更多工作，它在启动X Server后，通常会启动一个完整的**桌面环境**。

![](img/392ba6bc576a0e2d7595e22269e5c5e6_34.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_35.png)

## 桌面环境与显示管理器

![](img/392ba6bc576a0e2d7595e22269e5c5e6_37.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_39.png)

### 桌面环境

![](img/392ba6bc576a0e2d7595e22269e5c5e6_41.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_42.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_44.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_46.png)

桌面环境提供了一套完整的图形用户界面，包括窗口装饰（标题栏、最大化/最小化按钮）、桌面背景、任务栏、开始菜单等。Linux上主流的桌面环境有两种：

![](img/392ba6bc576a0e2d7595e22269e5c5e6_47.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_48.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_50.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_52.png)

*   **GNOME**：使用`GTK+`图形库。
*   **KDE**：使用`Qt`图形库。

![](img/392ba6bc576a0e2d7595e22269e5c5e6_54.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_56.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_58.png)

启动它们的方式通常是（在X Server运行后）：
```bash
startkde   # 启动KDE桌面环境
gnome-session # 启动GNOME桌面环境
```

![](img/392ba6bc576a0e2d7595e22269e5c5e6_60.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_61.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_62.png)

### 显示管理器

显示管理器是一个特殊的X Client，它提供了图形化的登录界面，在用户输入正确的用户名和密码后，再启动对应的桌面环境。

![](img/392ba6bc576a0e2d7595e22269e5c5e6_64.png)

*   **GDM**: GNOME Display Manager
*   **KDM**: KDE Display Manager
*   **XDM**: X Display Manager

![](img/392ba6bc576a0e2d7595e22269e5c5e6_66.png)

当Linux系统以运行级别5启动时，会自动运行一个DM，而不是直接进入桌面。这提供了更高的安全性。

## X Window的远程控制原理与实践

![](img/392ba6bc576a0e2d7595e22269e5c5e6_68.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_70.png)

X架构的精妙之处在于其网络透明性。X Client和X Server可以运行在不同的机器上。

![](img/392ba6bc576a0e2d7595e22269e5c5e6_72.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_74.png)

**核心原理**：控制发生在**X Server端**。你在X Server的显示器上看到图像，并用其键盘鼠标操作，这些操作被发送给远端的X Client程序执行。

以下是实现远程控制的步骤：

1.  **在作为X Server的机器A上**：
    *   启动X Server（例如用`startx`）。
    *   运行`xhost +`命令，允许任何远程客户端连接。（出于安全考虑，可以指定IP，如`xhost + 192.168.1.100`）。

![](img/392ba6bc576a0e2d7595e22269e5c5e6_76.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_77.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_79.png)

2.  **在运行X Client程序的机器B上**：
    *   通过SSH等方式登录到机器B。
    *   设置`DISPLAY`环境变量，告诉X Client将图形输出到机器A。
        ```bash
        export DISPLAY="机器A的IP地址:0"
        ```
        `:0`表示机器A上的第一个显示（通常就是主显示器）。
    *   运行任何图形程序（如`xclock`, `xterm`, `startkde`），其界面就会显示在机器A上。

**更极端的例子：Windows作为X Server**
你可以在Windows上安装`X Manager`、`Xming`等软件，让Windows充当X Server。然后从Linux机器设置`DISPLAY`指向Windows机器的IP，就可以在Windows桌面上看到并操作Linux的图形程序了。

## 常用工具与快捷键

以下是X Window系统中一些常用的配置工具和快捷键：

![](img/392ba6bc576a0e2d7595e22269e5c5e6_81.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_82.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_84.png)

**配置工具**：
*   `system-config-display`：图形化显示设置工具（RHEL/CentOS）。
*   `switchdesk`：切换默认的桌面环境（如GNOME或KDE）。

![](img/392ba6bc576a0e2d7595e22269e5c5e6_86.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_88.png)

![](img/392ba6bc576a0e2d7595e22269e5c5e6_89.png)

**常用快捷键**：
*   `Ctrl + Alt + F1~F6`：切换到第1-6个文本虚拟控制台。
*   `Ctrl + Alt + F7`（或`F8`等）：切换回图形界面（如果启动了多个X会话，则对应`:0`, `:1`...）。
*   `Ctrl + Alt + Backspace`：强制关闭当前的X Server。
*   `Ctrl + Alt + 小键盘+/-`：切换屏幕分辨率。
*   `Alt + F2`：弹出“运行命令”对话框（在GNOME/KDE中）。
*   `Alt + F4`：关闭当前活动窗口。
*   `Alt + Tab`：在窗口间切换。

## 图形界面下的常用应用程序

在桌面环境中，你可以找到许多实用的图形化工具，例如：
*   **终端**：`xterm`, `gnome-terminal`, `konsole`。
*   **网页浏览器**：`Firefox`。
*   **即时通讯**：`Pidgin`（原Gaim）。
*   **图像处理**：`GIMP`（类似Photoshop）。
*   **办公套件**：`LibreOffice`或`OpenOffice`。

## 总结与生产环境建议

本节课中我们一起学习了Linux下X图形显示体系。我们了解了X Window独特的客户端/服务器架构，学会了如何本地启动X以及进行跨机器的远程图形控制。我们还认识了桌面环境和显示管理器的作用。

![](img/392ba6bc576a0e2d7595e22269e5c5e6_91.png)

需要强调的是，在生产服务器环境中，图形界面（X Window）通常是不安装或不启用的，因为它会消耗较多的内存和CPU资源。Linux的强大之处在于其稳定、高效的命令行和脚本管理能力。图形界面更适合作为个人桌面使用或特定的图形工作站场景。因此，在学习Linux时，应将主要精力放在命令行和系统服务的配置管理上。