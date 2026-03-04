# Linux运维进阶：P60：Rsync+Inotify数据增量备份 📂

在本节课中，我们将要学习如何使用 Rsync 工具进行高效的数据同步与增量备份，并了解如何结合 Inotify 实现实时监控与自动同步。

---

## 概述

Rsync 是一个开源的、快速的、多功能的数据同步工具，主要用于本地或远程主机间的数据备份与同步。与传统的 `cp` 或 `tar` 命令不同，Rsync 采用增量备份机制，只同步发生变化的内容，从而极大地节省了存储空间和网络带宽。

上一节我们介绍了 NFS 网络文件共享，本节中我们来看看如何利用 Rsync 对共享或本地的数据进行智能备份。

---

## Rsync 的核心概念

传统的备份方式（如 `cp`）在多次备份时会产生大量重复数据，浪费存储空间。例如，一个文件第一次备份了内容 A，第二次增加了内容 B，传统方式会备份 A+B（包含重复的 A）。而 Rsync 的增量备份只会同步新增的 B 部分。

**核心优势**：节约存储空间，提高备份效率。

---

![](img/30f9d47231c3cf11135ea3ca5768ac68_1.png)

![](img/30f9d47231c3cf11135ea3ca5768ac68_3.png)

## 安装 Rsync

![](img/30f9d47231c3cf11135ea3ca5768ac68_5.png)

Rsync 是一个命令行工具，并非服务。首先需要检查并安装它。

![](img/30f9d47231c3cf11135ea3ca5768ac68_7.png)

![](img/30f9d47231c3cf11135ea3ca5768ac68_8.png)

```bash
# 检查是否已安装
rpm -q rsync

![](img/30f9d47231c3cf11135ea3ca5768ac68_10.png)

# 如果未安装，则进行安装
yum -y install rsync
```

![](img/30f9d47231c3cf11135ea3ca5768ac68_12.png)

---

![](img/30f9d47231c3cf11135ea3ca5768ac68_14.png)

## Rsync 的基本用法

Rsync 的用法主要分为两种格式，区别在于源目录路径是否以斜杠 (`/`) 结尾。

### 命令格式说明

![](img/30f9d47231c3cf11135ea3ca5768ac68_16.png)

以下是两种命令格式的对比：

1.  **同步整个目录**（包括目录本身）：
    ```
    rsync [选项] 源目录 目标目录
    ```
    示例：`rsync /root /backup` 会将 `root` 目录本身复制到 `/backup` 下。

2.  **同步目录下的内容**（不包括目录本身）：
    ```
    rsync [选项] 源目录/ 目标目录
    ```
    示例：`rsync /root/ /backup` 会将 `root` 目录下的所有文件和子目录复制到 `/backup` 下，但 `/backup` 下不会出现 `root` 文件夹。

---

### 常用选项介绍

以下是 Rsync 命令中一些常用选项的说明：

*   `-n`：试运行，显示将要执行的操作但不实际执行。
*   `--delete`：删除目标目录中源目录没有的文件，**使用需谨慎**。
*   `-v`：显示详细的操作信息。
*   `-z`：在传输过程中进行压缩，适用于大文件或慢速网络。
*   `-a`：归档模式，相当于 `-rlptgoD`，保持文件所有属性（权限、时间等），并递归同步。

![](img/30f9d47231c3cf11135ea3ca5768ac68_18.png)

---

![](img/30f9d47231c3cf11135ea3ca5768ac68_20.png)

### 本地同步演示

我们通过实际操作来理解上述概念。

首先，创建测试目录和文件：

![](img/30f9d47231c3cf11135ea3ca5768ac68_22.png)

```bash
mkdir /rsync_back
cd ~
touch hello.txt abc.txt
```

![](img/30f9d47231c3cf11135ea3ca5768ac68_24.png)

**演示1：同步整个目录**
```bash
# 将 /root 目录本身同步到 /rsync_back
rsync -avz /root /rsync_back
# 查看结果，会发现 /rsync_back 下有一个 root 目录
ls /rsync_back
```

![](img/30f9d47231c3cf11135ea3ca5768ac68_26.png)

**演示2：同步目录内容**
```bash
# 先清空目标目录
rm -rf /rsync_back/*
# 只同步 /root 目录下的内容到 /rsync_back
rsync -avz /root/ /rsync_back
# 查看结果，文件直接出现在 /rsync_back 下，没有 root 目录
ls /rsync_back
```

![](img/30f9d47231c3cf11135ea3ca5768ac68_28.png)

**演示3：验证增量备份**
修改 `hello.txt` 文件，然后再次执行同步命令。观察输出，Rsync 只会同步发生变化的 `hello.txt` 文件。

![](img/30f9d47231c3cf11135ea3ca5768ac68_30.png)

**演示4：`--delete` 选项的作用**
在目标目录 (`/rsync_back`) 中创建一个源目录 (`/root`) 没有的文件，然后分别使用不带 `--delete` 和带 `--delete` 的选项进行同步，观察目标目录中多余文件的变化。

![](img/30f9d47231c3cf11135ea3ca5768ac68_32.png)

---

## 远程同步

Rsync 同样可以方便地在不同主机之间同步数据，其语法与 `scp` 类似，但具备增量优势。

### 远程同步语法

```bash
rsync [选项] 本地文件或目录 用户名@远程主机IP:目标路径
rsync [选项] 用户名@远程主机IP:源路径 本地文件或目录
```

**示例：将本地目录同步到远程服务器**
```bash
rsync -avz /root/ root@192.168.0.13:/opt/
```
执行此命令需要输入远程主机的 root 密码。**注意**：远程主机也必须安装 Rsync。

![](img/30f9d47231c3cf11135ea3ca5768ac68_34.png)

---

### 配置 SSH 免密登录

![](img/30f9d47231c3cf11135ea3ca5768ac68_36.png)

为了避免每次同步都输入密码，可以配置 SSH 密钥对实现免密登录。

```bash
# 在本地生成密钥对（如果已有可跳过）
ssh-keygen -t rsa
# 将公钥复制到远程主机
ssh-copy-id root@192.168.0.13
```
配置完成后，再进行远程同步就无需输入密码了。

---

## Rsync + Inotify 实现实时同步

![](img/30f9d47231c3cf11135ea3ca5768ac68_38.png)

虽然可以通过计划任务 (`crontab`) 定时执行 Rsync 进行备份，但无法做到实时。Inotify 是 Linux 内核的一个子系统，可以监控文件系统的变化（如创建、修改、删除）。结合 Rsync 和 Inotify，可以实现数据的实时自动同步。

![](img/30f9d47231c3cf11135ea3ca5768ac68_40.png)

> **注意**：Inotify 持续监控会消耗一定的系统资源（CPU和内存），在生产环境中需评估后使用。

![](img/30f9d47231c3cf11135ea3ca5768ac68_42.png)

![](img/30f9d47231c3cf11135ea3ca5768ac68_44.png)

### 安装 Inotify-tools

![](img/30f9d47231c3cf11135ea3ca5768ac68_46.png)

![](img/30f9d47231c3cf11135ea3ca5768ac68_48.png)

Inotify-tools 是提供 `inotifywait` 等命令的工具集，需要编译安装。

1.  **下载源码包**：从官网 (https://github.com/rvoicilas/inotify-tools) 下载。
2.  **编译安装**：
    ```bash
    tar -zxf inotify-tools-*.tar.gz
    cd inotify-tools-*
    ./configure --prefix=/usr/local/inotify
    make && make install
    ```
3.  **创建软链接**（方便调用）：
    ```bash
    ln -s /usr/local/inotify/bin/inotifywait /usr/sbin/
    ```

![](img/30f9d47231c3cf11135ea3ca5768ac68_50.png)

![](img/30f9d47231c3cf11135ea3ca5768ac68_52.png)

---

![](img/30f9d47231c3cf11135ea3ca5768ac68_54.png)

![](img/30f9d47231c3cf11135ea3ca5768ac68_55.png)

### 编写监控同步脚本

![](img/30f9d47231c3cf11135ea3ca5768ac68_57.png)

以下是一个简单的脚本示例，它监控本地 `/root` 目录的变化，并实时同步到远程服务器。

![](img/30f9d47231c3cf11135ea3ca5768ac68_59.png)

![](img/30f9d47231c3cf11135ea3ca5768ac68_61.png)

![](img/30f9d47231c3cf11135ea3ca5768ac68_63.png)

```bash
#!/bin/bash
# 脚本名称：/scripts/arsync.sh

![](img/30f9d47231c3cf11135ea3ca5768ac68_65.png)

REMOTE_IP="192.168.0.13"
REMOTE_PATH="/opt/"

/usr/sbin/inotifywait -mrq -e modify,create,delete /root |
while read events
do
    rsync -avz --delete /root/ root@${REMOTE_IP}:${REMOTE_PATH}
    echo "`date +%F\ %T` 出现事件 $events， 已同步" >> /var/log/rsync.log 2>&1
done
```

**脚本说明**：
*   `inotifywait -mrq -e modify,create,delete /root`：递归 (`-r`)、安静模式 (`-q`) 监控 `/root` 目录的修改、创建、删除事件。
*   `while read events`：循环读取监控到的事件。
*   一旦事件发生，立即执行 `rsync` 命令进行同步，并将日志写入文件。

![](img/30f9d47231c3cf11135ea3ca5768ac68_67.png)

**运行脚本**：
```bash
chmod +x /scripts/arsync.sh
# 后台运行
nohup /scripts/arsync.sh &
```

![](img/30f9d47231c3cf11135ea3ca5768ac68_69.png)

![](img/30f9d47231c3cf11135ea3ca5768ac68_71.png)

---

## 总结

![](img/30f9d47231c3cf11135ea3ca5768ac68_73.png)

本节课中我们一起学习了 Rsync 数据同步工具的核心用法。

![](img/30f9d47231c3cf11135ea3ca5768ac68_75.png)

1.  我们理解了 **增量备份** 相较于传统备份的优势。
2.  我们掌握了 Rsync 进行 **本地同步** 的两种命令格式及关键选项（如 `-a`, `-v`, `-z`, `--delete`）。
3.  我们学会了如何配置 **SSH 免密登录** 并使用 Rsync 进行 **远程同步**。
4.  最后，我们了解了如何通过 **Inotify** 监控文件变化，并与 Rsync 结合编写脚本，实现 **数据的实时自动同步**。

![](img/30f9d47231c3cf11135ea3ca5768ac68_77.png)

![](img/30f9d47231c3cf11135ea3ca5768ac68_79.png)

对于生产环境，建议使用 `crontab` 定时执行 Rsync 脚本作为常规备份方案；对于有实时性要求的特定场景，则可评估后使用 Rsync+Inotify 方案。