# Kubernetes入门教程：P109：K8S集群初始化与测试 🚀

![](img/9e6f9da26b99ef92acb4bd895624a494_1.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_3.png)

在本节课中，我们将学习如何初始化一个Kubernetes高可用集群，并部署网络插件和测试应用，以验证集群是否正常工作。

![](img/9e6f9da26b99ef92acb4bd895624a494_5.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_6.png)

## 集群初始化

![](img/9e6f9da26b99ef92acb4bd895624a494_8.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_10.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_12.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_14.png)

上一节我们介绍了Kubernetes集群的准备工作，本节中我们来看看如何使用配置文件初始化集群。

![](img/9e6f9da26b99ef92acb4bd895624a494_16.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_18.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_20.png)

通过配置文件初始化集群，可以更新集群的token，这属于集群自身的密钥。我们后续会有专门讲解集群密钥的部分。

![](img/9e6f9da26b99ef92acb4bd895624a494_22.png)

以下是初始化集群的命令：
```bash
kubeadm init --config kubeadm-config.yaml
```

![](img/9e6f9da26b99ef92acb4bd895624a494_24.png)

对于`kubeadm`工具，它有很多命令。如果部署单节点Master集群，可以通过`--config`选项在命令行中初始化集群。但在公司环境中，单节点集群没有实际用途。因此，部署高可用集群通常需要生成配置文件。这个配置文件也是通过命令生成的，`kubeadm`有一个`config`选项可以帮助我们生成集群配置文件。

我们已经根据前面的环境修改好了配置文件。后续只需要按照自己的环境，将VIP地址修改为自己的即可。

![](img/9e6f9da26b99ef92acb4bd895624a494_26.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_27.png)

## 镜像准备

![](img/9e6f9da26b99ef92acb4bd895624a494_29.png)

这个命令需要下载镜像，在线下载速度较慢。因此，我们提前准备了镜像。

我们使用阿里云的镜像。这个镜像是从Google官方仓库下载后，通过`docker save`命令手动打包导出的。有了镜像之后，可以将其打包保存，后续需要时直接导入即可。

![](img/9e6f9da26b99ef92acb4bd895624a494_31.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_33.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_35.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_37.png)

以下是导入镜像的命令（在每个节点上执行）：
```bash
docker load -i k8s-images.tar.gz
```

![](img/9e6f9da26b99ef92acb4bd895624a494_39.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_41.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_43.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_45.png)

对于大量服务器的批量管理，我们使用Ansible工具。在运维工作中，最常用的模块是`shell`模块和`copy`模块，足以满足大部分工作需求。

## 集群初始化执行

镜像导入完成后，我们就可以在Master01节点上执行初始化命令了。

执行以下命令：
```bash
kubeadm init --config kubeadm-config.yaml
```

如果这一步报错，通常是环境部署不正确导致的。因为我们有本地镜像，所以集群初始化速度很快。如果在线下载镜像，速度会非常慢。

![](img/9e6f9da26b99ef92acb4bd895624a494_47.png)

集群初始化成功后，会显示“Your Kubernetes control-plane has initialized successfully!”。

## 集群配置

![](img/9e6f9da26b99ef92acb4bd895624a494_49.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_51.png)

初始化成功后，系统会提示执行以下命令来开始使用集群：

![](img/9e6f9da26b99ef92acb4bd895624a494_53.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_55.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_57.png)

1.  创建配置文件目录并复制配置文件：
    ```bash
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    ```
2.  声明环境变量（可选）：
    ```bash
    export KUBECONFIG=/etc/kubernetes/admin.conf
    ```

## 节点加入集群

![](img/9e6f9da26b99ef92acb4bd895624a494_59.png)

接下来，需要让其他Master节点和工作节点加入集群。

以下是Master节点加入集群的命令（在Master02和Master03上执行）：
```bash
kubeadm join <VIP>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash> --control-plane
```

以下是工作节点加入集群的命令（在Worker01和Worker02上执行）：
```bash
kubeadm join <VIP>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

![](img/9e6f9da26b99ef92acb4bd895624a494_61.png)

加入集群后，可以在Master节点上使用以下命令查看节点状态：
```bash
kubectl get nodes
```

![](img/9e6f9da26b99ef92acb4bd895624a494_63.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_65.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_67.png)

如果工作节点想执行`kubectl`命令，需要将Master节点`$HOME/.kube`目录下的配置文件拷贝过去，但通常工作节点不需要执行管理命令。

![](img/9e6f9da26b99ef92acb4bd895624a494_69.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_71.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_73.png)

## 集群重置

如果集群初始化失败，不一定需要恢复快照重新部署。可以在初始化失败的节点上执行重置命令，排错后再重新初始化。
```bash
kubeadm reset
```
这个命令会删除节点上生成的配置，然后可以重新排错和初始化。

![](img/9e6f9da26b99ef92acb4bd895624a494_75.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_77.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_79.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_81.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_83.png)

如果工作节点加入集群时提示token过期，可以在Master节点上重新生成token：
```bash
kubeadm token create
```
然后将新生成的token替换到加入集群的命令中即可。

![](img/9e6f9da26b99ef92acb4bd895624a494_85.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_87.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_88.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_90.png)

## 部署集群网络插件

![](img/9e6f9da26b99ef92acb4bd895624a494_92.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_94.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_96.png)

集群初始化完成后，节点状态可能显示`NotReady`，这是因为集群还没有网络。Kubernetes需要能够跨主机通信的网络。

![](img/9e6f9da26b99ef92acb4bd895624a494_98.png)

Kubernetes提供了很多网络插件，行业中使用较多的是Calico和Flannel。

![](img/9e6f9da26b99ef92acb4bd895624a494_100.png)

*   **Flannel**：功能相对简单，是一个二层网络，复用了Docker0网桥。
*   **Calico**：是一个纯三层的虚拟网络，具备更复杂的网络管理能力，在节点数量多或网络策略复杂的场景下更有优势。

![](img/9e6f9da26b99ef92acb4bd895624a494_102.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_104.png)

我们选择部署Calico网络插件。

以下是部署Calico的步骤：

![](img/9e6f9da26b99ef92acb4bd895624a494_106.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_108.png)

1.  安装Calico所需的资源定义：
    ```bash
    kubectl apply -f https://docs.projectcalico.org/manifests/tigera-operator.yaml
    ```
2.  下载自定义资源配置文件并修改：
    ```bash
    wget https://docs.projectcalico.org/manifests/custom-resources.yaml
    ```
    编辑`custom-resources.yaml`文件，将`spec.calicoNetwork.ipPools[0].cidr`的值修改为初始化配置文件`kubeadm-config.yaml`中定义的Pod网络地址（例如：`192.168.0.0/16`），确保两者一致。
3.  应用自定义资源配置：
    ```bash
    kubectl apply -f custom-resources.yaml
    ```

![](img/9e6f9da26b99ef92acb4bd895624a494_110.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_112.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_114.png)

部署后，需要移除Master节点上的污点，以允许Calico的Pod调度到Master节点上运行：
```bash
kubectl taint nodes --all node-role.kubernetes.io/master-
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```

![](img/9e6f9da26b99ef92acb4bd895624a494_116.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_118.png)

执行完毕后，可以查看Calico命名空间下的Pod状态，确认网络插件正常运行：
```bash
kubectl get pods -n calico-system
```

![](img/9e6f9da26b99ef92acb4bd895624a494_120.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_122.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_124.png)

当所有节点状态变为`Ready`时，集群网络就部署完成了。

![](img/9e6f9da26b99ef92acb4bd895624a494_126.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_128.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_130.png)

## 验证集群功能

![](img/9e6f9da26b99ef92acb4bd895624a494_132.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_134.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_136.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_138.png)

集群部署完成后，我们可以通过部署一个测试应用来验证其功能。

![](img/9e6f9da26b99ef92acb4bd895624a494_140.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_142.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_144.png)

1.  将测试镜像（例如一个Nginx镜像）导入到工作节点。
2.  在Master节点上部署一个Nginx应用：
    ```bash
    kubectl create deployment nginx-test --image=your-nginx-image:tag
    ```
3.  为Nginx服务暴露一个NodePort端口，以便从外部访问：
    ```bash
    kubectl expose deployment nginx-test --port=80 --type=NodePort
    ```
4.  查看服务暴露的端口：
    ```bash
    kubectl get svc nginx-test
    ```
5.  使用浏览器或`curl`命令访问集群**任意节点**的IP地址和上一步查到的端口（例如`<节点IP>:31452`），如果能看到Nginx的欢迎页面，说明集群部署成功且应用可以正常访问。

## 总结

![](img/9e6f9da26b99ef92acb4bd895624a494_146.png)

![](img/9e6f9da26b99ef92acb4bd895624a494_148.png)

本节课中我们一起学习了Kubernetes高可用集群的完整初始化流程。我们首先使用`kubeadm`和预定义的配置文件初始化了集群，然后让其他Master节点和工作节点加入集群。接着，我们部署了Calico网络插件来解决集群的网络通信问题，并通过移除Master节点污点确保了网络组件的正常运行。最后，我们通过部署一个简单的Nginx应用并成功从外部访问，验证了整个集群的功能完整性。至此，一个可以投入使用的Kubernetes集群就部署完成了。