# Linux运维进阶：P93：DevOps-4.Jenkins快速入门 🚀

在本节课中，我们将学习如何使用Jenkins实现一个基础的自动化流程。我们将创建一个Jenkins任务，从GitLab仓库拉取代码，并通过脚本将项目自动部署到多台Nginx Web服务器上。

---

## 创建管理员账户

上一节我们完成了Jenkins的安装和初始化。本节中，我们来看看如何创建管理员账户并进入Web界面。

![](img/341360942192527e13828d9d9ebbdb2f_1.png)

![](img/341360942192527e13828d9d9ebbdb2f_3.png)

在Jenkins的初始化设置页面，需要创建一个管理员用户。
以下是需要填写的字段：
*   **用户名**：例如 `admin` 或 `root`。
*   **密码**：Jenkins对密码要求不严格，例如设置为 `1`。
*   **确认密码**：再次输入相同密码。
*   **全名**：例如 `admin`。
*   **电子邮件地址**：例如 `admin@qq.com`。

填写完毕后，点击“保存并完成”。之后，系统会提示保存Jenkins的URL地址，再次点击“保存并完成”即可进入Jenkins的Web管理界面。

![](img/341360942192527e13828d9d9ebbdb2f_5.png)

![](img/341360942192527e13828d9d9ebbdb2f_6.png)

---

## 界面与插件

我们来到了Jenkins的Web界面。你可能会发现界面默认是中文的。

这是因为在初始安装时，我们选择了“安装社区推荐插件”。这些推荐的插件中包含了中文语言包。如果当时选择的是“自定义安装插件”，则需要手动勾选并安装中文插件。

![](img/341360942192527e13828d9d9ebbdb2f_8.png)

![](img/341360942192527e13828d9d9ebbdb2f_10.png)

---

## 创建第一个任务

进入界面后，我们现在应该去创建一个“任务”。任务（Job）是Jenkins实现工作流程自动化的核心单元，例如项目的打包、部署或定时执行某些操作。

![](img/341360942192527e13828d9d9ebbdb2f_12.png)

点击左侧菜单的“新建Item”。
1.  在“任务名称”输入框中，为任务命名（例如 `抽奖任务`）。此字段不能为空。
2.  选择任务类型。我们首先选择 **`Freestyle project`**（自由风格项目）。这种类型允许你自由定义任务中执行的操作。
3.  点击“确定”按钮，进入任务配置页面。

![](img/341360942192527e13828d9d9ebbdb2f_14.png)

![](img/341360942192527e13828d9d9ebbdb2f_15.png)

任务配置页面包含多个部分，例如“General”（常规设置）、“源码管理”、“构建触发器”等。

![](img/341360942192527e13828d9d9ebbdb2f_17.png)

![](img/341360942192527e13828d9d9ebbdb2f_19.png)

![](img/341360942192527e13828d9d9ebbdb2f_21.png)

---

![](img/341360942192527e13828d9d9ebbdb2f_23.png)

## 配置源码管理

![](img/341360942192527e13828d9d9ebbdb2f_25.png)

![](img/341360942192527e13828d9d9ebbdb2f_27.png)

![](img/341360942192527e13828d9d9ebbdb2f_29.png)

![](img/341360942192527e13828d9d9ebbdb2f_30.png)

在上一节创建的任务中，我们配置了基础信息。本节中我们来看看如何配置源码管理，让Jenkins从Git仓库拉取代码。

![](img/341360942192527e13828d9d9ebbdb2f_32.png)

![](img/341360942192527e13828d9d9ebbdb2f_34.png)

根据流程设计，Jenkins需要从GitLab仓库拉取代码。因此，我们需要在“源码管理”部分进行配置。

![](img/341360942192527e13828d9d9ebbdb2f_36.png)

![](img/341360942192527e13828d9d9ebbdb2f_38.png)

1.  勾选 **`Git`**。
    *   前提：Jenkins服务器必须安装了Git工具。如果未安装，需要在Jenkins服务器上执行 `yum install git -y`。
2.  在“Repository URL”中，填入Git仓库的克隆地址。
    *   地址可以从GitLab项目页面获取。推荐使用HTTP协议地址，例如 `http://web.gitlab.com/root/my-lottery.git`。
    *   如果Jenkins服务器无法解析该域名（如 `web.gitlab.com`），需要在Jenkins服务器的 `/etc/hosts` 文件中添加解析记录，例如：
        ```bash
        192.168.0.14 web.gitlab.com
        ```
3.  在“Credentials”（凭据）处添加访问仓库的认证信息。
    *   因为GitLab仓库是私有的，需要授权。点击“添加”按钮，类型选择“Jenkins”。
    *   凭据范围选择“全局”。
    *   类型选择“Username with password”（用户名和密码）。
    *   用户名填写GitLab上有权限访问该仓库的用户（如 `root`）。
    *   密码填写该用户的密码（如 `12345678`）。
    *   描述可以选填，然后点击“添加”。
4.  添加后，在凭据下拉列表中选择刚刚创建的凭据。
5.  在“Branches to build”中指定分支，默认为 `*/master`（主分支）。企业生产环境的代码通常存放在master分支。

![](img/341360942192527e13828d9d9ebbdb2f_40.png)

配置完成后，点击页面底部的“保存”按钮。

![](img/341360942192527e13828d9d9ebbdb2f_42.png)

![](img/341360942192527e13828d9d9ebbdb2f_44.png)

---

![](img/341360942192527e13828d9d9ebbdb2f_46.png)

![](img/341360942192527e13828d9d9ebbdb2f_47.png)

## 执行任务与验证

任务配置保存后，就创建成功了。现在我们可以手动执行这个任务。

![](img/341360942192527e13828d9d9ebbdb2f_49.png)

![](img/341360942192527e13828d9d9ebbdb2f_51.png)

在任务页面，点击左侧的“立即构建”（Build Now）。点击后，Jenkins会开始执行该任务。
*   在“构建历史”区域，会出现一个构建记录（例如 `#1`）。
*   构建状态图标：绿色对勾✅表示成功，红色叉❌表示失败。

![](img/341360942192527e13828d9d9ebbdb2f_53.png)

点击构建编号（如 `#1`），可以查看本次构建的详细控制台输出。如果输出最后显示 `SUCCESS`，则表明任务执行成功。

![](img/341360942192527e13828d9d9ebbdb2f_55.png)

![](img/341360942192527e13828d9d9ebbdb2f_56.png)

![](img/341360942192527e13828d9d9ebbdb2f_58.png)

这个任务执行的操作是：使用Git工具，以指定的凭据身份，克隆指定仓库master分支的代码到Jenkins服务器本地。

![](img/341360942192527e13828d9d9ebbdb2f_60.png)

![](img/341360942192527e13828d9d9ebbdb2f_62.png)

拉取到的代码存放在Jenkins的 `JENKINS_HOME/workspace/` 目录下。例如，任务名为“抽奖任务”，则代码路径为：
```
/var/lib/jenkins/workspace/抽奖任务/
```
该目录下包含了从GitLab仓库拉取的所有项目文件。

![](img/341360942192527e13828d9d9ebbdb2f_64.png)

---

![](img/341360942192527e13828d9d9ebbdb2f_66.png)

## 准备Web服务器

代码已经拉取到Jenkins服务器本地。接下来，我们需要将这些前端页面文件发布到Web服务器上运行。

我们将使用Nginx作为Web服务器。假设我们有两台Nginx服务器，IP地址分别为 `192.168.0.33` 和 `192.168.0.34`。
在每台服务器上安装并启动Nginx：
```bash
yum install nginx -y
systemctl enable nginx --now
```

---

![](img/341360942192527e13828d9d9ebbdb2f_68.png)

![](img/341360942192527e13828d9d9ebbdb2f_70.png)

![](img/341360942192527e13828d9d9ebbdb2f_72.png)

## 编写部署脚本

![](img/341360942192527e13828d9d9ebbdb2f_74.png)

为了将项目从Jenkins服务器发布到多台Nginx服务器，我们需要编写一个自动化部署脚本。

在Jenkins服务器的家目录下创建一个脚本目录并编写脚本：
```bash
cd /var/lib/jenkins
mkdir scripts
cd scripts
vim deploy-nginx.sh
```

脚本 `deploy-nginx.sh` 的核心逻辑如下：
1.  **定义变量**：设置项目路径、Web服务器根目录等。
2.  **打包项目**：进入项目目录，将文件打包成带时间戳的压缩包。
    ```bash
    cd $CODE_DIR
    tar czf /tmp/web-${TIME}.tar.gz .
    ```
3.  **定义目标主机**：创建一个文件（如 `nginx-hosts.txt`），列出所有Web服务器的IP地址。
4.  **循环部署**：遍历主机列表，将压缩包拷贝到每台服务器，解压到特定目录，并配置Nginx软链接。
    ```bash
    for IP in $(cat /var/lib/jenkins/scripts/nginx-hosts.txt)
    do
        scp /tmp/web-${TIME}.tar.gz root@$IP:$WEB_DIR
        ssh root@$IP "cd $WEB_DIR && mkdir web-${TIME}"
        ssh root@$IP "cd $WEB_DIR && tar xf web-${TIME}.tar.gz -C web-${TIME} && rm -f web-${TIME}.tar.gz"
        ssh root@$IP "cd $WEB_DIR && rm -rf html && ln -s web-${TIME} html"
    done
    ```

为了让Jenkins用户能执行此脚本并免密登录到Web服务器，需要进行以下配置：
1.  给脚本添加执行权限：`chmod +x deploy-nginx.sh`。
2.  修改Jenkins用户的默认shell，使其可以登录：
    ```bash
    usermod -s /bin/bash jenkins
    ```
3.  切换到Jenkins用户，生成SSH密钥对，并将公钥分发到所有Web服务器，实现免密登录。
    ```bash
    su - jenkins
    ssh-keygen -t rsa
    for IP in $(cat /var/lib/jenkins/scripts/nginx-hosts.txt); do ssh-copy-id root@$IP; done
    ```

---

## 在任务中集成部署

最后，我们需要修改之前的Jenkins任务，使其在拉取代码后，自动执行我们的部署脚本。

1.  回到“抽奖任务”的配置页面。
2.  找到“构建”区域，点击“增加构建步骤”，选择“执行shell”。
3.  在命令输入框中，填写脚本执行命令：
    ```bash
    /var/lib/jenkins/scripts/deploy-nginx.sh
    ```
4.  保存配置。

![](img/341360942192527e13828d9d9ebbdb2f_76.png)

![](img/341360942192527e13828d9d9ebbdb2f_77.png)

现在，当你再次点击“立即构建”时，Jenkins会先拉取最新代码，然后自动执行部署脚本，将项目发布到 `nginx-hosts.txt` 文件中列出的所有Nginx服务器上。

---

![](img/341360942192527e13828d9d9ebbdb2f_79.png)

本节课中我们一起学习了Jenkins的基础使用。我们创建了一个自由风格任务，配置了从GitLab拉取代码，并编写Shell脚本实现了项目向多台Nginx服务器的自动化部署。这构成了一个最简单的CI/CD工作流雏形。