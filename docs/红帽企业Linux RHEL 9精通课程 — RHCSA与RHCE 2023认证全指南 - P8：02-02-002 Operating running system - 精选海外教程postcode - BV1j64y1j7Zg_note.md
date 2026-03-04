# 红帽企业Linux RHEL 9精通课程：P8：02-02-002 操作运行中的系统 🖥️

![](img/365ea8b87f118ff877bf8a0e620c128c_1.png)

在本节课中，我们将学习如何操作一个正在运行的RHEL系统。内容包括安全地关闭与重启系统、在紧急情况下重置root密码、管理系统服务、监控系统进程与资源，以及在远程系统间安全地复制文件。这些是系统管理员日常维护和故障排除的核心技能。

## 安全关闭与重启系统 🔌

正常关闭或重启系统非常重要，不能直接拔掉电源或关闭开关。这可能导致正在运行的进程出现问题。

要关闭系统，可以使用 `systemctl` 命令。同样，重启系统也使用 `systemctl`。

以下是相关命令：
*   关闭系统：`systemctl poweroff`
*   重启系统：`systemctl reboot`

![](img/365ea8b87f118ff877bf8a0e620c128c_3.png)

您可以使用 `systemctl --help` 或 `man systemctl` 查看更多选项。执行这些命令通常需要 `root` 权限。

![](img/365ea8b87f118ff877bf8a0e620c128c_5.png)

**注意**：在Red Hat的早期版本中，可以直接使用 `poweroff` 或 `reboot` 命令。但现在推荐使用 `systemctl` 作为前缀。

![](img/365ea8b87f118ff877bf8a0e620c128c_7.png)

![](img/365ea8b87f118ff877bf8a0e620c128c_9.png)

## 中断启动过程以重置Root密码 🔑

![](img/365ea8b87f118ff877bf8a0e620c128c_11.png)

有时您可能被系统拒之门外，无法访问。管理无法访问的系统是不可能的。这时可以通过中断启动过程来重置root密码。

![](img/365ea8b87f118ff877bf8a0e620c128c_13.png)

以下是操作步骤：

1.  在系统启动时，按 `E` 键编辑内核启动参数。
2.  移动光标到最后一行。
3.  删除 `rhgb quiet` 参数。
4.  在行末添加 `rd.break enforcing=0`。
5.  按 `Ctrl+X` 启动系统。

进入紧急模式命令行后，需要重新挂载根文件系统并切换根环境：

1.  重新挂载根目录为可读写：`mount -o remount,rw /sysroot`
2.  切换根目录：`chroot /sysroot`
3.  现在可以重置root密码：`passwd root`
4.  重置完成后，创建SELinux重标记文件：`touch /.autorelabel`
5.  退出chroot环境：`exit`
6.  再次退出shell并重启系统：`exit`

## 管理系统服务 ⚙️

上一节我们介绍了系统的基本操作，本节我们来看看如何管理系统服务。我们将以HTTPD服务为例。

![](img/365ea8b87f118ff877bf8a0e620c128c_15.png)

首先，检查服务的状态。命令 `systemctl status` 可以查看服务是否运行。
```bash
sudo systemctl status httpd
```
如果服务未运行（inactive），可以启动它。
```bash
sudo systemctl start httpd
```
启动后，再次检查状态会显示为激活（active）并运行（running）。

![](img/365ea8b87f118ff877bf8a0e620c128c_17.png)

![](img/365ea8b87f118ff877bf8a0e620c128c_19.png)

要停止服务，使用stop命令。
```bash
sudo systemctl stop httpd
```

![](img/365ea8b87f118ff877bf8a0e620c128c_21.png)

![](img/365ea8b87f118ff877bf8a0e620c128c_23.png)

要获取服务的详细日志信息，可以使用 `journalctl` 命令。
```bash
sudo journalctl -xe -u httpd
```
参数 `-x` 会添加解释性文本，`-e` 会直接跳转到日志末尾，这对于排查当前问题非常有用。

服务可以设置为开机自动启动。
```bash
sudo systemctl enable httpd
```
启用服务会在 `/etc/systemd/system/multi-user.target.wants/` 目录下创建一个符号链接。禁用服务则会移除这个链接。
```bash
sudo systemctl disable httpd
```

![](img/365ea8b87f118ff877bf8a0e620c128c_25.png)

## 查看进程与资源利用率 📊

监控进程和资源利用率对于故障排除至关重要，尤其是在追踪高资源消耗进程时。

![](img/365ea8b87f118ff877bf8a0e620c128c_27.png)

要列出所有正在运行的进程，可以使用 `ps` 命令。
```bash
ps -af
```
参数 `-a` 列出所有进程，`-f` 显示完整格式列表。

![](img/365ea8b87f118ff877bf8a0e620c128c_29.png)

![](img/365ea8b87f118ff877bf8a0e620c128c_31.png)

通常需要过滤信息。例如，查找由 `apache` 用户运行的所有进程：
```bash
ps -af | grep apache
```
`ps` 命令提供的是瞬时快照。要查看实时动态更新的进程信息，可以使用 `top` 命令。
```bash
top
```
`top` 界面会定期更新，显示进程列表、CPU/内存使用率、平均负载和进程总数等信息。

![](img/365ea8b87f118ff877bf8a0e620c128c_33.png)

![](img/365ea8b87f118ff877bf8a0e620c128c_35.png)

在 `top` 界面中，可以按 `u` 键然后输入用户名来过滤显示特定用户的进程。按 `h` 键可以查看帮助信息。

![](img/365ea8b87f118ff877bf8a0e620c128c_37.png)

![](img/365ea8b87f118ff877bf8a0e620c128c_39.png)

![](img/365ea8b87f118ff877bf8a0e620c128c_41.png)

## 终止运行中的进程 🚫

![](img/365ea8b87f118ff877bf8a0e620c128c_43.png)

有时需要终止未正常关闭或行为异常的进程。这需要使用 `kill` 命令。

首先，使用 `ps` 或 `top` 找到目标进程的PID（进程ID）。`kill` 命令可以发送不同的信号。使用 `kill -l` 可以查看所有可用信号列表。

![](img/365ea8b87f118ff877bf8a0e620c128c_45.png)

常用的信号有两种：
*   `kill -15` 或 `kill`：发送SIGTERM信号，请求进程正常终止。
*   `kill -9`：发送SIGKILL信号，强制立即终止进程。

![](img/365ea8b87f118ff877bf8a0e620c128c_47.png)

例如，要强制终止PID为3299的进程：
```bash
kill -9 3299
```
有时需要终止父进程才能停止其所有子进程。

![](img/365ea8b87f118ff877bf8a0e620c128c_49.png)

## 在远程系统间复制文件 📁

在系统管理中，经常需要在不同主机间传输文件。

**安全复制（SCP）**：这是基于SSH协议进行文件复制的常用方法，既快速又安全。
```bash
scp local_file.txt clouduser@remote_server_ip:/tmp/
```
参数 `-r` 可以递归复制整个目录。

![](img/365ea8b87f118ff877bf8a0e620c128c_51.png)

![](img/365ea8b87f118ff877bf8a0e620c128c_53.png)

**安全文件传输协议（SFTP）**：它提供了一个交互式的文件传输会话，同样基于SSH，比传统的FTP更安全。
```bash
sftp clouduser@remote_server_ip
```
连接成功后，会进入sftp提示符。常用命令：
*   `ls`：列出远程目录文件。
*   `lls`：列出本地目录文件。
*   `put local_file`：上传文件到远程主机。
*   `get remote_file`：从远程主机下载文件。

![](img/365ea8b87f118ff877bf8a0e620c128c_55.png)

![](img/365ea8b87f118ff877bf8a0e620c128c_57.png)

---

![](img/365ea8b87f118ff877bf8a0e620c128c_59.png)

![](img/365ea8b87f118ff877bf8a0e620c128c_61.png)

本节课中，我们一起学习了操作运行中RHEL系统的多项核心技能：安全地关闭重启、紧急重置密码、启停监控服务、管理进程与资源，以及进行安全的远程文件传输。掌握这些技能是进行有效系统管理和故障排除的基础。