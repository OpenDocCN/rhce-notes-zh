# Linux软件包管理：P35：软件版本升级、网络YUM仓库配置、设置YUM仓库优先级

![](img/8e17e47c2773c552ef128731ada4ceb9_1.png)

在本节课中，我们将学习如何使用YUM包管理器进行软件版本升级、如何配置网络YUM仓库，以及如何设置仓库的优先级来控制软件包的下载来源。

![](img/8e17e47c2773c552ef128731ada4ceb9_3.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_4.png)

## 概述

YUM是Red Hat系列Linux发行版中强大的包管理工具。它不仅能自动解决软件依赖关系，还支持从多个仓库源（本地或网络）安装、升级和卸载软件。理解如何精确控制软件版本和仓库优先级，对于系统管理和维护至关重要。

上一节我们介绍了YUM的基本安装和查询命令，本节中我们来看看如何管理软件版本和配置更丰富的软件源。

![](img/8e17e47c2773c552ef128731ada4ceb9_6.png)

## 软件版本升级与指定安装

![](img/8e17e47c2773c552ef128731ada4ceb9_8.png)

默认情况下，使用 `yum install` 命令会安装仓库中最新的软件版本。但在企业环境中，我们可能需要安装特定的稳定版本。

### 列出仓库中所有可用版本

首先，我们需要知道某个软件包在仓库中有哪些版本可用。以下是列出所有版本的方法：

![](img/8e17e47c2773c552ef128731ada4ceb9_10.png)

```bash
yum list --showduplicates <软件包名>
```

![](img/8e17e47c2773c552ef128731ada4ceb9_12.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_14.png)

例如，要列出 `nginx` 的所有可用版本，可以执行：
```bash
yum list --showduplicates nginx
```
执行后，终端会显示类似 `nginx-1.18.0`， `nginx-1.20.1` 这样的版本列表。

![](img/8e17e47c2773c552ef128731ada4ceb9_16.png)

### 安装指定版本的软件包

确定版本后，可以使用以下命令安装特定版本：

```bash
yum install <软件包名>-<版本号>
```

![](img/8e17e47c2773c552ef128731ada4ceb9_18.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_20.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_22.png)

例如，要安装 `nginx` 的 `1.18.0` 版本，可以执行：
```bash
yum install nginx-1.18.0
```
**注意**：版本号必须与 `yum list` 列出的完整版本字符串匹配。

![](img/8e17e47c2773c552ef128731ada4ceb9_24.png)

### 升级软件包

![](img/8e17e47c2773c552ef128731ada4ceb9_26.png)

升级软件包到最新版本非常简单：

```bash
yum update <软件包名>
```

![](img/8e17e47c2773c552ef128731ada4ceb9_28.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_30.png)

如果想升级到另一个指定的新版本（非最新），同样使用 `yum install` 指定新版本号即可。YUM会先卸载旧版本，然后安装新版本。

![](img/8e17e47c2773c552ef128731ada4ceb9_31.png)

**重要提示**：现代软件包管理器在升级时通常会保留用户修改过的配置文件（例如 `/etc/nginx/nginx.conf`），并以 `.rpmnew` 或 `.rpmsave` 形式提供新版本的默认配置。但为了安全起见，在升级关键软件前，手动备份配置文件（如 `cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak`）是一个好习惯。

![](img/8e17e47c2773c552ef128731ada4ceb9_33.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_35.png)

### 卸载软件包

卸载软件包的命令如下：

![](img/8e17e47c2773c552ef128731ada4ceb9_37.png)

```bash
yum remove <软件包名>
```
现在的YUM在卸载时非常智能，通常只会移除指定的主软件包，而不会自动移除那些被其他软件所依赖的包。

## 配置网络YUM仓库

![](img/8e17e47c2773c552ef128731ada4ceb9_39.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_40.png)

本地镜像仓库的软件包可能有限。我们可以添加网络仓库（如阿里云、清华大学镜像站）来获取更丰富、更新的软件包。

### 添加阿里云YUM仓库

配置网络仓库通常只需下载对应的 `.repo` 仓库文件即可。以阿里云CentOS 7仓库为例：

1.  使用 `wget` 命令下载仓库文件（如果系统没有 `wget`，请先运行 `yum install -y wget` 安装）：
    ```bash
    wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
    ```
    这个命令会将阿里云的仓库配置文件下载并保存为 `/etc/yum.repos.d/CentOS-Base.repo`。

2.  下载完成后，可以查看该文件内容：
    ```bash
    cat /etc/yum.repos.d/CentOS-Base.repo
    ```
    你会看到文件中配置了 `[base]`、`[updates]`、`[extras]` 等多个仓库段，每个都有唯一的名称和指向阿里云镜像站的URL。

![](img/8e17e47c2773c552ef128731ada4ceb9_42.png)

3.  让YUM重新加载所有仓库元数据：
    ```bash
    yum makecache
    ```

![](img/8e17e47c2773c552ef128731ada4ceb9_44.png)

### 验证网络仓库

![](img/8e17e47c2773c552ef128731ada4ceb9_46.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_47.png)

执行以下命令查看所有已启用的仓库及其软件包数量：

```bash
yum repolist
```
输出结果中应该能看到来自 `base`、`updates` 等阿里云仓库的信息。

![](img/8e17e47c2773c552ef128731ada4ceb9_49.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_51.png)

## 设置YUM仓库优先级

![](img/8e17e47c2773c552ef128731ada4ceb9_53.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_54.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_55.png)

当系统配置了多个YUM仓库（例如本地仓库和多个网络仓库）时，YUM默认的仓库优先级可能不符合我们的需求。例如，我们希望软件包优先从本地仓库安装，以提升速度并节省流量。

![](img/8e17e47c2773c552ef128731ada4ceb9_57.png)

### 安装优先级插件

![](img/8e17e47c2773c552ef128731ada4ceb9_59.png)

首先，需要安装 `yum-plugin-priorities` 插件：
```bash
yum install -y yum-plugin-priorities
```

![](img/8e17e47c2773c552ef128731ada4ceb9_61.png)

### 配置仓库优先级

![](img/8e17e47c2773c552ef128731ada4ceb9_63.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_65.png)

优先级数字范围为 **1-99**，**数字越小，优先级越高**。

1.  编辑本地仓库的配置文件，例如 `/etc/yum.repos.d/local.repo`，在 `[local]` 仓库段中添加优先级设置：
    ```bash
    vim /etc/yum.repos.d/local.repo
    ```
2.  在仓库配置中添加一行：
    ```ini
    [local]
    name=Local Repository
    baseurl=file:///mnt/centos
    enabled=1
    gpgcheck=0
    priority=1  # 添加这行，设置最高优先级
    ```
    这里将本地仓库的 `priority` 设置为 `1`，确保它拥有最高优先级。

3.  对于网络仓库（如阿里云），你也可以编辑其 `.repo` 文件，为不同的仓库段设置稍低的优先级，例如 `priority=10`。

### 验证优先级效果

![](img/8e17e47c2773c552ef128731ada4ceb9_67.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_69.png)

配置完成后，当你使用 `yum install` 安装一个软件包时，YUM会优先从优先级高的仓库（本例中是本地仓库）查找和下载。你可以通过观察安装命令输出中的“来源”信息来验证。

## YUM缓存管理

YUM会将仓库的元数据（如软件包列表、依赖信息）缓存到本地，以加速后续操作。但有时缓存信息可能过时或出错，需要清理。

### 清理YUM缓存

以下是清理各种缓存的命令：

```bash
yum clean all          # 清理所有缓存（元数据、软件包等）
yum clean metadata     # 仅清理元数据缓存（常用）
yum clean packages     # 清理下载的软件包缓存
```

### 何时需要清理缓存？

![](img/8e17e47c2773c552ef128731ada4ceb9_71.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_73.png)

在以下情况，清理缓存可能有助于解决问题：
*   更改了仓库配置文件（如 `.repo` 文件）后。
*   仓库源本身发生变化（如本地镜像被卸载或网络仓库地址失效），但YUM仍使用旧的缓存信息导致安装失败。
*   执行 `yum` 命令时出现与仓库相关的莫名错误。

![](img/8e17e47c2773c552ef128731ada4ceb9_75.png)

**操作示例**：如果你卸载了本地镜像挂载点，但 `yum repolist` 仍显示旧的软件包数量，此时运行 `yum clean all` 后再执行 `yum repolist`，就会看到更新后的（通常为0或错误的）状态。

![](img/8e17e47c2773c552ef128731ada4ceb9_77.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_79.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_81.png)

## 总结

![](img/8e17e47c2773c552ef128731ada4ceb9_83.png)

![](img/8e17e47c2773c552ef128731ada4ceb9_85.png)

本节课中我们一起学习了YUM包管理器的高级应用：
1.  **版本控制**：使用 `yum list --showduplicates` 查看版本，并使用 `yum install <包名-版本号>` 安装特定版本。
2.  **网络仓库**：通过下载 `.repo` 文件轻松配置阿里云等网络镜像源，极大地扩展了可用软件包的范围。
3.  **仓库优先级**：通过安装 `yum-plugin-priorities` 插件并设置 `priority` 参数，可以控制软件包的下载来源顺序，优化安装速度和稳定性。
4.  **缓存管理**：了解 `yum clean` 命令的作用，学会在必要时清理缓存以解决仓库相关问题。

![](img/8e17e47c2773c552ef128731ada4ceb9_87.png)

掌握这些技能，将使你能够更灵活、更高效地管理Linux系统上的软件。