# Kubernetes入门教程：P110：K8S-6：kubectl集群管理命令详解 🛠️

![](img/12c69f9c29ca85b6c5bab95e496e470d_1.png)

在本节课中，我们将学习Kubernetes的核心管理工具 `kubectl` 的基本使用方法。`kubectl` 是与Kubernetes集群进行交互的命令行工具，掌握它是管理集群资源的第一步。

## 概述：Kubernetes与资源管理

![](img/12c69f9c29ca85b6c5bab95e496e470d_3.png)

Kubernetes的本质是一个容器集群的统一管理系统。用户可以在集群中部署各种服务，这些服务本质上就是运行在容器中的应用程序。因此，Kubernetes的核心功能是管理容器集群。

![](img/12c69f9c29ca85b6c5bab95e496e470d_5.png)

在Kubernetes中，所有被管理的实体都被抽象为**资源对象**。学习Kubernetes，很大程度上就是学习如何管理这些资源对象。

![](img/12c69f9c29ca85b6c5bab95e496e470d_7.png)

要查看Kubernetes支持的所有资源对象，可以使用以下命令：
```bash
kubectl api-resources
```
该命令会列出所有资源对象的名称、缩写和API版本。资源种类繁多，作为初学者，我们首先学习最常用和核心的资源。

![](img/12c69f9c29ca85b6c5bab95e496e470d_9.png)

![](img/12c69f9c29ca85b6c5bab95e496e470d_11.png)

## kubectl命令基础

![](img/12c69f9c29ca85b6c5bab95e496e470d_13.png)

上一节我们介绍了Kubernetes的资源概念，本节中我们来看看管理这些资源的核心工具 `kubectl`。

![](img/12c69f9c29ca85b6c5bab95e496e470d_15.png)

`kubectl` 是管理Kubernetes集群的命令行客户端。使用它主要有两种方式：
1.  直接在命令行中执行操作。
2.  将资源配置写入YAML文件，然后通过 `kubectl` 应用该文件。

![](img/12c69f9c29ca85b6c5bab95e496e470d_17.png)

### 命令格式与帮助

`kubectl` 的命令遵循一个基本格式：
```
kubectl [command] [TYPE] [NAME] [flags]
```
*   `[command]`：指定要对资源执行的操作，例如 `create`， `get`， `delete`。
*   `[TYPE]`：指定资源类型（如 `pod`， `node`），可以使用全称或缩写。
*   `[NAME]`：指定具体资源的名称（可选）。
*   `[flags]`：指定可选的标志，例如 `-o yaml` 指定输出格式。

要查看所有可用命令，可以运行：
```bash
kubectl --help
```

### 常用命令列表

以下是 `kubectl` 最常用的一些命令，初学者应重点掌握：

*   `create`：从文件或标准输入创建资源。
*   `get`：列出一个或多个资源。
*   `edit`：编辑服务器上的资源。
*   `delete`：通过文件名、标准输入、资源名或标签选择器删除资源。
*   `explain`：查看资源的文档字段。
*   `describe`：显示资源的详细状态。
*   `logs`：输出Pod中容器的日志。
*   `apply`：通过文件名或标准输入对资源进行配置（创建或更新）。
*   `version`：显示客户端和服务器版本信息。

![](img/12c69f9c29ca85b6c5bab95e496e470d_19.png)

![](img/12c69f9c29ca85b6c5bab95e496e470d_21.png)

## 命令实战演练

了解了命令格式后，我们通过一些具体例子来加深理解。

![](img/12c69f9c29ca85b6c5bab95e496e470d_23.png)

### 查看集群信息

![](img/12c69f9c29ca85b6c5bab95e496e470d_25.png)

首先，我们可以查看集群的版本：
```bash
kubectl version
```
这会显示客户端(`kubectl`)和Kubernetes服务器的版本。

### 查看与获取资源

`get` 命令是最常用的查看命令。

![](img/12c69f9c29ca85b6c5bab95e496e470d_27.png)

查看集群中的所有节点：
```bash
kubectl get nodes
# 或使用缩写
kubectl get no
```

![](img/12c69f9c29ca85b6c5bab95e496e470d_29.png)

![](img/12c69f9c29ca85b6c5bab95e496e470d_31.png)

查看所有命名空间：
```bash
kubectl get namespaces
# 或使用缩写
kubectl get ns
```

![](img/12c69f9c29ca85b6c5bab95e496e470d_33.png)

![](img/12c69f9c29ca85b6c5bab95e496e470d_35.png)

查看指定命名空间（例如 `kube-system`）：
```bash
kubectl get ns kube-system
```

![](img/12c69f9c29ca85b6c5bab95e496e470d_37.png)

查看所有Pod：
```bash
kubectl get pods
# 或使用缩写
kubectl get po
```

### 使用输出格式标志 (`flags`)

![](img/12c69f9c29ca85b6c5bab95e496e470d_39.png)

默认的 `get` 输出信息较简略。使用 `-o` (output) 标志可以获取更详细或不同格式的信息。

以扩展格式查看Pod详情（显示IP、节点等）：
```bash
kubectl get pod <pod-name> -o wide
```

以YAML格式查看资源配置：
```bash
kubectl get pod <pod-name> -o yaml
```

![](img/12c69f9c29ca85b6c5bab95e496e470d_41.png)

以JSON格式查看资源配置：
```bash
kubectl get pod <pod-name> -o json
```
JSON和YAML格式包含了资源的完整定义，常用于调试和了解资源结构。

![](img/12c69f9c29ca85b6c5bab95e496e470d_43.png)

## 补充说明与技巧

### 命令自动补全

为了提高效率，可以为 `kubectl` 安装命令自动补全功能（例如bash-completion）。安装后，可以自动补全命令、资源类型和资源名称。但鉴于初学阶段命令相对简单，此步骤可选。

![](img/12c69f9c29ca85b6c5bab95e496e470d_45.png)

### 在工作节点使用kubectl

默认情况下，`kubectl` 命令只在主节点（Master）配置好。如果需要在工作节点（Worker Node）上使用，可以将主节点上 `/root/.kube/config` 配置文件复制到工作节点的相同位置。但通常这不是必须的，管理工作集中在主节点进行即可。

![](img/12c69f9c29ca85b6c5bab95e496e470d_47.png)

## 总结

![](img/12c69f9c29ca85b6c5bab95e496e470d_49.png)

本节课中我们一起学习了Kubernetes集群管理命令 `kubectl` 的核心用法。
*   我们理解了Kubernetes“一切皆资源”的核心思想。
*   我们掌握了 `kubectl` 命令的基本格式：`kubectl [command] [TYPE] [NAME] [flags]`。
*   我们熟悉了 `create`， `get`， `delete`， `apply` 等最常用的命令。
*   我们通过实战演练，学会了如何查看集群版本、节点、命名空间和Pod，并使用 `-o wide`， `-o yaml` 等标志控制输出格式。

![](img/12c69f9c29ca85b6c5bab95e496e470d_51.png)

熟练掌握 `kubectl` 是操控Kubernetes集群的基石。下一节，我们将开始学习第一个具体的资源对象——**命名空间（Namespace）**。