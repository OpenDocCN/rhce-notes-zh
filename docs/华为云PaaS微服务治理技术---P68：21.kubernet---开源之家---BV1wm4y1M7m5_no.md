# 华为云PaaS微服务治理技术：P68：21.Kubernetes核心技术-Pod(2) 🚀

![](img/dd4b4417bcc1390804a0b9dee5642a7b_1.png)

![](img/dd4b4417bcc1390804a0b9dee5642a7b_3.png)

在本节课中，我们将继续深入学习Kubernetes的核心概念——Pod。我们将重点介绍Pod的创建、查看、删除等基本操作，并详细解析Pod的分类、生命周期、状态、重启策略以及资源配置。通过本课的学习，你将能够更全面地理解和管理Kubernetes中的Pod。

## Pod的基本操作 🔧

上一节我们介绍了Pod的基本概念，本节中我们来看看如何对Pod进行创建、查看和删除等操作。

![](img/dd4b4417bcc1390804a0b9dee5642a7b_5.png)

![](img/dd4b4417bcc1390804a0b9dee5642a7b_6.png)

### 创建Pod

![](img/dd4b4417bcc1390804a0b9dee5642a7b_7.png)

![](img/dd4b4417bcc1390804a0b9dee5642a7b_9.png)

我们可以使用 `kubectl create -f` 命令，并指定一个YAML配置文件来创建一个Pod。

以下是一个示例YAML文件 `demo2.yaml`，它定义了一个Pod：

![](img/dd4b4417bcc1390804a0b9dee5642a7b_11.png)

![](img/dd4b4417bcc1390804a0b9dee5642a7b_12.png)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox-pod
spec:
  containers:
  - name: busybox
    image: busybox
    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
```

![](img/dd4b4417bcc1390804a0b9dee5642a7b_14.png)

![](img/dd4b4417bcc1390804a0b9dee5642a7b_16.png)

### 查看Pod

创建Pod后，我们可以使用多种命令来查看Pod的状态和信息。

![](img/dd4b4417bcc1390804a0b9dee5642a7b_18.png)

以下是查看Pod的几种方式：

![](img/dd4b4417bcc1390804a0b9dee5642a7b_19.png)

![](img/dd4b4417bcc1390804a0b9dee5642a7b_20.png)

*   **查看所有Pod**：使用 `kubectl get pods` 或 `kubectl get po` 命令。
*   **查看指定Pod**：在命令后加上Pod的名称，例如 `kubectl get pod busybox-pod`。
*   **查看Pod详细信息**：使用 `kubectl describe pod <pod-name>` 命令，例如 `kubectl describe pod busybox-pod`。

### 删除Pod

当不再需要某个Pod时，我们可以将其删除。

以下是删除Pod的两种方式：

*   **通过配置文件删除**：使用 `kubectl delete -f <yaml-file>` 命令，例如 `kubectl delete -f demo1.yaml`。
*   **通过Pod名称删除**：使用 `kubectl delete pod <pod-name>` 命令，例如 `kubectl delete pod busybox-pod`。

![](img/dd4b4417bcc1390804a0b9dee5642a7b_22.png)

## Pod的分类 📦

了解完基本操作后，我们来看看Pod的分类。Pod主要分为两种类型：普通Pod和静态Pod。

*   **普通Pod**：这是最常用的类型。一旦被创建，就会被存储到ETCD中，随后由Master节点调度到某个具体的Node节点上绑定运行。Kubernetes会监控这些Pod，如果Pod内的容器停止或所在的Node节点宕机，系统会自动重启容器或将Pod重新调度到其他健康的Node节点上。
*   **静态Pod**：静态Pod由`kubelet`直接管理，仅存在于特定的Node节点上，不通过API Server进行管理。它无法与Replication Controller、Deployment等控制器关联，Kubernetes也无法对其执行健康检查。静态Pod在实际应用中较少使用。

## Pod的生命周期与状态 🔄

![](img/dd4b4417bcc1390804a0b9dee5642a7b_24.png)

Pod在其生命周期中会经历不同的状态。理解这些状态对于故障排查至关重要。

![](img/dd4b4417bcc1390804a0b9dee5642a7b_26.png)

![](img/dd4b4417bcc1390804a0b9dee5642a7b_27.png)

Pod主要有以下几种状态：

![](img/dd4b4417bcc1390804a0b9dee5642a7b_28.png)

*   **Pending（等待中）**：API Server已经创建了Pod对象，但Pod中的一个或多个容器镜像尚未创建完成（例如，正在下载镜像）。
*   **Running（运行中）**：Pod已经被调度到Node上，并且所有容器都已创建。至少有一个容器正在运行、正在启动或正在重启。
*   **Succeeded（成功）**：Pod中的所有容器均已成功执行并退出，且不会再重启。
*   **Failed（失败）**：Pod中的所有容器均已退出，但至少有一个容器是异常退出（即退出码非零）。
*   **Unknown（未知）**：由于某些原因（如网络通信故障）无法获取Pod的状态信息。

## Pod的重启策略 ⚙️

Pod的重启策略定义了当容器终止运行时，`kubelet`应该如何操作。重启策略在Pod的 `spec.restartPolicy` 字段中指定。

![](img/dd4b4417bcc1390804a0b9dee5642a7b_30.png)

![](img/dd4b4417bcc1390804a0b9dee5642a7b_31.png)

Pod支持三种重启策略：

![](img/dd4b4417bcc1390804a0b9dee5642a7b_32.png)

![](img/dd4b4417bcc1390804a0b9dee5642a7b_33.png)

*   **Always**：默认策略。只要容器终止运行，`kubelet`就会自动重启该容器。
*   **OnFailure**：仅当容器异常终止（退出码不为0）时，`kubelet`才会重启该容器。
*   **Never**：不论容器运行状态如何，`kubelet`都不会重启该容器。

![](img/dd4b4417bcc1390804a0b9dee5642a7b_34.png)

![](img/dd4b4417bcc1390804a0b9dee5642a7b_35.png)

## Pod的资源配置 💾

最后，我们来看看如何为Pod配置计算资源。在创建Pod时，我们可以为其中的容器设置CPU和内存的使用限额，以确保应用的稳定性和集群资源的合理分配。

![](img/dd4b4417bcc1390804a0b9dee5642a7b_37.png)

资源配置主要涉及两个参数：`requests` 和 `limits`。

![](img/dd4b4417bcc1390804a0b9dee5642a7b_38.png)

![](img/dd4b4417bcc1390804a0b9dee5642a7b_39.png)

![](img/dd4b4417bcc1390804a0b9dee5642a7b_40.png)

*   **`requests`**：定义容器**请求**的最小资源量。调度器会确保Node上有足够的资源满足这个请求，Pod才会被调度到该Node上。
*   **`limits`**：定义容器**允许使用**的最大资源量。如果容器尝试使用超过此限制的资源，它可能会被系统终止并可能根据重启策略被重启。

资源单位：
*   **CPU**：通常以核数（cores）表示，例如 `0.5` 代表半个CPU核心。
*   **内存**：以字节数表示，可以使用 `Mi`（Mebibyte）或 `Gi`（Gibibyte）等单位，例如 `128Mi` 代表128兆字节。

以下是一个资源配置的YAML示例：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-demo
spec:
  containers:
  - name: app
    image: myapp:latest
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"  # 250 milliCPU，即0.25个CPU核心
      limits:
        memory: "128Mi"
        cpu: "500m"  # 500 milliCPU，即0.5个CPU核心
```

在这个例子中，`app` 容器申请了最少0.25个CPU核心和64MiB内存。在运行过程中，它被允许使用的资源上限为0.5个CPU核心和128MiB内存。如果使用量超过这个上限，容器可能会被终止。

![](img/dd4b4417bcc1390804a0b9dee5642a7b_42.png)

---

![](img/dd4b4417bcc1390804a0b9dee5642a7b_44.png)

![](img/dd4b4417bcc1390804a0b9dee5642a7b_45.png)

本节课中我们一起学习了Kubernetes Pod的进阶知识。我们掌握了Pod的创建、查看和删除命令，理解了普通Pod与静态Pod的区别，熟悉了Pod生命周期中的各种状态及其含义，明确了Always、OnFailure和Never三种重启策略的适用场景，并学会了如何通过 `requests` 和 `limits` 为Pod配置CPU与内存资源。这些知识是进行有效的Kubernetes应用部署和运维的基础。