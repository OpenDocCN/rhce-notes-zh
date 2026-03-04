# Kubernetes集群部署：P107：K8S集群环境部署 - 高可用配置

在本节课程中，我们将学习如何为Kubernetes集群配置高可用性。我们将通过部署HAProxy和Keepalived服务，确保Master节点在发生故障时能够自动切换，从而提升集群的稳定性。

## Docker驱动配置优化

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_1.png)

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_3.png)

上一节我们完成了基础环境的准备，本节我们首先优化Docker的配置，以提升其运行稳定性。

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_5.png)

默认情况下，Docker使用的Cgroup驱动是`cgroupfs`。在某些情况下，这可能导致系统资源压力增大，进而引发不稳定问题。当系统使用systemd作为初始化进程时，将Docker的驱动改为`systemd`可以缓解此问题。

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_7.png)

以下是具体的操作步骤：

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_9.png)

1.  首先，我们需要在`/etc/docker/`目录下创建一个名为`daemon.json`的配置文件。
2.  然后，在该文件中指定Docker使用`systemd`作为Cgroup驱动。

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_11.png)

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_13.png)

由于我们的环境中`/etc/docker/`目录可能不存在，需要先创建它。

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_15.png)

```bash
# 创建docker配置目录
mkdir /etc/docker
```

接下来，创建并编辑`/etc/docker/daemon.json`文件，内容如下：

```json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
```

如果需要配置镜像加速器，可以在此文件中添加`registry-mirrors`配置项。

完成配置后，需要将此文件分发到集群中的所有节点，并重启Docker服务使其生效。

```bash
# 重启Docker服务
systemctl restart docker
# 设置Docker开机自启
systemctl enable docker
```

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_17.png)

## 部署高可用服务

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_19.png)

优化Docker配置后，我们开始部署集群的高可用服务。高可用的核心目标是防止某个Master节点宕机导致整个集群瘫痪。

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_21.png)

我们将使用HAProxy和Keepalived来实现这一目标。Keepalived负责在Master节点间进行故障切换（VIP漂移），而HAProxy则提供负载均衡功能。虽然在本场景中HAProxy的负载均衡功能使用不多，但它是必需的组件。

我们选择在其中的两台Master节点（例如`master01`和`master02`）上部署这两个服务，以实现主备高可用。

### 安装HAProxy与Keepalived

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_23.png)

首先，在选定的两台Master节点上安装必要的软件包。

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_25.png)

```bash
yum install -y haproxy keepalived
```

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_27.png)

### 配置HAProxy

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_29.png)

HAProxy的配置文件定义了其监听端口以及后端代理的服务器组。我们需要修改配置文件，使其代理我们的三台Master节点。

以下是配置文件的核心部分示例，你需要根据自己集群的Master节点IP进行修改：

```bash
# 前端监听配置
frontend kubernetes-apiserver
    bind *:8443
    mode tcp
    option tcplog
    default_backend kubernetes-apiserver

# 后端服务器组定义
backend kubernetes-apiserver
    mode tcp
    balance roundrobin
    server k8s-master01 192.168.0.10:6443 check
    server k8s-master02 192.168.0.11:6443 check
    server k8s-master03 192.168.0.12:6443 check
```

关键点说明：
*   `frontend`部分定义了HAProxy接收请求的入口。
*   `backend`部分定义了请求将被转发到的后端真实服务器，即你的三台Master节点，Kubernetes API Server的默认端口是`6443`。

将修改后的配置文件覆盖到`/etc/haproxy/haproxy.cfg`，然后启动HAProxy服务。

```bash
# 覆盖配置文件
cp haproxy.cfg /etc/haproxy/haproxy.cfg
# 启动并设置开机自启
systemctl start haproxy
systemctl enable haproxy
```

### 配置Keepalived

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_31.png)

Keepalived的配置决定了主备节点的角色、优先级以及虚拟IP（VIP）。我们需要分别配置主节点和备节点。

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_33.png)

主节点配置文件（例如`master01`）关键部分：

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_35.png)

```bash
global_defs {
    router_id LVS_DEVEL
}
# 检测脚本
vrrp_script check_apiserver {
    script "/etc/keepalived/check_apiserver.sh"
    interval 3
    weight -2
    fall 10
    rise 2
}
# 虚拟IP实例配置
vrrp_instance VI_1 {
    state MASTER        # 角色为主
    interface eth0      # 绑定VIP的网卡名，请根据实际情况修改
    virtual_router_id 51
    priority 101        # 优先级，主节点应更高
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass abc123
    }
    virtual_ipaddress {
        192.168.0.100/24 # 虚拟IP地址
    }
    track_script {
        check_apiserver # 调用健康检查脚本
    }
}
```

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_37.png)

备节点配置文件（例如`master02`）需要修改的地方：
*   `state` 应改为 `BACKUP`。
*   `priority` 优先级应设置得比主节点低，例如 `100`。
*   `interface` 网卡名需根据实际情况确认。
*   `virtual_router_id` 必须与主节点保持一致。

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_39.png)

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_40.png)

将配置文件覆盖到`/etc/keepalived/keepalived.conf`。

### 配置健康检查脚本

Keepalived需要一个健康检查脚本来判断何时进行故障切换。这个脚本通常用于检查HAProxy进程是否存活。

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_42.png)

脚本示例 (`check_apiserver.sh`)：

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_44.png)

```bash
#!/bin/bash
if ! pidof haproxy > /dev/null; then
    systemctl stop keepalived
fi
```

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_46.png)

脚本逻辑：如果检测不到HAProxy进程，则停止本机的Keepalived服务，触发VIP漂移到备节点。

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_48.png)

将脚本放到`/etc/keepalived/`目录，并赋予执行权限。

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_50.png)

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_52.png)

```bash
cp check_apiserver.sh /etc/keepalived/
chmod +x /etc/keepalived/check_apiserver.sh
```

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_54.png)

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_55.png)

最后，启动Keepalived服务。

```bash
systemctl start keepalived
systemctl enable keepalived
```

配置完成后，可以在主节点上使用 `ip addr show eth0` 命令查看虚拟IP `192.168.0.100` 是否已成功绑定到网卡。

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_57.png)

## 本节总结

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_59.png)

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_60.png)

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_62.png)

本节课中我们一起学习了如何为Kubernetes集群配置高可用环境。
1.  我们首先优化了Docker的Cgroup驱动，将其改为`systemd`以提升稳定性。
2.  接着，我们在两台Master节点上部署了HAProxy和Keepalived。
3.  通过配置HAProxy代理所有Master节点的API Server。
4.  通过配置Keepalived实现主备节点故障切换和虚拟IP的漂移。
5.  配置了健康检查脚本，使Keepalived能根据HAProxy的状态决定是否进行切换。

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_64.png)

![](img/8fa1eeef5a6180b6fd3cff4c5b19ee44_66.png)

这套高可用方案确保了即使一个Master节点发生故障，集群的API服务仍然可以通过虚拟IP或其它健康节点继续访问，从而保障了集群的核心服务不中断。这是构建生产级Kubernetes集群的关键一步。