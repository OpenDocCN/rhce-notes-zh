# RHCE集训-1：P10：RHCE考试环境与基础配置 🚀

![](img/2a550717992a4e23b6963fa890a4f05f_1.png)

![](img/2a550717992a4e23b6963fa890a4f05f_3.png)

![](img/2a550717992a4e23b6963fa890a4f05f_5.png)

在本节课中，我们将学习如何启动和配置RHCE考试环境，并完成一系列基础的系统配置任务。这些任务是后续所有复杂配置的基础，确保环境正确是成功的第一步。

![](img/2a550717992a4e23b6963fa890a4f05f_7.png)

## 虚拟机环境启动与概览

首先，我们需要启动考试环境中的虚拟机。考试环境包含一个主虚拟机，其中运行着三台KVM虚拟机：`classroom`、`server`和`desktop`。

![](img/2a550717992a4e23b6963fa890a4f05f_9.png)

![](img/2a550717992a4e23b6963fa890a4f05f_11.png)

以下是启动步骤：
1.  打开名为 `view rc excel` 的文档，这是与真实考试高度相似的考题。
2.  打开 `manager vms` 虚拟机管理器。
3.  检查虚拟机状态。`classroom` 默认已开启，`server` 和 `desktop` 处于关闭状态。
4.  **切勿点击重置（Reset）**，直接启动 `server` 和 `desktop` 虚拟机。

```bash
# 在 manager vms 中执行
virsh start server
virsh start desktop
```

![](img/2a550717992a4e23b6963fa890a4f05f_13.png)

![](img/2a550717992a4e23b6963fa890a4f05f_15.png)

启动后，使用 `view` 工具分别连接到 `server` 和 `desktop` 虚拟机，准备开始配置。

![](img/2a550717992a4e23b6963fa890a4f05f_17.png)

![](img/2a550717992a4e23b6963fa890a4f05f_19.png)

---

上一节我们启动了考试环境，本节中我们来看看配置前的准备工作。

![](img/2a550717992a4e23b6963fa890a4f05f_21.png)

## 配置YUM软件仓库

![](img/2a550717992a4e23b6963fa890a4f05f_23.png)

考试中所有软件安装都依赖于正确的YUM仓库配置。这是必须首先完成且不能出错的一步。需要在 `server` 和 `desktop` 两台机器上分别配置。

配置步骤如下：
1.  使用 `yum-config-manager` 命令添加指定的仓库地址。
2.  由于生成的仓库文件未禁用GPG检查，需手动添加 `gpgcheck=0`。
3.  清理缓存并测试仓库是否可用。

```bash
# 在 server 和 desktop 上分别执行
yum-config-manager --add-repo=http://content.example.com/rhel7/x86_64/dvd
echo "gpgcheck=0" >> /etc/yum.repos.d/content.example.com_rhel7_x86_64_dvd.repo
yum clean all
yum repolist
```

![](img/2a550717992a4e23b6963fa890a4f05f_25.png)

![](img/2a550717992a4e23b6963fa890a4f05f_27.png)

---

配置好软件源后，我们就可以开始进行系统层面的安全配置了。

## 配置SELinux

![](img/2a550717992a4e23b6963fa890a4f05f_29.png)

第一道考题要求将 `server` 和 `desktop` 的SELinux状态设置为 **enforcing**。

![](img/2a550717992a4e23b6963fa890a4f05f_31.png)

以下是配置方法：
1.  编辑SELinux配置文件 `/etc/selinux/config`。
2.  将 `SELINUX=` 参数的值从 `permissive` 修改为 `enforcing`。
3.  为了使更改立即生效（避免重启），可以使用 `setenforce 1` 命令。

```bash
# 修改配置文件
sed -i 's/SELINUX=permissive/SELINUX=enforcing/' /etc/selinux/config
# 立即生效
setenforce 1
```

**注意**：必须两台虚拟机都进行配置。修改后务必检查配置文件是否正确。

---

![](img/2a550717992a4e23b6963fa890a4f05f_33.png)

![](img/2a550717992a4e23b6963fa890a4f05f_35.png)

在加强了强制访问控制后，接下来我们需要配置网络访问控制。

## 配置防火墙规则

![](img/2a550717992a4e23b6963fa890a4f05f_37.png)

第二题要求配置防火墙，对SSH服务进行访问控制：允许 `example.com` 域访问，拒绝 `my133t.com` 域访问。

![](img/2a550717992a4e23b6963fa890a4f05f_39.png)

此配置分为三个步骤，且需要在 `server` 和 `desktop` 上分别执行：
1.  永久开放 `ssh` 服务。
2.  添加一条富规则（rich rule），拒绝特定网段的SSH连接。
3.  重新加载防火墙配置使其生效。

```bash
# 1. 开放SSH服务
firewall-cmd --permanent --add-service=ssh
# 2. 添加拒绝规则（网段：172.17.10.0/24）
firewall-cmd --permanent --add-rich-rule='rule family=ipv4 source address=172.17.10.0/24 service name=ssh reject'
# 3. 重载配置
firewall-cmd --reload
```

---

网络控制配置完成后，我们继续配置网络本身，即IPv6地址。

## 配置静态IPv6地址

![](img/2a550717992a4e23b6963fa890a4f05f_41.png)

第三题要求为 `server` 和 `desktop` 的 `eth0` 网卡配置指定的静态IPv6地址，且配置需永久有效。

![](img/2a550717992a4e23b6963fa890a4f05f_43.png)

使用 `nmcli` 命令进行配置，需同时设置三个关键属性：
1.  IPv6地址。
2.  地址获取方式为手动（manual）。
3.  连接为自动启动（autoconnect）。

![](img/2a550717992a4e23b6963fa890a4f05f_45.png)

```bash
# 在 server 上执行
nmcli connection modify "System eth0" ipv6.addresses "fd00:db8:f12a:ab1e::c0a8:1/64" ipv6.method manual connection.autoconnect yes
nmcli connection up "System eth0"

# 在 desktop 上执行 (地址不同)
nmcli connection modify "System eth0" ipv6.addresses "fd00:db8:f12a:ab1e::c0a8:2/64" ipv6.method manual connection.autoconnect yes
nmcli connection up "System eth0"
```

![](img/2a550717992a4e23b6963fa890a4f05f_47.png)

配置完成后，可使用 `ping6` 命令测试两台主机间的IPv6连通性。

---

基础网络配置好后，我们来提升网络的可靠性与性能。

## 配置链路聚合（Team）

![](img/2a550717992a4e23b6963fa890a4f05f_49.png)

![](img/2a550717992a4e23b6963fa890a4f05f_51.png)

第四题要求在 `server` 和 `desktop` 间使用 `eth1` 和 `eth2` 网卡创建链路聚合，模式为 `activebackup`，实现故障切换。

配置流程如下：
1.  创建Team接口，并指定运行模式。
2.  将两个物理网卡作为端口（port）加入Team。
3.  为Team接口配置IP地址并设置为开机自启。

```bash
# 在 server 上执行
# 1. 创建Team设备
nmcli connection add type team con-name team0 ifname team0 config '{"runner": {"name":"activebackup"}}'
# 2. 添加端口
nmcli connection add type team-slave con-name team0-port1 ifname eth1 master team0
nmcli connection add type team-slave con-name team0-port2 ifname eth2 master team0
# 3. 配置IP并启用
nmcli connection modify team0 ipv4.addresses 192.168.0.101/24 ipv4.method manual connection.autoconnect yes
nmcli connection up team0
```

![](img/2a550717992a4e23b6963fa890a4f05f_53.png)

**注意**：`desktop` 的配置步骤类似，但IP地址不同（`192.168.0.102`）。若配置错误，可使用 `nmcli connection delete <name>` 删除后重配。

---

![](img/2a550717992a4e23b6963fa890a4f05f_55.png)

网络配置暂告一段落，下面我们进行一些提高效率的系统定制。

## 创建自定义命令别名

第五题要求创建一个名为 `qstat` 的命令别名，功能是执行 `ps -ao pid,tt,user,fname,rs`，并对所有用户生效。

![](img/2a550717992a4e23b6963fa890a4f05f_57.png)

这需要将别名定义在系统的全局Shell配置文件中：
1.  编辑 `/etc/bashrc` 文件。
2.  在文件末尾添加别名定义。**注意**：命令必须用单引号括起。
3.  让配置在当前会话生效。

![](img/2a550717992a4e23b6963fa890a4f05f_59.png)

```bash
# 在 server 和 desktop 上执行
echo "alias qstat='ps -ao pid,tt,user,fname,rs'" >> /etc/bashrc
source /etc/bashrc
```

之后，在任何终端输入 `qstat` 即可执行定义好的命令。

---

![](img/2a550717992a4e23b6963fa890a4f05f_61.png)

![](img/2a550717992a4e23b6963fa890a4f05f_63.png)

本节课中我们一起学习了RHCE考试环境的启动、YUM仓库配置、SELinux与防火墙设置、IPv6地址配置、链路聚合以及自定义命令创建。这些都是RHCE考试中基础但至关重要的环节，确保这些配置准确无误，能为后续更复杂的服务配置打下坚实的基础。请务必在实验环境中反复练习，直至熟练。