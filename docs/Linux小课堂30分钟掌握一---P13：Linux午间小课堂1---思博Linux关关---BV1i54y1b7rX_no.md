# Linux小课堂：P13：Apache三部曲第三节课 - Apache HTTPD服务安装与配置 🚀

在本节课中，我们将要学习如何安装和配置Apache HTTPD服务。这是Apache三部曲的最后一节课，也是最重要的一课。我们将从检查服务是否已安装开始，逐步讲解安装步骤、核心配置文件解析、服务的启动与停止，并最终通过浏览器验证服务是否成功运行。

## 概述

Apache HTTPD服务，通常简称为Apache服务，是Apache软件基金会下的一个Web服务器软件。它主要用于代理和转发Web业务请求。本节课的目标是让初学者能够独立完成Apache服务的安装、基础配置和验证。

![](img/8ce1f93788ec6e378138193caed56743_1.png)

## 检查Apache服务是否已安装

![](img/8ce1f93788ec6e378138193caed56743_3.png)

![](img/8ce1f93788ec6e378138193caed56743_4.png)

在安装之前，我们需要先确认Linux服务器上是否已经安装了Apache HTTPD服务。有两种常用的检查方法。

![](img/8ce1f93788ec6e378138193caed56743_6.png)

### 方法一：使用RPM命令检查

![](img/8ce1f93788ec6e378138193caed56743_8.png)

RPM是Red Hat系列Linux发行版的包管理工具。我们可以使用以下命令来检查是否安装了相关的包：

```bash
rpm -qa | grep httpd
```

![](img/8ce1f93788ec6e378138193caed56743_10.png)

![](img/8ce1f93788ec6e378138193caed56743_11.png)

如果服务器上已经安装了`httpd`相关的RPM包，该命令会列出完整的包名。

### 方法二：使用YUM命令检查

![](img/8ce1f93788ec6e378138193caed56743_13.png)

YUM是另一个包管理工具，它基于RPM，但能自动处理依赖关系。我们可以使用以下命令来检查已安装的包：

```bash
yum list installed | grep httpd
```

![](img/8ce1f93788ec6e378138193caed56743_15.png)

这个命令会列出所有已安装的、包名中包含“httpd”关键字的软件包。其中，最核心的包通常是`httpd.x86_64`。

![](img/8ce1f93788ec6e378138193caed56743_17.png)

![](img/8ce1f93788ec6e378138193caed56743_19.png)

**执行示例：**
如果执行上述命令后能看到类似`httpd.x86_64`的包，说明服务已安装。如果没有任何输出，则需要进行安装。

![](img/8ce1f93788ec6e378138193caed56743_21.png)

## 安装Apache HTTPD服务

![](img/8ce1f93788ec6e378138193caed56743_23.png)

如果检查发现系统尚未安装Apache服务，我们可以使用YUM工具进行安装。

使用以下命令进行安装，`-y`参数表示在安装过程中对所有提示自动回答“yes”：

```bash
yum install -y httpd
```

![](img/8ce1f93788ec6e378138193caed56743_25.png)

![](img/8ce1f93788ec6e378138193caed56743_26.png)

安装完成后，我们就拥有了Apache HTTPD服务的核心软件，可以开始进行配置。

![](img/8ce1f93788ec6e378138193caed56743_28.png)

## 管理Apache服务

![](img/8ce1f93788ec6e378138193caed56743_30.png)

安装完成后，我们需要知道如何启动、停止服务以及设置开机自启。Linux系统提供了`systemctl`命令来管理系统服务。

### 设置开机自启

![](img/8ce1f93788ec6e378138193caed56743_32.png)

为了让Apache服务在服务器重启后自动运行，需要将其设置为开机自启：

```bash
systemctl enable httpd
```

执行后，可以使用以下命令查看服务状态，确认`enabled`字样出现：

```bash
systemctl status httpd
```

### 启动与停止服务

要手动启动或停止Apache服务，可以使用以下命令：

*   **启动服务：** `systemctl start httpd`
*   **停止服务：** `systemctl stop httpd`
*   **重启服务：** `systemctl restart httpd`

除了使用`systemctl`，Apache自身也提供了控制命令。

## Apache服务控制命令

Apache安装后，会提供两个主要的控制命令：`httpd`和`apachectl`。

*   `httpd`：是编译好的二进制可执行文件。
*   `apachectl`：是一个Shell脚本，内部会调用`httpd`命令，可以看作是`httpd`命令的封装，两者最终效果相同。

我们可以使用`httpd -h`或`httpd --help`来查看`httpd`命令的帮助信息。其中最常用的参数是`-k`：

```bash
httpd -k start    # 启动服务
httpd -k stop     # 停止服务
httpd -k restart  # 重启服务
httpd -k graceful # 优雅重启（重新加载配置文件，不中断现有连接）
```

另一个重要参数是`-t`，用于检查配置文件的语法错误，在修改配置文件后，建议先执行此命令进行验证：

```bash
httpd -t
```

如果语法正确，会显示“Syntax OK”。

## Apache核心配置文件解析

Apache服务的主要配置文件位于`/etc/httpd/`目录下。核心配置文件是`/etc/httpd/conf/httpd.conf`。理解其中的关键参数是配置Apache的基础。

以下是配置文件中一些关键指令的解析：

![](img/8ce1f93788ec6e378138193caed56743_34.png)

![](img/8ce1f93788ec6e378138193caed56743_36.png)

*   **IncludeOptional conf.d/*.conf**
    此指令用于加载`/etc/httpd/conf.d/`目录下所有以`.conf`结尾的配置文件。这样可以将不同网站的配置分离到独立文件中，便于管理。

![](img/8ce1f93788ec6e378138193caed56743_38.png)

![](img/8ce1f93788ec6e378138193caed56743_40.png)

![](img/8ce1f93788ec6e378138193caed56743_42.png)

*   **ServerName www.example.com:80**
    定义服务器的主机名和端口。`www.example.com`应替换为你的域名，`80`是HTTP默认端口，如果使用默认端口，可以省略`:80`。

![](img/8ce1f93788ec6e378138193caed56743_44.png)

*   **DocumentRoot “/var/www/html”**
    定义网站根目录，即存放网页文件（如HTML、PHP代码）的位置。Apache会从这个目录读取文件并返回给客户端。

![](img/8ce1f93788ec6e378138193caed56743_46.png)

![](img/8ce1f93788ec6e378138193caed56743_47.png)

*   **Directory 指令块**
    此指令用于对特定目录设置访问权限。例如：
    ```
    <Directory "/var/www/html">
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>
    ```
    *   `Options Indexes`：如果目录中没有默认首页文件（如index.html），则显示该目录的文件列表。
    *   `Require all granted`：允许所有客户端访问此目录。

![](img/8ce1f93788ec6e378138193caed56743_49.png)

![](img/8ce1f93788ec6e378138193caed56743_51.png)

*   **ErrorLog 和 CustomLog**
    这两个指令用于配置日志文件。
    *   `ErrorLog “logs/error_log”`：指定错误日志的存放路径。
    *   `CustomLog “logs/access_log” combined`：指定访问日志的存放路径和格式（`combined`是一种预定义的日志格式）。

![](img/8ce1f93788ec6e378138193caed56743_53.png)

![](img/8ce1f93788ec6e378138193caed56743_54.png)

![](img/8ce1f93788ec6e378138193caed56743_56.png)

## 实践：配置与验证

![](img/8ce1f93788ec6e378138193caed56743_58.png)

![](img/8ce1f93788ec6e378138193caed56743_60.png)

上一节我们介绍了配置文件的核心参数，本节中我们通过一个简单的实践来验证Apache服务。

1.  **启动Apache服务**
    确保服务已启动：
    ```bash
    systemctl start httpd
    # 或
    httpd -k start
    ```

![](img/8ce1f93788ec6e378138193caed56743_62.png)

2.  **检查服务状态**
    使用`systemctl status httpd`或`ps aux | grep httpd`查看进程是否在运行。

3.  **访问测试页面**
    在服务器的`/var/www/html/`目录下，默认可能有一个`index.html`测试文件。打开浏览器，输入服务器的IP地址（例如 `http://192.168.1.100`）。
    如果看到Apache的测试页面或目录列表，说明服务安装配置成功。

![](img/8ce1f93788ec6e378138193caed56743_64.png)

4.  **测试目录列表功能**
    如果`/var/www/html/`目录下没有`index.html`文件，根据配置（`Options Indexes`），浏览器会显示该目录的文件列表。你可以创建一个新文件来测试：
    ```bash
    touch /var/www/html/test.txt
    ```
    刷新浏览器页面，应该能看到`test.txt`文件出现在列表中。

![](img/8ce1f93788ec6e378138193caed56743_66.png)

## 总结

本节课中我们一起学习了Apache HTTPD服务的完整安装与配置流程。我们从检查服务状态开始，使用YUM工具完成了安装，并学会了使用`systemctl`和`httpd`命令管理服务生命周期。我们深入解析了`/etc/httpd/conf/httpd.conf`核心配置文件中的关键指令，理解了Web根目录、访问控制、虚拟主机和日志配置等概念。最后，我们通过启动服务和访问测试页面，验证了整个安装配置过程是成功的。

![](img/8ce1f93788ec6e378138193caed56743_68.png)

掌握这些基础知识，你就已经能够搭建并运行一个基本的Apache Web服务器了。这对于后续学习更复杂的Web应用部署至关重要。