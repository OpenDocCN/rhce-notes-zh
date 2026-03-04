# Linux教程RHCE：P20：Ansible使用和模块化深入解析 🚀

在本节课中，我们将深入学习Ansible的主配置文件、常用命令、主机模式以及核心模块的使用。通过本教程，你将掌握如何高效地配置和使用Ansible进行批量自动化管理。

---

![](img/fc50ff1a265fdcefbf178583eb15342b_1.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_3.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_5.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_7.png)

## Ansible主配置文件详解 ⚙️

![](img/fc50ff1a265fdcefbf178583eb15342b_9.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_11.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_13.png)

上一节我们介绍了Ansible的基本概念和主机清单。本节中，我们来看看Ansible的核心配置文件 `ansible.cfg`。

![](img/fc50ff1a265fdcefbf178583eb15342b_15.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_17.png)

`ansible.cfg` 是Ansible的主配置文件，它包含多个由中括号分隔的语句块。虽然文件有400多行，但大部分是注释，许多配置项保持默认值即可。

![](img/fc50ff1a265fdcefbf178583eb15342b_19.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_20.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_22.png)

以下是配置文件中的一些关键参数及其作用：

![](img/fc50ff1a265fdcefbf178583eb15342b_24.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_26.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_28.png)

*   **`inventory`**：指定主机清单文件的默认路径。
*   **`remote_tmp`** 与 **`local_tmp`**：这两个参数搭配使用。当执行Ansible命令时，Ansible会先将模块命令转换为Python脚本，存放在控制节点的 `~/.ansible/tmp` 目录下，然后复制到被控节点的临时目录中执行，执行完毕后自动删除。
*   **`forks`**：设置并行执行的任务数，默认为5。这可以提升批量操作的速度。
*   **`host_key_checking`**：默认被注释。如果启用（设为 `False`），Ansible将不会在首次连接时检查远程主机的SSH密钥，避免了手动确认的麻烦，这在管理大量新主机时非常有用。
*   **`log_path`**：默认被注释。取消注释并指定路径（如 `/var/log/ansible.log`）可以启用日志记录，便于审计和排错。

![](img/fc50ff1a265fdcefbf178583eb15342b_30.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_32.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_34.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_35.png)

**重要提示**：修改 `ansible.cfg` 后无需重启任何服务，因为Ansible不是常驻内存的服务，配置在下次执行命令时自动生效。

![](img/fc50ff1a265fdcefbf178583eb15342b_37.png)

---

![](img/fc50ff1a265fdcefbf178583eb15342b_39.png)

## Ansible常用命令与文档 📚

![](img/fc50ff1a265fdcefbf178583eb15342b_41.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_43.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_45.png)

了解了配置文件后，我们来看看Ansible提供的一系列命令工具。

![](img/fc50ff1a265fdcefbf178583eb15342b_47.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_49.png)

最核心的命令是 `ansible`，用于执行临时任务。此外，`ansible-doc` 命令类似于Linux的 `man` 命令，用于查看模块的帮助文档。

![](img/fc50ff1a265fdcefbf178583eb15342b_51.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_53.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_55.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_56.png)

以下是 `ansible-doc` 的常用选项：

![](img/fc50ff1a265fdcefbf178583eb15342b_58.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_60.png)

*   `ansible-doc -l`：列出所有可用模块及其简要说明。
*   `ansible-doc <模块名>`：查看指定模块的详细使用说明。
*   `ansible-doc -s <模块名>`：以简洁片段格式显示模块的用法。

![](img/fc50ff1a265fdcefbf178583eb15342b_62.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_64.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_66.png)

**示例**：查看 `ping` 模块的帮助。
```bash
ansible-doc ping
```

![](img/fc50ff1a265fdcefbf178583eb15342b_68.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_70.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_71.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_73.png)

Ansible社区非常活跃，模块数量增长迅速（目前已超过1600个），涵盖了管理各种系统、服务和应用程序的需求。

---

![](img/fc50ff1a265fdcefbf178583eb15342b_75.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_77.png)

## Ansible命令详解与主机模式 🎯

![](img/fc50ff1a265fdcefbf178583eb15342b_79.png)

现在，让我们深入 `ansible` 命令的语法和强大的主机模式。

![](img/fc50ff1a265fdcefbf178583eb15342b_81.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_83.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_85.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_87.png)

`ansible` 命令的基本格式如下：
```bash
ansible <主机模式> -m <模块名> -a "<模块参数>"
```

![](img/fc50ff1a265fdcefbf178583eb15342b_89.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_91.png)

其中，`-m` 指定模块，`-a` 指定模块参数。`-k` 用于交互式输入SSH密码，`-u` 指定远程连接用户，`-b` 用于提权执行（类似 `sudo`）。

![](img/fc50ff1a265fdcefbf178583eb15342b_93.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_95.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_97.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_99.png)

**主机模式** 定义了命令作用的目标主机，非常灵活：

![](img/fc50ff1a265fdcefbf178583eb15342b_101.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_103.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_105.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_107.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_109.png)

*   **分组名**：`ansible webservers -m ping`
*   **所有主机**：`ansible all -m ping`
*   **通配符**：`ansible ‘web*‘ -m ping` （匹配所有以 `web` 开头的分组）
*   **逻辑或**：`ansible ‘webservers:dbservers‘ -m ping` （在 `webservers` 或 `dbservers` 分组中的主机）
*   **逻辑与**：`ansible ‘webservers:&dbservers‘ -m ping` （同时在两个分组中的主机）
*   **逻辑非**：`ansible ‘webservers:!dbservers‘ -m ping` （在 `webservers` 但不在 `dbservers` 中的主机）
*   **正则表达式**：`ansible ‘~(web|db).*‘ -m ping` （使用正则匹配）

![](img/fc50ff1a265fdcefbf178583eb15342b_111.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_113.png)

**执行过程颜色提示**：
*   **绿色**：任务成功执行，且未对目标系统做任何更改（如 `ping`、`stat`）。
*   **黄色**：任务成功执行，并对目标系统做出了更改（如创建文件、安装软件）。
*   **红色**：任务执行失败。

![](img/fc50ff1a265fdcefbf178583eb15342b_115.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_117.png)

---

![](img/fc50ff1a265fdcefbf178583eb15342b_119.png)

## Ansible核心模块实战 🛠️

掌握了命令和模式，本节我们通过实战来学习几个最常用的Ansible模块。

![](img/fc50ff1a265fdcefbf178583eb15342b_121.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_123.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_125.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_127.png)

### 1. command 和 shell 模块

![](img/fc50ff1a265fdcefbf178583eb15342b_129.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_131.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_133.png)

`command` 模块是默认模块，用于在远程执行简单的命令。但它不支持管道 `|`、重定向 `>`、变量 `$` 等Shell特性。

![](img/fc50ff1a265fdcefbf178583eb15342b_135.png)

**示例**：在所有主机上创建目录。
```bash
ansible all -a “mkdir -p /data“
```

![](img/fc50ff1a265fdcefbf178583eb15342b_137.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_139.png)

`shell` 模块功能更强大，支持所有Shell特性。当命令中涉及管道、重定向或变量时，应使用 `shell` 模块。

**示例**：使用变量和重定向。
```bash
ansible all -m shell -a ‘echo $HOSTNAME > /data/hostinfo.txt‘
```

### 2. script 模块

`script` 模块用于将控制节点上的脚本复制到远程主机执行，无需手动分发脚本。

![](img/fc50ff1a265fdcefbf178583eb15342b_141.png)

**示例**：编写一个显示主机名的脚本 `show_hostname.sh`，然后在所有主机上运行。
```bash
ansible all -m script -a “/path/to/show_hostname.sh“
```

![](img/fc50ff1a265fdcefbf178583eb15342b_143.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_145.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_147.png)

### 3. copy 模块

![](img/fc50ff1a265fdcefbf178583eb15342b_149.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_151.png)

`copy` 模块用于将文件从控制节点复制到远程主机。

![](img/fc50ff1a265fdcefbf178583eb15342b_153.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_155.png)

**示例**：将本地的SELinux配置文件推送到所有主机，并备份原文件。
```bash
ansible all -m copy -a “src=./selinux dest=/etc/selinux/config backup=yes“
```

![](img/fc50ff1a265fdcefbf178583eb15342b_157.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_159.png)

`copy` 模块还可以直接生成文件内容：
```bash
ansible all -m copy -a “content=‘Hello World\n‘ dest=/data/hello.txt“
```

### 4. 其他实用模块简介

*   **`fetch` 模块**：与 `copy` 相反，用于将远程主机上的文件拉取到控制节点，常用于收集日志。
*   **`file` 模块**：专门用于管理文件和目录的属性，如创建、删除、修改权限和所有者等，比用 `shell` 模块更专业。
*   **`yum`/`apt` 模块**：用于软件包管理。
*   **`service` 模块**：用于管理服务状态。

![](img/fc50ff1a265fdcefbf178583eb15342b_161.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_163.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_165.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_166.png)

---

![](img/fc50ff1a265fdcefbf178583eb15342b_168.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_170.png)

## 总结 📝

![](img/fc50ff1a265fdcefbf178583eb15342b_172.png)

本节课中我们一起学习了Ansible的核心配置与高级用法。我们首先剖析了 `ansible.cfg` 配置文件，理解了关键参数如禁用主机密钥检查、启用日志的重要性。接着，我们熟悉了 `ansible-doc` 命令和灵活的 `ansible` 命令主机模式。最后，通过实战演练，我们掌握了 `command`、`shell`、`script`、`copy` 等核心模块的使用场景与区别。

![](img/fc50ff1a265fdcefbf178583eb15342b_174.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_176.png)

![](img/fc50ff1a265fdcefbf178583eb15342b_178.png)

记住，**`shell` 模块比 `command` 更强大**，而管理文件应优先使用 **`file`** 模块。合理利用这些模块和模式，可以极大地提升运维自动化效率和可靠性。