# Linux软件包管理：P33：RPM软件包管理、YUM本地软件仓库搭建、YUM常用命令学习 📦

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_1.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_2.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_4.png)

在本节课中，我们将要学习Linux系统中两种重要的软件包管理工具：RPM和YUM。我们将从基础的RPM命令开始，然后重点学习如何搭建一个本地的YUM软件仓库，并掌握YUM的常用命令，以实现便捷的软件安装、查询和管理。

## 概述

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_6.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_8.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_10.png)

上一节我们介绍了RPM软件包的基本概念和手动安装方法。本节中我们来看看如何利用YUM工具，通过搭建本地软件仓库，实现软件包的自动依赖解决和高效管理。

## 本地YUM仓库搭建 🏗️

我们已经知道，系统的ISO镜像文件中包含了大量（例如4000多个）可用的软件包。这些软件包就在本地的镜像文件里。现在，我们需要学习如何搭建一个本地仓库，以便YUM命令能够从本地获取并安装这些软件包。

本地仓库也称为本地YUM源。“源”指的是软件包的来源。本地YUM源意味着软件包的来源位于本机，因此它是一个本地的软件仓库。

以下是搭建本地YUM仓库的详细步骤：

### 1. 创建仓库配置文件

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_12.png)

YUM命令在安装软件时会自动到系统的特定路径下寻找仓库配置文件。因此，配置文件的路径和名称有严格要求。

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_14.png)

*   **路径**：必须位于 `/etc/yum.repos.d/` 目录下。
*   **文件名**：必须以 `.repo` 作为扩展名。

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_16.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_18.png)

如果文件不在这个路径，或者扩展名不是 `.repo`，YUM将不会读取其中的配置，也就无法按照配置去下载软件包。

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_20.png)

现在，我们就在指定路径下创建一个配置文件，例如 `local.repo`。

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_22.png)

```bash
vim /etc/yum.repos.d/local.repo
```

### 2. 编写仓库配置

打开文件后，需要写入具体的配置信息。一个基本的本地仓库配置包含以下几个部分：

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_24.png)

```ini
[local]
name=local centOS-ISO
baseurl=file:///mnt/centos/
enabled=1
gpgcheck=0
```

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_26.png)

我们来逐一解释每个配置项的含义：

*   **`[local]`**：这是仓库的名称，放在中括号内。名称可以自定义（避免使用中文），这里我们命名为 `local`。
*   **`name=local centOS-ISO`**：这是仓库的描述信息，用 `name` 关键字指定。它类似于仓库的备注，可以更清晰地说明仓库内容。
*   **`baseurl=file:///mnt/centos/`**：这是**最重要**的配置项，用于定义软件包的实际存放地址。
    *   `file://` 是固定格式，表示使用本地文件协议。
    *   `/mnt/centos/` 是之前挂载系统ISO镜像的目录路径。YUM会自动在该路径下寻找名为 `Packages` 的目录来获取软件包。**此路径必须正确**，否则YUM将找不到任何软件包。
*   **`enabled=1`**：此选项决定是否启用该仓库。`1` 表示启用，`0` 表示禁用。如果不写此行，默认也是启用状态。
*   **`gpgcheck=0`**：此选项决定是否检查软件包的GPG签名（即软件包内的密钥ID）。`1` 表示检查，`0` 表示不检查。
    *   对于自行搭建的本地仓库，通常没有严格的签名验证需求，设置为 `0` 可以避免因签名检查失败导致安装失败。
    *   对于官方网络仓库，此项通常为 `1`，并且需要配合 `gpgkey` 指定密钥文件地址。

配置完成后，保存并退出编辑器。至此，本地YUM仓库就搭建完成了。

## YUM常用命令学习 🛠️

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_28.png)

仓库搭建好后，我们就可以使用YUM命令来管理软件了。YUM最大的优势在于能够自动解决软件依赖关系。

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_30.png)

### 安装软件包

使用 `yum install` 命令安装软件。为了省去安装过程中的确认步骤，可以加上 `-y` 参数自动回答“yes”。

**命令格式**：
```bash
yum -y install <软件包名>
```

**示例**：安装GCC编译器
```bash
yum -y install gcc
```
执行后，YUM不仅会安装 `gcc` 主包，还会自动安装其所需的6个依赖包，这一切都是自动完成的。

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_32.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_34.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_36.png)

**示例**：安装VIM编辑器
```bash
yum -y install vim
```
VIM是一个功能更强大的文本编辑器，支持语法高亮，非常适合编辑配置文件和编程。安装它可能需要28个依赖，但YUM会一并处理。

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_38.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_39.png)

### 查询仓库信息

以下是几个常用的查询命令：

*   **`yum repolist`**：列出所有已配置并启用的仓库信息，包括仓库ID、名称和软件包数量。
*   **`yum list`**：列出仓库中所有可用的软件包（包括已安装和未安装的）。可以使用管道符 `|` 和 `grep` 命令进行过滤。
    ```bash
    # 列出所有包
    yum list

    # 过滤出包含‘java’关键词的包
    yum list | grep java

    # 进一步过滤出包含‘jdk’的包
    yum list | grep java | grep jdk
    ```
    这种方法在记不清完整包名时非常有用。
*   **`yum search <关键词>`**：在软件包名称和描述中搜索包含关键词的包。
    ```bash
    yum search java
    ```

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_41.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_43.png)

### 其他常用命令

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_45.png)

*   **`yum update`**：更新所有已安装的软件包到最新版本。**注意**：在生产环境中执行更新前，务必做好备份。
*   **`yum remove <软件包名>`**：移除指定的软件包（但通常不会自动移除其依赖）。

## 网络仓库配置示例 🌐

除了本地仓库，我们经常需要配置网络仓库来获取更新或更丰富的软件。以安装Nginx为例，其官方提供了仓库配置。

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_47.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_49.png)

1.  在 `/etc/yum.repos.d/` 目录下创建仓库文件，例如 `nginx.repo`。
2.  将官方提供的配置粘贴进去。网络仓库的 `baseurl` 通常是 `http://` 或 `https://` 开头的URL，并且 `gpgcheck` 通常为 `1`，同时会指定 `gpgkey`。
    ```ini
    [nginx-stable]
    name=nginx stable repo
    baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
    gpgcheck=1
    enabled=1
    gpgkey=https://nginx.org/keys/nginx_signing.key
    ```
3.  保存后，就可以使用 `yum -y install nginx` 来安装了。

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_50.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_52.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_54.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_56.png)

**重要提示**：当系统同时配置了多个仓库（本地和网络）时，YUM默认会优先从网络仓库获取软件包。

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_58.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_60.png)

## 总结

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_62.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_64.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_65.png)

![](img/b5f8574e20e7f2857e9bc995b45ac1b9_67.png)

本节课中我们一起学习了Linux软件包管理的核心内容。我们首先回顾了RPM的基础操作，然后重点掌握了如何搭建一个本地的YUM软件仓库，这使我们能够方便地利用系统镜像文件中的大量软件包。最后，我们学习了YUM的常用命令，包括安装、查询、更新和移除软件包，并了解了YUM自动解决依赖关系的强大功能，以及如何配置网络仓库来扩展软件来源。掌握这些工具和方法，将极大地提升我们在Linux系统中管理软件的效率和便捷性。