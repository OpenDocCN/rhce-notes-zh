# Linux运维教程：P36：源码包管理与systemd服务管理

![](img/77bd889a7a4a66b0ef783cd7705e0683_1.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_3.png)

在本节课中，我们将学习Linux中两种重要的软件管理方式：源码包安装和systemd服务管理。源码包安装提供了高度的自定义能力，而systemd则是现代Linux系统管理服务的核心工具。

![](img/77bd889a7a4a66b0ef783cd7705e0683_5.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_7.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_9.png)

## 源码包安装方式

上一节我们介绍了RPM和YUM包管理，它们虽然方便，但无法自定义软件的安装路径和功能模块。本节中我们来看看源码包安装，它允许我们进行更精细的控制。

![](img/77bd889a7a4a66b0ef783cd7705e0683_11.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_13.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_15.png)

### 源码包安装概述

![](img/77bd889a7a4a66b0ef783cd7705e0683_17.png)

源码包安装是指从软件的源代码开始，经过配置、编译和安装的过程。这种方式在企业中常用于需要自定义安装路径或特定功能模块的场景。

![](img/77bd889a7a4a66b0ef783cd7705e0683_19.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_21.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_23.png)

### 源码包安装流程

![](img/77bd889a7a4a66b0ef783cd7705e0683_25.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_27.png)

源码包安装遵循一个标准流程，以下是主要步骤：

![](img/77bd889a7a4a66b0ef783cd7705e0683_29.png)

1.  **下载源码包**：从软件官方网站获取源代码压缩包。
2.  **安装依赖包**：安装该软件运行所必需的其他软件包（依赖）。
3.  **解压源码包**：使用解压命令（如 `tar`）释放源代码文件。
4.  **配置编译选项**：进入源码目录，运行配置脚本（通常是 `./configure`），指定安装路径、启用或禁用功能模块。
5.  **编译源代码**：使用 `make` 命令将人类可读的源代码转换为计算机可执行的二进制文件。
6.  **安装软件**：使用 `make install` 命令将编译好的文件复制到指定的系统目录中。

### 以Nginx为例进行源码安装

![](img/77bd889a7a4a66b0ef783cd7705e0683_31.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_33.png)

我们以Nginx软件为例，演示源码安装的具体操作。

![](img/77bd889a7a4a66b0ef783cd7705e0683_35.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_37.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_39.png)

**第一步：下载源码包**
访问Nginx官方网站（nginx.org），在下载（Download）页面找到稳定版（Stable version）的源码包链接。在Linux终端中使用 `wget` 命令下载。
```bash
wget https://nginx.org/download/nginx-1.20.2.tar.gz
```

**第二步：安装依赖包**
查阅Nginx官方文档可知，编译Nginx通常需要PCRE、zlib和OpenSSL等开发库。我们可以使用YUM来安装这些依赖，这比编译安装依赖包更简便。
```bash
yum -y install pcre-devel zlib-devel openssl-devel
```

![](img/77bd889a7a4a66b0ef783cd7705e0683_41.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_43.png)

**第三步：解压并进入源码目录**
```bash
tar -zxvf nginx-1.20.2.tar.gz
cd nginx-1.20.2
```

**第四步：配置编译选项**
运行 `./configure` 脚本，并指定安装路径和需要的模块。例如，将Nginx安装到 `/usr/local/nginx` 目录，并启用SSL模块。
```bash
./configure --prefix=/usr/local/nginx --with-http_ssl_module
```
可以通过 `./configure --help` 查看所有可配置的选项。

![](img/77bd889a7a4a66b0ef783cd7705e0683_45.png)

**第五步：编译源代码**
```bash
make
```
此过程将源代码编译成二进制文件。

![](img/77bd889a7a4a66b0ef783cd7705e0683_47.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_49.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_51.png)

**第六步：安装软件**
```bash
make install
```
此命令会将编译好的文件复制到 `--prefix` 指定的目录（如 `/usr/local/nginx`）中。

![](img/77bd889a7a4a66b0ef783cd7705e0683_53.png)

安装完成后，所有与Nginx相关的文件都集中在 `/usr/local/nginx` 目录下，便于管理。

![](img/77bd889a7a4a66b0ef783cd7705e0683_55.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_57.png)

### 源码包管理的优缺点

![](img/77bd889a7a4a66b0ef783cd7705e0683_59.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_61.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_63.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_65.png)

*   **优点**：高度自定义，可以优化软件功能、指定安装路径，适合有特定需求的企业环境。
*   **缺点**：步骤繁琐，需要手动解决依赖关系，对初学者不友好。

![](img/77bd889a7a4a66b0ef783cd7705e0683_67.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_69.png)

---

![](img/77bd889a7a4a66b0ef783cd7705e0683_71.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_73.png)

## systemd服务管理

软件安装后，我们需要启动、停止或监控其运行状态。在RHEL/CentOS 7及以后版本中，`systemd` 是默认的系统和服务管理器。

![](img/77bd889a7a4a66b0ef783cd7705e0683_75.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_77.png)

### systemd简介

`systemd` 是系统的第一个进程（PID=1），它负责启动并管理其他所有系统服务。它提供了一系列命令来方便地控制服务。

![](img/77bd889a7a4a66b0ef783cd7705e0683_79.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_81.png)

### 常用systemctl命令

![](img/77bd889a7a4a66b0ef783cd7705e0683_83.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_85.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_87.png)

以下是管理服务（如vsftpd）最常用的 `systemctl` 命令：

![](img/77bd889a7a4a66b0ef783cd7705e0683_89.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_91.png)

*   **启动服务**：`systemctl start vsftpd`
*   **停止服务**：`systemctl stop vsftpd`
*   **重启服务**：`systemctl restart vsftpd` （无论服务是否运行，此命令都适用）
*   **查看服务状态**：`systemctl status vsftpd` （查看是否运行、运行时间等信息）
*   **设置服务开机自启**：`systemctl enable vsftpd`
*   **取消服务开机自启**：`systemctl disable vsftpd`
*   **查看服务是否开机自启**：`systemctl is-enabled vsftpd`

![](img/77bd889a7a4a66b0ef783cd7705e0683_93.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_95.png)

### 查看系统运行的服务

![](img/77bd889a7a4a66b0ef783cd7705e0683_97.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_99.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_101.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_103.png)

除了管理单个服务，我们还需要查看系统中哪些服务正在运行。可以通过检查端口号来间接查看，因为服务通常会监听特定的网络端口。

![](img/77bd889a7a4a66b0ef783cd7705e0683_105.png)

以下是两个常用的查看端口（服务）的命令：

1.  **`ss` 命令**：一个快速、高效的工具。
    ```bash
    ss -ntpl
    ```
    *   `-n`：以数字形式显示端口。
    *   `-t`：显示TCP连接。
    *   `-p`：显示占用端口的进程名。
    *   `-l`：仅显示监听状态的端口。

2.  **`netstat` 命令**：一个传统但功能详细的工具（可能需要安装 `net-tools` 包）。
    ```bash
    netstat -ntpl
    ```
    参数含义与 `ss` 命令类似。

![](img/77bd889a7a4a66b0ef783cd7705e0683_107.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_109.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_111.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_113.png)

例如，要查看Nginx服务（默认监听80端口）是否运行，可以结合 `grep` 过滤：
```bash
ss -ntpl | grep :80
```
或
```bash
netstat -ntpl | grep :80
```

---

![](img/77bd889a7a4a66b0ef783cd7705e0683_115.png)

### 本节课总结

![](img/77bd889a7a4a66b0ef783cd7705e0683_117.png)

本节课中我们一起学习了Linux中两种核心的软件管理技能：
1.  **源码包安装**：我们了解了从下载、解压、配置、编译到安装的完整流程，并以Nginx为例进行了演示。这种方式提供了最大的灵活性，但步骤较为复杂。
2.  **systemd服务管理**：我们掌握了使用 `systemctl` 命令启动、停止、重启、设置开机自启以及查看服务状态的方法。同时，学习了使用 `ss` 或 `netstat` 命令查看系统正在运行的服务（端口）。

![](img/77bd889a7a4a66b0ef783cd7705e0683_119.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_121.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_123.png)

![](img/77bd889a7a4a66b0ef783cd7705e0683_125.png)

掌握这两种方法，你将能够更自如地在Linux系统中部署和管理软件应用。