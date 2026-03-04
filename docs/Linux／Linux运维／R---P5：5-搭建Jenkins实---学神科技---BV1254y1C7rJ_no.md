# Linux运维：RHCE：扩展-GitLab与Jenkins结合构建持续集成-CI环境 - P5：5-搭建Jenkins实现持续集成

![](img/930f4bc4fabe797a4b58e72c0aff1da3_0.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_2.png)

在本节课中，我们将学习如何在Linux系统上安装和配置Jenkins，以搭建一个基础的持续集成环境。我们将从安装Java环境开始，逐步完成Jenkins的部署、初始化和基本配置。

---

![](img/930f4bc4fabe797a4b58e72c0aff1da3_4.png)

## 概述

![](img/930f4bc4fabe797a4b58e72c0aff1da3_6.png)

Jenkins是一个用Java编写的开源持续集成工具。在安装Jenkins之前，需要先安装Java运行环境。我们将使用OpenJDK，并详细讲解Jenkins的安装步骤、端口冲突解决、插件源配置以及常见问题的处理方法。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_8.png)

---

![](img/930f4bc4fabe797a4b58e72c0aff1da3_10.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_12.png)

## 安装Java环境

![](img/930f4bc4fabe797a4b58e72c0aff1da3_14.png)

Jenkins基于Java开发，因此首先需要安装Java Development Kit。

执行以下命令安装OpenJDK：
```bash
yum install -y java-1.8.0-openjdk
```
OpenJDK是开源的Java开发工具包，与Oracle官方的JDK功能基本一致。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_16.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_18.png)

---

![](img/930f4bc4fabe797a4b58e72c0aff1da3_20.png)

## 获取与安装Jenkins

上一节我们安装了Java环境，本节中我们来看看如何安装Jenkins。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_22.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_24.png)

Jenkins更新非常迅速。建议从官方网站（jenkins.io）下载**长期支持版（LTS）**，而不是每周更新版，以保证稳定性。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_26.png)

以下是安装Jenkins的步骤：
1.  在线安装：可以配置Jenkins的官方YUM仓库，然后通过`yum`命令安装。
2.  本地安装：如果网络环境不佳，可以提前下载好RPM安装包，使用`rpm`命令进行安装。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_28.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_30.png)

执行以下命令进行本地安装：
```bash
rpm -ivh jenkins-2.303.1-1.1.noarch.rpm
```

---

![](img/930f4bc4fabe797a4b58e72c0aff1da3_32.png)

## 配置与启动Jenkins

![](img/930f4bc4fabe797a4b58e72c0aff1da3_34.png)

安装完成后，需要修改Jenkins的配置以避免端口冲突，并调整运行用户。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_36.png)

新版本Jenkins的默认服务端口是8080，这可能与系统中其他服务（如GitLab）冲突。我们需要修改其配置文件。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_38.png)

配置文件路径为：`/etc/sysconfig/jenkins`

以下是需要修改的关键配置项：
*   **运行用户**：将第29行的`JENKINS_USER`改为`root`，以避免后续因权限问题导致的错误。
*   **服务端口**：将第56行的`JENKINS_PORT`从默认的`8080`修改为其他未占用的端口，例如`19880`。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_40.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_41.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_43.png)

修改完成后，启动Jenkins服务并配置防火墙：
```bash
systemctl start jenkins
systemctl enable jenkins
firewall-cmd --add-port=19880/tcp --permanent
firewall-cmd --reload
# 或直接关闭防火墙
systemctl stop firewalld
```

![](img/930f4bc4fabe797a4b58e72c0aff1da3_45.png)

---

![](img/930f4bc4fabe797a4b58e72c0aff1da3_46.png)

## 初始化Jenkins

![](img/930f4bc4fabe797a4b58e72c0aff1da3_48.png)

服务启动后，可以通过浏览器访问 `http://<服务器IP>:19880` 进行初始化。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_50.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_51.png)

首次访问时，Jenkins需要一些时间准备。当出现“Unlock Jenkins”页面时，需要输入初始管理员密码。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_53.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_55.png)

该密码存储在服务器上的一个文件中，路径通常为：
```
/var/lib/jenkins/secrets/initialAdminPassword
```
使用`cat`命令查看该文件内容并复制密码。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_57.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_59.png)

---

![](img/930f4bc4fabe797a4b58e72c0aff1da3_61.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_63.png)

## 配置插件源加速

![](img/930f4bc4fabe797a4b58e72c0aff1da3_65.png)

在初始化过程中，Jenkins需要安装大量插件。默认插件源位于国外，下载速度可能很慢甚至失败。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_67.png)

为了提高插件安装成功率，建议将插件更新源替换为国内镜像（如清华大学镜像源）。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_68.png)

执行以下命令进行替换：
```bash
sed -i 's|https://updates.jenkins.io/update-center.json|https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json|g' /var/lib/jenkins/hudson.model.UpdateCenter.xml
```
替换后，需要重启Jenkins服务使配置生效：
```bash
systemctl restart jenkins
```

![](img/930f4bc4fabe797a4b58e72c0aff1da3_70.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_71.png)

---

![](img/930f4bc4fabe797a4b58e72c0aff1da3_73.png)

## 完成安装与插件管理

![](img/930f4bc4fabe797a4b58e72c0aff1da3_75.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_76.png)

重启后，再次访问Jenkins，输入初始密码，进入“自定义Jenkins”页面。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_78.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_80.png)

以下是后续步骤：
1.  选择“安装推荐的插件”。这个过程可能需要较长时间。
2.  如果插件安装失败，可以点击“重试”。若多次重试仍失败，可以点击“继续”跳过，后续再手动安装。
3.  插件安装完成后，创建第一个管理员用户，填写用户名、密码等信息。
4.  保存并完成，即可进入Jenkins主界面。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_82.png)

如果插件安装失败，可以通过以下方式手动管理插件：
*   **网页上传**：在Jenkins管理界面，进入“系统管理” -> “插件管理” -> “高级”选项卡，在“上传插件”部分，选择本地已下载的 `.hpi` 格式插件文件进行安装。
*   **文件拷贝**：从一台已安装好插件的Jenkins服务器上，将 `/var/lib/jenkins/plugins/` 目录打包，复制到新服务器对应目录下解压，然后重启Jenkins服务。

![](img/930f4bc4fabe797a4b58e72c0aff1da3_84.png)

---

![](img/930f4bc4fabe797a4b58e72c0aff1da3_86.png)

## 总结

![](img/930f4bc4fabe797a4b58e72c0aff1da3_88.png)

![](img/930f4bc4fabe797a4b58e72c0aff1da3_90.png)

本节课中我们一起学习了搭建Jenkins持续集成环境的核心步骤。我们首先安装了必要的Java环境，然后安装并配置了Jenkins服务，解决了端口冲突问题。接着，我们完成了Jenkins的初始化，并通过更换插件源来加速插件安装。最后，介绍了当插件安装遇到困难时的两种解决方法：通过管理界面上传插件或直接拷贝插件文件。掌握这些步骤，你就成功部署了一个基础的Jenkins服务器，为后续实现持续集成流水线打下了基础。