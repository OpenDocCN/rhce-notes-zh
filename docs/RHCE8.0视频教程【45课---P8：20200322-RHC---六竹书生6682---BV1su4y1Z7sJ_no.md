# RHCE8.0视频教程：P8：软件包管理（RPM与YUM）详解

![](img/1fed21b0f6803e39de918125e9e33727_1.png)

![](img/1fed21b0f6803e39de918125e9e33727_3.png)

![](img/1fed21b0f6803e39de918125e9e33727_5.png)

![](img/1fed21b0f6803e39de918125e9e33727_7.png)

![](img/1fed21b0f6803e39de918125e9e33727_9.png)

![](img/1fed21b0f6803e39de918125e9e33727_11.png)

![](img/1fed21b0f6803e39de918125e9e33727_13.png)

在本节课中，我们将要学习Linux系统中两种核心的软件包管理工具：RPM和YUM。我们将从RPM包的安装、查询、卸载和验证开始，然后过渡到更高级的YUM仓库配置与管理，帮助你理解软件包管理的原理和实际操作。

![](img/1fed21b0f6803e39de918125e9e33727_15.png)

![](img/1fed21b0f6803e39de918125e9e33727_17.png)

## RPM包管理基础

![](img/1fed21b0f6803e39de918125e9e33727_19.png)

![](img/1fed21b0f6803e39de918125e9e33727_21.png)

![](img/1fed21b0f6803e39de918125e9e33727_23.png)

![](img/1fed21b0f6803e39de918125e9e33727_25.png)

上一节我们介绍了系统的基本操作，本节中我们来看看如何使用RPM（Red Hat Package Manager）来管理软件包。RPM是红帽系列Linux发行版中用于安装、查询、验证、更新和卸载软件包的工具。

![](img/1fed21b0f6803e39de918125e9e33727_27.png)

### 软件包的安装与卸载

![](img/1fed21b0f6803e39de918125e9e33727_29.png)

![](img/1fed21b0f6803e39de918125e9e33727_31.png)

安装RPM包的基本命令是 `rpm -i`，卸载则是 `rpm -e`。需要注意的是，命令后面跟的是**软件包名**，而不是完整的RPM文件名。

![](img/1fed21b0f6803e39de918125e9e33727_33.png)

![](img/1fed21b0f6803e39de918125e9e33727_35.png)

![](img/1fed21b0f6803e39de918125e9e33727_37.png)

**安装命令示例：**
```bash
rpm -ivh vsftpd-3.0.3-28.el8.x86_64.rpm
```
**卸载命令示例：**
```bash
rpm -e vsftpd
```

![](img/1fed21b0f6803e39de918125e9e33727_39.png)

### 查询软件包信息

![](img/1fed21b0f6803e39de918125e9e33727_41.png)

![](img/1fed21b0f6803e39de918125e9e33727_43.png)

![](img/1fed21b0f6803e39de918125e9e33727_45.png)

![](img/1fed21b0f6803e39de918125e9e33727_47.png)

![](img/1fed21b0f6803e39de918125e9e33727_49.png)

RPM提供了强大的查询功能，可以查看已安装软件包的各种信息。

![](img/1fed21b0f6803e39de918125e9e33727_51.png)

![](img/1fed21b0f6803e39de918125e9e33727_53.png)

![](img/1fed21b0f6803e39de918125e9e33727_55.png)

![](img/1fed21b0f6803e39de918125e9e33727_57.png)

![](img/1fed21b0f6803e39de918125e9e33727_59.png)

以下是常用的查询选项：
*   **`rpm -qa`**：查询所有已安装的软件包。
*   **`rpm -ql [包名]`**：列出指定软件包安装的所有文件。
*   **`rpm -qc [包名]`**：仅列出指定软件包的配置文件。
*   **`rpm -qd [包名]`**：列出指定软件包的文档文件。
*   **`rpm -qi [包名]`**：显示指定软件包的详细信息（版本、架构、安装时间等）。
*   **`rpm -q --scripts [包名]`**：查看软件包附带的安装/卸载脚本。
*   **`rpm -qf [文件路径]`**：查询某个文件是由哪个软件包安装的。

![](img/1fed21b0f6803e39de918125e9e33727_61.png)

![](img/1fed21b0f6803e39de918125e9e33727_63.png)

### RPM包的工作原理与手动安装

![](img/1fed21b0f6803e39de918125e9e33727_65.png)

![](img/1fed21b0f6803e39de918125e9e33727_67.png)

![](img/1fed21b0f6803e39de918125e9e33727_69.png)

![](img/1fed21b0f6803e39de918125e9e33727_71.png)

RPM包本质上是预先编译好并指定了安装路径的归档文件。我们可以手动解压RPM包来理解其安装原理。

![](img/1fed21b0f6803e39de918125e9e33727_73.png)

![](img/1fed21b0f6803e39de918125e9e33727_75.png)

![](img/1fed21b0f6803e39de918125e9e33727_77.png)

**解压RPM包命令：**
```bash
rpm2cpio tree-1.7.0-15.el8.x86_64.rpm | cpio -id
```
解压后，通常会生成 `usr/bin`、`usr/lib`、`usr/share` 等目录，对应系统根目录下的 `/usr/bin`、`/usr/lib`、`usr/share`。手动将这些文件复制到系统对应目录下，软件也能运行，但不会被RPM数据库记录。

![](img/1fed21b0f6803e39de918125e9e33727_79.png)

![](img/1fed21b0f6803e39de918125e9e33727_81.png)

### 软件包验证与数字签名

![](img/1fed21b0f6803e39de918125e9e33727_83.png)

![](img/1fed21b0f6803e39de918125e9e33727_85.png)

为了保证软件包的完整性和来源可信，RPM支持GPG数字签名验证。在安装来自官方源的软件包前，需要导入对应的公钥。

![](img/1fed21b0f6803e39de918125e9e33727_87.png)

![](img/1fed21b0f6803e39de918125e9e33727_89.png)

![](img/1fed21b0f6803e39de918125e9e33727_91.png)

**导入公钥：**
```bash
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
```
**验证软件包签名：**
```bash
rpm -K vsftpd-3.0.3-28.el8.x86_64.rpm
```
如果显示“OK”，则表示签名验证通过。

![](img/1fed21b0f6803e39de918125e9e33727_93.png)

![](img/1fed21b0f6803e39de918125e9e33727_95.png)

### 软件包更新

![](img/1fed21b0f6803e39de918125e9e33727_97.png)

![](img/1fed21b0f6803e39de918125e9e33727_99.png)

![](img/1fed21b0f6803e39de918125e9e33727_101.png)

使用 `rpm -U` 命令可以更新软件包。需要注意的是，普通服务软件更新时会替换旧版本，而内核软件更新时新旧版本可以共存，以保证系统稳定性。

![](img/1fed21b0f6803e39de918125e9e33727_103.png)

![](img/1fed21b0f6803e39de918125e9e33727_105.png)

**更新命令示例：**
```bash
rpm -Uvh kernel-4.18.0-147.el8.x86_64.rpm
```

![](img/1fed21b0f6803e39de918125e9e33727_107.png)

![](img/1fed21b0f6803e39de918125e9e33727_109.png)

![](img/1fed21b0f6803e39de918125e9e33727_111.png)

---

![](img/1fed21b0f6803e39de918125e9e33727_113.png)

![](img/1fed21b0f6803e39de918125e9e33727_115.png)

![](img/1fed21b0f6803e39de918125e9e33727_117.png)

![](img/1fed21b0f6803e39de918125e9e33727_119.png)

![](img/1fed21b0f6803e39de918125e9e33727_121.png)

![](img/1fed21b0f6803e39de918125e9e33727_123.png)

## YUM仓库管理

![](img/1fed21b0f6803e39de918125e9e33727_125.png)

![](img/1fed21b0f6803e39de918125e9e33727_127.png)

![](img/1fed21b0f6803e39de918125e9e33727_129.png)

上一节我们介绍了底层的RPM管理，本节中我们来看看更智能、能自动解决依赖关系的YUM（Yellowdog Updater Modified）工具。YUM基于RPM，但通过配置仓库（Repository）来管理软件源。

![](img/1fed21b0f6803e39de918125e9e33727_131.png)

### 配置本地YUM仓库

![](img/1fed21b0f6803e39de918125e9e33727_133.png)

![](img/1fed21b0f6803e39de918125e9e33727_135.png)

很多时候，我们需要配置本地光盘或目录作为YUM源。这主要分为三个步骤：准备软件目录、挂载介质、编写仓库配置文件。

![](img/1fed21b0f6803e39de918125e9e33727_137.png)

![](img/1fed21b0f6803e39de918125e9e33727_139.png)

**1. 创建目录并挂载光盘：**
```bash
mkdir /tkedu
mount /dev/sr0 /tkedu
```
**2. 编写仓库配置文件：**
仓库配置文件需放在 `/etc/yum.repos.d/` 目录下，并以 `.repo` 结尾。
```bash
vi /etc/yum.repos.d/local.repo
```
**配置文件内容示例：**
```ini
[local-baseos]
name=Local BaseOS
baseurl=file:///tkedu/BaseOS
enabled=1
gpgcheck=0

![](img/1fed21b0f6803e39de918125e9e33727_141.png)

![](img/1fed21b0f6803e39de918125e9e33727_143.png)

[local-appstream]
name=Local AppStream
baseurl=file:///tkedu/AppStream
enabled=1
gpgcheck=0
```
*   **`[ ]`**：仓库ID，唯一标识。
*   **`name`**：仓库描述名称。
*   **`baseurl`**：仓库的实际路径，`file://` 表示本地文件协议。
*   **`enabled`**：是否启用此仓库（1启用，0禁用）。
*   **`gpgcheck`**：是否进行GPG签名检查（1检查，0不检查）。如果设为1，通常还需指定 `gpgkey`。

![](img/1fed21b0f6803e39de918125e9e33727_145.png)

![](img/1fed21b0f6803e39de918125e9e33727_147.png)

![](img/1fed21b0f6803e39de918125e9e33727_149.png)

**3. 检查仓库：**
```bash
yum repolist
```

![](img/1fed21b0f6803e39de918125e9e33727_151.png)

![](img/1fed21b0f6803e39de918125e9e33727_153.png)

### YUM的基本使用

![](img/1fed21b0f6803e39de918125e9e33727_155.png)

![](img/1fed21b0f6803e39de918125e9e33727_157.png)

![](img/1fed21b0f6803e39de918125e9e33727_159.png)

![](img/1fed21b0f6803e39de918125e9e33727_161.png)

![](img/1fed21b0f6803e39de918125e9e33727_163.png)

![](img/1fed21b0f6803e39de918125e9e33727_165.png)

![](img/1fed21b0f6803e39de918125e9e33727_167.png)

配置好仓库后，就可以使用YUM方便地管理软件了。

![](img/1fed21b0f6803e39de918125e9e33727_169.png)

![](img/1fed21b0f6803e39de918125e9e33727_171.png)

![](img/1fed21b0f6803e39de918125e9e33727_173.png)

![](img/1fed21b0f6803e39de918125e9e33727_175.png)

**搜索软件包：**
```bash
yum list *ftp*        # 列出名称包含ftp的软件
yum search ftp        # 在名称和描述中搜索ftp
```
**安装软件包：**
```bash
yum install tree -y   # -y 选项自动确认安装
```
**卸载软件包：**
```bash
yum remove tree -y
yum erase tree -y     # erase 与 remove 功能相同
```
**更新软件包：**
```bash
yum update tree -y    # 更新指定软件包
yum update -y         # 更新所有可更新的软件包
```

![](img/1fed21b0f6803e39de918125e9e33727_177.png)

![](img/1fed21b0f6803e39de918125e9e33727_179.png)

### 高级仓库管理

**临时启用/禁用仓库：**
在安装软件时，可以临时指定使用或禁用某个仓库。
```bash
yum --enablerepo=local-appstream install php
yum --disablerepo=* --enablerepo=local-baseos install kernel
```
**永久启用/禁用仓库：**
修改仓库配置文件中的 `enabled` 值（1或0）。

![](img/1fed21b0f6803e39de918125e9e33727_181.png)

![](img/1fed21b0f6803e39de918125e9e33727_183.png)

**清除YUM缓存：**
当仓库配置出错或缓存异常时，可以清理缓存。
```bash
yum clean all
yum makecache
```
**仅下载软件包不安装：**
有时需要将软件包下载到本地。
```bash
yum install --downloadonly --downloaddir=/tmp tree
```
**注意：** 此功能通常要求软件包尚未安装。

![](img/1fed21b0f6803e39de918125e9e33727_185.png)

---

![](img/1fed21b0f6803e39de918125e9e33727_187.png)

![](img/1fed21b0f6803e39de918125e9e33727_189.png)

![](img/1fed21b0f6803e39de918125e9e33727_191.png)

![](img/1fed21b0f6803e39de918125e9e33727_193.png)

本节课中我们一起学习了RPM和YUM软件包管理工具。我们从RPM包的安装、查询、验证等基础操作开始，理解了软件包安装的原理。然后，我们深入学习了YUM仓库的配置与管理，包括本地仓库的搭建、软件搜索、安装卸载以及一些高级管理技巧。掌握这些知识，将使你能够高效、安全地在红帽Linux系统上管理软件。