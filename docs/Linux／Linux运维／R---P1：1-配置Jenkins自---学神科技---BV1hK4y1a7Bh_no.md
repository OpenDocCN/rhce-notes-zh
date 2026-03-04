# GitLab与Jenkins持续集成平台使用方法：1：配置Jenkins自动拉取GitLab代码并部署 🚀

![](img/5b029a3248079439f1daac8a01afa2db_1.png)

![](img/5b029a3248079439f1daac8a01afa2db_2.png)

![](img/5b029a3248079439f1daac8a01afa2db_4.png)

![](img/5b029a3248079439f1daac8a01afa2db_6.png)

![](img/5b029a3248079439f1daac8a01afa2db_8.png)

在本节课中，我们将学习如何配置Jenkins，使其能够自动从GitLab仓库拉取代码，并部署到指定的Web服务器上。我们将从环境检查开始，逐步完成凭据配置、项目构建和自动化部署，最终实现代码提交后自动触发构建和发布的完整流程。

![](img/5b029a3248079439f1daac8a01afa2db_10.png)

---

![](img/5b029a3248079439f1daac8a01afa2db_12.png)

![](img/5b029a3248079439f1daac8a01afa2db_13.png)

## 环境检查与准备

![](img/5b029a3248079439f1daac8a01afa2db_15.png)

![](img/5b029a3248079439f1daac8a01afa2db_17.png)

![](img/5b029a3248079439f1daac8a01afa2db_19.png)

上一节我们完成了GitLab和Jenkins的基础搭建。本节中，我们首先需要确保实验环境运行正常。

![](img/5b029a3248079439f1daac8a01afa2db_21.png)

1.  恢复虚拟机快照，并启动GitLab和Jenkins服务。
2.  清空防火墙规则，确保网络访问畅通。执行以下命令：
    ```bash
    iptables -F
    ```
3.  分别访问GitLab (`http://192.168.1.63`) 和 Jenkins (`http://192.168.1.63:8080`)，验证服务是否正常。
    *   GitLab 登录信息：用户名 `root`，密码 `admin`。
    *   Jenkins 登录信息：用户名 `admin`，密码 `123456`。

---

## 配置SSH密钥实现免密克隆

为了让Jenkins能够无密码地从GitLab拉取代码，我们需要配置SSH密钥对。其核心原理是：将公钥放置在GitLab服务器上，将对应的私钥配置在Jenkins中。

以下是配置步骤：

1.  在Jenkins服务器上，为`root`用户生成SSH密钥对。
    ```bash
    ssh-keygen -t rsa
    ```
    执行后一路回车，使用默认路径和空密码即可。
2.  将生成的公钥（`~/.ssh/id_rsa.pub`文件内容）添加到GitLab。
    *   登录GitLab，点击右上角用户头像 -> **Settings** -> **SSH Keys**。
    *   将公钥内容粘贴到“Key”区域，点击 **Add key**。
3.  测试免密克隆。在Jenkins服务器上执行：
    ```bash
    git clone git@192.168.1.63:root/xuegod-web.git
    ```
    首次连接需要输入`yes`确认主机指纹，之后即可无密码克隆成功。

**核心概念**：SSH密钥认证。公式表示为：`Client(私钥) <-> Server(公钥)`。当客户端使用私钥发起连接时，服务器用对应的公钥进行验证，匹配则授权访问。

![](img/5b029a3248079439f1daac8a01afa2db_23.png)

![](img/5b029a3248079439f1daac8a01afa2db_25.png)

---

![](img/5b029a3248079439f1daac8a01afa2db_27.png)

![](img/5b029a3248079439f1daac8a01afa2db_29.png)

## 在Jenkins中配置GitLab凭据

![](img/5b029a3248079439f1daac8a01afa2db_31.png)

现在，我们需要让Jenkins知晓并使用刚才生成的私钥来连接GitLab。

![](img/5b029a3248079439f1daac8a01afa2db_33.png)

![](img/5b029a3248079439f1daac8a01afa2db_35.png)

![](img/5b029a3248079439f1daac8a01afa2db_37.png)

1.  在Jenkins管理界面，点击 **Manage Jenkins** -> **Manage Credentials**。
2.  在“Stores scoped to Jenkins”下，点击 **Global credentials** -> **Add Credentials**。
3.  按以下信息填写凭据：
    *   **Kind**: SSH Username with private key
    *   **Scope**: Global
    *   **Username**: root
    *   **Private Key**: 选择 **Enter directly**，然后将Jenkins服务器上 `~/.ssh/id_rsa` 文件的内容（即私钥）完整粘贴进来。
4.  点击 **Create** 保存凭据。

---

## 创建Jenkins任务并手动构建

![](img/5b029a3248079439f1daac8a01afa2db_39.png)

![](img/5b029a3248079439f1daac8a01afa2db_41.png)

接下来，我们创建一个Jenkins任务来测试代码拉取功能。

1.  点击 Jenkins 首页的 **新建任务**。
2.  输入任务名称（例如 `xuegod-web-test`），选择 **Freestyle project**，点击 **确定**。
3.  在任务配置页面的 **源码管理** 部分，选择 **Git**。
    *   **Repository URL**: 填写GitLab项目的SSH地址，如 `git@192.168.1.63:root/xuegod-web.git`。
    *   **Credentials**: 选择上一步创建的包含root用户私钥的凭据。
    *   **Branches to build**: 填写要拉取的分支，如 `*/master`。
4.  点击页面底部的 **保存**。
5.  在任务页面，点击 **立即构建**。构建完成后，点击构建历史记录中的链接，查看 **控制台输出**，确认代码拉取成功。

![](img/5b029a3248079439f1daac8a01afa2db_43.png)

代码默认会被拉取到 Jenkins 的工作空间目录，路径通常为：`/var/lib/jenkins/workspace/{任务名称}/`。

![](img/5b029a3248079439f1daac8a01afa2db_45.png)

---

## 配置自动化部署到Web服务器

代码拉取成功后，我们需要将其自动部署到Web服务器（如Apache）。

### 准备Web服务器

![](img/5b029a3248079439f1daac8a01afa2db_47.png)

![](img/5b029a3248079439f1daac8a01afa2db_49.png)

首先，在目标服务器（如192.168.1.63）上安装并配置Apache。

1.  安装Apache HTTP服务器。
    ```bash
    yum install -y httpd
    ```
2.  修改Apache默认端口（因为GitLab可能已占用80端口）。编辑配置文件 `/etc/httpd/conf/httpd.conf`，将 `Listen 80` 改为 `Listen 81`。
3.  启动Apache并设置开机自启。
    ```bash
    systemctl start httpd
    systemctl enable httpd
    ```
4.  配置Jenkins服务器到Web服务器的SSH免密登录。
    *   在Jenkins服务器上，将root用户的公钥拷贝到Web服务器。
    ```bash
    ssh-copy-id root@192.168.1.63
    ```
    *   根据提示输入Web服务器root用户的密码。

### 在Jenkins任务中添加部署步骤

![](img/5b029a3248079439f1daac8a01afa2db_51.png)

![](img/5b029a3248079439f1daac8a01afa2db_53.png)

![](img/5b029a3248079439f1daac8a01afa2db_54.png)

现在，回到Jenkins任务配置，添加一个构建后执行的Shell脚本，用于文件传输。

![](img/5b029a3248079439f1daac8a01afa2db_56.png)

![](img/5b029a3248079439f1daac8a01afa2db_58.png)

1.  在任务配置页面，找到 **构建** 部分，点击 **增加构建步骤**，选择 **Execute shell**。
2.  在命令输入框中，编写部署脚本。例如，将工作空间的所有文件同步到Web服务器的网站目录：
    ```bash
    # 假设Web服务器网站目录为 /var/www/html/
    scp -r * root@192.168.1.63:/var/www/html/
    ```
    *   `scp -r *`：递归拷贝当前目录所有文件。
    *   `root@192.168.1.63:/var/www/html/`：目标服务器用户和目录。
3.  点击 **保存**。
4.  再次点击 **立即构建**。构建成功后，访问 `http://192.168.1.63:81`，即可看到部署的网站内容。

![](img/5b029a3248079439f1daac8a01afa2db_60.png)

**部署多台服务器**：只需在Shell脚本中增加多条`scp`命令，指向不同的服务器地址即可。

---

## 配置GitLab Webhook实现自动触发

![](img/5b029a3248079439f1daac8a01afa2db_62.png)

![](img/5b029a3248079439f1daac8a01afa2db_64.png)

目前我们的构建和部署是手动的。最后一步是实现自动化：当开发者向GitLab推送代码时，自动触发Jenkins执行构建和部署。

![](img/5b029a3248079439f1daac8a01afa2db_66.png)

![](img/5b029a3248079439f1daac8a01afa2db_67.png)

![](img/5b029a3248079439f1daac8a01afa2db_69.png)

![](img/5b029a3248079439f1daac8a01afa2db_71.png)

![](img/5b029a3248079439f1daac8a01afa2db_73.png)

1.  **在Jenkins任务中配置触发器**：
    *   进入任务配置页面，找到 **构建触发器** 部分。
    *   勾选 **Build when a change is pushed to GitLab**。系统会显示一个URL（如 `http://192.168.1.63:8080/project/xuegod-web-test`），复制此URL。
2.  **在GitLab项目中配置Webhook**：
    *   登录GitLab，进入你的项目（如 `xuegod-web`）。
    *   进入 **Settings** -> **Webhooks**。
    *   在 **URL** 字段中，粘贴上一步复制的Jenkins触发器URL。
    *   在 **Secret token** 部分，如果需要，可以在Jenkins任务的GitLab配置中生成并填写令牌，以增强安全性。
    *   取消勾选 **Enable SSL verification**（如果使用HTTP）。
    *   点击 **Add webhook**。
3.  **测试Webhook**：
    *   在GitLab的Webhook页面底部，点击 **Test** -> **Push events**。
    *   如果配置成功，GitLab会显示“Hook executed successfully”，并且Jenkins会立即开始一个新的构建任务。

![](img/5b029a3248079439f1daac8a01afa2db_75.png)

至此，一个完整的自动化流程已经建立：代码推送至GitLab -> GitLab通过Webhook通知Jenkins -> Jenkins拉取代码并执行部署脚本 -> 网站更新。

![](img/5b029a3248079439f1daac8a01afa2db_77.png)

---

![](img/5b029a3248079439f1daac8a01afa2db_79.png)

本节课中我们一起学习了如何配置Jenkins与GitLab的持续集成环境。我们从环境检查、SSH密钥配置开始，逐步完成了在Jenkins中创建任务、手动拉取代码、编写Shell脚本实现自动化部署，最后通过配置GitLab Webhook实现了代码提交后的全自动构建与部署流程。这套流程是DevOps实践中代码持续集成与持续部署（CI/CD）的基础。