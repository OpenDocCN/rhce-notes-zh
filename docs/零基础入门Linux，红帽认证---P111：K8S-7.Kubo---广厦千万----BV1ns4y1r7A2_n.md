# Kubernetes入门教程：P111：Kubernetes资源管理 - Namespace与Kuboard部署

![](img/ee021fb5b2294acd8ed69ad7e977df4f_1.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_3.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_5.png)

在本节课中，我们将学习Kubernetes中一个重要的资源——**Namespace（名称空间）**，并部署一个名为**Kuboard**的图形化管理平台，以简化集群的管理操作。

![](img/ee021fb5b2294acd8ed69ad7e977df4f_7.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_9.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_11.png)

## 什么是Namespace？🏠

![](img/ee021fb5b2294acd8ed69ad7e977df4f_13.png)

上一节我们介绍了Kubernetes的基本概念，本节中我们来看看**Namespace**。Namespace是Kubernetes中的一个核心资源，其主要作用是实现**资源隔离**。

![](img/ee021fb5b2294acd8ed69ad7e977df4f_15.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_17.png)

你可以将Namespace想象成现实生活中的房间。一个大房子需要被合理地划分成卧室、厨房、书房等不同空间，以便有效使用。同样，在Kubernetes集群中，Namespace可以将不同的Pod划分到不同的逻辑组中，实现隔离。

例如，一个集群可能运行着多个毫无关联的应用。通过创建不同的Namespace，可以将这些应用隔离开来，比如：
*   在名为 `app1` 的Namespace中运行APP1的业务程序。
*   在名为 `app2` 的Namespace中运行APP2的业务程序。

![](img/ee021fb5b2294acd8ed69ad7e977df4f_19.png)

这样，不同业务之间的资源就被逻辑隔离了。

![](img/ee021fb5b2294acd8ed69ad7e977df4f_21.png)

## 查看与管理Namespace

![](img/ee021fb5b2294acd8ed69ad7e977df4f_23.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_25.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_27.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_29.png)

在Kubernetes中，Namespace资源的全称是 `namespace`，其缩写为 `ns`。

![](img/ee021fb5b2294acd8ed69ad7e977df4f_31.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_33.png)

以下是查看和管理Namespace的基本操作：

![](img/ee021fb5b2294acd8ed69ad7e977df4f_35.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_37.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_39.png)

**1. 查看集群中所有Namespace**
使用 `kubectl get` 命令可以获取资源信息。要查看所有Namespace，命令如下：
```bash
kubectl get ns
```
执行后，你会看到类似以下的输出，这些是集群初始化时自动创建的一些Namespace：
```
NAME              STATUS   AGE
calico-apiserver   Active   5d
calico-system      Active   5d
default           Active   5d
kube-node-lease   Active   5d
kube-public       Active   5d
kube-system       Active   5d
```

**2. 查看特定Namespace下的资源**
如果你想查看某个Namespace（例如 `kube-system`）下运行的所有Pod，可以使用 `-n` 或 `--namespace` 参数来指定Namespace：
```bash
kubectl get pod -n kube-system
```

## 默认Namespace详解

![](img/ee021fb5b2294acd8ed69ad7e977df4f_41.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_43.png)

以下是Kubernetes集群中几个重要的默认Namespace及其作用：

![](img/ee021fb5b2294acd8ed69ad7e977df4f_45.png)

*   **`default`**：这是**默认的Namespace**。所有未明确指定Namespace的Pod等资源都会被创建在这个空间里。
*   **`kube-system`**：这个Namespace包含Kubernetes**集群的核心组件**，如 `kube-apiserver`、`kube-controller-manager` 等。
*   **`kube-public`**：这是一个**公共Namespace**，该空间下的资源可以被所有人（包括未经认证的用户）访问。通常不存放实际业务Pod。
*   **`kube-node-lease`**：此Namespace用于存放**集群节点间的心跳信息**，以提高节点状态检测的效率。
*   **`calico-system` / `calico-apiserver`**：这些是**网络插件Calico**所使用的Namespace，用于运行其相关的Pod。

![](img/ee021fb5b2294acd8ed69ad7e977df4f_47.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_49.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_51.png)

## 部署Kuboard图形化管理平台🖥️

![](img/ee021fb5b2294acd8ed69ad7e977df4f_53.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_55.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_57.png)

通过命令行管理集群虽然强大，但对初学者不够直观。接下来，我们将部署一个名为 **Kuboard** 的图形化管理界面，它能极大地简化集群管理操作。

![](img/ee021fb5b2294acd8ed69ad7e977df4f_58.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_59.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_60.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_62.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_64.png)

Kuboard是一个专为Kubernetes设计的免费管理界面，提供中文支持和丰富的教程。

![](img/ee021fb5b2294acd8ed69ad7e977df4f_66.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_68.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_69.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_71.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_73.png)

### 部署前提

![](img/ee021fb5b2294acd8ed69ad7e977df4f_74.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_75.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_77.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_79.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_81.png)

部署Kuboard需要准备一台独立的Linux服务器（虚拟机即可），并满足以下条件：
1.  已安装Docker，且版本不低于19.03。
2.  拥有一个Kubernetes集群（版本不低于1.13）。

![](img/ee021fb5b2294acd8ed69ad7e977df4f_83.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_85.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_87.png)

### 安装步骤

![](img/ee021fb5b2294acd8ed69ad7e977df4f_89.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_91.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_93.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_95.png)

我们将在新准备的服务器（IP: 192.168.0.206）上安装Kuboard。

![](img/ee021fb5b2294acd8ed69ad7e977df4f_97.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_99.png)

**1. 执行安装命令**
Kuboard提供了通过Docker一键部署的命令。在目标服务器上执行以下命令（请将 `192.168.0.206` 替换为你服务器的内网IP）：
```bash
sudo docker run -d \
  --restart=unless-stopped \
  --name=kuboard \
  -p 80:80/tcp \
  -p 10081:10081/tcp \
  -e KUBOARD_ENDPOINT="http://192.168.0.206:80" \
  -e KUBOARD_AGENT_SERVER_TCP_PORT="10081" \
  -v /root/kuboard-data:/data \
  eipwork/kuboard:v3
```
此命令会拉取Kuboard镜像并以容器方式运行。

**2. 访问Kuboard界面**
安装完成后，在浏览器中访问 `http://你的服务器IP`（例如 `http://192.168.0.206`）。
*   默认用户名：`admin`
*   默认密码：`Kuboard123` (注意K是大写)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_101.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_103.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_105.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_107.png)

### 将Kubernetes集群导入Kuboard

![](img/ee021fb5b2294acd8ed69ad7e977df4f_108.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_110.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_112.png)

首次登录后，界面中没有集群，需要将我们已有的集群导入。

以下是导入集群的步骤：
1.  点击 **“添加集群”**。
2.  选择 **“Agent”** 方式。
3.  填写集群名称（如 `my-k8s-cluster`）和描述。
4.  点击确定后，页面会生成两条需要在你的Kubernetes **Master节点**上执行的命令。
5.  在Master节点上依次执行这两条命令。它们会在你的集群中部署一个Kuboard Agent（运行在 `kuboard` 这个Namespace下），用于Kuboard界面与集群通信。
6.  命令执行成功后，回到Kuboard界面，选择使用 `kuboard-admin` 身份访问集群。
7.  导入完成，点击集群名称即可进入管理界面。

![](img/ee021fb5b2294acd8ed69ad7e977df4f_114.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_116.png)

### Kuboard界面初览

成功导入集群后，你可以在Kuboard界面中：
*   **集群概览**：查看节点数量、状态、集群版本等信息。
*   **节点列表**：查看所有Worker和Master节点的详细状态。
*   **Namespace管理**：直观地查看和管理所有Namespace。
*   **资源查看**：点击任一Namespace，可以查看其下运行的Pod（容器组）、Deployment等工作负载的详细信息，包括状态、所在节点、重启次数等，比命令行更加清晰直观。

![](img/ee021fb5b2294acd8ed69ad7e977df4f_118.png)

后续我们操作Kubernetes资源时，可以结合使用命令行和Kuboard界面，让学习和管理过程更高效。

![](img/ee021fb5b2294acd8ed69ad7e977df4f_120.png)

---

![](img/ee021fb5b2294acd8ed69ad7e977df4f_122.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_124.png)

![](img/ee021fb5b2294acd8ed69ad7e977df4f_126.png)

本节课中我们一起学习了Kubernetes的**Namespace资源**，它通过逻辑隔离帮助我们在集群内组织和管理应用。同时，我们成功部署了**Kuboard图形化管理平台**，为后续可视化操作Kubernetes集群打下了基础。从下一节课开始，我们将深入讲解YAML语法，学习如何通过声明式文件来创建和管理Kubernetes的各种资源。