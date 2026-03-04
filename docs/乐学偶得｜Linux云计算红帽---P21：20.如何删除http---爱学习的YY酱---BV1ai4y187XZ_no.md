# 乐学偶得｜Linux云计算红帽RHCSA／RHCE／RHCA - P21：20.如何删除httpd及repo位置 🗑️

![](img/b8a6a639c367342260c345e07a9a3066_0.png)

在本节课中，我们将要学习如何在Linux系统中使用包管理器删除软件包，并了解软件包的依赖关系以及系统仓库（Repository）的配置位置。我们将通过删除`httpd`（Apache HTTP服务器）的实例来演示这些操作。

## 软件包的依赖关系

![](img/b8a6a639c367342260c345e07a9a3066_2.png)

上一节我们介绍了如何使用包管理器安装软件。包管理器在安装文件时，会检查该文件有什么依赖项（dependency）。如果依赖其他文件，而系统中没有，包管理器会自动帮你安装上。

![](img/b8a6a639c367342260c345e07a9a3066_4.png)

我们可以查看已安装的`httpd`包有什么依赖项。`dependency`的简写是`dep`。

![](img/b8a6a639c367342260c345e07a9a3066_5.png)

![](img/b8a6a639c367342260c345e07a9a3066_7.png)

```bash
yum deplist httpd
```

执行该命令后，会发现`httpd`有很多依赖项。这体现了包管理器节省了大量时间，如果手动下载所有依赖文件将非常耗时。

## 删除软件包

![](img/b8a6a639c367342260c345e07a9a3066_9.png)

现在，如果我们想删除`httpd`软件包。首先需要获取管理员权限。

![](img/b8a6a639c367342260c345e07a9a3066_11.png)

![](img/b8a6a639c367342260c345e07a9a3066_12.png)

基本的删除命令是：
```bash
sudo yum remove httpd
```
执行此命令后，系统会提示确认是否删除。如果选择“是”，则仅删除`httpd`包本身。

![](img/b8a6a639c367342260c345e07a9a3066_14.png)

![](img/b8a6a639c367342260c345e07a9a3066_15.png)

## 自动删除软件包及其依赖

![](img/b8a6a639c367342260c345e07a9a3066_16.png)

在确认删除的提示出现时，我们考虑到`httpd`附带了许多依赖项。如果只想删除`httpd`而保留依赖，可能并非我们的本意。通常，我们希望彻底删除，即连同其依赖项一并删除。

![](img/b8a6a639c367342260c345e07a9a3066_18.png)

![](img/b8a6a639c367342260c345e07a9a3066_19.png)

因此，我们选择不执行简单的`remove`操作。为了将`httpd`及其所有依赖项一起删除，我们需要使用`autoremove`方法。

```bash
sudo yum autoremove httpd
```
`autoremove`命令会自动删除目标软件包及其不再被其他程序需要的依赖项。

执行后，终端会显示“removing httpd”以及“removing dependency”等信息。此时我们可以确认删除操作。

![](img/b8a6a639c367342260c345e07a9a3066_21.png)

操作完成后，系统会提示“complete”，表示不仅`httpd`被移除，其相关依赖也被删除。这个方法可以确保清理得更彻底。

![](img/b8a6a639c367342260c345e07a9a3066_23.png)

## 验证删除结果

删除完成后，我们需要验证`httpd`是否已被成功删除。

第一种方法是使用`which`命令查找`httpd`可执行文件的位置：
```bash
which httpd
```
如果返回“no httpd in ...”，则表明`httpd`已不在系统中。

![](img/b8a6a639c367342260c345e07a9a3066_25.png)

第二种方法是使用`yum list`命令列出已安装的包中是否包含`httpd`：
```bash
yum list installed | grep httpd
```
如果没有任何输出，或者提示“Error: No matching packages to list”，则证明`httpd`已被彻底删除。

## 了解软件仓库（Repository）位置

我们知道，通过`yum`安装软件包是从云端仓库下载的。之前安装时可以看到是从阿里云镜像下载的。

我们可以查看当前系统配置了哪些仓库：
```bash
yum repolist
```
该命令会列出所有启用的仓库，显示仓库ID（如`base`、`updates`）及其包含的软件包数量。从中可以看到仓库的源地址信息。

![](img/b8a6a639c367342260c345e07a9a3066_27.png)

![](img/b8a6a639c367342260c345e07a9a3066_29.png)

系统之所以知道从阿里云镜像下载，是因为本地有相应的配置文件。以下命令可能涉及未学过的知识，此处先做演示，后续课程会详细讲解。

首先，切换到存放仓库配置文件的目录：
```bash
cd /etc/yum.repos.d/
```
然后，列出该目录下的所有文件：
```bash
ls
```
你会看到一些以`.repo`结尾的文件，如`CentOS-Base.repo`。这些文件定义了软件仓库的源地址。

我们可以查看其中一个文件的内容，例如：
```bash
less CentOS-Base.repo
```
使用`less`命令可以分页查看，内容更清晰。在文件中，你可以看到`baseurl`或`mirrorlist`等配置项，它们指明了软件包的下载地址。这就是系统知道从哪里获取`repository`的原因。

## 总结

![](img/b8a6a639c367342260c345e07a9a3066_31.png)

本节课中我们一起学习了：
1.  使用 `yum deplist` 查看软件包的依赖关系。
2.  使用 `yum remove` 删除单个软件包。
3.  使用 `yum autoremove` 彻底删除软件包及其无用依赖。
4.  使用 `which` 和 `yum list installed` 验证删除结果。
5.  使用 `yum repolist` 查看已配置的软件仓库。
6.  了解了系统仓库配置文件位于 `/etc/yum.repos.d/` 目录下。

![](img/b8a6a639c367342260c345e07a9a3066_33.png)

![](img/b8a6a639c367342260c345e07a9a3066_34.png)

通过本课，你掌握了在RHEL/CentOS系统中安全、彻底删除软件包以及管理软件源的基本方法。