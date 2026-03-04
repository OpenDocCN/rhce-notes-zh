# Linux培训课程：第9章：SSH服务与日志管理 🔧

![](img/71ffb124c683c43732e3de8ab2f96008_1.png)

![](img/71ffb124c683c43732e3de8ab2f96008_3.png)

在本节课中，我们将要学习Linux系统中的SSH远程连接服务以及日志管理工具。SSH是管理Linux服务器最常用、最安全的协议之一，而日志管理则是系统运维中不可或缺的技能。我们将从基础概念讲起，逐步深入到实际配置和高级应用。

![](img/71ffb124c683c43732e3de8ab2f96008_5.png)

## 9.2：SSH远程连接协议 🌐

上一节我们介绍了Linux系统配置服务的基本原则。本节中我们来看看一个具体的服务——SSH。

![](img/71ffb124c683c43732e3de8ab2f96008_7.png)

SSH（Secure Shell）是一种用于远程登录和管理Linux服务器的安全网络协议。与Windows的图形化远程桌面不同，SSH基于命令行界面，传输的是文本指令，因此具有效率更高、延迟更低的特点。

![](img/71ffb124c683c43732e3de8ab2f96008_9.png)

在Linux系统中配置服务，遵循以下核心原则：
1.  Linux系统中的一切都是文件。
2.  配置一个服务，本质上是修改该服务对应的配置文件。
3.  修改配置参数后，通常需要重启对应的服务才能使新参数生效。
4.  为了确保服务在系统重启后依然可用，需要将服务加入启动项。

![](img/71ffb124c683c43732e3de8ab2f96008_11.png)

![](img/71ffb124c683c43732e3de8ab2f96008_13.png)

![](img/71ffb124c683c43732e3de8ab2f96008_15.png)

![](img/71ffb124c683c43732e3de8ab2f96008_17.png)

![](img/71ffb124c683c43732e3de8ab2f96008_19.png)

![](img/71ffb124c683c43732e3de8ab2f96008_21.png)

![](img/71ffb124c683c43732e3de8ab2f96008_23.png)

![](img/71ffb124c683c43732e3de8ab2f96008_25.png)

根据FHS（文件系统层次结构标准）协议，系统及服务的配置文件通常存放在 `/etc` 目录下。服务的配置文件主要分为两类：
*   **主配置文件**：保存着服务**最重要**的配置参数。
*   **一般配置文件**：保存着**一般重要**的配置参数，通常由主配置文件调用。

![](img/71ffb124c683c43732e3de8ab2f96008_27.png)

![](img/71ffb124c683c43732e3de8ab2f96008_29.png)

![](img/71ffb124c683c43732e3de8ab2f96008_31.png)

![](img/71ffb124c683c43732e3de8ab2f96008_33.png)

![](img/71ffb124c683c43732e3de8ab2f96008_35.png)

![](img/71ffb124c683c43732e3de8ab2f96008_37.png)

![](img/71ffb124c683c43732e3de8ab2f96008_39.png)

![](img/71ffb124c683c43732e3de8ab2f96008_41.png)

![](img/71ffb124c683c43732e3de8ab2f96008_43.png)

寻找主配置文件有一个通用口诀：在 `/etc` 目录下，存在一个以**服务名称**命名的文件夹，里面有一个以**服务名称**命名并以 `.conf` 结尾的文件。例如，SSH服务的主配置文件路径通常是：
```
/etc/ssh/sshd_config
```

![](img/71ffb124c683c43732e3de8ab2f96008_45.png)

![](img/71ffb124c683c43732e3de8ab2f96008_47.png)

### 配置SSH服务实例

![](img/71ffb124c683c43732e3de8ab2f96008_49.png)

![](img/71ffb124c683c43732e3de8ab2f96008_51.png)

![](img/71ffb124c683c43732e3de8ab2f96008_53.png)

![](img/71ffb124c683c43732e3de8ab2f96008_55.png)

![](img/71ffb124c683c43732e3de8ab2f96008_57.png)

以下是配置SSH服务禁止root用户远程登录的步骤：

![](img/71ffb124c683c43732e3de8ab2f96008_59.png)

1.  **编辑主配置文件**：使用vim编辑器打开SSH的主配置文件。
    ```bash
    vim /etc/ssh/sshd_config
    ```
2.  **修改参数**：在文件中找到 `#PermitRootLogin yes` 这一行，删除开头的 `#` 号（取消注释），并将 `yes` 改为 `no`。
    ```bash
    PermitRootLogin no
    ```
3.  **保存并重启服务**：保存文件退出后，必须重启SSH服务才能使配置生效。
    ```bash
    systemctl restart sshd
    ```
4.  **加入启动项**（可选，通常只需执行一次）：确保服务在系统重启后自动运行。
    ```bash
    systemctl enable sshd
    ```

![](img/71ffb124c683c43732e3de8ab2f96008_61.png)

![](img/71ffb124c683c43732e3de8ab2f96008_63.png)

![](img/71ffb124c683c43732e3de8ab2f96008_65.png)

![](img/71ffb124c683c43732e3de8ab2f96008_67.png)

![](img/71ffb124c683c43732e3de8ab2f96008_69.png)

![](img/71ffb124c683c43732e3de8ab2f96008_71.png)

![](img/71ffb124c683c43732e3de8ab2f96008_73.png)

**注意**：修改配置后不重启服务，旧参数依然有效。这是初学者常犯的错误。

![](img/71ffb124c683c43732e3de8ab2f96008_75.png)

![](img/71ffb124c683c43732e3de8ab2f96008_77.png)

![](img/71ffb124c683c43732e3de8ab2f96008_79.png)

### SSH的两种验证方式

![](img/71ffb124c683c43732e3de8ab2f96008_81.png)

![](img/71ffb124c683c43732e3de8ab2f96008_83.png)

SSH之所以安全，是因为它支持两种加密验证方式：
1.  **口令验证**：使用传统的用户名和密码进行验证。
2.  **密钥对验证**：使用非对称加密的密钥对进行验证，安全性更高。
    *   **私钥文件**：在客户端本地生成并严格保密，用于**加密**信息。
    *   **公钥文件**：发送给服务器，用于**解密和验证**来自对应私钥的信息。

![](img/71ffb124c683c43732e3de8ab2f96008_85.png)

![](img/71ffb124c683c43732e3de8ab2f96008_87.png)

![](img/71ffb124c683c43732e3de8ab2f96008_89.png)

![](img/71ffb124c683c43732e3de8ab2f96008_91.png)

密钥对验证的优先级高于口令验证。配置了密钥对验证后，即使服务器允许口令登录，客户端也会优先尝试密钥对验证。

![](img/71ffb124c683c43732e3de8ab2f96008_93.png)

![](img/71ffb124c683c43732e3de8ab2f96008_95.png)

![](img/71ffb124c683c43732e3de8ab2f96008_97.png)

![](img/71ffb124c683c43732e3de8ab2f96008_99.png)

![](img/71ffb124c683c43732e3de8ab2f96008_101.png)

### 配置密钥对验证

![](img/71ffb124c683c43732e3de8ab2f96008_103.png)

![](img/71ffb124c683c43732e3de8ab2f96008_105.png)

![](img/71ffb124c683c43732e3de8ab2f96008_107.png)

![](img/71ffb124c683c43732e3de8ab2f96008_109.png)

![](img/71ffb124c683c43732e3de8ab2f96008_111.png)

![](img/71ffb124c683c43732e3de8ab2f96008_113.png)

![](img/71ffb124c683c43732e3de8ab2f96008_115.png)

以下是配置SSH使用密钥对登录并禁用口令登录的步骤：

![](img/71ffb124c683c43732e3de8ab2f96008_117.png)

![](img/71ffb124c683c43732e3de8ab2f96008_119.png)

![](img/71ffb124c683c43732e3de8ab2f96008_121.png)

1.  **在客户端生成密钥对**：
    ```bash
    ssh-keygen
    ```
    执行命令后连续按回车，接受默认设置（密钥对保存在 `~/.ssh/` 目录下，不设置密钥密码）。
2.  **将公钥上传至服务器**：
    ```bash
    ssh-copy-id 192.168.10.10
    ```
    此命令会将客户端的公钥自动追加到服务器对应用户家目录下的 `~/.ssh/authorized_keys` 文件中。首次执行需要输入服务器密码。
3.  **在服务器端禁用口令验证**：
    *   编辑SSH主配置文件 `/etc/ssh/sshd_config`。
    *   找到 `#PasswordAuthentication yes`，取消注释并将 `yes` 改为 `no`。
        ```bash
        PasswordAuthentication no
        ```
    *   重启SSH服务：`systemctl restart sshd`。
4.  **测试连接**：配置完成后，客户端再次连接服务器将不再需要输入密码，直接通过密钥对验证登录。

![](img/71ffb124c683c43732e3de8ab2f96008_123.png)

## 9.3：基于SSH的文件传输与不间断会话 🚀

上一节我们学习了SSH的登录验证。本节中我们来看看基于SSH协议的更多实用功能。

### 远程文件传输

![](img/71ffb124c683c43732e3de8ab2f96008_125.png)

![](img/71ffb124c683c43732e3de8ab2f96008_127.png)

![](img/71ffb124c683c43732e3de8ab2f96008_129.png)

既然SSH可以安全地传输命令和文本，自然也可以用来传输文件。`scp` 命令就是基于SSH协议进行加密文件传输的工具。

以下是 `scp` 命令的使用方法：

![](img/71ffb124c683c43732e3de8ab2f96008_131.png)

*   **将本地文件上传到远程服务器**：
    ```bash
    scp /本地/文件路径 用户名@服务器IP:/远程/目录路径
    ```
    **示例**：`scp local_file.txt root@192.168.10.10:/root/`

![](img/71ffb124c683c43732e3de8ab2f96008_133.png)

![](img/71ffb124c683c43732e3de8ab2f96008_135.png)

![](img/71ffb124c683c43732e3de8ab2f96008_137.png)

*   **从远程服务器下载文件到本地**：
    ```bash
    scp 用户名@服务器IP:/远程/文件路径 /本地/目录路径
    ```
    **示例**：`scp root@192.168.10.10:/root/remote_file.txt /home/`

![](img/71ffb124c683c43732e3de8ab2f96008_139.png)

![](img/71ffb124c683c43732e3de8ab2f96008_141.png)

![](img/71ffb124c683c43732e3de8ab2f96008_143.png)

![](img/71ffb124c683c43732e3de8ab2f96008_145.png)

**注意**：`scp` 命令同样遵循SSH的验证规则。如果配置了密钥对验证，则传输文件时也无需输入密码。

![](img/71ffb124c683c43732e3de8ab2f96008_147.png)

![](img/71ffb124c683c43732e3de8ab2f96008_149.png)

![](img/71ffb124c683c43732e3de8ab2f96008_151.png)

### 不间断会话服务 (Tmux)

![](img/71ffb124c683c43732e3de8ab2f96008_153.png)

![](img/71ffb124c683c43732e3de8ab2f96008_155.png)

![](img/71ffb124c683c43732e3de8ab2f96008_157.png)

![](img/71ffb124c683c43732e3de8ab2f96008_159.png)

![](img/71ffb124c683c43732e3de8ab2f96008_161.png)

在进行长时间的远程操作（如编译软件、备份数据）时，网络中断或客户端关闭会导致任务意外终止。Tmux服务可以解决这个问题，它能够将会话保存在服务器端，实现“不间断会话”。

![](img/71ffb124c683c43732e3de8ab2f96008_163.png)

![](img/71ffb124c683c43732e3de8ab2f96008_165.png)

![](img/71ffb124c683c43732e3de8ab2f96008_167.png)

![](img/71ffb124c683c43732e3de8ab2f96008_169.png)

![](img/71ffb124c683c43732e3de8ab2f96008_171.png)

Tmux的主要功能包括：
1.  **会话持久化**：将会话运行在后台，即使网络断开或关闭终端，任务也不会中断。
2.  **多窗口管理**：在一个终端内分割出多个窗口，同时进行多项工作。
3.  **会话共享**：允许多个用户同时连接到同一个Tmux会话，实现屏幕共享和协同操作。

![](img/71ffb124c683c43732e3de8ab2f96008_173.png)

![](img/71ffb124c683c43732e3de8ab2f96008_175.png)

![](img/71ffb124c683c43732e3de8ab2f96008_177.png)

![](img/71ffb124c683c43732e3de8ab2f96008_179.png)

![](img/71ffb124c683c43732e3de8ab2f96008_181.png)

![](img/71ffb124c683c43732e3de8ab2f96008_183.png)

以下是Tmux的基本用法：

![](img/71ffb124c683c43732e3de8ab2f96008_185.png)

![](img/71ffb124c683c43732e3de8ab2f96008_187.png)

![](img/71ffb124c683c43732e3de8ab2f96008_189.png)

*   **安装Tmux**：
    ```bash
    dnf install tmux
    ```
*   **新建并命名会话**：
    ```bash
    tmux new -s session_name
    ```
*   **退出当前会话（会话在后台继续运行）**：在Tmux会话中，按下 `Ctrl+b`，然后按 `d`。
*   **查看所有会话**：
    ```bash
    tmux ls
    ```
*   **恢复指定会话**：
    ```bash
    tmux attach -t session_name
    ```
*   **在会话内分割窗口**：
    *   水平分割（上下窗口）：`Ctrl+b`，然后按 `"`
    *   垂直分割（左右窗口）：`Ctrl+b`，然后按 `%`
*   **在分割的窗口间切换**：`Ctrl+b`，然后按方向键（上、下、左、右）。

![](img/71ffb124c683c43732e3de8ab2f96008_191.png)

![](img/71ffb124c683c43732e3de8ab2f96008_193.png)

![](img/71ffb124c683c43732e3de8ab2f96008_195.png)

![](img/71ffb124c683c43732e3de8ab2f96008_197.png)

## 9.4：日志信息检索 📄

![](img/71ffb124c683c43732e3de8ab2f96008_199.png)

![](img/71ffb124c683c43732e3de8ab2f96008_201.png)

![](img/71ffb124c683c43732e3de8ab2f96008_203.png)

![](img/71ffb124c683c43732e3de8ab2f96008_205.png)

上一节我们介绍了提升远程工作效率的工具。本节中我们来看看如何高效地管理系统日志。

![](img/71ffb124c683c43732e3de8ab2f96008_207.png)

系统在运行中会产生大量日志，记录在 `/var/log/` 目录下，其中 `/var/log/messages` 是一个综合性的重要日志文件。直接查看整个文件效率低下，`journalctl` 命令是RHEL 8及之后版本中强大的日志管理工具，可以按条件精准检索。

### 使用journalctl检索日志

以下是 `journalctl` 命令的常用检索方式：

![](img/71ffb124c683c43732e3de8ab2f96008_209.png)

![](img/71ffb124c683c43732e3de8ab2f96008_211.png)

![](img/71ffb124c683c43732e3de8ab2f96008_213.png)

*   **按日志优先级筛选**：只显示紧急程度在“严重”及以上的日志。
    ```bash
    journalctl -p crit
    ```
    日志优先级从高到低为：`emerg` > `alert` > `crit` > `err` > `warning` > `notice` > `info` > `debug`。
*   **按时间筛选**：
    *   查看今天的日志：`journalctl --since today`
    *   查看指定时间段的日志：`journalctl --since "2022-01-01 00:00:00" --until "2022-01-02 00:00:00"`
    *   查看最近2小时的日志：`journalctl --since "2 hours ago"`
*   **按服务/程序单元筛选**：只查看与SSH服务相关的日志。
    ```bash
    journalctl -u sshd
    ```
*   **实时查看最新日志**（类似 `tail -f`）：
    ```bash
    journalctl -f
    ```

---

![](img/71ffb124c683c43732e3de8ab2f96008_215.png)

![](img/71ffb124c683c43732e3de8ab2f96008_217.png)

![](img/71ffb124c683c43732e3de8ab2f96008_218.png)

本节课中我们一起学习了SSH远程连接服务的配置与管理，包括口令和密钥对两种验证方式；掌握了基于SSH协议的文件传输命令 `scp`；了解了使用Tmux实现不间断会话、多窗口管理和屏幕共享；最后，学习了使用 `journalctl` 命令高效地检索系统日志。这些技能是Linux系统管理员进行日常维护和故障排查的基础。