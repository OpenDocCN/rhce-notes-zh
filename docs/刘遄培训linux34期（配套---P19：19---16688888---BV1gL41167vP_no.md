# Linux培训课程：第12章：网络文件共享服务

![](img/116c6ccbeb88a650b43b90e12929e5c1_0.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_2.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_4.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_6.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_8.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_10.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_12.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_14.png)

在本节课中，我们将学习两种在Linux系统间高效共享文件的方法：NFS（网络文件系统）和Autofs（自动挂载文件系统）。我们将从配置简单的NFS服务开始，然后了解如何按需自动挂载文件系统以节省资源。

![](img/116c6ccbeb88a650b43b90e12929e5c1_16.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_18.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_20.png)

## NFS网络文件系统

![](img/116c6ccbeb88a650b43b90e12929e5c1_22.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_24.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_26.png)

上一节我们介绍了Samba服务可以实现跨平台文件共享。本节中我们来看看如何在两台Linux服务器之间快速共享文件，我们将使用NFS服务。

![](img/116c6ccbeb88a650b43b90e12929e5c1_28.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_30.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_32.png)

NFS服务在RHEL 7/8系统中默认已安装，配置过程非常简洁。

![](img/116c6ccbeb88a650b43b90e12929e5c1_34.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_36.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_38.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_40.png)

以下是配置NFS服务前需要完成的准备工作：

1.  **配置防火墙**：清空防火墙默认规则，并永久放行NFS相关服务，确保网络访问不受阻。
    ```bash
    firewall-cmd --permanent --add-service=nfs
    firewall-cmd --permanent --add-service=rpc-bind
    firewall-cmd --permanent --add-service=mountd
    firewall-cmd --reload
    ```
2.  **检查网络**：确保两台服务器的IP地址配置正确且可以互相通信。
3.  **准备共享目录与文件**：在服务器端创建一个用于共享的目录，并在其中放置测试文件。
    ```bash
    mkdir /nfs_share
    echo "This is a test file." > /nfs_share/testfile.txt
    chmod -R 777 /nfs_share
    ```

![](img/116c6ccbeb88a650b43b90e12929e5c1_42.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_44.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_46.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_48.png)

准备工作完成后，即可开始配置NFS服务。

![](img/116c6ccbeb88a650b43b90e12929e5c1_50.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_52.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_54.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_56.png)

NFS服务的共享信息保存在 `/etc/exports` 配置文件中。我们需要按照特定格式编辑此文件。

![](img/116c6ccbeb88a650b43b90e12929e5c1_58.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_60.png)

以下是 `/etc/exports` 文件的基本配置格式说明：

![](img/116c6ccbeb88a650b43b90e12929e5c1_62.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_64.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_66.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_68.png)

*   **共享目录路径**：第一列写入要共享的目录绝对路径。
*   **允许访问的主机**：第二列指定允许访问的客户端。有三种写法：
    *   `*`：允许所有主机访问。
    *   `192.168.10.20`：允许指定IP的主机访问。
    *   `192.168.10.*`：允许指定网段的所有主机访问。
*   **权限与参数**：第三列设置共享参数，常见组合有：
    *   `rw`：允许读写。
    *   `sync`：同步写入，数据实时写入硬盘，更安全。
    *   `root_squash`：将远程root用户映射为本地普通用户（如nfsnobody），增强安全性。

一个完整的配置行示例如下：
```bash
/nfs_share 192.168.10.20(rw,sync,root_squash)
```

![](img/116c6ccbeb88a650b43b90e12929e5c1_70.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_72.png)

编辑保存配置文件后，需要让配置生效。在RHEL 7/8中，需要重启NFS服务。
```bash
systemctl restart nfs-server
systemctl enable nfs-server
```

![](img/116c6ccbeb88a650b43b90e12929e5c1_74.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_76.png)

服务端配置完成后，客户端即可进行挂载访问。

客户端需要执行以下三个步骤来使用NFS共享：

1.  **创建本地挂载点**：新建一个目录，用于挂载远程共享。
    ```bash
    mkdir /mnt/nfs_client
    ```
2.  **查看可用共享**：使用 `showmount` 命令探测服务器端提供了哪些共享。
    ```bash
    showmount -e 192.168.10.10
    ```
3.  **挂载共享目录**：使用 `mount` 命令进行挂载。NFS的挂载格式与Samba不同。
    ```bash
    mount -t nfs 192.168.10.10:/nfs_share /mnt/nfs_client
    ```

挂载成功后，即可像访问本地目录一样访问共享文件。为了在系统重启后自动挂载，需要将挂载信息写入 `/etc/fstab` 文件。
```bash
192.168.10.10:/nfs_share /mnt/nfs_client nfs defaults 0 0
```

## Autofs自动挂载文件系统

NFS实现了稳定的文件共享，但会持续占用系统资源。本节中我们来看看如何实现按需挂载，即只在访问时自动挂载文件系统，这需要用到Autofs服务。

Autofs服务可以监控指定的挂载点，仅在用户访问时触发挂载操作，闲置一段时间后自动卸载，从而节省系统资源。

![](img/116c6ccbeb88a650b43b90e12929e5c1_78.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_80.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_82.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_84.png)

首先需要安装Autofs软件包。
```bash
yum install -y autofs
```

Autofs的配置涉及两个主要文件：主配置文件 `/etc/auto.master` 和子配置文件。

主配置文件 `/etc/auto.master` 用于定义挂载点与子配置文件的对应关系。它并不包含具体的挂载参数。

编辑 `/etc/auto.master`，添加如下行：
```bash
/media /etc/auto.media
```
这表示将对 `/media` 目录下的挂载操作定义在 `/etc/auto.media` 子配置文件中。

接下来，编辑子配置文件 `/etc/auto.media`，定义具体的自动挂载规则。

![](img/116c6ccbeb88a650b43b90e12929e5c1_86.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_88.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_90.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_92.png)

子配置文件的格式为：`本地子目录名 挂载参数 设备或远程路径`。
例如，配置访问 `/media/cdrom` 时自动挂载光盘：
```bash
cdrom -fstype=iso9660,ro :/dev/cdrom
```

![](img/116c6ccbeb88a650b43b90e12929e5c1_94.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_96.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_98.png)

配置完成后，重启并启用Autofs服务使配置生效。
```bash
systemctl restart autofs
systemctl enable autofs
```

![](img/116c6ccbeb88a650b43b90e12929e5c1_100.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_102.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_104.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_106.png)

现在，当用户尝试进入 `/media/cdrom` 目录时，系统会自动挂载光盘；当该目录一段时间未被访问后，系统会自动卸载光盘。

![](img/116c6ccbeb88a650b43b90e12929e5c1_108.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_110.png)

## 课程总结

![](img/116c6ccbeb88a650b43b90e12929e5c1_112.png)

本节课中我们一起学习了两种Linux文件共享与挂载技术。

![](img/116c6ccbeb88a650b43b90e12929e5c1_114.png)

![](img/116c6ccbeb88a650b43b90e12929e5c1_115.png)

我们首先完成了NFS网络文件系统的配置，实现了两台Linux服务器间稳定、高效的文件共享。随后，我们探讨了Autofs自动挂载文件系统，学会了如何配置按需挂载，从而优化系统资源的使用。理解这两种服务的适用场景，将帮助你在实际工作中做出更合适的选择。