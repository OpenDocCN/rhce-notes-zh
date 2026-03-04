# Linux运维：P35：源码包管理与systemd服务管理

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_1.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_3.png)

在本节课中，我们将学习Linux中源码包的安装方式以及如何使用systemd来管理系统服务。源码包安装提供了高度的自定义能力，而systemd则是现代Linux系统中管理服务的核心工具。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_5.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_7.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_9.png)

## 源码包安装方式

上一节我们介绍了RPM包管理，本节中我们来看看源码包的安装方式。源码包在整个学习阶段使用频率不如RPM包广泛，但它在企业环境中仍有其特定价值。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_11.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_12.png)

### 源码包的应用场景

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_14.png)

有些企业需要以自定义的方式安装和配置软件。例如，企业可能需要自定义软件的安装路径或选择性地启用/禁用特定功能。RPM包安装方式无法满足这类需求，因为RPM包安装后，软件相关的文件会分散存储在系统的不同位置，不便于集中管理。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_16.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_18.png)

源码包安装则允许我们完全控制软件的安装路径和功能模块。这样，软件的所有相关文件都会集中存放在我们指定的目录下，便于后续的管理和维护。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_20.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_22.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_24.png)

### 源码包安装流程概述

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_26.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_28.png)

源码包安装大体遵循以下流程：
1.  从软件官方网站下载源码包。
2.  安装该软件所需的依赖包。
3.  解压源码包。
4.  进入解压后的目录，使用配置脚本指定安装路径和功能。
5.  编译源代码。
6.  执行安装。

接下来，我们以安装Nginx软件为例，详细讲解每个步骤。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_30.png)

### 详细安装步骤

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_32.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_34.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_36.png)

以下是源码包安装的具体操作步骤。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_38.png)

**第一步：下载源码包**
访问软件的官方网站（例如Nginx官网 `nginx.org`），找到下载页面。通常网站会提供主线版本、稳定版本和旧版本。建议下载稳定版本的源码包。可以使用 `wget` 命令直接在Linux系统中下载。
```bash
wget https://nginx.org/download/nginx-1.20.2.tar.gz
```

**第二步：安装依赖包**
在编译安装软件前，需要先安装其依赖的软件包。依赖信息通常可以在官方文档中找到。对于依赖包，建议直接使用yum安装，这比源码编译依赖包要简便得多。
例如，Nginx可能需要 `pcre`、`zlib`、`openssl` 等开发包。
```bash
yum install -y pcre-devel zlib-devel openssl-devel
```

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_40.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_42.png)

**第三步：解压源码包**
使用 `tar` 命令解压下载的源码压缩包。
```bash
tar -zxvf nginx-1.20.2.tar.gz
```

**第四步：配置安装选项**
进入解压后的目录，其中通常会有一个名为 `configure` 的配置脚本。执行此脚本可以指定安装路径、启用或禁用模块等功能。
*   使用 `--help` 参数可以查看所有可配置选项。
    ```bash
    cd nginx-1.20.2
    ./configure --help
    ```
*   一个常见的配置命令示例如下，它将安装路径指定为 `/usr/local/nginx`，并启用 `ssl` 模块。
    ```bash
    ./configure --prefix=/usr/local/nginx --with-http_ssl_module
    ```
    如果配置过程没有报错，会生成后续编译所需的Makefile文件。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_44.png)

**第五步：编译源代码**
使用 `make` 命令进行编译，这个过程会将源代码转换为计算机可执行的二进制文件。
```bash
make
```

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_46.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_48.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_50.png)

**第六步：执行安装**
使用 `make install` 命令将编译好的文件安装到第四步中 `--prefix` 指定的路径。
```bash
make install
```
安装完成后，所有Nginx相关的文件都会存放在 `/usr/local/nginx` 目录下。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_52.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_54.png)

### 源码包管理注意事项

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_56.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_58.png)

*   **依赖管理**：源码包本身不解决依赖关系，需要管理员手动处理。依赖包通常使用yum安装更为高效。
*   **后续管理**：源码安装的软件，其启动、停止等管理操作通常需要直接操作其自带的脚本或配置文件，与RPM包安装的软件使用systemd管理有所不同。
*   **功能增删**：若后期需要增加功能模块，需重新执行配置、编译步骤。通常只需重新 `make` 而不需要再次 `make install`，然后用新编译出的二进制文件替换旧文件即可。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_60.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_62.png)

## systemd 服务管理

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_64.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_66.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_68.png)

在安装好软件后，我们需要学习如何管理它们的服务。在RHEL/CentOS 7及以后版本中，`systemd` 是初始化系统和服务管理的核心。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_70.png)

### systemd 简介

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_72.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_74.png)

`systemd` 是系统启动后的第一个进程（PID=1），它负责启动和管理系统的其他所有服务。它提供了一系列命令来方便地控制服务。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_76.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_78.png)

### 服务状态查看

在管理服务之前，我们需要知道如何查看系统中正在运行的服务及其状态。
*   使用 `systemctl status <服务名>` 可以查看某个服务的详细状态，包括是否正在运行、启动时间以及相关的配置文件。
    ```bash
    systemctl status vsftpd
    ```
*   使用 `ss` 或 `netstat` 命令可以查看系统监听的网络端口，从而判断哪些服务已经启动。
    ```bash
    ss -antulp | grep :80
    # 或
    netstat -antulp | grep :80
    ```

### 服务生命周期管理

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_80.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_82.png)

以下是使用 `systemctl` 管理服务基本生命周期的常用命令。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_84.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_86.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_88.png)

**启动服务**
```bash
systemctl start vsftpd
```

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_90.png)

**停止服务**
```bash
systemctl stop vsftpd
```

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_92.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_94.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_96.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_98.png)

**重启服务**
`restart` 命令会先停止服务再启动。如果服务未运行，则该命令会启动它。
```bash
systemctl restart vsftpd
```

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_100.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_102.png)

**重新加载配置**
对于已经运行的服务，如果只修改了配置文件而不需要重启进程，可以使用 `reload` 命令让其重新加载配置。
```bash
systemctl reload vsftpd
```

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_104.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_106.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_108.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_110.png)

**查看服务状态**
```bash
systemctl status vsftpd
```

### 服务开机自启管理

我们还可以设置服务是否随系统开机自动启动。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_112.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_114.png)

**设置服务开机自启**
```bash
systemctl enable vsftpd
```
此命令会在系统相应的启动目录下创建服务的符号链接。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_116.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_118.png)

**禁用服务开机自启**
```bash
systemctl disable vsftpd
```
此命令会移除之前创建的符号链接。

**查看服务开机自启状态**
```bash
systemctl is-enabled vsftpd
```

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_120.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_122.png)

## 总结

本节课中我们一起学习了Linux中两种重要的软件管理方式。
1.  **源码包安装**：我们了解了其适用于需要高度自定义安装的场景，并详细演练了从下载、解压、配置、编译到安装的完整流程。关键在于理解 `./configure`、`make`、`make install` 这三个核心步骤。
2.  **systemd服务管理**：我们掌握了如何使用 `systemctl` 命令对通过RPM包安装的服务进行启动、停止、重启、查看状态以及设置开机自启等操作，这是日常运维中最常用的服务管理工具。

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_124.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_126.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_128.png)

![](img/3b0c12fd64d4de1fc83de5f1a6ab8131_130.png)

通过本章学习，你应能根据实际需求选择合适的软件安装方式，并有效地管理系统中的各项服务。