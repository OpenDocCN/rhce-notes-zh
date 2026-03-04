# Linux就该这么学：第34期：第20章：网络文件共享与DNS域名解析服务

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_0.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_2.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_3.png)

在本节课中，我们将要学习两种重要的网络服务：NFS网络文件共享服务和DNS域名解析服务。我们将从NFS的快速配置开始，然后深入探讨DNS的工作原理与配置方法，包括主服务器、从属服务器的搭建。

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_5.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_6.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_8.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_9.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_10.png)

---

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_12.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_13.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_15.png)

## NFS网络文件共享服务 🚀

上一节我们介绍了Samba服务，它可以实现跨平台的文件共享。本节中我们来看看如何在两台Linux服务器之间进行高效的文件共享。

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_17.png)

NFS服务配置起来非常简单快捷。如果工作场景仅限于Linux服务器间的文件共享，NFS是一个非常方便的选择。

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_19.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_20.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_22.png)

### 前期环境准备

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_24.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_25.png)

在配置NFS服务之前，需要确保网络畅通且防火墙策略允许访问。

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_27.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_28.png)

以下是配置防火墙和检查网络的步骤：

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_30.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_31.png)

1.  清空防火墙默认策略并设置为永久生效。
    ```bash
    firewall-cmd --permanent --zone=public --remove-service=dhcpv6-client
    firewall-cmd --permanent --zone=public --remove-service=ssh
    firewall-cmd --reload
    ```
2.  检查并配置网卡IP地址，确保两台主机可以互相ping通。

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_33.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_34.png)

### 配置NFS服务器端

假设我们想共享 `/home/haha` 目录。

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_36.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_38.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_39.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_41.png)

1.  创建共享目录和测试文件。
    ```bash
    mkdir /home/haha
    echo "Welcome to NFS share." > /home/haha/readme.txt
    ```
2.  编辑NFS主配置文件 `/etc/exports`，定义共享规则。
    ```
    /home/haha 192.168.10.20(rw,sync,root_squash)
    ```
    *   `/home/haha`: 要共享的目录路径。
    *   `192.168.10.20`: 允许访问的客户端IP地址。也可使用`*`（所有人）或`192.168.10.*`（整个网段）。
    *   `rw`: 允许读写。
    *   `sync`: 同步写入，确保数据实时写入硬盘。
    *   `root_squash`: 将远程root管理员映射为本地普通用户，增强安全性。
3.  让配置生效并启动相关服务。
    ```bash
    exportfs -r
    systemctl restart nfs-server
    systemctl enable nfs-server
    ```
4.  在RHEL 7/8中，还需开启防火墙相关服务，以便客户端能探测到共享。
    ```bash
    firewall-cmd --permanent --add-service=rpc-bind
    firewall-cmd --permanent --add-service=mountd
    firewall-cmd --permanent --add-service=nfs
    firewall-cmd --reload
    ```

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_42.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_44.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_46.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_48.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_49.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_50.png)

### 配置NFS客户端

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_52.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_54.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_55.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_57.png)

在客户端上，只需进行挂载操作即可访问共享目录。

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_59.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_61.png)

以下是客户端的操作步骤：

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_63.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_64.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_65.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_66.png)

1.  创建本地挂载点。
    ```bash
    mkdir /home/client_haha
    ```
2.  使用 `showmount` 命令查看服务器端共享信息。
    ```bash
    showmount -e 192.168.10.10
    ```
3.  手动挂载NFS共享目录。
    ```bash
    mount -t nfs 192.168.10.10:/home/haha /home/client_haha
    ```
4.  编辑 `/etc/fstab` 文件实现开机自动挂载。
    ```
    192.168.10.10:/home/haha /home/client_haha nfs defaults 0 0
    ```

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_68.png)

至此，NFS网络文件共享服务就配置完成了。可以看到，其配置过程非常简洁高效。

---

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_70.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_71.png)

## AutoFS自动挂载服务 ⚡

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_73.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_74.png)

上一节我们配置了NFS的自动挂载，但它是永久挂载。本节中我们来看看如何实现按需挂载，以节省系统资源。

AutoFS服务可以在用户访问指定目录时，才自动挂载对应的设备（如光盘、NFS共享），访问结束后自动卸载。

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_76.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_77.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_78.png)

### 安装与配置AutoFS

1.  安装autofs软件包。
    ```bash
    yum install -y autofs
    ```
2.  编辑主配置文件 `/etc/auto.master`，定义挂载点的上一级目录和对应的子配置文件。
    ```
    /media /etc/auto.media
    ```
    *   `/media`: 自动挂载的根目录。
    *   `/etc/auto.media`: 具体定义`/media`目录下子目录挂载规则的配置文件。
3.  编辑子配置文件 `/etc/auto.media`，定义具体的挂载规则。
    ```
    cdrom -fstype=iso9660,ro :/dev/cdrom
    ```
    *   `cdrom`: 在`/media`目录下创建的最终访问目录（`/media/cdrom`）。
    *   `-fstype=iso9660,ro`: 指定文件系统类型和只读权限。
    *   `:/dev/cdrom`: 要挂载的设备。
4.  重启autofs服务使其生效。
    ```bash
    systemctl restart autofs
    systemctl enable autofs
    ```

### 验证AutoFS效果

配置完成后，当用户访问 `/media/cdrom` 目录时，系统会自动将光盘挂载到该目录。当用户退出该目录一段时间后（默认5分钟），系统会自动卸载光盘。

这特别适用于挂载点众多但访问不频繁的场景，能有效降低系统资源消耗。

---

## DNS域名解析服务 🌐

前面我们完成了文件共享服务的学习。本节中我们将进入一个更核心的网络基础服务——DNS域名解析。虽然RHCE考试已不涉及，但理解DNS对深入掌握网络知识至关重要。

DNS负责将人类易记的域名（如 `www.linuxprobe.com`）转换为机器可识别的IP地址，这个过程称为**正向解析**。反之，将IP地址转换为域名的过程称为**反向解析**。DNS服务器中存储的正是**域名与IP地址的对应关系**。

### DNS服务器类型

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_80.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_81.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_82.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_83.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_85.png)

根据功能不同，DNS服务器主要分为三种类型：

*   **主服务器**：管理特定域名区域内域名与IP地址的对应关系，是数据的来源。
*   **从属服务器**：从主服务器同步数据，分担查询压力，提高查询效率和可靠性。
*   **缓存服务器**：不存储具体数据，仅转发查询请求并缓存结果，以加速本地查询。

全球有13台IPv4根域名服务器，它们是最顶层的主服务器。各国、各运营商部署的大量DNS服务器，大多属于从属或缓存服务器，它们共同构成了庞大的域名解析体系。

### 使用BIND软件部署DNS服务器

我们将使用BIND软件来部署DNS服务器。BIND由加州大学伯克利分校开发，是互联网上使用最广泛的DNS服务软件。

#### 部署主服务器

1.  安装BIND软件包及其安全插件。
    ```bash
    yum install -y bind bind-chroot
    ```
2.  编辑主配置文件 `/etc/named.conf`。
    *   将第11行 `listen-on port 53` 后的地址改为 `any`，监听所有网卡。
    *   将第17行 `allow-query` 后的地址改为 `any`，允许所有客户端查询。
3.  编辑区域配置文件 `/etc/named.rfc1912.zones`，定义我们要管理的域名区域。
    ```
    zone "linuxprobe.com" IN {
            type master;
            file "linuxprobe.com.zone";
            allow-transfer { none; };
    };
    zone "10.168.192.in-addr.arpa" IN {
            type master;
            file "192.168.10.arpa";
            allow-transfer { none; };
    };
    ```
    *   第一个 `zone` 定义正向解析区域 `linuxprobe.com`，数据文件为 `linuxprobe.com.zone`。
    *   第二个 `zone` 定义反向解析区域（对应 `192.168.10.0/24` 网段），数据文件为 `192.168.10.arpa`。注意IP地址需要反写。
    *   `allow-transfer { none; };` 表示不允许其他服务器同步此区域数据（从服务器配置时会修改）。
4.  根据模板创建并编辑正向解析数据文件 `/var/named/linuxprobe.com.zone`。
    ```
    $TTL 1D
    @       IN SOA  linuxprobe.com.  root.linuxprobe.com. (
                                            0       ; serial
                                            1D      ; refresh
                                            1H      ; retry
                                            1W      ; expire
                                            3H )    ; minimum
            NS      ns.linuxprobe.com.
    ns      A       192.168.10.10
    www     A       192.168.10.10
    ```
    *   `SOA` 记录起始授权机构。
    *   `NS` 记录域名服务器。
    *   `A` 记录将主机名（如 `ns`, `www`）解析到IP地址。
5.  根据模板创建并编辑反向解析数据文件 `/var/named/192.168.10.arpa`。
    ```
    $TTL 1D
    @       IN SOA  linuxprobe.com.  root.linuxprobe.com. (
                                            0       ; serial
                                            1D      ; refresh
                                            1H      ; retry
                                            1W      ; expire
                                            3H )    ; minimum
            NS      ns.linuxprobe.com.
    10      PTR     ns.linuxprobe.com.
    10      PTR     www.linuxprobe.com.
    ```
    *   `PTR` 记录将IP地址的主机位（如 `10`）反向解析到完整域名。
6.  启动DNS服务并设置开机自启，同时配置防火墙放行DNS服务（端口53）。
    ```bash
    systemctl start named
    systemctl enable named
    firewall-cmd --permanent --add-service=dns
    firewall-cmd --reload
    ```

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_87.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_89.png)

#### 配置客户端并测试

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_91.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_93.png)

在客户端（或服务器本机）将DNS服务器地址指向刚配置的DNS服务器IP（`192.168.10.10`）。

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_95.png)

然后使用 `nslookup` 命令进行测试：
```bash
nslookup www.linuxprobe.com 192.168.10.10
nslookup 192.168.10.10 192.168.10.10
```
第一条命令测试正向解析，应返回IP `192.168.10.10`。第二条命令测试反向解析，应返回域名 `www.linuxprobe.com`。

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_97.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_99.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_100.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_101.png)

#### 部署从属服务器

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_103.png)

从属服务器的作用是同步主服务器的数据，提供冗余和负载均衡。

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_105.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_107.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_108.png)

1.  在另一台主机上安装BIND软件。
2.  编辑其主配置文件 `/etc/named.conf`，修改监听和允许查询的设置（与主服务器类似）。
3.  编辑其区域配置文件 `/etc/named.rfc1912.zones`，关键是指定 `type` 为 `slave`，并指明主服务器地址。
    ```
    zone "linuxprobe.com" IN {
            type slave;
            file "slaves/linuxprobe.com.zone";
            masters { 192.168.10.10; };
    };
    zone "10.168.192.in-addr.arpa" IN {
            type slave;
            file "slaves/192.168.10.arpa";
            masters { 192.168.10.10; };
    };
    ```
    *   `type slave;` 声明此为从服务器。
    *   `masters { 192.168.10.10; };` 指定主服务器的IP地址。
    *   `file` 指定数据文件保存在 `/var/named/slaves/` 目录下，BIND会自动从主服务器同步并创建该文件。
4.  在主服务器的区域配置文件（`/etc/named.rfc1912.zones`）中，将对应区域的 `allow-transfer` 参数修改为允许从服务器IP地址同步。
    ```
    allow-transfer { 192.168.10.20; };
    ```
5.  重启主、从服务器的DNS服务。在从服务器的 `/var/named/slaves/` 目录下应能看到同步过来的数据文件。
6.  将客户端的DNS地址指向从服务器（`192.168.10.20`），同样可以使用 `nslookup` 命令验证解析是否正常。

---

## 总结 📚

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_110.png)

本节课中我们一起学习了：
1.  **NFS网络文件共享服务**：实现了Linux系统间的目录共享，配置简洁高效。
2.  **AutoFS自动挂载服务**：实现了存储设备的按需挂载与自动卸载，有助于节约系统资源。
3.  **DNS域名解析服务**：
    *   理解了DNS正向解析与反向解析的基本概念。
    *   学习了主服务器、从属服务器、缓存服务器三种类型的作用。
    *   使用BIND软件实战部署了DNS主服务器和从属服务器，并成功进行了域名解析测试。

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_112.png)

![](img/ab0cf6f3b48697b8bc07fa6fa7ebf707_114.png)

DNS是互联网的基石之一，虽然配置相对复杂，但通过本次学习，希望大家能对其工作原理有更深入的认识。下节课我们将继续学习DNS的高级功能，如TSIG密钥加密和分离解析技术。