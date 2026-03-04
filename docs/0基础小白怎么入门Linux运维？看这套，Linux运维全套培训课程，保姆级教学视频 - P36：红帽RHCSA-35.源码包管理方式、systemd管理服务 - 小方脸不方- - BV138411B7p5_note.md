# Linux运维全套培训课程：P36：源码包管理方式与systemd服务管理 🐧

![](img/fe0ba032d0128f5dc5735148f3f11f3c_1.png)

在本节课中，我们将学习Linux中两种重要的软件管理方式：源码包的编译安装，以及使用systemd管理系统服务。源码包安装提供了高度的自定义能力，而systemd则是现代Linux系统管理服务的核心工具。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_3.png)

## 源码包管理方式 🔧

![](img/fe0ba032d0128f5dc5735148f3f11f3c_5.png)

上一节我们介绍了RPM和YUM包管理，它们方便快捷。本节中我们来看看另一种更灵活但稍显复杂的软件安装方式——源码包安装。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_7.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_9.png)

源码包在整个学习阶段使用不多，应用场景不如RPM广泛，所以我们将其放在最后讲解。但有的企业确实需要你以这种自定义的方式去安装和优化软件。我们以NGINX这款软件为例，有的企业就需要我们自定义安装路径与功能。

一旦遇到需要自定义安装软件路径和功能的需求，我们都可以通过源码包的方式去安装。

### 源码包与二进制包的区别

![](img/fe0ba032d0128f5dc5735148f3f11f3c_11.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_13.png)

毕竟前面我们安装的软件包，例如最熟悉的VSFTPD，安装完成后我们无法控制软件相关文件在系统的哪个路径存放，也无法控制把软件安装在系统的哪个位置。这让管理显得不便。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_15.png)

安装完成后，你会发现软件相关的文件分散存储在系统的不同位置。例如，有的在`/var`目录下，有的在`/etc`目录下。这让我们有些不知所措，因为想管理这个软件时，需要记住多个路径。

如果一款软件能够被限制安装在我们指定的路径，例如`/opt`或`/usr/local`目录下，那么以后管理这款软件时，就可以直接来到这个路径，找到该软件相关的所有文件，而不需要去多个路径查找。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_17.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_19.png)

因此，源码包在企业中一旦遇到需要自定义安装的情况，就会派上用场。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_21.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_23.png)

### 源码包安装流程

![](img/fe0ba032d0128f5dc5735148f3f11f3c_25.png)

源码包安装大体需要以下流程：

![](img/fe0ba032d0128f5dc5735148f3f11f3c_27.png)

1.  **获取源码包**：从软件官方网站下载。
2.  **安装依赖**：安装该软件编译和运行所需的依赖包。
3.  **解压源码包**：通常源码包是压缩包格式。
4.  **配置编译选项**：进入解压后的目录，执行配置脚本，指定安装路径、启用功能模块等。
5.  **编译**：将源代码转换为计算机可执行的二进制文件。
6.  **安装**：将编译好的文件复制到指定的安装路径。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_29.png)

以下是具体步骤的详细说明：

#### 第一步：下载源码包

源码包一般从官方网站下载。我们以NGINX为例，后期学习服务时，会带领大家认识对应的官方网站。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_31.png)

去到NGINX的官方网站（nginx.org），在下载（Download）页面，通常有主线版本（Mainline）、稳定版本（Stable）和旧版本（Legacy）。我们选择稳定版本进行下载。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_33.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_35.png)

下载方式有两种：
*   通过浏览器直接点击下载。
*   在Linux系统中使用`wget`命令下载，例如：
    ```bash
    wget https://nginx.org/download/nginx-1.20.2.tar.gz
    ```
    `wget`命令可以从互联网下载文件到本地。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_37.png)

#### 第二步：安装依赖与解压

![](img/fe0ba032d0128f5dc5735148f3f11f3c_39.png)

在安装软件前，需要先安装其依赖。源码包的依赖信息通常在官方文档中说明。对于NGINX，其依赖可能包括`pcre`、`zlib`、`openssl`等。

**核心思想**：依赖包本身功能固定，没有必要进行复杂的源码编译。通常我们使用YUM来安装依赖包，这样更省事。例如：
```bash
yum install -y pcre pcre-devel zlib zlib-devel openssl openssl-devel
```
其中，`-devel`包（开发包）通常包含编译时所需的头文件和库。

下载的源码包通常是压缩包（如`.tar.gz`），需要先解压：
```bash
tar -zxvf nginx-1.20.2.tar.gz
```
解压后会生成一个目录（如`nginx-1.20.2`），里面包含了所有源代码文件。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_41.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_43.png)

#### 第三步：配置、编译与安装

进入解压后的源码目录：
```bash
cd nginx-1.20.2
```

**配置**：执行目录中的`configure`脚本。此脚本用于检测系统环境，并允许我们指定安装参数。
*   使用`--help`查看所有可配置选项：
    ```bash
    ./configure --help
    ```
*   常用配置示例，指定安装路径和启用SSL模块：
    ```bash
    ./configure --prefix=/usr/local/nginx --with-http_ssl_module
    ```
    *   `--prefix=/usr/local/nginx`：指定软件安装目录。
    *   `--with-http_ssl_module`：启用HTTP SSL模块。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_45.png)

执行配置命令后，如果没有报错（即没有出现`error`关键字），则表示环境检测通过，可以继续。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_47.png)

**编译**：使用`make`命令将源代码编译成二进制文件。
```bash
make
```
此过程会处理`src`目录下的所有源代码文件（如`.c`文件）。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_49.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_51.png)

**安装**：使用`make install`命令将编译好的文件安装到之前`--prefix`指定的路径。
```bash
make install
```
安装过程主要是将当前目录下编译好的文件复制到目标安装目录。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_53.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_55.png)

安装完成后，软件的所有相关文件都会集中在`/usr/local/nginx`目录下，便于管理。

#### 关于后续管理与模块增删

![](img/fe0ba032d0128f5dc5735148f3f11f3c_57.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_59.png)

*   **管理**：源码安装的软件，通常有其自己的管理脚本。例如NGINX，管理命令位于安装目录下的`sbin/nginx`。
*   **新增模块**：如果需要为已安装的软件新增模块，需要重新回到源码目录，在`configure`步骤中**同时加上旧的配置参数和新的模块参数**，然后执行`make`编译。**注意**：通常只需编译，不要再次执行`make install`，否则会覆盖原有安装。编译后，将新生成的二进制文件（通常在`objs`目录下）替换旧的文件即可。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_61.png)

### 源码安装流程总结

![](img/fe0ba032d0128f5dc5735148f3f11f3c_63.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_65.png)

1.  从官网下载源码包。
2.  查阅官方文档，使用YUM安装所需依赖包。
3.  解压源码包。
4.  进入解压目录，执行`./configure`，指定安装路径和功能。
5.  执行`make`编译。
6.  执行`make install`安装。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_67.png)

RPM安装方便，所以很多人喜欢。源码安装对初学者可能不太友好，觉得复杂，但用习惯后，就能体会到其灵活性的优势。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_69.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_71.png)

---

## systemd管理服务 ⚙️

![](img/fe0ba032d0128f5dc5735148f3f11f3c_73.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_75.png)

上一节我们完成了软件的安装，本节中我们来看看如何管理系统中的服务。`systemd`是现代Linux系统（如CentOS 7/8）中初始化系统和服务管理的核心。

`systemd`本身是系统内核启动后的第一个进程（PID=1），它提供了许多命令来管理系统的众多服务，例如启动、停止、重启、查看状态、设置开机自启等。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_77.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_79.png)

### 查看系统服务与端口

在管理服务前，有时我们需要查看系统当前运行了哪些服务。服务通常会监听网络端口。

以下是两条常用的查看端口（即服务）信息的命令，功能类似：

*   **`ss`命令**（推荐，较新）：
    ```bash
    ss -ntulp
    ```
*   **`netstat`命令**（传统）：
    ```bash
    netstat -ntulp
    ```
    （如果系统未安装，可使用`yum install net-tools`安装）

![](img/fe0ba032d0128f5dc5735148f3f11f3c_81.png)

**命令选项说明**：
*   `-n`：以数字形式显示地址和端口。
*   `-t`：显示TCP连接。
*   `-u`：显示UDP连接。
*   `-l`：仅显示监听状态的套接字。
*   `-p`：显示占用端口的进程信息。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_83.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_85.png)

执行后，可以看到类似下面的输出，其中`LISTEN`状态的行代表正在运行的服务：
```
State      Recv-Q Send-Q Local Address:Port  Peer Address:Port
LISTEN     0      128          *:22               *:*        users:(("sshd",pid=1234,fd=3))
LISTEN     0      128          *:80               *:*        users:(("nginx",pid=5678,fd=6))
```
这表示SSH服务（`sshd`）运行在22端口，NGINX服务（`nginx`）运行在80端口。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_87.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_89.png)

我们常结合`grep`来过滤特定服务：
```bash
ss -ntulp | grep 80
netstat -ntulp | grep nginx
```

![](img/fe0ba032d0128f5dc5735148f3f11f3c_91.png)

### 使用systemctl管理服务

![](img/fe0ba032d0128f5dc5735148f3f11f3c_93.png)

`systemctl`是`systemd`的主要管理工具。**请注意**：以下`systemctl`命令主要适用于通过RPM或YUM安装的服务。源码安装的服务通常有自己的管理方式。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_95.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_97.png)

以下是常用的服务管理命令：

![](img/fe0ba032d0128f5dc5735148f3f11f3c_99.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_101.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_103.png)

*   **启动服务**：
    ```bash
    systemctl start vsftpd
    ```

![](img/fe0ba032d0128f5dc5735148f3f11f3c_105.png)

*   **停止服务**：
    ```bash
    systemctl stop vsftpd
    ```

![](img/fe0ba032d0128f5dc5735148f3f11f3c_107.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_109.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_111.png)

*   **重启服务**：如果服务已修改配置，需要重启生效。此命令也适用于启动未运行的服务。
    ```bash
    systemctl restart vsftpd
    ```

![](img/fe0ba032d0128f5dc5735148f3f11f3c_113.png)

*   **查看服务状态**：
    ```bash
    systemctl status vsftpd
    ```
    输出中`Active`行显示为`active (running)`则表示服务正在运行，同时会显示启动时间和主配置文件路径。

*   **设置服务开机自动启动**：
    ```bash
    systemctl enable vsftpd
    ```
    此命令会在系统启动的相应目录（如`/etc/systemd/system/multi-user.target.wants/`）创建服务的符号链接。

*   **取消服务开机自动启动**：
    ```bash
    systemctl disable vsftpd
    ```
    此命令会移除上述的符号链接。

*   **查看服务是否开机自启**：
    ```bash
    systemctl is-enabled vsftpd
    ```
    输出`enabled`表示是，`disabled`表示否。

### 服务单元文件

![](img/fe0ba032d0128f5dc5735148f3f11f3c_115.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_117.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_119.png)

当使用`systemctl enable`启用一个服务时，`systemd`会读取对应的服务单元文件（Service Unit File），通常位于`/usr/lib/systemd/system/`目录下，例如`vsftpd.service`。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_121.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_123.png)

这个文件定义了如何启动、停止和管理该服务，包括：
*   服务描述。
*   依赖关系。
*   启动命令和配置文件路径。
*   运行用户等。

一般我们无需手动修改这些文件，了解其存在和作用即可。

---

## 总结 📚

![](img/fe0ba032d0128f5dc5735148f3f11f3c_125.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_127.png)

本节课中我们一起学习了Linux中两种关键的软件管理知识：

1.  **源码包管理**：我们掌握了从下载、解决依赖、配置、编译到安装的完整流程。源码安装提供了**自定义安装路径和软件功能模块**的能力，虽然步骤稍多，但灵活性极高，适用于有特定定制化需求的企业环境。关键在于理解`./configure`、`make`、`make install`这三个核心步骤。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_129.png)

2.  **systemd服务管理**：我们学习了使用`systemctl`命令对系统服务进行**启动**、**停止**、**重启**、**查看状态**以及**设置开机自启**等日常操作。同时，也了解了如何使用`ss`或`netstat`命令查看服务所占用的网络端口。这是运维工作中每天都会用到的基础技能。

![](img/fe0ba032d0128f5dc5735148f3f11f3c_131.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_133.png)

![](img/fe0ba032d0128f5dc5735148f3f11f3c_135.png)

软件包管理和服务管理是Linux运维的基础，需要大家多加练习，熟练掌握。