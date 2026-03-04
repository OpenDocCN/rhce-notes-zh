# Linux红帽认证教程：P3：3-03 配置默认存储库 🛠️

在本节课中，我们将学习如何为Linux系统配置默认的软件存储库。这是RHCSA认证考试中的一道典型题目，要求考生能够根据提供的地址手动创建可用的YUM仓库。

![](img/1b328f12994df38a77b05aa368a7729b_1.png)

## 概述

![](img/1b328f12994df38a77b05aa368a7729b_3.png)

![](img/1b328f12994df38a77b05aa368a7729b_5.png)

题目要求配置系统以使用指定的默认存储库。我们将通过手动创建仓库配置文件来完成此任务，并验证仓库是否配置成功。

![](img/1b328f12994df38a77b05aa368a7729b_7.png)

## 解题步骤

![](img/1b328f12994df38a77b05aa368a7729b_9.png)

![](img/1b328f12994df38a77b05aa368a7729b_11.png)

上一节我们介绍了题目的基本要求，本节中我们来看看具体的操作步骤。

![](img/1b328f12994df38a77b05aa368a7729b_13.png)

首先，我们需要登录到目标机器。使用SSH命令连接到`node1`服务器。

![](img/1b328f12994df38a77b05aa368a7729b_15.png)

![](img/1b328f12994df38a77b05aa368a7729b_17.png)

```bash
ssh root@node1
```

![](img/1b328f12994df38a77b05aa368a7729b_19.png)

![](img/1b328f12994df38a77b05aa368a7729b_20.png)

登录后，检查默认的YUM仓库目录`/etc/yum.repos.d/`。如果该目录下没有`.repo`结尾的仓库文件，说明我们需要手动创建。

![](img/1b328f12994df38a77b05aa368a7729b_22.png)

![](img/1b328f12994df38a77b05aa368a7729b_23.png)

```bash
ls /etc/yum.repos.d/
```

![](img/1b328f12994df38a77b05aa368a7729b_25.png)

![](img/1b328f12994df38a77b05aa368a7729b_27.png)

![](img/1b328f12994df38a77b05aa368a7729b_29.png)

考试环境中，`yum-config-manager`命令可能不可用，因此我们必须手动创建仓库文件。

以下是创建第一个仓库文件的步骤：

![](img/1b328f12994df38a77b05aa368a7729b_31.png)

1.  使用`vim`编辑器创建一个名为`baseos.repo`的文件。
    ```bash
    vim /etc/yum.repos.d/baseos.repo
    ```
2.  在文件中写入以下内容。其中，`baseurl`必须严格使用题目提供的地址。
    ```ini
    [baseos]
    name=baseos
    baseurl=http://content.example.com/rhel8.0/x86_64/dvd/BaseOS
    enabled=1
    gpgcheck=0
    ```
3.  保存并退出编辑器。

![](img/1b328f12994df38a77b05aa368a7729b_33.png)

![](img/1b328f12994df38a77b05aa368a7729b_35.png)

接下来，我们创建第二个仓库文件。

![](img/1b328f12994df38a77b05aa368a7729b_37.png)

![](img/1b328f12994df38a77b05aa368a7729b_39.png)

1.  使用`vim`编辑器创建一个名为`appstream.repo`的文件。
    ```bash
    vim /etc/yum.repos.d/appstream.repo
    ```
2.  在文件中写入以下内容。同样，`baseurl`需使用题目提供的第二个地址。
    ```ini
    [appstream]
    name=appstream
    baseurl=http://content.example.com/rhel8.0/x86_64/dvd/AppStream
    enabled=1
    gpgcheck=0
    ```
3.  保存并退出编辑器。

![](img/1b328f12994df38a77b05aa368a7729b_41.png)

## 验证配置

![](img/1b328f12994df38a77b05aa368a7729b_43.png)

创建完成后，我们需要验证仓库是否已成功添加并可用。

![](img/1b328f12994df38a77b05aa368a7729b_45.png)

使用以下命令列出所有已启用的仓库：
```bash
yum repolist
```
如果配置正确，你将看到`baseos`和`appstream`这两个仓库ID出现在列表中。

![](img/1b328f12994df38a77b05aa368a7729b_47.png)

![](img/1b328f12994df38a77b05aa368a7729b_49.png)

为了进一步测试仓库是否真正可用，可以尝试安装一个软件包：
```bash
yum install nfs-utils -y
```
如果安装成功，则说明仓库配置有效。你也可以使用`yum list`命令查看仓库中的软件包数量，以确认仓库内容已加载。
```bash
yum list | wc -l
```

![](img/1b328f12994df38a77b05aa368a7729b_51.png)

![](img/1b328f12994df38a77b05aa368a7729b_53.png)

## 总结

![](img/1b328f12994df38a77b05aa368a7729b_55.png)

![](img/1b328f12994df38a77b05aa368a7729b_57.png)

本节课中我们一起学习了如何在RHCSA考试环境中手动配置YUM存储库。我们完成了从登录服务器、检查环境、手动创建仓库配置文件到最终验证仓库可用性的全过程。关键点在于准确填写`baseurl`地址以及理解仓库配置文件的基本结构。掌握这项技能对于Linux系统管理至关重要。