# RHCE考前讲解：P8：配置SMB共享目录 📂

![](img/08b7de6fb103db26af0d509c35ca93ab_1.png)

在本节课中，我们将学习如何在Red Hat Enterprise Linux 7上配置SMB共享服务。SMB（Server Message Block）是一种网络文件共享协议，允许不同计算机之间共享文件和打印机。我们将按照考试要求，一步步完成共享目录的创建、配置、权限设置以及客户端访问测试。

![](img/08b7de6fb103db26af0d509c35ca93ab_2.png)

---

![](img/08b7de6fb103db26af0d509c35ca93ab_4.png)

## 概述与环境准备

![](img/08b7de6fb103db26af0d509c35ca93ab_6.png)

首先，我们需要在服务器端进行操作。确保系统已安装必要的SMB服务软件包，并准备好用于共享的目录和用户。

![](img/08b7de6fb103db26af0d509c35ca93ab_8.png)

上一节我们介绍了课程目标，本节中我们来看看具体的配置步骤。

![](img/08b7de6fb103db26af0d509c35ca93ab_10.png)

以下是初始环境检查与准备的步骤：

![](img/08b7de6fb103db26af0d509c35ca93ab_12.png)

1.  返回根目录。
2.  安装SMB服务端和客户端软件包。命令为：
    ```bash
    yum install samba samba-client -y
    ```
3.  创建一个用于共享的目录，例如 `/srv/group_dir`。
4.  创建一个系统组，例如 `star` 组。
5.  将共享目录的所属组修改为 `star` 组。命令为：
    ```bash
    chgrp star /srv/group_dir
    ```

![](img/08b7de6fb103db26af0d509c35ca93ab_14.png)

---

## 配置SELinux安全上下文

![](img/08b7de6fb103db26af0d509c35ca93ab_16.png)

在配置SMB共享前，需要正确设置SELinux的安全上下文，否则即使配置正确，客户端也可能无法访问。

![](img/08b7de6fb103db26af0d509c35ca93ab_18.png)

上一节我们创建了共享目录，本节中我们需要为其配置SELinux策略。

![](img/08b7de6fb103db26af0d509c35ca93ab_19.png)

设置SELinux安全上下文的命令是 `semanage fcontext` 和 `restorecon`。其作用是修改SELinux策略中文件或目录的默认安全标签。

![](img/08b7de6fb103db26af0d509c35ca93ab_21.png)

以下是具体操作：

![](img/08b7de6fb103db26af0d509c35ca93ab_23.png)

1.  使用 `semanage fcontext` 命令为共享目录添加默认的Samba共享上下文规则。
    ```bash
    semanage fcontext -a -t samba_share_t "/srv/group_dir(/.*)?"
    ```
2.  使用 `restorecon` 命令递归地应用新的安全上下文到该目录。
    ```bash
    restorecon -Rv /srv/group_dir
    ```
    此命令需要稍等片刻以完成操作。

---

## 编辑Samba主配置文件

![](img/08b7de6fb103db26af0d509c35ca93ab_25.png)

Samba服务的主要配置文件是 `/etc/samba/smb.conf`，我们需要在其中定义共享目录的参数。

上一节我们配置了SELinux，本节中我们来编辑Samba的核心配置文件。

![](img/08b7de6fb103db26af0d509c35ca93ab_27.png)

以下是需要修改的配置项：

![](img/08b7de6fb103db26af0d509c35ca93ab_29.png)

1.  找到 `workgroup` 参数（通常在文件靠前部分，如第89行），将其值修改为 `STAR`。
2.  找到 `hosts allow` 参数（如第95行），将其修改为允许访问的网段。根据题目要求，`example.com` 域的网段是 `172.25.0.0/24`，同时本机 `127.0.0.1` 也需要能够访问。因此配置应为：
    ```
    hosts allow = 127.0.0.1 172.25.0.
    ```
    注意需要去掉该行前的注释符号 `;`。
3.  在文件末尾（如第321行附近）添加共享定义。共享名为 `common`，路径为我们之前创建的目录。
    ```
    [common]
        path = /srv/group_dir
        browseable = yes
    ```
    其中，`browseable = yes` 表示该共享在网络上可以被浏览到。
4.  保存并退出配置文件。

---

![](img/08b7de6fb103db26af0d509c35ca93ab_31.png)

## 启动服务与添加用户

![](img/08b7de6fb103db26af0d509c35ca93ab_33.png)

配置文件修改完成后，需要启动Samba服务，并为需要访问共享的用户设置Samba密码。

![](img/08b7de6fb103db26af0d509c35ca93ab_35.png)

上一节我们完成了共享配置，本节中我们来启动服务并管理用户访问权限。

以下是启动服务和添加用户的步骤：

![](img/08b7de6fb103db26af0d509c35ca93ab_37.png)

1.  将Samba服务设置为开机自启，并立即启动服务。
    ```bash
    systemctl enable smb
    systemctl start smb
    ```
2.  根据题目要求，用户 `barney` 必须能够读取共享内容。我们需要使用 `smbpasswd` 命令为系统用户 `barney` 设置一个Samba专用密码。
    ```bash
    smbpasswd -a barney
    ```
    输入并确认密码（例如 `flectrag`）。

![](img/08b7de6fb103db26af0d509c35ca93ab_39.png)

---

## 设置文件系统权限

![](img/08b7de6fb103db26af0d509c35ca93ab_41.png)

除了Samba自身的配置，还需要在操作系统层面为共享目录设置正确的文件和目录权限。

![](img/08b7de6fb103db26af0d509c35ca93ab_43.png)

上一节我们确保了用户能通过Samba认证，本节中我们还需要设置文件系统的访问权限。

对于目录，`rwx` 权限中的 `x` 代表“可执行”，但对于目录而言，它表示用户可以“进入”该目录，这是访问目录内文件所必需的。

![](img/08b7de6fb103db26af0d509c35ca93ab_45.png)

以下是设置权限的命令：
```bash
chmod 2770 /srv/group_dir
```
此命令中：
*   `2` 代表设置SGID位，在此目录下创建的文件将自动继承 `star` 组的权限。
*   `770` 表示目录所有者（root）和所属组（star）拥有读、写、执行权限，其他用户无任何权限。

---

![](img/08b7de6fb103db26af0d509c35ca93ab_47.png)

## 配置防火墙

![](img/08b7de6fb103db26af0d509c35ca93ab_48.png)

最后，必须确保防火墙允许Samba服务的流量通过，否则客户端将无法连接到服务器。

上一节我们完成了服务端的所有本地配置，本节中我们进行最后一步：配置防火墙。

![](img/08b7de6fb103db26af0d509c35ca93ab_50.png)

以下是配置防火墙的命令：
```bash
firewall-cmd --permanent --add-service=samba
firewall-cmd --reload
```
第一条命令将Samba服务永久添加到防火墙允许规则中。第二条命令重新加载防火墙配置使规则生效。

![](img/08b7de6fb103db26af0d509c35ca93ab_52.png)

---

![](img/08b7de6fb103db26af0d509c35ca93ab_53.png)

## 总结

![](img/08b7de6fb103db26af0d509c35ca93ab_55.png)

本节课中我们一起学习了在RHEL 7上完整配置SMB共享目录的流程。我们回顾一下关键步骤：安装软件包、创建目录和组、配置SELinux安全上下文、编辑 `smb.conf` 主配置文件、启动Samba服务、添加Samba用户、设置文件系统权限，最后配置防火墙规则。完成这些步骤后，服务器端的SMB共享服务就配置完毕了，客户端即可使用指定用户进行访问。