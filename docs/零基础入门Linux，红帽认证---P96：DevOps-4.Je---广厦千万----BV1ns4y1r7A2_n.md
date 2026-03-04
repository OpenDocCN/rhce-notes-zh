# Linux运维与DevOps：P96：DevOps-4.Jenkins快速入门

在本节课中，我们将学习如何使用Jenkins实现一个基础的自动化工作流。我们将从创建一个简单的任务开始，学习如何配置Jenkins从Git仓库拉取代码，并编写一个Shell脚本将项目自动部署到多台Nginx Web服务器上。

## 创建管理员账户

上一节我们完成了Jenkins的安装和初始化。本节中，我们来看看如何创建第一个管理员账户。

在Jenkins的首次访问界面，需要创建一个管理员用户。以下是创建步骤：

![](img/299969b4cfe8de4c2d229534da87e457_1.png)

![](img/299969b4cfe8de4c2d229534da87e457_3.png)

![](img/299969b4cfe8de4c2d229534da87e457_5.png)

![](img/299969b4cfe8de4c2d229534da87e457_6.png)

*   **用户名**：可以设置为 `admin` 或 `root`。
*   **密码**：Jenkins对密码要求不严格，例如可以设置为 `1`。
*   **全名**：可以设置为 `admin`。
*   **电子邮件**：可以设置为一个有效的邮箱地址，例如 `admin@qq.com`。

![](img/299969b4cfe8de4c2d229534da87e457_8.png)

填写完毕后，点击“保存并完成”，即可进入Jenkins的Web管理界面。

![](img/299969b4cfe8de4c2d229534da87e457_9.png)

## 理解Jenkins中文界面

进入界面后，你可能会发现Jenkins默认是中文界面。

这是因为在初始安装插件时，我们选择了“安装推荐的插件”。社区推荐的插件包中包含了中文语言插件。如果当时选择了“选择插件来安装”，则需要手动勾选中文插件，但初学者可能不易找到。

![](img/299969b4cfe8de4c2d229534da87e457_11.png)

## 创建第一个任务

![](img/299969b4cfe8de4c2d229534da87e457_13.png)

Jenkins的核心是“任务”（Job），它用于实现工作流程的自动化，例如项目打包、部署或定时运行脚本。

现在，我们来创建一个自由风格的任务。

1.  点击左侧菜单的“新建Item”。
2.  输入任务名称，例如“抽奖任务”。
3.  选择任务类型为“Freestyle project”（自由风格项目）。
4.  点击“确定”。

![](img/299969b4cfe8de4c2d229534da87e457_15.png)

![](img/299969b4cfe8de4c2d229534da87e457_17.png)

## 配置任务：源码管理

![](img/299969b4cfe8de4c2d229534da87e457_19.png)

![](img/299969b4cfe8de4c2d229534da87e457_20.png)

![](img/299969b4cfe8de4c2d229534da87e457_22.png)

![](img/299969b4cfe8de4c2d229534da87e457_24.png)

![](img/299969b4cfe8de4c2d229534da87e457_26.png)

创建任务后，会进入任务配置页面。这里有很多配置项，我们首先配置“源码管理”。

![](img/299969b4cfe8de4c2d229534da87e457_28.png)

![](img/299969b4cfe8de4c2d229534da87e457_30.png)

![](img/299969b4cfe8de4c2d229534da87e457_32.png)

Jenkins需要从代码仓库拉取代码。我们将配置它使用Git工具从我们的GitLab仓库拉取。

![](img/299969b4cfe8de4c2d229534da87e457_34.png)

![](img/299969b4cfe8de4c2d229534da87e457_36.png)

1.  在“源码管理”部分，勾选“Git”。
2.  在“Repository URL”中，填入GitLab仓库的克隆地址。我们使用HTTP方式，地址格式为：`http://web.gitlab.com/root/仓库名.git`
3.  在“Credentials”（凭据）处，需要添加一个能访问该私有仓库的凭据。

![](img/299969b4cfe8de4c2d229534da87e457_38.png)

![](img/299969b4cfe8de4c2d229534da87e457_39.png)

![](img/299969b4cfe8de4c2d229534da87e457_41.png)

![](img/299969b4cfe8de4c2d229534da87e457_43.png)

由于我们的GitLab仓库是私有的，Jenkins需要权限才能访问。以下是添加凭据的步骤：

![](img/299969b4cfe8de4c2d229534da87e457_45.png)

![](img/299969b4cfe8de4c2d229534da87e457_47.png)

![](img/299969b4cfe8de4c2d229534da87e457_49.png)

![](img/299969b4cfe8de4c2d229534da87e457_51.png)

*   点击“添加”按钮，类型选择“Jenkins”。
*   范围选择“全局”。
*   类型选择“Username with password”（用户名和密码）。
*   用户名填写GitLab上有仓库访问权限的用户，例如 `root`。
*   密码填写该用户的密码。
*   点击“添加”。

![](img/299969b4cfe8de4c2d229534da87e457_53.png)

![](img/299969b4cfe8de4c2d229534da87e457_55.png)

![](img/299969b4cfe8de4c2d229534da87e457_57.png)

![](img/299969b4cfe8de4c2d229534da87e457_59.png)

添加后，在凭据下拉菜单中选择刚创建的凭据。

![](img/299969b4cfe8de4c2d229534da87e457_61.png)

4.  在“Branches to build”中，指定要拉取的分支，通常为 `*/main` 或 `*/master`（主分支）。
5.  点击页面底部的“保存”。

![](img/299969b4cfe8de4c2d229534da87e457_63.png)

![](img/299969b4cfe8de4c2d229534da87e457_65.png)

![](img/299969b4cfe8de4c2d229534da87e457_67.png)

## 执行任务与查看结果

![](img/299969b4cfe8de4c2d229534da87e457_69.png)

![](img/299969b4cfe8de4c2d229534da87e457_70.png)

任务配置保存后，即可开始执行。

![](img/299969b4cfe8de4c2d229534da87e457_72.png)

![](img/299969b4cfe8de4c2d229534da87e457_73.png)

1.  在任务页面，点击“Build Now”（立即构建）。
2.  在“Build History”（构建历史）中，会出现一个构建记录（例如 #1）。
3.  构建状态图标为蓝色圆点或绿色对勾表示成功，红色圆点表示失败。
4.  点击构建编号，可以查看“Console Output”（控制台输出），了解详细的执行日志。

![](img/299969b4cfe8de4c2d229534da87e457_75.png)

构建成功后，Jenkins会将代码拉取到其服务器上的特定目录。默认路径为：
```
/var/lib/jenkins/workspace/[任务名称]/
```
例如，我们的“抽奖任务”代码会被拉取到 `/var/lib/jenkins/workspace/抽奖任务/` 目录下。

![](img/299969b4cfe8de4c2d229534da87e457_77.png)

![](img/299969b4cfe8de4c2d229534da87e457_79.png)

## 准备Web服务器（Nginx）

![](img/299969b4cfe8de4c2d229534da87e457_81.png)

![](img/299969b4cfe8de4c2d229534da87e457_82.png)

![](img/299969b4cfe8de4c2d229534da87e457_84.png)

上一节我们让Jenkins拉取了代码，本节中我们来看看如何将这些代码部署到Web服务器上。

![](img/299969b4cfe8de4c2d229534da87e457_86.png)

![](img/299969b4cfe8de4c2d229534da87e457_88.png)

我们需要准备运行项目的生产环境服务器。这里我们使用两台安装Nginx的服务器作为Web服务器集群。

![](img/299969b4cfe8de4c2d229534da87e457_90.png)

![](img/299969b4cfe8de4c2d229534da87e457_92.png)

假设两台服务器的IP地址分别为：
*   `192.168.0.33`
*   `192.168.0.34`

![](img/299969b4cfe8de4c2d229534da87e457_94.png)

![](img/299969b4cfe8de4c2d229534da87e457_96.png)

在每台服务器上安装并启动Nginx服务：
```bash
yum install -y nginx
systemctl enable --now nginx
```
安装后，访问服务器IP，应能看到Nginx的默认欢迎页面。

## 编写项目部署脚本

我们需要编写一个Shell脚本，让Jenkins自动将项目文件打包并发布到上述Nginx服务器上。

![](img/299969b4cfe8de4c2d229534da87e457_98.png)

![](img/299969b4cfe8de4c2d229534da87e457_100.png)

脚本的核心逻辑如下：
1.  **定义变量**：指定项目源码在Jenkins上的路径、Nginx的Web根目录等。
2.  **打包项目**：将项目文件压缩成一个带时间戳的包。
3.  **循环发布**：将压缩包拷贝到所有Nginx服务器，解压，并更新Web根目录。

![](img/299969b4cfe8de4c2d229534da87e457_102.png)

![](img/299969b4cfe8de4c2d229534da87e457_104.png)

![](img/299969b4cfe8de4c2d229534da87e457_106.png)

以下是一个简化的脚本示例 (`deploy-nginx.sh`)：
```bash
#!/bin/bash
# 1. 定义变量
CODE_DIR="/var/lib/jenkins/workspace/抽奖任务" # Jenkins上的项目路径
WEB_DIR="/usr/share/nginx"                     # Nginx基础目录
HOSTS_FILE="/var/lib/jenkins/scripts/nginx.hosts" # 存放Nginx服务器IP的文件
TIME=$(date +%Y%m%d%H%M%S)                    # 当前时间戳

![](img/299969b4cfe8de4c2d229534da87e457_108.png)

# 2. 打包项目
cd $CODE_DIR
tar czf /tmp/web-${TIME}.tar.gz ./*

# 3. 循环发布到各服务器
for IP in $(cat $HOSTS_FILE)
do
    # 拷贝压缩包到服务器
    scp /tmp/web-${TIME}.tar.gz root@$IP:$WEB_DIR/
    # 连接到服务器执行部署操作
    ssh root@$IP "
        cd $WEB_DIR &&
        mkdir web-$TIME &&
        tar xzf web-$TIME.tar.gz -C web-$TIME/ &&
        rm -f web-$TIME.tar.gz &&
        rm -rf html &&
        ln -s web-$TIME html
    "
done
```
**脚本说明**：
*   `HOSTS_FILE` 文件内容应为每行一个Nginx服务器的IP地址。
*   脚本先在Jenkins主机打包项目。
*   然后通过 `scp` 将包拷贝到各Nginx服务器。
*   最后通过 `ssh` 在每台服务器上创建带时间戳的新目录、解压包、删除旧的 `html` 软链接并创建指向新版本的新软链接。这种方式便于回滚。

![](img/299969b4cfe8de4c2d229534da87e457_110.png)

![](img/299969b4cfe8de4c2d229534da87e457_112.png)

## 配置Jenkins用户与免密登录

为了让Jenkins能够执行脚本并远程操作Web服务器，需要进行以下配置：

![](img/299969b4cfe8de4c2d229534da87e457_114.png)

1.  **修改Jenkins用户Shell**：默认的Jenkins用户可能无法登录系统执行脚本。
    ```bash
    usermod -s /bin/bash jenkins
    ```
2.  **配置SSH免密登录**：Jenkins用户需要能免密登录到所有Nginx服务器。在Jenkins服务器上以 `jenkins` 用户执行：
    ```bash
    # 生成密钥对
    ssh-keygen -t rsa
    # 将公钥拷贝到各Nginx服务器（假设root用户）
    for IP in $(cat /var/lib/jenkins/scripts/nginx.hosts); do
      ssh-copy-id root@$IP
    done
    ```

![](img/299969b4cfe8de4c2d229534da87e457_116.png)

## 在Jenkins任务中调用部署脚本

最后，我们需要修改之前的“抽奖任务”，在拉取代码后自动执行部署脚本。

1.  在任务配置页面，找到“构建”部分。
2.  点击“增加构建步骤”，选择“Execute shell”（执行Shell）。
3.  在命令框中，填写执行部署脚本的命令，例如：
    ```bash
    /var/lib/jenkins/scripts/deploy-nginx.sh
    ```
4.  保存任务。

![](img/299969b4cfe8de4c2d229534da87e457_118.png)

![](img/299969b4cfe8de4c2d229534da87e457_119.png)

![](img/299969b4cfe8de4c2d229534da87e457_120.png)

现在，当你再次点击“Build Now”时，Jenkins会先拉取最新的代码，然后自动执行部署脚本，将项目发布到所有Nginx服务器上。你可以通过访问Nginx服务器的IP地址来验证部署是否成功。

## 总结

![](img/299969b4cfe8de4c2d229534da87e457_122.png)

本节课中我们一起学习了Jenkins的基础自动化流程。我们创建了一个Jenkins任务，配置其从GitLab私有仓库拉取代码；编写了自动化部署Shell脚本，实现了项目打包和向多台Nginx服务器的发布；最后配置了Jenkins任务，将代码拉取和自动部署串联起来，完成了一个简单的CI/CD流水线。这是自动化运维和DevOps实践的入门核心。