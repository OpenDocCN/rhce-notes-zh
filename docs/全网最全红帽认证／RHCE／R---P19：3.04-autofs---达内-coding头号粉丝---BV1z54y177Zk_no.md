# 红帽认证零基础入门教程：P19：3.04-autofs自动文件系统 🗂️

![](img/ef0210941aacb2d85ac83e6db7e91c2c_0.png)

![](img/ef0210941aacb2d85ac83e6db7e91c2c_2.png)

![](img/ef0210941aacb2d85ac83e6db7e91c2c_4.png)

在本节课中，我们将学习如何配置 **autofs（自动文件系统）**，以实现按需自动挂载远程NFS共享目录。这是RHCE/RHCSA认证考试中的一个重要考点。

![](img/ef0210941aacb2d85ac83e6db7e91c2c_6.png)

![](img/ef0210941aacb2d85ac83e6db7e91c2c_8.png)

## 概述与背景

![](img/ef0210941aacb2d85ac83e6db7e91c2c_10.png)

![](img/ef0210941aacb2d85ac83e6db7e91c2c_12.png)

上一节我们介绍了文件系统的基本概念，本节中我们来看看如何实现网络文件系统的自动挂载。

![](img/ef0210941aacb2d85ac83e6db7e91c2c_14.png)

在考试环境中，通常会有一台服务器（例如 `server1`）通过LDAP提供用户账号，并通过NFS共享用户的主目录。考生的任务是在本地机器上配置autofs，使得当指定用户（例如 `ldapuser0`）登录时，其主目录能自动从远程服务器挂载到本地。这样，用户就有了可用的家目录。

![](img/ef0210941aacb2d85ac83e6db7e91c2c_16.png)

虽然我们的练习环境没有配置LDAP服务器和对应的用户账号，但配置autofs的核心步骤是相同的。我们配置的目标是：当访问本地 `/home/ldapuser0` 目录时，autofs能自动将服务器 `server1` 上的 `/home/ldapuser0` 目录挂载过来。

## 核心概念解析

![](img/ef0210941aacb2d85ac83e6db7e91c2c_18.png)

在开始配置前，需要理解两个核心概念：

1.  **NFS（网络文件系统）**：它允许将远程服务器上的目录共享到本地网络中的其他机器上，就像访问本地磁盘一样。访问NFS共享的基本命令是 `showmount -e <服务器地址>` 和 `mount`。
2.  **autofs（自动挂载）**：与手动或开机永久挂载不同，autofs实现了“按需挂载”。只有当用户或程序真正访问某个特定目录时，autofs才会自动挂载对应的远程资源；在一段时间（默认5分钟）无访问后，它会自动卸载，以节省资源并提升安全性。

## 配置autofs详细步骤

以下是配置autofs实现题目要求的具体操作流程。

![](img/ef0210941aacb2d85ac83e6db7e91c2c_20.png)

![](img/ef0210941aacb2d85ac83e6db7e91c2c_22.png)

### 步骤一：安装必要软件包

![](img/ef0210941aacb2d85ac83e6db7e91c2c_24.png)

首先，确保系统已安装访问NFS共享和autofs功能所需的软件包。

```bash
yum install -y nfs-utils autofs
```
*   `nfs-utils`：提供了访问和管理NFS共享所需的客户端工具。
*   `autofs`：提供自动挂载服务。

### 步骤二：检查NFS共享

![](img/ef0210941aacb2d85ac83e6db7e91c2c_26.png)

在配置挂载前，先确认远程服务器提供了哪些共享目录。

```bash
showmount -e server1
```
如果命令执行成功，通常会看到类似输出，表明 `server1` 共享了 `/home` 目录。

![](img/ef0210941aacb2d85ac83e6db7e91c2c_28.png)

![](img/ef0210941aacb2d85ac83e6db7e91c2c_30.png)

![](img/ef0210941aacb2d85ac83e6db7e91c2c_32.png)

### 步骤三：配置autofs主配置文件

![](img/ef0210941aacb2d85ac83e6db7e91c2c_34.png)

autofs的主要配置文件是 `/etc/auto.master`。我们需要在此文件中指定“监视目录”和对应的“映射文件”。

![](img/ef0210941aacb2d85ac83e6db7e91c2c_36.png)

1.  编辑主配置文件：
    ```bash
    vim /etc/auto.master
    ```
2.  在文件末尾添加以下一行：
    ```
    /home    /etc/auto.home
    ```
    *   **`/home`**：这是“监视目录”或“挂载点父目录”。autofs将监视这个目录。
    *   **`/etc/auto.home`**：这是“映射文件”的路径。该文件定义了在 `/home` 下具体子目录的挂载规则。文件名可以自定义。

### 步骤四：创建映射文件并配置挂载规则

![](img/ef0210941aacb2d85ac83e6db7e91c2c_38.png)

现在，创建在上一步中指定的映射文件，并写入具体的挂载规则。

![](img/ef0210941aacb2d85ac83e6db7e91c2c_40.png)

![](img/ef0210941aacb2d85ac83e6db7e91c2c_41.png)

1.  创建并编辑映射文件：
    ```bash
    vim /etc/auto.home
    ```
2.  在文件中添加以下内容：
    ```
    ldapuser0    -fstype=nfs,rw    server1:/home/ldapuser0
    ```
    *   **`ldapuser0`**：这是“关键字”或“触发点”。当用户或程序访问 `/home/ldapuser0` 时，将触发挂载。
    *   **`-fstype=nfs,rw`**：这是挂载参数。指定文件系统类型为 `nfs`，挂载为可读写 (`rw`)。
    *   **`server1:/home/ldapuser0`**：这是要挂载的远程资源，格式为 `服务器:共享目录路径`。

### 步骤五：启动并启用autofs服务

![](img/ef0210941aacb2d85ac83e6db7e91c2c_43.png)

![](img/ef0210941aacb2d85ac83e6db7e91c2c_45.png)

![](img/ef0210941aacb2d85ac83e6db7e91c2c_47.png)

配置完成后，需要启动autofs服务使其生效，并设置为开机自启。

![](img/ef0210941aacb2d85ac83e6db7e91c2c_49.png)

```bash
systemctl enable --now autofs
```

### 步骤六：验证配置

配置完成后，可以进行验证。

1.  首先，查看 `/home` 目录，此时 `ldapuser0` 子目录可能还不存在。
    ```bash
    ls /home
    ```
2.  然后，尝试访问触发目录 `/home/ldapuser0`：
    ```bash
    ls /home/ldapuser0
    ```
    *   **如果配置成功**：你会看到 `ldapuser0` 目录被自动创建，并且执行 `ls` 命令后返回 **`Permission denied`** 错误。这是因为该目录权限属于远程用户 `ldapuser0`，本地root用户无权访问，但这恰恰证明了目录已成功从NFS服务器挂载。
    *   **如果配置失败**：系统会提示 `No such file or directory`。

![](img/ef0210941aacb2d85ac83e6db7e91c2c_51.png)

![](img/ef0210941aacb2d85ac83e6db7e91c2c_53.png)

**关键验证点**：对比访问一个不存在的用户目录（如 `ldapuser1`），系统会直接提示目录不存在。而访问 `ldapuser0` 时提示权限拒绝，则说明autofs已成功触发挂载。

![](img/ef0210941aacb2d85ac83e6db7e91c2c_55.png)

![](img/ef0210941aacb2d85ac83e6db7e91c2c_57.png)

## 工作原理与流程总结

![](img/ef0210941aacb2d85ac83e6db7e91c2c_59.png)

本节课中我们一起学习了autofs的配置。我们可以将整个过程比喻为“设置一个智能管家”：
1.  你（管理员）通过 `/etc/auto.master` 告诉管家（autofs服务）：“请帮我盯着 `/home` 这个房间。”
2.  你又通过 `/etc/auto.home` 给了管家一张更详细的纸条：“如果有人要进 `/home` 房间里的 `ldapuser0` 这个小隔间，你就立刻从 `server1` 仓库把对应的家具（`/home/ldapuser0`）搬过来布置好，并记下这是NFS类型的家具。”
3.  当用户尝试进入 `ldapuser0` 隔间（访问目录）时，管家瞬间完成搬运和布置（挂载）。
4.  如果一段时间没人使用这个隔间，管家会自动把家具收走（卸载），保持整洁。

![](img/ef0210941aacb2d85ac83e6db7e91c2c_61.png)

这种机制既保证了用户需要时资源立即可用，又避免了不必要的长期资源占用，是Linux系统中管理网络存储的优雅方案。在RHCE考试中，成功配置并验证此功能即可得分。