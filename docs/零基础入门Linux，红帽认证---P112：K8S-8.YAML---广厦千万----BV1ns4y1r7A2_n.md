# Kubernetes入门教程：P112：K8S-8.YAML语法介绍、Pod概念、通过YAML文件创建一个Pod

![](img/271e715b76138f43abdc070cc5e51a52_0.png)

![](img/271e715b76138f43abdc070cc5e51a52_2.png)

![](img/271e715b76138f43abdc070cc5e51a52_4.png)

![](img/271e715b76138f43abdc070cc5e51a52_6.png)

![](img/271e715b76138f43abdc070cc5e51a52_8.png)

![](img/271e715b76138f43abdc070cc5e51a52_10.png)

![](img/271e715b76138f43abdc070cc5e51a52_12.png)

![](img/271e715b76138f43abdc070cc5e51a52_14.png)

![](img/271e715b76138f43abdc070cc5e51a52_16.png)

在本节课中，我们将要学习Kubernetes中至关重要的YAML语法，理解Pod的核心概念，并最终通过编写一个YAML文件来亲手创建一个Pod。这是管理Kubernetes资源的基础。

![](img/271e715b76138f43abdc070cc5e51a52_18.png)

## YAML语法介绍

上一节我们介绍了Kubernetes的基本管理命令，本节中我们来看看如何通过YAML文件来定义和创建资源。YAML是一种人性化的数据格式语言，在Kubernetes中，几乎所有的资源都需要通过YAML文件来创建和管理。

YAML语法强调以数据为中心，结构简洁，易于阅读和编写。它的核心特点如下：

*   **严格区分大小写**：在YAML文件中，大写和小写字母代表不同的含义。
*   **使用缩进表示层级关系**：通常使用空格（建议两个空格）进行缩进，不同层级的元素通过缩进来区分。
*   **相同层级左对齐**：属于同一层级的元素应该在左侧对齐。
*   **`#` 表示注释**：以 `#` 开头的行是注释，不会被解析。
*   **键值对使用 `:` 分隔**：在 `:` 后面需要跟一个空格，然后再写值。

以下是一个描述个人信息的YAML示例，它清晰地展示了层级关系：
```yaml
user:
  name: 张三
  age: 35
  address: 天津
```
在这个例子中，`user` 是一个对象（或称为映射、字典），它包含了 `name`、`age`、`address` 三个键值对。`name`、`age`、`address` 是 `user` 的子属性，通过两个空格的缩进来体现。

YAML支持两种主要的数据结构：**对象**和**数组**。

*   **对象**：一组键值对的集合，如上例中的 `user`。
*   **数组**：一组按次序排列的值，以 `-` 开头。

![](img/271e715b76138f43abdc070cc5e51a52_20.png)

![](img/271e715b76138f43abdc070cc5e51a52_22.png)

![](img/271e715b76138f43abdc070cc5e51a52_24.png)

一个YAML文件可以包含多段配置，它们之间使用 `---` 进行分隔。例如，可以在一个文件中定义两个毫无关联的资源。

![](img/271e715b76138f43abdc070cc5e51a52_26.png)

![](img/271e715b76138f43abdc070cc5e51a52_28.png)

![](img/271e715b76138f43abdc070cc5e51a52_30.png)

![](img/271e715b76138f43abdc070cc5e51a52_32.png)

![](img/271e715b76138f43abdc070cc5e51a52_34.png)

![](img/271e715b76138f43abdc070cc5e51a52_36.png)

## Pod核心概念

![](img/271e715b76138f43abdc070cc5e51a52_38.png)

![](img/271e715b76138f43abdc070cc5e51a52_40.png)

![](img/271e715b76138f43abdc070cc5e51a52_41.png)

![](img/271e715b76138f43abdc070cc5e51a52_43.png)

![](img/271e715b76138f43abdc070cc5e51a52_45.png)

理解了YAML语法后，我们来看看Kubernetes中最基本的调度单元——Pod。

![](img/271e715b76138f43abdc070cc5e51a52_47.png)

![](img/271e715b76138f43abdc070cc5e51a52_49.png)

![](img/271e715b76138f43abdc070cc5e51a52_51.png)

Pod是Kubernetes能够创建和管理的最小单元。你可以将它想象成一个“豆荚”（Pod的原意），而豆荚里的“豆子”就是容器。一个Pod内部可以运行一个或多个紧密相关的容器，这些容器共享网络、存储等资源。

![](img/271e715b76138f43abdc070cc5e51a52_53.png)

![](img/271e715b76138f43abdc070cc5e51a52_55.png)

**公式**：`Pod = 一个或多个 Container`

![](img/271e715b76138f43abdc070cc5e51a52_57.png)

![](img/271e715b76138f43abdc070cc5e51a52_58.png)

![](img/271e715b76138f43abdc070cc5e51a52_60.png)

![](img/271e715b76138f43abdc070cc5e51a52_62.png)

Pod为容器提供了一个运行环境，你可以将其视为一台“逻辑主机”。Kubernetes直接管理的是Pod，而不是Pod内部的容器。通过管理Pod的生命周期（创建、销毁），就能间接管理其中运行的容器。

![](img/271e715b76138f43abdc070cc5e51a52_64.png)

![](img/271e715b76138f43abdc070cc5e51a52_66.png)

## 通过YAML文件创建Pod

![](img/271e715b76138f43abdc070cc5e51a52_68.png)

![](img/271e715b76138f43abdc070cc5e51a52_70.png)

![](img/271e715b76138f43abdc070cc5e51a52_72.png)

现在，我们将结合YAML语法和Pod概念，动手创建一个Pod。在Kubernetes中，创建资源通常遵循一个固定的YAML结构。

![](img/271e715b76138f43abdc070cc5e51a52_74.png)

![](img/271e715b76138f43abdc070cc5e51a52_76.png)

![](img/271e715b76138f43abdc070cc5e51a52_78.png)

![](img/271e715b76138f43abdc070cc5e51a52_80.png)

以下是编写资源清单文件时常用的一级属性：

![](img/271e715b76138f43abdc070cc5e51a52_82.png)

![](img/271e715b76138f43abdc070cc5e51a52_84.png)

1.  **`apiVersion`**: 定义要使用的Kubernetes API版本。
2.  **`kind`**: 定义要创建的资源类型，例如 `Pod`、`Namespace`。
3.  **`metadata`**: 定义资源的元数据，如名称、标签等。
4.  **`spec`**: 定义资源的期望状态（规格），这是最核心的部分，包含了容器的镜像、端口等配置。

我们不需要记忆所有属性的写法，可以使用 `kubectl explain` 命令来查询某个资源支持的属性。例如，查询Pod的资源定义：`kubectl explain pod`。

![](img/271e715b76138f43abdc070cc5e51a52_86.png)

![](img/271e715b76138f43abdc070cc5e51a52_87.png)

![](img/271e715b76138f43abdc070cc5e51a52_89.png)

接下来，我们一步步创建一个静态Pod（自主式Pod，不受控制器管理）。

![](img/271e715b76138f43abdc070cc5e51a52_91.png)

![](img/271e715b76138f43abdc070cc5e51a52_93.png)

首先，创建一个名为 `pod-demo.yaml` 的文件，并写入以下内容：
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: static-pod-demo
  labels:
    app: myapp
spec:
  containers:
  - name: my-nginx-container
    image: nginx:1.20
```
**代码解释**：
*   `apiVersion: v1` 和 `kind: Pod` 表明我们要创建一个v1版本的Pod资源。
*   `metadata.name` 指定了这个Pod的名字。
*   `metadata.labels` 为Pod打上了一个标签，便于后续筛选和管理。
*   `spec.containers` 是一个数组，定义了Pod中运行的容器列表。这里定义了一个名为 `my-nginx-container` 的容器，使用 `nginx:1.20` 镜像。

![](img/271e715b76138f43abdc070cc5e51a52_95.png)

然后，使用 `kubectl apply` 命令（更常用）或 `kubectl create` 命令来创建这个Pod：
```bash
kubectl apply -f pod-demo.yaml
```
创建成功后，可以使用以下命令查看Pod状态：
```bash
kubectl get pods
kubectl describe pod static-pod-demo
```
要删除这个Pod，可以执行：
```bash
kubectl delete -f pod-demo.yaml
# 或者
kubectl delete pod static-pod-demo
```
这种静态Pod一旦被删除，就不会自动重建。在实际生产中，我们通常会使用Deployment、StatefulSet等**控制器**来管理Pod，它们能提供故障恢复、滚动更新等高级功能。

![](img/271e715b76138f43abdc070cc5e51a52_97.png)

![](img/271e715b76138f43abdc070cc5e51a52_99.png)

## 总结

![](img/271e715b76138f43abdc070cc5e51a52_101.png)

![](img/271e715b76138f43abdc070cc5e51a52_103.png)

![](img/271e715b76138f43abdc070cc5e51a52_104.png)

![](img/271e715b76138f43abdc070cc5e51a52_106.png)

本节课中我们一起学习了Kubernetes资源管理的基石。我们首先介绍了YAML语法的基本规则和结构，然后理解了Pod作为容器组的核心概念。最后，我们通过一个完整的示例，学会了如何编写一个YAML格式的Pod资源清单文件，并使用 `kubectl` 命令将其创建到集群中。掌握这些知识，是进一步学习各种Kubernetes控制器和高级编排概念的关键第一步。