# 华为云PaaS微服务治理技术：P50：Kubernetes环境搭建常见问题解决 🛠️

![](img/155a1d79f398c38e38976f49c7b60a38_0.png)

在本节课中，我们将学习在Kubernetes快速入门案例中经常遇到的两个核心问题及其解决方案。我们将重点解决Docker镜像拉取失败和外部网络无法访问服务的问题，确保你的Kubernetes环境能够顺利运行。

![](img/155a1d79f398c38e38976f49c7b60a38_2.png)

## 问题一：Docker镜像拉取失败 🐳

![](img/155a1d79f398c38e38976f49c7b60a38_4.png)

在搭建Kubernetes环境时，由于网络限制，从国外镜像仓库拉取Docker镜像可能会失败。以下是解决此问题的两种方案。

### 解决方案概述

![](img/155a1d79f398c38e38976f49c7b60a38_6.png)

这两种方案的核心思路都是将镜像源替换为国内可访问的地址，或者使用预先下载到本地的镜像文件。

![](img/155a1d79f398c38e38976f49c7b60a38_8.png)

### 具体操作步骤

![](img/155a1d79f398c38e38976f49c7b60a38_10.png)

以下是两种修改Kubernetes配置文件以解决镜像拉取问题的方案。

![](img/155a1d79f398c38e38976f49c7b60a38_12.png)

**方案一：修改配置文件中的镜像仓库地址**
此方案通过修改配置文件，将默认的镜像仓库地址指向一个国内的镜像源或本地文件路径。
```bash
# 在 /etc/kubernetes/kubelet 配置文件中修改或添加如下参数
--pod-infra-container-image=registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.1
```

**方案二：使用本地镜像文件**
此方案先将所需镜像下载到本地，然后修改配置文件指向本地的镜像文件。
```bash
# 同样在 /etc/kubernetes/kubelet 配置文件中进行修改
--pod-infra-container-image=/path/to/your/local/image/pause:3.1
```

### 应用配置并重启服务

完成上述任一配置文件的修改后，需要重启`kubelet`服务以使更改生效。
```bash
sudo systemctl restart kubelet
```
重启服务后，可以使用以下命令检查Pod的状态，确认其是否从`ContainerCreating`变为`Running`。
```bash
kubectl get pods
```

![](img/155a1d79f398c38e38976f49c7b60a38_14.png)

---

![](img/155a1d79f398c38e38976f49c7b60a38_16.png)

## 问题二：外部网络无法访问服务 🌐

![](img/155a1d79f398c38e38976f49c7b60a38_18.png)

解决了镜像拉取问题后，部署的服务可能仍然无法从外部网络（如浏览器）访问。这通常需要修改Kubernetes代理服务的配置。

![](img/155a1d79f398c38e38976f49c7b60a38_20.png)

### 解决方案

![](img/155a1d79f398c38e38976f49c7b60a38_22.png)

此问题需要通过修改Kubernetes的代理配置文件来解决，添加允许外部访问的参数。

![](img/155a1d79f398c38e38976f49c7b60a38_24.png)

### 操作步骤

![](img/155a1d79f398c38e38976f49c7b60a38_26.png)

编辑 `/etc/kubernetes/kube-proxy` 配置文件，在文件中添加以下参数：
```bash
--proxy-mode=iptables
```
添加此参数后，同样需要重启相关的Kubernetes服务（如`kube-proxy`）或重新部署应用以使配置生效。之后，便可以通过节点的IP地址和服务的NodePort端口从外部浏览器访问部署的应用。

![](img/155a1d79f398c38e38976f49c7b60a38_28.png)

---

## 核心概念与操作回顾 📝

上一节我们介绍了两个具体问题的解决方法，本节中我们来回顾一下其中涉及的核心操作和概念。

![](img/155a1d79f398c38e38976f49c7b60a38_30.png)

![](img/155a1d79f398c38e38976f49c7b60a38_32.png)

### 关键命令总结

![](img/155a1d79f398c38e38976f49c7b60a38_34.png)

以下是本教程中使用的关键命令列表：
*   **重启服务**：`sudo systemctl restart <service_name>`
*   **查看Pod状态**：`kubectl get pods`
*   **重新部署资源**：`kubectl replace -f <filename.yaml>`
*   **删除资源**：`kubectl delete -f <filename.yaml>`
*   **创建资源**：`kubectl create -f <filename.yaml>`

### 扩容操作示例

Kubernetes的一个主要优势是便于扩容。例如，通过修改部署（Deployment）或副本控制器（ReplicationController）配置文件中的副本数量，可以轻松扩展应用实例。
```yaml
# 在RC或Deployment的YAML文件中，将 replicas 字段修改为期望的数量
spec:
  replicas: 2
```
修改后使用 `kubectl apply -f <filename.yaml>` 或 `kubectl replace -f <filename.yaml>` 命令即可完成扩容。

![](img/155a1d79f398c38e38976f49c7b60a38_36.png)

![](img/155a1d79f398c38e38976f49c7b60a38_38.png)

---

## 总结 🎯

![](img/155a1d79f398c38e38976f49c7b60a38_40.png)

本节课中我们一起学习了Kubernetes入门实践中的两个常见故障排除方法。我们首先解决了因网络导致的Docker镜像拉取失败问题，提供了两种修改配置的方案。接着，我们处理了服务部署后外部无法访问的情况，通过修改代理配置使其可被外网访问。最后，我们回顾了相关的关键命令和简单的应用扩容操作。通过这个快速入门案例，你应该能够初步理解如何使用Kubernetes来部署和管理容器化应用。如果在实践中遇到其他问题，可以参考讲义中提供的思路进行排查。