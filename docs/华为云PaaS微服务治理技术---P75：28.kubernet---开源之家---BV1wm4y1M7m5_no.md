# 华为云PaaS微服务治理技术 - P75：28. Kubernetes核心技术-Service(1) 🚪

在本节课中，我们将要学习Kubernetes的核心概念之一：Service。Service是Kubernetes中用于为一组功能相同的Pod提供稳定网络入口和负载均衡的关键资源。

上一节我们介绍了其他Kubernetes资源，本节中我们来看看Service的基本概念和用法。

## Service概述

![](img/f31f11712eb5e3f653bb0c7df2be4811_1.png)

![](img/f31f11712eb5e3f653bb0c7df2be4811_2.png)

Service是Kubernetes最核心的一个概念。通过Service，我们可以为一组具有相同功能的容器应用提供一个统一的入口地址，并且将请求负载分发到后端的各个容器上。

![](img/f31f11712eb5e3f653bb0c7df2be4811_3.png)

![](img/f31f11712eb5e3f653bb0c7df2be4811_4.png)

下面是一个完整的YAML格式的Service定义文件示例：

![](img/f31f11712eb5e3f653bb0c7df2be4811_5.png)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
  namespace: default
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

以下是Service定义中关键字段的说明：

![](img/f31f11712eb5e3f653bb0c7df2be4811_7.png)

![](img/f31f11712eb5e3f653bb0c7df2be4811_8.png)

![](img/f31f11712eb5e3f653bb0c7df2be4811_9.png)

*   **`apiVersion`**: 指定Kubernetes API的版本，对于Service通常是 `v1`。
*   **`kind`**: 资源类型，此处固定为 `Service`。
*   **`metadata`**: 资源的元数据，包括名称（`name`）和命名空间（`namespace`）等。
*   **`spec.selector`**: 标签选择器，用于选择后端哪些Pod属于这个Service。
*   **`spec.ports`**: 定义端口映射规则，`port`是Service对外的端口，`targetPort`是Pod内部监听的端口。

![](img/f31f11712eb5e3f653bb0c7df2be4811_10.png)

## Service的基本用法

![](img/f31f11712eb5e3f653bb0c7df2be4811_12.png)

![](img/f31f11712eb5e3f653bb0c7df2be4811_13.png)

前面我们说过，Service的主要目的是为功能相同的Pod提供一个统一的入口。那么如何提供这个统一的入口呢？我们通过一个基本用法来感受一下。

通常，对外提供服务的应用程序需要通过某种机制来实现。对于容器应用，最简单的方式是通过TCP/IP机制，监听特定的IP地址和端口号。

![](img/f31f11712eb5e3f653bb0c7df2be4811_15.png)

### 直接访问Pod的问题

![](img/f31f11712eb5e3f653bb0c7df2be4811_16.png)

![](img/f31f11712eb5e3f653bb0c7df2be4811_17.png)

首先，我们创建一个最基本的ReplicationController（RC）来管理一个Pod。

![](img/f31f11712eb5e3f653bb0c7df2be4811_18.png)

```yaml
# demo7-2-rc.yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: my-webapp
spec:
  replicas: 1
  selector:
    app: my-webapp
  template:
    metadata:
      labels:
        app: my-webapp
    spec:
      containers:
      - name: my-webapp
        image: tomcat
        ports:
        - containerPort: 8080
```

使用命令 `kubectl apply -f demo7-2-rc.yaml` 创建RC后，我们可以获取到Pod的IP地址。

```bash
kubectl get pods -o wide
```

然后，我们可以通过 `curl` 命令直接访问这个Pod的IP地址和端口（例如 `curl <POD_IP>:8080`）来访问应用。

![](img/f31f11712eb5e3f653bb0c7df2be4811_20.png)

![](img/f31f11712eb5e3f653bb0c7df2be4811_21.png)

但是，这种直接访问Pod的方式是不可靠的。因为当Pod所在的Node节点发生故障时，Pod会被Kubernetes重新调度到另一个Node上运行，这时Pod的IP地址就会发生改变，之前的访问方式就会失效。

![](img/f31f11712eb5e3f653bb0c7df2be4811_22.png)

### 通过Service访问Pod

![](img/f31f11712eb5e3f653bb0c7df2be4811_24.png)

![](img/f31f11712eb5e3f653bb0c7df2be4811_25.png)

为了解决上述问题，我们可以通过Service来提供一个稳定的访问入口。即使后端的Pod发生故障并迁移，只要Service与Pod的关联关系存在，我们仍然可以通过Service来访问应用。

以下是创建一个对应上述Pod的Service的YAML文件：

```yaml
# demo7-5-svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: my-webapp-svc
spec:
  selector:
    app: my-webapp
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8080
```

![](img/f31f11712eb5e3f653bb0c7df2be4811_27.png)

![](img/f31f11712eb5e3f653bb0c7df2be4811_28.png)

使用命令 `kubectl apply -f demo7-5-svc.yaml` 创建Service。创建后，我们可以查看Service的信息：

![](img/f31f11712eb5e3f653bb0c7df2be4811_29.png)

![](img/f31f11712eb5e3f653bb0c7df2be4811_30.png)

```bash
kubectl get svc my-webapp-svc
```

![](img/f31f11712eb5e3f653bb0c7df2be4811_31.png)

输出中会显示Service的集群IP（CLUSTER-IP），例如 `10.254.163.4`。现在，我们可以通过访问这个Service的IP和端口（`curl 10.254.163.4:8081`）来访问后端的Tomcat应用。Service会自动将流量转发到标签为 `app: my-webapp` 的Pod上。

![](img/f31f11712eb5e3f653bb0c7df2be4811_33.png)

本节课中我们一起学习了Kubernetes Service的核心概念和基本用法。我们了解到，直接访问Pod存在IP地址不稳定的问题，而Service通过提供统一的、稳定的虚拟IP和端口，并结合标签选择器将流量路由到后端Pod，有效解决了服务发现和负载均衡的问题，是构建可靠微服务架构的基础。