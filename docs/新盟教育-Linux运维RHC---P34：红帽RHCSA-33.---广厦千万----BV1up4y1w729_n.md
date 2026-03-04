# Linux软件包管理：P34：RPM软件包管理、YUM本地软件仓库搭建、YUM常用命令学习 📦

![](img/fc6e24a7dab147700a24e1df430aee73_1.png)

![](img/fc6e24a7dab147700a24e1df430aee73_2.png)

![](img/fc6e24a7dab147700a24e1df430aee73_4.png)

在本节课中，我们将要学习Linux系统中两种重要的软件包管理工具：RPM和YUM。我们将从基础的RPM命令开始，然后重点学习如何搭建一个本地的YUM软件仓库，并掌握YUM的常用命令，以实现便捷的软件安装与管理。

## 概述

软件包管理是Linux系统运维的核心技能之一。RPM（Red Hat Package Manager）是红帽系列Linux发行版的基础包管理工具，而YUM（Yellowdog Updater, Modified）则是在RPM基础上构建的、能够自动解决依赖关系的更高级工具。本节课将带你从手动管理RPM包，过渡到自动化管理的YUM，并亲手搭建一个本地软件仓库。

![](img/fc6e24a7dab147700a24e1df430aee73_6.png)

---

![](img/fc6e24a7dab147700a24e1df430aee73_8.png)

![](img/fc6e24a7dab147700a24e1df430aee73_10.png)

## RPM软件包管理基础

上一节我们介绍了软件包管理的概念，本节中我们来看看最基础的RPM工具。RPM允许我们直接安装、查询、验证、升级和卸载以`.rpm`为后缀的软件包文件。

### RPM常用命令

以下是RPM工具的几个核心操作命令：

*   **安装软件包**：`rpm -ivh 软件包全名.rpm`
    *   `-i`：安装。
    *   `-v`：显示详细信息。
    *   `-h`：显示安装进度条。
*   **查询已安装的软件包**：`rpm -qa`
    *   `-q`：查询。
    *   `-a`：所有。
*   **卸载软件包**：`rpm -e 软件包名`
    *   `-e`：卸载。

**注意**：使用RPM安装时，如果软件包有依赖关系，需要手动先安装所有依赖包，过程较为繁琐。

---

## 搭建本地YUM软件仓库 🏗️

上一节我们介绍了基础的RPM管理，本节中我们来看看如何搭建一个本地的YUM仓库，以享受自动解决依赖的便利。

![](img/fc6e24a7dab147700a24e1df430aee73_12.png)

![](img/fc6e24a7dab147700a24e1df430aee73_14.png)

首先，我们需要一个软件包的来源。通常，我们将系统安装镜像文件挂载到本地目录，从中获取大量的RPM软件包。

### 挂载系统镜像

![](img/fc6e24a7dab147700a24e1df430aee73_16.png)

假设我们已经将CentOS的系统镜像文件挂载到了 `/mnt/centos` 目录下。该目录下的 `Packages/` 子目录中包含了4000多个可用的软件包。

![](img/fc6e24a7dab147700a24e1df430aee73_18.png)

### 配置本地YUM仓库文件

![](img/fc6e24a7dab147700a24e1df430aee73_20.png)

![](img/fc6e24a7dab147700a24e1df430aee73_22.png)

YUM仓库的配置文件有严格的存放路径和命名规则。

1.  **路径**：必须位于 `/etc/yum.repos.d/` 目录下。
2.  **文件名**：必须以 `.repo` 作为扩展名。

现在，我们在此路径下创建一个本地仓库配置文件，例如 `local.repo`。

```bash
vim /etc/yum.repos.d/local.repo
```

打开文件后，需要写入以下配置内容：

![](img/fc6e24a7dab147700a24e1df430aee73_24.png)

```ini
[local]
name=local centOS-ISO
baseurl=file:///mnt/centos
enabled=1
gpgcheck=0
```

![](img/fc6e24a7dab147700a24e1df430aee73_26.png)

以下是每个配置项的含义：

*   `[local]`：仓库的名称，自定义，放在中括号内。
*   `name=local centOS-ISO`：仓库的描述信息，可自定义。
*   `baseurl=file:///mnt/centos`：**这是最重要的配置**。它指定了软件包的实际存放路径。`file://` 是固定格式，表示本地文件协议，后面接镜像挂载的绝对路径。
*   `enabled=1`：启用此仓库（1启用，0禁用）。
*   `gpgcheck=0`：不检查软件包的GPG签名。对于自建的本地仓库，通常设置为0以简化流程。如果设置为1，则必须通过 `gpgkey` 指定签名密钥文件。

保存并退出文件后，本地YUM仓库就配置完成了。

---

## YUM常用命令学习 🛠️

仓库搭建好后，我们就可以使用YUM命令来方便地管理软件了。YUM最大的优势在于能自动处理依赖关系。

### 1. 列出仓库信息

![](img/fc6e24a7dab147700a24e1df430aee73_28.png)

使用以下命令可以查看已配置的仓库列表及其状态：

![](img/fc6e24a7dab147700a24e1df430aee73_30.png)

```bash
yum repolist
```

这条命令会显示所有启用的仓库名称、描述和软件包数量。

### 2. 安装软件

安装软件时，使用 `install` 参数。`-y` 选项表示自动回答“yes”，省去安装确认环节。

```bash
yum -y install 软件包名
```
例如，安装VIM编辑器：
```bash
yum -y install vim
```
YUM会自动分析`vim`所需的所有依赖包（可能是几十个），并一次性全部安装。

![](img/fc6e24a7dab147700a24e1df430aee73_32.png)

### 3. 搜索软件

![](img/fc6e24a7dab147700a24e1df430aee73_34.png)

![](img/fc6e24a7dab147700a24e1df430aee73_36.png)

如果不记得软件包的全名，可以使用搜索功能。`list` 命令能列出仓库中的所有包，结合 `grep` 进行过滤是常用方法。

```bash
# 列出所有包含‘java’关键词的包
yum list | grep java

![](img/fc6e24a7dab147700a24e1df430aee73_38.png)

![](img/fc6e24a7dab147700a24e1df430aee73_39.png)

# 进一步过滤出包含‘jdk’的包
yum list | grep java | grep jdk
```

### 4. 卸载软件

使用 `remove` 参数来卸载软件包。

![](img/fc6e24a7dab147700a24e1df430aee73_41.png)

![](img/fc6e24a7dab147700a24e1df430aee73_43.png)

```bash
yum remove 软件包名
```
**注意**：与安装不同，默认情况下，`yum remove` **不会**移除作为依赖项被安装但现在未被其他软件使用的包。如果需要清理这些孤立依赖，可以使用 `autoremove` 参数。

---

## 网络YUM仓库简介

![](img/fc6e24a7dab147700a24e1df430aee73_45.png)

除了本地仓库，YUM更常见的用法是配置网络仓库（如官方源、阿里云源、清华源等）。网络仓库的配置方法与本地类似，但 `baseurl` 需要指向一个HTTP或FTP网址。

一个典型的网络仓库配置如下：

```ini
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
```
与本地仓库的主要区别在于：
1.  `baseurl` 使用的是 `http://` 协议。
2.  `gpgcheck=1` 通常为1，表示启用GPG签名检查以确保软件包来源可信。
3.  需要配置 `gpgkey` 来指定验证签名的公钥地址。

![](img/fc6e24a7dab147700a24e1df430aee73_47.png)

**YUM的优先级**：当系统配置了多个仓库时，YUM默认会优先从网络仓库查找软件包。

![](img/fc6e24a7dab147700a24e1df430aee73_49.png)

![](img/fc6e24a7dab147700a24e1df430aee73_50.png)

---

![](img/fc6e24a7dab147700a24e1df430aee73_52.png)

![](img/fc6e24a7dab147700a24e1df430aee73_54.png)

![](img/fc6e24a7dab147700a24e1df430aee73_56.png)

## 总结

本节课中我们一起学习了Linux下软件包管理的核心知识。

![](img/fc6e24a7dab147700a24e1df430aee73_58.png)

![](img/fc6e24a7dab147700a24e1df430aee73_60.png)

![](img/fc6e24a7dab147700a24e1df430aee73_62.png)

我们首先回顾了**RPM**工具的基础用法，它适合处理单个软件包，但需要手动解决依赖。然后，我们重点学习了**YUM**工具，通过亲手搭建一个**本地YUM仓库**，理解了仓库配置文件（`.repo`）的结构和关键配置项，特别是 `baseurl` 的作用。最后，我们掌握了YUM的常用命令，包括 `repolist`（查看仓库）、`install`（安装）、`list | grep`（搜索）和 `remove`（卸载），并了解了YUM自动解决依赖的巨大优势。

![](img/fc6e24a7dab147700a24e1df430aee73_64.png)

![](img/fc6e24a7dab147700a24e1df430aee73_65.png)

![](img/fc6e24a7dab147700a24e1df430aee73_67.png)

理解从RPM到YUM的演进，以及本地仓库与网络仓库的配置，是高效管理Linux系统软件的基础。