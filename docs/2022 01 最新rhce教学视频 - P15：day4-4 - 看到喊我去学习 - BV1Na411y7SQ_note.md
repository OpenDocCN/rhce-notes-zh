# Linux文件管理与传输：P15：压缩解压与文件传输 📁

在本节课中，我们将要学习Linux系统中常用的文件压缩、解压缩命令，以及在不同主机间传输文件的几种方法。这些技能对于日常系统管理和RHCE考试都非常重要。

## 压缩与解压缩命令概览 🔧

上一节我们介绍了基本的文件操作，本节中我们来看看如何高效地管理文件大小和进行归档。Linux提供了多种压缩和解压缩工具，它们各有特点。

![](img/88f1bb4ababf4bfbf166386e38b5df2f_1.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_3.png)

以下是常用的压缩与解压缩命令及其对应关系：
*   **压缩命令**：`gzip`、`bzip2`、`xz`、`zip`
*   **解压缩命令**：通常在压缩命令前加 `un`，如 `gunzip`、`bunzip2`、`unxz`、`unzip`。`gzip` 和 `bzip2` 也可以使用 `-d` 参数来解压。

![](img/88f1bb4ababf4bfbf166386e38b5df2f_5.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_7.png)

### 使用 gzip/gunzip 进行压缩 🔄

`gzip` 是常用的压缩工具。其压缩效率可以通过 `-1` 到 `-9` 的等级来调节，数字越大，压缩质量越好（文件越小），但速度越慢。

![](img/88f1bb4ababf4bfbf166386e38b5df2f_9.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_11.png)

常用参数如下：
*   `-d`：解压缩，相当于 `gunzip` 命令。
*   `-c`：将压缩或解压的内容输出到标准输出，而不改变原文件。
*   `-t`：测试压缩文件的完整性。
*   `-v`：显示压缩或解压的详细信息。
*   `-1` 到 `-9`：设置压缩等级。

![](img/88f1bb4ababf4bfbf166386e38b5df2f_12.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_13.png)

**操作示例**：
首先，我们复制一个文件到 `/data` 目录下进行操作。
```bash
cp /var/log/messages /data/
```
压缩该文件，并保留原文件：
```bash
gzip -c messages > messages.gz
```
解压缩该文件：
```bash
gzip -d messages.gz
# 或使用 gunzip
gunzip messages.gz
```
使用 `-c` 参数查看压缩文件内容：
```bash
gzip -cd messages.gz | cat
```
请注意，在压缩或解压过程中，文件的权限属性可能会发生变化。

![](img/88f1bb4ababf4bfbf166386e38b5df2f_15.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_17.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_19.png)

### 使用 bzip2/bunzip2 进行压缩 📦

![](img/88f1bb4ababf4bfbf166386e38b5df2f_21.png)

`bzip2` 的用法与 `gzip` 类似，只是命令和生成的文件后缀不同（`.bz2`）。

![](img/88f1bb4ababf4bfbf166386e38b5df2f_23.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_25.png)

**操作示例**：
压缩文件：
```bash
bzip2 -c messages > messages.bz2
```
查看压缩文件内容：
```bash
bzip2 -cd messages.bz2 | cat
```

![](img/88f1bb4ababf4bfbf166386e38b5df2f_27.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_29.png)

### 使用 xz/unxz 进行压缩 💎

`xz` 是另一个压缩工具，用法也与前两者相似。

**操作示例**：
压缩文件：
```bash
xz -c messages > messages.xz
```

### 使用 zip/unzip 进行压缩 📎

`zip` 命令在打包目录和多个文件时比较方便，但可能会丢失一些文件属性（如所有者和组信息）。通常建议使用 `tar` 进行打包。

![](img/88f1bb4ababf4bfbf166386e38b5df2f_31.png)

**操作示例**：
压缩当前目录：
```bash
zip -r data.zip .
```
解压文件：
```bash
unzip data.zip
```
解压到指定目录：
```bash
unzip messages.zip -d /opt/
```

![](img/88f1bb4ababf4bfbf166386e38b5df2f_33.png)

### 使用 tar 进行归档与压缩 🗃️

![](img/88f1bb4ababf4bfbf166386e38b5df2f_35.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_36.png)

`tar` 命令本身用于归档（打包），可以结合 `gzip`、`bzip2` 或 `xz` 进行压缩，功能强大且能很好保留文件属性。

常用参数组合：
*   `-c`：创建归档文件。
*   `-x`：解压归档文件。
*   `-v`：显示详细过程。
*   `-f`：指定归档文件名。
*   `-z`：通过 `gzip` 过滤归档（即用gzip压缩/解压）。
*   `-j`：通过 `bzip2` 过滤归档。
*   `-J`：通过 `xz` 过滤归档。
*   `-t`：列出归档内容。

![](img/88f1bb4ababf4bfbf166386e38b5df2f_38.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_40.png)

**操作示例**：
创建并用gzip压缩归档：
```bash
tar -czvf etc_backup.tar.gz /etc
```
列出归档内容：
```bash
tar -tzvf etc_backup.tar.gz
```
解压归档：
```bash
tar -xzvf etc_backup.tar.gz -C /tmp/
```

![](img/88f1bb4ababf4bfbf166386e38b5df2f_42.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_44.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_46.png)

## 文件传输命令 🚀

![](img/88f1bb4ababf4bfbf166386e38b5df2f_48.png)

掌握了文件的压缩打包后，我们来看看如何在不同的Linux主机之间安全地传输这些文件。

### 使用 scp 进行安全复制 🔐

![](img/88f1bb4ababf4bfbf166386e38b5df2f_50.png)

`scp` 基于SSH协议，用于在服务器之间安全地复制文件或目录。

![](img/88f1bb4ababf4bfbf166386e38b5df2f_52.png)

常用选项：
*   `-C`：压缩数据流。
*   `-r`：递归复制目录。
*   `-P`：指定远程主机的SSH端口（注意是大写P）。
*   `-p`：保留原文件的修改时间、访问时间和权限。
*   `-q`：静默模式，不显示传输进度。

**操作示例**：
将本地文件复制到远程服务器：
```bash
scp -P 22 /data/messages.gz root@192.168.181.135:/opt/
```
从远程服务器复制文件到本地：
```bash
scp root@192.168.181.135:/opt/messages.gz /data/
```

### 使用 rsync 进行高效同步 ⚡

`rsync` 是一个更高级的同步工具，支持增量同步，只传输发生变化的部分，效率比 `scp` 更高。

![](img/88f1bb4ababf4bfbf166386e38b5df2f_54.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_56.png)

常用选项：
*   `-a`：归档模式，保持所有文件属性，等同于 `-rlptgoD`。
*   `-v`：显示详细过程。
*   `-r`：递归处理目录。
*   `-z`：传输时进行压缩。
*   `-n`：模拟执行，不实际传输。
*   `--delete`：删除目标端源端没有的文件，实现严格同步。

**操作示例**：
同步本地目录到远程服务器：
```bash
rsync -avz /data/ root@192.168.181.135:/opt/
```
模拟同步（看哪些文件会变化）：
```bash
rsync -avzn /data/ root@192.168.181.135:/opt/
```

### 使用 sftp 进行交互式传输 💬

![](img/88f1bb4ababf4bfbf166386e38b5df2f_58.png)

`sftp` 提供了一个交互式的文件传输环境，类似于传统的FTP，但基于SSH，更加安全。

![](img/88f1bb4ababf4bfbf166386e38b5df2f_60.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_61.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_62.png)

**基本用法**：
连接远程服务器：
```bash
sftp root@192.168.181.135
```
进入sftp后，常用命令：
*   `ls`、`lls`：列出远程/本地目录。
*   `cd`、`lcd`：切换远程/本地目录。
*   `get`：下载文件。
*   `put`：上传文件。
*   `exit`：退出。

![](img/88f1bb4ababf4bfbf166386e38b5df2f_64.png)

**操作示例**：
下载远程文件：
```bash
sftp> get /opt/messages.gz /data/
```
上传本地文件：
```bash
sftp> put /data/newfile.txt /opt/
```

![](img/88f1bb4ababf4bfbf166386e38b5df2f_66.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_68.png)

此外，也可以使用图形化工具（如FileZilla）通过SFTP协议进行文件传输，操作更为直观。

![](img/88f1bb4ababf4bfbf166386e38b5df2f_70.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_72.png)

![](img/88f1bb4ababf4bfbf166386e38b5df2f_74.png)

## 总结 📝

![](img/88f1bb4ababf4bfbf166386e38b5df2f_76.png)

本节课中我们一起学习了Linux下的文件压缩、解压缩以及网络文件传输。
*   我们掌握了 `gzip`、`bzip2`、`xz`、`zip` 和功能强大的 `tar` 命令的使用方法，能够根据需求选择合适的工具对文件进行归档和压缩。
*   我们学习了三种文件传输方式：`scp` 用于简单安全复制，`rsync` 用于高效的增量同步，`sftp` 用于交互式的安全文件传输。理解它们各自的适用场景，能帮助我们在管理多台服务器时更得心应手。

![](img/88f1bb4ababf4bfbf166386e38b5df2f_78.png)

这些命令是Linux系统管理员日常工作中的基础，熟练掌握它们对于通过RHCE认证和实际运维工作都至关重要。