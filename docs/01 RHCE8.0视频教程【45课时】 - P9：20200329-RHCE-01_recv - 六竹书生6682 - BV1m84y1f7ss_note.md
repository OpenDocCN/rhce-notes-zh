# RHCE8.0 视频教程：01：搭建 FTP 协议的 YUM 源服务器与客户端

![](img/4995248c39faf0f51cb20216cd21fc74_0.png)

在本节课中，我们将学习如何搭建一个基于 FTP 协议的 YUM 源服务器，并配置客户端来使用这个远程源。我们将复习本地 YUM 源的配置，然后扩展到网络环境，实现服务器与客户端的通信。

## 概述与准备工作

上一节我们介绍了本地 YUM 源的基本概念和配置方法。本节中，我们来看看如何搭建一个网络化的 YUM 源服务器，让其他客户端也能通过网络访问。

首先，我们需要准备两台能够互相通信的 Linux 设备。在本例中：
*   **服务器端**：IP 地址为 `192.168.127.135`
*   **客户端**：IP 地址为 `192.168.127.133`

在开始配置前，请确保两台设备之间网络通畅。可以使用 `ping` 命令进行测试。

```bash
ping 192.168.127.135 -c 2
```

![](img/4995248c39faf0f51cb20216cd21fc74_2.png)

## 服务器端配置

服务器端的主要任务是：安装 FTP 服务软件，创建一个包含软件包的共享目录，并通过 FTP 协议将其发布出去。

以下是服务器端配置的具体步骤：

### 1. 安装 FTP 服务软件

首先，我们需要在服务器上安装 FTP 服务软件。这里我们使用 `vsftpd`。

```bash
# 使用本地 YUM 源安装 vsftpd
yum install vsftpd -y
```

### 2. 创建共享目录并挂载光盘

FTP 服务默认的根目录是 `/var/ftp`。我们需要在此目录下创建一个子目录（例如 `share/soft`），并将系统安装光盘挂载到这个目录下，以提供软件包。

```bash
# 创建共享目录
mkdir -p /var/ftp/share/soft

# 挂载光盘到共享目录
mount /dev/sr0 /var/ftp/share/soft
```

**注意**：在 RHEL 8 中，光盘内容被分为 `BaseOS` 和 `AppStream` 两个仓库。你需要根据要共享的软件类型，将路径指向正确的子目录（例如 `/var/ftp/share/soft/AppStream`）。

### 3. 启动 FTP 服务并设置开机自启

安装并配置好目录后，需要启动 FTP 服务。

```bash
# 启动 vsftpd 服务
systemctl start vsftpd

# 设置 vsftpd 服务开机自动启动
systemctl enable vsftpd

# 检查服务状态，确认其正在运行
systemctl status vsftpd
```

### 4. 临时关闭安全策略（为实验环境）

为了确保实验顺利进行，避免 SELinux 和防火墙的干扰，我们暂时将它们关闭。**请注意，在生产环境中应谨慎操作。**

```bash
# 临时关闭防火墙
iptables -F

# 临时将 SELinux 设置为宽容模式
setenforce 0
```

## 客户端配置

客户端配置相对简单，只需要创建一个指向服务器 FTP 共享目录的 YUM 源配置文件即可。

以下是客户端配置的具体步骤：

![](img/4995248c39faf0f51cb20216cd21fc74_4.png)

### 1. 创建 YUM 源配置文件

进入 YUM 源配置目录，创建一个新的 `.repo` 文件。

![](img/4995248c39faf0f51cb20216cd21fc74_6.png)

```bash
cd /etc/yum.repos.d/
vim ftp-server.repo
```

![](img/4995248c39faf0f51cb20216cd21fc74_8.png)

![](img/4995248c39faf0f51cb20216cd21fc74_10.png)

在配置文件中，需要写入以下核心信息：

![](img/4995248c39faf0f51cb20216cd21fc74_12.png)

![](img/4995248c39faf0f51cb20216cd21fc74_14.png)

![](img/4995248c39faf0f51cb20216cd21fc74_15.png)

*   `[ftp-server]`：源仓库的标识名称。
*   `name=FTP Server Repo`：仓库的描述信息。
*   `baseurl=ftp://192.168.127.135/share/soft/AppStream/`：**关键**。这里指定了远程服务器上软件仓库的具体路径。协议是 `ftp`，后面是服务器 IP 和我们在服务器端创建的目录路径。
*   `gpgcheck=0`：禁用 GPG 密钥检查（实验环境为了方便）。

![](img/4995248c39faf0f51cb20216cd21fc74_17.png)

![](img/4995248c39faf0f51cb20216cd21fc74_19.png)

一个完整的配置文件示例如下：
```ini
[ftp-server]
name=FTP Server Repo
baseurl=ftp://192.168.127.135/share/soft/AppStream/
gpgcheck=0
```

![](img/4995248c39faf0f51cb20216cd21fc74_21.png)

### 2. 验证客户端配置

配置文件保存后，使用以下命令清理旧缓存并列出所有可用的 YUM 源，以验证配置是否成功。

```bash
yum clean all
yum repolist
```

如果配置正确，命令会显示 `ftp-server` 仓库及其包含的软件包数量，而不会出现错误信息。

![](img/4995248c39faf0f51cb20216cd21fc74_23.png)

![](img/4995248c39faf0f51cb20216cd21fc74_25.png)

![](img/4995248c39faf0f51cb20216cd21fc74_26.png)

![](img/4995248c39faf0f51cb20216cd21fc74_27.png)

## 常见问题与注意事项

在配置过程中，可能会遇到一些问题。以下是一些关键点：

![](img/4995248c39faf0f51cb20216cd21fc74_29.png)

*   **路径准确性**：客户端 `baseurl` 中的路径必须与服务器端共享目录的实际路径完全一致。路径错误是导致 `yum repolist` 失败的最常见原因。
*   **服务与端口**：确保服务器端的 FTP 服务（`vsftpd`）已成功启动并运行在默认的 21 端口。
*   **网络与防火墙**：确保服务器和客户端之间的网络是连通的，并且防火墙没有阻止 FTP 流量（实验中我们已临时关闭）。
*   **SELinux 上下文**：如果开启了 SELinux，共享目录（`/var/ftp/share/soft`）需要具有正确的 SELinux 文件上下文（如 `public_content_t`），否则客户端可能无法访问。使用 `setenforce 0` 可以暂时规避此问题。

![](img/4995248c39faf0f51cb20216cd21fc74_31.png)

![](img/4995248c39faf0f51cb20216cd21fc74_33.png)

## 总结

![](img/4995248c39faf0f51cb20216cd21fc74_35.png)

![](img/4995248c39faf0f51cb20216cd21fc74_37.png)

本节课我们一起学习了如何搭建一个基于 FTP 协议的 YUM 源服务器和客户端。我们回顾了从安装服务软件、创建共享目录、启动服务，到在客户端编写配置文件并验证的完整流程。理解这个过程对于在局域网内高效地管理软件分发至关重要。下一节，我们将探讨文件归档、压缩以及计划任务等系统管理任务。