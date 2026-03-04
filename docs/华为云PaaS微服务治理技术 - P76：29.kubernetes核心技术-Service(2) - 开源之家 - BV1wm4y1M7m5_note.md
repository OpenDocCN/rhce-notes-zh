# 华为云PaaS微服务治理技术：P76：29. Kubernetes核心技术-Service(2) 🔧

在本节课中，我们将要学习Kubernetes Service的两种高级用法：多端口Service和外部服务Service。我们将了解如何为一个Pod定义多个服务端口，以及如何将Service指向集群外部的服务。

![](img/9d6f6a45c7c4567101c11922e880a045_1.png)

## 多端口Service 📡

![](img/9d6f6a45c7c4567101c11922e880a045_2.png)

![](img/9d6f6a45c7c4567101c11922e880a045_3.png)

![](img/9d6f6a45c7c4567101c11922e880a045_4.png)

上一节我们介绍了Service的基本概念和用法，本节中我们来看看如何配置多端口Service。有时一个容器应用可能需要提供多个端口的服务，例如同时提供Web服务和监控服务。在Service的定义中，可以设置多个端口映射来对应多个应用服务。

以下是多端口Service的配置示例：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-webapp
spec:
  selector:
    app: MyApp
  ports:
    - name: http
      port: 8080
      targetPort: 8680
    - name: admin
      port: 8005
      targetPort: 8005
```

![](img/9d6f6a45c7c4567101c11922e880a045_6.png)

![](img/9d6f6a45c7c4567101c11922e880a045_7.png)

![](img/9d6f6a45c7c4567101c11922e880a045_8.png)

在这个例子中，我们定义了一个名为`my-webapp`的Service。它在`spec.ports`下设置了两个端口映射：
*   `http`端口：将Service的8080端口映射到Pod容器的8680端口。
*   `admin`端口：将Service的8005端口映射到Pod容器的8005端口。

![](img/9d6f6a45c7c4567101c11922e880a045_10.png)

这个Service通过`selector`（`app: MyApp`）关联到具有相应标签的Pod。外部访问时，可以通过Service的IP或域名加上不同的端口号来访问Pod内不同的服务。配置多端口Service的方法很简单，就是在`ports`列表下定义多个`port`条目，并通过`targetPort`指定容器内的实际端口。

![](img/9d6f6a45c7c4567101c11922e880a045_11.png)

![](img/9d6f6a45c7c4567101c11922e880a045_12.png)

![](img/9d6f6a45c7c4567101c11922e880a045_13.png)

## 外部服务Service 🌐

![](img/9d6f6a45c7c4567101c11922e880a045_14.png)

接下来，我们探讨外部服务Service。在某些场景中，应用系统需要连接一个外部数据库作为后端服务，或者需要访问另一个集群或命名空间中的服务。这时，可以通过创建一个**无Selector**的Service，并结合Endpoint来实现。

一个常规的Service通过`selector`字段来匹配并关联一组Pod。而外部服务Service的核心在于**不定义`selector`**，这意味着Kubernetes不会自动为其创建和维护Endpoints列表。

![](img/9d6f6a45c7c4567101c11922e880a045_16.png)

![](img/9d6f6a45c7c4567101c11922e880a045_17.png)

以下是创建一个无Selector的Service的示例：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-external-service
spec:
  ports:
    - port: 80
```

![](img/9d6f6a45c7c4567101c11922e880a045_19.png)

如上所示，这个名为`my-external-service`的Service没有`selector`字段。如果我们此时查看它的Endpoints，会发现它是空的，因为没有关联任何Pod。

![](img/9d6f6a45c7c4567101c11922e880a045_20.png)

![](img/9d6f6a45c7c4567101c11922e880a045_21.png)

![](img/9d6f6a45c7c4567101c11922e880a045_22.png)

为了使这个Service能够指向一个具体的服务地址，我们需要**手动创建一个同名的Endpoint资源**。Endpoint资源用于定义一组网络端点（通常是IP地址和端口）。

以下是手动创建Endpoint的示例：

![](img/9d6f6a45c7c4567101c11922e880a045_24.png)

![](img/9d6f6a45c7c4567101c11922e880a045_25.png)

![](img/9d6f6a45c7c4567101c11922e880a045_26.png)

```yaml
apiVersion: v1
kind: Endpoints
metadata:
  # 名称必须与对应的Service名称一致
  name: my-external-service
subsets:
  - addresses:
      - ip: 192.168.1.100 # 外部服务的实际IP地址
    ports:
      - port: 8080 # 外部服务的实际端口
```

在这个Endpoint定义中：
*   `metadata.name`必须与前面创建的Service名称（`my-external-service`）完全一致，这样Kubernetes才会将它们关联起来。
*   `subsets.addresses.ip`指定了外部服务的真实IP地址（例如，一个外部数据库的IP）。
*   `subsets.ports.port`指定了外部服务的真实端口。

![](img/9d6f6a45c7c4567101c11922e880a045_28.png)

![](img/9d6f6a45c7c4567101c11922e880a045_29.png)

创建了这个Endpoint之后，当集群内的其他Pod访问Service `my-external-service:80` 时，流量就会被转发到Endpoint中定义的 `192.168.1.100:8080`。这就实现了Service对集群外部服务的抽象和访问。

![](img/9d6f6a45c7c4567101c11922e880a045_30.png)

![](img/9d6f6a45c7c4567101c11922e880a045_31.png)

![](img/9d6f6a45c7c4567101c11922e880a045_32.png)

## 总结 📝

![](img/9d6f6a45c7c4567101c11922e880a045_34.png)

![](img/9d6f6a45c7c4567101c11922e880a045_35.png)

本节课中我们一起学习了Kubernetes Service的两种高级配置。
*   我们首先了解了**多端口Service**，它允许一个Service定义多个端口映射，从而对外暴露Pod提供的多项服务。
*   接着，我们深入探讨了**外部服务Service**，通过创建无`selector`的Service并手动配置同名的Endpoint，可以将Service作为访问集群外部服务的统一入口。

![](img/9d6f6a45c7c4567101c11922e880a045_37.png)

![](img/9d6f6a45c7c4567101c11922e880a045_38.png)

掌握这些技巧，能够让你更灵活地运用Kubernetes Service来管理复杂的微服务网络访问。