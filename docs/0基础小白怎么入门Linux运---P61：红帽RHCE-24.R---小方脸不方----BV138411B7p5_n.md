# Linux运维全套培训课程：P61：Rsync+Inotify数据增量备份 📁

在本节课中，我们将要学习如何使用Rsync工具进行高效的数据备份与同步，并了解如何结合Inotify实现实时增量备份。我们将从基本概念讲起，逐步深入到本地同步、远程同步以及自动化脚本的编写。

---

## 概述：什么是Rsync与增量备份？

Rsync是一个开源的、快速的、多功能的数据同步与增量备份工具。它主要用于本地或远程主机间的数据备份与同步。与传统的`cp`或`tar`命令不同，Rsync的增量备份机制只同步发生变化的数据，从而极大地节省了存储空间和网络带宽。

传统的备份方式（如`cp`）在每次备份时都会复制整个文件或目录，即使只有少量数据发生变化，这会导致大量重复数据占用存储空间。例如，一个文件第一次备份有2条数据，第二次新增2条数据后再次备份，传统方式会保存4条数据（包含之前的2条），而Rsync的增量备份只会保存新增的2条数据。

**核心优势**：Rsync通过比较源和目标文件的差异，仅传输变化的部分，实现高效、节省空间的备份。

---

![](img/d64207f4c94f1c8529e143328cc7fea0_1.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_3.png)

## 安装Rsync

![](img/d64207f4c94f1c8529e143328cc7fea0_5.png)

Rsync是一个命令行工具，并非系统服务。在使用前，需要确保系统已安装该软件包。

![](img/d64207f4c94f1c8529e143328cc7fea0_7.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_8.png)

**检查与安装命令**：
```bash
# 检查是否已安装
rpm -q rsync

![](img/d64207f4c94f1c8529e143328cc7fea0_10.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_12.png)

# 如果未安装，则使用yum安装
yum -y install rsync
```
安装完成后，即可直接使用`rsync`命令。

![](img/d64207f4c94f1c8529e143328cc7fea0_14.png)

---

## Rsync本地同步 🖥️

![](img/d64207f4c94f1c8529e143328cc7fea0_16.png)

上一节我们介绍了Rsync的基本概念，本节中我们来看看如何在单台主机上进行本地目录同步。本地同步是指在同一台机器上的不同目录之间进行数据备份。

Rsync有两种基本的命令格式，区别在于源目录路径后是否添加斜杠(`/`)。

### 命令格式与选项说明

以下是Rsync常用选项的简要说明：
*   `-n`：测试运行，显示将要执行的操作但不实际执行。
*   `--delete`：删除目标目录中源目录没有的额外文件，使两端内容严格一致。（**慎用**，可能导致数据丢失）
*   `-v`：显示详细的操作信息。
*   `-z`：在传输过程中进行压缩，适用于大文件，可以加快传输速度。
*   `-a`：归档模式，相当于`-rlptgoD`的组合，能保留文件的权限、属性、符号链接等，是备份时最常用的选项。

通常，我们会组合使用`-avz`选项进行备份。

### 实践：两种同步格式

![](img/d64207f4c94f1c8529e143328cc7fea0_18.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_20.png)

我们通过实例来理解两种命令格式的区别。假设我们在`/root`目录下有两个文件，并准备同步到`/rsync_back`目录。

**1. 同步整个目录（源目录不加`/`）**
此格式会将源目录本身及其内容一起同步到目标位置。
```bash
rsync -avz /root /rsync_back
```
执行后，目标路径`/rsync_back`下会生成一个`root`目录，里面包含源`/root`的所有内容。

![](img/d64207f4c94f1c8529e143328cc7fea0_22.png)

**2. 同步目录下的内容（源目录加`/`）**
此格式只同步源目录内的所有文件和子目录，不包括源目录本身。
```bash
rsync -avz /root/ /rsync_back
```
执行后，源`/root`目录下的所有文件会直接出现在`/rsync_back`目录中，而不会创建`root`文件夹。

![](img/d64207f4c94f1c8529e143328cc7fea0_24.png)

**验证增量备份**：
修改`/root`下的某个文件内容，然后再次执行同步命令。你会发现，Rsync只同步了发生变化的那个文件，其他未变动的文件不会被重复处理。这证明了其增量备份的特性。

![](img/d64207f4c94f1c8529e143328cc7fea0_26.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_28.png)

**关于`--delete`选项的警告**：
如果目标目录(`/rsync_back`)有源目录(`/root`)不存在的文件，使用`--delete`选项后，Rsync会删除目标目录中的这些“多余”文件。这适用于要求两端完全一致的场景，但**在备份场景下通常不建议使用**，因为一旦源端误删文件，同步后备份端的文件也会被删除，失去备份的恢复意义。

---

![](img/d64207f4c94f1c8529e143328cc7fea0_30.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_32.png)

## Rsync远程同步 🌐

上一节我们掌握了本地同步，本节中我们来看看如何在不同机器之间进行远程数据同步。远程同步的语法与本地同步非常相似，只需在目标路径前指定远程主机的用户名和地址。

### 基本用法

远程同步的命令格式如下：
```bash
rsync -avz [本地目录或文件] [用户名]@[远程主机IP]:[目标路径]
```
**示例**：将本机`/root`目录下的内容同步到远程主机`192.168.0.13`的`/opt`目录。
```bash
rsync -avz /root/ root@192.168.0.13:/opt
```
**注意**：执行远程同步前，**必须确保远程主机也安装了Rsync工具**，否则命令会失败。

### 配置SSH免密登录

![](img/d64207f4c94f1c8529e143328cc7fea0_34.png)

每次执行远程同步都需要输入密码，不利于自动化。我们可以配置SSH密钥对，实现免密登录。

以下是配置免密登录的步骤：
1.  在本地生成SSH密钥对（如果已有可跳过）：
    ```bash
    ssh-keygen -t rsa
    ```
2.  将公钥复制到远程主机：
    ```bash
    ssh-copy-id root@192.168.0.13
    ```
配置完成后，再进行Rsync远程同步就无需输入密码了。

![](img/d64207f4c94f1c8529e143328cc7fea0_36.png)

---

## 结合Inotify实现实时同步 ⚡

![](img/d64207f4c94f1c8529e143328cc7fea0_38.png)

之前我们介绍的同步都需要手动执行命令。Inotify是Linux内核的一个特性，可以监控文件系统的变化（如创建、修改、删除文件）。将Rsync与Inotify结合，可以实现数据变化的实时自动同步。

![](img/d64207f4c94f1c8529e143328cc7fea0_40.png)

**注意**：Inotify持续监控会消耗一定的系统资源（CPU和内存），在生产环境中需评估使用。更常见的做法是使用`crontab`设置定时任务执行Rsync脚本。

![](img/d64207f4c94f1c8529e143328cc7fea0_42.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_44.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_46.png)

### 安装Inotify-tools

![](img/d64207f4c94f1c8529e143328cc7fea0_48.png)

Inotify的功能需要通过`inotify-tools`工具包来调用。我们需要从官网下载源码包进行编译安装。

![](img/d64207f4c94f1c8529e143328cc7fea0_50.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_52.png)

**安装步骤**：
1.  安装编译依赖`gcc`：
    ```bash
    yum -y install gcc
    ```
2.  下载并解压`inotify-tools`源码包（以3.13版本为例）：
    ```bash
    wget http://github.com/downloads/rvoicilas/inotify-tools/inotify-tools-3.13.tar.gz
    tar -zxf inotify-tools-3.13.tar.gz
    cd inotify-tools-3.13
    ```
3.  编译并安装到指定目录：
    ```bash
    ./configure --prefix=/usr/local/inotify
    make && make install
    ```
4.  创建软链接，方便全局调用命令：
    ```bash
    ln -s /usr/local/inotify/bin/inotifywait /usr/sbin/inotifywait
    ```

![](img/d64207f4c94f1c8529e143328cc7fea0_54.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_55.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_57.png)

### 编写自动同步脚本

![](img/d64207f4c94f1c8529e143328cc7fea0_59.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_61.png)

Inotify本身只负责监控，我们需要编写一个Shell脚本，当监控到文件变化时，自动触发Rsync同步命令。

![](img/d64207f4c94f1c8529e143328cc7fea0_63.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_65.png)

以下是一个示例脚本`auto_sync.sh`：
```bash
#!/bin/bash
# 监控本地/root目录，一旦有文件变化，就同步到远程主机
/usr/sbin/inotifywait -mrq -e modify,create,delete /root | while read event
do
    rsync -avz --delete /root/ root@192.168.0.13:/opt/
done
```
**脚本说明**：
*   `inotifywait -mrq -e modify,create,delete /root`：递归(`-r`)、安静模式(`-q`)监控`/root`目录的修改、创建、删除事件。
*   `while read event`：循环读取监控到的事件。
*   `rsync ...`：一旦事件发生，就执行同步命令。

**运行脚本**：
```bash
# 赋予脚本执行权限
chmod +x auto_sync.sh
# 在后台运行脚本
./auto_sync.sh &
```
现在，当你在`/root`目录下创建或修改文件时，脚本会自动将其同步到远程主机的`/opt`目录。

![](img/d64207f4c94f1c8529e143328cc7fea0_67.png)

**停止脚本**：可以使用`fg`命令将后台脚本调到前台，然后按`Ctrl+C`终止；或使用`pkill`命令根据进程名结束。

![](img/d64207f4c94f1c8529e143328cc7fea0_69.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_71.png)

---

## 总结 🎯

![](img/d64207f4c94f1c8529e143328cc7fea0_73.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_75.png)

本节课中我们一起学习了Linux下强大的数据同步工具Rsync。
*   我们首先理解了**增量备份**相较于传统备份的优势。
*   接着，我们学习了**Rsync的安装**、**本地同步**的两种命令格式及其关键选项（特别是慎用的`--delete`）。
*   然后，我们扩展到**远程同步**，并学会了配置**SSH免密登录**以方便自动化。
*   最后，我们介绍了**Inotify**工具，并通过编写脚本实现了**Rsync+Inotify的实时自动同步**方案，同时也指出了其资源消耗的特点，并提及了使用`crontab`进行定时备份的替代方案。

![](img/d64207f4c94f1c8529e143328cc7fea0_77.png)

![](img/d64207f4c94f1c8529e143328cc7fea0_79.png)

通过本章学习，你应能根据实际需求，选择合适的方式来实现高效、可靠的数据备份与同步。