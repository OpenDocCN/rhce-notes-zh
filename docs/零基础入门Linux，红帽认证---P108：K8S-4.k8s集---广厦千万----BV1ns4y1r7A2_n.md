# Kubernetes集群部署教程：P108：K8S集群初始化与测试 🚀

![](img/40d9d62444f0bda4a9219801ef3260c2_1.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_3.png)

## 概述
在本节课中，我们将学习如何完成Kubernetes集群的初始化配置，并对集群进行基本测试。我们将重点解决上节课遗留的VIP查看问题，并逐步安装集群核心组件，最终完成集群的搭建。

---

![](img/40d9d62444f0bda4a9219801ef3260c2_5.png)

## 上节回顾与问题排查 🔍

上一节我们部署了Haproxy和Keepalived以实现高可用，但在查看VIP时遇到了问题。

经过排查，发现问题并非配置错误。使用 `ifconfig` 命令确实无法看到配置的VIP地址，但这不代表配置失败。VIP实际上已经成功配置。

以下是查看VIP的正确方法：
*   **错误方法**：`ifconfig` 命令无法显示VIP。
*   **正确方法**：使用 `ip a` 命令可以查看到配置的VIP地址。

![](img/40d9d62444f0bda4a9219801ef3260c2_7.png)

这个差异是导致我们误以为配置失败的原因。

![](img/40d9d62444f0bda4a9219801ef3260c2_9.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_11.png)

---

![](img/40d9d62444f0bda4a9219801ef3260c2_13.png)

## 高可用架构解析 🏗️

![](img/40d9d62444f0bda4a9219801ef3260c2_15.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_17.png)

为了便于理解Haproxy和Keepalived在整个架构中的作用，我们通过下图来说明：

![](img/40d9d62444f0bda4a9219801ef3260c2_1.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_19.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_21.png)

以下是各组件的功能解析：

![](img/40d9d62444f0bda4a9219801ef3260c2_23.png)

1.  **Keepalived**：为Haproxy提供一个虚拟IP（VIP）。当某个Haproxy节点故障时，VIP会自动“漂移”到健康的Haproxy节点上。
2.  **Haproxy**：作为集群流量的入口，接收所有管理请求（例如通过 `kubectl` 命令或Dashboard发起的请求），并将这些请求负载均衡到后端的多个Master节点。
3.  **Master节点**：接收来自Haproxy转发的请求，处理集群的管理逻辑。
4.  **工作节点（Worker）**：与Master节点通信时，同样通过VIP地址进行。

这种架构确保了即使某个Haproxy或Master节点失效，集群的管理入口和高可用性也不会受到影响。

![](img/40d9d62444f0bda4a9219801ef3260c2_25.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_27.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_29.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_30.png)

---

![](img/40d9d62444f0bda4a9219801ef3260c2_32.png)

## VIP切换验证 ✅

![](img/40d9d62444f0bda4a9219801ef3260c2_34.png)

我们可以通过脚本或手动操作来验证VIP的高可用性。脚本的核心是检测Haproxy服务的健康状态。

![](img/40d9d62444f0bda4a9219801ef3260c2_36.png)

以下是手动验证步骤：
1.  在当前的Master节点（例如master01）上停止Haproxy服务：`systemctl stop haproxy`
2.  等待片刻后，在另一个Master节点（例如master02）上使用 `ip a` 命令查看，会发现VIP已经“漂移”过来。
3.  重新启动master01上的Haproxy服务：`systemctl start haproxy`
4.  VIP会根据优先级（如配置的101 > 100）重新漂移回master01。

![](img/40d9d62444f0bda4a9219801ef3260c2_38.png)

**注意**：VIP切换可能需要一些时间，并且需要确保Keepalived服务正常运行。

---

![](img/40d9d62444f0bda4a9219801ef3260c2_40.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_42.png)

## 安装Kubernetes核心组件 📦

![](img/40d9d62444f0bda4a9219801ef3260c2_44.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_46.png)

本节中，我们将在所有节点（包括Master和Worker）上安装Kubernetes的核心组件。

我们需要安装以下三个组件：
*   **kubeadm**：用于快速引导和建立Kubernetes集群的工具。
*   **kubelet**：在集群每个节点上运行的代理，负责接收API Server的指令，管理节点上的Pod。
*   **kubectl**：Kubernetes集群的命令行管理工具。

![](img/40d9d62444f0bda4a9219801ef3260c2_48.png)

由于网络原因，我们使用阿里云的镜像仓库进行安装。

首先，在所有节点上配置阿里云的Kubernetes yum仓库。仓库文件内容如下（以 `k8s.repo` 为例）：
```ini
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=0
```
可以使用Ansible等工具批量下发此仓库文件到所有节点的 `/etc/yum.repos.d/` 目录下。

配置完成后，在所有节点上执行安装命令，并指定版本为1.23.0：
```bash
yum install -y kubeadm-1.23.0 kubelet-1.23.0 kubectl-1.23.0
```

---

## 配置Kubelet 🛠️

因为之前我们将Docker的Cgroup驱动改为了 `systemd`，所以需要相应修改Kubelet的配置以保持一致。

修改 `/etc/sysconfig/kubelet` 文件，添加以下参数：
```bash
KUBELET_EXTRA_ARGS="--cgroup-driver=systemd"
```
将此配置文件同步到所有集群节点。

**注意**：目前**不要启动**kubelet服务，因为集群尚未初始化，缺少必要的配置文件。我们只需将其设置为开机自启：
```bash
systemctl enable kubelet
```

---

## 初始化Kubernetes集群 ⚙️

集群初始化操作**仅在第一个Master节点（例如master01）上执行**。

![](img/40d9d62444f0bda4a9219801ef3260c2_50.png)

我们需要一个初始化配置文件。以下是一个示例 `kubeadm-config.yaml` 的核心内容，你需要根据实际环境修改：
```yaml
apiVersion: kubeadm.k8s.io/v1beta3
kind: InitConfiguration
localAPIEndpoint:
  # 修改为当前master01节点的IP地址
  advertiseAddress: 192.168.0.201
  bindPort: 6443
nodeRegistration:
  # 修改为当前master01节点的主机名
  name: master01
  criSocket: /var/run/dockershim.sock
---
apiVersion: kubeadm.k8s.io/v1beta3
kind: ClusterConfiguration
kubernetesVersion: v1.23.0
controlPlaneEndpoint: "192.168.0.210:6443" # 填写你的VIP地址和端口
imageRepository: registry.aliyuncs.com/google_containers # 使用阿里云镜像仓库
networking:
  serviceSubnet: "10.96.0.0/12"
  podSubnet: "10.244.0.0/16" # 后续安装Flannel网络插件时需要与此一致
```
**关键修改项**：
1.  `advertiseAddress`：改为master01节点的真实IP。
2.  `name`：改为master01节点的主机名。
3.  `controlPlaneEndpoint`：填写之前配置的VIP地址和端口（6443）。

![](img/40d9d62444f0bda4a9219801ef3260c2_52.png)

配置文件准备就绪后，在master01节点上执行初始化命令：
```bash
kubeadm init --config=kubeadm-config.yaml --upload-certs
```
此命令会：
1.  从指定镜像仓库拉取Kubernetes核心组件（如API Server、Controller Manager、Scheduler、Etcd等）的镜像。
2.  生成集群运行所需的各类证书和配置文件。
3.  启动Master节点的核心控制平面组件。

![](img/40d9d62444f0bda4a9219801ef3260c2_54.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_56.png)

初始化成功后，命令行会输出加入集群的命令（用于其他Master和Worker节点），请务必保存好。

![](img/40d9d62444f0bda4a9219801ef3260c2_58.png)

---

![](img/40d9d62444f0bda4a9219801ef3260c2_60.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_62.png)

## 微服务架构简介（知识拓展） 💡

![](img/40d9d62444f0bda4a9219801ef3260c2_64.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_66.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_68.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_70.png)

在等待组件安装或初始化时，我们简单了解一下Kubernetes的主要应用场景——**微服务架构**。

![](img/40d9d62444f0bda4a9219801ef3260c2_72.png)

**微服务** 可以理解为将一个大型、复杂的单体应用，拆分为多个小型、独立的服务。每个服务都专注于一个特定的业务功能（例如用户管理、订单处理、支付网关），并可以独立开发、部署和扩展。

![](img/40d9d62444f0bda4a9219801ef3260c2_74.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_76.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_78.png)

**传统单体架构** 的缺点：
*   **维护困难**：代码库庞大，新人上手慢，核心人员离职风险高。
*   **部署低效**：任何微小修改都需要重新构建和部署整个应用，耗时漫长。
*   **可靠性差**：一个模块的Bug可能导致整个应用崩溃（蝴蝶效应）。

![](img/40d9d62444f0bda4a9219801ef3260c2_80.png)

**微服务架构的优势**：
*   **技术异构**：每个服务可以使用最适合的技术栈。
*   **独立部署**：服务可独立更新和发布，不影响其他服务。
*   **弹性扩展**：可根据需求单独扩展某个服务，资源利用率高。
*   **容错性高**：单个服务故障不会导致整个系统瘫痪。

Kubernetes为这些独立的微服务提供了完美的运行和管理平台，负责服务的部署、调度、网络通信、扩缩容和故障恢复，是微服务架构得以落地的重要基石。

![](img/40d9d62444f0bda4a9219801ef3260c2_82.png)

---

![](img/40d9d62444f0bda4a9219801ef3260c2_84.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_86.png)

## 总结 🎯

![](img/40d9d62444f0bda4a9219801ef3260c2_88.png)

本节课中我们一起学习了：
1.  **排查并解决了VIP查看问题**，明确了应使用 `ip a` 命令。
2.  **深入理解了高可用架构**中Keepalived、Haproxy和Master节点的协作关系。
3.  **在所有节点上安装了Kubernetes核心组件**（kubeadm， kubelet， kubectl）。
4.  **配置了Kubelet**的Cgroup驱动以匹配Docker。
5.  **准备了集群初始化配置文件**并了解了其关键参数。
6.  **拓展了解了微服务架构**的概念及其与Kubernetes的关系。

![](img/40d9d62444f0bda4a9219801ef3260c2_90.png)

![](img/40d9d62444f0bda4a9219801ef3260c2_92.png)

下一节，我们将执行集群初始化命令，并学习如何将其他节点加入集群，最终完成一个完整可用的Kubernetes集群的搭建。