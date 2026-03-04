# 华为云PaaS微服务治理技术 - P60：13.Kubernetes集群搭建Node安装-kubelet服务

![](img/25588c03bb0cccfbe6016461cc739847_0.png)

![](img/25588c03bb0cccfbe6016461cc739847_0.png)

![](img/25588c03bb0cccfbe6016461cc739847_2.png)

![](img/25588c03bb0cccfbe6016461cc739847_3.png)

![](img/25588c03bb0cccfbe6016461cc739847_4.png)

在本节课程中，我们将学习如何在Kubernetes集群的Node节点上安装和配置kubelet服务。kubelet是运行在每个Node节点上的主要代理，负责管理Pod和容器的生命周期。

![](img/25588c03bb0cccfbe6016461cc739847_5.png)

![](img/25588c03bb0cccfbe6016461cc739847_7.png)

上一节我们完成了Node节点基础环境的准备，本节中我们来看看如何具体配置kubelet服务。

![](img/25588c03bb0cccfbe6016461cc739847_9.png)

![](img/25588c03bb0cccfbe6016461cc739847_10.png)

## 创建kubelet服务文件

![](img/25588c03bb0cccfbe6016461cc739847_12.png)

首先，我们需要创建一个systemd服务文件来管理kubelet服务。

![](img/25588c03bb0cccfbe6016461cc739847_14.png)

以下是创建服务文件的步骤：

![](img/25588c03bb0cccfbe6016461cc739847_16.png)

![](img/25588c03bb0cccfbe6016461cc739847_17.png)

1.  使用文本编辑器创建服务文件。
    ```bash
    vi /usr/lib/systemd/system/kubelet.service
    ```
2.  将讲义中提供的服务配置内容复制到该文件中。

![](img/25588c03bb0cccfbe6016461cc739847_18.png)

## 创建必要的目录和配置文件

![](img/25588c03bb0cccfbe6016461cc739847_20.png)

![](img/25588c03bb0cccfbe6016461cc739847_22.png)

在启动服务之前，需要确保kubelet工作所需的目录和配置文件存在。

以下是需要创建的目录和文件：

![](img/25588c03bb0cccfbe6016461cc739847_24.png)

1.  创建工作目录。
    ```bash
    mkdir -p /var/lib/kubelet
    ```
2.  创建Kubernetes配置目录。
    ```bash
    mkdir -p /etc/kubernetes
    ```
3.  在`/etc/kubernetes/`目录下创建kubelet的配置文件`kubelet.config`。
    ```bash
    vi /etc/kubernetes/kubelet.config
    ```
    在此文件中，需要填写Node节点的IP地址等配置信息。例如，如果Node节点的IP是`192.168.1.148`，则需相应配置。

![](img/25588c03bb0cccfbe6016461cc739847_25.png)

![](img/25588c03bb0cccfbe6016461cc739847_26.png)

## 配置kubelet连接Master节点

![](img/25588c03bb0cccfbe6016461cc739847_28.png)

![](img/25588c03bb0cccfbe6016461cc739847_29.png)

kubelet需要知道如何连接到集群的Master节点（API Server）。

以下是配置连接信息的步骤：

1.  创建kubelet连接配置文件。
    ```bash
    vi /etc/kubernetes/kubelet.conf
    ```
2.  在该文件中，需要指定Master节点的地址。例如，如果Master节点的IP是`192.168.1.147`，则配置文件内容应类似如下YAML格式：
    ```yaml
    apiVersion: v1
    clusters:
    - cluster:
        server: https://192.168.1.147:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: system:node:node01
      name: default
    current-context: default
    kind: Config
    users:
    - name: system:node:node01
      user:
        client-certificate: /etc/kubernetes/pki/kubelet-client-current.pem
        client-key: /etc/kubernetes/pki/kubelet-client-current.pem
    ```
    请注意，YAML格式对缩进敏感，必须确保正确的缩进层次。关键是将`server`字段的值修改为你实际的Master节点IP地址。

![](img/25588c03bb0cccfbe6016461cc739847_31.png)

完成上述配置后，kubelet服务的基本配置就完成了。接下来，我们还需要配置`kube-proxy`服务，这将在后续章节中介绍。

![](img/25588c03bb0cccfbe6016461cc739847_33.png)

![](img/25588c03bb0cccfbe6016461cc739847_34.png)

本节课中我们一起学习了在Kubernetes Node节点上配置kubelet服务的完整流程，包括创建服务文件、工作目录以及关键的连接配置文件。确保每一步的配置准确无误是Node节点成功加入集群的基础。