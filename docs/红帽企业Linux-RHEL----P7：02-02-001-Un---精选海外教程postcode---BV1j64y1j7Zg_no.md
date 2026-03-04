# 红帽企业Linux RHEL 9精通课程：P7：02-02-001：理解和使用基本工具 🛠️

![](img/4252bcecd78e8e9b342c657234bcf726_1.png)

![](img/4252bcecd78e8e9b342c657234bcf726_3.png)

![](img/4252bcecd78e8e9b342c657234bcf726_5.png)

![](img/4252bcecd78e8e9b342c657234bcf726_7.png)

![](img/4252bcecd78e8e9b342c657234bcf726_9.png)

![](img/4252bcecd78e8e9b342c657234bcf726_11.png)

![](img/4252bcecd78e8e9b342c657234bcf726_13.png)

## 概述
在本节课程中，我们将学习作为红帽系统管理员所需掌握的核心基本工具。理解并熟练使用这些工具对于有效管理系统至关重要。我们将从远程登录开始，逐步学习文件操作、输入输出重定向、文本分析、归档压缩、权限管理以及查阅系统文档。

![](img/4252bcecd78e8e9b342c657234bcf726_15.png)

## 远程登录服务器
要管理一个系统，首先需要能够登录它。对于远程服务器，我们使用 `ssh` 命令。

![](img/4252bcecd78e8e9b342c657234bcf726_17.png)

![](img/4252bcecd78e8e9b342c657234bcf726_19.png)

![](img/4252bcecd78e8e9b342c657234bcf726_21.png)

![](img/4252bcecd78e8e9b342c657234bcf726_23.png)

![](img/4252bcecd78e8e9b342c657234bcf726_25.png)

以下是使用SSH登录远程服务器的步骤：
1.  打开终端。
2.  输入命令 `ssh username@host_ip_address`。
3.  按提示输入用户密码。

![](img/4252bcecd78e8e9b342c657234bcf726_27.png)

![](img/4252bcecd78e8e9b342c657234bcf726_29.png)

![](img/4252bcecd78e8e9b342c657234bcf726_31.png)

![](img/4252bcecd78e8e9b342c657234bcf726_33.png)

![](img/4252bcecd78e8e9b342c657234bcf726_35.png)

![](img/4252bcecd78e8e9b342c657234bcf726_37.png)

![](img/4252bcecd78e8e9b342c657234bcf726_39.png)

例如，登录到IP地址为 `192.168.1.100` 的服务器上的 `cloud_user` 账户：
```bash
ssh cloud_user@192.168.1.100
```

![](img/4252bcecd78e8e9b342c657234bcf726_41.png)

![](img/4252bcecd78e8e9b342c657234bcf726_43.png)

![](img/4252bcecd78e8e9b342c657234bcf726_45.png)

![](img/4252bcecd78e8e9b342c657234bcf726_47.png)

![](img/4252bcecd78e8e9b342c657234bcf726_49.png)

![](img/4252bcecd78e8e9b342c657234bcf726_51.png)

## 创建文件和目录
成功登录系统后，我们可以在文件系统中创建和删除文件与目录。

![](img/4252bcecd78e8e9b342c657234bcf726_53.png)

![](img/4252bcecd78e8e9b342c657234bcf726_55.png)

以下是相关的命令操作：
*   **创建目录**：使用 `mkdir` 命令。例如，`mkdir test` 会创建一个名为 `test` 的目录。
*   **创建文件**：有多种方法：
    *   使用 `touch` 命令创建空文件，例如 `touch test_one`。
    *   使用文本编辑器（如 `vi` 或 `nano`）创建并编辑文件，例如 `vi test_two`。
*   **删除文件**：使用 `rm` 命令。例如，`rm test_one`。
*   **删除目录**：
    *   删除空目录使用 `rmdir` 命令，例如 `rmdir test`。
    *   删除非空目录需要使用 `rm -r` 命令进行递归删除，例如 `rm -r test`。

![](img/4252bcecd78e8e9b342c657234bcf726_57.png)

![](img/4252bcecd78e8e9b342c657234bcf726_59.png)

![](img/4252bcecd78e8e9b342c657234bcf726_61.png)

## 输入和输出重定向
在Linux中，命令的输入和输出可以被重定向，这是自动化处理数据的关键。

![](img/4252bcecd78e8e9b342c657234bcf726_63.png)

![](img/4252bcecd78e8e9b342c657234bcf726_64.png)

![](img/4252bcecd78e8e9b342c657234bcf726_66.png)

![](img/4252bcecd78e8e9b342c657234bcf726_68.png)

![](img/4252bcecd78e8e9b342c657234bcf726_70.png)

*   **标准输出 (stdout)**：命令的正常输出。使用 `>` 符号可以将其重定向到文件（覆盖原有内容），使用 `>>` 可以追加到文件末尾。
    *   例如：`ls -la /var/log > logfile` 将目录列表保存到 `logfile` 文件中。
*   **标准错误 (stderr)**：命令的错误信息。使用 `2>` 将其重定向到文件。
    *   例如：`ls -l /nonexistent 2> error.log` 将错误信息保存到 `error.log`。
*   **标准输入 (stdin)**：命令接收数据的来源。使用 `<` 符号可以从文件读取输入。
    *   例如：`sort < logfile` 会读取 `logfile` 的内容并进行排序。
*   **管道 (|)**：将一个命令的输出作为另一个命令的输入。
    *   例如：`cat log_sorted | grep cron` 会先显示 `log_sorted` 文件内容，然后筛选出包含 “cron” 的行。

![](img/4252bcecd78e8e9b342c657234bcf726_72.png)

![](img/4252bcecd78e8e9b342c657234bcf726_74.png)

![](img/4252bcecd78e8e9b342c657234bcf726_76.png)

![](img/4252bcecd78e8e9b342c657234bcf726_78.png)

## 查看和分析文本
系统管理员经常需要查看和分析日志或配置文件的内容。

![](img/4252bcecd78e8e9b342c657234bcf726_80.png)

![](img/4252bcecd78e8e9b342c657234bcf726_82.png)

![](img/4252bcecd78e8e9b342c657234bcf726_84.png)

以下是常用的文本处理命令：
*   **`cat`**：连接文件并打印到标准输出，常用于快速查看文件内容。例如：`cat filename`。
*   **`less` / `more`**：分页查看文件内容，适合浏览大文件。
*   **`grep`**：强大的文本搜索工具，可以使用正则表达式。
    *   例如：`grep ‘samba$’ logfile` 搜索 `logfile` 中以 “samba” 结尾的行。
    *   例如：`grep ‘^rwx’ logfile` 搜索以 “rwx” 开头的行。

![](img/4252bcecd78e8e9b342c657234bcf726_86.png)

![](img/4252bcecd78e8e9b342c657234bcf726_88.png)

![](img/4252bcecd78e8e9b342c657234bcf726_90.png)

![](img/4252bcecd78e8e9b342c657234bcf726_92.png)

## 归档和压缩文件
为了备份或传输方便，我们经常需要将多个文件打包并压缩。

![](img/4252bcecd78e8e9b342c657234bcf726_94.png)

![](img/4252bcecd78e8e9b342c657234bcf726_96.png)

`tar` 命令是完成此任务的主要工具。其常用选项组合为：
*   **创建压缩归档**：`tar -czvf archive_name.tar.gz file1 file2 dir1`
    *   `-c`：创建新归档。
    *   `-z`：使用gzip压缩。
    *   `-v`：显示详细过程。
    *   `-f`：指定归档文件名。
*   **解压归档**：`tar -xzvf archive_name.tar.gz`
    *   `-x`：解压归档。
    *   可以添加 `-C` 选项指定解压到的目录，例如 `tar -xzvf archive.tar.gz -C /target/path`。

![](img/4252bcecd78e8e9b342c657234bcf726_98.png)

![](img/4252bcecd78e8e9b342c657234bcf726_100.png)

![](img/4252bcecd78e8e9b342c657234bcf726_102.png)

## 提升权限执行任务
许多系统管理任务需要更高的权限（root权限）。我们不建议直接以root用户登录，而是使用 `sudo` 命令临时提升权限。

*   **使用 `sudo`**：在命令前加上 `sudo`，以root权限执行该命令。
    *   例如：普通用户无法在 `/etc` 目录创建文件，但可以使用 `sudo touch /etc/test_file`。
*   **切换到root用户**：使用 `sudo -i` 或 `sudo su -` 可以切换到root用户shell环境，但操作需格外谨慎。

![](img/4252bcecd78e8e9b342c657234bcf726_104.png)

![](img/4252bcecd78e8e9b342c657234bcf726_106.png)

![](img/4252bcecd78e8e9b342c657234bcf726_108.png)

![](img/4252bcecd78e8e9b342c657234bcf726_110.png)

![](img/4252bcecd78e8e9b342c657234bcf726_112.png)

![](img/4252bcecd78e8e9b342c657234bcf726_114.png)

## 文件和目录权限
Linux系统的安全性很大程度上依赖于文件权限。我们使用 `chown`, `chgrp`, `chmod` 来管理权限。

*   **`chown`**：改变文件所有者和所属组。
    *   格式：`chown user:group filename`
*   **`chgrp`**：改变文件所属组。
*   **`chmod`**：改变文件权限。有两种模式：
    *   **数字模式**：用三位八进制数表示 `用户(u)`、`组(g)`、`其他(o)` 的 `读(r=4)`、`写(w=2)`、`执行(x=1)` 权限。
        *   例如：`chmod 764 file` 表示用户有rwx权限，组有rw-权限，其他用户有r--权限。
    *   **符号模式**：使用 `u/g/o/a` 和 `+/-/=` 以及 `r/w/x` 来增减权限。
        *   例如：`chmod u+x file` 给文件所有者增加执行权限。
*   **特殊权限**：
    *   **SetUID (4)**：文件执行时，以文件所有者的权限运行。例如：`chmod 4755 file`。
    *   **SetGID (2)**：目录中创建的新文件继承目录的所属组。
    *   **粘滞位 (1)**：只有文件所有者、目录所有者或root才能删除目录内的文件。常用于 `/tmp` 目录。

## 使用系统文档
没有人能记住所有命令和选项，善于查阅文档是优秀管理员的关键技能。

![](img/4252bcecd78e8e9b342c657234bcf726_116.png)

![](img/4252bcecd78e8e9b342c657234bcf726_117.png)

*   **`man`**：查看命令的完整手册页，是最重要的文档来源。例如：`man chmod`。
*   **`info`**：提供比 `man` 更结构化的文档，有时内容更详细。
*   **`/usr/share/doc`**：包含许多已安装软件的额外文档。
*   **`apropos` 或 `man -k`**：当你只记得某个功能关键词，而忘记具体命令名时，可以用它来搜索。例如：`apropos permission` 会列出所有与权限相关的命令。

![](img/4252bcecd78e8e9b342c657234bcf726_119.png)

![](img/4252bcecd78e8e9b342c657234bcf726_120.png)

![](img/4252bcecd78e8e9b342c657234bcf726_122.png)

## 总结
本节课我们一起学习了红帽系统管理员必备的基本工具。我们从远程登录 (`ssh`) 开始，掌握了文件和目录的创建删除操作，深入理解了输入输出重定向和管道的强大功能，并学会了使用 `grep` 等工具分析文本。接着，我们探讨了如何使用 `tar` 进行归档压缩，如何使用 `sudo` 安全地提升权限，以及如何通过 `chmod`、`chown` 精细控制文件和目录的访问权。最后，我们强调了熟练使用 `man`、`apropos` 等系统文档工具对于日常管理和备考认证的重要性。掌握这些基础工具是迈向自动化管理和通过更高级认证的坚实第一步。