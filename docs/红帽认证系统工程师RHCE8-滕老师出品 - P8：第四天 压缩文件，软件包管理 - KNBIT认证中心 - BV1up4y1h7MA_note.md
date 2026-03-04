# RHCE8 课程：P8：第四天 压缩文件，软件包管理 📦

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_1.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_3.png)

在本节课中，我们将要学习Linux系统中的文件归档与压缩，以及软件包管理。这是系统管理员日常工作中非常核心的技能，能够帮助我们高效地备份、传输文件，以及安装、维护系统软件。

## 网络配置回顾与补充 🌐

上一节我们介绍了网络配置的基础知识，本节中我们来看看一些补充内容和验证方法。

### 网络配置文件与命令

请注意，使用`nmcli`命令进行的网络修改或添加都是永久生效的，因为它会自动将配置写入到`/etc/sysconfig/network-scripts/`目录下的相应文件中。虽然你也可以使用`vi`编辑器直接编辑这些文件，但考试时首选`nmcli`命令。

**核心配置文件路径**：
```
/etc/sysconfig/network-scripts/ifcfg-<连接名>
```

这是一个系统管理员必须熟悉的网络配置文件。在红帽7之前的版本中，此配置文件默认存在，但在红帽8的考试环境中可能没有，因此强烈建议使用`nmcli`命令进行配置。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_5.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_7.png)

### 主机名与DNS配置

修改主机名是网络设置中的常见任务。以下是两种方法：

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_9.png)

1.  **使用hostnamectl命令（推荐）**：
    ```
    hostnamectl set-hostname <新主机名>
    ```
    这是永久修改主机名的方法，考试中会涉及。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_11.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_13.png)

2.  **编辑配置文件**：
    编辑`/etc/hostname`文件，写入新的主机名，然后保存退出。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_15.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_17.png)

**本地主机名解析**：
`/etc/hosts`文件用于本地静态名称解析，可以替代DNS。其格式为：
```
<IP地址> <主机名> [<短主机名>]
```
例如，添加一行`172.25.250.10 classroom.example.com classroom`后，`ping classroom`就等同于`ping 172.25.250.10`。

**配置DNS**：
DNS服务器地址在`/etc/resolv.conf`文件中配置。添加以下行即可：
```
nameserver <DNS服务器IP地址>
```
注意，如果你使用`vi`编辑器直接编辑网卡配置文件来设置DNS，一定要加上序号，如`DNS1=172.25.250.254`，否则可能不生效。

### 网络验证命令

配置完成后，必须进行验证。以下是几种方法：

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_19.png)

*   **使用ping命令**：既能测试连通性，也能测试DNS解析。例如：`ping classroom`。
*   **使用host命令**：专门用于DNS查询。例如：`host servera.example.com`。
*   **使用dig命令**：更详细的DNS查询工具。例如：`dig classroom.example.com`。

**关键端口号**：
*   **53**：DNS服务端口。
*   **514**：系统日志（rsyslog）服务端口。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_21.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_23.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_24.png)

养成每完成一道题目就验证的习惯，是确保考试通过的关键。

### 网络诊断工具

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_26.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_28.png)

**ss命令**：用于查看网络连接、监听端口等信息。
常用参数组合：`ss -ntlpa`
*   `-n`：显示端口号而非服务名。
*   `-t`：查看TCP连接。
*   `-l`：查看监听（LISTEN）状态的端口。
*   `-p`：显示使用端口的进程。
*   `-a`：显示所有套接字。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_30.png)

例如，`ss -ntlpa`可以查看所有TCP连接及其状态。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_32.png)

**注意**：在最小化安装的系统上，传统的`net-tools`工具包（包含`ifconfig`、`netstat`等命令）可能没有安装。此时可以使用`ip addr`（或`ip a`）命令来查看IP地址。如果需要安装`net-tools`，可以使用`yum install net-tools`。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_34.png)

---

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_36.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_38.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_40.png)

## 文件归档与压缩 📁

上一节我们回顾了网络配置，本节中我们来看看如何高效地打包和压缩文件。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_42.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_44.png)

### 理解归档与压缩

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_46.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_48.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_50.png)

首先需要明确概念：**`tar`命令本身只负责归档（打包），不负责压缩**。归档是将多个文件或目录集合成一个单独的包文件。我们常说的“压缩包”是在归档的同时，借助了其他压缩工具（如gzip、bzip2）的结果。

### tar命令详解

`tar`命令的基本格式为：`tar [选项] <目标归档文件> <源文件或目录>`
**注意**：这里先指定**打包成的文件名**，再指定**要打包的内容**。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_52.png)

**常用选项**：
*   `-c`：创建新的归档文件。
*   `-x`：从归档文件中提取（解包）文件。
*   `-t`：列出归档文件的内容列表。
*   `-v`：显示详细的处理过程。
*   `-f`：指定归档文件名（**必须放在所有选项最后**）。
*   `-z`：通过gzip进行压缩/解压，生成`.tar.gz`或`.tgz`文件。
*   `-j`：通过bzip2进行压缩/解压，生成`.tar.bz2`文件。
*   `-J`：通过xz进行压缩/解压，生成`.tar.xz`文件。
*   `-C`：解压到指定目录。

**常用组合示例**：
1.  **打包并压缩（使用bzip2）**：
    ```
    tar -cjvf backup.tar.bz2 /home
    ```
    这条命令将`/home`目录打包并用bzip2压缩，生成名为`backup.tar.bz2`的文件。

2.  **查看压缩包内容**：
    ```
    tar -tjvf backup.tar.bz2
    ```

3.  **解压到指定目录**：
    ```
    tar -xjvf backup.tar.bz2 -C /opt
    ```

### 独立的压缩工具

除了结合`tar`使用，gzip和bzip2也可以单独压缩文件。
*   **gzip**：
    *   压缩：`gzip filename` （生成`filename.gz`，**会删除原文件**）
    *   解压：`gzip -d filename.gz` 或 `gunzip filename.gz`
*   **bzip2**：
    *   压缩：`bzip2 filename` （生成`filename.bz2`，**会删除原文件**）
    *   解压：`bzip2 -d filename.bz2` 或 `bunzip2 filename.bz2`
    *   **保留原文件压缩**：`bzip2 -k filename`

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_54.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_56.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_58.png)

---

## 安全文件传输与同步 🔄

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_60.png)

上一节我们学会了打包文件，本节中我们来看看如何在不同的主机间安全地传输或同步这些文件。

### scp命令

`scp`（secure copy）基于SSH协议，用于在本地和远程主机之间安全地复制文件。
其语法类似于`ssh`和`cp`的结合。

**基本语法**：
```
scp [选项] <源文件> <用户名>@<远程主机>:<目标路径>
scp [选项] <用户名>@<远程主机>:<源文件> <目标路径>
```

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_62.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_64.png)

**常用选项**：
*   `-r`：递归复制整个目录。
*   `-C`：在传输过程中进行压缩，以节省带宽。

**示例**：
1.  将本地文件`data.txt`拷贝到远程服务器`serverb`的`/tmp`目录：
    ```
    scp data.txt root@serverb:/tmp
    ```
2.  从远程服务器下载目录到本地：
    ```
    scp -r root@serverb:/var/log /local/backup
    ```

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_66.png)

### rsync命令

`rsync`是一个更强大的文件同步工具，它支持**增量同步**，即只传输发生变化的部分，效率极高，常用于备份。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_68.png)

**基本语法**：
```
rsync [选项] <源> <目标>
```

**常用选项**：
*   `-a`：归档模式，等同于`-rlptgoD`，保持文件所有属性。
*   `-v`：显示详细过程。
*   `-r`：递归处理目录。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_70.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_72.png)

**一个关键细节**：源路径末尾的斜杠(`/`)含义不同。
*   `rsync -av /data/ root@serverb:/backup/`：将`/data/`目录下的**所有内容**同步到远程的`/backup/`目录下。
*   `rsync -av /data root@serverb:/backup/`：将`/data`这个**目录本身及其内容**同步到远程，结果会在`/backup/`下创建`data`子目录。

**示例**：将本地`/www`目录增量同步到备份服务器：
```
rsync -av /www/ root@backupserver:/backup/www/
```

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_74.png)

---

## 链接文件 🔗

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_76.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_78.png)

在深入软件包管理前，我们需要理解一个重要的基础概念：链接文件。Linux中的链接分为硬链接和软链接（符号链接）。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_80.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_82.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_84.png)

### inode概念

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_86.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_87.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_89.png)

Linux文件系统使用**inode**来标识文件。每个文件或目录都有一个唯一的inode号，其中存储了文件的元数据（如权限、所有者、大小、时间戳等，**但不包括文件名**）。文件名只是指向inode的一个“标签”。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_91.png)

使用`ls -i`命令可以查看文件的inode号。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_93.png)

### 硬链接

**创建命令**：`ln <源文件> <链接名>`
*   硬链接是**同一个inode的多个文件名**。删除其中一个文件名，只要inode的链接数不为0，文件数据就不会丢失。
*   **限制**：不能为目录创建硬链接；不能跨分区/文件系统创建。

**示例**：
```
ln file1.txt hardlink_to_file1
ls -li file1.txt hardlink_to_file1 # 查看inode号，两者相同
```

### 软链接

**创建命令**：`ln -s <源文件或目录> <链接名>`
*   软链接是一个独立的文件，其内容是指向目标路径的快捷方式。它拥有自己的inode。
*   如果删除源文件，软链接将失效（成为“悬空链接”）。
*   没有任何限制，可以为目录创建，也可以跨分区。

**示例**：
```
ln -s /usr/share/doc softlink_to_doc
ls -l softlink_to_doc # 文件类型显示为‘l’
```

---

## 软件包管理 📦

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_95.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_96.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_98.png)

本节是本章的核心，我们将学习如何在RHEL系统上安装、查询、卸载软件。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_100.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_102.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_104.png)

### RPM包管理

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_106.png)

RPM是Red Hat Package Manager的缩写。一个RPM包文件名通常包含以下信息：
`软件包名-版本号-发行号.系统架构.rpm`
例如：`vim-enhanced-8.0.1763-15.el8.x86_64.rpm`

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_108.png)

**常用rpm命令**：

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_110.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_112.png)

1.  **安装**：需要完整的包文件名（通常包含路径）。
    ```
    rpm -ivh package.rpm
    ```
    *   `-i`：安装。
    *   `-v`：显示详细信息。
    *   `-h`：显示进度条。
    *   `--force`：强制安装。
    *   `-U`：升级安装。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_114.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_116.png)

2.  **查询**：
    ```
    rpm -qa                      # 查询所有已安装的包
    rpm -qi package_name         # 查询某个包的详细信息
    rpm -ql package_name         # 查询某个包安装的文件列表
    rpm -qf /path/to/file        # 查询某个文件属于哪个包
    rpm -qc package_name         # 查询某个包的配置文件列表
    ```

3.  **卸载**：只需要软件包名，无需版本和架构信息。
    ```
    rpm -e package_name
    ```

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_118.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_120.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_122.png)

**RPM的依赖性问题**：RPM无法自动解决软件包之间的依赖关系，这是其最大弱点。例如，安装A包可能需要先安装B包和C包，这需要管理员手动处理，非常繁琐。

### YUM/DNF 包管理

为了自动解决依赖关系，红帽引入了YUM（Yellowdog Updater, Modified）工具。在RHEL8中，YUM实际上是DNf（Dandified YUM）的软链接。它通过配置**软件仓库**来工作。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_124.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_126.png)

**配置本地YUM仓库**：
如果系统没有网络，可以配置本地光盘作为仓库。
1.  挂载安装光盘：
    ```
    mount /dev/cdrom /mnt
    ```
2.  在`/etc/yum.repos.d/`目录下创建仓库配置文件（如`local.repo`）：
    ```ini
    [Local-BaseOS]
    name=Local BaseOS
    baseurl=file:///mnt/BaseOS
    enabled=1
    gpgcheck=0

    [Local-AppStream]
    name=Local AppStream
    baseurl=file:///mnt/AppStream
    enabled=1
    gpgcheck=0
    ```
    *   `[ ]`内是仓库ID。
    *   `name`：仓库描述。
    *   `baseurl`：仓库路径，`file://`表示本地文件。
    *   `enabled=1`：启用此仓库。
    *   `gpgcheck=0`：不进行GPG签名检查（简化操作，生产环境建议开启）。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_128.png)

**常用yum/dnf命令**：

1.  **安装与卸载**：
    ```
    yum install package_name
    yum remove package_name
    ```

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_130.png)

2.  **查询**：
    ```
    yum list all                          # 列出仓库所有包
    yum list installed                    # 列出已安装的包
    yum info package_name                 # 显示包详细信息
    yum search keyword                    # 搜索包
    yum provides /path/to/file            # 查询哪个包提供此文件
    ```

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_132.png)

3.  **组操作**：有些软件以软件组的形式提供。
    ```
    yum group list                        # 列出可用组
    yum group install "Development Tools" # 安装软件组（组名有空格需引号）
    yum group remove "Virtualization Host"
    ```

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_134.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_136.png)

4.  **清理与更新**：
    ```
    yum clean all                         # 清理缓存
    yum update                            # 更新所有可更新的包
    yum update package_name               # 更新指定包
    ```

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_138.png)

YUM会自动处理依赖关系，是日常管理和考试中的首选工具。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_140.png)

---

## 本章总结 🎯

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_142.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_144.png)

本节课中我们一起学习了Linux系统管理中非常实用的几组技能：

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_146.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_148.png)

1.  **网络配置深化**：掌握了主机名、本地解析（`/etc/hosts`）、DNS的配置与验证方法，并学习了`ss`等网络诊断工具。
2.  **文件归档与压缩**：理解了`tar`命令归档与压缩的区别，熟练掌握了使用`tar`结合`gzip`、`bzip2`进行打包压缩和解压的操作。
3.  **远程文件操作**：学会了使用`scp`进行安全文件拷贝，以及使用更高效的`rsync`命令进行增量同步和备份。
4.  **文件系统链接**：理解了inode的概念，并能够区分和创建硬链接与软链接。
5.  **软件包管理**：掌握了`rpm`命令的基本用法，并重点学习了通过配置YUM仓库和使用`yum`命令来自动化安装、管理软件，解决了依赖关系这一核心难题。

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_150.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_152.png)

![](img/f2f4b0a05b45e3ccc069c6ca400d0488_154.png)

这些技能是成为一名合格的Linux系统工程师的基石，请务必通过实验熟练掌握。