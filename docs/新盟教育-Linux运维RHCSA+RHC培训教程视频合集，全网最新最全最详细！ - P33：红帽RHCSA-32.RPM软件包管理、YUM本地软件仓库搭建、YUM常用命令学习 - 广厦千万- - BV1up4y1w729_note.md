# Linux运维RHCSA+RHC培训教程：33：RPM软件包管理、YUM本地软件仓库搭建、YUM常用命令学习

![](img/25dbb78a15e79e0b18468e5d659f27ee_1.png)

在本节课中，我们将要学习RPM软件包管理的卸载与升级操作，理解软件包签名，并重点学习如何搭建本地YUM软件仓库以及YUM的常用命令。

上一节我们介绍了RPM软件包的查询和安装功能，本节中我们来看看如何卸载和升级软件包。

## RPM软件包卸载与升级

### 软件包升级

![](img/25dbb78a15e79e0b18468e5d659f27ee_3.png)

升级软件包时，需要注意一个重要事项：升级操作会使用新版本的文件**完全覆盖**旧版本的所有文件，包括配置文件。

这意味着，如果你在旧版本中修改了配置文件，升级后这些修改会被新版本的默认配置文件覆盖。因此，在升级前，建议对重要的配置文件进行备份。

升级命令使用 `-U` 选项，`-v` 和 `-h` 选项用于显示详细信息和进度条。

**公式/代码：**
```bash
rpm -Uvh 软件包全名.rpm
```

![](img/25dbb78a15e79e0b18468e5d659f27ee_5.png)

备份与恢复配置文件的流程如下：
1.  备份旧配置文件：`cp -p /etc/软件名/配置文件 /opt/`
2.  执行升级命令。
3.  将备份的配置文件覆盖回新版本的位置：`cp -p /opt/配置文件 /etc/软件名/`

### 软件包卸载

![](img/25dbb78a15e79e0b18468e5d659f27ee_7.png)

卸载软件包使用 `-e` 选项。

**公式/代码：**
```bash
rpm -e 软件包名
```

在企业服务器中，通常不会轻易卸载已安装的软件，只需停止相关服务即可。卸载操作需谨慎。

### 忽略依赖关系卸载

在卸载软件包时，如果该软件包被其他软件所依赖，系统可能会提示或阻止卸载。此时可以使用 `--nodeps` 选项忽略依赖关系，强制卸载。

**公式/代码：**
```bash
rpm -e --nodeps 软件包名
```

![](img/25dbb78a15e79e0b18468e5d659f27ee_9.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_11.png)

但请注意，忽略依赖卸载可能导致依赖该包的其他软件无法正常运行，因此此选项应慎用。

![](img/25dbb78a15e79e0b18468e5d659f27ee_13.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_14.png)

### 导入红帽签名

![](img/25dbb78a15e79e0b18468e5d659f27ee_16.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_17.png)

红帽签名（GPG密钥）是软件包的“数字指纹”，用于验证软件包的来源是否可信且未被篡改。从官方渠道安装软件包时，系统会自动处理签名。

![](img/25dbb78a15e79e0b18468e5d659f27ee_19.png)

如果从其他途径获取软件包并使用RPM安装，可能会看到“未找到GPG密钥”的警告。虽然不影响安装，但为了消除警告，可以手动导入密钥文件。

**公式/代码：**
```bash
rpm --import /mnt/光盘挂载点/RPM-GPG-KEY-redhat-release
```

![](img/25dbb78a15e79e0b18468e5d659f27ee_21.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_23.png)

导入后，系统会将密钥存储在 `/etc/pki/rpm-gpg/` 目录下，后续安装来自该源的软件包时将不再出现警告。

## YUM软件包管理器

![](img/25dbb78a15e79e0b18468e5d659f27ee_25.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_27.png)

RPM命令在处理复杂的软件包依赖关系时非常繁琐。YUM（Yellowdog Updater Modified）工具可以自动解决依赖关系，极大简化了软件管理。

![](img/25dbb78a15e79e0b18468e5d659f27ee_29.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_30.png)

YUM的工作原理是从一个或多个**软件仓库**中获取软件包信息并自动下载安装。

### 软件仓库类型

![](img/25dbb78a15e79e0b18468e5d659f27ee_32.png)

软件仓库主要分为两类：
*   **网络仓库**：如阿里云、清华大学等镜像站。需要服务器能访问互联网，配置简单，软件包丰富。
*   **本地仓库**：将系统安装光盘或下载的软件包集合作为仓库源。适用于无网络的内网环境，需要自行搭建。

![](img/25dbb78a15e79e0b18468e5d659f27ee_34.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_36.png)

接下来，我们将学习如何搭建一个本地YUM仓库。

![](img/25dbb78a15e79e0b18468e5d659f27ee_38.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_40.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_42.png)

### 搭建本地YUM仓库

以下是搭建本地YUM仓库的步骤：

首先，确保系统安装光盘已挂载。例如，挂载到 `/mnt/cdrom` 目录。

**公式/代码：**
```bash
mount /dev/cdrom /mnt/cdrom
# 或写入 /etc/fstab 实现开机自动挂载
```

然后，创建一个YUM仓库配置文件。YUM仓库配置文件通常位于 `/etc/yum.repos.d/` 目录，并以 `.repo` 结尾。

![](img/25dbb78a15e79e0b18468e5d659f27ee_44.png)

**公式/代码：**
```bash
vim /etc/yum.repos.d/local.repo
```

![](img/25dbb78a15e79e0b18468e5d659f27ee_46.png)

在配置文件中输入以下内容：

![](img/25dbb78a15e79e0b18468e5d659f27ee_48.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_50.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_52.png)

**公式/代码：**
```bash
[Local-Base]          # 仓库ID，唯一标识
name=Local Repository # 仓库描述信息
baseurl=file:///mnt/cdrom # 仓库路径，file://表示本地文件
enabled=1             # 启用此仓库
gpgcheck=0            # 不检查GPG签名（本地源可关闭）
```

最后，清除YUM缓存并测试仓库是否可用。

**公式/代码：**
```bash
yum clean all     # 清理缓存
yum makecache     # 建立新缓存
yum list          # 列出仓库中所有软件包，测试配置
```

![](img/25dbb78a15e79e0b18468e5d659f27ee_54.png)

### YUM常用命令

![](img/25dbb78a15e79e0b18468e5d659f27ee_56.png)

本地仓库搭建完成后，就可以使用YUM命令来管理软件了。以下是YUM的常用命令：

![](img/25dbb78a15e79e0b18468e5d659f27ee_58.png)

**公式/代码：**
```bash
yum install 软件包名         # 安装指定软件包及其依赖
yum remove 软件包名          # 卸载指定软件包
yum update [软件包名]        # 更新所有或指定软件包
yum list                    # 列出所有可用软件包
yum search 关键词           # 搜索包含关键词的软件包
yum info 软件包名           # 显示软件包的详细信息
yum provides 文件名          # 查询哪个软件包提供指定文件
yum history                 # 查看YUM操作历史
yum grouplist               # 列出可用的软件组
yum groupinstall “组名”     # 安装一个软件组
```

![](img/25dbb78a15e79e0b18468e5d659f27ee_60.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_61.png)

例如，要安装一个之前用RPM安装会提示依赖失败的 `httpd` 软件包，现在只需一条命令：

![](img/25dbb78a15e79e0b18468e5d659f27ee_63.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_65.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_67.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_68.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_70.png)

**公式/代码：**
```bash
yum -y install httpd
```
`-y` 选项表示自动回答“yes”，无需手动确认。

---

![](img/25dbb78a15e79e0b18468e5d659f27ee_72.png)

![](img/25dbb78a15e79e0b18468e5d659f27ee_74.png)

本节课中我们一起学习了RPM软件包的升级与卸载注意事项，了解了软件包签名的概念，并重点掌握了如何搭建本地YUM软件仓库以及YUM工具的基本使用方法。使用YUM可以高效、自动地处理软件依赖问题，是Linux系统软件管理的利器。