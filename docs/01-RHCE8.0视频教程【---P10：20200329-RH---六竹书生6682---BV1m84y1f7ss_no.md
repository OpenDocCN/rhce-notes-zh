# RHCE8.0视频教程：02：配置软件仓库与证书管理

![](img/a6821e316ceb3f4240c442464808ac6d_0.png)

![](img/a6821e316ceb3f4240c442464808ac6d_2.png)

在本节课中，我们将学习如何配置YUM软件仓库，并解决配置过程中可能遇到的证书问题。课程内容包括两种配置仓库的方法，以及如何导入或下载仓库的GPG密钥。

![](img/a6821e316ceb3f4240c442464808ac6d_4.png)

## 概述

上一节我们介绍了FTP共享的基本配置。本节中，我们来看看如何为系统配置额外的软件仓库。在Linux系统中，软件仓库是获取和安装软件包的主要来源。正确配置仓库对于后续的软件管理至关重要。

![](img/a6821e316ceb3f4240c442464808ac6d_6.png)

![](img/a6821e316ceb3f4240c442464808ac6d_8.png)

## 配置YUM仓库的两种方法

配置YUM仓库主要有两种方式：手动编辑配置文件和使用`yum-config-manager`工具。以下是这两种方法的详细步骤。

![](img/a6821e316ceb3f4240c442464808ac6d_10.png)

### 方法一：手动编辑配置文件

![](img/a6821e316ceb3f4240c442464808ac6d_12.png)

这是最直接的方法，通过编辑`/etc/yum.repos.d/`目录下的`.repo`文件来添加仓库。

![](img/a6821e316ceb3f4240c442464808ac6d_14.png)

1.  **创建或编辑仓库文件**：
    使用文本编辑器（如`vim`）在`/etc/yum.repos.d/`目录下创建一个新的`.repo`文件，例如`myrepo.repo`。

    ```bash
    vim /etc/yum.repos.d/myrepo.repo
    ```

2.  **编写仓库配置**：
    在文件中添加以下格式的内容。请将示例中的`[repo-id]`、`name`和`baseurl`替换为实际的仓库信息。

    ```ini
    [repo-id]
    name=Repository Name
    baseurl=http://path.to.your/repository
    enabled=1
    gpgcheck=0
    ```

    *   `[repo-id]`：仓库的唯一标识符。
    *   `name`：仓库的描述性名称。
    *   `baseurl`：仓库的访问地址（URL）。
    *   `enabled=1`：启用此仓库。
    *   `gpgcheck=0`：暂时禁用GPG密钥检查（为了快速测试，后续会解决证书问题）。

![](img/a6821e316ceb3f4240c442464808ac6d_16.png)

3.  **保存并测试**：
    保存文件后，运行`yum repolist`命令来验证仓库是否已成功添加并可用。

![](img/a6821e316ceb3f4240c442464808ac6d_18.png)

### 方法二：使用 yum-config-manager 工具

![](img/a6821e316ceb3f4240c442464808ac6d_20.png)

`yum-config-manager`是一个可以动态管理仓库的命令行工具，但需要额外安装。

![](img/a6821e316ceb3f4240c442464808ac6d_22.png)

1.  **安装必要软件组**：
    首先，需要安装包含`yum-config-manager`的工具组。

    ```bash
    yum groupinstall "Development Tools"
    ```

    或者，更精确地，安装`dnf-utils`软件包（在RHEL 8/CentOS 8中，YUM的后端是DNF）。

    ```bash
    yum install dnf-utils -y
    ```

![](img/a6821e316ceb3f4240c442464808ac6d_24.png)

![](img/a6821e316ceb3f4240c442464808ac6d_26.png)

2.  **使用工具添加仓库**：
    安装完成后，可以使用以下命令直接添加仓库。

    ```bash
    yum-config-manager --add-repo http://path.to.your/repository
    ```

    此命令会自动在`/etc/yum.repos.d/`目录下生成对应的`.repo`文件。

![](img/a6821e316ceb3f4240c442464808ac6d_28.png)

![](img/a6821e316ceb3f4240c442464808ac6d_30.png)

## 处理仓库的GPG密钥

![](img/a6821e316ceb3f4240c442464808ac6d_32.png)

![](img/a6821e316ceb3f4240c442464808ac6d_34.png)

![](img/a6821e316ceb3f4240c442464808ac6d_36.png)

为了确保软件包的安全性，大多数官方仓库都使用GPG密钥进行签名验证。添加仓库时，需要处理其GPG密钥。

![](img/a6821e316ceb3f4240c442464808ac6d_38.png)

以下是导入GPG密钥的两种常用方法。

![](img/a6821e316ceb3f4240c442464808ac6d_40.png)

### 方法一：使用 rpm 命令直接导入

![](img/a6821e316ceb3f4240c442464808ac6d_42.png)

![](img/a6821e316ceb3f4240c442464808ac6d_44.png)

如果已知GPG密钥文件的URL，可以直接使用`rpm`命令导入。

![](img/a6821e316ceb3f4240c442464808ac6d_46.png)

![](img/a6821e316ceb3f4240c442464808ac6d_48.png)

```bash
rpm --import http://path.to.your/repository/RPM-GPG-KEY-example
```

![](img/a6821e316ceb3f4240c442464808ac6d_50.png)

![](img/a6821e316ceb3f4240c442464808ac6d_52.png)

### 方法二：下载密钥后配置

![](img/a6821e316ceb3f4240c442464808ac6d_54.png)

另一种方法是先将密钥文件下载到本地，然后在仓库配置文件中指定其路径。

1.  **下载GPG密钥文件**：
    使用`wget`或`curl`命令下载密钥文件到系统专用的目录，例如`/etc/pki/rpm-gpg/`。

    ```bash
    wget -O /etc/pki/rpm-gpg/RPM-GPG-KEY-example http://path.to.your/repository/RPM-GPG-KEY-example
    ```

![](img/a6821e316ceb3f4240c442464808ac6d_56.png)

![](img/a6821e316ceb3f4240c442464808ac6d_58.png)

2.  **在仓库配置中指定密钥路径**：
    编辑对应的`.repo`文件，将`gpgcheck`设置为`1`，并添加`gpgkey`行指向本地密钥文件。

    ```ini
    [repo-id]
    name=Repository Name
    baseurl=http://path.to.your/repository
    enabled=1
    gpgcheck=1
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-example
    ```

![](img/a6821e316ceb3f4240c442464808ac6d_60.png)

## 实践建议与注意事项

![](img/a6821e316ceb3f4240c442464808ac6d_62.png)

![](img/a6821e316ceb3f4240c442464808ac6d_64.png)

![](img/a6821e316ceb3f4240c442464808ac6d_65.png)

在考试或实际工作中配置仓库时，请注意以下几点：

![](img/a6821e316ceb3f4240c442464808ac6d_67.png)

![](img/a6821e316ceb3f4240c442464808ac6d_69.png)

*   **优先选择手动编辑**：对于RHCE考试，手动编辑`/etc/yum.repos.d/`目录下的配置文件通常更直接可靠，避免因工具安装问题而浪费时间。
*   **验证仓库地址**：在配置`baseurl`之前，可以尝试在图形界面或命令行中使用浏览器（如`links`）或`curl`访问该地址，确保路径正确，并能看到`repodata`等目录。
*   **灵活处理GPG检查**：如果时间紧迫或暂时无法获取密钥，可以先将`gpgcheck`设置为`0`以完成后续任务，但应知道正式环境中需要正确配置密钥验证。
*   **使用完整仓库集**：对于本地光盘或镜像源，确保同时配置`BaseOS`和`AppStream`仓库，以获取完整的软件包。

## 总结

![](img/a6821e316ceb3f4240c442464808ac6d_71.png)

本节课中我们一起学习了在RHEL 8系统中配置YUM软件仓库的核心技能。我们掌握了两种添加仓库的方法：直接编辑配置文件和使用`yum-config-manager`工具。同时，我们也深入了解了如何处理仓库的GPG密钥以保证软件安装的安全性，包括通过`rpm --import`直接导入和下载后本地配置两种方式。这些是进行系统软件管理的基础，请务必熟练掌握。