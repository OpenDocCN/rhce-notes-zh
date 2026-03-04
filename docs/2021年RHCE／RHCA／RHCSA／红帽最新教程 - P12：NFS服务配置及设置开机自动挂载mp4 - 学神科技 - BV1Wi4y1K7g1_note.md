# 红帽认证课程：P12：NFS服务配置及开机自动挂载 🖥️➡️📁

在本节课中，我们将要学习网络文件系统（NFS）的配置与管理。NFS允许系统在网络上共享目录和文件，使远程文件访问如同本地操作一样便捷。我们将从NFS的基本概念讲起，逐步完成服务端配置、客户端挂载以及实现开机自动挂载。

## NFS简介

上一节我们介绍了FTP服务，本节中我们来看看另一种文件共享服务——NFS。

![](img/edb51b4cf3976de0fa1f1de465d6572e_1.png)

NFS全称为Network File System，即网络文件系统。它是一种由FreeBSD支持的文件系统协议，允许一个系统在网络上与他人共享目录和文件。通过使用NFS，用户和程序可以像访问本地文件一样访问远端系统上的文件。

![](img/edb51b4cf3976de0fa1f1de465d6572e_3.png)

NFS采用客户端-服务器（C/S）架构，默认使用端口`2049`。在RHEL/CentOS 7版本中，默认使用NFSv4版本。NFS服务在运行时，除了自身服务外，还依赖于RPC（Remote Procedure Call）服务来管理动态端口。

## 安装NFS服务

![](img/edb51b4cf3976de0fa1f1de465d6572e_5.png)

要配置NFS服务器，首先需要确保相关软件包已安装。

![](img/edb51b4cf3976de0fa1f1de465d6572e_7.png)

以下是安装NFS所需的核心软件包：
*   `rpcbind`：RPC端口映射服务，用于管理NFS的端口注册。
*   `nfs-utils`：NFS服务的主程序包。

![](img/edb51b4cf3976de0fa1f1de465d6572e_9.png)

通常这些包在系统安装时已默认存在。若未安装，可使用以下命令安装：
```bash
yum install -y rpcbind nfs-utils
```

![](img/edb51b4cf3976de0fa1f1de465d6572e_11.png)

![](img/edb51b4cf3976de0fa1f1de465d6572e_13.png)

## 配置NFS服务器

安装好软件后，下一步是配置NFS服务器以共享指定的目录。

![](img/edb51b4cf3976de0fa1f1de465d6572e_15.png)

NFS服务器的配置文件位于`/etc/exports`。该文件定义了哪些目录可以被共享、允许哪些客户端访问以及访问权限。文件初始为空。

![](img/edb51b4cf3976de0fa1f1de465d6572e_17.png)

例如，若想将本地的`/media`目录共享给所有客户端，并赋予读写权限，可以在`/etc/exports`文件中添加如下一行：
```
/media *(rw)
```
其中，`/media`是共享目录路径，`*`表示允许所有网段的客户端访问，`(rw)`表示授予读写权限。

修改配置文件后，需要重启NFS相关服务以使配置生效。**需要注意的是，必须先启动`rpcbind`服务，再启动`nfs-server`服务**。

![](img/edb51b4cf3976de0fa1f1de465d6572e_19.png)

![](img/edb51b4cf3976de0fa1f1de465d6572e_21.png)

以下是启动服务并设置为开机自启的命令：
```bash
systemctl start rpcbind
systemctl start nfs-server
systemctl enable rpcbind
systemctl enable nfs-server
```

启动后，可以使用`showmount -e <服务器IP>`命令在客户端查看服务器共享的目录列表。

![](img/edb51b4cf3976de0fa1f1de465d6572e_23.png)

## 客户端挂载NFS共享

![](img/edb51b4cf3976de0fa1f1de465d6572e_25.png)

配置好服务器并共享目录后，客户端就可以挂载并使用这个远程目录了。

在客户端，使用`mount`命令即可挂载远程NFS共享。命令格式如下：
```bash
mount -t nfs <服务器IP>:<共享目录路径> <本地挂载点>
```
例如，将服务器`192.168.1.201`的`/media`目录挂载到本地的`/opt`目录：
```bash
mount -t nfs 192.168.1.201:/media /opt
```
挂载成功后，使用`df -hT`命令可以查看挂载信息，远程目录的空间使用情况会像本地磁盘一样显示。

![](img/edb51b4cf3976de0fa1f1de465d6572e_27.png)

## 设置开机自动挂载

手动挂载的目录在系统重启后会失效。为了实现永久挂载，需要将挂载信息写入配置文件。

客户端的文件系统挂载表位于`/etc/fstab`。在该文件中添加一行，即可实现开机自动挂载。

添加的格式如下：
```
<服务器IP>:<共享目录路径> <本地挂载点> nfs defaults 0 0
```
以前面的例子，添加如下内容：
```
192.168.1.201:/media /opt nfs defaults 0 0
```
保存文件后，重启系统或执行`mount -a`命令即可验证自动挂载是否成功。

## 权限问题与高级参数

在实际使用中，可能会遇到权限问题。即使服务器端共享时设置了`rw`（读写）权限，客户端也可能无法创建文件。

![](img/edb51b4cf3976de0fa1f1de465d6572e_29.png)

这是因为NFS默认会将客户端的root用户映射为服务器上的`nfsnobody`用户。如果共享目录对`nfsnobody`用户没有写权限，则操作会失败。解决方法是在服务器端修改共享目录的所有者或权限。

![](img/edb51b4cf3976de0fa1f1de465d6572e_31.png)

例如，将共享目录的所有者改为`nfsnobody`：
```bash
chown nfsnobody:nfsnobody /media
```

NFS的配置参数非常丰富，可以精确控制访问和行为。

![](img/edb51b4cf3976de0fa1f1de465d6572e_33.png)

![](img/edb51b4cf3976de0fa1f1de465d6572e_35.png)

以下是`/etc/exports`文件中一些常用参数：
*   `ro`：只读共享。
*   `rw`：读写共享。
*   `sync`：同步写入，数据同时写入内存和硬盘（更安全）。
*   `async`：异步写入，先存入内存再写入硬盘（性能更好）。
*   `no_root_squash`：允许客户端root用户保持root权限（需谨慎使用）。

![](img/edb51b4cf3976de0fa1f1de465d6572e_37.png)

参数写在客户端地址后的括号内，多个参数用逗号分隔，例如：`/shared 192.168.1.0/24(rw,sync,no_root_squash)`。

![](img/edb51b4cf3976de0fa1f1de465d6572e_39.png)

## 总结

![](img/edb51b4cf3976de0fa1f1de465d6572e_41.png)

本节课中我们一起学习了NFS网络文件系统的配置与管理。

![](img/edb51b4cf3976de0fa1f1de465d6572e_43.png)

我们首先了解了NFS的基本概念和工作原理。然后，逐步完成了在服务器端安装软件、配置`/etc/exports`文件以共享目录、启动相关服务等操作。在客户端，我们学会了如何使用`mount`命令手动挂载远程共享，以及通过编辑`/etc/fstab`文件实现开机自动挂载。最后，我们还探讨了常见的NFS权限问题及其解决方法，并简要介绍了更多高级配置参数。

![](img/edb51b4cf3976de0fa1f1de465d6572e_45.png)

通过掌握NFS，你可以在局域网内轻松实现文件资源的集中存储和共享访问。