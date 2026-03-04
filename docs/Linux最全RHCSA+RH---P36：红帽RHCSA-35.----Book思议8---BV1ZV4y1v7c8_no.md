# Linux软件包管理：P36：源码包管理与systemd服务管理 🐧

![](img/c4fc53130b817b90b6456a0a37b2fb0b_1.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_3.png)

在本节课中，我们将学习两种重要的软件管理方式：源码包安装和systemd服务管理。源码包安装提供了高度的自定义能力，而systemd则是现代Linux系统中管理服务的核心工具。掌握这两项技能，对于深入理解Linux系统至关重要。

![](img/c4fc53130b817b90b6456a0a37b2fb0b_5.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_7.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_9.png)

## 源码包安装方式详解 🔧

上一节我们介绍了RPM包管理，它虽然方便，但无法自定义安装路径和功能。本节中我们来看看源码包安装，它为我们提供了完全的控制权。

![](img/c4fc53130b817b90b6456a0a37b2fb0b_11.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_13.png)

### 源码包安装的核心思想

![](img/c4fc53130b817b90b6456a0a37b2fb0b_15.png)

源码包安装允许用户自定义软件的安装路径和功能模块。这与RPM包安装形成对比，RPM包安装后，软件文件会分散在系统的各个默认目录中，不便于集中管理。

![](img/c4fc53130b817b90b6456a0a37b2fb0b_17.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_19.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_21.png)

例如，使用RPM安装`vsftpd`后，其文件分散在`/etc`、`/var`等多个目录。而源码安装可以指定将所有相关文件安装到单一目录，如`/usr/local/software_name`下，便于后续管理和维护。

![](img/c4fc53130b817b90b6456a0a37b2fb0b_23.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_25.png)

### 源码包安装完整流程

![](img/c4fc53130b817b90b6456a0a37b2fb0b_27.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_29.png)

源码包安装遵循一个标准的流程。以下是详细的步骤：

1.  **下载源码包**
    *   从软件官方网站下载对应的源码压缩包。例如，Nginx的官网是 `nginx.org`。
    *   下载方式可以是浏览器直接下载，也可以在终端使用 `wget` 命令：
        ```bash
        wget https://nginx.org/download/nginx-1.20.2.tar.gz
        ```

![](img/c4fc53130b817b90b6456a0a37b2fb0b_31.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_33.png)

2.  **安装编译依赖**
    *   源码编译需要特定的开发工具和库，这些称为“依赖”。
    *   **最佳实践**：依赖包通常使用YUM等包管理器安装，而不是源码编译，以节省时间。
    *   所需依赖通常可以在官方文档中找到。例如，Nginx编译可能需要 `pcre-devel`、`zlib-devel`、`openssl-devel` 等包。
        ```bash
        yum install -y pcre-devel zlib-devel openssl-devel
        ```

![](img/c4fc53130b817b90b6456a0a37b2fb0b_35.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_37.png)

3.  **解压源码包**
    *   下载的源码包通常是 `.tar.gz` 格式的压缩包，需要先解压。
        ```bash
        tar -zxvf nginx-1.20.2.tar.gz
        cd nginx-1.20.2
        ```

![](img/c4fc53130b817b90b6456a0a37b2fb0b_39.png)

4.  **配置编译参数（核心步骤）**
    *   进入解压后的目录，执行 `./configure` 脚本进行配置。
    *   此步骤可以指定安装路径、启用或禁用特定功能模块。
    *   使用 `--help` 参数查看所有可配置选项：
        ```bash
        ./configure --help
        ```
    *   典型配置命令示例（指定安装路径和SSL模块）：
        ```bash
        ./configure --prefix=/usr/local/nginx --with-http_ssl_module
        ```
        *   `--prefix=/usr/local/nginx`：定义软件安装目录。
        *   `--with-http_ssl_module`：启用SSL加密功能模块。
    *   执行此命令会检查系统环境，如果缺少依赖会报错，成功则生成编译所需的Makefile文件。

5.  **编译源代码**
    *   使用 `make` 命令将源代码编译成计算机可执行的二进制文件。
        ```bash
        make
        ```
    *   此过程可能耗时较长，取决于软件规模和系统性能。

![](img/c4fc53130b817b90b6456a0a37b2fb0b_41.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_43.png)

6.  **安装软件**
    *   将编译好的文件复制到第4步中指定的安装目录。
        ```bash
        make install
        ```

7.  **管理软件**
    *   安装完成后，所有相关文件都位于指定的安装目录（如 `/usr/local/nginx`）下。
    *   启动、停止等操作需要运行该目录下的特定脚本，例如：
        ```bash
        /usr/local/nginx/sbin/nginx          # 启动
        /usr/local/nginx/sbin/nginx -s stop  # 停止
        ```

![](img/c4fc53130b817b90b6456a0a37b2fb0b_45.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_47.png)

### 源码包安装的优缺点总结

![](img/c4fc53130b817b90b6456a0a37b2fb0b_49.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_51.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_53.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_55.png)

*   **优点**：高度自定义、可优化、能安装最新或特定版本。
*   **缺点**：步骤繁琐、需手动解决依赖、编译耗时。

---

![](img/c4fc53130b817b90b6456a0a37b2fb0b_57.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_59.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_61.png)

## systemd 服务管理 ⚙️

![](img/c4fc53130b817b90b6456a0a37b2fb0b_63.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_65.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_67.png)

上一节我们完成了软件的安装，无论是RPM包还是源码包。本节中我们来看看如何使用systemd来管理系统服务的生命周期。

![](img/c4fc53130b817b90b6456a0a37b2fb0b_69.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_71.png)

systemd是系统启动后的第一个进程（PID=1），它提供了一套强大的命令来管理所有由RPM包安装的服务（通常不适用于源码安装的服务）。

![](img/c4fc53130b817b90b6456a0a37b2fb0b_73.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_75.png)

### 核心管理命令

以下是使用 `systemctl` 命令管理服务的基本操作：

![](img/c4fc53130b817b90b6456a0a37b2fb0b_77.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_79.png)

1.  **启动服务**
    ```bash
    systemctl start vsftpd
    ```

2.  **停止服务**
    ```bash
    systemctl stop vsftpd
    ```

![](img/c4fc53130b817b90b6456a0a37b2fb0b_81.png)

3.  **重启服务**
    *   如果服务未运行，此命令会启动它；如果已运行，则会先停止再启动，常用于使配置生效。
    ```bash
    systemctl restart vsftpd
    ```

![](img/c4fc53130b817b90b6456a0a37b2fb0b_83.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_85.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_87.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_89.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_91.png)

4.  **查看服务状态**
    ```bash
    systemctl status vsftpd
    ```
    *   输出信息包括服务是否正在运行（`active (running)`）、启动时间以及相关的日志片段。

5.  **设置服务开机自启**
    ```bash
    systemctl enable vsftpd
    ```
    *   此命令会在系统启动的特定目录下创建一个符号链接，确保系统启动时自动运行该服务。

![](img/c4fc53130b817b90b6456a0a37b2fb0b_93.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_95.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_97.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_99.png)

6.  **取消服务开机自启**
    ```bash
    systemctl disable vsftpd
    ```

![](img/c4fc53130b817b90b6456a0a37b2fb0b_101.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_103.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_105.png)

7.  **查看服务是否开机自启**
    ```bash
    systemctl is-enabled vsftpd
    ```

![](img/c4fc53130b817b90b6456a0a37b2fb0b_107.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_109.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_111.png)

### 查看系统运行的服务

![](img/c4fc53130b817b90b6456a0a37b2fb0b_113.png)

除了管理单个服务，我们还需要工具来查看系统中正在运行哪些服务。

1.  **通过端口查看**
    *   服务通常会监听特定的网络端口。使用 `ss` 或 `netstat` 命令可以查看端口占用情况。
    ```bash
    ss -nptul
    # 或
    netstat -nptul
    ```
    *   结合 `grep` 过滤特定服务（如查看Nginx是否启动）：
    ```bash
    ss -nptul | grep :80
    ```

![](img/c4fc53130b817b90b6456a0a37b2fb0b_115.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_117.png)

2.  **通过进程查看**
    *   使用 `ps` 命令配合过滤，可以查看服务的进程信息。
    ```bash
    ps aux | grep nginx
    ```

![](img/c4fc53130b817b90b6456a0a37b2fb0b_119.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_121.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_123.png)

---

## 课程总结 📚

![](img/c4fc53130b817b90b6456a0a37b2fb0b_125.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_127.png)

本节课中我们一起学习了Linux中两种高级的软件管理方式。

*   **源码包管理**：我们掌握了从下载、解压、解决依赖、配置、编译到安装的完整流程。关键在于使用 `./configure --prefix=路径` 来自定义安装位置，这为特定生产环境下的软件部署提供了灵活性。
*   **systemd服务管理**：我们学习了使用 `systemctl` 命令对服务进行启动(`start`)、停止(`stop`)、重启(`restart`)、查看状态(`status`)以及设置开机自启(`enable`/`disable`)等核心操作。这是日常运维中最常用的服务管理工具。

![](img/c4fc53130b817b90b6456a0a37b2fb0b_129.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_131.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_133.png)

![](img/c4fc53130b817b90b6456a0a37b2fb0b_135.png)

理解源码安装有助于你深入软件内部，而熟练使用systemd则是高效管理系统服务的基石。请务必通过实践练习来巩固这些知识。