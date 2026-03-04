# Linux运维培训教程：P60：Rsync+Inotify数据增量备份 📁

在本节课中，我们将要学习如何使用 Rsync 和 Inotify 工具实现高效的数据增量备份与同步。我们将从基本概念入手，逐步讲解本地同步、远程同步以及如何结合 Inotify 实现实时监控与自动同步。

## 概述

Rsync 是一个开源的、快速的、多功能的数据同步与增量备份工具。它主要用于数据备份和同步领域，能够高效地处理本地或远程主机间的文件同步，其核心优势在于仅同步发生变化的数据，从而节省存储空间和网络带宽。

上一节我们介绍了 NFS 网络文件存储，本节中我们来看看如何利用 Rsync 进行数据备份。

## Rsync 基础概念

传统的备份方式（如 `cp` 或 `tar`）在多次备份时会产生大量重复数据，浪费存储空间。例如，一个文件在三次备份中内容不断累积，每次备份都包含之前的所有数据。

增量备份则不同。Rsync 的同步机制能够识别源文件和目标文件之间的差异。**它只将新增或修改的内容传输到目标位置**，已经存在且未变化的文件不会被重复处理。这极大地节约了存储空间。

其核心同步逻辑可以简化为一个公式：
`传输内容 = 源文件 - 目标文件中已存在的相同部分`

![](img/f518e6a137bafbde1829aea06698e1c8_1.png)

![](img/f518e6a137bafbde1829aea06698e1c8_3.png)

## Rsync 安装与基本使用

![](img/f518e6a137bafbde1829aea06698e1c8_5.png)

首先，我们需要在系统上安装 Rsync 软件包。

![](img/f518e6a137bafbde1829aea06698e1c8_7.png)

![](img/f518e6a137bafbde1829aea06698e1c8_8.png)

```bash
# 检查是否已安装
rpm -q rsync

![](img/f518e6a137bafbde1829aea06698e1c8_10.png)

# 如果未安装，则使用 yum 安装
yum -y install rsync
```

![](img/f518e6a137bafbde1829aea06698e1c8_12.png)

![](img/f518e6a137bafbde1829aea06698e1c8_14.png)

Rsync 是一个命令行工具，安装后即可使用，无需启动服务。

### 命令格式与选项

Rsync 有两种主要的命令格式，区别在于源目录路径后是否添加斜杠 (`/`)：
*   **格式一（同步整个目录）**：`rsync [选项] 源目录 目标目录`
*   **格式二（同步目录下的内容）**：`rsync [选项] 源目录/ 目标目录`

![](img/f518e6a137bafbde1829aea06698e1c8_16.png)

以下是 Rsync 的常用选项：
*   `-n`：试运行，显示将要执行的操作而不实际执行。
*   `--delete`：删除目标目录中源目录没有的额外文件，以保持两端严格一致。（**使用需谨慎**）
*   `-v`：显示详细的操作信息。
*   `-z`：在传输过程中进行压缩，以提高传输效率。
*   `-a`：归档模式，相当于 `-rlptgoD`，**能保持文件所有属性（如权限、时间戳）并递归同步**。

### 本地同步演示

让我们通过实际操作来理解这两种格式和选项的作用。首先创建测试目录和文件。

```bash
# 创建备份目标目录
mkdir /rsync_back

# 在用户家目录创建测试文件
cd ~
touch hello.txt
touch abc.txt
```

**1. 同步整个目录（不加 `/`）**
此命令会将 `~/` 目录本身（包括其下的所有内容）同步到目标位置。

```bash
rsync -avz ~ /rsync_back/
```
执行后，查看 `/rsync_back/`，会发现里面有一个 `root`（或你的用户名）目录，其中包含了所有文件。

![](img/f518e6a137bafbde1829aea06698e1c8_18.png)

![](img/f518e6a137bafbde1829aea06698e1c8_20.png)

**2. 同步目录下的内容（加 `/`）**
此命令只同步 `~/` 目录下的文件到目标位置，而不会创建 `root` 目录本身。

```bash
# 先清空目标目录
rm -rf /rsync_back/*
# 执行同步
rsync -avz ~/ /rsync_back/
```
执行后，查看 `/rsync_back/`，会直接看到 `hello.txt` 和 `abc.txt` 等文件。

![](img/f518e6a137bafbde1829aea06698e1c8_22.png)

**3. 验证增量同步**
现在，我们修改一个文件，然后再次同步。

```bash
# 向 hello.txt 添加内容
echo "Hello World" >> ~/hello.txt
# 再次同步
rsync -avz ~/ /rsync_back/
```
观察输出，Rsync 只会处理发生变化的 `hello.txt` 文件，其他未变文件不会被重复传输。

![](img/f518e6a137bafbde1829aea06698e1c8_24.png)

![](img/f518e6a137bafbde1829aea06698e1c8_26.png)

**4. `--delete` 选项演示**
这个选项用于确保目标目录与源目录完全一致。

![](img/f518e6a137bafbde1829aea06698e1c8_28.png)

```bash
# 在目标目录创建一个源目录没有的文件
touch /rsync_back/extra.txt
# 不使用 --delete 同步
rsync -avz ~/ /rsync_back/
# 检查，extra.txt 依然存在

![](img/f518e6a137bafbde1829aea06698e1c8_30.png)

# 使用 --delete 同步
rsync -avz --delete ~/ /rsync_back/
# 检查，extra.txt 已被删除
```
**注意**：`--delete` 会永久删除目标端多余的文件，在备份场景下通常不建议使用，以免误删备份数据。

![](img/f518e6a137bafbde1829aea06698e1c8_32.png)

## 远程同步

Rsync 同样可以轻松实现不同服务器之间的数据同步，其语法与本地同步类似，只需在目标路径前指定远程主机的用户名和地址。

远程同步的通用格式为：
`rsync [选项] 源文件或目录 用户名@远程主机IP:目标路径`

**前提条件**：**目标远程服务器也必须安装 Rsync**。

**1. 基础远程同步**
以下命令将本地 `~/` 目录下的所有文件同步到远程服务器 `192.168.0.13` 的 `/opt/` 目录下。

![](img/f518e6a137bafbde1829aea06698e1c8_34.png)

```bash
rsync -avz ~/ root@192.168.0.13:/opt/
```
首次连接时需要输入远程服务器的 root 密码。

**2. 配置 SSH 免密登录**
为了避免每次同步都输入密码，可以配置 SSH 密钥对实现免密登录。

![](img/f518e6a137bafbde1829aea06698e1c8_36.png)

```bash
# 在本地生成密钥对（如果已有可跳过）
ssh-keygen -t rsa
# 将公钥拷贝到远程服务器
ssh-copy-id root@192.168.0.13
```
配置完成后，再次执行 Rsync 远程同步命令将不再需要输入密码。

## Rsync + Inotify 实现实时同步

虽然可以通过计划任务（Cron）定时执行 Rsync 来实现自动备份，但这无法做到实时。Inotify 是 Linux 内核的一个特性，用于监控文件系统事件（如创建、修改、删除）。结合 Rsync 和 Inotify，可以实现近乎实时的数据同步。

![](img/f518e6a137bafbde1829aea06698e1c8_38.png)

然而，**Inotify 持续监控会消耗一定的系统资源**，在生产环境中需评估使用。更常见的做法是使用 Cron 定时执行 Rsync 脚本。

![](img/f518e6a137bafbde1829aea06698e1c8_40.png)

### Inotify 安装与配置

![](img/f518e6a137bafbde1829aea06698e1c8_42.png)

![](img/f518e6a137bafbde1829aea06698e1c8_44.png)

Inotify-tools 是提供 `inotifywait` 命令的工具集，需要编译安装。

![](img/f518e6a137bafbde1829aea06698e1c8_46.png)

![](img/f518e6a137bafbde1829aea06698e1c8_48.png)

![](img/f518e6a137bafbde1829aea06698e1c8_50.png)

```bash
# 1. 安装编译依赖
yum -y install gcc

# 2. 下载 inotify-tools 源码包（需从官网下载，例如 inotify-tools-3.13.tar.gz）
# 3. 解压并编译安装
tar -zxf inotify-tools-3.13.tar.gz
cd inotify-tools-3.13
./configure --prefix=/usr/local/inotify
make && make install

![](img/f518e6a137bafbde1829aea06698e1c8_52.png)

![](img/f518e6a137bafbde1829aea06698e1c8_54.png)

![](img/f518e6a137bafbde1829aea06698e1c8_56.png)

# 4. 创建软链接，方便命令调用
ln -s /usr/local/inotify/bin/inotifywait /usr/sbin/inotifywait
```

![](img/f518e6a137bafbde1829aea06698e1c8_57.png)

![](img/f518e6a137bafbde1829aea06698e1c8_59.png)

### 编写实时同步脚本

![](img/f518e6a137bafbde1829aea06698e1c8_61.png)

![](img/f518e6a137bafbde1829aea06698e1c8_63.png)

以下是一个结合 `inotifywait` 和 `rsync` 的简单脚本示例。该脚本会持续监控本地某个目录，一旦有文件变化，立即触发 Rsync 同步到远程服务器。

![](img/f518e6a137bafbde1829aea06698e1c8_65.png)

![](img/f518e6a137bafbde1829aea06698e1c8_67.png)

```bash
#!/bin/bash
# 定义要监控的本地目录和远程目标
SRC_DIR="/root/"
DST_HOST="root@192.168.0.13"
DST_DIR="/opt/"

# 使用 inotifywait 持续监控
/usr/sbin/inotifywait -mrq -e modify,create,delete ${SRC_DIR} | while read event
do
    # 触发 rsync 同步
    rsync -avz --delete ${SRC_DIR} ${DST_HOST}:${DST_DIR}
done
```

**脚本说明**：
*   `inotifywait -mrq`：`-m` 持续监控，`-r` 递归监控子目录，`-q` 减少输出信息。
*   `-e modify,create,delete`：指定监控修改、创建、删除事件。
*   `while read event`：循环读取监控到的事件。

![](img/f518e6a137bafbde1829aea06698e1c8_69.png)

![](img/f518e6a137bafbde1829aea06698e1c8_71.png)

**运行脚本**：
```bash
# 赋予脚本执行权限
chmod +x /script/auto_rsync.sh
# 在后台运行脚本
/script/auto_rsync.sh &
```
运行后，在监控的 `SRC_DIR` 目录下进行文件创建、修改等操作，变化会几乎实时同步到远程服务器的 `DST_DIR`。

![](img/f518e6a137bafbde1829aea06698e1c8_73.png)

**重要提示**：此类常驻脚本会消耗资源，测试或生产环境使用时需充分考虑。停止脚本可使用 `pkill -f inotifywait` 或 `kill` 对应进程。

## 总结

![](img/f518e6a137bafbde1829aea06698e1c8_75.png)

![](img/f518e6a137bafbde1829aea06698e1c8_77.png)

本节课中我们一起学习了 Rsync 和 Inotify 的强大功能。
1.  **Rsync 核心**：掌握了 Rsync 作为增量备份工具的原理，以及其本地同步与远程同步的基本命令和关键选项（如 `-a`, `-z`, `--delete`）。
2.  **实践操作**：通过演示学会了如何区分同步目录本身和目录内容，并验证了其增量同步的特性。
3.  **自动化进阶**：了解了如何通过 Cron 计划任务实现定时自动备份，以及如何通过 Inotify-tools 监控文件变化并触发 Rsync，实现近乎实时的数据同步方案。

![](img/f518e6a137bafbde1829aea06698e1c8_79.png)

![](img/f518e6a137bafbde1829aea06698e1c8_81.png)

对于企业环境，建议将 Rsync 命令写入脚本，并结合 Cron 定时任务来执行，这是一个在资源消耗和备份时效性之间取得平衡的稳健方案。实时同步方案（Rsync+Inotify）则适用于对数据一致性要求极高的特定场景。