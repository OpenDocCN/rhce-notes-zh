# Linux运维全套培训课程：P94：DevOps-4.Jenkins快速入门 🚀

在本节课中，我们将学习如何使用Jenkins实现一个基础的自动化任务。我们将创建一个任务，从GitLab仓库拉取代码，并通过脚本将项目文件自动部署到Nginx服务器上。

## 创建管理员账户

上一节我们完成了Jenkins的初始化插件安装。本节中，我们来看看如何创建管理员账户并进入主界面。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_1.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_3.png)

在Jenkins的初始化设置页面，需要创建一个管理员账户。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_5.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_6.png)

以下是需要填写的账户信息：
*   **用户名**：可以设置为 `admin` 或 `root`。
*   **密码**：Jenkins对密码要求不严格，例如可以设置为 `1`。
*   **确认密码**：再次输入相同的密码。
*   **全名**：可以设置为 `admin`。
*   **电子邮件地址**：例如 `admin@qq.com`。

填写完毕后，点击“保存并完成”，然后点击“开始使用Jenkins”即可进入Web管理界面。

进入界面后，你可能会发现界面是中文的。这是因为在安装插件时，我们选择了“安装推荐的插件”，其中包含了中文语言包。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_8.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_10.png)

## 创建第一个任务

进入Jenkins主界面后，我们需要创建一个“任务”。任务就是Jenkins实现工作流程自动化的载体，例如项目的打包、部署或定时执行某些操作。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_12.png)

点击左侧菜单的“新建Item”来创建任务。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_14.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_15.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_17.png)

以下是创建任务的步骤：
1.  输入一个任务名称，例如“抽奖任务”。
2.  选择任务类型。我们选择“Freestyle project”（自由风格项目），它允许我们自定义任务中的操作步骤。
3.  点击“确定”按钮。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_19.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_21.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_23.png)

## 配置任务拉取代码

![](img/b5a6a949eb56da98846c32fd4d0bed0f_25.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_27.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_29.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_30.png)

任务创建后，会进入详细的配置页面。我们的目标是让Jenkins从GitLab仓库拉取代码。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_32.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_34.png)

首先，我们需要确保Jenkins服务器上安装了Git工具。如果没有安装，可以通过命令 `yum install git -y` 进行安装。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_36.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_38.png)

在任务配置页面，找到“源码管理”部分。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_40.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_42.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_44.png)

以下是配置源码管理的步骤：
1.  勾选“Git”。
2.  在“Repository URL”中，填入GitLab仓库的克隆地址。由于SSH方式可能受限，我们使用HTTP地址。例如：`http://web.gitlab.com/root/my-lottery.git`。
3.  在“Credentials”（凭据）处点击“添加”。因为我们的GitLab仓库是私有的，需要授权。
    *   类型选择“Username with password”。
    *   用户名填写GitLab上有权限访问该仓库的用户，例如 `root`。
    *   密码填写该用户的密码。
    *   点击“添加”。
4.  添加后，在凭据下拉列表中选择刚刚创建的凭据。
5.  在“分支指定器”中，填写要拉取的分支，例如 `*/main`（主分支）。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_46.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_47.png)

如果Jenkins服务器无法解析GitLab的域名（如`web.gitlab.com`），需要在Jenkins服务器的 `/etc/hosts` 文件中添加解析记录，例如：
```bash
192.168.0.14 web.gitlab.com
```
配置完成后，点击页面底部的“保存”。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_49.png)

## 执行任务与查看结果

![](img/b5a6a949eb56da98846c32fd4d0bed0f_51.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_53.png)

任务保存后，即可手动触发执行。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_55.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_56.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_58.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_60.png)

在任务页面，点击“立即构建”（Build Now）。页面左侧会生成一个构建历史，例如 `#1` 代表第一次构建。绿色对勾表示构建成功，红色叉号表示失败。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_62.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_64.png)

点击构建历史编号（如 `#1`），可以查看构建的详细日志。如果看到 `SUCCESS`，则说明任务执行成功。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_66.png)

这个任务执行的操作是：使用Git工具，通过提供的凭据，从指定的GitLab仓库地址克隆 `main` 分支的代码到Jenkins服务器的本地路径。

代码被拉取到了Jenkins的工作空间目录，默认路径是：
```
/var/lib/jenkins/workspace/[任务名称]/
```
例如，我们的“抽奖任务”代码就在 `/var/lib/jenkins/workspace/抽奖任务/` 目录下。该目录下的文件与GitLab仓库中的项目文件一致。

## 准备Web服务器与部署脚本

![](img/b5a6a949eb56da98846c32fd4d0bed0f_68.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_70.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_72.png)

![](img/b5a6a949eb56da98846c32fd4d0bed0f_74.png)

代码拉取到Jenkins本地后，我们需要将其发布到Web服务器（如Nginx）上运行。本节中，我们来看看如何准备服务器并编写自动化部署脚本。

我们准备两台Nginx服务器作为Web服务器，假设其IP地址分别为 `192.168.0.33` 和 `192.168.0.34`。需要在服务器上安装并启动Nginx服务。

部署的核心是一个Shell脚本。我们在Jenkins服务器的 `/var/lib/jenkins/scripts/` 目录下创建脚本文件，例如 `deploy-nginx.sh`。

脚本的主要逻辑分为以下几个部分：
1.  **定义变量**：指定Jenkins上项目代码的路径和Nginx服务器的网页根目录路径。
    ```bash
    code_dir="/var/lib/jenkins/workspace/买test/"
    web_dir="/usr/share/nginx"
    ```
    > **注意**：`code_dir` 需要根据你要发布的具体任务名称进行修改。
2.  **打包项目**：进入项目目录，将代码打包压缩，并在压缩包名称中加入时间戳。
    ```bash
    cd $code_dir
    tar czf /tmp/web-${time}.tar.gz ./*
    ```
3.  **发布到多台服务器**：循环读取存储了Web服务器IP地址的列表文件，将压缩包拷贝到各服务器，并执行解压、替换等操作。
    *   创建服务器列表文件，如 `nginx_hosts`，内容为：
        ```
        192.168.0.33
        192.168.0.34
        ```
    *   在脚本中使用循环：
        ```bash
        for host in $(cat /var/lib/jenkins/scripts/nginx_hosts)
        do
            # 1. 拷贝压缩包到远程服务器
            scp /tmp/web-${time}.tar.gz root@${host}:${web_dir}/
            # 2. 远程登录服务器，创建带时间戳的目录并解压
            ssh root@${host} "cd ${web_dir} && mkdir web-${time} && tar xf web-${time}.tar.gz -C web-${time}/ && rm -f web-${time}.tar.gz"
            # 3. 远程登录服务器，备份旧网页并创建软链接到新目录
            ssh root@${host} "cd ${web_dir} && rm -rf html && ln -s web-${time} html"
        done
        ```

## 配置Jenkins执行部署脚本

脚本编写完成后，需要让Jenkins用户有权执行它，并能够免密登录到各台Web服务器。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_76.png)

首先，为脚本添加执行权限：
```bash
chmod +x /var/lib/jenkins/scripts/deploy-nginx.sh
```
其次，修改Jenkins用户的登录Shell，使其可以执行命令：
```bash
usermod -s /bin/bash jenkins
```
然后，切换到Jenkins用户，为其配置到所有Web服务器的SSH免密登录：
```bash
su - jenkins
ssh-keygen -t rsa # 生成密钥对，一路回车
for ip in $(cat scripts/nginx_hosts); do ssh-copy-id root@$ip; done
```
最后，回到Jenkins任务配置页面，在“构建”步骤中，选择“执行Shell”，在命令框中填写脚本的绝对路径，例如：
```
/var/lib/jenkins/scripts/deploy-nginx.sh
```
保存任务后，再次点击“立即构建”。Jenkins会先拉取最新代码，然后自动执行部署脚本，将项目发布到列表中所有的Nginx服务器上。

![](img/b5a6a949eb56da98846c32fd4d0bed0f_77.png)

## 总结

![](img/b5a6a949eb56da98846c32fd4d0bed0f_79.png)

本节课中我们一起学习了Jenkins的基础自动化流程。我们创建了一个自由风格的任务，配置其从私有GitLab仓库拉取代码。随后，我们编写了一个Shell脚本，实现了将项目代码打包并自动部署到多台Nginx Web服务器的功能。最后，我们配置Jenkins任务在构建后执行该脚本，从而完成了从代码变更到服务部署的简易自动化流水线。