# RHCE8.0视频教程：P10：配置软件源与FTP服务

![](img/a3404f6f5109d608b909f948f732b097_0.png)

![](img/a3404f6f5109d608b909f948f732b097_2.png)

![](img/a3404f6f5109d608b909f948f732b097_4.png)

在本节课中，我们将学习如何配置YUM软件源以及解决FTP服务配置中遇到的问题。主要内容包括：使用`yum-config-manager`工具添加远程软件源、处理软件源的GPG密钥验证，以及回顾并修正FTP匿名访问的配置问题。

![](img/a3404f6f5109d608b909f948f732b097_6.png)

## 配置FTP匿名访问 🔧

![](img/a3404f6f5109d608b909f948f732b097_8.png)

上一节我们介绍了FTP服务的基本概念，本节中我们来看看如何配置匿名访问并解决连接问题。

![](img/a3404f6f5109d608b909f948f732b097_10.png)

在配置FTP服务时，需要确保匿名用户功能已启用，并正确配置防火墙和SELinux。

![](img/a3404f6f5109d608b909f948f732b097_12.png)

以下是配置FTP匿名访问的关键步骤：

![](img/a3404f6f5109d608b909f948f732b097_14.png)

1.  **编辑FTP主配置文件**：使用`vim`编辑`/etc/vsftpd/vsftpd.conf`文件。
2.  **启用匿名用户**：在配置文件中，找到并确保`anonymous_enable=YES`这一行未被注释。
3.  **关闭防火墙**：临时关闭防火墙以测试连接，命令为`systemctl stop firewalld`。
4.  **关闭SELinux**：将SELinux设置为宽容模式，命令为`setenforce 0`。
5.  **重启FTP服务**：使用`systemctl restart vsftpd`使配置生效。

配置完成后，在客户端使用`ftp`命令连接服务器IP地址的21端口进行测试。

![](img/a3404f6f5109d608b909f948f732b097_16.png)

![](img/a3404f6f5109d608b909f948f732b097_18.png)

## 使用yum-config-manager添加软件源 📦

![](img/a3404f6f5109d608b909f948f732b097_20.png)

![](img/a3404f6f5109d608b909f948f732b097_22.png)

上一节我们介绍了手动编辑配置文件来添加软件源，本节中我们来看看如何使用`yum-config-manager`工具来管理软件源。

![](img/a3404f6f5109d608b909f948f732b097_24.png)

`yum-config-manager`是一个更便捷的管理工具，但需要先安装对应的软件包组。

![](img/a3404f6f5109d608b909f948f732b097_26.png)

![](img/a3404f6f5109d608b909f948f732b097_28.png)

![](img/a3404f6f5109d608b909f948f732b097_30.png)

![](img/a3404f6f5109d608b909f948f732b097_32.png)

以下是安装和使用`yum-config-manager`的步骤：

![](img/a3404f6f5109d608b909f948f732b097_34.png)

![](img/a3404f6f5109d608b909f948f732b097_36.png)

![](img/a3404f6f5109d608b909f948f732b097_38.png)

1.  **安装开发工具组**：首先需要安装`Development Tools`软件包组。命令如下：
    ```bash
    yum groupinstall "Development Tools" -y
    ```
2.  **安装`dnf-utils`**：`yum-config-manager`工具包含在`dnf-utils`软件包中。安装命令为：
    ```bash
    yum install dnf-utils -y
    ```
3.  **添加远程软件源**：安装完成后，可以使用以下命令添加远程软件源。将`<URL>`替换为实际的软件源地址。
    ```bash
    yum-config-manager --add-repo "<URL>"
    ```

![](img/a3404f6f5109d608b909f948f732b097_40.png)

![](img/a3404f6f5109d608b909f948f732b097_42.png)

## 管理软件源的GPG密钥 🔐

![](img/a3404f6f5109d608b909f948f732b097_44.png)

![](img/a3404f6f5109d608b909f948f732b097_46.png)

在添加第三方软件源（如EPEL）时，经常会遇到GPG密钥验证失败的问题。本节中我们来看看如何解决这个问题。

![](img/a3404f6f5109d608b909f948f732b097_48.png)

![](img/a3404f6f5109d608b909f948f732b097_50.png)

GPG密钥用于验证软件包的完整性和来源。有两种主要处理方法。

![](img/a3404f6f5109d608b909f948f732b097_52.png)

![](img/a3404f6f5109d608b909f948f732b097_54.png)

以下是处理GPG密钥的两种方法：

![](img/a3404f6f5109d608b909f948f732b097_56.png)

![](img/a3404f6f5109d608b909f948f732b097_58.png)

1.  **临时禁用GPG检查（不推荐用于生产）**：在软件源配置文件中添加`gpgcheck=0`。例如，在`/etc/yum.repos.d/epel.repo`文件中每个仓库的配置节下添加。
2.  **导入正确的GPG密钥（推荐）**：
    *   **方法一：使用`rpm`命令直接导入**。首先从软件源网站找到GPG密钥文件的链接（通常以`.key`或`.asc`结尾），然后使用以下命令导入：
        ```bash
        rpm --import <GPG_KEY_URL>
        ```
    *   **方法二：下载密钥文件并本地配置**。
        1.  使用`wget`下载密钥文件到本地，例如下载到`/etc/pki/rpm-gpg/`目录：
            ```bash
            wget -O /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-8 <GPG_KEY_URL>
            ```
        2.  在对应的软件源配置文件（如`epel.repo`）中，指定本地密钥文件路径：
            ```bash
            gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-8
            ```

## 总结与建议 📝

![](img/a3404f6f5109d608b909f948f732b097_60.png)

![](img/a3404f6f5109d608b909f948f732b097_62.png)

![](img/a3404f6f5109d608b909f948f732b097_64.png)

![](img/a3404f6f5109d608b909f948f732b097_65.png)

![](img/a3404f6f5109d608b909f948f732b097_67.png)

本节课中我们一起学习了FTP匿名访问的配置修正、使用`yum-config-manager`工具添加软件源，以及管理软件源的GPG密钥。

![](img/a3404f6f5109d608b909f948f732b097_69.png)

对于RHCE考试，给出以下建议：
*   **软件源配置**：考试时若提供远程软件源地址，最稳妥的方法是直接编辑`/etc/yum.repos.d/`目录下的`.repo`配置文件。这种方式无需安装额外软件，步骤清晰。
*   **配置文件结构**：手动创建软件源配置文件时，需包含以下核心字段：
    ```bash
    [repository_id]
    name=Repository Name
    baseurl=URL_to_repository
    enabled=1
    gpgcheck=0 或 gpgkey=URL_to_gpg_key
    ```
*   **问题排查**：遇到软件包安装或验证问题时，首先检查软件源URL是否能正常访问，其次确认GPG密钥是否正确配置。

![](img/a3404f6f5109d608b909f948f732b097_71.png)

掌握这些软件管理和服务配置的基础技能，是完成后续更复杂系统管理任务的前提。