# Linux运维：2-1-2：SSHD服务安装与SSH命令使用方法 🔧

在本节课中，我们将要学习SSHD服务的安装以及SSH命令的基本使用方法。SSH是进行远程管理和安全文件传输的核心工具，掌握其安装与使用是Linux系统管理的基础。

---

## 概述 📖

SSH（Secure Shell）是一种建立在应用层和传输层基础上的安全网络协议。它用于远程控制计算机或在计算机之间安全地传输文件，相比早期的Telnet等明文传输协议要安全得多。

---

![](img/42efd18a791e1932e9fcc3b28d59e501_1.png)

![](img/42efd18a791e1932e9fcc3b28d59e501_3.png)

## SSHD服务介绍

![](img/42efd18a791e1932e9fcc3b28d59e501_5.png)

上一节我们介绍了网络基础，本节中我们来看看如何通过SSH实现安全的远程连接。SSHD是SSH协议的服务端程序。为了使用SSH，我们需要安装OpenSSH软件包，它同时提供了服务端和客户端工具。

OpenSSH主要包含以下四个核心软件包：
*   **openssh**： 包含服务端和客户端的核心文件。
*   **openssh-clients**： 包含SSH客户端工具。
*   **openssh-server**： 包含SSH服务端程序。
*   **openssh-askpass**： 支持在图形化界面下弹出密码输入对话框的工具。

![](img/42efd18a791e1932e9fcc3b28d59e501_7.png)

![](img/42efd18a791e1932e9fcc3b28d59e501_8.png)

![](img/42efd18a791e1932e9fcc3b28d59e501_10.png)

通常，这些包在系统安装时可能已经默认安装。我们可以使用YUM包管理器方便地安装或确认它们。

---

![](img/42efd18a791e1932e9fcc3b28d59e501_12.png)

## 安装OpenSSH

![](img/42efd18a791e1932e9fcc3b28d59e501_14.png)

以下是安装OpenSSH的步骤，推荐使用YUM命令以避免处理复杂的依赖关系。

1.  使用YUM安装openssh-server包（安装时会自动解决依赖，包括客户端）：
    ```bash
    yum install -y openssh-server
    ```

2.  验证安装的包：
    ```bash
    rpm -qa | grep openssh
    ```
    此命令会列出所有已安装的包含“openssh”字样的软件包。

![](img/42efd18a791e1932e9fcc3b28d59e501_16.png)

![](img/42efd18a791e1932e9fcc3b28d59e501_18.png)

3.  查看安装生成的主要文件：
    ```bash
    rpm -ql openssh-server
    ```
    此命令会列出`openssh-server`包安装的所有文件，其中最重要的配置文件位于`/etc/ssh/`目录下。

![](img/42efd18a791e1932e9fcc3b28d59e501_20.png)

---

## 配置文件与服务管理

![](img/42efd18a791e1932e9fcc3b28d59e501_22.png)

![](img/42efd18a791e1932e9fcc3b28d59e501_24.png)

安装完成后，我们需要了解如何配置和管理SSHD服务。

![](img/42efd18a791e1932e9fcc3b28d59e501_26.png)

![](img/42efd18a791e1932e9fcc3b28d59e501_28.png)

OpenSSH有两个主要的配置文件：
*   **`/etc/ssh/ssh_config`**： 客户端全局配置文件。
*   **`/etc/ssh/sshd_config`**： 服务端配置文件。我们大部分的服务器端设置都在此文件中修改。

管理SSHD服务的方法如下：
*   **启动/停止/重启服务**： 使用`systemctl`命令。
    ```bash
    systemctl start|stop|restart|status sshd
    ```
*   **设置开机自启**：
    ```bash
    systemctl enable sshd
    ```
*   **检查服务状态**：
    ```bash
    systemctl is-enabled sshd
    ```

---

![](img/42efd18a791e1932e9fcc3b28d59e501_30.png)

![](img/42efd18a791e1932e9fcc3b28d59e501_32.png)

## 使用SSH连接远程主机

![](img/42efd18a791e1932e9fcc3b28d59e501_34.png)

![](img/42efd18a791e1932e9fcc3b28d59e501_35.png)

![](img/42efd18a791e1932e9fcc3b28d59e501_37.png)

现在，我们来学习如何使用SSH客户端命令连接到远程Linux主机。主要有两种命令格式。

![](img/42efd18a791e1932e9fcc3b28d59e501_39.png)

![](img/42efd18a791e1932e9fcc3b28d59e501_41.png)

![](img/42efd18a791e1932e9fcc3b28d59e501_43.png)

**方法一：直接指定用户和主机**
命令格式为：`ssh [用户名@]主机IP地址 [-p 端口号]`
*   如果省略`用户名@`，则会以当前本地登录的用户名去尝试连接远程主机。
*   如果服务使用默认的22端口，`-p`选项可以省略。

例如，以root用户连接192.168.1.64：
```bash
ssh root@192.168.1.64
```
首次连接一台新主机时，客户端会提示你确认远程主机的指纹信息，输入`yes`后，该主机的信息会被保存到本地用户家目录的`~/.ssh/known_hosts`文件中，下次连接便不再提示。

![](img/42efd18a791e1932e9fcc3b28d59e501_45.png)

**方法二：使用 `-l` 参数指定用户**
命令格式为：`ssh -l 用户名 主机IP地址 [-p 端口号]`
这种方法与方法一效果相同，只是参数形式不同。
例如：
```bash
ssh -l root 192.168.1.64
```

无论使用哪种方法，连接建立后都会提示输入对应用户在远程主机上的密码，验证成功即可获得远程主机的Shell。

![](img/42efd18a791e1932e9fcc3b28d59e501_47.png)

---

## 总结 🎯

![](img/42efd18a791e1932e9fcc3b28d59e501_49.png)

![](img/42efd18a791e1932e9fcc3b28d59e501_51.png)

![](img/42efd18a791e1932e9fcc3b28d59e501_53.png)

本节课中我们一起学习了SSH协议的基础知识、如何在Linux系统上安装OpenSSH服务端、管理SSHD服务以及使用SSH客户端命令进行远程连接。记住，`ssh user@hostname` 是最常用的连接命令，而服务端的配置核心是`/etc/ssh/sshd_config`文件。安全可靠的远程管理是系统运维的基石，务必熟练掌握。