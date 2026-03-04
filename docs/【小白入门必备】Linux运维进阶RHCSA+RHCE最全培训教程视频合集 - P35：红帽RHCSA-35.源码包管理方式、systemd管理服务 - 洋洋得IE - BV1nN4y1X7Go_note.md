# Linux运维进阶：P35：源码包管理与systemd服务管理 🔧

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_1.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_3.png)

在本节课中，我们将学习两种重要的软件管理方式：源码包安装和systemd服务管理。源码包安装提供了高度的自定义能力，而systemd则是现代Linux系统中管理服务的核心工具。掌握这两项技能，对于进行高级运维工作至关重要。

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_5.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_7.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_9.png)

## 源码包安装方式 📦

上一节我们介绍了RPM和YUM包管理，它们虽然方便，但无法自定义软件的安装路径和功能模块。本节中我们来看看源码包安装，它为我们提供了这种灵活性。

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_11.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_13.png)

源码包安装允许我们自定义软件的安装路径和选择需要编译的功能模块。这种方式在企业中，当需要优化软件或满足特定部署需求时，会被采用。

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_15.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_17.png)

### 源码包安装的核心流程

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_19.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_21.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_23.png)

以下是源码包安装的标准步骤：

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_25.png)

1.  **下载源码包**：从软件的官方网站获取源代码压缩包。
2.  **安装依赖包**：根据官方文档，安装该软件编译和运行所必需的其他软件包。依赖包通常使用YUM安装以简化流程。
3.  **解压源码包**：使用解压命令（如 `tar`）释放源代码文件。
4.  **配置编译选项**：进入解压后的目录，执行 `./configure` 脚本。在此步骤中，可以指定安装路径（`--prefix`）和需要启用的功能模块。
5.  **编译源代码**：执行 `make` 命令，将人类可读的源代码转换为计算机可执行的二进制文件。
6.  **安装软件**：执行 `make install` 命令，将编译好的文件复制到之前指定的安装路径中。

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_27.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_29.png)

### 实战示例：以Nginx为例

我们以Nginx为例，演示源码安装的关键步骤。

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_31.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_33.png)

**第一步：下载源码包**
可以从Nginx官网(`nginx.org`)下载，或使用 `wget` 命令直接下载到服务器。
```bash
wget http://nginx.org/download/nginx-1.20.2.tar.gz
```

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_35.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_37.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_39.png)

**第二步：解压并进入目录**
```bash
tar -zxvf nginx-1.20.2.tar.gz
cd nginx-1.20.2
```

**第三步：配置安装参数**
执行 `./configure` 脚本，并可以通过 `--help` 查看所有可配置选项。
```bash
./configure --help
```
一个常见的配置命令示例，指定安装路径并启用SSL模块：
```bash
./configure --prefix=/usr/local/nginx --with-http_ssl_module
```
如果配置过程没有报错（即没有出现 `error` 关键字），说明环境检查通过。

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_41.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_43.png)

**第四步：编译与安装**
```bash
make
sudo make install
```
安装完成后，所有Nginx相关的文件都将位于 `/usr/local/nginx` 目录下，便于集中管理。

### 源码包安装的注意事项

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_45.png)

*   **依赖管理**：源码包所需的依赖，其名称通常与RPM包一致。可以使用 `yum search` 或 `yum provides` 来查找并安装。
*   **官方文档**：不同软件的源码安装细节差异很大，遇到问题时，首要参考是官方提供的安装文档。
*   **功能定制**：通过 `./configure` 参数可以精简或增加功能，有助于生成更符合需求的软件版本。
*   **后续管理**：源码安装的软件，其启动、停止等管理方式通常需要参考其自身的脚本或文档，与RPM包安装的服务管理方式不同。

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_47.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_49.png)

---

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_51.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_53.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_55.png)

## systemd 管理服务 ⚙️

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_57.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_59.png)

在安装好软件后，我们需要学习如何管理它们，即如何启动、停止、设置开机自启等。在RHEL/CentOS 7及以后版本中，这是通过 `systemd` 系统和服务管理器来完成的。

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_61.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_63.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_65.png)

`systemd` 是系统的第一个进程（PID=1），它提供了强大的服务管理能力。

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_67.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_69.png)

### 常用的 systemctl 命令

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_71.png)

`systemctl` 是与 `systemd` 交互的主要命令。以下是管理服务（以`vsftpd`为例）的核心操作：

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_73.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_75.png)

**启动服务**
```bash
sudo systemctl start vsftpd
```

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_77.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_79.png)

**查看服务状态**
```bash
sudo systemctl status vsftpd
```
状态显示为 `active (running)` 即表示服务正在运行。

**停止服务**
```bash
sudo systemctl stop vsftpd
```

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_81.png)

**重启服务**
```bash
sudo systemctl restart vsftpd
```
此命令无论服务当前是否运行，都会尝试重启它，常用于使配置文件修改生效。

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_83.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_85.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_87.png)

**设置服务开机自启**
```bash
sudo systemctl enable vsftpd
```
此命令会创建相应的符号链接，使服务在系统启动时自动运行。

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_89.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_91.png)

**禁用服务开机自启**
```bash
sudo systemctl disable vsftpd
```

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_93.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_95.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_97.png)

**检查服务是否启用开机自启**
```bash
sudo systemctl is-enabled vsftpd
```

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_99.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_101.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_103.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_105.png)

### 查看系统服务与端口

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_107.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_109.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_111.png)

除了管理，我们还需要查看哪些服务正在运行。`ss` 和 `netstat` 命令可以帮助我们查看服务监听的网络端口。

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_113.png)

**使用 `ss` 命令（推荐）**
```bash
sudo ss -nptul
```
*   `-n`：显示数字形式的地址和端口。
*   `-p`：显示占用端口的进程。
*   `-t`：显示TCP端口。
*   `-u`：显示UDP端口。
*   `-l`：仅显示监听状态的端口。

**使用 `netstat` 命令（传统）**
```bash
sudo netstat -nptul
```
（在某些最小化安装的系统上，可能需要先安装 `net-tools` 包：`sudo yum install net-tools`）

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_115.png)

**过滤特定服务**
可以结合 `grep` 命令查看特定服务（如Nginx）的端口信息：
```bash
sudo ss -nptul | grep nginx
```

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_117.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_119.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_121.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_123.png)

---

## 总结 📝

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_125.png)

本节课中我们一起学习了Linux系统中两种高级管理技能。

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_127.png)

1.  **源码包安装**：我们了解了其**自定义安装路径和功能模块**的核心优势，并掌握了从下载、解压、配置(`./configure`)、编译(`make`)到安装(`make install`)的完整流程。关键在于阅读官方文档和处理依赖关系。
2.  **systemd服务管理**：我们学习了使用 `systemctl` 命令对系统服务进行**启动(`start`)、停止(`stop`)、重启(`restart`)、查看状态(`status`)** 以及**设置开机自启(`enable`/`disable`)** 等操作。同时，也学会了使用 `ss` 或 `netstat` 命令**查看服务占用的网络端口**。

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_129.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_131.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_133.png)

![](img/9a5fe2940a2ad6593f965ab4f9dfb857_135.png)

源码包管理提供了灵活性，而systemd服务管理则保证了部署的规范性与便捷性。这两者都是Linux运维工程师必须熟练掌握的基础能力，请务必多加练习。