# Linux服务管理：2-1-1：学习Linux服务前期环境准备、搭建CentOS 7环境 🛠️

在本节课中，我们将学习如何为后续的Linux服务学习搭建一个标准、干净的CentOS 7实验环境。这是第二阶段学习的起点，一个良好的基础环境至关重要。

![](img/d72d11b90643fc6280a991428751b368_1.png)

上一节我们完成了第一阶段的基础学习，本节中我们来看看如何为服务学习准备环境。

![](img/d72d11b90643fc6280a991428751b368_3.png)

![](img/d72d11b90643fc6280a991428751b368_5.png)

## 环境准备与配置

为了确保后续的实验顺利进行，我们需要对系统进行一些基础配置，主要是关闭可能影响实验的网络策略和安全组件。

以下是需要完成的配置步骤：

![](img/d72d11b90643fc6280a991428751b368_7.png)

1.  **清空并关闭防火墙**
    防火墙（firewalld）可能会阻止服务端口，影响实验。我们首先清空所有规则，然后停止并禁用该服务。
    ```bash
    iptables -F
    systemctl stop firewalld
    systemctl disable firewalld
    systemctl status firewalld
    ```

![](img/d72d11b90643fc6280a991428751b368_9.png)

![](img/d72d11b90643fc6280a991428751b368_11.png)

2.  **关闭SELinux**
    SELinux是一个安全增强系统，在初学阶段可能会带来复杂的权限问题。我们先将其临时关闭，并修改配置文件永久关闭。
    ```bash
    getenforce
    setenforce 0
    ```
    永久关闭需要编辑配置文件 `/etc/selinux/config`，将 `SELINUX=` 的值改为 `disabled`，然后重启系统生效。

![](img/d72d11b90643fc6280a991428751b368_13.png)

## 网络配置

一个稳定的静态IP地址是服务器的基础。接下来，我们配置网络接口。

![](img/d72d11b90643fc6280a991428751b368_15.png)

![](img/d72d11b90643fc6280a991428751b368_17.png)

以下是配置静态IP的步骤：

![](img/d72d11b90643fc6280a991428751b368_19.png)

1.  编辑网卡配置文件 `/etc/sysconfig/network-scripts/ifcfg-ens33`。
2.  将 `BOOTPROTO` 改为 `static`。
3.  设置 `ONBOOT` 为 `yes`。
4.  指定 `IPADDR`、`NETMASK`、`GATEWAY` 等参数。
5.  重启网络服务使配置生效。
    ```bash
    systemctl restart network
    ifconfig ens33
    ```
6.  配置DNS，可以编辑 `/etc/resolv.conf` 文件，添加 `nameserver` 记录。

![](img/d72d11b90643fc6280a991428751b368_21.png)

![](img/d72d11b90643fc6280a991428751b368_23.png)

## 系统基础设置

![](img/d72d11b90643fc6280a991428751b368_24.png)

为了方便管理和实验，我们还需要进行一些系统层面的设置。

以下是需要调整的系统设置：

1.  **关闭NetworkManager服务**
    为了避免网络管理冲突，我们关闭NetworkManager服务。
    ```bash
    systemctl stop NetworkManager
    systemctl disable NetworkManager
    ```

![](img/d72d11b90643fc6280a991428751b368_26.png)

![](img/d72d11b90643fc6280a991428751b368_27.png)

2.  **配置本地主机名解析**
    编辑 `/etc/hosts` 文件，添加IP地址和主机名的映射关系，方便后续通过主机名访问。

3.  **修改主机名**
    使用 `hostnamectl` 命令修改主机名，并使其永久生效。
    ```bash
    hostnamectl set-hostname server63
    ```

![](img/d72d11b90643fc6280a991428751b368_29.png)

## 配置软件仓库（Yum源）

为了能够顺利安装软件，我们需要配置Yum软件仓库。我们将配置本地光盘源和网络源。

以下是配置Yum源的步骤：

![](img/d72d11b90643fc6280a991428751b368_31.png)

![](img/d72d11b90643fc6280a991428751b368_32.png)

![](img/d72d11b90643fc6280a991428751b368_34.png)

![](img/d72d11b90643fc6280a991428751b368_36.png)

![](img/d72d11b90643fc6280a991428751b368_38.png)

1.  **挂载光盘镜像**
    将CentOS 7的安装光盘镜像挂载到系统目录（如 `/mnt`），并设置开机自动挂载（编辑 `/etc/fstab` 文件）。

![](img/d72d11b90643fc6280a991428751b368_40.png)

![](img/d72d11b90643fc6280a991428751b368_42.png)

![](img/d72d11b90643fc6280a991428751b368_44.png)

2.  **配置本地Yum源**
    在 `/etc/yum.repos.d/` 目录下创建本地源的配置文件（如 `local.repo`），指向光盘挂载目录。
    ```ini
    [local]
    name=Local Repository
    baseurl=file:///mnt
    enabled=1
    gpgcheck=0
    ```

![](img/d72d11b90643fc6280a991428751b368_46.png)

3.  **配置网络Yum源（如阿里云源）**
    使用 `wget` 工具下载阿里云的Yum源配置文件，放入 `/etc/yum.repos.d/` 目录。
    ```bash
    wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
    ```

![](img/d72d11b90643fc6280a991428751b368_48.png)

![](img/d72d11b90643fc6280a991428751b368_50.png)

4.  **清理并重建Yum缓存**
    执行以下命令，让系统识别新的软件源。
    ```bash
    yum clean all
    yum makecache
    ```

![](img/d72d11b90643fc6280a991428751b368_52.png)

![](img/d72d11b90643fc6280a991428751b368_54.png)

5.  **安装EPEL扩展源**
    EPEL源提供了大量额外的软件包。可以直接通过Yum安装。
    ```bash
    yum install -y epel-release
    ```

![](img/d72d11b90643fc6280a991428751b368_56.png)

![](img/d72d11b90643fc6280a991428751b368_58.png)

![](img/d72d11b90643fc6280a991428751b368_60.png)

![](img/d72d11b90643fc6280a991428751b368_62.png)

## 设置运行级别与创建快照

![](img/d72d11b90643fc6280a991428751b368_64.png)

为了方便进行纯命令行实验，我们将系统默认运行级别设置为3（多用户文本模式）。

![](img/d72d11b90643fc6280a991428751b368_66.png)

![](img/d72d11b90643fc6280a991428751b368_68.png)

![](img/d72d11b90643fc6280a991428751b368_70.png)

![](img/d72d11b90643fc6280a991428751b368_72.png)

运行以下命令设置默认运行级别为3：
```bash
systemctl set-default multi-user.target
```
设置完成后，需要重启系统 (`reboot`) 才能生效。

![](img/d72d11b90643fc6280a991428751b368_74.png)

![](img/d72d11b90643fc6280a991428751b368_76.png)

![](img/d72d11b90643fc6280a991428751b368_78.png)

最后，在虚拟机管理软件（如VMware）中为当前这个配置好的、干净的系统创建一个“快照”。这样，在后续实验出错或环境混乱时，可以快速恢复到此刻的初始状态，极大地提高学习效率。

![](img/d72d11b90643fc6280a991428751b368_80.png)

![](img/d72d11b90643fc6280a991428751b368_82.png)

![](img/d72d11b90643fc6280a991428751b368_84.png)

本节课中我们一起学习了如何搭建一个标准的CentOS 7实验环境。我们完成了防火墙和SELinux的关闭、静态IP的配置、系统基础设置、Yum软件仓库的搭建，并创建了系统快照。这个环境将作为我们后续所有Linux服务学习的起点，请务必熟练掌握。