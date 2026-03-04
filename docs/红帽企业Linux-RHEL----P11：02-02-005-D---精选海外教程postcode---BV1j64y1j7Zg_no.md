# 红帽企业Linux RHEL 9精通课程：P11：部署、配置与维护系统 🚀

![](img/afdde4fb0659d602677cc3c5e9cf45f2_1.png)

在本节课中，我们将学习如何部署、配置和维护RHEL系统。核心内容包括：配置Yum软件仓库、使用Yum安装软件包、管理系统服务、使用`cron`和`at`调度任务、管理系统启动目标以及配置时间同步服务。掌握这些技能是成为一名合格的红帽认证系统管理员（RHCSA）的基础。

---

![](img/afdde4fb0659d602677cc3c5e9cf45f2_3.png)

## 配置Yum软件仓库 📦

![](img/afdde4fb0659d602677cc3c5e9cf45f2_5.png)

![](img/afdde4fb0659d602677cc3c5e9cf45f2_7.png)

为了在系统中安装软件包，首先需要配置软件仓库。Yum仓库的配置文件位于`/etc/yum.repos.d/`目录中。

以下是查看该目录内容的命令：
```bash
cd /etc/yum.repos.d/
ls
```
所有位于此目录下的`.repo`文件都会被Yum自动识别和使用。

### 理解仓库配置文件

让我们查看一个示例仓库文件，例如`redhat.repo`。理解其结构对于创建或复制仓库配置至关重要。

一个仓库配置文件通常包含多个用方括号`[]`定义的仓库部分。方括号内的名称是该仓库的唯一标识符。

在每个仓库部分中，包含以下主要指令：
*   **`name`**： 提供该仓库的描述性名称。
*   **`baseurl`** 或 **`mirrorlist`**： 指定软件包的实际下载地址。`mirrorlist`指向一个镜像列表，当无法连接`baseurl`时使用。
*   **`enabled`**： 控制是否启用此仓库。`1`表示启用，`0`表示禁用。
*   **`gpgcheck`**： 控制是否进行GPG签名验证。`1`表示启用检查，`0`表示禁用。
*   **`gpgkey`**： 指定用于验证的GPG密钥文件的位置。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_9.png)

将新的`.repo`文件放入`/etc/yum.repos.d/`目录，系统就会自动识别该仓库。

---

![](img/afdde4fb0659d602677cc3c5e9cf45f2_11.png)

![](img/afdde4fb0659d602677cc3c5e9cf45f2_12.png)

## 使用Yum安装软件包 🔧

![](img/afdde4fb0659d602677cc3c5e9cf45f2_14.png)

上一节我们介绍了如何配置软件源，本节我们将使用Yum工具来安装软件包。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_16.png)

![](img/afdde4fb0659d602677cc3c5e9cf45f2_18.png)

我们将安装`httpd`软件包（Apache HTTP服务器），因为RHEL的某些基础镜像默认不包含它。使用`-y`参数可以在安装过程中自动确认。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_20.png)

安装命令如下：
```bash
sudo yum -y install httpd
```
这个软件包将提供我们需要管理的服务。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_22.png)

![](img/afdde4fb0659d602677cc3c5e9cf45f2_24.png)

---

## 管理系统服务 ⚙️

安装好`httpd`软件包后，我们现在来学习如何启动、启用和检查其服务状态。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_26.png)

首先，启动`httpd`服务：
```bash
sudo systemctl start httpd
```
然后，检查服务的运行状态：
```bash
sudo systemctl status httpd
```
如果状态显示为`active (running)`，则表示服务已成功启动。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_28.png)

接下来，启用服务，使其在系统启动时自动运行：
```bash
sudo systemctl enable httpd
```
可以再次使用`status`命令确认服务已启用（`enabled`）。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_30.png)

如果需要停止服务，可以运行：
```bash
sudo systemctl stop httpd
```
在本教程中，我们将保持`httpd`服务运行，以便用于后续示例。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_32.png)

---

## 使用Cron和At调度任务 ⏰

有时，安排任务定期运行或在未来某个特定时间运行一次是非常必要的。为此，我们使用`cron`和`at`命令。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_34.png)

### 使用Cron进行周期性调度

`cron`用于调度周期性任务。系统级的`cron`配置位于`/etc/crontab`。查看其格式：
```bash
cat /etc/crontab
```
输出显示了`cron`条目的标准格式：**分钟 小时 日 月 星期 用户名 要执行的命令**。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_36.png)

![](img/afdde4fb0659d602677cc3c5e9cf45f2_38.png)

除了直接编辑`/etc/crontab`，系统还预定义了`/etc/cron.hourly/`、`/etc/cron.daily/`、`/etc/cron.weekly/`、`/etc/cron.monthly/`等目录。将脚本放入这些目录，它们就会在相应的时间周期内自动运行。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_40.png)

对于用户自定义的任务，可以使用`crontab`命令编辑当前用户的`cron`表：
```bash
crontab -e
```
在编辑器中，可以按照上述格式添加条目。例如，添加一个在每天10点以`cloud-user`身份运行`ls`命令的任务：
```
0 10 * * * cloud-user ls
```
保存并退出后，该任务即被添加。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_42.png)

要列出当前用户的所有`cron`任务：
```bash
crontab -l
```
要删除所有`cron`任务，可以使用：
```bash
crontab -r
```
用户定义的`cron`表文件存储在`/var/spool/cron/`目录下，以用户名命名。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_44.png)

![](img/afdde4fb0659d602677cc3c5e9cf45f2_46.png)

### 使用At进行一次性调度

![](img/afdde4fb0659d602677cc3c5e9cf45f2_48.png)

与`cron`不同，`at`命令用于安排任务在未来某个特定时间点**运行一次**。其语法非常简单：`at [时间]`。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_50.png)

例如，安排一个在下午3点创建目录的任务：
```bash
at 3:00 PM
at> mkdir /tmp/test
at> [按 Ctrl+D 退出]
```
命令输入完毕后，按`Ctrl+D`保存并退出`at`提示符。

要查看已安排的`at`作业队列：
```bash
atq
```
输出会显示作业编号、运行时间和队列信息。

要查看某个作业的详细信息（包括要运行的命令）：
```bash
at -c [作业编号]
```
要删除一个已安排的作业：
```bash
atrm [作业编号]
```
或者使用：
```bash
at -d [作业编号]
```

---

![](img/afdde4fb0659d602677cc3c5e9cf45f2_52.png)

## 管理系统启动目标 🎯

在RHEL 7及更高版本中，系统使用“目标”（target）来替代旧版本的运行级别（runlevel）。目标定义了系统启动时要激活的一组服务。

要查看当前的默认启动目标：
```bash
systemctl get-default
```
通常，服务器会设置为`multi-user.target`（多用户命令行模式）。

![](img/afdde4fb0659d602677cc3c5e9cf45f2_54.png)

要列出所有已加载的目标单元：
```bash
systemctl list-units --type=target
```
要切换到另一个目标（例如救援模式）：
```bash
sudo systemctl isolate rescue.target
```
**注意**：`isolate`命令只改变当前运行的目标，不会改变默认目标。重启后系统仍会进入默认目标。

要永久更改系统启动时的默认目标：
```bash
sudo systemctl set-default graphical.target
```
此命令会将默认目标设置为图形界面模式。

常用的目标包括：
*   `multi-user.target`： 多用户命令行模式（服务器常用）。
*   `graphical.target`： 图形界面模式。
*   `rescue.target`： 救援模式，用于系统故障修复。

要立即进入救援模式，可以使用：
```bash
sudo systemctl rescue
```

---

## 配置时间同步服务 🕐

保持多台服务器的时间同步至关重要。在RHEL中，我们使用`chrony`服务来实现网络时间协议（NTP）同步。

首先，确保已安装`chrony`软件包（通常默认安装）：
```bash
sudo yum -y install chrony
```
启动`chrony`服务并设置开机自启：
```bash
sudo systemctl start chronyd
sudo systemctl enable chronyd
```
检查服务状态：
```bash
sudo systemctl status chronyd
```
主要的配置文件是`/etc/chrony.conf`。要添加或更改NTP服务器，可以编辑此文件，加入类似下面的行：
```
server ntp.server.address iburst
```
修改配置后，需要重启`chrony`服务以使更改生效：
```bash
sudo systemctl restart chronyd
```

---

## 总结 📝

![](img/afdde4fb0659d602677cc3c5e9cf45f2_56.png)

![](img/afdde4fb0659d602677cc3c5e9cf45f2_57.png)

本节课我们一起学习了RHEL系统部署、配置与维护的核心技能。我们涵盖了：配置Yum软件仓库以获取软件包；使用`yum`安装软件；使用`systemctl`管理服务生命周期；利用`cron`和`at`工具灵活调度任务；理解并管理系统启动目标；以及配置`chrony`服务确保时间同步。这些是日常系统管理的基础，也是RHCSA认证考核的重点内容。