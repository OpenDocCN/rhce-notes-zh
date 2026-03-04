# Linux基础入门教学：9：应用程序安装与管理 🚀

在本节课中，我们将学习如何在Linux系统上安装和管理应用程序。我们将重点介绍两种主流的安装方式：`rpm` 和 `yum`/`dnf`，并通过简单的例子帮助初学者理解其工作原理和基本操作。

![](img/7052864e9c727138f793bff68837c07b_1.png)

---

![](img/7052864e9c727138f793bff68837c07b_3.png)

## 回顾：用户管理

上一节我们介绍了Linux用户和权限管理。在Linux系统中，用户主要分为三类：
*   **超级用户**：UID和GID均为 `0`。
*   **系统用户**：UID和GID范围为 `1` 到 `999`，通常用于运行系统服务。
*   **普通用户**：UID和GID从 `1000` 开始。

创建一个普通用户会写入六个核心配置文件：
1.  用户的家目录。
2.  `/var/spool/mail/` 下的用户邮件文件。
3.  `/etc/passwd`：用户账户信息。
4.  `/etc/shadow`：用户密码信息。
5.  `/etc/group`：用户组信息。
6.  `/etc/gshadow`：用户组密码信息。

---

![](img/7052864e9c727138f793bff68837c07b_5.png)

## 应用程序的构成

![](img/7052864e9c727138f793bff68837c07b_7.png)

![](img/7052864e9c727138f793bff68837c07b_9.png)

一个完整的应用程序通常包含四个部分：
1.  **可执行程序**：可以直接运行的文件，如Windows的 `.exe` 文件。
2.  **配置文件**：程序运行所需的设置文件。
3.  **帮助文件**：程序的使用说明文档。
4.  **库文件**：程序运行所依赖的共享代码模块。在Windows中是 `.dll` 文件，在Linux中通常是 `.ko` 模块或 `.so` 共享库。

![](img/7052864e9c727138f793bff68837c07b_11.png)

---

![](img/7052864e9c727138f793bff68837c07b_13.png)

## Linux应用程序安装方式

在Linux上，有多种安装应用程序的方式。本节重点介绍两种在Red Hat系列发行版（如CentOS、RHEL）中主流的工具：
*   **`rpm`**：Red Hat Package Manager，用于管理 `.rpm` 格式的软件包。
*   **`yum` / `dnf`**：基于 `rpm` 的高级包管理工具，能自动解决软件依赖关系。

> **注意**：不同的Linux发行版使用不同的包格式。例如，Debian/Ubuntu系列使用 `.deb` 格式。

---

![](img/7052864e9c727138f793bff68837c07b_15.png)

## 使用 `rpm` 管理软件包

![](img/7052864e9c727138f793bff68837c07b_17.png)

`rpm` 是基础的软件包管理工具。要使用它，我们通常需要先挂载包含软件包的光盘或指定软件包所在的目录。

首先，我们挂载光盘到 `/mnt` 目录：
```bash
mount /dev/cdrom /mnt
```
然后，进入软件包目录。在RHEL 8中，软件包位于 `/mnt/AppStream/Packages/` 和 `/mnt/BaseOS/Packages/`。

![](img/7052864e9c727138f793bff68837c07b_19.png)

以下是 `rpm` 的常用操作，我们通过一个“购买泡面”的比喻来理解：

![](img/7052864e9c727138f793bff68837c07b_21.png)

### 查询软件包
查询系统是否已安装某个软件（就像查看家里有没有泡面）。
```bash
rpm -q vsftpd
```

![](img/7052864e9c727138f793bff68837c07b_23.png)

![](img/7052864e9c727138f793bff68837c07b_25.png)

![](img/7052864e9c727138f793bff68837c07b_27.png)

![](img/7052864e9c727138f793bff68837c07b_29.png)

![](img/7052864e9c727138f793bff68837c07b_31.png)

### 安装软件包
安装软件需要指定软件包的**完整路径和名称**（就像去小卖部买泡面，必须找到具体的商品）。
```bash
rpm -ivh /mnt/AppStream/Packages/vsftpd-3.0.3-28.el8.x86_64.rpm
```
*   `-i`：安装。
*   `-v`：显示详细信息。
*   `-h`：显示安装进度条。

![](img/7052864e9c727138f793bff68837c07b_33.png)

![](img/7052864e9c727138f793bff68837c07b_35.png)

![](img/7052864e9c727138f793bff68837c07b_37.png)

![](img/7052864e9c727138f793bff68837c07b_39.png)

### 卸载软件包
卸载已安装的软件（就像把不想要的泡面扔掉）。
```bash
rpm -e vsftpd
```

### 查询所有已安装的软件
```bash
rpm -qa
```

![](img/7052864e9c727138f793bff68837c07b_41.png)

![](img/7052864e9c727138f793bff68837c07b_43.png)

![](img/7052864e9c727138f793bff68837c07b_45.png)

### 查询软件包详细信息
*   查询**已安装**软件的信息：
    ```bash
    rpm -qi vsftpd
    ```
*   查询**未安装**的软件包文件信息（需要完整路径）：
    ```bash
    rpm -qpi /path/to/package.rpm
    ```

![](img/7052864e9c727138f793bff68837c07b_47.png)

![](img/7052864e9c727138f793bff68837c07b_49.png)

### 查询软件包安装的文件列表
```bash
rpm -ql vsftpd
```

### 处理依赖问题
安装时若提示依赖其他软件包，可以使用 `--nodeps` 忽略依赖强制安装（不推荐，可能导致软件无法运行）。
```bash
rpm -ivh --nodeps package.rpm
```

![](img/7052864e9c727138f793bff68837c07b_51.png)

### 升级软件包
*   `-U`：如果软件未安装则安装，已安装则升级。
*   `-F`：仅升级已安装的软件包。
```bash
rpm -Uvh package-new.rpm
```

![](img/7052864e9c727138f793bff68837c07b_53.png)

### 强制安装
在升级内核等特殊场景，当系统提示已安装时，可使用 `--force` 强制重新安装。
```bash
rpm -ivh --force kernel-package.rpm
```

![](img/7052864e9c727138f793bff68837c07b_55.png)

![](img/7052864e9c727138f793bff68837c07b_57.png)

---

## 使用 `yum/dnf` 管理软件包

`rpm` 工具在解决复杂的软件依赖关系时比较麻烦。`yum`（或新一代的 `dnf`）通过维护一个“软件仓库”来解决这个问题。你可以把仓库想象成一个大型超市，`yum` 则是智能导购，帮你找到商品并自动解决“买A需要先买B和C”的问题。

### 配置本地 `yum` 仓库
首先，我们需要创建仓库配置文件，告诉系统去哪里找软件包。

![](img/7052864e9c727138f793bff68837c07b_59.png)

![](img/7052864e9c727138f793bff68837c07b_61.png)

![](img/7052864e9c727138f793bff68837c07b_63.png)

![](img/7052864e9c727138f793bff68837c07b_65.png)

1.  进入仓库配置目录：
    ```bash
    cd /etc/yum.repos.d/
    ```
2.  创建一个新的仓库文件，必须以 `.repo` 结尾，例如 `local.repo`：
    ```bash
    vim local.repo
    ```
3.  写入以下内容（以配置 `AppStream` 仓库为例）：
    ```ini
    [AppStream]          # 仓库ID，唯一标识
    name=Local AppStream # 仓库描述信息
    baseurl=file:///mnt/AppStream # 仓库位置，file://表示本地文件
    enabled=1            # 启用此仓库（1启用，0禁用）
    gpgcheck=0           # 不检查GPG密钥（学习环境可关闭）
    ```
    可以按同样格式添加多个仓库，如 `[BaseOS]`。

### `yum` 常用命令
配置好仓库后，就可以使用 `yum` 命令了。

*   **安装软件**：无需指定完整路径，自动解决依赖。
    ```bash
    yum install gcc
    ```
*   **卸载软件**：
    ```bash
    yum remove gcc
    ```
*   **查询所有已安装软件**：
    ```bash
    yum list installed
    ```
*   **搜索软件包**：
    ```bash
    yum search keyword
    ```
*   **查看软件包信息**：
    ```bash
    yum info package_name
    ```
*   **更新所有软件包**：
    ```bash
    yum update
    ```
*   **安装软件组**（如开发工具套件）：
    ```bash
    yum groupinstall "Development Tools"
    ```

![](img/7052864e9c727138f793bff68837c07b_67.png)

![](img/7052864e9c727138f793bff68837c07b_69.png)

### `dnf` 命令
`dnf` 是 `yum` 的下一代版本，命令用法基本相同，但性能更好，支持并行操作。
```bash
dnf install gcc
```

![](img/7052864e9c727138f793bff68837c07b_71.png)

![](img/7052864e9c727138f793bff68837c07b_73.png)

![](img/7052864e9c727138f793bff68837c07b_75.png)

![](img/7052864e9c727138f793bff68837c07b_77.png)

### 配置网络 `yum` 源（拓展）
除了本地源，还可以配置网络源来获取最新的软件。这通常涉及添加第三方仓库配置文件并导入其GPG密钥。
```bash
# 示例：添加EPEL仓库（需网络）
yum install epel-release
```
安装后，会在 `/etc/yum.repos.d/` 目录下生成相应的 `.repo` 文件。

![](img/7052864e9c727138f793bff68837c07b_79.png)

---

![](img/7052864e9c727138f793bff68837c07b_81.png)

![](img/7052864e9c727138f793bff68837c07b_83.png)

![](img/7052864e9c727138f793bff68837c07b_85.png)

## 总结

![](img/7052864e9c727138f793bff68837c07b_87.png)

本节课我们一起学习了在Linux系统中管理应用程序的核心方法。

![](img/7052864e9c727138f793bff68837c07b_89.png)

![](img/7052864e9c727138f793bff68837c07b_91.png)

1.  我们回顾了应用程序的四个组成部分。
2.  重点掌握了 `rpm` 包管理工具的基本操作：安装（`-ivh`）、查询（`-q`、`-qi`、`-ql`）、卸载（`-e`）和升级（`-Uvh`）。
3.  深入学习了更强大的 `yum/dnf` 工具，它通过配置软件仓库，极大地简化了依赖管理和软件安装过程。我们学会了如何配置本地仓库文件，并使用 `install`、`remove`、`search` 等命令高效管理软件。

![](img/7052864e9c727138f793bff68837c07b_93.png)

![](img/7052864e9c727138f793bff68837c07b_95.png)

理解并掌握这两种工具，是高效使用Red Hat系列Linux发行版的基础。希望大家通过实践，能够熟练地进行软件安装与管理。