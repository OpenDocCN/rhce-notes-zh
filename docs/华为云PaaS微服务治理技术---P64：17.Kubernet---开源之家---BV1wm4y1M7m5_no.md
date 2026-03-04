# 华为云PaaS微服务治理技术：P64：17.Kubernetes集群健康检查与测试(1) 🩺

在本节课中，我们将学习如何检查Kubernetes集群的健康状态，并部署一个简单的Nginx应用进行测试。我们将从修复一个命令缺失问题开始，逐步完成集群状态检查和应用的创建与验证。

![](img/681d1abc1fe644d30575c86688f80745_1.png)

---

![](img/681d1abc1fe644d30575c86688f80745_3.png)

![](img/681d1abc1fe644d30575c86688f80745_4.png)

## 集群状态检查 🔍

上一节我们完成了Kubernetes集群的搭建。本节中，我们首先来检查集群的运行状态。

执行 `kubectl get node` 命令可以查看集群中的节点状态。但在执行时，可能会遇到命令未找到的错误。

```bash
kubectl get node
```

![](img/681d1abc1fe644d30575c86688f80745_6.png)

![](img/681d1abc1fe644d30575c86688f80745_7.png)

出现该问题的原因是，在搭建Master节点时，`kubectl` 命令文件可能未被正确复制到系统路径下。

![](img/681d1abc1fe644d30575c86688f80745_8.png)

![](img/681d1abc1fe644d30575c86688f80745_10.png)

以下是解决步骤：
1.  进入Kubernetes命令所在的目录：`/usr/local/kubernetes/bin`。
2.  将 `kubectl` 命令复制到 `/usr/bin` 目录下。
3.  再次执行 `kubectl get node` 命令。

成功执行后，将看到类似以下的输出，表明已成功识别到两个Node节点（148和149）：
```
NAME    STATUS   ROLES    AGE   VERSION
node1   Ready    <none>   10m   v1.xx.x
node2   Ready    <none>   10m   v1.xx.x
```

![](img/681d1abc1fe644d30575c86688f80745_12.png)

如果执行后仍显示 `No resources found`，则需要按照前期快速入门中的指导，检查并修改Kubernetes的配置文件。

![](img/681d1abc1fe644d30575c86688f80745_13.png)

![](img/681d1abc1fe644d30575c86688f80745_14.png)

![](img/681d1abc1fe644d30575c86688f80745_15.png)

接下来，我们检查Master组件自身的状态。

![](img/681d1abc1fe644d30575c86688f80745_17.png)

执行以下命令可以查看集群控制平面的组件状态：
```bash
kubectl get cs
```
命令输出显示所有组件均为健康状态，表明整个Kubernetes集群运行正常。

---

![](img/681d1abc1fe644d30575c86688f80745_19.png)

## 部署测试应用 🚀

![](img/681d1abc1fe644d30575c86688f80745_20.png)

在确认集群健康后，我们将部署一个简单的Nginx应用进行功能测试。这需要创建两个核心的Kubernetes资源对象：ReplicationController和Service。

![](img/681d1abc1fe644d30575c86688f80745_22.png)

首先，在 `/usr/local/kubernetes` 目录下，创建一个定义ReplicationController的YAML文件。

![](img/681d1abc1fe644d30575c86688f80745_23.png)

以下是创建名为 `nginx-rc.yaml` 文件的命令和其核心内容：
```bash
vi nginx-rc.yaml
```
文件内容定义了应用的基本信息：
*   **apiVersion** 和 **kind**： 指定资源类型为ReplicationController。
*   **replicas**： 设定Pod副本数量为3个，确保高可用。
*   **containers**： 定义使用 `nginx` 镜像，并将容器内的80端口暴露出来。

![](img/681d1abc1fe644d30575c86688f80745_25.png)

接着，创建定义Service的YAML文件，用于为Nginx Pods提供稳定的网络访问入口。

![](img/681d1abc1fe644d30575c86688f80745_26.png)

以下是创建名为 `nginx-svc.yaml` 文件的命令：
```bash
vi nginx-svc.yaml
```
文件内容定义了服务的网络信息：
*   **kind**： 指定资源类型为Service。
*   **type**： 设置为 `NodePort`，这意味着Kubernetes将在每个节点上开放一个端口（30000-32767范围），将流量转发到Service。
*   **port** 和 **nodePort**： 将Service的80端口映射到节点的33333端口。

![](img/681d1abc1fe644d30575c86688f80745_28.png)

---

![](img/681d1abc1fe644d30575c86688f80745_30.png)

## 应用部署与验证 ✅

![](img/681d1abc1fe644d30575c86688f80745_32.png)

现在，我们使用定义好的YAML文件来创建应用资源。

![](img/681d1abc1fe644d30575c86688f80745_34.png)

首先，创建ReplicationController来启动Nginx Pods：
```bash
kubectl create -f nginx-rc.yaml
```

然后，创建Service来暴露这些Pods：
```bash
kubectl create -f nginx-svc.yaml
```

资源创建完成后，我们可以查看Pod的运行状态。使用以下命令列出所有Pod：
```bash
kubectl get pods
```
如果此时显示 `No resources found`，通常是因为Pod尚未完成创建和调度，请稍等片刻再次查询，直到看到三个状态为 `Running` 的Nginx Pod。

![](img/681d1abc1fe644d30575c86688f80745_36.png)

---

![](img/681d1abc1fe644d30575c86688f80745_38.png)

本节课中我们一起学习了Kubernetes集群的基本健康检查方法，包括查看节点状态和控制平面组件状态。随后，我们通过编写YAML文件，定义并成功部署了一个包含三个副本的Nginx应用及其Service，完成了从集群检查到应用部署的完整测试流程。