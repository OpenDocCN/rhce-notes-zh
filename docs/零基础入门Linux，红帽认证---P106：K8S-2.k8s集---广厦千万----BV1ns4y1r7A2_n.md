# Kubernetes集群部署教程：P106：K8S集群环境部署

## 概述
在本节课中，我们将学习如何部署一个Kubernetes集群环境。课程内容涵盖从系统内核升级、网络配置到容器运行时安装等一系列前期准备工作。我们将使用Ansible工具进行批量操作，以提高部署效率。

---

## Kubernetes的历史背景与现状

在容器编排领域，Kubernetes目前占据主导地位。在Docker诞生初期，除了Google公司的Kubernetes，Apache等许多公司也研发了自己的容器编排工具。各大公司都意识到容器技术将成为未来的主流，因此竞相研发相关工具。

然而，Kubernetes凭借其强大的技术背景最终脱颖而出。Google公司基于其内部成熟的Borg系统项目进行开发，该项目自2004年起已全面应用于生产环境，技术领先行业近十年。相比之下，其他公司大多从2014年才开始向容器化方向发展。因此，包括Docker自家的Swarm在内的其他工具，都未能在激烈的竞争中存活下来。

---

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_1.png)

## 系统内核升级与配置

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_3.png)

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_5.png)

上一节我们了解了Kubernetes的背景，本节中我们来看看具体的环境部署步骤。首先需要升级系统内核并调整启动顺序。

执行以下命令，使新版本内核在重启后优先启动。此命令需要在所有集群节点上执行。

```bash
grub2-set-default 0
```

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_7.png)

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_9.png)

执行完成后，可以验证内核版本是否已更新为较高版本（例如6.x）。

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_11.png)

---

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_13.png)

## 配置IPv4流量转发

接下来，我们需要配置系统以转发IPv4流量，并允许iptables处理桥接流量。这是Kubernetes官方的强制要求，因为集群内部的流量转发最终依赖于底层的iptables来实现。

以下是需要执行的命令，它们会将配置写入指定文件。

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_15.png)

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_17.png)

```bash
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
```

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_19.png)

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_21.png)

为了方便操作，我们可以在主控节点（如m401）上执行此命令生成配置文件，然后使用Ansible将其分发到其他所有节点。

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_22.png)

```bash
ansible k8s -m copy -a "src=/etc/sysctl.d/k8s.conf dest=/etc/sysctl.d/k8s.conf"
```

文件分发完成后，需要加载`br_netfilter`内核模块，并让配置生效。

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_24.png)

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_26.png)

```bash
# 加载模块
modprobe br_netfilter
# 检查模块是否加载
lsmod | grep br_netfilter
# 使sysctl配置生效
sysctl --system
```

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_28.png)

---

## 配置IPVS功能

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_30.png)

IPVS是Kubernetes中Service四层代理将使用的一种流量转发模式。它提供丰富的负载均衡算法和健康检查功能。要启用IPVS模式，需要手动加载相关内核模块。

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_32.png)

首先，在所有节点上安装`ipvsadm`软件包。

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_34.png)

```bash
yum install -y ipvsadm
```

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_36.png)

然后，创建一个脚本文件来加载所需的IPVS模块。

```bash
cat > /etc/sysconfig/modules/ipvs.modules << EOF
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack
EOF
```

同样，使用Ansible将此文件分发到所有节点，并执行脚本以加载模块。

```bash
ansible k8s -m copy -a "src=/etc/sysconfig/modules/ipvs.modules dest=/etc/sysconfig/modules/ipvs.modules"
ansible k8s -m shell -a "chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules && lsmod | grep ip_vs"
```

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_38.png)

执行后，若能查看到`ip_vs`等相关模块，说明配置成功。

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_40.png)

---

## 永久关闭Swap分区

Kubernetes强制要求禁用Swap分区，否则会影响集群调度性能。我们需要编辑`/etc/fstab`文件，注释掉Swap相关的挂载行。

由于每台机器的磁盘UUID可能不同，不能直接拷贝修改后的文件。我们可以使用`sed`命令配合Ansible的`shell`模块来批量处理。通过查找包含`swap`的行并在行首添加`#`注释符来实现。

```bash
ansible k8s -m shell -a "sed -i '/swap/s/^/#/' /etc/fstab"
```

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_42.png)

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_44.png)

执行后，可以检查Swap是否已被禁用。

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_46.png)

```bash
ansible k8s -m shell -a "swapon -s"
```

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_48.png)

如果输出为空或全部为0，则表示Swap已成功关闭。

---

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_50.png)

## 重启系统并验证

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_52.png)

由于我们升级了内核，需要重启所有节点以使新内核生效。

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_54.png)

```bash
ansible k8s -m shell -a "reboot"
```

> **注意**：使用Ansible批量重启时，主控节点会首先重启，可能导致后续命令无法下发。重启后需要重新连接节点。

所有节点重启后，验证内核版本是否已更新，并再次确认Swap状态。

```bash
ansible k8s -m shell -a "uname -r && swapon -s"
```

至此，前期的系统环境准备工作全部完成。

---

## 容器运行时：Docker与CRI

环境准备完成后，我们需要安装容器运行时。Kubernetes通过CRI接口与容器运行时交互。在1.24版本之前，Kubernetes代码中内置了一个名为`dockershim`的适配器来对接不支持CRI的Docker。

然而，随着Kubernetes成为行业标准，维护额外的适配器代码增加了成本。因此，从1.24版本开始，Kubernetes移除了`dockershim`。这意味着，如果要在新版本中使用Docker，需要手动安装由Docker官方维护的`cri-dockerd`适配器。

我们的教程基于1.23版本，仍然可以直接使用Docker。但了解这一历史背景有助于理解为什么Kubernetes官方文档中不再首要推荐Docker。

---

## 安装Docker运行时

我们将使用离线安装包在所有节点上快速部署Docker。

首先，将准备好的Docker离线压缩包上传到各个节点（例如`/root`目录下），然后进行解压和安装。

```bash
# 解压离线包
tar -xzf /root/docker-20.10.tgz -C /root/
# 进入解压目录并安装所有rpm包
cd /root/docker && yum install -y ./*.rpm
```

使用Ansible批量执行上述安装命令。

```bash
ansible k8s -m shell -a "tar -xzf /root/docker-20.10.tgz -C /root/ && cd /root/docker && yum install -y ./*.rpm"
```

安装完成后，验证Docker是否安装成功。

```bash
ansible k8s -m shell -a "rpm -qa | grep docker"
```

最后，需要配置Docker使用`systemd`作为Cgroup驱动，这与Kubernetes的要求保持一致。编辑Docker的配置文件`/etc/docker/daemon.json`（如果不存在则创建）。

```json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
```

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_56.png)

然后重启Docker服务使配置生效。

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_58.png)

```bash
systemctl daemon-reload
systemctl restart docker
systemctl enable docker
```

---

## 总结

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_60.png)

本节课中，我们一起学习了部署Kubernetes集群的前期环境准备工作。主要内容包括：
1.  升级系统内核并配置启动项。
2.  配置网络参数，启用IPv4转发和桥接过滤。
3.  配置IPVS模块，为后续的Service代理做准备。
4.  永久关闭Swap分区以满足Kubernetes的运行要求。
5.  理解容器运行时与CRI接口的关系，以及Docker在K8S中的历史地位变化。
6.  在所有节点上安装并配置Docker容器运行时。

![](img/efd32f23d55f3e5491fbb49ebcdd86b5_62.png)

通过使用Ansible进行批量操作，我们大大提高了在多台机器上部署环境的效率。下一节课，我们将在此基础上继续安装Kubernetes的核心组件。