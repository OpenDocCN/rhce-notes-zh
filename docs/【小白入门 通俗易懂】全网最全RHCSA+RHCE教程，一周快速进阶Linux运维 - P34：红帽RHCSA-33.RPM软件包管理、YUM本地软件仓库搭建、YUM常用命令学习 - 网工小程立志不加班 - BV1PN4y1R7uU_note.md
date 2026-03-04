# Linux软件包管理：P34：RPM软件包管理、YUM本地软件仓库搭建、YUM常用命令学习 🐧

![](img/6b6f59fe442347d4af9d4e573c0ba927_0.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_2.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_4.png)

在本节课中，我们将要学习Linux系统中至关重要的软件包管理知识。我们将从基础的RPM软件包管理入手，然后重点学习如何搭建一个本地的YUM软件仓库，最后掌握一系列实用的YUM命令。通过本章的学习，你将能够高效地管理服务器上的软件，并理解其背后的工作原理。

![](img/6b6f59fe442347d4af9d4e573c0ba927_6.png)

## 本地YUM仓库的搭建 🏗️

![](img/6b6f59fe442347d4af9d4e573c0ba927_8.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_10.png)

上一节我们介绍了RPM软件包的基本管理。本节中我们来看看如何搭建一个本地的YUM软件仓库，以便更方便地安装和管理软件。

本地软件仓库，也称为本地YUM源。“源”指的是软件包的来源。本地YUM源意味着软件包存储在我们自己的服务器上。

搭建本地仓库需要遵循特定的配置规则。以下是配置步骤：

首先，仓库配置文件必须存放在 `/etc/yum.repos.d/` 目录下。YUM命令在安装软件时会自动到这个目录下寻找配置文件。

其次，配置文件的名称必须以 `.repo` 作为后缀。如果文件不以 `.repo` 结尾，YUM将不会读取其中的配置。

满足了路径和文件名要求后，就可以开始编写配置文件内容了。

![](img/6b6f59fe442347d4af9d4e573c0ba927_12.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_14.png)

以下是配置一个本地仓库的示例内容：

![](img/6b6f59fe442347d4af9d4e573c0ba927_16.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_18.png)

```bash
[local]
name=local centos-iso
baseurl=file:///mnt/centos/
enabled=1
gpgcheck=0
```

![](img/6b6f59fe442347d4af9d4e573c0ba927_20.png)

我们来逐一解释这些配置项的含义：
*   **`[local]`**： 这是仓库的名称，定义在中括号内，可以自定义，但建议不要使用中文。
*   **`name=local centos-iso`**： 这是仓库的描述信息，用 `name` 关键字指定，是对仓库的简要说明。
*   **`baseurl=file:///mnt/centos/`**： 这是**最重要**的一行，它定义了软件包的实际存放路径。`file://` 是固定语法，用于指定本地路径。这里的 `/mnt/centos/` 应指向包含 `Packages` 目录的挂载点。YUM会自动在该路径下的 `Packages` 目录中查找软件包。
*   **`enabled=1`**： 这个选项控制是否启用此仓库。`1` 表示启用，`0` 表示禁用。如果不写此行，默认状态也是启用。
*   **`gpgcheck=0`**： 这个选项控制是否检查软件包的GPG签名。`1` 表示检查，`0` 表示不检查。对于自己搭建的本地仓库，通常设置为 `0` 以避免签名验证失败导致安装出错。如果是配置网络官方仓库，则通常需要设置为 `1` 并配合 `gpgkey` 指定密钥文件。

![](img/6b6f59fe442347d4af9d4e573c0ba927_22.png)

配置文件保存退出后，一个本地YUM仓库就搭建完成了。此时，当你使用 `yum install` 命令安装软件时，YUM就会从这个本地仓库中查找软件包并自动解决依赖关系。

## YUM常用命令详解 ⚙️

在成功搭建本地仓库后，我们需要掌握如何使用YUM工具。YUM最大的优势在于能自动处理软件包依赖。以下是几个核心的YUM命令。

![](img/6b6f59fe442347d4af9d4e573c0ba927_24.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_26.png)

**1. 列出可用仓库与软件包**

使用 `yum repolist` 命令可以列出系统中所有已配置并启用的仓库信息。

```bash
yum repolist
```
这条命令会显示仓库ID、仓库名称、状态以及仓库中包含的软件包数量。

**2. 搜索与列出软件包**

当我们需要安装一个软件但不确定完整包名时，`yum list` 命令非常有用。

![](img/6b6f59fe442347d4af9d4e573c0ba927_28.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_30.png)

```bash
# 列出仓库中所有软件包（包括已安装和未安装的）
yum list

# 结合 grep 过滤查找特定软件包
yum list | grep java
yum list | grep java | grep jdk
```
通过管道符 `|` 配合 `grep` 命令进行关键字过滤，可以快速定位到我们想要的软件包。

**3. 安装软件包**

![](img/6b6f59fe442347d4af9d4e573c0ba927_32.png)

使用 `yum install` 命令安装软件。建议加上 `-y` 选项来自动确认安装提示，使安装过程无需人工干预。

![](img/6b6f59fe442347d4af9d4e573c0ba927_34.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_36.png)

```bash
# 安装软件包，并自动确认
yum -y install package_name

![](img/6b6f59fe442347d4af9d4e573c0ba927_38.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_40.png)

# 例如，安装 vim 编辑器
yum -y install vim
```
在安装过程中，YUM会显示将要安装的主包及其所有依赖包，并自动将它们全部安装到系统中。

**4. 其他实用命令**

![](img/6b6f59fe442347d4af9d4e573c0ba927_42.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_44.png)

除了安装，YUM还提供其他管理功能：
*   **`yum update`**： 更新指定的软件包到最新版本。更新前请注意做好数据备份。
*   **`yum remove`**： 移除指定的软件包（但默认不删除其依赖包）。
*   **`yum search`**： 在软件包描述和名称中搜索关键字。
*   **`yum provides`**： 查找某个特定文件是由哪个软件包提供的。

## 网络YUM仓库配置示例 🌐

![](img/6b6f59fe442347d4af9d4e573c0ba927_46.png)

有时我们需要安装官方提供的最新软件，这就需要配置网络YUM仓库。其原理与本地仓库类似，但 `baseurl` 指向互联网地址，并且通常需要启用GPG签名检查。

以下是配置Nginx官方网络仓库的示例：

![](img/6b6f59fe442347d4af9d4e573c0ba927_48.png)

```bash
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
```
在这个配置中：
*   **`baseurl`** 使用了 `http://` 协议，指向Nginx官方的软件包存储地址。
*   **`gpgcheck=1`** 启用了签名验证。
*   **`gpgkey`** 指定了用于验证签名的公钥文件地址。

![](img/6b6f59fe442347d4af9d4e573c0ba927_50.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_52.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_54.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_56.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_58.png)

网络仓库的配置信息通常可以从软件官方网站获取。配置完成后，就可以像使用本地仓库一样，使用 `yum install nginx` 来安装软件了。

![](img/6b6f59fe442347d4af9d4e573c0ba927_60.png)

---

![](img/6b6f59fe442347d4af9d4e573c0ba927_62.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_64.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_66.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_67.png)

![](img/6b6f59fe442347d4af9d4e573c0ba927_69.png)

本节课中我们一起学习了Linux软件包管理的核心内容。我们从理解RPM包格式开始，逐步完成了本地YUM软件仓库的搭建，并掌握了 `yum install`， `yum list`， `yum repolist` 等关键命令的使用方法。理解本地仓库的配置项，特别是 `baseurl` 和 `gpgcheck` 的含义，是灵活运用YUM的基础。同时，我们也了解了如何配置网络仓库以获取更广泛的软件源。掌握这些技能，将极大地提升你在Linux系统上部署和维护软件的效率。