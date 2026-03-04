# Kubernetes容器集群管理：P4：安装Kubernetes Dashboard与节点加入集群

在本节中，我们将学习如何为Kubernetes集群安装一个Web管理界面——Kubernetes Dashboard，并演示如何将新的工作节点加入到集群中。通过图形化界面，可以更直观地管理和监控集群资源。

![](img/ecf26cfd01123273be2884d964b75896_1.png)

## 安装Kubernetes Dashboard

上一节我们完成了集群的初始化，本节我们来看看如何部署Dashboard。首先需要获取其部署配置文件。

![](img/ecf26cfd01123273be2884d964b75896_3.png)

![](img/ecf26cfd01123273be2884d964b75896_4.png)

由于官方资源可能无法直接访问，建议将整个YAML页面内容保存或复制到本地。在应用配置文件前，需要修改一项关键配置。

![](img/ecf26cfd01123273be2884d964b75896_6.png)

默认情况下，Dashboard服务没有设置NodePort的端口映射范围。我们需要修改配置文件，将NodePort的起始端口设置为30000。修改位置通常在文件的第42行附近，需要添加以下内容：

![](img/ecf26cfd01123273be2884d964b75896_8.png)

```yaml
  type: NodePort
  ports:
    - port: 443
      targetPort: 8443
      nodePort: 30000
```

![](img/ecf26cfd01123273be2884d964b75896_10.png)

**注意**：YAML文件对格式（特别是空格缩进）要求严格。新增的`nodePort`配置项必须与上方的`port`和`targetPort`对齐。修改后的效果应确保`type`、`ports`等层级关系正确。

配置文件修改完成后，即可应用它来创建Dashboard所需的资源。

以下是应用配置文件的命令：
```bash
kubectl apply -f recommended.yaml
```
此命令会根据YAML文件创建命名空间、Deployment、Service等资源。如果拥有离线镜像，Pod会很快进入运行状态；否则从网络拉取镜像可能较慢。

![](img/ecf26cfd01123273be2884d964b75896_12.png)

部署完成后，可以查看Dashboard相关的Pod状态。
```bash
kubectl get pods -n kubernetes-dashboard
```
当Pod状态显示为`Running`时，表示部署成功。

![](img/ecf26cfd01123273be2884d964b75896_13.png)

![](img/ecf26cfd01123273be2884d964b75896_15.png)

![](img/ecf26cfd01123273be2884d964b75896_16.png)

## 配置Dashboard访问凭证

![](img/ecf26cfd01123273be2884d964b75896_18.png)

Dashboard部署好后，需要获取访问令牌（Token）进行登录。我们创建一个服务账户并绑定集群管理员角色来获取令牌。

首先，创建一个包含服务账户和集群角色绑定的YAML配置文件，或直接使用命令追加配置。以下是配置内容示例：

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

应用此配置后，即可获取该服务账户的令牌。

![](img/ecf26cfd01123273be2884d964b75896_20.png)

![](img/ecf26cfd01123273be2884d964b75896_22.png)

获取登录令牌的命令如下：
```bash
kubectl describe secret -n kubernetes-dashboard $(kubectl get secret -n kubernetes-dashboard | grep admin-user | awk '{print $1}')
```
在输出信息中找到`token:`字段后面的值，即为登录所需的令牌。

![](img/ecf26cfd01123273be2884d964b75896_24.png)

![](img/ecf26cfd01123273be2884d964b75896_26.png)

## 访问Dashboard并加入工作节点

现在可以通过浏览器访问Dashboard。访问地址为`https://<Master节点IP>:30000`。首次访问会提示证书不安全，选择继续访问即可。在登录界面选择“Token”方式，并粘贴上一步获取的令牌。

登录后，在Dashboard中可能只看到Master节点。为了构建一个多节点集群，需要将其他节点作为Worker加入。

还记得在Master节点初始化完成后输出的`kubeadm join`命令吗？该命令用于将新节点注册到集群。在需要加入集群的节点（如node1）上，确保已安装Docker、kubelet、kubeadm等必要组件，然后执行该命令。

节点加入命令格式类似如下：
```bash
kubeadm join <Master节点IP>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash值>
```
执行后，该节点会开始连接Master并注册自己。稍等片刻，在Master节点上执行`kubectl get nodes`即可看到新节点加入，但其角色（ROLE）可能还未定义。

![](img/ecf26cfd01123273be2884d964b75896_28.png)

可以为新加入的节点打上`worker`标签以明确其角色。
```bash
kubectl label node <node名称> node-role.kubernetes.io/worker=
```
之后，再次查看节点列表，该节点的角色就会显示为`worker`。

![](img/ecf26cfd01123273be2884d964b75896_30.png)

![](img/ecf26cfd01123273be2884d964b75896_32.png)

按照相同的流程，可以继续将更多节点（如node2）加入到集群中：安装必备软件、执行`kubeadm join`命令、并为其打上标签。

![](img/ecf26cfd01123273be2884d964b75896_34.png)

![](img/ecf26cfd01123273be2884d964b75896_36.png)

## 总结

![](img/ecf26cfd01123273be2884d964b75896_38.png)

![](img/ecf26cfd01123273be2884d964b75896_40.png)

本节课中我们一起学习了Kubernetes集群管理的重要环节。我们首先通过修改和应用YAML配置文件，成功部署了Kubernetes Dashboard，并创建了具有管理员权限的Token用于登录。随后，我们回顾了如何使用`kubeadm join`命令将新的工作节点加入到集群中，并通过打标签的方式明确其角色。这些操作为管理和扩展Kubernetes集群提供了图形化界面和灵活性的基础。