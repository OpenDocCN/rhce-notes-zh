# Linux运维全套培训课程：P33：红帽RHCSA-32.RPM软件包管理、YUM本地软件仓库搭建、YUM常用命令学习 🐧

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_1.png)

在本节课中，我们将要学习RPM软件包管理的升级与卸载操作，并重点介绍YUM软件包管理工具，包括如何搭建本地软件仓库以及YUM的常用命令。通过本课的学习，你将能够更高效地管理Linux系统中的软件。

## 软件包升级与卸载 🔄

上一节我们介绍了RPM软件包的查询与安装功能，本节中我们来看看如何进行软件包的升级与卸载。

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_3.png)

### 软件包升级

软件包升级是指将已安装的软件包替换为更新的版本。升级操作会覆盖旧版本的所有文件，包括配置文件。因此，在执行升级前，对重要的配置文件进行备份是至关重要的。

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_5.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_7.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_9.png)

以下是升级软件包的命令格式：
```bash
rpm -Uvh 软件包全名.rpm
```
其中，`-U` 选项代表升级，`-v` 显示详细信息，`-h` 显示安装进度条。

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_11.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_13.png)

**升级注意事项：**
升级时，新版本软件包会完全覆盖旧版本的所有文件。如果你不希望某些自定义的配置文件被覆盖，必须在升级前对其进行备份。升级完成后，可以将备份的配置文件复制回原位置，以保留原有配置。

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_15.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_17.png)

### 软件包卸载

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_19.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_21.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_23.png)

卸载软件包是指从系统中移除已安装的软件。在服务器环境中，通常不会轻易卸载软件，而是选择停止相关服务。

以下是卸载软件包的命令格式：
```bash
rpm -e 软件包名
```

**卸载注意事项：**
某些软件包在卸载时，可能会提示依赖关系错误。如果确定要卸载主包但保留其依赖（因为其他软件可能也需要这些依赖），可以使用 `--nodeps` 选项忽略依赖关系强制卸载。
```bash
rpm -e --nodeps 软件包名
```

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_25.png)

## RPM签名验证 🔑

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_27.png)

在安装软件包时，系统可能会提示“未找到GPG密钥”的警告。这是RPM包的一种安全机制，用于验证软件包的来源是否可信。

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_29.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_31.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_33.png)

要消除此警告，需要将软件仓库的GPG密钥导入系统。通常，系统镜像文件中包含此密钥文件（如 `RPM-GPG-KEY-redhat-release`）。

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_35.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_37.png)

导入密钥的命令如下：
```bash
rpm --import /路径/到/密钥文件
```
导入后，再次安装软件包就不会出现警告信息了。密钥文件通常位于 `/etc/pki/rpm-gpg/` 目录下。

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_39.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_41.png)

## YUM软件包管理器 📦

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_43.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_45.png)

我们之前已经体验过，使用RPM手动安装软件时，经常需要处理复杂的依赖关系，效率很低。而YUM（Yellowdog Updater Modified）工具可以自动解决依赖关系，极大地简化了软件管理。

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_47.png)

YUM的工作原理是从配置好的**软件仓库**中搜索、下载并安装软件包及其依赖。这类似于Windows系统中的“软件商店”。

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_49.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_51.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_53.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_55.png)

软件仓库主要分为两类：
1.  **本地仓库**：软件包存放在本地（如系统安装镜像），无需网络即可使用，但需要手动配置。
2.  **网络仓库**：软件包存放在互联网服务器上（如阿里云、清华大学镜像站），使用方便，但需要系统能够访问外网。

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_57.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_59.png)

## 搭建本地YUM仓库 🛠️

对于内网环境或没有网络连接的服务器，搭建本地YUM仓库是非常实用的技能。以下是搭建步骤：

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_61.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_63.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_65.png)

1.  **挂载系统镜像**：首先，将CentOS/RHEL的系统安装ISO镜像文件挂载到本地目录。
    ```bash
    mount /dev/cdrom /mnt/centos/
    ```
    为了实现开机自动挂载，需要将挂载信息写入 `/etc/fstab` 文件：
    ```bash
    /dev/cdrom /mnt/centos iso9660 defaults 0 0
    ```
    然后执行 `mount -a` 加载配置。

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_67.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_69.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_71.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_73.png)

2.  **配置YUM仓库文件**：在 `/etc/yum.repos.d/` 目录下创建一个新的 `.repo` 文件（例如 `local.repo`）。
    ```bash
    vim /etc/yum.repos.d/local.repo
    ```

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_75.png)

3.  **编写仓库配置**：在文件中输入以下内容。
    ```bash
    [Local-Base] # 仓库ID，唯一即可
    name=Local CentOS Base # 仓库描述信息
    baseurl=file:///mnt/centos # 仓库路径，指向挂载点
    enabled=1 # 启用此仓库
    gpgcheck=0 # 不进行GPG签名检查（内网环境可关闭）
    ```
    *   `baseurl` 支持 `file://`（本地）、`http://`、`ftp://` 等协议。
    *   如果镜像包含GPG密钥，也可设置 `gpgcheck=1` 并指定 `gpgkey` 文件路径。

4.  **清除并重建缓存**：让YUM识别新配置的仓库。
    ```bash
    yum clean all
    yum makecache
    ```

## YUM常用命令大全 📋

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_77.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_79.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_81.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_83.png)

配置好仓库后，就可以使用YUM命令来管理软件了。以下是YUM最常用的命令列表：

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_85.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_87.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_89.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_91.png)

*   **搜索软件包**：在仓库中查找包含特定关键词的软件。
    ```bash
    yum search 关键词
    ```

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_93.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_95.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_97.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_99.png)

*   **安装软件包**：自动解决依赖并安装软件。
    ```bash
    yum install 软件包名
    ```
    使用 `-y` 选项可以自动确认所有提示。

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_101.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_103.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_105.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_107.png)

*   **更新软件包**：更新指定软件到最新版本。
    ```bash
    yum update 软件包名
    ```
    如果不指定包名，则更新所有可更新的软件包。

*   **卸载软件包**：移除指定的软件包（会尝试移除不再需要的依赖）。
    ```bash
    yum remove 软件包名
    ```

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_109.png)

*   **列出软件包**：
    *   列出所有已安装的软件包：`yum list installed`
    *   列出仓库中所有可安装的软件包：`yum list all`
    *   列出可更新的软件包：`yum list updates`

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_111.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_113.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_115.png)

*   **查看软件信息**：显示软件包的详细信息，如版本、描述、依赖等。
    ```bash
    yum info 软件包名
    ```

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_117.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_119.png)

*   **查看命令属于哪个软件包**：当某个命令找不到时，可以用此命令查询它由哪个软件包提供。
    ```bash
    yum provides */命令名
    ```

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_121.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_123.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_125.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_127.png)

## 总结 📝

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_129.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_131.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_132.png)

本节课中我们一起学习了RPM软件包管理的升级与卸载操作，了解了备份配置文件的重要性。接着，我们深入探讨了YUM软件包管理工具，掌握了其自动解决依赖的核心优势。我们学习了如何为内网环境搭建本地YUM仓库，并详细列出了YUM的各类常用命令，包括搜索、安装、更新、卸载和查询等。

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_134.png)

![](img/a7e8c8c7917b2bbd2057b1abd3ca66da_136.png)

通过本节内容，你应当能够根据实际环境（有网/无网）选择合适的软件管理方式，并熟练使用YUM来高效地管理系统软件，这是Linux系统运维中一项非常基础且重要的技能。