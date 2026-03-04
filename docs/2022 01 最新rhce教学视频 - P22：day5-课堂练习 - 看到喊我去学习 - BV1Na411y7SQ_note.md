# Linux进程与文件管理：P22：Day5课堂练习

![](img/aedf68484749f90b0186e629cb0b890d_0.png)

![](img/aedf68484749f90b0186e629cb0b890d_2.png)

在本节课中，我们将通过一系列练习题，巩固Linux系统中关于进程管理、文本处理、文件操作和网络服务配置的核心命令。这些练习旨在帮助初学者熟悉常用命令的实际应用场景。

![](img/aedf68484749f90b0186e629cb0b890d_3.png)

![](img/aedf68484749f90b0186e629cb0b890d_5.png)

## 1️⃣ 第一题：进程状态

进程在其生命周期中会经历几种主要状态。

以下是进程的几种基本状态：
*   **创建**：进程被创建时的初始状态。
*   **就绪**：进程已准备好运行，等待CPU分配时间片。
*   **执行**：进程正在CPU上运行。
*   **阻塞**：进程因等待某个事件（如I/O操作完成）而暂停执行。
*   **终止**：进程执行完毕或被强制结束。

![](img/aedf68484749f90b0186e629cb0b890d_7.png)

![](img/aedf68484749f90b0186e629cb0b890d_9.png)

## 2️⃣ 第二题：查看进程树

![](img/aedf68484749f90b0186e629cb0b890d_11.png)

![](img/aedf68484749f90b0186e629cb0b890d_12.png)

![](img/aedf68484749f90b0186e629cb0b890d_14.png)

上一节我们介绍了进程状态，本节中我们来看看如何查看系统中进程的层次关系。

![](img/aedf68484749f90b0186e629cb0b890d_16.png)

查看进程树可以使用 `pstree` 命令。它能以树状图形式显示进程间的父子关系。

![](img/aedf68484749f90b0186e629cb0b890d_18.png)

**命令示例**：
```bash
pstree
```

![](img/aedf68484749f90b0186e629cb0b890d_20.png)

![](img/aedf68484749f90b0186e629cb0b890d_21.png)

## 3️⃣ 第三题：查看指定进程信息

![](img/aedf68484749f90b0186e629cb0b890d_23.png)

![](img/aedf68484749f90b0186e629cb0b890d_24.png)

除了查看进程树，我们经常需要获取特定进程的详细信息。

![](img/aedf68484749f90b0186e629cb0b890d_26.png)

以下是查看进程信息的常用命令：
*   `ps aux`：查看系统中所有用户的进程。
*   `ps -ef`：以完整格式列出所有进程。
*   `ps -eF`：以更详细的格式（包括内存使用率）列出进程。

![](img/aedf68484749f90b0186e629cb0b890d_28.png)

![](img/aedf68484749f90b0186e629cb0b890d_29.png)

## 4️⃣ 第四题：筛选与进程相关的行

![](img/aedf68484749f90b0186e629cb0b890d_31.png)

![](img/aedf68484749f90b0186e629cb0b890d_33.png)

有时我们需要从大量文本或命令输出中筛选出与特定关键词（如进程ID）相关的信息。

![](img/aedf68484749f90b0186e629cb0b890d_34.png)

![](img/aedf68484749f90b0186e629cb0b890d_36.png)

`grep` 命令可以用于筛选文本。例如，要筛选出包含“PID”的行，可以执行：
```bash
ps -ef | grep PID
```
这将从 `ps -ef` 的输出中，只显示包含“PID”字符串的行。

## 5️⃣ 第五题：统计文本中关键词出现次数

在文本处理中，统计某个关键词出现的频率是一个常见需求。

![](img/aedf68484749f90b0186e629cb0b890d_38.png)

![](img/aedf68484749f90b0186e629cb0b890d_40.png)

![](img/aedf68484749f90b0186e629cb0b890d_41.png)

`grep` 命令结合 `-c` 选项可以统计匹配行的数量。例如，统计文件 `test.txt` 中包含“test”的行数：
```bash
grep -c “test” test.txt
```
另一种方法是结合 `wc -l` 命令统计行数：
```bash
grep “test” test.txt | wc -l
```

![](img/aedf68484749f90b0186e629cb0b890d_42.png)

![](img/aedf68484749f90b0186e629cb0b890d_43.png)

## 6️⃣ 第六题：显示匹配行的上下文

`grep` 命令不仅能找到匹配行，还能显示该行前后的内容，这在日志分析中非常有用。

![](img/aedf68484749f90b0186e629cb0b890d_45.png)

![](img/aedf68484749f90b0186e629cb0b890d_46.png)

`grep` 命令的上下文控制选项如下：
*   `-A n`：显示匹配行之后的 **n** 行（After）。
*   `-B n`：显示匹配行之前的 **n** 行（Before）。
*   `-C n`：显示匹配行之前和之后的各 **n** 行（Context）。

![](img/aedf68484749f90b0186e629cb0b890d_47.png)

## 7️⃣ 第七题：查看与控制系统服务

![](img/aedf68484749f90b0186e629cb0b890d_49.png)

![](img/aedf68484749f90b0186e629cb0b890d_50.png)

在Linux中，我们使用 `systemctl` 命令来管理systemd服务。

![](img/aedf68484749f90b0186e629cb0b890d_52.png)

![](img/aedf68484749f90b0186e629cb0b890d_54.png)

以下是 `systemctl` 的常用操作：
*   `systemctl status sshd`：查看sshd服务的状态。
*   `systemctl start sshd`：启动sshd服务。
*   `systemctl stop sshd`：停止sshd服务。
*   `systemctl restart sshd`：重启sshd服务。
*   `systemctl enable sshd`：设置sshd服务开机自启。
*   `systemctl disable sshd`：取消sshd服务开机自启。

![](img/aedf68484749f90b0186e629cb0b890d_56.png)

![](img/aedf68484749f90b0186e629cb0b890d_58.png)

## 8️⃣ 第八题：SSH远程连接与指定端口

![](img/aedf68484749f90b0186e629cb0b890d_59.png)

![](img/aedf68484749f90b0186e629cb0b890d_61.png)

`ssh` 命令用于远程登录服务器。当目标服务器的SSH端口不是默认的22时，需要使用 `-p` 选项指定端口。

![](img/aedf68484749f90b0186e629cb0b890d_62.png)

![](img/aedf68484749f90b0186e629cb0b890d_63.png)

![](img/aedf68484749f90b0186e629cb0b890d_64.png)

**命令格式**：
```bash
ssh -p [端口号] [用户名]@[服务器地址]
```
例如，使用端口 2222 连接：
```bash
ssh -p 2222 root@192.168.1.100
```

![](img/aedf68484749f90b0186e629cb0b890d_66.png)

![](img/aedf68484749f90b0186e629cb0b890d_68.png)

## 9️⃣ 第九题：SCP跨端口文件传输

![](img/aedf68484749f90b0186e629cb0b890d_69.png)

![](img/aedf68484749f90b0186e629cb0b890d_71.png)

与 `ssh` 类似，`scp` 命令用于在本地和远程主机间安全地复制文件，同样支持指定非默认端口。

![](img/aedf68484749f90b0186e629cb0b890d_73.png)

![](img/aedf68484749f90b0186e629cb0b890d_75.png)

**命令格式**：
```bash
scp -P [端口号] [源文件] [目标路径]
```
**记忆技巧**：`ssh` 指定端口用 **小写 -p**，`scp` 指定端口用 **大写 -P**。
例如，将本地文件复制到远程服务器的 `/tmp` 目录，使用端口 2222：
```bash
scp -P 2222 ./local_file root@192.168.1.100:/tmp/
```

![](img/aedf68484749f90b0186e629cb0b890d_77.png)

## 🔟 第十题：配置NTP时间同步

保持系统时间准确非常重要，NTP（网络时间协议）用于同步系统时钟。

![](img/aedf68484749f90b0186e629cb0b890d_79.png)

![](img/aedf68484749f90b0186e629cb0b890d_80.png)

配置NTP服务通常涉及以下步骤：
1.  安装NTP客户端软件包（如 `chrony`）。
2.  编辑配置文件（如 `/etc/chrony.conf`），添加时间服务器地址。
3.  启动并启用服务，然后手动同步一次时间。
    ```bash
    systemctl start chronyd
    systemctl enable chronyd
    chronyc sources -v # 查看时间源状态
    ```

## 1️⃣1️⃣ 第十一题：配置防火墙规则

![](img/aedf68484749f90b0186e629cb0b890d_82.png)

`firewall-cmd` 是管理firewalld防火墙的主要工具。

![](img/aedf68484749f90b0186e629cb0b890d_84.png)

![](img/aedf68484749f90b0186e629cb0b890d_86.png)

添加一条允许TCP协议访问80端口的永久规则，并重载配置：
```bash
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --reload
```

![](img/aedf68484749f90b0186e629cb0b890d_87.png)

![](img/aedf68484749f90b0186e629cb0b890d_89.png)

## 1️⃣2️⃣ 第十二题：文件归档与压缩

![](img/aedf68484749f90b0186e629cb0b890d_90.png)

`tar` 命令用于将多个文件打包成一个归档文件，并可选进行压缩。

以下是创建和解压归档文件的常用命令：
*   **创建压缩包**：`tar -czvf archive_name.tar.gz /path/to/directory`
    *   `-c`：创建归档。
    *   `-z`：使用gzip压缩。
    *   `-v`：显示详细过程。
    *   `-f`：指定文件名。
*   **解压压缩包**：`tar -xzvf archive_name.tar.gz -C /target/directory`
    *   `-x`：解压归档。
    *   `-C`：指定解压目标目录。

## 总结

![](img/aedf68484749f90b0186e629cb0b890d_92.png)

本节课中我们一起学习了多个Linux核心命令的实践应用。我们回顾了进程的状态管理和查看命令（`ps`， `pstree`），练习了文本处理工具（`grep`， `wc`）的多种用法，包括搜索、统计和显示上下文。我们还操作了系统服务（`systemctl`）、网络连接（`ssh`， `scp`）、时间同步（NTP）、防火墙（`firewall-cmd`）以及文件打包压缩（`tar`）。熟练掌握这些命令是进行有效系统管理的基础。