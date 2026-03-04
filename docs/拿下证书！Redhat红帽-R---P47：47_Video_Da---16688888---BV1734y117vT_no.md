# 红帽认证体系课程：RH134：Ch09a_NFS与autofs

![](img/7d8f9b0ff8428cf750b24175f8938eb3_0.png)

## 概述
在本节课程中，我们将学习网络文件系统（NFS）的基本概念、配置方法以及如何实现自动挂载（autofs）。NFS允许Linux/Unix系统之间共享目录和文件，而autofs可以实现按需挂载，提升资源访问的灵活性和效率。

---

## NFS网络文件系统概念

上一节我们介绍了存储管理的基础知识，本节中我们来看看如何通过网络共享存储资源。

![](img/7d8f9b0ff8428cf750b24175f8938eb3_2.png)

网络文件系统（NFS）主要用于Unix和Linux系统之间的资源共享。客户端可以通过挂载的方式，像访问本地目录一样访问服务器端共享出来的目录。

![](img/7d8f9b0ff8428cf750b24175f8938eb3_4.png)

**核心概念**：
*   **共享目录**：服务器端发布出来供网络访问的目录。
*   **挂载**：客户端将服务器端的共享目录关联到本地一个空目录的过程。
*   **权限**：共享时可以设置读写（`rw`）或只读（`ro`）权限。出于安全考虑，默认会将客户端的root用户权限压缩为低权限用户（`nobody`，ID为65534）来访问共享目录。

---

## 配置NFS服务器端

![](img/7d8f9b0ff8428cf750b24175f8938eb3_6.png)

理解了基本概念后，我们开始动手配置。首先，我们需要在服务器端安装软件、设置共享目录并配置导出规则。

![](img/7d8f9b0ff8428cf750b24175f8938eb3_8.png)

**1. 安装NFS服务器软件包**
在服务器（例如 `servera`）上，确保已安装 `nfs-utils` 软件包。
```bash
dnf install -y nfs-utils
```

![](img/7d8f9b0ff8428cf750b24175f8938eb3_10.png)

![](img/7d8f9b0ff8428cf750b24175f8938eb3_12.png)

**2. 启动并启用NFS服务**
安装完成后，启动NFS服务并设置为开机自启。
```bash
systemctl enable --now nfs-server
systemctl status nfs-server
```

**3. 创建共享目录与示例文件**
创建一个用于共享的目录，并在其中生成测试文件，以便验证共享是否成功。
```bash
mkdir -p /mnt/share
touch /mnt/share/file{1..2}
```

**4. 配置共享导出规则**
NFS通过 `/etc/exports` 文件定义共享规则。编辑此文件，添加共享目录、允许访问的网络范围及权限。
```bash
vim /etc/exports
```
文件内容格式如下：
```
共享目录 客户端IP或网段(权限选项)
```
例如，允许 `192.168.14.0/24` 网段读写访问 `/mnt/share` 目录：
```
/mnt/share 192.168.14.0/24(rw,sync)
```
*   `rw`：读写权限。
*   `ro`：只读权限。
*   `sync`：同步写入，数据更安全。

![](img/7d8f9b0ff8428cf750b24175f8938eb3_14.png)

**关于`root_squash`**：
*   默认启用，将客户端的root用户映射为服务器上的 `nobody` 用户，增强安全性。
*   `no_root_squash` 选项会禁用此功能，允许客户端root保留权限，**存在安全风险，不推荐使用**。

![](img/7d8f9b0ff8428cf750b24175f8938eb3_16.png)

![](img/7d8f9b0ff8428cf750b24175f8938eb3_17.png)

![](img/7d8f9b0ff8428cf750b24175f8938eb3_18.png)

**5. 使配置生效并检查**
应用导出配置，并检查共享列表。
```bash
exportfs -rv
showmount -e localhost
```

![](img/7d8f9b0ff8428cf750b24175f8938eb3_20.png)

**6. 配置防火墙**
如果客户端无法访问，可能是防火墙阻止了。需要在服务器防火墙中放行NFS相关服务。
```bash
firewall-cmd --permanent --add-service={nfs,mountd,rpc-bind}
firewall-cmd --reload
```

![](img/7d8f9b0ff8428cf750b24175f8938eb3_22.png)

![](img/7d8f9b0ff8428cf750b24175f8938eb3_24.png)

---

## 配置NFS客户端挂载

服务器配置好后，客户端就可以挂载共享目录了。我们来看看临时挂载和永久挂载两种方式。

**1. 创建本地挂载点**
在客户端（例如 `serverb`）上，创建一个空目录作为挂载点。
```bash
mkdir -p /mnt/nfs_share
```

![](img/7d8f9b0ff8428cf750b24175f8938eb3_26.png)

![](img/7d8f9b0ff8428cf750b24175f8938eb3_28.png)

**2. 临时挂载**
使用 `mount` 命令进行临时挂载，重启后失效。
```bash
mount -t nfs 192.168.14.128:/mnt/share /mnt/nfs_share
```
*   `-t nfs`：指定文件系统类型为NFS。
*   `192.168.14.128:/mnt/share`：服务器IP和共享目录路径。
*   `/mnt/nfs_share`：本地挂载点。

使用 `df -hT` 命令检查挂载结果。

**3. 永久挂载**
编辑 `/etc/fstab` 文件实现开机自动挂载。
```bash
vim /etc/fstab
```
添加如下一行：
```
192.168.14.128:/mnt/share /mnt/nfs_share nfs defaults 0 0
```
保存后，使用 `mount -a` 测试配置是否正确。

**4. 解决客户端写入权限问题**
挂载后，客户端可能无法写入文件，这是因为服务器端共享目录的权限限制。

![](img/7d8f9b0ff8428cf750b24175f8938eb3_30.png)

![](img/7d8f9b0ff8428cf750b24175f8938eb3_31.png)

![](img/7d8f9b0ff8428cf750b24175f8938eb3_32.png)

以下是两种解决方法：
*   **方法一（推荐）**：在服务器端修改共享目录的权限，允许“其他用户”写入。
    ```bash
    chmod -R o+w /mnt/share
    ```
*   **方法二（不推荐）**：在服务器端的 `/etc/exports` 文件中为共享规则添加 `no_root_squash` 选项（安全风险高）。

---

![](img/7d8f9b0ff8428cf750b24175f8938eb3_34.png)

## 配置autofs自动挂载

手动挂载需要预先执行命令，而autofs可以实现按需自动挂载，闲置一段时间后自动卸载，非常方便。

![](img/7d8f9b0ff8428cf750b24175f8938eb3_36.png)

**1. 客户端安装autofs软件包**
在客户端上安装 `autofs` 软件。
```bash
dnf install -y autofs
```

![](img/7d8f9b0ff8428cf750b24175f8938eb3_38.png)

![](img/7d8f9b0ff8428cf750b24175f8938eb3_40.png)

![](img/7d8f9b0ff8428cf750b24175f8938eb3_42.png)

![](img/7d8f9b0ff8428cf750b24175f8938eb3_44.png)

![](img/7d8f9b0ff8428cf750b24175f8938eb3_46.png)

**2. 启动并启用autofs服务**
```bash
systemctl enable --now autofs
systemctl status autofs
```

**3. 配置主映射文件**
编辑 `/etc/auto.master` 文件，定义监控目录和对应的子映射文件。
```bash
vim /etc/auto.master
```
添加一行，例如监控 `/mnt/autofs` 目录，其规则定义在 `/etc/auto.ld` 文件中：
```
/mnt/autofs /etc/auto.ld
```

**4. 创建子映射文件**
创建并编辑子映射文件 `/etc/auto.ld`。
```bash
cp /etc/auto.misc /etc/auto.ld
vim /etc/auto.ld
```
在文件中添加自动挂载规则，格式为：
```
挂载点子目录 -挂载选项 服务器:共享目录
```
例如，实现访问 `/mnt/autofs/nfs` 时自动挂载远程NFS共享：
```
nfs -fstype=nfs 192.168.14.128:/mnt/share
```

![](img/7d8f9b0ff8428cf750b24175f8938eb3_48.png)

![](img/7d8f9b0ff8428cf750b24175f8938eb3_50.png)

**5. 重启服务并测试**
重启autofs服务使配置生效。
```bash
systemctl restart autofs
```
现在，当你访问 `/mnt/autofs/nfs` 时，系统会自动挂载远程共享；超过默认时间（如5分钟）无访问后，会自动卸载。

![](img/7d8f9b0ff8428cf750b24175f8938eb3_52.png)

---

## 总结
本节课我们一起学习了NFS网络文件系统的配置与管理。我们从NFS的基本概念讲起，逐步完成了服务器端的共享配置、客户端的挂载操作，并探讨了如何解决常见的写入权限问题。最后，我们学习了使用autofs服务实现按需自动挂载，这大大提升了资源访问的灵活性和管理效率。掌握这些技能，对于构建和维护网络存储环境至关重要。