# Linux运维RHCSA+RHC培训教程：P35：软件版本升级、网络YUM仓库配置、设置YUM仓库优先级 🚀

![](img/1107d4f6169fd0fd40180214f3dd43df_1.png)

在本节课中，我们将学习如何使用YUM包管理器进行软件版本升级、如何配置网络YUM仓库，以及如何设置仓库优先级来控制软件包的下载来源。这些技能对于高效、稳定地管理Linux系统软件至关重要。

![](img/1107d4f6169fd0fd40180214f3dd43df_3.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_4.png)

## 软件版本升级 📈

上一节我们介绍了YUM的基本安装和查询命令。本节中我们来看看如何指定版本安装和升级软件包。

![](img/1107d4f6169fd0fd40180214f3dd43df_6.png)

### 列出仓库中软件包的所有版本

![](img/1107d4f6169fd0fd40180214f3dd43df_8.png)

要查看一个软件包在仓库中的所有可用版本，可以使用 `yum list` 命令配合特定选项。

以下是具体命令格式：
```bash
yum list <package_name> --showduplicates
```
例如，列出 `nginx` 的所有版本：
```bash
yum list nginx --showduplicates
```
执行后，会显示仓库中所有 `nginx` 的版本列表。

### 安装指定版本的软件包

![](img/1107d4f6169fd0fd40180214f3dd43df_10.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_12.png)

默认情况下，`yum install` 会安装仓库中最新的版本。如果需要安装特定版本，需要在包名后指定版本号。

![](img/1107d4f6169fd0fd40180214f3dd43df_14.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_16.png)

以下是安装指定版本 `nginx` 的命令格式：
```bash
yum install <package_name>-<version>
```
例如，安装 `nginx-1.18.0`：
```bash
yum install nginx-1.18.0
```
或者使用更简洁的通配符方式：
```bash
yum install nginx-1.18.0*
```

### 升级软件包

![](img/1107d4f6169fd0fd40180214f3dd43df_18.png)

升级软件包使用 `yum update` 命令。如果直接使用 `yum update <package_name>`，会升级到仓库中的最新版本。

![](img/1107d4f6169fd0fd40180214f3dd43df_20.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_22.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_24.png)

以下是升级 `nginx` 的命令：
```bash
yum update nginx
```
在升级过程中，新版本的软件通常不会覆盖用户手动修改过的配置文件。系统会保留旧的配置文件（通常以 `.rpmnew` 或 `.rpmsave` 后缀备份），但为了安全起见，在升级前手动备份重要配置文件仍然是推荐的做法。

![](img/1107d4f6169fd0fd40180214f3dd43df_26.png)

### 卸载软件包

卸载软件包使用 `yum remove` 命令。现代YUM在卸载时通常只移除指定的主包，而不会自动移除其依赖包，这比早期版本更加人性化。

![](img/1107d4f6169fd0fd40180214f3dd43df_28.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_30.png)

以下是卸载 `nginx` 的命令：
```bash
yum remove nginx
```

![](img/1107d4f6169fd0fd40180214f3dd43df_31.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_33.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_35.png)

## 网络YUM仓库配置 🌐

本地YUM仓库的软件包可能不全。接下来，我们学习如何配置网络YUM仓库来获取更丰富的软件源。

![](img/1107d4f6169fd0fd40180214f3dd43df_37.png)

### 获取网络仓库文件

以阿里云镜像站为例，网络仓库的配置非常简单，只需要下载对应的 `.repo` 仓库文件即可。

![](img/1107d4f6169fd0fd40180214f3dd43df_39.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_40.png)

首先，确保系统安装了 `wget` 工具：
```bash
yum install -y wget
```
然后，使用 `wget` 下载阿里云为CentOS 7提供的Base仓库文件：
```bash
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```
这个命令会从阿里云镜像站下载仓库配置文件，并保存为 `/etc/yum.repos.d/CentOS-Base.repo`。

### 理解仓库文件

下载完成后，可以查看该文件的内容：
```bash
cat /etc/yum.repos.d/CentOS-Base.repo
```
一个 `.repo` 文件内可以定义多个软件仓库，每个仓库由 `[repository_id]` 开始，包含 `name`、`baseurl`、`enabled`、`gpgcheck` 等配置项。阿里云的仓库文件通常包含了 `base`、`updates`、`extras` 等多个仓库段，提供了大量软件包。

### 使用网络仓库

![](img/1107d4f6169fd0fd40180214f3dd43df_42.png)

配置完成后，更新YUM缓存，即可使用新仓库：
```bash
yum clean all
yum makecache
```
然后就可以使用 `yum install` 从网络仓库安装软件了。网络仓库的软件包数量通常远多于本地镜像。

![](img/1107d4f6169fd0fd40180214f3dd43df_44.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_46.png)

## 设置YUM仓库优先级 ⚖️

![](img/1107d4f6169fd0fd40180214f3dd43df_47.png)

当系统配置了多个YUM仓库（如本地和网络）时，YUM默认的下载顺序可能不符合我们的需求。例如，我们希望优先从本地仓库下载以提升速度。这时就需要设置仓库优先级。

![](img/1107d4f6169fd0fd40180214f3dd43df_49.png)

### 安装优先级插件

![](img/1107d4f6169fd0fd40180214f3dd43df_51.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_53.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_54.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_55.png)

设置优先级需要先安装 `yum-plugin-priorities` 插件：
```bash
yum install -y yum-plugin-priorities
```

![](img/1107d4f6169fd0fd40180214f3dd43df_57.png)

### 配置仓库优先级

![](img/1107d4f6169fd0fd40180214f3dd43df_59.png)

编辑需要设置优先级的仓库文件（例如本地仓库文件 `/etc/yum.repos.d/local.repo`），在相应的仓库段中添加 `priority` 参数。

![](img/1107d4f6169fd0fd40180214f3dd43df_61.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_63.png)

以下是配置示例，在 `[local]` 段中添加：
```ini
[local]
name=Local Repository
baseurl=file:///mnt/centos
enabled=1
gpgcheck=0
priority=1
```
**priority** 的值范围是1到99，**数字越小，优先级越高**。这里设置为1，表示该仓库拥有最高优先级。

![](img/1107d4f6169fd0fd40180214f3dd43df_65.png)

### 优先级生效验证

配置完成后，再次使用 `yum install` 安装软件包，YUM会优先从优先级高的仓库（本例中是本地仓库）查找和下载软件。这可以显著提高安装速度，并节省网络流量。

## YUM缓存管理 🗑️

![](img/1107d4f6169fd0fd40180214f3dd43df_67.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_69.png)

YUM会将仓库的元数据（如软件包列表）缓存到本地，以加速后续操作。但有时缓存信息可能过时或出错，需要清理。

### 清除YUM缓存

如果遇到软件包下载失败或列表信息不准确，可以清除所有YUM缓存：
```bash
yum clean all
```
这个命令会清除所有已下载的包头和元数据缓存。

### 重建YUM缓存

清除缓存后，或者在新配置仓库后，可以重建缓存：
```bash
yum makecache
```
这个命令会从所有已启用的仓库下载最新的元数据并建立缓存。

缓存问题的一个典型场景是：当本地仓库的挂载点（如 `/mnt/centos`）被卸载后，YUM缓存仍记录着该路径下有软件包，导致安装失败并提示模糊的错误信息。此时，执行 `yum clean all` 清除旧缓存，就能得到真实的仓库状态提示。

![](img/1107d4f6169fd0fd40180214f3dd43df_71.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_73.png)

## 总结 📝

![](img/1107d4f6169fd0fd40180214f3dd43df_75.png)

本节课中我们一起学习了YUM包管理器的高级应用。

![](img/1107d4f6169fd0fd40180214f3dd43df_77.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_79.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_81.png)

![](img/1107d4f6169fd0fd40180214f3dd43df_83.png)

我们掌握了如何查询和安装指定版本的软件包，以及如何进行安全的升级和卸载操作。接着，我们配置了网络YUM仓库，极大地扩展了可用软件源。为了解决多仓库共存时的下载顺序问题，我们学习了通过安装插件和设置 `priority` 参数来定义仓库优先级，确保优先从本地或更快的源获取软件。最后，我们了解了YUM缓存的工作原理及如何通过 `clean` 和 `makecache` 命令管理缓存，以解决因缓存过期导致的各类问题。

![](img/1107d4f6169fd0fd40180214f3dd43df_85.png)

这些技能能帮助你更精细、更高效地管理系统软件，是Linux系统运维中的核心能力。