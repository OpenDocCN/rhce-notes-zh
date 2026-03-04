# 华为云PaaS微服务治理技术：P51：4.Kubernetes基本架构与常用术语 🚢

![](img/d73878b6ab42d0721be9a7d38a501863_0.png)

在本节课中，我们将要学习Kubernetes的基本架构和一些核心术语。理解这些概念是掌握Kubernetes容器编排技术的基础。

![](img/d73878b6ab42d0721be9a7d38a501863_2.png)

## 概述

![](img/d73878b6ab42d0721be9a7d38a501863_4.png)

![](img/d73878b6ab42d0721be9a7d38a501863_6.png)

Kubernetes集群由多个组件构成，其一切功能都基于分布式存储系统。接下来，我们将详细介绍其架构和关键术语。

## Kubernetes集群架构

![](img/d73878b6ab42d0721be9a7d38a501863_8.png)

![](img/d73878b6ab42d0721be9a7d38a501863_10.png)

Kubernetes集群包含节点代理、Kubelet和Master节点。其整体架构图清晰地展示了各个组件的关系。



![](img/d73878b6ab42d0721be9a7d38a501863_12.png)

![](img/d73878b6ab42d0721be9a7d38a501863_0.png)



![](img/d73878b6ab42d0721be9a7d38a501863_14.png)

![](img/d73878b6ab42d0721be9a7d38a501863_16.png)

上图将服务分为两类：运行在工作节点上的服务和组成集群级别控制平面的服务。右边部分是工作节点服务，也称为Node节点。



![](img/d73878b6ab42d0721be9a7d38a501863_18.png)

![](img/d73878b6ab42d0721be9a7d38a501863_4.png)



![](img/d73878b6ab42d0721be9a7d38a501863_20.png)

![](img/d73878b6ab42d0721be9a7d38a501863_22.png)

左边部分是整个Master控制平面。



![](img/d73878b6ab42d0721be9a7d38a501863_24.png)

![](img/d73878b6ab42d0721be9a7d38a501863_6.png)



![](img/d73878b6ab42d0721be9a7d38a501863_26.png)

Kubernetes节点上运行着应用容器必备的服务，这些服务都受Master控制。每个节点上都需要运行Docker，负责具体镜像的下载和容器运行。

![](img/d73878b6ab42d0721be9a7d38a501863_28.png)

![](img/d73878b6ab42d0721be9a7d38a501863_30.png)

## 核心组件

![](img/d73878b6ab42d0721be9a7d38a501863_32.png)

Kubernetes主要由以下几个核心组件组成。

![](img/d73878b6ab42d0721be9a7d38a501863_34.png)

以下是Master控制平面的核心组件：

![](img/d73878b6ab42d0721be9a7d38a501863_36.png)

1.  **ETCD**：保存整个集群的状态。
    ![](img/d73878b6ab42d0721be9a7d38a501863_10.png)

![](img/d73878b6ab42d0721be9a7d38a501863_38.png)

2.  **API Server**：提供资源操作的唯一入口，负责认证、授权、访问控制、API注册和发现等机制。
    ![](img/d73878b6ab42d0721be9a7d38a501863_14.png)

![](img/d73878b6ab42d0721be9a7d38a501863_40.png)

![](img/d73878b6ab42d0721be9a7d38a501863_42.png)

3.  **Controller Manager**：负责维护集群的状态，例如故障检测、自动扩展和滚动更新。
    ![](img/d73878b6ab42d0721be9a7d38a501863_20.png)

![](img/d73878b6ab42d0721be9a7d38a501863_44.png)

4.  **Scheduler**：负责资源的调度。
    ![](img/d73878b6ab42d0721be9a7d38a501863_24.png)

![](img/d73878b6ab42d0721be9a7d38a501863_46.png)

![](img/d73878b6ab42d0721be9a7d38a501863_48.png)

以下是Node工作节点上的核心组件：

1.  **Kubelet**：负责Pod管理，以及Pod和容器的真正运行。
2.  **Kube Proxy**：负责为Service提供集群内部的服务发现和负载均衡。
    ![](img/d73878b6ab42d0721be9a7d38a501863_26.png)

![](img/d73878b6ab42d0721be9a7d38a501863_50.png)

除了这些核心组件，还有一些推荐的附加组件，这些将在后续的高级课程中讲解。

![](img/d73878b6ab42d0721be9a7d38a501863_52.png)

![](img/d73878b6ab42d0721be9a7d38a501863_54.png)

## 设计理念与分层架构

Kubernetes的设计理念和功能类似于Linux的分层架构。

![](img/d73878b6ab42d0721be9a7d38a501863_56.png)

*   **核心层**：Kubernetes最核心的功能，对外提供API构建高层应用，对内提供插件式应用执行环境。
*   **应用层**：部署、路由、系统质量管理等。
*   **管理层**：自动化、自动扩容等。
*   **接口层**：`kubectl`命令行工具、客户端SDK等，后续练习中会用到。

![](img/d73878b6ab42d0721be9a7d38a501863_58.png)

Kubernetes的生态系统庞大，在接口层上可分为两个范畴：Kubernetes内部的和外部的生态系统。

![](img/d73878b6ab42d0721be9a7d38a501863_60.png)

## 关键术语详解

![](img/d73878b6ab42d0721be9a7d38a501863_62.png)

上一节我们介绍了Kubernetes的整体架构，本节中我们来看看几个最关键的术语。

![](img/d73878b6ab42d0721be9a7d38a501863_64.png)

### Cluster（集群）

![](img/d73878b6ab42d0721be9a7d38a501863_66.png)

Cluster是计算、存储和网络资源的集合。Kubernetes利用这些资源来运行各个基于容器的应用。整个Kubernetes集群由Master和Node组成。



![](img/d73878b6ab42d0721be9a7d38a501863_68.png)

![](img/d73878b6ab42d0721be9a7d38a501863_44.png)



![](img/d73878b6ab42d0721be9a7d38a501863_70.png)

### Master（主节点）

![](img/d73878b6ab42d0721be9a7d38a501863_72.png)

Master是集群的大脑，主要职责是调度，即决定将应用放在哪个Node上运行。Master运行在Linux系统上，可以是物理机或虚拟机。它包含以下服务：

*   **API Server**：提供HTTP RESTful API，是Kubernetes里所有资源进行增删改查（CRUD）等操作的唯一入口。
*   **Scheduler**：负责资源调度，决定将Pod放在哪个Node上运行。
*   **Controller Manager**：所有资源对象的自动化控制中心，负责管理集群各种资源，保证它们处于预期状态。不同的Controller管理不同的资源，例如Deployment Controller管理Deployment。
*   **ETCD**：负责保存Kubernetes集群的配置信息和各种资源的状态信息。当数据发生变化时，ETCD会快速通知Kubernetes相关组件。
*   **Pod网络**：保障Pod之间的网络通信。



![](img/d73878b6ab42d0721be9a7d38a501863_74.png)

![](img/d73878b6ab42d0721be9a7d38a501863_76.png)

![](img/d73878b6ab42d0721be9a7d38a501863_52.png)



![](img/d73878b6ab42d0721be9a7d38a501863_78.png)

![](img/d73878b6ab42d0721be9a7d38a501863_80.png)

### Node（节点）

![](img/d73878b6ab42d0721be9a7d38a501863_82.png)

Node是真正“做事”的节点。除了Master，集群中其他机器都称为Node节点。Node的职责是运行容器，由Master管理。Node负责监控并汇报容器状态，同时根据Master的要求管理容器的生命周期。Node也运行在Linux系统上，包含以下组件：

*   **Kubelet**：负责Pod对应容器的创建、启动等，也是与Master节点协作实现集群管理的关键。
*   **Kube Proxy**：负责Service的通信以及负载均衡。
*   **Docker引擎**：负责运行容器。



![](img/d73878b6ab42d0721be9a7d38a501863_84.png)

![](img/d73878b6ab42d0721be9a7d38a501863_72.png)



![](img/d73878b6ab42d0721be9a7d38a501863_86.png)

### Pod

Pod是Kubernetes中最小的、也是最重要和最基本的概念单元。



![](img/d73878b6ab42d0721be9a7d38a501863_80.png)



![](img/d73878b6ab42d0721be9a7d38a501863_88.png)

![](img/d73878b6ab42d0721be9a7d38a501863_90.png)

每个Pod包含一个或多个容器（如Docker容器）。Pod中的容器会作为一个整体被Master调度到某个Node上运行。Kubernetes为每个Pod分配唯一的IP地址，称为Pod IP。一个Pod内的多个容器共享网络空间（包括IP地址）。在Kubernetes中，不同主机上的Pod容器之间能够直接通信。

![](img/d73878b6ab42d0721be9a7d38a501863_92.png)

### Service

![](img/d73878b6ab42d0721be9a7d38a501863_94.png)

Service是外界访问Pod中特定服务的方式。Service有自己的IP地址和端口，与Pod不同。Service为Pod提供了负载均衡，是Kubernetes最核心的资源对象之一。每个Service可以对应微服务架构中的一个微服务。

![](img/d73878b6ab42d0721be9a7d38a501863_96.png)

![](img/d73878b6ab42d0721be9a7d38a501863_98.png)

### Replication Controller (RC)

![](img/d73878b6ab42d0721be9a7d38a501863_100.png)

Replication Controller（简称RC）是Kubernetes系统的核心概念之一。它定义了一个期望的场景，即声明某种Pod的副本数量在任意时刻都符合某个预期值。

![](img/d73878b6ab42d0721be9a7d38a501863_102.png)

RC定义了以下内容：
*   `spec.replicas`：期待的Pod副本数量。
*   `spec.selector`：用于筛选目标Pod的标签选择器（Label Selector）。
*   `spec.template`：当Pod副本数量小于预期时，用于创建新Pod的模板。

![](img/d73878b6ab42d0721be9a7d38a501863_104.png)

![](img/d73878b6ab42d0721be9a7d38a501863_106.png)

简单来说，创建RC后，就可以设置镜像、副本数量等。RC通过Label Selector机制实现对Pod副本数量的自动控制，确保实际运行的Pod数量与期望值一致。

![](img/d73878b6ab42d0721be9a7d38a501863_108.png)

## 总结

![](img/d73878b6ab42d0721be9a7d38a501863_110.png)

本节课中我们一起学习了Kubernetes的基本架构和核心术语。我们了解到Kubernetes集群由Master控制平面和Node工作节点组成，并深入探讨了Pod、Service、Replication Controller等关键概念。这些是理解和使用Kubernetes进行容器化应用编排和微服务治理的基石。后续课程将在这些基础上展开更深入的学习。