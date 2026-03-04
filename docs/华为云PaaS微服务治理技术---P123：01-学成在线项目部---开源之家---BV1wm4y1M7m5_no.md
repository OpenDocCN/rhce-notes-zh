# 华为云PaaS微服务治理技术 - P123：01-学成在线项目部署-前端学习中心部署 🚀

在本节课中，我们将学习如何部署“学成在线”项目的学习中心前端工程。我们将使用Nginx镜像在华为云上创建工作负载，并通过门户的Nginx服务进行代理访问，以节省公网IP资源。

---

## 工作负载创建

上一节我们介绍了门户的部署，本节中我们来看看学习中心的部署。学习中心同样是一个前端工程，我们将使用Nginx镜像来创建其工作负载。

首先，进入工作负载管理界面，点击“无状态”创建新负载。

以下是创建负载的具体步骤：
1.  为工作负载命名，例如 `nginx-ucenter`。
2.  添加容器实例，选择Nginx镜像。
3.  设置实例数量为1，CPU为1核，内存为1024MB。

## 数据卷配置

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_1.png)

接下来，我们需要配置数据卷，其方式与部署门户时类似，主要区别在于映射到主机的本地目录不同。

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_3.png)

以下是需要配置的三个数据卷映射：
*   **日志目录**：将容器内的 `/var/log` 目录映射到主机的 `nginx-ucenter` 目录下。
*   **配置文件**：将Nginx配置文件映射到指定位置。
*   **工程目录**：将前端打包后的静态文件目录映射到容器内。

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_5.png)

配置完成后，进入下一步。

## 服务访问设置

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_7.png)

在服务访问设置环节，需要注意与门户部署的区别。学习中心不直接对外提供公网访问，而是通过门户的Nginx进行代理转发。

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_8.png)

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_10.png)

因此，我们选择“集群内访问”类型，创建一个名为 `ucenter` 的服务。学习中心在开发阶段使用的端口是13000，我们在此保持一致，将容器端口设置为13000，访问端口设置为1000。

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_11.png)

完成配置后，创建工作负载。

## 文件上传与配置

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_13.png)

工作负载创建成功后，我们需要将前端文件上传至服务器。学习中心的前端工程在开发完成后，会通过 `npm run build` 命令打包，生成静态文件。

以下是文件上传与配置的步骤：
1.  进入自动生成的学习中心工程目录。
2.  将提供的打包后ZIP文件上传至此目录。
3.  解压ZIP文件，确保目录下包含 `index.html` 和 `static` 文件夹。
4.  将正确的Nginx配置文件放置到指定位置。

文件准备就绪后，回到工作负载实例列表，重启实例以使配置生效。

## 配置Nginx代理转发

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_15.png)

实例运行后，我们需要通过门户的Nginx来代理访问学习中心。这需要在门户的Nginx配置中添加相应的转发规则。

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_17.png)

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_19.png)

具体操作是，在门户的Nginx配置中，为学习中心设置一个虚拟主机（server block）。例如，可以配置一个名为 `ucenter.xuecheng.com` 的虚拟主机，将其根路径的请求转发到学习中心的内网服务地址。

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_21.png)

配置规则示例如下：
```nginx
server {
    listen 80;
    server_name ucenter.xuecheng.com;
    location / {
        proxy_pass http://ucenter:1000;
    }
}
```

修改门户的Nginx配置文件后，需要将其重新上传至服务器，并重启门户的Nginx容器实例，使新配置生效。

## 验证访问

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_23.png)

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_25.png)

最后，我们来验证学习中心是否部署成功。由于我们通过门户代理访问，因此需要访问配置的代理域名。

在本地hosts文件中，将域名 `ucenter.xuecheng.com` 指向门户的弹性负载均衡公网IP地址。随后，在浏览器中访问 `http://ucenter.xuecheng.com`。

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_27.png)

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_28.png)

如果页面能够正常打开（即使部分动态数据如视频可能因后端服务未就绪而无法加载），则表明学习中心前端已通过门户Nginx成功代理访问。

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_29.png)

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_30.png)

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_31.png)

---

![](img/a26dc74ffde9fd8d352d4aaccdcdfe25_33.png)

本节课中我们一起学习了学习中心前端工程的完整部署流程。核心要点在于：学习中心采用集群内访问方式，通过修改门户Nginx的配置实现代理转发，从而无需为学习中心单独申请公网IP和负载均衡器，有效利用了资源并统一了访问入口。