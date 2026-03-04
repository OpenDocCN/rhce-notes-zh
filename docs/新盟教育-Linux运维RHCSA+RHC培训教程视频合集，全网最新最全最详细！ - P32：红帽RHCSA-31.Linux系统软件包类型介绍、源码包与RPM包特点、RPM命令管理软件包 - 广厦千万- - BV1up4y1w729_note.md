# Linux软件包管理：第32课：Linux系统软件包类型介绍、源码包与RPM包特点、RPM命令管理软件包 📦

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_1.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_3.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_5.png)

在本节课中，我们将要学习Linux系统中软件包管理的基础知识。软件包管理是Linux运维中的核心技能，它关系到如何在系统中安装、查询和维护各种应用程序。我们将从软件包的类型开始，详细对比源码包和RPM二进制包的特点，并学习如何使用RPM命令来管理软件包。

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_7.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_9.png)

## 软件包类型介绍

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_11.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_12.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_14.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_15.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_17.png)

上一节我们介绍了软件包管理的重要性，本节中我们来看看Linux系统中软件包的主要类型。

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_19.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_21.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_23.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_24.png)

在Linux系统下，软件包主要分为两大类：**源码包**和**二进制包**（通常指RPM包）。理解它们的区别是选择合适安装方式的关键。

### 开源与闭源软件

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_26.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_28.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_29.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_31.png)

在深入讲解软件包类型前，需要先理解开源与闭源的概念。

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_33.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_34.png)

*   **开源软件**：软件的源代码公开发布，允许用户查看、修改和进行二次开发。例如，淘宝基于Nginx二次开发出了Tengine服务器。开源软件的优势在于用户使用放心（代码公开可审查），并且即使原开发者停止维护，社区也可能继续维护。
*   **闭源软件**：软件的源代码不公开，用户无法查看和修改。例如，Windows操作系统。闭源软件的缺点是，一旦开发者停止维护（如Windows 7），用户遇到问题将难以自行解决。

开源软件不一定免费，它可能通过提供技术支持或高级功能来收费。

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_36.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_38.png)

### 源码包的特点

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_40.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_41.png)

源码包是直接提供软件源代码的压缩包。例如，我们从官网下载的Nginx压缩包。

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_43.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_45.png)

以下是源码包的主要特点：

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_46.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_48.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_49.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_51.png)

*   **优点**：
    *   **开放源代码**：用户可以查看和修改源代码。
    *   **安装灵活**：在编译过程中，可以**自定义安装路径**和**选择需要的功能模块**，有助于节约系统资源。
    *   **卸载方便**：直接删除自定义的安装目录即可彻底卸载，无残留文件。
*   **缺点**：
    *   **安装过程复杂**：需要用户手动执行编译过程，将源代码转换为计算机可执行的二进制文件。
    *   **依赖关系需手动解决**：安装前需要自行安装该软件所依赖的其他库或软件包。

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_53.png)

在企业中，对安全性、定制化要求极高的场景（如银行系统）可能会选择源码包安装，以实现最小化安装和严格的功能控制。

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_55.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_57.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_59.png)

### RPM二进制包的特点

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_61.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_63.png)

RPM包是Red Hat系列Linux发行版（如CentOS、RHEL）使用的预编译二进制软件包，其后缀名为 `.rpm`。

以下是RPM包的主要特点：

*   **优点**：
    *   **安装简单快捷**：软件已经由官方预先编译好，无需用户手动编译，安装速度快。我们之前使用的 `yum -y install` 命令安装的就是RPM包。
    *   **功能全面**：官方编译的包通常会包含软件的绝大多数功能，以满足大部分用户的通用需求。
*   **缺点**：
    *   **无法自定义功能**：用户不能选择安装哪些功能，通常会安装所有功能。
    *   **无法自定义安装路径**：安装路径由包定义，用户无法指定。
    *   **看不到源代码**：因为是二进制文件，用户无法查看其源代码。

对于大多数日常需求，使用RPM包安装是最简单高效的方式。

## RPM包命名规则与来源

了解了RPM包的特点后，我们来看看如何识别它以及从哪里获取它。

一个典型的RPM包名称遵循以下规则：
`vsftpd-3.0.2-25.el7.x86_64.rpm`

*   `vsftpd`：软件包名称。
*   `3.0.2`：软件版本号（主版本.次版本.修订版本）。
*   `25`：发布版本号，代表该版本软件被打过25次补丁。
*   `el7`：适合的操作系统平台（例如，`el7` 表示 Red Hat Enterprise Linux 7 / CentOS 7）。
*   `x86_64`：适合的CPU架构（64位）。
*   `.rpm`：扩展名，表明这是一个RPM包。

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_65.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_67.png)

RPM包的来源主要有两个：
1.  **系统镜像**：安装系统时使用的ISO镜像文件内包含了大量的RPM软件包（通常有数千个）。这些包位于镜像的 `Packages/` 目录下。
2.  **网络仓库**：互联网上由社区或厂商维护的软件仓库，可以通过 `yum` 命令自动从网络下载并安装。

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_69.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_71.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_73.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_75.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_76.png)

要使用镜像中的软件包，需要先将镜像文件挂载到系统目录。例如：
```bash
mkdir /mnt/cdrom
mount /dev/cdrom /mnt/cdrom
# 然后即可访问 /mnt/cdrom/Packages/ 目录下的所有RPM包
```

## RPM命令管理软件包

现在，我们开始学习如何使用 `rpm` 命令来管理这些RPM软件包。`rpm` 命令功能强大，但需要手动解决软件包的依赖关系（即一个软件运行需要其他软件或库的支持）。依赖关系可能很复杂，包括树形依赖、环形依赖等，手动解决较为繁琐。

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_78.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_80.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_82.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_84.png)

以下是 `rpm` 命令最常用的选项组合：

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_86.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_88.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_90.png)

### 安装软件包

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_91.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_92.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_93.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_95.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_97.png)

使用 `-ivh` 选项组合进行安装。
*   `-i`：安装（install）。
*   `-v`：显示详细信息（verbose）。
*   `-h`：显示安装进度条（hash）。

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_99.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_101.png)

**命令格式**：`rpm -ivh <软件包全名>`
安装时必须指定软件包的**完整名称**（包括版本、架构等信息）。如果当前目录有该包，可以使用 `Tab` 键自动补全。
```bash
cd /mnt/cdrom/Packages
rpm -ivh vsftpd-3.0.2-25.el7.x86_64.rpm
```

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_103.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_105.png)

### 查询软件包

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_107.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_109.png)

查询是 `rpm` 命令最常用的功能之一。

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_111.png)

*   **查询软件是否安装**：`rpm -q <软件名>`
    ```bash
    rpm -q vsftpd
    ```

*   **查询系统中所有已安装的软件包**：`rpm -qa`
    通常会结合 `grep` 进行过滤，或使用 `wc -l` 统计数量。
    ```bash
    rpm -qa | grep vsf
    rpm -qa | wc -l
    ```

*   **查询软件包的详细信息**：`rpm -qi <软件名>`
    可以查看软件的描述、版本、安装时间、大小等详细信息。
    ```bash
    rpm -qi vsftpd
    ```

*   **查询软件包安装了哪些文件**：`rpm -ql <软件名>`
    列出该软件包在系统中安装的所有文件及其路径。
    ```bash
    rpm -ql coreutils # 查看提供基础命令的coreutils包安装了哪些文件
    ```

*   **查询某个文件是由哪个软件包安装的**：`rpm -qf <文件绝对路径>`
    ```bash
    rpm -qf /etc/passwd
    rpm -qf /bin/ls
    ```

### 软件包与程序名

请注意，通过 `rpm` 管理时使用的是**程序名**（有时也叫软件名），而不是完整的RPM包名。程序名通常与包名的主体部分相同（如 `vsftpd`），但也有例外（如 `samba` 包的程序名是 `smb`）。如果不确定，可以使用 `rpm -qa | grep` 进行模糊搜索。

---

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_113.png)

![](img/5f3ea0d730eabe5d4de7bcb710c3253f_115.png)

本节课中我们一起学习了Linux软件包的基础知识。我们区分了源码包和RPM二进制包的特点与适用场景，了解了RPM包的命名规则，并重点掌握了使用 `rpm` 命令进行软件包安装和多种方式查询的核心技能。虽然 `rpm` 命令需要手动处理依赖，但其强大的查询功能在日常运维中极其有用。下节课我们将学习更先进的 `yum` 工具，它能自动解决依赖关系，让软件包管理变得更加轻松。