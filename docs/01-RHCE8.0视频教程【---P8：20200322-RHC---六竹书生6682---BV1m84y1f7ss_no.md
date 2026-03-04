# RHCE 8.0 课程：01：软件包管理基础

![](img/1b0f973aca2a22535f6c193b8d97febb_0.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_2.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_4.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_6.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_8.png)

在本节课中，我们将要学习 Linux 系统中的软件包管理，包括如何使用 `rpm` 和 `yum` 命令来安装、卸载、查询和更新软件包。我们还会学习如何配置本地 YUM 源，以便更高效地管理软件。

![](img/1b0f973aca2a22535f6c193b8d97febb_10.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_12.png)

## 软件包的卸载与安装

![](img/1b0f973aca2a22535f6c193b8d97febb_14.png)

上一节我们介绍了查找命令，本节中我们来看看如何卸载和安装软件包。

![](img/1b0f973aca2a22535f6c193b8d97febb_16.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_18.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_20.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_22.png)

首先，如果需要卸载一个已安装的软件，可以使用 `rpm -e` 命令。卸载时，只需要跟上软件包的名称，而不是完整的文件名。

![](img/1b0f973aca2a22535f6c193b8d97febb_24.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_26.png)

```bash
rpm -e vsftpd
```

这条命令会卸载名为 `vsftpd` 的软件包。命令执行后没有回显通常表示卸载成功。

![](img/1b0f973aca2a22535f6c193b8d97febb_28.png)

接下来，如果需要安装一个软件包，需要先定位到软件包所在的目录，然后使用 `rpm -ivh` 命令进行安装。

![](img/1b0f973aca2a22535f6c193b8d97febb_30.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_32.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_34.png)

```bash
cd /root/hel/appstream/Packages/
rpm -ivh vsftpd-3.0.3-28.el8.x86_64.rpm
```

![](img/1b0f973aca2a22535f6c193b8d97febb_36.png)

`rpm` 包是预编译好的，其文件的安装路径已经内置在包中，安装过程主要是将文件复制到这些预设的路径下。

![](img/1b0f973aca2a22535f6c193b8d97febb_38.png)

## RPM 包的安装原理

![](img/1b0f973aca2a22535f6c193b8d97febb_40.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_42.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_44.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_46.png)

为了理解 `rpm` 包的安装原理，我们可以将一个 `rpm` 包解压出来查看其内部结构。

![](img/1b0f973aca2a22535f6c193b8d97febb_48.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_50.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_52.png)

解压 `rpm` 包的命令是 `rpm2cpio`，结合 `cpio` 命令可以将包内文件提取出来。

![](img/1b0f973aca2a22535f6c193b8d97febb_54.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_56.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_58.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_60.png)

```bash
rpm2cpio tree-1.7.0-15.el8.x86_64.rpm | cpio -id
```

![](img/1b0f973aca2a22535f6c193b8d97febb_62.png)

解压后，通常会生成 `usr/bin`、`usr/lib`、`usr/share` 等目录。`rpm` 安装时，就是将包内对应目录下的文件，复制到系统根目录下相同的路径中。

![](img/1b0f973aca2a22535f6c193b8d97febb_64.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_66.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_68.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_70.png)

例如，包内的 `usr/bin/tree` 文件会被复制到系统的 `/usr/bin/tree`。这种方式解压后，通过指定完整路径可以直接运行程序，但为了更方便地使用，通常需要将其安装到系统标准路径，或将其所在目录添加到环境变量 `PATH` 中。

![](img/1b0f973aca2a22535f6c193b8d97febb_72.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_74.png)

## 查询软件包信息

![](img/1b0f973aca2a22535f6c193b8d97febb_76.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_78.png)

安装好软件后，我们经常需要查询软件的相关信息。`rpm` 提供了丰富的查询选项。

![](img/1b0f973aca2a22535f6c193b8d97febb_80.png)

*   **查询软件包安装的所有文件**：使用 `rpm -ql` 命令。
    ```bash
    rpm -ql vsftpd
    ```

![](img/1b0f973aca2a22535f6c193b8d97febb_82.png)

*   **查询软件包的配置文件**：使用 `rpm -qc` 命令。
    ```bash
    rpm -qc vsftpd
    ```

![](img/1b0f973aca2a22535f6c193b8d97febb_84.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_86.png)

*   **查询软件包的文档文件**：使用 `rpm -qd` 命令。
    ```bash
    rpm -qd vsftpd
    ```

![](img/1b0f973aca2a22535f6c193b8d97febb_88.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_90.png)

*   **查询软件包的详细信息**：使用 `rpm -qi` 命令。
    ```bash
    rpm -qi vsftpd
    ```

![](img/1b0f973aca2a22535f6c193b8d97febb_92.png)

*   **根据文件查找所属的软件包**：使用 `rpm -qf` 命令。这在系统恢复时非常有用。
    ```bash
    rpm -qf /boot/vmlinuz-4.18.0-80.el8.x86_64
    ```

![](img/1b0f973aca2a22535f6c193b8d97febb_94.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_96.png)

*   **查询未安装软件包的信息**：使用 `rpm -qip` 命令。
    ```bash
    rpm -qip /path/to/package.rpm
    ```

![](img/1b0f973aca2a22535f6c193b8d97febb_98.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_100.png)

## 软件包的验证与更新

![](img/1b0f973aca2a22535f6c193b8d97febb_102.png)

为了保证软件包的完整性和来源可信，RPM 支持数字签名验证。在安装前，可以导入红帽的公钥。

![](img/1b0f973aca2a22535f6c193b8d97febb_104.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_106.png)

```bash
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
```

![](img/1b0f973aca2a22535f6c193b8d97febb_108.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_110.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_112.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_114.png)

导入后，可以使用 `rpm -K` 命令验证软件包。

![](img/1b0f973aca2a22535f6c193b8d97febb_116.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_118.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_120.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_122.png)

```bash
rpm -K /path/to/package.rpm
```

![](img/1b0f973aca2a22535f6c193b8d97febb_124.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_126.png)

关于软件更新，使用 `rpm -U` 命令。普通服务软件更新时会替换旧版本，而内核软件更新时新旧版本可以共存，以保证系统稳定性。

![](img/1b0f973aca2a22535f6c193b8d97febb_128.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_130.png)

```bash
rpm -Uvh package.rpm
```

## 配置本地 YUM 源

![](img/1b0f973aca2a22535f6c193b8d97febb_132.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_134.png)

`yum` 是基于 `rpm` 的包管理器，能自动处理依赖关系。首先，我们来配置一个本地 YUM 源。

![](img/1b0f973aca2a22535f6c193b8d97febb_136.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_138.png)

以下是配置本地 YUM 源的步骤：

![](img/1b0f973aca2a22535f6c193b8d97febb_140.png)

1.  **创建本地仓库目录并挂载安装介质**：
    ```bash
    mkdir /tkyum
    mount /dev/sr0 /tkyum
    ```
    使用 `df -h` 命令可以验证挂载是否成功。

![](img/1b0f973aca2a22535f6c193b8d97febb_142.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_144.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_146.png)

2.  **创建 YUM 源配置文件**：在 `/etc/yum.repos.d/` 目录下创建 `.repo` 文件。
    ```bash
    vim /etc/yum.repos.d/local.repo
    ```
    文件内容示例：
    ```ini
    [local_appstream]
    name=Local AppStream
    baseurl=file:///tkyum/AppStream
    gpgcheck=0
    enabled=1

    [local_baseos]
    name=Local BaseOS
    baseurl=file:///tkyum/BaseOS
    gpgcheck=0
    enabled=1
    ```
    *   `[local_appstream]`：仓库ID，必须唯一。
    *   `name`：仓库描述名称。
    *   `baseurl`：仓库的实际路径，`file://` 表示本地文件协议。
    *   `gpgcheck`：是否进行GPG签名验证，0为关闭，1为开启（需配置 `gpgkey`）。
    *   `enabled`：是否启用此仓库，1为启用，0为禁用。

![](img/1b0f973aca2a22535f6c193b8d97febb_148.png)

3.  **检查 YUM 源**：使用 `yum repolist` 命令查看配置的仓库是否生效。
    ```bash
    yum repolist
    ```

![](img/1b0f973aca2a22535f6c193b8d97febb_150.png)

## 使用 YUM 管理软件

![](img/1b0f973aca2a22535f6c193b8d97febb_152.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_154.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_156.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_158.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_160.png)

配置好 YUM 源后，就可以方便地管理软件了。

![](img/1b0f973aca2a22535f6c193b8d97febb_162.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_164.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_166.png)

*   **搜索软件包**：
    ```bash
    yum list *ftp*
    yum search ftp
    ```

![](img/1b0f973aca2a22535f6c193b8d97febb_168.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_170.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_172.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_174.png)

*   **安装软件包**（`-y` 选项自动确认）：
    ```bash
    yum install -y tree
    ```

![](img/1b0f973aca2a22535f6c193b8d97febb_176.png)

*   **卸载软件包**：
    ```bash
    yum remove -y tree
    ```

![](img/1b0f973aca2a22535f6c193b8d97febb_178.png)

*   **更新软件包**：
    ```bash
    yum update -y tree
    ```

*   **清除 YUM 缓存**：
    ```bash
    yum clean all
    yum makecache
    ```

![](img/1b0f973aca2a22535f6c193b8d97febb_180.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_182.png)

*   **仅下载软件包不安装**：
    ```bash
    yum install --downloadonly --downloaddir=/tmp/ tree
    ```

*   **启用或禁用特定 YUM 源**：可以通过临时命令，也可以通过修改配置文件中的 `enabled` 值来永久设置。
    ```bash
    yum --enablerepo=local_appstream install package
    ```

![](img/1b0f973aca2a22535f6c193b8d97febb_184.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_186.png)

## 总结

![](img/1b0f973aca2a22535f6c193b8d97febb_188.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_190.png)

![](img/1b0f973aca2a22535f6c193b8d97febb_192.png)

本节课中我们一起学习了 Linux 下软件包管理的基础知识。我们掌握了使用 `rpm` 命令进行软件包的安装、卸载、查询和验证，理解了 RPM 包的基本安装原理。接着，我们重点学习了 `yum` 包管理器的使用，包括如何配置本地 YUM 源，以及如何使用 `yum` 命令来搜索、安装、卸载和更新软件包，这大大简化了处理软件依赖关系的复杂度。这些技能是进行系统管理和维护的重要基础。