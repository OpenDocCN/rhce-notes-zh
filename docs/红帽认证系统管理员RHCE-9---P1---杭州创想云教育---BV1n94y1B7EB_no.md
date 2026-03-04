# RHCE 9.0 课程：第6章：归档、传输与软件包管理

在本节课中，我们将学习如何归档和压缩文件、在不同系统间安全地传输文件，以及使用RPM和DNF工具管理软件包。这些是系统管理员日常工作中必备的核心技能。

## 回顾与概述

上一节我们介绍了系统日志、时区设置和网络管理。本节中，我们来看看文件归档、传输和软件包管理。

本章内容分为三个主要部分：
1.  使用 `tar` 命令归档和压缩文件。
2.  使用 `sftp` 和 `rsync` 命令安全地传输和同步文件。
3.  使用 `rpm` 和 `dnf` 命令管理软件包。

## 第一部分：归档与压缩文件 📦

归档是将多个文件或目录整合为一个文件的过程，而压缩则是为了减少文件占用的磁盘空间或网络传输带宽。

### 使用 `tar` 命令归档

`tar` 命令主要用于归档，而非直接压缩。

以下是创建归档的基本命令：
```bash
tar -cf archive.tar file1 directory1
```
*   `-c`：创建归档。
*   `-f`：指定归档文件名。

例如，归档 `/usr/share/doc` 目录和 `/etc/fstab` 文件：
```bash
tar -cf example1.tar /usr/share/doc /etc/fstab
```

要查看归档文件的内容，使用 `-t` 选项：
```bash
tar -tf example1.tar
```

要展开归档文件到当前目录，使用 `-x` 选项：
```bash
tar -xf example1.tar
```

![](img/dcf8e14250439f6a144e931a2944c7d7_1.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_3.png)

若要展开到指定目录，需结合 `-C` 选项：
```bash
tar -xf example1.tar -C /mnt
```

![](img/dcf8e14250439f6a144e931a2944c7d7_5.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_7.png)

### 使用压缩工具

![](img/dcf8e14250439f6a144e931a2944c7d7_9.png)

Linux 系统常用的压缩工具有 `gzip`、`bzip2` 和 `xz`。它们只能压缩文件，不能直接压缩目录。

![](img/dcf8e14250439f6a144e931a2944c7d7_11.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_12.png)

以下是压缩与解压命令：
*   **gzip**:
    *   压缩：`gzip filename`
    *   解压：`gunzip filename.gz`
*   **bzip2**:
    *   压缩：`bzip2 filename`
    *   解压：`bunzip2 filename.bz2`
*   **xz**:
    *   压缩：`xz filename`
    *   解压：`unxz filename.xz`

![](img/dcf8e14250439f6a144e931a2944c7d7_14.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_16.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_18.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_19.png)

这三个工具的压缩率从高到低依次是：`xz` > `bzip2` > `gzip`。

![](img/dcf8e14250439f6a144e931a2944c7d7_21.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_22.png)

### 结合归档与压缩

`tar` 命令可以结合压缩选项，一次性完成归档和压缩。

以下是结合不同压缩工具的选项：
*   `-z`：使用 `gzip` 压缩。
*   `-j`：使用 `bzip2` 压缩。
*   `-J`：使用 `xz` 压缩。

例如，创建并用 `gzip` 压缩的归档文件：
```bash
tar -czf example2.tar.gz /usr/share/doc
```

解压并展开此类文件：
```bash
tar -xzf example2.tar.gz
```

![](img/dcf8e14250439f6a144e931a2944c7d7_24.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_25.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_27.png)

## 第二部分：安全传输文件 🔐

![](img/dcf8e14250439f6a144e931a2944c7d7_28.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_30.png)

上一节我们学习了如何打包文件，本节中我们来看看如何将这些文件安全地传输到其他系统。

### 使用 SFTP 传输文件

`scp` 命令已逐渐被更安全的 `sftp` 替代。`sftp` 基于 SSH 协议，提供了交互式的文件传输方式。

连接到远程服务器：
```bash
sftp user@remote_host
```

连接后，可以使用以下常用命令：
*   `ls`：列出远程服务器当前目录的文件。
*   `lls`：列出本地当前目录的文件。
*   `cd`：切换远程服务器目录。
*   `lcd`：切换本地目录。
*   `put local_file`：上传本地文件到远程服务器。
*   `get remote_file`：下载远程文件到本地。
*   `exit`：退出 sftp 会话。

例如，上传文件到远程服务器的 `/tmp/backup` 目录：
```bash
sftp user@server_b
cd /tmp/backup
put example2.tar.gz
```

![](img/dcf8e14250439f6a144e931a2944c7d7_32.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_33.png)

### 使用 Rsync 同步文件

![](img/dcf8e14250439f6a144e931a2944c7d7_35.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_37.png)

`rsync` 命令用于高效地在系统间同步文件和目录，它只传输有变动的部分，节省带宽和时间。

`rsync` 的基本语法如下：
```bash
rsync [选项] 源文件 目标位置
```

![](img/dcf8e14250439f6a144e931a2944c7d7_39.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_41.png)

常用选项包括：
*   `-v`：显示详细过程。
*   `-r`：递归同步目录。
*   `-u`：仅更新（增量同步），目标文件比源文件新时跳过。
*   `-a`：归档模式，相当于 `-rlptgoD`（保留权限、时间等属性）。
*   `-P`：显示进度条。

例如，将本地文件同步到远程服务器：
```bash
rsync -avP example2.tar.gz user@server_b:/tmp/backup/
```

## 第三部分：管理软件包 📦

![](img/dcf8e14250439f6a144e931a2944c7d7_43.png)

系统安装后，需要通过安装软件包来扩展功能。本节中我们来看看如何使用 Red Hat 系的包管理工具。

![](img/dcf8e14250439f6a144e931a2944c7d7_44.png)

### 了解 RPM 包

RPM 是 Red Hat Package Manager 的缩写。一个 RPM 包文件名通常包含以下信息：
`名称-版本-发行号.架构.rpm`
例如：`coreutils-8.32-31.el9.x86_64.rpm`

### 使用 `rpm` 命令查询

![](img/dcf8e14250439f6a144e931a2944c7d7_46.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_48.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_50.png)

`rpm` 命令现在主要用于查询软件包信息，而非安装（因为依赖关系处理复杂）。

以下是常用的查询命令：
*   `rpm -qa`：列出所有已安装的包。
*   `rpm -qf /path/to/file`：查询文件由哪个软件包提供。
*   `rpm -q package_name`：查询指定包是否安装及其版本。
*   `rpm -qi package_name`：显示软件包的详细信息。
*   `rpm -ql package_name`：列出软件包安装的所有文件。
*   `rpm -qc package_name`：列出软件包的配置文件。
*   `rpm -qd package_name`：列出软件包的文档文件。

### 使用 `dnf` 命令管理

![](img/dcf8e14250439f6a144e931a2944c7d7_52.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_54.png)

`dnf`（或 `yum`）是高级包管理工具，能自动解决依赖关系。在 RHEL 9 中，`yum` 是 `dnf` 的软链接。

以下是 `dnf` 的常用命令：
*   `dnf list all`：列出所有可用软件包。
*   `dnf list installed`：列出已安装的软件包。
*   `dnf search keyword`：搜索包含关键词的软件包。
*   `dnf info package_name`：显示软件包的详细信息。
*   `dnf install package_name`：安装软件包（加 `-y` 自动确认）。
*   `dnf update package_name`：更新软件包。
*   `dnf remove package_name`：卸载软件包。
*   `dnf group list`：列出软件包组。
*   `dnf group install "Group Name"`：安装软件包组。
*   `dnf history`：查看操作历史。
*   `dnf history undo ID`：撤销指定历史操作。

例如，安装 Nginx 服务器：
```bash
dnf install nginx -y
```

![](img/dcf8e14250439f6a144e931a2944c7d7_56.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_57.png)

### 软件仓库概念

![](img/dcf8e14250439f6a144e931a2944c7d7_59.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_61.png)

RHEL 9 的软件源主要分为两部分：
1.  **BaseOS**：提供操作系统核心功能包，生命周期长，变化小。
2.  **AppStream**：提供用户空间应用程序、运行时语言和数据库。引入了“模块流”概念，允许安装同一软件的不同版本。

## 总结

本节课中我们一起学习了：
1.  **文件归档与压缩**：使用 `tar` 结合 `gzip`、`bzip2`、`xz` 进行归档和压缩，以节省空间或准备传输。
2.  **安全文件传输**：使用 `sftp` 进行交互式安全文件传输，以及使用 `rsync` 进行高效的文件同步。
3.  **软件包管理**：使用 `rpm` 命令查询软件包信息，以及使用 `dnf` 命令安装、更新、卸载软件包，并了解了 BaseOS 和 AppStream 仓库的区别。

![](img/dcf8e14250439f6a144e931a2944c7d7_63.png)

![](img/dcf8e14250439f6a144e931a2944c7d7_65.png)

掌握这些命令将帮助你有效地管理服务器上的文件、部署软件以及在不同系统间迁移数据。