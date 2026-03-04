# RHCE红帽认证全套入门教程：P18：3.04-autofs自动文件系统 🚀

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_0.png)

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_2.png)

在本节课中，我们将学习如何配置 **autofs（自动文件系统）**，以实现按需自动挂载远程NFS共享目录。这是RHCE考试中的一个重要考点，我们将通过模拟考试环境来掌握其配置方法。

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_4.png)

---

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_6.png)

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_8.png)

## 课程概述 📖

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_10.png)

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_12.png)

在Linux系统中，我们通常使用 `/etc/fstab` 文件来配置开机自动挂载本地或网络文件系统。然而，这种方式会使挂载的资源一直存在，不够灵活。**autofs** 提供了一种更智能的“按需挂载”机制：只有当用户访问特定目录时，系统才会自动挂载远程资源；在一段时间不使用后，系统会自动卸载，以节省资源并提升安全性。

本节课，我们将模拟考试场景：配置 autofs，使得一个来自LDAP服务器的远程用户（`ldapuser0`）在登录时，能自动将其位于NFS服务器上的家目录挂载到本地。

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_14.png)

---

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_16.png)

## 理解环境与需求 🔍

上一节我们介绍了文件系统的基本概念，本节中我们来看看具体的考试要求与环境。

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_18.png)

1.  **考试环境说明**：在考试中，会有一台服务器（例如 `server.lab.example.com`）提供LDAP用户认证和NFS共享。
    *   用户 `ldapuser0` 的账户信息由LDAP服务器管理。
    *   用户 `ldapuser0` 的家目录 `/home/ldapuser0` 通过NFS共享出来。
    *   你的考试机器（客户端）已经配置好LDAP客户端，可以使用 `ldapuser0` 账户登录，但登录后没有家目录。

2.  **我们的任务**：在客户端配置 autofs，实现当 `ldapuser0` 用户登录（或任何用户访问其家目录路径）时，自动将NFS服务器上的 `/home/ldapuser0` 挂载到客户端的 `/home/ldapuser0`。

3.  **核心概念**：
    *   **NFS（网络文件系统）**：允许在网络中共享目录和文件。
    *   **autofs**：自动挂载器服务，根据访问需求动态挂载文件系统。

---

## 第一步：检查NFS共享 📡

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_20.png)

在配置之前，我们需要确认NFS服务器共享了哪些目录。这需要使用 `showmount` 命令。

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_22.png)

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_24.png)

**注意**：最小化安装的系统中可能没有该命令，需要先安装 `nfs-utils` 软件包。

```bash
# 安装 nfs-utils 软件包
sudo yum install -y nfs-utils

# 查看NFS服务器（假设IP为172.25.250.10）的共享目录
showmount -e 172.25.250.10
```
执行成功后，你可能会看到类似输出，表明服务器共享了 `/home` 目录：
```
Export list for 172.25.250.10:
/home 172.25.250.0/24
```

---

## 第二步：安装与配置autofs ⚙️

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_26.png)

理解了需求并确认NFS共享可用后，现在开始配置 autofs。

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_28.png)

以下是配置 autofs 的核心步骤：

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_30.png)

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_32.png)

1.  **安装 autofs 软件包**
    ```bash
    sudo yum install -y autofs
    ```

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_34.png)

2.  **编辑主配置文件 `/etc/auto.master`**
    这个文件定义了“挂载点父目录”和对应的“映射文件”。
    ```bash
    sudo vim /etc/auto.master
    ```
    在文件末尾添加一行，意思是：监控 `/home` 目录，其具体的自动挂载规则由 `/etc/auto.home` 文件定义。
    ```
    /home /etc/auto.home
    ```

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_36.png)

3.  **创建并编辑映射文件 `/etc/auto.home`**
    这个文件定义了具体的触发挂载规则。
    ```bash
    sudo vim /etc/auto.home
    ```
    添加以下内容：
    ```
    ldapuser0 -rw,sync 172.25.250.10:/home/ldapuser0
    ```
    *   `ldapuser0`：这是“触发键”。当有人访问 `/home/ldapuser0` 时，规则生效。
    *   `-rw,sync`：挂载参数，表示以读写模式挂载，并使用同步I/O。
    *   `172.25.250.10:/home/ldapuser0`：这是NFS资源位置，格式为 `服务器地址:共享目录路径`。

---

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_38.png)

## 第三步：启动服务并验证 🧪

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_40.png)

配置完成后，需要启动 autofs 服务使其生效。

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_42.png)

1.  **启动并设置开机自启**
    ```bash
    sudo systemctl enable --now autofs
    ```

2.  **验证自动挂载**
    首先，查看 `/home` 目录，此时 `ldapuser0` 子目录可能还不存在。
    ```bash
    ls /home
    ```
    然后，尝试访问或列出 `/home/ldapuser0` 目录。这将触发 autofs 进行挂载。
    ```bash
    ls -l /home/ldapuser0
    ```
    **预期结果**：如果配置成功，你会看到类似 `permission denied` 的提示。这恰恰说明目录已经成功挂载，只是因为当前用户没有 `ldapuser0` 目录的权限而无法访问内容。你可以对比访问一个不存在的目录（如 `ls /home/ldapuser1`），后者会提示 `No such file or directory`。

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_44.png)

3.  **检查挂载点**
    使用 `mount` 或 `df -h` 命令，可以看到 `/home/ldapuser0` 已经作为一个NFS文件系统挂载上了。
    ```bash
    df -h | grep ldapuser0
    ```

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_46.png)

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_48.png)

4.  **理解自动卸载**
    autofs 默认在挂载点空闲5分钟后自动卸载。你可以等待几分钟后再次执行 `ls /home` 和 `df -h`，会发现 `ldapuser0` 目录消失或卸载。再次访问 `/home/ldapuser0` 又会触发挂载。

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_50.png)

---

## 手动挂载对比（可选了解） 🔗

为了帮助你理解 autofs 的便利性，这里简单对比一下手动挂载NFS的方式：
```bash
# 手动挂载NFS共享到本地目录
sudo mount -t nfs 172.25.250.10:/home/ldapuser0 /mnt

# 查看挂载
df -h | grep mnt

# 卸载
sudo umount /mnt
```
手动挂载需要管理员权限，且一旦挂载，除非手动卸载或重启，否则会一直占用资源。

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_52.png)

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_54.png)

---

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_56.png)

## 总结 🎯

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_58.png)

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_60.png)

本节课中我们一起学习了 **autofs 自动文件系统** 的配置与管理。

*   **核心原理**：autofs 像一个智能管家，监控指定目录，仅在需要时自动挂载远程资源，闲置一段时间后自动卸载。
*   **配置流程**：
    1.  安装 `autofs` 和 `nfs-utils` 软件包。
    2.  在主配置文件 `/etc/auto.master` 中指定监控目录和映射文件。
    3.  在映射文件（如 `/etc/auto.home`）中定义具体的“触发键”和对应的NFS资源路径。
    4.  启动 `autofs` 服务。
*   **验证方法**：通过访问触发目录来验证自动挂载是否成功，通常通过观察目录出现及权限错误来确认。

![](img/ffa0d349b440f5c8cf6f6fecbbadf0a1_62.png)

通过掌握 autofs，你不仅能够应对RHCE考试中的相关题目，还能在实际工作中更优雅地管理网络存储资源，提升系统的安全性与灵活性。