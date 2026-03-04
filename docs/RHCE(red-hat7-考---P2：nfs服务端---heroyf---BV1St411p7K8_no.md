# RHCE 考前讲解：P2：NFS 服务端配置 🚀

![](img/f56f86b6501e7835e7c2a052c1c42498_0.png)

![](img/f56f86b6501e7835e7c2a052c1c42498_2.png)

在本节课中，我们将学习如何在 Red Hat Enterprise Linux 7 上配置 NFS 服务端。我们将按照最优做法，一步步完成共享目录的创建、配置文件的编写、服务的启动以及防火墙规则的设置，确保整个过程清晰、无坑。

---

![](img/f56f86b6501e7835e7c2a052c1c42498_4.png)

## 创建共享目录 📁

![](img/f56f86b6501e7835e7c2a052c1c42498_6.png)

首先，我们需要创建用于共享的目录。我们将在所有节点上创建一个名为 `public` 的目录，并将其所有者设置为 `nobody` 用户。

![](img/f56f86b6501e7835e7c2a052c1c42498_8.png)

![](img/f56f86b6501e7835e7c2a052c1c42498_10.png)

执行以下命令：
```bash
mkdir /public
chown nobody /public
```

![](img/f56f86b6501e7835e7c2a052c1c42498_12.png)

## 配置 NFS 共享 📝

![](img/f56f86b6501e7835e7c2a052c1c42498_14.png)

![](img/f56f86b6501e7835e7c2a052c1c42498_15.png)

上一节我们创建了共享目录，本节中我们来看看如何配置 NFS 服务以共享这个目录。这主要通过编辑 `/etc/exports` 配置文件来实现。

![](img/f56f86b6501e7835e7c2a052c1c42498_17.png)

以下是配置步骤：
1.  打开 `/etc/exports` 文件进行编辑。
2.  添加一行配置，指定共享目录、允许访问的网络以及相关选项。
3.  保存并退出编辑器。

![](img/f56f86b6501e7835e7c2a052c1c42498_19.png)

具体的配置内容如下：
```
/public 172.25.0.0/24(rw,sync,sec=krb5)
```
这条配置的含义是：将 `/public` 目录共享给 `172.25.0.0/24` 网段，客户端拥有读写权限，采用同步写入模式，并使用 Kerberos 5 进行安全验证。

![](img/f56f86b6501e7835e7c2a052c1c42498_21.png)

## 配置 Kerberos 认证 🔐

![](img/f56f86b6501e7835e7c2a052c1c42498_23.png)

为了使用 `sec=krb5` 选项，我们需要配置 Kerberos。这涉及到下载并安装 Kerberos 相关的软件包。

![](img/f56f86b6501e7835e7c2a052c1c42498_25.png)

![](img/f56f86b6501e7835e7c2a052c1c42498_27.png)

执行以下命令来安装必要的软件包：
```bash
yum install -y krb5-workstation
```

![](img/f56f86b6501e7835e7c2a052c1c42498_29.png)

## 启动并启用服务 ⚙️

![](img/f56f86b6501e7835e7c2a052c1c42498_31.png)

![](img/f56f86b6501e7835e7c2a052c1c42498_33.png)

配置文件准备就绪后，我们需要启动相关的服务，并设置为开机自动启动，以确保 NFS 服务在系统重启后能正常运行。

![](img/f56f86b6501e7835e7c2a052c1c42498_35.png)

以下是需要启动和启用的服务列表：
*   `nfs-server`：NFS 服务主程序。
*   `nfs-secure-server`：用于支持 NFS 安全认证的服务。
*   `rpcbind`：RPC 端口映射服务，NFS 依赖它。

![](img/f56f86b6501e7835e7c2a052c1c42498_37.png)

![](img/f56f86b6501e7835e7c2a052c1c42498_38.png)

执行以下命令：
```bash
systemctl start nfs-server nfs-secure-server rpcbind
systemctl enable nfs-server nfs-secure-server rpcbind
```

![](img/f56f86b6501e7835e7c2a052c1c42498_39.png)

## 配置防火墙 🔥

![](img/f56f86b6501e7835e7c2a052c1c42498_41.png)

为了让客户端能够访问 NFS 服务，我们需要在防火墙中开放相应的服务端口。Red Hat 7 的 `firewalld` 提供了预定义的 NFS 服务规则。

![](img/f56f86b6501e7835e7c2a052c1c42498_43.png)

![](img/f56f86b6501e7835e7c2a052c1c42498_45.png)

执行以下命令以永久开放 NFS 服务并重载防火墙配置：
```bash
firewall-cmd --permanent --add-service=nfs
firewall-cmd --reload
```

![](img/f56f86b6501e7835e7c2a052c1c42498_47.png)

---

![](img/f56f86b6501e7835e7c2a052c1c42498_49.png)

本节课中我们一起学习了在 RHEL 7 上配置 NFS 服务端的完整流程。我们从创建共享目录开始，逐步完成了 NFS 配置文件的编写、Kerberos 认证的初步设置、服务的启动与启用，最后配置了防火墙规则以允许访问。遵循这些步骤，可以确保 NFS 服务端正确、安全地运行。