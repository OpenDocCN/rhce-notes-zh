# Linux软件管理：19-1：软件包安装基础

![](img/1a53b0eb5c66f0977ee4144931c3039f_1.png)

在本节课中，我们将要学习Linux系统中两种核心的软件包安装方式：基于`rpm`的安装和基于`yum`仓库的安装。我们将从基本概念入手，逐步掌握软件包的安装、查询、升级、卸载等管理操作。

![](img/1a53b0eb5c66f0977ee4144931c3039f_3.png)

## 基于RPM的软件包安装

上一节我们介绍了软件包安装的两种方式，本节中我们来看看第一种：基于RPM的安装。RPM（Red Hat Package Manager）是一种用于单个软件包安装的格式，类似于Windows系统中的`.exe`可执行文件。在Linux中，对应的文件格式是`.rpm`。

![](img/1a53b0eb5c66f0977ee4144931c3039f_5.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_7.png)

需要注意的是，`rpm`工具的功能非常强大，不仅仅用于安装软件。

### RPM工具的核心功能

![](img/1a53b0eb5c66f0977ee4144931c3039f_9.png)

以下是`rpm`命令能够执行的主要功能总结：

![](img/1a53b0eb5c66f0977ee4144931c3039f_11.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_13.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_15.png)

1.  **安装软件包**
    *   `-i`：代表安装（install）。
    *   `-h`：以井号（`#`）方式显示安装进度，每个井号代表2%的进度。
    *   `-v`：显示安装的详细信息。
    *   `-vv`：显示更加详细的安装信息。
    *   `--nodeps`：忽略软件包的依赖关系进行安装。这是RPM安装的一个缺点，它默认不自动解决依赖。
    *   `--replacepkgs`：重新安装并替换原有的安装包（覆盖安装）。
    *   `--force`：强制安装、强制降级或强制覆盖。

![](img/1a53b0eb5c66f0977ee4144931c3039f_17.png)

**安装示例：**
```bash
rpm -ivh zsh-5.5.1-6.el8.x86_64.rpm
```

![](img/1a53b0eb5c66f0977ee4144931c3039f_19.png)

2.  **查询软件包**
    *   `-qa`：查询系统中所有已安装的RPM包。
    *   `-qi <package_name>`：查询指定软件包的详细信息（如版本、描述等）。
    *   `-ql <package_name>`：查询指定软件包安装后生成的所有文件列表。
    *   `-qc <package_name>`：查询指定软件包安装后生成的**配置文件**列表。这个命令非常实用。
    *   `-qd <package_name>`：查询指定软件包安装后生成的**帮助文档**列表。
    *   `-qf <file_path>`：查询指定文件是由哪个RPM包安装生成的。
    *   `-q --scripts <package_name>`：查看指定安装包中包含的脚本。
    *   `-qpi <rpm_file>`：查询**未安装**的RPM包文件中的信息。
    *   `-qpl <rpm_file>`：查询**未安装**的RPM包文件安装后会生成哪些文件。

![](img/1a53b0eb5c66f0977ee4144931c3039f_21.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_23.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_25.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_27.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_29.png)

**查询示例：**
```bash
rpm -qa | grep zsh          # 查看是否安装了zsh
rpm -qi vim-enhanced        # 查看vim软件的详细信息
rpm -ql chrony              # 查看chrony服务安装的文件
rpm -qc chrony              # 查看chrony服务的配置文件路径
rpm -qf /etc/chrony.conf    # 查看/etc/chrony.conf文件来自哪个软件包
```

3.  **升级软件包**
    *   `-Uvh`：升级软件包。如果系统中没有旧版本，则执行安装。
    *   `-Fvh`：升级软件包。仅当系统中存在旧版本时才执行升级，否则退出。

![](img/1a53b0eb5c66f0977ee4144931c3039f_31.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_33.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_35.png)

4.  **卸载软件包**
    *   `-e`：卸载（erase）指定的软件包。

![](img/1a53b0eb5c66f0977ee4144931c3039f_37.png)

**卸载示例：**
```bash
rpm -e zsh
```

![](img/1a53b0eb5c66f0977ee4144931c3039f_39.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_41.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_43.png)

5.  **校验软件包**
    *   `-V`：校验已安装软件包的文件是否被修改过。
    *   `-K`：校验RPM包文件的来源合法性和完整性（需要GPG密钥）。

![](img/1a53b0eb5c66f0977ee4144931c3039f_45.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_47.png)

**校验示例：**
```bash
rpm -V vim-enhanced  # 校验vim文件，无输出表示未被修改
rpm -K some-package.rpm # 校验RPM包签名
```

![](img/1a53b0eb5c66f0977ee4144931c3039f_49.png)

6.  **数据库管理**
    *   `--initdb`：初始化RPM数据库。
    *   `--rebuilddb`：重建RPM数据库。

![](img/1a53b0eb5c66f0977ee4144931c3039f_51.png)

7.  **提取文件**
    可以从RPM包中提取特定文件而不安装整个软件包。

![](img/1a53b0eb5c66f0977ee4144931c3039f_53.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_55.png)

**提取文件示例：**
```bash
rpm2cpio zsh-5.5.1-6.el8.x86_64.rpm | cpio -id “./usr/share/zsh/*”
```

![](img/1a53b0eb5c66f0977ee4144931c3039f_57.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_59.png)

## 基于YUM仓库的软件包管理

了解了基础的RPM管理后，本节我们来看看更高级的软件包管理方式：YUM。YUM（Yellowdog Updater, Modified）可以理解为Linux上的“软件管家”，它基于仓库（Repository）工作，能够自动解决软件依赖关系。

### YUM仓库架构与类型

YUM采用C/S（客户端/服务器）架构。
*   **客户端（C）**：即需要安装软件的Linux机器。
*   **服务器（S）**：即软件仓库服务器，存放着大量的RPM包及其元数据（如依赖关系列表）。

搭建YUM仓库服务器主要有以下几种方式：

1.  **红帽官方仓库**：需要互联网连接和红帽订阅（付费）。
2.  **第三方公共仓库**：如阿里云、163镜像站，需要互联网连接但免费。
3.  **本地仓库**：
    *   **局域网仓库**：在内部网络搭建私有仓库服务器。
    *   **本地镜像仓库**：使用系统安装DVD/ISO镜像文件搭建，无需网络。

![](img/1a53b0eb5c66f0977ee4144931c3039f_61.png)

在我们的实验环境中，通常使用**局域网仓库**。客户端安装软件时，会先从仓库服务器同步软件包列表和依赖关系元数据到本地缓存，然后根据需要在安装时下载具体的RPM包。

![](img/1a53b0eb5c66f0977ee4144931c3039f_63.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_65.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_67.png)

### YUM客户端配置

![](img/1a53b0eb5c66f0977ee4144931c3039f_69.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_71.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_73.png)

YUM客户端的核心配置文件位于`/etc/yum.repos.d/`目录下，文件以`.repo`结尾。

![](img/1a53b0eb5c66f0977ee4144931c3039f_75.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_77.png)

在RHEL/CentOS 8及以后版本中，通常需要配置两个仓库：

*   **BaseOS**：提供核心操作系统软件包。
*   **AppStream**：提供应用程序、运行时语言和工具。

![](img/1a53b0eb5c66f0977ee4144931c3039f_79.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_81.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_83.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_85.png)

以下是`.repo`文件的配置格式示例：

![](img/1a53b0eb5c66f0977ee4144931c3039f_87.png)

```ini
[baseos] # 仓库ID，可自定义
name=Local BaseOS # 仓库名称，可自定义
baseurl=http://your-repo-server/BaseOS # 仓库的URL地址，这是关键
enabled=1 # 启用此仓库 (1/yes/true)
gpgcheck=0 # 不进行GPG签名检查 (0/no/false)

![](img/1a53b0eb5c66f0977ee4144931c3039f_89.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_91.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_93.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_95.png)

[appstream]
name=Local AppStream
baseurl=http://your-repo-server/AppStream
enabled=1
gpgcheck=0
```

![](img/1a53b0eb5c66f0977ee4144931c3039f_97.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_99.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_101.png)

**重要配置项：**
*   `baseurl`：指定仓库的实际地址。若使用公网仓库，可替换为 `http://mirrors.aliyun.com/centos/...` 或 `http://mirrors.163.com/centos/...`。
*   `enabled`：是否启用。
*   `gpgcheck`：是否检查软件包GPG签名。

![](img/1a53b0eb5c66f0977ee4144931c3039f_103.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_105.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_107.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_109.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_110.png)

### YUM常用命令

![](img/1a53b0eb5c66f0977ee4144931c3039f_112.png)

以下是使用YUM管理软件包的核心命令：

![](img/1a53b0eb5c66f0977ee4144931c3039f_114.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_116.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_118.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_120.png)

*   `yum list [installed | all]`：列出所有/已安装的软件包。
*   `yum search <keyword>`：根据关键字搜索软件包。
*   `yum info <package_name>`：显示软件包的详细信息（类似`rpm -qi`）。
*   `yum provides <file_path>`：查询哪个软件包提供了指定文件（类似`rpm -qf`）。
*   `yum install <package_name>`：安装指定软件包（**自动解决依赖**）。
*   `yum update <package_name>`：更新指定软件包。
*   `yum remove <package_name>`：卸载指定软件包。
*   `yum history`：查看YUM操作历史记录。
*   `yum group list`：列出可用的软件包组。
*   `yum group install “Development Tools”`：安装名为“Development Tools”的软件包组（包含一系列开发工具）。

![](img/1a53b0eb5c66f0977ee4144931c3039f_122.png)

### YUM模块（Module）管理（RHEL 8+ 特性）

![](img/1a53b0eb5c66f0977ee4144931c3039f_124.png)

红帽8引入了模块化仓库的概念，将某些复杂软件（如不同版本的Python、PostgreSQL）及其依赖打包成模块。

![](img/1a53b0eb5c66f0977ee4144931c3039f_126.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_128.png)

*   `yum module list`：列出所有可用模块。
*   `yum module info <module_name>`：查看模块详细信息。
*   `yum module install <module_name>:<stream>/<version>`：安装特定模块流和版本。例如，安装Python 3.6。
    ```bash
    yum module install python36
    ```
*   `yum module remove <module_name>`：移除模块。

![](img/1a53b0eb5c66f0977ee4144931c3039f_130.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_132.png)

---

![](img/1a53b0eb5c66f0977ee4144931c3039f_134.png)

![](img/1a53b0eb5c66f0977ee4144931c3039f_136.png)

本节课中我们一起学习了Linux系统软件管理的两大基石：RPM和YUM。我们掌握了如何使用`rpm`命令直接管理`.rpm`软件包，包括安装、查询、升级和卸载。更重要的是，我们理解了YUM仓库的工作原理，学会了如何配置仓库并使用`yum`命令进行高效的软件管理，它能自动处理依赖关系，极大地简化了运维工作。最后，我们还了解了RHEL 8中新增的模块化（Module）软件管理方式。