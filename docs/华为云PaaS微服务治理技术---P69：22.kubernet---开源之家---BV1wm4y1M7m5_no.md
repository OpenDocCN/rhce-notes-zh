# 华为云PaaS微服务治理技术：P69：22. Kubernetes核心技术-Label 🏷️

在本节课中，我们将要学习Kubernetes中的另一个核心概念——Label。Label是用于对集群中的资源对象进行分组和管理的键值对。我们将了解它的定义、作用以及如何在实践中使用它。

![](img/1c2968a8a5067c92076a1e1fe5663144_1.png)

![](img/1c2968a8a5067c92076a1e1fe5663144_2.png)

## 概述

Label是Kubernetes系统中的核心概念之一。一个Label本质上是一个键值对（Key-Value Pair），其中键（K）和值（V）都由用户自行指定。它可以被附加到各种资源对象上，例如Node、Pod、Service、RC等，以实现灵活的资源分组管理。

## 什么是Label？

Label是一个键值对，其格式可以表示为：`key: value`。用户可以根据需要定义任意数量的Label，并将其附加到一个或多个资源对象上。

Label通常在资源对象的定义文件中确定，也可以在对象创建后动态地进行添加或删除操作。

![](img/1c2968a8a5067c92076a1e1fe5663144_4.png)

![](img/1c2968a8a5067c92076a1e1fe5663144_5.png)

## Label的用法

![](img/1c2968a8a5067c92076a1e1fe5663144_6.png)

![](img/1c2968a8a5067c92076a1e1fe5663144_7.png)

![](img/1c2968a8a5067c92076a1e1fe5663144_8.png)

Label最常见的用法是：在资源对象的`metadata.labels`字段中定义Label，然后通过另一个对象的`spec.selector`字段来引用这些Label，从而建立对象之间的关联关系。

以下是一个在ReplicationController（RC）定义文件中使用Label的例子：

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc
spec:
  replicas: 3
  selector:
    app: myapp # 这里通过selector引用具有特定label的Pod
  template:
    metadata:
      labels:
        app: myapp # 这里为Pod模板定义了一个label
    spec:
      containers:
      - name: myapp-container
        image: myapp:1.0
```

在这个例子中，我们在Pod模板的`metadata.labels`下定义了一个Label：`app: myapp`。接着，在RC的`spec.selector`中，我们通过`app: myapp`这个条件，指定这个RC要管理所有带有此Label的Pod。Service资源也可以使用类似的`selector`来定位后端Pod。

## Label的作用与意义

![](img/1c2968a8a5067c92076a1e1fe5663144_10.png)

![](img/1c2968a8a5067c92076a1e1fe5663144_11.png)

![](img/1c2968a8a5067c92076a1e1fe5663144_12.png)

Label的主要作用是对Kubernetes集群中的各种资源对象进行分组管理。而实现分组管理的核心机制就是Label Selector。

![](img/1c2968a8a5067c92076a1e1fe5663144_13.png)

![](img/1c2968a8a5067c92076a1e1fe5663144_14.png)

Label和Label Selector都不能单独存在，它们必须被定义和应用于具体的资源对象上，通常是在ReplicationController或Service的定义中。

![](img/1c2968a8a5067c92076a1e1fe5663144_15.png)

![](img/1c2968a8a5067c92076a1e1fe5663144_16.png)

定义Label的目的，是为了给资源打上“标签”，从而可以通过Selector方便地筛选和管理具有相同标签的一组资源，例如：“将所有运行前端服务的Pod进行扩容”或“将流量路由到特定版本的应用”。

![](img/1c2968a8a5067c92076a1e1fe5663144_17.png)

![](img/1c2968a8a5067c92076a1e1fe5663144_18.png)

## 实践演示

假设我们有一个定义文件`demo3.yaml`，内容如下：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: labeled-pod-demo
  labels:
    app: myapp
    tier: frontend
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
```

我们可以通过以下命令来创建这个Pod并查看其Label：

![](img/1c2968a8a5067c92076a1e1fe5663144_20.png)

1.  删除可能存在的同名Pod（如果存在）：
    ```bash
    kubectl delete pod labeled-pod-demo
    ```
2.  使用YAML文件创建Pod：
    ```bash
    kubectl create -f demo3.yaml
    ```
3.  查看Pod的详细信息，其中包含我们定义的Label：
    ```bash
    kubectl describe pod labeled-pod-demo
    ```
    在输出中，你可以找到`Labels: app=myapp, tier=frontend`，这就是我们附加到Pod上的标签。

通过这个实践，我们可以看到Label被成功定义并附加到了Pod资源上。后续，其他资源（如Service）就可以通过Selector（例如 `app: myapp`）来找到这个Pod。

![](img/1c2968a8a5067c92076a1e1fe5663144_22.png)

## 总结

![](img/1c2968a8a5067c92076a1e1fe5663144_23.png)

![](img/1c2968a8a5067c92076a1e1fe5663144_24.png)

本节课我们一起学习了Kubernetes的核心概念——Label。

![](img/1c2968a8a5067c92076a1e1fe5663144_25.png)

![](img/1c2968a8a5067c92076a1e1fe5663144_26.png)

我们了解到Label是一个用户自定义的键值对，用于给资源对象打上标识。它通过`metadata.labels`字段定义，并通过`spec.selector`字段被其他资源引用，从而实现灵活的资源分组和选择。Label本身不能独立使用，必须附加在如Pod、Service、RC等资源对象上，它是进行高效资源管理和服务编排的基础工具之一。