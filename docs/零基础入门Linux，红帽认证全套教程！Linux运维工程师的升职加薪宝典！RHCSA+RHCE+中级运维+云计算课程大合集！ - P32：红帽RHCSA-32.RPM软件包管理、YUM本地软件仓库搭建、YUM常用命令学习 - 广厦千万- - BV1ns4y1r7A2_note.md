# Linux软件包管理：P32：RPM软件包管理、YUM本地软件仓库搭建、YUM常用命令学习

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_1.png)

在本节课中，我们将要学习RPM软件包管理工具的进阶使用，包括软件包的升级、卸载以及签名导入。同时，我们也将开始学习更强大的YUM软件包管理工具，了解如何搭建本地YUM仓库来解决复杂的软件依赖问题。

## RPM软件包升级与卸载

上一节我们介绍了RPM的查询和安装功能，本节中我们来看看如何使用RPM进行软件包的升级和卸载。

### 软件包升级

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_3.png)

升级软件包时，使用 `-U` 选项。升级操作会用新版本完全覆盖旧版本的所有文件，包括配置文件。

**升级命令格式：**
```bash
rpm -Uvh 软件包全名.rpm
```
其中，`-v` 显示详细信息，`-h` 显示安装进度条。

**升级注意事项：**
升级时，新版本的配置文件会覆盖旧版本的配置文件。如果你不希望原有的配置文件被覆盖，必须在升级前对其进行备份。升级完成后，再将备份的配置文件复制回去，这样既能使用新版本的软件功能，又能保留原有的配置。

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_5.png)

以下是备份和恢复配置文件的步骤：
1.  备份重要配置文件：`cp -p /etc/vsftpd/vsftpd.conf /opt/`
2.  执行升级命令：`rpm -Uvh vsftpd-新版本.rpm`
3.  恢复原有配置：`cp -p /opt/vsftpd.conf /etc/vsftpd/`

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_7.png)

### 软件包卸载

卸载软件包使用 `-e` 选项，后跟软件包名（无需版本号）。

**卸载命令格式：**
```bash
rpm -e 软件包名
```
例如，卸载 `vsftpd` 软件包：`rpm -e vsftpd`。

卸载后，可以使用 `rpm -q 软件包名` 来验证是否已成功卸载。

**卸载注意事项：**
在实际服务器运维中，很少会直接卸载一个已安装的软件。通常，如果暂时不需要某个服务，只需将其停止即可。卸载操作主要用于彻底移除不再需要的软件。

### 处理卸载时的依赖关系

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_9.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_11.png)

在卸载一个软件包时，如果系统提示该软件包被其他软件所依赖，可以使用 `--nodeps` 选项来忽略依赖关系，强制卸载。

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_13.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_14.png)

**强制卸载命令格式：**
```bash
rpm -e --nodeps 软件包名
```
**注意：** 此操作需谨慎，因为被卸载软件包的依赖项可能也是其他重要软件的组成部分，强制卸载可能导致其他软件无法正常运行。

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_16.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_17.png)

## RPM软件包签名

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_19.png)

为了保证软件包的完整性和来源可信，RPM包通常包含数字签名。在安装来自非官方仓库的软件包时，系统可能会发出“未找到密钥”的警告。

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_21.png)

### 导入签名密钥

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_23.png)

要消除此警告，需要将软件包提供方的公钥（GPG密钥）导入系统。

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_25.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_27.png)

**导入密钥命令格式：**
```bash
rpm --import /路径/到/密钥文件
```
例如，红帽系统的密钥文件通常位于安装镜像的 `RPM-GPG-KEY-redhat-release`。导入后，再安装软件包就不会出现警告了。

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_29.png)

即使不导入密钥，警告也不会阻止软件包的安装和使用，它只是一个安全提示。

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_30.png)

## 引入YUM软件包管理器

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_32.png)

我们之前看到，使用RPM手动安装软件时，经常需要处理复杂的依赖关系，这非常低效。为了解决这个问题，我们引入YUM（Yellowdog Updater Modified）工具。

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_34.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_36.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_38.png)

YUM是一个基于RPM的Shell前端软件包管理器，它能够从指定的软件仓库（Repository）自动下载RPM包并安装，**自动处理依赖关系**。

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_40.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_42.png)

可以将YUM仓库理解为Linux系统的“应用商店”。它主要分为两类：
*   **网络仓库：** 如阿里云、清华大学等镜像站提供的仓库。需要服务器能访问互联网，配置简单，软件包丰富。
*   **本地仓库：** 将系统安装镜像或下载好的RPM包组织成本地目录作为仓库。适用于无外网环境的内部服务器。

## 搭建本地YUM仓库

对于没有互联网连接的环境，搭建本地YUM仓库是必不可少的技能。

以下是搭建本地YUM仓库的步骤：

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_44.png)

1.  **挂载系统安装镜像**
    首先，将CentOS/RHEL的系统安装ISO镜像文件挂载到某个目录，例如 `/mnt/cdrom`。
    ```bash
    mount /dev/cdrom /mnt/cdrom
    ```
    为了实现开机自动挂载，需要将挂载信息写入 `/etc/fstab` 文件：
    ```bash
    /dev/cdrom /mnt/cdrom iso9660 defaults 0 0
    ```
    然后执行 `mount -a` 使配置生效。

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_46.png)

2.  **创建仓库配置文件**
    在 `/etc/yum.repos.d/` 目录下，创建一个新的 `.repo` 文件，例如 `local.repo`。该目录下原有的网络仓库配置文件可以暂时移走或备份。
    ```bash
    cd /etc/yum.repos.d/
    mkdir bak
    mv *.repo bak/
    ```
    编辑新的仓库文件：
    ```bash
    vim /etc/yum.repos.d/local.repo
    ```
    添加以下内容：
    ```ini
    [Local-Base]          # 仓库ID，唯一标识
    name=Local Repository # 仓库名称描述
    baseurl=file:///mnt/cdrom # 仓库路径，file://表示本地文件
    enabled=1             # 启用此仓库
    gpgcheck=0            # 不检查GPG签名（本地镜像通常可信）
    ```
    **参数说明：**
    *   `[Local-Base]`：仓库的唯一标识符。
    *   `name`：仓库的人类可读名称。
    *   `baseurl`：仓库的实际地址。`file://` 指本地文件系统。
    *   `enabled=1`：启用该仓库。
    *   `gpgcheck=0`：禁用GPG密钥检查。如果设为1，则需要配置 `gpgkey` 指向密钥文件。

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_48.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_50.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_52.png)

3.  **清除并重建YUM缓存**
    让YUM识别新配置的仓库。
    ```bash
    yum clean all     # 清除所有旧缓存
    yum makecache     # 创建新缓存
    ```

4.  **测试本地仓库**
    使用 `yum list` 命令查看可用软件包，或尝试安装一个软件来测试。
    ```bash
    yum list          # 列出仓库所有包
    yum install vsftpd -y # 测试安装
    ```

## YUM常用命令

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_54.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_56.png)

本地仓库搭建好后，就可以使用YUM来高效管理软件了。

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_58.png)

以下是YUM的常用命令列表：

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_60.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_61.png)

*   **搜索软件包：** `yum search 关键词`
*   **列出所有可用软件包：** `yum list`
*   **列出已安装的软件包：** `yum list installed`
*   **安装软件包：** `yum install 软件包名 -y` （`-y` 表示自动确认）
*   **重新安装软件包：** `yum reinstall 软件包名 -y`
*   **升级软件包：** `yum update 软件包名 -y` （不指定包名则升级所有可升级包）
*   **卸载软件包：** `yum remove 软件包名 -y`
*   **查看软件包信息：** `yum info 软件包名`
*   **查看软件包提供了哪些命令或文件：** `yum provides 命令或文件路径`
*   **按组安装软件（如开发工具）：** `yum groupinstall “组名” -y`
*   **清除缓存：** `yum clean all`

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_63.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_65.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_67.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_68.png)

**举例：**
之前我们用RPM安装 `httpd` 遇到依赖问题，现在用YUM可以轻松解决：
```bash
yum install httpd -y
```
YUM会自动从配置好的仓库中找到 `httpd` 及其所有依赖包，并一次性完成安装。

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_70.png)

---

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_72.png)

![](img/9ab7c3c0fd4fe7b549ce9b0059ff3949_74.png)

本节课中我们一起学习了RPM包管理的升级、卸载和签名验证，并重点介绍了YUM工具的原理和优势。通过动手搭建本地YUM仓库，你已经掌握了在内网环境下高效管理软件包的核心方法。从下一节开始，我们将利用YUM来安装和配置各种常见的服务器软件。