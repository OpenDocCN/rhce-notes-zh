# Linux基础课程（RHCSA）：4：软件和包的管理 🛠️

在本节课中，我们将学习在红帽企业版Linux（RHEL）及其衍生系统（如CentOS）中管理软件的三种主要方式：RPM包管理、YUM仓库管理以及源代码编译安装。我们将详细介绍每种方法的使用场景、基本命令和操作流程。

## 概述 📋

![](img/009ed5b0da40ed37f2750f160f2a3059_1.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_2.png)

Linux系统中的软件管理是系统管理员的核心技能之一。红帽系列系统主要使用RPM包格式，并提供了RPM和YUM两种工具进行管理。此外，我们还可以通过源代码编译的方式安装软件，以获得更高的灵活性。本节将逐一讲解这三种方法。

![](img/009ed5b0da40ed37f2750f160f2a3059_4.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_5.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_7.png)

## RPM包管理 📦

![](img/009ed5b0da40ed37f2750f160f2a3059_9.png)

RPM是红帽包管理器（Red Hat Package Manager）的缩写，是红帽系列Linux发行版中软件包的基础格式，文件后缀为 `.rpm`。使用RPM命令可以直接管理这些软件包。

### RPM基本命令

以下是RPM包管理的核心命令及其用途：

![](img/009ed5b0da40ed37f2750f160f2a3059_11.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_13.png)

*   **安装软件包**：使用 `rpm -ivh` 命令。
    ```bash
    rpm -ivh package_name.rpm
    ```
    *   `-i`：安装。
    *   `-v`：显示详细信息。
    *   `-h`：显示安装进度条。

*   **查询已安装的软件包**：使用 `rpm -qa` 命令。
    ```bash
    rpm -qa | grep keyword
    ```

![](img/009ed5b0da40ed37f2750f160f2a3059_15.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_16.png)

*   **查询软件包安装的文件**：使用 `rpm -ql` 命令。
    ```bash
    rpm -ql package_name
    ```

![](img/009ed5b0da40ed37f2750f160f2a3059_18.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_20.png)

*   **查询文件属于哪个软件包**：使用 `rpm -qf` 命令。
    ```bash
    rpm -qf /path/to/file
    ```

![](img/009ed5b0da40ed37f2750f160f2a3059_22.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_24.png)

*   **查询软件包信息**：使用 `rpm -qi` 命令。
    ```bash
    rpm -qi package_name
    ```

*   **升级软件包**：使用 `rpm -Uvh` 命令。
    ```bash
    rpm -Uvh package_name.rpm
    ```

![](img/009ed5b0da40ed37f2750f160f2a3059_26.png)

*   **卸载软件包**：使用 `rpm -e` 命令。
    ```bash
    rpm -e package_name
    ```

![](img/009ed5b0da40ed37f2750f160f2a3059_28.png)

### RPM的依赖关系问题

![](img/009ed5b0da40ed37f2750f160f2a3059_30.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_31.png)

使用RPM命令安装软件时，最大的挑战是处理**依赖关系**。例如，安装A包可能需要先安装B包，而B包又依赖于C包。这种手动处理依赖的方式效率较低。

上一节我们介绍了RPM的基本操作，但提到了其依赖管理的复杂性。本节中我们来看看一个更强大的工具——YUM，它能自动解决依赖问题。

## YUM仓库管理 🗃️

YUM（Yellowdog Updater, Modified）是一个基于RPM的Shell前端软件包管理器。它能够从指定的服务器（仓库）自动下载RPM包并安装，可以自动处理依赖关系。

### 配置YUM仓库

要使用YUM，首先需要配置仓库。仓库配置文件位于 `/etc/yum.repos.d/` 目录下，后缀为 `.repo`。

一个典型的本地仓库配置文件内容如下：
```ini
[base]
name=Local Repository
baseurl=file:///mnt
enabled=1
gpgcheck=0
```
*   `[base]`：仓库ID。
*   `name`：仓库描述。
*   `baseurl`：仓库位置。`file://` 表示本地文件系统路径。
*   `enabled`：是否启用此仓库（1为启用）。
*   `gpgcheck`：是否进行GPG签名检查（0为不检查）。

### YUM基本命令

![](img/009ed5b0da40ed37f2750f160f2a3059_33.png)

以下是YUM的常用命令：

![](img/009ed5b0da40ed37f2750f160f2a3059_35.png)

*   **列出所有可用软件包**：`yum list`
*   **搜索软件包**：`yum search keyword`
*   **安装软件包**：`yum install package_name`
    *   添加 `-y` 参数可自动确认安装。
*   **卸载软件包**：`yum remove package_name`
*   **更新软件包**：`yum update package_name`
*   **列出软件包组**：`yum grouplist`
*   **安装软件包组**：`yum groupinstall "Group Name"`
*   **清除缓存**：`yum clean all`

![](img/009ed5b0da40ed37f2750f160f2a3059_37.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_39.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_41.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_43.png)

YUM极大地简化了软件管理，但安装位置和功能选项通常是固定的。接下来，我们将探讨最灵活但也最复杂的软件安装方式——源代码编译安装。

![](img/009ed5b0da40ed37f2750f160f2a3059_45.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_47.png)

## 源代码编译安装 🏗️

![](img/009ed5b0da40ed37f2750f160f2a3059_49.png)

源代码安装通常提供一个压缩包（如 `.tar.gz` 或 `.tar.bz2`）。用户需要解压后，自行编译并安装到指定位置。这种方式最为灵活，可以自定义安装路径和功能模块，但步骤繁琐，同样需要手动解决依赖。

![](img/009ed5b0da40ed37f2750f160f2a3059_51.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_53.png)

### 源代码安装基本步骤

![](img/009ed5b0da40ed37f2750f160f2a3059_55.png)

源代码编译安装通常遵循以下三步曲：

![](img/009ed5b0da40ed37f2750f160f2a3059_57.png)

1.  **配置（Configure）**：运行 `./configure` 脚本检查系统环境并生成编译规则。可以使用 `--prefix` 参数指定安装目录。
    ```bash
    ./configure --prefix=/usr/local/software_name
    ```

![](img/009ed5b0da40ed37f2750f160f2a3059_59.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_61.png)

2.  **编译（Make）**：运行 `make` 命令，根据上一步生成的规则将源代码编译成二进制文件。
    ```bash
    make
    ```

![](img/009ed5b0da40ed37f2750f160f2a3059_63.png)

3.  **安装（Install）**：运行 `make install` 命令，将编译好的文件复制到 `--prefix` 指定的目录中。
    ```bash
    make install
    ```

### 处理编译依赖

在 `./configure` 阶段，如果系统缺少必要的开发库或工具（如 `gcc`），会报错。你需要根据错误提示，使用YUM安装相应的软件包（通常是 `软件包名-devel` 格式），然后重新配置。

![](img/009ed5b0da40ed37f2750f160f2a3059_65.png)

## 总结 🎯

本节课我们一起学习了Linux中三种主要的软件管理方式：
1.  **RPM**：直接管理 `.rpm` 文件，命令直观，但需手动处理依赖，适合安装无依赖或已知依赖的独立软件包。
2.  **YUM**：基于仓库的自动化管理工具，能自动解决依赖关系，是日常管理中最推荐的方式。
3.  **源代码编译**：提供最大的灵活性，可定制安装路径和功能，但过程复杂，适合安装最新版或特殊定制的软件。

![](img/009ed5b0da40ed37f2750f160f2a3059_67.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_69.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_71.png)

![](img/009ed5b0da40ed37f2750f160f2a3059_72.png)

掌握这三种方法，你将能够应对绝大多数Linux环境下的软件安装与管理需求。