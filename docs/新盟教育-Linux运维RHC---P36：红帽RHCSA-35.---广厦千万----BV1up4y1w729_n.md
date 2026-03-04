# Linux运维RHCSA+RHCE培训教程：P36：源码包管理与systemd服务管理 🐧

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_1.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_3.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_5.png)

在本节课中，我们将要学习Linux系统中源码包的安装方式以及如何使用systemd来管理系统服务。源码包安装虽然不如RPM包应用广泛，但在需要自定义软件安装路径和功能时至关重要。同时，掌握systemd服务管理是运维工作的基础技能。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_7.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_9.png)

## 源码包安装方式 🔧

上一节我们介绍了RPM包管理，本节中我们来看看源码包的安装方式。源码包在整个学习阶段使用频率不如RPM包高，因此我们将其放在最后讲解。然而，在某些企业环境中，确实需要采用这种自定义方式来安装和配置软件。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_11.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_12.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_14.png)

例如，对于Nginx这款软件，有的企业就要求自定义其安装路径和功能模块。一旦遇到这类需求，我们就可以通过源码包的方式进行安装。这与我们之前使用yum安装的软件（如VSFTPD）不同，那些软件的文件会分散存储在系统的不同位置（如`/var`、`/etc`等），不便于集中管理。而源码安装允许我们将软件的所有相关文件安装到指定的目录（如`/opt`或`/usr/local`下），便于后续的管理和维护。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_16.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_18.png)

源码包安装大体遵循以下流程。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_20.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_22.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_24.png)

以下是源码包安装的核心步骤：
1.  **获取源码包**：从软件的官方网站下载对应的源码压缩包。
2.  **安装依赖**：根据官方文档，安装该软件编译和运行所需的依赖包。通常，依赖包可以直接使用yum安装，无需源码编译。
3.  **解压源码包**：使用解压命令（如`tar`）解压下载的源码压缩包，会生成一个包含源代码的目录。
4.  **配置编译选项**：进入解压后的源码目录，执行其中的`configure`脚本。此步骤可以指定安装路径（`--prefix`参数）和需要启用的功能模块。
5.  **编译源代码**：执行`make`命令，将人类可读的源代码编译成计算机可执行的二进制文件。
6.  **安装软件**：执行`make install`命令，将编译好的文件复制到之前指定的安装路径中。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_26.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_28.png)

### 实战演练：以Nginx为例

我们以Nginx为例，演示源码安装的完整过程。请注意，以下演示侧重于讲解方法和流程，具体的模块和参数需要根据实际需求参考官方文档。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_30.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_32.png)

1.  **下载源码包**：访问Nginx官方网站，找到下载页面，选择稳定版本（Stable version）的源码包，复制其下载链接。在Linux终端中使用`wget`命令下载。
    ```bash
    wget https://nginx.org/download/nginx-1.20.2.tar.gz
    ```

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_34.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_36.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_38.png)

2.  **安装依赖**：参考Nginx官方文档，常见的编译依赖包括`pcre-devel`、`zlib-devel`和`openssl-devel`。我们可以使用yum快速安装这些依赖包。
    ```bash
    yum -y install pcre-devel zlib-devel openssl-devel
    ```
    **核心思想**：对于依赖包，通常没有必要进行繁琐的源码编译，直接使用yum安装其对应的RPM包（通常是`-devel`结尾的开发包）是最高效的方式。

3.  **解压与配置**：解压源码包并进入目录。
    ```bash
    tar -zxvf nginx-1.20.2.tar.gz
    cd nginx-1.20.2
    ```
    执行`configure`脚本进行配置。例如，指定安装到`/usr/local/nginx`目录，并启用`ssl`模块。
    ```bash
    ./configure --prefix=/usr/local/nginx --with-http_ssl_module
    ```
    如果配置过程没有报错（即没有出现`error`关键字），说明环境检测通过。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_40.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_42.png)

4.  **编译与安装**：依次执行编译和安装命令。
    ```bash
    make
    make install
    ```
    执行完毕后，Nginx就会被安装到`/usr/local/nginx`目录下。该目录包含了Nginx的所有配置文件、程序文件和日志目录，便于集中管理。

5.  **管理软件**：安装完成后，可以通过其自带的脚本启动。
    ```bash
    /usr/local/nginx/sbin/nginx
    ```
    要查看安装时指定的参数和模块，可以执行：
    ```bash
    /usr/local/nginx/sbin/nginx -V
    ```

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_44.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_46.png)

**关于后续修改**：如果后期需要增加新的功能模块，需要重新回到源码目录，执行`configure`（包含所有原有参数及新模块参数）、`make`步骤。**注意**：通常只需替换新编译出的主程序文件（位于`objs`目录），而**不需要**再次执行`make install`，以免覆盖原有配置。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_48.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_50.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_52.png)

## systemd 服务管理 ⚙️

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_54.png)

上一节我们完成了软件的安装，本节中我们来看看如何管理系统中的服务。在RHEL/CentOS 7及以后版本中，`systemd`是默认的初始化系统和服务管理器，其进程PID为1。它提供了一系列命令来方便地控制服务。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_56.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_58.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_60.png)

服务管理主要涉及两个层面：一是手动控制服务的**启动、停止、重启**；二是设置服务是否在**系统开机时自动启动**。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_62.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_64.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_66.png)

### 查看系统服务与端口

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_68.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_70.png)

在管理服务前，我们常常需要查看哪些服务正在运行。除了`ps`命令，我们还可以通过查看端口来确认服务状态，因为服务通常会监听特定的网络端口。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_72.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_74.png)

以下是两个常用的查看网络连接和端口的命令：
*   `ss -antup` 或 `netstat -antup`：这两个命令功能类似，用于显示系统中所有TCP/UDP端口的监听状态。
    *   `-a`：显示所有连接和监听端口。
    *   `-n`：以数字形式显示地址和端口号。
    *   `-t`/`-u`：显示TCP/UDP连接。
    *   `-p`：显示占用端口的进程信息。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_76.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_78.png)

例如，查看Nginx（默认监听80端口）或SSH服务（默认监听22端口）是否运行：
```bash
ss -antup | grep :80
netstat -antup | grep :22
```

### 使用 systemctl 管理服务

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_80.png)

`systemctl`是`systemd`的主要管理工具。**请注意**，以下命令主要适用于通过RPM包安装的服务。对于源码安装的服务，通常需要使用其自带的控制脚本。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_82.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_84.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_86.png)

以下是`systemctl`的常用命令：
*   **启动服务**：`systemctl start service_name`
*   **停止服务**：`systemctl stop service_name`
*   **重启服务**：`systemctl restart service_name` (无论服务当前是否运行，此命令都适用)
*   **查看服务状态**：`systemctl status service_name` (查看服务是否运行、最近日志等信息)
*   **设置开机自启**：`systemctl enable service_name` (此操作会在`/etc/systemd/system/`目录下创建符号链接)
*   **取消开机自启**：`systemctl disable service_name`
*   **查看是否开机自启**：`systemctl is-enabled service_name`

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_88.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_90.png)

**示例：管理VSFTPD服务**
```bash
# 启动VSFTPD服务
systemctl start vsftpd

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_92.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_94.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_96.png)

# 查看服务状态，确认是否为 active (running)
systemctl status vsftpd

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_98.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_100.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_102.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_104.png)

# 设置VSFTPD开机自动启动
systemctl enable vsftpd

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_106.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_108.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_110.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_112.png)

# 确认开机自启设置
systemctl is-enabled vsftpd

# 取消开机自启
systemctl disable vsftpd

# 重启服务（例如修改配置文件后）
systemctl restart vsftpd
```

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_114.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_116.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_118.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_120.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_121.png)

## 总结 📚

本节课中我们一起学习了Linux中两种重要的高级管理技能。

首先，我们深入探讨了**源码包安装**的全流程。我们了解到，虽然过程比RPM安装复杂，涉及下载、解压、解决依赖、配置、编译和安装多个步骤，但它提供了极高的灵活性，允许我们自定义软件的安装路径和功能模块，特别适合有特定需求的企业环境。记住一个关键原则：依赖包尽量使用yum安装以简化流程。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_123.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_125.png)

其次，我们系统学习了如何使用**systemd**来管理系统的服务。通过`systemctl`命令，我们可以轻松地启动、停止、重启服务，并管理其开机自启行为。同时，掌握了使用`ss`或`netstat`命令结合端口查看来监控服务运行状态的方法。

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_127.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_129.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_131.png)

![](img/a1dba300fcbaa8854ff04833c9cc7c3c_133.png)

软件包管理和服务管理是Linux系统运维的核心基础，需要大家课后多加练习，熟练掌握。