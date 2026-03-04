# Linux 运维教程：P61：Rsync + Inotify 数据增量备份 🔄

在本节课中，我们将要学习 Rsync 和 Inotify 这两个强大的工具，它们可以帮助我们实现高效、自动化的数据增量备份与同步。我们将从基本概念讲起，逐步深入到本地同步、远程同步以及自动监控同步的配置。

---

## 概述：什么是 Rsync 与增量备份？

Rsync 是一个开源的、快速的、多功能的数据同步与备份工具。它的核心优势在于能够实现**增量备份**。为了理解这一点，我们先来看看传统备份方式的不足。

上一节我们介绍了数据备份的基本需求，本节中我们来看看传统备份方式的问题。

传统的备份方式（如使用 `cp` 或 `tar` 命令）在每次备份时，都会将整个文件或目录完整复制一遍。例如，一个文件在第一次备份时有两条数据，第二次备份时新增了两条数据，那么第二次的备份文件中就会包含全部四条数据，导致第一次备份的两条数据被重复存储。随着备份次数的增加，会浪费大量的存储空间。

而 Rsync 的增量备份则不同。它能够智能地识别出源文件中**发生变化的部分**，在后续备份时，只同步这些新增或修改的内容到目标位置。这样可以极大地节约存储空间，提高备份效率。

---

## Rsync 基础：安装与本地同步

![](img/5c3776c1013c63a8fa18e8bd88656ab6_1.png)

首先，我们需要在系统上安装 Rsync 工具。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_3.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_5.png)

### 安装 Rsync

Rsync 通常不是默认安装的，我们可以使用包管理器进行安装。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_7.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_8.png)

```bash
yum -y install rsync
```

![](img/5c3776c1013c63a8fa18e8bd88656ab6_10.png)

安装完成后，Rsync 就可以直接作为命令使用了，它本身不是一个常驻服务。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_12.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_14.png)

### Rsync 命令的基本格式

Rsync 有两种常用的命令格式，区别在于是否同步目录本身。

*   **格式一：同步整个目录（包括目录本身）**
    ```
    rsync [选项] 源目录 目标目录
    ```
    *   注意：源目录路径**末尾没有斜杠** `/`。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_16.png)

*   **格式二：只同步目录下的内容（不包括目录本身）**
    ```
    rsync [选项] 源目录/ 目标目录
    ```
    *   注意：源目录路径**末尾有斜杠** `/`。

### 常用选项说明

以下是 Rsync 的一些核心选项：

*   `-a`：归档模式，保持文件所有属性（权限、时间、属主等），并递归同步子目录。
*   `-v`：显示同步过程的详细信息。
*   `-z`：在传输过程中进行压缩，适用于大文件，可以加快网络传输速度。
*   `--delete`：**危险选项**。删除目标目录中源目录没有的文件，使两端完全一致。使用需谨慎，以免误删备份数据。

### 本地同步实践

让我们通过实际操作来理解这两种格式和增量备份的特性。

1.  **准备环境**：创建测试目录和文件。
    ```bash
    mkdir /rsync_back
    cd ~
    touch hello.txt abc.txt
    ```

2.  **同步整个目录（格式一）**：将整个 `/root` 目录同步到备份目录。
    ```bash
    rsync -av /root /rsync_back
    ```
    查看 `/rsync_back`，会发现里面有一个 `root` 目录，其中包含了所有文件。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_18.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_20.png)

3.  **只同步目录内容（格式二）**：先清空目标目录，然后只同步 `/root` 下的文件。
    ```bash
    rm -rf /rsync_back/*
    rsync -av /root/ /rsync_back
    ```
    查看 `/rsync_back`，会发现文件直接存放在该目录下，而没有 `root` 这个上层目录。

4.  **验证增量备份**：修改源文件，然后再次同步。
    ```bash
    echo "new data" >> /root/hello.txt
    rsync -av /root/ /rsync_back
    ```
    观察输出，Rsync 只会同步发生变化的 `hello.txt` 文件，而不会处理未变的 `abc.txt` 文件。这就是增量备份。

5.  **体验 `--delete` 选项**：在目标目录创建一个源目录没有的文件，然后使用 `--delete` 选项同步。
    ```bash
    touch /rsync_back/extra.txt
    rsync -av --delete /root/ /rsync_back
    ```
    你会发现，Rsync 在同步后删除了目标目录中多余的 `extra.txt` 文件。这个选项确保了备份目录是源目录的精确镜像，但也意味着在源端误删的文件，在备份端也会被同步删除，可能无法恢复。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_22.png)

---

![](img/5c3776c1013c63a8fa18e8bd88656ab6_24.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_26.png)

## Rsync 进阶：远程同步

![](img/5c3776c1013c63a8fa18e8bd88656ab6_28.png)

Rsync 的强大之处在于它能轻松实现跨主机的远程同步，其语法与熟悉的 `scp` 命令类似。

### 远程同步语法

![](img/5c3776c1013c63a8fa18e8bd88656ab6_30.png)

```
rsync [选项] 源文件或目录 用户名@远程主机IP:目标路径
rsync [选项] 用户名@远程主机IP:源文件或目录 目标路径
```

![](img/5c3776c1013c63a8fa18e8bd88656ab6_32.png)

### 配置 SSH 免密登录

为了在远程同步时无需手动输入密码，我们需要先配置 SSH 密钥对登录。

1.  在**源主机**生成密钥对（如果已有可跳过）：
    ```bash
    ssh-keygen
    ```
2.  将公钥拷贝到**目标主机**：
    ```bash
    ssh-copy-id root@192.168.0.13
    ```
    输入目标主机的密码后，即可建立免密信任关系。

### 远程同步实践

假设要将本地 `/root` 目录同步到远程主机 `192.168.0.13` 的 `/opt` 目录。

```bash
rsync -av /root/ root@192.168.0.13:/opt
```

![](img/5c3776c1013c63a8fa18e8bd88656ab6_34.png)

执行后，本地 `/root` 下的文件就会增量同步到远程主机的 `/opt` 目录下。由于配置了免密登录，整个过程无需交互。

**注意**：远程主机也需要安装 `rsync` 软件包，否则命令会执行失败。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_36.png)

---

## 自动化监控：Rsync + Inotify 实时同步

虽然我们可以通过计划任务（Cron）定期执行 Rsync 来实现自动备份，但这并非实时。`Inotify` 是 Linux 内核提供的一种文件系统事件监控机制。结合 Rsync，可以实现**实时**的增量同步。

上一节我们实现了手动的和定时的同步，本节中我们来看看如何实现实时监控与同步。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_38.png)

### Inotify 的工作原理与注意事项

![](img/5c3776c1013c63a8fa18e8bd88656ab6_40.png)

Inotify 可以监控指定目录下的文件创建、修改、删除等事件。一旦检测到变化，可以立即触发一个动作（例如执行 Rsync 命令）。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_42.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_44.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_45.png)

**重要提示**：Inotify 持续监控会消耗一定的系统资源（CPU 和内存）。在生产环境中，对于变更非常频繁的目录，需要评估其影响。更常见的做法是使用 Cron 定时执行备份脚本。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_47.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_49.png)

### 安装与配置 Inotify-tools

![](img/5c3776c1013c63a8fa18e8bd88656ab6_51.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_52.png)

Inotify 需要用户态工具 `inotify-tools`。由于软件包较旧，我们通常从其官网下载源码编译安装。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_54.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_56.png)

1.  **下载源码包**：从 [官网](https://github.com/inotify-tools/inotify-tools/releases?page=2) 下载最新版本（如 `inotify-tools-3.13.tar.gz`）。
2.  **编译安装**：
    ```bash
    tar -zxf inotify-tools-3.13.tar.gz
    cd inotify-tools-3.13
    ./configure --prefix=/usr/local/inotify
    make && make install
    ```
3.  **创建命令软链接**（方便使用）：
    ```bash
    ln -s /usr/local/inotify/bin/inotifywait /usr/sbin/inotifywait
    ```

![](img/5c3776c1013c63a8fa18e8bd88656ab6_58.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_60.png)

### 编写实时同步脚本

![](img/5c3776c1013c63a8fa18e8bd88656ab6_62.png)

以下是一个结合 Inotify 和 Rsync 的简单监控脚本示例：

![](img/5c3776c1013c63a8fa18e8bd88656ab6_64.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_66.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_68.png)

```bash
#!/bin/bash
# 监控目录
MON_DIR="/root"
# 使用 inotifywait 递归（-r）监控目录，事件包括修改、创建、删除、移动
# -q 减少输出，--format 指定输出格式
/usr/sbin/inotifywait -mrq --format '%w%f' -e modify,create,delete,move $MON_DIR | while read FILE
do
    # 当监控到事件后，执行 rsync 同步到远程主机
    rsync -av --delete $MON_DIR/ root@192.168.0.13:/opt/
done
```

![](img/5c3776c1013c63a8fa18e8bd88656ab6_70.png)

**脚本说明**：
*   `inotifywait` 持续监控 `/root` 目录。
*   一旦有文件被修改、创建、删除或移动，就会触发 `do...done` 之间的命令。
*   触发后，立即执行 `rsync` 命令将变化同步到远程主机。

### 运行与测试脚本

1.  给脚本添加执行权限：
    ```bash
    chmod +x /script/rsync_inotify.sh
    ```
2.  在后台运行脚本：
    ```bash
    /script/rsync_inotify.sh &
    ```
3.  进行测试：在 `/root` 目录下创建或修改文件。
    ```bash
    touch /root/test_realtime.txt
    ```
4.  立即检查远程主机 `192.168.0.13` 的 `/opt` 目录，会发现新文件几乎同步出现。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_72.png)

**停止脚本**：由于脚本在后台运行，可以使用 `fg` 命令调到前台后按 `Ctrl+C` 终止，或使用 `pkill -f inotifywait` 结束相关进程。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_74.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_76.png)

---

## 总结

![](img/5c3776c1013c63a8fa18e8bd88656ab6_78.png)

本节课中我们一起学习了 Rsync 和 Inotify 在数据备份与同步中的应用。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_80.png)

1.  **Rsync 核心**：我们理解了增量备份的原理，掌握了 Rsync 进行**本地同步**和**远程同步**的基本命令和关键选项（如 `-a`, `-v`, `-z`, `--delete`）。
2.  **自动化基础**：我们学习了通过配置 **SSH 免密登录** 来简化远程同步，并提到可以通过 **Cron 计划任务** 定期执行 Rsync 脚本，实现自动化定时备份。
3.  **实时同步**：我们探讨了使用 **Inotify** 监控文件系统事件，并编写脚本将其与 Rsync 结合，实现了数据的实时同步。同时，我们也了解了这种方案可能带来的资源消耗。

![](img/5c3776c1013c63a8fa18e8bd88656ab6_82.png)

![](img/5c3776c1013c63a8fa18e8bd88656ab6_84.png)

对于企业环境，建议根据实际数据变更频率和重要性，选择**定时任务备份**或**实时监控备份**方案。Rsync 凭借其高效、可靠的特性，是构建数据备份策略不可或缺的工具。