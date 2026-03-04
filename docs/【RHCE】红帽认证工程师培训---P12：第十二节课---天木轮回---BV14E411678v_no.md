# RHCE红帽认证工程师培训课程：P12：第九章节 - 远程控制与网站服务

![](img/220f286889923d8424b6d06ee0f7146d_1.png)

![](img/220f286889923d8424b6d06ee0f7146d_2.png)

![](img/220f286889923d8424b6d06ee0f7146d_4.png)

在本节课中，我们将要学习两个核心的网络服务：SSH远程控制服务和Apache网站服务。我们将从SSH服务的基础配置开始，逐步深入到密钥认证、文件传输以及会话管理。随后，我们将转向Apache网站服务，学习其部署、配置，并理解SELinux安全子系统如何影响服务的访问控制。通过实践操作，你将掌握配置和管理这些关键服务的基本技能。

## 9.2：远程控制服务（SSH）

![](img/220f286889923d8424b6d06ee0f7146d_6.png)

![](img/220f286889923d8424b6d06ee0f7146d_7.png)

上一节我们介绍了课程的整体规划，本节中我们来看看如何通过SSH服务实现远程控制。SSH（Secure Shell）协议允许我们安全地连接到远程服务器并进行管理。

### 配置网络连通性

![](img/220f286889923d8424b6d06ee0f7146d_9.png)

![](img/220f286889923d8424b6d06ee0f7146d_10.png)

在进行任何服务配置之前，必须确保网络是连通的。我们使用两台虚拟机：服务端（192.168.10.10）和客户端（192.168.10.20）。

**验证网络连通性命令：**
```bash
ping 192.168.10.20
```

![](img/220f286889923d8424b6d06ee0f7146d_12.png)

![](img/220f286889923d8424b6d06ee0f7146d_13.png)

### 基础SSH连接

在客户端上，使用`ssh`命令即可连接到服务端。

**连接命令格式：**
```bash
ssh [用户名]@[服务器IP地址]
```
例如，连接服务端：
```bash
ssh root@192.168.10.10
```
首次连接时会提示接受主机密钥，输入服务端对应用户的密码后即可建立连接。连接成功后，在客户端执行的命令（如`reboot`）实际上会在服务端生效，这证明了远程控制的实现。

![](img/220f286889923d8424b6d06ee0f7146d_15.png)

### 配置SSH服务

Linux中一切皆文件，配置服务即是修改其配置文件。SSH服务的主配置文件路径遵循一般规律。

![](img/220f286889923d8424b6d06ee0f7146d_17.png)

![](img/220f286889923d8424b6d06ee0f7146d_19.png)

**SSH主配置文件路径：**
```
/etc/ssh/sshd_config
```

配置文件中的内容分为注释信息（以`#`或`;`开头，通常显示为蓝色）和配置参数。我们只需关注并修改配置参数。

![](img/220f286889923d8424b6d06ee0f7146d_21.png)

![](img/220f286889923d8424b6d06ee0f7146d_23.png)

以下是几个常见的配置示例：

![](img/220f286889923d8424b6d06ee0f7146d_25.png)

![](img/220f286889923d8424b6d06ee0f7146d_27.png)

1.  **修改默认端口**：为了安全，可以修改SSH服务的监听端口。
    找到 `#Port 22` 这一行，去掉`#`并将`22`改为其他端口（如`3389`）。
    ```
    Port 3389
    ```
    修改后，连接时需要指定端口：`ssh -p 3389 root@192.168.10.10`

![](img/220f286889923d8424b6d06ee0f7146d_29.png)

![](img/220f286889923d8424b6d06ee0f7146d_30.png)

2.  **禁止root用户远程登录**：降低安全风险。
    找到 `#PermitRootLogin yes` 这一行，去掉`#`并将`yes`改为`no`。
    ```
    PermitRootLogin no
    ```

**重要原则**：修改服务的配置文件后，必须重启该服务才能使新配置生效。

**重启SSH服务命令：**
```bash
systemctl restart sshd
```
此外，为了确保服务器重启后服务自动运行，应将其加入开机自启。
```bash
systemctl enable sshd
```

![](img/220f286889923d8424b6d06ee0f7146d_32.png)

### 密钥认证

![](img/220f286889923d8424b6d06ee0f7146d_34.png)

![](img/220f286889923d8424b6d06ee0f7146d_35.png)

相比于密码，使用密钥对进行认证更安全、更方便。其原理是在客户端生成一对密钥（公钥和私钥），将公钥上传至服务端。之后客户端连接时，即可免密码登录。

**配置步骤如下：**

![](img/220f286889923d8424b6d06ee0f7146d_37.png)

![](img/220f286889923d8424b6d06ee0f7146d_38.png)

1.  **在客户端生成密钥对**：
    ```bash
    ssh-keygen
    ```
    执行命令后，连续按回车接受默认设置（密钥存储路径为`~/.ssh/id_rsa`，不设置密钥密码）。生成后，私钥为`~/.ssh/id_rsa`，公钥为`~/.ssh/id_rsa.pub`。

![](img/220f286889923d8424b6d06ee0f7146d_40.png)

![](img/220f286889923d8424b6d06ee0f7146d_42.png)

2.  **将公钥上传至服务端**：
    ```bash
    ssh-copy-id root@192.168.10.10
    ```
    输入服务端root用户的密码，即可将公钥传送到服务端对应用户的`~/.ssh/authorized_keys`文件中。

![](img/220f286889923d8424b6d06ee0f7146d_44.png)

![](img/220f286889923d8424b6d06ee0f7146d_46.png)

3.  **测试密钥登录**：
    再次执行`ssh root@192.168.10.10`，将不再需要输入密码。

4.  **（可选）禁用密码认证，仅允许密钥登录**：
    在服务端的`/etc/ssh/sshd_config`文件中，找到`#PasswordAuthentication yes`，修改为：
    ```
    PasswordAuthentication no
    ```
    重启`sshd`服务后，任何没有对应私钥的尝试都将无法登录，即使知道密码。

### 安全文件传输（SCP）

基于SSH协议，我们可以使用`scp`命令在本地和远程主机之间安全地传输文件。

![](img/220f286889923d8424b6d06ee0f7146d_48.png)

![](img/220f286889923d8424b6d06ee0f7146d_50.png)

![](img/220f286889923d8424b6d06ee0f7146d_51.png)

**命令格式：**
- **上传文件到远程主机**：
  ```bash
  scp [本地文件路径] [用户名]@[远程主机IP]:[远程目录路径]
  ```
  例如：`scp /home/client/file.txt root@192.168.10.10:/root/`
- **从远程主机下载文件**：
  ```bash
  scp [用户名]@[远程主机IP]:[远程文件路径] [本地目录路径]
  ```
  例如：`scp root@192.168.10.10:/root/file.txt /home/client/`

![](img/220f286889923d8424b6d06ee0f7146d_53.png)

![](img/220f286889923d8424b6d06ee0f7146d_55.png)

### 会话管理工具（Screen）

![](img/220f286889923d8424b6d06ee0f7146d_57.png)

![](img/220f286889923d8424b6d06ee0f7146d_58.png)

![](img/220f286889923d8424b6d06ee0f7146d_59.png)

在进行长时间远程任务时，网络中断可能导致任务终止。`screen`工具可以创建持久会话，防止任务中断，并支持会话共享。

**安装screen：**
首先需要配置Yum仓库（后续章节详解），然后安装。
```bash
yum install -y screen
```

![](img/220f286889923d8424b6d06ee0f7146d_61.png)

**基本使用：**
- **创建新会话**：`screen -S [会话名]`
- **列出所有会话**：`screen -ls`
- **恢复指定会话**：`screen -r [会话名或PID]`
- **会话共享**：用户A创建会话后，用户B使用`screen -x [会话名]`即可加入同一会话，实现屏幕共享和协同操作。

---

![](img/220f286889923d8424b6d06ee0f7146d_63.png)

## 10.1 - 10.3：网站服务（Apache）与SELinux

上一节我们掌握了远程管理，本节中我们来看看如何部署和配置一个基础的网站服务——Apache HTTP Server（简称Apache）。

![](img/220f286889923d8424b6d06ee0f7146d_65.png)

![](img/220f286889923d8424b6d06ee0f7146d_67.png)

![](img/220f286889923d8424b6d06ee0f7146d_68.png)

![](img/220f286889923d8424b6d06ee0f7146d_69.png)

### 为什么学习Apache？

![](img/220f286889923d8424b6d06ee0f7146d_71.png)

1.  **历史悠久且稳定**：Apache是许多Linux发行版默认的网页服务器，久经考验，稳定性和安全性俱佳。
2.  **应用广泛**：学习Apache有助于理解Web服务原理，通用于多数环境。
3.  **考试重点**：在RHCE认证考试中，Apache服务的配置与管理是重要考点。

![](img/220f286889923d8424b6d06ee0f7146d_73.png)

### 安装与启动Apache

1.  **安装Apache软件包**：
    软件包名称为`httpd`，协议名称为`http`。
    ```bash
    yum install -y httpd
    ```

![](img/220f286889923d8424b6d06ee0f7146d_75.png)

![](img/220f286889923d8424b6d06ee0f7146d_76.png)

2.  **启动并设置开机自启**：
    ```bash
    systemctl start httpd
    systemctl enable httpd
    ```

3.  **配置防火墙**（如果开启）：
    允许`http`服务通过防火墙。
    ```bash
    firewall-cmd --permanent --add-service=http
    firewall-cmd --reload
    ```

![](img/220f286889923d8424b6d06ee0f7146d_78.png)

![](img/220f286889923d8424b6d06ee0f7146d_80.png)

4.  **测试访问**：
    在浏览器中输入服务器IP地址（如`http://192.168.10.10`）。如果看到Apache的默认测试页，说明服务安装成功。

### 部署网站页面

Apache默认的网站数据目录是`/var/www/html/`。网站首页文件通常命名为`index.html`。

![](img/220f286889923d8424b6d06ee0f7146d_82.png)

**创建首页文件：**
```bash
echo "Hello, This is my website." > /var/www/html/index.html
```
创建后，刷新浏览器即可看到自定义内容。

### 修改网站默认目录

有时我们需要更改网站数据的存储位置。

1.  **编辑Apache主配置文件**：
    ```
    /etc/httpd/conf/httpd.conf
    ```
2.  **找到定义文档根目录的指令**（约第119行）：
    ```
    DocumentRoot "/var/www/html"
    ```
    将其修改为目标目录，例如：`DocumentRoot "/home/wwwroot"`
3.  **找到对应目录的权限块**（约第124行）：
    ```
    <Directory "/var/www/html">
    ```
    将其中的路径也修改为`/home/wwwroot`。
4.  **创建新目录并添加首页文件**：
    ```bash
    mkdir -p /home/wwwroot
    echo "New Website Location" > /home/wwwroot/index.html
    ```
5.  **重启Apache服务**：
    ```bash
    systemctl restart httpd
    ```

![](img/220f286889923d8424b6d06ee0f7146d_84.png)

完成以上步骤后，访问网站可能会看到“403 Forbidden”错误。这通常不是权限问题，而是受到了SELinux的限制。

### 理解并配置SELinux

![](img/220f286889923d8424b6d06ee0f7146d_86.png)

SELinux（Security-Enhanced Linux）是一个由美国国家安全局和开源社区共同开发的安全子系统，其核心目标是实施“最小权限原则”，即进程只能访问其完成任务所必需的资源。

![](img/220f286889923d8424b6d06ee0f7146d_88.png)

SELinux主要通过两种机制实现控制：
- **域（Domain）**：限制进程（服务）的能力。
- **安全上下文（Security Context）**：给文件、目录、端口等资源打上标签。只有当进程的域和资源的安全上下文匹配时，访问才被允许。

![](img/220f286889923d8424b6d06ee0f7146d_90.png)

**处理网站目录的SELinux问题：**

![](img/220f286889923d8424b6d06ee0f7146d_92.png)

![](img/220f286889923d8424b6d06ee0f7146d_94.png)

我们自定义的网站目录（`/home/wwwroot`）原有的安全上下文与Web服务不匹配，导致Apache无法访问。

![](img/220f286889923d8424b6d06ee0f7146d_96.png)

1.  **查看安全上下文**：
    ```bash
    ls -ldZ /var/www/html/
    ls -ldZ /home/wwwroot/
    ```
    对比两者，`/var/www/html/`的上下文包含`httpd_sys_content_t`，这表示该目录内容可供Apache进程读取。

![](img/220f286889923d8424b6d06ee0f7146d_98.png)

![](img/220f286889923d8424b6d06ee0f7146d_99.png)

![](img/220f286889923d8424b6d06ee0f7146d_101.png)

2.  **修改目录的安全上下文**：
    使用`semanage fcontext`命令修改默认规则，然后用`restorecon`命令应用更改。
    ```bash
    # 添加一条新的默认上下文规则
    semanage fcontext -a -t httpd_sys_content_t "/home/wwwroot(/.*)?"
    # 应用新的上下文规则到目录及其现有内容
    restorecon -Rv /home/wwwroot/
    ```
    **注意**：`semanage`命令可能需要安装`policycoreutils-python`包。

![](img/220f286889923d8424b6d06ee0f7146d_103.png)

![](img/220f286889923d8424b6d06ee0f7146d_105.png)

3.  **再次测试访问**：
    刷新浏览器，现在应该可以正常显示`/home/wwwroot/index.html`的内容了。

![](img/220f286889923d8424b6d06ee0f7146d_107.png)

**SELinux工作模式**：
- **enforcing**：强制模式，违反策略的行为将被阻止并记录。
- **permissive**：宽容模式，违反策略的行为只会被记录，不会被阻止。常用于调试。
- **disabled**：禁用模式（不推荐）。

**查看与临时修改模式**：
```bash
# 查看当前模式
getenforce
# 临时设置为宽容模式
setenforce 0
# 临时设置为强制模式
setenforce 1
```
永久修改需编辑`/etc/selinux/config`文件，将`SELINUX=`的值改为`enforcing`、`permissive`或`disabled`，然后重启系统。

![](img/220f286889923d8424b6d06ee0f7146d_109.png)

![](img/220f286889923d8424b6d06ee0f7146d_110.png)

---

## 总结

本节课中我们一起学习了两个至关重要的Linux网络服务。
1.  **SSH服务**：我们学习了如何进行远程连接、修改服务配置（如端口、禁用root登录）、配置更安全的密钥认证、使用SCP进行文件传输，以及利用screen工具管理持久会话。
2.  **Apache网站服务**：我们完成了Apache的安装、启动、部署简单网页，并实践了修改网站根目录。在此过程中，我们遇到了SELinux安全子系统的拦截，通过理解其“安全上下文”机制，使用`semanage`和`restorecon`命令修正了问题，使服务恢复正常。

![](img/220f286889923d8424b6d06ee0f7146d_112.png)

这些操作是Linux系统管理员日常工作的基础。请务必动手完成实验，特别是尝试修改Apache的默认目录并解决随之而来的SELinux问题，这将极大地加深你对服务配置和安全管理的理解。