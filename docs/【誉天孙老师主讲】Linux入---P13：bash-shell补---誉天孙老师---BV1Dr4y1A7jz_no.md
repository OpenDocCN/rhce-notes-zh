# Linux入门：P13：bash shell补充

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_1.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_3.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_5.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_7.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_9.png)

在本节课中，我们将深入学习bash shell的进阶知识，包括作业回顾、变量作用域、环境变量配置文件以及登录shell与非登录shell的区别。这些概念对于理解Linux系统配置和编写shell脚本至关重要。

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_11.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_13.png)

## 作业回顾与常见问题解析

上一节我们介绍了正则表达式和sed等文本处理工具，本节我们来看看上周作业中的常见问题。

以下是作业中需要注意的关键点：

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_15.png)

*   **用户家目录创建**：使用 `useradd -D /data/user1` 命令时，如果 `/data` 目录已存在且权限为755（属主root），会导致新用户 `user1` 对其家目录没有写权限。正确做法是让命令自动创建家目录，例如 `useradd -d /data/user1 user1`。
*   **移动用户家目录**：使用 `usermod -d /new/home user1` 仅修改配置文件中的家目录路径。如果需要同时迁移家目录下的文件，必须加上 `-m` 选项：`usermod -m -d /new/home user1`。
*   **组密码设置**：给组设置密码应使用 `gpasswd` 命令，而不是 `usermod -p`。`usermod -p` 设置的是加密后的用户密码，并非登录密码。
*   **递归修改权限**：使用 `chown -R user1:group1 dir` 命令时，`dir` 是递归的起始点。如果路径是 `/a/b/c`，则修改的是 `c` 目录及其下所有内容的属主属组。
*   **复制权限**：将一个目录的权限复制给另一个目录，可以使用 `chmod` 的 `--reference` 选项：`chmod --reference=源目录 目标目录`。
*   **排序命令选项**：`sort` 命令的选项合并需注意。`-t` 和 `-k` 后面需要接参数（如 `-t: -k3`），而 `-r` 和 `-n` 可以合并为 `-rn`。
*   **定义带日期备份的别名**：别名中要使用动态日期，可以直接使用命令替换。例如：`alias backup='cp -a /source /backup/$(date +%F)'`。
*   **查找文件内容**：在目录中递归查找包含特定字符串的文件，应使用 `grep -r` 命令。例如：`grep -rn "pass" /etc`。
*   **正则表达式应用**：作业中的正则表达式题目旨在练习 `grep` 和 `sed` 的文本处理能力，掌握后能应对绝大多数文本处理场景。

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_17.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_19.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_21.png)

## 变量进阶：环境变量配置文件

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_23.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_25.png)

之前我们学习了本地变量和环境变量，但它们会随着shell会话的结束而消失。为了让变量永久生效，我们需要将其定义在配置文件中。

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_27.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_29.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_31.png)

系统主要使用四个配置文件来管理bash shell的变量和别名：

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_33.png)

1.  **`/etc/profile`**：**全局配置文件**。系统启动时，为**每个用户**读取此文件，用于设置全局环境变量。
2.  **`~/.bash_profile`**：**用户配置文件**。用户登录时，在读取 `/etc/profile` **之后**读取此文件（位于用户家目录），用于设置用户个人的环境变量。
3.  **`/etc/bashrc`**：**全局bashrc文件**。为**所有用户**的**非登录shell**读取此文件，通常用于设置全局的shell函数和别名。
4.  **`~/.bashrc`**：**用户bashrc文件**。用户的**非登录shell**会读取此文件（位于用户家目录），通常用于设置用户个人的shell函数和别名。

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_35.png)

**优先级**：当同一个变量在多个文件中定义时，后读取的文件会覆盖先读取的。对于环境变量，`~/.bash_profile` 中的定义会覆盖 `/etc/profile` 中的定义。

## 登录Shell与非登录Shell

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_37.png)

理解环境变量如何生效，关键在于区分两种shell类型。

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_39.png)

**登录Shell (Login Shell)** 指的是需要用户进行身份验证的完整会话。
**非登录Shell (Non-Login Shell)** 指的是在已有会话中启动的不需要重新登录的子shell。

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_41.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_43.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_45.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_47.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_49.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_51.png)

以下是两种shell的常见场景：

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_53.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_55.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_57.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_59.png)

*   **登录Shell**：
    *   通过终端或SSH直接输入用户名密码登录。
    *   在图形界面下打开的第一个终端模拟器（取决于发行版配置）。
    *   使用 `su - username` 或 `su -l username` 切换用户（`-` 代表模拟完整登录）。
*   **非登录Shell**：
    *   在图形界面终端中再打开一个新的终端标签页或窗口。
    *   在shell中直接执行 `bash` 命令启动的子shell。
    *   执行shell脚本时启动的shell。
    *   使用 `su username`（不加 `-`）切换用户。

**配置文件读取规则**：
*   **登录Shell** 启动时，会按顺序读取：`/etc/profile` -> `~/.bash_profile` (或 `~/.bash_login`, `~/.profile`) -> `~/.bashrc` (通常由 `~/.bash_profile` 调用)。
*   **非登录Shell** 启动时，通常只读取：`~/.bashrc` -> `/etc/bashrc` (通常由 `~/.bashrc` 调用)。

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_61.png)

**核心概念示例**：
假设在以下文件定义了变量：
*   `/etc/profile`: `A=100`
*   `~/.bash_profile`: `B=200`
*   `/etc/bashrc`: `C=300`
*   `~/.bashrc`: `D=400`

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_63.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_64.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_66.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_68.png)

不同操作下变量的可见性：
*   `su - user` (登录Shell): A, B, C, D 均可见。
*   `su user` (非登录Shell): 仅 C, D 可见（且D仅对当前用户有效）。
*   在终端中新开标签（非登录Shell）：通常 C, D 可见。

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_70.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_72.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_74.png)

**让变量永久生效**：
*   要使一个**环境变量**对所有用户生效，应将其定义在 `/etc/profile` 中，并执行 `export 变量名=值`。
*   要使一个**环境变量**对特定用户生效，应将其定义在 `~/.bash_profile` 中。
*   要使一个**别名或shell函数**对所有用户生效，应将其定义在 `/etc/bashrc` 中。
*   要使一个**别名或shell函数**对特定用户生效，应将其定义在 `~/.bashrc` 中。

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_76.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_78.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_80.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_82.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_84.png)

修改配置文件后，需要**重启终端**或使用 `source` 命令（例如 `source ~/.bashrc`）使更改立即在当前shell生效。

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_86.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_88.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_90.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_92.png)

## 引号与反斜杠的转义

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_94.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_96.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_98.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_100.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_102.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_104.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_106.png)

在shell中，引号和反斜杠用于控制字符的特殊含义。

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_108.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_110.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_112.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_114.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_116.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_118.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_120.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_122.png)

*   **反斜杠 `\`**：**转义字符**。去除紧随其后的**一个字符**的特殊含义，使其变为普通字符。
    ```bash
    echo \$HOME  # 输出 $HOME，而不是变量值
    ```
*   **单引号 `' '`**：**强引用**。引号内的**所有字符**都失去特殊含义，原样输出。
    ```bash
    echo '$HOME $(date)'  # 输出 $HOME $(date)
    ```
*   **双引号 `" "`**：**弱引用**。引号内**大部分**字符失去特殊含义，但以下字符**保留**：
    *   美元符号 `$` （变量替换）
    *   反引号 `` ` `` 或 `$()` （命令替换）
    *   反斜杠 `\` （但只对 `$`, `` ` ``, `"`, `\`, `!`(在某些shell中) 等字符有效）
    ```bash
    echo "My home is $HOME, today is $(date +%F)" # 变量和命令被替换
    echo "The cost is \$5" # 使用\转义$，输出 The cost is $5
    ```

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_124.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_126.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_128.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_129.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_131.png)

**选择原则**：
*   需要原样输出字符串时，用**单引号**。
*   需要变量替换、命令替换或包含单引号时，用**双引号**。
*   只需转义个别字符时，用**反斜杠**。

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_133.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_135.png)

---

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_137.png)

![](img/012e4b3a94fe2c37f0b58d62205e0dfd_139.png)

本节课中我们一起学习了bash shell的进阶内容。我们回顾了作业中的易错点，深入理解了环境变量如何通过配置文件永久生效，掌握了登录shell与非登录shell的区别及其读取配置文件的规则，并学会了如何使用引号和反斜杠精确控制字符的解析。这些知识是进行系统定制和编写可靠shell脚本的基础。