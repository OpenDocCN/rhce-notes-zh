# RHCE8红帽认证课程：P4：SSH升级教程 🔧

在本节课中，我们将学习如何升级Linux系统中的SSH服务。SSH是远程连接服务器的核心协议，其安全性至关重要。当发现SSH服务存在安全漏洞时，必须及时升级以修补漏洞，防止未授权访问。本教程将指导你通过源码编译的方式，将SSH升级到指定版本。

![](img/9478aac429119988e5c04488a231dc32_1.png)

![](img/9478aac429119988e5c04488a231dc32_3.png)

## 概述与准备工作

上一节我们介绍了SSH服务的基础配置与管理。本节中，我们来看看如何安全地升级SSH服务。

![](img/9478aac429119988e5c04488a231dc32_5.png)

升级过程存在风险，如果操作失误可能导致无法远程连接服务器。因此，在开始前必须配置一个备用连接通道。

![](img/9478aac429119988e5c04488a231dc32_7.png)

以下是升级前的关键准备工作：
1.  **安装并配置Telnet服务**：作为SSH升级期间的备用远程连接方式。
2.  **备份现有SSH配置文件**：防止升级失败后无法恢复。

![](img/9478aac429119988e5c04488a231dc32_9.png)

首先，我们需要安装Telnet服务及其守护进程。

```bash
yum install -y telnet-server xinetd
```

![](img/9478aac429119988e5c04488a231dc32_11.png)

![](img/9478aac429119988e5c04488a231dc32_13.png)

接着，需要修改一个安全终端配置文件，允许通过Telnet登录。

![](img/9478aac429119988e5c04488a231dc32_15.png)

![](img/9478aac429119988e5c04488a231dc32_17.png)

![](img/9478aac429119988e5c04488a231dc32_19.png)

```bash
# 编辑 /etc/securetty 文件，在文件末尾添加以下行
echo “pts/0” >> /etc/securetty
echo “pts/1” >> /etc/securetty
echo “pts/2” >> /etc/securetty
echo “pts/3” >> /etc/securetty
```

![](img/9478aac429119988e5c04488a231dc32_21.png)

然后，启动相关服务。

![](img/9478aac429119988e5c04488a231dc32_23.png)

![](img/9478aac429119988e5c04488a231dc32_25.png)

![](img/9478aac429119988e5c04488a231dc32_27.png)

![](img/9478aac429119988e5c04488a231dc32_29.png)

```bash
systemctl start xinetd
systemctl start telnet.socket
```

![](img/9478aac429119988e5c04488a231dc32_31.png)

现在，你可以使用Telnet客户端（如PuTTY、Xshell）通过23端口连接到服务器进行验证。完成验证后，后续的升级操作建议在Telnet会话中进行，以防SSH服务中断导致连接丢失。

## 升级操作步骤

![](img/9478aac429119988e5c04488a231dc32_33.png)

在确保Telnet备用连接可用后，我们就可以开始正式的SSH升级流程了。

![](img/9478aac429119988e5c04488a231dc32_35.png)

![](img/9478aac429119988e5c04488a231dc32_37.png)

以下是详细的升级步骤：
1.  **安装编译依赖包**：升级SSH需要编译源码，因此需先安装必要的开发工具和库。
    ```bash
    yum install -y gcc gcc-c++ glibc glibc-devel openssl-devel pam-devel rpm-build
    ```
2.  **备份现有配置**：将当前的SSH配置文件移动到安全位置。
    ```bash
    mkdir /opt/sshback
    mv /etc/ssh/* /opt/sshback/
    ```
3.  **创建安装目录**：为新版SSH准备安装路径。
    ```bash
    mkdir /usr/local/sshd
    ```
4.  **解压源码包**：上传或下载新版本的OpenSSH源码包并解压。这里以 `openssh-8.3p1.tar.gz` 为例。
    ```bash
    tar -zxvf openssh-8.3p1.tar.gz -C /usr/local/src/
    cd /usr/local/src/openssh-8.3p1/
    ```
5.  **配置编译选项**：执行 `configure` 脚本，指定安装路径和功能模块。
    ```bash
    ./configure --prefix=/usr/local/sshd --sysconfdir=/etc/ssh --with-pam --with-md5-passwords --with-tcp-wrappers
    ```
6.  **编译与安装**：完成配置后，进行编译和安装。
    ```bash
    make && make install
    ```
7.  **复制必要文件**：将PAM配置文件和系统服务脚本复制到正确位置。
    ```bash
    cp contrib/redhat/sshd.pam /etc/pam.d/sshd
    cp contrib/redhat/sshd.init /etc/init.d/sshd
    ```
8.  **修改新配置文件**：编辑新生成的SSH配置文件 `/etc/ssh/sshd_config`，确保关键设置正确。
    *   找到 `PermitRootLogin` 行，确保其设置为 `yes`，允许root登录。
    *   找到 `PubkeyAuthentication` 行，确保其设置为 `yes`，启用公钥认证。
    *   找到 `UseDNS` 行，建议设置为 `no`，以加速连接。
9.  **设置开机自启**：将旧的SSH服务启动文件移走，并启用新的服务脚本。
    ```bash
    mv /usr/lib/systemd/system/sshd.service /opt/sshback/
    chkconfig --add sshd
    chkconfig sshd on
    ```
10. **重启SSH服务**：使用新的脚本启动服务，并检查端口监听情况。
    ```bash
    systemctl daemon-reload
    service sshd restart
    netstat -lntp | grep 22
    ```
11. **验证升级结果**：断开当前Telnet连接，使用SSH重新连接服务器，并检查版本。
    ```bash
    ssh -V
    ```
    如果显示为新版本（例如 `OpenSSH_8.3p1`），则表明升级成功。

## 收尾工作

升级验证成功后，为了系统安全，应立即关闭并禁用临时启用的Telnet服务。

```bash
systemctl stop telnet.socket
systemctl stop xinetd
systemctl disable telnet.socket
systemctl disable xinetd
```

## 总结

![](img/9478aac429119988e5c04488a231dc32_39.png)

![](img/9478aac429119988e5c04488a231dc32_41.png)

本节课中我们一起学习了SSH服务的完整升级流程。

![](img/9478aac429119988e5c04488a231dc32_43.png)

我们首先认识了在升级关键服务前配置备用连接（如Telnet）的重要性。然后，我们逐步完成了从安装依赖、备份配置、编译源码到配置新服务的所有操作。最后，我们验证了升级结果并清理了临时服务。

![](img/9478aac429119988e5c04488a231dc32_45.png)

整个流程的核心在于**谨慎**和**有备无患**。通过本次学习，你不仅掌握了升级SSH的技能，也理解了在Linux系统维护中制定回滚方案和应急计划的基本思路。请务必在测试环境中熟练此过程后，再在生产环境中操作。