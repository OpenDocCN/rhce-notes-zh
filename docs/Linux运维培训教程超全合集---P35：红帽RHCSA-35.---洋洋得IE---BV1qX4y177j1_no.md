# Linux运维培训教程：1.35：源码包管理与systemd服务管理 🐧

![](img/cae8a89100d9f816c4493566358bfa0d_1.png)

![](img/cae8a89100d9f816c4493566358bfa0d_3.png)

![](img/cae8a89100d9f816c4493566358bfa0d_5.png)

在本节课中，我们将要学习两种重要的软件管理方式：源码包安装和systemd服务管理。源码包安装提供了高度的自定义能力，而systemd则是现代Linux系统中管理服务的核心工具。掌握这两项技能，是成为一名合格运维工程师的基础。

![](img/cae8a89100d9f816c4493566358bfa0d_7.png)

![](img/cae8a89100d9f816c4493566358bfa0d_9.png)

## 源码包安装方式

![](img/cae8a89100d9f816c4493566358bfa0d_11.png)

上一节我们介绍了RPM和YUM包管理，它们虽然方便，但无法自定义软件的安装路径和功能模块。本节中我们来看看如何通过源码包安装来实现高度定制化。

![](img/cae8a89100d9f816c4493566358bfa0d_13.png)

![](img/cae8a89100d9f816c4493566358bfa0d_15.png)

源码包安装的核心思想是：从软件官方网站下载源代码，在本地编译并安装。这种方式允许我们指定安装路径、启用或禁用特定功能模块。

![](img/cae8a89100d9f816c4493566358bfa0d_17.png)

![](img/cae8a89100d9f816c4493566358bfa0d_19.png)

![](img/cae8a89100d9f816c4493566358bfa0d_21.png)

![](img/cae8a89100d9f816c4493566358bfa0d_23.png)

### 源码包安装流程

![](img/cae8a89100d9f816c4493566358bfa0d_25.png)

以下是源码包安装的标准流程，我们以Nginx软件为例进行说明。

![](img/cae8a89100d9f816c4493566358bfa0d_27.png)

![](img/cae8a89100d9f816c4493566358bfa0d_29.png)

1.  **下载源码包**
    首先，需要从软件的官方网站下载其源代码压缩包。例如，访问Nginx官网(`nginx.org`)的下载页面，选择稳定版本进行下载。可以使用`wget`命令直接在终端下载。
    ```bash
    wget https://nginx.org/download/nginx-1.20.2.tar.gz
    ```

2.  **安装依赖包**
    在编译源码之前，需要确保系统已安装必要的开发工具和库文件（即依赖）。依赖信息通常可以在官方文档中找到。对于依赖包，建议直接使用YUM安装，这比编译安装依赖包本身要高效得多。
    ```bash
    yum install -y pcre-devel zlib-devel openssl-devel gcc make
    ```

![](img/cae8a89100d9f816c4493566358bfa0d_31.png)

![](img/cae8a89100d9f816c4493566358bfa0d_33.png)

![](img/cae8a89100d9f816c4493566358bfa0d_35.png)

![](img/cae8a89100d9f816c4493566358bfa0d_37.png)

3.  **解压源码包**
    下载的源码包通常是压缩格式，需要先解压。
    ```bash
    tar -zxvf nginx-1.20.2.tar.gz
    cd nginx-1.20.2
    ```

![](img/cae8a89100d9f816c4493566358bfa0d_39.png)

4.  **配置编译参数（核心步骤）**
    进入解压后的目录，执行`configure`脚本。这是自定义安装的关键步骤，你可以通过参数指定安装路径和需要启用的功能。
    *   `--prefix`： 指定软件安装的目标路径。
    *   `--with-xxx_module`： 启用特定的功能模块。
    可以使用 `./configure --help` 查看所有可用参数。
    ```bash
    ./configure --prefix=/usr/local/nginx --with-http_ssl_module
    ```
    此命令将Nginx配置为安装到`/usr/local/nginx`目录，并启用SSL模块。

5.  **编译源代码**
    配置完成后，使用`make`命令将源代码编译成计算机可执行的二进制文件。
    ```bash
    make
    ```

![](img/cae8a89100d9f816c4493566358bfa0d_41.png)

![](img/cae8a89100d9f816c4493566358bfa0d_43.png)

6.  **安装软件**
    最后，将编译好的文件安装到之前配置的路径中。
    ```bash
    make install
    ```
    安装完成后，所有与Nginx相关的文件都会集中在`/usr/local/nginx`目录下，便于管理。

![](img/cae8a89100d9f816c4493566358bfa0d_45.png)

**总结**：源码安装流程可归纳为：下载 -> 安依赖 -> 解压 -> 配置 -> 编译 -> 安装。这种方式赋予了管理员对软件安装位置和功能的完全控制权。

![](img/cae8a89100d9f816c4493566358bfa0d_47.png)

![](img/cae8a89100d9f816c4493566358bfa0d_49.png)

![](img/cae8a89100d9f816c4493566358bfa0d_51.png)

## systemd管理服务 🚀

![](img/cae8a89100d9f816c4493566358bfa0d_53.png)

![](img/cae8a89100d9f816c4493566358bfa0d_55.png)

软件安装好后，我们需要让它运行起来，这就是服务管理。在RHEL/CentOS 7及以后版本中，`systemd`是初始化系统和服务管理的核心。

![](img/cae8a89100d9f816c4493566358bfa0d_57.png)

![](img/cae8a89100d9f816c4493566358bfa0d_59.png)

![](img/cae8a89100d9f816c4493566358bfa0d_61.png)

### 什么是服务？

![](img/cae8a89100d9f816c4493566358bfa0d_63.png)

![](img/cae8a89100d9f816c4493566358bfa0d_65.png)

![](img/cae8a89100d9f816c4493566358bfa0d_67.png)

服务（Service）是指在后台持续运行的程序，例如Web服务器（Nginx）、文件传输服务（vsftpd）等。`systemd`是系统的第一个进程（PID=1），它负责管理和控制所有这些服务。

![](img/cae8a89100d9f816c4493566358bfa0d_69.png)

![](img/cae8a89100d9f816c4493566358bfa0d_71.png)

### 常用的systemctl命令

![](img/cae8a89100d9f816c4493566358bfa0d_73.png)

![](img/cae8a89100d9f816c4493566358bfa0d_75.png)

`systemctl`是与`systemd`交互的主要命令，用于控制服务的生命周期。

![](img/cae8a89100d9f816c4493566358bfa0d_77.png)

![](img/cae8a89100d9f816c4493566358bfa0d_79.png)

*   **启动服务**：`systemctl start 服务名`
    ```bash
    systemctl start vsftpd
    ```

*   **停止服务**：`systemctl stop 服务名`
    ```bash
    systemctl stop vsftpd
    ```

![](img/cae8a89100d9f816c4493566358bfa0d_81.png)

*   **重启服务**：`systemctl restart 服务名`
    （如果服务未运行，此命令会启动它；如果已运行，则先停止再启动，常用于使新配置生效）
    ```bash
    systemctl restart vsftpd
    ```

![](img/cae8a89100d9f816c4493566358bfa0d_83.png)

![](img/cae8a89100d9f816c4493566358bfa0d_85.png)

![](img/cae8a89100d9f816c4493566358bfa0d_87.png)

![](img/cae8a89100d9f816c4493566358bfa0d_89.png)

*   **查看服务状态**：`systemctl status 服务名`
    此命令可以查看服务是否正在运行、最近的日志以及相关的进程ID（PID）。
    ```bash
    systemctl status vsftpd
    ```

![](img/cae8a89100d9f816c4493566358bfa0d_91.png)

![](img/cae8a89100d9f816c4493566358bfa0d_93.png)

*   **设置服务开机自启**：`systemctl enable 服务名`
    此命令会在系统启动时自动运行该服务。
    ```bash
    systemctl enable vsftpd
    ```

![](img/cae8a89100d9f816c4493566358bfa0d_95.png)

![](img/cae8a89100d9f816c4493566358bfa0d_97.png)

![](img/cae8a89100d9f816c4493566358bfa0d_99.png)

![](img/cae8a89100d9f816c4493566358bfa0d_101.png)

*   **禁用服务开机自启**：`systemctl disable 服务名`
    ```bash
    systemctl disable vsftpd
    ```

![](img/cae8a89100d9f816c4493566358bfa0d_103.png)

![](img/cae8a89100d9f816c4493566358bfa0d_105.png)

![](img/cae8a89100d9f816c4493566358bfa0d_107.png)

![](img/cae8a89100d9f816c4493566358bfa0d_109.png)

*   **查看服务是否开机自启**：`systemctl is-enabled 服务名`
    ```bash
    systemctl is-enabled vsftpd
    ```

![](img/cae8a89100d9f816c4493566358bfa0d_111.png)

### 查看系统运行的服务

除了管理单个服务，我们还需要查看系统中正在运行哪些服务。可以通过检查端口号来间接查看，因为服务通常会监听特定的网络端口。

![](img/cae8a89100d9f816c4493566358bfa0d_113.png)

![](img/cae8a89100d9f816c4493566358bfa0d_115.png)

![](img/cae8a89100d9f816c4493566358bfa0d_117.png)

以下是两个常用的命令：

![](img/cae8a89100d9f816c4493566358bfa0d_119.png)

*   **ss命令**：一个快速、高效的工具，用于转储套接字统计信息。
    ```bash
    ss -ntpl
    ```
    *   `-n`： 以数字形式显示地址和端口。
    *   `-t`： 显示TCP连接。
    *   `-p`： 显示使用套接字的进程。
    *   `-l`： 仅显示监听状态的套接字。

*   **netstat命令**：一个传统的网络统计工具，功能与`ss`类似，但可能需单独安装。
    ```bash
    netstat -ntpl
    ```
    这些命令的输出会显示类似`0.0.0.0:80`的信息，表示有服务正在监听80端口。结合`grep`命令可以过滤特定服务：
    ```bash
    ss -ntpl | grep :80
    ```

![](img/cae8a89100d9f816c4493566358bfa0d_121.png)

![](img/cae8a89100d9f816c4493566358bfa0d_123.png)

---

![](img/cae8a89100d9f816c4493566358bfa0d_125.png)

![](img/cae8a89100d9f816c4493566358bfa0d_127.png)

![](img/cae8a89100d9f816c4493566358bfa0d_129.png)

![](img/cae8a89100d9f816c4493566358bfa0d_131.png)

本节课中我们一起学习了源码包安装的完整流程和`systemd`服务管理的基本操作。源码包安装提供了灵活性，而`systemd`则提供了统一、强大的服务控制方式。它们是Linux系统管理中不可或缺的技能，请务必通过实践加以巩固。