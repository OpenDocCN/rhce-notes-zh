# Linux运维全套培训课程：P35：红帽RHCSA-34.软件版本升级、网络YUM仓库配置、设置YUM仓库优先级

![](img/daa59790ab4cd26f7a56bfc57134f010_1.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_3.png)

在本节课中，我们将学习如何使用YUM包管理器进行软件版本升级、如何配置网络YUM仓库，以及如何设置仓库优先级来控制软件包的下载来源。这些技能对于管理Linux系统中的软件至关重要。

![](img/daa59790ab4cd26f7a56bfc57134f010_5.png)

## 软件版本升级

上一节我们介绍了YUM的基本安装操作，本节中我们来看看如何使用YUM进行软件版本升级。

![](img/daa59790ab4cd26f7a56bfc57134f010_7.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_9.png)

首先，我们需要查看仓库中某个软件包的所有可用版本。可以使用以下命令格式：

```bash
yum list --showduplicates <软件包名>
```

例如，要列出`nginx`的所有版本，可以执行：

![](img/daa59790ab4cd26f7a56bfc57134f010_11.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_13.png)

```bash
yum list --showduplicates nginx
```

![](img/daa59790ab4cd26f7a56bfc57134f010_15.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_17.png)

执行后，会显示仓库中所有可用的nginx版本列表。

接下来，如果需要安装或升级到特定版本（而非默认的最新版），可以使用以下命令格式：

![](img/daa59790ab4cd26f7a56bfc57134f010_19.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_21.png)

```bash
yum install <软件包名>-<版本号>
```

![](img/daa59790ab4cd26f7a56bfc57134f010_23.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_25.png)

例如，要安装`nginx`的`1.18.0`版本，可以执行：

![](img/daa59790ab4cd26f7a56bfc57134f010_27.png)

```bash
yum install nginx-1.18.0
```

![](img/daa59790ab4cd26f7a56bfc57134f010_29.png)

YUM会处理依赖关系并安装指定版本的软件包。

![](img/daa59790ab4cd26f7a56bfc57134f010_31.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_33.png)

在升级软件时，一个重要的考虑是配置文件是否会保留。现代软件包管理器（如YUM）在升级时通常不会覆盖用户修改过的配置文件，而是会保留原有配置。例如，升级nginx后，其主配置文件`/etc/nginx/nginx.conf`中的自定义设置通常会被保留。但为了安全起见，在升级关键软件前，建议手动备份重要配置文件。

![](img/daa59790ab4cd26f7a56bfc57134f010_35.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_37.png)

卸载软件包可以使用`yum remove`命令。现代的YUM在卸载时通常只移除指定的主软件包，而不会自动删除其依赖包，这比早期版本更加人性化。

```bash
yum remove nginx
```

![](img/daa59790ab4cd26f7a56bfc57134f010_39.png)

## 网络YUM仓库配置

![](img/daa59790ab4cd26f7a56bfc57134f010_41.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_43.png)

本地仓库的软件包可能有限，接下来我们看看如何配置网络YUM仓库来获取更丰富的软件源。

网络仓库（如阿里云、清华大学镜像站）提供了与官方同步的大量软件包，下载速度通常比连接国外官方源更快。

配置网络YUM仓库非常简单，通常只需要下载仓库配置文件即可。以下是使用阿里云仓库的步骤：

1.  使用`wget`命令下载阿里云为CentOS 7提供的仓库配置文件。
2.  如果没有`wget`，需要先安装它：`yum install -y wget`。
3.  执行下载命令（具体命令需从阿里云镜像站获取，例如）：
    ```bash
    wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
    ```
4.  下载完成后，仓库配置文件会自动保存在`/etc/yum.repos.d/`目录下。

![](img/daa59790ab4cd26f7a56bfc57134f010_45.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_47.png)

一个仓库配置文件（`.repo`文件）内可以定义多个软件仓库，每个仓库有唯一的`[仓库名]`、描述和指向软件包列表的地址。配置完成后，可以使用`yum repolist`命令查看所有已启用仓库及其包含的软件包数量。

![](img/daa59790ab4cd26f7a56bfc57134f010_49.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_51.png)

## 设置YUM仓库优先级

当我们同时配置了本地和网络仓库后，YUM默认会从所有仓库中查找软件包。有时，我们希望优先从本地仓库（速度最快）查找，找不到时再使用网络仓库，这时就需要设置仓库优先级。

![](img/daa59790ab4cd26f7a56bfc57134f010_53.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_55.png)

设置优先级需要先安装`yum-plugin-priorities`插件：

![](img/daa59790ab4cd26f7a56bfc57134f010_57.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_59.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_61.png)

```bash
yum install -y yum-plugin-priorities
```

![](img/daa59790ab4cd26f7a56bfc57134f010_63.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_65.png)

安装完成后，编辑本地仓库的配置文件（例如`/etc/yum.repos.d/local.repo`），在相应的仓库配置段中加入优先级设置：

![](img/daa59790ab4cd26f7a56bfc57134f010_67.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_69.png)

```ini
[local]
name=Local Repository
baseurl=file:///mnt/cdrom
enabled=1
gpgcheck=0
priority=1
```

![](img/daa59790ab4cd26f7a56bfc57134f010_71.png)

其中，`priority=`用于设置优先级，数字范围为1-99，**数字越小，优先级越高**。例如，设置为`1`表示最高优先级。

保存退出后，YUM在安装软件时就会优先从优先级高的仓库中查找。如果高优先级仓库中没有所需软件包，则会自动转向较低优先级的仓库。

![](img/daa59790ab4cd26f7a56bfc57134f010_73.png)

## YUM缓存管理

![](img/daa59790ab4cd26f7a56bfc57134f010_75.png)

YUM会缓存仓库的元数据（如软件包列表）以加速后续操作。但有时缓存信息可能过时或出错，导致无法正常安装软件。

生成缓存命令：
```bash
yum makecache
```

清除所有缓存命令：
```bash
yum clean all
```

例如，如果卸载了本地仓库源（如卸载了挂载的光盘），但YUM缓存仍记录着该仓库的信息，尝试安装软件时就会失败。此时，清除缓存再更新列表，就能得到真实的仓库状态。

另一个有用的命令是`yum provides`，它可以查询某个命令或文件是由哪个软件包提供的。例如，如果系统没有`ab`命令，可以运行：
```bash
yum provides ab
```
YUM会搜索并告知`ab`命令来自`httpd-tools`软件包，然后你就可以安装该包来获得命令。

![](img/daa59790ab4cd26f7a56bfc57134f010_77.png)

## 课程总结

![](img/daa59790ab4cd26f7a56bfc57134f010_79.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_81.png)

本节课中我们一起学习了YUM包管理器的进阶操作。

![](img/daa59790ab4cd26f7a56bfc57134f010_83.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_85.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_87.png)

![](img/daa59790ab4cd26f7a56bfc57134f010_89.png)

我们掌握了如何查看和安装软件包的特定版本，了解了软件升级时配置文件的保留机制。接着，我们学习了如何配置阿里云等网络YUM仓库来扩展软件源。最后，我们探讨了通过设置仓库优先级来优化软件安装策略，以及如何使用缓存管理命令来排查相关问题。

![](img/daa59790ab4cd26f7a56bfc57134f010_91.png)

这些技能能帮助您更灵活、高效地管理Linux系统上的软件。