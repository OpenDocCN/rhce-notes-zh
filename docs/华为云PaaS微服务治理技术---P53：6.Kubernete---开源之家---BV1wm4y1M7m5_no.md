# 华为云PaaS微服务治理技术：P53：6.Kubernetes集群搭建Master安装-Docker安装 🐳

![](img/1b1fbb7b19c32a57c33770733a0f754f_1.png)

在本节课程中，我们将学习如何在Kubernetes集群的Master节点上手动安装Docker。虽然在前期的ETCD安装中可能已经自动安装了Docker，但为了进行二进制安装，我们需要掌握手动安装的步骤。

![](img/1b1fbb7b19c32a57c33770733a0f754f_3.png)

上一节我们介绍了Kubernetes集群的整体规划，本节中我们来看看如何为Master节点安装Docker容器运行时。

![](img/1b1fbb7b19c32a57c33770733a0f754f_5.png)

![](img/1b1fbb7b19c32a57c33770733a0f754f_7.png)

## 准备工作

![](img/1b1fbb7b19c32a57c33770733a0f754f_9.png)

在进行Docker安装之前，需要先更新系统的软件包源。这能确保我们安装的是最新、最兼容的软件版本。

![](img/1b1fbb7b19c32a57c33770733a0f754f_11.png)

以下是更新软件包源的命令：
```bash
yum update
```
根据不同的机器配置，更新过程可能需要一些时间，请耐心等待。

![](img/1b1fbb7b19c32a57c33770733a0f754f_13.png)

![](img/1b1fbb7b19c32a57c33770733a0f754f_15.png)

## 安装Docker

更新完成后，我们就可以开始安装Docker了。安装过程分为两步：配置Docker的YUM源，然后执行安装。

![](img/1b1fbb7b19c32a57c33770733a0f754f_17.png)

以下是安装Docker的具体步骤：

![](img/1b1fbb7b19c32a57c33770733a0f754f_19.png)

1.  **配置Docker YUM源**：
    首先，编辑或创建YUM源配置文件。如果文件不存在，系统会自动创建。
    ```bash
    vi /etc/yum.repos.d/docker.repo
    ```
    在文件中插入以下配置内容：
    ```
    [docker-ce-stable]
    name=Docker CE Stable - $basearch
    baseurl=https://download.docker.com/linux/centos/$releasever/$basearch/stable
    enabled=1
    gpgcheck=1
    gpgkey=https://download.docker.com/linux/centos/gpg
    ```
    编辑完成后，保存并退出。

![](img/1b1fbb7b19c32a57c33770733a0f754f_21.png)

![](img/1b1fbb7b19c32a57c33770733a0f754f_23.png)

2.  **执行安装命令**：
    使用YUM包管理器安装Docker引擎。`-y`参数表示自动确认所有提示。
    ```bash
    yum install -y docker-ce docker-ce-cli containerd.io
    ```
    系统会下载并安装Docker及其相关依赖包。安装的软件包数量可能因系统环境而异。

![](img/1b1fbb7b19c32a57c33770733a0f754f_25.png)

## 验证安装

![](img/1b1fbb7b19c32a57c33770733a0f754f_27.png)

安装完成后，我们需要验证Docker是否成功安装并查看其版本信息。

![](img/1b1fbb7b19c32a57c33770733a0f754f_29.png)

![](img/1b1fbb7b19c32a57c33770733a0f754f_31.png)

以下是验证命令：
```bash
docker --version
```
执行该命令后，终端会显示已安装的Docker版本号，例如 `Docker version 20.10.12, build e91ed57`。这表明Docker已经成功安装到系统中。

![](img/1b1fbb7b19c32a57c33770733a0f754f_32.png)

![](img/1b1fbb7b19c32a57c33770733a0f754f_34.png)

## 总结

![](img/1b1fbb7b19c32a57c33770733a0f754f_36.png)

本节课中我们一起学习了在Kubernetes Master节点上手动安装Docker的完整流程。我们首先更新了系统软件源，然后通过配置YUM源和运行安装命令成功安装了Docker，最后通过查看版本信息验证了安装结果。掌握这一步是后续搭建Kubernetes集群的基础。