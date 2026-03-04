# Kubernetes入门：P105：K8S概念与集群环境准备

![](img/6fe6a43214edd0b9abb00be056dcb7d5_1.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_2.png)

## 概述

![](img/6fe6a43214edd0b9abb00be056dcb7d5_4.png)

在本节课中，我们将学习Kubernetes（简称K8S）的核心概念，并开始准备一个高可用的K8S集群环境。K8S是当前运维领域，特别是容器化应用管理方面至关重要的技术。

![](img/6fe6a43214edd0b9abb00be056dcb7d5_6.png)

## 什么是Kubernetes？

![](img/6fe6a43214edd0b9abb00be056dcb7d5_8.png)

Kubernetes是一个开源的容器集群编排系统，由Google公司于2015年基于Go语言开发。它主要用于解决企业环境中大规模容器的统一管理问题。

K8S名称的由来是“Kubernetes”单词中K和S之间有8个字母。其前身是Google内部一个非常成熟的容器管理系统Borg，该系统早在2004年就已投入生产环境，能够自动化管理数十亿容器的创建与销毁。因此，K8S可以说是“含着金钥匙”诞生的技术，一经推出便迅速成为行业标准。

## Kubernetes的核心架构与组件

一个K8S集群主要由两部分组成：**控制节点（Master）** 和 **工作节点（Node）**。

### 控制节点（Master）

控制节点是集群的管理者，负责整个集群的管理和调度。它包含以下核心组件：

*   **API Server**：集群的统一管理入口，接收并处理所有管理指令。
*   **Scheduler**：负责集群的资源调度计算，根据预定的策略决定将应用（Pod）分配到哪个工作节点上运行。
*   **Controller Manager**：负责运行各种控制器进程，管理集群中应用的部署、故障检测、自动扩展和滚动更新等。
*   **etcd**：一个高可用的键值数据库，用于存储集群的所有配置数据和状态信息。

### 工作节点（Node）

工作节点是真正运行容器化应用的节点。它包含以下核心组件：

*   **kubelet**：负责与Master节点通信，接收指令并管理本节点上容器的生命周期。
*   **kube-proxy**：负责节点上的网络代理和负载均衡，使集群内部或外部的客户端能够访问到运行的服务。
*   **容器运行时（Container Runtime）**：负责运行容器的底层软件，例如Docker、containerd或CRI-O。kubelet通过控制容器运行时来执行具体的容器操作。

## 组件工作流程解析

为了理解这些组件如何协同工作，我们以一个部署网站应用的请求为例：

1.  用户通过`kubectl`命令向集群发送“部署一个网站”的指令。
2.  **API Server** 接收该指令。
3.  **API Server** 调用 **Scheduler**。Scheduler查询 **etcd** 中各个Node节点的资源使用情况。
4.  **Scheduler** 经过计算，决定将网站应用部署到`node01`节点，并将结果反馈给API Server。
5.  **API Server** 调用 **Controller Manager**，由它负责将部署任务安排到`node01`节点。
6.  `node01`节点上的 **kubelet** 接收到任务。
7.  **kubelet** 调用本节点的 **容器运行时**（如Docker），创建并运行包含网站应用的容器。
8.  用户访问网站时，流量经由 **kube-proxy** 被正确路由到运行在`node01`上的网站容器。

## Kubernetes的主要特性

K8S提供了强大的容器管理功能，主要包括：

*   **自我修复**：当容器故障时，自动重启或替换容器，确保服务高可用。
*   **弹性伸缩**：可根据CPU使用率等指标，自动增加或减少运行中的容器副本数量。
*   **服务发现与负载均衡**：服务之间可以自动发现彼此，并且可以为服务提供负载均衡。
*   **自动发布和回滚**：可以控制应用的滚动更新，并在发现问题时一键回滚到之前的版本。
*   **存储编排**：支持自动挂载所需的存储系统，无论是本地存储、网络存储还是云存储。
*   **密钥与配置管理**：可以管理敏感信息和应用配置，并能在不重建容器镜像的情况下更新配置。

## 集群类型

根据Master节点的数量，K8S集群可分为两种类型：

*   **一主多从集群**：单个Master节点管理多个Node节点。这种架构存在单点故障风险，适用于学习和测试环境。
*   **多主多从集群**：多个Master节点构成高可用集群，即使一个Master节点宕机，集群仍可正常运作。这是生产环境的推荐架构。

## 集群环境准备

接下来，我们将开始准备一个多主多从的高可用K8S集群环境。

### 环境规划

我们将部署一个包含3个Master节点和2个Worker节点的集群：

*   **Master节点**：`master01` (192.168.0.10), `master02` (192.168.0.11), `master03` (192.168.0.12)
*   **Worker节点**：`worker01` (192.168.0.13), `worker02` (192.168.0.14)
*   **虚拟IP（VIP）**：192.168.0.100 (用于高可用访问入口)
*   **系统要求**：每个节点至少2核CPU、4GB内存、50GB磁盘。

### 基础环境配置

以下操作需要在所有5台主机上执行。我们将使用Ansible进行批量操作以提高效率。

首先，在`master01`节点上安装Ansible并配置主机名解析：

```bash
# 1. 安装Ansible（在master01上执行）
tar -xf ansible.tar.gz
cd ansible/root/
yum install -y ./*.rpm

![](img/6fe6a43214edd0b9abb00be056dcb7d5_10.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_12.png)

# 2. 配置本地hosts文件（在master01上执行）
cat > /etc/hosts << EOF
192.168.0.10 master01
192.168.0.11 master02
192.168.0.12 master03
192.168.0.13 worker01
192.168.0.14 worker02
EOF

# 3. 配置SSH免密登录，方便Ansible管理
ssh-keygen -t rsa
for ip in master01 master02 master03 worker01 worker02; do
  ssh-copy-id $ip
done

# 4. 配置Ansible主机清单
cat > /etc/ansible/hosts << EOF
[k8s]
master01
master02
master03
worker01
worker02
EOF

![](img/6fe6a43214edd0b9abb00be056dcb7d5_14.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_16.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_18.png)

# 5. 将hosts文件批量分发到所有节点
ansible k8s -m copy -a "src=/etc/hosts dest=/etc/hosts"
```

![](img/6fe6a43214edd0b9abb00be056dcb7d5_20.png)

### 升级系统内核

![](img/6fe6a43214edd0b9abb00be056dcb7d5_22.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_24.png)

K8S对Linux内核版本有要求，建议升级到较新的长期支持版本。

```bash
# 1. 启用ELRepo仓库
ansible k8s -m shell -a "rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org"
ansible k8s -m shell -a "rpm -Uvh https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm"

![](img/6fe6a43214edd0b9abb00be056dcb7d5_26.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_28.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_30.png)

# 2. 安装最新的长期支持内核
ansible k8s -m shell -a "yum --enablerepo=elrepo-kernel install -y kernel-lt"

![](img/6fe6a43214edd0b9abb00be056dcb7d5_32.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_34.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_36.png)

# 3. 设置新内核为默认启动项
ansible k8s -m shell -a "grub2-set-default 0"
```

![](img/6fe6a43214edd0b9abb00be056dcb7d5_38.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_40.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_42.png)

内核安装和设置完成后，需要重启所有服务器以使新内核生效。

![](img/6fe6a43214edd0b9abb00be056dcb7d5_44.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_46.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_48.png)

## 总结

![](img/6fe6a43214edd0b9abb00be056dcb7d5_50.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_51.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_53.png)

本节课我们一起学习了Kubernetes的基本概念、核心架构组件及其协同工作流程，并了解了K8S提供的强大管理特性。同时，我们开始了高可用K8S集群的部署准备工作，完成了主机网络配置和系统内核升级。

![](img/6fe6a43214edd0b9abb00be056dcb7d5_55.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_57.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_59.png)

![](img/6fe6a43214edd0b9abb00be056dcb7d5_61.png)

在接下来的课程中，我们将继续完成容器运行时、K8S组件本身的安装与配置，最终搭建起一个完整的多主多从K8S集群。