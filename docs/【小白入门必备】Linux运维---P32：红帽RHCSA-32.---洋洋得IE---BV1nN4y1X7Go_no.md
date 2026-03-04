# Linux运维进阶：P32：RPM软件包管理、YUM本地软件仓库搭建、YUM常用命令学习 📦

![](img/0237f7c8ef052b7dfac1721e686e8ac8_1.png)

在本节课中，我们将要学习RPM软件包管理的进阶操作，包括软件包的升级、卸载以及签名导入。同时，我们将重点介绍YUM软件包管理工具，学习如何搭建本地YUM仓库以及YUM的常用命令，以解决RPM安装时复杂的依赖关系问题。

## RPM软件包升级与卸载 🔄

上一节我们介绍了RPM软件包的查询与安装功能，本节中我们来看看如何升级和卸载软件包。

![](img/0237f7c8ef052b7dfac1721e686e8ac8_3.png)

### 软件包升级

升级软件包时，新版本会完全覆盖旧版本的所有文件，包括配置文件。因此，在升级前，对重要的配置文件进行备份至关重要。

**升级命令格式：**
```bash
rpm -Uvh 软件包全名.rpm
```
其中，`-U` 选项代表升级（Upgrade），`-v` 显示详细信息，`-h` 显示安装进度条。

**升级操作流程：**
1.  备份不希望被覆盖的配置文件。
    ```bash
    cp -p /etc/vsftpd/vsftpd.conf /opt/
    ```
2.  执行升级命令。
    ```bash
    rpm -Uvh vsftpd-新版本.rpm
    ```
3.  升级完成后，如果需要保留旧配置，可将备份的配置文件复制回原位置，覆盖新版本的文件。
    ```bash
    cp -p /opt/vsftpd.conf /etc/vsftpd/
    ```

![](img/0237f7c8ef052b7dfac1721e686e8ac8_5.png)

### 软件包卸载

![](img/0237f7c8ef052b7dfac1721e686e8ac8_7.png)

卸载软件包使用 `-e` 选项。在企业服务器中，通常停止服务即可，很少直接卸载软件。

**卸载命令格式：**
```bash
rpm -e 软件包名
```
例如，卸载 `vsftpd`：
```bash
rpm -e vsftpd
```

**卸载时忽略依赖关系：**
某些情况下，卸载主包时会连带卸载其依赖包。如果该依赖包还被其他软件使用，则可能导致问题。此时可以使用 `--nodeps` 选项忽略依赖关系，仅卸载指定的包。
```bash
rpm -e --nodeps 软件包名
```

## RPM软件包签名验证 🔏

在安装软件包时，系统可能会提示警告，表示未找到该软件包的GPG密钥签名。这类似于商品上的安全认证标识，用于验证软件包的来源是否可信。

![](img/0237f7c8ef052b7dfac1721e686e8ac8_9.png)

**导入GPG密钥文件：**
如果软件包来源可靠，可以导入其密钥文件以消除警告。密钥文件通常位于系统镜像的根目录。
```bash
rpm --import /mnt/centos/RPM-GPG-KEY-CentOS-7
```
导入后，系统会将密钥存储在 `/etc/pki/rpm-gpg/` 目录下，后续安装来自该源的软件包时将不再出现警告。

![](img/0237f7c8ef052b7dfac1721e686e8ac8_11.png)

## YUM软件包管理器简介 🚀

![](img/0237f7c8ef052b7dfac1721e686e8ac8_13.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_15.png)

我们之前已经多次使用YUM命令安装软件，它相比RPM的最大优势在于能够**自动解决软件包之间复杂的依赖关系**。

![](img/0237f7c8ef052b7dfac1721e686e8ac8_17.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_19.png)

**YUM与RPM的关系：**
*   **RPM**：是底层的软件包管理工具，需要手动处理依赖关系。
*   **YUM**：是上层的前端工具，基于软件仓库（Repository）自动解决依赖并安装。

![](img/0237f7c8ef052b7dfac1721e686e8ac8_21.png)

**软件仓库类型：**
1.  **本地仓库**：将系统镜像或软件包集合存放在服务器本地目录。无需网络即可使用，适合内网环境。
2.  **网络仓库**：如阿里云、清华大学等提供的公共镜像站。需要服务器能连接互联网，配置简单，软件版本新。

![](img/0237f7c8ef052b7dfac1721e686e8ac8_23.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_25.png)

## 搭建本地YUM仓库 🏗️

![](img/0237f7c8ef052b7dfac1721e686e8ac8_27.png)

对于无法连接互联网的内网环境，搭建本地YUM仓库是必不可少的技能。

![](img/0237f7c8ef052b7dfac1721e686e8ac8_29.png)

以下是搭建本地YUM仓库的步骤：

![](img/0237f7c8ef052b7dfac1721e686e8ac8_31.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_33.png)

1.  **挂载系统镜像**：首先，将CentOS系统ISO镜像文件挂载到指定目录（如 `/mnt/centos`）。
    ```bash
    # 临时挂载
    mount /dev/cdrom /mnt/centos
    # 永久挂载（编辑 /etc/fstab 文件）
    # 添加一行：/dev/cdrom /mnt/centos iso9660 defaults 0 0
    # 然后执行 mount -a 生效
    ```

2.  **配置本地仓库文件**：在 `/etc/yum.repos.d/` 目录下创建一个新的 `.repo` 文件（例如 `local.repo`），并写入仓库配置。
    ```bash
    vim /etc/yum.repos.d/local.repo
    ```
    文件内容如下：
    ```ini
    [Local-Base]          # 仓库ID，唯一即可
    name=Local CentOS Base # 仓库描述
    baseurl=file:///mnt/centos # 仓库路径，file://表示本地文件
    enabled=1             # 启用此仓库
    gpgcheck=0            # 不检查GPG签名（本地镜像通常不检查）
    ```
    *   `[Local-Base]`：仓库的唯一标识。
    *   `name`：仓库的描述信息。
    *   `baseurl`：指定仓库的实际路径。`file://` 表示本地文件系统。
    *   `enabled=1`：启用这个仓库源。
    *   `gpgcheck=0`：禁用GPG密钥检查。对于可信的本地源，可以关闭以简化操作。

![](img/0237f7c8ef052b7dfac1721e686e8ac8_35.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_37.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_39.png)

3.  **清除并重建YUM缓存**：让YUM识别新配置的仓库。
    ```bash
    yum clean all      # 清除旧缓存
    yum makecache      # 建立新缓存
    ```

![](img/0237f7c8ef052b7dfac1721e686e8ac8_41.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_43.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_45.png)

4.  **测试本地仓库**：使用YUM命令测试是否可以从本地仓库安装软件。
    ```bash
    yum install vsftpd -y
    ```

## YUM常用命令速查 📋

YUM命令语法直观，功能强大。以下是其核心用法介绍：

**1. 安装软件包**
```bash
yum install 软件包名 -y
```
`-y` 选项表示自动确认安装，无需手动输入 `y`。

**2. 卸载软件包**
```bash
yum remove 软件包名 -y
```

![](img/0237f7c8ef052b7dfac1721e686e8ac8_47.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_49.png)

**3. 升级软件包**
```bash
yum update 软件包名 -y
# 升级所有可升级的软件包
yum update -y
```

![](img/0237f7c8ef052b7dfac1721e686e8ac8_51.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_53.png)

**4. 搜索软件包**
```bash
yum search 关键词
```

![](img/0237f7c8ef052b7dfac1721e686e8ac8_55.png)

**5. 查看软件包信息**
```bash
yum info 软件包名
```

**6. 列出所有已安装的软件包**
```bash
yum list installed
```

![](img/0237f7c8ef052b7dfac1721e686e8ac8_57.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_59.png)

**7. 列出仓库中所有可用的软件包**
```bash
yum list all
```

![](img/0237f7c8ef052b7dfac1721e686e8ac8_61.png)

**8. 查看可用的软件包组（如“开发工具”）**
```bash
yum grouplist
```

![](img/0237f7c8ef052b7dfac1721e686e8ac8_63.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_65.png)

**9. 安装软件包组**
```bash
yum groupinstall "组名" -y
```

![](img/0237f7c8ef052b7dfac1721e686e8ac8_67.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_69.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_71.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_73.png)

**10. 清除YUM缓存**
```bash
yum clean all
```

![](img/0237f7c8ef052b7dfac1721e686e8ac8_75.png)

---

![](img/0237f7c8ef052b7dfac1721e686e8ac8_77.png)

![](img/0237f7c8ef052b7dfac1721e686e8ac8_79.png)

本节课中我们一起学习了RPM软件包的升级、卸载及签名管理，深入探讨了YUM工具的原理。我们重点掌握了在内网环境下如何搭建本地YUM仓库，并熟悉了YUM一系列高效的日常管理命令。理解并熟练运用YUM，将极大提升在Linux系统中管理软件包的效率和便捷性。