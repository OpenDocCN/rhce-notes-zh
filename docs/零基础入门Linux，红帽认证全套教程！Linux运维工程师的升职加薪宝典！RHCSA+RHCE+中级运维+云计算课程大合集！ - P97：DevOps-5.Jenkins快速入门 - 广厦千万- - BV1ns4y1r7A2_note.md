# Linux运维与DevOps：P97：Jenkins快速入门

在本节课中，我们将学习如何通过Jenkins实现自动化项目发布，并了解如何从代码仓库获取项目、编写发布脚本，以及通过Jenkins任务一键完成部署。我们还将探讨项目回滚和GitLab的一些基础配置。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_1.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_3.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_5.png)

## 配置SSH免密登录与脚本验证

![](img/5c2dfadf1d28a3bea5cac8c46252db64_7.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_9.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_11.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_13.png)

上一节我们介绍了项目发布的基本流程，本节中我们来看看如何配置服务器间的免密登录，并验证发布脚本。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_15.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_17.png)

首先，需要在Jenkins服务器上生成SSH密钥对，并将公钥分发到目标Web服务器。以下是生成和分发密钥的命令：

![](img/5c2dfadf1d28a3bea5cac8c46252db64_19.png)

```bash
ssh-keygen -t rsa
ssh-copy-id root@目标服务器IP
```

![](img/5c2dfadf1d28a3bea5cac8c46252db64_21.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_23.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_24.png)

执行`ssh-copy-id`命令时，首次连接需要输入`yes`进行验证，然后输入目标服务器的root密码。公钥下发成功后，再次执行远程脚本就无需输入密码了。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_26.png)

## 验证项目文件同步

![](img/5c2dfadf1d28a3bea5cac8c46252db64_28.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_30.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_31.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_32.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_34.png)

完成免密配置后，我们执行发布脚本。脚本会将项目文件从Jenkins服务器推送到指定的Web服务器。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_36.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_38.png)

以下是查看Web服务器上同步目录的命令：

![](img/5c2dfadf1d28a3bea5cac8c46252db64_40.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_42.png)

```bash
ls -l /usr/share/nginx/
```

![](img/5c2dfadf1d28a3bea5cac8c46252db64_44.png)

在Web服务器的`/usr/share/nginx/`目录下，可以看到解压后的项目目录。这些目录中的文件就是我们通过脚本拷贝过来的项目文件。

## 理解Web服务目录结构

![](img/5c2dfadf1d28a3bea5cac8c46252db64_46.png)

项目文件被同步到`/usr/share/nginx/`目录后，需要被Web服务访问。通常，Nginx的默认网站根目录`/usr/share/nginx/html`是一个指向`/usr/share/nginx/`下某个子目录的软链接。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_48.png)

我们可以通过以下命令查看软链接指向：

![](img/5c2dfadf1d28a3bea5cac8c46252db64_50.png)

```bash
ls -l /usr/share/nginx/html
```

![](img/5c2dfadf1d28a3bea5cac8c46252db64_52.png)

只要确保软链接正确指向包含项目文件的目录，用户通过浏览器访问服务器IP时，就能看到部署的网页。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_54.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_56.png)

## 访问发布的网页

![](img/5c2dfadf1d28a3bea5cac8c46252db64_58.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_60.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_62.png)

在浏览器中访问Web服务器的IP地址（例如`http://192.168.0.34`），刷新页面，即可看到刚刚发布的抽奖项目页面。

同样，访问另一台Web服务器（例如`http://192.168.0.33`），由于发布的是相同的项目，看到的也是相同的页面。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_64.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_66.png)

## 获取与更换项目模板

上述演示的页面模板可以从代码托管平台获取。例如，可以从Gitee（国内版的GitHub，常被称为“码云”）搜索并下载HTML模板。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_68.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_70.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_72.png)

以下是搜索和下载项目模板的步骤：
1.  访问Gitee官网。
2.  在“开源软件”或搜索栏中，搜索如“html 抽奖页面”等关键词。
3.  选择合适的项目，通过`git clone`或直接下载压缩包的方式获取代码。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_74.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_76.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_77.png)

我们可以下载新的模板（如一个阿里巴巴风格的首页），替换服务器上的旧页面，来演示项目更新。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_79.png)

## 配置Jenkins自动化构建

![](img/5c2dfadf1d28a3bea5cac8c46252db64_81.png)

手动执行脚本不够智能。我们可以将脚本集成到Jenkins任务中，实现一键构建和部署。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_83.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_85.png)

以下是配置步骤：
1.  在Jenkins中进入对应的任务（如“抽奖任务”）配置页面。
2.  找到“构建”环节。
3.  点击“增加构建步骤”，选择“Execute shell”。
4.  在命令框中，填写执行脚本的命令，例如：`bash /opt/scripts/deploy.sh`
5.  保存配置。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_87.png)

配置完成后，只需在Jenkins任务界面点击“Build Now”，Jenkins就会自动执行拉取代码和运行部署脚本的流程。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_89.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_91.png)

## 实现项目回滚

![](img/5c2dfadf1d28a3bea5cac8c46252db64_93.png)

如果新发布的项目有问题，需要回滚到上一个版本。由于旧版本的项目目录仍然保留在服务器上，因此可以快速回滚。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_95.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_97.png)

以下是手动回滚的命令示例：

![](img/5c2dfadf1d28a3bea5cac8c46252db64_99.png)

```bash
# 删除当前错误的软链接
rm -f /usr/share/nginx/html
# 重新创建软链接，指向旧版本的项目目录
ln -s /usr/share/nginx/旧项目目录 /usr/share/nginx/html
```

![](img/5c2dfadf1d28a3bea5cac8c46252db64_101.png)

![](img/5c2dfadf1d28a3bea5cac8c46252db64_103.png)

执行后，刷新浏览器，页面就会恢复到之前的版本。同理，也可以再次切换回新版本。这种保留历史版本目录的方式，为回滚提供了便利。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_105.png)

## 不同项目的发布策略

![](img/5c2dfadf1d28a3bea5cac8c46252db64_107.png)

对于不同的项目，最好使用独立的部署脚本，但核心思路一致。目前演示的是静态HTML项目，无需编译。如果发布Java等需要编译和解决依赖的项目，流程会更为复杂，但整体“拉取->构建->打包->发布”的流水线思想是相同的。

这个自动化流程可以概括为：开发人员将代码提交到Git仓库，Jenkins拉取代码，进行必要的构建和打包，最后将产物发布到生产或测试环境。

## 配置GitLab SSH密钥认证

在推送代码到GitLab时，为了避免每次输入密码，可以配置SSH密钥认证。

以下是配置步骤：
1.  在本地机器生成SSH密钥对：`ssh-keygen -t rsa`
2.  查看并复制公钥内容：`cat ~/.ssh/id_rsa.pub`
3.  登录GitLab，进入个人设置（Edit profile）。
4.  找到“SSH Keys”选项，将公钥粘贴进去并添加。

配置完成后，本地机器向该GitLab账户下的仓库推送（push）或拉取（pull）代码时，就不再需要输入用户名和密码了。

## GitLab界面定制与管理

最后，我们了解一下GitLab的一些界面定制和管理功能。

以下是几个可配置的选项：
*   **定制Logo与标题**：在管理员后台的“外观”设置中，可以上传自定义Logo、修改页面标题和描述，用于展示公司信息。
*   **主题与高亮**：在“偏好设置”中，可以修改导航主题颜色和代码语法高亮样式。
*   **注册限制**：在“设置”中，可以禁用公开注册功能。禁用后，新用户只能由管理员创建，这更适合企业内部管理。

![](img/5c2dfadf1d28a3bea5cac8c46252db64_109.png)

本节课中我们一起学习了通过Jenkins实现自动化项目发布的完整流程，包括环境配置、脚本编写、Jenkins集成、版本回滚，以及GitLab的基本管理和SSH认证配置。掌握这些技能，是迈向CI/CD和自动化运维的重要一步。