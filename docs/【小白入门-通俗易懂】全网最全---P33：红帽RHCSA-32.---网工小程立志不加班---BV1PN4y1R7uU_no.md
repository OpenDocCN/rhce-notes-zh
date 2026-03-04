# Linux软件包管理：P33：RPM软件包管理、YUM本地软件仓库搭建、YUM常用命令学习 🐧

![](img/9363e2a736939df6fe81dadef579bbb8_1.png)

在本节课中，我们将要学习RPM软件包管理的进阶操作，包括软件包的升级、卸载以及签名导入。随后，我们将重点介绍YUM软件包管理工具，学习如何搭建本地YUM仓库以及YUM的常用命令，以解决RPM安装时复杂的依赖关系问题。

## RPM软件包管理进阶

上一节我们介绍了RPM的查询和安装功能，本节中我们来看看软件包的升级、卸载以及签名管理。

### 软件包升级

![](img/9363e2a736939df6fe81dadef579bbb8_3.png)

升级软件包时，新版本会完全覆盖旧版本的所有文件，包括配置文件。因此，在升级前，对重要的配置文件进行备份至关重要。

以下是升级软件包的命令格式：
```bash
rpm -Uvh [软件包全名]
```
其中，`-U` 代表升级，`-v` 显示详细信息，`-h` 显示安装进度条。

**升级操作的核心步骤：**
1.  备份不希望被覆盖的配置文件（例如：`cp -p /etc/vsftpd/vsftpd.conf /opt/`）。
2.  执行升级命令。
3.  升级完成后，如果需要保留旧配置，可将备份的配置文件拷贝回原路径，覆盖新版本的文件。

![](img/9363e2a736939df6fe81dadef579bbb8_5.png)

### 软件包卸载

卸载软件包使用 `-e` 选项。通常，服务器上已安装的软件很少被卸载，一般选择停止服务即可。

![](img/9363e2a736939df6fe81dadef579bbb8_7.png)

以下是卸载软件包的命令：
```bash
rpm -e [软件包名]
```
有时卸载主软件包时会连带卸载其依赖包，这可能影响其他依赖该包的软件。此时可以使用 `--nodeps` 选项忽略依赖关系，仅卸载指定的包。
```bash
rpm -e --nodeps [软件包名]
```

### 导入软件包签名

RPM软件包包含一个唯一的签名（GPG密钥），用于验证软件包的来源和完整性。如果系统未导入相应的密钥，安装时会出现警告（但不影响安装）。

导入签名文件的命令如下：
```bash
rpm --import [GPG密钥文件路径]
```
例如，导入红帽的GPG密钥：
```bash
rpm --import /mnt/centos/RPM-GPG-KEY-CentOS-7
```
导入后，系统会将密钥存储在 `/etc/pki/rpm-gpg/` 目录下，后续安装来自该源的软件包时将不再出现警告。

## 认识YUM软件包管理器

![](img/9363e2a736939df6fe81dadef579bbb8_9.png)

![](img/9363e2a736939df6fe81dadef579bbb8_11.png)

通过前面的学习，我们发现使用RPM手动安装软件时，经常需要解决复杂的依赖关系，效率很低。YUM（Yellowdog Updater Modified）就是为了解决这个问题而生的工具。

![](img/9363e2a736939df6fe81dadef579bbb8_13.png)

YUM是一个基于RPM的Shell前端软件包管理器，它能够从指定的软件仓库（Repository）自动下载RPM包并安装，**自动处理依赖关系**。

![](img/9363e2a736939df6fe81dadef579bbb8_15.png)

![](img/9363e2a736939df6fe81dadef579bbb8_17.png)

### 软件仓库简介

![](img/9363e2a736939df6fe81dadef579bbb8_19.png)

软件仓库是集中存放大量RPM软件包的地方，类似于Windows系统中的“软件商店”或“应用中心”。

![](img/9363e2a736939df6fe81dadef579bbb8_21.png)

软件仓库主要分为两类：
*   **本地仓库**：软件包存放在本地（如系统安装镜像），无需网络即可使用，但需要手动配置。
*   **网络仓库**：软件包存放在互联网服务器上（如阿里云、清华大学等镜像站），需要网络连接，但通常配置简单，软件版本丰富。

![](img/9363e2a736939df6fe81dadef579bbb8_23.png)

![](img/9363e2a736939df6fe81dadef579bbb8_25.png)

## 搭建本地YUM仓库 🛠️

![](img/9363e2a736939df6fe81dadef579bbb8_27.png)

在企业内网等无网络环境中，搭建本地YUM仓库非常实用。接下来，我们学习如何搭建一个本地仓库。

![](img/9363e2a736939df6fe81dadef579bbb8_29.png)

**搭建本地YUM仓库的核心步骤：**

![](img/9363e2a736939df6fe81dadef579bbb8_31.png)

![](img/9363e2a736939df6fe81dadef579bbb8_33.png)

1.  **挂载系统安装镜像**：将包含软件包的ISO镜像文件挂载到系统目录。
    ```bash
    # 临时挂载
    mount /dev/cdrom /mnt/centos
    # 永久挂载（编辑/etc/fstab文件）
    # 添加一行：/dev/cdrom /mnt/centos iso9660 defaults 0 0
    # 然后执行 mount -a 生效
    ```

2.  **创建仓库配置文件**：在 `/etc/yum.repos.d/` 目录下创建一个以 `.repo` 结尾的配置文件（例如 `local.repo`）。
    ```bash
    vim /etc/yum.repos.d/local.repo
    ```

![](img/9363e2a736939df6fe81dadef579bbb8_35.png)

![](img/9363e2a736939df6fe81dadef579bbb8_37.png)

3.  **编写仓库配置**：在配置文件中输入以下内容。
    ```ini
    [Local-Base]          # 仓库ID，唯一即可
    name=Local CentOS Repo # 仓库描述
    baseurl=file:///mnt/centos # 仓库路径，file://表示本地文件
    enabled=1             # 启用此仓库
    gpgcheck=0            # 不检查GPG签名（内网环境可关闭）
    ```
    *   `[Local-Base]`：仓库的唯一标识。
    *   `name`：仓库的描述信息。
    *   `baseurl`：仓库的地址，`file://` 指向本地路径。
    *   `enabled`：设置为1启用该仓库。
    *   `gpgcheck`：设置为0则不进行GPG密钥校验。

![](img/9363e2a736939df6fe81dadef579bbb8_39.png)

![](img/9363e2a736939df6fe81dadef579bbb8_41.png)

4.  **清除并重建YUM缓存**：让YUM识别新配置的仓库。
    ```bash
    yum clean all
    yum makecache
    ```

![](img/9363e2a736939df6fe81dadef579bbb8_43.png)

![](img/9363e2a736939df6fe81dadef579bbb8_45.png)

完成以上步骤后，本地YUM仓库就搭建好了。你可以使用 `yum repolist` 命令查看已启用的仓库列表。

## YUM常用命令 📦

本地仓库搭建完成后，就可以使用YUM命令来方便地管理软件了。以下是YUM最常用的命令。

**YUM命令基本格式：**
```bash
yum [选项] [命令] [软件包名]
```

![](img/9363e2a736939df6fe81dadef579bbb8_47.png)

![](img/9363e2a736939df6fe81dadef579bbb8_49.png)

以下是YUM的核心操作命令列表：

![](img/9363e2a736939df6fe81dadef579bbb8_51.png)

*   **搜索软件包**：在仓库中搜索包含关键字的软件包。
    ```bash
    yum search [关键字]
    ```

![](img/9363e2a736939df6fe81dadef579bbb8_53.png)

![](img/9363e2a736939df6fe81dadef579bbb8_55.png)

*   **安装软件包**：自动解决依赖并安装软件包。`-y` 选项表示自动确认。
    ```bash
    yum install -y [软件包名]
    ```

*   **更新软件包**：更新指定软件包到最新版本。
    ```bash
    yum update [软件包名]
    ```

![](img/9363e2a736939df6fe81dadef579bbb8_57.png)

*   **卸载软件包**：卸载指定的软件包（谨慎使用）。
    ```bash
    yum remove [软件包名]
    ```

![](img/9363e2a736939df6fe81dadef579bbb8_59.png)

*   **列出已安装软件**：列出所有通过YUM安装的软件包。
    ```bash
    yum list installed
    ```

![](img/9363e2a736939df6fe81dadef579bbb8_61.png)

*   **查看软件包信息**：显示软件包的详细信息。
    ```bash
    yum info [软件包名]
    ```

![](img/9363e2a736939df6fe81dadef579bbb8_63.png)

![](img/9363e2a736939df6fe81dadef579bbb8_65.png)

*   **检查可更新的软件**：列出所有可以更新的软件包。
    ```bash
    yum check-update
    ```

![](img/9363e2a736939df6fe81dadef579bbb8_67.png)

![](img/9363e2a736939df6fe81dadef579bbb8_69.png)

![](img/9363e2a736939df6fe81dadef579bbb8_71.png)

![](img/9363e2a736939df6fe81dadef579bbb8_73.png)

*   **清除缓存**：清理下载的软件包和旧的仓库元数据。
    ```bash
    yum clean all
    ```

![](img/9363e2a736939df6fe81dadef579bbb8_75.png)

---

![](img/9363e2a736939df6fe81dadef579bbb8_77.png)

![](img/9363e2a736939df6fe81dadef579bbb8_79.png)

本节课中我们一起学习了RPM软件包的升级、卸载与签名管理，深入了解了YUM工具如何解决软件依赖的难题。我们重点实践了如何搭建一个本地YUM仓库，并掌握了YUM的搜索、安装、更新等常用命令。掌握这些技能，将使你在Linux环境下的软件管理变得高效而轻松。