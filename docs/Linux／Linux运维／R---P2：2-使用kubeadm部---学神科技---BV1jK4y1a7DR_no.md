# Kubernetes部署教程：P2：使用kubeadm部署Kubernetes集群

![](img/edc645529b03d89fa3e35919157c1c98_1.png)

在本节课中，我们将学习如何使用 `kubeadm` 工具快速部署一个Kubernetes集群。`kubeadm` 是目前主流的部署方式，它能极大地简化手动部署的复杂过程，类似于使用脚本快速部署OpenStack。

![](img/edc645529b03d89fa3e35919157c1c98_3.png)

![](img/edc645529b03d89fa3e35919157c1c98_5.png)

## 概述

我们将搭建一个包含一个Master节点和一个Node节点的简单集群。Master节点建议配置为8G内存，Node节点建议配置为4G内存。以下是部署的核心步骤概览。

## 环境准备

![](img/edc645529b03d89fa3e35919157c1c98_7.png)

上一节我们介绍了Kubernetes的基本概念，本节中我们来看看如何为集群部署准备基础环境。首先，我们需要在所有节点上执行一些通用的系统配置。

![](img/edc645529b03d89fa3e35919157c1c98_9.png)

以下是需要执行的基础配置步骤：

![](img/edc645529b03d89fa3e35919157c1c98_11.png)

![](img/edc645529b03d89fa3e35919157c1c98_13.png)

1.  **生成SSH密钥并配置免密登录**：为了方便后续的文件拷贝和批量操作，需要在Master节点生成密钥并分发到所有Node节点。
    ```bash
    ssh-keygen -t rsa
    ssh-copy-id root@192.168.1.63
    ssh-copy-id root@192.168.1.64
    ```

![](img/edc645529b03d89fa3e35919157c1c98_15.png)

2.  **关闭防火墙**：Kubernetes需要开放大量端口，建议直接关闭防火墙。
    ```bash
    systemctl stop firewalld
    systemctl disable firewalld
    ```

![](img/edc645529b03d89fa3e35919157c1c98_17.png)

3.  **关闭SELinux**：临时关闭并永久禁用SELinux。
    ```bash
    setenforce 0
    sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
    ```

![](img/edc645529b03d89fa3e35919157c1c98_18.png)

![](img/edc645529b03d89fa3e35919157c1c98_19.png)

4.  **关闭Swap分区**：Kubernetes官方建议关闭Swap以确保kubelet正常工作。
    ```bash
    swapoff -a
    # 永久关闭：注释掉 /etc/fstab 文件中所有包含 `swap` 的行
    ```

![](img/edc645529b03d89fa3e35919157c1c98_21.png)

5.  **加载内核模块并修改内核参数**：启用 `br_netfilter` 模块并调整相关内核参数，以确保iptables能正确查看桥接流量。
    ```bash
    modprobe br_netfilter
    echo "modprobe br_netfilter" >> /etc/rc.local
    cat > /etc/sysctl.d/k8s.conf << EOF
    net.bridge.bridge-nf-call-ip6tables = 1
    net.bridge.bridge-nf-call-iptables = 1
    net.ipv4.ip_forward = 1
    EOF
    sysctl -p /etc/sysctl.d/k8s.conf
    ```

![](img/edc645529b03d89fa3e35919157c1c98_23.png)

> **提示**：可以使用 `pssh` 工具批量在所有节点上执行相同的命令，提高效率。

## 安装容器运行时与Kubernetes组件

基础环境配置完成后，我们需要安装容器运行时（Docker）和Kubernetes的核心组件。

### 1. 配置Docker离线源并安装

Kubernetes集群的每个节点都需要安装Docker。我们使用离线安装包以提高部署速度。

以下是配置Docker离线源的步骤：

![](img/edc645529b03d89fa3e35919157c1c98_25.png)

![](img/edc645529b03d89fa3e35919157c1c98_27.png)

```bash
# 解压离线安装包
tar -zxvf k8s-docker.tar.gz -C /opt
# 配置yum仓库文件
cat > /etc/yum.repos.d/docker.repo << EOF
[docker]
name=Docker Repository
baseurl=file:///opt/k8s-docker
enabled=1
gpgcheck=0
EOF
```

![](img/edc645529b03d89fa3e35919157c1c98_29.png)

![](img/edc645529b03d89fa3e35919157c1c98_31.png)

配置完成后，在Master和Node节点上安装Docker及其依赖：

![](img/edc645529b03d89fa3e35919157c1c98_33.png)

![](img/edc645529b03d89fa3e35919157c1c98_35.png)

```bash
yum install -y yum-utils device-mapper-persistent-data lvm2
yum install -y docker-ce
systemctl start docker && systemctl enable docker
```

![](img/edc645529b03d89fa3e35919157c1c98_37.png)

### 2. 配置Docker镜像加速与驱动

为了提升镜像拉取速度，并确保与 `kubelet` 的 `cgroup` 驱动一致，需要修改Docker的守护进程配置。

![](img/edc645529b03d89fa3e35919157c1c98_39.png)

![](img/edc645529b03d89fa3e35919157c1c98_40.png)

![](img/edc645529b03d89fa3e35919157c1c98_42.png)

```bash
cat > /etc/docker/daemon.json << EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "registry-mirrors": ["https://your-aliyun-mirror.mirror.aliyuncs.com"]
}
EOF
systemctl daemon-reload
systemctl restart docker
```

![](img/edc645529b03d89fa3e35919157c1c98_44.png)

![](img/edc645529b03d89fa3e35919157c1c98_46.png)

> **核心概念解释**：`systemd` 是Linux的系统和服务管理器。`kubelet` 默认使用 `systemd` 作为 `cgroup` 驱动，因此需要将Docker的驱动也设置为 `systemd` 以确保兼容性。

![](img/edc645529b03d89fa3e35919157c1c98_48.png)

![](img/edc645529b03d89fa3e35919157c1c98_50.png)

### 3. 配置Kubernetes yum源并安装组件

![](img/edc645529b03d89fa3e35919157c1c98_52.png)

![](img/edc645529b03d89fa3e35919157c1c98_54.png)

接下来，配置Kubernetes的yum源，并安装 `kubeadm`、`kubelet` 和 `kubectl`。

```bash
cat > /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

![](img/edc645529b03d89fa3e35919157c1c98_56.png)

yum install -y kubelet kubeadm kubectl
systemctl enable kubelet
```

![](img/edc645529b03d89fa3e35919157c1c98_58.png)

> **组件说明**：
> *   **`kubelet`**：运行在所有节点上，负责启动Pod和容器。
> *   **`kubeadm`**：用于初始化集群、管理集群生命周期（如升级）的命令行工具。
> *   **`kubectl`**：Kubernetes集群管理命令行工具，用于与API Server通信，管理和查看各种资源。

## 初始化Master节点

![](img/edc645529b03d89fa3e35919157c1c98_60.png)

所有前置工作完成后，我们开始在Master节点上初始化Kubernetes控制平面。

![](img/edc645529b03d89fa3e35919157c1c98_62.png)

### 1. 导入Kubernetes所需镜像

![](img/edc645529b03d89fa3e35919157c1c98_64.png)

![](img/edc645529b03d89fa3e35919157c1c98_65.png)

![](img/edc645529b03d89fa3e35919157c1c98_67.png)

`kubeadm` 部署集群时，需要从网络拉取一系列核心组件镜像。为了避免网络问题，我们提前下载并导入离线镜像包。

![](img/edc645529b03d89fa3e35919157c1c98_69.png)

![](img/edc645529b03d89fa3e35919157c1c98_71.png)

![](img/edc645529b03d89fa3e35919157c1c98_73.png)

```bash
# 解压镜像包
tar -zxvf k8s-images.tar.gz -C /opt
# 编写脚本批量导入镜像
for image in `ls /opt/k8s-images/*.tar`; do docker load -i $image; done
```

![](img/edc645529b03d89fa3e35919157c1c98_75.png)

![](img/edc645529b03d89fa3e35919157c1c98_77.png)

### 2. 执行初始化命令

![](img/edc645529b03d89fa3e35919157c1c98_79.png)

![](img/edc645529b03d89fa3e35919157c1c98_81.png)

![](img/edc645529b03d89fa3e35919157c1c98_82.png)

使用 `kubeadm init` 命令初始化Master节点。此命令会生成一个用于Node节点加入集群的令牌（Token）。

```bash
kubeadm init \
  --kubernetes-version=v1.18.0 \
  --apiserver-advertise-address=192.168.1.63 \
  --image-repository=registry.aliyuncs.com/google_containers \
  --service-cidr=10.10.0.0/16 \
  --pod-network-cidr=10.22.0.0/16
```

![](img/edc645529b03d89fa3e35919157c1c98_84.png)

> **参数解释**：
> *   `--apiserver-advertise-address`：API Server对外提供服务的IP地址。
> *   `--image-repository`：指定镜像仓库地址，使用国内镜像加速。
> *   `--service-cidr`：为Kubernetes Service分配的内部虚拟IP网段（Cluster IP）。
> *   `--pod-network-cidr`：为Pod分配的IP地址网段。

![](img/edc645529b03d89fa3e35919157c1c98_86.png)

初始化成功后，命令行会输出类似以下的提示，请务必保存好 `kubeadm join` 开头的命令，后续Node节点需要用它加入集群。

![](img/edc645529b03d89fa3e35919157c1c98_88.png)

```
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

![](img/edc645529b03d89fa3e35919157c1c98_90.png)

kubeadm join 192.168.1.63:6443 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:xxxxxxxx...
```

### 3. 配置kubectl

根据上一步的提示，配置 `kubectl` 的配置文件，以便能正常管理集群。

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

![](img/edc645529b03d89fa3e35919157c1c98_92.png)

现在，你可以使用 `kubectl` 查看集群节点状态，但此时Master节点状态应为 `NotReady`，因为尚未安装网络插件。

```bash
kubectl get nodes
```

![](img/edc645529b03d89fa3e35919157c1c98_94.png)

## 部署网络插件与加入Node节点

![](img/edc645529b03d89fa3e35919157c1c98_96.png)

上一节我们成功初始化了Master节点，本节中我们来看看如何安装网络插件并将Node节点加入集群。

![](img/edc645529b03d89fa3e35919157c1c98_98.png)

### 1. 部署Pod网络插件（CNI）

![](img/edc645529b03d89fa3e35919157c1c98_100.png)

Kubernetes集群需要网络插件来实现Pod之间的通信。这里以Flannel为例。

```bash
# 下载Flannel的配置文件
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
# 部署Flannel网络插件
kubectl apply -f kube-flannel.yml
```

部署完成后，稍等片刻，再次使用 `kubectl get nodes` 命令，Master节点的状态应变为 `Ready`。

### 2. 将Node节点加入集群

在Node节点（192.168.1.64）上，执行之前在Master初始化时保存的 `kubeadm join` 命令。

![](img/edc645529b03d89fa3e35919157c1c98_102.png)

```bash
kubeadm join 192.168.1.63:6443 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:xxxxxxxx...
```

![](img/edc645529b03d89fa3e35919157c1c98_104.png)

加入成功后，回到Master节点，执行 `kubectl get nodes`，可以看到Node节点已经成功加入集群。

![](img/edc645529b03d89fa3e35919157c1c98_106.png)

```bash
NAME         STATUS   ROLES    AGE   VERSION
k8s-master   Ready    master   10m   v1.18.0
k8s-node1    Ready    <none>   2m    v1.18.0
```

![](img/edc645529b03d89fa3e35919157c1c98_108.png)

## 总结

![](img/edc645529b03d89fa3e35919157c1c98_110.png)

本节课中我们一起学习了使用 `kubeadm` 部署一个双节点Kubernetes集群的完整流程。我们主要完成了以下工作：

1.  **环境准备**：包括系统参数调整、防火墙与SELinux关闭、Swap分区关闭等。
2.  **组件安装**：在所有节点上安装Docker、`kubeadm`、`kubelet` 和 `kubectl`。
3.  **Master初始化**：在Master节点执行 `kubeadm init`，初始化控制平面，并配置 `kubectl`。
4.  **网络部署**：安装Flannel网络插件，使Pod能够互通。
5.  **Node加入**：使用 `kubeadm join` 命令将工作节点加入集群。

![](img/edc645529b03d89fa3e35919157c1c98_112.png)

![](img/edc645529b03d89fa3e35919157c1c98_114.png)

至此，一个功能完整的Kubernetes集群已经搭建成功，你可以开始在其上部署和管理容器化应用了。