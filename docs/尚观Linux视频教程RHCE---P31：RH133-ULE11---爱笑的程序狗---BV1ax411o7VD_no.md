# 尚观Linux视频教程RHCE精品课程：P31：RH133-ULE115-0-2-RHEL-kickstart-无人值守安装 🚀

![](img/8d27e75e22228bafc6ffdee9044cb815_0.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_2.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_4.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_6.png)

在本节课中，我们将要学习如何在RHEL5系统中配置和使用Kickstart进行无人值守安装。我们将从生成应答文件开始，逐步讲解如何设置安装源、配置网络服务，并最终引导一台新机器完成自动化安装。

---

## 概述与准备工作 📋

上一节我们介绍了Kickstart的基本概念。本节中我们来看看如何在RHEL5中具体操作。首先，任何已安装好的RHEL系统，在root用户的主目录下，通常都有一个名为 `anaconda-ks.cfg` 的文件。这个文件记录了该系统安装时的配置，可以作为Kickstart应答文件的模板。

**注意**：直接修改和使用 `anaconda-ks.cfg` 文件存在风险，因为它包含特定于原机器的配置（如分区信息）。更安全的方法是使用系统工具生成。

以下是生成Kickstart配置文件的步骤：
1.  确保系统已安装 `system-config-kickstart` 和 `pykickstart` 软件包。
    ```bash
    rpm -ivh system-config-kickstart
    rpm -ivh pykickstart
    ```
2.  安装完成后，执行 `system-config-kickstart` 命令，会打开一个图形化配置界面。

![](img/8d27e75e22228bafc6ffdee9044cb815_8.png)

---

## 使用图形工具配置Kickstart 🛠️

执行 `system-config-kickstart` 后，会出现一个与手动安装过程类似的向导界面，引导你一步步设置。

以下是需要配置的主要部分及其说明：
*   **基本配置**：设置语言、键盘、时区、root密码等。
*   **安装方法**：选择安装来源，如NFS、FTP或HTTP。需要指定服务器IP（如 `192.168.3.95`）和包含安装文件的目录路径（如 `/var/ftp/pub`）。
*   **引导装载程序选项**：设置GRUB密码、安装位置（如MBR）以及内核参数（例如 `vga=0x314` 设置800x600分辨率）。
*   **分区信息**：清除现有分区表并创建新分区。例如，创建 `/` 根分区（10G）、`/boot` 分区（200M）和 `swap` 分区（2G）。**注意**：此工具默认不支持LVM，但你可以从一台使用LVM安装的机器的 `anaconda-ks.cfg` 中复制相关配置行到此文件中。
*   **网络配置**：为待安装的机器配置网络，通常选择 **DHCP**。
*   **验证、防火墙与SELinux**：按需设置。RHCE考试通常要求打开SELinux。
*   **显示配置**：如果不安装图形界面，此处可不配置。
*   **软件包选择**：根据需求选择要安装的软件包组。
*   **预安装与安装后脚本**：可以添加在安装前或安装后自动执行的脚本。例如，在安装后关闭防火墙：
    ```bash
    service iptables off
    ```
    **注意**：脚本中的命令需要使用完整路径。

配置完成后，点击 **Save File** 按钮，将文件保存（例如保存为 `/root/ks.cfg`）。这样生成的 `ks.cfg` 文件结构清晰，便于使用。

![](img/8d27e75e22228bafc6ffdee9044cb815_10.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_12.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_14.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_15.png)

---

![](img/8d27e75e22228bafc6ffdee9044cb815_16.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_17.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_19.png)

## 搭建安装服务器 🌐

![](img/8d27e75e22228bafc6ffdee9044cb815_21.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_23.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_25.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_27.png)

有了Kickstart应答文件后，需要将其和系统安装文件（安装树）放到一个服务器上，供客户端访问。

以下是搭建安装服务器的步骤：
1.  **准备安装树**：将RHEL5安装光盘的所有内容复制到服务器的一个目录中，这个目录就称为“安装树”。
    ```bash
    cp -r /mnt/* /var/ftp/pub/rhel5/
    ```
    （如果磁盘空间不足，可以暂时用 `mount` 命令挂载光盘镜像，但可能会影响NFS共享，建议使用FTP方式）。
2.  **放置应答文件**：将生成的 `ks.cfg` 文件复制到服务器共享目录。
    ```bash
    cp /root/ks.cfg /var/ftp/
    ```
3.  **配置并启动共享服务**：可以选择NFS或FTP共享目录。
    *   **NFS方式**：编辑 `/etc/exports` 文件，添加共享配置。
        ```
        /var/ftp 192.168.3.0/24(ro,sync)
        ```
        然后启动NFS服务：`service nfs start`。
    *   **FTP方式**：安装并启动VSFTPD服务。
        ```bash
        rpm -ivh vsftpd
        service vsftpd start
        ```
    **重要区别**：NFS要求客户端使用**实际服务器路径**访问，而FTP/HTTP使用相对于FTP根目录（如`/var/ftp`）的**虚拟路径**访问。

![](img/8d27e75e22228bafc6ffdee9044cb815_29.png)

---

![](img/8d27e75e22228bafc6ffdee9044cb815_31.png)

## 执行无人值守安装 ⚡

服务器配置好后，就可以在客户端启动安装了。

以下是启动安装的命令格式：
1.  用RHEL5安装光盘或ISO镜像启动客户端机器。
2.  在出现引导界面时，按提示（通常是按 `F2` 或直接输入）进入启动参数输入模式。
3.  输入启动命令，指定Kickstart文件的位置。
    *   **如果使用NFS共享**：
        ```
        linux ks=nfs:192.168.3.96:/var/ftp/ks.cfg
        ```
    *   **如果使用FTP共享**：
        ```
        linux ks=ftp://192.168.3.96/ks.cfg
        ```
4.  回车后，安装程序会引导内核启动，然后加载 `anaconda` 安装程序。`anaconda` 会自动读取指定的 `ks.cfg` 文件，并开始全自动的安装过程，无需人工干预。

![](img/8d27e75e22228bafc6ffdee9044cb815_33.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_34.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_36.png)

---

## 常见问题与技巧 💡

![](img/8d27e75e22228bafc6ffdee9044cb815_38.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_39.png)

在配置过程中可能会遇到一些问题，以下是两个常见问题的解决方法：

![](img/8d27e75e22228bafc6ffdee9044cb815_41.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_43.png)

1.  **跳过安装序列号输入**：在 `ks.cfg` 文件中添加以下行，可以避免安装过程中输入序列号。
    ```
    key --skip
    ```
2.  **使用LVM分区**：`system-config-kickstart` 图形工具不直接支持配置LVM。你可以先在一台使用LVM安装的系统中，将其 `/root/anaconda-ks.cfg` 文件中关于 `part` 和 `volgroup` 的配置段落复制到你自己的 `ks.cfg` 文件中。

![](img/8d27e75e22228bafc6ffdee9044cb815_45.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_47.png)

---

![](img/8d27e75e22228bafc6ffdee9044cb815_49.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_51.png)

## 总结 🎯

![](img/8d27e75e22228bafc6ffdee9044cb815_52.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_53.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_55.png)

![](img/8d27e75e22228bafc6ffdee9044cb815_57.png)

本节课中我们一起学习了在RHEL5上实现Kickstart无人值守安装的完整流程。关键步骤包括：使用 `system-config-kickstart` 工具生成应答文件、搭建包含“安装树”和应答文件的服务器（可选择NFS或FTP方式）、以及在客户端通过引导参数指定Kickstart文件来启动自动化安装。虽然初始配置需要一些步骤，但一次配置成功后，就可以快速、批量地部署完全相同的系统，极大地提高了效率。