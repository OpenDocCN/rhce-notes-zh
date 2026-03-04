# Docker容器虚拟化平台部署教程：P2：部署Docker并配置镜像加速

## 概述
在本节课中，我们将学习如何在CentOS 7.5系统上部署Docker容器虚拟化平台，并配置国内的镜像加速地址，以解决从国外官方源拉取镜像速度慢或失败的问题。

---

![](img/caece9282e17d3575bade97b23688958_1.png)

![](img/caece9282e17d3575bade97b23688958_3.png)

## 1️⃣ 安装Docker依赖包
首先，我们需要安装Docker运行所必需的系统依赖包。

![](img/caece9282e17d3575bade97b23688958_5.png)

![](img/caece9282e17d3575bade97b23688958_7.png)

执行以下命令进行安装：
```bash
yum install -y yum-utils device-mapper-persistent-data lvm2
```

**命令解析：**
*   `yum-utils`: 提供了`yum-config-manager`等工具，用于管理Yum仓库。
*   `device-mapper-persistent-data` 和 `lvm2`: 这是`devicemapper`存储驱动所需的包，Docker使用它来管理镜像和容器的存储层。

![](img/caece9282e17d3575bade97b23688958_9.png)

![](img/caece9282e17d3575bade97b23688958_11.png)

---

![](img/caece9282e17d3575bade97b23688958_13.png)

![](img/caece9282e17d3575bade97b23688958_15.png)

![](img/caece9282e17d3575bade97b23688958_16.png)

## 2️⃣ 配置国内Docker Yum源
系统自带的Yum源中的Docker版本通常较旧。为了安装新版Docker CE（社区版），我们需要添加官方的Docker仓库。为了加快下载速度，这里使用阿里云的镜像源。

![](img/caece9282e17d3575bade97b23688958_18.png)

你可以选择以下任一方式添加仓库：

![](img/caece9282e17d3575bade97b23688958_20.png)

**方式一：使用`yum-config-manager`命令**
```bash
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

![](img/caece9282e17d3575bade97b23688958_22.png)

**方式二：使用`wget`命令直接下载仓库文件**
```bash
sudo wget -O /etc/yum.repos.d/docker-ce.repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

![](img/caece9282e17d3575bade97b23688958_24.png)

执行上述任一命令后，会在`/etc/yum.repos.d/`目录下生成一个`docker-ce.repo`文件，其中包含了Docker CE的软件源信息。

---

![](img/caece9282e17d3575bade97b23688958_26.png)

![](img/caece9282e17d3575bade97b23688958_28.png)

![](img/caece9282e17d3575bade97b23688958_29.png)

## 3️⃣ 安装Docker CE及相关组件
配置好Yum源后，现在可以安装Docker了。

![](img/caece9282e17d3575bade97b23688958_31.png)

运行以下命令安装Docker核心组件：
```bash
sudo yum install -y docker-ce docker-ce-cli containerd.io
```

**软件包说明：**
*   `docker-ce`: Docker社区版的主程序。
*   `docker-ce-cli`: Docker命令行工具包，用于执行`docker`命令。
*   `containerd.io`: 容器运行时，负责管理容器的生命周期。

> **提示**：如果不确定某个软件包的作用，可以使用`yum info <package_name>`命令查看其详细信息。

安装完成后，启动Docker服务并设置开机自启：
```bash
sudo systemctl start docker
sudo systemctl enable docker
```

验证Docker是否安装成功及查看版本：
```bash
docker --version
```

---

![](img/caece9282e17d3575bade97b23688958_33.png)

![](img/caece9282e17d3575bade97b23688958_34.png)

## 4️⃣ 配置Docker镜像加速器
默认情况下，Docker会从国外的Docker Hub拉取镜像，速度可能很慢或不稳定。为了解决这个问题，我们需要配置国内的镜像加速器。

![](img/caece9282e17d3575bade97b23688958_36.png)

![](img/caece9282e17d3575bade97b23688958_38.png)

![](img/caece9282e17d3575bade97b23688958_40.png)

以下是配置步骤：

![](img/caece9282e17d3575bade97b23688958_42.png)

**步骤一：创建或修改Docker守护进程配置文件**
Docker的配置文件位于`/etc/docker/daemon.json`。如果文件不存在，则创建它。
```bash
sudo vim /etc/docker/daemon.json
```

**步骤二：写入镜像加速器地址**
在文件中输入以下内容（这里以阿里云加速器为例，你需要注册阿里云账号后获取专属加速地址）：
```json
{
  "registry-mirrors": ["https://your-aliyun-mirror.mirror.aliyuncs.com"]
}
```

![](img/caece9282e17d3575bade97b23688958_44.png)

> **注意**：请将 `"https://your-aliyun-mirror.mirror.aliyuncs.com"` 替换为你从阿里云容器镜像服务控制台获取的实际加速器地址。

![](img/caece9282e17d3575bade97b23688958_46.png)

**其他常用公共加速器地址（可选）：**
*   网易：`https://hub-mirror.c.163.com`
*   中科大：`https://docker.mirrors.ustc.edu.cn`

![](img/caece9282e17d3575bade97b23688958_48.png)

如果需要配置多个镜像源，可以按如下格式填写：
```json
{
  "registry-mirrors": [
    "https://docker.mirrors.ustc.edu.cn",
    "https://hub-mirror.c.163.com"
  ]
}
```

**步骤三：重启Docker服务使配置生效**
```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

**步骤四：验证配置是否生效**
执行以下命令，在输出信息中查看`Registry Mirrors`部分，确认加速器地址已添加成功。
```bash
docker info
```

---

## 5️⃣ 拉取并验证Docker镜像
配置好加速器后，我们来测试拉取一个镜像。

![](img/caece9282e17d3575bade97b23688958_50.png)

**搜索镜像：**
```bash
docker search centos
```
此命令会列出Docker Hub上所有与`centos`相关的镜像。`OFFICIAL`列为`OK`的表示是官方镜像。

![](img/caece9282e17d3575bade97b23688958_52.png)

**拉取镜像：**
```bash
docker pull centos:7
```
此命令会从配置的镜像加速器拉取标签为`7`的CentOS官方镜像。配置加速器后，下载速度会显著提升。

**查看本地镜像：**
镜像拉取成功后，可以使用以下命令查看：
```bash
docker images
```

![](img/caece9282e17d3575bade97b23688958_54.png)

![](img/caece9282e17d3575bade97b23688958_56.png)

---

## 6️⃣ 其他配置与注意事项

![](img/caece9282e17d3575bade97b23688958_58.png)

### 开启内核路由转发
为了让Docker容器能够访问外部网络，需要确保系统的IP转发功能已开启。通常启动Docker服务时会自动开启，但也可以手动确认或设置。

**临时开启：**
```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

![](img/caece9282e17d3575bade97b23688958_60.png)

**永久开启：**
编辑`/etc/sysctl.conf`文件，添加或修改以下行：
```conf
net.ipv4.ip_forward = 1
```
然后执行 `sudo sysctl -p` 使配置生效。

![](img/caece9282e17d3575bade97b23688958_62.png)

![](img/caece9282e17d3575bade97b23688958_64.png)

### 关于防火墙
在学习和测试环境中，为了方便，可以暂时关闭防火墙：
```bash
sudo systemctl stop firewalld
sudo systemctl disable firewalld
```

![](img/caece9282e17d3575bade97b23688958_66.png)

**重要说明**：即使关闭了`firewalld`服务，重启Docker后，你仍可能看到`iptables`规则。这是因为Docker会直接调用Linux内核的Netfilter模块来设置必要的网络规则（如NAT、端口映射等），以实现容器网络功能。`firewalld`和`iptables`都只是配置Netfilter的工具。

![](img/caece9282e17d3575bade97b23688958_68.png)

![](img/caece9282e17d3575bade97b23688958_70.png)

![](img/caece9282e17d3575bade97b23688958_71.png)

---

![](img/caece9282e17d3575bade97b23688958_73.png)

## 总结
本节课我们一起学习了在CentOS 7系统上部署Docker容器平台的完整流程：
1.  **安装依赖**：安装了`yum-utils`、`device-mapper`等必要软件包。
2.  **配置Yum源**：添加了阿里云的Docker CE镜像仓库，以便安装新版本。
3.  **安装Docker**：安装了`docker-ce`、`docker-ce-cli`和`containerd.io`三个核心组件。
4.  **配置镜像加速**：通过修改`/etc/docker/daemon.json`文件，配置了国内的镜像加速器地址，极大提升了拉取镜像的速度和稳定性。
5.  **验证与测试**：通过拉取CentOS镜像验证了Docker安装和加速器配置成功。
6.  **网络与防火墙**：了解了开启内核IP转发的重要性以及Docker与系统防火墙（Netfilter）的关系。

![](img/caece9282e17d3575bade97b23688958_75.png)

![](img/caece9282e17d3575bade97b23688958_77.png)

至此，一个可用的Docker基础环境已经搭建完成，接下来就可以开始创建和运行容器了。