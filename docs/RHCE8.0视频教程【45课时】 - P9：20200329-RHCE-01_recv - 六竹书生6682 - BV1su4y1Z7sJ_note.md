# RHCE8.0视频教程：P9：Yum服务器与客户端配置

![](img/0cd2b60fa3d6624f05a259395122508d_0.png)

在本节课中，我们将学习如何配置Yum服务器与客户端，实现通过网络共享软件包。主要内容包括复习本地Yum源搭建、配置FTP服务器共享软件目录，以及配置客户端通过FTP协议访问远程Yum源。

## 概述

本节课将完成以下三个主要任务：
1.  复习并搭建本地Yum源。
2.  配置FTP服务器，将软件目录共享出去。
3.  配置客户端，使其能够通过FTP协议访问服务器端的Yum源。

---

## 服务器端配置

上一节我们介绍了本节课的目标，本节中我们来看看服务器端的具体配置步骤。服务器端需要安装FTP服务，并共享包含软件包的目录。

![](img/0cd2b60fa3d6624f05a259395122508d_2.png)

### 1. 检查网络连通性

在开始配置前，必须确保客户端与服务器之间网络通畅。

使用 `ping` 命令测试连通性：
```bash
ping 192.168.127.135
```
如果网络不通，后续步骤无法进行。

### 2. 搭建本地Yum源（复习）

在配置FTP服务前，需要先确保服务器本地有可用的软件包源。我们将复习如何搭建本地Yum源。

以下是搭建本地Yum源的步骤：

1.  **进入Yum源配置目录**：
    ```bash
    cd /etc/yum.repos.d/
    ```

2.  **创建本地Yum源配置文件**：
    创建一个以 `.repo` 结尾的文件，例如 `aa.repo`。
    ```bash
    vi aa.repo
    ```
    在文件中写入以下四行核心配置：
    ```ini
    [LocalRepo]          # 仓库标签
    name=Local Repository # 仓库名称
    baseurl=file:///tkedu135 # 本地仓库路径，使用file协议
    gpgcheck=0           # 关闭GPG校验
    ```

3.  **创建仓库目录并挂载光盘**：
    首先创建配置文件里指定的目录，然后将系统安装光盘挂载到该目录下。
    ```bash
    mkdir /tkedu135
    mount /dev/sr0 /tkedu135
    ```
    **注意**：在RHEL8中，光盘内容分为 `BaseOS` 和 `AppStream` 两个目录。常规软件包位于 `AppStream` 目录中。因此，`baseurl` 路径需要精确到子目录，例如：
    ```ini
    baseurl=file:///tkedu135/AppStream
    ```

4.  **验证Yum源**：
    使用以下命令清理缓存并列出仓库，如果显示软件包数量而非错误信息，则配置成功。
    ```bash
    yum clean all
    yum repolist
    ```

### 3. 安装与配置FTP服务

本地源搭建好后，需要安装FTP服务并将其共享出去。

以下是安装与配置FTP服务的步骤：

1.  **查询并安装FTP软件**：
    首先检查 `vsftpd` 软件是否已安装。
    ```bash
    yum list | grep ftp
    ```
    如果未安装（状态为 `available`），则进行安装。
    ```bash
    yum install vsftpd -y
    ```

2.  **准备FTP共享目录**：
    FTP服务的默认根目录是 `/var/ftp`。我们需要在此目录下创建子目录用于共享。
    ```bash
    mkdir /var/ftp/share_soft
    ```
    将包含软件包的光盘目录挂载到刚创建的共享目录下。
    ```bash
    mount /dev/sr0 /var/ftp/share_soft
    ```
    此时，光盘内容在 `/tkedu135` 和 `/var/ftp/share_soft` 下均可访问。

3.  **启动FTP服务并设置开机自启**：
    启动 `vsftpd` 服务，并设置为系统启动时自动运行。
    ```bash
    systemctl restart vsftpd
    systemctl enable vsftpd
    ```
    使用以下命令确认服务已正常运行：
    ```bash
    systemctl status vsftpd
    ```

### 4. 临时关闭安全设置（为实验环境）

为保证实验顺利进行，需要临时关闭防火墙和SELinux。

以下是临时关闭安全设置的命令：
```bash
iptables -F          # 清空防火墙规则（临时）
setenforce 0         # 将SELinux设置为宽容模式（临时）
```
**注意**：这只是临时生效，重启后会恢复。永久关闭的方法将在后续课程中讲解。

---

## 客户端配置

服务器端配置完成后，本节我们来看看客户端的配置。客户端的任务相对简单，只需配置一个指向服务器FTP共享目录的Yum源即可。

### 1. 创建远程Yum源配置文件

![](img/0cd2b60fa3d6624f05a259395122508d_4.png)

在客户端机器上，进入Yum配置目录并创建新的配置文件。

以下是创建远程Yum源配置文件的步骤：

![](img/0cd2b60fa3d6624f05a259395122508d_6.png)

1.  **进入配置目录并创建文件**：
    ```bash
    cd /etc/yum.repos.d/
    vi ftp.repo
    ```

![](img/0cd2b60fa3d6624f05a259395122508d_8.png)

![](img/0cd2b60fa3d6624f05a259395122508d_10.png)

2.  **编写配置文件内容**：
    在 `ftp.repo` 文件中写入以下配置。关键点在于 `baseurl` 使用 `ftp://` 协议，并指向服务器IP和共享目录的 `AppStream` 子目录。
    ```ini
    [FTP_Repo]
    name=FTP Repository
    baseurl=ftp://192.168.127.135/share_soft/AppStream
    gpgcheck=0
    ```
    **解释**：当客户端通过FTP访问服务器 `192.168.127.135` 时，会直接进入FTP的根目录 `/var/ftp`。因此，路径 `share_soft/AppStream` 对应的是服务器上的 `/var/ftp/share_soft/AppStream`。

![](img/0cd2b60fa3d6624f05a259395122508d_12.png)

![](img/0cd2b60fa3d6624f05a259395122508d_14.png)

![](img/0cd2b60fa3d6624f05a259395122508d_15.png)

### 2. 验证客户端Yum源

![](img/0cd2b60fa3d6624f05a259395122508d_17.png)

![](img/0cd2b60fa3d6624f05a259395122508d_19.png)

配置文件保存后，需要验证客户端是否能正确访问服务器端的Yum源。

![](img/0cd2b60fa3d6624f05a259395122508d_21.png)

使用以下命令清理缓存并列出仓库信息：
```bash
yum clean all
yum repolist
```
如果配置正确，该命令会列出从服务器同步过来的软件仓库信息，并显示可用的软件包数量。

---

## 故障排查与注意事项

在配置过程中可能会遇到问题，以下是一些常见的注意事项和排查点：

![](img/0cd2b60fa3d6624f05a259395122508d_23.png)

![](img/0cd2b60fa3d6624f05a259395122508d_25.png)

![](img/0cd2b60fa3d6624f05a259395122508d_26.png)

![](img/0cd2b60fa3d6624f05a259395122508d_27.png)

*   **路径准确性**：无论是本地源还是FTP源，`baseurl` 中的路径必须精确到包含 `repodata` 目录的层级（如 `AppStream`）。
*   **服务状态**：确保服务器端的 `vsftpd` 服务处于运行 (`running`) 状态。
*   **网络与防火墙**：确保客户端能 `ping` 通服务器，并确认实验环境中防火墙和SELinux已临时关闭。
*   **配置文件格式**：确保 `.repo` 文件内容格式正确，无多余空格或拼写错误。
*   **通配符使用**：在Yum查询中使用通配符 `*` 时，若当前目录存在匹配的文件名，可能会被优先解析。更安全的做法是在通配符外加引号，例如 `yum list "*ftp*"`。

---

![](img/0cd2b60fa3d6624f05a259395122508d_29.png)

## 总结

![](img/0cd2b60fa3d6624f05a259395122508d_31.png)

本节课中我们一起学习了如何配置基于FTP的Yum服务器与客户端。
1.  我们首先复习了**本地Yum源**的搭建，强调了RHEL8中 `BaseOS` 和 `AppStream` 目录的区别。
2.  接着，我们在服务器端安装了 **`vsftpd`** 服务，创建共享目录并挂载光盘，最后启动服务以实现目录共享。
3.  然后，我们在客户端创建了指向服务器FTP地址的 **Yum源配置文件**，完成了客户端的配置。
4.  最后，我们讨论了配置过程中需要注意的**路径、服务和安全性**等关键点，以及简单的故障排查思路。

![](img/0cd2b60fa3d6624f05a259395122508d_33.png)

![](img/0cd2b60fa3d6624f05a259395122508d_35.png)

![](img/0cd2b60fa3d6624f05a259395122508d_37.png)

通过这套流程，可以实现软件包在局域网内的集中管理和分发。